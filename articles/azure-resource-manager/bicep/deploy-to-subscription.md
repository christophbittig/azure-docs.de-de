---
title: Verwenden von Bicep zum Bereitstellen von Ressourcen in einem Abonnement
description: Hier wird das Erstellen einer Bicep-Datei beschrieben, die Ressourcen im Bereich des Azure-Abonnements bereitstellt. Es wird gezeigt, wie Sie eine Ressourcengruppe erstellen.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: a683e0c46547be3ecb48a20a88f9a95a5b6c8ef8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453424"
---
# <a name="subscription-deployments-with-bicep-files"></a>Abonnementbereitstellungen mit Bicep-Dateien

In diesem Artikel wird beschrieben, wie Sie bei der Bereitstellung in einem Abonnement den Bereich mit Bicep festlegen.

Um die Verwaltung von Ressourcen zu vereinfachen, können Sie Ressourcen auf der Ebene Ihres Azure-Abonnements bereitstellen. Beispielsweise können Sie [Richtlinien](../../governance/policy/overview.md) und [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) für Ihr Abonnement bereitstellen, wodurch diese dann in Ihrem gesamten Abonnement angewendet wird. Außerdem können Sie Ressourcengruppen innerhalb des Abonnements erstellen und Ressourcen in Ressourcengruppen im Abonnement bereitstellen.

> [!NOTE]
> In einer Bereitstellung auf Abonnementebene können Sie in 800 verschiedenen Ressourcengruppen bereitstellen.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Nicht alle Ressourcentypen können auf Abonnementebene bereitgestellt werden. Im folgenden Abschnitt werden die unterstützten Ressourcentypen aufgelistet.

