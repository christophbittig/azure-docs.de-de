---
title: Serialisierung des Tokencache (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Serialisierung und benutzerdefinierte Serialisierung des Tokencaches mithilfe der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 216fd3f132464b9866bc1f3b1b61b143de117019
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083465"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialisierung des Tokencaches in MSAL.NET

Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) [erwirbt ein Token](msal-acquire-cache-tokens.md) und speichert es dann zwischen. Öffentliche Clientanwendungen (Desktop-/Mobil-Apps) sollten versuchen, ein Token aus dem Cache abzurufen, bevor sie mithilfe einer anderen Methode ein Token erwerben. Methoden zum Erwerb in vertrauenswürdigen Clientanwendungen verwalten die Caches selbst. Dieser Artikel beschreibt die standardmäßige und benutzerdefinierte Serialisierung des Tokencaches in MSAL.NET.

## <a name="quick-summary"></a>Kurze Zusammenfassung

Empfehlung:
- Verwenden Sie in Web-Apps und Web-APIs die [Tokencacheserialisierer von „Microsoft.Identity.Web“](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization). Diese stellen sogar ein verteiltes Datenbank- oder Cachesystem bereit, um Token zu speichern.
  - Verwenden Sie Microsoft.Identity.Web in [Web-Apps](scenario-web-app-call-api-overview.md) und [Web-APIs](scenario-web-api-call-api-overview.md) von ASP.NET Core als allgemeinere API in ASP.NET Core.
  - Verwenden Sie in ASP.NET Classic, .NET Core und .NET Framework MSAL.NET direkt mit [Tokencacheserialisierungs-Adaptern für MSAL](), die in Microsoft.Identity.Web bereitgestellt sind. 
- Verwenden Sie in Desktopanwendungen (die ein Dateisystem zum Speichern von Token verwenden können) [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache) mit MSAL.Net.
- In mobilen Anwendungen (Xamarin.iOS, Xamarin.Android, Universelle Windows-Plattform) ist keine Aktion Ihrerseits erforderlich, da MSAL.NET den Cache für Sie verarbeitet: Diese Plattformen verfügen über einen sicheren Speicher.

## <a name="aspnet-core-web-apps-and-web-apis"></a>[ASP.NET Core-Web-Apps und -Web-APIs](#tab/aspnetcore)

Die Bibliothek [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) stellt das NuGet-Paket [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) bereit, das die Tokencacheserialisierung enthält:

| Erweiterungsmethode | BESCHREIBUNG  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | InMemory-Tokencacheserialisierung. Diese Implementierung eignet sich hervorragend für Beispiele. Sie eignet sich auch für Produktionsanwendungen, falls es Ihnen nichts ausmacht, wenn der Tokencache beim Neustart der Web-App verloren geht. `AddInMemoryTokenCaches` verwendet einen optionalen Parameter vom Typ `MsalMemoryTokenCacheOptions`, mit dem Sie die Zeitspanne angeben können, nach der der Cacheeintrag abläuft, sofern er nicht verwendet wird.
| `AddSessionTokenCaches` | Der Tokencache ist an die Benutzersitzung gebunden. Diese Option ist nicht ideal, wenn das ID-Token viele Ansprüche enthält, da das Cookie zu groß wird.
| `AddDistributedTokenCaches` | Der Tokencache ist ein Adapter für die ASP.NET Core-Implementierung `IDistributedCache`. Daher stehen ein verteilter Speichercache, ein Redis-Cache, ein verteilter NCache oder ein SQL Server-Cache zur Auswahl. Details zur Implementierung von `IDistributedCache` finden Sie unter [Verteilter Arbeitsspeichercache](/aspnet/core/performance/caching/distributed).


Im Folgenden finden Sie ein Codebeispiel für die Verwendung des In-Memory-Caches in der [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)-Methode der [Startup](/aspnet/core/fundamentals/startup)-Klasse in einer ASP.NET Core-Anwendung:

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

Aus Sicht des Caches ist der Code in ASP.NET Core-Web-APIs sehr ähnlich.


Beispiele für mögliche verteilte Caches:

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// and then choose your implementation of distributed cache

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or a Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

Die Nutzung wird im [Tutorial zum Erstellen einer Web-App mit ASP.NET Core](/aspnet/core/tutorials/first-mvc-app/) in der Phase [2-2 Tokencache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) dargestellt.

## <a name="non-aspnet-core-web-apps-and-web-apis"></a>[Nicht-ASP.NET Core-Web-Apps und -Web-APIs](#tab/aspnet)

