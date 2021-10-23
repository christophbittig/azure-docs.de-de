---
title: Aktivieren von Angular-Anwendungsoptionen mit Azure Active Directory B2C
description: Aktivieren Sie die Verwendung von Angular-Anwendungsoptionen auf verschiedene Arten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 94057e0b1d566d092089725dea96a53e907f2a71
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041099"
---
# <a name="configure-authentication-options-in-an-angular-application-by-using-azure-active-directory-b2c"></a>Konfigurieren von Authentifizierungsoptionen in einer Angular-Anwendung mit Azure Active Directory B2C

In diesem Artikel werden Möglichkeiten beschrieben, wie Sie die Azure Active Directory B2C-Authentifizierungsoberfläche (Azure AD B2C) für Ihre Single-Page-Webanwendung (SPA) in Angular anpassen und verbessern können. Machen Sie sich zunächst mit dem Inhalt eines der folgenden Artikeln vertraut: [Konfigurieren der Authentifizierung in einem Beispiel einer Angular-Single-Page-Webanwendung](configure-authentication-sample-angular-spa-app.md) oder [Aktivieren der Authentifizierung in einer eigenen Angular-Anwendung](enable-authentication-angular-spa-app.md).


## <a name="sign-in-and-sign-out-behavior"></a>An- und Abmeldeverhalten


Sie können Ihre Single-Page-Webanwendung auf zwei verschiedene Arten für die Anmeldung von Benutzern mit MSAL.js konfigurieren:

- **Popupfenster**: Die Authentifizierung erfolgt in einem Popupfenster, während der Status der Anwendung beibehalten wird. Diese Methode ist geeignet, wenn die Benutzer während der Authentifizierung Ihre Anwendungsseite nicht verlassen sollen.  Informationen zu [bekannten Problemen bei Popupfenstern im Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups) finden Sie hier.
  - Verwenden Sie zur Anmeldung mit Popupfenstern in der Klasse `src/app/app.component.ts` die Methode `loginPopup`.  
  - Legen Sie in der Klasse `src/app/app.module.ts` das Attribut `interactionType` auf `InteractionType.Popup` fest.
  - Verwenden Sie zur Abmeldung mit Popupfenstern in der Klasse `src/app/app.component.ts` die Methode `logoutPopup`. Sie können `logoutPopup` auch so konfigurieren, dass das Hauptfenster nach abgeschlossener Abmeldung auf eine andere Seite (z. B. die Startseite oder die Anmeldeseite) umgeleitet wird, indem Sie `mainWindowRedirectUri` als Teil der Anforderung übergeben.
- **Umleitung**: Der Benutzer wird zu Azure AD B2C umgeleitet, um den Authentifizierungsflow abzuschließen. Diese Methode ist geeignet, wenn für Benutzer Browsereinschränkungen oder Richtlinien gelten, bei denen Popupfenster deaktiviert sind. 
  - Verwenden Sie zur Anmeldung mit der Umleitung in der Klasse `src/app/app.component.ts` die Methode `loginRedirect`.  
  - Legen Sie in der Klasse `src/app/app.module.ts` das Attribut `interactionType` auf `InteractionType.Redirect` fest.
  - Verwenden Sie zur Abmeldung mit der Umleitung in der Klasse `src/app/app.component.ts` die Methode `logoutRedirect`. Durch Festlegen von `postLogoutRedirectUri` können Sie den Ziel-URI der Umleitung nach der Abmeldung konfigurieren. Dieser URI sollte in Ihrer Anwendungsregistrierung als Umleitungs-URI registriert sein.
  
Im folgenden Beispiel werden die An- und Abmeldung veranschaulicht:

#### <a name="pop-up"></a>[Popup](#tab/popup)


```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest);
  } else {
    this.authService.loginPopup();
  }
}

logout() { 
  this.authService.logoutPopup({
    mainWindowRedirectUri: '/',
  });
}
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
//src/app/app.component.ts
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
```

---

Die MSAL-Bibliothek in Angular verfügt über drei Anmeldeflows: die interaktive Anmeldung (bei der ein Benutzer auf die Anmeldeschaltfläche klickt), MSAL Guard und den MSAL-Interceptor. Die Konfigurationen von MSAL Guard und dem MSAL-Interceptor werden wirksam, wenn ein Benutzer versucht, ohne gültiges Zugriffstoken auf eine geschützte Ressource zuzugreifen. In diesem Fall erzwingt die MSAL-Bibliothek eine Anmeldung. 

In den folgenden Beispielen wird veranschaulicht, wie Sie MSAL Guard und den MSAL-Interceptor für die Anmeldung mit einem Popupfenster oder einer Umleitung konfigurieren: 

#### <a name="pop-up"></a>[Popup](#tab/popup)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Popup,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Redirect,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

