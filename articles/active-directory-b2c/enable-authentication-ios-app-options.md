---
title: Aktivieren von mobilen iOS Swift-Anwendungsoptionen mithilfe von Azure Active Directory B2C
description: In diesem Artikel werden verschiedene Möglichkeiten zum Aktivieren von Optionen für mobile iOS Swift-Anwendungen mithilfe von Azure Active Directory B2C erläutert.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 128c5000e6614e769100a92303007317eadaed96
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220699"
---
# <a name="enable-authentication-options-in-an-ios-swift-app-by-using-azure-ad-b2c"></a>Aktivieren der Authentifizierungsoptionen in einer iOS Swift-App mithilfe von Azure AD B2C 

In diesem Artikel werden verschiedene Möglichkeiten beschrieben, wie Sie die Azure Active Directory B2C-Authentifizierungsoberfläche (Azure AD B2C) für Ihre iOS Swift-Anwendung aktivieren, anpassen und verbessern können. 

Lesen Sie die folgenden Artikel, bevor Sie beginnen: 
* [Konfigurieren der Authentifizierung in einer iOS Swift-Beispielanwendung mithilfe von Azure Active Directory B2C](configure-authentication-sample-ios-app.md)
* [Aktivieren der Authentifizierung in einer eigenen iOS Swift-Anwendung mit Azure Active Directory B2C](enable-authentication-ios-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Führen Sie die folgenden Schritte aus, um eine benutzerdefinierte Domäne und Ihre Mandanten-ID in der Authentifizierungs-URL zu verwenden: 

1. Befolgen Sie die Anleitung unter [Aktivieren von benutzerdefinierten Domänen für Azure Active Directory B2C](custom-domain.md).
1. Aktualisieren Sie den Klassenmember `kAuthorityHostName` mit Ihrer benutzerdefinierten Domäne.
1. Aktualisieren Sie den Klassenmember `kTenantName` mit Ihrer [Mandanten-ID](tenant-management.md#get-your-tenant-id).

Der folgende Swift-Code zeigt die App-Einstellungen vor der Änderung:

```swift
let kTenantName = "contoso.onmicrosoft.com" 
let kAuthorityHostName = "contoso.b2clogin.com" 
```

Der folgende Swift-Code zeigt die App-Einstellungen nach der Änderung:

```swift
let kTenantName = "00000000-0000-0000-0000-000000000000" 
let kAuthorityHostName = "login.contoso.com" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 
1. Wechseln Sie zu Ihrem MSAL-Konfigurationsobjekt (Microsoft Authentication Library), und fügen Sie die `withLoginHint()`-Methode mit dem Anmeldehinweis hinzu.

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.loginHint = "bob@contoso.com"
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie der Liste den `domain_hint`-Parameter mit dem entsprechenden Domänennamen hinzu (z. B. `facebook.com`).
1. Übergeben Sie die zusätzliche Abfrageparameterliste an das `extraQueryParameters`-Attribut des MSAL-Konfigurationsobjekts.

```swift
let extraQueryParameters: [String: String] = ["domain_hint": "facebook.com"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Konfigurieren Sie die Sprachanpassung](language-customization.md).
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie der Liste den `ui_locales`-Parameter mit dem entsprechenden Sprachcode hinzu (z. B. `en-us`).
1. Übergeben Sie die zusätzliche Abfrageparameterliste an das `extraQueryParameters`-Attribut des MSAL-Konfigurationsobjekts.

```swift
let extraQueryParameters: [String: String] = ["ui_locales": "en-us"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)-Element.
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie den benutzerdefinierten Abfragezeichenfolgenparameter hinzu (z. B. `campaignId`). Legen Sie den Parameterwert fest (z. B. `germany-promotion`).
1. Übergeben Sie die zusätzliche Abfrageparameterliste an das `extraQueryParameters`-Attribut des MSAL-Konfigurationsobjekts.

```swift
let extraQueryParameters: [String: String] = ["campaignId": "germany-promotion"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Definieren Sie ein [technisches ID-Token-Hinweisprofil](id-token-hint.md) in Ihrer benutzerdefinierten Richtlinie.
1. Generieren Sie in Ihrem Code ein ID-Token, oder rufen Sie ein ID-Token ab. Legen Sie dieses dann auf eine Variable fest (z. B. `idToken`). 
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie den `id_token_hint`-Parameter mit der entsprechenden Variablen hinzu, in der das ID-Token gespeichert wird.
1. Übergeben Sie die zusätzliche Abfrageparameterliste an das `extraQueryParameters`-Attribut des MSAL-Konfigurationsobjekts.

```swift
let extraQueryParameters: [String: String] = ["id_token_hint": idToken]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


Die MSAL-Protokollierung sollte so früh wie möglich in der App-Startsequenz festgelegt werden, bevor MSAL-Anforderungen gesendet werden. Konfigurieren Sie die [MSAL-Protokollierung](../active-directory/develop/msal-logging-ios.md) in der `application`-Methode *AppDelegate.swift*.

Der folgende Codeausschnitt veranschaulicht das Konfigurieren der MSAL-Protokollierung:

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        MSALGlobalConfig.loggerConfig.logLevel = .verbose
        MSALGlobalConfig.loggerConfig.setLogCallback { (logLevel, message, containsPII) in
            
            // If PiiLoggingEnabled is set YES, this block will potentially contain sensitive information (Personally Identifiable Information), but not all messages will contain it.
            // containsPII == YES indicates if a particular message contains PII.
            // You might want to capture PII only in debug builds, or only if you take necessary actions to handle PII properly according to legal requirements of the region
            if let displayableMessage = message {
                if (!containsPII) {
                    #if DEBUG
                    // NB! This sample uses print just for testing purposes
                    // You should only ever log to NSLog in debug mode to prevent leaking potentially sensitive information
                    print(displayableMessage)
                    #endif
                }
            }
        }
        return true
    }
```

## <a name="embedded-web-view-experience"></a>Benutzeroberfläche für eingebettete Webansicht

Webbrowser sind eine Voraussetzung für die interaktive Authentifizierung. Die MSAL-Bibliothek verwendet standardmäßig die Systemwebansicht. Während der Anmeldung öffnet die MSAL-Bibliothek die iOS-Systemwebansicht mit der Azure AD B2C-Benutzeroberfläche.  

Weitere Informationen finden Sie im Artikel [Anpassen von Browsern und Webansichten für iOS/macOS](../active-directory/develop/customize-webviews.md).

Je nach Ihren Anforderungen können Sie die eingebettete Webansicht verwenden. Zwischen der eingebetteten Webansicht und der Systemwebansicht in MSAL gibt es visuelle Unterschiede und Unterschiede bei SSO-Vorgängen.

![Screenshot: Unterschied zwischen der Benutzeroberfläche der Systemwebansicht und der eingebetteten Webansicht](./media/enable-authentication-ios-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> Es wird empfohlen, die Plattformstandardeinstellung zu verwenden, die normalerweise der Systembrowser ist. Der Systembrowser kann die Benutzer besser speichern, die sich zuvor angemeldet haben. Einige Identitätsanbieter*innen (z. B. Google) unterstützen keine eingebettete Ansicht.

Um dieses Verhalten zu ändern, ändern Sie das `webviewType`-Attribut von `MSALWebviewParameters` in `wkWebView`. Im folgenden Beispiel wird veranschaulicht, wie der Webansichtstyp in eine eingebettete Ansicht geändert wird: 

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    
    // Use embedded view experience
    self.webViewParameters?.webviewType = .wkWebView
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [MSAL für iOS Swift-Konfigurationsoptionen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki).
