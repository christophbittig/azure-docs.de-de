---
title: Zuweisen von Azure AD-Rollen zu Benutzern – Azure Active Directory
description: Erfahren Sie, wie Sie Benutzern in Azure Active Directory Zugriff gewähren, indem Sie Azure AD-Rollen zuweisen.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/15/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 311478f52de547a99354a6effad376145166704b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345894"
---
# <a name="assign-azure-ad-roles-to-users"></a>Zuweisen von Azure AD-Rollen zu Benutzern

Um Benutzern in Azure Active Directory Zugriff zu gewähren, weisen Sie Azure AD-Rollen zu. Bei einer Rolle handelt es sich um eine Sammlung von Berechtigungen. In diesem Artikel wird beschrieben, wie Azure AD-Rollen über das Azure-Portal und über PowerShell zugewiesen werden.

## <a name="prerequisites"></a>Voraussetzungen

- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- Azure AD Premium P2-Lizenz bei Verwendung von Privileged Identity Management (PIM)
- AzureADPreview-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="azure-portal"></a>Azure-Portal

Führen Sie diese Schritte aus, um Azure AD-Rollen über das Azure-Portal zuzuweisen. Die jeweilige Funktionalität hängt davon ab, ob [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) aktiviert ist.

### <a name="assign-a-role"></a>Zuweisen einer Rolle

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus, um die Liste mit allen verfügbaren Rollen anzuzeigen.

    ![Seite „Rollen und Administratoren“ in Azure Active Directory.](./media/manage-roles-portal/roles-and-administrators.png)

1. Wählen Sie eine Rolle aus, um ihre Zuweisungen anzuzeigen.

    Verwenden Sie **Filter hinzufügen**, um die Rollen zu filtern und die Suche der benötigten Rolle zu erleichtern.

1. Wählen Sie **Zuweisungen hinzufügen** und dann die Benutzer aus, die Sie dieser Rolle zuweisen möchten.

    Wenn Anzeige von der folgenden Abbildung abweicht, ist PIM möglicherweise aktiviert. Siehe nächster Abschnitt.

    ![Bereich „Zuweisungen hinzufügen“ für die ausgewählte Rolle.](./media/manage-roles-portal/add-assignments.png)

1. Wählen Sie **Hinzufügen** aus, um die Rolle zuzuweisen.

### <a name="assign-a-role-using-pim"></a>Zuweisen einer Rolle mit PIM

Wenn [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) aktiviert ist, sind weitere Rollenzuweisungsfunktionen verfügbar. Sie können beispielsweise einen Benutzer für eine Rolle berechtigen oder die Dauer festlegen. Wenn PIM aktiviert ist, gibt es zwei Möglichkeiten, Rollen über das Azure-Portal zuzuweisen. Sie können die Seite „Rollen und Administratoren“ oder die PIM-Benutzeroberfläche verwenden. In beiden Fällen wird der gleiche PIM-Dienst verwendet.

