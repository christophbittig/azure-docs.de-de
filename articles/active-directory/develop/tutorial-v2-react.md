---
title: 'Tutorial: Erstellen einer React-Single-Page-Webanwendung mit Verwendung des Autorisierungscodeflows | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial wird eine React-Single-Page-Webanwendung erstellt, mit der Benutzer sich anmelden und den Authentifizierungscodeflow verwenden können, um ein Zugriffstoken von Microsoft Identity Platform zu erhalten und die Microsoft Graph-API aufzurufen.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/16/2021
ms.author: jamesmantu
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a42f4e5cd0b58c2078a4ba498b262efb804edcf8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050278"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-react-single-page-app-spa-using-auth-code-flow"></a>Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API über eine React-Single-Page-Webanwendung (SPA) unter Verwendung des Autorisierungscodeflows

In diesem Tutorial wird eine React-Single-Page-Webanwendung (Single-Page Application, SPA) erstellt, die Benutzer anmeldet und Microsoft Graph über den Autorisierungscodeflow mit PKCE aufruft. Die von Ihnen erstellte SPA verwendet die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für React.

In diesem Tutorial wird Folgendes durchgeführt:
> [!div class="checklist"]
> * Erstellen eines React-Projekts mit `npm`
> * Registrieren der Anwendung im Azure-Portal
> * Hinzufügen von Code zur Unterstützung der Benutzeranmeldung und -abmeldung
> * Hinzufügen von Code zum Aufrufen der Microsoft Graph-API
> * Testen der App

Von MSAL React wird anstelle des impliziten Genehmigungsflows der Autorisierungscodeflow im Browser unterstützt. Der implizite Flow wird von MSAL React **NICHT** unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/) zum Ausführen eines lokalen Webservers
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

## <a name="how-the-tutorial-app-works"></a>Funktionsweise der Tutorial-App

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramm, das den Autorisierungscodefluss in einer Single-Page-Webanwendung darstellt.":::

Die in diesem Tutorial erstellte Anwendung ermöglicht es einer React-SPA, Sicherheitstoken von Microsoft Identity Platform abzurufen und die Microsoft Graph-API abzufragen. Dabei wird die Microsoft Authentication Library (MSAL) für React verwendet – ein Wrapper der MSAL.js v2-Bibliothek. Mit MSAL React können auf React 16+ basierende Anwendungen sowohl Unternehmensbenutzer über Azure Active Directory (Azure AD) als auch Benutzer mit Microsoft-Konten sowie Benutzer mit Social Media-Identität (z. B. Facebook, Google und LinkedIn) authentifizieren. Die Bibliothek ermöglicht es Anwendungen zudem, Zugriff auf Microsoft Cloud Services und Microsoft Graph zu erhalten.

In diesem Szenario wird nach einer Benutzeranmeldung ein Zugriffstoken angefordert und den HTTP-Anforderungen im Autorisierungsheader hinzugefügt. Tokenabruf und -verlängerung werden von der Microsoft Authentication Library für React (MSAL React) gehandhabt.

### <a name="libraries"></a>Bibliotheken

In diesem Tutorial werden die folgenden Bibliotheken verwendet:

