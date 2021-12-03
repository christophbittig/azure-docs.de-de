---
title: Tutorial zum Migrieren des Okta-Verbunds zur verwalteten Azure Active Directory-Authentifizierung
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
ms.openlocfilehash: ede3537ba6bd849a3792b11dffc96a073302db20
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451576"
---
# <a name="tutorial-migrate-okta-federation-to-azure-active-directory-managed-authentication"></a>Tutorial: Migrieren des Okta-Verbunds zur verwalteten Azure Active Directory-Authentifizierung

In diesem Tutorial erfahren Sie, wie Sie Ihre vorhandenen Office 365-Mandanten mit Okta mit den Funktionen für einmaliges Anmelden (SSO) verbinden.

Sie können den Verbund schrittweise zu Azure Active Directory (Azure AD) migrieren, um eine optimale Authentifizierung für Benutzer*innen sicherzustellen. Bei einer schrittweisen Migration können Sie außerdem den Verbundzugriff zurück auf verbleibende Okta-SSO-Anwendungen testen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Office 365-Mandant, verbunden mit Okta für einmaliges Anmelden
- Ein Azure AD Connect-Server oder Agents für die Azure AD Connect-Cloudbereitstellung, konfiguriert für die Benutzerbereitstellung in Azure AD

## <a name="configure-azure-ad-connect-for-authentication"></a>Konfigurieren von Azure AD Connect für die Authentifizierung

Wenn die Office 365-Domänen mit Okta in einem Verbund konfiguriert wurden, verfügen Sie derzeit möglicherweise nicht über eine gültige in Azure AD konfigurierte Authentifizierungsmethode. Überprüfen und konfigurieren Sie Azure AD Connect vor der Migration zur verwalteten Authentifizierung, um die Benutzeranmeldung zu ermöglichen.

Richten Sie die für Ihre Umgebung am besten geeignete Anmeldemethode ein:

- **Kennwort-Hashsynchronisierung:** Die [Kennwort-Hashsynchronisierung](../hybrid/whatis-phs.md) ist eine Erweiterung der durch den Azure AD Connect-Server oder die Agents für die Cloudbereitstellung implementierten Verzeichnissynchronisierungsfunktion. Dieses Feature kann für die Anmeldung bei Azure AD-Diensten wie Microsoft 365 verwendet werden. Sie melden sich bei dem Dienst mit dem gleichen Kennwort an, das Sie zur Anmeldung bei Ihrer lokalen Active Directory-Instanz verwenden.
- **Passthrough-Authentifizierung:** Mit der [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) von Azure AD können sich Benutzer*innen mit den gleichen Kennwörtern bei lokalen und bei cloudbasierten Anwendungen anmelden. Wenn Benutzer*innen sich über Azure AD anmelden, werden mit dem Agent für die Passthrough-Authentifizierung die Kennwörter direkt anhand der lokalen Active Directory-Instanz überprüft.
- **Nahtloses einmaliges Anmelden**: [Mit dem nahtlosen einmaligen Anmelden von Azure AD](../hybrid/how-to-connect-sso.md) werden Benutzer*innen automatisch angemeldet, wenn sie an ihren Unternehmensdesktops arbeiten, die mit dem Unternehmensnetzwerk verbunden sind. Nahtloses einmaliges Anmelden ermöglicht Benutzer*innen einen einfachen Zugriff auf cloudbasierte Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind.

Das nahtlose einmalige Anmelden kann für die Kennwort-Hashsynchronisierung oder die Passthrough-Authentifizierung bereitgestellt werden, um Benutzer*innen in Azure AD eine nahtlose Authentifizierung zu ermöglichen.

Befolgen Sie den [Bereitstellungsleitfaden](../hybrid/how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites), um sicherzustellen, dass Sie alle notwendigen Voraussetzungen für ein nahtloses einmaliges Anmelden für Ihre Benutzer*innen bereitstellen.

