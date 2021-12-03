---
title: 'Tutorial: Erstellen einer Angular-App mit Autorisierungscodeflow, die Microsoft Identity Platform für die Authentifizierung verwendet | Azure'
titleSuffix: Microsoft identity platform
description: In diesem Tutorial erstellen Sie eine Angular-Single-Page-Webanwendung (SPA) mit Autorisierungscodeflow, die Microsoft Identity Platform zum Anmelden von Benutzern und zum Abrufen eines Zugriffstokens verwendet, um im Namen der Benutzer die Microsoft Graph-API aufzurufen.
services: active-directory
author: jo-arroyo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/14/2021
ms.author: joarroyo
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 398117120e560db00be97a344feed09351a26258
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050335"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa-using-auth-code-flow"></a>Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API über eine Angular-Single-Page-Webanwendung (SPA) unter Verwendung des Autorisierungscodeflows

In diesem Tutorial wird eine Angular-Single-Page-Webanwendung (Single-Page Application, SPA) erstellt, die Benutzer anmeldet und die Microsoft Graph-API über den Autorisierungscodeflow mit PKCE aufruft. Die von Ihnen erstellte SPA verwendet die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Angular v2.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Erstellen eines Angular-Projekts mit `npm`
> * Registrieren der Anwendung im Azure-Portal
> * Hinzufügen von Code zur Unterstützung der Benutzeranmeldung und -abmeldung
> * Hinzufügen von Code zum Aufrufen der Microsoft Graph-API
> * Testen der App

