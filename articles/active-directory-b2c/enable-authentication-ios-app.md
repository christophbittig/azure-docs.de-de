---
title: Aktivieren der Authentifizierung in einer iOS Swift-App mithilfe von Azure AD B2C
description: In diesem Artikel erfahren Sie, wie Sie die Authentifizierung in einer iOS Swift-Anwendung mit den Bausteinen von Azure Active Directory B2C aktivieren. Erfahren Sie, wie Sie Azure AD B2C für die Anmeldung und Registrierung von Benutzern in einer iOS Swift-Anwendung verwenden.
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
ms.openlocfilehash: bb590ee57cc78c27c0e6cec7dc54cc15a9fd7648
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186099"
---
# <a name="enable-authentication-in-your-own-ios-swift-app-by-using-azure-ad-b2c"></a>Aktivieren der Authentifizierung in Ihrer eigenen iOS Swift-App mithilfe von Azure AD B2C

In diesem Artikel wird beschrieben, wie Sie Ihrer eigenen mobilen iOS Swift-Anwendung die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen. Erfahren Sie, wie Sie eine iOS Swift-Anwendung in die [Microsoft-Authentifizierungsbibliothek (MSAL) für iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) integrieren. 

Verwenden Sie diesen Artikel mit dem Artikel [Konfigurieren der Authentifizierung in einer iOS Swift-Beispielwebanwendung](./configure-authentication-sample-ios-app.md), und ersetzen Sie dabei die iOS Swift-Beispiel-App durch Ihre eigene iOS Swift-App. Nachdem Sie die Anleitung in diesem Artikel ausgeführt haben, akzeptiert Ihre Anwendung Anmeldungen über Azure AD B2C.

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die Voraussetzungen und Integrationsschritte unter [Konfigurieren der Authentifizierung in einer iOS Swift-Beispielanwendung mithilfe von Azure AD B2C](configure-authentication-sample-ios-app.md).

## <a name="create-an-ios-swift-app-project"></a>Erstellen eines iOS Swift-App-Projekts

Falls Sie noch nicht über eine iOS Swift-Anwendung verfügen, richten Sie wie folgt ein neues Projekt ein.

