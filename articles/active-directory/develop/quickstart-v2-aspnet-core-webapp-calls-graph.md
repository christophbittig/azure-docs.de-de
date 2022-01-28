---
title: 'Schnellstart: ASP.NET Core-Web-App zum Anmelden von Benutzern und Aufrufen von Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine App mithilfe von Microsoft.Identity.Web die Microsoft-Anmeldung bei einer ASP.NET Core-Web-App, die Open ID Connect verwendet, implementiert und Microsoft Graph aufruft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, "scenarios:getting-started", "languages:aspnet-core", mode-other
ms.openlocfilehash: e093dd1b5a3942ae0c98dc11feb10dd26ead0162
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2022
ms.locfileid: "137804366"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Schnellstart: ASP.NET Core-Web-App zum Anmelden von Benutzern und Aufrufen von Microsoft Graph in deren Namen

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus. In diesem Beispiel wird gezeigt, wie eine ASP.NET Core-Web-App Benutzer aus einer beliebigen Azure Active Directory (Azure AD)-Organisation anmelden und Microsoft Graph aufrufen kann.

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal

Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie den **Umleitungs-URI** `https://localhost:44321/signin-oidc` sowie `https://localhost:44321/signout-oidc` als **URL für Front-Channel-Abmeldung** in der App-Registrierung hinzufügen.
> [!div class="nextstepaction"]
> [Diese Änderung für mich vornehmen]()

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

## <a name="step-2-download-the-aspnet-core-project"></a>Schritt 2: Herunterladen des ASP.NET Core-Projekts

Führen Sie das Projekt aus.

> [!div class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


## <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit

Wir haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="about-the-code"></a>Informationen zum Code

Dieser Abschnitt enthält eine Übersicht über den Code, der zum Anmelden von Benutzern und zum Aufrufen der Microsoft Graph-API in deren Namen erforderlich ist. Diese Übersicht kann hilfreich sein, um die Funktionsweise des Codes und die Hauptargumente zu verstehen und zu ermitteln, ob Sie einer vorhandenen ASP.NET Core-Anwendung eine Anmeldung hinzufügen und Microsoft Graph aufrufen möchten. Dabei wird [Microsoft.Identity.Web](microsoft-identity-web.md), ein Wrapper um [MSAL.NET](msal-overview.md), verwendet.

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startklasse

Von der Middleware *Microsoft.AspNetCore.Authentication* wird eine Klasse vom Typ `Startup` verwendet. Sie wird ausgeführt, wenn der Hostprozess initialisiert wird:

```csharp

  public void ConfigureServices(IServiceCollection services)
  {  
    // Get the scopes from the configuration (appsettings.json)
    var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');
  
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

Die Methode `AddAuthentication()` konfiguriert den Dienst, um eine cookiebasierte Authentifizierung hinzuzufügen, die in Browserszenarien zum Einsatz kommt und dazu dient, die Aufforderung auf OpenID Connect festzulegen.

Die Zeile, die `.AddMicrosoftIdentityWebApp` enthält, fügt Ihrer Anwendung die Microsoft Identity Platform-Authentifizierung hinzu. Diese wird von [Microsoft.Identity.Web](microsoft-identity-web.md) bereitgestellt. Sie wird anschließend für die Anmeldung unter Verwendung von Microsoft Identity Platform konfiguriert – basierend auf den Informationen im Abschnitt `AzureAD` der Konfigurationsdatei *appsettings.json*:

| *appsettings.json* (Schlüssel) | Beschreibung                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Anwendungs-ID (Client)** der im Azure-Portal registrierten Anwendung.                                                                                       |
| `Instance`             | STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Dieser Wert ist in der Regel `https://login.microsoftonline.com/` (öffentliche Azure-Cloud). |
| `TenantId`             | Name Ihres Mandanten, zugehörige Mandanten-ID (eine GUID) oder *common* für die Anmeldung von Benutzern mit Geschäfts-, Schul- oder Unikonto oder mit persönlichem Microsoft-Konto.                             |

Mit der Methode `EnableTokenAcquisitionToCallDownstreamApi` kann Ihre Anwendung ein Token zum Aufrufen geschützter APIs abrufen. Mit `AddMicrosoftGraph` können Ihre Controller oder Razor Pages direkt von den Methoden `GraphServiceClient` (durch Abhängigkeitsinjektion) und `AddInMemoryTokenCaches` profitieren, die Ihrer App die Nutzung eines Tokencaches ermöglichen.

Die Methode `Configure()` enthält mit `app.UseAuthentication()` und `app.UseAuthorization()` zwei wichtige Methoden, um die genannte Funktion zu aktivieren. Außerdem müssen Sie in der `Configure()`-Methode die Routen von Microsoft Identity Web über mindestens einen Aufruf von `endpoints.MapControllerRoute()` oder einen Aufruf von `endpoints.MapControllers()` registrieren.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Schützen eines Controllers oder der Methode eines Controllers

Durch Zuweisen des Attributs `[Authorize]` zur Controllerklasse oder zu mindestens einer zugehörigen Methode können Sie einen Controller oder dessen Methoden schützen. Das Attribut `[Authorize]` beschränkt den Zugriff auf authentifizierte Benutzer. Wenn sich der Benutzer noch nicht authentifiziert hat, kann für den Zugriff auf den Controller eine Authentifizierungsabfrage gestartet werden. In dieser Schnellstartanleitung werden die Bereiche aus der Konfigurationsdatei gelesen:

```csharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Das GitHub-Repository mit dem in diesem Schnellstart referenzierten ASP.NET Core-Codebeispiel enthält auch Anleitungen und weitere Codebeispiele für folgende Aufgaben:

- Hinzufügen einer Authentifizierung zu einer neuen ASP.NET Core-Webanwendung
- Aufrufen von Microsoft Graph, anderer Microsoft-APIs oder Ihrer eigenen Web-APIs
- Autorisierung hinzufügen
- Anmelden von Benutzern in nationalen Clouds oder mit Identitäten sozialer Netzwerke

> [!div class="nextstepaction"]
> [Tutorials für ASP.NET Core-Web-Apps auf GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
