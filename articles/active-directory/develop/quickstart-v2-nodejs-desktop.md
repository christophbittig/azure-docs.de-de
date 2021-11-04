---
title: 'Schnellstart: Aufrufen von Microsoft Graph über eine Node.js-Desktop-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine Node.js Electron-Desktopanwendung Benutzer anmelden und ein Zugriffstoken abrufen kann, um eine durch einen Microsoft Identity Platform-Endpunkt geschützte API aufzurufen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: 9020f5952e224ae93e4c1acaab2ab62fcefa81fa
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501816"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Schnellstart: Abrufen eines Zugriffstokens und Aufrufen der Microsoft Graph-API über eine Electron-Desktop-App

Das Codebeispiel, das in dieser Schnellstartanleitung heruntergeladen und ausgeführt wird, zeigt, wie eine Electron-Desktopanwendung Benutzer anmelden und Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen.

In dieser Schnellstartanleitung wird die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) mit dem [Autorisierungscodeflow mit PKCE](v2-oauth2-auth-code-flow.md) verwendet.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrieren und Herunterladen der Beispielanwendung
>
> Führen Sie die folgenden Schritte aus:
>
> #### <a name="step-1-register-the-application"></a>Schritt 1: Registrieren der Anwendung
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie die Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `msal-node-desktop`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
> 1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
> 1. Wählen Sie **Plattform hinzufügen** > **Mobilgerät- und Desktopanwendungen** aus.
> 1. Geben Sie im Abschnitt **Umleitungs-URIs** den URI `msal://redirect` ein.
> 1. Wählen Sie **Konfigurieren** aus.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Schritt 1: Konfigurieren der Anwendung im Azure-Portal
> Damit das Codebeispiel für diese Schnellstartanleitung funktioniert, muss ein Antwort-URI als **msal://redirect** hinzugefügt werden.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-windows-desktop/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-electron-sample-project"></a>Schritt 2: Herunterladen des Electron-Beispielprojekts

> [!div renderon="docs"]
> [Laden Sie das Codebeispiel herunter](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip).

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Schritt 3: Konfigurieren des Electron-Beispielprojekts
>
> 1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses (beispielsweise unter *C:\Azure-Samples*).
> 1. Bearbeiten Sie *.env*, und ersetzen Sie die Felder `TENANT_ID` und `CLIENT_ID` durch den folgenden Ausschnitt:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Hierbei gilt:
>    - `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
>    - `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** finden Sie im Azure-Portal auf der Seite **Übersicht** der App.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

Sie müssen einmalig die Abhängigkeiten dieses Beispiels installieren:

```console
npm install
```

Führen Sie dann die Anwendung über die Eingabeaufforderung oder über die Konsole aus:

```console
npm start
```

Daraufhin sollte die Benutzeroberfläche der Anwendung mit einer **Anmeldeschaltfläche** angezeigt werden.

## <a name="about-the-code"></a>Informationen zum Code

Im Anschluss werden einige wichtige Aspekte der Beispielanwendung erläutert.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird. Weitere Informationen zur Verwendung von MSAL Node mit Desktop-Apps finden Sie in [diesem Artikel](scenario-desktop-overview.md).

MSAL Node kann mithilfe des folgenden npm-Befehls installiert werden:

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL Node hinzufügen, indem Sie den folgenden Code hinzufügen:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Hierbei gilt: |BESCHREIBUNG |
> |---------|---------|
> | `clientId` | Die **Anwendungs-ID (Client)** für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |
> | `authority`    | Der STS-Endpunkt für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}` für die öffentliche Cloud, wobei „{tenant}“ der Name Ihres Mandanten bzw. Ihre Mandanten-ID ist.|

### <a name="requesting-tokens"></a>Anfordern von Token

Bereiten Sie im ersten Abschnitt des Autorisierungscodeflows mit PKCE eine Autorisierungscodeanforderung mit den entsprechenden Parametern vor, und senden Sie sie. Lauschen Sie dann im zweiten Abschnitt des Flows auf die Antwort des Autorisierungscodes. Nachdem der Code abgerufen wurde, tauschen Sie ihn aus, um ein Token zu erhalten.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

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
```

> |Hierbei gilt:| BESCHREIBUNG |
> |---------|---------|
> | `authWindow` | Das aktuell in Bearbeitung befindliche Electron-Fenster. |
> | `tokenRequest` | Enthält die angeforderten Bereiche (etwa `"User.Read"` für Microsoft Graph oder `"api://<Application ID>/access_as_user"` für benutzerdefinierte Web-APIs). |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Entwicklung von Electron-Desktop-Apps mit MSAL Node finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API in einer Electron-Desktop-App](tutorial-v2-nodejs-desktop.md)