1. Öffnen Sie [Xcode](https://developer.apple.com/xcode/), und wählen Sie **Datei** > **Neu** > **Projekt** aus.
1. Für iOS-Apps: Wählen Sie **iOS** > **App** und dann **Weiter** aus.
1. Geben Sie für **Optionen für Ihr neues Projekt auswählen** Folgendes an:
    1. **Product name** (Produktname) z. B. `MSALiOS`.
    1. **Organization identifier** (Organisations-ID), z. B. `contoso.com`.
    1. Wählen Sie unter **Interface** (Schnittstelle) **Storyboard** aus.
    1. Wählen Sie unter **Life cycle** (Lebenszyklus) **UIKit App Delegate** (UIKit-App-Delegat) aus.
    1. Wählen Sie unter **Language** (Sprache) **Swift** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie einen Ordner für das Erstellen Ihrer App und dann die Option **Erstellen** aus.


## <a name="step-1-install-the-msal-library"></a>Schritt 1: Installieren der MSAL-Bibliothek

1. Installieren Sie die MSAL-Bibliothek mithilfe von [CocoaPods](https://cocoapods.org/).   Erstellen Sie im Ordner mit der Datei *.xcodeproj* Ihres Projekts, wenn die Datei *podfile* nicht vorhanden ist, eine leere Datei mit dem Namen *podfile*. Fügen Sie der *podfile*-Datei den folgenden Code hinzu:

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. Ersetzen Sie `<your-target-here>` durch den Namen Ihres Projekts (z. B. `MSALiOS`). Weitere Informationen finden Sie in der [Referenz zur Podfile-Syntax](https://guides.cocoapods.org/syntax/podfile.html#podfile).
1. Navigieren Sie in einem Terminalfenster zu dem Ordner, der die *podfile*-Datei enthält, und führen Sie dann *pod install* zum Installieren der MSAL-Bibliothek aus.
1. Nachdem Sie den Befehl `pod install` ausgeführt haben, wird eine *\<your project name>.xcworkspace*-Datei erstellt. Schließen Sie Xcode, und öffnen Sie die Datei *\<your project name>.xcworkspace*, um das Projekt in Xcode neu zu laden.

## <a name="step-2-set-the-app-url-scheme"></a>Schritt 2: Festlegen des URL-Schemas der App

Wenn sich ein Benutzer authentifiziert, sendet Azure AD B2C einen Autorisierungscode an die App, und zwar über den in der Azure AD B2C-Anwendungsregistrierung konfigurierten Umleitungs-URI. 

Das MSAL-Standardformat für den Umleitungs-URI ist `msauth.[Your_Bundle_Id]://auth`. Beispielsweise `msauth.com.microsoft.identitysample.MSALiOS://auth`, wobei `msauth.com.microsoft.identitysample.MSALiOS` das URL-Schema ist

Registrieren Sie in diesem Schritt Ihr URL-Schema mithilfe des `CFBundleURLSchemes`-Arrays. Ihre Anwendung lauscht beim URL-Schema auf den Rückruf von Azure AD B2C. 

Öffnen Sie in Xcode die Datei [*Info.plist*](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) als Quellcodedatei. Fügen Sie in Abschnitt `<dict>` den folgenden XML-Codeausschnitt hinzu: 

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.com.microsoft.identitysample.MSALiOS</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="step-3-add-the-authentication-code"></a>Schritt 3: Hinzufügen des Authentifizierungscodes

Der [Beispielcode](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) besteht aus einer `UIViewController`-Klasse. Die Klasse hat folgende Merkmale:

- Definiert die Struktur einer Benutzeroberfläche.
- Enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die App stellt mithilfe dieser Informationen eine Vertrauensstellung mit Azure AD B2C her. 
- Enthält den Authentifizierungscode, um Benutzer zu authentifizieren, Token zu beziehen und zu überprüfen

Wählen Sie einen `UIViewController` aus, bei dem sich die Benutzer authentifizieren.  Führen Sie in Ihrem `UIViewController` den Code mit dem [Code zusammen, der in GitHub bereitgestellt wird](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift). 

## <a name="step-4-configure-your-ios-swift-app"></a>Schritt 4: Konfigurieren Ihrer iOS Swift-App

Nachdem Sie [den Authentifizierungscode hinzugefügt](#step-3-add-the-authentication-code) haben, konfigurieren Sie Ihre iOS Swift-App mit Ihren Azure AD B2C-Einstellungen. Einstellungen des Azure AD B2C-Identitätsanbieters werden in der im vorherigen Abschnitt gewählten `UIViewController`-Klasse konfiguriert. 

Informationen zum Konfigurieren Ihrer iOS Swift-App finden Sie unter [Konfigurieren der Authentifizierung in einer iOS Swift-Beispiel-App mit Azure AD B2C](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

## <a name="step-5-run-and-test-the-mobile-app"></a>Schritt 5: Ausführen und Testen der mobilen App

1. Erstellen Sie das Projekt, und führen Sie es mit einem [Simulator eines verbundenen iOS-Geräts](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device) aus.
1. Wählen Sie **Registrieren**, und melden Sie sich mit Ihrem lokalen oder Social Media-Konto für Azure AD B2C an.
1. Nachdem Sie sich erfolgreich authentifiziert haben, wird Ihr Anzeigename auf der Navigationsleiste angezeigt.

## <a name="step-6-customize-your-code-building-blocks"></a>Schritt 6: Anpassen Ihrer Codebausteine

In diesem Abschnitt werden die Codebausteine beschrieben, die Ihrer iOS Swift-App die Authentifizierung ermöglichen. Hier finden Sie eine Liste der UiViewController-Methoden und erfahren, wie Sie Ihren Code anpassen. 

### <a name="step-61-instantiate-a-public-client-application"></a>Schritt 6.1: Instanziieren einer öffentlichen Clientanwendung

Öffentlichen Clientanwendungen sind nicht vertrauenswürdig genug, um Anwendungsgeheimnisse sicher aufzubewahren, deshalb enthalten sie keine geheimen Clientschlüssel. Instanziieren Sie eine MSAL in [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload) mithilfe eines öffentlichen Clientanwendungsobjekts.

Der folgende Codeausschnitt veranschaulicht, wie die MSAL-Bibliothek mit einem `MSALPublicClientApplicationConfig`-Konfigurationsobjekt initialisiert wird. 

Das Konfigurationsobjekt stellt Informationen zu Ihrer Azure AD B2C-Umgebung zur Verfügung. Sie stellt beispielsweise die Client-ID, den Umleitungs-URI und die Autorität zum Erstellen von Authentifizierungsanforderungen für Azure AD B2C bereit. Informationen zum Konfigurationsobjekt finden Sie unter [Konfigurieren der mobilen Beispiel-App](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

```swift
do {

    let signinPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: signinPolicyAuthority)
    pcaConfig.knownAuthorities = [signinPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

Mit der `initWebViewParams`-Methode wird die Umgebung zur [interaktiver Authentifizierung](../active-directory/develop/customize-webviews.md) konfiguriert. 

Der folgende Swift-Codeausschnitt initialisiert den Klassenmember `webViewParameters` mit der Systemwebansicht. Weitere Informationen finden Sie im Artikel [Anpassen von Browsern und Webansichten für iOS/macOS](../active-directory/develop/customize-webviews.md).

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParameters?.webviewType = .default
}
```

### <a name="step-62-start-an-interactive-authorization-request"></a>Schritt 6.2: Starten einer interaktiven Autorisierungsanforderung

Eine interaktive Autorisierungsanforderung ist ein Flow, bei dem Benutzer mithilfe der Systemwebansicht zur Registrierung oder Anmeldung aufgefordert werden. Wenn Benutzer auf die Schaltfläche **Anmelden** klicken, wird die `authorizationButton`-Methode aufgerufen.

Die `authorizationButton`-Methode bereitet das Objekt `MSALInteractiveTokenParameters` mit relevanten Daten zur Autorisierungsanforderung vor. Die `acquireToken`-Methode verwendet `MSALInteractiveTokenParameters`, um den Benutzer mithilfe der Systemwebansicht zu authentifizieren.

Der folgende Codeausschnitt veranschaulicht, wie die interaktive Autorisierungsanforderung gestartet wird. 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error information" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
Sobald die Benutzer den Autorisierungsflow abgeschlossen haben (erfolgreich oder nicht), wird das Ergebnis an den [Abschluss](https://docs.swift.org/swift-book/LanguageGuide/Closures.html) der `acquireToken`-Methode zurückgegeben. 

Die `acquireToken`-Methode gibt die Objekte `result` und `error` zurück. Verwenden Sie diesen Abschluss für Folgendes:

- Aktualisieren der Benutzeroberfläche der mobilen App mit Informationen nach Abschluss der Authentifizierung
- Aufrufen eines Web-API-Diensts mit einem Zugriffstoken
- Behandeln von Authentifizierungsfehlern, z. B. wenn ein Benutzer den Anmeldeflow abbricht
 
### <a name="step-63-call-a-web-api"></a>Schritt 6.3: Aufrufen einer Web-API

Zum Aufrufen einer [tokenbasierten Autorisierungs-Web-API](enable-authentication-web-api.md) benötigt die App ein gültiges Zugriffstoken. Die App führt folgende Schritte aus:

1. Beziehen eines Zugriffstokens mit den erforderlichen Berechtigungen (Bereichen) für den Web-API-Endpunkt
1. Übergeben des Zugriffstokens als Bearertoken im Autorisierungsheader der HTTP-Anforderung in folgendem Format:

```http
Authorization: Bearer <access-token>
```

Wenn sich Benutzer [interaktiv authentifizieren](#step-62-start-an-interactive-authorization-request), erhält die App im Abschluss `acquireToken` ein Zugriffstoken. Verwenden Sie für nachfolgende Web-API-Aufrufe die automatische Methode zum Abrufen des Tokens (`acquireTokenSilent`), wie in diesem Abschnitt beschrieben. 

Die `acquireTokenSilent`-Methode führt folgende Schritte aus:

1. Sie versucht, ein Zugriffstoken mit den angeforderten Bereichen aus dem Tokencache abzurufen. Wenn das Token vorhanden und nicht abgelaufen ist, wird es zurückgegeben. 
1. Wenn das Token nicht im Tokencache vorhanden oder abgelaufen ist, versucht die MSAL-Bibliothek, mithilfe des Aktualisierungstokens ein neues Zugriffstoken zu beziehen. 
1. Wenn das Aktualisierungstoken nicht vorhanden oder abgelaufen ist, wird eine Ausnahme zurückgegeben. In diesem Fall müssen Sie den Benutzer auffordern, [sich interaktiv anzumelden](#step-62-start-an-interactive-authorization-request).  

Der folgende Codeausschnitt veranschaulicht das Beziehen eines Zugriffstokens:

```swift
do {

// Get the authority using the sign-in or sign-up user flow
let authority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)

// Get the current account from the application context
guard let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy) else {
    self.updateLoggingText(text: "There is no account available!")
    return
}

// Configure the acquire token silent parameters
let parameters = MSALSilentTokenParameters(scopes: kScopes, account:thisAccount)
parameters.authority = authority
parameters.loginHint = "username"

// Acquire token silent
self.applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        
        let nsError = error as NSError
        
        // interactionRequired means we need to ask the user to sign in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        
        if (nsError.domain == MSALErrorDomain) {
            
            if (nsError.code == MSALError.interactionRequired.rawValue) {
                
                // Start an interactive authorization code
                // Notice we supply the account here. This ensures we acquire token for the same account
                // as we originally authenticated.
                
                ...
            }
        }
        
        self.updateLoggingText(text: "Could not acquire token: \(error)")
        return
    }
    
    guard let result = result else {
        
        self.updateLoggingText(text: "Could not acquire token: No result returned")
        return
    }
    
    // On success, set the access token to the accessToken class member. 
    // The callGraphAPI method uses the access token to call a web API  
    self.accessToken = result.accessToken
    ...
}
} catch {
self.updateLoggingText(text: "Unable to construct parameters before calling acquire token \(error)")
}
```

Die `callGraphAPI`-Methode ruft das Zugriffstoken ab und die Web-API auf, wie Sie hier sehen können:

```swift
@objc func callGraphAPI(_ sender: UIButton) {
    guard let accessToken = self.accessToken else {
        self.updateLoggingText(text: "Operation failed because could not find an access token!")
        return
    }
    
    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 30
    let url = URL(string: self.kGraphURI)
    var request = URLRequest(url: url!)
    request.setValue("Bearer \(accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    self.updateLoggingText(text: "Calling the API....")
    
    urlSession.dataTask(with: request) { data, response, error in
        guard let validData = data else {
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error information" as! Error)")
            return
        }
        
        let result = try? JSONSerialization.jsonObject(with: validData, options: [])
        
        guard let validResult = result as? [String: Any] else {
            self.updateLoggingText(text: "Nothing returned from API")
            return
        }
        
        self.updateLoggingText(text: "API response: \(validResult.debugDescription)")
        }.resume()
}
```

### <a name="step-64-sign-out-users"></a>Schritt 6.4: Abmelden von Benutzern

Bei der Abmeldung mit MSAL werden alle bekannten Informationen über einen Benutzer aus der Anwendung entfernt. Verwenden Sie die Abmeldemethode, um Benutzer abzumelden und die Benutzeroberfläche zu aktualisieren. Sie können beispielsweise geschützte Benutzeroberflächenelemente und die Abmeldeschaltfläche ausblenden, oder die Anmeldeschaltfläche anzeigen.

Im folgenden Codeausschnitt wird das Abmelden von Benutzern veranschaulicht:

```swift
@objc func signoutButton(_ sender: UIButton) {
do {
    
    
    let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy)
    
    if let accountToRemove = thisAccount {
        try applicationContext.remove(accountToRemove)
    } else {
        self.updateLoggingText(text: "There is no account to signing out!")
    }
    
    ...
    
} catch  {
    self.updateLoggingText(text: "Received error signing out: \(error)")
}
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:
* [Aktivieren der Authentifizierung in einer iOS Swift-App mithilfe von Azure AD B2C](enable-authentication-ios-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer eigenen Web-API mit Azure AD B2C](enable-authentication-web-api.md)
