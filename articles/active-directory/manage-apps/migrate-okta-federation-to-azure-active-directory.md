---
title: Tutorial zum Migrieren des Okta-Verbunds zur verwalteten Azure AD-Authentifizierung
titleSuffix: Active Directory
description: Erfahren Sie, wie Sie Ihre Okta-Verbundanwendungen zur verwalteten Azure AD-Authentifizierung migrieren.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: b2486f4be20d2347f0cadff04ef8d0aa776ccdc5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791686"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>Tutorial: Migrieren des Okta-Verbunds zur verwalteten Azure Active Directory-Authentifizierung

In diesem Tutorial erfahren Sie, wie Sie Ihre vorhandenen Office 365-Mandanten mit Okta mit den Funktionen für einmaliges Anmelden (SSO) verbinden.

Die Migration des Verbunds zu Azure Active Directory (AD) kann schrittweise erfolgen, um die gewünschte Authentifizierung für Benutzer sicherzustellen. Außerdem kann der Verbundzugriff zurück auf die verbleibenden Okta-SSO-Anwendungen getestet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Office 365-Mandant, verbunden mit Okta für einmaliges Anmelden
- Konfigurieren eines Azure AD Connect-Servers oder von Agents für die Azure AD Connect-Cloudbereitstellung für die Benutzerbereitstellung für Azure AD

## <a name="step-1---configure-azure-ad-connect-for-authentication"></a>Schritt 1: Konfigurieren von Azure AD Connect für die Authentifizierung

Wenn Sie Ihre Office 365-Domänen mit Okta in einem Verbund konfiguriert haben, verfügen Sie derzeit möglicherweise nicht über eine gültige in Azure AD konfigurierte Authentifizierungsmethode. Vor der Migration zur verwalteten Authentifizierung muss Azure AD Connect überprüft und mit einer der folgenden Optionen konfiguriert werden, um die Benutzeranmeldung zu ermöglichen.

Verwenden Sie die folgenden Methoden, um zu bestimmen, welche Methode sich am besten für Ihre Umgebung eignet:

- **Kennwort-Hashsynchronisierung** - Die [Kennwort-Hashsynchronisierung](../hybrid/whatis-phs.md) ist eine Erweiterung der durch den Azure AD Connect-Server oder die Agents für die Cloudbereitstellung implementierten Verzeichnissynchronisierungsfunktion. Diese Funktion kann für die Anmeldung bei Azure AD-Diensten wie Microsoft 365 verwendet werden. Sie melden sich bei dem Dienst mit dem gleichen Kennwort an, das Sie zur Anmeldung bei Ihrer lokalen Active Directory-Instanz verwenden.

- **Passthrough-Authentifizierung:** Mit der Azure AD-[Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) können sich Benutzer mit den gleichen Kennwörtern bei lokalen und bei cloudbasierten Anwendungen anmelden. Wenn Benutzer sich über Azure AD anmelden, werden mit dieser Funktion die Benutzerkennwörter direkt anhand der lokalen Active Directory-Instanz über den Agent für die Passthrough-Authentifizierung überprüft.

- **Nahtloses einmaliges Anmelden** - [Mit dem nahtlosen einmaligen Anmelden mit Azure AD](../hybrid/how-to-connect-sso.md) werden Benutzer automatisch angemeldet, wenn sie an ihren mit dem Unternehmensnetzwerk verbundenen Unternehmens-Desktops arbeiten. Nahtloses einmaliges Anmelden ermöglicht Ihren Benutzern einen einfachen Zugriff auf Ihre cloudbasierten Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind.

Das nahtlose einmalige Anmelden kann auch für die Kennwort-Hashsynchronisierung oder die Passthrough-Authentifizierung bereitgestellt werden, um Benutzern in Azure AD eine nahtlose Authentifizierung zu ermöglichen.

Stellen Sie sicher, dass Sie alle erforderlichen Voraussetzungen für nahtloses einmaliges Anmelden für Ihre Endbenutzer bereitstellen, indem Sie die Anweisungen im [Bereitstellungsleitfaden](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) befolgen.

In diesem Beispiel werden die Kennwort-Hashsynchronisierung und nahtloses einmaliges Anmelden konfiguriert.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Konfigurieren von Azure AD Connect für die Kennwort-Hashsynchronisierung und nahtloses einmaliges Anmelden

