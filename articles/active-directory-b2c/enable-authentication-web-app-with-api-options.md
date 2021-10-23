---
title: Aktivieren einer Webanwendung, die Web-API-Optionen mithilfe von Azure Active Directory B2C aufruft
description: In diesem Artikel wird erläutert, wie Sie die Verwendung einer Webanwendung aktivieren, die Web-API-Optionen auf verschiedene Weise aufruft.
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
ms.openlocfilehash: e6d43846307ddf97983a408c244ff3470bea2641
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040871"
---
# <a name="configure-authentication-options-in-a-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Konfigurieren der Authentifizierungsoptionen in einer Web-App, die eine Web-API mithilfe von Azure AD B2C aufruft 

In diesem Artikel werden Möglichkeiten beschrieben, wie Sie die Azure Active Directory B2C-Authentifizierungsoberfläche (Azure AD B2C) für Ihre Webanwendung, die eine Web-API aufruft, anpassen und verbessern können. Lesen Sie die folgenden Artikel, bevor Sie beginnen: 
* [Konfigurieren der Authentifizierung in einer Beispielwebanwendung](configure-authentication-sample-web-app-with-api.md)
* [Aktivieren der Authentifizierung in Ihrer Web-Anwendung](enable-authentication-web-app-with-api.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)] 

Um eine benutzerdefinierte Domäne und Ihre Mandanten-ID in der Authentifizierungs-URL zu verwenden, befolgen Sie die Anleitung unter [Aktivieren von benutzerdefinierten Domänen](custom-domain.md). Öffnen Sie im Projektstammordner die Datei `appsettings.json`. Diese Datei enthält Informationen zu Ihrem Azure AD B2C-Identitätsanbieter. 

- Aktualisieren Sie den `Instance`-Eintrag mit Ihrer benutzerdefinierten Domäne.
- Aktualisieren Sie den `Domain`-Eintrag mit Ihrer [Mandanten-ID](tenant-management.md#get-your-tenant-id). Weitere Informationen finden Sie unter [Verwenden der Mandanten-ID](custom-domain.md#optional-use-tenant-id).

Der folgende JSON-Code zeigt die App-Einstellungen *vor* der Änderung: 

```json
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

Der folgende JSON-Code zeigt die App-Einstellungen *nach* der Änderung: 

```json
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>Die Unterstützung für erweiterte Szenarien

Die `AddMicrosoftIdentityWebAppAuthentication`-Methode in der Microsoft Identity Platform-API ermöglicht es den Entwicklern, einen Code für erweiterte Authentifizierungsszenarien hinzuzufügen oder OpenIdConnect-Ereignisse zu abonnieren. Beispielsweise können Sie „OnRedirectToIdentityProvider“ abonnieren, mit dem Sie die Authentifizierungsanforderung anpassen können, die Ihre App an Azure AD B2C sendet.

Um erweiterte Szenarien zu unterstützen, öffnen Sie die Datei *Startup.cs* und ersetzen Sie in der `ConfigureServices`-Funktion `AddMicrosoftIdentityWebAppAuthentication` durch den folgenden Codeausschnitt: 

```csharp
// Configuration to sign in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

Der obige Code fügt das „OnRedirectToIdentityProvider“-Ereignis mit einem Verweis auf die *OnRedirectToIdentityProviderFunc*-Methode hinzu. Fügen Sie den folgenden Codeausschnitt zu der `Startup.cs`-Klasse hinzu.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

Sie können die Parameter zwischen Ihrem Controller und der *OnRedirectToIdentityProvider*-Funktion mithilfe von Kontextparametern übergeben. 


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 

1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.

1. Fügen Sie die folgende Codezeile der *OnRedirectToIdentityProvider*-Funktion hinzu:
    
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
1. Fügen Sie die folgende Codezeile zur *OnRedirectToIdentityProvider*-Funktion in der *OnRedirectToIdentityProviderFunc*-Funktion hinzu:
    
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
1. Fügen Sie die folgende Codezeile der *OnRedirectToIdentityProvider*-Funktion hinzu:

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Konfigurieren Sie das Element [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Schließen Sie das Verfahren [Unterstützen erweiterter Szenarien](#support-advanced-scenarios) ab.
1. Fügen Sie die folgende Codezeile der *OnRedirectToIdentityProvider*-Funktion hinzu:
    
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
1. Fügen Sie die folgende Codezeile der *OnRedirectToIdentityProvider*-Funktion hinzu:
    
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

Wenn Sie die Aktionen *Anmelden*, *Registrieren* oder *Abmelden* anpassen möchten, wird empfohlen, dass Sie einen eigenen Controller erstellen. Mit einem eigenen Controller können Sie die Parameter zwischen Ihrem Controller und der Authentifizierungsbibliothek übertragen. `AccountController` ist Teil des `Microsoft.Identity.Web.UI` NuGet-Pakets, das die Anmelde- und Abmeldeaktionen verarbeitet. Die Implementierung dafür finden Sie in der [Microsoft Identity Web-Bibliothek](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs). 

Der folgende Codeausschnitt veranschaulicht eine benutzerdefinierte `MyAccountController` mit der **SignIn**-Aktion. Die Aktion übergibt einen Parameter namens `campaign_id` an die Authentifizierungsbibliothek.

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
            properties.Items["campaign_id"] = "1234";
            return Challenge(properties, scheme);
        }

    }
}

```

Ändern Sie in der `_LoginPartial.cshtml`-Ansicht den Anmeldelink zu Ihrem Controller

```
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

In `OnRedirectToIdentityProvider` der `Startup.cs`-Aufrufe können Sie den benutzerdefinierten Parameter lesen:

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = (context.Properties.Items.ContainsKey("campaign_id"))
    
    // Add your custom code here
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Mit der [Autorisierung in ASP.NET Core](/aspnet/core/security/authorization/introduction) können Sie die [rollenbasierte Autorisierung](/aspnet/core/security/authorization/roles), die [anspruchsbasierte Autorisierung](/aspnet/core/security/authorization/claims) oder die [richtlinienbasierte Autorisierung](/aspnet/core/security/authorization/policies) verwenden, um zu überprüfen, ob der Benutzer für den Zugriff auf eine geschützte Ressource autorisiert ist.

Fügen Sie die *AddAuthorization*-Methode, die das Autorisierungsmodell hinzufügt, zur *ConfigureServices*-Methode hinzu. Im folgenden Beispiel wird eine Richtlinie namens `EmployeeOnly` erstellt. Die Richtlinie überprüft, ob ein Anspruch `EmployeeNumber` vorhanden ist. Der Wert des Anspruchs muss eine der folgenden IDs sein: 1, 2, 3, 4 oder 5.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

Sie steuern die Autorisierung in ASP.NET Core mit dem [AuthorizeAttribute](/aspnet/core/security/authorization/simple) und dessen verschiedenen Parametern. Wenn Sie das `[Authorize]`-Attribut in seiner grundlegendsten Form auf einen Controller, eine Aktion oder eine Razor-Seite anwenden, beschränken Sie den Zugriff auf die authentifizierten Benutzer dieser Komponente.

Die Richtlinien werden von Ihnen auf die Controller angewendet, indem das `[Authorize]`-Attribut mit dem Richtliniennamen verwendet wird. Der folgende Code schränkt den Zugriff auf die `Claims`-Aktion auf die Benutzer ein, die durch die `EmployeeOnly`-Richtlinie autorisiert sind:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie hier: [Einführung in die Autorisierung in ASP.NET Core](/aspnet/core/security/authorization/introduction).
