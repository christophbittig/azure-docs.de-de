---
title: Auflisten von Azure AD-Rollendefinitionen – Azure AD
description: Erfahren Sie, wie Sie in Azure integrierte und benutzerdefinierte Rollen auflisten.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/23/2021
ms.author: rolyon
ms.reviewer: absinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5131cb56ff65b6c559186cf491c4367ecbc7d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340179"
---
# <a name="list-azure-ad-role-definitions"></a>Auflisten von Azure AD-Rollendefinitionen

Eine Rollendefinition ist eine Sammlung von ausführbaren Berechtigungen wie etwa Lesen, Schreiben und Löschen. Sie wird in der Regel einfach als „Rolle“ bezeichnet. Azure Active Directory enthält über 60 integrierte Rollen. Sie können aber auch eigene benutzerdefinierte Rollen erstellen. Falls Sie sich jemals gefragt haben, welchen Zweck diese Rollen haben, können Sie für jede der Rollen eine ausführliche Liste mit den Berechtigungen anzeigen.

In diesem Artikel wird beschrieben, wie Sie die in Azure AD integrierten und die benutzerdefinierten Rollen zusammen mit ihren Berechtigungen auflisten.

## <a name="prerequisites"></a>Voraussetzungen

- AzureADPreview-Modul bei Verwendung von PowerShell
- Administratorzustimmung bei Verwendung von Graph-Tester für die Microsoft Graph-API

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder bei [Azure AD Admin Center](https://aad.portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus, um die Liste mit allen verfügbaren Rollen anzuzeigen.

    ![Liste der Rollen im Azure-Portal](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Wählen Sie auf der rechten Seite die Auslassungspunkte und dann **Beschreibung** aus, um die vollständige Liste der Berechtigungen für eine Rolle anzuzeigen.

    Die Seite enthält Links zu relevanter Dokumentation, die Ihnen bei der Verwaltung von Rollen als Unterstützung dienen soll.

    ![Screenshot der Seite „Globaler Administrator – Beschreibung“](./media/role-definitions-list/role-description-updated.png)

## <a name="powershell"></a>PowerShell

Führen Sie diese Schritte aus, um Azure AD-Rollen über PowerShell aufzulisten.

1. Öffnen Sie ein PowerShell-Fenster, und verwenden Sie [Import-Module](/powershell/module/microsoft.powershell.core/import-module), um das AzureADPreview-Modul zu importieren. Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

2. Verwenden Sie in einem PowerShell-Fenster [Connect-AzureAD](/powershell/module/azuread/connect-azuread), um sich bei Ihrem Mandanten anzumelden.

    ```powershell
    Connect-AzureAD
    ```
3. Verwenden Sie [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition), um alle Rollen abzurufen.

    ```powershell
    Get-AzureADMSRoleDefinition
    ```

4. Verwenden Sie das folgende Cmdlet, um die Liste der Berechtigungen einer Rolle anzuzeigen.
    
    ```powershell
    # Do this avoid truncation of the list of permissions
    $FormatEnumerationLimit = -1
    
    (Get-AzureADMSRoleDefinition -Filter "displayName eq 'Conditional Access Administrator'").RolePermissions | Format-list
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Befolgen Sie diese Anweisungen, um Azure AD-Rollen mithilfe der Microsoft Graph-API in [Graph-Tester](https://aka.ms/ge) aufzulisten.

1. Melden Sie sich bei [Graph-Tester](https://aka.ms/ge) an.
2. Wählen Sie in der Dropdownliste **GET** als HTTP-Methode aus. 
3. Wählen Sie **beta** für die API-Version aus.
4. Fügen Sie die folgende Abfrage hinzu, um die API [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) zu verwenden.

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
   ```

5. Wählen Sie **Abfrage ausführen** aus, um die Rollen auflisten.
6. Um die Berechtigungen einer Rolle anzuzeigen, verwenden Sie die folgende API.

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=DisplayName eq 'Conditional Access Administrator'&$select=rolePermissions
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Auflisten von Azure AD-Rollenzuweisungen](view-assignments.md).
* [Zuweisen von Azure AD-Rollen zu Benutzern](manage-roles-portal.md).
* [Integrierte Rollen in Azure AD](permissions-reference.md).
