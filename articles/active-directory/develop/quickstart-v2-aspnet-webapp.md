---
title: 'Schnellstart: ASP.NET-Web-App, die Benutzer anmeldet'
titleSuffix: Microsoft identity platform
description: Laden Sie ein Codebeispiel herunter, das zeigt, wie sich eine ASP.NET Web-App die Azure AD-Benutzern anmelden kann, und führen Sie es aus.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", contperf-fy21q1, mode-other
ms.openlocfilehash: 734003bb94b0d1ccd4e622b698feeb41120a6041
ms.sourcegitcommit: 34d047300d800cf6ff7d9dd3e573a0d785f61abc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135919318"
---
# <a name="quickstart-aspnet-web-app-that-signs-in-azure-ad-users"></a>Schnellstart: ASP.NET-Web-App, die Azure AD-Benutzer anmeldet

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine ASP.NET-Web-App Benutzer aus Azure AD-Konten (Azure Active Directory) anmelden kann.

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie **https://localhost:44368/** als **Umleitungs-URI** eingeben.

> [!div class="nextstepaction"]
> [Diese Änderung für mich vornehmen]()

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesem Attribut konfiguriert.

#### <a name="step-2-download-the-project"></a>Schritt 2: Herunterladen des Projekts

Führen Sie das Projekt mit Visual Studio 2019 aus.
> [!div class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
Sie haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert.

1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Stammverzeichnisses. Extrahieren Sie es beispielsweise in *C:\Azure-Samples*.
   
   Es wird empfohlen, das Archiv in ein Verzeichnis in der Nähe des Stammverzeichnisses Ihres Laufwerks zu extrahieren, um Fehler zu vermeiden, die durch Beschränkungen der Pfadlänge unter Windows verursacht werden.
2. Öffnen Sie die Projektmappe in Visual Studio (*AppModelv2-WebApp-OpenIDConnect-DotNet.sln*).
3. Abhängig von der verwendeten Visual Studio-Version müssen Sie möglicherweise mit der rechten Maustaste auf das Projekt **AppModelv2-WebApp-OpenIDConnect-DotNet** klicken und **NuGet-Pakete wiederherstellen** auswählen.
4. Öffnen Sie die Paket-Manager-Konsole, indem Sie **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole** auswählen. Führen Sie dann `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r` aus.

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Weitere Informationen

In diesem Abschnitt erhalten Sie eine Übersicht über den erforderlichen Code für die Benutzeranmeldung. Diese Übersicht kann hilfreich sein, um die Funktionsweise des Codes und die Hauptargumente zu verstehen und zu ermitteln, wie Sie einer vorhandenen ASP.NET-Anwendung eine Anmeldung hinzufügen.


### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Diagramm der Interaktion zwischen Webbrowser, Web-App und Microsoft Identity Platform in der Beispiel-App](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>NuGet-Pakete der OWIN-Middleware

Sie können die Authentifizierungspipeline mit cookiebasierter Authentifizierung über OpenID Connect in ASP.NET mit OWIN-Middlewarepaketen einrichten. Sie können diese Pakete installieren, indem Sie die folgenden Befehle in der Paket-Manager-Konsole in Visual Studio ausführen:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN-Startklasse

Die OWIN-Middleware verwendet eine *Startklasse*, die beim Start des Hostingprozesses ausgeführt wird. In dieser Schnellstartanleitung befindet sich die Datei *startup.cs* im Stammordner. Der folgende Code zeigt die Parameter, die in dieser Schnellstartanleitung verwendet werden:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Hierbei gilt:  | BESCHREIBUNG |
> |---------|---------|
> | `ClientId`     | Die Anwendungs-ID der im Azure-Portal registrierten Anwendung |
> | `Authority`    | STS-Endpunkt (Security Token Service, Sicherheitstokendienst) für den zu authentifizierenden Benutzer. Dies ist in der Regel `https://login.microsoftonline.com/{tenant}/v2.0` für die öffentliche Cloud. In dieser URL ist *{tenant}* der Name Ihres Mandanten, Ihre Mandanten-ID oder `common` für einen Verweis auf den allgemeinen Endpunkt. (Der allgemeine Endpunkt wird für mehrinstanzenfähige Anwendungen verwendet.) |
> | `RedirectUri`  | Die URL, an die Benutzer nach der Authentifizierung über Microsoft Identity Platform umgeleitet werden |
> | `PostLogoutRedirectUri`     | Die URL, an die Benutzer nach der Abmeldung umgeleitet werden. |
> | `Scope`     | Die Liste der angeforderten Bereiche, getrennt durch Leerzeichen |
> | `ResponseType`     | Die Anforderung, dass die Antwort von der Authentifizierung einen Autorisierungscode und ein ID-Token enthält |
> | `TokenValidationParameters`     | Eine Liste von Parametern für die Tokenüberprüfung. In diesem Fall wird `ValidateIssuer` auf `false` festgelegt, um anzugeben, dass Anmeldungen von beliebigen persönlichen Kontotypen oder Geschäfts-, Schul- oder Unikontotypen akzeptiert werden können. |
> | `Notifications`     | Eine Liste von Delegaten, die für `OpenIdConnect`-Nachrichten ausgeführt werden können |


> [!NOTE]
> Zur Vereinfachung dieser Schnellstartanleitung wird `ValidateIssuer = false` festgelegt. Überprüfen Sie in einer echten Anwendung den Aussteller. Informationen dazu finden Sie in den Beispielen.

### <a name="authentication-challenge"></a>Authentifizierungsaufforderung

Sie können den Benutzer zur Anmeldung zwingen, indem Sie eine Authentifizierungsaufforderung in Ihrem Controller anfordern:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Das Anfordern einer Authentifizierungsaufforderung mithilfe dieser Methode ist optional. Dies wird in der Regel verwendet, wenn Sie möchten, dass auf eine Ansicht sowohl durch authentifizierte als auch durch nicht authentifizierte Benutzer zugegriffen werden kann. Alternativ können Sie Controller mit der im nächsten Abschnitt beschriebenen Methode schützen.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Attribut zum Schützen eines Controllers oder von Controlleraktionen

Sie können einen Controller oder Controlleraktionen mithilfe des `[Authorize]`-Attributs schützen. Dieses Attribut beschränkt den Zugriff auf den Controller oder die Aktionen, indem nur authentifizierte Benutzer auf die Aktionen im Controller zugreifen können. Eine Authentifizierungsaufforderung erfolgt dann automatisch, wenn ein nicht authentifizierter Benutzer auf eine Aktion oder einen Controller zugreifen möchte, die bzw. der mit dem Attribut `[Authorize]` versehen ist.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie das ASP.NET-Tutorial aus, um eine vollständige Schritt-für-Schritt-Anleitung zum Erstellen von Anwendungen und neuen Features zu erhalten, einschließlich einer vollständigen Erläuterung dieses Schnellstarts.

> [!div class="nextstepaction"]
> [Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App](tutorial-v2-asp-webapp.md)
