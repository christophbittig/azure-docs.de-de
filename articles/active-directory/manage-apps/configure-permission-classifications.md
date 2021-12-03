---
title: Konfigurieren von Berechtigungsklassifizierungen
titleSuffix: Azure AD
description: Hier erfahren Sie, wie Sie delegierte Berechtigungsklassifizierungen verwalten können.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 7981bdf26d3bafe301667e02af3fecdf53931021
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548442"
---
# <a name="configure-permission-classifications"></a>Konfigurieren von Berechtigungsklassifizierungen

In diesem Artikel erfahren Sie, wie Sie Berechtigungsklassifizierungen in Azure Active Directory (Azure AD) konfigurieren. Mit Berechtigungsklassifizierungen können Sie die Auswirkung ermitteln, die unterschiedliche Berechtigungen gemäß den Richtlinien Ihres Unternehmens und den Risikobewertungen haben. Beispielsweise können Sie Berechtigungsklassifizierungen in Einwilligungsrichtlinien verwenden, um den Berechtigungssatz zu identifizieren, dem Benutzer zustimmen dürfen.

Derzeit wird nur die Berechtigungsklassifizierung „Geringe Auswirkung“ unterstützt. Nur delegierte Berechtigungen, für die keine Administratoreinwilligung erforderlich ist, können als „Geringe Auswirkung“ klassifiziert werden.

Die für die einfache Anmeldung erforderlichen Mindestberechtigungen sind `openid`, `profile`, `email`, `User.Read` und `offline_access` – alle delegierte Berechtigungen für Microsoft Graph. Mit diesen Berechtigungen kann eine App die vollständigen Profildetails des angemeldeten Benutzers lesen und diesen Zugriff auch dann beibehalten, wenn der Benutzer die App nicht mehr verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren von Berechtigungsklassifizierungen sind folgende Voraussetzungen erforderlich:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

## <a name="manage-permission-classifications"></a>Verwalten von Berechtigungsklassifizierungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte zum Klassifizieren von Berechtigungen über das Azure-Portal aus:

1. Melden Sie sich als [globaler Administrator](../roles/permissions-reference.md#global-administrator), als [Anwendungsadministrator](../roles/permissions-reference.md#application-administrator) oder als [Cloudanwendungsadministrator](../roles/permissions-reference.md#cloud-application-administrator) beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Einwilligung und Berechtigungen** > **Berechtigungsklassifizierungen** aus.
1. Wählen Sie **Berechtigungen hinzufügen** aus, um eine weitere Berechtigung als „Geringe Auswirkung“ zu klassifizieren.
1. Wählen Sie die API und dann die delegierten Berechtigungen aus.

In diesem Beispiel haben wir den minimalen Berechtigungssatz klassifiziert, der für einmaliges Anmelden erforderlich ist:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Berechtigungsklassifizierungen":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie können das neueste Azure AD PowerShell-Vorschaumodul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) verwenden, um Berechtigungen zu klassifizieren. Berechtigungsklassifizierungen werden für das **ServicePrincipal**-Objekt der API konfiguriert, die die Berechtigungen veröffentlicht.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Auflisten der aktuellen Berechtigungsklassifizierungen für eine API

1. Rufen Sie das **ServicePrincipal**-Objekt für die API ab. Hier rufen wir das ServicePrincipal-Objekt für die Microsoft Graph-API ab:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Lesen Sie die Klassifizierungen für delegierte Berechtigungen für die API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Klassifizieren einer Berechtigung als „Geringe Auswirkung“

1. Rufen Sie das **ServicePrincipal**-Objekt für die API ab. Hier rufen wir das ServicePrincipal-Objekt für die Microsoft Graph-API ab:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Suchen Sie nach der delegierten Berechtigung, die Sie klassifizieren möchten:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Legen Sie die Berechtigungsklassifizierung mithilfe des Berechtigungsnamens und der ID fest:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Entfernen einer delegierten Berechtigungsklassifizierung

1. Rufen Sie das **ServicePrincipal**-Objekt für die API ab. Hier rufen wir das ServicePrincipal-Objekt für die Microsoft Graph-API ab:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Suchen Sie nach der Klassifizierung für eine delegierte Berechtigung, die Sie entfernen möchten:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Löschen Sie die Berechtigungsklassifizierung:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

- Wechseln Sie zu [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)