In diesem Beispiel werden die Kennwort-Hashsynchronisierung und nahtloses einmaliges Anmelden konfiguriert.

### <a name="configure-azure-ad-connect-for-password-hash-synchronization-and-seamless-sso"></a>Konfigurieren von Azure AD Connect für die Kennwort-Hashsynchronisierung und nahtloses einmaliges Anmelden

Führen Sie die folgenden Schritte aus, um Azure AD Connect für die Kennwort-Hashsynchronisierung zu konfigurieren:

1. Öffnen Sie auf Ihrem Azure AD Connect-Server die App **Azure AD Connect**, und wählen Sie **Konfigurieren** aus.

   ![Screenshot des Azure AD-Symbols und der Schaltfläche „Konfigurieren“](media/migrate-okta-federation-to-azure-active-directory/configure-azure-ad.png)

1. Wählen Sie **Benutzeranmeldung ändern** > **Weiter** aus.

   ![Screenshot der Seite zum Ändern der Benutzeranmeldung](media/migrate-okta-federation-to-azure-active-directory/change-user-signin.png)

1. Geben Sie Ihre Anmeldeinformationen als globaler Administrator ein.

   ![Screenshot zur Eingabe der Anmeldeinformationen als globaler Administrator](media/migrate-okta-federation-to-azure-active-directory/global-admin-credentials.png)

1. Derzeit ist der Server für den Verbund mit Okta konfiguriert. Ändern Sie die Auswahl in **Kennwort-Hashsynchronisierung**. Aktivieren Sie dann **Einmaliges Anmelden aktivieren**.

1. Klicken Sie auf **Weiter**.

Gehen Sie zum Aktivieren des nahtlosen einmaligen Anmeldens wie folgt vor:

1. Geben Sie die Anmeldeinformationen des Domänenadministrators für das lokale System ein. Wählen Sie **Weiter** aus.

   ![Screenshot der Einstellungen für die Benutzeranmeldung](media/migrate-okta-federation-to-azure-active-directory/domain-admin-credentials.png)

1. Wählen Sie auf der letzten Seite die Option **Konfigurieren** aus, um den Azure AD Connect-Server zu aktualisieren.

   ![Screenshot der Konfigurationsseite](media/migrate-okta-federation-to-azure-active-directory/update-azure-ad-connect-server.png)

1. Die Warnung zu Azure AD Hybrid Join können Sie vorerst ignorieren. Sie konfigurieren die Geräteoptionen neu, nachdem Sie den Verbund mit Okta entfernt haben.

   ![Screenshot mit dem Link zum Konfigurieren der Geräteoptionen](media/migrate-okta-federation-to-azure-active-directory/reconfigure-device-options.png)

## <a name="configure-staged-rollout-features"></a>Konfigurieren von Features für den gestaffelten Rollout

