---
title: 'Schnellstart: Hinzufügen der Anmeldung mit Microsoft Identity zu einer ASP.NET Core-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Microsoft-Anmeldung mithilfe von OpenID Connect in einer ASP.NET Core-Web-App implementieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 534e922436ef279c849ccd80eb32387222c9be15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588407"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Schnellstart: Hinzufügen von „Bei Microsoft anmelden“ zu einer ASP.NET Core-Web-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine ASP.NET Core-Web-App Benutzer aus einer beliebigen Azure AD-Organisation (Azure Active Directory) anmelden kann.  

> [!div renderon="docs"]
> Das folgende Diagramm zeigt die Funktionsweise der Beispiel-App:
>
> ![Diagramm der Interaktion zwischen Webbrowser, Web-App und Microsoft Identity Platform in der Beispiel-App](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Voraussetzungen
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) oder [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Registrieren und Herunterladen der App
> Sie haben zwei Möglichkeiten, mit der Anwendungserstellung zu beginnen: automatische oder manuelle Konfiguration.
>
> ### <a name="automatic-configuration"></a>Automatische Konfiguration
> Wenn Sie die App automatisch konfigurieren und dann das Codebeispiel herunterladen möchten, führen Sie die folgenden Schritte aus:
>
> 1. Navigieren Sie zur <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure-Portal-Seite für die App-Registrierung</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="manual-configuration"></a>Manuelle Konfiguration
> Wenn Sie die Anwendung und das Codebeispiel manuell konfigurieren möchten, verwenden Sie die folgenden Verfahren.
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, in dem Sie die Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein. Geben Sie beispielsweise **AspNetCore-Quickstart** ein. Benutzern Ihrer App wird dieser Name angezeigt. Sie können ihn später ändern.
> 1. Geben Sie **https://localhost:44321/signin-oidc** als **Umleitungs-URI** ein.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
> 1. Geben Sie unter **URL für Front-Channel-Abmeldung** die URL **https://localhost:44321/signout-oidc** ein.
> 1. Wählen Sie für **Implizite Genehmigung und Hybridflows** die Option **ID-Token** aus.
> 1. Wählen Sie **Speichern** aus.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen folgende Schritte ausgeführt werden:
> - Geben Sie **https://localhost:44321/** und **https://localhost:44321/signin-oidc** als **Umleitungs-URI** ein.
> - Geben Sie unter **URL für Front-Channel-Abmeldung** die URL **https://localhost:44321/signout-oidc** ein. 
>
> Der Autorisierungsendpunkt gibt Anforderungs-ID-Token aus.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-aspnet-core-project"></a>Schritt 2: Herunterladen des ASP.NET Core-Projekts

> [!div renderon="docs"]
> [ASP.NET Core-Lösung herunterladen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Führen Sie das Projekt aus.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
> Sie haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Schritt 3: Konfigurieren Ihres ASP.NET Core-Projekts
> 1. Extrahieren Sie das ZIP-Archiv in einem lokalen Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Extrahieren Sie es beispielsweise in *C:\Azure-Samples*.
> 
>    Es wird empfohlen, das Archiv in ein Verzeichnis in der Nähe des Stammverzeichnisses Ihres Laufwerks zu extrahieren, um Fehler zu vermeiden, die durch Beschränkungen der Pfadlänge unter Windows verursacht werden.
> 1. Öffnen Sie die Projektmappe in Visual Studio 2019.
> 1. Öffnen Sie die Datei *appsettings.json*, und ändern Sie den folgenden Code:
>
>    ```json
>    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Ersetzen Sie `Enter_the_Application_Id_here` durch die Anwendungs-ID (Client) der im Azure-Portal registrierten Anwendung. Den Wert für **Anwendungs-ID (Client)** finden Sie auf der Seite **Übersicht** der App.
>    - Ersetzen Sie `common` durch eine der folgenden Optionen:
>       - Wenn Ihre Anwendung **Nur Konten in diesem Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch die Verzeichnis-ID (Mandant) (eine GUID) oder durch den Mandantennamen (beispielsweise `contoso.onmicrosoft.com`). Die **Verzeichnis-ID (Mandant)** finden Sie auf der Seite **Übersicht** der App.
>       - Falls Ihre Anwendung **Konten in einem beliebigen Organisationsverzeichnis** unterstützt, ersetzen Sie diesen Wert durch `organizations`.
>       - Wenn Ihre Anwendung **Alle Microsoft-Kontobenutzer** unterstützt, behalten Sie `common` für diesen Wert bei.
>
> Ändern Sie in dieser Schnellstartanleitung keine anderen Werte in der Datei *appsettings.json*.
>
> #### <a name="step-4-build-and-run-the-application"></a>Schritt 4: Erstellen und Ausführen der Anwendung
>
> Erstellen Sie die App in Visual Studio, und führen Sie sie aus. Wählen Sie hierzu im Menü **Debuggen** die Option **Debuggen starten** aus, oder drücken Sie F5.
>
> Sie werden zur Eingabe Ihrer Anmeldeinformationen aufgefordert und anschließend gebeten, Ihre Einwilligung für die von Ihrer App benötigten Berechtigungen zu geben. Wählen Sie in der Zustimmungsaufforderung **Akzeptieren** aus.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Screenshot: Dialogfeld für die Einwilligung mit den Berechtigungen, die die App vom Benutzer anfordert":::
>
> Nachdem Sie Ihre Einwilligung für die angeforderten Berechtigungen gegeben haben, wird in der App angezeigt, dass Sie sich erfolgreich mit Ihren Azure Active Directory-Anmeldeinformationen angemeldet haben.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Screenshot: Webbrowser mit der ausgeführten Web-App und dem angemeldeten Benutzer":::

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
