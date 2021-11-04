---
title: 'Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API in einer Electron-Desktop-App | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine Electron-Desktop-App, mit der sich Benutzer anmelden können, und verwenden den Autorisierungscodeflow, um ein Zugriffstoken von Microsoft Identity Platform zu erhalten und die Microsoft Graph-API aufzurufen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: 083c31b427c0f3d8cc261af7c9033d4ec1ffbeae
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500467"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API in einer Electron-Desktop-App

In diesem Tutorial wird eine Electron-Desktop-Anwendung erstellt, die Benutzer anmeldet und Microsoft Graph über den Autorisierungscodeflow mit PKCE aufruft. Die von Ihnen erstellte Desktop-App verwendet die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Führen Sie die Schritte in diesem Tutorial für folgende Aktionen aus:

> [!div class="checklist"]
> - Registrieren der Anwendung im Azure-Portal
> - Erstellen eines Projekts für eine Electron-Desktop-App
> - Hinzufügen von Authentifizierungslogik zur App
> - Hinzufügen einer Methode zum Anrufen einer Web-API
> - Hinzufügen von App-Registrierungsdetails
> - Testen der App

## <a name="prerequisites"></a>Voraussetzungen

- [Node.js](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

## <a name="register-the-application"></a>Registrieren der Anwendung

Führen Sie zunächst die Schritte unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md) aus, um Ihre App zu registrieren.

Verwenden Sie die folgenden Einstellungen für Ihre App-Registrierung:

- Name: `ElectronDesktopApp` (Vorschlag)
- Unterstützte Kontotypen: **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig) und persönliche Microsoft-Konten (z. B. Skype, Xbox)**
- Plattformtyp: **Mobile Anwendungen und Desktopanwendungen**
- Umleitungs-URI: `msal://redirect`

## <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie einen Ordner zum Hosten Ihrer Anwendung, z. B. *ElectronDesktopApp*.

1. Wechseln Sie zunächst in Ihrem Terminal zu Ihrem Projektverzeichnis, und führen Sie dann die folgenden `npm`-Befehle aus:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Erstellen Sie anschließend einen Ordner mit dem Namen *App*. Erstellen Sie in diesem Ordner eine Datei mit dem Namen *index.html*, die als Benutzeroberfläche dient. Fügen Sie dort den folgenden Code hinzu:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Erstellen Sie als Nächstes eine Datei namens *main.js*, und fügen Sie den folgenden Code hinzu:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

Im obigen Codeausschnitt initialisieren Sie ein Electron-Hauptfensterobjekt und erstellen einige Ereignishandler für Interaktionen mit dem Electron-Fenster. Außerdem importieren Sie Konfigurationsparameter, instanziieren die Klasse *authProvider* für die Behandlung von Anmeldung, Abmeldung und Tokenabruf und rufen die Microsoft Graph-API auf.

4. Im gleichen Ordner (*App*) erstellen Sie eine weitere Datei mit dem Namen *renderer.js* und fügen den folgenden Code hinzu:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Erstellen Sie abschließend eine Datei mit dem Namen *constants.js*, in der die Zeichenfolgenkonstanten zum Beschreiben der **Anwendungsereignisse** gespeichert werden:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Sie verfügen jetzt über eine einfache GUI und Interaktionen für Ihre Electron-App. Nachdem Sie den Rest des Tutorials abgeschlossen haben, sollte die Datei- und Ordnerstruktur Ihres Projekts in etwa wie folgt aussehen:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Hinzufügen von Authentifizierungslogik zur App

