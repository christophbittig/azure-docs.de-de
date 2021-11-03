---
title: Konfigurieren der Authentifizierung in einem Beispiel einer Single-Page-Webanwendung (SPA) mithilfe von Azure Active Directory B2C
description: In diesem Artikel wird erörtert, wie Sie Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer Single-Page-Webanwendung (SPA) verwenden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 620afdfbcc62c3ce8e07c1572dd4ec20634ebd64
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007877"
---
# <a name="configure-authentication-in-a-sample-single-page-application-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einem Beispiel einer Single-Page-Webanwendung (SPA) mithilfe von Azure AD B2C

In diesem Artikel wird anhand eines Beispiels einer Single-Page-Webanwendung (SPA) für JavaScript veranschaulicht, wie Sie Ihren SPAs die Authentifizierung bei Azure Active Directory B2C (Azure AD B2C) hinzufügen.

## <a name="overview"></a>Übersicht

OpenID Connect (OIDC) ist ein Authentifizierungsprotokoll, das auf OAuth 2.0 basiert. Sie können damit einen Benutzer sicher bei einer Anwendung anmelden. Für dieses Beispiel einer Single-Page-Webanwendung werden [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) und der OIDC PKCE-Flow verwendet. „MSAL.js“ ist eine von Microsoft bereitgestellte Bibliothek, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung für SPAs vereinfacht.

### <a name="sign-in-flow"></a>Anmeldefluss

Der Anmeldeflow umfasst die folgenden Schritte:

1. Benutzer navigieren zur Web-App und wählen **Anmelden** aus.
1. Die App löst eine Authentifizierungsanforderung aus und leitet Benutzer an Azure AD B2C um.
1. Benutzer [registrieren oder melden sich an](add-sign-up-and-sign-in-policy.md) und [setzen das Kennwort](add-password-reset-policy.md) zurück. Alternativ können sie sich mit einem [Social Media-Konto](add-identity-provider.md) anmelden.
1. Nach erfolgreicher Benutzeranmeldung gibt Azure AD B2C einen Autorisierungscode an die App zurück.
1. Die Single-Page-Webanwendung überprüft das ID-Token, liest die Ansprüche und ermöglicht es dem Benutzer wiederum, geschützte Ressourcen/APIs aufzurufen.

### <a name="app-registration-overview"></a>Übersicht über die App-Registrierung

Damit sich Ihre App bei Azure AD B2C anmelden und eine Web-API aufrufen kann, registrieren Sie im Azure AD B2C-Verzeichnis zwei Anwendungen.  

- Durch die **Webanwendungsregistrierung** kann sich Ihre App bei Azure AD B2C anmelden. Während der Registrierung geben Sie den *Umleitungs-URI* an. Der Umleitungs-URI ist der Endpunkt, an den Benutzer von Azure AD B2C umgeleitet werden, nachdem die Authentifizierung mithilfe von Azure AD B2C abgeschlossen ist. Beim App-Registrierungsprozess wird eine *Anwendungs-ID* generiert, die auch als *Client-ID* bezeichnet wird und Ihre App eindeutig identifiziert.

- Durch die **Web-API**-Registrierung kann Ihre App eine sichere Web-API aufrufen. Die Registrierung umfasst die Web-API-*Bereiche*. Die Bereiche bieten eine Möglichkeit, Berechtigungen für geschützte Ressourcen wie Ihre Web-API zu verwalten. Sie erteilen der Webanwendung Berechtigungen für die Bereiche der Web-API. Wenn ein Zugriffstoken angefordert wird, gibt Ihre App im Bereichsparameter der Anforderung die gewünschten Berechtigungen an.  

App-Architektur und -Registrierungen sind im folgenden Diagramm dargestellt:

