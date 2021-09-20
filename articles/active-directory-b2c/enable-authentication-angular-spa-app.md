---
title: Aktivieren der Authentifizierung in einer Angular-Anwendung mit den Bausteinen von Azure Active Directory B2C
description: Verwenden Sie die Bausteine von Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer Angular-Anwendung.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 100fed96c2a7adaa0d0934ab316db1d70bffdcb9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220822"
---
# <a name="enable-authentication-in-your-own-angular-application-by-using-azure-active-directory-b2c"></a>Aktivieren der Authentifizierung in einer eigenen Angular-Anwendung mit Azure Active Directory B2C

In diesem Artikel wird beschrieben, wie Sie Ihrer eigenen Angular-Single-Page-Webanwendung (SPA) die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen. Erfahren Sie, wie Sie eine Angular-Anwendung in die Authentifizierungsbibliothek [MSAL für Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) integrieren. 

Verwenden Sie diesen Artikel mit dem zugehörigen Artikel [Konfigurieren der Authentifizierung in einer beispielhaften Angular Single-Page-Anwendung](./configure-authentication-sample-angular-spa-app.md). Ersetzen Sie die beispielhafte Angular-App durch Ihre eigene Angular App. Nachdem Sie die Schritte in diesem Artikel ausgeführt haben, akzeptiert Ihre Anwendung Anmeldungen über Azure AD B2C.

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die Voraussetzungen und die Integrationsschritte unter [Konfigurieren der Authentifizierung in einer Angular Single-Page-Beispielwebanwendung](configure-authentication-sample-angular-spa-app.md).

## <a name="create-an-angular-app-project"></a>Erstellen eines Angular-App-Projekts

Sie können ein vorhandenes Angular-App-Projekt verwenden oder ein neues Projekt erstellen. Führen Sie den folgenden Befehl aus, um ein neues Projekt zu erstellen:

Die Befehle haben folgende Aufgaben:

