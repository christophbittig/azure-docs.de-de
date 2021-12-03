---
title: Überprüfen von Bereichen und App-Rollen mit geschützter Web-API | Azure
titleSuffix: Microsoft identity platform
description: Vergewissern Sie sich, dass die API nur von Anwendungen mit den richtigen Bereichen (im Namen von Benutzern) und von Daemon-Apps mit den richtigen Anwendungsrollen aufgerufen wird.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2021
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 23b9f2e8bb14d74a7f2b722945251acb182b71db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222569"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Geschützte Web-API: Überprüfen von Bereichen und App-Rollen

Dieser Artikel beschreibt, wie Sie Ihrer Web-API eine Berechtigung hinzufügen können. Dieser Schutz stellt sicher, dass die API nur aufgerufen wird von:

- Anwendungen im Auftrag von Benutzern, die über die richtigen Bereiche verfügen
- Daemon-Apps mit den richtigen Anwendungsrollen

Die Codeausschnitte in diesem Artikel stammen aus den folgenden Codebeispielen auf GitHub:

- [Inkrementelles Tutorial zur ASP.NET Core-Web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
- [Beispiel zur ASP.NET-Web-API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Um eine ASP.NET- oder ASP.NET Core-Web-API zu schützen, müssen Sie das `[Authorize]`-Attribut einem der folgenden Elemente hinzufügen:

- dem Controller selbst, wenn alle Aktionen des Controllers geschützt werden sollen
- einer individuellen Controlleraktion für Ihre API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     // ...
    }
```

Aber dieser Schutz reicht nicht aus. Es wird nur garantiert, dass ASP.NET und ASP.NET Core das Token validieren. Ihre API muss überprüfen, ob das zum Aufrufen der API verwendete Token mit den erwarteten Ansprüchen angefordert wurde. Diese Ansprüche müssen insbesondere überprüft werden:

- Die _Bereiche_, wenn die API im Namen eines Benutzers aufgerufen wird.
- Die _App-Rollen_, wenn die API von einer Daemon-App aufgerufen wird.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Überprüfen von Bereichen in APIs, die im Namen von Benutzern aufgerufen werden

Wenn Ihre API von einer Client-App im Namen eines Benutzers aufgerufen wird, muss sie ein Bearertoken anfordern, das bestimmte Bereiche für die API enthält. Weitere Informationen finden Sie unter [Codekonfiguration | Bearertoken](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core können Sie Microsoft.Identity.Web verwenden, um Bereiche in jeder Controlleraktion zu überprüfen. Sie können sie auch auf der Controllerebene oder für die gesamte Anwendung überprüfen.

#### <a name="verify-the-scopes-on-each-controller-action"></a>Überprüfen der Bereiche bei jeder Controlleraktion

Sie können die Bereiche in der Controlleraktion mithilfe des `[RequiredScope]`-Attributs überprüfen. Dieses Attribut weist mehrere Außerkraftsetzungen auf. Eine, die die erforderlichen Bereiche direkt übernimmt, und eine, die einen Schlüssel für die Konfiguration übernimmt.

##### <a name="verify-the-scopes-on-a-controller-action-with-hardcoded-scopes"></a>Überprüfen der Bereiche für eine Controlleraktion mit hartcodierten Bereichen

Der folgende Codeausschnitt zeigt die Verwendung des `[RequiredScope]`-Attributs mit hartcodierten Bereichen.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    [RequiredScope(scopeRequiredByApi)]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-action-with-scopes-defined-in-configuration"></a>Überprüfen der Bereiche für eine Controlleraktion mit in der Konfiguration definierten Bereichen

Sie können diese erforderlichen Bereiche auch in der Konfiguration deklarieren und auf den Konfigurationsschlüssel verweisen:

Beispiel: In appsettings.json haben Sie die folgende Konfiguration:

```JSon
{
 "AzureAd" : {
   // more settings
   "Scopes" : "access_as_user access_as_admin"
  }
}
```

Verweisen Sie dann im `[RequiredScope]`-Attribut darauf:

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    [RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-scopes-conditionally"></a>Bedingtes Überprüfen von Bereichen

Es gibt Fälle, in denen Sie Bereiche bedingt überprüfen möchten. Hierzu können Sie die `VerifyUserHasAnyAcceptedScope`-Erweiterungsmethode für `HttpContext` verwenden.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-at-the-level-of-the-controller"></a>Überprüfen der Bereiche auf Controllerebene

Sie können die Bereiche auch auf Controllerebene überprüfen.

##### <a name="verify-the-scopes-on-a-controller-with-hardcoded-scopes"></a>Überprüfen der Bereiche für einen Controller mit hartcodierten Bereichen

Der folgende Codeausschnitt zeigt die Verwendung des `[RequiredScope]`-Attributs mit hartcodierten Bereichen auf dem Controller.

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(scopeRequiredByApi)]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-with-scopes-defined-in-configuration"></a>Überprüfen der Bereiche für einen Controller mit in der Konfiguration definierten Bereichen

Wie bei Aktionen können Sie diese erforderlichen Bereiche auch in der Konfiguration deklarieren und auf den Konfigurationsschlüssel verweisen:

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-more-globally"></a>Globaleres Überprüfen der Bereiche

Als Vorgehensweise werden die Definition präziser Bereiche für Ihre Web-API und die Überprüfung der Bereiche bei jeder Controlleraktion empfohlen. Es ist jedoch auch möglich, die Bereiche auf Anwendungs- oder Controllerebene zu überprüfen. Einzelheiten hierzu finden Sie in der ASP.NET Core-Dokumentation unter [Anspruchsbasierte Autorisierung](/aspnet/core/security/authorization/claims).

#### <a name="what-is-verified"></a>Was wird überprüft?

Mit dem `[RequiredScope]`-Attribut und der `VerifyUserHasAnyAcceptedScope`-Methode werden in etwa folgende Schritte ausgeführt:

- Überprüfen, ob ein Anspruch mit dem Namen `http://schemas.microsoft.com/identity/claims/scope` oder `scp` vorhanden ist
- Überprüfen, ob der Anspruch einen Wert hat, der den von der API erwarteten Bereich enthält

### <a name="aspnet-classic"></a>[ASP.NET Classic](#tab/aspnet)

In einer ASP.NET Anwendung können Sie Bereiche wie folgt überprüfen:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateScopes(new[] {"read"; "admin" } );
       // ...
    }
