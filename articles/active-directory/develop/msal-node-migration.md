---
title: Migrieren einer Node.js-Anwendung von ADAL zu MSAL | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine vorhandene Node.js-Anwendung so aktualisieren, dass anstelle der Active Directory-Authentifizierungsbibliothek (ADAL) die Microsoft-Authentifizierungsbibliothek (MSAL) für die Authentifizierung und Autorisierung verwendet wird.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/26/2021
ms.author: marsma
ms.custom: has-adal-ref
ms.openlocfilehash: 33dd6efdbd03ce14beaf392e7bc24e051381903e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503013"
---
# <a name="how-to-migrate-a-nodejs-app-from-adal-to-msal"></a>Migrieren einer Node.js-Anwendung von ADAL zu MSAL

Die [Microsoft-Authentifizierungsbibliothek für Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) (Microsoft Authentication Library, MSAL Node) ist jetzt das empfohlene SDK zum Aktivieren der Authentifizierung und Autorisierung für Ihre in Microsoft Identity Platform registrierten Anwendungen. In diesem Artikel werden wichtige Schritte beschrieben, die Sie ausführen müssen, um Anwendungen von der Active Directory-Authentifizierungsbibliothek für Node (ADAL Node) zu MSAL Node zu migrieren.

## <a name="prerequisites"></a>Voraussetzungen