|Bibliothek|Beschreibung|
|---|---|
|[MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)|Microsoft Authentication Library für JavaScript: React-Wrapper|
|[MSAL-Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Microsoft Authentication Library für JavaScript v2: Browserpaket|

## <a name="get-the-completed-code-sample"></a>Abrufen des abgeschlossenen Codebeispiels

Möchten Sie stattdessen lieber das abgeschlossene Beispielprojekt dieses Tutorials herunterladen? Wenn Sie das Projekt mit einem lokalen Webserver (beispielsweise Node.js) ausführen möchten, können Sie das Repository [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) klonen:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-react-spa`

Um das Codebeispiel zu konfigurieren, bevor Sie es ausführen, wechseln Sie dann zum [Konfigurationsschritt](#register-your-application).

Um mit dem Tutorial fortzufahren und die Anwendung selbst zu erstellen, fahren Sie mit dem nächsten Abschnitt, [Voraussetzungen](#prerequisites), fort.

## <a name="create-your-project"></a>Erstellen Ihres Projekts

Öffnen Sie nach der Installation von [Node.js](https://nodejs.org/en/download/) ein Terminalfenster, und führen Sie die folgenden Befehle aus:

```console
npx create-react-app msal-react-tutorial # Create a new React app
cd msal-react-tutorial # Change to the app directory
npm install @azure/msal-browser @azure/msal-react # Install the MSAL packages
npm install react-bootstrap bootstrap # Install Bootstrap for styling
```

Sie haben nun mittels Bootstrapping unter Verwendung von [create-react-app](https://create-react-app.dev/docs/getting-started) ein kleines React-Projekt erstellt. Dies bildet die Grundlage für den Rest des Tutorials. Wenn Sie die Änderungen sehen möchten, die im Laufe dieses Tutorials an Ihrer App vorgenommen werden, können Sie den folgenden Befehl ausführen: 

```console
npm start
```

Es sollte automatisch ein Browserfenster mit Ihrer App geöffnet werden. Falls nicht, öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:3000. Sobald Sie eine Datei mit aktualisiertem Code speichern, wird die Seite neu geladen, um die Änderungen widerzuspiegeln.

## <a name="register-your-application"></a>Registrieren der Anwendung

Führen Sie die unter [Single-Page-Webanwendung: App-Registrierung](./scenario-spa-app-registration.md) beschriebenen Schritte aus, um eine App-Registrierung für Ihre SPA über das Azure-Portal zu erstellen.

Geben Sie im Schritt [Umleitungs-URI: MSAL.js 2.0 mit dem Authentifizierungscodeflow](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) die Zeichenfolge `http://localhost:3000` ein. Hierbei handelt es sich um den Standardort, an dem Ihre Anwendung durch „create-react-app“ bereitgestellt wird.

### <a name="configure-your-javascript-spa"></a>Konfigurieren Ihrer JavaScript-SPA

1. Erstellen Sie im Ordner *src* eine Datei mit dem Namen *authConfig.js*, die die Konfigurationsparameter für die Authentifizierung enthält, und fügen Sie dann den folgenden Code hinzu:

    ```javascript
    export const msalConfig = {
      auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here", // This is a URL (e.g. https://login.microsoftonline.com/{your tenant ID})
        redirectUri: "Enter_the_Redirect_Uri_Here",
      },
      cache: {
        cacheLocation: "sessionStorage", // This configures where your cache will be stored
        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      }
    };
    
    // Add scopes here for ID token to be used at Microsoft identity platform endpoints.
    export const loginRequest = {
     scopes: ["User.Read"]
    };
    
    // Add the endpoints here for Microsoft Graph API services you'd like to use.
    export const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me"
    };
    ```

1. Ändern Sie die Werte im Abschnitt `msalConfig`, wie hier beschrieben:
    
    |Wertname| Info|
    |----------|------|
    |`Enter_the_Application_Id_Here`| Die **Anwendungs-ID (Client)** der von Ihnen registrierten Anwendung.|
    |`Enter_the_Cloud_Instance_Id_Here`| Die Azure Cloud-Instanz, in der Ihre Anwendung registriert ist. Geben Sie für die Azure-Hauptcloud (oder die *globale* Cloud) `https://login.microsoftonline.com` ein. Geeignete Werte für **nationale** Clouds (z. B. für China) finden Sie unter [Nationale Clouds](authentication-national-cloud.md).
    |`Enter_the_Tenant_Info_Here`| Legen Sie eine der folgenden Optionen fest: Wenn Ihre Anwendung *Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die Verzeichnis-ID (Mandant) oder den Mandantennamen (z. B. **contoso.microsoft.com**). Wenn Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch **Organisationen**. Wenn Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten* unterstützt, ersetzen Sie diesen Wert durch **common**. Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch **consumers**. |
    |`Enter_the_Redirect_Uri_Here`|Ersetzen Sie diesen Wert durch **http://localhost:3000** .|
    |`Enter_the_Graph_Endpoint_Here`| Die Instanz der Microsoft Graph-API, mit der die Anwendung kommunizieren soll. Ersetzen Sie beide Vorkommen dieser Zeichenfolge für den **globalen** Microsoft Graph-API-Endpunkt durch `https://graph.microsoft.com`. Informationen zu Endpunkten in **nationalen** Cloudbereitstellungen finden Sie in der Microsoft Graph Dokumentation unter [Bereitstellungen nationaler Clouds](/graph/deployments).|
    
    Weitere Informationen zu den verfügbaren konfigurierbaren Optionen finden Sie unter [Initialisieren von Clientanwendungen](msal-js-initializing-client-applications.md).

1. Öffnen Sie die Datei *src/index.js*, und fügen Sie die folgenden Importe hinzu:

    ```javascript
    import "bootstrap/dist/css/bootstrap.min.css";
    import { PublicClientApplication } from "@azure/msal-browser";
    import { MsalProvider } from "@azure/msal-react";
    import { msalConfig } from "./authConfig";
    ```

1. Erstellen Sie unterhalb der Importe in *src/index.js* eine Instanz von `PublicClientApplication`. Verwenden Sie dabei die Konfiguration aus Schritt 1.

    ```javascript
    const msalInstance = new PublicClientApplication(msalConfig);
    ``` 

1. Suchen Sie in *src/index.js* nach der Komponente `<App />`, und umschließen Sie sie mit der Komponente `MsalProvider`. Ihre Renderfunktion sollte wie folgt aussehen:

    ```jsx
    ReactDOM.render(
        <React.StrictMode>
            <MsalProvider instance={msalInstance}>
                <App />
            </MsalProvider>
        </React.StrictMode>,
        document.getElementById("root")
    );
    ``` 


## <a name="sign-in-users"></a>Anmelden von Benutzern

Erstellen Sie in *src* einen Ordner namens *components* und in diesem Ordner eine Datei namens *SignInButton.jsx*. Fügen Sie den Code aus einem der folgenden Abschnitte hinzu, um die Anmeldung mittels Popupfenster oder Vollbildumleitung aufzurufen:

### <a name="sign-in-using-pop-ups"></a>Anmelden mit Hilfe von Popups

Fügen Sie in *src/components/SignInButton.jsx* den folgenden Code hinzu, um eine Schaltflächenkomponente zu erstellen, durch die eine Popupanmeldung aufgerufen wird, wenn ein Benutzer sie auswählt:

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginPopup(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for login
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Popup</Button>
    );
}
```

### <a name="sign-in-using-redirects"></a>Anmelden mittels Umleitungen

Fügen Sie in *src/components/SignInButton.jsx* den folgenden Code hinzu, um eine Schaltflächenkomponente zu erstellen, durch die eine Umleitungsanmeldung aufgerufen wird, wenn ein Benutzer sie auswählt:

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginRedirect(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the login prompt
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Redirect</Button>
    );
}
```