MSAL Angular v2 bringt Verbesserungen gegenüber MSAL Angular v1 mit sich und unterstützt anstelle des impliziten Genehmigungsflows den Autorisierungscodeflow. Der implizite Flow wird von MSAL Angular v2 **NICHT** unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* [Node.js](https://nodejs.org/en/download/) zum Ausführen eines lokalen Webservers
* [Visual Studio Code](https://code.visualstudio.com/download) oder einen anderen Editor zum Bearbeiten von Projektdateien

## <a name="how-the-sample-app-works"></a>Funktionsweise der Beispiel-App

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramm, das den Autorisierungscodefluss in einer Single-Page-Webanwendung darstellt.":::

Die in diesem Tutorial erstellte Beispiel-App ermöglicht einer Angular-SPA das Abfragen der Microsoft Graph-API oder einer Web-API, die von der Microsoft Identity Platform ausgegebene Token akzeptiert. Dabei wird die Microsoft Authentication Library (MSAL) für Angular v2 verwendet – ein Wrapper der MSAL.js v2-Bibliothek. Mit MSAL Angular können auf Angular 9+ basierende Anwendungen sowohl Unternehmensbenutzer über Azure Active Directory (Azure AD) als auch Benutzer von Microsoft-Konten sowie Benutzer mit Social Media-Identität (z. B. Facebook, Google und LinkedIn) authentifizieren. Die Bibliothek ermöglicht es Anwendungen zudem, Zugriff auf Microsoft Cloud Services und Microsoft Graph zu erhalten.

In diesem Szenario wird nach der Benutzeranmeldung ein Zugriffstoken angefordert und den HTTP-Anforderungen über den Autorisierungsheader hinzugefügt. Die MSAL übernimmt die Tokenbeschaffung und -erneuerung.

### <a name="libraries"></a>Bibliotheken

In diesem Tutorial werden die folgenden Bibliotheken verwendet:

|Bibliothek|Beschreibung|
|---|---|
|[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)|Microsoft Authentication Library für JavaScript: Angular-Wrapper|
|[MSAL-Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Microsoft Authentication Library für JavaScript v2: Browserpaket |

Den Quellcode für sämtliche MSAL.js-Bibliotheken finden Sie auf GitHub im Repository [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

## <a name="create-your-project"></a>Erstellen Ihres Projekts

Öffnen Sie nach der Installation von [Node.js](https://nodejs.org/en/download/) ein Terminalfenster, und führen Sie die folgenden Befehle aus, um eine neue Angular-Anwendung zu generieren:

```bash
npm install -g @angular/cli                         # Install the Angular CLI
ng new msal-angular-tutorial --routing=true --style=css --strict=false    # Generate a new Angular app
cd msal-angular-tutorial                            # Change to the app directory
npm install @angular/material @angular/cdk          # Install the Angular Material component library (optional, for UI)
npm install @azure/msal-browser @azure/msal-angular # Install MSAL Browser and MSAL Angular in your application
ng generate component home                          # To add a home page
ng generate component profile                       # To add a profile page
```

## <a name="register-your-application"></a>Anwendung registrieren

Befolgen Sie die Anleitung zum [Registrieren einer Single-Page-Webanwendung](./scenario-spa-app-registration.md) im Azure-Portal.

Notieren Sie sich auf der Seite **Übersicht** Ihres Registrierungsvorgangs den Wert von **Anwendungs-ID (Client)** zur späteren Verwendung.

Registrieren Sie den Wert für Ihren **Umleitungs-URI** als **http://localhost:4200/** und den Typ als „SPA“.

## <a name="configure-the-application"></a>Konfigurieren der Anwendung

1. Bearbeiten Sie im Ordner *src/app* die Datei *app.module.ts*, und fügen Sie `MsalModule` und `MsalInterceptor` zu `imports` sowie die Konstante `isIE` hinzu. Ihr Code sollte folgendermaßen aussehen:

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

    Ersetzen Sie die folgenden Werte:

    |Wertname|Info|
    |---------|---------|
    |Enter_the_Application_Id_Here|Der Wert für **Anwendungs-ID (Client)** auf der Seite **Übersicht** Ihrer Anwendungsregistrierung. |
    |Enter_the_Cloud_Instance_Id_Here|Die Instanz der Azure-Cloud. Geben Sie für die Azure-Hauptcloud oder für die globale Azure-Cloud **https://login.microsoftonline.com** ein. Informationen zu nationalen Clouds (z. B. für China) finden Sie unter [Nationale Clouds](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Legen Sie eine der folgenden Optionen fest: Wenn Ihre Anwendung *Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die Verzeichnis-ID (Mandant) oder den Mandantennamen (z. B. **contoso.microsoft.com**). Wenn Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch **Organisationen**. Wenn Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten* unterstützt, ersetzen Sie diesen Wert durch **common**. Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch **consumers**. |
    |Enter_the_Redirect_Uri_Here|Ersetzen Sie diesen Wert durch **http://localhost:4200** .|

    Weitere Informationen zu den verfügbaren konfigurierbaren Optionen finden Sie unter [Initialisieren von Clientanwendungen](msal-js-initializing-client-applications.md).

2. Fügen Sie in *src/app/app-routing.module.ts* Routen zu den Komponenten „home“ und „profile“ hinzu. Ihr Code sollte wie folgt aussehen:

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

## <a name="replace-base-ui"></a>Ersetzen der Basisbenutzeroberfläche

1. Ersetzen Sie in *src/app/app.component.html* den Platzhaltercode durch Folgendes:

    ```HTML
    <mat-toolbar color="primary">
      <a class="title" href="/">{{ title }}</a>

      <div class="toolbar-spacer"></div>

      <a mat-button [routerLink]="['profile']">Profile</a>

      <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>

    </mat-toolbar>
    <div class="container">
      <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
      <router-outlet *ngIf="!isIframe"></router-outlet>
    </div>
    ```

2. Fügen Sie *src/app/app.module.ts* Materialmodule hinzu. Ihr App-Modul (`AppModule`) sollte wie folgt aussehen:

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, 
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

3. (OPTIONAL) Fügen Sie *src/style.css* CSS hinzu:

    ```css
    @import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

    html, body { height: 100%; }
    body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
    .container { margin: 1%; }
    ```

4. (OPTIONAL) Fügen Sie *src/app/app.component.css* CSS hinzu:

    ```css
    .toolbar-spacer {
        flex: 1 1 auto;
      }
      
      a.title {
        color: white;
      }
    ```

## <a name="sign-in-a-user"></a>Anmelden eines Benutzers

Fügen Sie den Code aus den folgenden Abschnitten hinzu, um die Anmeldung mittels Popupfenster oder Vollbildumleitung aufzurufen: 

### <a name="sign-in-using-pop-ups"></a>Anmelden mit Hilfe von Popups

1. Ändern Sie den Code in *src/app/app.component.ts* wie folgt, um einen Benutzer unter Verwendung eines Popupfensters anzumelden:

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginPopup()
          .subscribe({
            next: (result) => {
              console.log(result);
              this.setLoginDisplay();
            },
            error: (error) => console.log(error)
          });
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

> [!NOTE]
> Aufgrund eines [bekannten Problems](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md) bei der Behandlung von Popupfenstern durch Internet Explorer wird für Microsoft Internet Explorer im weiteren Verlauf dieses Tutorials die Methode `loginRedirect` verwendet.

### <a name="sign-in-using-redirects"></a>Anmelden mittels Umleitungen

1. Aktualisieren Sie *src/app/app.module.ts*, um ein Bootstrapping für `MsalRedirectComponent` durchzuführen. Dies ist eine dedizierte Umleitungskomponente zur Behandlung von Umleitungen. Ihr Code sollte jetzt folgendermaßen aussehen:

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular'; // Updated import
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent, MsalRedirectComponent] // MsalRedirectComponent bootstrapped here
    })
    export class AppModule { }
    ```

2. Fügen Sie *src/index.html* den Selektor `<app-redirect>` hinzu. Dieser Selektor wird von `MsalRedirectComponent` verwendet. *src/index.html* sollte wie folgt aussehen:

    ```HTML
    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>msal-angular-tutorial</title>
      <base href="/">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <link rel="icon" type="image/x-icon" href="favicon.ico">
    </head>
    <body>
      <app-root></app-root>
      <app-redirect></app-redirect>
    </body>
    </html>
    ```

3. Ersetzen Sie den Code in *src/app/app.component.ts* durch Folgendes, um einen Benutzer unter Verwendung einer Vollbildumleitung anzumelden:

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

4. Ersetzen Sie vorhandenen Code in *src/app/home/home.component.ts*, um das Ereignis `LOGIN_SUCCESS` zu abonnieren. Dies ermöglicht den Zugriff auf das Ergebnis der erfolgreichen Anmeldung mit Umleitung. Ihr Code sollte folgendermaßen aussehen:

    ```javascript
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
      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
      }
    }
    ```

## <a name="conditional-rendering"></a>Bedingtes Rendern

Wenn bestimmte Komponenten der Benutzeroberfläche nur für authentifizierte Benutzer gerendert werden sollen, muss `MsalBroadcastService` für die Komponenten abonniert werden, um zu prüfen, ob Benutzer angemeldet wurden und ob die Interaktion abgeschlossen ist.

1. Fügen Sie `MsalBroadcastService` zu *src/app/app.component.ts* hinzu, und abonnieren Sie das observable-Element `inProgress$`, um vor dem Rendern der Benutzeroberfläche zu überprüfen, ob die Interaktion abgeschlossen und ein Konto angemeldet ist. Ihr Code sollte jetzt folgendermaßen aussehen:

    ```javascript
    import { Component, OnInit, OnDestroy } from '@angular/core';
    import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
    import { InteractionStatus } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

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
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

