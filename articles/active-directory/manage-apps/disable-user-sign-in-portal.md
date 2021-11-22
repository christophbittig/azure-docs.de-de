---
title: Deaktivieren des Anmeldeverfahrens für Benutzer
titleSuffix: Azure AD
description: Deaktivieren einer Unternehmens-App in Azure Active Directory, damit sich Benutzer nicht mehr bei der App anmelden können
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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72f0dc346e23d78ac9725b388b99f1a440136672
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548404"
---
# <a name="disable-how-a-user-signs-in-for-an-application"></a>Deaktivieren, wie sich ein Benutzer bei einer Anwendung anmeldet

In diesem Artikel deaktivieren Sie, wie sich ein Benutzer bei einer Anwendung in Azure Active Directory anmeldet.

## <a name="prerequisites"></a>Voraussetzungen

Um zu deaktivieren, wie sich ein Benutzer anmeldet, müssen Sie:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

## <a name="disable-how-a-user-signs-in"></a>Deaktivieren der Benutzeranmeldung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie **Unternehmensanwendungen**.
1. Suchen Sie nach der Anwendung, die Sie für die Anmeldung eines Benutzers sperren möchten, und wählen Sie die Anwendung aus.
1. Wählen Sie **Eigenschaften** aus.
1. Wählen Sie **„Nein“** für **„Aktiviert für Benutzer zur Anmeldung?“**
1. Wählen Sie **Speichern** aus.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Deaktivieren einer nicht aufgelisteten App mit Azure AD PowerShell

Stellen Sie sicher, dass Sie das AzureAD-Modul installiert haben (verwenden Sie den Befehl Install-Module -Name AzureAD). Falls Sie aufgefordert werden, ein NuGet-Modul oder das neue „Azure Active Directory V2 PowerShell“-Modul zu installieren, geben Sie Y ein und drücken Sie die Eingabetaste.

Wenn Sie die App-ID einer App kennen, die nicht in der Liste der Unternehmens-Apps angezeigt wird (weil Sie z.B. die App gelöscht haben oder der Dienstprinzipal noch nicht erstellt wurde, da die App von Microsoft vorab autorisiert wurde), können Sie den Dienstprinzipal für die App manuell erstellen und die App dann mit einem [AzureAD PowerShell-Cmdlet](/powershell/module/azuread/New-AzureADServicePrincipal) deaktivieren.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App](./assign-user-or-group-access-portal.md)
