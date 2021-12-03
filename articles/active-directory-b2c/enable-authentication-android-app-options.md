---
title: Aktivieren von mobilen Android-Anwendungsoptionen mithilfe von Azure Active Directory B2C
description: In diesem Artikel werden verschiedene Möglichkeiten zum Aktivieren von Optionen für mobile Android-Anwendungen mithilfe von Azure Active Directory B2C erläutert.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 8631c5c0852f915596e3b51c76054301eca64c02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289191"
---
# <a name="configure-authentication-options-in-an-android-app-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierungsoptionen in einer Android-App mithilfe von Azure AD B2C 

In diesem Artikel wird beschrieben, wie Sie die Azure Active Directory B2C-Authentifizierungsoberfläche (Azure AD B2C) für Ihre Android-Anwendung aktivieren, anpassen und verbessern können. 

Lesen Sie die folgenden Artikel, bevor Sie beginnen: 
* [Konfigurieren der Authentifizierung in einem Android-Beispiel-App mithilfe von Azure AD B2C](configure-authentication-sample-android-app.md)
* [Aktivieren der Authentifizierung in Ihrer eigenen Android -App mithilfe von Azure AD B2C](enable-authentication-android-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Um eine benutzerdefinierte Domäne und Ihre Mandanten-ID in der Authentifizierungs-URL zu verwenden, befolgen Sie die Anleitung unter [Aktivieren von benutzerdefinierten Domänen](custom-domain.md). Suchen Sie Ihr MSAL-Konfigurationsobjekt (Microsoft Authentication Library), und aktualisieren Sie dann *authorities* mit Ihrem benutzerdefinierten Domänennamen und der Mandanten-ID.


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

Der folgende Kotlin-Code zeigt das MSAL-Konfigurationsobjekt vor der Änderung:

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

Der folgende Kotlin-Code zeigt das MSAL-Konfigurationsobjekt nach der Änderung:

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

Der folgende Kotlin-Code zeigt das MSAL-Konfigurationsobjekt vor der Änderung:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
Der folgende Kotlin-Code zeigt das MSAL-Konfigurationsobjekt nach der Änderung:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 
1. Wechseln Sie zu Ihrem MSAL-Konfigurationsobjekt, und fügen Sie die `withLoginHint()`-Methode mit dem Anmeldehinweis hinzu.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie der Liste den `domain_hint`-Parameter mit dem entsprechenden Domänennamen hinzu (zum Beispiel `facebook.com`).
1. Übergeben Sie die zusätzliche Abfrageparameterliste an die `withAuthorizationQueryStringParameters`-Methode des MSAL-Konfigurationsobjekts.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Konfigurieren Sie die Sprachanpassung](language-customization.md).
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie der Liste den `ui_locales`-Parameter mit dem entsprechenden Sprachcode hinzu (zum Beispiel `en-us`).
1. Übergeben Sie die zusätzliche Abfrageparameterliste an die `withAuthorizationQueryStringParameters`-Methode des MSAL-Konfigurationsobjekts.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)-Element.
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie den benutzerdefinierten Abfragezeichenfolgenparameter hinzu (z. B. `campaignId`). Legen Sie den Parameterwert fest (zum Beispiel `germany-promotion`).
1. Übergeben Sie die zusätzliche Abfrageparameterliste an die `withAuthorizationQueryStringParameters`-Methode des MSAL-Konfigurationsobjekts.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Definieren Sie ein [technisches ID-Token-Hinweisprofil](id-token-hint.md) in Ihrer benutzerdefinierten Richtlinie.
1. Generieren Sie in Ihrem Code ein ID-Token, oder rufen Sie ein ID-Token ab. Legen Sie dieses dann auf eine Variable fest (z. B. `idToken`). 
1. Verwenden Sie ein vorhandenes Listenobjekt, um zusätzliche Abfrageparameter zu speichern, oder erstellen Sie ein neues Listenobjekt.
1. Fügen Sie den `id_token_hint`-Parameter mit der entsprechenden Variablen hinzu, in der das ID-Token gespeichert wird.
1. Übergeben Sie die zusätzliche Abfrageparameterliste an die `withAuthorizationQueryStringParameters`-Methode des MSAL-Konfigurationsobjekts.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

## <a name="embedded-web-view-experience"></a>Benutzeroberfläche für eingebettete Webansicht

Webbrowser sind eine Voraussetzung für die interaktive Authentifizierung. Die MSAL-Bibliothek verwendet standardmäßig die Systemwebansicht. Während der Anmeldung öffnet die MSAL-Bibliothek die Android-Systemwebansicht mit der Azure AD-B2C-Benutzeroberfläche.  

Weitere Informationen finden Sie im Abschnitt [Aktivieren des appübergreifenden einmaligen Anmeldens unter Android mit MSAL](../active-directory/develop/msal-android-single-sign-on.md#sso-through-system-browser).

Je nach Ihren Anforderungen können Sie die eingebettete Webansicht verwenden. Zwischen der eingebetteten Webansicht und der Systemwebansicht in MSAL gibt es visuelle Unterschiede und Unterschiede bei SSO-Vorgängen.

![Screenshot: Unterschied zwischen der Benutzeroberfläche der Systemwebansicht und der eingebetteten Webansicht](./media/enable-authentication-android-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> Es wird empfohlen, die Plattformstandardeinstellung zu verwenden, die normalerweise der Systembrowser ist. Der Systembrowser kann die Benutzer besser speichern, die sich zuvor angemeldet haben. Einige Identitätsanbieter*innen (z. B. Google) unterstützen keine eingebettete Ansicht.

Um dies zu ändern, öffnen Sie die Datei *app/src/main/res/raw/auth_config_b2c.json*. Fügen Sie im Attribut `authorization_user_agent` den Wert `WEBVIEW` hinzu. Im folgenden Beispiel wird veranschaulicht, wie der Webansichtstyp in eine eingebettete Ansicht geändert wird:

```json
{
  "authorization_user_agent": "WEBVIEW" 
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Android-Konfiguration finden Sie unter [MSAL für Android-Konfigurationsoptionen](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki).
