---
title: Zuweisen von Benutzern und Gruppen
titleSuffix: Azure AD
description: Erfahren Sie, wie Sie Benutzer und Gruppen für eine App zuweisen und deren Zuweisung aufheben, indem Sie Azure Active Directory für die Identitätsverwaltung verwenden.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 1bb1d7c65451be88864440d2e5b1327b3688f337
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061350"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory

In diesem Artikel wird das Zuweisen von Benutzern und Gruppen zu einer Unternehmensanwendung in Azure Active Directory (Azure AD) mit PowerShell veranschaulicht. Wenn Sie einen Benutzer einer Anwendung zuweisen, wird die Anwendung im Portal „Meine Apps“ für den Benutzer angezeigt, um den Zugriff zu erleichtern. Wenn die Anwendung Rollen verfügbar macht, können Sie dem Benutzer auch eine bestimmte Rolle zuweisen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Zuweisen von Benutzern zu einer App mithilfe von PowerShell benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Wenn Sie das Azure AD-Modul noch nicht installiert haben, verwenden Sie den Befehl `Install-Module -Name AzureAD`. Wenn Sie zum Installieren eines NuGet-Moduls oder des neuen Azure Active Directory V2-PowerShell-Moduls aufgefordert werden, geben Sie „J“ ein, und drücken Sie die EINGABETASTE.
- Azure Active Directory Premium P1- oder P2-Edition für die gruppenbasierte Zuweisung. Weitere Informationen zu Lizenzierungsanforderungen für die in diesem Artikel erläuterten Features finden Sie in der [Preisübersicht für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).
- Optional: Gehen Sie den Schnellstart [Konfigurieren einer App](add-application-portal-configure.md) durch.

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Zuweisen von Benutzern und Gruppen zu einer App mithilfe von PowerShell

1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
1. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an.
1. Weisen Sie mithilfe des folgenden Skripts einer Anwendung einen Benutzer und eine Rolle zu:

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

To assign a group to an enterprise app, you must replace `Get-AzureADUser` with `Get-AzureADGroup` and replace `New-AzureADUserAppRoleAssignment` with `New-AzureADGroupAppRoleAssignment`.

For more information about how to assign a group to an application role, see the documentation for [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### Example

This example assigns the user Britta Simon to the Microsoft Workplace Analytics application using PowerShell.

1. In PowerShell, assign the corresponding values to the variables $username, $app_name and $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"

1. In this example, we don't know what is the exact name of the application role we want to assign to Britta Simon. Run the following commands to get the user ($user) and the service principal ($sp) using the user UPN and the service principal display names.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"

1. Run the command `$sp.AppRoles` to display the roles available for the Workplace Analytics application. In this example, we want to assign Britta Simon the Analyst (Limited access) Role.
   ![Shows the roles available to a user using Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
1. Assign the role name to the `$app_role_name` variable.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

1. Run the following command to assign the user to the app role:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Aufheben der Zuweisung von Benutzern und Gruppen zu einer App mithilfe von PowerShell

1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
1. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an. Entfernen Sie mithilfe des folgenden Skripts einen Benutzer und eine Rolle aus einer Anwendung:

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

- [Zuweisen oder Aufheben der Zuweisung eines Benutzers oder einer Gruppe mithilfe des Azure-Portals](add-application-portal-assign-users.md)
- [Zuweisen von Benutzern und Gruppen für eine App und Aufheben ihrer Zuweisung mit der Graph-API](/graph/api/resources/approleassignment)
- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)

## <a name="next-steps"></a>Nächste Schritte

- [Ausblenden einer App für einen Benutzer](hide-application-from-user-portal.md)
