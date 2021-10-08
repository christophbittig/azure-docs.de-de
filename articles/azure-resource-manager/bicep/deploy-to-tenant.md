---
title: Verwenden von Bicep zum Bereitstellen von Ressourcen für den Mandanten
description: Hier erfahren Sie, wie Sie Ressourcen im Mandantenbereich in einer Bicep-Datei bereitstellen.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: fe5b8a1a17af47570bb2399172866a1e720b0bab
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793719"
---
# <a name="tenant-deployments-with-bicep-file"></a>Mandantenbereitstellungen mit Bicep-Datei

Im Zuge der Entwicklung Ihrer Organisation müssen unter Umständen [Richtlinien](../../governance/policy/overview.md) oder die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) für Ihren Azure AD-Mandanten definiert und zugewiesen werden. Mit Vorlagen auf der Mandantenebene können Sie Richtlinien deklarativ anwenden und Rollen global zuweisen.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen zu Bereitstellungsbereichen und praktische Anleitungen finden Sie unter [Bereitstellen von Ressourcen für Abonnements, Verwaltungsgruppen und Mandanten mithilfe von Bicep](/learn/modules/deploy-resources-scopes-bicep/) auf **Microsoft Learn**.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Nicht alle Ressourcentypen können auf Mandantenebene bereitgestellt werden. Im folgenden Abschnitt werden die unterstützten Ressourcentypen aufgelistet.

Verwenden Sie für rollenbasierte Zugriffssteuerung von Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Verwenden Sie für geschachtelte Vorlagen, die om Verwaltungsgruppen, Abonnements oder Ressourcengruppen bereitstellen:

* [Bereitstellungen](/azure/templates/microsoft.resources/deployments)

Verwenden Sie zum Erstellen von Verwaltungsgruppen:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Verwenden Sie zum Erstellen von Abonnements:

* [Aliase](/azure/templates/microsoft.subscription/aliases)

