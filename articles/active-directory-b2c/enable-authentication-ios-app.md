---
title: 'Aktivieren der Authentifizierung in einer iOS Swift-App: Azure AD B2C'
description: Aktivieren Sie Authentifizierung in einer iOS Swift-Anwendung mit den Bausteinen von Azure Active Directory B2C. Erfahren Sie, wie Sie Azure AD B2C für die Anmeldung und Registrierung von Benutzern in einer iOS Swift-Anwendung verwenden.
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
ms.openlocfilehash: d9efe9b3d7810eccb94906c236b34534861db092
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338906"
---
# <a name="enable-authentication-in-your-own-ios-swift-application-using-azure-active-directory-b2c"></a>Aktivieren der Authentifizierung in einer eigenen iOS Swift-Anwendung mit Azure Active Directory B2C

In diesem Artikel wird beschrieben, wie Sie Ihrer eigenen mobilen iOS Swift-Anwendung die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen. Erfahren Sie, wie Sie eine iOS Swift-Anwendung in die Authentifizierungsbibliothek [MSAL für iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) integrieren. 

Verwenden Sie diesen Artikel mit dem Artikel [Konfigurieren der Authentifizierung in einer iOS Swift-Beispielwebanwendung](./configure-authentication-sample-ios-app.md), und ersetzen Sie dabei die iOS Swift-Beispiel-App durch Ihre eigene iOS Swift-App. Nachdem Sie die Schritte in diesem Artikel ausgeführt haben, akzeptiert Ihre Anwendung Anmeldungen über Azure AD B2C.

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die Voraussetzungen und Integrationsschritte unter [Konfigurieren der Authentifizierung in einer iOS Swift-Beispielanwendung](configure-authentication-sample-ios-app.md).

## <a name="create-an-ios-swift-app-project"></a>Erstellen eines iOS Swift-App-Projekts

Falls Sie noch nicht über eine iOS Swift-Anwendung verfügen, gehen Sie wie folgt vor, um ein neues Projekt einzurichten.