![Diagramm einer Web-App mit Web-API-Aufrufregistrierungen und Token.](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Aufrufen einer Web-API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>Abmeldeflow

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Computer, auf dem Folgendes ausgeführt wird:

* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor
* [Node.js-Runtime](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>Schritt 1: Konfigurieren Ihres Benutzerflows

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>Schritt 2: Registrieren Ihrer Single-Page-Webanwendung (SPA) und API

In diesem Schritt erstellen Sie die SPA- und Web-API-Anwendungsregistrierungen und geben die Bereiche Ihrer Web-API an.

### <a name="step-21-register-the-web-api-application"></a>Schritt 2.1 Registrieren der Web-API-Anwendung

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-scopes"></a>Schritt 2.2: Konfigurieren von Bereichen

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-23-register-the-spa"></a>Schritt 2.3: Registrieren der SPA

Gehen Sie zur Erstellung der SPA-Registrierung folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie einen **Namen** für die Anwendung ein, z. B. *MyApp*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus. 
1. Wählen Sie unter **Umleitungs-URI** die Option **Single-Page-Webanwendung (SPA)** aus, und geben Sie `http://localhost:6420` in das Feld „URL“ ein.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen **Administratoreinwilligung für OpenID- und Offlinezugriffsberechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

### <a name="step-24-enable-the-implicit-grant-flow"></a>Schritt 2.4 Aktivieren des Flows zur impliziten Genehmigung

Aktivieren Sie als Nächstes den Flow zur impliziten Genehmigung:

1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.

1. Wählen Sie **Neue Benutzeroberfläche ausprobieren** aus (sofern die Option angezeigt wird).

1. Aktivieren Sie unter **Implizite Genehmigung** das Kontrollkästchen **ID-Token**.

1. Wählen Sie **Speichern** aus.

   Notieren Sie sich die **Anwendungs- bzw. Client-ID**, die Sie später beim Konfigurieren der Webanwendung verwenden.

    ![Screenshot der Seite „Übersicht“ der Web-App zum Aufzeichnen Ihrer Webanwendungs-ID.](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-25-grant-permissions"></a>Schritt 2.5: Erteilen von Berechtigungen

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>Schritt 3: Abrufen des SPA-Beispielcodes

In diesem Beispiel wird veranschaulicht, wie eine Single-Page-Webanwendung Azure AD B2C für die Benutzerregistrierung und -anmeldung verwenden kann. Anschließend bezieht die App ein Zugriffstoken und ruft eine geschützte Web-API auf. 

Um den SPA-Beispielcode abzurufen, können Sie einen der folgenden Schritte ausführen: 

* [Laden Sie eine ZIP-Datei herunter.](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) 
* Klonen Sie das Beispiel auf GitHub, indem Sie den folgenden Befehl ausführen:

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

### <a name="step-31-update-the-spa-sample"></a>Schritt 3.1: Aktualisieren des SPA-Beispiels

Nachdem Sie das SPA-Beispiel abgerufen haben, aktualisieren Sie den Code mit Ihren Azure AD B2C- und Web-API-Werten. Öffnen Sie im Ordner des Beispiels unter dem Ordner `App` die in der folgenden Tabelle aufgeführten JavaScript-Dateien, und aktualisieren Sie sie mit den entsprechenden Werten.  


|Datei  |Key  |Wert  |
|---------|---------|---------|
|authConfig.js|clientId| Die SPA-ID aus [Schritt 2.3](#step-23-register-the-spa).|
|policies.js| Namen| Die Benutzerflows oder die benutzerdefinierte Richtlinie, die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben.|
|policies.js|authorities|Der Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`). Ersetzen Sie ihn durch die Benutzerflows oder die benutzerdefinierte Richtlinie, den/die Sie in [Schritt 1](#step-1-configure-your-user-flow) erstellt haben (z. B. `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`).|
|policies.js|authorityDomain|Der Name Ihres Azure AD B2C-[Mandanten](tenant-management.md#get-your-tenant-name) (z. B. `contoso.onmicrosoft.com`).|
|apiConfig.js|b2cScopes|Die Web-API-Bereiche, die Sie in [Schritt 2.2](#step-22-configure-scopes) erstellt haben (z. B. `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`).|
|apiConfig.js|webApi|Die URL der Web-API: `http://localhost:5000/tasks`.|
| | | |

Der sich ergebende Code sollte in etwa wie das folgende Beispiel aussehen:

*authConfig.js:*

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

*policies.js:*

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

*apiConfig.js:*

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>Schritt 4: Abrufen des Web-API-Beispielcodes

Nachdem Sie die Web-API registriert und die zugehörigen Bereiche definiert haben, können Sie den Web-API-Code für die Verwendung mit Ihrem Azure AD B2C-Mandanten konfigurieren. 

Führen Sie einen der folgenden Schritte aus, um den Beispielcode der Web-API abzurufen:

* [Laden Sie ein \*ZIP-Archiv](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) herunter.

* Klonen Sie das Web-API-Beispielprojekt auf GitHub, indem Sie den folgenden Befehl ausführen:

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
    ```

* Sie können auch direkt zum Projekt [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) auf GitHub navigieren.

### <a name="step-41-update-the-web-api"></a>Schritt 4.1 Aktualisieren der Web-API

1. Öffnen Sie die Datei *config.json* in Ihrem Code-Editor.
1. Ändern Sie die Variablenwerte gemäß der zuvor erstellten Anwendungsregistrierung. Aktualisieren Sie außerdem den Richtliniennamen (`policyName`) mit dem Benutzerflow, den Sie im Rahmen der Voraussetzungen erstellt haben, z. B. *b2c_1_susi*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="step-42-enable-cors"></a>Schritt 4.2 Aktivieren von CORS

Damit Ihre Single-Page-Webanwendung die Node.js-Web-API aufrufen kann, müssen Sie in der Web-API [CORS](https://expressjs.com/en/resources/middleware/cors.html) (Cross-Origin Resource Sharing) aktivieren. Achten Sie in einer Produktionsanwendung sorgfältig darauf, von welcher Domäne die Anforderung stammt. In diesem Beispiel können Sie Anforderungen von jeder beliebigen Domäne zulassen.

Verwenden Sie die folgende Middleware, um CORS zu aktivieren. In dem Node.js-Web-API-Codebeispiel, das Sie heruntergeladen haben, wurde sie bereits der Datei *index.js* hinzugefügt.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>Schritt 5: Ausführen der SPA und der Web-API

Nun können Sie den bereichsbezogenen API-Zugriff der Single-Page-Webanwendung testen. Führen Sie sowohl die Node.js-Web-API als auch die exemplarische JavaScript-Single-Page-Webanwendung auf Ihrem lokalen Computer aus. Melden Sie sich anschließend bei der Single-Page-Webanwendung an, und wählen Sie die Schaltfläche **API aufrufen** aus, um eine Anforderung an die geschützte API zu initiieren.

### <a name="run-the-nodejs-web-api"></a>Ausführen der Node.js-Web-API

1. Öffnen Sie ein Konsolenfenster, und wechseln Sie zum Verzeichnis mit dem Node.js-Web-API-Beispiel. Beispiel:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Führen Sie die folgenden Befehle aus:

    ```console
    npm install && npm update
    node index.js
    ```

    Im Konsolenfenster wird die Portnummer angezeigt, unter der die Anwendung gehostet wird.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Ausführen der Single-Page-Webanwendung

1. Öffnen Sie ein weiteres Konsolenfenster, und wechseln Sie zum Verzeichnis mit dem JavaScript-SPA-Beispiel. Beispiel:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Führen Sie die folgenden Befehle aus:

    ```console
    npm install && npm update
    npm start
    ```

    Im Konsolenfenster wird die Portnummer angezeigt, unter der die Anwendung gehostet wird.

    ```console
    Listening on port 6420...
    ```

1. Um die Anwendung anzuzeigen, rufen Sie in Ihrem Browser `http://localhost:6420` auf.

    ![Screenshot der im Browserfenster angezeigten SPA-Beispiel-App](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. Schließen Sie den Registrierungs- oder Anmeldevorgang ab. Nach erfolgreicher Anmeldung sollte das Beispiel „User \<your username> logged in“ angezeigt werden.
1. Wählen Sie die Schaltfläche **API aufrufen** aus. Die SPA sendet das Zugriffstoken in einer Anforderung an die geschützte Web-API, die den Anzeigenamen des angemeldeten Benutzers zurückgibt:

    ![Screenshot der SPA in einem Browserfenster mit dem JSON-Ergebnis mit dem Benutzernamen, das von der API zurückgegeben wird.](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>Bereitstellen der Anwendung 

In einer Produktionsanwendung handelt es sich bei dem Umleitungs-URI der App-Registrierung in der Regel um einen öffentlich zugänglichen Endpunkt, an dem Ihre App ausgeführt wird, z. B. `https://contoso.com/signin-oidc`. 

Sie können Umleitungs-URIs in Ihren registrierten Anwendungen jederzeit hinzufügen und ändern. Für Umleitungs-URIs gelten die folgenden Einschränkungen:

* Die Antwort-URL muss mit dem Schema `https` beginnen.
* Bei der Antwort-URL muss die Groß-/Kleinschreibung beachtet werden. Die Groß-/Kleinschreibung muss der Groß-/Kleinschreibung des URL-Pfads Ihrer ausgeführten Anwendung entsprechen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel erörterten Konzepten finden Sie hier:
* [Weitere Informationen zum Codebeispiel](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)
* [Aktivieren der Authentifizierung in Ihrer SPA](enable-authentication-spa-app.md)
* [Konfigurieren von Authentifizierungsoptionen in Ihrer SPA](enable-authentication-spa-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
