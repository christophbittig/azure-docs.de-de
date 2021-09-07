---
title: Erstellen benutzerdefinierter Rollen zum Verwalten von Unternehmens-Apps in Azure Active Directory
description: Erstellen und Zuweisen benutzerdefinierter Azure AD-Rollen für den Zugriff auf Unternehmens-Apps in Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf49cfd4d1e0e9b3e65354c9f6c89dd7efd71970
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339696"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Erstellen benutzerdefinierter Rollen zum Verwalten von Unternehmens-Apps in Azure Active Directory

In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Rolle mit Berechtigungen zum Verwalten von Unternehmens-App-Zuweisungen für Benutzer und Gruppen in Azure Active Directory (Azure AD) erstellen. Erläuterungen der Elemente von Rollenzuweisungen und der Bedeutung von Begriffen wie Untertyp, Berechtigung und Eigenschaftensatz finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Lizenz vom Typ Azure AD Premium P1 oder P2
- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- AzureADPreview-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="enterprise-app-role-permissions"></a>Rollenberechtigungen für Unternehmens-Apps

In diesem Artikel werden zwei Berechtigungen für Unternehmens-Apps erörtert. In allen Beispielen wird die Berechtigung zum Aktualisieren („update“) verwendet.

* Zum Lesen der Benutzer- und Gruppenzuweisungen im Bereich erteilen Sie die Berechtigung `microsoft.directory/servicePrincipals/appRoleAssignedTo/read`.
* Zum Verwalten der Benutzer- und Gruppenzuweisungen im Bereich erteilen Sie die Berechtigung `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`.

Das Erteilen der Berechtigung zum Aktualisieren führt dazu, dass die zugewiesene Person Zuweisungen von Benutzern und Gruppen zu Unternehmens-Apps verwalten kann. Der Bereich von Benutzer- und/oder Gruppenzuweisungen kann für eine einzelne Anwendung oder für alle Anwendungen erteilt werden. Wenn die Erteilung auf organisationsweiter Ebene erfolgt, kann die zugewiesene Person Zuweisungen für alle Anwendungen verwalten. Wenn die Erteilung auf Anwendungsebene erfolgt, kann die zugewiesene Person nur Zuweisungen für die angegebene Anwendung verwalten.

Das Erteilen der Berechtigung zum Aktualisieren erfolgt in zwei Schritten:

