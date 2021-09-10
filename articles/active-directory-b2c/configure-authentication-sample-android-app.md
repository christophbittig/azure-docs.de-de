---
title: Konfigurieren der Authentifizierung in einer Android-Beispielanwendung mit Azure Active Directory B2C
description: Hier finden Sie Informationen zur Verwendung von Azure Active Directory B2C zum Anmelden und Registrieren von Benutzern in einer Android-Beispielanwendung.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b77ef28c5161c92bf8eb6c22cf62d5af41698441
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2021
ms.locfileid: "122353643"
---
# <a name="configure-authentication-in-a-sample-android-application-using-azure-active-directory-b2c"></a>Konfigurieren der Authentifizierung in einer Android-Beispielanwendung mit Azure Active Directory B2C

In diesem Artikel wird anhand einer Android-Beispielwebanwendung (Kotlin und Java) veranschaulicht, wie Sie Ihren mobilen Apps die Authentifizierung mithilfe von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein auf OAuth 2.0 basierendes Authentifizierungsprotokoll, mit dem sich ein Benutzer bei einer Anwendung sicher anmelden kann. In diesem Beispiel für eine mobile App wird die [MSAL](../active-directory/develop/msal-overview.md)-Bibliothek mit dem PKCE-Flow für OpenId Connect-Autorisierungscode verwendet. MSAL ist eine von Microsoft bereitgestellte Bibliothek, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu mobilen Apps vereinfacht. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Der Benutzer öffnet die App und wählt **Anmelden** aus.
1. Die App öffnet den Systembrowser des mobilen Geräts und startet eine Authentifizierungsanforderung an Azure AD B2C.
1. Der Benutzer [registriert sich oder meldet sich an](add-sign-up-and-sign-in-policy.md), [setzt das Kennwort zurück](add-password-reset-policy.md) oder meldet sich mit einem [Social Media-Konto](add-identity-provider.md) an.
1. Nach erfolgreicher Anmeldung gibt Azure AD B2C einen Autorisierungscode an die App zurück.
1. Die App führt die folgenden Aktionen aus:
    1. Sie tauscht den Autorisierungscode gegen ein ID-Token, ein Zugriffstoken und ein Aktualisierungstoken.
    1. Sie liest die ID-Tokenansprüche.
    1. Sie speichert die Token zur späteren Verwendung in einem In-Memory-Cache.

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App bei Azure AD B2C anmelden und eine Web-API aufrufen kann, registrieren Sie im Azure AD B2C-Verzeichnis zwei Anwendungen.  

- Durch die Registrierung der **mobilen Anwendung** kann sich Ihre App bei Azure AD B2C anmelden. Geben Sie während der App-Registrierung den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den der Benutzer von Azure AD B2C umgeleitet wird, nachdem die Authentifizierung mit Azure AD B2C abgeschlossen ist. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre mobile App eindeutig identifiziert. Beispiel: **App-ID: 1**.

- Durch die **Web-API**-Registrierung kann Ihre App eine geschützte Web-API aufrufen. Die Registrierung macht die Web-API-Berechtigungen (Bereiche) verfügbar. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die Ihre Web-API eindeutig identifiziert.  Beispiel: **App-ID: 2**. Erteilen Sie Ihrer mobilen App (App-ID: 1) Berechtigungen für die Web-API-Bereiche (App-ID: 2). 


In den folgenden Diagrammen werden die App-Registrierung und die Anwendungsarchitektur veranschaulicht.

