---
title: Erstellen von Arbeitsbereichen mit der Azure CLI
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Machine Learning-Erweiterung für die Azure-Befehlszeilenschnittstelle verwenden, um einen neuen Azure Machine Learning-Arbeitsbereich zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8f98b1d5d020df0b8a2047fa5881c6d978a0ad94
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281739"
---
# <a name="manage-azure-machine-learning-workspaces-using-azure-cli"></a>Verwalten von Azure Machine Learning-Arbeitsbereichen mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Arbeitsbereiche mit der Azure CLI erstellen und verwalten. Die Azure CLI bietet Befehle für die Verwaltung von Azure-Ressourcen und ist so konzipiert, dass Sie schnell mit Azure arbeiten können, wobei der Fokus auf der Automatisierung liegt. Die Erweiterung der CLI für maschinelles Lernen unterstützt Befehle für die Arbeit mit Azure Machine Learning-Ressourcen.

> [!NOTE]
> Die Beispiele in diesem Artikel beziehen sich sowohl auf die Version 1.0 CLI als auch auf die Version 2.0 CLI. Wenn für einen Befehl keine Version angegeben ist, funktioniert er entweder mit der Version 1.0 oder 2.0 der Befehlsschnittstelle. Die CLI für maschinelles Lernen 2.0 befindet sich derzeit in der öffentlichen Vorschau. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

* Um die CLI-Befehle in diesem Dokument aus Ihrer **lokalen Umgebung** zu verwenden, benötigen Sie die [Azure CLI](/cli/azure/install-azure-cli).

    Wenn Sie die [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) verwenden, befindet sich die CLI in der Cloud, und der Zugriff erfolgt über den Browser.

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Herstellen einer Verbindung zwischen der CLI und Ihrem Azure-Abonnement

> [!IMPORTANT]
> Wenn Sie Azure Cloud Shell verwenden, können Sie diesen Abschnitt überspringen. Die Cloud Shell authentifiziert Sie automatisch mit dem Konto, mit dem Sie sich bei Ihrem Azure-Abonnement anmelden.

Ihnen stehen mehrere Möglichkeiten zur Verfügung, sich über die CLI bei Ihrem Azure-Abonnement zu authentifizieren. Am einfachsten ist die interaktive Authentifizierung mithilfe eines Browsers. Öffnen Sie zur interaktiven Authentifizierung eine Befehlszeile oder ein Terminal, und verwenden Sie den folgenden Befehl:

