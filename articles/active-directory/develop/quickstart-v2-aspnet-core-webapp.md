---
title: 'Schnellstart: Hinzufügen der Anmeldung mit Microsoft Identity zu einer ASP.NET Core-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Microsoft-Anmeldung mithilfe von OpenID Connect in einer ASP.NET Core-Web-App implementieren.
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
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-other
ms.openlocfilehash: 7dad1d4840e24c1a2a7b65094ee5c28f45a73f44
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2022
ms.locfileid: "137802502"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Schnellstart: Hinzufügen von „Bei Microsoft anmelden“ zu einer ASP.NET Core-Web-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine ASP.NET Core-Web-App Benutzer aus einer beliebigen Azure AD-Organisation (Azure Active Directory) anmelden kann.  

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen folgende Schritte ausgeführt werden:
- Geben Sie **https://localhost:44321/** und **https://localhost:44321/signin-oidc** als **Umleitungs-URI** ein.
- Geben Sie unter **URL für Front-Channel-Abmeldung** die URL **https://localhost:44321/signout-oidc** ein. 

Der Autorisierungsendpunkt gibt Anforderungs-ID-Token aus.
> [!div class="nextstepaction"]
> [Diese Änderung für mich vornehmen]()

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-aspnet-core-project"></a>Schritt 2: Herunterladen des ASP.NET Core-Projekts

Führen Sie das Projekt aus.

> [!div class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
Sie haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Weitere Informationen

In diesem Abschnitt erhalten Sie eine Übersicht über den erforderlichen Code für die Benutzeranmeldung. Diese Übersicht kann hilfreich sein, um die Funktionsweise des Codes und die Hauptargumente zu verstehen und zu ermitteln, wie Sie einer vorhandenen ASP.NET Core-Anwendung eine Anmeldung hinzufügen.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
>
> ![Diagramm der Interaktion zwischen Webbrowser, Web-App und Microsoft Identity Platform in der Beispiel-App](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startklasse

Von der Middleware *Microsoft.AspNetCore.Authentication* wird eine Klasse vom Typ `Startup` verwendet. Sie wird ausgeführt, wenn der Hostprozess gestartet wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

    services.AddControllersWithViews(options =>
    {
        var policy = new AuthorizationPolicyBuilder()
            .RequireAuthenticatedUser()
            .Build();
        options.Filters.Add(new AuthorizeFilter(policy));
    });
   services.AddRazorPages()
        .AddMicrosoftIdentityUI();
}
```

Durch die Methode `AddAuthentication()` wird der Dienst konfiguriert, um die cookiebasierte Authentifizierung hinzuzufügen. Diese Authentifizierung kommt in Browserszenarien zum Einsatz und dient dazu, die Aufforderung auf OpenID Connect festzulegen.

Durch die Zeile mit `.AddMicrosoftIdentityWebApp` wird Ihrer Anwendung die Microsoft Identity Platform-Authentifizierung hinzugefügt. Die Anwendung wird anschließend für die Anmeldung von Benutzern basierend auf den Informationen im Abschnitt `AzureAD` der Konfigurationsdatei *appsettings.json* konfiguriert:

| *appsettings.json* (Schlüssel) | Beschreibung                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Anwendungs-ID (Client) der im Azure-Portal registrierten Anwendung.                                                                                       |
| `Instance`             | STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Dieser Wert ist in der Regel `https://login.microsoftonline.com/` (öffentliche Azure-Cloud). |
| `TenantId`             | Name Ihres Mandanten, Mandanten-ID (eine GUID) oder `common` für die Anmeldung von Benutzern mit Geschäfts-, Schul- oder Unikonto oder mit persönlichem Microsoft-Konto                             |

Die Methode `Configure()` enthält mit `app.UseAuthentication()` und `app.UseAuthorization()` zwei wichtige Methoden, um die genannte Funktion zu aktivieren. Außerdem müssen Sie in der `Configure()`-Methode die Routen von Microsoft Identity Web über mindestens einen Aufruf von `endpoints.MapControllerRoute()` oder einen Aufruf von `endpoints.MapControllers()` registrieren:

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
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Attribut zum Schützen eines Controllers oder von Methoden

Sie können einen Controller oder Controllermethoden mithilfe des `[Authorize]`-Attributs schützen. Dieses Attribut beschränkt den Zugriff auf den Controller oder die Methoden, indem nur authentifizierte Benutzer zugelassen werden. Wenn sich der Benutzer noch nicht authentifiziert hat, kann für den Zugriff auf den Controller eine Authentifizierungsabfrage gestartet werden.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Das GitHub-Repository mit diesem ASP.NET Core-Tutorial enthält Anleitungen und weitere Codebeispiele für Folgendes:

- Hinzufügen einer Authentifizierung zu einer neuen ASP.NET Core-Webanwendung
- Aufrufen von Microsoft Graph, anderer Microsoft-APIs oder Ihrer eigenen Web-APIs
- Hinzufügen der Autorisierung
- Anmelden von Benutzern in nationalen Clouds oder mit Identitäten sozialer Netzwerke

> [!div class="nextstepaction"]
> [Tutorials für ASP.NET Core-Web-Apps auf GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