Auch wenn Sie MSAL.NET verwenden, können Sie von den Tokencacheserialisierern von Microsoft.Identity.Web profitieren. 

### <a name="referencing-the-nuget-package"></a>Verweisen auf das NuGet-Paket

Fügen Sie das NuGet-Paket [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) zusätzlich zu MSAL.NET zu Ihrem Projekt hinzu.

### <a name="configuring-the-token-cache"></a>Konfigurieren des Tokencaches

Der folgende Code zeigt, wie Sie einen sinnvoll partitionierten In-Memory-Tokencache zu Ihrer App hinzufügen.

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

public static async Task<AuthenticationResult> GetTokenAsync(string clientId, X509Certificate cert, string authority, string[] scopes)
 {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithAuthority(authority)
       .Build();

     // Add a static in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();  // Microsoft.Identity.Web 1.16+
   
     // Make the call to get a token for client_credentials flow (app to app scenario) 
     return await app.AcquireTokenForClient(scopes).ExecuteAsync();
     
     // OR Make the call to get a token for OBO (web api scenario)
     return await app.AcquireTokenOnBehalfOf(scopes, userAssertion).ExecuteAsync();
     
     // OR Make the call to get a token via auth code (web app scenario)
     return await app.AcquireTokenByAuthorizationCode(scopes, authCode);    
     
     // OR, when the user has previously logged in, get a token silently
     var homeAccountId = GetHomeAccountIdFromClaimsPrincipal(); // uid and utid claims
     var account = await app.GetAccountAsync(homeAccountId);
     try
     {
          return await app.AcquireTokenSilent(scopes, account).ExecuteAsync();; 
     } 
     catch (MsalUiRequiredException)
     {
        // cannot get a token silently, so redirect the user to be challenged 
     }
  }
```

### <a name="available-caching-technologies"></a>Verfügbare Zwischenspeicherungstechnologien

Anstelle von `app.AddInMemoryTokenCache();` können Sie verschiedene Zwischenspeicherungstechnologien verwenden, einschließlich der von .NET bereitgestellten verteilter Tokencaches.

#### <a name="in-memory-token-cache"></a>In-Memory-Tokencache

In Beispielen eignet sich die Serialisierung des Speichertokencaches hervorragend. Sie eignet sich auch für Produktionsanwendungen, falls es Ihnen nichts ausmacht, wenn der Tokencache beim Neustart der Web-App verloren geht.

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

#### <a name="distributed-caches"></a>Verteilte Caches

Wenn Sie `IDistributedCache` verwenden, ist der Tokencache ein Adapter für die .NET Core-Implementierung `app.AddDistributedTokenCache`. Daher stehen ein verteilter Speichercache, ein Redis-Cache, eine CosmosDb oder ein SQL Server-Cache zur Auswahl. Details zur Implementierung von `IDistributedCache` finden Sie unter [Verteilter Arbeitsspeichercache](/aspnet/core/performance/caching/distributed).

##### <a name="distributed-in-memory-token-cache"></a>Verteilter In-Memory-Tokencache

```CSharp 
     // In memory distributed token cache
     app.AddDistributedTokenCache(services =>
     {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
       services.AddDistributedMemoryCache();
     });
```

##### <a name="sql-server"></a>Datenbank importieren

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value which is above 60 mins (or the lifetime of a token in case of longer lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

##### <a name="redis-cache"></a>Redis Cache

```CSharp 
     // Redis token cache
     app.AddDistributedTokenCache(services =>
     {
       // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });
      });
```

Weitere Informationen finden Sie unter [Deaktivieren der Cachesynchronisierung](#disabling-cache-synchronization), wenn Sie feststellen, dass der Tokenabruf gelegentlich so viel Zeit in Anspruch nimmt wie das Redis-Cachetimeout. 

##### <a name="cosmos-db"></a>Cosmos DB

```CSharp 
      // Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

### <a name="disabling-legacy-token-cache"></a>Deaktivieren eines Legacytokencaches

