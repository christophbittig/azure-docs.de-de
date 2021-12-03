---
title: Konfigurieren der Authentifizierung in einer iOS Swift-Beispielanwendung mithilfe von Azure Active Directory B2C
description: In diesem Artikel wird erörtert, wie Sie Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer iOS Swift-Webanwendung verwenden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 07c41b55d494167bcf267d4af1bdd154c6958ee9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007860"
---
# <a name="configure-authentication-in-a-sample-ios-swift-app-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einer iOS Swift-Beispielanwendung mithilfe von Azure Active Directory B2C

In diesem Artikel wird anhand einer [iOS Swift](https://developer.apple.com/swift/)-Beispielwebanwendung veranschaulicht, wie Sie Ihren mobilen Anwendungen die Authentifizierung mithilfe von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können OIDC verwenden, um Benutzer sicher bei einer Anwendung anzumelden. In diesem Beispiel für eine mobile App wird die [Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md) mit dem OIDC-Autorisierungscode Proof Key for Code Exchange (PKCE) verwendet. MSAL ist eine von Microsoft bereitgestellte Bibliothek, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu mobilen Apps vereinfacht. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer öffnen die App und wählen **Anmeldedaten** aus.
1. Die App öffnet den Systembrowser des mobilen Geräts und richtet eine Authentifizierungsanforderung an Azure AD B2C.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md), [setzen das Passwort zurück](add-password-reset-policy.md), oder melden sich mit einem [Social Account](add-identity-provider.md) an.
1. Nach erfolgreicher Benutzeranmeldung gibt Azure AD B2C einen Autorisierungscode an die App zurück.
1. Die App führt die folgenden Aktionen aus:
    1. Sie tauscht den Autorisierungscode gegen ein ID-Token, ein Zugriffstoken und ein Aktualisierungstoken ein.
    1. Sie liest die ID-Tokenansprüche.
    1. Sie speichert die Token zur späteren Verwendung in einem In-Memory-Cache.

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App bei Azure AD B2C anmelden und eine Web-API aufrufen kann, registrieren Sie im Azure AD B2C-Verzeichnis zwei Anwendungen:  

- Durch die Registrierung der **mobilen Anwendung** kann sich Ihre App bei Azure AD B2C anmelden. Geben Sie bei der App-Registrierung den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den die Benutzer von Azure AD B2C umgeleitet werden, nachdem sie sich mit Azure AD B2C authentifiziert haben. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre mobile App eindeutig identifiziert (Beispiel: *App ID: 1*).

- Durch die **Web-API**-Registrierung kann Ihre App eine geschützte Web-API aufrufen. Die Registrierung macht die Web-API-Berechtigungen (Bereiche) verfügbar. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die Ihre Web-API eindeutig identifiziert (Beispiel: *App ID: 2*). Erteilen Sie Ihrer mobilen App (App-ID: 1) Berechtigungen für die Web-API-Bereiche (App-ID: 2). 


Die Anwendungsregistrierung und -architektur sind in den folgenden Diagrammen dargestellt:

