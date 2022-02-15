---
title: 'Schnellstart: Schützen einer ASP.NET Core-Web-API mit Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung wird ein Codebeispiel heruntergeladen und geändert, um zu veranschaulichen, wie Sie eine ASP.NET Core-Web-API schützen, indem Sie Microsoft Identity Platform für die Autorisierung verwenden.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/11/2022
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: devx-track-csharp, "scenarios:getting-started", "languages:aspnet-core", mode-api
ms.openlocfilehash: 46f8e744c1f5296a94a3106a1815333cf9a0b047
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2022
ms.locfileid: "138042346"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Schnellstart: Schützen einer ASP.NET Core-Web-API mit Microsoft Identity Platform

> [!div renderon="docs"]
> Willkommen! Dies ist wahrscheinlich nicht die Seite, die Sie erwartet haben. Während wir an einer Korrektur arbeiten, sollten Sie über diesen Link zum richtigen Artikel gelangen:
>
> > [Schnellstart: Schützen einer ASP.NET Core-Web-API](web-api-quickstart.md?pivots=devlang-aspnet-core)
> 
> Wir entschuldigen uns für die Unannehmlichkeiten und bitten Sie um Geduld, während wir an einer Lösung arbeiten.

> [!div renderon="portal" class="sxs-lookup"]
> In dieser Schnellstartanleitung laden Sie ein ASP.NET Core-Web-API-Codebeispiel herunter und überprüfen, wie es den Ressourcenzugriff auf autorisierte Konten beschränkt. Das Beispiel unterstützt die Autorisierung von persönlichen Microsoft-Konten sowie Konten in einer beliebigen Azure AD-Organisation (Azure Active Directory).
> 
> 
> ## <a name="prerequisites"></a>Voraussetzungen
> 
> - Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Azure Active Directory-Mandant](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) oder [Visual Studio Code](https://code.visualstudio.com/)
> 
> ## <a name="step-1-register-the-application"></a>Schritt 1: Registrieren der Anwendung
> 
> Registrieren Sie zunächst die Web-API in Ihrem Azure AD-Mandanten, und fügen Sie einen Bereich hinzu:
> 
> 1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, in dem Sie die Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein. Geben Sie beispielsweise **AspNetCoreWebApi-Quickstart** ein. Benutzern Ihrer App wird dieser Name angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Optionen **Eine API verfügbar machen** > **Bereich hinzufügen** aus. Übernehmen Sie für **Anwendungs-ID-URI** den Standardwert, indem Sie **Speichern und fortfahren** auswählen. Geben Sie dann die folgenden Informationen ein:
>     - **Bereichsname**: `access_as_user`
>    - **Zum Einwilligen berechtigte Personen**: **Administratoren und Benutzer**
>    - **Anzeigename der Administratoreinwilligung**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beschreibung der Administratoreinwilligung**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Anzeigename der Benutzereinwilligung**: `Access AspNetCoreWebApi-Quickstart`
>    - **Beschreibung der Benutzereinwilligung**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Status:** **Aktiviert**
> 1. Wählen Sie **Bereich hinzufügen** aus, um das Hinzufügen des Bereichs abzuschließen.
> 
> ## <a name="step-2-download-the-aspnet-core-project"></a>Schritt 2: Herunterladen des ASP.NET Core-Projekts
> 
> [Laden Sie die ASP.NET Core-Lösung von GitHub herunter.](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip)
> 
> [!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]
> 
> 
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Schritt 3: Konfigurieren des ASP.NET Core-Projekts
> 
> In diesem Schritt wird der Beispielcode für die Verwendung der zuvor erstellten App-Registrierung konfiguriert.
> 
> 1. Extrahieren Sie das ZIP-Archiv in einem Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Extrahieren Sie es beispielsweise in *C:\Azure-Samples*.
> 
>    Es wird empfohlen, das Archiv in ein Verzeichnis in der Nähe des Stammverzeichnisses Ihres Laufwerks zu extrahieren, um Fehler zu vermeiden, die durch Beschränkungen der Pfadlänge unter Windows verursacht werden.
> 
> 1. Öffnen Sie die Lösung aus dem Ordner *webapi* in Ihrem Code-Editor.
> 1. Öffnen Sie die Datei *appsettings.json*, und ändern Sie den folgenden Code:
> 
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
> 
>    - Ersetzen Sie `Enter_the_Application_Id_here` durch die Anwendungs-ID (Client) der im Azure-Portal registrierten Anwendung. Sie finden die Anwendungs-ID (Client) auf der Seite **Übersicht** der App.
>    - Ersetzen Sie `Enter_the_Tenant_Info_Here` durch eine der folgenden Optionen:
>       - Wenn Ihre Anwendung **Nur Konten in diesem Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch die Verzeichnis-ID (Mandant) (eine GUID) oder durch den Mandantennamen (beispielsweise `contoso.onmicrosoft.com`). Die Verzeichnis-ID (Mandant) finden Sie auf der Seite **Übersicht** der App.
>       - Falls Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch `organizations`.
>       - Wenn Ihre Anwendung **Alle Microsoft-Kontobenutzer** unterstützt, behalten Sie `common` für diesen Wert bei.
> 
> Ändern Sie in dieser Schnellstartanleitung keine anderen Werte in der Datei *appsettings.json*.
> 
> ## <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
> 
> Die Web-API empfängt ein Token von einer Clientanwendung, und das Token wird durch den Code in der Web-API überprüft. Eine ausführlichere Erläuterung dieses Szenarios finden Sie unter [Szenario: Geschützte Web-API](scenario-protected-web-api-overview.md).
> 
> ### <a name="startup-class"></a>Startklasse
> 
> Von der Middleware *Microsoft.AspNetCore.Authentication* wird eine Klasse vom Typ `Startup` verwendet. Sie wird ausgeführt, wenn der Hostprozess gestartet wird. In der zugehörigen Methode `ConfigureServices` wird die von *Microsoft.Identity.Web* bereitgestellte Erweiterungsmethode `AddMicrosoftIdentityWebApi` aufgerufen.
> 
> ```csharp
>     public void ConfigureServices(IServiceCollection services)
>     {
>         services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
>                 .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
>     }
> ```
> 
> Durch die Methode `AddAuthentication()` wird der Dienst konfiguriert, um die JwtBearer-basierte Authentifizierung hinzuzufügen.
> 
> Durch die Zeile mit `.AddMicrosoftIdentityWebApi` wird Ihrer Web-API die Microsoft Identity Platform-Autorisierung hinzugefügt. Diese wird anschließend für die Überprüfung von Zugriffstoken konfiguriert, die von Microsoft Identity Platform ausgegeben werden – basierend auf den Informationen im Abschnitt `AzureAD` der Konfigurationsdatei *appsettings.json*:
> 
> | *appsettings.json* (Schlüssel) | Beschreibung                                                                                                                                                          |
> |------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `ClientId`             | Anwendungs-ID (Client) der im Azure-Portal registrierten Anwendung.                                                                                       |
> | `Instance`             | STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Dieser Wert ist in der Regel `https://login.microsoftonline.com/` (öffentliche Azure-Cloud). |
> | `TenantId`             | Name Ihres Mandanten, zugehörige Mandanten-ID (eine GUID) oder `common` für die Anmeldung von Benutzern mit Geschäfts-, Schul- oder Unikonto oder mit persönlichem Microsoft-Konto.                             |
> 
> Die Methode `Configure()` enthält mit `app.UseAuthentication()` und `app.UseAuthorization()` zwei wichtige Methoden, um die genannte Funktion zu aktivieren:
> 
> ```csharp
> // The runtime calls this method. Use this method to configure the HTTP request pipeline.
> public void Configure(IApplicationBuilder app, IHostingEnvironment env)
> {
>     // more code
>     app.UseAuthentication();
>     app.UseAuthorization();
>     // more code
> }
> ```
> 
> ### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Schützen eines Controllers, der Methode eines Controllers oder einer Razor-Seite
> 
> Sie können einen Controller oder Controllermethoden mithilfe des `[Authorize]`-Attributs schützen. Dieses Attribut beschränkt den Zugriff auf den Controller oder die Methoden, indem nur authentifizierte Benutzer zugelassen werden. Wenn sich der Benutzer noch nicht authentifiziert hat, kann für den Zugriff auf den Controller eine Authentifizierungsabfrage gestartet werden.
> 
> ```csharp
> namespace webapi.Controllers
> {
>     [Authorize]
>     [ApiController]
>     [Route("[controller]")]
>     public class WeatherForecastController : ControllerBase
> ```
> 
> ### <a name="validation-of-scope-in-the-controller"></a>Überprüfen des Bereichs im Controller
> 
> Durch den Code in der API wird mithilfe von `HttpContext.VerifyUserHasAnyAcceptedScope> (scopeRequiredByApi);` überprüft, ob die erforderlichen Bereiche im Token enthalten sind:
> 
> ```csharp
> namespace webapi.Controllers
> {
>     [Authorize]
>     [ApiController]
>     [Route("[controller]")]
>     public class WeatherForecastController : ControllerBase
>     {
>         // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
>         static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };
> 
>         [HttpGet]
>         public IEnumerable<WeatherForecast> Get()
>         {
>             HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
> 
>             // some code here
>         }
>     }
> }
> ```
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>Nächste Schritte
> 
> Das GitHub-Repository mit diesem Codebeispiel für die ASP.NET Core-Web-API enthält Anleitungen und weitere Codebeispiele für Folgendes:
> 
> - Hinzufügen einer Authentifizierung zu einer neuen ASP.NET Core-Web-API
> - Aufrufen der Web-API über eine Desktopanwendung
> - Aufrufen von Downstream-APIs wie Microsoft Graph und anderen Microsoft-APIs
> 
> > [!div class="nextstepaction"]
> > [Tutorials für die ASP.NET Core-Web-API auf GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