```azurecli-interactive
az login
```

Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Anmeldeseite. Andernfalls müssen Sie einen Browser öffnen und die Anweisungen in der Befehlszeile befolgen. Die Anweisungen umfassen das Navigieren zu [https://aka.ms/devicelogin](https://aka.ms/devicelogin) und Eingeben eines Autorisierungscodes.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Andere Methoden zur Authentifizierung finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Der Azure Machine Learning-Arbeitsbereich muss innerhalb einer Ressourcengruppe erstellt werden. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Um __eine neue Ressourcengruppe zu erstellen__, verwenden Sie den folgenden Befehl. Ersetzen Sie `<resource-group-name>` durch den Namen, der für diese Ressourcengruppe verwendet werden soll. Ersetzen Sie `<location>` durch die Azure-Region, die für diese Ressourcengruppe verwendet werden soll:

> [!NOTE]
> Wählen Sie eine Region aus, in der Azure Machine Learning verfügbar ist. Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Die Antwort dieses Befehls ähnelt dem folgenden JSON-Code. Sie können die Ausgabewerte verwenden, um die erstellten Ressourcen zu lokalisieren oder sie als Eingabe für nachfolgende CLI-Schritte zur Automatisierung zu analysieren.

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Weitere Informationen zum Arbeiten mit Ressourcengruppen finden Sie unter [az group](/cli/azure/group).

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Wenn Sie einen Azure Machine Learning-Arbeitsbereich bereitstellen, [werden verschiedene andere Dienste als abhängige zugeordnete Ressourcen benötigt](./concept-workspace.md#resources). Wenn Sie die Befehlszeilenschnittstelle verwenden, um den Arbeitsbereich zu erstellen, kann die Befehlszeilenschnittstelle entweder neue zugehörige Ressourcen in Ihrem Namen erstellen oder Sie können vorhandene Ressourcen anfügen.

> [!IMPORTANT]
> Wenn Sie Ihr eigenes Speicherkonto anfügen, stellen Sie sicher, dass es die folgenden Kriterien erfüllt:
>
> * Das Speicherkonto ist _kein_ Premium-Konto (Premium_LRS oder Premium_GRS).
> * Sowohl Azure Blob- als auch Azure-Dateifunktionen sind aktiviert.
> * „Hierarchischer Namespace“ (ADLS Gen 2) ist deaktiviert. Diese Anforderungen gelten nur für das vom Arbeitsbereich verwendete _standardmäßige_ Speicherkonto.
>
> Wenn Sie die Azure-Container-Registrierung anhängen, müssen Sie das [Administratorkonto](../container-registry/container-registry-authentication.md#admin-account) aktiviert haben, bevor es mit einem Azure Machine Learning-Arbeitsbereich verwendet werden kann.

# <a name="create-with-new-resources"></a>[Erstellen mit neuen Ressourcen](#tab/createnewresources)

Um einen neuen Arbeitsbereich zu erstellen, in dem die __Dienste automatisch erstellt werden__, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

# <a name="bring-existing-resources-10-cli"></a>[Verwenden vorhandener Ressourcen (CLI 1.0)](#tab/bringexistingresources1)
Um einen Arbeitsbereich zu erstellen, der vorhandene Ressourcen verwendet, müssen Sie die Ressourcen-ID für die einzelnen Ressourcen bereitstellen. Sie können diese ID entweder über die Registerkarte „Eigenschaften“ der einzelnen Ressourcen über das Azure-Portal abrufen oder mithilfe der Azure CLI die folgenden Befehle ausführen.

  * **Azure Storage-Konto**:     `az storage account show --name <storage-account-name> --query "id"`
  * **Azure Application Insights**:     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
  * **Azure Key Vault**:     `az keyvault show --name <key-vault-name> --query "ID"`
  * **Azure Container Registry**:     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

  Die zurückgegebene Ressourcen-ID hat das folgende Format: `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`.

Sobald Sie über die IDs für die Ressourcen verfügen, die Sie mit dem Arbeitsbereich verwenden möchten, verwenden Sie den grundlegenden `az workspace create -w <workspace-name> -g <resource-group-name>`-Befehl, und fügen Sie die Parameter und IDs für die vorhandenen Ressourcen hinzu. Beispielsweise wird mit dem folgenden Befehl ein Arbeitsbereich erstellt, in dem eine vorhandene Containerregistrierung verwendet wird:

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

# <a name="bring-existing-resources-20-cli---preview"></a>[Vorhandene Ressourcen übernehmen (2.0 CLI - Vorschau)](#tab/bringexistingresources2)

Um einen neuen Arbeitsbereich zu erstellen und dabei vorhandene zugehörige Ressourcen mithilfe der CLI zu verwenden, müssen Sie zunächst in einer Konfigurationsdatei festlegen, wie Ihr Arbeitsbereich konfiguriert werden soll.

:::code language="YAML" source="~/azureml-examples-main/cli/resources/workspace/with-existing-resources.yml":::

Dann können Sie auf diese Konfigurationsdatei als Teil des CLI-Befehls zur Erstellung des Arbeitsbereichs verweisen.

```azurecli-interactive
az ml workspace create -g <resource-group-name> --file workspace.yml
```

Wenn Sie vorhandene Ressourcen anfügen, müssen Sie die ID für die Ressourcen angeben. Sie können diese ID entweder über die Registerkarte „Eigenschaften“ der einzelnen Ressourcen im Azure-Portal abrufen oder mithilfe der Azure CLI die folgenden Befehle ausführen.

* **Azure Storage-Konto**:     `az storage account show --name <storage-account-name> --query "id"`
* **Azure Application Insights**:     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
* **Azure Key Vault**:     `az keyvault show --name <key-vault-name> --query "ID"`
* **Azure Container Registry**:     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

Der Wert der Ressourcen-ID sieht ähnlich aus wie der folgende: `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`.

---

> [!IMPORTANT]
> Wenn Sie vorhandene Ressourcen anfügen, müssen Sie nicht alle angeben. Sie können eine oder mehrere angeben. Beispielsweise können Sie ein vorhandenes Speicherkonto angeben, und der Arbeitsbereich erstellt die sonstigen Ressourcen.

Die Ausgabe des Befehls zur Erstellung des Arbeitsbereichs sieht ähnlich aus wie der folgende JSON-Code. Sie können die Ausgabewerte verwenden, um die erstellten Ressourcen zu lokalisieren oder sie als Eingabe für nachfolgende CLI-Schritte zu analysieren.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}

```

## <a name="advanced-configurations"></a>Erweiterte Konfigurationen
### <a name="configure-workspace-for-private-network-connectivity"></a>Konfigurieren des Arbeitsbereichs für private Netzwerkkonnektivität

Abhängig von Ihrem Anwendungsfall und Ihren organisatorischen Anforderungen können Sie Azure Machine Learning mithilfe einer privaten Netzwerkkonnektivität konfigurieren. Sie können die Azure CLI verwenden, um einen Arbeitsbereich und einen Private Link-Endpunkt für die Arbeitsbereichsressource bereitzustellen. Weitere Informationen zur Verwendung eines privaten Endpunkts und eines virtuellen Netzwerks (VNet) mit Ihrem Arbeitsbereich finden Sie unter [Übersicht über Isolierung und Datenschutz im virtuellen Netzwerk](how-to-network-security-overview.md). Für komplexe Ressourcenkonfigurationen beachten Sie auch die vorlagenbasierten Bereitstellungsoptionen einschließlich [Azure Resource Manager](how-to-create-workspace-template.md).

# <a name="10-cli"></a>[1.0 CLI](#tab/vnetpleconfigurationsv1cli)

Wenn Sie den Zugriff auf Ihren Arbeitsbereich auf ein virtuelles Netzwerk beschränken möchten, können Sie die folgenden Parameter als Teil des `az ml workspace create`-Befehls oder die `az ml workspace private-endpoint`-Befehle verwenden.

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --pe-name "<pe name>"
                       --pe-auto-approval "<pe-autoapproval>"
                       --pe-resource-group "<pe name>"
                       --pe-vnet-name "<pe name>"
                       --pe-subnet-name "<pe name>"
```

* `--pe-name`: Der Name des erstellten privaten Endpunkts.
* `--pe-auto-approval`: Gibt an, ob private Endpunktverbindungen mit dem Arbeitsbereich automatisch genehmigt werden sollen.
* `--pe-resource-group`: Die für den privaten Endpunkt zu erstellende Ressourcengruppe. Muss dieselbe Gruppe sein, die auch das virtuelle Netzwerk enthält.
* `--pe-vnet-name`: Das vorhandene virtuelle Netzwerk, in dem der private Endpunkt erstellt werden soll.
* `--pe-subnet-name`: Der Name des Subnetzes, in dem der private Endpunkt erstellt werden soll. Der Standardwert ist `default`.

Weitere Einzelheiten zur Verwendung dieser Befehle finden Sie auf den [CLI-Referenzseiten](/cli/azure/ml(v1)/workspace).

# <a name="20-cli---preview"></a>[2.0 CLI - Vorschau](#tab/vnetpleconfigurationsv2cli)

Wenn Sie einen privaten Link verwenden, kann Ihr Arbeitsbereich keine Azure Container Registry-Aufgaben für die Image-Erstellung verwenden. Daher müssen Sie die Eigenschaft image_build_compute auf den Namen eines CPU-Compute-Clusters setzen, der für die Erstellung der Docker-Image-Umgebung verwendet werden soll. Mit der Eigenschaft public_network_access können Sie außerdem festlegen, ob der private Link-Arbeitsbereich über das Internet zugänglich sein soll.

:::code language="YAML" source="~/azureml-examples-main/cli/resources/workspace/privatelink.yml":::

```azurecli-interactive
az ml workspace create -g <resource-group-name> --file privatelink.yml
```

Nachdem Sie den Arbeitsbereich erstellt haben, verwenden Sie die [Azure Networking CLI-Befehle](/cli/azure/network/private-endpoint#az_network_private_endpoint_create), um einen privaten Link-Endpunkt für den Arbeitsbereich zu erstellen.

```azurecli-interactive
az network private-endpoint create \
    --name <private-endpoint-name> \
    --vnet-name <vnet-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id "/subscriptions/<subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>" \
    --group-id amlworkspace \
    --connection-name workspace -l <location>
```

Um die privaten DNS-Zoneneinträge für den Arbeitsbereich zu erstellen, verwenden Sie die folgenden Befehle:

```azurecli-interactive
# Add privatelink.api.azureml.ms
az network private-dns zone create \
    -g <resource-group-name> \
    --name 'privatelink.api.azureml.ms'

az network private-dns link vnet create \
    -g <resource-group-name> \
    --zone-name 'privatelink.api.azureml.ms' \
    --name <link-name> \
    --virtual-network <vnet-name> \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
    -g <resource-group-name> \
    --endpoint-name <private-endpoint-name> \
    --name myzonegroup \
    --private-dns-zone 'privatelink.api.azureml.ms' \
    --zone-name 'privatelink.api.azureml.ms'

# Add privatelink.notebooks.azure.net
az network private-dns zone create \
    -g <resource-group-name> \
    --name 'privatelink.notebooks.azure.net'

az network private-dns link vnet create \
    -g <resource-group-name> \
    --zone-name 'privatelink.notebooks.azure.net' \
    --name <link-name> \
    --virtual-network <vnet-name> \
    --registration-enabled false

az network private-endpoint dns-zone-group add \
    -g <resource-group-name> \
    --endpoint-name <private-endpoint-name> \
    --name myzonegroup \
    --private-dns-zone 'privatelink.notebooks.azure.net' \
    --zone-name 'privatelink.notebooks.azure.net'
```

---

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Kundenseitig verwalteter Schlüssel und Arbeitsbereich mit starken geschäftlichen Auswirkungen

Standardmäßig werden Metadaten für den Arbeitsbereich auf einer Azure Cosmos DB-Instanz gespeichert, die von Microsoft verwaltet wird. Diese Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt. Anstatt den von Microsoft verwalteten Schlüssel zu verwenden, können Sie auch Ihren eigenen Schlüssel bereitstellen. Dadurch wird ein zusätzlicher Satz von Ressourcen in Ihrem Azure-Abonnement erstellt, um Ihre Daten zu speichern.

Weitere Informationen über die Ressourcen, die erstellt werden, wenn Sie Ihren eigenen Schlüssel für die Verschlüsselung verwenden, finden Sie unter [Datenverschlüsselung mit Azure Machine Learning](./concept-data-encryption.md#azure-cosmos-db).

Die folgenden CLI-Befehle enthalten Beispiele für die Erstellung eines Arbeitsbereichs, der vom Kunden verwaltete Schlüssel für die Verschlüsselung mit den Versionen 1.0 CLI und 2.0 CLI verwendet.

# <a name="10-cli"></a>[1.0 CLI](#tab/vnetpleconfigurationsv1cli)

Verwenden Sie den `--cmk-keyvault`-Parameter, um den Azure Key Vault anzugeben, der den Schlüssel enthält, und `--resource-cmk-uri`, um die Ressourcen-ID und den URI des Schlüssels innerhalb des Tresors anzugeben.

Um die [von Microsoft in Ihrem Arbeitsbereich erfassten Daten einzuschränken](./concept-data-encryption.md#encryption-at-rest), können Sie zusätzlich den `--hbi-workspace`-Parameter angeben. 

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --cmk-keyvault "<cmk keyvault name>"
                       --resource-cmk-uri "<resource cmk uri>"
                       --hbi-workspace
```

# <a name="20-cli---preview"></a>[2.0 CLI - Vorschau](#tab/vnetpleconfigurationsv2cli)

Verwenden Sie den `customer_managed_key`-Parameter, der die Parameter `key_vault` und `key_uri` enthält, um die Ressourcen-ID und den URI des Schlüssels im Tresor anzugeben.

Um die [von Microsoft in Ihrem Arbeitsbereich erfassten Daten einzuschränken](./concept-data-encryption.md#encryption-at-rest), können Sie zusätzlich die `hbi_workspace`-Eigenschaft angeben. 

:::code language="YAML" source="~/azureml-examples-main/cli/resources/workspace/cmk.yml":::

Dann können Sie auf diese Konfigurationsdatei als Teil des CLI-Befehls zur Erstellung des Arbeitsbereichs verweisen.

```azurecli-interactive
az ml workspace create -g <resource-group-name> --file cmk.yml
```
---

> [!NOTE]
> Autorisieren Sie die __Machine Learning-App__ (in der Identitäts- und Zugriffsverwaltung) mit den Berechtigungen für Mitwirkende in Ihrem Abonnement, um die Datenverschlüsselung für weitere Ressourcen zu verwalten.

> [!NOTE]
> Azure Cosmos DB wird __nicht__ verwendet, um Daten zur Modellleistung oder bei Experimenten oder Ihren Modellimplementierungen protokollierte Informationen zu speichern. Weitere Informationen zur Überwachung dieser Elemente finden Sie im Abschnitt [Überwachung und Protokollierung](concept-azure-machine-learning-architecture.md) des Artikels zur Architektur und zu den Konzepten.

> [!IMPORTANT]
> Die Auswahl von „starken geschäftlichen Auswirkungen“ kann nur beim Erstellen eines Arbeitsbereichs erfolgen. Diese Einstellung kann nach dem Erstellen des Arbeitsbereichs nicht mehr geändert werden.

Weitere Informationen zu kundenseitig verwalteten Schlüsseln und Arbeitsbereichen mit starken geschäftlichen Auswirkungen finden Sie unter [Enterprise-Sicherheit für Machine Learning](concept-data-encryption.md#encryption-at-rest).

## <a name="using-the-cli-to-manage-workspaces"></a>Verwenden der CLI zum Verwalten von Arbeitsbereichen

### <a name="get-workspace-information"></a>Abrufen von Informationen zum Arbeitsbereich

Um Informationen zu einem Arbeitsbereich zu erhalten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace show](/cli/azure/ml/workspace#az_ml_workspace_show).

### <a name="update-a-workspace"></a>Aktualisieren eines Arbeitsbereichs

Verwenden Sie den folgenden Befehl, um einen Arbeitsbereich zu aktualisieren:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update).

### <a name="sync-keys-for-dependent-resources"></a>Synchronisieren von Schlüsseln für abhängige Ressourcen

Wenn Sie Zugriffsschlüssel für eine der Ressourcen ändern, die von Ihrem Arbeitsbereich verwendet werden, verwenden Sie den folgenden Befehl, um die neuen Schlüssel mit dem Arbeitsbereich zu synchronisieren. Wenn Sie erzwingen möchten, dass der Arbeitsbereich die neuen Schlüssel sofort synchronisiert, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Weitere Informationen zum Ändern von Schlüsseln finden Sie unter [Erneutes Generieren von Speicherzugriffsschlüsseln](how-to-change-storage-access-key.md).

Weitere Informationen über den Befehl „sync-keys“ finden Sie unter [az ml workspace sync-keys](/cli/azure/ml/workspace#az_ml_workspace_sync-keys).

### <a name="delete-a-workspace"></a>Löschen eines Arbeitsbereichs

[!INCLUDE [machine-learning-delete-workspace](../../includes/machine-learning-delete-workspace.md)]

Um einen Arbeitsbereich zu löschen, wenn er nicht mehr benötigt wird, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Beim Löschen eines Arbeitsbereichs werden Application Insights, Speicherkonto, Schlüsseltresor oder Containerregistrierung, die vom Arbeitsbereich verwendet werden, nicht gelöscht.

Sie können auch die Ressourcengruppe löschen, wodurch der Arbeitsbereich und alle anderen Azure-Ressourcen in der Ressourcengruppe gelöscht werden. Um die Ressourcengruppe zu löschen, verwenden Sie folgenden Befehl:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace delete](/cli/azure/ml/workspace#az_ml_workspace_delete).

Wenn Sie Ihren Arbeitsbereich versehentlich gelöscht haben, können Sie Ihre Notizbücher trotzdem wiederherstellen. Weitere Informationen finden Sie in [dieser Dokumentation](/azure/machine-learning/how-to-high-availability-machine-learning#workspace-deletion).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Verschieben des Arbeitsbereichs

> [!WARNING]
> Das Verschieben des Azure Machine Learning-Arbeitsbereichs in ein anderes Abonnement oder das Verschieben des besitzenden Abonnements in einen neuen Mandanten wird nicht unterstützt. Andernfalls können Fehler auftreten.

### <a name="deleting-the-azure-container-registry"></a>Löschen der Azure Container Registry

Der Azure Machine Learning-Arbeitsbereich verwendet für einige Operationen die Azure Container Registry (ACR). Es wird automatisch eine ACR-Instanz erstellt, wenn erstmals eine erforderlich ist.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI-Erweiterung für maschinelles Lernen finden Sie in der Dokumentation zu [az ml](/cli/azure/ml).