- Node, Version 10, 12 oder 14. Siehe [Hinweis zur Versionsunterstützung](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node#node-version-support).

## <a name="update-app-registration-settings"></a>Aktualisieren der App-Registrierungseinstellungen

Bei Verwendung von ADAL Node haben Sie wahrscheinlich den **Azure AD v1.0-Endpunkt** verwendet. Bei Anwendungen, die von ADAL zu MSAL migriert werden, sollte auch der Wechsel zum **Azure AD v2.0-Endpunkt** in Betracht gezogen werden.

1. Sehen Sie sich die [Unterschiede zwischen v1- und v2-Endpunkten](../azuread-dev/azure-ad-endpoint-comparison.md) an.
1. Passen Sie die vorhandenen App-Registrierungen bei Bedarf entsprechend an.

> [!NOTE]
> Zur Gewährleistung der Abwärtskompatibilität werden mit MSAL Node sowohl v1.0- als auch v2.0-Endpunkte unterstützt.

## <a name="install-and-import-msal"></a>Installieren und Importieren von MSAL

1. Installieren Sie das MSAL Node-Paket über NPM:

```console
npm install @azure/msal-node
```

1. Importieren Sie MSAL Node danach in den Code:

```javascript
const msal = require('@azure/msal-node');
```

1. Deinstallieren Sie schließlich das ADAL Node-Paket, und entfernen Sie alle Verweise im Code:

```console
npm uninstall adal-node
```

## <a name="initialize-msal"></a>MSAL initialisieren

In ADAL Node initialisieren Sie ein `AuthenticationContext`-Objekt, das dann die Methoden verfügbar macht, die Sie in verschiedenen Authentifizierungsflows verwenden können (z. B. `acquireTokenWithAuthorizationCode` für Web-Apps). Bei der Initialisierung ist der **Autoritäts-URI** der einzige obligatorische Parameter:

```javascript
var adal = require('adal-node');

var authorityURI = "https://login.microsoftonline.com/common";
var authenticationContex = new adal.AuthenticationContext(authorityURI);
```

In MSAL Node gibt es stattdessen zwei Alternativen: Wenn Sie eine mobile App oder eine Desktop-App erstellen, instanziieren Sie ein `PublicClientApplication`-Objekt. Der Konstruktor erwartet ein [Konfigurationsobjekt](#configure-msal), das mindestens den Parameter `clientId` enthält. In MSAL wird standardmäßig der Autoritäts-URI `https://login.microsoftonline.com/common` verwendet, wenn Sie ihn nicht angeben.

```javascript
const msal = require('@azure/msal-node');

const pca = new msal.PublicClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID"
        }
    });
```

> [!NOTE]
> Bei Verwendung dieser Autorität (`https://login.microsoftonline.com/common`) in v2.0 geben Sie Benutzern die Möglichkeit, sich mit einer beliebigen Azure AD-Organisation oder einem persönlichen Microsoft-Konto (MSA) anzumelden. Wenn Sie in MSAL Node Anmeldungen auf ein Azure AD-Konto beschränken möchten (dasselbe Verhalten wie in ADAL Node), verwenden Sie stattdessen `https://login.microsoftonline.com/organizations`.

Wenn Sie dagegen eine Web-App oder eine Daemon-App erstellen, instanziieren Sie ein `ConfidentialClientApplication`-Objekt. Bei diesen Apps müssen Sie auch *Clientanmeldeinformationen* angeben, z. B. einen geheimen Clientschlüssel oder ein Zertifikat:

```javascript
const msal = require('@azure/msal-node');

const cca = new msal.ConfidentialClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID",
            clientSecret = "YOUR_CLIENT_SECRET"
        }
    });
```

Im Unterschied zu `AuthenticationContext` in ADAL sind `PublicClientApplication` und `ConfidentialClientApplication` an eine Client-ID gebunden. Das bedeutet, dass Sie bei unterschiedlichen Client-IDs, die in Ihrer Anwendung verwendet werden sollen, für jede ID eine neue MSAL-Instanz instanziieren müssen. Weitere Informationen finden Sie unter [Initialization of MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) (Initialisierung von MSAL Node).

## <a name="configure-msal"></a>Konfigurieren von MSAL

Beim Erstellen von Apps in Microsoft Identity Platform enthalten die Apps zahlreiche Parameter, die mit der Authentifizierung zusammenhängen. In ADAL Node umfasst das `AuthenticationContext`-Objekt eine begrenzte Anzahl von Konfigurationsparametern, mit denen Sie es instanziieren können, während die verbleibenden Parameter frei im Code enthalten sind (z. B. *clientSecret*):

```javascript
var adal = require('adal-node');

var authority = "https://login.microsoftonline.com/YOUR_TENANT_ID"
var validateAuthority = true,
var cache = null;

var authenticationContext = new adal.AuthenticationContext(authority, validateAuthority, cache);
```

- `authority`: URL, die eine Tokenautorität angibt
- `validateAuthority`: Funktion, die verhindert, dass mit Ihrem Code Token von einer potenziell schädlichen Autorität angefordert werden
- `cache`: Tokencache, der von der AuthenticationContext-Instanz verwendet wird.  Wenn dieser Parameter nicht festgelegt ist, wird standardmäßig der In-Memory-Cache verwendet.

In MSAL Node wird dagegen ein Konfigurationsobjekt vom Typ [Configuration](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html#configuration) verwendet. Es enthält die folgenden Eigenschaften:

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "YOUR_CLIENT_ID",
        authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
        clientSecret: "YOUR_CLIENT_SECRET",
        knownAuthorities: [],
    },
    cache: {
        // your implementation of caching
    },
    system: {
        loggerOptions: { /** logging related options */ }
    }
}


const cca = new msal.ConfidentialClientApplication(msalConfig);
```

Ein wesentlicher Unterschied besteht darin, dass MSAL kein Flag zum Deaktivieren der Autoritätsüberprüfung bietet und die Autoritäten standardmäßig immer überprüft werden. In MSAL wird die angeforderte Autorität mit einer Liste von in Microsoft bekannten Autoritäten oder einer Liste von Autoritäten verglichen, die Sie in der Konfiguration angegeben haben. Weitere Informationen finden Sie unter [Configuration Options](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) (Konfigurationsoptionen).

## <a name="switch-to-msal-api"></a>Wechseln zur MSAL-API

Die meisten öffentlichen Methoden in ADAL Node verfügen über Entsprechungen in MSAL Node:

| ADAL                                | MSAL                              | Notizen                             |
|-------------------------------------|-----------------------------------|-----------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | Wurde umbenannt und erwartet nun ein [account](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo)-Objekt |
| `acquireTokenWithAuthorizationCode` | `acquireTokenByCode`              |                                   |
| `acquireTokenWithClientCredentials` | `acquireTokenByClientCredential` |                                   |
| `acquireTokenWithRefreshToken`      | `acquireTokenByRefreshToken`      | Nützlich für die Migration gültiger [Aktualisierungstoken](#remove-logic-around-refresh-tokens)              |
| `acquireTokenWithDeviceCode`        | `acquireTokenByDeviceCode`        | Abstrahiert nun die Erfassung des Benutzercodes (siehe unten) |
| `acquireTokenWithUsernamePassword`  | `acquireTokenByUsernamePassword`  |                                   |

Einige Methoden in ADAL Node sind jedoch veraltet. MSAL Node bietet hingegen neue Methoden:

| ADAL                              | MSAL                            | Notizen                             |
|-----------------------------------|---------------------------------|-----------------------------------|
| `acquireUserCode`                   | –                             | Wurde mit `acquireTokeByDeviceCode` zusammengeführt (siehe oben)|
| –                               | `acquireTokenOnBehalfOf`          | Eine neue Methode, die den [OBO-Fluss](./v2-oauth2-on-behalf-of-flow.md) abstrahiert |
| `acquireTokenWithClientCertificate` | –                             | Ist nicht mehr erforderlich, da Zertifikate jetzt während der Initialisierung zugewiesen werden (siehe [Konfigurationsoptionen](#configure-msal)) |
| –                               | `getAuthCodeUrl`                  | Eine neue Methode, die die Erstellung von URLs für [Autorisierungsendpunkte](./active-directory-v2-protocols.md#endpoints) abstrahiert |

## <a name="use-scopes-instead-of-resources"></a>Verwenden von Bereichen anstelle von Ressourcen

Ein wichtiger Unterschied zwischen v1.0- und v2.0-Endpunkten besteht im Zugriff auf die Ressourcen. In ADAL Node registrieren Sie zunächst eine Berechtigung im App-Registrierungsportal und fordern dann wie unten gezeigt ein Zugriffstoken für eine Ressource an (z. B. Microsoft Graph):

```javascript
authenticationContext.acquireTokenWithAuthorizationCode(
    req.query.code,
    redirectUri,
    resource, // e.g. 'https://graph.microsoft.com'
    clientId,
    clientSecret,
    function (err, response) {
        // do something with the authentication response
    }
);
```

MSAL Node unterstützt **v1.0**- sowie **v2.0.** -Endpunkte. Im v2.0-Endpunkt wird für den Zugriff auf Ressourcen ein *bereichsbezogenes* Modell verwendet. Wenn Sie also ein Zugriffstoken für eine Ressource anfordern, müssen Sie auch den Bereich für die Ressource angeben:

```javascript
const tokenRequest = {
    code: req.query.code,
    scopes: ["https://graph.microsoft.com/User.Read"],
    redirectUri: REDIRECT_URI,
};

pca.acquireTokenByCode(tokenRequest).then((response) => {
    // do something with the authentication response
}).catch((error) => {
    console.log(error);
});
```

Ein Vorteil des bereichsbezogenen Modells ist die Möglichkeit, *dynamische Bereiche* zu verwenden. Beim Erstellen von Anwendungen mit v1.0 mussten Sie den vollständigen Satz der in der Anwendung erforderlichen Berechtigungen (sogenannte *statische Bereiche*) registrieren, damit bei der Anmeldung die Benutzereinwilligung eingeholt werden konnte. In v2.0 können Sie die Berechtigungen über den scope-Parameter zum gewünschten Zeitpunkt anfordern (daher *dynamische Bereiche*). Dadurch kann der Benutzer Bereichen **inkrementelle Einwilligungen** erteilen. Wenn Sie also am Anfang nur möchten, dass sich der Benutzer bei Ihrer Anwendung anmeldet und Sie keinen Zugriff benötigen, können Sie dies tun. Wenn Sie später in der Lage sein müssen, den Kalender des Benutzers zu lesen, können Sie dann den Kalendergeltungsbereich in den Tokenabrufmethoden („acquireToken“) anfordern und die Einwilligung des Benutzers einholen. Weitere Informationen finden Sie unter [Resources and scopes](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md) (Ressourcen und Bereiche).

## <a name="use-promises-instead-of-callbacks"></a>Verwenden von Zusagen anstelle von Rückrufen

In ADAL Node werden Rückrufe für jeden Vorgang verwendet, nachdem die Authentifizierung erfolgreich war, und es wird eine Antwort abgerufen:

```javascript
var context = new AuthenticationContext(authorityUrl, validateAuthority);

context.acquireTokenWithClientCredentials(resource, clientId, clientSecret, function(err, response) {
    if (err) {
        console.log(err);
    } else {
        // do something with the authentication response
    }
});
```

In MSAL Node werden stattdessen Zusagen verwendet:

```javascript
    const cca = new msal.ConfidentialClientApplication(msalConfig);

    cca.acquireTokenByClientCredential(tokenRequest).then((response) => {
        // do something with the authentication response
    }).catch((error) => {
        console.log(error);
    });
```

Sie können außerdem die in ES8 enthaltene **async/await**-Syntax verwenden:

```javascript
    try {
        const authResponse = await cca.acquireTokenByCode(tokenRequest);
    } catch (error) {
        console.log(error);
    }
```

## <a name="enable-logging"></a>Aktivieren der Protokollierung

In ADAL Node konfigurieren Sie die Protokollierung separat an einer beliebigen Stelle im Code:

```javascript
var adal = require('adal-node');

//PII or OII logging disabled. Default Logger does not capture any PII or OII.
adal.logging.setLoggingOptions({
  log: function (level, message, error) {
    console.log(message);

    if (error) {
        console.log(error);
    }
  },
  level: logging.LOGGING_LEVEL.VERBOSE, // provide the logging level
  loggingWithPII: false  // Determine if you want to log personal identification information. The default value is false.
});
```

In MSAL Node ist die Protokollierung Teil der Konfigurationsoptionen und wird bei der Initialisierung der MSAL Node-Instanz erstellt:

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters
    },
    cache: {
        // cache related parameters
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
}

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

## <a name="enable-token-caching"></a>Aktivieren der Zwischenspeicherung von Token

In ADAL Node bestand die Möglichkeit, einen In-Memory-Tokencache zu importieren. Der Tokencache wird beim Initialisieren eines `AuthenticationContext`-Objekts als Parameter verwendet:

```javascript
var MemoryCache = require('adal-node/lib/memory-cache');

var cache = new MemoryCache();
var authorityURI = "https://login.microsoftonline.com/common";

var context = new AuthenticationContext(authorityURI, true, cache);
```

In MSAL Node ist der In-Memory-Tokencache die Standardeinstellung. Sie brauchen ihn nicht explizit zu importieren; der speicherinterne Token-Cache wird als Teil der Klassen `ConfidentialClientApplication` und `PublicClientApplication` bereitgestellt.

```javascript
const msalTokenCache = publicClientApplication.getTokenCache();
```

Wichtig: Ihr vorheriger Tokencache mit ADAL Node kann nicht auf MSAL Node übertragen werden, da Cacheschemas nicht kompatibel sind. Sie können jedoch in MSAL Node die gültigen Aktualisierungstoken verwenden, die Ihre App zuvor mit ADAL Node abgerufen hat. Weitere Informationen dazu im Abschnitt über [Aktualisierungstoken](#remove-logic-around-refresh-tokens).

Sie können den Cache zudem auf den Datenträger schreiben, indem Sie Ihr eigenes **Cache-Plug-In** angeben. Das Cache-Plug-In muss die [ICachePlugin](https://azuread.github.io/microsoft-authentication-library-for-js/ref/interfaces/_azure_msal_common.icacheplugin.html)-Schnittstelle implementieren. Wie die Protokollierung ist die Zwischenspeicherung Teil der Konfigurationsoptionen und wird bei der Initialisierung der MSAL Node-Instanz erstellt:

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters
    },
    cache: {
        cachePlugin // your implementation of cache plugin
    },
    system: {
        // logging related options
    }
}

const msalInstance = new ConfidentialClientApplication(msalConfig);
```

Ein Cache-Plug-In kann wie im folgenden Beispiel implementiert werden:

```javascript
const fs = require('fs');

// Call back APIs which automatically write and read into a .json file - example implementation
const beforeCacheAccess = async (cacheContext) => {
    cacheContext.tokenCache.deserialize(await fs.readFile(cachePath, "utf-8"));
};

const afterCacheAccess = async (cacheContext) => {
    if(cacheContext.cacheHasChanged) {
        await fs.writeFile(cachePath, cacheContext.tokenCache.serialize());
    }
};

// Cache Plugin
const cachePlugin = {
    beforeCacheAccess,
    afterCacheAccess
};
```

Wenn Sie [öffentliche Clientanwendungen](./msal-client-applications.md) wie Desktop-Apps entwickeln, bieten die [Microsoft-Authentifizierungserweiterungen für Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions) sichere Mechanismen für Clientanwendungen, um die plattformübergreifende Serialisierung und Persistenz für den Tokencache durchzuführen. Dabei werden die Plattformen Windows, Mac und Linux unterstützt.

> [!NOTE]
> Die [Microsoft-Authentifizierungserweiterungen für Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions) werden für Webanwendungen **nicht** empfohlen, da dies zu Skalierungs- und Leistungsproblemen führen kann. Stattdessen wird bei Web-Apps empfohlen, den Cache in der Sitzung zu speichern.

## <a name="remove-logic-around-refresh-tokens"></a>Entfernen von Logik für Aktualisierungstoken

In ADAL Node wurden Aktualisierungstoken verfügbar gemacht, sodass Sie Lösungen für die Tokenverwendung entwickeln konnten. Dazu wurden die Token zwischengespeichert und die `acquireTokenWithRefreshToken`-Methode verwendet. Typische Szenarien, in denen Aktualisierungstoken besonders relevant sind:

- Zeitintensive Dienste, über die verschiedene Aktionen (beispielsweise Aktualisieren von Dashboards) für Benutzer ausgeführt werden, wenn die Benutzer nicht mehr verbunden sind.
- WebFarm-Szenarien, die es dem Client ermöglichen, das Aktualisierungstoken im Webdienst zur Verfügung zu stellen (die Zwischenspeicherung erfolgt auf Client- und nicht auf Serverseite in einem verschlüsselten Cookie).

In MSAL Node wie auch in MSAL sind Aktualisierungstoken aus Sicherheitsgründen nicht zugänglich. Stattdessen übernimmt MSAL das Aktualisieren von Token für Sie. Daher muss dafür keine Logik mehr erstellt werden. Sie **können** jedoch Ihre zuvor erworbenen (und noch gültigen) Aktualisierungstoken aus dem Cache von ADAL Node verwenden, um einen neuen Satz von Token mit MSAL Node zu erhalten. Zu diesem Zweck bietet MSAL Node `acquireTokenByRefreshToken` an, was der `acquireTokenWithRefreshToken`-Methode von ADAL Node entspricht:

```javascript
var msal = require('@azure/msal-node');

const config = {
    auth: {
        clientId: "ENTER_CLIENT_ID",
        authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
        clientSecret: "ENTER_CLIENT_SECRET"
    }
};

const cca = new msal.ConfidentialClientApplication(config);

const refreshTokenRequest = {
    refreshToken: "", // your previous refresh token here
    scopes: ["user.read"],
};

cca.acquireTokenByRefreshToken(refreshTokenRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

> [!NOTE]
> Es wird empfohlen, den älteren ADAL-Knotentokencache zu zerstören, sobald Sie die noch gültigen Aktualisierungstoken verwenden, um mithilfe der `acquireTokenByRefreshToken`-Methode von MSAL Node wie oben gezeigt einen neuen Tokensatz abzurufen.

## <a name="handle-errors-and-exceptions"></a>Behandeln von Fehlern und Ausnahmen

Bei Verwendung des MSAL-Knotens tritt der Fehler `interaction_required` am häufigsten auf. Dieser Fehler kann häufig durch Initiierung einer interaktiven Tokenerfassungsaufforderung behoben werden. Wenn Beispielsweise bei Verwendung von `acquireTokenSilent` keine zwischengespeicherten Aktualisierungstoken vorhanden sind, kann MSAL Node kein Zugriffstoken im Hintergrund abrufen. Auf ähnliche Weise kann für die Web-API, auf die Sie zugreifen möchten, eine Richtlinie für [bedingten Zugriff](../conditional-access/overview.md) gelten, die erfordert, dass der Benutzer die [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md) (Multi-Factor Authentication, MFA) durchführen muss. In solchen Fällen kann der `interaction_required`-Fehler durch Auslösen von `acquireTokenByCode` behandelt werden, wodurch Benutzer zur MFA aufgefordert werden und diese so durchführen können.

Ein weiterer häufiger Fehler ist `consent_required`. Er tritt auf, wenn Berechtigungen, die zum Abrufen eines Zugriffstokens für eine geschützte Ressource erforderlich sind, vom Benutzer nicht bewilligt werden. Wie bei `interaction_required` ist die Lösung für den `consent_required`-Fehler häufig das Initiieren einer interaktiven Tokenbeschaffungsaufforderung mithilfe der `acquireTokenByCode`-Methode.

## <a name="run-the-app"></a>Ausführen der App

Nachdem Sie die gewünschten Änderungen vorgenommen haben, können Sie die App ausführen und das Authentifizierungsszenario testen:

```console
npm start
```

## <a name="example-acquiring-tokens-with-adal-node-vs-msal-node"></a>Beispiel: Schützen von Web-Apps mit ADAL Node und MSAL Node

Der folgende Codeausschnitt veranschaulicht eine vertrauliche Client-Web-App im Express.js-Framework. Eine Anmeldung wird ausgeführt, wenn ein Benutzer die Authentifizierungsroute `/auth` erreicht, über die Route `/redirect` ein Zugriffstoken für Microsoft Graph abruft und dann den Inhalt des Tokens anzeigt.


<table>
<tr><td> Verwenden von ADAL Node </td><td> Verwenden von MSAL Node </td></tr>
<tr>
<td>

```javascript
// Import dependencies
var express = require('express');
var crypto = require('crypto');
var adal = require('adal-node');

// Authentication parameters
var clientId = 'Enter_the_Application_Id_Here';
var clientSecret = 'Enter_the_Client_Secret_Here';
var tenant = 'common';
var authorityUrl = 'https://login.microsoftonline.com/' + tenant;
var redirectUri = 'http://localhost:3000/redirect';
var resource = 'https://graph.microsoft.com';

// Configure logging
adal.Logging.setLoggingOptions({
    log: function (level, message, error) {
        console.log(message);
    },
    level: adal.Logging.LOGGING_LEVEL.VERBOSE,
    loggingWithPII: false
});

// Auth code request URL template
var templateAuthzUrl = 'https://login.microsoftonline.com/'
    + tenant + '/oauth2/authorize?response_type=code&client_id='
    + clientId + '&redirect_uri=' + redirectUri
    + '&state=<state>&resource=' + resource;

// Initialize express
var app = express();

// State variable persists throughout the app lifetime
app.locals.state = "";

app.get('/auth', function(req, res) {

    // Create a random string to use against XSRF
    crypto.randomBytes(48, function(ex, buf) {
        app.locals.state = buf.toString('base64')
            .replace(/\//g, '_')
            .replace(/\+/g, '-');

        // Construct auth code request URL
        var authorizationUrl = templateAuthzUrl
            .replace('<state>', app.locals.state);

        res.redirect(authorizationUrl);
    });
});

app.get('/redirect', function(req, res) {
    // Compare state parameter against XSRF
    if (app.locals.state !== req.query.state) {
        res.send('error: state does not match');
    }

    // Initialize an AuthenticationContext object
    var authenticationContext =
        new adal.AuthenticationContext(authorityUrl);

    // Exchange auth code for tokens
    authenticationContext.acquireTokenWithAuthorizationCode(
        req.query.code,
        redirectUri,
        resource,
        clientId,
        clientSecret,
        function(err, response) {
            res.send(response);
        }
    );
});

app.listen(3000, function() {
    console.log(`listening on port 3000!`);
});
```

</td>
<td>

```javascript
// Import dependencies
const express = require("express");
const msal = require('@azure/msal-node');

// Authentication parameters
const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/common",
        clientSecret: "Enter_the_Client_Secret_Here"
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
};

const REDIRECT_URI = "http://localhost:3000/redirect";

// Initialize MSAL Node object using authentication parameters
const cca = new msal.ConfidentialClientApplication(config);

// Initialize express
const app = express();

app.get('/auth', (req, res) => {

    // Construct a request object for auth code
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Request auth code, then redirect
    cca.getAuthCodeUrl(authCodeUrlParameters)
        .then((response) => {
            res.redirect(response);
        }).catch((error) => res.send(error));
});

app.get('/redirect', (req, res) => {

    // Use the auth code in redirect request to construct
    // a token request object
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Exchange the auth code for tokens
    cca.acquireTokenByCode(tokenRequest)
        .then((response) => {
            res.send(response);
        }).catch((error) => res.status(500).send(error));
});

app.listen(3000, () =>
    console.log(`listening on port 3000!`));
```

</td>
</tr>
</table>

## <a name="next-steps"></a>Nächste Schritte

- [Referenz für die MSAL Node-API](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html)
- [Codebeispiele für MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples)