2. Aktualisieren Sie den Code in *src/app/home/home.component.ts*, um ebenfalls zu überprüfen, ob die Interaktion abgeschlossen ist, bevor die Benutzeroberfläche aktualisiert wird. Ihr Code sollte jetzt folgendermaßen aussehen:

    ```javascript
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

3. Ersetzen Sie den Code in *src/app/home/home.component.html* durch die folgenden bedingten Anzeigen:

    ```HTML
    <div *ngIf="!loginDisplay">
        <p>Please sign-in to see your profile information.</p>
    </div>

    <div *ngIf="loginDisplay">
        <p>Login successful!</p>
        <p>Request your profile information by clicking Profile above.</p>
    </div>
    ```

## <a name="guarding-routes"></a>Schützen von Routen

### <a name="angular-guard"></a>Angular-Wächter

MSAL Angular bietet mit `MsalGuard` eine Klasse, mit der Sie Routen schützen und vor dem Zugriff auf die geschützte Route eine Authentifizierung erzwingen können. Mit den folgenden Schritten wird `MsalGuard` der Route `Profile` hinzugefügt. Der Schutz der Route `Profile` bedeutet Folgendes: Auch wenn sich ein Nutzer nicht über die Schaltfläche `Login` anmeldet, wird er vor der Anzeige der Seite `Profile` von `MsalGuard` mittels Popup oder Umleitung zur Authentifizierung aufgefordert, wenn versucht wird, auf die Route `Profile` zuzugreifen oder wenn auf die Schaltfläche `Profile` geklickt wird.

`MsalGuard` ist eine Komfortklasse zur Verbesserung der Benutzerfreundlichkeit. Sie sollte jedoch nicht als Sicherheitsfeature verwendet werden. Angreifer können clientseitige Wächter möglicherweise umgehen. Stellen Sie daher sicher, dass der Server keine Daten zurückgibt, auf die der Benutzer keinen Zugriff haben soll.

1. Fügen Sie Ihrer Anwendung in *src/app/app.module.ts* die Klasse `MsalGuard` als Anbieter hinzu, und fügen Sie die Konfigurationen für `MsalGuard` hinzu. Bereiche, die später zum Abrufen von Token benötigt werden, können in `authRequest` angegeben werden, und die Art der Interaktion für den Wächter kann auf `Redirect` oder `Popup` festgelegt werden. Ihr Code sollte wie folgt aussehen:

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard } from '@azure/msal-angular'; // MsalGuard added to imports
    import { PublicClientApplication, InteractionType } from '@azure/msal-browser'; // InteractionType added to imports

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
            interactionType: InteractionType.Redirect, // MSAL Guard Configuration
            authRequest: {
              scopes: ['user.read']
            }
        }, null)
      ],
      providers: [
        MsalGuard // MsalGuard added as provider here
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }
    ```

