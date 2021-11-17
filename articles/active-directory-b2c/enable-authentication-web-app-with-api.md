---
title: Aktivieren der Authentifizierung in Web-Apps, die eine Web-API mithilfe der Bausteine von Azure Active Directory B2C aufrufen
description: In diesem Artikel werden die Bausteine einer ASP.NET-Web-App beschrieben, die eine Web-API mithilfe von Azure Active Directory B2C aufruft.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 987fbce373b0b6696f13fb40aa2731c3c239aea4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156024"
---
# <a name="enable-authentication-in-web-apps-that-call-a-web-api-by-using-azure-ad-b2c"></a>Aktivieren der Authentifizierung in Web-Apps, die eine Web-API mithilfe von Azure AD B2C aufrufen

In diesem Artikel wird beschrieben, wie Sie einer ASP.NET-Webanwendung, die eine Web-API aufruft, die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen. Hier erfahren Sie, wie Sie eine ASP.NET Core-Webanwendung mit ASP.NET Core-Middleware erstellen, die das [OpenID Connect](openid-connect.md)-Protokoll verwendet. 

Wenn Sie diesen Artikel in Verbindung mit dem Artikel [Konfigurieren der Authentifizierung in einer Beispiel-Web-App, die eine Web-API aufruft](configure-authentication-sample-web-app-with-api.md) verwenden, ersetzen Sie die Beispiel-Web-App durch Ihre eigene Web-App.

Der Schwerpunkt dieses Artikels liegt auf dem Webanwendungsprojekt. Anweisungen zum Erstellen der Web-API finden Sie in der [Aufgabenliste des Web-API-Beispiels](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die Voraussetzungen und die Integrationsschritte unter [Konfigurieren der Authentifizierung in einer Beispiel-Web-App, die eine Web-API aufruft](configure-authentication-sample-web-app-with-api.md).

In den folgenden Abschnitten erfahren Sie, wie Sie einer ASP.NET-Webanwendung die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen.

## <a name="step-1-create-a-web-app-project"></a>Schritt 1: Erstellen eines Web-App-Projekts

Sie können ein vorhandenes ASP.NET MVC-Web-App-Projekt (Model View Controller) verwenden oder ein neues Projekt erstellen. Öffnen Sie zum Erstellen eines neuen Projekts eine Befehlsshell, und führen Sie dann den folgenden Befehl aus:

```dotnetcli
dotnet new mvc -o mywebapp
```

Mit dem vorherigen Befehl wird eine neue MVC-Web-App erstellt. Mit dem `-o mywebapp`-Parameter wird ein Verzeichnis mit dem Namen *mywebapp* und den Quelldateien für die App erstellt.

## <a name="step-2-add-the-authentication-libraries"></a>Schritt 2: Hinzufügen der Authentifizierungsbibliotheken

Fügen Sie zuerst die Microsoft Identity Web-Bibliothek hinzu. Dabei handelt es sich um eine Reihe von ASP.NET Core-Bibliotheken, die das Hinzufügen von Azure AD B2C-Authentifizierungs- und -Autorisierungsunterstützung zu Ihrer Web-App vereinfachen. Die Microsoft Identity Web-Bibliothek richtet die Authentifizierungspipeline mit cookiebasierter Authentifizierung ein. Sie übernimmt das Senden und Empfangen von HTTP-Authentifizierungsnachrichten, die Tokenüberprüfung, das Extrahieren von Ansprüchen und mehr.

Um die Microsoft Identity Web-Bibliothek hinzuzufügen, installieren Sie die Pakete, indem Sie die folgenden Befehle ausführen: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```dotnetcli
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.UI
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
Install-Package Microsoft.Identity.Web
Install-Package Microsoft.Identity.Web.UI
```

---

## <a name="step-3-initiate-the-authentication-libraries"></a>Schritt 3: Initiieren der Authentifizierungsbibliotheken

Die Microsoft Identity Web-Middleware verwendet eine Startklasse, die beim Start des Hostingprozesses ausgeführt wird. In diesem Schritt fügen Sie den zum Initiieren der Authentifizierungsbibliotheken erforderlichen Code hinzu.

Öffnen Sie `Startup.cs`, und fügen Sie am Anfang der Klasse die folgenden `using`-Deklarationen hinzu:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Da Microsoft Identity Web die cookiebasierte Authentifizierung zum Schützen Ihrer Web-App verwendet, werden die *SameSite*-Cookieeinstellungen mit dem folgenden Code festgelegt. Anschließend werden die `AzureADB2C`-Anwendungseinstellungen gelesen, und der Middlewarecontroller wird mit der zugehörigen Ansicht initiiert. 

Ersetzen Sie die Funktion `ConfigureServices(IServiceCollection services)` durch den folgenden Codeausschnitt: 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.Unspecified;
        // Handling SameSite cookie according to https://docs.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-3.1
        options.HandleSameSiteCookieCompatibility();
    });

    // Configuration to sign in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C")
            // Enable token acquisition to call downstream web API
            .EnableTokenAcquisitionToCallDownstreamApi(new string[] { Configuration["TodoList:TodoListScope"] })
            // Add refresh token in-memory cache
            .AddInMemoryTokenCaches();

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

