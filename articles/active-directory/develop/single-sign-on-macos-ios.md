---
title: Konfigurieren von SSO unter macOS und iOS
titleSuffix: Microsoft identity platform
description: Es wird beschrieben, wie Sie einmaliges Anmelden (Single Sign-On, SSO) unter macOS und iOS konfigurieren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 02140728536e458c85e8c63a0fa1ca3c9663506b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050504"
---
# <a name="configure-sso-on-macos-and-ios"></a>Konfigurieren von SSO unter macOS und iOS

Die Microsoft Authentication Library (MSAL) für macOS und iOS unterstützt einmaliges Anmelden (Single Sign-On, SSO) zwischen macOS/iOS-Apps und Browsern. In diesem Artikel werden die folgenden SSO-Szenarien beschrieben:

- [SSO im Hintergrund zwischen mehreren Apps](#silent-sso-between-apps)

Diese Art von SSO funktioniert zwischen mehreren Apps, die von demselben Apple-Entwickler herausgegeben werden. SSO im Hintergrund (also ohne Aufforderung des Benutzers zur Eingabe von Anmeldeinformationen) wird ermöglicht, indem von anderen Apps geschriebene Aktualisierungstoken aus der Keychain gelesen und gegen Zugriffstoken ausgetauscht werden.  

- [SSO per Authentifizierungsbroker](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Diese Option ist unter macOS nicht verfügbar.

Von Microsoft werden als „Broker“ bezeichnete Apps bereitgestellt, die SSO zwischen Anwendungen unterschiedlicher Anbieter ermöglichen, sofern das mobile Gerät in Azure Active Directory (AAD) registriert ist. Für diese Art von SSO muss auf dem Gerät des Benutzers eine Brokeranwendung installiert sein.

- **SSO zwischen MSAL und Safari**

SSO wird hierbei per [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)-Klasse ermöglicht. Es wird der vorhandene Anmeldestatus anderer Apps und des Safari-Browsers genutzt. Dies ist nicht auf Apps beschränkt, die von demselben Apple-Entwickler herausgegeben werden, aber es sind einige Benutzerinteraktionen erforderlich.

Wenn Sie für die Anmeldung von Benutzern die Standardwebansicht in Ihrer App verwenden, erhalten Sie automatisches SSO zwischen MSAL-basierten Anwendungen und Safari. Weitere Informationen zu den Webansichten, die von der MSAL unterstützt werden, finden Sie unter [Anpassen von Browsern und Webansichten](customize-webviews.md).

> [!IMPORTANT]
> Diese Art von SSO ist derzeit unter macOS nicht verfügbar. Die MSAL unter macOS unterstützt nur WKWebView, und hierfür ist keine SSO-Unterstützung für Safari vorhanden. 

- **SSO im Hintergrund zwischen ADAL und MSAL für macOS/iOS-Apps**

MSAL Objective-C unterstützt die Migration und SSO mit ADAL Objective-C-basierten Apps. Die Apps müssen von demselben Apple-Entwickler herausgegeben werden.

Eine Anleitung für App-übergreifendes SSO zwischen ADAL- und MSAL-basierten Apps finden Sie unter [SSO zwischen ADAL- und MSAL-Apps auf macOS und iOS](sso-between-adal-msal-apps-macos-ios.md).

## <a name="silent-sso-between-apps"></a>SSO im Hintergrund zwischen Apps

Die MSAL unterstützt die SSO-Freigabe über Zugriffsgruppen der iOS-Keychain.

Zum Aktivieren von übergreifendem SSO für Ihre Anwendungen müssen Sie die folgenden Schritte ausführen, die unten ausführlicher beschrieben sind:

1. Stellen Sie sicher, dass für Ihre gesamten Anwendungen die gleiche Client-ID oder Anwendungs-ID verwendet wird.
1. Stellen Sie sicher, dass alle Ihre Anwendungen das gleiche Signaturzertifikat von Apple verwenden, um die gemeinsame Verwendung von Keychains zu ermöglichen.
1. Fordern Sie für jede Ihrer Anwendungen die gleichen Schlüsselbundberechtigung an.
1. Informieren Sie die MSAL SDKs über die freigegebene Keychain, die verwendet werden soll, falls sie sich von der Standardkeychain unterscheidet.

### <a name="use-the-same-client-id-and-application-id"></a>Verwenden der gleichen Client-ID und Anwendungs-ID

Damit die Microsoft Identity Platform darüber informiert ist, welche Anwendungen Token gemeinsam nutzen können, muss für diese Anwendungen die gleiche Client-ID bzw. Anwendungs-ID verwendet werden. Hierbei handelt es sich um den eindeutigen Bezeichner, den Sie bei der Registrierung Ihrer ersten Anwendung im Portal erhalten haben.

Von der Microsoft Identity Platform können Apps, für die die gleiche Anwendungs-ID verwendet wird, anhand ihrer **Umleitungs-URIs** unterschieden werden. Jede Anwendung kann mehrere Umleitungs-URIs im Integrationsportal registrieren. Jede App innerhalb Ihrer Suite kann einen anderen Umleitungs-URI verwenden. Beispiel:

Umleitungs-URI für App1: `msauth.com.contoso.mytestapp1://auth`  
Umleitungs-URI für App2: `msauth.com.contoso.mytestapp2://auth`  
Umleitungs-URI für App3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Das Format von Umleitungs-URIs muss mit dem von MSAL unterstützten Format kompatibel sein. Dies ist unter [MSAL-Formatanforderungen für Umleitungs-URIs](redirect-uris-ios.md#msal-redirect-uri-format-requirements) dokumentiert.

### <a name="setup-keychain-sharing-between-applications"></a>Einrichten einer anwendungsübergreifenden Keychainfreigabe

Weitere Informationen zum Aktivieren der Keychainfreigabe finden Sie im Artikel zum Thema [Hinzufügen von Funktionen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) von Apple. Wichtig ist, dass Sie sich für einen Namen für die Keychain entscheiden und diese Funktion Ihren gesamten Anwendungen hinzufügen, für die SSO verwendet werden soll.

Wenn Sie die Berechtigungen richtig eingerichtet haben, wird in Ihrem Projektverzeichnis die Datei `entitlements.plist` angezeigt, die in etwa Daten wie im folgenden Beispiel enthält:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Hinzufügen einer neuen Keychaingruppe

Fügen Sie Ihrem Projekt **Funktionen** eine neue Keychaingruppe hinzu. Die Keychaingruppe sollte wie folgt lauten:
* `com.microsoft.adalcache` unter iOS 
* `com.microsoft.identity.universalstorage` unter macOS

![Beispiel für eine Keychain](media/single-sign-on-macos-ios/keychain-example.png)

Weitere Informationen finden Sie unter [Keychaingruppen](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurieren des Anwendungsobjekts

Nachdem Sie die Keychainberechtigung in allen Anwendungen aktiviert haben und zur Verwendung von SSO bereit sind, können Sie `MSALPublicClientApplication` mit Ihrer Keychainzugriffsgruppe wie im folgenden Beispiel konfigurieren:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Bei anwendungsübergreifender Nutzung einer Keychain kann jede Anwendung Benutzer und sogar anwendungsübergreifend alle Token löschen.
> Dies hat besonders starke Auswirkungen, wenn Sie über Anwendungen verfügen, die zur Ausführung von Hintergrundaufgaben auf Token zurückgreifen.
> Bei der Freigabe einer Keychain müssen Sie sehr sorgfältig vorgehen, wenn in Ihrer App Entfernungsvorgänge des Microsoft Identity SDK genutzt werden.

Das ist alles! Das Microsoft Identity SDK verwendet Anmeldeinformationen jetzt über Ihre gesamten Anwendungen hinweg. Die Kontoliste wird ebenfalls über alle Anwendungsinstanzen hinweg verwendet.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO per Authentifizierungsbroker unter iOS

Die MSAL verfügt über Unterstützung für die Authentifizierung per Broker mit Microsoft Authenticator. Microsoft Authenticator ermöglicht SSO für Geräte, die bei AAD registriert sind, und unterstützt Ihre Anwendung zudem bei der Einhaltung der Richtlinien für bedingten Zugriff.

Mit den folgenden Schritten aktivieren Sie SSO, indem Sie einen Authentifizierungsbroker für Ihre App verwenden:

1. Registrieren Sie in der Datei „Info.plist“ Ihrer App ein brokerkompatibles Umleitungs-URI-Format für die Anwendung. Das brokerkompatible Umleitungs-URI-Format lautet `msauth.<app.bundle.id>://auth`. Ersetzen Sie „<app.bundle.id>“ durch die Paket-ID Ihrer Anwendung. Beispiel:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Fügen Sie der Datei „Info.plist“ Ihrer App unter `LSApplicationQueriesSchemes` die folgenden Schemas hinzu:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Fügen Sie Ihrer Datei `AppDelegate.m` für die Verarbeitung von Rückrufen Folgendes hinzu:

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Bei Verwendung von Xcode 11** müssen Sie den MSAL-Rückruf stattdessen in der Datei `SceneDelegate` anordnen.
Wenn Sie zur Erzielung von Kompatibilität mit älteren iOS-Versionen sowohl „UISceneDelegate“ als auch „UIApplicationDelegate“ unterstützen, muss der MSAL-Rückruf in beide Dateien eingefügt werden.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zu [Authentifizierungsfluss und Anwendungsszenarios](authentication-flows-app-scenarios.md)
