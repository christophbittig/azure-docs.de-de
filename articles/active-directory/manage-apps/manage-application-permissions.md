---
title: Überprüfen von Berechtigungen, die Anwendungen erteilt wurden
titleSuffix: Azure AD
description: Hier erfahren Sie, wie Sie Berechtigungen für eine Anwendung in Azure Active Directory überprüfen und verwalten.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb8f00735e9455fd9d6adbdaf9e0cdec6940377
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553171"
---
# <a name="review-permissions-granted-to-applications"></a>Überprüfen von Berechtigungen, die Anwendungen erteilt wurden

In diesem Artikel erfahren Sie, wie Sie Berechtigungen überprüfen, die Anwendungen in Ihrem Azure Active Directory-Mandanten erteilt wurden. Sie müssen die Berechtigungen möglicherweise überprüfen, wenn Sie eine schädliche Anwendung erkannt haben oder wenn der Anwendung mehr Berechtigungen erteilt wurden als erforderlich.

Die in diesem Artikel aufgeführten Schritte gelten für alle Anwendungen, die Ihrem Azure Active Directory-Mandanten per Benutzer- oder Administratoreinwilligung hinzugefügt wurden. Weitere Informationen zur Einwilligung für Anwendungen finden Sie unter [Azure Active Directory-Zustimmungsframework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Voraussetzungen

Um Berechtigungen zu überprüfen, die Anwendungen erteilt wurden, benötigen Sie:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

Über das Azure AD-Portal können Sie kontextbezogene PowerShell-Skripts abrufen, um die Aktionen auszuführen.

## <a name="review-application-permissions"></a>Überprüfen von Anwendungsberechtigungen

Gehen Sie wie folgt vor, um Anwendungsberechtigungen zu überprüfen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) mit einer der Rollen an, die im Abschnitt „Voraussetzungen“ aufgeführt sind.
1. Wählen Sie **Azure Active Directory** und dann **Unternehmensanwendungen** aus.
1. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
1. Wählen Sie **Berechtigungen** aus. Klicken Sie in der Befehlsleiste auf **Berechtigungen überprüfen**.
![Screenshot des Fensters „Berechtigungen überprüfen“](./media/manage-application-permissions/review-permissions.png)
1. Geben Sie einen Grund an, warum Sie die Berechtigungen für die Anwendung überprüfen möchten, indem Sie eine der Optionen auswählen, die nach der Frage **„Warum möchten Sie Berechtigungen für diese Anwendung überprüfen?“** aufgelistet werden.

Jede Option generiert PowerShell-Skripts, mit denen Sie den Benutzerzugriff auf die Anwendung steuern und die Berechtigungen überprüfen können, die der Anwendung erteilt wurden. Informationen zum Steuern des Benutzerzugriffs auf eine Anwendung finden Sie unter [Aufheben des Zugriffs eines Benutzers auf eine Anwendung](methods-for-removing-user-access.md).

## <a name="revoke-permissions-using-powershell-commands"></a>Widerrufen von Berechtigungen mithilfe von PowerShell-Befehlen

Mit dem folgenden PowerShell-Skript werden alle Berechtigungen widerrufen, die der jeweiligen Anwendung erteilt wurden.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```

> [!NOTE]
> Durch das Widerrufen aktueller Berechtigungen wird eine erneute Benutzereinwilligung für die Anwendung nicht verhindert. Wenn Sie die Benutzereinwilligung blockieren möchten, lesen Sie [Konfigurieren der Benutzereinwilligung für Anwendungen](configure-user-consent.md).

## <a name="invalidate-the-refresh-tokens"></a>Machen Sie die Aktualisierungstoken ungültig.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des Workflows für die Administratoreinwilligung](configure-admin-consent-workflow.md)