```

Im Folgenden finden Sie eine vereinfachte Version von `ValidateScopes`:

```csharp
private void ValidateScopes(IEnumerable<string> acceptedScopes)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim scopeClaim = ClaimsPrincipal.Current.FindFirst("scp");
    if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Forbidden,
            ReasonPhrase = $"The 'scp' claim does not contain '{scopeClaim}' or was not found"
        });
    }
}
```

Eine Vollversion von `ValidateScopes` für ASP.NET Core: [_ScopesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/ScopesRequiredHttpContextExtensions.cs)

---

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Überprüfen von App-Rollen in APIs, die von Daemon-Apps aufgerufen werden

Wenn Ihre Web-API von einer [Daemon-App](scenario-daemon-overview.md) aufgerufen wird, sollte diese App eine Anwendungsberechtigung für Ihre Web-API anfordern. Unter [Verfügbarmachen von Anwendungsberechtigungen (App-Rollen)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) haben Sie gesehen, dass Ihre API solche Berechtigungen verfügbar macht. Ein Beispiel hierfür ist die App-Rolle `access_as_application`.

Ihre API muss nun überprüfen, ob das empfangene Token den Anspruch `roles` enthält und ob dieser Anspruch den erwarteten Wert hat. Der Prüfcode ähnelt dem Code, der delegierte Berechtigungen überprüft, wobei jedoch Ihre Controlleraktion jetzt Rollen und nicht Bereiche testet:

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Der folgende Codeausschnitt zeigt, wie Sie die Anwendungsrolle überprüfen.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        // ...
    }
```