2. Legen Sie `MsalGuard` in *src/app/app-routing.module.ts* für die Routen fest, die Sie schützen möchten:

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';
    import { MsalGuard } from '@azure/msal-angular';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [MsalGuard]
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

3. Passen Sie die Anmeldeaufrufe in *src/app/app.component.ts* an, um den Satz `authRequest` in den Wächterkonfigurationen zu berücksichtigen. Ihr Code sollte nun wie folgt aussehen:

    ```javascript
    import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
    import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
    import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

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

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

## <a name="acquire-a-token"></a>Abrufen eines Token

### <a name="angular-interceptor"></a>Interceptor von Angular

MSAL Angular verfügt über eine `Interceptor`-Klasse, mit der automatisch Token für ausgehende Anforderungen bezogen werden, bei denen der `http`-Client von Angular für bekannte geschützte Ressourcen verwendet wird.

1. Fügen Sie Ihrer Anwendung in *src/app/app.module.ts* die Klasse `Interceptor` als Anbieter sowie die zugehörigen Konfigurationen hinzu. Ihr Code sollte jetzt folgendermaßen aussehen:

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';
    import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http"; // Import 

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard, MsalInterceptor } from '@azure/msal-angular'; // Import MsalInterceptor
    import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        HttpClientModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_Here',
            authority: 'Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here',
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
          interactionType: InteractionType.Redirect,
          authRequest: {
            scopes: ['user.read']
            }
        }, {
          interactionType: InteractionType.Redirect, // MSAL Interceptor Configuration
          protectedResourceMap: new Map([ 
              ['Enter_the_Graph_Endpoint_Here/v1.0/me', ['user.read']]
          ])
        })
      ],
      providers: [
        {
          provide: HTTP_INTERCEPTORS,
          useClass: MsalInterceptor,
          multi: true
        },
        MsalGuard
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }

    ```

    Die geschützten Ressourcen werden als `protectedResourceMap` bereitgestellt. Bei den URLs, die Sie in der `protectedResourceMap`-Sammlung angeben, muss die Groß-/Kleinschreibung beachtet werden. Fügen Sie für jede Ressource Bereiche hinzu, die für die Rückgabe im Zugriffstoken angefordert werden.

    Beispiel:

    * `["user.read"]` für Microsoft Graph
    * `["<Application ID URL>/scope"]` für benutzerdefinierte Web-APIs (`api://<Application ID>/access_as_user`)
    
    Ändern Sie die Werte in `protectedResourceMap` wie im Anschluss beschrieben:

    |Wertname| Info|
    |----------|------|
    |`Enter_the_Graph_Endpoint_Here`| Die Instanz der Microsoft Graph-API, mit der die Anwendung kommunizieren soll. Ersetzen Sie beide Vorkommen dieser Zeichenfolge für den **globalen** Microsoft Graph-API-Endpunkt durch `https://graph.microsoft.com`. Informationen zu Endpunkten in **nationalen** Cloudbereitstellungen finden Sie in der Microsoft Graph Dokumentation unter [Bereitstellungen nationaler Clouds](/graph/deployments).|

2. Ersetzen Sie den Code in *src/app/profile/profile.component.ts*, um das Profil eines Benutzers mit einer HTTP-Anforderung abzurufen:

    ```JavaScript
    import { Component, OnInit } from '@angular/core';
    import { HttpClient } from '@angular/common/http';

    const GRAPH_ENDPOINT = 'Enter_the_Graph_Endpoint_Here/v1.0/me';

    type ProfileType = {
      givenName?: string,
      surname?: string,
      userPrincipalName?: string,
      id?: string
    };

    @Component({
      selector: 'app-profile',
      templateUrl: './profile.component.html',
      styleUrls: ['./profile.component.css']
    })
    export class ProfileComponent implements OnInit {
      profile!: ProfileType;

      constructor(
        private http: HttpClient
      ) { }

      ngOnInit() {
        this.getProfile();
      }

      getProfile() {
        this.http.get(GRAPH_ENDPOINT)
          .subscribe(profile => {
            this.profile = profile;
          });
      }
    }
    ```