Die MSAL verfügt über internen Code, der speziell dafür konzipiert ist, die Fähigkeit zur Interaktion mit ADAL-Legacycaches zu aktivieren. Wenn MSAL und ADAL nicht parallel nebeneinander verwendet werden (und daher der Legacycache nicht verwendet wird), ist der entsprechende Code für Legacycaches nicht erforderlich. MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0) fügt die Möglichkeit hinzu, ADAL-Legacycachecode zu deaktivieren und die Leistung bei der Cacheverwendung zu verbessern. Einen Leistungsvergleich vor und nach dem Deaktivieren des Legacycaches finden Sie im Pull Request [#2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309). Rufen Sie `.WithLegacyCacheCompatibility(false)` in einem Anwendungsgenerator auf, wie unten gezeigt.

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>Deaktivieren der Cachesynchronisierung

Standardmäßig sperrt MSAL den Cachezugriff auf der vertraulichen Clientanwendungsebene zwischen Cachelese- und -schreibvorgängen. Diese Sperre kann ein Problem darstellen, wenn das Cacheserialisierungsprogramm lange benötigt und ein Timeout eintritt, was bei Redis-Caches der Fall sein kann. Sie können das `WithCacheSynchronization`-Flag auf „false“ festlegen, um eine Strategie für optimistische Cachesperren zu aktivieren. Dies kann zu einer besseren Leistung führen, insbesondere dann, wenn ConfidentialClientApplication-Objekte anforderungsübergreifend wiederverwendet werden. 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```

### <a name="monitor-cache-hit-ratios-and-cache-performance"></a>Überwachen von Cachetrefferverhältnissen und Cacheleistung

MSAL macht wichtige Metriken als Teil des [AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata)-Objekts verfügbar: 

| Metrik       | Bedeutung     | Wann soll ein Alarm ausgelöst werden?    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | Gesamtzeit in MSAL, einschließlich Netzwerkaufrufen und Cache   | Alarm bei hoher Gesamtlatenz (> 1 s). Der Wert hängt von der Tokenquelle ab. Aus dem Cache: ein Cachezugriff. Aus AAD: zwei Cachezugriffe + ein HTTP-Aufruf. Der allererste Aufruf (pro Prozess) dauert aufgrund eines zusätzlichen HTTP-Aufrufs länger. |
|  `DurationInCacheInMs` | Zeitbedarf für Lade- oder Speichervorgang des Tokencaches, der vom App-Entwickler angepasst wird (z. B. Speichern in Redis).| Alarm bei Spitzen. |
|  `DurationInHttpInMs`| Zeitbedarf für HTTP-Aufrufe an AAD.  | Alarm bei Spitzen.|
|  `TokenSource` | Gibt die Quelle der Tokens an. Token werden viel schneller aus dem Cache abgerufen (z. B. ~100 ms im Vergleich zu ~700 ms). Kann verwendet werden, um das Cachetrefferverhältnis zu überwachen und zu alarmieren. | Verwendung mit `DurationTotalInMs` |

### <a name="samples"></a>Beispiele

- Die Verwendung der Tokencacheserialisierer in .NET Framework- und .NET Core-Anwendungen wird in diesem Beispiel veranschaulicht: [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache). 
- Das folgende Beispiel ist eine ASP.NET-Web-App, die dieselben Techniken verwendet: https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect (Siehe [WebApp/Utils/MsalAppBuilder.cs)](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs).

## <a name="desktop-apps"></a>[Desktop-App](#tab/desktop)

Bei Desktopanwendungen lautet die Empfehlung, den plattformübergreifenden Tokencache zu verwenden.

#### <a name="cross-platform-token-cache-msal-only"></a>Plattformübergreifender Tokencache (nur MSAL)

MSAL.NET bietet einen plattformübergreifenden Tokencache in einer separaten Bibliothek namens „Microsoft.Identity.Client.Extensions.Msal“, deren Quellcode unter https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet zur Verfügung steht.

##### <a name="referencing-the-nuget-package"></a>Verweisen auf das NuGet-Paket

Fügen Sie das NuGet-Paket [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) zu Ihrem Projekt hinzu.

##### <a name="configuring-the-token-cache"></a>Konfigurieren des Tokencaches

Einzelheiten dazu finden Sie unter https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache. Hier sehen Sie ein Beispiel für die Verwendung des plattformübergreifenden Tokencaches.

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```

## <a name="mobile-apps"></a>[Mobile Apps](#tab/mobile)

In MSAL.NET wird standardmäßig ein InMemory-Tokencache bereitgestellt. Für Plattformen, bei denen Benutzern ein sicherer Speicher als Teil der Plattform zur Verfügung steht, wird die Serialisierung standardmäßig bereitgestellt: Universal Windows Platform (UWP), Xamarin.iOS und Xamarin.Android.

## <a name="write-your-own-cache"></a>[Schreiben eines eigenen Caches](#tab/custom)

Wenn Sie wirklich selbst einen Tokencacheserialisierer schreiben möchten, bietet MSAL.NET eine benutzerdefinierte Tokencacheserialisierung auf Unterplattformen von .NET Framework und .NET Core. Ereignisse werden beim Zugriff auf den Cache ausgelöst; Apps können auswählen, ob der Cache serialisiert oder deserialisiert werden soll. In vertraulichen Clientanwendungen, die Benutzer behandeln (Web-Apps, die Benutzer anmelden und Web-APIs aufrufen, und Web-APIs, die nachgeschaltete Web-APIs aufrufen), können viele Benutzer vorhanden sein. Die Benutzer werden dann parallel verarbeitet. Aus Sicherheits- und Leistungsgründen wird empfohlen, jeweils einen Cache pro Benutzer zu serialisieren. Serialisierungsereignisse berechnen anhand der Identität des verarbeiteten Benutzers einen Cacheschlüssel und serialisieren/deserialisieren einen Tokencache für diesen Benutzer.

Beachten Sie, dass für mobile Plattformen (UWP, Xamarin.iOS und Xamarin.Android) keine benutzerdefinierte Serialisierung verfügbar ist. MSAL implementiert bereits einen sicheren und leistungsfähigen Serialisierungsmechanismus für diese Plattformen. .NET Desktop- und .NET Core-Anwendungen verfügen jedoch über unterschiedliche Architekturen, sodass MSAL keinen allgemeingültigen Serialisierungsmechanismus implementieren kann. Token können von Websites beispielsweise bevorzugt in einem Redis-Cache und von Desktopanwendungen bevorzugt in einer verschlüsselten Datei gespeichert werden. Für die Serialisierung gilt es also keine universelle Lösung. Passen Sie die Serialisierung an, um eine einheitliche Nutzung des Tokencaches in .NET Desktop oder .NET Core sicherzustellen.

Die folgenden Klassen und Schnittstellen sind an der Serialisierung des Tokencaches beteiligt:

- `ITokenCache` definiert Ereignisse zum Abonnieren von Anforderungen zur Tokencacheserialisierung sowie Methoden zum Serialisieren oder Deserialisieren des Caches in verschiedenen Formaten (ADAL v3.0, MSAL 2.x und MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` ist ein an die Ereignisse übergebener Rückruf, damit Sie die Serialisierung verarbeiten können. Sie werden mit Argumenten vom Typ `TokenCacheNotificationArgs` aufgerufen.
- `TokenCacheNotificationArgs` stellt nur den `ClientId` der Anwendung und einen Verweis auf den Benutzer bereit, für den das Token verfügbar ist.

  ![Klassendiagramm](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET erstellt automatisch Tokencaches und stellt den `IToken`-Cache für Sie bereit, wenn Sie die `UserTokenCache`-Methode und die `AppTokenCache`-Eigenschaften einer Anwendung aufrufen. Sie müssen die Schnittstelle nicht selbst implementieren. Beim Implementieren einer benutzerdefinierten Serialisierung des Tokencaches müssen Sie folgende Aufgaben ausführen:
> - Sie müssen auf `BeforeAccess`- und `AfterAccess`-„Ereignisse“ (oder deren asynchrone Varianten) reagieren. Der `BeforeAccess`-Delegat ist für die Deserialisierung des Caches zuständig, während der `AfterAccess`-Delegat den Cache serialisiert.
> - Einige dieser Ereignisse speichern oder laden Blobs, die über das Ereignisargument an den gewünschten Speicher übergeben werden.

Die Strategien variieren, je nachdem, ob Sie eine Tokencacheserialisierung für eine [öffentliche Clientanwendung](msal-client-applications.md) (Desktop) oder eine [vertrauliche Clientanwendung](msal-client-applications.md) (Web-App/Web-API, Daemon-App) schreiben.

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>Benutzerdefinierter Tokencache für eine Web-App oder Web-API (vertrauenswürdige Clientanwendung)

In Web-Apps oder Web-APIs kann der Cache die Sitzung, einen Redis-Cache, eine SQL-Datenbank oder eine Cosmos DB-Datenbank verwenden. Halten Sie in Web-Apps oder Web-APIs jeweils einen Tokencache pro Konto bereit: 
- Bei Web-Apps sollte der Tokencache durch die Konto-ID mit einem Schlüssel versehen werden.
- Bei Web-APIs sollte das Konto durch den Hash des Tokens, das zum Aufrufen der API verwendet wird, mit einem Schlüssel versehen werden.

Beispiele für Tokencacheserialisierer finden Sie unter [Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders).

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>Benutzerdefinierter Tokencache für eine Desktop- oder Mobil-App (öffentliche Clientanwendung)

Seit MSAL.NET v2.x haben Sie mehrere Optionen, um den Tokencache eines öffentlichen Clients zu serialisieren. Sie können den Cache nur für das MSAL.NET-Format serialisieren (das einheitliche Cacheformat wird von MSAL und Plattformen unterstützt).  Auch die [ältere](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization), in ADAL V3 gebräuchliche Serialisierung des Tokencaches wird unterstützt.

Im folgenden Beispiel wird u. a. erläutert, wie die Serialisierung des Tokencaches angepasst werden kann, damit der SSO-Zustand von ADAL.NET 3.x, ADAL.NET 5.x und MSAL.NET gemeinsam verwendet werden kann: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

> [!Note]
> Das Tokencacheformat für die MSAL.NET 1.1.4-Vorschau wird in MSAL 2.x nicht mehr unterstützt. Wenn Sie über Anwendungen verfügen, die MSAL.NET 1.x nutzen, müssen sich Ihre Benutzer erneut anmelden. Dafür wird jedoch die Migration von ADAL 4.x (und 3.x) unterstützt.

#### <a name="simple-token-cache-serialization-msal-only"></a>Einfache Serialisierung des Tokencaches (nur MSAL)

Das folgende Beispiel zeigt eine einfache Implementierung der benutzerdefinierten Serialisierung eines Tokencaches für Desktopanwendungen. Hier entspricht der Benutzertokencache einer Datei im selben Ordner wie die Anwendung.

Nach dem Erstellen der Anwendung aktivieren Sie die Serialisierung durch einen Aufruf der `TokenCacheHelper.EnableSerialization()`-Methode, wobei der `UserTokenCache` der Anwendung übergeben wird.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Die `TokenCacheHelper`-Hilfsklasse ist wie folgt definiert:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Ein dateibasiertes Serialisierungsmodul für einen Tokencache in Produktionsqualität, das für öffentliche Clientanwendungen (Desktopanwendungen unter Windows, Mac und Linux) verwendet wird, ist in der Open-Source-Bibliothek [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) verfügbar. Sie können das Modul aus dem folgenden NuGet-Paket in Ihre Anwendungen einbeziehen: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Duale Serialisierung des Tokencaches (einheitlicher MSAL-Cache und ADAL v3)

Um die Serialisierung des Tokencaches sowohl mit dem einheitlichen Cacheformat (üblich in ADAL.NET 4.x und MSAL.NET 2.x) als auch mit anderen MSALs (derselben oder einer früheren Generation auf derselben Plattform) zu implementieren, können Sie den folgenden Code nutzen:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Hierbei ist die Hilfsklasse wie folgt definiert:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

---

## <a name="plain-text-fallback-mode"></a>Nur-Text-Fallbackmodus

MIT MSAL können Sie unverschlüsselte Token in Klartext speichern. Dies ist nur für die Verwendung in Entwicklungsumgebungen zu Debugzwecken vorgesehen. Sie können den Nur-Text-Fallbackmodus mit dem folgenden Codemuster verwenden.

```csharp
storageProperties =
    new StorageCreationPropertiesBuilder(
        Config.CacheFileName + ".plaintext",
        Config.CacheDir)
    .WithUnprotectedFile()
    .Build();

var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties).ConfigureAwait(false);
```

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Beispiele veranschaulichen die Serialisierung des Tokencaches.

| Beispiel | Plattform | BESCHREIBUNG|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Desktop (WPF) | Windows Desktop .NET (WPF)-Anwendung, die die Microsoft Graph-API aufruft ![Das Diagramm zeigt eine Topologie mit dem Flow der WPF-Desktop-App „TodoListClient“ zu Azure AD und durch interaktives Abrufen eines Tokens zu Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Desktop (Konsole) | Eine Reihe von Visual Studio-Lösungen, welche die Migration von Azure AD v1.0-Anwendungen (mit ADAL.NET) zu Microsoft Identity Platform-Anwendungen (mit MSAL.NET) veranschaulichen. Weitere Informationen finden Sie insbesondere unter [Tokencachemigration](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) und [Vertraulicher Clienttokencache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache). |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET (net472) | Beispiel für die Tokencacheserialisierung in ASP.NET MVC-Anwendung (mit MSAL.NET). Siehe insbesondere [MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)
