---
title: Migrieren von OWIN-basierten Web-APIs zu b2clogin.com oder einer benutzerdefinierten Domäne
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie eine .NET-Web-API für die Unterstützung mehrerer Tokenaussteller aktivieren können, während Sie Ihre Anwendungen zu b2clogin.com migrieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d11ce0d3f3890cfb2aa2ea7f957370d6739b6fe3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036358"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>Migrieren einer OWIN-basierten Web-API zu b2clogin.com oder einer benutzerdefinierten Domäne

In diesem Artikel wird ein Verfahren zum Aktivieren der Unterstützung für mehrere Tokenaussteller in Web-APIs beschrieben, die [Open Web Interface für .NET (OWIN)](http://owin.org/) implementieren. Die Unterstützung mehrerer Tokenendpunkte ist nützlich, wenn Sie Azure AD B2C-APIs (Azure Active Directory B2C) und die zugehörigen Anwendungen zwischen Domänen migrieren. Beispiel: eine Migration von *login.microsoftonline.com* zu *b2clogin.com* oder einer [benutzerdefinierten Domäne](custom-domain.md).

Indem Sie in Ihrer API die Unterstützung für die Annahme von Token hinzufügen, die sowohl von b2clogin.com als auch von login.microsoftonline.com oder einer benutzerdefinierten Domäne ausgestellt wurden, können Sie Ihre Webanwendungen schrittweise migrieren, bevor Sie die Unterstützung für die von login.microsoftonline.com ausgestellten Token aus der API entfernen.

Die folgenden Abschnitte enthalten ein Beispiel für das Aktivieren mehrerer Aussteller in einer Web-API, die [Microsoft OWIN][katana]-Middlewarekomponenten (Katana) verwenden. Die Codebeispiele sind zwar für die Microsoft OWIN-Middleware spezifisch, das allgemeine Verfahren gilt jedoch auch für andere OWIN-Bibliotheken.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen die folgenden Azure AD B2C-Ressourcen, bevor Sie mit den Schritten dieses Artikels fortfahren können:

* In Ihrem Mandanten erstellte [Benutzerflows](tutorial-create-user-flows.md?pivots=b2c-user-flow) oder [benutzerdefinierte Richtlinien](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="get-token-issuer-endpoints"></a>Abrufen von Tokenausstellerendpunkten

Sie müssen zuerst die Tokenausstellerendpunkt-URIs für jeden Aussteller abrufen, den Sie in Ihrer API unterstützen möchten. Damit die Endpunkte *b2clogin.com* und *login.microsoftonline.com* von Ihrem Azure AD B2C-Mandanten unterstützt werden, führen Sie im Azure-Portal die folgenden Schritte aus:

Wählen Sie zunächst einen der vorhandenen Benutzerflows aus:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure AD B2C-Mandanten.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows (Richtlinien)** aus.
1. Wählen Sie eine vorhandene Richtlinie (etwa *B2C_1_signupsignin1*) und dann **Benutzerflow ausführen** aus.
1. Wählen Sie im oberen Bereich der Seite unterhalb der Überschrift **Benutzerflow ausführen** den Link aus, um zum OpenID Connect-Ermittlungsendpunkt für diesen Benutzerflow zu navigieren.

    ![Bekannter URI-Link auf der Seite „Jetzt ausführen“ im Azure-Portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Notieren Sie sich den Wert `issuer`, der auf der im Browser geöffneten Seite angezeigt wird, z. B.:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Wählen Sie im Dropdownmenü **Domäne auswählen** die andere Domäne aus, wiederholen Sie anschließend die beiden vorherigen Schritte, und notieren Sie den Wert für `issuer`.

Sie sollten nun zwei URIs notiert haben, die ungefähr wie folgt aussehen:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Wenn Sie anstelle von Benutzerflows benutzerdefinierte Richtlinien verwenden, können Sie die Aussteller-URIs mit einem ähnlichen Prozess abrufen.

1. Navigieren Sie zu Ihrem Azure AD B2C-Mandanten.
1. Wählen Sie **Framework für die Identitätsfunktion** aus.
1. Wählen Sie eine der Richtlinien der vertrauenden Seite aus, etwa *B2C_1A_signup_signin*.
1. Wählen Sie im Dropdownmenü **Domäne auswählen** eine Domäne aus, etwa *yourtenant.b2clogin.com*.
1. Wählen Sie den Link unter **OpenID Connect-Ermittlungsendpunkt** aus.
1. Notieren Sie sich den Wert `issuer`.
1. Führen Sie die Schritte 4 bis 6 für die andere Domäne aus, z. B. *login.microsoftonline.com*.

## <a name="get-the-sample-code"></a>Laden Sie den Beispielcode herunter

Sie haben nun beide Tokenendpunkt-URIs und müssen Ihren Code aktualisieren, um anzugeben, dass beide Endpunkte gültige Aussteller sind. Wenn Sie sich ein Beispiel ansehen möchten, laden Sie die Beispielanwendung herunter, oder klonen Sie sie, und aktualisieren Sie dann das Beispiel, sodass beide Endpunkte als gültige Aussteller unterstützt werden.

Laden Sie das Archiv [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive] herunter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Aktivieren mehrerer Aussteller in der Web-API

In diesem Abschnitt aktualisieren Sie den Code, um anzugeben, dass beide Tokenausstellerendpunkte gültig sind.

1. Öffnen Sie die Projektmappe **B2C-WebAPI-DotNet.sln** in Visual Studio.
1. Öffnen Sie im Projekt **TaskService** die Datei *TaskService\\App_Start\\ **Startup.Auth.cs**.* im Editor.
1. Fügen Sie am Anfang der Datei die folgende `using`-Anweisung hinzu:

    `using System.Collections.Generic;`
1. Fügen Sie der Definition [`TokenValidationParameters`][tokenvalidationparameters] die Eigenschaft [`ValidIssuers`][validissuers] hinzu, und geben Sie die beiden im vorherigen Abschnitt notierten URIs an:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` wird von MSAL.NET angegeben und von der OWIN-Middleware im nächsten Codeabschnitt von *Startup.Auth.cs* verwendet. Bei Angabe mehrerer gültiger Aussteller wird der OWIN-Anwendungspipeline mitgeteilt, dass beide Tokenendpunkte gültige Aussteller sind.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Wie bereits zuvor erwähnt, bieten andere OWIN-Bibliotheken in der Regel eine ähnliche Funktion zur Unterstützung mehrerer Aussteller. Dieser Artikel enthält zwar keine Beispiele für jede Bibliothek, für die meisten Bibliotheken ist das Verfahren jedoch ähnlich.

## <a name="switch-endpoints-in-web-app"></a>Wechseln von Endpunkten in einer Web-App

Beide URIs werden von Ihrer Web-API unterstützt. Nun müssen Sie Ihre Webanwendung so aktualisieren, dass sie Token vom Endpunkt „b2clogin.com“ abruft.

So können Sie beispielsweise die Beispielwebanwendung zur Verwendung des neuen Endpunkts konfigurieren, indem Sie den Wert `ida:AadInstance` in der Datei *TaskWebApp\\**Web.config** des Projekts „* TaskWebApp“ ändern.

Ändern Sie den Wert `ida:AadInstance` in der Datei *Web.config* von TaskWebApp so, dass auf `{your-b2c-tenant-name}.b2clogin.com` anstelle von `login.microsoftonline.com` verwiesen wird.

Vorher:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Nachher (Ersetzen Sie `{your-b2c-tenant}` durch den Namen Ihres B2C-Mandanten.):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Werden die Endpunktzeichenfolgen während der Ausführung der Web-App erstellt, werden beim Anfordern von Token die auf b2clogin.com basierenden Endpunkte verwendet.

Bei Verwendung einer benutzerdefinierten Domäne:

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde eine Methode zum Konfigurieren einer Web-API vorgestellt, die die Microsoft OWIN-Middleware (Katana) implementiert, um Token von mehreren Ausstellerendpunkten zu akzeptieren. Wie Sie vielleicht bemerkt haben, enthalten die Dateien *Web.Config* der Projekte „TaskService“ und „TaskWebApp“ verschiedene andere Zeichenfolgen, die geändert werden müssen, wenn Sie diese Projekte für Ihren eigenen Mandanten erstellen und ausführen möchten. Sie können diese Projekte gerne anpassen, wenn Sie sie in Aktion sehen möchten, eine ausführliche Anleitung ist jedoch nicht Gegenstand dieses Artikels.

Weitere Informationen zu den verschiedenen Arten von Sicherheitstoken, die von Azure AD B2C ausgegeben werden, finden Sie in der [Übersicht über Token in Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