Führen Sie die folgenden Schritte aus, um Azure AD Connect für die Kennwort-Hashsynchronisierung zu konfigurieren:

1. Starten Sie auf Ihrem Azure AD Connect-Server die App **Azure AD Connect** über das Startmenü oder das Desktopsymbol, und wählen Sie **Konfigurieren** aus.

   ![Abbildung des Azure AD-Symbols und der Schaltfläche „Konfigurieren“](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

2. Wählen Sie **Benutzeranmeldung ändern** > **Weiter** aus.

   ![Abbildung des Bildschirms „Benutzeranmeldung ändern“](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

3. Geben Sie auf der nächsten Seite die Anmeldeinformationen des globalen Administrators ein.

   ![Abbildung der Eingabe der Anmeldeinformationen des globalen Administrators](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

4. Derzeit ist der Server für den Verbund mit Okta konfiguriert. Ändern Sie die Auswahl in „Kennwort-Hashsynchronisierung“. Aktivieren Sie das Kontrollkästchen **Einmaliges Anmelden aktivieren**.

5. Wählen Sie anschließend die Schaltfläche **Weiter** aus.

Gehen Sie zum Aktivieren des nahtlosen einmaligen Anmeldens wie folgt vor:

1. Geben Sie die Anmeldeinformationen des Domänenadministrators für die lokale Umgebung ein, und wählen Sie **Weiter**  aus.

   ![Abbildung der Eingabe der Anmeldeinformationen des Domänenadministrators](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

2. Wählen Sie auf der letzten Seite die Option **Konfigurieren** aus, um den Azure AD Connect-Server zu aktualisieren.

   ![Abbildung: Aktualisieren des Azure AD Connect-Servers](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

3. Die Warnung zu Azure AD Hybrid Join können Sie vorerst ignorieren. Die **Geräteoptionen** müssen jedoch nach dem Deaktivieren des Verbunds mit Okta neu konfiguriert werden.

   ![Abbildung der Neukonfiguration der Geräteoptionen](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="step-2---configure-staged-rollout-features"></a>Schritt 2: Konfigurieren von Features für den gestaffelten Rollout

Der [gestaffelte Rollout der Cloudauthentifizierung](../hybrid/how-to-connect-staged-rollout.md) ist ein Feature von Azure AD, mit dem die Aufhebung des Verbunds für Benutzer getestet werden kann, bevor die Aufhebung des Verbunds für eine gesamte Domäne erfolgt. Überprüfen Sie vor der Bereitstellung die entsprechenden [Voraussetzungen](../hybrid/how-to-connect-staged-rollout.md#prerequisites).

Führen Sie nach dem Aktivieren der Kennwort-Hashsynchronisierung und des nahtlosen einmaligen Anmeldens auf dem Azure AD Connect-Server die folgenden Schritte aus, um den gestaffelten Rollout zu konfigurieren.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/#home) die Option **Ansicht** oder **Azure Active Directory verwalten** aus.

   ![Abbildung des Azure-Portals](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

2. Wählen Sie im Menü „Azure Active Directory“ die Option **Azure AD Connect** aus, und vergewissern Sie sich, dass die Kennwort-Hashsynchronisierung im Mandanten als aktiviert angezeigt wird.

3. Wählen Sie nach der Überprüfung den Link **Gestaffelten Rollout für verwaltete Benutzeranmeldung aktivieren** aus.

   ![Abbildung der Aktivierung des gestaffelten Rollouts](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

4. Die Einstellung für die Kennwort-Hashsynchronisierung wurde möglicherweise nach dem Konfigurieren des Servers in **Ein** geändert. Wenn dies nicht der Fall ist, aktivieren Sie sie jetzt. Sie können dann feststellen, dass nahtloses einmaliges Anmelden auf **Aus** festgelegt ist. Wenn Sie versuchen, diese Einstellung im Menü zu aktivieren, wird in einer Fehlermeldung angezeigt, dass sie für Benutzer im Mandanten bereits aktiviert ist.

5. Wählen Sie nach dem Aktivieren der Kennwort-Hashsynchronisierung die Option **Gruppen verwalten** aus.

   ![Abbildung der Aktivierung der Kennwort-Hashsynchronisierung](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

Befolgen Sie die Anweisungen zum Hinzufügen einer Gruppe zum Rollout der Kennwort-Hashsynchronisierung. Im folgenden Beispiel wird eine Sicherheitsgruppe mit zunächst 10 Mitgliedern verwendet.

![Abbildung eines Beispiels für eine Sicherheitsgruppe](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

Warten Sie nach dem Hinzufügen der Gruppe ca. 30 Minuten, bis das Feature im Mandanten wirksam wird. Anschließend werden Ihre Benutzer nicht mehr an Okta weitergeleitet, wenn sie auf Office 365-Dienste zugreifen möchten.

Bei einem gestaffelten Rollout werden einige Szenarien nicht unterstützt:  

- Die Legacyauthentifizierung wie POP3 und SMTP wird nicht unterstützt.

- Wenn Sie Azure AD Hybrid Join zur Verwendung mit Okta konfiguriert haben, werden alle Azure AD Hybrid Join-Flows weiterhin zu Okta geleitet, bis der Verbund für die Domäne entfernt wurde. Eine Anmelderichtlinie sollte in Okta verbleiben, die die Legacyauthentifizierung für Azure AD Hybrid Join für Windows-Clients ermöglicht.

## <a name="step-3---create-okta-app-in-azure-ad"></a>Schritt 3: Erstellen einer Okta-App in Azure AD

Benutzer, die auf die verwaltete Authentifizierung umgestellt werden, verfügen möglicherweise noch über Anwendungen in Okta, auf die sie zugreifen müssen. Diesen Benutzern kann ein einfacher Zugriff auf diese Anwendungen ermöglicht werden. Erfahren Sie, wie Sie eine Azure AD-Anwendungsregistrierung konfigurieren, die für Benutzer mit der Okta-Homepage verknüpft ist.

1. Zum Konfigurieren der Unternehmensanwendungsregistrierung für Okta wechseln Sie zum [Azure-Portal](https://portal.azure.com/#home). Wählen Sie unter **Azure Active Directory verwalten** die Option **Ansicht** aus.

2. Wählen Sie dann im Menü unter dem Bereich „Verwalten“ die Option **Unternehmensanwendungen** aus.

   ![Abbildung der Option „Unternehmensanwendungen“](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

3. Wählen Sie im Menü **Alle Anwendungen** die Option **Neue Anwendung** aus.

   ![Abbildung der Option „Neue Anwendung“](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

4. Wählen Sie **Eigene Anwendung erstellen** aus, und geben Sie der Okta-App im angezeigten Seitenmenü einen Namen. Wählen Sie anschließend das Optionsfeld **Von Ihnen bearbeitete Anwendung für die Integration in Azure AD registrieren** und dann **Erstellen** aus.

   ![Abbildung der Registrierung einer Anwendung](media/migrate-okta-federation-to-azure-active-directory/register-application.png)

5. Nachdem Sie die Anwendung registriert haben, wählen Sie für die Anwendung die Option „Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)“ und dann **Registrieren** aus.

   ![Abbildung der Registrierung einer Anwendung und der Änderung des Anwendungskontos](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

6. Wechseln Sie nach dem Hinzufügen der Registrierung zurück zum Azure AD-Menü, und wählen Sie **App-Registrierungen** aus. Öffnen Sie dann die neu erstellte Registrierung.

   ![Abbildung der Option „App-Registrierungen“](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

7. Notieren Sie sich nach dem Öffnen der Anwendung Ihre Mandanten-ID und die Anwendungs-ID.

   >[!Note]
   >Sie benötigen die Mandanten-ID und die Anwendungs-ID später zum Konfigurieren des Identitätsanbieters in Okta.

   ![Abbildung mit Mandanten-ID und Anwendungs-ID](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

8. Wählen Sie **Zertifikate und Geheimnisse** im linken Menü aus. Wählen Sie **Neuer geheimer Clientschlüssel** aus, geben Sie ihm einen generischen Namen, und legen Sie die Ablaufzeit fest.

9. Notieren Sie sich den Wert und die ID des Geheimnisses, bevor Sie diese Seite schließen.

   >[!NOTE]
   >Sie können diese Informationen später nicht mehr notieren. Stattdessen müssen Sie das Geheimnis neu erstellen, wenn die Daten verloren gehen.

   ![Abbildung geheimer Clientschlüssel](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

10. Wählen Sie im linken Menü die Option **API-Berechtigungen** aus, und gewähren Sie der Anwendung Zugriff auf den OpenID Connect-Stapel (OIDC).

11. Wählen Sie **Berechtigung hinzufügen** > **Microsoft Graph** > **Delegierte Berechtigungen** aus.

    ![Abbildung der Option „Delegierte Berechtigungen“](media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png)

12. Fügen Sie im Bereich „OpenID-Berechtigungen“ **email**, **openID** und **profile** hinzu, und wählen Sie dann **Berechtigungen hinzufügen** aus.

    ![Abbildung des Hinzufügens von Berechtigungen](media/migrate-okta-federation-to-azure-active-directory/add-permissions.png)

13. Wählen Sie die Option **Administratorzustimmung für „Mandantendomänenname“ erteilen** aus, und warten Sie, bis der Status „Gewährt“ angezeigt wird.

    ![Abbildung der Einwilligung](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

14. Nachdem die Berechtigungen erteilt wurden, fügen Sie in **Branding** unter „URL der Startseite“ die Homepage der Anwendung des Benutzers ein.

    ![Abbildung des Hinzufügens von Branding](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

15. Wechseln Sie nach dem Konfigurieren der Anwendung zum Okta-Verwaltungsportal, und konfigurieren Sie Microsoft als Identitätsanbieter. Wählen Sie **Security** > **Identity Providers** (Sicherheit >Identitätsanbieter) aus, um einen neuen Identitätsanbieter hinzuzufügen. Wählen Sie die Standardoption **Microsoft** aus.

    ![Abbildung: Konfigurieren des Identitätsanbieters](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

16. Kopieren Sie auf der Seite „Identity Provider“ (Identitätsanbieter) Ihre Anwendungs-ID in das Feld „Client ID“ (Client-ID) und den geheimen Clientschlüssel in das Feld „Client Secret“ (Geheimer Clientschlüssel).

17. Wählen Sie **Show Advanced Settings** (Erweiterte Einstellungen anzeigen) aus. Standardmäßig werden dadurch die Benutzerprinzipalnamen (UPN) in Okta und Azure AD für den umgekehrten Verbundzugriff verknüpft.

    >[!IMPORTANT]
    >Wenn Ihre UPNs in Okta und Azure AD nicht übereinstimmen, wählen Sie ein gemeinsames Attribut der Benutzer aus.

18. Nehmen Sie die Auswahl für die automatische Bereitstellung vor. Wenn ein Benutzer nicht in Okta übereinstimmt, wird standardmäßig versucht, ihn in Azure AD bereitzustellen. Wenn Sie die Bereitstellung von Okta migriert haben, wählen Sie die Option **Redirect to Okta Sign-in page** (Zur Okta-Anmeldeseite umleiten) aus.

    ![Abbildung der Option „Redirect to Okta Sign-in page“ (Zur Okta-Anmeldeseite umleiten)](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    Nach dem Erstellen des Identitätsanbieters ist eine zusätzliche Konfiguration erforderlich, um Benutzer an den richtigen Identitätsanbieter weiterzuleiten.

19. Wählen Sie im Menü „Identity Providers“ (Identitätsanbieter) die Option **Routing Rules** (Routingregeln) und dann **Add Routing Rule** (Routingregel hinzufügen) unter Verwendung eines der im Okta-Profil verfügbaren Attribute aus.

20. Konfigurieren Sie die Richtlinie wie gezeigt, um Anmeldungen von allen Geräten und IP-Adressen an Azure AD weiterzuleiten.

    In diesem Beispiel wird das Attribut **division** in keinem Okta-Profil verwendet, sodass es einfach für das Routing an Identitätsanbieter verwendet werden kann.

    ![Abbildung des Attributs „division“ für das IdP-Routing](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

21. Notieren Sie sich nach dem Hinzufügen der Routingregel den Umleitungs-URI, um ihn in der **Anwendungsregistrierung** hinzufügen zu können.

    ![Abbildung der Anwendungsregistrierung](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

22. Navigieren Sie zurück zu Ihrer Anwendungsregistrierung, und wählen Sie die Registerkarte „Authentifizierung“ und dann **Plattform hinzufügen** und **Web** aus.

    ![Abbildung der Option „Plattform hinzufügen“](media/migrate-okta-federation-to-azure-active-directory/add-platform.png)

23. Fügen Sie den Umleitungs-URI aus dem Identitätsanbieter in Okta hinzu, und wählen Sie dann **Zugriffstoken und ID-Token** aus.

    ![Abbildung der Zugriffs- und ID-Token von Okta](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

24. Wählen Sie in der Verwaltungskonsole die Option **Verzeichnis** > **Personen** aus. Wählen Sie den ersten Testbenutzer und das zugehörige Profil aus.

25. Fügen Sie beim Bearbeiten des Profils wie im Beispiel gezeigt **ToAzureAD** hinzu, und wählen Sie dann **Speichern** aus.

    ![Abbildung der Bearbeitung des Profils](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

26. Versuchen Sie nach dem Speichern der Benutzerattribute, sich als geänderter Benutzer beim [Microsoft 356-Portal](https://portal.office.com) anzumelden. Wenn der Benutzer nicht dem Pilotprojekt für die verwaltete Authentifizierung angehört, werden Sie feststellen, dass eine Schleife durchlaufen wird. Damit der Benutzer die Schleife beenden kann, fügen Sie ihn der verwalteten Authentifizierung hinzu.

## <a name="step-4---test-okta-app-access-on-pilot-members"></a>Schritt 4: Testen des Zugriffs der Okta-App für Pilotmitglieder

Nach der Konfiguration der Okta-App in Azure AD und des Identitätsanbieters im Okta-Portal müssen Sie die Anwendung Benutzern zuweisen.

1. Wählen Sie im Azure-Portal die Optionen **Azure Active Directory** > **Unternehmensanwendungen** aus.

2. Wählen Sie die zuvor erstellte App-Registrierung aus, und navigieren Sie zu **Benutzer und Gruppen**. Fügen Sie die Gruppe hinzu, die dem Pilotprojekt für die verwaltete Authentifizierung entspricht.

>[!NOTE]
>Benutzer und Gruppen können nur über die Auswahl „Unternehmensanwendungen“ hinzugefügt werden. Im Menü „App-Registrierungen“ können keine Benutzer hinzugefügt werden.

   ![Abbildung des Hinzufügens einer Gruppe](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

3. Melden Sie sich nach ca. 15 Minuten als einer der Benutzer des Pilotprojekts für die verwaltete Authentifizierung an, und greifen Sie auf [Meine Apps](https://myapplications.microsoft.com) zu.

   ![Abbildung des Zugriffs auf „Meine Apps“](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

4. Nach der Authentifizierung wird die Kachel **Okta Application Access** (Zugriff auf Okta-Anwendung) angezeigt, über die Benutzer wieder zur Okta-Homepage geleitet werden.

## <a name="step-5---test-managed-authentication-on-pilot-members"></a>Schritt 5: Testen der verwalteten Authentifizierung für Pilotmitglieder

Lassen Sie Ihre Benutzer nach der Konfiguration der Okta-App für den umgekehrten Verbundzugriff die verwaltete Authentifizierung umfassend testen. Es empfiehlt sich, ein Unternehmensbranding einzurichten, sodass die Benutzer den richtigen Mandanten erkennen können, bei dem sie sich anmelden. [Hier](../fundamentals/customize-branding.md) finden Sie einen Leitfaden zum Einrichten von Unternehmensbranding.

>[!IMPORTANT]
>Bestimmen Sie alle zusätzlichen Richtlinien für bedingten Zugriff, die möglicherweise erforderlich sind, bevor Sie den Verbund aller Domänen aus Okta entfernen. Schritte zum Schützen Ihrer Umgebung vor der vollständigen Trennung finden Sie unter **Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff**.

## <a name="step-6---remove-federation-for-office-365-domains"></a>Schritt 6: Entfernen des Verbunds für Office 365-Domänen

Wenn Ihre Organisation mit der verwalteten Authentifizierung zufrieden ist, kann der Verbund für die Domäne aus Okta entfernt werden. Dazu stellen Sie mit den folgenden Befehlen eine Verbindung mit MSOnline PowerShell her. Wenn Sie noch nicht über das MSOnline PowerShell-Modul verfügen, können Sie es zunächst über „install-module MSOnline“ herunterladen.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

Nach dem Festlegen der Domäne auf die verwaltete Authentifizierung haben Sie den Verbund für Ihren Office 365-Mandanten erfolgreich aus Okta entfernt und gleichzeitig sichergestellt, dass der Benutzerzugriff auf die Okta-Homepage erhalten bleibt. 

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md)

- [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)

- [Migrieren von Anwendungen von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)