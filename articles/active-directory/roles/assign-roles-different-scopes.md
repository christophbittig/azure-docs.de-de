---
title: Zuweisen von Azure AD-Rollen mit verschiedenen Gültigkeitsbereichen – Azure Active Directory
description: Hier erfahren Sie, wie Sie Rollen mit verschiedenen Gültigkeitsbereichen in Azure Active Directory zuweisen.
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c9e92d09ce929b47cd9a71559eabb4e7c40858
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349856"
---
# <a name="assign-azure-ad-roles-at-different-scopes"></a>Zuweisen von Azure AD-Rollen mit verschiedenen Gültigkeitsbereichen

In diesem Artikel wird beschrieben, wie Sie Azure AD-Rollen mit verschiedenen Gültigkeitsbereichen zuweisen. Informationen zu Bereichen in Azure AD finden Sie in diesem Dokument: [Übersicht über RBAC in Azure AD](custom-overview.md). Im Allgemeinen müssen Sie sich in dem Bereich befinden, auf den die Rollenzuweisung beschränkt werden soll. Wenn Sie z. B. die Rolle „Helpdeskadministrator“ im Bereich einer [Verwaltungseinheit](administrative-units.md) zuweisen möchten, sollten Sie zu **Azure AD > Verwaltungseinheiten > {Verwaltungseinheit} > Rollen und Administratoren** wechseln und dann die Rollenzuweisung ausführen. Dadurch wird eine Rollenzuweisung für die Verwaltungseinheit und nicht für den gesamten Mandanten erstellt.

## <a name="prerequisites"></a>Voraussetzungen

- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“.
- AzureADPreview-Modul bei Verwendung von PowerShell.
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API.

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="assign-roles-scoped-to-the-tenant"></a>Zuweisen von Rollen mit dem Mandanten als Bereich

In diesem Abschnitt wird beschrieben, wie Sie Rollen im Mandantenbereich zuweisen.

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus, um die Liste mit allen verfügbaren Rollen anzuzeigen.

    ![Seite „Rollen und Administratoren“ in Azure Active Directory.](./media/manage-roles-portal/roles-and-administrators.png)

1. Wählen Sie eine Rolle aus, um ihre Zuweisungen anzuzeigen. Verwenden Sie **Filter hinzufügen**, um die Rollen zu filtern und die Suche der benötigten Rolle zu erleichtern.

1. Wählen Sie **Zuweisungen hinzufügen** und dann die Benutzer aus, die Sie dieser Rolle zuweisen möchten.

    ![Bereich „Zuweisungen hinzufügen“ für die ausgewählte Rolle.](./media/manage-roles-portal/add-assignments.png)

1. Wählen Sie **Hinzufügen** aus, um die Rolle zuzuweisen.

### <a name="powershell"></a>PowerShell

Führen Sie diese Schritte aus, um Azure AD-Rollen über PowerShell zuzuweisen.

1. Öffnen Sie ein PowerShell-Fenster, und verwenden Sie [Import-Module](/powershell/module/microsoft.powershell.core/import-module), um das AzureADPreview-Modul zu importieren. Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Verwenden Sie in einem PowerShell-Fenster [Connect-AzureAD](/powershell/module/azuread/connect-azuread), um sich bei Ihrem Mandanten zu anmelden.

    ```powershell
    Connect-AzureAD
    ```