### <a name="add-the-sign-in-button"></a>Hinzufügen der Anmeldeschaltfläche

1. Erstellen Sie im Ordner *components* eine weitere Datei namens *PageLayout.jsx*, und fügen Sie den folgenden Code hinzu, um eine Navigationsleistenkomponente für die soeben erstellte Anmeldeschaltfläche zu erstellen:

    ```jsx
    import React from "react";
    import Navbar from "react-bootstrap/Navbar";
    import { useIsAuthenticated } from "@azure/msal-react";
    import { SignInButton } from "./SignInButton";
    
    /**
     * Renders the navbar component with a sign-in button if a user is not authenticated
     */
    export const PageLayout = (props) => {
        const isAuthenticated = useIsAuthenticated();
    
        return (
            <>
                <Navbar bg="primary" variant="dark">
                    <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                    { isAuthenticated ? <span>Signed In</span> : <SignInButton /> }
                </Navbar>
                <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
                <br />
                <br />
                {props.children}
            </>
        );
    };
    ```

2. Öffnen Sie nun *src/App.js*, und ersetzen Sie den vorhandenen Inhalt durch den folgenden Code: 

    ```jsx
    import React from "react";
    import { PageLayout } from "./components/PageLayout";
    
    function App() {
      return (
          <PageLayout>
              <p>This is the main app content!</p>
          </PageLayout>
      );
    }
    
    export default App;
    ```

Ihre App verfügt nun über eine Anmeldeschaltfläche, die nur für nicht authentifizierte Benutzer angezeigt wird.

Wenn ein Benutzer zum ersten Mal die Schaltfläche **Sign in using Popup** (Mittels Popup anmelden) oder **Sign in using Redirect** (Mittels Umleitung anmelden) auswählt, ruft der Handler `onClick` je nach ausgewählter Schaltfläche entweder `loginPopup` oder `loginRedirect` auf, um den Benutzer anzumelden. Die `loginPopup`-Methode öffnet ein Popupfenster mit dem *Microsoft Identity Platform-Endpunkt*, um die Anmeldeinformationen des Benutzers anzufordern und zu überprüfen. Nach einer erfolgreichen Anmeldung wird von *msal.js* der [Autorisierungcodefluss](v2-oauth2-auth-code-flow.md) initiiert.