1. Installieren die [Angular CLI](https://angular.io/cli) mit dem Paket-Manager npm
1. [Erstellen einen Angular Arbeitsbereich](https://angular.io/cli/new) mit Routingmodul Der App-Name ist `msal-angular-tutorial`. Sie können ihn in einen beliebigen gültigen Angular-App-Namen ändern, z. B. `contoso-car-service`.
1. Wechseln Sie zum Ordner mit dem App-Verzeichnis.

```
npm install -g @angular/cli 
ng new msal-angular-tutorial --routing=true --style=css --strict=false
cd msal-angular-tutorial
```

## <a name="install-the-dependencies"></a>Installieren der Abhängigkeiten

Führen Sie in Ihrer Befehlsshell die folgenden Befehle aus, um die Bibliotheken [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-browser) und [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) in Ihrer Anwendung zu installieren:

```
npm install @azure/msal-browser @azure/msal-angular
```

Installieren Sie die [Komponentenbibliothek „Angular Material“](https://material.angular.io/) (optional, für die Benutzeroberfläche).

```
npm install @angular/material @angular/cdk
```

## <a name="add-the-authentication-components"></a>Hinzufügen der Authentifizierungskomponenten

Der Beispielcode umfasst die folgenden Komponenten: 

|Komponente  |type  |BESCHREIBUNG  |
|---------|---------|---------|
| auth-config.ts| Konstanten | Diese Konfigurationsdatei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter und dem Web-API-Dienst. Die Angular-App nutzt diese Informationen, um eine Vertrauensstellung mit Azure AD B2C herzustellen, den Benutzer an- und abzumelden sowie Token zu beziehen und zu überprüfen. |
| app.module.ts| [Angular-Modul](https://angular.io/guide/architecture-modules)| Diese Komponente beschreibt, wie die Anwendungsteile zusammenpassen. Dies ist das Stammmodul, das zum Bootstrap und Öffnen der Anwendung verwendet wird. In dieser exemplarischen Vorgehensweise fügen Sie dem Modul *app.module.ts* einige Komponenten hinzu und starten die MSAL-Bibliothek mit dem MSAL-Konfigurationsobjekt.  |
| app-routing.module.ts | [Angular-Routingmodul](https://angular.io/tutorial/toh-pt5) | Diese Komponente ermöglicht die Navigation durch Interpretieren einer Browser-URL und Laden der entsprechenden Komponente. In dieser exemplarischen Vorgehensweise fügen Sie dem Routingmodul einige Komponenten hinzu und schützen Komponenten mit [MSAL Guard](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-guard.md). Nur autorisierte Benutzer können auf die geschützten Komponenten zugreifen.   |
| app.component.* | [Angular-Komponente](https://angular.io/guide/architecture-components) | Mit dem Befehl `ng new` wurde ein Angular-Projekt mit einer Stammkomponente erstellt. In dieser exemplarischen Vorgehensweise ändern Sie die *App*-Komponente so, dass sie die obere Navigationsleiste hostet. Die Navigationsleiste enthält verschiedene Schaltflächen, einschließlich Anmelde- und Abmeldeschaltflächen. Die `app.component.ts`-Klasse verarbeitet die Anmelde- und Abmeldeereignisse.  |
| home.component.* | [Angular-Komponente](https://angular.io/guide/architecture-components)|In dieser exemplarischen Vorgehensweise fügen Sie die Komponente *Home* hinzu, um die Startseite für anonymen Zugriff zu rendern. Diese Komponente veranschaulicht, wie überprüft wird, ob sich ein Benutzer angemeldet hat.  |
| profile.component.* | [Angular-Komponente](https://angular.io/guide/architecture-components) | In dieser exemplarischen Vorgehensweise fügen Sie die Komponente *profile* hinzu, um zu erfahren, wie die ID-Tokenansprüche gelesen werden. |
| webapi.component.* | [Angular-Komponente](https://angular.io/guide/architecture-components)| In dieser exemplarischen Vorgehensweise fügen Sie die Komponente *webapi* hinzu, um zu erfahren, wie eine Web-API aufgerufen wird. |

Um die folgenden Komponenten zu Ihrer App hinzuzufügen, führen Sie die folgenden Angular CLI-Befehle aus. Die `generate component`-Befehle haben folgende Aufgaben:

1. Erstellen einen Ordner für jede Komponente Der Ordner enthält die TypeScript-, HTML-, CSS- und Testdateien. 
1. Aktualisieren die Dateien `app.module.ts` und `app-routing.module.ts` mit Verweisen auf die neuen Komponenten 

```
ng generate component home
ng generate component profile
ng generate component webapi
```

## <a name="add-the-app-settings"></a>Hinzufügen der App-Einstellungen

Einstellungen für den Azure AD B2C-Identitätsanbieter und die Web-API werden in der Datei *auth-config.ts* gespeichert. Erstellen Sie im Ordner *src/app* eine Datei mit dem Namen *auth-config.ts* mit dem folgenden Code. Ändern Sie dann die Einstellungen wie unter [3.1 Konfigurieren des Angular-Beispiels](configure-authentication-sample-angular-spa-app.md#31-configure-the-angular-sample) beschrieben.

```typescript
import { LogLevel, Configuration, BrowserCacheLocation } from '@azure/msal-browser';

const isIE = window.navigator.userAgent.indexOf("MSIE ") > -1 || window.navigator.userAgent.indexOf("Trident/") > -1;
 
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn.
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
     cache: {
         cacheLocation: BrowserCacheLocation.LocalStorage,.
         storeAuthStateInCookie: isIE, 
     },
     system: {
         loggerOptions: {
            loggerCallback: (logLevel, message, containsPii) => {
                console.log(message);
             },
             logLevel: LogLevel.Verbose,
             piiLoggingEnabled: false
         }
     }
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
export const loginRequest = {
  scopes: []
};
```

## <a name="start-the-authentication-libraries"></a>Starten der Authentifizierungsbibliotheken

Öffentlichen Clientanwendungen sind nicht vertrauenswürdig genug, um Anwendungsgeheimnisse sicher aufzubewahren, deshalb enthalten sie keine geheimen Clientschlüssel. Öffnen Sie im Ordner *src/app* die Datei *app.module.ts*, und nehmen Sie die folgenden Änderungen vor:

1. Importieren Sie die MSAL Angular- und MSAL-Browserbibliotheken.
1. Importieren Sie das Azure AD B2C-Konfigurationsmodul.
1. Importieren Sie `HttpClientModule`. Der HTTP-Client dient zum Aufrufen von Web-APIs.
1. Importieren Sie den HTTP-Interceptor von Angular. MSAL verwendet den Interceptor, um das Bearertoken in den HTTP-Autorisierungsheader einzufügen.
1. Fügen Sie die wesentlichen Angular-Materialien hinzu.
1. Instanziieren Sie die MSAL mithilfe des öffentlichen Clientanwendungsobjekts mit mehreren Konten. Die MSAL-Initialisierung umfasst die Übergabe der folgenden Objekte:
    1. Des Konfigurationsobjekts für *auth-config.ts*
    1. Des Konfigurationsobjekts für den Routingschutz
    1. Des Konfigurationsobjekts für den MSAL-Interceptor Die Interceptor-Klasse bezieht automatisch Token für ausgehende Anforderungen, die die [HttpClient](https://angular.io/api/common/http/HttpClient)-Klasse von Angular für bekannte geschützte Ressourcen verwenden.
1. Konfigurieren Sie die [Angular-Anbieter](https://angular.io/guide/providers) `HTTP_INTERCEPTORS` und `MsalGuard`.  
1. Fügen Sie `MsalRedirectComponent` zum [Angular-Bootstrap](https://angular.io/guide/bootstrapping) hinzu.

Bearbeiten Sie im Ordner *src/app* die Datei *app.module.ts*, und nehmen Sie die folgenden Änderungen vor, die im folgenden Codeausschnitt gezeigt werden. Die Änderungen werden mit „Änderungen beginnen hier“ und „Änderungen enden hier“ gekennzeichnet. 

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

/* Changes start here. */
// Import MSAL and MSAL browser libraries. 
import { MsalGuard, MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';
import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

// Import the Azure AD B2C configuration 
import { msalConfig, protectedResources } from './auth-config';

// Import the Angular HTTP interceptor. 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ProfileComponent } from './profile/profile.component';
import { HomeComponent } from './home/home.component';
import { WebapiComponent } from './webapi/webapi.component';

// Add the essential Angular materials.
import { MatButtonModule } from '@angular/material/button';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatListModule } from '@angular/material/list';
import { MatTableModule } from '@angular/material/table';
/* Changes end here. */

@NgModule({
  declarations: [
    AppComponent,
    ProfileComponent,
    HomeComponent,
    WebapiComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    /* Changes start here. */
    // Import the following Angular materials. 
    MatButtonModule,
    MatToolbarModule,
    MatListModule,
    MatTableModule,
    // Import the HTTP client. 
    HttpClientModule,

    // Initiate the MSAL library with the MSAL configuration object
    MsalModule.forRoot(new PublicClientApplication(msalConfig),
      {
        // The routing guard configuration. 
        interactionType: InteractionType.Redirect,
        authRequest: {
          scopes: protectedResources.todoListApi.scopes
        }
      },
      {
        // MSAL interceptor configuration.
        // The protected resource mapping maps your web API with the corresponding app scopes. If your code needs to call another web API, add the URI mapping here.
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
        ])
      })
    /* Changes end here. */
  ],
  providers: [
    /* Changes start here. */
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalGuard
    /* Changes end here. */
  ],
  bootstrap: [
    AppComponent,
    /* Changes start here. */
    MsalRedirectComponent
    /* Changes end here. */
  ]
})
export class AppModule { }
```

## <a name="configure-routes"></a>Konfigurieren von Routen

Konfigurieren Sie in diesem Abschnitt die Routen Ihrer Angular-Anwendung. Wenn ein Benutzer einen Link auf der Seite auswählt, um sich innerhalb Ihrer Single-Page-Webanwendung zu bewegen, oder eine URL in die Adressleiste eingibt, ordnen die Routen die URL einer Angular-Komponente zu. Die Angular-Routingschnittstelle [canActivate](https://angular.io/api/router/CanActivate) verwendet MSAL Guard, um zu prüfen, ob der Benutzer angemeldet ist. Wenn der Benutzer nicht angemeldet ist, leitet MSAL den Benutzer zur Authentifizierung an Azure AD B2C weiter.

Bearbeiten Sie im Ordner *src/app* die Datei *app-routing.module.ts*, und nehmen Sie die folgenden Änderungen vor, die im folgenden Codeausschnitt gezeigt werden. Die Änderungen werden mit „Änderungen beginnen hier“ und „Änderungen enden hier“ gekennzeichnet. 

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';
import { WebapiComponent } from './webapi/webapi.component';

const routes: Routes = [
  /* Changes start here. */
  {
    path: 'profile',
    component: ProfileComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    path: 'webapi',
    component: WebapiComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    // The home component allows anonymous access
    path: '',
    component: HomeComponent
  }
  /* Changes end here. */
];


@NgModule({
  /* Changes start here. */
  // Replace the following line with the next one
  //imports: [RouterModule.forRoot(routes)],
  imports: [RouterModule.forRoot(routes, {
    initialNavigation:'enabled'
  })],
  /* Changes end here. */
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## <a name="add-the-sign-in-and-sign-out-buttons"></a>Hinzufügen der Schaltflächen zum An- und Abmelden

In diesem Abschnitt fügen Sie der *app*-Komponente die Schaltflächen zum An- und Abmelden hinzu. Öffnen Sie im Ordner *src/app* die Datei *app.component.ts*, und nehmen Sie die folgenden Änderungen vor:

1. Importieren Sie die erforderlichen Komponenten.
1. Ändern Sie die Klasse so, dass die [OnInit](https://angular.io/api/core/OnInit)-Methode implementiert wird. Die `OnInit`-Methode abonniert [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) und das beobachtbare Ereignis `inProgress$`. Verwenden Sie dieses Ereignis, um den Status von Benutzerinteraktionen zu erfahren, insbesondere um zu prüfen, ob die Interaktionen abgeschlossen sind. 

   Bevor Sie mit dem MSAL-Kontoobjekt interagieren, prüfen Sie, ob die Eigenschaft `InteractionStatus` den Wert `InteractionStatus.None` zurückgibt. Das Ereignis `subscribe` ruft die `setLoginDisplay`-Methode auf, um zu prüfen, ob der Benutzer authentifiziert ist.
1. Fügen Sie Klassenvariablen hinzu.
1. Fügen Sie die `login`-Methode hinzu, die den Autorisierungsablauf startet.
1. Fügen Sie die `logout`-Methode hinzu, die den Benutzer abmeldet. 
1. Fügen Sie die `setLoginDisplay`-Methode hinzu, die prüft, ob der Benutzer authentifiziert ist.
1. Fügen Sie die [ngOnDestroy](https://angular.io/api/core/OnDestroy)-Methode hinzu, um das abonnierte Ereignis `inProgress$` zu bereinigen.

Nach den Änderungen sollte Ihr Code wie der folgende Codeausschnitt aussehen:

```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

/* Changes start here. */
export class AppComponent implements OnInit{
  title = 'msal-angular-tutorial';
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { 
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
  /* Changes end here. */
}
```

Öffnen Sie im Ordner *src/app* die Datei *app.component.html*, und nehmen Sie die folgenden Änderungen vor:

1. Fügen Sie einen Link zu den Profil- und Web-API-Komponenten hinzu.
1. Fügen Sie die Anmeldeschaltfläche mit auf die `login()`-Methode festgelegtem Klickereignisattribut hinzu. Diese Schaltfläche wird nur angezeigt, wenn die `loginDisplay`-Klassenvariable `false` ist.
1. Fügen Sie die Abmeldeschaltfläche mit auf die `logout()`-Methode festgelegtem Klickereignisattribut hinzu. Diese Schaltfläche wird nur angezeigt, wenn die `loginDisplay`-Klassenvariable `true` ist.
1. Fügen Sie das Element [router-outlet](https://angular.io/api/router/RouterOutlet) hinzu. 

Nach den Änderungen sollte Ihr Code wie der folgende Codeausschnitt aussehen:

```html
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>
  <a mat-button [routerLink]="['webapi']">Web API</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <router-outlet></router-outlet>
</div>
```

Aktualisieren Sie optional die Datei *app.component.css* mit dem folgenden CSS-Ausschnitt: 

```css
.toolbar-spacer {
    flex: 1 1 auto;
  }

  a.title {
    color: white;
  }
```

## <a name="handle-the-app-redirects"></a>Behandeln der App-Umleitungen 

Bei Verwendung von Umleitungen mit MSAL müssen Sie die Anweisung [app-redirect](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/redirects.md) in die Datei *index.html* einfügen. Bearbeiten Sie im Ordner *src* die Datei *index.html*, wie im folgenden Codeausschnitt gezeigt:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MsalAngularTutorial</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <!-- Changes start here -->
  <app-redirect></app-redirect>
  <!-- Changes end here -->
</body>
</html>
```

## <a name="set-app-css-optional"></a>Festlegen von CSS für die App (optional)

Aktualisieren Sie im Ordner */src* die Datei *styles.css* mit dem folgenden CSS-Ausschnitt: 

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
.container { margin: 1%; }
```

> [!TIP]
> An diesem Punkt können Sie Ihre App ausführen und den Ablauf der Anmeldung testen. Informationen zum Ausführen Ihrer App finden Sie im Abschnitt [Ausführen der Angular-Anwendung](#run-the-angular-application).

## <a name="check-if-a-user-is-authenticated"></a>Prüfen, ob ein Benutzer authentifiziert ist

Die Datei *home.component* veranschaulicht, wie die Authentifizierung des Benutzers überprüft werden kann. Aktualisieren Sie im Ordner *src/app/home* die Datei *home.component.ts* mit dem folgenden Codeausschnitt. 

Der Code:

1. Abonniert [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) und die beobachtbaren Ereignisse `msalSubject$` und `inProgress$`. 
1. Sorgt dafür, dass das Ereignis `msalSubject$` das Ergebnis der Authentifizierung in die Browserkonsole schreibt.
1. Sorgt dafür, dass das Ereignis `inProgress$` prüft, ob ein Benutzer authentifiziert ist. Die `getAllAccounts()`-Methode gibt ein oder mehrere Objekte zurück.


```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  loginDisplay = false;

  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {
    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        this.setLoginDisplay();
      })
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }
}
```

Aktualisieren Sie im Ordner *src/app/home* die Datei *home.component.html* mit dem folgenden HTML-Ausschnitt. Die Anweisung [*ngIf](https://angular.io/api/common/NgIf) prüft die Klassenvariable `loginDisplay`, um die Begrüßungsmeldungen ein- oder auszublenden.

```html
<div *ngIf="!loginDisplay">
    <p>Please sign-in to see your profile information.</p>
</div>

<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <p>Request your profile information by clicking Profile above.</p>
</div>
```

## <a name="read-the-id-token-claims"></a>Lesen der ID-Tokenansprüche

Die Datei *profile.component* veranschaulicht, wie der Zugriff auf die ID-Tokenansprüche des Benutzers erfolgt. Aktualisieren Sie im Ordner *src/app/profile* die Datei *profile.component.ts* mit dem folgenden Codeausschnitt. 

Der Code:

1. Importiert die erforderlichen Komponenten.
1. Abonniert [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) und das beobachtbare Ereignis `inProgress$`. Das Ereignis lädt das Konto und liest die ID-Tokenansprüche.
1. Sorgt dafür, dass die `checkAndSetActiveAccount`-Methode das aktive Konto prüft und festlegt. Diese Aktion ist üblich, wenn die App mit mehreren Azure AD B2C-Benutzerflows oder benutzerdefinierten Richtlinien interagiert.
1. Sorgt dafür, dass die `getClaims`-Methode die ID-Tokenansprüche aus dem aktiven MSAL-Kontoobjekt abruft. Die Methode fügt dann die Ansprüche dem `dataSource`-Array hinzu. Das Array wird für den Benutzer mit der Vorlagenbindung der Komponente gerendert.

```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css']
})

export class ProfileComponent implements OnInit {
  displayedColumns: string[] = ['claim', 'value'];
  dataSource: Claim[] = [];
  private readonly _destroying$ = new Subject<void>();
  
  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) =>  status === InteractionStatus.None || status === InteractionStatus.HandleRedirect),
        takeUntil(this._destroying$)
      )
      .subscribe(() => {
        this.checkAndSetActiveAccount();
        this.getClaims(this.authService.instance.getActiveAccount()?.idTokenClaims)
      })
  }

  checkAndSetActiveAccount() {

    let activeAccount = this.authService.instance.getActiveAccount();

    if (!activeAccount && this.authService.instance.getAllAccounts().length > 0) {
      let accounts = this.authService.instance.getAllAccounts();
      this.authService.instance.setActiveAccount(accounts[0]);
    }
  }

  getClaims(claims: any) {

    let list: Claim[]  =  new Array<Claim>();

    Object.keys(claims).forEach(function(k, v){
      
      let c = new Claim()
      c.id = v;
      c.claim = k;
      c.value =  claims ? claims[k]: null;
      list.push(c);
    });
    this.dataSource = list;

  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}

export class Claim {
  id: number;
  claim: string;
  value: string;
}
``` 

Aktualisieren Sie im Ordner *src/app/profile* die Datei *profile.component.html* mit dem folgenden HTML-Ausschnitt: 

```html
<h1>ID token claims:</h1>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">

  <!-- Claim Column -->
  <ng-container matColumnDef="claim">
    <th mat-header-cell *matHeaderCellDef> Claim </th>
    <td mat-cell *matCellDef="let element"> {{element.claim}} </td>
  </ng-container>

  <!-- Value Column -->
  <ng-container matColumnDef="value">
    <th mat-header-cell *matHeaderCellDef> Value </th>
    <td mat-cell *matCellDef="let element"> {{element.value}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
</table>
```

## <a name="call-a-web-api"></a>Aufrufen einer Web-API

Zum Aufrufen einer [tokenbasierten Autorisierungs-Web-API](enable-authentication-web-api.md) benötigt die App ein gültiges Zugriffstoken. Der Anbieter [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) bezieht automatisch Token für ausgehende Anforderungen, die die [HttpClient](https://angular.io/api/common/http/HttpClient)-Klasse von Angular für bekannte geschützte Ressourcen verwenden.

> [!IMPORTANT]
> Die MSAL-Initialisierungsmethode (in der Klasse `app.module.ts`) ordnet geschützte Ressourcen, wie z. B. Web-APIs, mithilfe des `protectedResourceMap`-Objekts den erforderlichen App-Bereichen zu. Wenn Ihr Code eine andere Web-API aufrufen muss, fügen Sie den Web-API-URI, die HTTP-Methode der Web-API und die entsprechenden Bereiche zum `protectedResourceMap`-Objekt hinzu. Weitere Informationen finden Sie unter [Geschützte Ressourcenzuordnung](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/master/lib/msal-angular/docs/v2-docs/msal-interceptor.md#protected-resource-map).


Wenn das [HttpClient](https://angular.io/api/common/http/HttpClient)-Objekt eine Web-API aufruft, führt der Anbieter [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) die folgenden Schritte aus:

1. Beziehen eines Zugriffstokens mit den erforderlichen Berechtigungen (Bereichen) für den Web-API-Endpunkt 
1. Übergeben des Zugriffstokens als Bearertoken im Autorisierungsheader der HTTP-Anforderung in folgendem Format:

   ```http
   Authorization: Bearer <access-token>
   ```

Die Datei *webapi.component* veranschaulicht das Aufrufen einer Web-API. Aktualisieren Sie im Ordner *src/app/webapi* die Datei *webapi.component.ts* mit dem folgenden Codeausschnitt.  

Der Code:

1. Verwendet die [HttpClient](https://angular.io/guide/http)-Klasse von Angular zum Aufrufen der Web-API.
1. Liest das `protectedResources.todoListApi.endpoint`-Element der `auth-config`-Klasse. Dieses Element gibt den URI der Web-API an. Basierend auf dem URI der Web-API bezieht der MSAL-Interceptor ein Zugriffstoken mit den entsprechenden Bereichen. 
1. Ruft das Profil aus der Web-API ab und legt die Klassenvariable `profile` fest.

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { protectedResources } from '../auth-config';

type ProfileType = {
  name?: string
};

@Component({
  selector: 'app-webapi',
  templateUrl: './webapi.component.html',
  styleUrls: ['./webapi.component.css']
})
export class WebapiComponent implements OnInit {
  todoListEndpoint: string = protectedResources.todoListApi.endpoint;
  profile!: ProfileType;

  constructor(
    private http: HttpClient
  ) { }

  ngOnInit() {
    this.getProfile();
  }

  getProfile() {
    this.http.get(this.todoListEndpoint)
      .subscribe(profile => {
        this.profile = profile;
      });
  }
}
```

Aktualisieren Sie im Ordner *src/app/webapi* die Datei *webapi.component.html* mit dem folgenden HTML-Ausschnitt. Die Vorlage der Komponente rendert den Namen, den die Web-API zurückgibt. Am unteren Rand der Seite rendert die Vorlage die Web-API-Adresse.

```html
<h1>The web API returns:</h1>
<div>
    <p><strong>Name: </strong> {{profile?.name}}</p>
</div>

<div class="footer-text">
    Web API: {{todoListEndpoint}}
</div>
```

Aktualisieren Sie optional die Datei *webapi.component.css* mit dem folgenden CSS-Ausschnitt: 

```css
.footer-text {
    position: absolute;
    bottom: 50px;
    color: gray;
}
```

## <a name="run-the-angular-application"></a>Ausführen der Angular-Anwendung


Führen Sie den folgenden Befehl aus:

```console
npm start
```

Im Konsolenfenster wird die Portnummer angezeigt, unter der die Anwendung gehostet wird.

```console
Listening on port 4200...
```

> [!TIP]
> Alternativ können Sie den Befehl `npm start` auch mit dem [Visual Studio Code-Debugger](https://code.visualstudio.com/docs/editor/debugging) ausführen. Mit dem Debugger können Sie Ihre Bearbeitungs-, Kompilierungs- und Debugschleife beschleunigen.

Navigieren Sie in Ihrem Browser zu `http://localhost:4200`, um die Anwendung anzuzeigen.


## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Authentifizierungsoptionen in einer eigenen Angular-Anwendung mit Azure AD B2C](enable-authentication-angular-spa-app-options.md)
* [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
