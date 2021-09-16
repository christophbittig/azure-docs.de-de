---
title: Aktivieren von Web-App-Authentifizierungsoptionen mit Azure Active Directory B2C
description: In diesem Artikel werden mehrere Möglichkeiten zum Aktivieren von Web-App-Authentifizierungsoptionen erläutert.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 50cdb5f171614c138427b358f2418b8b81751457
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220348"
---
# <a name="enable-authentication-options-in-a-web-app-by-using-azure-ad-b2c"></a>Aktivieren von Authentifizierungsoptionen in einer Web-App mit Azure AD B2C 

In diesem Artikel werden die Möglichkeiten beschrieben, wie Sie die Authentifizierungsfunktionalität von Azure Active Directory B2C (Azure AD B2C) für Ihre Webanwendung aktivieren, anpassen und verbessern können. 

Bevor Sie beginnen, sollten Sie sich mit den folgenden Artikeln vertraut machen: 
* [Konfigurieren der Authentifizierung in einer Beispiel-Web-App](configure-authentication-sample-web-app.md)
* [Aktivieren Sie die Authentifizierung in Ihrer eigenen Web-App](enable-authentication-web-application.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Um eine benutzerdefinierte Domäne und Ihre Mandanten-ID in der Authentifizierungs-URL zu verwenden, befolgen Sie die Anleitung unter [Aktivieren von benutzerdefinierten Domänen](custom-domain.md). Öffnen Sie die Datei *appsettings.json* in dem Projektstammordner. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter.

Öffnen Sie die Datei *appsettings.json*, und führen Sie folgende Aktionen aus:

- Aktualisieren Sie den `Instance`-Eintrag mit Ihrer benutzerdefinierten Domäne.
- Aktualisieren Sie den `Domain`-Eintrag mit Ihrer [Mandanten-ID](tenant-management.md#get-your-tenant-id). Weitere Informationen finden Sie unter [Verwenden der Mandanten-ID](custom-domain.md#optional-use-tenant-id).

Die folgende JSON zeigt die App-Einstellungen vor der Änderung: 

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

Die folgende JSON zeigt die App-Einstellungen nach der Änderung: 

```JSON
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>Die Unterstützung für erweiterte Szenarien

Die `AddMicrosoftIdentityWebAppAuthentication`-Methode in der Microsoft Identity Platform-API ermöglicht es den Entwicklern, einen Code für erweiterte Authentifizierungsszenarien hinzuzufügen oder OpenIdConnect-Ereignisse zu abonnieren. Beispielsweise können Sie „OnRedirectToIdentityProvider“ abonnieren, mit dem Sie die Authentifizierungsanforderung anpassen können, die Ihre App an Azure AD B2C sendet.

Um erweiterte Szenarien zu unterstützen, öffnen Sie die Datei *Startup.cs* und ersetzen Sie `AddMicrosoftIdentityWebAppAuthentication` in der `ConfigureServices`-Funktion durch den folgenden Codeausschnitt: 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

Der vorangehende Code fügt das OnRedirectToIdentityProvider-Ereignis mit einem Verweis auf die Methode `OnRedirectToIdentityProviderFunc` hinzu. Fügen Sie den folgenden Codeausschnitt zu der `Startup.cs`-Klasse hinzu.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

Sie können die Parameter zwischen Ihrem Controller und der Funktion `OnRedirectToIdentityProvider` mithilfe von Kontextparametern übergeben. 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 
1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.
1. Fügen Sie der `OnRedirectToIdentityProvider`-Funktion die folgende Codezeile hinzu:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.
1. Fügen Sie in der Funktion `OnRedirectToIdentityProviderFunc` die folgende Codezeile zur `OnRedirectToIdentityProvider`-Funktion hinzu:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Konfigurieren Sie die Sprachanpassung](language-customization.md).
1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.
1. Fügen Sie der `OnRedirectToIdentityProvider`-Funktion die folgende Codezeile hinzu:

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)-Element.
1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.
1. Fügen Sie der `OnRedirectToIdentityProvider`-Funktion die folgende Codezeile hinzu:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.
1. Definieren Sie ein [technisches ID-Token-Hinweisprofil](id-token-hint.md) in Ihrer benutzerdefinierten Richtlinie.
1. Fügen Sie der `OnRedirectToIdentityProvider`-Funktion die folgende Codezeile hinzu:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>Der Konto-Controller

Wenn Sie die Aktionen *Anmelden*, *Registrieren* oder *Abmelden* anpassen möchten, wird empfohlen, dass Sie einen eigenen Controller erstellen. Mit einem eigenen Controller können Sie die Parameter zwischen Ihrem Controller und der Authentifizierungsbibliothek übergeben. `AccountController` ist Teil des `Microsoft.Identity.Web.UI` NuGet-Pakets, das die Anmelde- und Abmeldeaktionen verarbeitet. Die Implementierung dafür finden Sie in der [Microsoft Identity Web-Bibliothek](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs). 

### <a name="add-the-account-controller"></a>Hinzufügen des Kontocontrollers

Klicken Sie im Visual Studio-Projekt mit der rechten Maustaste auf den Ordner *Controller*, und fügen Sie dann einen neuen **Controller** hinzu. Wählen Sie **Leerer MVC-Controller** aus, und geben Sie dann den Namen **MyAccountController.cs** an.

Der folgende Codeausschnitt veranschaulicht eine benutzerdefinierte `MyAccountController` mit der *SignIn*-Aktion.

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            return Challenge(properties, scheme);
        }

    }
}
```

Ändern Sie in der *_LoginPartial.cshtml*-Ansicht den Anmeldelink zu Ihrem Controller.

```html
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

### <a name="pass-the-azure-ad-b2c-policy-id"></a>Übergeben der Azure AD B2C-Richtlinien-ID

Der folgende Codeausschnitt veranschaulicht einen benutzerdefinierten `MyAccountController` mit den Aktionen **SignIn** und **SignUp**. Die Aktion übergibt einen Parameter namens `policy` an die Authentifizierungsbibliothek. Dadurch können Sie die richtige Azure AD B2C-Richtlinien-ID für die jeweilige Aktion angeben.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    return Challenge(properties, scheme);
}

public IActionResult SignUp([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignUp";
    return Challenge(properties, scheme);
}
```

Ändern Sie in der Ansicht *_LoginPartial.cshtml* für alle anderen Authentifizierungslinks den `asp-controller`-Wert in `MyAccountController` (z. B. für die Registrierung oder die Profilbearbeitung).

### <a name="pass-custom-parameters"></a>Übergeben benutzerdefinierter Parameter

Der folgende Codeausschnitt veranschaulicht eine benutzerdefinierte `MyAccountController` mit der **SignIn**-Aktion. Die Aktion übergibt einen Parameter namens `campaign_id` an die Authentifizierungsbibliothek.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    properties.Items["campaign_id"] = "1234";
    return Challenge(properties, scheme);
}
```

Schließen Sie das Verfahren [Erweiterte Szenarien unterstützen](#support-advanced-scenarios) aus, und lesen Sie dann in der `OnRedirectToIdentityProvider` Methode den benutzerdefinierten Parameter:

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = context.Properties.Items.FirstOrDefault(x => x.Key == "campaign_id").Value;

    // Add your custom code here
    if (campaign_id != null)
    {
        // Send parameter to authentication request
        context.ProtocolMessage.SetParameter("campaign_id", campaign_id);
    }
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="secure-your-logout-redirect"></a>Sichern der Umleitung beim Abmelden

Nach der Abmeldung wird der Benutzer an den im `post_logout_redirect_uri`-Parameter angegebenen URI umgeleitet, ungeachtet der Antwort-URLs, die für die Anwendung angegeben wurden. Wenn jedoch ein gültiger `id_token_hint`-Wert übergeben wird und die Option [ID-Token in Abmeldeanforderungen erforderlich](session-behavior.md#secure-your-logout-redirect) aktiviert ist, überprüft Azure AD B2C, ob der Wert von `post_logout_redirect_uri` einem der für die Anwendung konfigurierten Umleitungs-URIs entspricht, bevor die Umleitung ausgeführt wird. Wenn keine entsprechende Antwort-URL für die Anwendung konfiguriert ist, wird eine Fehlermeldung angezeigt, und der Benutzer wird nicht umgeleitet.

Um eine sichere Umleitung der Abmeldung von Ihrer Anwendung zu unterstützen, führen Sie zunächst die Schritte in den Abschnitten [Kontocontroller](enable-authentication-web-application-options.md#add-the-account-controller) und [Unterstützung erweiterter Szenarien](#support-advanced-scenarios) aus. Führen Sie danach die folgenden Schritte aus:

1. Fügen Sie im Controller `MyAccountController.cs` mithilfe des folgenden Codeausschnitts eine **SignOut**-Aktion hinzu:

    ```csharp
    [HttpGet("{scheme?}")]
    public async Task<IActionResult> SignOutAsync([FromRoute] string scheme)
    {
        scheme ??= OpenIdConnectDefaults.AuthenticationScheme;

        //obtain the id_token
        var idToken = await HttpContext.GetTokenAsync("id_token");
        //send the id_token value to the authentication middleware
        properties.Items["id_token_hint"] = idToken;            

        return SignOut(properties,CookieAuthenticationDefaults.AuthenticationScheme,scheme);
    }
    ```

1. Analysieren Sie in der Klasse **Startup.cs** den Wert von `id_token_hint`, und fügen Sie den Wert an die Authentifizierungsanforderung an. Der folgende Codeausschnitt veranschaulicht das Übergeben des Werts von `id_token_hint` an die Authentifizierungsanforderung:

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
        var id_token_hint = context.Properties.Items.FirstOrDefault(x => x.Key == "id_token_hint").Value;
        if (id_token_hint != null)
        {
            // Send parameter to authentication request
            context.ProtocolMessage.SetParameter("id_token_hint", id_token_hint);
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

1. Fügen Sie in der `ConfigureServices`-Funktion die Option `SaveTokens` hinzu, damit **Controller** Zugriff auf den `id_token`-Wert erhalten: 

    ```csharp
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
        {
            Configuration.Bind("AzureAdB2C", options);
            options.Events ??= new OpenIdConnectEvents();        
            options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
            options.SaveTokens = true;
        });
    ```

1. Fügen Sie in der Konfigurationsdatei **appsettings.json** im Schlüssel `SignedOutCallbackPath` Ihren Umleitungs-URI-Pfad für die Abmeldung hinzu.

    ```json
    "AzureAdB2C": {
      "Instance": "https://<your-tenant-name>.b2clogin.com",
      "ClientId": "<web-app-application-id>",
      "Domain": "<your-b2c-domain>",
      "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
      "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
    }
    ```

Im obigen Beispiel weist **post_logout_redirect_uri**, das an die Abmeldeanforderung übergeben wird, das folgende Format auf: `https://your-app.com/signout/<your-sign-up-in-policy>`. Diese URL muss zur Antwort-URL der Anwendungsregistrierung hinzugefügt werden.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Mit [Autorisierung in ASP.NET Core](/aspnet/core/security/authorization/introduction) können Sie mithilfe einer der folgenden Methoden überprüfen, ob Benutzer für den Zugriff auf eine geschützte Ressource autorisiert sind: 
* [Rollenbasierte Autorisierung](/aspnet/core/security/authorization/roles) 
* [Anspruchsbasierte Autorisierung](/aspnet/core/security/authorization/claims) 
* [Richtlinienbasierte Autorisierung](/aspnet/core/security/authorization/policies)