An diesem Punkt wird ein mit PKCE geschützter Autorisierungscode an den mit CORS geschützten Tokenendpunkt gesendet und gegen Token ausgetauscht. Ein ID-Token, Zugriffstoken und Aktualisierungstoken werden von Ihrer Anwendung empfangen und von *msal.js* verarbeitet, und die in den Token enthaltenen Informationen werden jeweils zwischengespeichert.

## <a name="sign-users-out"></a>Abmelden von Benutzern

Erstellen Sie in *src/components* eine Datei mit dem Namen *SignOutButton.jsx*. Fügen Sie den Code aus einem der folgenden Abschnitte hinzu, um die Abmeldung mittels Popupfenster oder Vollbildumleitung aufzurufen:

### <a name="sign-out-using-pop-ups"></a>Durch Pop-Ups abmelden

Fügen Sie in *src/components/SignInButton.jsx* den folgenden Code hinzu, um eine Schaltflächenkomponente zu erstellen, durch die eine Popupanmeldung aufgerufen wird, wenn ein Benutzer sie auswählt:

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutPopup().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for logout
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Popup</Button>
    );
}
```

### <a name="sign-out-using-redirects"></a>Abmelden mittels Umleitungen

Fügen Sie in *src/components/SignOutButton.jsx* den folgenden Code hinzu, um eine Schaltflächenkomponente zu erstellen, durch die eine Umleitungsabmeldung aufgerufen wird, wenn ein Benutzer sie auswählt:

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutRedirect().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the logout prompt
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Redirect</Button>
    );
}
```

### <a name="add-the-sign-out-button"></a>Hinzufügen der Abmeldeschaltfläche

Aktualisieren Sie in *src/components/PageLayout.jsx* die Komponente `PageLayout`, um die neue Komponente `SignOutButton` für authentifizierte Benutzer zu rendern. Ihr Code sollte folgendermaßen aussehen:

```jsx
import React from "react";
import Navbar from "react-bootstrap/Navbar";
import { useIsAuthenticated } from "@azure/msal-react";
import { SignInButton } from "./SignInButton";
import { SignOutButton } from "./SignOutButton";

/**
 * Renders the navbar component with a sign-in button if a user is not authenticated
 */
export const PageLayout = (props) => {
    const isAuthenticated = useIsAuthenticated();

    return (
        <>
            <Navbar bg="primary" variant="dark">
                <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                { isAuthenticated ? <SignOutButton /> : <SignInButton /> }
            </Navbar>
            <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
            <br />
            <br />
            {props.children}
        </>
    );
};
```

## <a name="conditionally-render-components"></a>Bedingtes Rendern von Komponenten

Verwenden Sie wie unten gezeigt `AuthenticateTemplate` und/oder `UnauthenticatedTemplate`, um bestimmte Komponenten nur für authentifizierte oder nur für nicht authentifizierte Benutzer zu rendern.

1. Fügen Sie in *src/App.js* den folgenden Import hinzu:

    ```javascript
    import { AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";
    ```
    
1. Wenn bestimmte Komponenten nur für authentifizierte Benutzer gerendert werden sollen, aktualisieren Sie in *src/App.js* die Funktion `App` mit dem folgenden Code: 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

1. Sollen bestimmte Komponenten nur für nicht authentifizierte Benutzer gerendert werden (beispielsweise ein Anmeldevorschlag), aktualisieren Sie in *src/App.js* die Funktion `App` mit dem folgenden Code: 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
                <UnauthenticatedTemplate>
                    <p>You are not signed in! Please sign in.</p>
                </UnauthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

## <a name="acquire-a-token"></a>Abrufen eines Token

1. Vor dem Aufrufen einer API (beispielsweise Microsoft Graph) muss zunächst ein Zugriffstoken abgerufen werden. Fügen Sie *src/App.js* mithilfe des folgenden Codes eine neue Komponente namens `ProfileContent` hinzu:

    ```jsx
    function ProfileContent() {
        const { instance, accounts, inProgress } = useMsal();
        const [accessToken, setAccessToken] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestAccessToken() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                setAccessToken(response.accessToken);
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    setAccessToken(response.accessToken);
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {accessToken ? 
                    <p>Access Token Acquired!</p>
                    :
                    <Button variant="secondary" onClick={RequestAccessToken}>Request Access Token</Button>
                }
            </>
        );
    };
    ```

