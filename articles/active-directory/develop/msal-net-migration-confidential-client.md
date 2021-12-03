---
title: Migrieren vertraulicher Client-Anwendungen zu MSAL.NET
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine vertrauliche Clientanwendung von der Azure Active Directory-Authentifizierungsbibliothek für .NET (ADAL.NET) zur Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) migrieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 4e362812224f8e538d7a36dfa5378e23f6438d6e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462820"
---
# <a name="migrate-confidential-client-applications-from-adalnet-to-msalnet"></a>Migrieren vertraulicher Clientanwendungen von ADAL.NET zu MSAL.NET

In diesem Artikel wird beschrieben, wie Sie eine vertrauliche Clientanwendung von der Azure Active Directory-Authentifizierungsbibliothek für .NET (ADAL.NET) zur Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) migrieren. Vertrauliche Clientanwendungen sind Web-Apps, Web-APIs und Daemonanwendungen, die im eigenen Namen einen anderen Dienst aufrufen. Weitere Informationen zu vertraulichen Anwendungen finden Sie unter [Authentifizierungsflows und Anwendungsszenarien](authentication-flows-app-scenarios.md). Wenn Ihre App auf ASP.NET Core basiert, verwenden Sie [Microsoft.Identity.Web](microsoft-identity-web.md).

Für App-Registrierungen gilt:

- Sie müssen keine neue App-Registrierung erstellen. (Sie behalten die gleiche Client-ID bei.)
- Sie müssen die Vorautorisierungen (per Administratoreinwilligung erteilte API-Berechtigungen) nicht ändern.

## <a name="migration-steps"></a>Schritte bei der Migration

1. Suchen Sie in Ihrer App nach Code, der ADAL.NET verwendet.

   Code in einer vertraulichen Clientanwendung, der ADAL nutzt, instanziiert `AuthenticationContext` und ruft entweder `AcquireTokenByAuthorizationCode` oder eine Überschreibung von `AcquireTokenAsync` auf. Hierbei werden die folgenden Parameter verwendet:

   - Eine `resourceId`-Zeichenfolge. Diese Variable ist der App-ID-URI der Web-API, die Sie aufrufen möchten.
   - Eine Instanz von `IClientAssertionCertificate` oder von `ClientAssertion`. Diese Instanz stellt die Clientanmeldeinformationen für Ihre App bereit, um die Identität Ihrer App nachzuweisen.

