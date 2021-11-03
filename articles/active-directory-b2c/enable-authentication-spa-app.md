---
title: Aktivieren der Authentifizierung in einer Single-Page-Webanwendung mithilfe der Bausteine von Azure Active Directory B2C
description: Dieser Artikel erläutert die Bausteine von Azure Active Directory B2C zum Registrieren und Anmelden von Benutzern für eine Single-Page-Webanwendung (Single Page Application, SPA).
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: a42d8f356286e2936ca9d6bdd15151bbf9ef1064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007332"
---
# <a name="enable-authentication-in-your-own-single-page-application-by-using-azure-ad-b2c"></a>Aktivieren der Authentifizierung in Ihrer eigenen Single-Page-Webanwendung mithilfe von Azure AD B2C

In diesem Artikel wird beschrieben, wie Sie Ihrer eigenen Single-Page-Webanwendung (SPA) die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen. Sie erfahren, wie Sie eine SPA mithilfe der [Microsoft Authentication Library for JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) erstellen. 

Verwenden Sie diesen Artikel zusammen mit dem Artikel [Konfigurieren der Authentifizierung in einer Single-Page-Beispielwebanwendung (SPA) mit Azure Active Directory B2C](./configure-authentication-sample-spa-app.md), und ersetzen Sie die Beispiel-SPA durch Ihre eigene SPA.

## <a name="overview"></a>Übersicht

