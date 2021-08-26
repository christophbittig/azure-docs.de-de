---
title: Bereitstellen von Ressourcen in Ressourcengruppen mithilfe von Bicep
description: Hier wird erläutert, wie Ressourcen in einer Bicep-Datei bereitgestellt werden. Es wird gezeigt, wie Sie mehrere Ressourcengruppen als Ziel festlegen.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 4d68d0d28763c21574a3fd4f2f4c57561759e51e
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634476"
---
# <a name="resource-group-deployments-with-bicep-files"></a>Bereitstellung von Ressourcengruppen mit Bicep-Dateien

In diesem Artikel wird beschrieben, wie Sie bei der Bereitstellung in einer Ressourcengruppe den Bereich mit Bicep festlegen.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Die meisten Ressourcen können in einer Ressourcengruppe bereitgestellt werden. Eine Liste der verfügbaren Ressourcen finden Sie unter [Definieren von Ressourcen in Azure Resource Manager-Vorlagen](/azure/templates).

## <a name="set-scope"></a>Mengenbereich

Der Bereich einer Bicep-Datei wird standardmäßig auf die Ressourcengruppe festgelegt. Wenn Sie den Bereich explizit festlegen möchten, verwenden Sie Folgendes:

```bicep
targetScope = 'resourceGroup'
```

Das Festlegen des Zielbereichs auf eine Ressourcengruppe ist jedoch nicht erforderlich, da dieser Bereich standardmäßig verwendet wird.

## <a name="deployment-commands"></a>Bereitstellungsbefehle

Verwenden Sie für die Bereitstellung in einer Ressourcengruppe die Ressourcengruppen-Bereitstellungsbefehle.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie für die Azure CLI [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create). Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-file main.bicep \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie als Bereitstellungsbefehl in PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile main.bicep `
  -storageAccountType Standard_GRS `
```

---

Ausführlichere Informationen über Bereitstellungsbefehle und -optionen für die Bereitstellung von ARM-Vorlagen finden Sie in den folgenden Artikeln:

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md)
* [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung in einer Ressourcengruppe können Sie Ressourcen an folgenden Orten bereitstellen:

* in der Zielressourcengruppe für den Bereitstellungsvorgang
* In anderen Ressourcengruppen im selben Abonnement oder in anderen Abonnements
* in einem beliebigen Abonnement im Mandanten
* im Mandanten für die Ressourcengruppe

Für eine [Erweiterungsressource](scope-extension-resources.md) kann der Bereich auf ein Ziel festgelegt werden, das sich vom Bereitstellungsziel unterscheidet.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

In diesem Abschnitt wird das Festlegen verschiedener Bereiche veranschaulicht. Sie können diese verschiedenen Bereiche in einer Vorlage kombinieren.

### <a name="scope-to-target-resource-group"></a>Bereich: Zielressourcengruppe

Um Ressourcen für die Zielressourcengruppe bereitzustellen, fügen Sie diese Ressourcen der Bicep-Datei hinzu.

```bicep
// resource deployed to target resource group
resource exampleResource 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