1. Nachdem Sie festgestellt haben, dass Sie über Apps mit Verwendung von ADAL.NET verfügen, installieren Sie das MSAL.NET-NuGet-Paket [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) und aktualisieren Ihre Projektbibliotheksverweise. Weitere Informationen finden Sie unter [Installieren eines NuGet-Pakets](https://www.bing.com/search?q=install+nuget+package). Wenn Sie Module zur Serialisierung des Tokencaches verwenden möchten, installieren Sie auch [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache).

1. Aktualisieren Sie den Code gemäß dem Szenario für vertrauliche Clients. Einige der erforderlichen Schritte gelten für alle Szenarien mit vertraulichen Clients. Andere Schritte gelten jeweils nur für ein einzelnes Szenario. 

   Es gibt folgende Szenarien für vertrauliche Clients:

   - [Daemonszenarien](?tabs=daemon#migrate-daemon-apps): Dieses Szenario wird von Web-Apps, Web-APIs und Daemon-Konsolenanwendungen unterstützt.
   - [Web-API, die nachgeschaltete Web-APIs aufruft](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis): Dieses Szenario wird von Web-APIs unterstützt, die im Namen des Benutzers nachgeschaltete Web-APIs aufrufen.
   - [Web-App, die Web-APIs aufruft](?tabs=authcode#migrate-a-web-api-that-calls-downstream-web-apis): Dieses Szenario wird von Web-Apps unterstützt, die Benutzer anmelden und eine nachgeschaltete Web-API aufrufen.

Möglicherweise verwenden Sie einen Wrapper für ADAL.NET, um Zertifikate und Zwischenspeicherung zu verarbeiten. In diesem Artikel wird derselbe Ansatz verwendet, um das Vorgehen zum Migrieren von ADAL.NET zu MSAL.NET zu veranschaulichen. Dieser Code dient jedoch nur zu Demonstrationszwecken. Kopieren bzw. fügen Sie diese Wrapper nicht unverändert in Ihren Code ein.

## <a name="daemon"></a>[Daemon](#tab/daemon)

### <a name="migrate-daemon-apps"></a>Migrieren von Daemon-Apps

Daemonszenarien verwenden den OAuth2.0-[Client-Anmeldeinformationsfluss](v2-oauth2-client-creds-grant-flow.md). Diese Szenarien werden auch als Dienst-zu-Dienst-Aufrufe bezeichnet. Ihre App ruft nicht im Namen eines Benutzers, sondern in eigenem Namen ein Token ab.

#### <a name="find-out-if-your-code-uses-daemon-scenarios"></a>Ermitteln, ob Ihr Code Daemonszenarien verwendet

Der ADAL-Code für Ihre App verwendet Daemonszenarien, wenn er einen Aufruf von `AuthenticationContext.AcquireTokenAsync` mit den folgenden Parametern enthält:

- Eine Ressource (App-ID-URI) als erster Parameter
- `IClientAssertionCertificate` oder `ClientAssertion` als zweiter Parameter

`AuthenticationContext.AcquireTokenAsync` weist keinen Parameter vom Typ `UserAssertion` auf. Wenn dem so ist, handelt es sich bei Ihrer App um eine Web-API, die das Szenario [Web-API, die nachgeschaltete Web-APIs aufruft](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis) verwendet.

#### <a name="update-the-code-of-daemon-scenarios"></a>Aktualisieren des Codes für Daemonszenarien

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IConfidentialClientApplication.AcquireTokenClient`.

Im Folgenden sehen Sie eine Gegenüberstellung des ADAL.NET- und MSAL.NET-Codes für Daemonszenarien:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult()
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);


  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                );

  return authResult;
 }
}
```
:::column-end:::   
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult()
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }

  var authResult = await app.AcquireTokenForClient(
              new [] { $"{resourceId}/.default" })
              // .WithTenantId(specificTenant)
              // See https://aka.ms/msal.net/withTenantId
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>Vorteile der Zwischenspeicherung von Token

Um vom In-Memory-Cache zu profitieren, muss die Instanz von `IConfidentialClientApplication` in einer Membervariablen gespeichert werden. Wenn Sie die vertrauliche Clientanwendung bei jeder Tokenanforderung neu erstellen, profitieren Sie nicht vom Tokencache.

Sie müssen `AppTokenCache` serialisieren, wenn Sie den standardmäßigen In-Memory-App-Tokencache nicht verwenden möchten. Ebenso müssen Sie `AppTokenCache` serialisieren, wenn Sie einen verteilten Tokencache implementieren möchten. Weitere Informationen finden Sie unter [Tokencache für eine Web-App oder Web-API (vertrauliche Clientanwendung)](msal-net-token-cache-serialization.md?tabs=aspnet) und im Beispiel [active-directory-dotnet-v1-to-v2/ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).

[Erfahren Sie mehr über das Daemonszenario](scenario-daemon-overview.md) und seine Implementierung mit MSAL.NET oder Microsoft.Identity.Web in neuen Anwendungen.

## <a name="web-api-calling-downstream-web-apis"></a>[Web-API, die nachgeschaltete Web-APIs aufruft](#tab/obo)

### <a name="migrate-a-web-api-that-calls-downstream-web-apis"></a>Migrieren einer Web-API, die nachgeschaltete Web-APIs aufruft

Web-APIs, die nachgeschaltete Web-APIs aufrufen, verwenden den OAuth2.0-[On-Behalf-Of-Fluss (OBO)](v2-oauth2-on-behalf-of-flow.md). Die Web-API verwendet das aus dem HTTP-**Autorisierungs**-Header abgerufene Token und validiert es. Dieses Token wird gegen ein Token zum Aufruf der nachgeschalteten Web-API ausgetauscht. Dieses Token wird sowohl in ADAL.NET als auch in MSAL.NET als eine `UserAssertion`-Instanz verwendet.

#### <a name="find-out-if-your-code-uses-obo"></a>Ermitteln, ob Ihr Code OBO verwendet

Der ADAL-Code für Ihre App verwendet OBO, wenn er einen Aufruf von `AuthenticationContext.AcquireTokenAsync` mit den folgenden Parametern enthält:

- Eine Ressource (App-ID-URI) als erster Parameter
- `IClientAssertionCertificate` oder `ClientAssertion` als zweiter Parameter
- Ein Parameter vom Typ `UserAssertion`

#### <a name="update-the-code-by-using-obo"></a>Aktualisieren des Codes mit Verwendung von OBO

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IConfidentialClientApplication.AcquireTokenOnBehalfOf`.

Im Folgenden sehen Sie eine Gegenüberstellung des OBO-Codes für ADAL.NET und MSAL.NET:

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
:::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId, 
  string tokenUsedToCallTheWebApi)
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                      userAssertion,
                                );

  return authResult;
 }
}
```
:::column-end:::
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string tokenUsedToCallTheWebApi)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }


  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await app.AcquireTokenOnBehalfOf(
              new string[] { $"{resourceId}/.default" },
              userAssertion)
              // .WithTenantId(specificTenant) 
              // See https://aka.ms/msal.net/withTenantId
              .ExecuteAsync()
              .ConfigureAwait(false);
  
  return authResult;
 }
}
```
:::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>Vorteile der Zwischenspeicherung von Token

Für die Tokenzwischenspeicherung in OBO müssen Sie einen verteilten Tokencache verwenden. Ausführliche Informationen hierzu finden Sie unter [Tokencache für eine Web-App oder Web-API (vertrauliche Clientanwendung)](msal-net-token-cache-serialization.md?tabs=aspnet) und im [Beispielcode](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).

```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