3. Ersetzen Sie die Benutzeroberfläche in *src/app/profile/profile.component.html*, um Profilinformationen anzuzeigen: 

    ```HTML
    <div>
        <p><strong>First Name: </strong> {{profile?.givenName}}</p>
        <p><strong>Last Name: </strong> {{profile?.surname}}</p>
        <p><strong>Email: </strong> {{profile?.userPrincipalName}}</p>
        <p><strong>Id: </strong> {{profile?.id}}</p>
    </div>
    ```

## <a name="sign-out"></a>Abmelden

Aktualisieren Sie den Code in *src/app/app.component.html* zur bedingten Anzeige einer Schaltfläche vom Typ `Logout`:

```HTML
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
  <router-outlet *ngIf="!isIframe"></router-outlet>
</div>
```

### <a name="sign-out-using-redirects"></a>Abmelden mittels Umleitungen

Aktualisieren Sie den Code in *src/app/app.component.ts*, um einen Benutzer unter Verwendung von Umleitungen abzumelden:

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

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

  logout() { // Add log out function here
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
}
```

### <a name="sign-out-using-pop-ups"></a>Durch Pop-Ups abmelden

Aktualisieren Sie den Code in *src/app/app.component.ts*, um einen Benutzer unter Verwendung von Popups abzumelden:

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, PopupRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

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
      this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest)
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    } else {
      this.authService.loginPopup()
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    }
  }

  logout() { // Add log out function here
    this.authService.logoutPopup({
      mainWindowRedirectUri: "/"
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

## <a name="test-your-code"></a>Testen Ihres Codes

1.  Starten Sie den Webserver, um an dem Port zu lauschen, indem Sie an einer Eingabeaufforderung im Anwendungsordner die folgenden Befehle ausführen:

    ```bash
    npm install
    npm start
    ```
1. Geben Sie in Ihrem Browser **http://localhost:4200** oder **http://localhost:{port}** ein, wobei *port* der Port ist, an dem der Webserver lauscht. Die daraufhin angezeigte Seite sollte wie folgt aussehen:

    :::image type="content" source="media/tutorial-v2-angular-auth-code/angular-01-not-signed-in.png" alt-text="Webbrowser mit dem Anmeldedialogfeld.":::


### <a name="provide-consent-for-application-access"></a>Zustimmen zum Anwendungszugriff

Wenn Sie sich erstmalig bei Ihrer Anwendung anmelden, werden Sie aufgefordert, ihr Zugriff auf Ihr Profil zu gewähren und ihr zu gestatten, Sie anzumelden:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Im Webbrowser angezeigtes Inhaltsdialogfeld.":::

Wenn Sie in die angeforderten Berechtigungen einwilligen, wird von der Webanwendung eine Seite für die erfolgreiche Anmeldung angezeigt:

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-02-signed-in.png" alt-text="Ergebnisse einer erfolgreichen Anmeldung im Webbrowser.":::

### <a name="call-the-graph-api"></a>Aufrufen der Graph-API

Wählen Sie nach der Anmeldung die Option **Profile** (Profil) aus, um die Benutzerprofilinformationen anzuzeigen, die in der Antwort des Aufrufs der Microsoft Graph-API zurückgegeben werden:

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-03-profile-data.png" alt-text="Im Browser angezeigte Profilinformationen aus Microsoft Graph.":::

## <a name="add-scopes-and-delegated-permissions"></a>Hinzufügen von Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich _User.Read_, um das Benutzerprofil zu lesen. Der Bereich _User.Read_ wird automatisch jeder App-Registrierung hinzugefügt, die Sie im Azure-Portal erstellen. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt beispielsweise den Bereich _Mail.Read_, um die E-Mail des Benutzers aufzuführen.

Wenn Sie Bereiche hinzufügen, werden Ihre Benutzer möglicherweise aufgefordert, zusätzliche Zustimmung für die hinzugefügten Bereiche zu erteilen.

>[!NOTE]
>Wenn Sie die Anzahl der Bereiche erhöhen, werden Benutzer ggf. zu weiteren Genehmigungen aufgefordert.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Vertiefen Sie Ihr Wissen über die SPA-Entwicklung mit Microsoft Identity Platform in der mehrteiligen Artikelreihe.

> [!div class="nextstepaction"]
> [Szenario: Einseitige Anwendung](scenario-spa-overview.md)