1. Aktualisieren Sie Ihre Importe in *src/App.js* wie folgt:

    ```js
    import React, { useState } from "react";
    import { PageLayout } from "./components/PageLayout";
    import { AuthenticatedTemplate, UnauthenticatedTemplate, useMsal } from "@azure/msal-react";
    import { loginRequest } from "./authConfig";
    import Button from "react-bootstrap/Button";
    ```

1. Fügen Sie abschließend Ihre neue Komponente `ProfileContent` in *src/App.js* in der Komponente `App` als untergeordnetes Element von `AuthenticatedTemplate` hinzu. Die Komponente `App` sollte wie folgt aussehen:

    ```javascript
    function App() {
      return (
          <PageLayout>
              <AuthenticatedTemplate>
                  <ProfileContent />
              </AuthenticatedTemplate>
              <UnauthenticatedTemplate>
                  <p>You are not signed in! Please sign in.</p>
              </UnauthenticatedTemplate>
          </PageLayout>
      );
    }
    ```

Mit dem obigen Code wird eine Schaltfläche für angemeldete Benutzer gerendert, über die sie ein Zugriffstoken für Microsoft Graph anfordern können.

Angemeldete Benutzer sollte von Ihrer App nicht bei jedem Zugriff auf eine geschützte Ressource erneut zur Authentifizierung (also zur Anforderung eines Tokens) aufgefordert werden. Rufen Sie zur Vermeidung solcher erneuten Authentifizierungsanforderungen `acquireTokenSilent` auf. Dadurch wird zunächst nach einem zwischengespeicherten, nicht abgelaufenen Zugriffstoken gesucht und bei Bedarf das Aktualisierungstoken verwendet, um ein neues Zugriffstoken abzurufen. Es gibt aber einige Situationen, in denen Sie die Interaktion mit Microsoft Identity Platform ggf. erzwingen müssen. Beispiel:

- Benutzer müssen ihre Anmeldeinformationen erneut eingeben, da die Sitzung abgelaufen ist.
- Das Aktualisierungstoken ist abgelaufen.
- Ihre Anwendung fordert Zugriff auf eine Ressource an, und die Einwilligung des Benutzers ist erforderlich.
- Die zweistufige Authentifizierung ist erforderlich.

Beim Aufrufen von `acquireTokenPopup` wird ein Popupfenster geöffnet (oder Benutzer werden über `acquireTokenRedirect` an Microsoft Identity Platform umgeleitet). In diesem Fenster müssen Benutzer ihre Anmeldeinformationen bestätigen, ihre Zustimmung für die erforderliche Ressource geben oder die zweistufige Authentifizierung durchführen.

> [!NOTE]
> Wenn Sie Internet Explorer verwenden, empfiehlt es sich, die Methoden `loginRedirect` und `acquireTokenRedirect` zu verwenden, da ein [bekanntes Problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups) mit Internet Explorer und Popupfenstern vorliegt.

## <a name="call-the-microsoft-graph-api"></a>Aufrufen der Microsoft Graph-API

