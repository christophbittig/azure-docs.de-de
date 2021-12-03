---
title: Anmelden und Abmelden bei einer Single-Page-Webanwendung
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine Single-Page-Webanwendung (Anmeldung) erstellen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 2fa29b8cfd15ccee133ef416d729c1807aa0a400
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017918"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Single-Page-Webanwendung: An- und Abmeldung

Hier erfahren Sie, wie Sie die Anmeldung zum Code Ihrer Single-Page-Webanwendung hinzufügen.

Bevor Sie die Token für den Zugriff auf APIs in Ihrer Anwendung abrufen können, benötigen Sie einen authentifizierten Benutzerkontext. Sie können Benutzer in MSAL.js auf zwei Arten bei Ihrer Anwendung anmelden:

* [Popupfenster](#sign-in-with-a-pop-up-window) mithilfe der `loginPopup`-Methode
* [Umleiten](#sign-in-with-redirect) mithilfe der `loginRedirect`-Methode

Sie können auch optional diejenigen Bereiche der APIs übergeben, für die der Benutzer zum Zeitpunkt der Anmeldung seine Zustimmung erteilen muss.

> [!NOTE]
> Wenn Ihre Anwendung bereits Zugriff auf einen authentifizierten Benutzerkontext oder das ID-Token hat, können Sie den Anmeldeschritt überspringen und Token direkt abrufen. Weitere Informationen hierzu finden Sie unter [SSO-Anmeldung ohne MSAL.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Auswählen zwischen einem Popupfenster oder einer Umleitung

Die Entscheidung für ein Popupfenster oder eine Umleitung hängt von Ihrem Anwendungsfluss ab:

* Wenn Sie nicht möchten, dass Benutzer während der Authentifizierung Ihre Hauptanwendungsseite verlassen, wird die Popupmethode empfohlen. Da die Authentifizierungsumleitung in einem Popupfenster erfolgt, wird der Status der Hauptanwendung beibehalten.

* Wenn für Benutzer Browsereinschränkungen oder Richtlinien gelten, bei denen Popupfenster deaktiviert sind, können Sie die Umleitungsmethode verwenden. Wenn Sie Internet Explorer als Browser nutzen, verwenden Sie die Umleitungsmethode, weil [in Internet Explorer bekannte Probleme mit Popupfenstern bestehen](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups).

## <a name="sign-in-with-a-pop-up-window"></a>Anmelden mit einem Popupfenster

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        accountId = loginResponse.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

Mit dem MSAL Angular-Wrapper können Sie bestimmte Routen in Ihrer Anwendung sichern, indem Sie `MsalGuard` zu der Routendefinition hinzufügen. Dieser Wächter ruft die Methode für die Anmeldung auf, wenn auf diese Route zugegriffen wird.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
    {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [
            MsalGuard
        ]
    },
    {
        path: '',
        component: HomeComponent
    }
];

@NgModule({
    imports: [RouterModule.forRoot(routes, { useHash: false })],
    exports: [RouterModule]
})
export class AppRoutingModule { }
```

Legen Sie zur Verwendung der Methode mit Popupfenster in der Guard-Konfiguration die `interactionType`-Konfiguration auf `InteractionType.Popup` fest. Sie können wie folgt auch die Bereiche übergeben, die eine Zustimmung erfordern:

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Popup, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
Mit dem MSAL Angular-Wrapper können Sie bestimmte Routen in Ihrer Anwendung sichern, indem Sie `MsalGuard` zu der Routendefinition hinzufügen. Dieser Wächter ruft die Methode für die Anmeldung auf, wenn auf diese Route zugegriffen wird.
```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Aktivieren Sie für ein Popupfenster die Konfigurationsoption `popUp`. Sie können wie folgt auch die Bereiche übergeben, die eine Zustimmung erfordern:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```

# <a name="react"></a>[React](#tab/react)

Mit dem MSAL React-Wrapper können Sie bestimmte Komponenten schützen, indem Sie diese in der Komponente `MsalAuthenticationTemplate` umschließen. Diese Komponente ruft die Anmeldung auf, wenn ein Benutzer noch nicht angemeldet ist, oder rendert andernfalls untergeordnete Komponenten.

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Popup}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

Sie können auch die `@azure/msal-browser`-APIs direkt verwenden, um eine Anmeldung in Kombination mit der Komponente `AuthenticatedTemplate` und/oder `UnauthenticatedTemplate` aufzurufen, damit bestimmte Inhalte für angemeldete bzw. abgemeldete Benutzer gerendert werden. Dies ist der empfohlene Ansatz, wenn Sie eine Anmeldung als Ergebnis einer Benutzerinteraktion (wie dem Klicken auf eine Schaltfläche) aufrufen müssen.

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginPopup();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-in-with-redirect"></a>Anmelden mit einer Umleitung

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    if (response !== null) {
        accountId = response.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    } else {
        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();

        if (currentAccounts.length === 0) {
            // no accounts signed-in, attempt to sign a user in
            myMsal.loginRedirect(loginRequest);
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            accountId = currentAccounts[0].homeAccountId;
        }
    }
}

myMsal.handleRedirectPromise().then(handleResponse);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

Die Umleitungsmethoden geben aufgrund der Navigation weg von der Hauptanwendung keine Zusage zurück. Registrieren Sie Erfolgs- und Fehlerrückrufe vor dem Abrufen der Umleitungsmethoden, um die zurückgegebenen Token zu verarbeiten und auf sie zuzugreifen.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

Hier entspricht der Code dem zuvor im Abschnitt „Anmelden mit einem Popupfenster“ beschriebenen Code, mit der Ausnahme, dass `interactionType` für die MsalGuard-Konfiguration auf `InteractionType.Redirect` festgelegt ist und für `MsalRedirectComponent` ein Bootstrapping-Vorgang ausgeführt wird, um Umleitungen zu verarbeiten.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

Hier entspricht der Code dem zuvor im Abschnitt über das Anmelden mit einem Popupfenster beschriebenen Code. Der Standardfluss wird umgeleitet.

# <a name="react"></a>[React](#tab/react)

Mit dem MSAL React-Wrapper können Sie bestimmte Komponenten schützen, indem Sie diese in der Komponente `MsalAuthenticationTemplate` umschließen. Diese Komponente ruft die Anmeldung auf, wenn ein Benutzer noch nicht angemeldet ist, oder rendert andernfalls untergeordnete Komponenten.

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Redirect}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

Sie können auch die `@azure/msal-browser`-APIs direkt verwenden, um eine Anmeldung in Kombination mit der Komponente `AuthenticatedTemplate` und/oder `UnauthenticatedTemplate` aufzurufen, damit bestimmte Inhalte für angemeldete bzw. abgemeldete Benutzer gerendert werden. Dies ist der empfohlene Ansatz, wenn Sie eine Anmeldung als Ergebnis einer Benutzerinteraktion (wie dem Klicken auf eine Schaltfläche) aufrufen müssen.

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginRedirect();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-pop-up-window"></a>Abmelden mit einem Popupfenster

MSAL.js v2 bietet eine `logoutPopup`-Methode, die den Cache im Browserspeicher löscht und ein Popupfenster für die Abmeldeseite von Azure Active Directory (Azure AD) öffnet. Nach der Abmeldung leitet Azure AD das Popup zurück zu Ihrer Anwendung, und MSAL.js schließt das Popup.

Durch Festlegen von `postLogoutRedirectUri` können Sie den URI konfigurieren, zu dem Azure AD nach der Abmeldung umleiten soll. Dieser URI sollte in Ihrer Anwendungsregistrierung als Umleitungs-URI registriert sein.

Sie können `logoutPopup` auch so konfigurieren, dass das Hauptfenster nach Abschluss der Abmeldung auf eine andere Seite (z. B. auf die Startseite oder Anmeldeseite) umgeleitet wird, indem Sie `mainWindowRedirectUri` als Teil der Anforderung übergeben.

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", // defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId),
    mainWindowRedirectUri: "your_app_main_window_redirect_uri"
}

await myMsal.logoutPopup(logoutRequest);
```
# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

Die Abmeldung mit einem Popupfenster wird in MSAL.js v1 nicht unterstützt.

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutPopup({
        mainWindowRedirectUri: "/"
    });
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

Die Abmeldung mit einem Popupfenster wird in MSAL Angular v1 nicht unterstützt.

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        mainWindowRedirectUri: "your_app_main_window_redirect_uri",
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutPopup(logoutRequest);
}

// SignOutButton Component returns a button that invokes a popup logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-redirect"></a>Abmelden mit einer Umleitung

MSAL.js bietet in v1 eine `logout`-Methode und in v2 eine `logoutRedirect`-Methode, die den Cache im Browserspeicher löscht und das Fenster zur Abmeldeseite von Azure Active Directory (Azure AD) umleitet. Nach der Abmeldung leitet Azure AD standardmäßig zu der Seite zurück, die die Abmeldung aufgerufen hat.

Durch Festlegen von `postLogoutRedirectUri` können Sie den URI konfigurieren, zu dem nach der Anmeldung umgeleitet werden soll. Dieser URI sollte in Ihrer Anwendungsregistrierung als Umleitungs-URI registriert sein.

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId)
}

myMsal.logoutRedirect(logoutRequest);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutRedirect();
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})
// In app.component.ts
this.authService.logout();
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutRedirect(logoutRequest);
}

// SignOutButton Component returns a button that invokes a redirect logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens für die App](scenario-spa-acquire-token.md).