In Azure AD können Sie einen [gestaffelten Rollout der Cloudauthentifizierung](../hybrid/how-to-connect-staged-rollout.md) verwenden, um die Aufhebung des Verbunds für Benutzer*innen zu testen, bevor die Aufhebung des Verbunds für eine gesamte Domäne erfolgt. Überprüfen Sie vor der Bereitstellung die entsprechenden [Voraussetzungen](../hybrid/how-to-connect-staged-rollout.md#prerequisites).

Führen Sie nach dem Aktivieren der Kennwort-Hashsynchronisierung und des nahtlosen einmaligen Anmeldens auf dem Azure AD Connect-Server die folgenden Schritte aus, um einen gestaffelten Rollout zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/#home) die Option **Ansicht** oder **Azure Active Directory verwalten** aus.

   ![Screenshot des Azure-Portals](media/migrate-okta-federation-to-azure-active-directory/azure-portal.png)

1. Wählen Sie im Menü **Azure Active Directory** die Option **Azure AD Connect** aus. Vergewissern Sie sich dann, dass **Kennwort-Hashsynchronisierung** im Mandanten aktiviert ist.

1. Wählen Sie **Gestaffelten Rollout für verwaltete Benutzeranmeldung aktivieren** aus.

   ![Screenshot der Option zum Aktivieren des gestaffelten Rollouts](media/migrate-okta-federation-to-azure-active-directory/enable-staged-rollout.png)

1. Die Einstellung für **Kennwort-Hashsynchronisierung** wurde möglicherweise nach dem Konfigurieren des Servers in **Ein** geändert. Wenn die Einstellung nicht aktiviert ist, aktivieren Sie sie jetzt.

   Beachten Sie, dass das **nahtlose einmalige Anmelden** auf **Aus** festgelegt ist. Wenn Sie versuchen, diese Einstellung zu aktivieren, wird eine Fehlermeldung angezeigt, da sie für Benutzer*innen im Mandanten bereits aktiviert ist.

1. Wählen Sie **Gruppen verwalten** aus.

   ![Screenshot der Schaltfläche zum Verwalten von Gruppen](media/migrate-okta-federation-to-azure-active-directory/password-hash-sync.png)

Befolgen Sie die Anweisungen zum Hinzufügen einer Gruppe zum Rollout der Kennwort-Hashsynchronisierung. Im folgenden Beispiel umfasst die Sicherheitsgruppe zunächst 10 Mitglieder*innen.

![Screenshot mit einem Beispiel für eine Sicherheitsgruppe](media/migrate-okta-federation-to-azure-active-directory/example-security-group.png)

Warten Sie nach dem Hinzufügen der Gruppe etwa 30 Minuten, bis das Feature im Mandanten wirksam wird. Anschließend werden Ihre Benutzer nicht mehr an Okta weitergeleitet, wenn sie auf Office 365-Dienste zugreifen möchten.

Bei einem gestaffelten Rollout werden einige Szenarien nicht unterstützt:

- Die Legacyauthentifizierung wie POP3 und SMTP wird nicht unterstützt.
- Wenn Sie Azure AD Hybrid Join zum Verwenden mit Okta konfiguriert haben, werden alle Azure AD Hybrid Join-Flows zu Okta geleitet, bis die Domäne aus dem Verbund entfernt wird. Sie sollten Anmelderichtlinie in Okta belassen, um die Legacyauthentifizierung für Azure AD Hybrid Join für Windows-Clients zu ermöglichen.

## <a name="create-an-okta-app-in-azure-ad"></a>Erstellen einer Okta-App in Azure AD

Benutzer*innen, die zur verwalteten Authentifizierung gewechselt sind, benötigen möglicherweise weiterhin Zugriff auf Anwendungen in Okta. Um einen einfachen Zugriff auf diese Anwendungen zu ermöglichen, können Sie eine Azure AD-Anwendung registrieren, die mit der Okta-Homepage verknüpft ist.

So konfigurieren Sie die Unternehmensanwendungsregistrierung für Okta

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/#home) unter **Azure Active Directory verwalten** die Option **Ansicht** aus.

1. Wählen Sie im linken Menü unter **Verwalten** die Option **Unternehmensanwendungen** aus.

   ![Screenshot der Auswahl von „Unternehmensanwendungen“](media/migrate-okta-federation-to-azure-active-directory/enterprise-application.png)

1. Wählen Sie im Menü **Alle Anwendungen** die Option **Neue Anwendung** aus.

   ![Screenshot der Auswahl von „Neue Anwendung“](media/migrate-okta-federation-to-azure-active-directory/new-application.png)

1. Wählen Sie **Eigene Anwendung erstellen** aus. Geben Sie im daraufhin geöffneten Menü einen Namen für die Okta-App ein, und wählen Sie **Von Ihnen bearbeitete Anwendung für die Integration in Azure AD registrieren** aus. Klicken Sie anschließend auf **Erstellen**.

   :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/register-application.png" alt-text="Screenshot: Registrieren einer Anwendung" lightbox="media/migrate-okta-federation-to-azure-active-directory/register-application.png":::

1. Wählen Sie **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)**  > **Registrieren** aus.

   ![Screenshot zum Registrieren einer Anwendung und Ändern des Anwendungskontos](media/migrate-okta-federation-to-azure-active-directory/register-change-application.png)

