---
title: Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory
description: Erfahren Sie, wie Sie Benutzer und Gruppen für eine App zuweisen und deren Zuweisung aufheben, indem Sie Azure Active Directory für die Identitätsverwaltung verwenden.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 91ffbc9e71da6a1837cd77efff3f0af435c437d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346641"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory

In diesem Artikel wird gezeigt, wie Sie Benutzer und Gruppen in Azure Active Directory (Azure AD) Unternehmensanwendungen zuweisen können, und zwar entweder im Azure-Portal oder über PowerShell. Wenn Sie einen Benutzer einer Anwendung zuweisen, wird diese unter [Meine Apps](https://myapps.microsoft.com/) für den Benutzer angezeigt, um den Zugriff zu erleichtern. Wenn die Anwendung Rollen verfügbar macht, können Sie dem Benutzer auch eine bestimmte Rolle zuweisen.

Für eine bessere Kontrolle können bestimmte Arten von Unternehmensanwendungen so konfiguriert werden, dass [eine Benutzerzuweisung](#configure-an-application-to-require-user-assignment) erforderlich ist.

> [!IMPORTANT]
> Wenn Sie einer Anwendung eine Gruppe zuweisen, haben nur die Benutzer in der Gruppe Zugriff. Die Zuweisung wird nicht an geschachtelte Gruppen weitergegeben.

> [!NOTE]
> Für die gruppenbasierte Zuweisung ist die Azure Active Directory Premium P1- oder P2-Edition erforderlich. Die gruppenbasierte Zuweisung wird nur für Sicherheitsgruppen unterstützt. Geschachtelte Gruppenmitgliedschaften und Microsoft 365-Gruppen werden aktuell nicht unterstützt. Weitere Informationen zu Lizenzierungsanforderungen für die in diesem Artikel erläuterten Features finden Sie in der [Preisübersicht für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurieren einer Anwendung für das Anfordern einer Benutzerzuweisung

Bei den folgenden Anwendungstypen haben Sie die Möglichkeit anzufordern, dass Benutzer der Anwendung zugeordnet werden müssen, ehe sie darauf zugreifen können:

- Anwendungen, die für einmaliges Anmelden (SSO) im Verbund mit SAML-basierter Authentifizierung konfiguriert sind
- Anwendungsproxyanwendungen mit Azure Active Directory-Vorauthentifizierung
- Anwendungen, die auf der Azure AD-Anwendungsplattform bei Verwendung der OAuth 2.0/OpenID Connect-Authentifizierung erstellt werden (nachdem ein Benutzer oder Administrator seine Zustimmung für die Anwendung erteilt hat).

Wenn eine Benutzerzuweisung erforderlich ist, können sich nur die Benutzer anmelden, die Sie der Anwendung explizit zuweisen (durch direkte Benutzerzuweisung oder basierend auf Gruppenmitgliedschaft). Sie können auf die App auf ihrer Seite „Meine Apps“ oder über einen direkten Link zugreifen.

Wenn die Zuweisung *nicht erforderlich* ist, entweder weil Sie diese Option auf **Nein** festgelegt haben oder die Anwendung einen anderen SSO-Modus verwendet, kann jeder Benutzer auf die Anwendung zugreifen. Dazu muss er über einen direkten Link zur Anwendung oder die **Benutzerzugriffs-URL** auf der Seite **Eigenschaften** der Anwendung verfügen.

Diese Einstellung hat keine Auswirkung darauf, ob eine Anwendung in „Meine Apps“ angezeigt wird. Anwendungen werden im Zugriffsbereich „Meine Apps“ von Benutzern angezeigt, sobald Sie der Anwendung einen Benutzer oder eine Gruppe zugewiesen haben. Hintergrundinformationen finden Sie unter [Verwaltung des Zugriffs auf Apps](what-is-access-management.md).

> [!NOTE]
> Wenn eine Anwendung eine Zuweisung erfordert, ist die Benutzereinwilligung für diese Anwendung nicht zulässig. Dies gilt auch dann, wenn die Benutzereinwilligung für diese App andernfalls zugelassen worden wäre. Vergewissern Sie sich, dass Sie für Apps, die eine Zuweisung erfordern, eine [mandantenweite Administratoreinwilligung erteilen](../manage-apps/grant-admin-consent.md).

So fordern Sie eine Benutzerzuweisung für eine Anwendung an

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Administratorkonto oder als Besitzer der Anwendung an.
2. Wählen Sie **Azure Active Directory** aus. Wählen Sie im linken Navigationsmenü **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung in der Liste aus. Wenn die Anwendung nicht angezeigt wird, beginnen Sie mit der Eingabe ihres Namens in das Suchfeld. Oder verwenden Sie die Filtersteuerelemente, um den Anwendungstyp, Status oder die Sichtbarkeit auszuwählen, und wählen Sie dann **Übernehmen** aus.
4. Wählen Sie im Navigationsmenü auf der linken Seite **Eigenschaften** aus.
5. Vergewissern Sie sich, dass **Benutzerzuweisung erforderlich?** auf **Ja** festgelegt ist.
   > [!NOTE]
   > Wenn der Umschalter **Benutzerzuweisung erforderlich?** nicht verfügbar ist, können Sie mit PowerShell die Eigenschaft appRoleAssignmentRequired auf den Dienstprinzipal festlegen.
6. Wählen Sie oben auf der Seite die Schaltfläche **Speichern** aus.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Zuweisen von Benutzern und Gruppen für eine App und Aufheben ihrer Zuweisung über das Azure-Portal

Weitere Informationen zum Zuweisen eines Benutzers oder einer Gruppe und das Aufheben ihrer Zuweisung über das Azure-Portal finden Sie in der [Schnellstartreihe zur Anwendungsverwaltung](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Zuweisen von Benutzern und Gruppen für eine App und Aufheben ihrer Zuweisung mit der Graph-API

Mit der Graph-API können Sie einer App Benutzer und Gruppen zuweisen und die Zuweisung aufheben. Weitere Informationen finden Sie unter [App-Rollenzuweisungen](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Zuweisen von Benutzern und Gruppen zu einer App mithilfe von PowerShell

1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
   > [!NOTE]
   > Sie müssen das AzureAD-Modul installieren (verwenden Sie den Befehl `Install-Module -Name AzureAD`). Wenn Sie zum Installieren eines NuGet-Moduls oder des neuen Azure Active Directory V2-PowerShell-Moduls aufgefordert werden, geben Sie „J“ ein, und drücken Sie die EINGABETASTE.
2. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an.
3. Weisen Sie mithilfe des folgenden Skripts einer Anwendung einen Benutzer und eine Rolle zu:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id

For more information about how to assign a user to an application role, see the documentation for [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

To assign a group to an enterprise app, you must replace `Get-AzureADUser` with `Get-AzureADGroup` and replace `New-AzureADUserAppRoleAssignment` with `New-AzureADGroupAppRoleAssignment`.

For more information about how to assign a group to an application role, see the documentation for [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### Example

This example assigns the user Britta Simon to the [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) application using PowerShell.

1. In PowerShell, assign the corresponding values to the variables $username, $app_name and $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"

2. In this example, we don't know what is the exact name of the application role we want to assign to Britta Simon. Run the following commands to get the user ($user) and the service principal ($sp) using the user UPN and the service principal display names.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"

3. Run the command `$sp.AppRoles` to display the roles available for the Workplace Analytics application. In this example, we want to assign Britta Simon the Analyst (Limited access) Role.
   ![Shows the roles available to a user using Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Assign the role name to the `$app_role_name` variable.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

5. Run the following command to assign the user to the app role:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Aufheben der Zuweisung von Benutzern und Gruppen zu einer App mithilfe von PowerShell

1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
   > [!NOTE]
   > Sie müssen das AzureAD-Modul installieren (verwenden Sie den Befehl `Install-Module -Name AzureAD`). Wenn Sie zum Installieren eines NuGet-Moduls oder des neuen Azure Active Directory V2-PowerShell-Moduls aufgefordert werden, geben Sie „J“ ein, und drücken Sie die EINGABETASTE.
2. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an.
3. Entfernen Sie mithilfe des folgenden Skripts einen Benutzer und eine Rolle aus einer Anwendung:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="related-articles"></a>Verwandte Artikel

- [Weitere Informationen zum Endbenutzerzugriff auf Anwendungen](end-user-experiences.md)
- [Planen einer Bereitstellung über „Meine Apps“ in Azure AD](my-apps-deployment-plan.md)
- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)

## <a name="next-steps"></a>Nächste Schritte

- [Alle meine Gruppen anzeigen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App]()
- [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App](disable-user-sign-in-portal.md)
- [Ändern des Namens oder Logos einer Unternehmens-App](./add-application-portal-configure.md)
