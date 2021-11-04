---
title: Migrieren Ihrer JavaScript-Anwendung von ADAL.js zu MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine vorhandene JavaScript-Anwendung so aktualisieren, dass anstelle der Active Directory-Authentifizierungsbibliothek (ADAL) die Microsoft-Authentifizierungsbibliothek (MSAL) für die Authentifizierung und Autorisierung verwendet wird.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/06/2021
ms.author: marsma
ms.custom: has-adal-ref
ms.openlocfilehash: 6614c1d06dd1e5093dfbe7e3ea3a20ff13ec74c1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501037"
---
# <a name="how-to-migrate-a-javascript-app-from-adaljs-to-msaljs"></a>Gewusst wie: Migrieren einer JavaScript-App von ADAL.js zu MSAL.js

[Microsoft-Authentifizierungsbibliothek für JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) (MSAL.js, auch als *msal-browser* bezeichnet) 2.x ist die Authentifizierungsbibliothek, die mit JavaScript-Anwendungen auf der Microsoft Identity Platform verwendet werden sollte. In diesem Artikel werden die Änderungen hervorgehoben, die Sie vornehmen müssen, um eine App zu migrieren, die ADAL.js zur Verwendung von MSAL.js 2.x einsetzt.

> [!NOTE]
> Sie sollten unbedingt MSAL.js 2.x statt MSAL.js 1.x verwenden. Der Ablauf der Gewährung von Authentifizierungscode ist sicherer und ermöglicht Single-Page-Anwendungen neben anderen Vorteilen, trotz der Datenschutzmaßnahmen, die in Browsern wie Safari implementiert sind, um Cookies von Drittanbietern zu blockieren, ein hohes Maß an Benutzerfreundlichkeit zu bieten.

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen für **Plattform** / **Antwort-URL-Typ** im App-Registrierungsportal **Single-Page-Anwendung** einstellen (wenn Sie andere Plattformen wie **Web** in Ihrer App-Registrierung hinzugefügt haben, müssen Sie sicherstellen, dass sich die Umleitungs-URIs nicht überlappen. Siehe [Einschränkungen bei Umleitungs-URIs](./reply-url.md).
- Sie müssen [polyfills](./msal-js-use-ie-browser.md) für ES6-Funktionen bereitstellen, von denen MSAL.js abhängig ist (z. B. Zusagen), damit Ihre Apps im **Internet Explorer** ausgeführt werden können.
- Stellen Sie sicher, dass Sie Ihre Azure AD-Apps zum [v2-Endpunkt](../azuread-dev/azure-ad-endpoint-comparison.md) migriert haben, falls dies noch nicht geschehen ist.

## <a name="install-and-import-msal"></a>Installieren und Importieren von MSAL

Es gibt zwei Möglichkeiten, die MSAL.js-2.x-Bibliothek zu installieren:

### <a name="via-npm"></a>Über NPM:

```console
npm install @azure/msal-browser
```

Importieren Sie sie dann je nach Modulsystem wie unten gezeigt:

```javascript
import * as msal from "@azure/msal-browser"; // ESM

const msal = require('@azure/msal-browser'); // CommonJS
```

### <a name="via-cdn"></a>Über CDN:

Laden Sie das Skript im Headerabschnitt Ihres HTML-Dokuments:

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js"></script>
  </head>
</html>
```

Alternative CDN-Links und bewährte Methoden bei der Verwendung von CDN finden Sie unter [CDN Usage](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md) (CDN-Nutzung).

## <a name="initialize-msal"></a>MSAL initialisieren

In ADAL.js instanziieren Sie die Klasse [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext), die dann die Methoden bereitstellt, die Sie für die Authentifizierung verwenden können (`login`, `acquireTokenPopup` usw.). Dieses Objekt dient als Darstellung der Verbindung Ihrer Anwendung mit dem Autorisierungsserver oder Identitätsanbieter. Bei der Initialisierung ist die **clientId** der einzige obligatorische Parameter:

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);
```

In MSAL.js instanziieren Sie stattdessen die Klasse [PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html). Wie ADAL.js erwartet der Konstruktor ein [Konfigurationsobjekt](#configure-msal), das mindestens den Parameter `clientId` enthält. Weitere Informationen finden Sie unter [Initialize MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md).

```javascript
const msalConfig = {
  auth: {
      clientId: 'YOUR_CLIENT_ID'
  }
};

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

Sowohl in ADAL.js als auch MSAL.js wird der Autoritäts-URI standardmäßig auf `https://login.microsoftonline.com/common` festgelegt, wenn Sie ihn nicht angeben.

> [!NOTE]
> Bei Verwendung dieser Autorität (`https://login.microsoftonline.com/common`) in v2.0 geben Sie Benutzern die Möglichkeit, sich mit einer beliebigen Azure AD-Organisation oder einem persönlichen Microsoft-Konto (MSA) anzumelden. Wenn Sie in MSAL.js Anmeldungen auf ein Azure AD-Konto beschränken möchten (dasselbe Verhalten wie bei ADAL.js), verwenden Sie stattdessen `https://login.microsoftonline.com/organizations`.

## <a name="configure-msal"></a>Konfigurieren von MSAL

Einige der [Konfigurationsoptionen in ADAL.js](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context), die bei der Initialisierung von [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) verwendet werden, sind in MSAL.js veraltet, während einige neue Optionen eingeführt wurden. Weitere Informationen finden Sie in der [vollständigen Liste der verfügbaren Optionen](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md). Wichtig ist, dass viele dieser Optionen mit Ausnahme von `clientId` während des Tokenerwerbs außer Kraft gesetzt werden können, so dass Sie sie für *jede Anforderung individuell* festlegen können. Sie können zum Beispiel einen anderen **Autoritäts-URI** oder **Umleitungs-URI** verwenden als den, den Sie während der Initialisierung beim Erwerb von Token festgelegt haben.

Darüber hinaus müssen Sie die Anmeldebenutzeroberfläche (d. h., ob Popupfenster verwendet werden oder die Seite umgeleitet wird) nicht mehr über die Konfigurationsoptionen festlegen. Stattdessen stellt `MSAL.js` `loginPopup`- und `loginRedirect`-Methoden über die `PublicClientApplication`-Instanz zur Verfügung.

## <a name="enable-logging"></a>Aktivieren der Protokollierung

In ADAL.js konfigurieren Sie die Protokollierung separat an einer beliebigen Stelle im Code:

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);

var Logging = {
  level: 3,
  log: function (message) {
      console.log(message);
  },
  piiLoggingEnabled: false
};


authContext.log(Logging)
```

In MSAL.js ist die Protokollierung Teil der Konfigurationsoptionen und wird während der Initialisierung von `PublicClientApplication` erstellt:

```javascript
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

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

## <a name="switch-to-msal-api"></a>Wechseln zur MSAL-API

Die meisten öffentlichen Methoden in ADAL.js verfügen über Entsprechungen in MSAL.js:

| ADAL                                | MSAL                              | Notizen                                        |
|-------------------------------------|-----------------------------------|----------------------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | Wurde umbenannt und erwartet nun ein [account](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo)-Objekt |
| `acquireTokenPopup`                 | `acquireTokenPopup`               | Jetzt asynchron, gibt außerdem eine Zusage zurück.              |
| `acquireTokenRedirect`              | `acquireTokenRedirect`            | Jetzt asynchron, gibt außerdem eine Zusage zurück.              |
| `handleWindowCallback`              | `handleRedirectPromise`           | Erforderlich bei Verwendung der Umleitung          |
| `getCachedUser`                     | `getAllAccounts`                  | Umbenannt und gibt jetzt ein Array von Konten zurück.|

Andere sind veraltet, während MSAL.js neue Methoden bietet:

| ADAL                              | MSAL                            | Notizen                                            |
|-----------------------------------|---------------------------------|--------------------------------------------------|
| `login`                           | –                             | Veraltet. `loginPopup` oder `loginRedirect` verwenden  |
| `logOut`                          | –                             | Veraltet. `logoutPopup` oder `logoutRedirect` verwenden|
| –                               | `loginPopup`                    |                                                  |
| Nicht zutreffend                               | `loginRedirect`                 |                                                  |
| Nicht zutreffend                               | `logoutPopup`                   |                                                  |
| Nicht zutreffend                               | `logoutRedirect`                |                                                  |
| –                               | `getAccountByHomeId`            | Filtert Konten nach Home-ID (oid + Mandanten-ID)    |
| –                               | `getAccountLocalId`             | Filtert Konten nach lokaler ID (nützlich für ADFS)   |
| –                               | `getAccountUsername`            | Filtert Konten nach Benutzername (falls vorhanden)         |

Da MSAL.js im Gegensatz zu ADAL.js in TypeScript implementiert wird, werden außerdem verschiedene Typen und Schnittstellen verfügbar gemacht, die Sie in Ihren Projekten verwenden können. Ausführlichere Informationen finden Sie in der [MSAL.js-API-Referenz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/).

## <a name="use-scopes-instead-of-resources"></a>Verwenden von Bereichen anstelle von Ressourcen

Ein wichtiger Unterschied zwischen Azure AD **v1.0**- und **v2.0**-Endpunkten besteht im Zugriff auf die Ressourcen. Bei Verwendung von ADAL.js mit dem **v1.0**-Endpunkt registrieren Sie zunächst eine Berechtigung im App-Registrierungsportal und fordern dann wie unten gezeigt ein Zugriffstoken für eine Ressource an (z. B. Microsoft Graph):

```javascript
authContext.acquireTokenRedirect("https://graph.microsoft.com", function (error, token) {
  // do something with the access token
});
```

MSAL.js unterstützt **v1.0**- sowie **v2.0.** -Endpunkte. Im **v2.0**-Endpunkt wird für den Zugriff auf Ressourcen ein *bereichsbezogenes* Modell verwendet. Wenn Sie also ein Zugriffstoken für eine Ressource anfordern, müssen Sie auch den Bereich für die Ressource angeben:

```javascript
msalInstance.acquireTokenRedirect({
  scopes: ["https://graph.microsoft.com/User.Read"]
});
```

Ein Vorteil des bereichsbezogenen Modells ist die Möglichkeit, *dynamische Bereiche* zu verwenden. Beim Erstellen von Anwendungen mit dem v1.0-Endpunkt mussten Sie den vollständigen Satz der in der Anwendung erforderlichen Berechtigungen (sogenannte *statische Bereiche*) registrieren, damit bei der Anmeldung die Benutzereinwilligung eingeholt werden konnte. In v2.0 können Sie die Berechtigungen über den scope-Parameter zum gewünschten Zeitpunkt anfordern (daher *dynamische Bereiche*). Dadurch kann der Benutzer Bereichen **inkrementelle Einwilligungen** erteilen. Wenn Sie also am Anfang nur möchten, dass sich der Benutzer bei Ihrer Anwendung anmeldet und Sie keinen Zugriff benötigen, können Sie dies tun. Wenn Sie später in der Lage sein müssen, den Kalender des Benutzers zu lesen, können Sie dann den Kalendergeltungsbereich in den Tokenabrufmethoden („acquireToken“) anfordern und die Einwilligung des Benutzers einholen. Weitere Informationen finden Sie unter [Resources and scopes](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md) (Ressourcen und Bereiche).

## <a name="use-promises-instead-of-callbacks"></a>Verwenden von Zusagen anstelle von Rückrufen

In ADAL.js werden Rückrufe für jeden Vorgang verwendet, nachdem die Authentifizierung erfolgreich war, und es wird eine Antwort abgerufen:

```javascript
authContext.acquireTokenPopup(resource, extraQueryParameter, claims, function (error, token) {
  // do something with the access token
});
```

In MSAL.js werden stattdessen Zusagen verwendet:

```javascript
msalInstance.acquireTokenPopup({
      scopes: ["User.Read"] // shorthand for https://graph.microsoft.com/User.Read
  }).then((response) => {
      // do something with the auth response
  }).catch((error) => {
      // handle errors
  });
```

Sie können außerdem die in ES8 enthaltene **async/await**-Syntax verwenden:

```javascript
const getAccessToken = async() => {
  try {
      const authResponse = await msalInstance.acquireTokenPopup({
          scopes: ["User.Read"]
      });
  } catch (error) {
      // handle errors
  }
}
```

## <a name="cache-and-retrieve-tokens"></a>Zwischenspeichern und Abrufen von Token

Wie ADAL.js speichert MSAL.js Token und andere Authentifizierungsartefakte mithilfe der [Web-Storage-API](https://developer.mozilla.org/docs/Web/API/Web_Storage_API) im Browserspeicher zwischen. Sie sollten die `sessionStorage`-Option (siehe: [Konfiguration](#configure-msal)) verwenden, da sie sicherer ist, was die Speicherung von Token angeht, die von Ihren Benutzern erworben werden, aber `localStorage` ermöglicht Ihnen Registerkarten und Benutzersitzungen übergreifend [Einmaliges Anmelden](./msal-js-sso.md).

Wichtig: Sie sollten nicht direkt auf den Cache zugreifen. Stattdessen sollten Sie eine geeignete MSAL.js-API zum Abrufen von Authentifizierungsartefakten wie Zugriffstoken oder Benutzerkonten verwenden.

## <a name="renew-tokens-with-refresh-tokens"></a>Erneuern von Token mit Aktualisierungstoken

ADAL.js verwendet den [impliziten OAuth 2.0-Fluss](./v2-oauth2-implicit-grant-flow.md), der aus Sicherheitsgründen keine Aktualisierungstoken zurückgibt (Aktualisierungstoken haben eine längere Lebensdauer als Zugriffstoken und sind daher in den Händen böswilliger Akteure gefährlicher). Daher führt ADAL.js die Tokenerneuerung mithilfe eines ausgeblendeten iFrames aus, sodass der Benutzer nicht wiederholt zur Authentifizierung aufgefordert wird.

Mit dem Authentifizierungscodeflow mit PKCE-Unterstützung erhalten Apps, die MSAL.js 2.x verwenden, Aktualisierungstoken zusammen mit ID- und Zugriffstoken, die zum Erneuern verwendet werden können. Die Verwendung von Aktualisierungstoken wird abstrahiert, und die Entwickler sollten keine Logik um sie herum erstellen. Stattdessen verwaltet MSAL die Tokenerneuerung mithilfe von Aktualisierungstoken selbst. Ihr vorheriger Tokencache mit ADAL.js kann nicht auf MSAL.js übertragen werden, da sich das Tokencacheschema geändert hat und nicht mit dem in ADAL.js verwendeten Schema kompatibel ist.

## <a name="handle-errors-and-exceptions"></a>Behandeln von Fehlern und Ausnahmen

Bei Verwendung von MSAL.js tritt der Fehler `interaction_in_progress` am häufigsten auf. Dieser Fehler wird ausgelöst, wenn eine interaktive API (`loginPopup`, `loginRedirect`, `acquireTokenPopup`, `acquireTokenRedirect`) aufgerufen wird, während noch eine andere interaktive API ausgeführt wird. Die `login*`- und `acquireToken*`-API sind *asynchron*, so dass Sie sicherstellen müssen, dass die resultierenden Zusagen aufgelöst wurden, bevor Sie eine weitere aufrufen.

Ein weiterer häufiger Fehler ist `interaction_required`. Dieser Fehler kann häufig durch Initiierung einer interaktiven Tokenerfassungsaufforderung behoben werden. Beispielsweise kann für die Web-API, auf die Sie zugreifen möchten, eine Richtlinie für [bedingten Zugriff](../conditional-access/overview.md) gelten, die erfordert, dass der Benutzer die [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md) (Multi-Factor Authentication, MFA) durchführen muss. In diesem Fall fordert die Behandlung des `interaction_required`-Fehlers durch Auslösen von `acquireTokenPopup` oder `acquireTokenRedirect` den Benutzer zur MFA auf und ermöglicht ihm die Durchführung.

Ein weiterer häufiger Fehler ist `consent_required`. Er tritt auf, wenn Berechtigungen, die zum Abrufen eines Zugriffstokens für eine geschützte Ressource erforderlich sind, vom Benutzer nicht bewilligt werden. Wie bei `interaction_required` ist die Lösung für den `consent_required`-Fehler häufig das Initiieren einer interaktiven Tokenbeschaffungsaufforderung mithilfe der `acquireTokenPopup`- oder `acquireTokenRedirect`-Methode.

Weitere Informationen zu häufigen MSAL.js-Fehlern und ihrer Behandlung finden Sie [hier](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/errors.md).

## <a name="use-the-events-api"></a>Verwenden der Ereignis-API

MSAL.js (>=v2.4) führt eine Ereignis-API ein, die Sie in Ihren Apps verwenden können. Diese Ereignisse beziehen sich auf den Authentifizierungsprozess und die Aktivitäten von MSAL zu einem beliebigen Zeitpunkt und können verwendet werden, um die Benutzeroberfläche zu aktualisieren, Fehlermeldungen anzuzeigen, zu überprüfen, ob eine Interaktion ausgeführt wird, usw. Im Folgenden finden Sie beispielsweise einen Ereignisrückruf, der aufgerufen wird, wenn beim Anmeldevorgang aus beliebigem Grund ein Fehler auftritt:

```javascript
const callbackId = msalInstance.addEventCallback((message) => {
  // Update UI or interact with EventMessage here
  if (message.eventType === EventType.LOGIN_FAILURE) {
      if (message.error instanceof AuthError) {
          // Do something with the error
      }
    }
});
```

Der Leistung wegen ist es wichtig, die Registrierung von Ereignisrückrufen aufzuheben, wenn sie nicht mehr benötigt werden. Weitere Informationen finden Sie unter [Events](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/events.md) (Ereignisse).

## <a name="handle-multiple-accounts"></a>Behandeln mehrerer Konten

Im Konzept von ADAL.js stellt ein *Benutzer* die aktuell authentifizierte Entität dar. MSAL.js ersetzt *Benutzer* durch *Konten*, da einem Benutzer mehrere Konten zugeordnet werden können. Dies bedeutet auch, dass Sie jetzt mehrere Konten steuern und das am besten geeignete Konto auswählen müssen, um damit zu arbeiten. Der folgende Codeausschnitt veranschaulicht diesen Prozess:

```javascript
let homeAccountId = null; // Initialize global accountId (can also be localAccountId or username) used for account lookup later, ideally stored in app state

// This callback is passed into `acquireTokenPopup` and `acquireTokenRedirect` to handle the interactive auth response
function handleResponse(resp) {
  if (resp !== null) {
      homeAccountId = resp.account.homeAccountId; // alternatively: resp.account.homeAccountId or resp.account.username
  } else {
      const currentAccounts = myMSALObj.getAllAccounts();
      if (currentAccounts.length < 1) { // No cached accounts
          return;
      } else if (currentAccounts.length > 1) { // Multiple account scenario
          // Add account selection logic here
      } else if (currentAccounts.length === 1) {
          homeAccountId = currentAccounts[0].homeAccountId; // Single account scenario
      }
  }
}
```

Weitere Informationen finden Sie unter [Accounts in MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/accounts.md) (Konten in MSAL.js).

## <a name="use-the-wrappers-libraries"></a>Verwenden der Wrapperbibliotheken

Wenn Sie für Angular- und React-Frameworks entwickeln, können Sie [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) bzw. [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react) verwenden. Diese Wrapper machen die gleiche öffentliche API verfügbar wie MSAL.js und bieten frameworkspezifische Methoden und Komponenten, die die Authentifizierungs- und Tokenerfassungsprozesse optimieren können.

## <a name="run-the-app"></a>Ausführen der App

Nachdem Sie die gewünschten Änderungen vorgenommen haben, können Sie die App ausführen und das Authentifizierungsszenario testen:

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>Beispiel: Schützen von Web-Apps mit ADAL Node und MSAL Node

Die folgenden Codeausschnitte zeigen den minimal erforderlichen Code für eine Single-Page-Anwendung, die Benutzer mit der Microsoft Identity Platform authentifiziert und ein Zugriffstoken für Microsoft Graph abruft, indem zuerst ADAL.js und dann MSAL.js verwendet wird:

<table>
<tr><td> Verwenden von ADAL.js </td><td> Verwenden von MSAL.js </td></tr>
<tr>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script
    type="text/javascript"
    src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.18/js/adal.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>

    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    var authContext = new AuthenticationContext({
        instance: 'https://login.microsoftonline.com/',
        clientId: "ENTER_CLIENT_ID",
        tenant: "ENTER_TENANT_ID",
        cacheLocation: "sessionStorage",
        redirectUri: "http://localhost:3000",
        popUp: true,
        callback: function (errorDesc, token, error, tokenType) {
            console.log('Hello ' + authContext.getCachedUser().profile.upn)

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        }
    });

    authContext.log({
        level: 3,
        log: function (message) {
            console.log(message);
        },
        piiLoggingEnabled: false
    });

    loginButton.addEventListener('click', function () {
        authContext.login();
    });

    logoutButton.addEventListener('click', function () {
        authContext.logOut();
    });

    tokenButton.addEventListener('click', () => {
        authContext.acquireTokenPopup(
            "https://graph.microsoft.com",
            null, null,
            function (error, token) {
                console.log(error, token);
            }
        )
    });
  </script>
</body>

</html>

```

</td>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script
    type="text/javascript"
    src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>
    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    const pca = new msal.PublicClientApplication({
        auth: {
            clientId: "ENTER_CLIENT_ID",
            authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
            redirectUri: "http://localhost:3000",
        },
        cache: {
            cacheLocation: "sessionStorage"
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
    });

    loginButton.addEventListener('click', () => {
        pca.loginPopup().then((response) => {
            console.log(`Hello ${response.account.username}!`);

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        })
    });

    logoutButton.addEventListener('click', () => {
        pca.logoutPopup().then((response) => {
            window.location.reload();
        })
    });

    tokenButton.addEventListener('click', () => {
        pca.acquireTokenPopup({
            scopes: ["User.Read"]
        }).then((response) => {
            console.log(response);
        })
    });
  </script>
</body>

</html>

```

</td>
</tr>
</table>

## <a name="next-steps"></a>Nächste Schritte

- [MSAL.js-API-Referenz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)
- [Node.js-Codebeispiele](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples)