[Erfahren Sie mehr über Web-APIs, die nachgeschaltete Web-APIs aufrufen](scenario-web-api-call-api-overview.md), und ihre Implementierung mit MSAL.NET oder Microsoft.Identity.Web in neuen Anwendungen.

## <a name="web-app-calling-web-apis"></a>[Web-App, die Web-APIs aufruft](#tab/authcode)

### <a name="migrate-a-web-app-that-calls-web-apis"></a>Migrieren einer Web-App, die Web-APIs aufruft

Wenn Ihre App ASP.NET Core verwendet, wird dringend empfohlen, auf Microsoft.Identity.Web zu aktualisieren, das die gesamte Verarbeitung für Sie übernimmt. Eine kurze Präsentation finden Sie in der [Ankündigung der allgemeinen Verfügbarkeit von Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/1.0.0). Weitere Informationen zur Verwendung in einer Web-App finden Sie unter [Gründe für die Verwendung von Microsoft.Identity.Web in Web-Apps](https://aka.ms/ms-id-web/webapp).

Web-Apps, die Benutzer anmelden und Web-APIs im Namen von Benutzern aufrufen, verwenden den OAuth2.0-[Autorisierungscodefluss](v2-oauth2-auth-code-flow.md). Typische Merkmale:

1. Die Web-App meldet einen Benutzer durch Ausführen eines ersten Abschnitts des Autorisierungscodeflusses an. Dazu wird der Autorisierungsendpunkt von der Microsoft Identity Plattform verwendet. Der Benutzer meldet sich an und führt bei Bedarf eine mehrstufige Authentifizierung durch. Als Ergebnis dieses Vorgangs erhält die App den Autorisierungscode. Die Authentifizierungsbibliothek wird in dieser Phase nicht verwendet.
1. Die App führt den zweiten Abschnitt des Autorisierungscodeflusses aus. Sie verwendet den Autorisierungscode, um ein Zugriffstoken, ein ID-Token und ein Aktualisierungstoken abzurufen. Ihre Anwendung muss den Wert `redirectUri` angeben. Dabei handelt es sich um den URI, in dem der Microsoft-Identity-Plattform-Endpunkt die Sicherheitstoken bereitstellt. Nachdem die App diesen URI empfangen hat, ruft sie in der Regel `AcquireTokenByAuthorizationCode` für ADAL oder MSAL auf, um den Code einzulösen und ein Token abzurufen, das im Tokencache gespeichert wird.
1. Die App verwendet ADAL oder MSAL für den Aufruf von `AcquireTokenSilent`, damit sie Token zum Aufrufen der benötigten Web-APIs abrufen kann. Dies erfolgt über die Web-App-Controller.

#### <a name="find-out-if-your-code-uses-the-auth-code-flow"></a>Ermitteln, ob Ihr Code den Authentifizierungscodefluss verwendet

Der ADAL-Code für Ihre App verwendet den Authentifizierungscodefluss, wenn er einen Aufruf von `AuthenticationContext.AcquireTokenByAuthorizationCodeAsync` enthält.

#### <a name="update-the-code-by-using-the-authorization-code-flow"></a>Aktualisieren des Codes mit Verwendung des Autorisierungscodeflusses

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)] 

