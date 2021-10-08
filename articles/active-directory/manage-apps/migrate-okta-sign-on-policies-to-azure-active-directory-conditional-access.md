---
title: Anleitung zur Migration von Okta-Anmelderichtlinien zu Azure Active Directory Conditional Access
titleSuffix: Active Directory
description: Erfahren Sie, wie Sie Okta-Anmelderichtlinien zu Azure Active Directory Conditional Access migrieren können.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: c36ad9b56ce49234d2ee28f53073267944152db0
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080433"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>Anleitung: Migrieren Sie Okta-Anmelderichtlinien zu Azure Active Directory Conditional Access

In diesem Tutorial erfahren Sie, wie Unternehmen von globalen oder anwendungsbezogenen Anmeldungsrichtlinien in Okta zu Azure Active Directory (AD) Conditional Access (CA)-Richtlinien migrieren können, um den Benutzerzugriff in Azure AD und verbundenen Anwendungen zu sichern.

In diesem Tutorial wird davon ausgegangen, dass Sie über einen Office 365-Tenant verfügen, der für die Anmeldung und Multifaktor-Authentifizierung (MFA) mit Okta föderiert ist. Außerdem sollten Sie Azure AD Connect Server oder Azure AD Connect Cloud Provisioning Agents für die Bereitstellung von Benutzern in Azure AD konfiguriert haben.

## <a name="prerequisites"></a>Voraussetzungen

Beim Wechsel von Okta sign on zu Azure AD CA ist es wichtig, die Lizenzierungsanforderungen zu verstehen. Azure AD CA erfordert, dass Benutzern eine Azure AD Premium P1-Lizenz zugewiesen wird, bevor sie sich für Azure AD Multi-Faktor-Authentifizierung registrieren.

Bevor Sie einen der Schritte für den hybriden Azure AD-Beitritt ausführen, benötigen Sie eine Unternehmensadministrator-Anmeldeinformation in der lokalen Gesamtstruktur, um den SCP-Datensatz (Service Connection Point) zu konfigurieren.

## <a name="step-1---catalog-current-okta-sign-on-policies"></a>Schritt 1 - Katalogisieren der aktuellen Okta-Anmeldungsrichtlinien

Für eine erfolgreiche Umstellung auf CA sollten die bestehenden Okta-Anmelderichtlinien ausgewertet werden, um die Anwendungsfälle und Anforderungen zu ermitteln, die auf Azure AD übertragen werden sollen.

