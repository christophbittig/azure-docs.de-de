---
title: Anleitung zur Migration von Okta-Anmelderichtlinien zum bedingten Azure Active Directory-Zugriff
titleSuffix: Active Directory
description: In diesem Tutorial wird beschrieben, wie Sie von der Nutzung von Okta-Anmelderichtlinien zum bedingten Azure Active Directory-Zugriff migrieren.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 5cca89b361152d3a4a5635f008f70f145d5d68a0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459430"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>Tutorial: Migrieren von Okta-Anmelderichtlinien zum bedingten Azure Active Directory-Zugriff

In diesem Tutorial wird beschrieben, wie Ihr Unternehmen von globalen oder anwendungsbezogenen Anmelderichtlinien in Okta zu Richtlinien für den bedingten Azure AD-Zugriff (Azure Active Directory Conditional Access) migrieren kann, um den Benutzerzugriff in Azure AD und für die verbundenen Anwendungen zu schützen.

In diesem Tutorial wird davon ausgegangen, dass Sie über einen Office 365-Mandanten verfügen, für den für die Anmeldung und mehrstufige Authentifizierung (MFA) ein Verbund mit Okta besteht. Außerdem sollten Sie Azure AD Connect Server oder Azure AD Connect Cloud Provisioning Agents für die Bereitstellung von Benutzern in Azure AD konfiguriert haben.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie von der Okta-Anmeldung zum bedingten Zugriff wechseln, ist es wichtig, dass Sie mit den Lizenzierungsanforderungen vertraut sind. Für den bedingten Zugriff muss Benutzer*innen eine Azure AD Premium P1-Lizenz zugewiesen werden, bevor die Registrierung für Azure AD Multi-Factor Authentication durchgeführt wird.

Bevor Sie einen der Schritte für Azure AD Hybrid Join ausführen, benötigen Sie Unternehmensadministrator-Anmeldeinformationen für die lokale Gesamtstruktur, um den Eintrag für den Dienstverbindungspunkt (Service Connection Point, SCP) zu konfigurieren.

## <a name="catalog-current-okta-sign-on-policies"></a>Katalogisieren der aktuellen Okta-Anmelderichtlinien

Gehen Sie wie folgt vor, damit die Umstellung auf den bedingten Zugriff erfolgreich ist: Evaluieren Sie die vorhandenen Okta-Anmelderichtlinien, um die Anwendungsfälle und Anforderungen zu ermitteln, die auf Azure AD umgestellt werden sollen.

