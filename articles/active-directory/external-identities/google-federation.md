---
title: Hinzufügen von Google als Identitätsanbieter für B2B – Azure AD
description: Erstellen eines Verbunds mit Google, um Gastbenutzern die Anmeldung bei Ihren Azure AD-Apps mit ihren eigenen Gmail-Konten zu ermöglichen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1be96c86d99a5d2fdb01fcf870a2216ab5167d5e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428891"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer

Durch die Einrichtung eines Verbunds mit Google können Sie eingeladenen Benutzern ermöglichen, sich bei Ihren freigegebenen Apps und Ressourcen mit ihren eigenen Gmail-Konten anzumelden, ohne dass sie Microsoft-Konten erstellen müssen. Nachdem Sie Google als Anmeldeoption für Ihre Anwendung hinzugefügt haben, können Benutzer auf der **Anmeldeseite** einfach die Gmail-Adresse eingeben, mit der sie sich bei Google anmelden.

![Anmeldeoptionen für Google-Benutzer](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Der Google-Verbund wurde speziell für Gmail-Benutzer konzipiert. Verwenden Sie für einen Verbund mit Google Workspace-Domänen den [SAML-/WS-Fed-Identitätsanbieterverbund](direct-federation.md).

> [!IMPORTANT]
>
> - Wenn Azure AD B2B-Kunden **ab dem 12. Juli 2021** neue Google-Integrationen zur Verwendung mit Self-Service-Registrierung oder zum Einladen externer Benutzer für ihre benutzerdefinierten oder branchenspezifischen Anwendungen einrichten, kann die Authentifizierung für Gmail-Benutzer blockiert werden (im folgenden Fehlerbildschirm unter [Was ist zu erwarten?](#what-to-expect)). Dieses Problem tritt nur auf, wenn Sie Benutzerflows für die Google-Integration zur Self-Service-Registrierung oder Einladungen nach dem 12. Juli 2021 erstellen und Gmail-Authentifizierungen in Ihren benutzerdefinierten oder branchenspezifischen Anwendungen nicht in Systemwebansichten verschoben wurden. Da Systemwebansichten standardmäßig aktiviert sind, sind die meisten Apps nicht betroffen. Um das Problem zu vermeiden, sollten Sie unbedingt Gmail-Authentifizierungen in Systembrowser verschieben, bevor Sie neue Google-Integrationen zur Self-Service-Registrierung erstellen. Weitere Informationen finden Sie unter [Für eingebettete Webansichten erforderliche Aktion](#action-needed-for-embedded-frameworks).
> - Ab dem **30. September 2021** wird Google die [Unterstützung für die Anmeldung in der Webansicht einstellen](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Wenn Ihre Apps Benutzer mit einer eingebetteten Webansicht authentifizieren und Sie den Google-Verbund mit [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) oder Azure AD B2B für externe Benutzereinladungen oder die [Self-Service-Registrierung](identity-providers.md) verwenden, können sich Google Gmail-Benutzer nicht authentifizieren. [Weitere Informationen](#deprecation-of-web-view-sign-in-support)

## <a name="what-is-the-experience-for-the-google-user"></a>Wie läuft der Vorgang für Google-Benutzer ab?

Wenn ein Google-Benutzer Ihre Einladung einlöst, hängt das Benutzererlebnis davon ab, ob er schon bei Google angemeldet ist:

- Gastbenutzer, die nicht bei Google angemeldet sind, werden aufgefordert, sich anzumelden.
- Gastbenutzer, die bereits bei Google angemeldet sind, werden aufgefordert, das zu verwendende Konto auszuwählen. Er muss das Konto auswählen, das Sie für die Einladung verwendet haben.

Gastbenutzer, bei denen ein Fehler aufgrund eines zu langen Headers angezeigt wird, können Cookies löschen oder ein privates oder Inkognito-Fenster öffnen und sich erneut anmelden.

![Screenshot der Google-Anmeldeseite](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Endpunkte für die Anmeldung

Google-Gastbenutzer können sich nun mithilfe eines [gemeinsamen Endpunkts](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (d. h. mit einer allgemeinen App-URL, die Ihren Mandantenkontext nicht enthält) bei Ihren mehrmandantenfähigen Anwendungen oder bei Microsoft-Erstanbieter-Apps anmelden. Beim Anmeldevorgang wählt der Gastbenutzer zuerst **Anmeldeoptionen** und dann **Bei einer Organisation anmelden** aus. Der Benutzer gibt dann den Namen Ihres Unternehmens ein und setzt den Vorgang mit seinen Google-Anmeldeinformationen fort.

Google-Gastbenutzer können auch Anwendungsendpunkte verwenden, die Ihre Mandanteninformationen enthalten, z. B.:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Sie können Google-Gastbenutzern auch einen direkten Link zu einer Anwendung oder Ressource zur Verfügung stellen und Ihre Mandanteninformationen einfügen, z. B. `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`.

## <a name="deprecation-of-web-view-sign-in-support"></a>Einstellung der Unterstützung für die WebView-Anmeldung

Ab dem 30. September 2021 wird Google die [Unterstützung für die Anmeldung in der eingebetteten Webansicht einstellen](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Wenn Ihre Apps Benutzer mit einer eingebetteten Webansicht authentifizieren und Sie den Google-Verbund mit [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) oder Azure AD B2B für [externe Benutzereinladungen](google-federation.md) oder die [Self-Service-Registrierung](identity-providers.md) verwenden, können sich Google Gmail-Benutzer nicht authentifizieren.

Im Folgenden finden Sie bekannte Szenarien mit Auswirkungen auf Gmail-Benutzer:
- Microsoft-Apps (z. B. Teams und PowerApps) auf Windows 
- Windows-Apps, die das [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview)-Steuerelement [WebView2](/microsoft-edge/webview2/) oder das ältere WebBrowser-Steuerelement für die Authentifizierung verwenden. Diese Apps sollten mithilfe des WAM-Flows (Web Account Manager) migriert werden.
- Android-Anwendungen, die das WebView-Benutzeroberflächenelement verwenden 
- iOS-Anwendungen, die UIWebView/WKWebview verwenden 
- [Apps, die ADAL verwenden](../develop/howto-get-list-of-all-active-directory-auth-library-apps.md)

Diese Änderung wirkt sich nicht auf Folgendes aus:
- Web-Apps
- Mobile Apps, die Systemwebansichten für die Authentifizierung verwenden ([SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) unter iOS, [Benutzerdefinierte Registerkarten](https://developer.chrome.com/docs/android/custom-tabs/overview/) unter Android).  
- Google Workspace-Identitäten, z. B. bei Verwendung eines [SAML-basierten Verbunds](direct-federation.md) mit Google Workspace

Wir ermitteln derzeit in Rücksprache mit Google, ob diese Änderung Folgendes betrifft:
- Windows-Apps, die WAM (Web Account Manager) oder WAB (Web Authentication Broker) verwenden.  

Wir testen weiterhin verschiedene Plattformen und Szenarien und werden diesen Artikel auf dem neuesten Stand halten.

### <a name="action-needed-for-embedded-web-views"></a>Für eingebettete Webansichten erforderliche Aktion

Ändern Sie Ihre Apps so, dass diese den Systembrowser für die Anmeldung verwenden. Weitere Informationen finden Sie in der MSAL.NET-Dokumentation unter [Eingebettete Webbenutzeroberfläche im Vergleich zur System-Webbenutzeroberfläche](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Alle MSAL-SDKs verwenden standardmäßig die Systemwebansicht.

### <a name="what-to-expect"></a>Ausblick

Bevor Google diese Änderungen am 30. September 2021 einführt, stellt Microsoft eine Problemumgehung für Apps zur Verfügung, die noch eingebettete Webansichten verwenden, um sicherzustellen, dass die Authentifizierung nicht blockiert wird. Benutzer, die sich mit einem Gmail-Konto in einer eingebetteten Webansicht anmelden, werden aufgefordert, einen Code in einem separaten Browser einzugeben, um die Anmeldung abzuschließen.

Alternativ können Sie ihre vorhandenen und neuen Gmail-Benutzer mit Einmalkennung anmelden. So lassen Sie Ihre Gmail-Benutzer eine Einmalkennung verwenden:
1. [Aktivieren der Einmalkennung per E-Mail](one-time-passcode.md#enable-email-one-time-passcode)
2. [Entfernen eines Verbunds mit Google](google-federation.md#how-do-i-remove-google-federation)
3. [Setzen Sie den Einlösungsstatus Ihrer Gmail-Benutzer zurück](reset-redemption-status.md), damit sie in Zukunft eine Einmalkennung verwenden können.

Anwendungen, die zu einer zulässigen Webansicht für die Authentifizierung migriert werden, sind nicht betroffen, und Benutzer können sich wie gewohnt über Google authentifizieren.

Wenn Anwendungen nicht zu einer zulässigen Webansicht für die Authentifizierung migriert werden, wird den betroffenen Gmail-Benutzern der folgende Bildschirm angezeigt.

![Google-Anmeldefehler, wenn Apps nicht zu Systembrowsern migriert werden](media/google-federation/google-sign-in-error-ewv.png)

Wir werden dieses Dokument aktualisieren, sobald Google uns Datumsangaben und weitere Details mitteilt.

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>Unterscheiden zwischen CEF/Electron und eingebetteten Webansichten

Neben der [ Unterstützung für eingebettete Webansichten und Frameworkanmeldungen](#deprecation-of-web-view-sign-in-support) stellt Google auch die [CEF-basierte Gmail-Authentifizierung (Chromium Embedded Framework)](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) ein. Für CEF-basierte Anwendungen wie z. B. Electron-Apps wird die Authentifizierung von Google am 30. Juni 2021 deaktiviert. Betroffene Anwendungen wurden direkt von Google benachrichtigt und werden in dieser Dokumentation nicht behandelt.  Dieses Dokument bezieht sich auf die oben beschriebenen eingebetteten Webansichten, die von Google am 30. September 2021 eingeschränkt werden.

### <a name="action-needed-for-embedded-frameworks"></a>Für eingebettete Frameworks erforderliche Aktion

Ermitteln Sie anhand des [Google-Leitfadens](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html), ob Ihre Apps betroffen sind.

## <a name="step-1-configure-a-google-developer-project"></a>Schritt 1: Konfigurieren eines Google-Entwicklerprojekts

Erstellen Sie zunächst ein neues Projekt in der Google Developers Console, um eine Client-ID und einen geheimen Clientschlüssel abzurufen. Beide fügen Sie später in Azure Active Directory (Azure AD) hinzu. 
1. Wechseln Sie zur Google-APIs unter https://console.developers.google.com, und melden Sie sich mit Ihrem Google-Konto an. Es wird empfohlen, ein freigegebenes Google-Teamkonto zu verwenden.
2. Stimmen Sie den Vertragsbedingungen zu, wenn Sie dazu aufgefordert werden.
3. Erstellen Sie ein neues Projekt: Wählen Sie in der oberen linken Ecke der Seite die Projektliste aus, und wählen Sie dann auf der Seite **Projekt auswählen** die Option **Neues Projekt** aus.
4. Geben Sie auf der Seite **Neues Projekt** dem Projekt einen Namen (z. B. **Azure AD B2B**), und wählen Sie dann **Erstellen** aus: 
   
   ![Screenshot der Seite „Neues Projekt“](media/google-federation/google-new-project.png)

4. Wählen Sie auf der Seite **APIs & Dienste** unter dem neuen Projekt die Option **Anzeigen** aus.

5. Wählen Sie auf der Karte „APIs“die Option **Go to APIs overview** (Zur API-Übersicht wechseln) aus. Wählen Sie **OAuth-Zustimmungsbildschirm** aus.

6. Wählen Sie **Extern** und anschließend **Erstellen** aus. 

7. Geben Sie im **OAuth-Einwilligungsbildschirm** einen **Anwendungsnamen** ein:

   ![Screenshot des Google-OAuth-Einwilligungsbildschirms](media/google-federation/google-oauth-consent-screen.png)

8. Scrollen Sie zum Abschnitt **Authorized domains** (Autorisierte Domänen), und geben Sie **microsoftonline.com** ein:

   ![Screenshot des Abschnitts „Authorized domains“ (Autorisierte Domänen)](media/google-federation/google-oauth-authorized-domains.PNG)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie **Credentials** aus. Wählen Sie im Menü **Anmeldedaten erstellen** die Option **OAuth-Client-ID** aus:

    ![Screenshot des Google APIs-Menüs „Anmeldedaten erstellen“](media/google-federation/google-api-credentials.png)

11. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus. Geben Sie der Anwendung einen geeigneten Namen, z. B. **Azure AD B2B**. Geben Sie unter **Autorisierte Weiterleitungs-URIs** die folgenden URIs ein:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(`<tenant ID>` ist Ihre Mandanten-ID.)
    - `https://login.microsoftonline.com/te/<tenant name>.onmicrosoft.com/oauth2/authresp` <br>(`<tenant name>` ist Ihr Mandantenname.)
   
    > [!NOTE]
    > Ihre Mandanten-ID finden Sie im [Azure-Portal](https://portal.azure.com). Wählen Sie dort unter **Azure Active Directory** die Option **Eigenschaften** aus, und kopieren Sie die **Mandanten-ID**.

    ![Screenshot des Abschnitts „Autorisierte Umleitungs-URIs“](media/google-federation/google-create-oauth-client-id.png)

12. Klicken Sie auf **Erstellen**. Kopieren Sie die Client-ID und den geheimen Clientschlüssel. Sie verwenden diese Informationen, wenn Sie den Identitätsanbieter im Azure-Portal hinzufügen.

    ![Screenshot von OAuth-Client-ID und geheimem Clientschlüssel](media/google-federation/google-auth-client-id-secret.png)

13. Sie können Ihr Projekt im Veröffentlichungsstatus **Testen** belassen und Testbenutzer dem OAuth-Zustimmungsbildschirm hinzufügen. Alternativ können Sie auf dem OAuth-Zustimmungsbildschirm die Schaltfläche **App veröffentlichen** auswählen, um die App für jeden Benutzer mit einem Google-Konto verfügbar zu machen.

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Schritt 2: Konfigurieren des Google-Verbunds in Azure AD 

Sie legen nun die Google-Client-ID und den geheimen Clientschlüssel fest. Dazu können Sie das Azure-Portal oder PowerShell verwenden. Testen Sie unbedingt die Konfiguration des Google-Verbunds, indem Sie sich selbst einladen. Verwenden Sie eine Gmail-Adresse, und versuchen Sie, die Einladung mit Ihrem eingeladenen Google-Konto einzulösen. 

**So konfigurieren Sie den Google-Verbund im Azure-Portal** 
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** und dann die Schaltfläche **Google** aus.
4. Geben Sie die Client-ID und den geheimen Clientschlüssel ein, die Sie zuvor erhalten haben. Wählen Sie **Speichern** aus: 

   ![Screenshot der Seite „Google als Identitätsanbieter hinzufügen“](media/google-federation/google-identity-provider.png)

**So konfigurieren Sie den Google-Verbund mithilfe von PowerShell**
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie den folgenden Befehl aus: `Connect-AzureAD`
3. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.  
4. Führen Sie den folgenden Befehl aus: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Verwenden Sie die Client-ID und den geheimen Clientschlüssel der App, die Sie hier erstellt haben: „Schritt 1: Konfigurieren eines Google-Entwicklerprojekts“. Weitere Informationen finden Sie unter [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="how-do-i-remove-google-federation"></a>Wie entferne ich einen Google Verbund?

Sie können Ihre Google-Verbundeinrichtung löschen. Wenn Sie dies tun, können sich Google-Gastbenutzer, die ihre Einladungen bereits eingelöst haben, nicht mehr anmelden. Sie können ihnen jedoch den Zugriff auf ihre Ressourcen zurückgeben, indem Sie [ihren Einlösungsstatus zurücksetzen](reset-redemption-status.md).
 
**So löschen Sie den Google-Verbund im Azure AD-Portal**
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** aus.
4. Wählen Sie in der Zeile **Google** die Schaltfläche mit den Auslassungspunkten ( **...** ) und dann **Löschen** aus. 
   
   ![Screenshot der Schaltfläche „Löschen“ für den Identitätsanbieter für soziale Netzwerke](media/google-federation/google-social-identity-providers.png)

1. Wählen Sie **Ja** aus, um den Löschvorgang zu bestätigen. 

**So löschen Sie den Google-Verbund mithilfe von PowerShell** 
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie `Connect-AzureAD` aus.  
4. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.  
5. Geben Sie den folgenden Befehl ein:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Weitere Informationen finden Sie unter [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true).