Verwenden Sie für Azure Blueprints:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Versionen (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Verwenden Sie für Azure-Richtlinien:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Verwenden Sie für rollenbasierte Zugriffssteuerung von Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Verwenden Sie für geschachtelte Vorlagen, die in Ressourcengruppen bereitstellen:

* [Bereitstellungen](/azure/templates/microsoft.resources/deployments)

Verwenden Sie zum Erstellen neuer Ressourcengruppen:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Verwenden Sie zum Verwalten Ihres Abonnements:

* [Advisor-Konfigurationen](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [Anwendungsänderungsanalyseprofil](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tags](/azure/templates/microsoft.resources/tags)

Andere unterstützte Typen sind unter anderem:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="set-scope"></a>Mengenbereich

Legen Sie den Bereich wie folgt auf ein Abonnement fest:

```bicep
targetScope = 'subscription'
```

## <a name="deployment-commands"></a>Bereitstellungsbefehle

Verwenden Sie die Bereitstellungsbefehle auf Abonnementbereich für die Bereitstellung in einem Abonnement.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie für die Azure-Befehlszeilenschnittstelle [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create). Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-file main.bicep \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie als PowerShell-Bereitstellungsbefehl [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) oder den zugehörigen Alias `New-AzSubscriptionDeployment`. Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateFile main.bicep `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Ausführlichere Informationen über Bereitstellungsbefehle und -optionen für die Bereitstellung von ARM-Vorlagen finden Sie in den folgenden Artikeln:

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md)
* [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Bereitstellungsspeicherort und -name

Für Bereitstellungen auf Abonnementebene müssen Sie einen Speicherort für die Bereitstellung angeben. Der Speicherort der Bereitstellung ist vom Speicherort der Ressourcen getrennt, die Sie bereitstellen. Der Bereitstellungsspeicherort gibt an, wo Bereitstellungsdaten gespeichert werden sollen. Die Bereitstellungen von [Verwaltungsgruppe](deploy-to-management-group.md) und [Mandant](deploy-to-tenant.md) benötigen auch einen Speicherort. Für [Ressourcengruppe](deploy-to-resource-group.md)-Bereitstellungen wird der Speicherort der Ressourcengruppe zum Speichern der Bereitstellungsdaten verwendet.

Sie können einen Namen für die Bereitstellung angeben oder den Bereitstellungsstandardnamen verwenden. Der Standardname ist der Name der Vorlagendatei. Wenn Sie beispielsweise eine Vorlage mit dem Namen _main.json_ bereitstellen, wird der Standardbereitstellungsname **main** erstellt.

Der Speicherort für jeden Bereitstellungsnamen ist unveränderlich. Sie können keine Bereitstellung an einem Speicherort erstellen, wenn bereits eine Bereitstellung mit demselben Namen an einem anderen Speicherort vorhanden ist. Wenn Sie z. B. eine Abonnementbereitstellung mit dem Namen **deployment1** in **centralus** erstellen, können Sie später keine weitere Bereitstellung mit dem Namen **deployment1**, aber einen Speicherort **westus** erstellen. Wenn Sie den Fehlercode `InvalidDeploymentLocation` erhalten, verwenden Sie entweder einen anderen Namen oder denselben Speicherort wie bei der vorherigen Bereitstellung für diesen Namen.

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung in ein Abonnement können Sie Ressourcen an folgenden Orten bereitstellen:

* im Zielabonnement des Vorgangs
* in einem beliebigen Abonnement im Mandanten
* in anderen Ressourcengruppen im Abonnement oder in anderen Abonnements
* im Mandanten für das Abonnement

Für eine [Erweiterungsressource](scope-extension-resources.md) kann der Bereich auf ein Ziel festgelegt werden, das sich vom Bereitstellungsziel unterscheidet.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

### <a name="scope-to-subscription"></a>Bereich: Abonnement

Um Ressourcen für das Zielabonnement bereitzustellen, fügen Sie diese Ressourcen mit dem Schlüsselwort `resource` hinzu.

```bicep
targetScope = 'subscription'

// resource group created in target subscription
resource exampleResource 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  ...
}
```

Beispiele für die Bereitstellung im Abonnement finden Sie unter [Erstellen von Ressourcengruppen](#create-resource-groups) und [Zuweisen von Richtliniendefinitionen](#assign-policy-definition).

Zum Bereitstellen von Ressourcen in einem anderen Abonnement als dem des Vorgangs fügen Sie ein [Modul](modules.md) hinzu. Verwenden Sie die [subscription-Funktion](bicep-functions-scope.md#subscription), um die Eigenschaft `scope` festzulegen. Legen Sie `subscriptionId` auf die ID des Abonnements fest, in dem die Bereitstellung erfolgen soll.

```bicep
targetScope = 'subscription'

param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentSub'
  scope: subscription(otherSubscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Bereich: Ressourcengruppe

Zum Bereitstellen von Ressourcen in einer Ressourcengruppe im Abonnement fügen Sie ein Modul hinzu und legen seine Eigenschaft `scope` fest. Wenn die Ressourcengruppe bereits vorhanden ist, verwenden Sie die [resourceGroup-Funktion](bicep-functions-scope.md#resourcegroup), um den Bereichswert festzulegen. Geben Sie den Ressourcengruppennamen an.

```bicep
targetScope = 'subscription'

param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

Wenn die Ressourcengruppe in derselben Bicep-Datei erstellt wird, verwenden Sie den symbolischen Namen der Ressourcengruppe, um den Bereichswert festzulegen. Ein Beispiel für das Festlegen des Bereichs auf den symbolischen Namen finden Sie unter [Erstellen einer Ressourcengruppen und von Ressourcen](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Bereich: Mandant

Zum Erstellen von Ressourcen im Mandanten fügen Sie ein Modul hinzu. Verwenden Sie die [tenant-Funktion](bicep-functions-scope.md#tenant), um die Eigenschaft `scope` festzulegen.

Der Benutzer, der die Vorlage bereitstellt, muss über den [erforderlichen Zugriff zum Bereitstellen im Mandanten](deploy-to-tenant.md#required-access) verfügen.

Das folgende Beispiel enthält ein Modul, das im Mandanten bereitgestellt wird.

```bicep
targetScope = 'subscription'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Anstatt ein Modul zu verwenden, können Sie für einige Ressourcentypen den Bereich auf `tenant()` festlegen. Im folgenden Beispiel wird eine Verwaltungsgruppe im Mandanten bereitgestellt.

```bicep
targetScope = 'subscription'

param mgName string = 'mg-${uniqueString(newGuid())}'

// management group created at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

Weitere Informationen finden Sie unter [Verwaltungsgruppe](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Ressourcengruppen

### <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

Definieren Sie zum Erstellen einer Ressourcengruppe eine Ressource vom Typ [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) mit einem Namen und einem Standort für die Ressourcengruppe.

Im folgenden Beispiel wird eine leere Ressourcengruppe erstellt.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

### <a name="create-resource-group-and-resources"></a>Erstellen einer Ressourcengruppen und von Ressourcen

Fügen Sie zum Erstellen der Ressourcengruppe und Bereitstellen von Ressourcen für diese ein Modul hinzu. Das Modul enthält die Ressourcen, die in der Ressourcengruppe bereitgestellt werden sollen. Legen Sie den Bereich für das Modul auf den symbolischen Namen für die von Ihnen erstellte Ressourcengruppe fest. Sie können in bis zu 800 Ressourcengruppen bereitstellen.

Das folgende Beispiel erstellt eine Ressourcengruppe und stellt ein Speicherkonto in der Ressourcengruppe bereit. Beachten Sie, dass die Eigenschaft `scope` für ein Modul auf `newRG` festgelegt wird. Dabei handelt es sich um den symbolischen Namen für die Ressourcengruppe, die erstellt wird.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string
param storageName string
param storageLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}

module storageAcct 'storage.bicep' = {
  name: 'storageModule'
  scope: newRG
  params: {
    storageLocation: storageLocation
    storageName: storageName
  }
}
```

Das Modul verwendet eine Bicep-Datei namens **storage.bicep** mit folgendem Inhalt:

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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Zuweisen einer Richtliniendefinition

Im folgenden Beispiel wird dem Abonnement eine vorhandene Richtliniendefinition zugewiesen. Wenn die Richtliniendefinition Parameter unterstützt, stellen Sie diese als Objekt bereit. Wenn die Richtliniendefinition keine Parameter unterstützt, verwenden Sie das standardmäßige leere Objekt.

```bicep
targetScope = 'subscription'

param policyDefinitionID string
param policyName string
param policyParameters object = {}

resource policyAssign 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: policyName
  properties: {
    policyDefinitionId: policyDefinitionID
    parameters: policyParameters
  }
}
```

### <a name="create-and-assign-policy-definitions"></a>Erstellen und Zuweisen von Richtliniendefinitionen

Sie können eine Richtliniendefinition in derselben Bicep-Datei [definieren](../../governance/policy/concepts/definition-structure.md) und zuweisen.

```bicep
targetScope = 'subscription'

resource locationPolicy 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationpolicy'
  properties: {
    policyType: 'Custom'
    parameters: {}
    policyRule: {
      if: {
        field: 'location'
        equals: 'northeurope'
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource locationRestrict 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'allowedLocation'
  properties: {
    policyDefinitionId: locationPolicy.id
  }
}
```

## <a name="access-control"></a>Zugriffssteuerung

Informationen zum Zuweisen von Rollen finden Sie unter [Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md).

Im folgenden Beispiel wird eine Ressourcengruppe erstellt, eine Sperre darauf angewendet und einem Prinzipal eine Rolle zugewiesen.

```bicep
targetScope = 'subscription'

@description('Name of the resourceGroup to create')
param resourceGroupName string

@description('Location for the resourceGroup')
param resourceGroupLocation string

@description('principalId of the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition to apply to the resourceGroup - default is contributor')
param roleDefinitionId string = 'b24988ac-6180-42a0-ab88-20f7382dd24c'

@description('Unique name for the roleAssignment in the format of a guid')
param roleAssignmentName string = guid(principalId, roleDefinitionId, resourceGroupName)

var roleID = '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/${roleDefinitionId}'

resource newResourceGroup 'Microsoft.Resources/resourceGroups@2019-10-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
  properties: {}
}

module applyLock 'lock.bicep' = {
  name: 'applyLock'
  scope: newResourceGroup
}

module assignRole 'role.bicep' = {
  name: 'assignRBACRole'
  scope: newResourceGroup
  params: {
    principalId: principalId
    roleNameGuid: roleAssignmentName
    roleDefinitionId: roleID
  }
}
```

Im folgenden Beispiel wird das Modul zum Anwenden der Sperre gezeigt:

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

Im nächsten Beispiel wird das Modul zum Zuweisen der Rolle gezeigt:

```bicep
@description('The principal to assign the role to')
param principalId string

@description('A GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

param roleDefinitionId string

resource roleNameGuid_resource 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Bereichen finden Sie unter Folgendem:

* [Bereitstellungen von Ressourcengruppen](deploy-to-resource-group.md)
* [Verwaltungsgruppenbereitstellungen mit Bicep-Dateien](deploy-to-management-group.md)
* [Mandantenbereitstellungen mit Bicep-Datei](deploy-to-tenant.md)
