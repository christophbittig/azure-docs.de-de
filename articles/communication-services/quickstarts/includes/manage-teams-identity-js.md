---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 52549e92a066df3caea479b0a20b21c747cb5ed4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461151"
---
## <a name="set-up-prerequisites"></a>Einrichten erforderlicher Komponenten

- [Node.js](https://nodejs.org/), Active LTS- und Maintenance LTS-Versionen (8.11.1 und 10.14.1 empfohlen)

## <a name="set-up"></a>Einrichtung

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
```

Führen Sie `npm init -y` aus, um eine Datei `package.json` mit den Standardeinstellungen zu erstellen.

```console
npm init -y
```

### <a name="install-the-package"></a>Installieren des Pakets

Verwenden Sie den Befehl `npm install`, um das Azure Communication Services Identity SDK für JavaScript zu installieren.

```console

npm install @azure/communication-identity@beta --save
npm install @azure/msal-node --save
npm install express --save

```

Durch die Option `--save` wird die Bibliothek als Abhängigkeit in der Datei **package.json** aufgeführt.

## <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen einer neuen Textdatei im Code-Editor
1. Fügen Sie einen Aufruf vom Typ `require` hinzu, um den Kommunikationsidentitätsclient (`CommunicationIdentityClient`) zu laden.
1. Erstellen der Struktur für das Programm, einschließlich einer einfachen Ausnahmebehandlung

    ```javascript
    const { CommunicationIdentityClient } = require('@azure/communication-identity');
    const express = require("express");
    const msal = require('@azure/msal-node');
    
    const SERVER_PORT = process.env.PORT || 80;
    const REDIRECT_URI = "http://localhost"; 
    
    // Quickstart code goes here
    
    app.listen(SERVER_PORT, () => console.log(`Communication access token application started on ${SERVER_PORT}!`))
    
    ```

1. Speichern Sie die neue Datei als `issue-communication-access-token.js` im Verzeichnis `access-tokens-quickstart`.

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Schritt 1: Empfangen des Azure AD-Benutzertokens über die MSAL-Bibliothek

Der erste Schritt im Tokenaustauschflow besteht im Abrufen eines Tokens für Ihren Teams-Benutzer mithilfe von [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```javascript
const msalConfig = {
    auth: {
        clientId: "<contoso_application_id>",
        authority: "https://login.microsoftonline.com/<contoso_tenant_id>",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const provider = new msal.CryptoProvider();

const app = express();

app.get('/', async (req, res) => {
    const {verifier, challenge} = await provider.generatePkceCodes();
    const authCodeUrlParameters = {
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
        codeChallenge: challenge, 
        codeChallengeMethod: "S256"
    };

    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

app.get('/redirect', async (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("Response: ", response);
        //TODO: the following code snippets go here
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Schritt 2: Initialisieren von CommunicationIdentityClient

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string).

Fügen Sie der `then`-Methode folgenden Code hinzu:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Schritt 3: Austauschen des Azure AD-Benutzertokens gegen das Teams-Zugriffstoken

Verwenden Sie die `getTokenForTeamsUser`-Methode, um ein Zugriffstoken für den Teams-Benutzer auszustellen, das mit den Azure Communication Services SDKs verwendet werden kann.

```javascript
let accessToken = await identityClient.getTokenForTeamsUser(teamsToken);
console.log(`Token: ${accessToken}`);
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie an einer Konsoleneingabeaufforderung zum Verzeichnis mit der Datei *issue-communication-access-token.js*, und führen Sie anschließend den folgenden `node`-Befehl aus, um die App auszuführen.

```console
node ./issue-communication-access-token.js
```
