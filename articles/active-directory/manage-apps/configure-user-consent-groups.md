---
title: Konfigurieren der Gruppenbesitzereinwilligung für Apps, die auf Gruppendaten zugreifen
titleSuffix: Azure AD
description: Hier erfahren Sie, ob Gruppen- und Teambesitzer in Anwendungen einwilligen können, die Zugriff auf die Daten der Gruppe bzw. des Teams haben werden.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/31/2021
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5195b32c05b5f7f8e10e22200c2a345248e43490
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550758"
---
# <a name="configure-group-owner-consent-to-applications"></a>Konfigurieren der Gruppenbesitzereinwilligung für Anwendungen

Gruppen- und Teambesitzer können Anwendungen (z. B. von Drittanbietern veröffentlichte Anwendungen) autorisieren, auf die einer Gruppe zugeordneten Daten Ihrer Organisation zuzugreifen. Beispielsweise kann ein Teambesitzer in Microsoft Teams einer App gestatten, alle Teamnachrichten des Teams zu lesen oder das Basisprofil der Mitglieder einer Gruppe aufzulisten. Weitere Informationen finden Sie unter [Resource-specific consent in Microsoft Teams](/microsoftteams/resource-specific-consent) (Ressourcenspezifische Einwilligung in Microsoft Teams).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Rolle „Globaler Administrator“
- Einrichten von Azure AD PowerShell. Weitere Informationen finden Sie unter [Azure AD PowerShell](/powershell/azure/).

## <a name="manage-group-owner-consent-to-apps"></a>Verwalten der Gruppenbesitzereinwilligung für Apps

Sie können konfigurieren, welche Benutzer in den Zugriff von Apps auf die Daten ihrer Gruppen oder Teams einwilligen dürfen, oder Sie können diese Funktion für alle Benutzer deaktivieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte zum Verwalten der Gruppenbesitzereinwilligung für Apps aus, die auf Gruppendaten zugreifen:

1. Melden Sie sich als [globaler Administrator](../roles/permissions-reference.md#global-administrator) beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Einwilligung und Berechtigungen** > **Einstellungen für Benutzereinwilligung** aus.
3. Wählen Sie unter **Gruppenbesitzereinwilligung für Apps für Datenzugriff** die Option aus, die Sie aktivieren möchten.
4. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.

In diesem Beispiel können alle Gruppenbesitzer einwilligen, dass Apps auf die Daten ihrer Gruppen zugreifen:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Einstellungen für die Gruppenbesitzereinwilligung":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Mit dem Azure AD PowerShell-Vorschaumodul ([AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)) können Sie aktivieren oder deaktivieren, ob Gruppenbesitzer in Anwendungen einwilligen können, die auf die Daten Ihrer Organisation für die Gruppen zugreifen, die sie besitzen.

1. Stellen Sie sicher, dass Sie das Modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) verwenden. Dieser Schritt ist wichtig, wenn auf Ihrem Computer sowohl das Modul [AzureAD](/powershell/module/azuread/) als auch das Modul [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) installiert ist.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Stellen Sie eine Verbindung mit Azure AD PowerShell her.

   ```powershell
   Connect-AzureAD
   ```

1. Rufen Sie den aktuellen Wert für die Verzeichniseinstellungen der **Zustimmungsrichtlinieneinstellungen** in Ihrem Mandanten ab. Dazu muss überprüft werden, ob die Verzeichniseinstellungen für dieses Feature erstellt wurden, und wenn dies nicht der Fall ist, müssen die Werte aus der entsprechenden Vorlage für die Verzeichniseinstellungen verwendet werden.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Sie müssen die Einstellungswerte verstehen. Es gibt zwei Einstellungswerte, die definieren, welche Benutzer einer App Zugriff auf die Daten ihrer Gruppe gewähren dürfen:

    | Einstellung       | type         | BESCHREIBUNG  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean | Flag, das angibt, ob Gruppenbesitzer gruppenspezifische Berechtigungen erteilen dürfen. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Wenn _EnableGroupSpecificConsent_ auf TRUE und dieser Wert auf die Objekt-ID einer Gruppe festgelegt ist, werden die Mitglieder der identifizierten Gruppe autorisiert, den Gruppen, die Sie besitzen, gruppenspezifische Berechtigungen zu erteilen. |

1. Aktualisieren Sie die Einstellungswerte für die gewünschte Konfiguration:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Speichern Sie die Einstellungen.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

> [!NOTE]
> Das Deaktivieren der Einstellung „Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten“ führt nicht zur Deaktivierung der Option „Benutzer können den Apps Zugriff auf Unternehmensdaten für Gruppen gewähren, für die sie als Besitzer fungieren.“.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Konfigurieren von Einstellungen für die Benutzereinwilligung](configure-user-consent.md)
* [Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)
* Weitere Informationen zum [Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen](manage-consent-requests.md)
* [Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung](grant-admin-consent.md)
* [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)

So erhalten Sie Hilfe oder finden Antworten auf Ihre Fragen:

* [Azure AD bei Microsoft Q&A](/answers/topics/azure-active-directory.html)