In diesem Artikel werden Node.js und [Express](https://expressjs.com/) verwendet, um eine einfache Node.js-Web-App zu erstellen. Express ist ein einfaches und flexibles Node.js-Web-App-Framework, das eine Reihe von Features für Web- und Mobilanwendungen bietet.

Die [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)-Authentifizierungsbibliothek ist eine von Microsoft bereitgestellte Bibliothek, die das Hinzufügen von Authentifizierungs- und Autorisierungsunterstützung für SPAs vereinfacht.

> [!TIP]
> Der gesamte MSAL.js-Code wird auf der Clientseite ausgeführt. Sie können den serverseitigen Code für Node.js und Express durch andere Lösungen wie z. B. die Programmiersprachen .NET Core, Java und PHP (Hypertext Preprocessor) ersetzen.

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen sowie Anweisungen zur Integration finden Sie unter [Konfigurieren der Authentifizierung in einer Single-Page-Beispielwebanwendung (SPA) mit Azure Active Directory B2C](configure-authentication-sample-spa-app.md).

## <a name="step-1-create-an-spa-app-project"></a>Schritt 1: Erstellen eines SPA-Projekts

Sie können ein vorhandenes SPA-Projekt verwenden oder ein neues erstellen. Um ein neues Projekt zu erstellen, gehen Sie folgendermaßen vor:

1. Öffnen Sie eine Befehlsshell, und erstellen Sie ein neues Verzeichnis (z. B. *myApp*). Dieses Verzeichnis enthält Ihren App-Code, die Benutzeroberfläche und Konfigurationsdateien.

1. Geben Sie das von Ihnen erstellte Verzeichnis an.

1. Verwenden Sie den Befehl `npm init`, um eine `package.json`-Datei für Ihre App zu erstellen. Dieser Befehl fordert Sie zur Eingabe von Informationen über Ihre App auf (z. B. den Namen und die Version Ihrer App und den Namen des anfänglichen Einstiegspunkts, die *index.js*-Datei). Führen Sie den folgenden Befehl aus, und übernehmen Sie die Standardwerte:

```
npm init
```

## <a name="step-2-install-the-dependencies"></a>Schritt 2: Installieren der Abhängigkeiten

Zum Installieren des Express-Pakets führen Sie den folgenden Befehl in der Befehlsshell aus:

```
npm install express
```

Um die statischen Dateien der App zu lokalisieren, verwendet der serverseitige Code das Paket [Path](https://www.npmjs.com/package/path). 

Zum Installieren des Path-Pakets führen Sie den folgenden Befehl in der Befehlsshell aus:

```
npm install path
```

## <a name="step-3-configure-your-web-server"></a>Schritt 3: Konfigurieren Ihres Webservers

Erstellen Sie im Ordner *myApp* eine Datei namens *index.js*, die den folgenden Code enthält:

```javascript
// Initialize express
const express = require('express');
const app = express();

// The port to listen to incoming HTTP requests
const port = 6420;

// Initialize path
const path = require('path');

// Set the front-end folder to serve public assets.
app.use(express.static('App'));

// Set up a route for the index.html
app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname + '/index.html'));
});

// Start the server, and listen for HTTP requests
app.listen(port, () => {
  console.log(`Listening on http://localhost:${port}`);
});
```

## <a name="step-4-create-the-spa-user-interface"></a>Schritt 4: Erstellen der SPA-Benutzeroberfläche

Fügen Sie die SPA-Datei `index.html` hinzu. Diese Datei implementiert eine Benutzeroberfläche, die mit einem Bootstrap-Framework kompiliert wird, und importiert Skriptdateien für Konfiguration, Authentifizierung und Web-API-Aufrufe.

Die durch die Datei *index.html* referenzierten Ressourcen sind in der folgenden Tabelle aufgelistet: 

|Verweis |Definition|
|---|---|
|MSAL.js-Bibliothek| Der [CDN-Pfad](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md) der MSAL.js-Authentifizierungsbibliothek für JavaScript.|
|[Bootstrap-Stylesheet](https://getbootstrap.com/) | Ein kostenloses Front-End-Framework für eine schnellere und einfachere Webentwicklung. Das Framework umfasst HTML- und CSS-basierte Entwurfsvorlagen. |
|[`policies.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/policies.js) | Enthält die benutzerdefinierten Richtlinien und Benutzerflows für Azure AD B2C. |
|[`authConfig.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authConfig.js) | Enthält Konfigurationsparameter für die Authentifizierung.|
|[`authRedirect.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authRedirect.js) | Enthält die Authentifizierungslogik. |
|[`apiConfig.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/apiConfig.js) | Enthält Web-API-Bereiche und den Speicherort des API-Endpunkts. |
|[`api.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/api.js) | Definiert die Methode zum Aufrufen Ihrer API und Verarbeiten der Antwort.|
|[`ui.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/ui.js) | Steuert die Benutzeroberflächenelemente. |
| | |

Um die SPA-Indexdatei im *myApp*-Ordner zu rendern, erstellen Sie eine Datei namens *index.html*, die den folgenden HTML-Codeausschnitt enthält.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Azure AD B2C test app</title>
    </head>
    <body>
        <h2>My Azure AD B2C test app</h2>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign-in</button>
        <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign-out</button>
        <h5 id="welcome-div" class="card-header text-center d-none"></h5>
        <br />
        <!-- Content -->
        <div class="card">
            <div class="card-body text-center">
                <pre id="response" class="card-text"></pre>
                <button type="button" id="callApiButton" class="btn btn-primary d-none" onclick="passTokenToApi()">Call API</button>
            </div>
        </div>
        <script src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js" integrity="sha384-ggh+EF1aSqm+Y4yvv2n17KpurNcZTeYtUZUvhPziElsstmIEubyEB6AIVpKLuZgr" crossorigin="anonymous"></script>

        <!-- Importing app scripts (load order is important) -->
        <script type="text/javascript" src="./apiConfig.js"></script>
        <script type="text/javascript" src="./policies.js"></script>
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authRedirect.js"></script>
        <script type="text/javascript" src="./api.js"></script>
    </body>
</html>
 ```

## <a name="step-5-configure-the-authentication-library"></a>Schritt 5: Konfigurieren der Authentifizierungsbibliothek

Konfigurieren Sie die Art und Weise, in der die MSAL.js-Bibliothek in Azure AD B2C integriert wird. Die MSAL.js-Bibliothek verwendet ein allgemeines Konfigurationsobjekt, um eine Verbindung mit den Authentifizierungsendpunkten Ihres Azure AD B2C-Mandanten herzustellen.

Um die Authentifizierungsbibliothek zu konfigurieren, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Ordner *myApp* einen neuen Ordner namens *App*. 
1. Erstellen Sie im Ordner *App* eine neue Datei namens *authConfig.js*.
1. Fügen Sie der Datei *authConfig.js* den folgenden JavaScript-Code hinzu:

    ```javascript
    const msalConfig = {
        auth: {
        clientId: "<Application-ID>", 
        authority: b2cPolicies.authorities.signUpSignIn.authority, 
        knownAuthorities: [b2cPolicies.authorityDomain], 
        redirectUri: "http://localhost:6420",
        },
        cache: {
        cacheLocation: "localStorage", .
        storeAuthStateInCookie: false, 
        }
    };

    const loginRequest = {
    scopes: ["openid", ...apiConfig.b2cScopes],
    };

    const tokenRequest = {
    scopes: [...apiConfig.b2cScopes],
    forceRefresh: false
    };
    ```

1. Ersetzen Sie `<Application-ID>` durch die Anwendungs-ID Ihrer App-Registrierung. Weitere Informationen finden Sie unter [Konfigurieren der Authentifizierung in einer Single-Page-Beispielwebanwendung (SPA) mit Azure Active Directory B2C](./configure-authentication-sample-spa-app.md#step-23-register-the-spa).

> [!TIP]
> Weitere Konfigurationsoptionen für MSAL-Objekte finden Sie im Artikel [Authentifizierungsoptionen](./enable-authentication-spa-app-options.md).

## <a name="step-6-specify-your-azure-ad-b2c-user-flows"></a>Schritt 6: Angeben Ihrer Azure AD B2C-Benutzerflows

Erstellen Sie die Datei *policies.js*, die Informationen zu Ihrer Azure AD B2C-Umgebung bereitstellt. Die MSAL.js-Bibliothek verwendet diese Informationen, um Authentifizierungsanforderungen für Azure AD B2C zu erstellen.

Um Ihre Azure AD B2C-Benutzerflows anzugeben, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Ordner *App* eine neue Datei namens *policies.js*.
1. Fügen Sie der Datei *policies.js* den folgenden Code hinzu:

    ```javascript
    const b2cPolicies = {
        names: {
            signUpSignIn: "B2C_1_SUSI",
            editProfile: "B2C_1_EditProfile"
        },
        authorities: {
            signUpSignIn: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-SignInOrSignUp-Policy-Id",
            },
            editProfile: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-EditProfile-Policy-Id"
            }
        },
        authorityDomain: "contoso.b2clogin.com"
    }
    ```

1. Ersetzen Sie `B2C_1_SUSI` durch den Azure AD B2C-Richtliniennamen für die Anmeldung.
1. Ersetzen Sie `B2C_1_EditProfile` durch den Azure AD B2C-Richtliniennamen für die Profilbearbeitung.
1. Ersetzen Sie alle Instanzen von `contoso` durch den [Namen Ihres Azure AD B2C-Mandanten](./tenant-management.md#get-your-tenant-name).

## <a name="step-7-use-the-msal-to-sign-in-the-user"></a>Schritt 7: Verwenden der MSAL zum Anmelden des Benutzers

In diesem Schritt implementieren Sie die Methoden zum Initialisieren des Anmeldeflows, zum Erwerb des API-Zugriffstokens und zur Abmeldung. 

Weitere Informationen finden Sie in der Klassenreferenz zu [MSAL PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) und unter [Verwenden der MSAL (Microsoft Authentication Library) für die Benutzeranmeldung](../active-directory/develop/tutorial-v2-javascript-spa.md#use-the-microsoft-authentication-library-msal-to-sign-in-the-user).

Um den Benutzer anzumelden, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Ordner *App* eine neue Datei namens *authRedirect.js*.
1. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *authRedirect.js* ein:

    ```javascript
    // Create the main myMSALObj instance
    // configuration parameters are located at authConfig.js
    const myMSALObj = new msal.PublicClientApplication(msalConfig);

    let accountId = "";
    let idTokenObject = "";
    let accessToken = null;

    myMSALObj.handleRedirectPromise()
        .then(response => {
            if (response) {
                /**
                 * For the purpose of setting an active account for UI update, we want to consider only the auth response resulting
                 * from SUSI flow. "tfp" claim in the id token tells us the policy (NOTE: legacy policies may use "acr" instead of "tfp").
                 * To learn more about B2C tokens, visit https://docs.microsoft.com/en-us/azure/active-directory-b2c/tokens-overview
                 */
                if (response.idTokenClaims['tfp'].toUpperCase() === b2cPolicies.names.signUpSignIn.toUpperCase()) {
                    handleResponse(response);
                }
            }
        })
        .catch(error => {
            console.log(error);
        });


    function setAccount(account) {
        accountId = account.homeAccountId;
        idTokenObject = account.idTokenClaims;
        myClaims= JSON.stringify(idTokenObject);
        welcomeUser(myClaims);
    }

    function selectAccount() {

        /**
         * See here for more information on account retrieval: 
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */

        const currentAccounts = myMSALObj.getAllAccounts();

        if (currentAccounts.length < 1) {
            return;
        } else if (currentAccounts.length > 1) {
        
            /**
             * Due to the way MSAL caches account objects, the auth response from initiating a user-flow
             * is cached as a new account, which results in more than one account in the cache. Here we make
             * sure we are selecting the account with homeAccountId that contains the sign-up/sign-in user-flow, 
             * as this is the default flow the user initially signed-in with.
             */
            const accounts = currentAccounts.filter(account =>
                account.homeAccountId.toUpperCase().includes(b2cPolicies.names.signUpSignIn.toUpperCase())
                &&
                account.idTokenClaims.iss.toUpperCase().includes(b2cPolicies.authorityDomain.toUpperCase())
                &&
                account.idTokenClaims.aud === msalConfig.auth.clientId 
                );

            if (accounts.length > 1) {
                // localAccountId identifies the entity for which the token asserts information.
                if (accounts.every(account => account.localAccountId === accounts[0].localAccountId)) {
                    // All accounts belong to the same user
                    setAccount(accounts[0]);
                } else {
                    // Multiple users detected. Logout all to be safe.
                    signOut();
                };
            } else if (accounts.length === 1) {
                setAccount(accounts[0]);
            }

        } else if (currentAccounts.length === 1) {
            setAccount(currentAccounts[0]);
        }
    }

    // in case of page refresh
    selectAccount();

    async function handleResponse(response) {

        if (response !== null) {
            setAccount(response.account);
        } else {
            selectAccount();
        }
    }

    function signIn() {
        myMSALObj.loginRedirect(loginRequest);
    }

    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
        };

        myMSALObj.logoutRedirect(logoutRequest);
    }

    function getTokenRedirect(request) {
        request.account = myMSALObj.getAccountByHomeId(accountId); 

        return myMSALObj.acquireTokenSilent(request)
            .then((response) => {
                // In case the response from B2C server has an empty accessToken field
                // throw an error to initiate token acquisition
                if (!response.accessToken || response.accessToken === "") {
                    throw new msal.InteractionRequiredAuthError;
                } else {
                    console.log("access_token acquired at: " + new Date().toString());
                    accessToken = response.accessToken;
                    passTokenToApi();
                }
            }).catch(error => {
                console.log("Silent token acquisition fails. Acquiring token using popup. \n", error);
                if (error instanceof msal.InteractionRequiredAuthError) {
                    // fallback to interaction when silent call fails
                    return myMSALObj.acquireTokenRedirect(request);
                } else {
                    console.log(error);   
                }
        });
    }
    
    // Acquires and access token and then passes it to the API call
    function passTokenToApi() {
        if (!accessToken) {
            getTokenRedirect(tokenRequest);
        } else {
            try {
                callApi(apiConfig.webApi, accessToken);
            } catch(error) {
                console.log(error); 
            }
        }
    }

    function editProfile() {


        const editProfileRequest = b2cPolicies.authorities.editProfile;
        editProfileRequest.loginHint = myMSALObj.getAccountByHomeId(accountId).username;

        myMSALObj.loginRedirect(editProfileRequest);
    }
    ```

## <a name="step-8-configure-the-web-api-location-and-scope"></a>Schritt 8: Konfigurieren des Speicherorts und Bereichs der Web-API

Damit Ihre SPA eine Web-API aufrufen kann, geben Sie den Speicherort und die [Bereiche](./configure-authentication-sample-spa-app.md#app-registration-overview) der Web-API an, die zum Autorisieren des Zugriffs auf die Web-API verwendet werden sollen.

Um den Speicherort und die Bereiche der Web-API zu konfigurieren, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Ordner *App* eine neue Datei namens *apiConfig.js*.
1. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *apiConfig.js* ein:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://contoso.onmicrosoft.com/tasks/tasks.read"],
        webApi: "https://mydomain.azurewebsites.net/tasks"
    };
    ```

1. Ersetzen Sie `contoso` durch den Namen Ihres Mandanten. Im Artikel [Konfigurieren der Authentifizierung in einer Single-Page-Beispielwebanwendung (SPA) mit Azure Active Directory B2C](./configure-authentication-sample-spa-app.md#step-22-configure-scopes) wird beschrieben, wie Sie den Namen des erforderlichen Bereichs finden.
1. Ersetzen Sie den Wert für `webApi` durch den Speicherort Ihres Web-API-Endpunkts.

## <a name="step-9-call-your-web-api"></a>Schritt 9: Aufrufen Ihrer Web-API

Definieren Sie die HTTP-Anforderung an Ihren API-Endpunkt. Die HTTP-Anforderung wird so konfiguriert, dass das Zugriffstoken, das mit *MSAL.js* erworben wurde, im `Authorization`-HTTP-Header in der Anforderung übergeben wird.

Der folgende Code definiert die HTTP-`GET`-Anforderung an den API-Endpunkt, wobei das Zugriffstoken im `Authorization`-HTTP-Header übergeben wird. Der API-Speicherort wird durch den Schlüssel `webApi` in der Datei *apiConfig.js* definiert. 

Um Ihre Web-API mithilfe des von Ihnen erworbenen Tokens aufzurufen, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Ordner *App* eine neue Datei namens *api.js*.
1. Fügen Sie der Datei *api.js* den folgenden Code hinzu:

    ```javascript
    function callApi(endpoint, token) {
        
        const headers = new Headers();
        const bearer = `Bearer ${token}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        logMessage('Calling web API...');
        
        fetch(endpoint, options)
        .then(response => response.json())
        .then(response => {

            if (response) {
            logMessage('Web API responded: ' + response.name);
            }
            
            return response;
        }).catch(error => {
            console.error(error);
        });
    }
    ```

## <a name="step-10-add-the-ui-elements-reference"></a>Schritt 10: Hinzufügen der Referenz für die Benutzeroberflächenelemente

Die SPA verwendet JavaScript, um die Benutzeroberflächenelemente zu steuern. So werden beispielsweise die Anmelde- und Abmeldeschaltflächen angezeigt und die Benutzer-ID-Tokenansprüche auf dem Bildschirm gerendert.

Um die Referenz für die Benutzeroberflächenelemente hinzuzufügen, gehen Sie folgendermaßen vor:

1. Erstellen Sie im Ordner *App* eine Datei namens *ui.js*.
1. Fügen Sie der Datei *ui.js* den folgenden Code hinzu:

    ```javascript
    // Select DOM elements to work with
    const signInButton = document.getElementById('signIn');
    const signOutButton = document.getElementById('signOut')
    const titleDiv = document.getElementById('title-div');
    const welcomeDiv = document.getElementById('welcome-div');
    const tableDiv = document.getElementById('table-div');
    const tableBody = document.getElementById('table-body-div');
    const editProfileButton = document.getElementById('editProfileButton');
    const callApiButton = document.getElementById('callApiButton');
    const response = document.getElementById("response");
    const label = document.getElementById('label');

    function welcomeUser(claims) {
        welcomeDiv.innerHTML = `Token claims: </br></br> ${claims}!`

        signInButton.classList.add('d-none');
        signOutButton.classList.remove('d-none');
        welcomeDiv.classList.remove('d-none');
        callApiButton.classList.remove('d-none');
    }

    function logMessage(s) {
        response.appendChild(document.createTextNode('\n' + s + '\n'));
    }
    ```
  
## <a name="step-11-run-your-spa-application"></a>Schritt 11: Ausführen der SPA

Führen Sie in der Befehlsshell die folgenden Befehle aus:

``` powershell
npm install  
npm ./index.js
```

1. Navigieren Sie zu https://localhost:6420. 
1. Wählen Sie **Anmeldung** aus.
1. Schließen Sie den Registrierungs- oder Anmeldevorgang ab.

Nachdem Sie sich erfolgreich authentifiziert haben, wird das analysierte ID-Token auf dem Bildschirm angezeigt. Wählen Sie `Call API` aus, um Ihren API-Endpunkt aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Codebeispiel](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)
* [Konfigurieren der Authentifizierung in einer Single-Page-Webanwendung (SPA) mit Azure AD B2C](enable-authentication-spa-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