1. Wählen Sie im Azure AD-Menü die Option **App-Registrierungen** aus. Öffnen Sie dann die neu erstellte Registrierung.

   ![Screenshot der neuen App-Registrierung](media/migrate-okta-federation-to-azure-active-directory/app-registration.png)

1. Notieren Sie sich Ihre Mandanten-ID und die Anwendungs-ID.

   >[!Note]
   >Sie benötigen die Mandanten-ID und die Anwendungs-ID zum Konfigurieren des Identitätsanbieters in Okta.

   ![Screenshot mit Mandanten-ID und Anwendungs-ID](media/migrate-okta-federation-to-azure-active-directory/record-ids.png)

1. Wählen Sie im linken Menü die Option **Zertifikate und Geheimnisse** aus. Wählen Sie dann **Neuer geheimer Clientschlüssel** aus. Geben Sie dem Clientschlüssel einen generischen Namen, und legen Sie das Ablaufdatum fest.

1. Notieren Sie sich den Wert und die ID des Clientschlüssels.

   >[!NOTE]
   >Der Wert und die ID werden später nicht mehr angezeigt. Wenn Sie diese Informationen zu diesem Zeitpunkt nicht notieren, müssen Sie den Clientschlüssel neu generieren.

   ![Screenshot, der zeigt, wann der Wert und die ID des Clientschlüssels zu notieren sind](media/migrate-okta-federation-to-azure-active-directory/record-secrets.png)

1. Wählen Sie im linken Menü die Option **API-Berechtigungen** aus. Gewähren Sie der Anwendung Zugriff auf den OpenID Connect-Stapel (OIDC).

1. Wählen Sie **Berechtigung hinzufügen** > **Microsoft Graph** > **Delegierte Berechtigungen** aus.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png" alt-text="Screenshot der delegierten Berechtigungen" lightbox="media/migrate-okta-federation-to-azure-active-directory/delegated-permissions.png":::

1. Fügen Sie im Bereich „OpenID-Berechtigungen“ **email**, **openid** und **profile** hinzu. Klicken Sie dann auf **Berechtigungen hinzufügen**.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png" alt-text="Screenshot zum Hinzufügen von Berechtigungen" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-permissions.png":::

1. Wählen Sie **Administratorzustimmung für \<tenant domain name> erteilen** aus, und warten Sie, bis der Status **Gewährt** angezeigt wird.

    ![Screenshot der erteilten Einwilligung](media/migrate-okta-federation-to-azure-active-directory/grant-consent.png)

1. Wählen Sie im linken Menü die Option **Branding** aus. Fügen Sie unter **URL der Startseite** die Homepage der Anwendung des Benutzers ein.

    ![Screenshot zum Hinzufügen von Branding](media/migrate-okta-federation-to-azure-active-directory/add-branding.png)

1. Wählen Sie im Okta-Verwaltungsportal die Optionen **Security** > **Identity Providers** (Sicherheit >Identitätsanbieter) aus, um einen neuen Identitätsanbieter hinzuzufügen. Wählen Sie **Add Microsoft** (Microsoft hinzufügen) aus.

    ![Screenshot zum Hinzufügen des Identitätsanbieters](media/migrate-okta-federation-to-azure-active-directory/configure-idp.png)

1. Kopieren Sie auf der Seite **Identity Provider** (Identitätsanbieter) Ihre Anwendungs-ID in das Feld **Client ID** (Client-ID). Kopieren Sie den geheimen Clientschlüssel in das Feld **Client Secret** (Geheimer Clientschlüssel).