Mit dem folgenden Code wird die Cookierichtlinie hinzugefügt und das Authentifizierungsmodell verwendet. Ersetzen Sie die Funktion `Configure` durch den folgenden Codeausschnitt: 

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    // Add the Microsoft Identity Web cookie policy
    app.UseCookiePolicy();
    app.UseRouting();
    // Add the ASP.NET Core authentication service
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // Add endpoints for Razor pages
        endpoints.MapRazorPages();
    });
};
```

## <a name="step-4-add-the-ui-elements"></a>Schritt 4: Hinzufügen der Benutzeroberflächenelemente

Verwenden Sie zum Hinzufügen von Benutzeroberflächenelementen eine Teilansicht. Die Teilansicht enthält Logik, mit der überprüft wird, ob ein Benutzer angemeldet ist. Wenn der Benutzer nicht angemeldet ist, rendert die Teilansicht die Schaltfläche „Anmelden“. Wenn der Benutzer angemeldet ist, werden der Anzeigename der Person und die Schaltfläche „Abmelden“ angezeigt.
  
Erstellen Sie mit dem folgenden Codeausschnitt im Ordner `Views/Shared` eine neue Datei mit dem Namen `_LoginPartial.cshtml`:

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package, and it defines some well-known actions, such as SignUp/In, SignOut, and EditProfile. -->
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="EditProfile">
                <button type="submit" class="btn btn-primary" style="margin-right:5px">Edit Profile</button>
            </form>
        </li>
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">
                <button type="submit" class="btn btn-primary">Sign Out</button>
            </form>
        </li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">
                <button type="submit" class="btn btn-primary">Sign Up/In</button>
            </form>
        </li>
    </ul>
}
```

Fügen Sie in `Views\Shared\_Layout.cshtml` die von Ihnen hinzugefügte Datei *_LoginPartial.cshtml* ein. Die Datei *_Layout.cshtml* stellt ein allgemeines Layout dar, das dem Benutzer beim Navigieren von Seite zu Seite eine konsistente Benutzeroberfläche bietet. Das Layout umfasst allgemeine Benutzeroberflächenelemente wie App-Kopfzeilen und -Fußzeilen.

> [!NOTE]
> Je nach .NET Core-Version und abhängig davon, ob Sie einer vorhandenen App eine Anmeldeoption hinzufügen, können die Benutzeroberflächenelemente anders aussehen. Wenn dies der Fall ist, müssen Sie *_LoginPartial* im Seitenlayout an der richtigen Position einfügen.

Öffnen Sie */Views/Shared/_Layout.cshtml*, und fügen Sie das folgende `div`-Element hinzu.

```razor
<div class="navbar-collapse collapse">
...
</div>
```

Ersetzen Sie dieses Element durch den folgenden Razor-Code:

```razor
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Claims">Claims</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="TodoList">To do list</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

Der vorstehende Razor-Code enthält einen Link zu den Aktionen `Claims` und `TodoList`, die Sie in den nächsten Schritten erstellen.

## <a name="step-5-add-the-claims-view"></a>Schritt 5: Hinzufügen der Ansicht „Claims“ (Ansprüche)

Fügen Sie zum Anzeigen der ID-Tokenansprüche unter dem Ordner `Views/Home` die Ansicht `Claims.cshtml` hinzu.

```razor
@using System.Security.Claims

@{
  ViewData["Title"] = "Claims";
}
<h2>@ViewData["Title"].</h2>

<table class="table-hover table-condensed table-striped">
  <tr>
    <th>Claim Type</th>
    <th>Claim Value</th>
  </tr>

  @foreach (Claim claim in User.Claims)
  {
    <tr>
      <td>@claim.Type</td>
      <td>@claim.Value</td>
    </tr>
  }