---  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu. Eine Anleitung hierzu finden Sie unter [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name). 
1. Erstellen oder verwenden Sie ein vorhandenes MSAL-Konfigurationsobjekt vom Typ `PopupRequest` oder `RedirectRequest`.
1. Legen Sie das Attribut `loginHint` mit dem entsprechenden Anmeldehinweis fest. 

Die folgenden Codeausschnitte veranschaulichen, wie der Anmeldehinweisparameter übergeben wird. In diesem Beispiel wird `bob@contoso.com` als Attributwert verwendet.

#### <a name="pop-up"></a>[Popup](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

---  


[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Erstellen oder verwenden Sie ein vorhandenes MSAL-Konfigurationsobjekt vom Typ `PopupRequest` oder `RedirectRequest`.
1. Legen Sie das Attribut `domainHint` mit dem entsprechenden Domänenhinweis fest.

Die folgenden Codeausschnitte veranschaulichen, wie der Domänenhinweisparameter übergeben wird. In diesem Beispiel wird `facebook.com` als Attributwert verwendet.

#### <a name="pop-up"></a>[Popup](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

---  

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Konfigurieren Sie die Sprachanpassung](language-customization.md). 
1. Erstellen oder verwenden Sie ein vorhandenes MSAL-Konfigurationsobjekt vom Typ `PopupRequest` oder `RedirectRequest` mit `extraQueryParameters`-Attributen.
1. Fügen Sie den `extraQueryParameters`-Attributen den Parameter `ui_locales` mit dem entsprechenden Sprachcode hinzu.

Die folgenden Codeausschnitte veranschaulichen, wie der Domänenhinweisparameter übergeben wird. In diesem Beispiel wird `es-es` als Attributwert verwendet.

#### <a name="pop-up"></a>[Popup](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

---   
 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)-Element.
1. Erstellen oder verwenden Sie ein vorhandenes MSAL-Konfigurationsobjekt vom Typ `PopupRequest` oder `RedirectRequest` mit `extraQueryParameters`-Attributen.
1. Fügen Sie den benutzerdefinierten Abfragezeichenfolgenparameter hinzu (z. B. `campaignId`). Legen Sie den Parameterwert fest. 

Die folgenden Codeausschnitte veranschaulichen, wie ein benutzerdefinierter Abfragezeichenfolgenparameter übergeben wird. In diesem Beispiel wird `germany-promotion` als Attributwert verwendet.

#### <a name="pop-up"></a>[Popup](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"campaignId" : "germany-promotion"}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Definieren Sie in Ihrer benutzerdefinierten Richtlinie das [technische Profil eines ID-Tokenhinweises](id-token-hint.md).
1. Erstellen oder verwenden Sie ein vorhandenes MSAL-Konfigurationsobjekt vom Typ `PopupRequest` oder `RedirectRequest` mit `extraQueryParameters`-Attributen.
1. Fügen Sie den Parameter `id_token_hint` mit der entsprechenden Variable hinzu, die das ID-Token speichert.

Die folgenden Codeausschnitte veranschaulichen, wie ein ID-Tokenhinweis definiert wird:

#### <a name="pop-up"></a>[Popup](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

#### <a name="redirect"></a>[Umleiten](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};;

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Eine Anleitung für die Nutzung der benutzerdefinierten Domäne für Ihre Mandanten-ID in der Authentifizierungs-URL finden Sie unter [Aktivieren von benutzerdefinierten Domänen](custom-domain.md). Öffnen Sie das MSAL-Konfigurationsobjekt `src/app/auth-config.ts`, und ändern Sie `authorities` und `knownAuthorities` so ab, dass diese Ihren benutzerdefinierten Domänennamen und die Mandanten-ID verwenden.  

Im folgenden JavaScript-Code sehen Sie das MSAL-Konfigurationsobjekt vor der Änderung: 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

Im folgenden JavaScript-Code sehen Sie das MSAL-Konfigurationsobjekt nach der Änderung: 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]

Konfigurieren Sie für die [Anmeldung](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/logging.md) bei Angular in *src/app/auth-config.ts* die folgenden Schlüssel:

- `loggerCallback`: Die Rückruffunktion der Protokollierung. 
- `logLevel`: Zur Angabe des Protokolliergrads. Mögliche Werte: `Error`, `Warning`, `Info` und `Verbose`.
- `piiLoggingEnabled`: Ermöglicht die Eingabe personenbezogener Daten. Mögliche Werte: `true` oder `false`.
 
Im folgenden Codeausschnitt wird das Konfigurieren der MSAL-Protokollierung veranschaulicht:

```typescript
export const msalConfig: Configuration = {
  ...
  system: {
    loggerOptions: {
        loggerCallback: (logLevel, message, containsPii) => {  
            console.log(message);
          },
          logLevel: LogLevel.Verbose,
          piiLoggingEnabled: false
      }
  }
  ...
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen: [MSAL.js-Konfigurationsoptionen](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)