1. Wählen Sie **Show Advanced Settings** (Erweiterte Einstellungen anzeigen) aus. Standardmäßig wird bei dieser Konfiguration der Benutzerprinzipalname (UPN) in Okta mit dem Benutzerprinzipalnamen in Azure AD für den umgekehrten Verbundzugriff verknüpft.

    >[!IMPORTANT]
    >Wenn Ihre UPNs in Okta und Azure AD nicht übereinstimmen, wählen Sie ein allgemeines Attribut für alle Benutzer*innen aus.

1. Nehmen Sie die Auswahl für die automatische Bereitstellung vor. Wenn Benutzer*innen nicht in Okta übereinstimmen, wird standardmäßig versucht, sie in Azure AD bereitzustellen. Wenn Sie die Bereitstellungen von Okta weg migriert haben, wählen Sie **Redirect to Okta sign-in page** (Zur Okta-Anmeldeseite umleiten) aus.

    ![Screenshot der Option zum Umleiten zur Okta-Anmeldeseite](media/migrate-okta-federation-to-azure-active-directory/redirect-okta.png)

    Nach dem Erstellen des Identitätsanbieters müssen Sie Benutzer*innen an den richtigen Identitätsanbieter weiterleiten.

1. Wählen Sie im Menü **Identity Providers** (Identitätsanbieter) die Optionen **Routing Rules** > **Add Routing Rule** (Routingregeln > Routingregel hinzufügen) aus. Verwenden Sie eines der im Okta-Profil verfügbaren Attribute.

1. Richten Sie die Richtlinie wie in der folgenden Abbildung gezeigt ein, um Anmeldungen von allen Geräten und IP-Adressen an Azure AD weiterzuleiten.

    In diesem Beispiel wird das Attribut **division** in keinem Okta-Profil verwendet, sodass es sich gut für das Routing an Identitätsanbieter eignet.

    ![Screenshot des Attributs „division“ für Routing an Identitätsanbieter](media/migrate-okta-federation-to-azure-active-directory/division-idp-routing.png)

1. Notieren Sie sich nach dem Hinzufügen der Routingregel den Umleitungs-URI, um ihn in der Anwendungsregistrierung hinzufügen zu können.

    ![Screenshot des Speicherorts des Umleitungs-URI](media/migrate-okta-federation-to-azure-active-directory/application-registration.png)

1. Wählen Sie in der Anwendungsregistrierung im linken Menü die Option **Authentifizierung** aus. Wählen Sie dann **Plattform hinzufügen** > **Web** aus.

    :::image type="content" source="media/migrate-okta-federation-to-azure-active-directory/add-platform.png" alt-text="Screenshot zum Hinzufügen einer Webplattform" lightbox="media/migrate-okta-federation-to-azure-active-directory/add-platform.png":::

1. Fügen Sie den Umleitungs-URI hinzu, den Sie für den Identitätsanbieter in Okta notiert haben. Wählen Sie dann **Zugriffstoken** und **ID-Token** aus.

    ![Screenshot zu Zugriffstoken und ID-Token in Okta](media/migrate-okta-federation-to-azure-active-directory/access-id-tokens.png)

1. Wählen Sie in der Verwaltungskonsole die Optionen **Verzeichnis** > **Personen** aus. Wählen Sie den ersten Testbenutzer zum Bearbeiten des Profils aus.

1. Fügen Sie im Profil wie in der folgenden Abbildung gezeigt **ToAzureAD** hinzu. Klicken Sie dann auf **Speichern**.

    ![Screenshot zum Bearbeiten eines Profils](media/migrate-okta-federation-to-azure-active-directory/profile-editing.png)