1. Verwenden Sie [Get-AzureADUser](/powershell/module/azuread/get-azureaduser), um den Benutzer abzurufen.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Verwenden Sie [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Legen Sie den Mandanten als Bereich der Rollenzuweisung fest.

    ```powershell
    $directoryScope = '/'
    ```

1. Verwenden Sie [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment), um die Rolle zuzuweisen.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```
 
### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Befolgen Sie diese Anweisungen, um eine Rolle mithilfe der Microsoft Graph-API im [Graph-Explorer](https://aka.ms/ge) zu zuweisen.

1. Melden Sie sich bei [Graph-Explorer](https://aka.ms/ge) an.

1. Verwenden Sie die API [Benutzer auflisten](/graph/api/user-list), um den Benutzer abzurufen.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Verwenden Sie die API [roleDefinitions auflisten](/graph/api/rbacapplication-list-roledefinitions), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Billing Administrator'
    ```
    
1. Verwenden Sie die API [roleAssignments erstellen](/graph/api/rbacapplication-post-roleassignments), um die Rolle zuzuweisen.\

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/"
    }
    ```

## <a name="assign-roles-scoped-to-an-administrative-unit"></a>Zuweisen von Rollen mit einer Verwaltungseinheit als Bereich

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory > Verwaltungseinheiten** aus, um die Liste aller Verwaltungseinheiten anzuzeigen.

1. Wählen Sie die Verwaltungseinheit aus.

    ![Verwaltungseinheiten in Azure Active Directory.](./media/assign-roles-different-scopes/admin-units.png)

1. Wählen Sie im linken Navigationsmenü **Rollen und Administratoren** aus, um die Liste aller Rollen anzuzeigen, die über eine Verwaltungseinheit zugewiesen werden können.

    ![Menü „Rollen und Administratoren“ unter „Verwaltungseinheiten“ in Azure Active Directory.](./media/assign-roles-different-scopes/admin-units-roles.png)

1. Wählen Sie die gewünschte Rolle aus.

1. Wählen Sie **Zuweisungen hinzufügen** und dann die Benutzer oder die Gruppe aus, denen bzw. der Sie diese Rolle zuweisen möchten.

1. Wählen Sie **Hinzufügen** aus, um die Rolle im Bereich der Verwaltungseinheit zuzuweisen.

>[!Note] 
>Hier wird nicht die gesamte Liste der in Azure AD integrierten oder benutzerdefinierten Rollen angezeigt. Dies entspricht dem erwarteten Verhalten. Es werden die Rollen angezeigt, die über Berechtigungen im Zusammenhang mit den Objekten verfügen, die innerhalb der Verwaltungseinheit unterstützt werden. Eine Liste der in einer Verwaltungseinheit unterstützten Objekte finden Sie in [dieser Dokumentation](administrative-units.md).

### <a name="powershell"></a>PowerShell

Führen Sie diese Schritte aus, um Azure AD-Rollen im Bereich einer Verwaltungseinheit mithilfe von PowerShell zuzuweisen.

1. Öffnen Sie ein PowerShell-Fenster, und verwenden Sie [Import-Module](/powershell/module/microsoft.powershell.core/import-module), um das AzureADPreview-Modul zu importieren. Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Verwenden Sie in einem PowerShell-Fenster [Connect-AzureAD](/powershell/module/azuread/connect-azuread), um sich bei Ihrem Mandanten zu anmelden.

    ```powershell
    Connect-AzureAD
    ```

1. Verwenden Sie [Get-AzureADUser](/powershell/module/azuread/get-azureaduser), um den Benutzer abzurufen.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Verwenden Sie [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'User Administrator'"
    ```

1. Verwenden Sie [Get-AzureADMSAdministrativeUnit](/powershell/module/azuread/get-azureadmsadministrativeunit), um die Verwaltungseinheit abzurufen, die als Bereich für die Rollenzuweisung verwendet werden soll.

    ```powershell
    $adminUnit = Get-AzureADMSAdministrativeUnit -Filter "displayName eq 'Seattle Admin Unit'"
    $directoryScope = '/administrativeUnits/' + $adminUnit.Id
    ```

