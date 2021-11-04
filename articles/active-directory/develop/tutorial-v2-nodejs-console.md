---
title: 'Tutorial: Aufrufen von Microsoft Graph in einer Node.js-Konsolen-App | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine Konsolen-App zum Aufrufen von Microsoft Graph in einer Node.js-Konsolen-App.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: c46fbea002314c0b60046d942423815b3d8c1115
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507964"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Tutorial: Aufrufen der Microsoft Graph-API in einer Node.js-Konsolen-App

In diesem Tutorial erstellen Sie eine Konsolen-App, die die Microsoft Graph-API unter Verwendung ihrer eigenen Identität aufruft. Die von Ihnen erstellte Konsolen-App verwendet die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Führen Sie die Schritte in diesem Tutorial für folgende Aktionen aus:

> [!div class="checklist"]
> - Registrieren der Anwendung im Azure-Portal
> - Erstellen eines Node.js-Konsolen-App-Projekts
> - Hinzufügen von Authentifizierungslogik zur App
> - Hinzufügen von App-Registrierungsdetails
> - Hinzufügen einer Methode zum Anrufen einer Web-API
> - Testen der App

## <a name="prerequisites"></a>Voraussetzungen

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

## <a name="register-the-application"></a>Registrieren der Anwendung

Führen Sie zunächst die Schritte unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md) aus, um Ihre App zu registrieren.

Verwenden Sie die folgenden Einstellungen für Ihre App-Registrierung:

- Name: `NodeConsoleApp` (Vorschlag)
- Unterstützte Kontotypen: **Nur Konten in diesem Organisationsverzeichnis**
- API-Berechtigungen: **Microsoft-APIs** > **Microsoft Graph** > **Anwendungsberechtigungen** > `User.Read.All`
- Geheimer Clientschlüssel: `*********` (Notieren Sie diesen Wert für die Verwendung in einem späteren Schritt. Er wird nur einmal angezeigt.)

## <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie einen Ordner zum Hosten Ihrer Anwendung, z. B. *NodeConsoleApp*.

1. Wechseln Sie zunächst in Ihrem Terminal zu Ihrem Projektverzeichnis, und führen Sie dann die folgenden NPM-Befehle aus:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Erstellen Sie anschließend einen Ordner mit dem Namen *bin*. Erstellen Sie dann in diesem Ordner eine Datei mit dem Namen *index.js*, und fügen Sie den folgenden Code hinzu:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Diese Datei verweist auf zwei weitere Node-Module: Das Modul *auth.js* enthält eine Implementierung von MSAL Node zum Abrufen von Zugriffstoken, und *fetch.js* enthält eine Methode zum Senden einer HTTP-Anforderung an die Microsoft Graph-API mit einem Zugriffstoken. Nachdem Sie den Rest des Tutorials abgeschlossen haben, sollte die Datei- und Ordnerstruktur Ihres Projekts in etwa wie folgt aussehen:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Hinzufügen von Authentifizierungslogik

Erstellen Sie im Ordner *bin* eine weitere Datei mit dem Namen *auth.js*, und fügen Sie den folgenden Code zum Abrufen eines Zugriffstokens hinzu, das beim Aufrufen der Microsoft Graph-API angegeben werden soll.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '/.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + '/v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

Im obigen Codeausschnitt erstellen Sie zuerst ein Konfigurationsobjekt (*msalConfig*) und übergeben es, um ein MSAL-Element vom Typ [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) zu initialisieren. Anschließend erstellen Sie eine Methode zum Abrufen von Token über **Clientanmeldeinformationen** und machen dieses Modul schließlich verfügbar, damit darauf von *main.js* zugegriffen werden kann. Die Konfigurationsparameter in diesem Modul stammen aus einer Umgebungsdatei, die im nächsten Schritt erstellt wird.

## <a name="add-app-registration-details"></a>Hinzufügen von App-Registrierungsdetails

Erstellen Sie eine Umgebungsdatei zum Speichern der App-Registrierungsdetails, die beim Abrufen von Token verwendet werden. Erstellen Sie hierzu im Stammordner des Beispiels (*NodeConsoleApp*) eine Datei mit dem Namen *.env*, und fügen Sie den folgenden Code hinzu:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Füllen Sie diese Details mit den Werten aus, die Sie aus dem Azure-Portal für die App-Registrierung abrufen:

