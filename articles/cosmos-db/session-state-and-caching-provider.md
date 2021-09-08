---
title: Verwenden von Azure Cosmos DB als ASP.NET-Sitzungszustands- und Cacheanbieter
description: Erfahren Sie, wie Sie Azure Cosmos DB als ASP.NET-Sitzungszustands- und Cacheanbieter verwenden.
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: c55428557e17615ed58140cbeab5cbe0b5450608
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290379"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>Verwenden von Azure Cosmos DB als ASP.NET-Sitzungszustands- und Cacheanbieter

Der Azure Cosmos DB-Sitzungs- und Cacheanbieter ermöglicht es Ihnen, Azure Cosmos DB zu verwenden und dessen Funktionen für geringe Latenz und globale Skalierung zum Speichern von Sitzungszustandsdaten und als verteilten Cache innerhalb Ihrer Anwendung zu nutzen.

## <a name="what-is-session-state"></a>Was ist der Sitzungszustand?

Beim [Sitzungszustand](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true) handelt es sich um Benutzerdaten, die das Navigieren eines Benutzers durch eine Webanwendung während eines bestimmten Zeitraums innerhalb desselben Browsers nachführen. Der Sitzungszustand läuft ab und ist auf die Interaktionen eines bestimmten Browsers beschränkt, die nicht browserübergreifend erweitert werden. Die Daten werden als kurzlebig betrachtet. Wenn sie nicht vorhanden sind, wird die Anwendung nicht unterbrochen. Wenn sie jedoch vorhanden sind, werden Vorgänge für den Benutzer beschleunigt, da die Webanwendung sie nicht bei jeder Browseranforderung für denselben Benutzer abrufen muss.

Dies wird häufig durch einen Speichermechanismus unterstützt, der sich in einigen Fällen außerhalb des aktuellen Webservers befinden und Lastenausgleichsanforderungen desselben Browsers über mehrere Webserver ermöglichen kann, um höhere Skalierbarkeit zu erreichen.

Der einfachste Sitzungszustandsanbieter ist der In-Memory-Anbieter, der Daten nur im Arbeitsspeicher des lokalen Webservers speichert und es erforderlich macht, dass die Anwendung das [Routing von Anwendungsanforderungen](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) verwendet. Dadurch wird die Browsersitzung an einen bestimmten Webserver gebunden (alle Anforderungen für diesen Browser müssen immer bei demselben Webserver ankommen). Der Anbieter funktioniert gut in einfachen Szenarien, aber die Bindungsanforderung kann beim Skalieren von Webanwendungen zu Problemen mit dem Lastenausgleich führen.

Es gibt viele externe Speicheranbieter, die die Sitzungsdaten so speichern können, dass sie von mehreren Webservern gelesen und darauf zugegriffen werden kann, ohne dass eine Sitzungsbindung erforderlich ist, und die eine höhere Skalierung ermöglichen.

## <a name="session-state-scenarios"></a>Szenarien für den Sitzungszustand

Cosmos DB kann über das Erweiterungspaket [Microsoft.Extensions.Caching.Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos) als Sitzungszustandsanbieter verwendet werden. Dieses Paket nutzt das [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-standard.md), wobei ein Container als effektiver Sitzungsspeicher basierend auf einem Schlüssel-Wert-Ansatz, bei dem der Schlüssel der Sitzungsbezeichner ist, verwendet wird.

Nachdem das Paket hinzugefügt wurde, können Sie `AddCosmosCache` als Teil des Startprozesses verwenden („services.AddSession“ und „app.UseSession“ sind [allgemeine Initialisierungsschritte](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true), die für jeden Sitzungszustandsanbieter erforderlich sind):

```csharp
public void ConfigureServices(IServiceCollection services)
{
  /* Other service configurations */
  services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
  {
      CosmosClientBuilder clientBuilder = new CosmosClientBuilder("myConnectionString")
        .WithApplicationRegion("West US");
      cacheOptions.ContainerName = "myContainer";
      cacheOptions.DatabaseName = "myDatabase";
      cacheOptions.ClientBuilder = clientBuilder;
      /* Creates the container if it does not exist */
      cacheOptions.CreateIfNotExists = true; 
  });

  services.AddSession(options =>
  {
      options.IdleTimeout = TimeSpan.FromSeconds(3600);
      options.Cookie.IsEssential = true;
  });
  /* Other service configurations */
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /* Other configurations */

    app.UseSession();

    /* app.UseEndpoints and other configurations */
}
```

Hier geben Sie die Datenbank und den Container an, in denen der Sitzungszustand gespeichert werden soll, und erstellen diese optional, wenn sie nicht vorhanden sind.