Stattdessen können Sie die Attribute [Authorize("role")] auf dem Controller oder einer Aktion (oder einer Razor-Seite) verwenden.

```CSharp
[Authorize("role")]
MyController : ApiController
{
    // ...
}
```

Hierfür müssen Sie den Rollenanspruch jedoch „roles“ in der Datei Startup.cs zuordnen:

```CSharp
 services.Configure<OpenIdConnectOptions>(OpenIdConnectDefaults.AuthenticationScheme, options =>
 {
    // The claim in the Jwt token where App roles are available.
    options.TokenValidationParameters.RoleClaimType = "roles";
 });
```

Dies ist nicht die beste Lösung, wenn Sie auch eine Autorisierung basierend auf Gruppen durchführen müssen.

Weitere Informationen finden Sie im inkrementellen Web-App-Tutorial zur [Autorisierung nach Rollen und Gruppen](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ).

### <a name="aspnet-classic"></a>[ASP.NET Classic](#tab/aspnet)

In einer ASP.NET Anwendung können Sie Anwendungsrollen wie folgt überprüfen:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateAppRole("access_as_application");
       // ...
    }
```

Eine vereinfachte Version von `ValidateAppRole` ist:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
```

Eine Vollversion von `ValidateAppRole` für ASP.NET Core finden Sie im Code von [_ScopesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs).

---

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Akzeptieren von reinen App-Token, wenn die Web-API nur von Daemon-Apps aufgerufen werden sollte

Benutzer können auch Rollenansprüche in Benutzerzuweisungsmustern verwenden, wie unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](howto-add-app-roles-in-azure-ad-apps.md) beschrieben. Wenn Sie also Rollen überprüfen, können sich Apps als Benutzer anmelden und umgekehrt, wenn die Rollen beiden zugewiesen werden können. Wir empfehlen, verschiedene Rollen für Benutzer und Apps zu deklarieren, um diese Verwechslung zu vermeiden.

Wenn nur Daemon-Apps Ihre Web-API aufrufen können sollen, fügen Sie bei der Validierung der App-Rolle die Bedingung hinzu, dass das Token ein reines App-Token sein muss.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnly = oid != null && sub != null && oid == sub;
```

Durch das Überprüfen der inversen Bedingung können nur Apps, die einen Benutzer anmelden, Ihre API aufrufen.

### <a name="using-acl-based-authorization"></a>Verwenden der ACL-basierten Autorisierung

Alternativ zur auf Anwendungsrollen basierenden Autorisierung können Sie Ihre Web-API mit einem ACL-basierten Autorisierungsmuster (Zugriffssteuerungsliste) schützen, um [Token ohne den `roles`-Anspruch](v2-oauth2-client-creds-grant-flow.md#controlling-tokens-without-the-roles-claim) zu steuern.

Wenn Sie Microsoft.Identity.Web auf ASP.NET Core verwenden, müssen Sie deklarieren, dass Sie die ACL-basierte Autorisierung verwenden. Andernfalls löst Microsoft Identity Web eine Ausnahme aus, wenn sich weder Rollen noch Bereiche in den bereitgestellten Ansprüchen befinden:

```Text
System.UnauthorizedAccessException: IDW10201: Neither scope or roles claim was found in the bearer token.
```

Um diese Ausnahme zu vermeiden, legen Sie die `AllowWebApiToBeAuthorizedByACL`-Konfigurationseigenschaft im appsettings.json oder programmgesteuert auf „true“ fest.

```Json
{
 "AzureAD"
 {
  // other properties
  "AllowWebApiToBeAuthorizedByACL" : true,
  // other properties
 }
}
```

Wenn Sie `AllowWebApiToBeAuthorizedByACL` auf „true“ festlegen, liegt dies in **Ihrer Verantwortung**, um den ACL-Mechanismus sicherzustellen.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](scenario-protected-web-api-production.md).