Eine Beispielvorlage finden Sie unter [Bereitstellen in der Zielressourcengruppe](#deploy-to-target-resource-group).

### <a name="scope-to-different-resource-group"></a>Bereich: andere Ressourcengruppe

Wenn Sie Ressourcen in einer anderen Ressourcengruppe als der Zielressourcengruppe bereitstellen möchten, fügen Sie ein [Modul](modules.md) hinzu. Verwenden Sie die [resourceGroup-Funktion](bicep-functions-scope.md#resourcegroup), um die Eigenschaft `scope` für dieses Modul festzulegen.

Wenn sich die Ressourcengruppe in einem anderen Abonnement befindet, geben Sie die Abonnement-ID und den Namen der Ressourcengruppe an. Wenn sich die Ressourcengruppe im selben Abonnement wie die aktuelle Bereitstellung befindet, geben Sie nur den Namen der Ressourcengruppe an. Wenn Sie kein Abonnement in der [resourceGroup-Funktion](bicep-functions-scope.md#resourcegroup) angeben, wird das aktuelle Abonnement verwendet.

Das folgende Beispiel zeigt ein Modul, das auf eine Ressourcengruppe in einem anderen Abonnement ausgerichtet ist.

```bicep
param otherResourceGroup string
param otherSubscriptionID string

// module deployed to different subscription and resource group
module exampleModule 'module.bicep' = {
  name: 'otherSubAndRG'
  scope: resourceGroup(otherSubscriptionID, otherResourceGroup)
}
```

Das nächste Beispiel zeigt ein Modul, das auf eine Ressourcengruppe im selben Abonnement ausgerichtet ist.

```bicep
param otherResourceGroup string

// module deployed to resource group in the same subscription
module exampleModule 'module.bicep' = {
  name: 'otherRG'
  scope: resourceGroup(otherResourceGroup)
}
```

Eine Beispielvorlage finden Sie unter [Bereitstellen in mehreren Ressourcengruppen](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Bereich: Abonnement

Fügen Sie zum Bereitstellen von Ressourcen zu einem Abonnement ein Modul hinzu. Verwenden Sie die [subscription-Funktion](bicep-functions-scope.md#subscription), um die Eigenschaft `scope` festzulegen.

Verwenden Sie zum Bereitstellen im aktuellen Abonnement die subscription-Funktion ohne Parameter.

```bicep

// module deployed at subscription level
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

Geben Sie zum Bereitstellen in einem anderen Abonnement diese Abonnement-ID als Parameter in der subscription-Funktion an.

```bicep
param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(otherSubscriptionID)
}
```

Eine Beispielvorlage finden Sie unter [Erstellen der Ressourcengruppe](#create-resource-group).

### <a name="scope-to-tenant"></a>Bereich: Mandant

Zum Erstellen von Ressourcen im Mandanten fügen Sie ein Modul hinzu. Verwenden Sie die [tenant-Funktion](bicep-functions-scope.md#tenant), um die Eigenschaft `scope` festzulegen.

Der Benutzer, der die Vorlage bereitstellt, muss über den [erforderlichen Zugriff zum Bereitstellen im Mandanten](deploy-to-tenant.md#required-access) verfügen.

Das folgende Beispiel enthält ein Modul, das im Mandanten bereitgestellt wird.

```bicep
// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Anstatt ein Modul zu verwenden, können Sie für einige Ressourcentypen den Bereich auf `tenant()` festlegen. Im folgenden Beispiel wird eine Verwaltungsgruppe im Mandanten bereitgestellt.

```bicep
param mgName string = 'mg-${uniqueString(newGuid())}'

// ManagementGroup deployed at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

Weitere Informationen finden Sie unter [Verwaltungsgruppe](deploy-to-management-group.md#management-group).

## <a name="deploy-to-target-resource-group"></a>Bereitstellen in der Zielressourcengruppe

Zum Bereitstellen von Ressourcen in der Zielressourcengruppe definieren Sie die Ressourcen im Abschnitt `resources` der Vorlage. Mit der folgenden Vorlage wird ein Speicherkonto in der Ressourcengruppe erstellt, die im Bereitstellungsvorgang angegeben ist.

:::code language="bicep" source="~/azure-docs-bicep-samples/get-started-with-bicep-files/add-output/azuredeploy.bicep":::

## <a name="deploy-to-multiple-resource-groups"></a>Bereitstellen in mehreren Ressourcengruppen

Sie können in einer einzelnen Bicep-Datei Ressourcen in mehr als einer Ressourcengruppe bereitstellen.

> [!NOTE]
> Sie können mit einer einzigen Bereitstellung in **800 Ressourcengruppen** bereitstellen. Normalerweise bedeutet diese Einschränkung, dass Sie die Bereitstellung für eine Ressourcengruppe durchführen können, die für die übergeordnete Vorlage angegeben ist, sowie für bis zu 799 Ressourcengruppen in geschachtelten oder verknüpften Bereitstellungen. Wenn Ihre übergeordnete Vorlage aber nur geschachtelte oder verknüpfte Vorlagen enthält und selbst keine Ressourcen bereitstellt, können Sie bis zu 800 Ressourcengruppen in geschachtelte oder verknüpfte Bereitstellungen einbinden.

Im folgenden Beispiel werden zwei Speicherkonten bereitgestellt. Das erste Speicherkonto wird in der Ressourcengruppe bereitgestellt, die Sie im Bereitstellungsvorgang angegeben haben. Das zweite Speicherkonto wird in der Ressourcengruppe bereitgestellt, die in den Parametern `secondResourceGroup` und `secondSubscriptionID` angegeben wurde:

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondStorageLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
var secondStorageName = '${storagePrefix}${uniqueString(secondSubscriptionID, secondResourceGroup)}'

module firstStorageAcct 'storage.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

module secondStorageAcct 'storage.bicep' = {
  name: 'storageModule2'
  scope: resourceGroup(secondSubscriptionID, secondResourceGroup)
  params: {
    storageLocation: secondStorageLocation
    storageName: secondStorageName
  }
}
```

Beide Module verwenden dieselbe Bicep-Datei mit dem Namen **storage.bicep**.

```bicep
param storageLocation string
param storageName string

resource storageAcct 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  location: storageLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Sie können von der Bereitstellung einer Ressourcengruppe auf Abonnementebene wechseln und eine Ressourcengruppe erstellen. Die folgende Vorlage stellt ein Speicherkonto in der Zielressourcengruppe bereit und erstellt eine neue Ressourcengruppe im angegebenen Abonnement.

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

// resource deployed to target resource group
module firstStorageAcct 'storage2.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

// module deployed to subscription
module newRG 'resourceGroup.bicep' = {
  name: 'newResourceGroup'
  scope: subscription(secondSubscriptionID)
  params: {
    resourceGroupName: secondResourceGroup
    resourceGroupLocation: secondLocation
  }
}
```

Im vorherigen Beispiel wird die folgende Bicep-Datei für das Modul verwendet, das die neue Ressourcengruppe erstellt.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Bereichen finden Sie unter folgenden Links:

* [Abonnementbereitstellungen mit Bicep-Dateien](deploy-to-subscription.md)
* [Verwaltungsgruppenbereitstellungen mit Bicep-Dateien](deploy-to-management-group.md)
* [Mandantenbereitstellungen mit Bicep-Datei](deploy-to-tenant.md)