In diesem Fall ersetzen wir den Aufruf von `AuthenticationContext.AcquireTokenAsync` durch einen Aufruf von `IConfidentialClientApplication.AcquireTokenByAuthorizationCode`.

Im Folgenden sehen Sie eine Gegenüberstellung des beispielhaften Autorisierungscodeflusses für ADAL.NET und MSAL.NET:

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
     = "https://login.microsoftonline.com/common";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {


  var ac = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var authResult = await ac.AcquireTokenByAuthorizationCodeAsync(
                                      authorizationCode,
                                      redirectUri,
                                      clientAssertionCert,
                                      resourceId,
                                );
  return authResult;
 }
}
```      
   :::column-end:::
   :::column span="":::
```csharp
using Microsoft.Identity.Client;
using Microsoft.Identity.Web;
using System;
using System.Security.Claims;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority
    = "https://login.microsoftonline.com/{tenant}";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();

 public IConfidentialClientApplication CreateApplication()
 {
  IConfidentialClientApplication app;

  app = ConfidentialClientApplicationBuilder.Create(ClientId)
               .WithCertificate(certificate)
               .WithAuthority(authority)
               .WithRedirectUri(redirectUri.ToString())
               .WithLegacyCacheCompatibility(false)
               .Build();

  // Add a token cache. For details about other serialization
  // see https://aka.ms/msal-net-cca-token-cache-serialization
  app.AddInMemoryTokenCache();

  return app;
 }

 // Called from 'code received event'.
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId,
      string authorizationCode)
 {
  IConfidentialClientApplication app = CreateApplication();

  var authResult = await app.AcquireTokenByAuthorizationCode(
                  new[] { $"{resourceId}/.default" },
                  authorizationCode)
                  .ExecuteAsync()
                  .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

Durch das Aufrufen von `AcquireTokenByAuthorizationCode` wird dem Tokencache ein Token hinzugefügt, wenn der Autorisierungscode empfangen wird. Um zusätzliche Token für andere Ressourcen oder Mandanten abzurufen, verwenden Sie `AcquireTokenSilent` in Ihren Controllern.

```csharp
public partial class AuthWrapper
{
 // Called from controllers
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId2,
      string authority)
 {
  IConfidentialClientApplication app = CreateApplication();
  AuthenticationResult authResult;

  var scopes = new[] { $"{resourceId2}/.default" };
  var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());

  try
  {
   // try to get an already cached token
   authResult = await app.AcquireTokenSilent(
               scopes,
               account)
                // .WithTenantId(specificTenantId) 
                // See https://aka.ms/msal.net/withTenantId
                .ExecuteAsync().ConfigureAwait(false);
  }
  catch (MsalUiRequiredException)
  {
   // The controller will need to challenge the user
   // including asking for claims={ex.Claims}
   throw;
  }
  return authResult;
 }
}
```

#### <a name="benefit-from-token-caching"></a>Vorteile der Zwischenspeicherung von Token

Da Ihre Web-App `AcquireTokenByAuthorizationCode` verwendet, muss Ihre App einen verteilten Tokencache für die Tokenzwischenspeicherung nutzen. Ausführliche Informationen hierzu finden Sie unter [Tokencache für eine Web-App oder Web-API](msal-net-token-cache-serialization.md?tabs=aspnet) und im [Beispielcode](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).


```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

#### <a name="handling-msaluirequiredexception"></a>Behandeln von MsalUiRequiredException

Wenn Ihr Controller versucht, ein Token für verschiedene Bereiche/Ressourcen im Hintergrund zu erhalten, löst MSAL.NET möglicherweise die Ausnahme `MsalUiRequiredException` aus. Dies wird erwartet, wenn sich der Benutzer beispielsweise erneut anmelden muss oder wenn der Zugriff auf die Ressource mehr Ansprüche erfordert (z. B. aufgrund einer Richtlinie für bedingten Zugriff). Ausführliche Informationen zur Problembehebung finden Sie unter [Behandeln von Fehlern und Ausnahmen in MSAL.NET](msal-error-handling-dotnet.md).

[Erfahren Sie mehr über Web-Apps, die nachgeschaltete Web-APIs aufrufen](scenario-web-app-call-api-overview.md), und ihre Implementierung mit MSAL.NET oder Microsoft.Identity.Web in neuen Anwendungen.

---

## <a name="msal-benefits"></a>Vorteile von MSAL

Dies sind die wichtigsten Vorteile von MSAL.NET für Ihre Anwendung:

- **Resilienz**. MSAL.NET verbessert in folgender Weise die Resilienz Ihrer App:

   - Vorteile durch Azure AD Cached Credential Service (CCS). CCS fungiert als Azure AD-Sicherung.
   - Token werden proaktiv erneuert, wenn die von Ihnen aufgerufene API über eine [fortlaufende Zugriffsevaluierung](app-resilience-continuous-access-evaluation.md) langlebige Token unterstützt.

- **Sicherheit**. Sie können PoP-Token (Proof of Possession) abrufen, wenn die aufzurufende Web-API dies erfordert. Weitere Informationen finden Sie unter [PoP-Token (Proof Of Possession) in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Proof-Of-Possession-(PoP)-tokens)

- **Leistung und Skalierbarkeit:** Wenn Sie Ihren Cache nicht für ADAL.NET freigeben müssen, deaktivieren Sie beim Erstellen der vertraulichen Clientanwendung die Legacycachekompatibilität (`.WithLegacyCacheCompatibility(false)`). Dies erhöht die Leistung erheblich.
  
  ```csharp
  app = ConfidentialClientApplicationBuilder.Create(ClientId)
          .WithCertificate(certificate)
          .WithAuthority(authority)
          .WithLegacyCacheCompatibility(false)
          .Build();
  ```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="msalserviceexception"></a>MsalServiceException

Die folgenden Informationen zur Problembehandlung gehen von zwei Annahmen aus: 

- Ihr ADAL.NET-Code hat funktioniert.
- Bei der Migration zu MSAL wurde die Client-ID beibehalten.

Es wird eine Ausnahme ausgelöst, und Ihnen wird eine der folgenden Meldungen angezeigt: 

> `AADSTS700027: Client assertion contains an invalid signature. [Reason - The key was not found.]`

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

Sie können die Ausnahme mithilfe der folgenden Schritte beheben:

1. Stellen Sie sicher, dass Sie die neueste Version von [MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/) verwenden.
1. Vergewissern Sie sich, dass der Autoritätshost, den Sie beim Erstellen der vertraulichen Clientanwendung festgelegt haben, und der mit ADAL verwendete Autoritätshost übereinstimmen. Insbesondere sollte dieselbe [Cloud](msal-national-cloud.md) (Azure Government, Azure China 21Vianet oder Azure Deutschland) verwendet werden.

### <a name="msalclientexception"></a>MsalClientException

In Anwendungen mit mehreren Mandanten können Sie beim Erstellen der Anwendung eine gemeinsame Autorität angeben, aber dann einen bestimmten Mandanten (z. B. den Mandanten des Benutzers) als Ziel verwenden, wenn Sie eine Web-API aufrufen. Seit MSAL.NET 4.37.0 können Sie, wenn Sie bei der Anwendungserstellung `.WithAzureRegion` angeben, nicht mehr die Autorität angeben, die während der Tokenanforderung `.WithAuthority` verwendet. Wenn Sie dies tun, erscheint beim Aktualisieren von früheren Versionen von MSAL.NET folgender Fehler:

  `MsalClientException - "You configured WithAuthority at the request level, and also WithAzureRegion. This is not supported when the environment changes from application to request. Use WithTenantId at the request level instead."`

Um dieses Problem zu beheben, ersetzen Sie `.WithAuthority` auf dem AcquireTokenXXX-Ausdruck durch `.WithTenantId`. Geben Sie den Mandanten mithilfe einer GUID oder eines Domänennamens an.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:
- [Unterschiede zwischen ADAL.NET- und MSAL.NET-Anwendungen](msal-net-differences-adal-net.md).
- [Serialisierung des Tokencaches in MSAL.NET](msal-net-token-cache-serialization.md)