---
title: Gewähren von Benutzerberechtigungen für bestimmte Labrichtlinien
description: Erfahren Sie, wie Sie Berechtigungen für bestimmte Labrichtlinien in DevTest Labs basierend auf den Anforderungen der Benutzer gewähren.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: baf520f1d34d94926e4cf5eff5191d84936e34e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650511"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Gewähren von Benutzerberechtigungen für bestimmte Labrichtlinien
## <a name="overview"></a>Übersicht
Dieser Artikel veranschaulicht, wie Sie mit PowerShell Benutzerberechtigungen für eine bestimmte Labrichtlinie gewähren. Auf diese Weise können Berechtigungen basierend auf den Anforderungen der einzelnen Benutzer gewährt werden. Sie möchten beispielsweise einem bestimmten Benutzer die Möglichkeit zum Ändern der Richtlinieneinstellungen der virtuellen Computer gewähren, jedoch nicht für die Kostenrichtlinien.

## <a name="policies-as-resources"></a>Richtlinien als Ressourcen
Wie im Artikel [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md) verdeutlicht, ermöglicht die Azure RBAC eine differenzierte Zugriffsverwaltung für Azure-Ressourcen. Mithilfe der Azure RBAC können Sie Aufgaben in Ihrem DevOps-Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

In DevTest Labs ist eine Richtlinie ein Ressourcentyp, der die Azure RBAC-Aktion **Microsoft.DevTestLab/labs/policySets/policies/** ermöglicht. Jede Labrichtlinie ist eine Ressource im Richtlinienressourcentyp und kann als Bereich einer Azure-Rolle zugewiesen werden.

Um Benutzern beispielsweise Lese-/Schreibberechtigungen für die Richtlinie **Zulässige VM-Größen** zu erteilen, erstellen Sie eine benutzerdefinierte Rolle, die mit der Aktion **Microsoft.DevTestLab/labs/policySets/policies/** arbeitet, und weisen dieser benutzerdefinierten Rolle anschließend im Bereich von **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab** die entsprechenden Benutzer zu.

Weitere Informationen zu benutzerdefinierten Rollen in Azure RBAC finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Erstellen einer benutzerdefinierten Labrolle mithilfe von PowerShell
Um zu beginnen, müssen Sie [Azure PowerShell installieren](/powershell/azure/install-az-ps). 

Nachdem Sie Azure PowerShell-Cmdlets eingerichtet haben, können Sie die folgenden Aufgaben ausführen:

* Auflisten aller Vorgänge/Aktionen für einen Ressourcenanbieter
* Auflisten der Aktionen in einer bestimmten Rolle
* Erstellen einer benutzerdefinierten Rolle

Im folgenden PowerShell-Skript sind Beispiele für diese Aufgaben veranschaulicht:

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Zuweisen von Benutzerberechtigungen für eine bestimmte Richtlinie mithilfe von benutzerdefinierten Rollen
Nachdem Sie Ihre benutzerdefinierten Rollen definiert haben, können Sie diese Benutzern zuweisen. Um einem Benutzer eine benutzerdefinierte Rolle zuzuweisen, benötigen Sie zunächst die **ObjectId** dieses Benutzers. Verwenden Sie dazu das **Get-AzADUser**-Cmdlet.

Im folgenden Beispiel lautet die **ObjectId** des Benutzers *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

Sobald Sie über die **ObjectId** für den Benutzer und den Namen einer benutzerdefinierte Rolle verfügen, können Sie dem Benutzer diese Rolle mit dem **New-AzRoleAssignment**-Cmdlet zuweisen:

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

Im vorherigen Beispiel wird die **AllowedVmSizesInLab** -Richtlinie verwendet. Sie können auch eine der folgenden Richtlinien verwenden:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Benutzerberechtigungen für bestimmte Labrichtlinien gewährt haben, finden Sie im Folgenden Informationen zu den nächsten möglichen Schritten:

* [Sicherer Zugriff auf ein Lab](devtest-lab-add-devtest-user.md)
* [Festlegen von Labrichtlinien](devtest-lab-set-lab-policy.md)
* [Erstellen einer Labvorlage](devtest-lab-create-template.md)
* [Erstellen benutzerdefinierter Artefakte für Ihre VMs](devtest-lab-artifact-author.md)
* [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md)