1. Überprüfen Sie die globalen Anmelderichtlinien, indem Sie zu **Sicherheit** > **Authentifizierung** > **Anmelden** navigieren.

   ![Screenshot: Globale Anmelderichtlinien](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   In diesem Beispiel wird mit der globalen Anmelderichtlinie MFA für alle Sitzungen außerhalb unserer konfigurierten Netzwerkzonen erzwungen.

   ![Screenshot: Globale Anmelderichtlinien zur Erzwingung von MFA](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. Navigieren Sie zu **Anwendungen**, und überprüfen Sie die Anmelderichtlinien auf Anwendungsebene. Wählen Sie **Anwendungen** aus dem Untermenü, und wählen Sie dann Ihre mit Office 365 verbundene Instanz aus der Liste **Aktive Anwendungen**.

3. Wählen Sie **Anmelden** aus, und scrollen Sie bis zum Ende der Seite.

Im folgenden Beispiel verfügt die Anmelderichtlinie der Office 365-Anwendung über vier separate Regeln:

- **Erzwingen von MFA für mobile Sitzungen**: Macht MFA für jede moderne Authentifizierung oder Browsersitzung unter iOS oder Android obligatorisch.
- **Zulassen von vertrauten Windows-Geräten**: Verhindert, dass für Ihre vertrauenswürdigen Okta-Geräte weitere Verifizierungen bzw. Verifizierungsstufen angefordert werden.
- **Verlangen von MFA von nicht vertrauenswürdigen Windows-Geräten**: Macht MFA für jede moderne Authentifizierung oder Browsersitzung auf nicht vertrauenswürdigen Windows-Geräten obligatorisch.
- **Blockieren von Legacy-Authentifizierung**: Verhindert, dass Clients mit Legacy-Authentifizierung eine Verbindung mit dem Dienst herstellen.

  ![Screenshot: Office 365-Anmelderegeln](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="configure-condition-prerequisites"></a>Konfigurieren von Bedingungsvoraussetzungen

Richtlinien für den bedingten Zugriff können für die meisten Szenarien ohne zusätzliche Konfigurationsschritte so konfiguriert werden, dass die Okta-Bedingungen erfüllt sind.

Bei einigen Szenarien müssen Sie unter Umständen weitere Einrichtungsschritte ausführen, bevor Sie die Richtlinien für bedingten Zugriff konfigurieren. Zum Zeitpunkt der Erstellung dieses Artikels sind folgende zwei Szenarien bekannt:

- **Von Okta-Netzwerkadressen zu benannten Standorten in Azure AD**: Befolgen Sie die Anleitung unter [Verwenden der Standortbedingung in einer Richtlinie für bedingten Zugriff](../conditional-access/location-condition.md#named-locations), um benannte Standorte in Azure AD zu konfigurieren.
- **Von Okta-Gerätevertrauensstellung zu gerätebasiertem bedingtem Zugriff**: Beim bedingten Zugriff haben Sie zwei Optionen zum Evaluieren des Geräts eines Benutzers:

  - [Verwenden von Azure AD Hybrid Join:](#hybrid-azure-ad-join-configuration) Dies ist ein auf dem Azure AD Connect-Server aktivierbares Feature, mit dem aktuelle Windows-Geräte, z. B. mit Windows 10, Windows Server 2016 und Windows Server 2019, mit Azure AD synchronisiert werden können.
  - [Registrieren des Geräts bei Endpoint Manager:](#configure-device-compliance) Führen Sie die Registrierung durch, und weisen Sie eine Konformitätsrichtlinie zu.

### <a name="hybrid-azure-ad-join-configuration"></a>Azure AD Hybrid-Verbindungskonfiguration

Führen Sie den Konfigurations-Assistenten aus, um Azure AD Hybrid Join auf Ihrem Azure AD Connect-Server zu aktivieren. Sie müssen nach der Konfiguration die entsprechenden Schritte ausführen, damit Geräte automatisch registriert werden.

>[!NOTE]
>Azure AD Hybrid Join wird von den Azure AD Connect Cloud Provisioning Agents nicht unterstützt.

1. Befolgen Sie [diese Anleitung](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join), um Azure AD Hybrid Join zu aktivieren.

1. Wählen Sie auf der Seite **SCP-Konfiguration** das Dropdownmenü **Authentifizierungsdienst** aus. Wählen Sie die URL Ihres Okta-Verbundanbieters und dann die Option **Hinzufügen** aus. Geben Sie die Anmeldeinformationen Ihres lokalen Unternehmensadministrators ein, und wählen Sie dann **Weiter** aus.

   ![Screenshot: SCP-Konfiguration](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

1. Wenn Sie die Legacy-Authentifizierung auf Windows-Clients entweder in der globalen oder der App-Anmelderichtlinie blockiert haben, sollten Sie eine Regel erstellen, um den Abschluss des Azure AD Hybrid Join-Prozesses zuzulassen.

1. Lassen Sie den gesamten Legacy-Authentifizierungsstapel für alle Windows-Clients zu. Sie können sich auch an den Okta-Support wenden, um die entsprechende benutzerdefinierte Clientzeichenfolge für Ihre vorhandenen App-Richtlinien aktivieren zu lassen.

### <a name="configure-device-compliance"></a>Konfigurieren Sie die Gerätekonformität

Azure AD Hybrid Join ist ein direkter Ersatz für die Okta-Gerätevertrauensstellung unter Windows. Mit Richtlinien für bedingten Zugriff kann auch die Gerätekonformität für Geräte überprüft werden, die vollständig bei Endpoint Manager registriert sind:

- **Konformitätsübersicht**: Informationen hierzu finden Sie unter den [Konformitätsrichtlinien für Geräte in Intune](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows).
- **Gerätekonformität**: Erstellen Sie [Richtlinien in Intune](/mem/intune/protect/create-compliance-policy).
- **Windows-Registrierung:** Wenn Sie sich für die Bereitstellung von Azure AD Hybrid Join entschieden haben, können Sie eine weitere Gruppenrichtlinie bereitstellen, um den [Prozess für die automatische Registrierung dieser Geräte in Intune](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy) durchzuführen.
- **iOS/iPadOS-Registrierung**: Vor dem Registrieren eines iOS-Geräts müssen Sie auf der Endpoint Manager-Konsole [weitere Konfigurationen](/mem/intune/enrollment/ios-enroll) vornehmen.
- **Android-Registrierung**: Vor dem Registrieren eines Android-Geräts müssen Sie auf der Endpoint Manager-Konsole [weitere Konfigurationen](/mem/intune/enrollment/android-enroll) vornehmen.

## <a name="configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Konfigurieren der Einstellungen des Mandanten für die mehrstufige Azure AD-Authentifizierung

Bestätigen Sie vor der Umstellung auf den bedingten Zugriff, dass die Grundeinstellungen des Mandanten für die mehrstufige Azure AD-Authentifizierung für Ihre Organisation stimmen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit dem Konto eines globalen Administrators an.

1. Wählen Sie **Azure Active Directory** > **Benutzer** > **Mehrstufige Authentifizierung** aus, um zum Legacy-Portal für die mehrstufige Azure AD-Authentifizierung zu navigieren.

   ![Screenshot: Legacy-Portal für die mehrstufige Azure AD-Authentifizierung](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   Sie können auch den Legacy-Link verwenden, um zum [Portal für die mehrstufige Azure AD-Authentifizierung](https://aka.ms/mfaportal) zu navigieren.

1. Nehmen Sie im Legacy-Menü für die **mehrstufige Authentifizierung** Änderungen im Statusmenü bis zu den Optionen **Aktiviert** und **Erzwungen** vor, um sicherzustellen, dass bei Ihnen Legacy-MFA nicht für Benutzer aktiviert ist. Wenn Ihr Mandant über Benutzer in den folgenden Ansichten verfügt, müssen Sie diese im Legacy-Menü deaktivieren. Erst dann werden Richtlinien für bedingten Zugriff für die jeweiligen Konten wirksam.

   ![Screenshot: Deaktivieren eines Benutzers im Legacy-Portal für die mehrstufige Azure AD-Authentifizierung](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   Das Feld **Erzwungen** sollte ebenfalls leer sein.

1. Wählen Sie die Option **Diensteinstellungen** aus. Ändern Sie die Auswahl **App-Passwörter** auf **Nicht zulassen, dass Benutzer App-Passwörter erstellen, um sich bei Nicht-Browser-Apps anzumelden**.

   ![Screenshot der Einstellungen für das Anwendungskennwort, die Benutzer*innen das Erstellen von App-Kennwörtern verbieten](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/app-password-selection.png)

1. Stellen Sie sicher, dass die Kontrollkästchen **Für Anforderungen von Partnerbenutzern in meinem Intranet die mehrstufige Authentifizierung überspringen** und **Benutzern das Speichern der mehrstufigen Authentifizierung auf vertrauenswürdigen Geräten ermöglichen (zwischen 1 und 365 Tagen)** nicht aktiviert sind, und wählen Sie dann die Option **Speichern** aus.

  >[!NOTE]
   >Informieren Sie sich über die [bewährten Methoden für die Konfiguration der Einstellungen zur MFA-Aufforderung](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

![Screenshot: Deaktivierte Kontrollkästchen im Legacy-Portal für die mehrstufige Azure AD-Authentifizierung](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="configure-conditional-access-policies"></a>Konfigurieren von Richtlinien für bedingten Zugriff

Nachdem Sie die Voraussetzungen konfiguriert und die Grundeinstellungen festgelegt haben, können Sie die erste Richtlinie für bedingten Zugriff erstellen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um in Azure AD Richtlinien für bedingten Zugriff zu konfigurieren. Wählen Sie unter **Azure Active Directory verwalten** die Option **Ansicht** aus.

   Konfigurieren Sie Richtlinien für bedingten Zugriff, indem Sie die [bewährten Methoden für die Bereitstellung und den Entwurf des bedingten Zugriffs](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components) verwenden.

1. [Erstellen Sie eine Richtlinie](../conditional-access/howto-conditional-access-policy-all-users-mfa.md), um die globale MFA-Anmelderichtlinie von Okta zu imitieren.

1. Erstellen Sie eine [Regel für bedingten Zugriff, die auf der Gerätevertrauensstellung basiert](../conditional-access/require-managed-devices.md).

   Diese Richtlinie kann wie alle anderen in diesem Tutorial auf eine bestimmte Anwendung, eine Testgruppe mit Benutzern oder beides ausgerichtet werden.

   ![Screenshot: Testen eines Benutzers](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![Screenshot: Erfolgreicher Test eines Benutzers](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

1. Nachdem Sie die standortbasierte Richtlinie und die Richtlinie für die Gerätevertrauensstellung konfiguriert haben, können Sie die entsprechende Richtlinie zum [Blockieren der Legacy-Authentifizierung](../conditional-access/howto-conditional-access-policy-block-legacy.md) konfigurieren.

Mit diesen drei Richtlinien für bedingten Zugriff wird die ursprüngliche Umgebung mit den Okta-Anmelderichtlinien in Azure AD repliziert. Die nächsten Schritte umfassen die Registrierung der Benutzer*innen über Azure AD Multi-Factor Authentication und das Testen der Richtlinien.

## <a name="enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Registrieren von Pilotmitgliedern für die mehrstufige Azure AD-Authentifizierung

Nachdem Sie die Richtlinien für bedingten Zugriff konfiguriert haben, müssen sich Benutzer*innen für Azure AD Multi-Factor Authentication-Methoden registrieren. Die Benutzer können sich über verschiedene Methoden registrieren lassen.

1. Wenn Sie die individuelle Registrierung ermöglichen möchten, leiten Sie Benutzer an den [Bereich für die Microsoft-Anmeldung](https://aka.ms/mfasetup) weiter, in dem diese ihre Registrierungsinformationen manuell eingeben können.

1. Benutzer können auf die [Seite mit den Microsoft-Sicherheitsinformationen](https://aka.ms/mysecurityinfo) zugreifen, um Informationen einzugeben oder die MFA-Registrierung zu verwalten.

Siehe [diesen Leitfaden](../authentication/howto-registration-mfa-sspr-combined.md), um den MFA-Registrierungsprozess vollständig zu verstehen.

Navigieren Sie zum [Bereich für die Microsoft-Anmeldung](https://aka.ms/mfasetup). Nachdem Sie sich über die mehrstufige Okta-Authentifizierung angemeldet haben, werden Sie angewiesen, sich für MFA mit Azure AD zu registrieren.

>[!NOTE]
>Wenn die Registrierung für einen Benutzer bereits durchgeführt wurde, wird er nach Beantwortung der MFA-Aufforderung zur Informationsseite **Meine Sicherheit** weitergeleitet.
Weitere Informationen finden Sie in der [Benutzerdokumentation zur MFA-Registrierung](../user-help/security-info-setup-signin.md).

## <a name="enable-conditional-access-policies"></a>Aktivieren von Richtlinien für bedingten Zugriff

1. Um den Testbetrieb aufzunehmen, ändern Sie die in den früheren Beispielen erstellten Richtlinien in **Aktivierte Testbenutzeranmeldung**.

   ![Screenshot: Aktivieren eines Testbenutzers](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

1. Beim nächsten Zugriff auf den Bereich **Anmelden** von Office 365 wird der Testbenutzer John Smith aufgefordert, sich per mehrstufiger Okta-Authentifizierung und mehrstufiger Azure AD-Authentifizierung anzumelden.

   ![Screenshot: Azure-Bereich für die Anmeldung](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

1. Schließen Sie die MFA-Verifizierung über Okta ab.

   ![Screenshot: MFA-Verifizierung über Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

1. Nachdem der Benutzer die MFA-Aufforderung von Okta abgeschlossen hat, wird er zum Durchführen des bedingten Zugriffs aufgefordert. Stellen Sie sicher, dass die Richtlinien richtig konfiguriert wurden und die Bedingungen erfüllen, die für die MFA-Auslösung erforderlich sind.

   ![Screenshot: MFA-Verifizierung über Okta mit Aufforderung für bedingten Zugriff](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="cut-over-from-sign-on-to-conditional-access-policies"></a>Umstellen von der Anmeldung auf Richtlinien für bedingten Zugriff

Nachdem Sie für die Pilotmitglieder sorgfältig getestet und sichergestellt haben, dass der bedingte Zugriff wie erwartet funktioniert, können nach Abschluss des Registrierungsvorgangs die restlichen Mitglieder der Organisation hinzugefügt werden.

Um eine doppelte Aufforderung für Azure AD MFA und Okta MFA zu vermeiden, sollten Sie die Nutzung von Okta MFA deaktivieren, indem Sie die Anmelderichtlinien ändern.

Der abschließende Schritt der Migration zum bedingten Zugriff kann in Phasen oder als einmaliger Umstellungsvorgang erfolgen.

1. Navigieren Sie zur Okta-Verwaltungskonsole, wählen Sie **Sicherheit** > **Authentifizierung** aus, und greifen Sie dann auf **Anmelderichtlinie** zu.

   >[!NOTE]
   > Legen Sie globale Richtlinien nur dann auf **Inaktiv** fest, wenn alle Anwendungen von Okta durch eigene Anmelderichtlinien für Anwendungen geschützt sind.
1. Legen Sie die Richtlinie **MFA erzwingen** auf **Inaktiv** fest. Sie können die Richtlinie auch einer neuen Gruppe zuweisen, die nicht die Azure AD-Benutzer enthält.

   ![Screenshot: Globale Richtlinie für Anmeldung per MFA ist „Inaktiv“](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

1. Legen Sie im Bereich für die Anmelderichtlinien auf Anwendungsebene die Richtlinien auf **Inaktiv** fest, indem Sie die Option **Regel deaktivieren** auswählen. Sie können die Richtlinie auch einer neuen Gruppe zuweisen, die nicht die Azure AD-Benutzer enthält.

1. Stellen Sie sicher, dass mindestens eine anwendungsbezogene Anmelderichtlinie für die Anwendung aktiviert ist, für die der Zugriff ohne MFA zulässig ist.

   ![Screenshot: Anwendungszugriff ohne MFA](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

1. Nachdem Sie die Okta-Anmelderichtlinien deaktiviert oder die migrierten Azure AD-Benutzer aus den Erzwingungsgruppen ausgeschlossen haben, werden Benutzer bei der nächsten Anmeldung *nur* zum Durchführen des bedingten Zugriffs aufgefordert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Migration von Okta zu Azure AD finden Sie unter:

- [Migrieren von Anwendungen von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [Migration von Okta-Verbund zu Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md)