1. Verwenden Sie [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment), um die Rolle zuzuweisen.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Befolgen Sie diese Anweisungen, um eine Rolle mithilfe der Microsoft Graph-API im [Graph-Explorer](https://aka.ms/ge) im Bereich einer Verwaltungseinheit zuzuweisen.

1. Melden Sie sich bei [Graph-Explorer](https://aka.ms/ge) an.

1. Verwenden Sie die API [Benutzer auflisten](/graph/api/user-list), um den Benutzer abzurufen.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Verwenden Sie die API [roleDefinitions auflisten](/graph/api/rbacapplication-list-roledefinitions), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'User Administrator'
    ```
    
1. Verwenden Sie die API [administrativeUnits auflisten](/graph/api/administrativeunit-list), um die Verwaltungseinheit abzurufen, die als Bereich für die Rollenzuweisung verwendet werden soll.

    ```HTTP
    GET https://graph.microsoft.com/beta/administrativeUnits?$filter=displayName eq 'Seattle Admin Unit'
    ```

1. Verwenden Sie die API [roleAssignments erstellen](/graph/api/rbacapplication-post-roleassignments), um die Rolle zuzuweisen.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/administrativeUnits/<provide objectId of the admin unit obtained above>"
    }
    ```
    
>[!Note] 
>Hier wird directoryScopeId als */administrativeUnits/foo* anstelle von */foo* angegeben. Dies ist beabsichtigt. Der Bereich */administrativeUnits/foo* bedeutet, dass der Prinzipal die Mitglieder der Verwaltungseinheit (auf Grundlage der Rolle, die ihm zugewiesen ist) und nicht die Verwaltungseinheit selbst verwalten kann. Der Bereich */foo* bedeutet, dass der Prinzipal das Azure AD-Objekt selbst verwalten kann. Im folgenden Abschnitt sehen Sie, dass der Bereich */foo* ist, da eine Rolle mit dem Bereich einer App-Registrierung die Berechtigung zum Verwalten des Objekts selbst gewährt.

## <a name="assign-roles-scoped-to-an-app-registration"></a>Zuweisen von Rollen mit einer App-Registrierung als Bereich

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory > App-Registrierungen** aus, um eine Liste aller App-Registrierungen anzuzeigen.

1. Wählen Sie eine Anwendung aus. Verwenden Sie das Suchfeld, um die gewünschte App zu finden.

    ![App-Registrierungen in Azure Active Directory.](./media/assign-roles-different-scopes/app-reg.png)

1. Wählen Sie im linken Navigationsmenü **Rollen und Administratoren** aus, um die Liste aller Rollen anzuzeigen, die über eine App-Registrierung zugewiesen werden können.

    ![Rollen für eine App-Registrierung in Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-roles.png)

1. Wählen Sie die gewünschte Rolle aus.

1. Wählen Sie **Zuweisungen hinzufügen** und dann die Benutzer oder die Gruppe aus, denen bzw. der Sie diese Rolle zuweisen möchten.

    ![Hinzufügen einer Rollenzuweisung im Bereich einer App-Registrierung in Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-add-assignment.png)

1. Wählen Sie **Hinzufügen** aus, um die Rolle im Bereich der App-Registrierung zuzuweisen. 

    ![Erfolgreich hinzugefügte Rollenzuweisung im Bereich einer App-Registrierung in Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-assignment.png)
    
    ![Dem Benutzer zugewiesene Rolle im Bereich einer App-Registrierung in Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-scoped-assignment.png)
    

>[!Note] 
>Hier wird nicht die gesamte Liste der in Azure AD integrierten oder benutzerdefinierten Rollen angezeigt. Dies entspricht dem erwarteten Verhalten. Es werden nur die Rollen angezeigt, die über Berechtigungen zum Verwalten von App-Registrierungen verfügen. 


### <a name="powershell"></a>PowerShell

Führen Sie diese Schritte aus, um Azure AD-Rollen im Bereich einer Anwendung mithilfe von PowerShell zuzuweisen.

1. Öffnen Sie ein PowerShell-Fenster, und verwenden Sie [Import-Module](/powershell/module/microsoft.powershell.core/import-module), um das AzureADPreview-Modul zu importieren. Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Verwenden Sie in einem PowerShell-Fenster [Connect-AzureAD](/powershell/module/azuread/connect-azuread), um sich bei Ihrem Mandanten zu anmelden.

    ```powershell
    Connect-AzureAD
    ```

1. Verwenden Sie [Get-AzureADUser](/powershell/module/azuread/get-azureaduser), um den Benutzer abzurufen.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Verwenden Sie [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Administrator'"
    ```

1. Verwenden Sie [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication), um die App-Registrierung abzurufen, die als Bereich für die Rollenzuweisung verwendet werden soll.

    ```powershell
    $appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
    $directoryScope = '/' + $appRegistration.objectId
    ```

1. Verwenden Sie [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment), um die Rolle zuzuweisen.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph-API

Befolgen Sie diese Anweisungen, um eine Rolle mithilfe der Microsoft Graph-API im [Graph-Explorer](https://aka.ms/ge) im Bereich einer Anwendung zuzuweisen.

1. Melden Sie sich bei [Graph-Explorer](https://aka.ms/ge) an.

1. Verwenden Sie die API [Benutzer auflisten](/graph/api/user-list), um den Benutzer abzurufen.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Verwenden Sie die API [roleDefinitions auflisten](/graph/api/rbacapplication-list-roledefinitions), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Application Administrator'
    ```
    
1. Verwenden Sie die API [Anwendungen auflisten](/graph/api/application-list), um die Anwendung abzurufen, die als Bereich für die Rollenzuweisung verwendet werden soll.

    ```HTTP
    GET https://graph.microsoft.com/beta/applications?$filter=displayName eq 'f/128 Filter Photos'
    ```

1. Verwenden Sie die API [roleAssignments erstellen](/graph/api/rbacapplication-post-roleassignments), um die Rolle zuzuweisen.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/<provide objectId of the app registration obtained above>"
    }
    ```

>[!Note] 
>Hier wird im Gegensatz zum Abschnitt oben directoryScopeId als */foo* angegeben. Dies ist beabsichtigt. Der Bereich */foo* bedeutet, dass der Prinzipal das Azure AD-Objekt verwalten kann. Der Bereich */administrativeUnits/foo* bedeutet, dass der Prinzipal die Mitglieder der Verwaltungseinheit (auf Grundlage der Rolle, die ihm zugewiesen ist) und nicht die Verwaltungseinheit selbst verwalten kann.


## <a name="next-steps"></a>Nächste Schritte

* [Auflisten von Azure AD-Rollenzuweisungen](view-assignments.md).
* [Zuweisen von Azure AD-Rollen zu Benutzern](manage-roles-portal.md).
* [Zuweisen von Azure AD-Rollen zu Gruppen](groups-assign-role.md)