1. Öffnen Sie [Xcode](https://developer.apple.com/xcode/), und wählen Sie **File (Datei)**  > **New (Neu)**  > **Project (Projekt)** aus.
1. Für iOS-Apps: Wählen Sie **iOS** > **App** und dann **Next** (Weiter) aus.
1. Geben Sie für **Choose options for your new project** (Optionen für Ihr neues Projekt auswählen) Folgendes an:
    1. **Product name** (Produktname) z. B. `MSALiOS`.
    1. **Organization identifier** (Organisations-ID), z. B. `contoso.com`.
    1. Wählen Sie unter **Interface** (Schnittstelle) **Storyboard** aus.
    1. Wählen Sie unter **Life cycle** (Lebenszyklus) **UIKit App Delegate** (UIKit-App-Delegat) aus.
    1. Wählen Sie unter **Language** (Sprache) **Swift** aus. 
1. Klicken Sie auf **Weiter**.
1. Wählen Sie einen Ordner für das Erstellen Ihrer App und dann die Option **Erstellen** aus.


## <a name="install-the-msal-library"></a>Installieren der MSAL-Bibliothek

1. Installieren Sie die MSAL-Bibliothek mithilfe von [CocoaPods](https://cocoapods.org/).   Erstellen Sie im Ordner mit der Datei `.xcodeproj` Ihres Projekts, wenn die Datei `podfile` nicht vorhanden ist, eine leere Datei mit dem Namen `podfile`. Fügen Sie der Datei `podfile` den folgenden Code hinzu:

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. Ersetzen Sie `<your-target-here>` durch den Namen Ihres Projekts. Beispiel: `MSALiOS`. Weitere Informationen finden Sie in der [Referenz zur Podfile-Syntax](https://guides.cocoapods.org/syntax/podfile.html#podfile).
1. Navigieren Sie in einem Terminalfenster zum Ordner mit der Datei `podfile`. Führen Sie `pod install` aus, um die MSAL-Bibliothek zu installieren.
1. Nachdem Sie den Befehl `pod install` ausgeführt haben, wird eine `<your project name>.xcworkspace`-Datei erstellt. Um das Projekt in Xcode neu zu laden, schließen Sie Xcode, und öffnen Sie `<your project name>.xcworkspace`.

## <a name="set-the-app-url-scheme"></a>Festlegen des URL-Schemas der App

Wenn sich ein Benutzer authentifiziert, sendet Azure AD B2C einen Autorisierungscode an die App, und zwar über den in der Azure AD B2C-Anwendungsregistrierung konfigurierten Umleitungs-URI. 

Das MSAL-Standardformat für den Umleitungs-URI ist `msauth.[Your_Bundle_Id]://auth`. Beispielsweise `msauth.com.microsoft.identitysample.MSALiOS://auth`, wobei `msauth.com.microsoft.identitysample.MSALiOS` das URL-Schema ist.

Registrieren Sie in diesem Schritt Ihr URL-Schema mithilfe des `CFBundleURLSchemes`-Arrays. Ihre Anwendung lauscht beim URL-Schema auf den Rückruf von Azure AD B2C. 

Öffnen Sie in Xcode die Datei [Info.plist](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) als Quellcodedatei. Fügen Sie im Abschnitt `<dict>` den folgenden XML-Ausschnitt ein. 

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

## <a name="add-the-authentication-code"></a>Hinzufügen des Authentifizierungscodes

Der [Beispielcode](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) besteht aus einer `UIViewController`-Klasse. Die Klasse hat folgende Merkmale:

- Definiert die Struktur einer Benutzeroberfläche.
- Enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die App stellt mithilfe dieser Informationen eine Vertrauensstellung mit Azure AD B2C her. 
- Enthält den Authentifizierungscode, um Benutzer zu authentifizieren, Token zu beziehen und zu überprüfen.

Wählen Sie einen `UIViewController` aus, bei dem sich die Benutzer authentifizieren. Führen Sie den Code mit dem [hier bereitgestellten](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift) Code in Ihrem `UIViewController` zusammen. 

## <a name="configure-your-ios-swift-app"></a>Konfigurieren Ihrer iOS Swift-App

Nachdem Sie [den Authentifizierungscode hinzugefügt](#add-the-authentication-code) haben, konfigurieren Sie Ihre iOS Swift-App mit Ihren Azure AD B2C-Einstellungen. Einstellungen des Azure AD B2C-Identitätsanbieters werden in der im vorherigen Abschnitt gewählten `UIViewController`-Klasse konfiguriert. 

Befolgen Sie die Anleitung zum [Konfigurieren der mobilen Beispiel-App](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

## <a name="run-and-test-the-mobile-app"></a>Ausführen und Testen der mobilen App

1. Erstellen Sie das Projekt, und führen Sie es mit einem [Simulator eines verbundenen iOS-Geräts](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device) aus.
1. Wählen Sie **Anmelden** aus. Registrieren Sie sich, oder melden Sie sich dann mit Ihrem lokalen oder Social Media-Konto für Azure AD B2C an.
1. Nach erfolgreicher Authentifizierung wird Ihr Anzeigename in der Navigationsleiste angezeigt.

## <a name="how-it-works"></a>So funktioniert's

In diesem Abschnitt werden die Codebausteine beschrieben, die Ihrer iOS Swift-App die Authentifizierung ermöglichen. Hier finden Sie eine Liste der UiViewController-Methoden und erfahren, wie Sie Ihren Code anpassen. 

### <a name="instantiate-a-public-client-application"></a>Instanziieren einer öffentlichen Clientanwendung

Öffentlichen Clientanwendungen sind nicht vertrauenswürdig genug, um Anwendungsgeheimnisse sicher aufzubewahren, und haben daher keine geheimen Clientschlüssel. Instanziieren Sie MSAL in [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload) mithilfe eines öffentlichen Clientanwendungsobjekts.

Der folgende Codeausschnitt veranschaulicht, wie die MSAL-Bibliothek mit dem Konfigurationsobjekt `MSALPublicClientApplicationConfig` initialisiert wird. 

Das Konfigurationsobjekt enthält Informationen zu Ihrer Azure AD B2C-Umgebung, z. B. Client-ID, Umleitungs-URI und Befugnisse zum Erstellen von Authentifizierungsanforderungen an Azure AD B2C. Informationen zum Konfigurationsobjekt finden Sie unter [Konfigurieren der mobilen Beispiel-App](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

```swift
do {

    let siginPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: siginPolicyAuthority)
    pcaConfig.knownAuthorities = [siginPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

Mit der `initWebViewParams`-Methode wird die Umgebung zur [interaktiver Authentifizierung](../active-directory/develop/customize-webviews.md) konfiguriert. 

Der folgende Swift-Codeausschnitt initialisiert den Klassenmember `webViewParamaters` mit der Systemwebansicht. Weitere Informationen finden Sie im Artikel [Anpassen von Browsern und WebViews für iOS/macOS](../active-directory/develop/customize-webviews.md).

```swift
func initWebViewParams() {
    self.webViewParamaters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParamaters?.webviewType = .default
}
```

### <a name="interactive-authorization-request"></a>Interaktive Autorisierungsanforderung

Eine interaktive Autorisierungsanforderung ist ein Flow, bei dem der Benutzer mithilfe der Systemwebansicht zur Registrierung oder Anmeldung aufgefordert wird. Wenn der Benutzer auf die Schaltfläche **Anmelden** klickt, wird die `authorizationButton`-Methode aufgerufen.

Die `authorizationButton`-Methode bereitet das Objekt `MSALInteractiveTokenParameters` mit relevanten Daten zur Autorisierungsanforderung vor. Die `acquireToken`-Methode verwendet `MSALInteractiveTokenParameters`, um den Benutzer mithilfe der Systemwebansicht zu authentifizieren.

Der folgende Codeausschnitt veranschaulicht, wie die interaktive Autorisierungsanforderung gestartet wird. 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error informarion" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
Sobald der Benutzer den Autorisierungsflow abgeschlossen hat (erfolgreich oder nicht), wird das Ergebnis an den [Abschluss](https://docs.swift.org/swift-book/LanguageGuide/Closures.html) der `acquireToken`-Methode zurückgegeben. 

Die acquireToken-Methode gibt die Objekte `result` und `error` zurück. Verwenden Sie diesen Abschluss für Folgendes:

- Aktualisieren der Benutzeroberfläche der mobilen App mit Informationen nach Abschluss der Authentifizierung
- Aufrufen eines Web-API-Diensts mit einem Zugriffstoken
- Behandeln von Authentifizierungsfehlern, z. B. wenn ein Benutzer den Anmeldeflow abbricht
 
### <a name="call-a-web-api"></a>Aufrufen einer Web-API

Zum Aufrufen einer [tokenbasierten Autorisierungs-Web-API](enable-authentication-web-api.md) benötigt die App ein gültiges Zugriffstoken. Die App führt die folgenden Schritte aus:

1. Beziehen eines Zugriffstokens mit den erforderlichen Berechtigungen (Bereichen) für den Web-API-Endpunkt
1. Übergeben des Zugriffstokens als Bearertoken im Autorisierungsheader der HTTP-Anforderung in folgendem Format:

```http
Authorization: Bearer <access-token>
```

Wenn sich Benutzer [interaktiv authentifizieren](#interactive-authorization-request), erhält die App im Abschluss `acquireToken` ein Zugriffstoken. Verwenden Sie für nachfolgende Web-API-Aufrufe die automatische Methode zum Abrufen des Tokens (`acquireTokenSilent`), wie in diesem Abschnitt beschrieben. 

Die `acquireTokenSilent`-Methode führt die folgenden Schritte aus:

1. Versucht, ein Zugriffstoken mit den angeforderten Bereichen aus dem Tokencache abzurufen. Wenn das Token vorhanden und nicht abgelaufen ist, wird es zurückgegeben. 
1. Wenn das Token nicht im Tokencache vorhanden oder abgelaufen ist, versucht die MSAL-Bibliothek, mithilfe des Aktualisierungstokens ein neues Zugriffstoken zu beziehen. 
1. Wenn das Aktualisierungstoken nicht vorhanden oder abgelaufen ist, wird eine Ausnahme zurückgegeben. In diesem Fall müssen Sie den Benutzer auffordern, [sich interaktiv anzumelden](#interactive-authorization-request).  

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

Die `callGraphAPI`-Methode ruft das Zugriffstoken ab und die Web-API auf.

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
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error informarion" as! Error)")
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

### <a name="sign-out"></a>Abmeldung

Bei der Abmeldung mit MSAL werden alle bekannten Informationen über einen Benutzer aus der Anwendung entfernt. Verwenden Sie die Abmeldemethode, um Benutzer abzumelden und die Benutzeroberfläche zu aktualisieren. Blenden Sie beispielsweise geschützte Benutzeroberflächenelemente und die Abmeldeschaltfläche aus, und zeigen Sie die Anmeldeschaltfläche an.

Im folgenden Codeausschnitt wird das Abmelden eines Benutzers veranschaulicht:

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

* [Konfigurieren von Authentifizierungsoptionen in einer iOS Swift-Anwendung](enable-authentication-ios-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
