---
title: 'Tutorial: Anmelden von Benutzern bei einer Node.js- und Express-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial fügen Sie Unterstützung für die Anmeldung von Benutzern in einer Web-App hinzu.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: 1a61eb908221d4fe2eea9d74d4fbc02c7660ba85
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507926"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Tutorial: Anmelden von Benutzern bei einer Node.js- und Express-Web-App

In diesem Tutorial erstellen Sie eine Web-App, die Benutzer anmeldet. Die von Ihnen erstellte Desktop-App verwendet die [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Führen Sie die Schritte in diesem Tutorial für folgende Aktionen aus:

> [!div class="checklist"]
> - Registrieren der Anwendung im Azure-Portal
> - Erstellen eines Express-Web-App-Projekts
> - Installieren der Pakete für die Authentifizierungsbibliothek
> - Hinzufügen von App-Registrierungsdetails
> - Hinzufügen von Code für die Benutzeranmeldung
> - Testen der App

## <a name="prerequisites"></a>Voraussetzungen

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

## <a name="register-the-application"></a>Registrieren der Anwendung

Führen Sie zunächst die Schritte unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md) aus, um Ihre App zu registrieren.

Verwenden Sie die folgenden Einstellungen für Ihre App-Registrierung:

- Name: `ExpressWebApp` (Vorschlag)
- Unterstützte Kontotypen: **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig) und persönliche Microsoft-Konten (z. B. Skype, Xbox)**
- Plattformtyp: **Web**
- Umleitungs-URI: `http://localhost:3000/redirect`
- Geheimer Clientschlüssel: `*********` (Notieren Sie diesen Wert für die Verwendung in einem späteren Schritt. Er wird nur einmal angezeigt.)

## <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie einen Ordner zum Hosten Ihrer Anwendung, z. B. *ExpressWebApp*.

1. Wechseln Sie zunächst in Ihrem Terminal zu Ihrem Projektverzeichnis, und führen Sie dann die folgenden `npm`-Befehle aus:

```console
    npm init -y
    npm install --save express
```

2. Erstellen Sie als Nächstes eine Datei namens *index.js*, und fügen Sie den folgenden Code hinzu:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Sie verfügen nun über einen einfachen Webserver, der an Port 3000 ausgeführt wird. Die Datei- und Ordnerstruktur Ihres Projekts sollte in etwa wie folgt aussehen:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Installieren der Authentifizierungsbibliothek

Suchen Sie den Stamm Ihres Projektverzeichnisses in einem Terminal, und installieren Sie das MSAL Node-Paket über NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Hinzufügen von App-Registrierungsdetails

Fügen Sie in der zuvor erstellten Datei *index.js* den folgenden Code hinzu:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
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
- `Enter_the_Client_secret`: Ersetzen Sie diesen Wert durch den zuvor erstellten geheimen Clientschlüssel. Generieren Sie im Azure-Portal mithilfe von **Zertifikate und Geheimnisse** in den App-Registrierungseinstellungen einen neuen Schlüssel.

> [!WARNING]
> Ein Klartextgeheimnis im Quellcode stellt ein höheres Sicherheitsrisiko dar. In diesem Artikel wird nur der Einfachheit halber ein geheimer Clientschlüssel in Klartext verwendet. Verwenden Sie in vertraulichen Clientanwendungen [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md) anstelle von geheimen Clientschlüsseln. Dies gilt insbesondere bei Apps, die Sie in der Produktion bereitstellen möchten.

## <a name="add-code-for-user-login"></a>Hinzufügen von Code für die Benutzeranmeldung

Fügen Sie in der zuvor erstellten Datei *index.js* den folgenden Code hinzu:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Testen der Anmeldung

Sie haben die Erstellung der Anwendung abgeschlossen und sind nun bereit, die Funktionalität der App zu testen.

1. Starten Sie die Node.js-Konsolen-App, indem Sie den folgenden Befehl im Stammverzeichnis Ihres Projektordners ausführen:

```console
   node index.js
```

2. Öffnen Sie ein Browserfenster, und navigieren Sie zu `http://localhost:3000`. Ein Anmeldebildschirm wird angezeigt:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Angezeigter Azure AD-Anmeldebildschirm":::

3. Nachdem Sie Ihre Anmelde Informationen eingegeben haben, wird ein Zustimmungsbildschirm angezeigt, auf dem Sie zur Genehmigung der Berechtigungen für die App aufgefordert werden.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Angezeigter Azure AD-Zustimmungsbildschirm":::

## <a name="how-the-application-works"></a>Funktionsweise der Anwendung

In diesem Tutorial haben Sie ein MSAL Node-Objekt vom Typ [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) initialisiert, indem Sie ein Konfigurationsobjekt (*msalConfig*) übergeben haben, das aus der Azure AD-App-Registrierung im Azure-Portal abgerufene Parameter enthält. Die von Ihnen erstellte Web-App nutzt den [OAuth 2.0-Genehmigungsflow per Autorisierungscode](./v2-oauth2-auth-code-flow.md) zum Anmelden von Benutzern und Abrufen von ID- und Zugriffstoken.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich ausführlicher mit der Entwicklung von Node.js- und Express-Webanwendungen auf Microsoft Identity Platform beschäftigen möchten, können Sie sich die mehrteilige Szenarioreihe ansehen:

> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)
