---
title: Konfigurieren der Authentifizierung in einer Android-Beispielanwendung mit Azure Active Directory B2C
description: In diesem Artikel wird erörtert, wie Sie Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer Android-Anwendung verwenden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 699e548809b123589466062f1e9600edee9e7b78
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007933"
---
# <a name="configure-authentication-in-a-sample-android-app-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einer Android-Beispiel-App mithilfe von Azure AD B2C

In diesem Artikel wird anhand einer Android-Beispielwebanwendung (Kotlin und Java) veranschaulicht, wie Sie Ihren mobilen Apps die Authentifizierung mithilfe von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können OIDC verwenden, um Benutzer sicher bei einer Anwendung anzumelden. In diesem Beispiel für eine mobile App wird die [Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md) mit dem OIDC-Autorisierungscodeflow PKCE verwendet. MSAL ist eine von Microsoft bereitgestellte Bibliothek, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung zu mobilen Apps vereinfacht. 

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer öffnen die App und wählen **Anmelden** aus.
1. Die App öffnet den Systembrowser des mobilen Geräts und richtet eine Authentifizierungsanforderung an Azure AD B2C.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md), [setzen das Passwort zurück](add-password-reset-policy.md), oder melden sich mit einem [Social Account](add-identity-provider.md) an.
1. Nach erfolgreicher Benutzeranmeldung gibt Azure AD B2C einen Autorisierungscode an die App zurück.
1. Die App führt die folgenden Aktionen aus:
    1. Sie tauscht den Autorisierungscode gegen ein ID-Token, ein Zugriffstoken und ein Aktualisierungstoken ein.
    1. Sie liest die ID-Tokenansprüche.
    1. Sie speichert die Token zur späteren Verwendung in einem In-Memory-Cache.

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App bei Azure AD B2C anmelden und eine Web-API aufrufen kann, registrieren Sie im Azure AD B2C-Verzeichnis zwei Anwendungen.  

- Durch die Registrierung der **mobilen Anwendung** kann sich Ihre App bei Azure AD B2C anmelden. Geben Sie bei der App-Registrierung den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den die Benutzer von Azure AD B2C umgeleitet werden, nachdem sie sich mit Azure AD B2C authentifiziert haben. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre mobile App eindeutig identifiziert (z. B. *App ID: 1*).

- Durch die **Web-API**-Registrierung kann Ihre App eine geschützte Web-API aufrufen. Die Registrierung macht die Web-API-Berechtigungen (Bereiche) verfügbar. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die Ihre Web-API eindeutig identifiziert (Beispiel: *App ID: 2*). Erteilen Sie Ihrer mobilen App (App-ID: 1) Berechtigungen für die Web-API-Bereiche (App-ID: 2). 


Anwendungsregistrierung und -architektur sind in den folgenden Diagrammen dargestellt:

