---
title: Aktivieren der Authentifizierung in einer Webanwendung mit den Bausteinen von Azure Active Directory B2C
description: Hier finden Sie Informationen zu den Bausteinen von Azure Active Directory B2C für die Anmeldung und Registrierung von Benutzern in einer ASP.NET-Webanwendung.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 2a89f2c5179e9280e09741d8fc524406698c31e0
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071531"
---
# <a name="enable-authentication-in-your-own-web-application-using-azure-active-directory-b2c"></a>Aktivieren der Authentifizierung in Ihrer eigenen Webanwendung mit Azure Active Directory B2C

In diesem Artikel wird beschrieben, wie Sie Ihrer eigenen ASP.NET-Webanwendung die Authentifizierung von Azure Active Directory B2C (Azure AD B2C) hinzufügen. Erfahren Sie, wie Sie eine ASP.NET Core-Webanwendung mit ASP.NET Core-Middleware erstellen, die das [OpenID Connect](openid-connect.md)-Protokoll verwendet. Verwenden Sie diesen Artikel in Verbindung mit dem Artikel [Konfigurieren der Authentifizierung in einer Beispielwebanwendung](configure-authentication-sample-web-app.md), und ersetzen Sie dabei die Beispiel-Web-App durch Ihre eigene Web-App.

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die Voraussetzungen und die Integrationsschritte unter [Konfigurieren der Authentifizierung in einer Beispielwebanwendung](configure-authentication-sample-web-app.md).

## <a name="create-a-web-app-project"></a>Erstellen eines Web-App-Projekts

Sie können ein vorhandenes ASP.NET MVC-Web-App-Projekt verwenden oder ein neues Projekt erstellen. Öffnen Sie zum Erstellen eines neuen Projekts eine Befehlsshell, und geben Sie den folgenden Befehl ein:

```dotnetcli
dotnet new mvc -o mywebapp
```

Der vorherige Befehl:

* Erstellt eine neue MVC-Web-App.  
* Mit dem `-o mywebapp`-Parameter wird ein Verzeichnis mit dem Namen *mywebapp* und den Quelldateien für die App erstellt.

## <a name="add-the-authentication-libraries"></a>Hinzufügen der Authentifizierungsbibliotheken

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


## <a name="initiate-the-authentication-libraries"></a>Initiieren der Authentifizierungsbibliotheken

Die Microsoft Identity Web-Middleware verwendet eine Startklasse, die beim Start des Hostingprozesses ausgeführt wird. In diesem Schritt fügen Sie den zum Initiieren der Authentifizierungsbibliotheken erforderlichen Code hinzu.

Öffnen Sie `Startup.cs`, und fügen Sie am Anfang der Klasse die folgenden `using`-Deklarationen hinzu:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Da Microsoft Identity Web die cookiebasierte Authentifizierung zum Schützen Ihrer Web-App verwendet, werden die *SameSite*-Cookieeinstellungen mit dem folgenden Code festgelegt. Anschließend werden die `AzureAdB2C`-Anwendungseinstellungen gelesen, und der Middlewarecontroller wird mit der zugehörigen Ansicht initiiert. 

Ersetzen Sie die Funktion `ConfigureServices(IServiceCollection services)` durch den folgenden Codeausschnitt. 

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

    // Configuration to sign-in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

Mit dem folgenden Code wird die Cookierichtlinie hinzugefügt und das Authentifizierungsmodell verwendet. Ersetzen Sie die Funktion `Configure` durch den folgenden Codeausschnitt. 

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

## <a name="add-the-ui-elements"></a>Hinzufügen der Benutzeroberflächenelemente

Um Benutzeroberflächenelemente hinzuzufügen, verwenden Sie eine Teilansicht, die Logik enthält, mit der überprüft wird, ob ein Benutzer angemeldet ist oder nicht. Wenn der Benutzer nicht angemeldet ist, rendert die Teilansicht die Schaltfläche „Anmelden“. Wenn der Benutzer angemeldet ist, werden der Anzeigename des Benutzers und die Schaltfläche „Abmelden“ angezeigt.
  
Erstellen Sie mit dem folgenden Codeausschnitt im Ordner `Views/Shared` eine neue Datei mit dem Namen `_LoginPartial.cshtml`:

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package and
            it defines some well known actions such as SignUp/In, SignOut and EditProfile-->
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
  </ul>
  <partial name="_LoginPartial" />
</div>
```

Der vorstehende Razor-Code enthält einen Link zur Aktion `Claims`, die Sie im nächsten Schritt erstellen.

## <a name="add-the-claims-view"></a>Hinzufügen der Ansicht „Claims“ (Ansprüche)

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

Fügen Sie im Controller `/Controllers/HomeController.cs` die folgende Aktion hinzu.

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

## <a name="add-the-app-settings"></a>Hinzufügen der App-Einstellungen

Die Azure AD B2C-Identitätsanbietereinstellungen werden in der Datei `appsettings.json` gespeichert. Öffnen Sie die Datei „appsettings.json“, und fügen Sie die folgenden Einstellungen hinzu:

```JSon
"AzureAdB2C": {
  "Instance": "https://<your-tenant-name>.b2clogin.com",
  "ClientId": "<web-app-application-id>",
  "Domain": "<your-b2c-domain>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

Die erforderlichen Informationen werden im Artikel [Konfigurieren der Authentifizierung in einer Beispielwebanwendung](configure-authentication-sample-web-app.md) beschrieben. Verwenden Sie folgende Einstellungen:

* **Instanz:** Ersetzen Sie `<your-tenant-name>` durch den ersten Teil des [Azure AD B2C-Mandantennamens](tenant-management.md#get-your-tenant-name). Beispiel: `https://contoso.b2clogin.com`.
* **Domäne:** Ersetzen Sie `<your-b2c-domain>` durch den vollständigen [Azure AD B2C-Mandantennamen](tenant-management.md#get-your-tenant-name). Beispiel: `contoso.onmicrosoft.com`.
* **Client-ID**: Ersetzen Sie `<web-app-application-id>` durch die Anwendungs-ID aus [Schritt 2](configure-authentication-sample-web-app.md#step-2-register-a-web-application).
* **Richtlinienname**: Ersetzen Sie `<your-sign-up-in-policy>` durch die Benutzerflows, die Sie in [Schritt 1](configure-authentication-sample-web-app.md#step-1-configure-your-user-flow) erstellt haben.

## <a name="run-your-application"></a>Ausführen der Anwendung

1. Erstellen Sie das Projekt, und führen Sie es aus.
1. Navigieren Sie zu https://localhost:5001. 
1. Wählen Sie **Anmelden/Registrieren** aus.
1. Schließen Sie den Registrierungs- oder Anmeldevorgang ab.

Nachdem Sie sich erfolgreich authentifiziert haben, wird Ihr Anzeigename auf der Navigationsleiste angezeigt. Wenn Sie die Ansprüche anzeigen möchten, die das Azure AD B2C-Token an Ihre App zurückgibt, wählen Sie **Ansprüche** aus.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [die Azure AD B2C-Authentifizierungsumgebung für Ihre Web-App anpassen und verbessern](enable-authentication-web-application-options.md) können.