![Mobile App mit Web-API-Aufrufregistrierungen und Token](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Aufrufen einer Web-API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>Abmeldung

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem Folgendes ausgeführt wird: 


- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 oder höher.
- [Apache Maven](https://maven.apache.org/)
- [Android-API-Ebene 16](https://developer.android.com/studio/releases/platforms) oder höher.
- [Android Studio](https://developer.android.com/studio) oder ein anderer Code-Editor.


## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Schritt 2: Registrieren mobiler Anwendungen

In diesem Schritt erstellen Sie die mobile App und die Web-API-Anwendungsregistrierung. Dann geben Sie die Bereiche Ihrer Web-API an.

### <a name="21-register-the-web-api-app"></a>2.1 Registrieren der Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Konfigurieren von Web-API-App-Bereichen

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 Registrieren der mobilen App

Führen Sie die folgenden Schritte aus, um die Registrierung der mobilen App zu erstellen:

1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispiel: *android-app1*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Öffentlicher Client/nativ (mobil und Desktop)** aus. Geben Sie dann im Textfeld „URL“ einen der folgenden URIs ein:
    - Für das Kotlin-Beispiel: `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Für das Java-Beispiel: `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. Wählen Sie **Registrieren**.
1. Wählen Sie nach Abschluss der App-Registrierung **Übersicht** aus.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie in einem späteren Schritt beim Konfigurieren der mobilen Anwendung verwenden werden.

    ![Abrufen Ihrer ID für die mobile Anwendung](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 Erteilen von Berechtigungen für die mobile App zur Verwendung der Web-API

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>Schritt 3: Abrufen eines Beispiels für die mobile Android-App

Laden Sie eines der folgenden Beispiele herunter: [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip) oder [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip). Extrahieren Sie die ZIP-Beispieldatei in Ihren Arbeitsordner.

Oder klonen Sie das Beispiel für die mobile Android-Anwendung aus GitHub. 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```bash
git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
```

#### <a name="java"></a>[Java](#tab/java)

```bash
git clone https://github.com/Azure-Samples/ms-identity-android-java
```

--- 


## <a name="step-4-configure-the-sample-web-api"></a>Schritt 4: Konfigurieren der Beispiel-Web-API

In diesem Beispiel wird ein Zugriffstoken mit den relevanten Bereichen bezogen, das die mobile App für eine Web-API verwenden kann. Führen Sie die folgenden Schritte aus, um eine Web-API im Code aufzurufen:

1. Verwenden Sie eine vorhandene Web-API, oder erstellen Sie eine neue. Weitere Informationen finden Sie unter [Aktivieren der Authentifizierung in Ihrer eigenen Web-API mit Azure AD B2C](enable-authentication-web-api.md).
1. Ändern Sie den Beispielcode so, dass [eine Web-API aufgerufen wird](enable-authentication-android-app.md#call-a-web-api).

## <a name="step-5-configure-the-sample-mobile-app"></a>Schritt 5: Konfigurieren des Beispiels für die mobile App

Öffnen Sie das Beispielprojekt mit Android Studio oder einem anderen Code-Editor.  Öffnen Sie dann die Datei `/app/src/main/res/raw/auth_config_b2c.json`. 

Die Konfigurationsdatei *auth_config_b2c.json* enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die mobile App nutzt diese Informationen, um eine Vertrauensstellung mit Azure AD B2C herzustellen, den Benutzer an- und abzumelden sowie Token zu beziehen und zu überprüfen. 

Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen:

|Key  |Wert  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | Die ID der mobilen Anwendung aus [Schritt 2.3](#23-register-the-mobile-app). | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | Der Umleitungs-URI der mobilen Anwendung aus [Schritt 2.3](#23-register-the-mobile-app). | 
| [authorities](../active-directory/develop/msal-client-application-configuration.md#authority)| Die Autorität ist eine URL, die ein Verzeichnis angibt, aus dem die MSAL Token anfordern kann. Verwenden Sie das folgende Format: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. Ersetzen Sie `<your-tenant-name>` durch Ihren Azure AD B2C-[Mandantennamen](tenant-management.md#get-your-tenant-name). Ersetzen Sie dann `<your-sign-in-sign-up-policy>` durch die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben. | 


Öffnen Sie die Klasse `B2CConfiguration`, und aktualisieren Sie die folgenden Klassenmitglieder:

|Key  |Wert  |
|---------|---------|
| Richtlinien| Liste der Benutzerflows oder benutzerdefinierten Richtlinien, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
| azureAdB2CHostName| Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name). Beispielsweise `https://contoso.b2clogin.com`.|
| tenantName| Der vollständige [Mandantenname](tenant-management.md#get-your-tenant-name) Ihres Azure AD B2C-Mandanten. Beispielsweise `contoso.onmicrosoft.com`.|
| Bereiche| Die Web-API-Bereiche, die Sie in [Schritt 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api) erstellt haben.|


## <a name="step-6-run-and-test-the-mobile-app"></a>Schritt 6: Ausführen und Testen der mobilen App

1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Wählen Sie das „Hamburger“-Symbol aus.
    
    ![Screenshot: Auswählen des „Hamburger“-Symbols](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. Wählen Sie den **„B2C“-Modus** aus.

    ![Screenshot: Auswählen des „B2C“-Modus](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. Wählen Sie **BENUTZERFLOW AUSFÜHREN** aus.

    ![Screenshot: Starten des Anmeldeflows](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Registrieren Sie sich, oder aber melden Sie sich mit Ihrem lokalen oder Social Media-Konto für Azure AD B2C an.

1. Nach erfolgreicher Authentifizierung wird Ihr Anzeigename in der Navigationsleiste angezeigt.

    ![Azure AD B2C-Zugriffstoken und Benutzer-ID.](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich, wie Sie die [Authentifizierung in Ihrer eigenen Android-Anwendung aktivieren](enable-authentication-android-app.md).
* [Konfigurieren von Authentifizierungsoptionen in einer Android-Anwendung](enable-authentication-android-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer eigenen Web-API](enable-authentication-web-api.md)
