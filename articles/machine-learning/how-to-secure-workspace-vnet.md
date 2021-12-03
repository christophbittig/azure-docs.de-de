---
title: Schützen eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken
titleSuffix: Azure Machine Learning
description: Verwenden Sie eine isolierte Azure Virtual Network-Instanz, um Ihren Azure Machine Learning-Arbeitsbereich und zugehörige Ressourcen zu schützen.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, security
ms.openlocfilehash: 11e501615e10ccd3bfb407e12f1839d27e81e337
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562287"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Schützen eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken

In diesem Artikel erfahren Sie, wie Sie einen Azure Machine Learning-Arbeitsbereich und die zugehörigen Ressourcen in einem virtuellen Netzwerk schützen.

> [!TIP]
> Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:
>
> * [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
> * [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
> * [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
> * [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
> * [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
> * [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)
>
> Ein Tutorial zum Erstellen eines sicheren Arbeitsbereichs finden Sie unter [Tutorial: Erstellen eines sicheren Arbeitsbereichs](tutorial-create-secure-workspace.md).

In diesem Artikel erfahren Sie, wie Sie die folgenden Arbeitsbereichsressourcen in einem virtuellen Netzwerk aktivieren:
> [!div class="checklist"]
> - Azure Machine Learning-Arbeitsbereich
> - Azure-Speicherkonten
> - Azure Machine Learning-Datenspeicher und -Datasets
> - Azure-Schlüsseltresor
> - Azure Container Registry

## <a name="prerequisites"></a>Voraussetzungen

+ Im Artikel [Übersicht über die Netzwerksicherheit](how-to-network-security-overview.md) finden Sie Informationen zu gängigen Szenarien im Zusammenhang mit virtuellen Netzwerken sowie zur Gesamtarchitektur virtueller Netzwerke.

+ Lesen Sie den Artikel [Bewährte Azure Machine Learning-Methoden für die Unternehmenssicherheit](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security), um mehr über bewährte Methoden zu erfahren.

+ Ein vorhandenes virtuelles Netzwerk und Subnetz, die mit Ihren Computeressourcen verwendet werden können

    > [!TIP]
    > Wenn Sie planen, Azure Container Instances im virtuellen Netzwerk zu verwenden (um Modelle bereitzustellen), müssen sich der Arbeitsbereich und das virtuelle Netzwerk in derselben Ressourcengruppe befinden. Andernfalls können sie sich in verschiedenen Gruppen befinden.

+ Ihr Benutzerkonto muss über die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zu den folgenden Aktionen berechtigt werden, um Ressourcen in einem virtuellen Netzwerk oder Subnetz bereitstellen zu können:

    - „Microsoft.Network/virtualNetworks/join/action“ auf der virtuellen Netzwerkressource
    - „Microsoft.Network/virtualNetworks/subnet/join/action“ auf der Subnetzressource

    Weitere Informationen zur rollenbasierten Zugriffssteuerung von Azure in Netzwerken finden Sie unter [Integrierte Netzwerkrollen](../role-based-access-control/built-in-roles.md#networking).

### <a name="azure-container-registry"></a>Azure Container Registry

* Für Ihre Azure Container Registry-Instanz (ACR) muss ein Premium-Tarif erworben worden sein. Informationen zum Durchführen eines Upgrades finden Sie unter [Wechseln von SKUs](../container-registry/container-registry-skus.md#changing-tiers).

* Azure Container Registry muss sich in demselben virtuellen Netzwerk und Subnetz befinden wie das Speicherkonto und die Computeziele, die für Training oder Rückschluss verwendet werden.

* Der Azure Machine Learning-Arbeitsbereich muss einen [Azure Machine Learning-Computecluster](how-to-create-attach-compute-cluster.md) enthalten.

## <a name="limitations"></a>Einschränkungen

### <a name="azure-storage-account"></a>Azure Storage-Konto

Wenn sowohl der Azure Machine Learning-Arbeitsbereich als auch das Azure Storage-Konto einen privaten Endpunkt verwenden, um eine Verbindung mit dem VNet herzustellen, müssen sich beide innerhalb desselben Subnetzes befinden.

### <a name="azure-container-registry"></a>Azure Container Registry

Wenn sich ACR hinter einem virtuellen Netzwerk befindet, kann Azure Machine Learning es nicht zum direkten Erstellen von Docker-Images verwenden. Stattdessen wird zum Erstellen der Images der Computecluster verwendet.

> [!IMPORTANT]
> Der Computecluster, der zum Erstellen von Docker-Images verwendet wird, muss auf die Paket-Repositories zugreifen können, die zum Schulen und Bereitstellen Ihrer Modelle verwendet werden. Möglicherweise müssen Sie Netzwerk-Sicherheitsregeln hinzufügen, die den Zugriff auf öffentliche Repositories zulassen, [private Python-Pakete verwenden](how-to-use-private-python-packages.md) oder [benutzerdefinierte Docker-Images](how-to-train-with-custom-image.md) verwenden, die bereits die Pakete enthalten.

> [!WARNING]
> Wenn Ihre Azure Container Registry einen privaten Endpunkt zur Kommunikation mit dem virtuellen Netzwerk verwendet, können Sie keine verwaltete Identität mit einem Azure Machine Learning-Computecluster verwenden. Um eine verwaltete Identität mit einem Computecluster zu verwenden, verwenden Sie einen Dienstendpunkt mit der Azure Container Registry für den Arbeitsbereich.

## <a name="required-public-internet-access"></a>Erforderlicher öffentlicher Internetzugriff

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

Informationen zur Verwendung einer Firewalllösung finden Sie unter [Verwenden einer Firewall mit Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## <a name="secure-the-workspace-with-private-endpoint"></a>Schützen des Arbeitsbereichs mit privatem Endpunkt

Azure Private Link ermöglicht Ihnen das Herstellen einer Verbindung mit Ihrem Arbeitsbereich über einen privaten Endpunkt. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in Ihrem virtuellen Netzwerk. Sie können dann den Zugriff auf Ihren Arbeitsbereich so einschränken, dass er nur über die privaten IP-Adressen erfolgt. Private Endpunkte helfen dabei, das Risiko einer Datenexfiltration zu verringern.

Weitere Informationen zum Konfigurieren eines privaten Endpunkts für Ihren Arbeitsbereich finden Sie unter [Konfigurieren eines privaten Endpunkts](how-to-configure-private-link.md).

> [!WARNING]
> Wenn Sie einen Arbeitsbereich mit privaten Endpunkten sichern, wird die End-to-End-Sicherheit allein nicht sichergestellt. Sie müssen die Schritte im restlichen Artikel und in der VNET-Serie ausführen, um einzelne Komponenten Ihrer Lösung zu sichern. Wenn Sie z. B. einen privaten Endpunkt für den Arbeitsbereich verwenden, ihr Azure Storage-Konto sich jedoch nicht hinter dem VNet befindet, verwendet der Datenverkehr zwischen dem Arbeitsbereich und dem Speicher das VNet nicht zur Sicherung.

## <a name="secure-azure-storage-accounts"></a>Schützen von Azure-Speicherkonten

Azure Machine Learning unterstützt Speicherkonten, die so konfiguriert sind, dass sie entweder einen privaten Endpunkt oder einen Dienstendpunkt verwenden. 

# <a name="private-endpoint"></a>[Privater Endpunkt](#tab/pe)

1. Wählen Sie das Azure Storage-Konto im Azure-Portal aus.
1. Verwenden Sie die Informationen unter [Verwenden privater Endpunkte für Azure Storage](../storage/common/storage-private-endpoints.md#creating-a-private-endpoint), um private Endpunkte für die folgenden Speicherunterressourcen hinzuzufügen:

    * **Blob**
    * **Datei**
    * **Warteschlange**: Nur erforderlich, wenn Sie [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) in einer Azure Machine Learning-Pipeline verwenden möchten.
    * **Tabelle**: Nur erforderlich, wenn Sie [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) in einer Azure Machine Learning-Pipeline verwenden möchten.

    :::image type="content" source="./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png" alt-text="Screenshot: Seite für die Konfiguration von privaten Endpunkten mit den Optionen „Blob“ und „Datei“":::

    > [!TIP]
    > Wenn Sie ein Speicherkonto konfigurieren, das **nicht** der Standardspeicher ist, wählen Sie als **untergeordnete Zielressource** den entsprechenden Typ für das Speicherkonto aus, das Sie hinzufügen möchten.

1. Nachdem Sie die privaten Endpunkte für die drei Unterressourcen erstellt haben, wählen Sie die Registerkarte __Firewalls und virtuelle Netzwerke__ unter __Netzwerk__ für das Speicherkonto aus.
1. Klicken Sie auf __Ausgewählte Netzwerke__, und wählen Sie dann unter __Ressourceninstanzen__ den Typ `Microsoft.MachineLearningServices/Workspace` als __Ressourcentyp__ aus. Wählen Sie Ihren Arbeitsbereich über __Instanzname__ aus. Weitere Informationen finden Sie unter [Vertrauenswürdiger Zugriff auf der Grundlage einer systemseitig zugewiesenen verwalteten Identität](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity).

    > [!TIP]
    > Alternativ können Sie __Azure-Diensten in der Liste vertrauenswürdiger Dienste den Zugriff auf dieses Speicherkonto gestatten__ auswählen, um einen breiteren Zugriff über vertrauenswürdige Dienste zuzulassen. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-no-vnet.png" alt-text="Der Netzwerkbereich auf der Azure Storage-Seite im Azure-Portal bei Verwendung eines privaten Endpunkts":::

1. Wählen Sie zum Speichern der Konfiguration __Speichern__ aus.

> [!TIP]
> Wenn Sie einen privaten Endpunkt verwenden, können Sie auch den öffentlichen Zugriff deaktivieren. Weitere Informationen finden Sie unter [Öffentlichen Lesezugriff verweigern](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

# <a name="service-endpoint"></a>[Dienstendpunkt](#tab/se)

1. Wählen Sie das Azure Storage-Konto im Azure-Portal aus.

1. Wählen Sie im Bereich __Sicherheit + Netzwerkbetrieb__ auf der linken Seite die Option __Netzwerk__ und dann die Registerkarte __Firewalls und virtuelle Netzwerke__ aus.

1. Klicken Sie auf __Ausgewählte Netzwerke__. Wählen Sie unter __Virtuelle Netzwerke__ den Link __Vorhandenes virtuelles Netzwerk hinzufügen__ und dann das virtuelle Netzwerk aus, das Ihr Arbeitsbereich verwendet.

    > [!IMPORTANT]
    > Das Speicherkonto muss sich in demselben virtuellen Netzwerk und Subnetz befinden wie die Compute-Instanzen oder Cluster, die für Training oder Rückschluss verwendet werden.

1. Wählen Sie unter __Ressourceninstanzen__ entsprechend `Microsoft.MachineLearningServices/Workspace` als __Ressourcentyp__ und dann Ihren Arbeitsbereich mithilfe von __Instanzname__ aus. Weitere Informationen finden Sie unter [Vertrauenswürdiger Zugriff auf der Grundlage einer systemseitig zugewiesenen verwalteten Identität](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity).

    > [!TIP]
    > Alternativ können Sie __Azure-Diensten in der Liste vertrauenswürdiger Dienste den Zugriff auf dieses Speicherkonto gestatten__ auswählen, um einen breiteren Zugriff über vertrauenswürdige Dienste zuzulassen. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png" alt-text="Der Netzwerkbereich auf der Azure Storage-Seite im Azure-Portal.":::

1. Wählen Sie zum Speichern der Konfiguration __Speichern__ aus.

> [!TIP]
> Wenn Sie einen Dienstendpunkt verwenden, können Sie auch den öffentlichen Zugriff deaktivieren. Weitere Informationen finden Sie unter [Öffentlichen Lesezugriff verweigern](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

---

## <a name="secure-azure-key-vault"></a>Schützen von Azure Key Vault

Azure Machine Learning verwendet eine zugeordnete Key Vault-Instanz zum Speichern der folgenden Anmeldeinformationen:
* Der verknüpften Speicherkonto-Verbindungszeichenfolge
* Kennwörter in Azure Container Repository-Instanzen
* Verbindungszeichenfolgen zur Verbindung mit Datenspeichern.

Azure Key Vault kann für die Verwendung eines privaten Endpunkts oder eines Dienstendpunkts konfiguriert werden. Um die Azure Machine Learning-Experimentierfunktionen mit Azure Key Vault hinter einem virtuellen Netzwerk zu verwenden, gehen Sie wie folgt vor:

> [!TIP]
> Unabhängig davon, ob Sie einen privaten Endpunkt oder einen Dienstendpunkt verwenden, muss sich der Schlüsseltresor im selben Netzwerk befinden wie der private Endpunkt des Arbeitsbereichs.

# <a name="private-endpoint"></a>[Privater Endpunkt](#tab/pe)

Informationen zur Verwendung eines privaten Endpunkts mit Azure Key Vault finden Sie unter [Integrieren von Key Vault in Azure Private Link](../key-vault/general/private-link-service.md#establish-a-private-link-connection-to-key-vault-using-the-azure-portal).


# <a name="service-endpoint"></a>[Dienstendpunkt](#tab/se)

1. Wechseln Sie zu der Key Vault-Instanz, die mit dem Arbeitsbereich verknüpft ist.

1. Wählen Sie auf der Seite __Key Vault__ im linken Bereich die Option __Netzwerk__ aus.

1. Führen Sie auf der Registerkarte __Firewalls und virtuelle Netzwerke__ folgende Aktionen aus:
    1. Wählen Sie unter __Zugriff erlauben von__ den Eintrag __Privater Endpunkt und ausgewählte Netzwerke__ aus.
    1. Wählen Sie unter __Virtuelle Netzwerke__ die Option __Vorhandenes virtuelles Netzwerk hinzufügen__ aus, um das virtuelle Netzwerk hinzuzufügen, in dem sich die Computeressourcen für Ihre Experimente befinden.
    1. Wählen Sie unter __Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben__ die Option __Ja__ aus.

    :::image type="content" source="./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png" alt-text="Abschnitt „Firewalls und virtuelle Netzwerke“ im Bereich „Key Vault“":::

Weitere Informationen finden Sie unter [Konfigurieren von Azure Key Vault-Netzwerkeinstellungen](../key-vault/general/how-to-azure-key-vault-network-security.md).

---

## <a name="enable-azure-container-registry-acr"></a>Aktivieren von Azure Container Registry (ACR)

> [!TIP]
> Wenn Sie beim Erstellen des Arbeitsbereichs keine vorhandene Azure Container Registry verwendet haben, ist möglicherweise keine vorhanden. Standardmäßig wird vom Arbeitsbereich erst eine ACR-Instanz erstellt, wenn eine solche benötigt wird. Um die Erstellung einer solchen zu erzwingen, trainieren Sie ein Modell mit Ihrem Arbeitsbereich, oder stellen Sie es bereit, bevor Sie die Schritte in diesem Abschnitt ausführen.

Azure Container Registry kann so konfiguriert werden, dass ein privater Endpunkt verwendet wird. Führen Sie die folgenden Schritte aus, um Ihren Arbeitsbereich so zu konfigurieren, dass er ACR verwendet, wenn er sich im virtuellen Netzwerk befindet:

1. Suchen Sie den Namen der Azure Container Registry-Instanz für Ihren Arbeitsbereich mit einer der folgenden Methoden:

    __Azure portal__

    Im Abschnitt „Übersicht“ des Arbeitsbereichs ist der Wert __Registrierung__ mit der Azure Container Registry-Instanz verknüpft.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry für den Arbeitsbereich" border="true":::

    __Azure-Befehlszeilenschnittstelle__

    Wenn Sie die [Machine Learning-Erweiterung für die Azure CLI installiert](reference-azure-machine-learning-cli.md) haben, können Sie mit dem Befehl `az ml workspace show` die Arbeitsbereichsinformationen anzeigen.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Der Befehl gibt einen Wert zurück, der `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` ähnelt. Der letzte Teil der Zeichenfolge ist der Name der Azure Container Registry-Instanz für den Arbeitsbereich.

1. Beschränken Sie den Zugriff auf Ihr virtuelles Netzwerk mithilfe der Schritte in [Herstellen einer privaten Verbindung mit einer Azure Container Registry-Instanz](../container-registry/container-registry-private-link.md). Wenn Sie das virtuelle Netzwerk hinzufügen, wählen Sie das virtuelle Netzwerk und das Subnetz für die Azure Machine Learning-Ressourcen aus.

1. Konfigurieren Sie den ACR für den Arbeitsbereich, um den [Zugriff durch vertrauenswürdige Dienste zuzulassen](../container-registry/allow-access-trusted-services.md).

1. Erstellen eines Computeclusters für Azure Machine Learning Dieser wird verwendet, um Docker-Images zu erstellen, wenn sich ACR hinter einem VNet befindet. Weitere Informationen finden Sie unter [Erstellen eines Computeclusters](how-to-create-attach-compute-cluster.md).

1. Verwenden Sie das Azure Machine Learning Python SDK, um den Arbeitsbereich zu konfigurieren und Docker-Images mithilfe der Compute-Instanz zu erstellen. Der folgende Codeausschnitt zeigt, wie der Arbeitsbereich aktualisiert wird, um einen Erstellungscompute festzulegen. Ersetzen Sie `mycomputecluster` durch den Namen des zu verwendenden Clusters:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Das Speicherkonto, der Computecluster und die Azure Container Registry-Instanz müssen sich in demselben Subnetz des virtuellen Netzwerks befinden.
    
    Weitere Informationen finden Sie in der Referenz zur [update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)-Methode.

> [!TIP]
> Wenn sich ACR hinter einem VNet befindet, können Sie auch [den öffentlichen Zugriff darauf deaktivieren](../container-registry/container-registry-access-selected-networks.md#disable-public-network-access).

## <a name="datastores-and-datasets"></a>Datenspeicher und Datasets
In der folgenden Tabelle sind die Dienste aufgeführt, für die Sie die Prüfung überspringen müssen:

| Dienst | Überspringen der Prüfung erforderlich? |
| ----- |:-----:|
| Azure Blob Storage | Ja |
| Azure-Dateifreigabe | Ja |
| Azure Data Lake Store Gen1 | Nein |
| Azure Data Lake Store Gen2 | Nein |
| Azure SQL-Datenbank | Ja |
| PostgreSQL | Ja |

> [!NOTE]
> Azure Data Lake Store Gen1 und Azure Data Lake Store Gen2 überspringen standardmäßig die Überprüfung, sodass keine Aktion erforderlich ist.

Im folgenden Codebeispiel wird ein neuer Azure-Blobdatenspeicher erstellt und `skip_validation=True` festgelegt.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Verwenden von Datasets

Die Syntax zum Überspringen der Überprüfung von Datasets ähnelt den folgenden Datasettypen:
- Durch Trennzeichen getrennte Datei
- JSON 
- Parquet
- SQL
- Datei

Der folgende Code erstellt ein neues JSON-Dataset und legt `validate=False`fest.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 
```

## <a name="securely-connect-to-your-workspace"></a>Sicheres Herstellen einer Verbindung mit Ihrem Arbeitsbereich

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="workspace-diagnostics"></a>Arbeitsbereichsdiagnose

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

* [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
* [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)