1. Erstellen einer benutzerdefinierten Rolle mit der Berechtigung `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Erteilen von Berechtigungen für Benutzer oder Gruppen zum Verwalten von Benutzer- und Gruppenzuweisungen für Unternehmens-Apps: Dabei können Sie den Bereich auf die organisationsweite Ebene oder auf eine einzelne Anwendung festlegen.

## <a name="azure-portal"></a>Azure-Portal

### <a name="create-a-new-custom-role"></a>Erstellen einer neuen benutzerdefinierten Rolle

>[!NOTE]
> Benutzerdefinierte Rollen werden auf Organisationsebene erstellt und verwaltet und sind nur auf der Seite „Übersicht“ der Organisation verfügbar.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** und dann **Neue benutzerdefinierte Rolle** aus.

    ![Hinzufügen einer neuen benutzerdefinierten Rolle aus der Liste der Rollen in Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Geben Sie auf der Registerkarte **Grundeinstellungen** im Feld „Name“ für die Rolle die Bezeichnung „Benutzer- und Gruppenzuweisungen verwalten“ und im Feld „Beschreibung“ den Text „Berechtigungen zum Verwalten von Benutzer- und Gruppenzuweisungen erteilen“ ein, und wählen Sie dann **Weiter** aus.

    ![Angeben eines Namens und einer Beschreibung für die benutzerdefinierte Rolle](./media/custom-enterprise-apps/role-name-and-description.png)

1. Geben Sie auf der Registerkarte **Berechtigungen** im Suchfeld die Zeichenfolge „microsoft.directory/servicePrincipals/appRoleAssignedTo/update“ ein. Aktivieren Sie dann die Kontrollkästchen neben den gewünschten Berechtigungen, und wählen Sie anschließend **Weiter** aus.

    ![Hinzufügen der Berechtigungen für die benutzerdefinierte Rolle](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Überprüfen Sie die Berechtigungen auf der Registerkarte **Überprüfen + erstellen**, und wählen Sie **Erstellen** aus.

    ![Jetzt können Sie die benutzerdefinierte Rolle erstellen.](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-portal"></a>Zuweisen der Rolle zu einem Benutzer über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus.

1. Wählen Sie die Rolle **Berechtigungen zum Verwalten von Benutzer- und Gruppenzuweisungen erteilen** aus.

    ![„Rollen und Administratoren“ öffnen und nach der benutzerdefinierten Rolle suchen](./media/custom-enterprise-apps/select-custom-role.png)

1. Wählen Sie **Zuweisung hinzufügen** aus, wählen Sie den gewünschten Benutzer aus, und klicken Sie dann auf **Auswählen**, um dem Benutzer eine Rollenzuweisung hinzuzufügen.

    ![Dem Benutzer eine Zuweisung für die benutzerdefinierte Rolle hinzufügen](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Zuweisungstipps

* Wenn Sie zugewiesenen Personen Berechtigungen zum Verwalten des Benutzer- und Gruppenzugriffs für alle organisationsweiten Unternehmens-Apps erteilen möchten, öffnen Sie in Azure AD die Seite **Übersicht** für Ihre Organisation, und bearbeiten Sie die Liste der organisationsweiten **Rollen und Administratoren**.
* Wenn Sie zugewiesenen Personen Berechtigungen zum Verwalten des Benutzer- und Gruppenzugriffs für eine bestimmte Unternehmens-App erteilen möchten, navigieren Sie in Azure AD zu dieser App, und öffnen Sie die Liste **Rollen und Administratoren** für diese App. Wählen Sie die neue benutzerdefinierte Rolle aus, und schließen Sie die Benutzer- oder Gruppenzuweisung ab. Die zugewiesenen Personen können den Benutzer- und Gruppenzugriff nur für diese bestimmte App verwalten.
* Wenn Sie Ihre benutzerdefinierte Rollenzuweisung testen möchten, melden Sie sich als die zugewiesene Person an, und öffnen Sie die Seite **Benutzer und Gruppen** einer Anwendung, um zu überprüfen, ob die Option **Benutzer hinzufügen** aktiviert ist.

    ![Überprüfen der Benutzerberechtigungen](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="powershell"></a>PowerShell

Ausführliche Informationen finden Sie unter [Erstellen und Zuweisen einer benutzerdefinierten Rolle](custom-create.md) und [Zuweisen benutzerdefinierter Rollen mit Ressourcengeltungsbereich unter Verwendung von PowerShell in Azure Active Directory](custom-assign-powershell.md).

### <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Verwenden Sie zum Erstellen einer neuen Rolle das folgende PowerShell-Skript:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Zuweisen der benutzerdefinierten Rolle

Weisen Sie die Rolle mithilfe des folgenden PowerShell-Skripts zu.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Erstellen Sie anhand des bereitgestellten Beispiels eine benutzerdefinierte Rolle in der Microsoft Graph-API. Ausführliche Informationen finden Sie unter [Erstellen und Zuweisen einer benutzerdefinierten Rolle](custom-create.md) und [Zuweisen von benutzerdefinierten Administratorrollen mithilfe von Microsoft Graph-API in Azure Active Directory](custom-assign-graph.md).

HTTP-Anforderung zum Erstellen der benutzerdefinierten Rolle:

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-the-microsoft-graph-api"></a>Zuweisen der benutzerdefinierten Rolle mithilfe der Microsoft Graph-API

Die Rollenzuweisung kombiniert eine Sicherheitsprinzipal-ID (bei der es sich um einen Benutzer oder Dienstprinzipal handeln kann), eine Rollendefinitions-ID und einen Azure AD-Ressourcenbereich. Weitere Informationen zu den Elementen einer Rollenzuweisung finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md).

HTTP-Anforderung zum Zuweisen einer benutzerdefinierten Rolle:

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die verfügbaren [benutzerdefinierten Rollenberechtigungen für Unternehmens-Apps](custom-enterprise-app-permissions.md).