![Diagramm der mobilen App mit Web-API-Aufrufregistrierungen und Token.](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Aufrufen einer Web-API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Der Abmeldeflow

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem Folgendes ausgeführt wird: 


- [Java Development Kit (JDK) 8 oder höher](https://openjdk.java.net/)
- [Apache Maven](https://maven.apache.org/)
- [Android-API-Ebene 16 oder höher](https://developer.android.com/studio/releases/platforms)
- [Android Studio](https://developer.android.com/studio) oder ein anderer Code-Editor


## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Schritt 2: Registrieren mobiler Anwendungen

Erstellen Sie die mobile App und die Web-API-Anwendungsregistrierung, und geben Sie die Bereiche Ihrer Web-API an.

### <a name="step-21-register-the-web-api-app"></a>Schritt 2.1: Registrieren der Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Schritt 2.2: Konfigurieren von Bereichen für die Web-API-App

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-mobile-app"></a>Schritt 2.3: Registrieren der mobilen App

Gehen Sie zum Erstellen der Registrierung für die mobile App folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein, (z. B. *android-app1*).
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Öffentlicher Client/nativ (mobil und Desktop)** aus. Geben Sie dann im Textfeld „URL“ einen der folgenden URIs ein:
    - Für das Kotlin-Beispiel: `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Für das Java-Beispiel: `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. Wählen Sie **Registrieren**.
1. Wählen Sie nach Abschluss der App-Registrierung **Übersicht** aus.
1. Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie später beim Konfigurieren der mobilen Anwendung verwenden.

    ![Screenshot mit hervorgehobener ID der Android-Anwendung](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-24-grant-the-mobile-app-permissions-for-the-web-api"></a>Schritt 2.4: Erteilen von Berechtigungen für die mobile App zur Verwendung der Web-API

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>Schritt 3: Abrufen eines Beispiels für die mobile Android-App

Führen Sie einen der folgenden Schritte aus:

- Laden Sie eines der folgenden Beispiele herunter: 
   - [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip)
   - [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip) 

   Extrahieren Sie die ZIP-Beispieldatei in Ihren Arbeitsordner.

- Klonen Sie das Beispiel für die mobile Android-Anwendung aus GitHub. 

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

In diesem Beispiel wird ein Zugriffstoken mit den relevanten Bereichen abgerufen, das die mobile App für eine Web-API verwenden kann. Gehen Sie wie folgt vor, um eine Web-API aus Code aufzurufen:

1. Verwenden Sie eine vorhandene Web-API, oder erstellen Sie eine neue. Weitere Informationen finden Sie unter [Authentifizierung in Ihrer eigenen Web-API mithilfe von Azure AD B2C](enable-authentication-web-api.md) aktivieren.
1. Ändern Sie den Beispielcode so, dass [eine Web-API aufgerufen wird](enable-authentication-android-app.md#step-6-call-a-web-api).

## <a name="step-5-configure-the-sample-mobile-app"></a>Schritt 5: Konfigurieren des Beispiels für die mobile App

Öffnen Sie das Beispielprojekt mit Android Studio oder einem anderen Code-Editor, und öffnen Sie dann die Datei */app/src/main/res/raw/auth_config_b2c.json*. 

Die Konfigurationsdatei *auth_config_b2c.json* enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. Die mobile App nutzt diese Informationen, um eine Vertrauensstellung mit Azure AD B2C herzustellen, Benutzer an- und abzumelden sowie Token zu beziehen und zu überprüfen. 

Aktualisieren Sie die folgenden Eigenschaften der App-Einstellungen:

|Schlüssel  |Wert  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | Die ID der mobilen Anwendung aus [Schritt 2.3](#step-23-register-the-mobile-app). | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | Der Umleitungs-URI der mobilen Anwendung aus [Schritt 2.3](#step-23-register-the-mobile-app). | 
| [authorities](../active-directory/develop/msal-client-application-configuration.md#authority)| Die Autorität ist eine URL, die ein Verzeichnis angibt, aus dem die MSAL Token anfordern kann. Verwenden Sie das folgende Format: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. Ersetzen Sie `<your-tenant-name>` durch den Namen des Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name). Ersetzen Sie dann `<your-sign-in-sign-up-policy>` durch die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben. |
| | | 


Öffnen Sie die Klasse `B2CConfiguration`, und aktualisieren Sie die folgenden Klassenmitglieder:

|Schlüssel  |Wert  |
|---------|---------|
| Richtlinien| Liste der Benutzerflows oder benutzerdefinierten Richtlinien, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
| azureAdB2CHostName| Der erste Teil Ihres Azure AD B2C-[Mandantennamens](tenant-management.md#get-your-tenant-name) (z. B. `https://contoso.b2clogin.com`).|
| tenantName| Der vollständige Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`).|
| Bereiche| Die Web-API-Bereiche, die Sie in [Schritt 2.4](#step-24-grant-the-mobile-app-permissions-for-the-web-api) erstellt haben.|
| | |


## <a name="step-6-run-and-test-the-mobile-app"></a>Schritt 6: Ausführen und Testen der mobilen App

1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Wählen Sie oben links das Hamburger-Symbol (auch als Symbol für reduziertes Menü bezeichnet) aus (siehe Abbildung):
    
    ![Screenshot mit hervorgehobenem Hamburger-Symbol (bzw. Symbol für reduziertes Menü).](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. Wählen Sie im linken Bereich **B2C-Modus** aus.

    ![Screenshot mit hervorgehobenem Befehl „B2C-Modus“ im linken Bereich.](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. Wählen Sie **Benutzerflow ausführen** aus.

    ![Screenshot mit hervorgehobener Schaltfläche „Benutzerflow ausführen“.](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Registrieren Sie sich, oder melden Sie sich mit Ihrem lokalen Azure AD B2C- oder Social Media-Konto an.

1. Nach der erfolgreichen Authentifizierung wird Ihr Anzeigename im Bereich **B2C-Modus** angezeigt.

    ![Screenshot nach erfolgreicher Authentifizierung, angezeigt werden angemeldeter Benutzer und Richtlinie.](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:
* [Aktivieren der Authentifizierung in Ihrer eigenen Android-App](enable-authentication-android-app.md)
* [Konfigurieren von Authentifizierungsoptionen in einer Android-App](enable-authentication-android-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