Sie können Ihre SDK-Clientkonfiguration mit `CosmosClientBuilder` anpassen. Wenn Ihre Anwendung bereits einen `CosmosClient` für andere Vorgänge mit Cosmos DB verwendet, können Sie diesen auch in den Anbieter einbeziehen:

```csharp
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = "myContainer";
    cacheOptions.DatabaseName = "myDatabase";
    cacheOptions.CosmosClient = preExistingClient;
    /* Creates the container if it does not exist */
    cacheOptions.CreateIfNotExists = true; 
});
```

Anschließend können Sie ASP.NET Core-Sitzungen wie bei jedem anderen Anbieter verwenden und das „HttpContext.Session“-Objekt nutzen. Denken Sie daran, Ihre Sitzungsinformationen gemäß den [ASP.NET-Empfehlungen](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true) nach Möglichkeit immer asynchron zu laden.

##  <a name="distributed-cache-scenarios"></a>Szenarien mit verteilten Caches

Da der Cosmos DB-Anbieter die [IDistributedCache-Schnittstelle implementiert, um als Anbieter verteilter Caches zu fungieren](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true), kann er auch für jede Anwendung verwendet werden, die verteilten Cache erfordert, und nicht nur für Webanwendungen, die einen leistungsstarken und verteilten Sitzungszustandsanbieter erfordern.

Verteilte Caches erfordern Datenkonsistenz, um unabhängige Instanzen bereitstellen zu können, die die zwischengespeicherten Daten gemeinsam nutzen können. Die Verwendung des Cosmos DB-Anbieters bietet Ihnen folgende Möglichkeiten:

- Verwenden Sie Ihr Cosmos DB-Konto in **Sitzungskonsistenz**, wenn Sie das [Routing von Anwendungsanforderungen](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) aktivieren und Anfragen an eine bestimmte Instanz binden können.
- Verwenden Sie Ihr Cosmos DB-Konto in **Begrenzte Veraltung oder Starke Konsistenz**, ohne dass eine Anforderungsbindung erforderlich ist. Dies bietet die größte Skalierung in Bezug auf die Lastverteilung über Ihre Instanzen.

Um den Cosmos DB-Anbieter als verteilten Cache zu verwenden, muss er mit dem Aufruf `services.AddCosmosCache` in `ConfiguredService` registriert werden. Sobald dies geschehen ist, kann jeder Konstruktor in der Anwendung den Cache durch Verweis auf `IDistributedCache` anfordern und erhält die durch [Abhängigkeitsinjektion](/dotnet/core/extensions/dependency-injection) einbezogene Instanz zur Verwendung:

```csharp
public class MyBusinessClass
{
    private readonly IDistributedCache cache;

    public MyBusinessClass(IDistributedCache cache)
    {
        this.cache = cache;
    }
    
    public async Task SomeOperationAsync()
    {
        string someCachedValue = await this.cache.GetStringAsync("someKey");
        /* Use the cache */
    }
}
```

## <a name="troubleshooting-and-diagnosing"></a>Problembehandlung und Diagnose

Da der Cosmos DB-Anbieter das darunter liegende .NET SDK verwendet, gelten alle vorhandenen [Leistungsrichtlinien](performance-tips-dotnet-sdk-v3-sql.md) und [Leitfäden zur Problembehandlung](troubleshoot-dot-net-sdk.md) für die Ermittlung potenzieller Probleme. Beachten Sie, dass es eine eigene Methode gibt, um Zugriff auf die Diagnose der zugrunde liegenden Cosmos DB-Vorgänge zu erhalten, da sie nicht über die IDistributedCache-APIs verfügbar gemacht werden können.

Die Registrierung des optionalen Diagnosedelegaten ermöglicht Ihnen die Erfassung und bedingte Protokollierung von Diagnosen, um Probleme in Fällen wie hoher Latenz zu beheben:

```csharp
void captureDiagnostics(CosmosDiagnostics diagnostics)
{
    if (diagnostics.GetClientElapsedTime() > SomePredefinedThresholdTime)
    {
        Console.WriteLine(diagnostics.ToString());
    }
}

services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.DiagnosticsHandler = captureDiagnostics;
    /* other options */
});
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Azure Cosmos DB-Sitzungs- und Cacheanbieter finden Sie im [Quellcode auf GitHub](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/).
- [Testen](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample) Sie den Azure Cosmos DB-Sitzungs- und Cacheanbieter, indem Sie das Beispiel „Untersuchen einer ASP.NET Core-Webanwendung“ durchgehen.
- Lesen Sie mehr über [verteilte Caches](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true) in .NET.