</table>
```

In diesem Schritt fügen Sie die Aktion `Claims` hinzu, die die Ansicht *Claims.cshtml* mit dem *Home*-Controller verknüpft. Hier wird das `[Authorize]`-Attribut verwendet, das den Zugriff auf die Aktion „Claims“ auf authentifizierte Benutzer beschränkt.

Fügen Sie im Controller */Controllers/HomeController.cs* die folgende Aktion hinzu:

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

Fügen Sie am Anfang der Klasse die folgende `using`-Deklaration hinzu:

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-todolistcshtml-view"></a>Schritt 6: Hinzufügen der Ansicht „TodoList.cshtml“ (Aufgabenliste)

Zum Aufrufen der Web-API „TodoList.cshtml“ benötigen Sie ein Zugriffstoken mit den richtigen Bereichen. In diesem Schritt fügen Sie dem `Home`-Controller eine Aktion hinzu. Fügen Sie unter dem Ordner `Views/Home` die Ansicht `TodoList.cshtml` hinzu.

```razor
@{
    ViewData["Title"] = "To do list";
}

<div class="text-left">
  <h1 class="display-4">Your access token</h1>
  @* Remove following line in production environments *@
  <code>@ViewData["accessToken"]</code>
</div>
```

Nachdem Sie die Ansicht hinzugefügt haben, fügen Sie die Aktion `TodoList` hinzu, die die Ansicht *TodoList.cshtml* mit dem *Home*-Controller verknüpft. Hier wird das `[Authorize]`-Attribut verwendet, das den Zugriff auf die Aktion „TodoList“ auf authentifizierte Benutzer beschränkt.  

Fügen Sie im Controller */Controllers/HomeController.cs* den folgenden Aktionsklassenmember hinzu, und fügen Sie den Tokenabrufdienst in Ihren Controller ein.

```csharp
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    // Add the token acquisition service member variable
    private readonly ITokenAcquisition _tokenAcquisition; 
    
    // Inject the acquisition service
    public HomeController(ILogger<HomeController> logger, ITokenAcquisition tokenAcquisition)
    {
        _logger = logger;
        // Set the acquisition service member variable
        _tokenAcquisition = tokenAcquisition;
    }

    // More code...
}
```

Fügen Sie dann die folgende Aktion hinzu, die veranschaulicht, wie Sie eine Web-API zusammen mit dem Bearertoken aufrufen. 

```csharp
[Authorize]
public async Task<IActionResult> TodoListAsync()
{
    // Acquire an access token with the relevant scopes.
    var accessToken = await _tokenAcquisition.GetAccessTokenForUserAsync(new[] { "https://your-tenant.onmicrosoft.com/tasks-api/tasks.read", "https://your-tenant.onmicrosoft.com/tasks-api/tasks.write" });
    
    // Remove this line in production environments    
    ViewData["accessToken"] = accessToken;

    using (HttpClient client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

        HttpResponseMessage response = await client.GetAsync("https://path-to-your-web-api");
    }

    return View();
}
```

## <a name="step-7-add-the-app-settings"></a>Schritt 7: Hinzufügen der App-Einstellungen

Die Azure AD B2C-Identitätsanbietereinstellungen werden in der Datei *appsettings.json* gespeichert. Öffnen Sie die Datei *appsettings.json*, und fügen Sie die App-Einstellungen hinzu, wie es unter [Konfigurieren der Authentifizierung in einer Beispiel-Web-App, die eine Web-API mithilfe von Azure AD B2C aufruft](configure-authentication-sample-web-app-with-api.md#step-5-configure-the-sample-web-app) im Abschnitt „Schritt 5: Konfigurieren der Beispiel-Web-App“ beschrieben ist.

## <a name="step-8-run-your-application"></a>Schritt 8: Ausführen der Anwendung

1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Navigieren Sie zu https://localhost:5001, und wählen Sie dann **Anmelden/Registrieren** aus.
1. Schließen Sie den Anmelde- oder Registrierungsvorgang ab.

Nachdem Sie sich erfolgreich in der App authentifiziert haben, überprüfen Sie Ihren Anzeigenamen auf der Navigationsleiste angezeigt. 

* Wenn Sie die Ansprüche anzeigen möchten, die das Azure AD B2C-Token an Ihre App zurückgibt, wählen Sie **Ansprüche** aus.
* Wenn Sie das Zugriffstoken anzeigen möchten, wählen Sie **Aufgabenliste** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:
* [Anpassen und Verbessern der Azure AD B2C-Authentifizierungsumgebung in Ihrer Web-App](enable-authentication-web-application-options.md)
* [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