Verwenden Sie zum Verwalten von Kosten:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Verwenden Sie zum Konfigurieren des Portals:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene, aber Sie können keine benutzerdefinierten Richtliniendefinitionen im Mandanten bereitstellen. Ein Beispiel für das Zuweisen einer integrierten Richtliniendefinition zu einer Ressource finden Sie unter [tenantResourceId-Beispiel.](./bicep-functions-resource.md#tenantresourceid)

## <a name="set-scope"></a>Mengenbereich

Verwenden Sie Folgendes, um den Bereich auf den Mandanten festzulegen:

```bicep
targetScope = 'tenant'
```

## <a name="required-access"></a>Erforderlicher Zugriff

Der Prinzipal, der die Vorlage bereitstellt, muss über Berechtigungen zum Erstellen von Ressourcen im Mandantenbereich verfügen. Der Prinzipal muss über zum Ausführen der Bereitstellungsaktionen (`Microsoft.Resources/deployments/*`) sowie zum Erstellen der in der Vorlage definierten Ressourcen berechtigt sein. Soll also beispielsweise eine Verwaltungsgruppe erstellt werden, muss der Prinzipal im Mandantenbereich über die Berechtigung „Mitwirkender“ verfügen. Zum Erstellen von Rollenzuweisungen muss der Prinzipal über die Berechtigung „Besitzer“ verfügen.

Der globale Administrator für die Azure Active Directory-Instanz ist nicht automatisch zum Zuweisen von Rollen berechtigt. Um Vorlagenbereitstellungen im Mandantenbereich zu ermöglichen, muss der globale Administrator folgende Schritte ausführen:

1. Erhöhen der Kontozugriffsrechte, damit der globale Administrator Rollen zuweisen kann. Weitere Informationen finden Sie unter [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](../../role-based-access-control/elevate-access-global-admin.md).

1. Zuweisen der Rolle „Besitzer“ oder „Mitwirkender“ für den Prinzipal, der die Vorlagen bereitstellen muss:

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Der Prinzipal verfügt nun über die erforderlichen Berechtigungen, um die Vorlage bereitzustellen.

## <a name="deployment-commands"></a>Bereitstellungsbefehle

Die Befehle für Mandantenbereitstellungen unterscheiden sich von den Befehlen für Ressourcengruppenbereitstellungen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie bei der Azure CLI [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-file main.bicep
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie für Azure PowerShell den Befehl [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateFile main.bicep
```

---

Ausführlichere Informationen über Bereitstellungsbefehle und -optionen für die Bereitstellung von ARM-Vorlagen finden Sie in den folgenden Artikeln:

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md)
* [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Bereitstellungsspeicherort und -name

Für Bereitstellungen auf der Mandantenebene müssen Sie einen Speicherort für die Bereitstellung angeben. Der Speicherort der Bereitstellung ist vom Speicherort der Ressourcen getrennt, die Sie bereitstellen. Der Bereitstellungsspeicherort gibt an, wo Bereitstellungsdaten gespeichert werden sollen. [Abonnement](deploy-to-subscription.md)- und [Verwaltungsgruppen](deploy-to-management-group.md)bereitstellungen erfordern ebenfalls einen Speicherort. Für [Ressourcengruppe](deploy-to-resource-group.md)nbereitstellungen wird der Speicherort der Ressourcengruppe zum Speichern der Bereitstellungsdaten verwendet.

Sie können einen Namen für die Bereitstellung angeben oder den Bereitstellungsstandardnamen verwenden. Der Standardname ist der Name der Vorlagendatei. Wenn Sie beispielsweise eine Datei mit dem Namen _main.bicep_ bereitstellen, wird der Standardbereitstellungsname **main** erstellt.

Der Speicherort für jeden Bereitstellungsnamen ist unveränderlich. Sie können keine Bereitstellung an einem Speicherort erstellen, wenn bereits eine Bereitstellung mit demselben Namen an einem anderen Speicherort vorhanden ist. Wenn Sie z. B. eine Mandantenbereitstellung mit dem Namen **deployment1** in **centralus** erstellen, können Sie später keine weitere Bereitstellung mit dem Namen **deployment1**, aber einen Speicherort **westus** erstellen. Wenn Sie den Fehlercode `InvalidDeploymentLocation` erhalten, verwenden Sie entweder einen anderen Namen oder denselben Speicherort wie bei der vorherigen Bereitstellung für diesen Namen.

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung auf einem Mandanten können Sie Ressourcen an folgenden Orten bereitstellen:

* im Mandanten
* in Verwaltungsgruppen innerhalb des Mandanten
* subscriptions
* Ressourcengruppen

Für eine [Erweiterungsressource](scope-extension-resources.md) kann der Bereich auf ein Ziel festgelegt werden, das sich vom Bereitstellungsziel unterscheidet.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

In diesem Abschnitt wird das Festlegen verschiedener Bereiche veranschaulicht. Sie können diese verschiedenen Bereiche in einer Vorlage kombinieren.

### <a name="scope-to-tenant"></a>Bereich: Mandant

Ressourcen, die innerhalb der Bicep-Datei definiert sind, werden auf den Mandanten angewendet.

```bicep
targetScope = 'tenant'

// create resource at tenant
resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  ...
}
```

### <a name="scope-to-management-group"></a>Bereich: Verwaltungsgruppe

Fügen Sie ein [Modul](modules.md) hinzu, um eine Verwaltungsgruppe innerhalb des Mandanten als Bereitstellungsziel zu verwenden. Verwenden Sie die [managementGroup-Funktion](bicep-functions-scope.md#managementgroup), um ihre Eigenschaft `scope` festzulegen. Geben Sie den Namen der Verwaltungsgruppe an.

```bicep
targetScope = 'tenant'

param managementGroupName string

// create resources at management group level
module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

### <a name="scope-to-subscription"></a>Bereich: Abonnement

Um ein Abonnement innerhalb des Mandanten als Bereitstellungsziel zu verwenden, fügen Sie ein Modul hinzu. Verwenden Sie die [subscription-Funktion](bicep-functions-scope.md#subscription), um ihre Eigenschaft `scope` festzulegen. Geben Sie die Abonnement-ID an.

```bicep
targetScope = 'tenant'

param subscriptionID string

// create resources at subscription level
module  'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Bereich: Ressourcengruppe

Um eine Ressourcengruppe innerhalb des Mandanten als Bereitstellungsziel zu verwenden, fügen Sie ein Modul hinzu. Verwenden Sie die [resourceGroup-Funktion](bicep-functions-scope.md#resourcegroup), um ihre Eigenschaft `scope` festzulegen. Geben Sie die Abonnement-ID und den Namen der Ressourcengruppe an.

```bicep
targetScope = 'tenant'

param resourceGroupName string
param subscriptionID string

// create resources at resource group level
module  'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

## <a name="create-management-group"></a>Erstellen einer Verwaltungsgruppe

Die folgende Vorlage dient zum Erstellen einer Verwaltungsgruppe:

```bicep
targetScope = 'tenant'
param mgName string = 'mg-${uniqueString(newGuid())}'

resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  name: mgName
  properties: {}
}
```

Verfügt Ihr Konto nicht über die Berechtigung zum Bereitstellen im Mandanten, können Sie durch die Bereitstellung in einem anderen Bereich dennoch Verwaltungsgruppen erstellen. Weitere Informationen finden Sie unter [Verwaltungsgruppe](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Zuweisen einer Rolle

Die folgende Vorlage dient zum Zuweisen einer Rolle im Mandantenbereich:

```bicep
targetScope = 'tenant'

@description('principalId if the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition for the assignment - default is owner')
param roleDefinitionId string = '8e3af657-a8ff-443c-a75c-2fe8c4bcb635'

var roleAssignmentName = guid(principalId, roleDefinitionId)

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2020-03-01-preview' = {
  name: roleAssignmentName
  properties: {
    roleDefinitionId: tenantResourceId('Microsoft.Authorization/roleDefinitions', roleDefinitionId)
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Bereichen finden Sie unter Folgendem:

* [Bereitstellungen von Ressourcengruppen](deploy-to-resource-group.md)
* [Abonnementbereitstellungen](deploy-to-subscription.md)
* [Bereitstellungen von Verwaltungsgruppen](deploy-to-management-group.md)