- `Enter_the_Tenant_Id_here` sollte einer der folgenden Werte sein:
  - Wenn Ihre Anwendung *Nur Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen**. Beispiel: `contoso.microsoft.com`.
  - Falls Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch `organizations`.
  - Unterstützt Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten*, ersetzen Sie diesen Wert durch `common`.
  - Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch `consumers`.
- `Enter_the_Application_Id_Here`: Die **Anwendungs-ID (Client)** der von Ihnen registrierten Anwendung.
- `Enter_the_Cloud_Instance_Id_Here`: Die Azure Cloud-Instanz, in der Ihre Anwendung registriert ist.
  - Geben Sie für die Azure-Hauptcloud (oder die *globale* Cloud) `https://login.microsoftonline.com` ein.
  - Geeignete Werte für **nationale** Clouds (z. B. für China) finden Sie unter [Nationale Clouds](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` ist die Instanz der Microsoft Graph-API, mit der die Anwendung kommunizieren soll.
  - Ersetzen Sie beide Vorkommen dieser Zeichenfolge für den **globalen** Microsoft Graph-API-Endpunkt durch `https://graph.microsoft.com`.
  - Informationen zu Endpunkten in **nationalen** Cloudbereitstellungen finden Sie in der Microsoft Graph Dokumentation unter [Bereitstellungen nationaler Clouds](/graph/deployments).

## <a name="add-a-method-to-call-a-web-api"></a>Hinzufügen einer Methode zum Anrufen einer Web-API

Erstellen Sie im Ordner *bin* eine weitere Datei mit dem Namen *fetch.js*, und fügen Sie den folgenden Code hinzu, der REST-Aufrufe der Microsoft Graph-API durchführt:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

In dieser Beispielanwendung dient die `callApi`-Methode zum Senden einer HTTP `GET`-Anforderung an eine geschützte Ressource, die ein Zugriffstoken erfordert. Der Inhalt wird anschließend an den Aufrufer zurückgegeben. Diese Methode fügt das abgerufene Token in den *HTTP-Autorisierungsheader* ein. Die geschützte Ressource ist in diesem Fall der [Endpunkt „users“](/graph/api/user-list) der Microsoft Graph-API, der die Benutzer im Mandanten anzeigt, in dem diese App registriert ist.

## <a name="test-the-app"></a>Testen der App

Sie haben die Erstellung der Anwendung abgeschlossen und sind nun bereit, die Funktionalität der App zu testen.

Starten Sie die Node.js-Konsolen-App, indem Sie den folgenden Befehl im Stammverzeichnis Ihres Projektordners ausführen:

```console
node . --op getUsers
```

Dadurch sollten Sie eine JSON-Antwort von der Microsoft Graph-API erhalten, und ein Array von Benutzerobjekten sollte in der Konsole angezeigt werden:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Befehlszeilenschnittstelle mit Graph-Antwort":::

## <a name="how-the-application-works"></a>Funktionsweise der Anwendung

Diese Anwendung verwendet die [Gewährung von OAuth 2.0-Clientanmeldeinformationen](./v2-oauth2-client-creds-grant-flow.md). Diese Art der Gewährung wird häufig für Interaktionen zwischen Servern verwendet, die ohne Benutzereingriff im Hintergrund ausgeführt werden müssen. Beim Flow zur Gewährung von Anmeldeinformationen kann ein Webdienst (ein vertraulicher Client) seine eigenen Anmeldeinformationen zum Authentifizieren verwenden, wenn ein anderer Webdienst aufgerufen wird, anstatt die Identität eines Benutzers anzunehmen. In der Regel werden für dieses Authentifizierungsmodell Anwendungen vom Typ **Daemon** oder **Dienstkonto** unterstützt.

Der anzufordernde Bereich für einen Anmeldeinformationsfluss für Clients ist der Name der Ressource, gefolgt von `/.default`. Durch diese Notation weiß Azure Active Directory (Azure AD), dass die Berechtigungen auf Anwendungsebene verwendet werden sollen, die im Zuge der Anwendungsregistrierung statisch deklariert wurden. Außerdem müssen diese API-Berechtigungen von einem **Mandantenadministrator** gewährt werden.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich ausführlicher mit der Entwicklung von Node.js-Konsolenanwendungen auf Microsoft Identity Platform beschäftigen möchten, können Sie sich die mehrteilige Szenarioreihe ansehen:

> [!div class="nextstepaction"]
> [Szenario: Daemonanwendung](scenario-daemon-overview.md)