1. Erstellen Sie im Ordner *src* eine Datei mit dem Namen *graph.js*, und fügen Sie den folgenden Code hinzu, um REST-Aufrufe an die Microsoft Graph-API zu senden:

    ```javascript
    import { graphConfig } from "./authConfig";
    
    /**
     * Attaches a given access token to a Microsoft Graph API call. Returns information about the user
     */
    export async function callMsGraph(accessToken) {
        const headers = new Headers();
        const bearer = `Bearer ${accessToken}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        return fetch(graphConfig.graphMeEndpoint, options)
            .then(response => response.json())
            .catch(error => console.log(error));
    }
    ```

1. Erstellen Sie als Nächstes in *src/components* eine Datei mit dem Namen *ProfileData.jsx*, und fügen Sie den folgenden Code hinzu:

    ```javascript
    import React from "react";
    
    /**
     * Renders information about the user obtained from Microsoft Graph
     */
    export const ProfileData = (props) => {
        return (
            <div id="profile-div">
                <p><strong>First Name: </strong> {props.graphData.givenName}</p>
                <p><strong>Last Name: </strong> {props.graphData.surname}</p>
                <p><strong>Email: </strong> {props.graphData.userPrincipalName}</p>
                <p><strong>Id: </strong> {props.graphData.id}</p>
            </div>
        );
    };
    ```

1. Öffnen Sie *src/App.js*, und fügen Sie den Importen Folgendes hinzu:
    
    ```javascript
    import { ProfileData } from "./components/ProfileData";
    import { callMsGraph } from "./graph";
    ```

1. Aktualisieren Sie abschließend in *src/App.js* die Komponente `ProfileContent`, um nach dem Abrufen des Tokens Microsoft Graph aufzurufen und die Profildaten anzuzeigen. Die Komponente `ProfileContent` sollte wie folgt aussehen:

    ```javascript
    function ProfileContent() {
        const { instance, accounts } = useMsal();
        const [graphData, setGraphData] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestProfileData() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                callMsGraph(response.accessToken).then(response => setGraphData(response));
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    callMsGraph(response.accessToken).then(response => setGraphData(response));
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {graphData ? 
                    <ProfileData graphData={graphData} />
                    :
                    <Button variant="secondary" onClick={RequestProfileData}>Request Profile Information</Button>
                }
            </>
        );
    };
    ```

In den obigen Änderungen wird die Methode `callMSGraph()` verwendet, um eine HTTP-Anforderung vom Typ `GET` an eine geschützte Ressource zu richten, die ein Token erfordert. Der Inhalt wird anschließend an den Aufrufer zurückgegeben. Diese Methode fügt das abgerufene Token in den *HTTP-Autorisierungsheader* ein. In der in diesem Tutorial erstellten Beispielanwendung ist die geschützte Ressource der Endpunkt *me* der Microsoft Graph-API, der die Profilinformationen des angemeldeten Benutzers anzeigt.

## <a name="test-your-application"></a>Testen Ihrer Anwendung

Sie haben die Erstellung der Anwendung abgeschlossen und sind nun bereit, den Webserver zu starten und die Funktionalität der App zu testen.

1. Stellen Sie Ihre App bereit, indem Sie im Stammverzeichnis Ihres Projektordners den folgenden Befehl ausführen:

   ```console
   npm start
   ```
1. Es sollte automatisch ein Browserfenster mit Ihrer App geöffnet werden. Falls nicht, öffnen Sie Ihren Browser, und navigieren Sie zu `http://localhost:3000`. Die daraufhin angezeigte Seite sollte wie folgt aussehen:

    :::image type="content" source="media/tutorial-v2-react/react-01-unauthenticated.png" alt-text="Webbrowser mit dem Anmeldedialogfeld.":::

1. Wählen Sie die Anmeldeschaltfläche aus, um sich anzumelden.

### <a name="provide-consent-for-application-access"></a>Zustimmen zum Anwendungszugriff

Wenn Sie sich zum ersten Mal bei Ihrer Anwendung anmelden, werden Sie aufgefordert, ihr Zugriff auf Ihr Profil zu gewähren und sich anzumelden:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Im Webbrowser angezeigtes Inhaltsdialogfeld.":::

Wenn Sie in die angeforderten Berechtigungen einwilligen, wird von der Webanwendung Ihr Name angezeigt, um zu signalisieren, dass die Anmeldung erfolgreich war:

:::image type="content" source="media/tutorial-v2-react/react-02-authenticated.png" alt-text="Ergebnisse einer erfolgreichen Anmeldung im Webbrowser.":::

### <a name="call-the-graph-api"></a>Aufrufen der Graph-API

Nachdem Sie sich angemeldet haben, wählen Sie **Profil anzeigen** aus, um die Benutzerprofilinformationen anzuzeigen, die in der Antwort des Aufrufs der Microsoft Graph-API zurückgegeben werden:

:::image type="content" source="media/tutorial-v2-react/react-03-graph-data.png" alt-text="Im Browser angezeigte Profilinformationen aus Microsoft Graph.":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich *user.read*, um das Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die im Azure-Portal registriert ist. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt beispielsweise den Bereich *Mail.Read*, um die E-Mail des Benutzers aufzuführen.

Wenn Sie Bereiche hinzufügen, werden Ihre Benutzer möglicherweise aufgefordert, zusätzliche Zustimmung für die hinzugefügten Bereiche zu erteilen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie sich noch ausführlicher mit der Entwicklung von JavaScript-Single-Page-Webanwendungen auf der Microsoft Identity Platform beschäftigen möchten, können Sie sich die mehrteilige Szenarioreihe ansehen:

> [!div class="nextstepaction"]
> [Szenario: Einseitige Anwendung](scenario-spa-overview.md)
