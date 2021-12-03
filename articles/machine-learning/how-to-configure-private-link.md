---
title: Konfigurieren eines privaten Endpunkts
titleSuffix: Azure Machine Learning
description: Verwenden Sie einen privaten Endpunkt, um sicher von einem virtuellen Netzwerk aus auf Ihren Azure Machine Learning-Arbeitsbereich zuzugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: 134eeff68debe0faa799d5fb5bfa4b477deef7f0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562559"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Konfigurieren eines privaten Endpunkts für einen Azure Machine Learning-Arbeitsbereich

Hier erfahren Sie, wie Sie einen privaten Endpunkt für Ihren Azure Machine Learning-Arbeitsbereich konfigurieren. Informationen zur Erstellung eines virtuellen Netzwerks für Azure Machine Learning finden Sie unter [Übersicht über die Isolierung und den Datenschutz für virtuelle Netzwerke](how-to-network-security-overview.md).

Azure Private Link ermöglicht Ihnen das Herstellen einer Verbindung mit Ihrem Arbeitsbereich über einen privaten Endpunkt. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in Ihrem virtuellen Netzwerk. Sie können dann den Zugriff auf Ihren Arbeitsbereich so einschränken, dass er nur über die privaten IP-Adressen erfolgt. Private Endpunkte helfen dabei, das Risiko einer Datenexfiltration zu verringern. Weitere Informationen zu privaten Endpunkten finden Sie im Artikel zu [Azure Private Link](../private-link/private-link-overview.md).