![Diagramm einer mobilen App mit Web-API-Aufrufregistrierungen und Token.](./media/configure-authentication-sample-ios-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Aufrufen einer Web-API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Der Abmeldeflow

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem Folgendes ausgeführt wird: 

- [Xcode 13 oder höher](https://developer.apple.com/xcode/).
- [CocoaPods-Abhängigkeits-Manager](https://cocoapods.org/) für Cocoa-Projekte für Swift- und Objective-C


## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Schritt 2: Registrieren mobiler Anwendungen

Erstellen Sie die mobile App und Web-API-Anwendungsregistrierung und geben Sie die Bereiche Ihrer Web-API an.

### <a name="step-21-register-the-web-api-app"></a>Schritt 2.1: Registrieren der Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Schritt 2.2: Konfigurieren von Bereichen für die Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-mobile-app"></a>Schritt 2.3: Registrieren der mobilen App

Gehen Sie zum Erstellen der Registrierung für die mobile App folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein, (z. B. *iOS-app1*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Öffentlicher Client/nativ (mobil und Desktop)** aus, und geben Sie dann in das URL-Feld `msauth.com.microsoft.identitysample.MSALiOS://auth` ein.
1. Wählen Sie **Registrieren**.
1. Wählen Sie nach Abschluss der App-Registrierung **Übersicht** aus.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie später beim Konfigurieren der mobilen Anwendung verwenden.
    ![Screenshot, der die ID der mobilen Anwendung hervorhebt.](./media/configure-authentication-sample-ios-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-24-grant-the-mobile-app-permissions-for-the-web-api"></a>Schritt 2.4: Erteilen von Berechtigungen für die mobile App zur Verwendung der Web-API

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>Schritt 3: Konfigurieren der Beispiel-Web-API

In diesem Beispiel wird ein Zugriffstoken mit den relevanten Bereichen bezogen, die die mobile App für eine Web-API verwenden kann. Gehen Sie wie folgt vor, um eine Web-API aus Code aufzurufen:

1. Verwenden Sie eine vorhandene Web-API, oder erstellen Sie eine neue. Weitere Informationen finden Sie unter [Authentifizierung in Ihrer eigenen Web-API mithilfe von Azure AD B2C](enable-authentication-web-api.md) aktivieren.
1. Ändern Sie den Beispielcode so, dass eine [Web-API aufgerufen wird](enable-authentication-iOs-app.md#step-63-call-a-web-api).
1. Nachdem Sie die Web-API konfiguriert haben, kopieren Sie den URI des Web-API-Endpunkts. In den nächsten Schritten verwenden Sie den Web-API-Endpunkt.

> [!TIP]
> Wenn Sie keine Web-API haben, können Sie dieses Beispiel trotzdem ausführen. In diesem Fall gibt die App das Token für den Zugriff zurück, kann aber die Web-API nicht aufrufen. 

## <a name="step-4-get-the-ios-mobile-app-sample"></a>Schritt 4: Abrufen des Beispiels für die mobile iOS-App

1. [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/archive/refs/heads/vNext.zip), oder klonen Sie die beispielhafte Webanwendung aus dem [GitHub-Repository](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal). 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/tree/vNext.git
    ``` 

1. Installieren Sie die MSAL-Bibliothek mithilfe von [CocoaPods](https://cocoapods.org/). Wechseln Sie in einem Terminalfenster zum Stammordner des Projekts. Dieser Ordner enthält die *Pod-Datei*. Führen Sie den folgenden Befehl aus:

    ```bash
    pod install
    ```

1. Öffnen Sie den Arbeitsbereich `MSALiOS.xcworkspace` mithilfe von Xcode.



## <a name="step-5-configure-the-sample-mobile-app"></a>Schritt 5: Konfigurieren des Beispiels für die mobile App

Öffnen Sie die *Datei ViewController.swift*. Die Member der `ViewController`-Klasse enthalten Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die mobile App nutzt diese Informationen, um eine Vertrauensstellung mit Azure AD B2C herzustellen, Benutzer an- und abzumelden sowie Token zu beziehen und zu überprüfen. 

Aktualisieren Sie die folgenden Klassenmember:

|Schlüssel  |Wert  |
|---------|---------|
|kTenantName| Der vollständige Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`).|
|kAuthorityHostName|Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `contoso.b2clogin.com`).|
|kClientID|Die ID der mobilen Anwendung aus [Schritt 2.3](#step-23-register-the-mobile-app).|
|kRedirectUri|Der Umleitungs-URI der mobilen Anwendung aus [Schritt 2.3](#step-23-register-the-mobile-app): `msauth.com.microsoft.identitysample.MSALiOS://auth`.|
|kSignupOrSigninPolicy| Der Benutzerflow für Registrierung und Anmeldung oder die benutzerdefinierte Richtlinie, den/die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
|kEditProfilePolicy|Der Benutzerflow zur Profilbearbeitung oder die benutzerdefinierte Richtlinie, den/die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
|kGraphURI| (Optional) Der Web-API-Endpunkt, den Sie in [Schritt 3](#step-3-configure-the-sample-web-api) erstellt haben (z. B. `https://contoso.azurewebsites.net/hello`).|
| kScopes | Die Web-API-Bereiche, die Sie in [Schritt 2.4](#step-24-grant-the-mobile-app-permissions-for-the-web-api) erstellt haben.|
| | | 



## <a name="step-6-run-and-test-the-mobile-app"></a>Schritt 6: Ausführen und Testen der mobilen App

1. Erstellen Sie das Projekt, und führen Sie es mit einem [Simulator eines verbundenen iOS-Geräts](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device) aus.

1. Wählen Sie **Registrieren**, und melden Sie sich mit Ihrem lokalen oder Social Media-Konto für Azure AD B2C an.

    ![Screenshot, der das Starten des Anmeldeflows hervorhebt.](./media/configure-authentication-sample-ios-app/sign-in.png)

1. Nach erfolgreicher Authentifizierung wird Ihr Anzeigename in der Navigationsleiste angezeigt.

    ![Screenshot, der Azure AD B2C-Zugriffstoken und Benutzer-ID hervorhebt.](./media/configure-authentication-sample-ios-app/post-sign-in.png) 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:
* [Aktivieren der Authentifizierung in Ihrer eigenen iOS-App mit Azure AD B2C](enable-authentication-ios-app.md)
* [Aktivieren der Authentifizierung in einer iOS-App mithilfe von Azure AD B2C](enable-authentication-ios-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer eigenen Web-API mit Azure AD B2C](enable-authentication-web-api.md)