Fügen Sie in der `ConfigureServices`-Methode die `AddAuthorization`-Methode hinzu, die das Autorisierungsmodell hinzufügt. Im folgenden Beispiel wird eine Richtlinie namens `EmployeeOnly` erstellt. Die Richtlinie überprüft, ob ein Anspruch `EmployeeNumber` vorhanden ist. Der Wert des Anspruchs muss eine der folgenden IDs sein: 1, 2, 3, 4 oder 5.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

Sie steuern die Autorisierung in ASP.NET Core mit dem [AuthorizeAttribute](/aspnet/core/security/authorization/simple) und dessen verschiedenen Parametern. In seiner grundlegendsten Form schränkt das Anwenden des `Authorize`-Attributs auf einen Controller, eine Aktion oder eine Razor-Seite den Zugriff auf die authentifizierten Benutzer dieser Komponente ein.

Die Richtlinien werden von Ihnen auf die Controller angewendet, indem das `Authorize`-Attribut mit dem Richtliniennamen verwendet wird. Der folgende Code schränkt den Zugriff auf die `Claims`-Aktion auf die Benutzer ein, die durch die `EmployeeOnly`-Richtlinie autorisiert sind:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Autorisierung finden Sie unter [Einführung in die Autorisierung in ASP.NET Core](/aspnet/core/security/authorization/introduction).