> [!WARNING]
> Wenn Sie einen Arbeitsbereich mit privaten Endpunkten sichern, wird die End-to-End-Sicherheit allein nicht sichergestellt. Sie müssen alle verschiedenen Komponenten Ihrer Lösung sichern. Wenn Sie z. B. einen privaten Endpunkt für den Arbeitsbereich verwenden, ihr Azure Storage-Konto sich jedoch nicht hinter dem VNet befindet, wird der Datenverkehr zwischen dem Arbeitsbereich und dem Speicher nicht vom VNet geschützt.
>
> Weitere Informationen zum Sichern von Ressourcen, die von Azure Machine Learning verwendet werden, finden Sie in den folgenden Artikeln:
>
> * [Übersicht zu Isolation und Datenschutz in virtuellen Netzwerken](how-to-network-security-overview.md)
> * [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
> * [Schützen von Trainingsumgebungen](how-to-secure-training-vnet.md)
> * [Schützen von Rückschlussumgebungen](how-to-secure-inferencing-vnet.md)
> * [Verwenden von Azure Machine Learning Studio in einem VNet](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* Sie benötigen ein vorhandenes virtuelles Netzwerk, in dem der private Endpunkt erstellt werden soll. 
* [Sie müssen die privaten Endpunkt-Netzwerkrichtlinien deaktivieren](../private-link/disable-private-endpoint-network-policy.md) bevor sie private Endpunkte hinzufügen.

## <a name="limitations"></a>Einschränkungen

* Wenn öffentlicher Zugriff für einen Arbeitsbereich aktiviert wird, der mit einem privaten Endpunkt abgesichert ist, und Sie Azure Machine Learning Studio über das öffentliche Internet verwenden, können einige Features wie der Designer beim Zugriff auf Ihre Daten fehlschlagen. Dieses Problem tritt auf, wenn die Daten in einem Dienst gespeichert werden, der hinter dem VNet geschützt ist. Dies gilt z. B. für ein Azure Storage-Konto.
* Wenn Sie Mozilla Firefox verwenden, treten möglicherweise Probleme beim Zugriff auf den privaten Endpunkt für Ihren Arbeitsbereich auf. Dieses Problem kann im Zusammenhang mit DNS über HTTPS in Mozilla auftreten. Es wird empfohlen, zur Problemumgehung Microsoft Edge oder Google Chrome zu verwenden.
* Einen privaten Endpunkt zu verwenden hat keine Auswirkungen auf die Azure-Steuerungsebene, d. h. auf Verwaltungsvorgänge wie das Löschen des Arbeitsbereichs oder das Verwalten von Computeressourcen. Beispiele: Erstellen, Aktualisieren oder Löschen eines Computeziels. Diese Vorgänge werden ganz normal über das öffentliche Internet ausgeführt. Bei Vorgängen auf Datenebene, etwa bei der Verwendung von Azure Machine Learning Studio, APIs (einschließlich veröffentlichter Pipelines) oder des SDK, wird der private Endpunkt verwendet.
* Wenn Sie eine Compute-Instanz oder einen Computecluster in einem Arbeitsbereich mit einem privaten Endpunkt erstellen, müssen sich die Compute-Instanz und der Computecluster in derselben Azure-Region wie der Arbeitsbereich befinden.
* Beim Erstellen oder Anfügen eines Azure Kubernetes Service-Clusters an einen Arbeitsbereich mit einem privaten Endpunkt muss sich der Cluster in derselben Region wie der Arbeitsbereich befinden.
* Wenn Sie einen Arbeitsbereich mit mehreren privaten Endpunkten (Vorschau) verwenden, muss sich einer der privaten Endpunkte im selben VNET wie die folgenden Abhängigkeitsdienste befinden:

    * Azure Storage Konto, das den Standardspeicher für den Arbeitsbereich bietet
    * Azure Key Vault für den Arbeitsbereich
    * Azure Container Registry für den Arbeitsbereich

    Beispielsweise würde ein VNET (Dienst-VNET) einen privaten Endpunkt für die Abhängigkeitsdienste und den Arbeitsbereich enthalten. Diese Konfiguration ermöglicht dem Arbeitsbereich die Kommunikation mit den Diensten. Ein anderes VNET („Clients“) kann nur einen privaten Endpunkt für den Arbeitsbereich enthalten und nur für die Kommunikation zwischen Cliententwicklungscomputern und dem Arbeitsbereich verwendet werden.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Erstellen eines Arbeitsbereichs, der einen privaten Endpunkt verwendet

Verwenden Sie eine der folgenden Methoden, um einen Arbeitsbereich mit einem privaten Endpunkt zu erstellen. Jede dieser Methoden __erfordert ein vorhandenes virtuelles Netzwerk__:

> [!TIP]
> Wenn Sie einen Arbeitsbereich, einen privaten Endpunkt und ein virtuelles Netzwerk gleichzeitig erstellen möchten, lesen Sie die Informationen unter [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Das Azure Machine Learning Python SDK bietet die Klasse [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig), die mit [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) verwendet werden kann, um einen Arbeitsbereich mit einem privaten Endpunkt zu erstellen. Diese Klasse erfordert ein vorhandenes virtuelles Netzwerk.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Die Azure CLI-[Erweiterung 1.0 für maschinelles Lernen](reference-azure-machine-learning-cli.md) bietet den Befehl [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create). Mit den folgenden Parametern für diesen Befehl kann ein Arbeitsbereich mit einem privaten Netzwerk erstellt werden, aber er erfordert ein vorhandenes virtuelles Netzwerk:

* `--pe-name`: Der Name des erstellten privaten Endpunkts.
* `--pe-auto-approval`: Gibt an, ob private Endpunktverbindungen mit dem Arbeitsbereich automatisch genehmigt werden sollen.
* `--pe-resource-group`: Die für den privaten Endpunkt zu erstellende Ressourcengruppe. Muss dieselbe Gruppe sein, die auch das virtuelle Netzwerk enthält.
* `--pe-vnet-name`: Das vorhandene virtuelle Netzwerk, in dem der private Endpunkt erstellt werden soll.
* `--pe-subnet-name`: Der Name des Subnetzes, in dem der private Endpunkt erstellt werden soll. Der Standardwert ist `default`.

Diese Parameter gelten zusätzlich zu anderen erforderlichen Parametern für den create-Befehl. Der folgende Befehl erstellt z. B. einen neuen Arbeitsbereich in der Region „USA, Westen“ und verwendet dabei eine vorhandene Ressourcengruppe und ein VNet:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Die Registerkarte __Netzwerk__ im Azure Machine Learning Studio ermöglicht Ihnen die Konfiguration eines privaten Endpunkts. Es erfordert jedoch ein vorhandenes virtuelles Netzwerk. Weitere Informationen finden Sie unter [Erstellen von Arbeitsbereichen im Portal](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Hinzufügen eines privaten Endpunkts zu einem Arbeitsbereich

Verwenden Sie eine der folgenden Methoden, um einem vorhandenen Arbeitsbereich einen privaten Endpunkt hinzuzufügen:

> [!WARNING]
>
> Wenn Sie über vorhandene Computeziele verfügen, die diesem Arbeitsbereich zugeordnet sind und sich nicht hinter dem gleichen virtuellen Netzwerk befinden, in dem der private Endpunkt erstellt wird, funktionieren sie nicht.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen und Methoden finden Sie unter [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) und [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Die Azure CLI-[Erweiterung 1.0 für maschinelles Lernen](reference-azure-machine-learning-cli.md) bietet den Befehl [az ml workspace private-endpoint add](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_add).

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure Machine Learning-Arbeitsbereich im Portal die Option __Private Endpunktverbindungen__ und dann __+ Privater Endpunkt__ aus. Erstellen Sie mithilfe der Felder einen neuen privaten Endpunkt.

* Wählen Sie beim Festlegen von __Region__ die gleiche Region wie für Ihr virtuelles Netzwerk aus. 
* Verwenden Sie beim Auswählen von __Ressourcentyp__ die Option __Microsoft.MachineLearningServices/workspaces__. 
* Legen Sie __Ressource__ auf den Namen des Arbeitsbereichs fest.

Wählen Sie abschließend __Erstellen__ aus, um den privaten Endpunkt zu erstellen.

---

## <a name="remove-a-private-endpoint"></a>Entfernen eines privaten Endpunkts

Verwenden Sie eine der folgenden Methoden, um einen privaten Endpunkt aus einem Arbeitsbereich zu entfernen:

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-), um einen privaten Endpunkt zu entfernen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Die Azure CLI-[Erweiterung 1.0 für maschinelles Lernen](reference-azure-machine-learning-cli.md) bietet den Befehl [az ml workspace private-endpoint delete](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Azure Machine Learning-Arbeitsbereich im Portal die Option __Private Endpunktverbindungen__ und dann den zu entfernenden Endpunkt aus. Wählen Sie abschließend __Entfernen__ aus.

---

## <a name="securely-connect-to-your-workspace"></a>Sicheres Herstellen einer Verbindung mit Ihrem Arbeitsbereich

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="multiple-private-endpoints-preview"></a>Mehrere private Endpunkte (Vorschau)

Als Previewfunktion unterstützt Azure Machine Learning mehrere private Endpunkte für einen Arbeitsbereich. Es werden häufig mehrere private Endpunkte verwendet, wenn Sie unterschiedliche Umgebungen voneinander trennen möchten. Im Folgenden finden Sie einige Szenarios, die mithilfe mehrerer privater Endpunkte aktiviert werden:

* Cliententwicklungsumgebungen in einem separaten VNET.
* Ein Azure Kubernetes Service-Cluster (AKS) in einem separaten VNET.
* Andere Azure-Dienste in einem separaten VNET. Beispielsweise können Azure Synapse und Azure Data Factory ein von Microsoft verwaltetes virtuelles Netzwerk verwenden. In beiden Fällen kann dem verwalteten VNET, das von diesen Diensten verwendet wird, ein privater Endpunkt für den Arbeitsbereich hinzugefügt werden. Weitere Informationen zur Verwendung eines verwalteten virtuellen Netzwerks mit diesen Diensten finden Sie in den folgenden Artikeln:

    * [Verwaltete private Endpunkte in Synapse](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)
    * [Verwaltetes virtuelles Netzwerk in Azure Data Factory](../data-factory/managed-virtual-network-private-endpoint.md)

    > [!IMPORTANT]
    > [Der Schutz vor Datenexfiltration von Synapse](../synapse-analytics/security/workspace-data-exfiltration-protection.md) wird bei der Azure Machine Learning nicht unterstützt.

> [!IMPORTANT]
> Jedes VNet, das einen privaten Endpunkt für den Arbeitsbereich enthält, muss außerdem Zugriff auf das Azure-Storage-Konto, den Azure Key Vault und Azure Container Registry haben, die vom Arbeitsbereich verwendet werden. Beispielsweise können Sie einen privaten Endpunkt für die Dienste in jedem VNet erstellen.

Zum Hinzufügen mehrerer Endpunkte werden die gleichen Schritte wie im Abschnitt [Hinzufügen eines privaten Endpunkts zu einem Arbeitsbereich](#add-a-private-endpoint-to-a-workspace) beschrieben.

### <a name="scenario-isolated-clients"></a>Szenario: Isolierte Clients

Wenn Sie die Entwicklungsclients isolieren möchten, damit sie keinen direkten Zugriff auf die Computeressourcen haben, die von Azure Machine Learning verwendet werden, führen Sie die folgenden Schritte aus:

> [!NOTE]
> Bei diesen Schritten wird davon ausgegangen, dass Sie über einen vorhandenen Arbeitsbereich, Azure Storage, Azure Key Vault und Azure Container Registry verfügen. Jeder dieser Dienste verfügt über einen privaten Endpunkt in einem vorhandenen VNet.

1. Erstellen Sie ein weiteres VNet für Clients. Dieses VNet kann Azure Virtual Machines enthalten, die als Ihre Clients fungieren, oder ein VPN-Gateway, das von lokalen Clients zum Herstellen einer Verbindung mit dem VNet verwendet wird.
1. Fügen Sie einen neuen privaten Endpunkt für das Azure Storage-Konto, Azure Key Vault und die Azure Container Registry Ihres Arbeitsbereichs hinzu. Diese privaten Endpunkte sollten im Client-VNet vorhanden sein.
1. Wenn Sie über zusätzlichen Speicher verfügen, der von Ihrem Arbeitsbereich verwendet wird, fügen Sie einen neuen privaten Endpunkt für diesen Speicher hinzu. Der private Endpunkt sollte im Client-VNet vorhanden sein und die Integration der privaten DNS-Zone aktiviert sein.
1. Fügen Sie Ihrem Arbeitsbereich einen neuen privaten Endpunkt hinzu. Dieser private Endpunkt sollte im Client-VNet vorhanden sein und die Integration der privaten DNS-Zone aktiviert sein.
1. Verwenden Sie die Schritte im Artikel [Verwenden von Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account), um Studio den Zugriff auf die Speicherkonto(en) zu ermöglichen.

Das folgende Diagramm veranschaulicht diese Konfiguration: Das __Workload__-VNet enthält Computes, die vom Arbeitsbereich für das Training und die Bereitstellung erstellt werden. Das __Client__-VNet enthält Clients oder ExpressRoute-/VPN-Clientverbindungen. Beide VNets enthalten private Endpunkte für den Arbeitsbereich, Azure Storage Konto, Azure Key Vault und Azure Container Registry.

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-client.png" alt-text="Diagramm des isolierten Client-VNet":::

### <a name="scenario-isolated-azure-kubernetes-service"></a>Szenario: Isolierte Azure Kubernetes Service

Wenn Sie einen isolierten Azure Kubernetes Service erstellen möchten, der vom Arbeitsbereich verwendet wird, führen Sie die folgenden Schritte aus:

> [!NOTE]
> Bei diesen Schritten wird davon ausgegangen, dass Sie über einen vorhandenen Arbeitsbereich, Azure Storage, Azure Key Vault und Azure Container Registry verfügen. Jeder dieser Dienste verfügt über einen privaten Endpunkt in einem vorhandenen VNet.

1. Erstellen Sie eine Azure Kubernetes Service-Instanz (AKS). Während der Erstellung erstellt AKS ein VNet, das den AKS-Cluster enthält.
1. Fügen Sie einen neuen privaten Endpunkt für das Azure Storage-Konto, Azure Key Vault und die Azure Container Registry Ihres Arbeitsbereichs hinzu. Diese privaten Endpunkte sollten im Client-VNet vorhanden sein.
1. Wenn Sie über einen anderen Speicher verfügen, der von Ihrem Arbeitsbereich verwendet wird, fügen Sie einen neuen privaten Endpunkt für diesen Speicher hinzu. Der private Endpunkt sollte im Client-VNet vorhanden sein und die Integration der privaten DNS-Zone aktiviert sein.
1. Fügen Sie Ihrem Arbeitsbereich einen neuen privaten Endpunkt hinzu. Dieser private Endpunkt sollte im Client-VNet vorhanden sein und die Integration der privaten DNS-Zone aktiviert sein.
1. Fügen Sie den AKS-Cluster zum Azure Machine Learning-Arbeitsbereich hinzu. Weitere Informationen finden Sie unter [Erstellen und Anfügen eines Azure Kubernetes Service-Clusters](how-to-create-attach-kubernetes.md#attach-an-existing-aks-cluster).

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-aks.png" alt-text="Diagramm des isolierten AKS-VNet":::

## <a name="enable-public-access"></a>Aktivieren des öffentlichen Zugriffs

Es kann Situationen geben, in denen Sie jemandem ermöglichen möchten, eine Verbindung mit Ihrem geschützten Arbeitsbereich über einen öffentlichen Endpunkt (anstatt über das VNET) herzustellen. Nachdem Sie einen Arbeitsbereich mit einem privaten Endpunkt konfiguriert haben, können Sie optional den öffentlichen Zugriff auf den Arbeitsbereich aktivieren. Dadurch wird der private Endpunkt nicht entfernt. Die gesamte Kommunikation zwischen Komponenten hinter dem VNET ist weiterhin geschützt. Der öffentliche Zugriff wird nur für den Arbeitsbereich ermöglicht (zusätzlich zum privaten Zugriff über das VNET).

> [!WARNING]
> Beim Herstellen einer Verbindung über den öffentlichen Endpunkt:
> * __Einige Features von Studio können nicht auf Ihre Daten zugreifen__. Dieses Problem tritt auf, wenn die _Daten in einem Dienst gespeichert werden, der hinter dem VNet geschützt ist_. Dies gilt z. B. für ein Azure Storage-Konto. 
> * Die Verwendung von Jupyter, JupyterLab und RStudio auf einer Compute-Instanz, einschließlich der Ausführung von Notebooks, __wird nicht unterstützt__.

Führen Sie die folgenden Schritte aus, um den öffentlichen Zugriff auf einen private Endpunkte nutzenden Arbeitsbereich zu ermöglichen:

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-), um einen privaten Endpunkt zu entfernen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Die Azure CLI-[Erweiterung 1.0 für maschinelles Lernen](reference-azure-machine-learning-cli.md) bietet den Befehl [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update). Fügen Sie zum Aktivieren des öffentlichen Zugriffs auf den Arbeitsbereich den Parameter `--allow-public-access true` hinzu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zurzeit gibt es keine Möglichkeit, diese Funktionalität über das Portal zu aktivieren.

---

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Schützen Ihres Azure Machine Learning-Arbeitsbereichs finden Sie im Artikel [Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md).

* Wenn Sie vorhaben, eine benutzerdefinierte DNS-Lösung in Ihrem virtuellen Netzwerk zu verwenden, finden Sie weitere Informationen unter [Verwenden eines Arbeitsbereichs mit einem benutzerdefinierten DNS-Server](how-to-custom-dns.md).