Erstellen Sie im Ordner *App* eine Datei namens *AuthProvider.js*. Diese enthält eine Authentifizierungsanbieterklasse, die Anmeldung, Abmeldung, Tokenabruf, Kontoauswahl und zugehörige Authentifizierungsaufgaben mithilfe von MSAL Node verarbeitet. Fügen Sie dort den folgenden Code hinzu:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

        authWindow.loadURL(navigateUrl);

        return new Promise((resolve, reject) => {
            authWindow.webContents.on('will-redirect', (event, responseUrl) => {
                try {
                    const parsedUrl = new URL(responseUrl);
                    const authCode = parsedUrl.searchParams.get('code');
                    resolve(authCode);
                } catch (err) {
                    reject(err);
                }
            });
        });
    }

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

Im obigen Codeausschnitt haben Sie durch Übergabe eines Konfigurationsobjekts (`msalConfig`) zuerst das MSAL Node-Element `PublicClientApplication` initialisiert. Anschließend haben Sie die Methoden `login`, `logout` und `getToken` verfügbar gemacht, die vom Hauptmodul (*main.js*) aufgerufen werden sollen. Rufen Sie in `login` und `getToken` jeweils ID- und Zugriffstoken ab. Fordern Sie dazu zuerst einen Autorisierungscode an, und tauschen Sie diesen dann über die öffentliche MSAL Node-API `acquireTokenByCode` mit einem Token aus.

## <a name="add-a-method-to-call-a-web-api"></a>Hinzufügen einer Methode zum Anrufen einer Web-API

Erstellen Sie eine weitere Datei mit dem Namen *fetch.js*. Diese Datei enthält einen Axios-HTTP-Client zum Ausführen von REST-Aufrufen der Microsoft Graph-API.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Hinzufügen von App-Registrierungsdetails

Erstellen Sie abschließend eine Umgebungsdatei zum Speichern der App-Registrierungsdetails, die beim Abrufen von Token verwendet werden. Erstellen Sie hierzu im Stammordner des Beispiels (*ElectronDesktopApp*) eine Datei mit dem Namen *.env*, und fügen Sie den folgenden Code hinzu:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Füllen Sie diese Details mit den Werten aus, die Sie aus dem Azure-Portal für die App-Registrierung abrufen:

- `Enter_the_Tenant_Id_here` sollte einer der folgenden Werte sein:
  - Wenn Ihre Anwendung *Nur Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen**. Beispiel: `contoso.microsoft.com`.
  - Falls Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch `organizations`.
  - Unterstützt Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten*, ersetzen Sie diesen Wert durch `common`.
  - Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch `consumers`.
