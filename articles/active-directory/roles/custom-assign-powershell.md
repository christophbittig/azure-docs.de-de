---
title: Zuweisen von benutzerdefinierten Rollen mit Azure AD PowerShell – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Mitglieder einer benutzerdefinierten Azure AD-Administratorrolle mithilfe von Azure AD PowerShell verwalten.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 09/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348f862c97b62ed4f1bab8bcf6aa418aaa43e7da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057303"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Zuweisen benutzerdefinierter Rollen mit Ressourcengeltungsbereich unter Verwendung von PowerShell in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie eine Rollenzuweisung mit organisationsweitem Geltungsbereich in Azure Active Directory (Azure AD) erstellen. Durch Zuweisen einer Rolle mit organisationsweitem Geltungsbereich wird Zugriff innerhalb der gesamten Azure AD-Organisation gewährt. Informationen zum Erstellen einer Rollenzuweisung mit einem Geltungsbereich für eine einzelne Azure AD-Ressource finden Sie unter [Erstellen einer benutzerdefinierten Rolle und Zuweisen der Rolle im Ressourcengeltungsbereich](custom-create.md). Dieser Artikel verwendet das [Azure Active Directory PowerShell Version 2](/powershell/module/azuread/#directory_roles)-Modul.

Weitere Informationen zu Azure AD-Rollen finden Sie unter [Integrierte Rollen in Azure AD](permissions-reference.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Lizenz vom Typ Azure AD Premium P1 oder P2
- „Administrator für privilegierte Rollen“ oder „Globaler Administrator“
- AzureADPreview-Modul bei Verwendung von PowerShell

Weitere Informationen finden Sie unter [Voraussetzungen für die Verwendung von PowerShell oder Graph-Tester](prerequisites.md).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Zuweisen einer Verzeichnisrolle zu einem Benutzer oder Dienstprinzipal mit Ressourcengeltungsbereich

1. Laden Sie das Azure AD PowerShell-Modul (Vorschauversion).
1. Führen Sie den Befehl `Connect-AzureAD` aus, um sich anzumelden.
1. Erstellen Sie mithilfe des folgenden PowerShell-Skripts eine neue Rolle:

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$directoryScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Falls Sie die Rolle keinem Benutzer, sondern einem Dienstprinzipal zuweisen möchten, verwenden Sie das Cmdlet [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

## <a name="role-definitions"></a>Rollendefinitionen

Rollendefinitionsobjekte enthalten die Definition der integrierten oder benutzerdefinierten Rolle sowie die Berechtigungen, die durch diese Rollenzuweisung erteilt werden. Diese Ressource zeigt sowohl benutzerdefinierte Rollendefinitionen als auch integrierte Verzeichnisrollen (in einem ähnlichen Format wie bei „roleDefinition“) an. Für eine Azure AD-Organisation können aktuell maximal 30 individuelle benutzerdefinierte Rollendefinitionen festgelegt werden.

### <a name="create-a-role-definition"></a>Erstellen einer Rollendefinition

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Lesen und Auflisten von Rollendefinitionen

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Aktualisieren einer Rollendefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Löschen einer Rollendefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Rollenzuweisungen

Rollenzuweisungen enthalten Informationen, die einen bestimmten Sicherheitsprinzipal (Benutzer oder Anwendungsdienstprinzipal) mit einer Rollendefinition verknüpfen. Bei Bedarf können Sie für die zugewiesenen Berechtigungen einen Geltungsbereich für eine einzelne Azure AD-Ressource hinzufügen.  Das Einschränken des Geltungsbereichs für eine Rollenzuweisung wird für integrierte und benutzerdefinierte Rollen unterstützt.

### <a name="create-a-role-assignment"></a>Erstellen einer Rollenzuweisung

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$directoryScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>Lesen und Auflisten von Rollenzuweisungen

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Löschen einer Rollenzuweisung

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Nächste Schritte

- Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) können Sie sich mit uns in Verbindung setzen.
- Weitere Informationen zu Rollen und Azure AD-Administratorrollenzuweisungen finden Sie unter [Zuweisen von Administratorrollen](permissions-reference.md).
- Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md).
