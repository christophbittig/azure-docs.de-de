---
title: Konfigurieren eines privaten Endpunkts
titleSuffix: Azure Machine Learning
description: Verwenden Sie einen privaten Endpunkt, um sicher von einem virtuellen Netzwerk aus auf Ihren Azure Machine Learning-Arbeitsbereich zuzugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2021
ms.openlocfilehash: 1c07e96a82814e59c635a592313e461d06a6fcc3
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217465"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Konfigurieren eines privaten Endpunkts für einen Azure Machine Learning-Arbeitsbereich

Hier erfahren Sie, wie Sie einen privaten Endpunkt für Ihren Azure Machine Learning-Arbeitsbereich konfigurieren. Informationen zum Erstellen eines virtuellen Netzwerks für Azure Machine Learning finden Sie unter [Übersicht über die Isolation virtueller Netzwerke und Datenschutz](how-to-network-security-overview.md).

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

* Sie benötigen ein vorhandenes virtuelles Netzwerk, in dem der private Endpunkt erstellt werden soll. Außerdem müssen Sie vor dem Hinzufügen des privaten Endpunkts [Netzwerkrichtlinien für private Endpunkte deaktivieren](../private-link/disable-private-endpoint-network-policy.md).

## <a name="limitations"></a>Einschränkungen

* Die Verwendung eines Azure Machine Learning-Arbeitsbereichs mit einem privaten Endpunkt ist in den Regionen vom Typ „Azure Government“ nicht verfügbar.
* Wenn öffentlicher Zugriff für einen Arbeitsbereich aktiviert wird, der mit einem privaten Endpunkt abgesichert ist, und Sie Azure Machine Learning Studio über das öffentliche Internet verwenden, können einige Features wie der Designer beim Zugriff auf Ihre Daten fehlschlagen. Dieses Problem tritt auf, wenn die Daten in einem Dienst gespeichert werden, der hinter dem VNet geschützt ist. Dies gilt z. B. für ein Azure Storage-Konto.
* Wenn Sie Mozilla Firefox verwenden, treten möglicherweise Probleme beim Zugriff auf den privaten Endpunkt für Ihren Arbeitsbereich auf. Dieses Problem kann im Zusammenhang mit DNS über HTTPS in Mozilla auftreten. Es wird empfohlen, zur Problemumgehung Microsoft Edge oder Google Chrome zu verwenden.
* Einen privaten Endpunkt zu verwenden hat keine Auswirkungen auf die Azure-Steuerungsebene, d. h. auf Verwaltungsvorgänge wie das Löschen des Arbeitsbereichs oder das Verwalten von Computeressourcen. Beispiele: Erstellen, Aktualisieren oder Löschen eines Computeziels. Diese Vorgänge werden ganz normal über das öffentliche Internet ausgeführt. Bei Vorgängen auf Datenebene, etwa bei der Verwendung von Azure Machine Learning Studio, APIs (einschließlich veröffentlichter Pipelines) oder des SDK, wird der private Endpunkt verwendet.

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

## <a name="using-a-workspace-over-a-private-endpoint"></a>Verwenden eines Arbeitsbereichs über einen privaten Endpunkt

Da die Kommunikation mit dem Arbeitsbereich nur aus dem virtuellen Netzwerk zulässig ist, müssen alle Entwicklungsumgebungen, in denen der Arbeitsbereich verwendet wird, Mitglieder des virtuellen Netzwerks sein. Beispiel: ein virtueller Computer im virtuellen Netzwerk.

> [!IMPORTANT]
> Um eine vorübergehende Unterbrechung der Konnektivität zu vermeiden, empfiehlt Microsoft, den DNS-Cache auf Computern, die eine Verbindung mit dem Arbeitsbereich herstellen, nach dem Aktivieren eines privaten Endpunkts zu leeren. 

Weitere Informationen zu Azure Virtual Machines finden Sie in der [Dokumentation zu Virtual Machines](../virtual-machines/index.yml).

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
