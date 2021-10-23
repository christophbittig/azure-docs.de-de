---
title: Aktivieren von WPF-Desktopanwendungsoptionen mithilfe von Azure Active Directory B2C
description: Sie können die Nutzung von WPF-Desktopanwendungsoptionen auf verschiedene Weisen aktivieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 16b625f739df35604b649b9d7391d969669af6be
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040757"
---
# <a name="enable-authentication-options-in-a-wpf-desktop-app-by-using-azure-ad-b2c"></a>Aktivieren von Authentifizierungsoptionen in einer WPF-Desktop-App mit Azure AD B2C 

In diesem Artikel werden Möglichkeiten beschrieben, wie Sie die Azure Active Directory B2C-Authentifizierungsoberfläche (Azure AD B2C) für Ihre WPF-Desktopanwendung (Windows Presentation Foundation) anpassen und verbessern können. 

Machen Sie sich zunächst mit dem folgenden Artikel vertraut: [Konfigurieren der Authentifizierung in einer Beispiel-WPF-Desktop-App mit Azure AD B2C](configure-authentication-sample-wpf-desktop-app.md).


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 
1. Wechseln Sie zu Ihrem MSAL-Konfigurationsobjekt (Microsoft Authentication Library), und fügen Sie die `withLoginHint()`-Methode mit dem Anmeldehinweis hinzu.

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Erstellen oder verwenden Sie ein vorhandenes `Dictionary`-Objekt, um zusätzliche Abfrageparameter zu speichern.
1. Fügen Sie dem Wörterbuch den Parameter `domain_hint` mit dem entsprechenden Domänennamen hinzu (z. B. `facebook.com`).
1. Übergeben Sie das zusätzliche Abfrageparameterobjekt an die `WithExtraQueryParameters`-Methode des MSAL-Konfigurationsobjekts.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("domain_hint", "facebook.com");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Konfigurieren Sie die Sprachanpassung](language-customization.md).
1. Erstellen oder verwenden Sie ein vorhandenes `Dictionary`-Objekt, um zusätzliche Abfrageparameter zu speichern.
1. Fügen Sie dem Wörterbuch den Parameter `ui_locales` mit dem entsprechenden Sprachcode hinzu (z. B. `en-us`).
1. Übergeben Sie das zusätzliche Abfrageparameterobjekt an die `WithExtraQueryParameters`-Methode des MSAL-Konfigurationsobjekts.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("ui_locales", "en-us");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)-Element.
1. Erstellen oder verwenden Sie ein vorhandenes `Dictionary`-Objekt, um zusätzliche Abfrageparameter zu speichern.
1. Fügen Sie den benutzerdefinierten Abfragezeichenfolgen-Parameter hinzu, z. B. `campaignId`. Legen Sie den Parameterwert fest (z. B. `germany-promotion`).
1. Übergeben Sie das zusätzliche Abfrageparameterobjekt an die `WithExtraQueryParameters`-Methode des MSAL-Konfigurationsobjekts.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("campaignId", "germany-promotion");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Definieren Sie ein [technisches ID-Token-Hinweisprofil](id-token-hint.md) in Ihrer benutzerdefinierten Richtlinie.
1. Generieren Sie in Ihrem Code ein ID-Token, oder rufen Sie ein ID-Token ab. Legen Sie dieses dann auf eine Variable fest (z. B. `idToken`). 
1. Erstellen oder verwenden Sie ein vorhandenes `Dictionary`-Objekt, um zusätzliche Abfrageparameter zu speichern.
1. Fügen Sie den Parameter `id_token_hint` mit der entsprechenden Variablen hinzu, in der das ID-Token gespeichert wird.
1. Übergeben Sie das zusätzliche Abfrageparameterobjekt an das Attribut `extraQueryParameters` des MSAL-Konfigurationsobjekts.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("id_token_hint", idToken);

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


Der folgende Codeausschnitt veranschaulicht das Konfigurieren der MSAL-Protokollierung:

```csharp
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
    .WithB2CAuthority(AuthoritySignUpSignIn)
    .WithRedirectUri(RedirectUri)
    .WithLogging(Log, LogLevel.Info, false) // don't log P(ersonally) I(dentifiable) I(nformation) details on a regular basis
    .Build();
```

## <a name="configure-the-redirect-uri"></a>Konfigurieren des Umleitungs-URI

Beachten Sie während des Prozesses der [Registrierung der Desktop-App](configure-authentication-sample-wpf-desktop-app.md#step-23-register-the-desktop-app) bei der Auswahl eines Umleitungs-URI die folgenden wichtigen Aspekte:

* **Entwicklung**: Zur Verwendung zur Entwicklung in Desktop-Apps können Sie den Umleitungs-URI auf `http://localhost` festlegen. Dann berücksichtigt Azure AD B2C alle Ports in der Anforderung. Wenn der registrierte URI einen Port enthält, verwendet Azure AD B2C nur diesen Port. Wenn der registrierte Umleitungs-URI beispielsweise `http://localhost` lautet, kann in der Anforderung der Umleitungs-URI `http://localhost:<randomport>` verwendet werden. Lautet der registrierte Umleitungs-URI `http://localhost:8080` muss der Umleitungs-URI in der Anforderung `http://localhost:8080` lauten.
* **Eindeutig:** Das Schema für den Umleitungs-URI muss für jede Anwendung eindeutig sein. Im Beispiel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` ist `com.onmicrosoft.contosob2c.exampleapp` das Schema. Dieses Muster sollte befolgt werden. Wenn zwei Anwendungen das gleiche Schema verwenden, erhalten Benutzer eine Auswahl von Anwendungen. Wenn Benutzer falsch auswählen, tritt bei der Anmeldung ein Fehler auf.
* **Vollständigkeit**: Der Umleitungs-URI muss ein Schema und einen Pfad aufweisen. Der Pfad muss mindestens einen Schrägstrich nach der Domäne enthalten. Zum Beispiel funktioniert `//oauth/`, bei `//oauth` tritt ein Fehler auf. Verwenden Sie keine Sonderzeichen im URI. Beispielsweise ist der Unterstrich (_) nicht zulässig.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [MSAL für .NET, UWP, NetCore und Xamarin-Konfigurationsoptionen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki).
