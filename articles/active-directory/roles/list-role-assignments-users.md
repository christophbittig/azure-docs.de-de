---
title: Auflisten von Azure AD-Rollenzuweisungen für einen Benutzer – Azure Active Directory
description: Hier erfahren Sie, wie Sie Azure AD-Rollenzuweisungen eines Benutzers auflisten.
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
ms.openlocfilehash: 5ed1a0906e31691cae86547f165d5b44ac6e1f7c
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340346"
---
# <a name="list-azure-ad-role-assignments-for-a-user"></a>Auflisten von Azure AD-Rollenzuweisungen für einen Benutzer

Eine Rolle kann einem Benutzer direkt oder transitiv über eine Gruppe zugewiesen werden. In diesem Artikel wird beschrieben, wie Sie die einem Benutzer zugewiesenen Azure AD Rollen auflisten. Informationen zum Zuweisen von Rollen zu Gruppen finden Sie unter [Verwenden von Azure AD Gruppen zum Verwalten von Rollenzuweisungen](groups-concept.md).

## <a name="prerequisites"></a>Voraussetzungen

- AzureADPreview-Modul bei Verwendung von PowerShell
- Microsoft. Graph-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="azure-portal"></a>Azure-Portal
Führen Sie diese Schritte aus, um Azure AD-Rollen für einen Benutzer mithilfe des Azure-Portals aufzulisten. Die jeweilige Funktionalität hängt davon ab, ob [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) aktiviert ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure AD Admin Center](https://aad.portal.azure.com) an.

2. Wählen Sie **Azure Active Directory** > **Benutzer** > *Benutzername* > **Zugewiesene Rollen** aus.

    Sie können die Liste der Rollen anzeigen, die dem Benutzer in verschiedenen Bereichen zugewiesen sind. Darüber hinaus können Sie sehen, ob die Rolle direkt oder über eine Gruppe zugewiesen wurde.
    
    ![Liste der Rollen, die einem Benutzer zugewiesen sind, im Azure-Portal](./media/list-role-assignments-users/list-role-definition.png)

    Wenn Sie über eine Premium P2-Lizenz verfügen, wird die PIM-Benutzeroberfläche angezeigt, die über berechtigte, aktive und abgelaufene Rollenzuweisungsdetails verfügt.

    ![Liste der Rollen, die einem Benutzer zugewiesen sind, in PIM](./media/list-role-assignments-users/list-role-definition-pim.png)

## <a name="powershell"></a>PowerShell

Führen Sie diese Schritte aus, um mithilfe von PowerShell Azure AD-Rollen aufzulisten, die einem Benutzer zugewiesen sind.

1. Installieren Sie AzureADPreview und Microsoft.Graph-Modul mit [Install-module](/powershell/azure/active-directory/install-adv2).
  
    ```powershell
    Install-module -name AzureADPreview
    Install-module -name Microsoft.Graph
    ```
  
2. Öffnen Sie ein PowerShell-Fenster, und verwenden Sie [Import-module](/powershell/module/microsoft.powershell.core/import-module), um das AzureADPreview-Modul zu importieren. Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

3. Verwenden Sie in einem PowerShell-Fenster [Connect-AzureAD](/powershell/module/azuread/connect-azuread), um sich bei Ihrem Mandanten zu anmelden.

    ```powershell
    Connect-AzureAD
    ```
4. Verwenden Sie [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment), um Rollen abzurufen, die einem Benutzer direkt zugewiesen sind.

    ```powershell
    #Get the user
    $userId = (Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'").ObjectId

    #Get direct role assignments to the user
    $directRoles = (Get-AzureADMSRoleAssignment -Filter "principalId eq '$userId'").RoleDefinitionId
    ```

5. Verwenden Sie die folgenden Cmdlets, um dem Benutzer transitive Rollen zuzuweisen.
  
    a. Verwenden Sie [Get-AzureADMSGroup](/powershell/module/azuread/get-azureadmsgroup), um die Liste aller rollenzuweisbaren Gruppen abzurufen.  
  
      ```powershell
      $roleAssignableGroups = (Get-AzureADMsGroup -All $true | Where-Object IsAssignableToRole -EQ 'True').Id
      ```

    b. Verwenden Sie [Connect-MgGraph](/graph/powershell/get-started), um sich anzumelden und Microsoft Graph PowerShell-Cmdlets zu verwenden.
  
      ```powershell
      Connect-MgGraph -Scopes "User.Read.All”
      ```
  
    c. Verwenden Sie die [checkMemberObjects](/graph/api/user-checkmemberobjects)-API, um herauszufinden, in welcher der rollenzuweisbaren Gruppen der Benutzer Mitglied ist. 
    
      ```powershell
      $uri = "https://graph.microsoft.com/beta/directoryObjects/$userId/microsoft.graph.checkMemberObjects"

      $userRoleAssignableGroups = (Invoke-MgGraphRequest -Method POST -Uri $uri -Body @{"ids"= $roleAssignableGroups}).value
      ```
  
    d. Verwenden Sie [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment), um die Gruppen zu durchlaufen und die ihnen zugewiesenen Rollen abzurufen.
  
      ```powershell
      $transitiveRoles=@()
      foreach($item in $userRoleAssignableGroups){
          $transitiveRoles += (Get-AzureADMSRoleAssignment -Filter "principalId eq '$item'").RoleDefinitionId
      }
      ```

6. Kombinieren Sie sowohl direkte als auch transitive Rollenzuweisungen des Benutzers.
  
    ```powershell
    $allRoles = $directRoles + $transitiveRoles
    ```
  
## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Befolgen Sie diese Anweisungen, um Azure AD-Rollen mithilfe der Microsoft Graph-API in [Graph-Explorer](https://aka.ms/ge) aufzulisten.

1. Melden Sie sich bei [Graph-Explorer](https://aka.ms/ge) an.

1. Verwenden Sie die [List roleAssignments](/graph/api/rbacapplication-list-roleassignments)-API, um Rollen abzurufen, die einem Benutzer direkt zugewiesen sind. Fügen Sie die folgende Abfrage zur URL hinzu, und wählen Sie **Abfrage ausführen** aus.

   ```HTTP
   GET https://graph.microsoft.com/beta/rolemanagement/directory/roleAssignments?$filter=principalId eq '55c07278-7109-4a46-ae60-4b644bc83a31'
   ```
  
3. Führen Sie die folgenden Schritte aus, um die dem Benutzer zugewiesenen transitiven Rollen abzurufen.

    a. Verwenden Sie [Gruppen auflisten](/graph/api/group-list), um die Liste aller rollenzuweisbaren Gruppen abzurufen.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/groups?$filter=isAssignableToRole eq true 
      ```
  
    b. Übergeben Sie diese Liste der [checkMemberObjects](/graph/api/user-checkmemberobjects)-API, um herauszufinden, in welcher der rollenzuweisbaren Gruppen der Benutzer Mitglied ist. 
    
      ```HTTP
      POST https://graph.microsoft.com/beta/users/55c07278-7109-4a46-ae60-4b644bc83a31/checkMemberObjects
      {
          "ids": [
              "936aec09-47d5-4a77-a708-db2ff1dae6f2",
              "5425a4a0-8998-45ca-b42c-4e00920a6382",
              "ca9631ad-2d2a-4a7c-88b7-e542bd8a7e12",
              "ea3cee12-360e-411d-b0ba-2173181daa76",
              "c3c263bb-b796-48ee-b4d2-3fbc5be5f944"
          ]
      }
      ```
  
    c. Verwenden Sie die [List roleAssignments](/graph/api/rbacapplication-list-roleassignments)-API, um die Gruppen zu durchlaufen und die ihnen zugewiesenen Rollen abzurufen.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq '5425a4a0-8998-45ca-b42c-4e00920a6382' 
      ```

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten von Azure AD-Rollenzuweisungen](view-assignments.md)
* [Zuweisen von Azure AD-Rollen zu Benutzern](manage-roles-portal.md)
* [Zuweisen von Azure AD-Rollen zu Gruppen](groups-assign-role.md)