- `Enter_the_Application_Id_Here`: Die **Anwendungs-ID (Client)** der von Ihnen registrierten Anwendung.
- `Enter_the_Cloud_Instance_Id_Here`: Die Azure Cloud-Instanz, in der Ihre Anwendung registriert ist.
  - Geben Sie für die Azure-Hauptcloud (oder die *globale* Cloud) `https://login.microsoftonline.com/` ein.
  - Geeignete Werte für **nationale** Clouds (z. B. für China) finden Sie unter [Nationale Clouds](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` ist die Instanz der Microsoft Graph-API, mit der die Anwendung kommunizieren soll.
  - Ersetzen Sie beide Vorkommen dieser Zeichenfolge für den **globalen** Microsoft Graph-API-Endpunkt durch `https://graph.microsoft.com/`.
  - Informationen zu Endpunkten in **nationalen** Cloudbereitstellungen finden Sie in der Microsoft Graph Dokumentation unter [Bereitstellungen nationaler Clouds](/graph/deployments).

## <a name="test-the-app"></a>Testen der App

Sie haben die Erstellung der Anwendung abgeschlossen und sind nun bereit, die Electron-Desktop-App zu starten und die Funktionalität der App zu testen.

1. Starten Sie die App, indem Sie den folgenden Befehl im Stammverzeichnis Ihres Projektordners ausführen:

```console
electron App/main.js
```

2. Der Inhalt der Datei *index.html* und die Schaltfläche **Anmelden** sollten im Hauptfenster der Anwendung angezeigt werden.

## <a name="test-sign-in-and-sign-out"></a>Testen der An- und Abmeldung

Wählen Sie nach dem Laden der Datei *index.html* die Option **Anmelden** aus. Sie werden aufgefordert, sich mit Microsoft Identity Platform anzumelden:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="Aufforderung zum Anmelden":::

Wenn Sie den angeforderten Berechtigungen zustimmen, zeigt die Webanwendung Ihren Benutzernamen an, was eine erfolgreiche Anmeldung signalisiert:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="Erfolgreiche Anmeldung":::

## <a name="test-web-api-call"></a>Testen des Web-API-Aufrufs

Nachdem Sie sich angemeldet haben, wählen Sie **Profil anzeigen** aus, um die Benutzerprofilinformationen anzuzeigen, die in der Antwort des Aufrufs der Microsoft Graph-API zurückgegeben werden:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Profilinformationen aus Microsoft Graph":::

Wählen Sie **Read Mails** (E-Mails lesen) aus, um die Nachrichten im Konto des Benutzers anzuzeigen. Ein Zustimmungsbildschirm wird angezeigt:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="Zustimmungsbildschirm für die Berechtigung „read.mail“":::

Nach der Zustimmung zeigen Sie die Nachrichten an, die in der Antwort des Aufrufs der Microsoft Graph-API zurückgegeben wurden:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="E-Mail-Informationen aus Microsoft Graph":::

## <a name="how-the-application-works"></a>Funktionsweise der Anwendung

Wenn ein Benutzer die Schaltfläche **Anmelden** zum ersten Mal auswählt, wird die Get-Methode `getTokenInteractive` von *AuthProvider.js* aufgerufen. Diese Methode leitet den Benutzer zur Anmeldung mit dem *Microsoft Identity Platform-Endpunkt* um, überprüft die Anmeldeinformationen des Benutzers und ruft dann einen **Autorisierungscode** ab. Dieser Code wird anschließend mithilfe der öffentlichen MSAL Node-API `acquireTokenByCode` gegen ein Zugriffstoken ausgetauscht.

An diesem Punkt wird ein mit PKCE geschützter Autorisierungscode an den mit CORS geschützten Tokenendpunkt gesendet und gegen Token ausgetauscht. Ein ID-Token, Zugriffstoken und Aktualisierungstoken werden von Ihrer Anwendung empfangen und von MSAL Node verarbeitet, und die in den Token enthaltenen Informationen werden jeweils zwischengespeichert.

Das ID-Token enthält grundlegende Informationen zum Benutzer, z. B. dessen Anzeigenamen. Das Zugriffstoken hat eine begrenzte Lebensdauer und läuft nach 24 Stunden ab. Wenn Sie planen, diese Token für den Zugriff auf eine geschützte Ressource zu nutzen, *muss* Ihr Back-End-Server diese überprüfen, um zu garantieren, dass das Token für einen gültigen Benutzer Ihrer Anwendung ausgestellt wurde.

Die von Ihnen in diesem Tutorial erstellte Desktop-App führt mithilfe eines Zugriffstokens als Bearertoken im Anforderungsheader ([RFC 6750](https://tools.ietf.org/html/rfc6750)) einen REST-Aufruf für die Microsoft Graph-API aus.

Die Microsoft Graph-API benötigt den Bereich *user.read*, um das Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die im Azure-Portal registriert ist. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt beispielsweise den Bereich *Mail.Read*, um die E-Mail des Benutzers aufzuführen.

Wenn Sie Bereiche hinzufügen, werden Ihre Benutzer möglicherweise aufgefordert, zusätzliche Zustimmung für die hinzugefügten Bereiche zu erteilen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich ausführlicher mit der Entwicklung von Node.js- und Electron-Desktopanwendungen auf Microsoft Identity Platform beschäftigen möchten, können Sie sich die mehrteilige Szenarioreihe ansehen:

> [!div class="nextstepaction"]
> [Szenario: Desktop-App, die Web-APIs aufruft](scenario-desktop-overview.md)