Führen Sie die folgenden Schritte aus, um Rollen auf der Seite [Rollen und Administratoren](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) zuzuweisen. Informationen zum Zuweisen von Rollen auf der Seite [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) finden Sie unter [Zuweisen von Azure AD-Rollen in Privileged Identity Management](../privileged-identity-management/pim-how-to-add-role-to-user.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus, um die Liste mit allen verfügbaren Rollen anzuzeigen.

    ![Seite „Rollen und Administratoren“ in Azure Active Directory, wenn PIM aktiviert ist.](./media/manage-roles-portal/roles-and-administrators.png)

1. Wählen Sie eine Rolle aus, um die jeweils berechtigten, aktiven und abgelaufenen Rollenzuweisungen anzuzeigen.

    Verwenden Sie **Filter hinzufügen**, um die Rollen zu filtern und die Suche der benötigten Rolle zu erleichtern.

1. Wählen Sie **Zuweisungen hinzufügen** aus.

1. Wählen Sie **Kein Mitglied ausgewählt** und dann die Benutzer aus, die Sie dieser Rolle zuweisen möchten.

    ![Seite „Zuweisungen hinzufügen“ und Bereich „Mitglied auswählen“, wenn PIM aktiviert ist.](./media/manage-roles-portal/add-assignments-pim.png)

1. Klicken Sie auf **Weiter**.

1. Wählen Sie auf der Registerkarte **Einstellung** aus, ob diese Rollenzuweisung **Berechtigt** oder **Aktiv** sein soll.

    Eine berechtigte Rollenzuweisung bedeutet, dass der Benutzer mindestens eine Aktion ausführen muss, um die Rolle zu verwenden. Eine aktive Rollenzuweisung bedeutet, dass der Benutzer keine Aktion ausführen muss, um die Rolle zu verwenden. Weitere Informationen zur Bedeutung dieser Einstellungen Sie in der [PIM-Terminologie](../privileged-identity-management/pim-configure.md#terminology).

    ![Seite „Zuweisungen hinzufügen“ und Registerkarte „Einstellung“, wenn PIM aktiviert ist.](./media/manage-roles-portal/add-assignments-pim-setting.png)

1. Verwenden Sie die verbleibenden Optionen, um die Dauer für die Zuweisung festzulegen.

1. Wählen Sie **Zuweisen** aus, um die Rolle zuzuweisen.

## <a name="powershell"></a>PowerShell

Führen Sie diese Schritte aus, um Azure AD-Rollen über PowerShell zuzuweisen.

### <a name="setup"></a>Setup

1. Öffnen Sie ein PowerShell-Fenster, und verwenden Sie [Import-Module](/powershell/module/microsoft.powershell.core/import-module), um das AzureADPreview-Modul zu importieren. Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Verwenden Sie in einem PowerShell-Fenster [Connect-AzureAD](/powershell/module/azuread/connect-azuread), um sich bei Ihrem Mandanten zu anmelden.

    ```powershell
    Connect-AzureAD
    ```

1. Verwenden Sie [Get-AzureADUser](/powershell/module/azuread/get-azureaduser), um den Benutzer abzurufen, dem Sie eine Rolle zuweisen möchten.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'user@contoso.com'"
    ```

### <a name="assign-a-role"></a>Zuweisen einer Rolle

1. Verwenden Sie [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Verwenden Sie [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment), um die Rolle zuzuweisen.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="assign-a-role-as-eligible-using-pim"></a>Zuweisen einer Rolle als berechtigt mit PIM

Wenn PIM aktiviert ist, sind zusätzliche Funktionen verfügbar, z. B. das Berechtigen eines Benutzers für eine Rollenzuweisung oder das Definieren der Start- und Endzeit für eine Rollenzuweisung. Diese Funktionen verwenden andere PowerShell-Befehle. Weitere Informationen zur Verwendung von PowerShell und PIM finden Sie unter [PowerShell für Azure AD-Rollen in Privileged Identity Management (PIM)](../privileged-identity-management/powershell-for-azure-ad-roles.md).


1. Verwenden Sie [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition), um die Rolle abzurufen, die Sie zuweisen möchten.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Verwenden Sie [Get-AzureADMSPrivilegedResource](/powershell/module/azuread/get-azureadmsprivilegedresource), um die privilegierte Ressource abzurufen. In diesem Fall handelt es sich um Ihren Mandanten.

    ```powershell
    $aadTenant = Get-AzureADMSPrivilegedResource -ProviderId aadRoles
    ```

1. Verwenden Sie [New-Object](/powershell/module/microsoft.powershell.utility/new-object), um ein neues `AzureADMSPrivilegedSchedule`-Objekt zu erstellen, das Start- und Endzeit der Rollenzuweisung definiert.

    ```powershell
    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.EndDateTime = "2021-07-25T20:00:00.000Z"
    ```

1. Verwenden Sie [Open-AzureADMSPrivilegedRoleAssignmentRequest](/powershell/module/azuread/open-azureadmsprivilegedroleassignmentrequest), um die Rolle als berechtigt zuzuweisen.

    ```powershell
    $roleAssignmentEligible = Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId $aadTenant.Id -RoleDefinitionId $roleDefinition.Id -SubjectId $user.objectId -Type 'AdminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "Review billing info"
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Befolgen Sie diese Anweisungen, um eine Rolle mithilfe der Microsoft Graph-API im [Graph-Explorer](https://aka.ms/ge) zu zuweisen.

### <a name="assign-a-role"></a>Zuweisen einer Rolle

In diesem Beispiel wird einem Sicherheitsprinzipal mit der objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` die Rolle „Abrechnungsadministrator“ (Rollendefinitions-ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`) im Mandantenbereich zugewiesen. Informationen zum Anzeigen der Liste der unveränderlichen Rollenvorlagen-IDs aller integrierten Rollen finden Sie unter [Integrierte Rollen in Azure AD](permissions-reference.md).

1. Melden Sie sich bei [Graph-Explorer](https://aka.ms/ge) an.
2. Wählen Sie in der Dropdown-Liste **POST** als HTTP-Methode aus. 
3. Wählen Sie **beta** für die API-Version aus.
4. Verwenden Sie die [roleAssignments](/graph/api/rbacapplication-post-roleassignments)-API, um Rollen zuzuweisen. Fügen Sie die folgenden Details zur URL und zum Anforderungstext hinzu, und wählen Sie **Abfrage ausführen** aus.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "@odata.type": "#microsoft.graph.unifiedRoleAssignment",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "directoryScopeId": "/"
}
```

### <a name="assign-a-role-using-pim"></a>Zuweisen einer Rolle mit PIM

In diesem Beispiel wird einem Sicherheitsprinzipal mit der objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` eine zeitgebundene berechtigte Rollenzuweisung als Abrechnungsadministrator (Rollendefinitions-ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`) für 180 Tage zugewiesen.

1. Melden Sie sich bei [Graph-Explorer](https://aka.ms/ge) an.
2. Wählen Sie in der Dropdown-Liste **POST** als HTTP-Methode aus. 
3. Wählen Sie **beta** für die API-Version aus.
4. Fügen Sie die folgenden Details zur URL und zum Anforderungstext hinzu, und wählen Sie **Abfrage ausführen** aus.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "AfterDuration",
            "duration": "PT180D"
        }
    }
}

```

Im folgenden Beispiel wird einem Sicherheitsprinzipal eine dauerhafte berechtigte Rollenzuweisung als Abrechnungsadministrator zugewiesen.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "NoExpiration"
        }
    }
}

```

Verwenden Sie die folgende API, um die Rollenzuweisung zu aktivieren.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests

Content-type: application/json

{
    "action": "SelfActivate",
    "justification": "activating role assignment for admin privileges",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d"
}

```

## <a name="next-steps"></a>Nächste Schritte

- [Auflisten von Azure AD-Rollenzuweisungen](view-assignments.md)
- [Zuweisen benutzerdefinierter Rollen mit Ressourcengeltungsbereich unter Verwendung von PowerShell](custom-assign-powershell.md)
- [Integrierte Rollen in Azure AD](permissions-reference.md)
