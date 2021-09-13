---
title: Verwenden von Bicep zum Bereitstellen von Ressourcen in einer Verwaltungsgruppe
description: Beschreibt das Erstellen einer Bicep-Datei, die Ressourcen im Verwaltungsgruppenbereich bereitstellt.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: afa4a0f266eb7720a569df123c9828fd151d21e0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453598"
---
# <a name="management-group-deployments-with-bicep-files"></a>Bereitstellung von Verwaltungsgruppen mit Bicep-Dateien

In diesem Artikel wird beschrieben, wie Sie den Bereich mit Bicep bei der Bereitstellung in einer Verwaltungsgruppe festlegen.

Wenn Ihre Organisation sich weiterentwickelt, können Sie eine Bicep-Datei bereitstellen, um Ressourcen auf Verwaltungsgruppenebene zu erstellen. Beispielsweise müssen Sie möglicherweise [Richtlinien](../../governance/policy/overview.md) oder die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure-RBAC)](../../role-based-access-control/overview.md) für eine Verwaltungsgruppe definieren und zuweisen. Mit Vorlagen auf Verwaltungsgruppenebene können Sie Richtlinien deklarativ anwenden und Rollen auf Verwaltungsgruppenebene zuweisen.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Nicht alle Ressourcentypen können auf Mandantengruppenebene bereitgestellt werden. Im folgenden Abschnitt werden die unterstützten Ressourcentypen aufgelistet.

Verwenden Sie für Azure Blueprints:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Verwenden Sie für Azure Policy Folgendes:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Verwenden Sie für rollenbasierte Zugriffssteuerung von Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Verwenden Sie für geschachtelte Vorlagen, die in Abonnements oder Ressourcengruppen bereitstellen:

* [Bereitstellungen](/azure/templates/microsoft.resources/deployments)

Verwenden Sie für die Verwaltung Ihrer Ressourcen:

* [Tags](/azure/templates/microsoft.resources/tags)