1. Versuchen Sie, sich als geänderter Benutzer beim [Microsoft 356-Portal](https://portal.office.com) anzumelden. Wenn Benutzer*innen nicht dem Pilotprojekt für die verwaltete Authentifizierung angehören, werden Sie feststellen, dass für die Aktion eine Schleife durchlaufen wird. Um die Schleife zu beenden, fügen Sie die Benutzer*innen der verwalteten Authentifizierung hinzu.

## <a name="test-okta-app-access-on-pilot-members"></a>Testen des Zugriffs der Okta-App für Pilotmitglieder*innen

Nach der Konfiguration der Okta-App in Azure AD und des Identitätsanbieters im Okta-Portal müssen Sie die Anwendung Benutzer*innen zuweisen.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Unternehmens-Apps** aus.

1. Wählen Sie die zuvor erstellte App-Registrierung aus, und wechseln Sie zu **Benutzer und Gruppen**. Fügen Sie die Gruppe hinzu, die dem Pilotprojekt für die verwaltete Authentifizierung entspricht.

   >[!NOTE]
   >Benutzer*innen und Gruppen können Sie nur auf der Seite **Unternehmensanwendungen** hinzufügen. Im Menü **App-Registrierungen** können keine Benutzer*innen hinzugefügt werden.

   ![Screenshot zum Hinzufügen einer Gruppe](media/migrate-okta-federation-to-azure-active-directory/add-group.png)

1. Melden Sie sich nach ca. 15 Minuten als Benutzer*in des Pilotprojekts für die verwaltete Authentifizierung an, und wechseln Sie zu [Meine Apps](https://myapplications.microsoft.com).

   ![Screenshot des Katalogs „Meine Apps“](media/migrate-okta-federation-to-azure-active-directory/my-applications.png)

1. Wählen Sie die Kachel **Okta Application Access** (Zugriff auf Okta-Anwendung) aus, um zur Okta-Homepage zurückzukehren.

## <a name="test-managed-authentication-on-pilot-members"></a>Testen der verwalteten Authentifizierung für Pilotmitglieder

Lassen Sie Ihre Benutzer*innen nach der Konfiguration der Okta-App für den umgekehrten Verbundzugriff die verwaltete Authentifizierung umfassend testen. Es wird empfohlen, ein Unternehmensbranding einzurichten, sodass die Benutzer*innen den Mandanten erkennen können, bei dem sie sich anmelden. Weitere Informationen finden Sie unter [Hinzufügen von Branding zur Azure Active Directory-Anmeldeseite Ihrer Organisation](../fundamentals/customize-branding.md).

>[!IMPORTANT]
>Identifizieren Sie alle zusätzlichen Richtlinien für bedingten Zugriff, die Sie möglicherweise benötigen, bevor Sie den Verbund der Domänen mit Okta vollständig entfernen. Informationen zum Sichern Ihrer Umgebung vor dem vollständigen Trennung finden Sie unter [Migrieren von Okta-Anmelderichtlinien zum bedingten Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md).

## <a name="defederate-office-365-domains"></a>Entfernen von Office 365-Domänen aus dem Verbund

Wenn Ihre Organisation mit der verwalteten Authentifizierung zufrieden ist, können Sie den Verbund der Domäne mit Okta entfernen. Verwenden Sie zunächst die folgenden Befehle, um eine Verbindung mit MSOnline PowerShell herzustellen. Wenn Sie noch nicht über das MSOnline-PowerShell-Modul verfügen, laden Sie es herunter, indem Sie `install-module MSOnline` eingeben.

```PowerShell

import-module MSOnline
Connect-Msolservice
Set-msoldomainauthentication 
-domainname yourdomain.com -authentication managed

```

Nach dem Festlegen der Domäne auf die verwaltete Authentifizierung haben Sie den Verbund Ihres Office 365-Mandanten mit Okta erfolgreich entfernt und gleichzeitig sichergestellt, dass der Benutzerzugriff auf die Okta-Homepage erhalten bleibt.

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren der Okta-Synchronisierungsbereitstellung zu Azure AD Connect-basierter Synchronisierung](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [Migrieren von Okta-Anmelderichtlinien zu bedingtem Azure AD-Zugriff](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
- [Migrieren von Anwendungen von Okta zu Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
