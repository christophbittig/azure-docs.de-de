---
title: Aktivieren von SPA-Anwendungsoptionen mit Azure Active Directory B2C
description: In diesem Artikel werden mehrere Möglichkeiten zum Aktivieren der Verwendung von SPA-Anwendungen erläutert.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: fb4d234f2ec6d991faa1c671a6b311eeff5ed299
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037110"
---
# <a name="configure-authentication-options-in-a-single-page-application-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierung in einer Single-Page-Webanwendung (SPA) mit Azure AD B2C

In diesem Artikel werden Möglichkeiten beschrieben, wie Sie die Authentifizierungsfunktionalität von Azure Active Directory B2C (Azure AD B2C) für Ihre Single-Page-Webanwendung anpassen und verbessern können. 

Machen Sie sich zunächst mit dem folgenden Artikel vertraut: [Konfigurieren der Authentifizierung in einer Beispielwebanwendung](configure-authentication-sample-spa-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Um eine benutzerdefinierte Domäne und Ihre Mandanten-ID in der Authentifizierungs-URL zu verwenden, befolgen Sie die Anleitung unter [Aktivieren von benutzerdefinierten Domänen](custom-domain.md). Suchen Sie Ihr MSAL-Konfigurationsobjekt (Microsoft Authentication Library), und ändern Sie *authorities* und *knownAuthorities* so, dass der benutzerdefinierte Domänenname und die Mandanten-ID verwendet werden.

Der folgende JavaScript-Code zeigt das MSAL-Konfigurationsobjekt *vor* der Änderung: 

```Javascript
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

Der folgende JavaScript-Code zeigt das MSAL-Konfigurationsobjekt *nach* der Änderung: 

```Javascript
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

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 
1. Erstellen Sie ein Objekt, in dem **login_hint** gespeichert wird, und übergeben Sie dieses Objekt an die **MSAL loginPopup()** -Methode.

    ```javascript
    let loginRequest = {
        loginHint: "bob@contoso.com"
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Erstellen Sie ein Objekt, in dem **extraQueryParameters** gespeichert wird, und übergeben Sie dieses Objekt an die **MSAL loginPopup()** -Methode.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {domain_hint: 'facebook.com'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Konfigurieren Sie die Sprachanpassung](language-customization.md).
1. Erstellen Sie ein Objekt, in dem **extraQueryParameters** gespeichert wird, und übergeben Sie dieses Objekt an die **MSAL loginPopup()** -Methode.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {ui_locales: 'en-us'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)-Element.
1. Erstellen Sie ein Objekt, in dem **extraQueryParameters** gespeichert wird, und übergeben Sie dieses Objekt an die **MSAL loginPopup()** -Methode.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {campaignId: 'germany-promotion'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Definieren Sie ein [technisches ID-Token-Hinweisprofil](id-token-hint.md) in Ihrer benutzerdefinierten Richtlinie.
1. Erstellen Sie ein Objekt, in dem **extraQueryParameters** gespeichert wird, und übergeben Sie dieses Objekt an die **MSAL loginPopup()** -Methode.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {id_token_hint: 'id-token-hint-value'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

## <a name="secure-your-logout-redirect"></a>Sichern der Umleitung beim Abmelden

Nach der Abmeldung wird der Benutzer an den im `post_logout_redirect_uri`-Parameter angegebenen URI umgeleitet, ungeachtet der Antwort-URLs, die für die Anwendung angegeben wurden. Wenn jedoch ein gültiger `id_token_hint`-Wert übergeben wird und die Option [ID-Token in Abmeldeanforderungen erforderlich](session-behavior.md#secure-your-logout-redirect) aktiviert ist, überprüft Azure AD B2C, ob der Wert von `post_logout_redirect_uri` einem der für die Anwendung konfigurierten Umleitungs-URIs entspricht, bevor die Umleitung ausgeführt wird. Wenn keine entsprechende Antwort-URL für die Anwendung konfiguriert ist, wird eine Fehlermeldung angezeigt, und der Benutzer wird nicht umgeleitet.

Führen Sie die folgenden Schritte aus, um einen gesicherten Umleitungs-URI für die Abmeldung zu unterstützen:

1. Erstellen Sie eine global zugängliche Variable, um `id_token` zu speichern.
    ```javascript
    let id_token = "";
    ```
    
1. Parsen Sie `id_token` in der MSAL-Funktion`handleResponse` vom `authenticationResult`-Objekt in die `id_token`-Variable.
    ```javascript
    function handleResponse(response) {
        if (response !== null) {
            setAccount(response.account);
            id_token = response.idToken;
        } else {
            selectAccount();
        }
    }
    ```
    
1. Fügen Sie in der `signOut`-Funktion den `id_token_hint`-Parameter zum **logoutRequest**-Objekt hinzu.
    ```javascript
    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
            //set id_token_hint to the id_token value
            idTokenHint : id_token,
            mainWindowRedirectUri: msalConfig.auth.redirectUri
        };
        myMSALObj.logoutPopup(logoutRequest);
    }
    ```

Im obigen Beispiel weist **post_logout_redirect_uri**, das an die Abmeldeanforderung übergeben wird, das folgende Format auf: `https://your-app.com/`. Diese URL muss zur Antwort-URL der Anwendungsregistrierung hinzugefügt werden.

## <a name="enable-single-logout"></a>Aktivieren des einmaligen Abmeldens

Einmaliges Abmelden in Azure AD B2C verwendet OpenId Connect Front-Channel-Abmeldung, um Abmeldeanforderungen an alle Anwendungen zu senden, bei der sich der Benutzer über Azure AD B2C angemeldet hat.

Diese Abmeldeanforderungen werden in der Azure AD B2C-Abmeldeseite in einem ausgeblendeten Iframe durchgeführt. Die Iframes senden HTTP-Anforderungen an alle Front-Channel-Abmeldeendpunkte, die für die Apps registriert sind, die von Azure AD B2C als angemeldet erfasst wurden. 

Der Anmeldeendpunkt für jedes Anwendung muss die **MSAL logout()** -Methode aufrufen. Darüber hinaus müssen Sie MSAL in diesem Szenario explizit für die Ausführung in einem Iframe konfigurieren, indem Sie `allowRedirectInIframe`auf `true` festlegen.

Das folgende Codebeispiel legt `allowRedirectInIframe` auf `true` fest:

```javascript
const msalConfig = {
    auth: {
        clientId: "enter_client_id_here",
        .....
    },
    cache: {
        cacheLocation: "..",
        ....
    },
    system: {
        allowRedirectInIframe: true
    };
}

async function logoutSilent(MSAL) {
   return MSAL.logout({
      onRedirectNavigate: (url) => {
         return false;
       }
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den [MSAL.js-Konfigurationsoptionen](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md).