1. Überprüfen Sie die globalen Anmeldungsrichtlinien, indem Sie zu **Sicherheit** navigieren, **Authentifizierung** auswählen und dann **Anmeldung**.

   ![Bild zeigt globale Anmeldungsrichtlinien](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   In diesem Beispiel erzwingt unsere globale Anmeldungsrichtlinie MFA für alle Sitzungen außerhalb unserer konfigurierten Netzwerkzonen.

   ![Abbildung zeigt globale Anmeldungsrichtlinien, die MFA erzwingen](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. Navigieren Sie als Nächstes zu **Anwendungen**, und überprüfen Sie die Anmeldungsrichtlinien auf Anwendungsebene. Wählen Sie **Anwendungen** aus dem Untermenü, und wählen Sie dann Ihre mit Office 365 verbundene Instanz aus der Liste **Aktive Anwendungen**.

3. Wählen Sie schließlich **Anmelden** und scrollen Sie bis zum Ende der Seite.

Im folgenden Beispiel hat unsere Office 365-Anmeldungsrichtlinie vier separate Regeln.

- **Erzwinge MFA für mobile Sitzungen** - Erfordert MFA von jeder modernen Authentifizierungs- oder Browsersitzung auf iOS oder Android.

- **Vertraute Windows-Geräte zulassen** - Verhindert, dass Ihre vertrauenswürdigen Okta-Geräte zu einer zusätzlichen Verifizierung oder zu Faktoren aufgefordert werden.

- **MFA von nicht vertrauenswürdigen Windows-Geräten verlangen** - Erfordert MFA bei jeder modernen Authentifizierung oder Browser-Sitzung auf nicht vertrauenswürdigen Windows-Geräten.

- **Blockieren von Legacy-Authentifizierung** - Verhindert, dass sich Clients mit Legacy-Authentifizierung mit dem Dienst verbinden.

  ![Abbildung zeigt o365-Anmelderegeln](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="step-2---configure-condition-pre-requisites"></a>Schritt 2 - Konfigurieren der Bedingungsvoraussetzungen

Azure AD CA-Richtlinien können für die meisten Szenarien ohne zusätzliche Konfiguration so konfiguriert werden, dass sie mit den Bedingungen von Okta übereinstimmen.

In einigen Szenarien müssen Sie möglicherweise zusätzliche Einstellungen vornehmen, bevor Sie die CA-Richtlinien konfigurieren. Zum Zeitpunkt der Erstellung dieses Artikels sind folgende zwei Szenarien bekannt:

- **Okta-Netzwerkstandorte zu benannten Standorten in Azure AD** - Folgen Sie [diesem Artikel](../conditional-access/location-condition.md#named-locations), um benannte Standorte in Azure AD zu konfigurieren.

- **Okta Gerätevertrauen zu gerätebasierter CA** - CA bietet zwei mögliche Optionen bei der Bewertung des Geräts eines Benutzers.

  - [Azure AD Hybrid-Verbindung](#hybrid-azure-ad-join-configuration) - Eine Funktion, die innerhalb des Azure AD Connect-Servers aktiviert ist und aktuelle Windows-Geräte, wie Windows 10, Server 2016 und 2019, mit Azure AD synchronisiert.

  - [Registrieren Sie das Gerät in Microsoft Endpoint Manager](#configure-device-compliance) und weisen Sie eine Compliance-Richtlinie zu.

### <a name="hybrid-azure-ad-join-configuration"></a>Azure AD Hybrid-Verbindungskonfiguration

Die Aktivierung des hybriden Azure AD Join kann auf Ihrem Azure AD Connect Server erfolgen, indem Sie den Konfigurationsassistenten ausführen. Nach der Konfiguration müssen Schritte unternommen werden, um Geräte automatisch zu registrieren.

>[!NOTE]
>Azure AD Hybrid Join wird von den Azure AD Connect Cloud Provisioning Agents nicht unterstützt.

1. Befolgen Sie diese [Anweisungen](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join), um Azure AD Hybrid Join zu aktivieren.

2. Wählen Sie auf der SCP-Konfigurationsseite das Dropdown-Menü **Authentifizierungsdienst**. Wählen Sie die URL Ihres Okta-Verbundanbieters, gefolgt von **Hinzufügen**. Geben Sie die Anmeldedaten Ihres lokalen Unternehmensadministrators ein und wählen Sie dann **Weiter**.

   ![Abbildung zeigt scp-Konfiguration](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

3. Wenn Sie die Legacy-Authentifizierung auf Windows-Clients entweder in der globalen oder der App-Anmelderichtlinie blockiert haben, erstellen Sie eine Regel, um den Abschluss des hybriden Azure AD-Beitrittsprozesses zu ermöglichen.

4. Sie können entweder den gesamten Legacy-Authentifizierungsstapel für alle Windows-Clients zulassen oder sich an den Okta-Support wenden, um die benutzerdefinierte Client-Zeichenfolge für Ihre bestehenden Anwendungsrichtlinien zu aktivieren.

### <a name="configure-device-compliance"></a>Konfigurieren Sie die Gerätekonformität

Während der hybride Azure AD-Beitritt ein direkter Ersatz für das Okta-Gerätevertrauen unter Windows ist, können CA-Richtlinien auch die Gerätekonformität für Geräte prüfen, die sich vollständig beim Microsoft Endpoint Manager angemeldet haben.

- **Übersicht über die Konformität** - Siehe [Geräte-Compliance-Richtlinien in Microsoft Intune](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows).

- **Gerätekonformität** - Erstellen Sie [Richtlinien in Microsoft Intune](/mem/intune/protect/create-compliance-policy).

- **Windows-Registrierung** - Wenn Sie sich für eine hybride Azure AD-Verbindung entschieden haben, kann eine zusätzliche Gruppenrichtlinie bereitgestellt werden, um den [Autoregistrierungsprozess dieser Geräte in Microsoft Intune](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy) abzuschließen.

- **iOS/iPadOS-Registrierung** - Vor der Registrierung eines iOS-Geräts müssen [zusätzliche Konfigurationen](/mem/intune/enrollment/ios-enroll) in der Endpoint Management Console vorgenommen werden.

- **Android-Registrierung** - Vor der Registrierung eines Android-Geräts müssen [zusätzliche Konfigurationen](/mem/intune/enrollment/android-enroll) in der Endpoint Management-Konsole vorgenommen werden.

## <a name="step-3---configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Schritt 3 - Konfigurieren der Azure AD Multi-Faktor-Authentifizierungs-Mandanteneinstellungen

Bestätigen Sie vor der Umstellung auf CA die Grundeinstellungen des Azure AD-Multifaktorauthentifizierungs-Mandanten für Ihre Organisation.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com) und melden Sie sich mit einem globalen Administratorkonto an.

1. Wählen Sie **Azure Active Directory**, gefolgt von **Benutzer** und dann **Multi-Faktor-Authentifizierung**. Dadurch gelangen Sie zum Legacy Azure MFA-Portal.

   ![Das Bild zeigt das alte Azure AD Multi-Factor-Authentifizierungsportal.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   Stattdessen können Sie **<https://aka.ms/mfaportal>** verwenden.

1. Ändern Sie im Menü **Legacy Azure MFA** das Statusmenü durch **enabled** und **enforced**, um zu bestätigen, dass Sie keine Benutzer für Legacy MFA aktiviert haben. Wenn Ihr Mandant Benutzer in den unten aufgeführten Ansichten hat, müssen Sie diese im Legacy-Menü deaktivieren. Erst dann werden die CA-Richtlinien für ihr Konto wirksam.

   ![Das Bild zeigt einen deaktivierten Benutzer im alten Azure AD Multi-Faktor-Authentifizierungsportal](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   Das Feld **Erzwungen** sollte ebenfalls leer sein.

   ![Das Bild zeigt, dass das Feld Erzwungen im Azure AD Multi-Faktor-Authentifizierungsportal leer ist.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enforced-empty-legacy-azure-ad-portal.png)

1. Nachdem Sie bestätigt haben, dass keine Benutzer für Legacy-MFA konfiguriert sind, wählen Sie die Option **Diensteinstellungen**. Ändern Sie die Auswahl **App-Passwörter** auf **Nicht zulassen, dass Benutzer App-Passwörter erstellen, um sich bei Nicht-Browser-Apps anzumelden**.

1. Stellen Sie sicher, dass die Kontrollkästchen **Multifaktorauthentifizierung für Anforderungen von Verbundbenutzern in meinem Intranet überspringen** und **Benutzern erlauben, sich die Multifaktorauthentifizierung auf Geräten, denen sie vertrauen, zu merken (zwischen einem und 365 Tagen)** nicht markiert sind, und wählen Sie dann **Speichern**.

   >[!NOTE]
   >Siehe [Best Practices für die Konfiguration von MFA-Aufforderungseinstellungen](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

   ![Das Bild zeigt die nicht aktivierten Felder im Azure AD Multi-Faktor-Authentifizierungsportal](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="step-4---configure-ca-policies"></a>Schritt 4 - Konfigurieren Sie CA-Richtlinien

Nachdem Sie die Voraussetzungen konfiguriert und die Grundeinstellungen festgelegt haben, ist es an der Zeit, die erste CA-Richtlinie zu erstellen.

1. Um CA-Richtlinien in Azure AD zu konfigurieren, navigieren Sie zum [Azure-Portal](https://portal.azure.com). Wählen Sie **Ansicht** auf Azure Active Directory verwalten.

2. Bei der Konfiguration von CA-Richtlinien sollten Sie die [Best Practices für die Bereitstellung und Gestaltung von CA](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components) berücksichtigen.

3. Um die globale Anmelde-MFA-Richtlinie von Okta zu imitieren, [erstellen Sie eine Richtlinie](../conditional-access/howto-conditional-access-policy-all-users-mfa.md).

4. Erstellen Sie eine [auf Gerätevertrauen basierende CA-Regel](../conditional-access/require-managed-devices.md).

5. Diese Richtlinie kann wie alle anderen in diesem Tutorial auf eine bestimmte Anwendung, eine Testgruppe von Benutzern oder beides ausgerichtet werden.

   ![Bild zeigt Testbenutzer](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![Das Bild zeigt den Erfolg beim Testbenutzer](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

6. Nachdem Sie die standortbasierte Richtlinie und die Richtlinie für Gerätevertrauen konfiguriert haben, ist es an der Zeit, die entsprechende Richtlinie [**Block Legacy Authentication**](../conditional-access/howto-conditional-access-policy-block-legacy.md) zu konfigurieren.

Mit diesen drei CA-Richtlinien wurde die ursprüngliche Okta-Anmelderichtlinienerfahrung in Azure AD repliziert. Die nächsten Schritte umfassen die Anmeldung des Benutzers bei Azure MFA und das Testen der Richtlinien.

## <a name="step-5---enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Schritt 5 - Registrierung von Pilotmitgliedern in Azure AD Multi-Faktor-Authentifizierung

Sobald die CA-Richtlinien konfiguriert sind, müssen sich die Benutzer für Azure MFA-Methoden registrieren. Die Benutzer können sich über verschiedene Methoden registrieren lassen.

1. Für eine individuelle Registrierung können Sie die Benutzer zu <https://aka.ms/mfasetup> leiten, um die Registrierungsinformationen manuell einzugeben.

2. Benutzer können zu <https://aka.ms/mysecurityinfo> gehen, um Informationen einzugeben oder die Form der MFA-Registrierung zu verwalten.

Siehe [diesen Leitfaden](../authentication/howto-registration-mfa-sspr-combined.md), um den MFA-Registrierungsprozess vollständig zu verstehen.  

Navigieren Sie zu <https://aka.ms/mfasetup>, nachdem Sie sich mit Okta MFA angemeldet haben, werden Sie angewiesen, sich für MFA mit Azure AD zu registrieren.

>[!NOTE]
>Wenn die Registrierung für diesen Benutzer bereits in der Vergangenheit stattgefunden hat, wird er nach Beantwortung der MFA-Aufforderung zur Informationsseite **Meine Sicherheit** weitergeleitet.

Siehe die [Endbenutzer-Dokumentation zur MFA-Registrierung](../user-help/security-info-setup-signin.md).

## <a name="step-6---enable-ca-policies"></a>Schritt 6 - Aktivieren von CA-Richtlinien

1. Um den Testbetrieb aufzunehmen, ändern Sie die in den früheren Beispielen erstellten Richtlinien in **Aktivierte Testbenutzeranmeldung**. 

   ![Bild zeigt aktivierten Testbenutzer](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

2. Bei der nächsten Anmeldung bei Office 365 wird der Testbenutzer John Smith aufgefordert, sich mit Okta MFA und Azure AD Multi-Factor-Authentifizierung anzumelden.

   ![Bild zeigt die Anmeldung über Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

3. Schließen Sie die MFA-Verifizierung über Okta ab.

   ![Bild zeigt die MFA-Verifizierung durch Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

4. Nachdem der Benutzer die MFA-Aufforderung von Okta abgeschlossen hat, wird er aufgefordert, eine CA anzugeben. Stellen Sie sicher, dass die Richtlinien ordnungsgemäß konfiguriert wurden und die Bedingungen für die Auslösung von MFA erfüllt sind.

   ![Das Bild zeigt die MFA-Verifizierung durch Okta mit der Aufforderung zur Eingabe einer CA](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="step-7---cutover-from-sign-on-to-ca-policies"></a>Schritt 7 - Umstellung von der Anmeldung auf CA-Richtlinien

Nach der Durchführung gründlicher Tests mit den Pilotmitgliedern, um sicherzustellen, dass CA wie erwartet funktioniert, können die verbleibenden Organisationsmitglieder nach Abschluss der Registrierung zu CA-Richtlinien hinzugefügt werden.

Um eine doppelte Eingabeaufforderung zwischen Azure MFA und Okta MFA zu vermeiden, sollten Sie Okta MFA abwählen, indem Sie die Anmeldungsrichtlinien ändern.

Der abschließende Migrationsschritt zu CA kann schrittweise oder in einem Übergang erfolgen.

1. Navigieren Sie zur Okta-Verwaltungskonsole, wählen Sie **Sicherheit**, gefolgt von **Authentifizierung**, und navigieren Sie dann zu **Anmelderichtlinien**.

>[!NOTE]
>Globale Richtlinien sollten nur dann auf inaktiv gesetzt werden, wenn alle Anwendungen von Okta durch ihre eigenen Anwendungsanmeldungsrichtlinien geschützt sind.

2. Setzen Sie die Richtlinie Enforce MFA auf **Inaktiv** oder weisen Sie die Richtlinie einer neuen Gruppe zu, die unsere Azure AD-Nutzer nicht enthält.

   ![Bild zeigt MFA-Richtlinie auf inaktiv](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

3. Aktualisieren Sie in der Anmeldungsrichtlinie auf Anwendungsebene die Richtlinien auf inaktiv, indem Sie die Option **Regel deaktivieren** wählen. Sie können die Richtlinie auch einer neuen Gruppe zuweisen, die nicht die Azure AD-Benutzer enthält.

4. Stellen Sie sicher, dass es mindestens eine Anmeldungsrichtlinie auf Anwendungsebene gibt, die für die Anwendung aktiviert ist, die den Zugriff ohne MFA erlaubt.

   ![Bild zeigt Anwendungszugriff ohne MFA](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

5. Nach der Deaktivierung der Okta-Anmelderichtlinien oder dem Ausschluss der migrierten Azure AD-Benutzer aus den Erzwingungsgruppen sollten die Benutzer bei ihrer nächsten Anmeldung zur Eingabe der CA aufgefordert werden **nur**.

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren von Anwendungen von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [Migration von Okta-Verbund zu Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md)