Bei Verwaltungsgruppen handelt es sich um Ressourcen auf Mandantenebene. Sie haben aber die Möglichkeit, Verwaltungsgruppen in einer Verwaltungsgruppenbereitstellung zu erstellen, indem Sie den Bereich der neuen Verwaltungsgruppe auf den Mandanten festlegen. Weitere Informationen finden Sie unter [Verwaltungsgruppe](#management-group).

## <a name="set-scope"></a>Mengenbereich

Verwenden Sie Folgendes, um den Bereich auf die Verwaltungsgruppe festzulegen:

```bicep
targetScope = 'managementGroup'
```

## <a name="deployment-commands"></a>Bereitstellungsbefehle

Verwenden Sie für die Bereitstellung in einer Verwaltungsgruppe die Verwaltungsgruppen-Bereitstellungsbefehle.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie bei der Azure CLI [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie für Azure PowerShell [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Ausführlichere Informationen über Bereitstellungsbefehle und -optionen für die Bereitstellung von ARM-Vorlagen finden Sie in den folgenden Artikeln:

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md)
* [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Bereitstellungsspeicherort und -name

Für Bereitstellungen auf Verwaltungsgruppenebene müssen Sie einen Speicherort für die Bereitstellung angeben. Der Speicherort der Bereitstellung ist vom Speicherort der Ressourcen getrennt, die Sie bereitstellen. Der Bereitstellungsspeicherort gibt an, wo Bereitstellungsdaten gespeichert werden sollen. Die Bereitstellungen von [Abonnement](deploy-to-subscription.md) und [Mandant](deploy-to-tenant.md) benötigen auch einen Speicherort. Für [Ressourcengruppe](deploy-to-resource-group.md)nbereitstellungen wird der Speicherort der Ressourcengruppe zum Speichern der Bereitstellungsdaten verwendet.

Sie können einen Namen für die Bereitstellung angeben oder den Bereitstellungsstandardnamen verwenden. Der Standardname ist der Name der Vorlagendatei. Wenn Sie beispielsweise eine Vorlage mit dem Namen _main.bicep_ bereitstellen, wird der Standardbereitstellungsname **main** erstellt.

Der Speicherort für jeden Bereitstellungsnamen ist unveränderlich. Sie können keine Bereitstellung an einem Speicherort erstellen, wenn bereits eine Bereitstellung mit demselben Namen an einem anderen Speicherort vorhanden ist. Wenn Sie z. B. eine Verwaltungsgruppenbereitstellung mit dem Namen **deployment1** in **centralus** erstellen, können Sie später keine weitere Bereitstellung mit dem Namen **deployment1**, aber einen Speicherort **westus** erstellen. Wenn Sie den Fehlercode `InvalidDeploymentLocation` erhalten, verwenden Sie entweder einen anderen Namen oder denselben Speicherort wie bei der vorherigen Bereitstellung für diesen Namen.

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung in einer Verwaltungsgruppe können Sie Ressourcen an folgenden Orten bereitstellen:

* In der Zielverwaltungsgruppe des Vorgangs
* In einer anderen Verwaltungsgruppe im Mandanten
* In Abonnements in der Verwaltungsgruppe
* In Ressourcengruppen in der Verwaltungsgruppe
* Im Mandanten für die Ressourcengruppe

Für eine [Erweiterungsressource](scope-extension-resources.md) kann der Bereich auf ein Ziel festgelegt werden, das sich vom Bereitstellungsziel unterscheidet.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

### <a name="scope-to-management-group"></a>Bereich: Verwaltungsgruppe

Um Ressourcen für die Zielverwaltungsgruppe bereitzustellen, fügen Sie diese Ressourcen mit dem Schlüsselwort `resource` hinzu.

```bicep
targetScope = 'managementGroup'

// policy definition created in the management group
resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  ...
}
```

Wenn Sie eine andere Verwaltungsgruppe als Bereitstellungsziel verwenden möchten, können Sie ein [Modul](modules.md) hinzufügen. Verwenden Sie die [managementGroup-Funktion](bicep-functions-scope.md#managementgroup), um die Eigenschaft `scope` festzulegen. Geben Sie den Namen der Verwaltungsgruppe an.

```bicep
targetScope = 'managementGroup'

param otherManagementGroupName string

// module deployed at management group level but in a different management group
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentMG'
  scope: managementGroup(otherManagementGroupName)
}
```

### <a name="scope-to-subscription"></a>Bereich: Abonnement

Sie können auch Abonnements innerhalb einer Verwaltungsgruppe als Bereitstellungsziel verwenden. Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

Um ein Abonnement innerhalb der Verwaltungsgruppe als Bereitstellungsziel zu verwenden, fügen Sie ein Modul hinzu. Verwenden Sie die [subscription-Funktion](bicep-functions-scope.md#subscription), um die Eigenschaft `scope` festzulegen. Geben Sie die Abonnement-ID an.

```bicep
targetScope = 'managementGroup'

param subscriptionID string

// module deployed to subscription in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Bereich: Ressourcengruppe

Sie können auch Ressourcengruppen innerhalb der Verwaltungsgruppe als Bereitstellungsziel verwenden. Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

Um eine Ressourcengruppe innerhalb der Verwaltungsgruppe als Bereitstellungsziel zu verwenden, fügen Sie ein Modul hinzu. Verwenden Sie die [resourceGroup-Funktion](bicep-functions-scope.md#resourcegroup), um die Eigenschaft `scope` festzulegen.  Geben Sie die Abonnement-ID und den Namen der Ressourcengruppe an.

```bicep
targetScope = 'managementGroup'

param subscriptionID string
param resourceGroupName string

// module deployed to resource group in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

### <a name="scope-to-tenant"></a>Bereich: Mandant

Zum Erstellen von Ressourcen im Mandanten fügen Sie ein Modul hinzu. Verwenden Sie die [tenant-Funktion](bicep-functions-scope.md#tenant), um die Eigenschaft `scope` festzulegen. Der Benutzer, der die Vorlage bereitstellt, muss über den [erforderlichen Zugriff zum Bereitstellen im Mandanten](deploy-to-tenant.md#required-access) verfügen.

```bicep
targetScope = 'managementGroup'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Alternativ können Sie den Bereich für einige Ressourcentypen wie z. B. Verwaltungsgruppen auf `/` festlegen. Die Erstellung einer neuen Verwaltungsgruppe ist im nächsten Abschnitt beschrieben.

## <a name="management-group"></a>Verwaltungsgruppe

Zum Erstellen einer Verwaltungsgruppe in einer Verwaltungsgruppenbereitstellung müssen Sie den Bereich für die Verwaltungsgruppe auf `/` festlegen.

Im folgenden Beispiel wird in der Stammverwaltungsgruppe eine neue Verwaltungsgruppe erstellt.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output newManagementGroup string = mgName
```

Im nächsten Beispiel wird eine neue Verwaltungsgruppe in der Verwaltungsgruppe erstellt, die als übergeordnetes Element angegeben ist.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'
param parentMGName string

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: parentMG.id
      }
    }
  }
}

resource parentMG 'Microsoft.Management/managementGroups@2020-05-01' existing = {
  name: parentMGName
  scope: tenant()
}

output newManagementGroup string = mgName
```

## <a name="subscriptions"></a>Abonnements

Informationen zur Verwendung einer ARM-Vorlage zum Erstellen eines neuen Azure-Abonnements in einer Verwaltungsgruppe finden Sie unter:

* [Programmgesteuertes Erstellen von Azure Enterprise-Abonnements](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Programmgesteuertes Erstellen von Azure-Abonnements für eine Microsoft-Kundenvereinbarung](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Programmgesteuertes Erstellen von Azure-Abonnements für eine Microsoft Partner-Vereinbarung](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Informationen zum Bereitstellen einer Vorlage, die ein vorhandenes Azure-Abonnement in eine neue Verwaltungsgruppe verschiebt, finden Sie unter [Verschieben von Abonnements in ARM-Vorlagen](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template).

## <a name="azure-policy"></a>Azure Policy

Benutzerdefinierte Richtliniendefinitionen, die für die Verwaltungsgruppe bereitgestellt werden, sind Erweiterungen der Verwaltungsgruppe. Verwenden Sie die Funktion [extensionResourceId()](./bicep-functions-resource.md#extensionresourceid), um die ID einer benutzerdefinierten Richtliniendefinition abzurufen. Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Verwenden Sie die Funktion [tenantResourceId()](./bicep-functions-resource.md#tenantresourceid), um die ID einer integrierten Richtliniendefinition abzurufen.

Im folgenden Beispiel wird veranschaulicht, wie eine Richtlinie auf der Verwaltungsgruppenebene [definiert](../../governance/policy/concepts/definition-structure.md) und zugewiesen wird.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Bereichen finden Sie unter Folgendem:

* [Bereitstellungen von Ressourcengruppen](deploy-to-resource-group.md)
* [Abonnementbereitstellungen](deploy-to-subscription.md)
* [Mandantenbereitstellungen](deploy-to-tenant.md)
