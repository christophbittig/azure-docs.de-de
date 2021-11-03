---
title: Application Insights-Protokollierung mit .NET
description: Erfahren Sie, wie Sie Application Insights mit der ILogger-Schnittstelle in .NET verwenden.
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 63ffa404c5f36bbb9bddfd86fd275f4bb740a66a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079033"
---
# <a name="application-insights-logging-with-net"></a>Application Insights-Protokollierung mit .NET

In diesem Artikel erfahren Sie, wie Sie Protokolle mit Application Insights in .NET-Apps mithilfe mehrerer NuGet-Pakete erfassen:

- **Core-Paket:**
  - [`Microsoft.Extensions.Logging.ApplicationInsights`][nuget-ai]
- **Workloadpakete:**
  - [`Microsoft.ApplicationInsights.AspNetCore`][nuget-ai-anc]
  - [`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`][nuget-ai-ws-tc]

[nuget-ai]: https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights
[nuget-ai-anc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore
[nuget-ai-ws]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService
[nuget-ai-ws-tc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel

> [!TIP]
> Das [`Microsoft.ApplicationInsights.WorkerService`][nuget-ai-ws] NuGet-Paket geht über den Rahmen dieses Artikels hinaus. Es kann verwendet werden, um Application Insights für Hintergrunddienste zu aktivieren. Weitere Informationen finden Sie unter [Application Insights für Workerdienst-Apps](./worker-service.md).

Abhängig vom Application Insights-Protokollierungspaket, das Sie verwenden, gibt es verschiedene Möglichkeiten zum Registrieren von `ApplicationInsightsLoggerProvider`. `ApplicationInsightsLoggerProvider` ist eine Implementierung des <xref:Microsoft.Extensions.Logging.ILoggerProvider>, der für die Bereitstellung der <xref:Microsoft.Extensions.Logging.ILogger>- und <xref:Microsoft.Extensions.Logging.ILogger%601>-Implementierungen verantwortlich ist.

## <a name="aspnet-core-applications"></a>ASP.NET Core-Anwendungen

Verwenden Sie das `Microsoft.ApplicationInsights.AspNetCore` NuGet-Paket, um ASP.NET Core-Anwendungen Telemetriedaten hinzuzufügen. Dies können Sie über [Visual Studio als verbundener Dienst](/visualstudio/azure/azure-app-insights-add-connected-service) oder manuell konfigurieren.

Standardmäßig ist für ASP.NET Core-Anwendungen ein Application Insights-Protokollierungsanbieter registriert, wenn sie mit [Code](./asp-net-core.md) oder mithilfe eines [codelosen](./azure-web-apps-net-core.md#enable-agent-based-monitoring) Ansatzes konfiguriert wurden. Der registrierte Anbieter ist so konfiguriert, dass Protokollereignisse mit einem Schweregrad von <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> oder höher automatisch erfasst werden. Sie können Schweregrad und Kategorien anpassen. Weitere Informationen finden Sie unter [Protokolliergrad](#logging-level).

1. Vergewissern Sie sich, dass das NuGet-Paket installiert ist:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />
    </ItemGroup>
   ```

1. Stellen Sie sicher, dass die `Startup.ConfigureServices`-Methode `services.AddApplicationInsightsTelemetry` aufruft:

    ```csharp
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Configuration;
    
    namespace WebApplication
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddApplicationInsightsTelemetry();
                // Configure the Connection String/Instrumentation key in appsettings.json
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                // omitted for brevity
            }
        }
    }
    ```

Wenn das NuGet-Paket installiert ist und der Anbieter mit Abhängigkeitsinjektion registriert wird, kann die App Protokollierungen vornehmen. Bei Konstruktorinjektion ist entweder <xref:Microsoft.Extensions.Logging.ILogger> oder die generische Alternative <xref:Microsoft.Extensions.Logging.ILogger%601> erforderlich. Wenn diese Implementierungen aufgelöst werden, stellt `ApplicationInsightsLoggerProvider` diese zur Verfügung. Protokollierte Meldungen oder Ausnahmen werden an Application Insights gesendet. 

Betrachten Sie den folgenden Beispielcontroller:

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");

        return new string[] { "value1", "value2" };
    }
}
```

Weitere Informationen finden Sie unter [Logging in ASP.NET Core](/aspnet/core/fundamentals/logging) (Protokollierung in ASP.NET Core).

### <a name="capture-logs-within-aspnet-core-startup-code"></a>Erfassen von Protokollen im ASP.NET Core-Startcode

In einigen Szenarien müssen Protokolle als Teil der App-Startroutine erfasst werden, bevor die Anforderung/Antwort-Pipeline bereit ist, Anforderungen zu akzeptieren. `ILogger`-Implementierungen sind jedoch nicht einfach über die Abhängigkeitsinjektion in *Program.cs* und *Startup.cs* verfügbar. Weitere Informationen finden Sie unter [Protokollierung in .NET: Erstellen von Protokollen in Main](/dotnet/core/extensions/logging?tabs=command-line#create-logs-in-main).

Bei der Protokollierung über *Program.cs* und *Startup.cs* gelten mehrere Einschränkungen:

* Telemetriedaten werden über den Telemetriekanal [InMemoryChannel](./telemetry-channels.md) gesendet.
* Auf Telemetriedaten wird keine [Stichprobenentnahme](./sampling.md) angewendet.
* Standard-[Telemetrieinitialisierer oder -Telemetrieprozessoren](./api-filtering-sampling.md) sind nicht verfügbar.

Die folgenden Beispiele veranschaulichen dies, indem *Program.cs* und *Startup.cs* explizit instanziiert und konfiguriert werden.

#### <a name="example-programcs"></a>Beispiel für „Program.cs“

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("From Program, running the host now.");

            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                })
                .ConfigureLogging((context, builder) =>
                {
                    // Providing an instrumentation key is required if you're using the
                    // standalone Microsoft.Extensions.Logging.ApplicationInsights package,
                    // or when you need to capture logs during application startup, such as
                    // in Program.cs or Startup.cs itself.
                    builder.AddApplicationInsights(
                        context.Configuration["APPLICATIONINSIGHTS_CONNECTION_STRING"]);

                    // Capture all log-level entries from Program
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Program).FullName, LogLevel.Trace);

                    // Capture all log-level entries from Startup
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Startup).FullName, LogLevel.Trace);
                });
    }
}
```

Im vorangehenden Code wird `ApplicationInsightsLoggerProvider` mit Ihrer `"APPLICATIONINSIGHTS_CONNECTION_STRING"`-Verbindungszeichenfolge konfiguriert. Filter werden angewendet, wodurch der Protokolliergrad auf <xref:Microsoft.Extensions.Logging.LogLevel.Trace?displayProperty=nameWithType> festgelegt wird.

> [!IMPORTANT]
> Wir empfehlen, [Verbindungszeichenfolgen](./sdk-connection-string.md?tabs=net) gegenüber Instrumentierungsschlüsseln zu bevorzugen. Neue Azure-Regionen *erfordern* die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. 
>
> Eine Verbindungszeichenfolge identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.

#### <a name="example-startupcs"></a>Beispiel für „Startup.cs“

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace WebApplication
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddApplicationInsightsTelemetry();
            // Configure the Connection String/Instrumentation key in appsettings.json
        }

        // The ILogger<Startup> is resolved by dependency injection
        // and available in Startup.Configure.
        public void Configure(
            IApplicationBuilder app, IWebHostEnvironment env, ILogger<Startup> logger)
        {
            logger.LogInformation(
                "Configuring for {Environment} environment",
                env.EnvironmentName);

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

## <a name="console-application"></a>Konsolenanwendung

Dies sind die installierten Pakete:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="5.0.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.17.0"/>
</ItemGroup>
```

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new InMemoryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(config => config.TelemetryChannel = channel);
                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}

```

Im vorangehenden Beispiel wird das Paket `Microsoft.Extensions.Logging.ApplicationInsights` verwendet. Standardmäßig verwendet diese Konfiguration das „absolute Minimum“ `TelemetryConfiguration`-Setup zum Senden von Daten an Application Insights: den j`InMemoryChannel`-Kanal. Es erfolgt keine Stichprobenentnahme, und es gibt keinen `TelemetryInitializer`-Standardinstanz. Sie können dieses Verhalten für eine Konsolenanwendung außer Kraft setzen, wie das folgende Beispiel zeigt.

Installieren Sie dieses zusätzliche Paket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.17.0" />
```

Im folgenden Abschnitt wird gezeigt, wie das `TelemetryConfiguration`-Standardsetup mithilfe der Methode <xref:Microsoft.Extensions.Options.ConfigureOptions%601.Configure(%600)> außer Kraft gesetzt werden kann. In diesem Beispiel werden `ServerTelemetryChannel` und die Stichprobenentnahme eingerichtet. Es fügt eine benutzerdefinierte `TelemetryInitializer`-Instanz zu `TelemetryConfiguration` hinzu.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new ServerTelemetryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(
                    config =>
                    {
                        config.TelemetryChannel = channel;

                        // Optional: implement your own TelemetryInitializer instance and configure it here
                        // config.TelemetryInitializers.Add(new MyTelemetryInitializer());

                        config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
                        channel.Initialize(config);
                    });

                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}
```

## <a name="logging-level"></a>Protokolliergrad

`ILogger`-Implementierungen verfügen über einen integrierten Mechanismus zum Anwenden der [Protokollfilterung](/dotnet/core/extensions/logging#how-filtering-rules-are-applied). Mit dieser Filterung können Sie die Protokolle steuern, die an jeden registrierten Anwender gesendet werden, einschließlich des Application Insights-Anbieters. Sie können die Filterung entweder in der Konfiguration (z. B. mittels einer *appsettings.json*-Datei) oder im Code verwenden.

In den folgenden Beispielen wird die Anwendung von Filterregeln auf `ApplicationInsightsLoggerProvider` veranschaulicht.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Erstellen von Filterregeln in der Konfiguration mit „appsettings.json“

Der Alias von `ApplicationInsightsLoggerProvider` lautet „ApplicationInsights“. Im folgenden Abschnitt von *appsettings.json* wird die Standardprotokollebene <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> von Application Insights überschrieben, um Kategorien zu protokollieren, die mit "Microsoft" auf der Ebene <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> und höher beginnen.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Erstellen von Filterregeln im Code

Der folgende Codeausschnitt konfiguriert Protokolle, die für diese Elemente an `ApplicationInsightsLoggerProvider` gesendet werden sollen:

- <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> und höher aus allen Kategorien
- <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> und höher aus Kategorien, die mit „Microsoft“ beginnen

```csharp
Host.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(builder =>
    {
        builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Warning);
        builder.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
    });
```


## <a name="logging-scopes"></a>Protokollierungsbereiche

`ApplicationInsightsLoggingProvider` unterstützt [Protokollbereiche](/dotnet/core/extensions/logging#log-scopes). Bereiche sind standardmäßig aktiviert. 

Bei einem Bereich vom Typ `IReadOnlyCollection<KeyValuePair<string,object>>` wird jedes Schlüssel-Wert-Paar in der Sammlung als benutzerdefinierte Eigenschaft zur Application Insights-Telemetrie hinzugefügt. Im folgenden Beispiel werden Protokolle als `TraceTelemetry` aufgezeichnet und enthalten `("MyKey", "MyValue")` in den Eigenschaften.

```csharp
using (_logger.BeginScope(new Dictionary<string, object> { ["MyKey"] = "MyValue" }))
{
    _logger.LogError("An example of an Error level message");
}
```

Wenn ein anderer Typ als Bereich verwendet wird, wird er unter der Eigenschaft `Scope` in der Application Insights-Telemetrie gespeichert. Im folgenden Beispiel verfügt `TraceTelemetry` über eine Eigenschaft namens `Scope`, die den Bereich enthält.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Welche ist die alte und welche die neue Version von ApplicationInsightsLoggerProvider?

Das [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthielt eine integrierte `ApplicationInsightsLoggerProvider`-Instanz (`Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider`), die über `ILoggerFactory`-Erweiterungsmethoden aktiviert wurde. Dieser Anbieter ist seit Version 2.7.1 als veraltet markiert. Er ist für die vollständige Entfernung in der nächsten Hauptversionsänderung vorgesehen. 

Das Paket [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) selbst ist nicht veraltet. Es ist erforderlich, die Überwachung von Elementen wie Anforderungen und Abhängigkeiten zu aktivieren.

Die empfohlene Alternative ist das neue eigenständige Paket [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), das eine verbesserte `ApplicationInsightsLoggerProvider`-Instanz (`Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider`) sowie Erweiterungsmethoden in `ILoggerBuilder` für dessen Aktivierung enthält.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), Version 2.7.1, ist vom neuen Paket abhängig und aktiviert die `ILogger`-Erfassung automatisch.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Warum werden einige ILogger-Protokolle in Application Insights zweimal angezeigt?

Es kann zu Duplizierungen kommen, wenn Sie die ältere (jetzt veraltete) Version von `ApplicationInsightsLoggerProvider` durch Aufruf von `AddApplicationInsights` für `ILoggerFactory` aktiviert haben. Überprüfen Sie, ob Ihre `Configure`-Methode folgenden Code enthält, und entfernen sie den Eintrag:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Wenn Sie beim Debuggen aus Visual Studio doppelte Protokollierung feststellen, legen Sie im Code, der Application Insights aktiviert, `EnableDebugLogger` wie folgt auf `false` fest. Diese Duplizierung und Behebung des Problems betreffen nur das Debuggen der Anwendung.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions
    {
        EnableDebugLogger = false
    }
    services.AddApplicationInsightsTelemetry(options);
    // ...
}
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ich habe ein Update auf Microsoft.ApplicationInsights.AspNet SDK Version 2.7.1 durchgeführt, und Protokolle aus ILogger werden automatisch erfasst. Wie kann ich dieses Feature vollständig deaktivieren?

Allgemeine Informationen zum Filtern von Protokollen finden Sie im Abschnitt [Protokolliergrad](#logging-level). Um `ApplicationInsightsLoggerProvider` zu deaktivieren, verwenden Sie `LogLevel.None` in Ihrem Aufruf zum Konfigurieren der Protokollierung. Im folgenden Befehl hat `builder` den Wert <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.

```csharp
builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.None);
```

Hier sehen Sie die Änderung in der Datei *appsettings.json*:

```json
{
    "Logging": {
        "ApplicationInsights": {
            "LogLevel": {
                "Default": "None"
            }
        }
    }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Warum weisen einige ILogger-Protokolle nicht dieselben Eigenschaften wie andere auf?

Application Insights erfasst und sendet `ILogger`-Protokolle mithilfe derselben `TelemetryConfiguration`-Informationen, die auch für andere Telemetriedaten verwendet werden. Es gibt jedoch eine Ausnahme. Standardmäßig ist die `TelemetryConfiguration` nicht vollständig eingerichtet, wenn Sie aus *Program.cs* oder *Startup.cs* protokollieren. Protokolle aus diesen Quellen weisen nicht die Standardkonfiguration auf, daher führen sie nicht alle `TelemetryInitializer`- und `TelemetryProcessor`-Instanzen aus.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Ich verwende das eigenständige Paket „Microsoft.Extensions.Logging.ApplicationInsights“ und möchte einige zusätzliche benutzerdefinierte Telemetriedaten manuell protokollieren. Wie sollte ich dazu vorgehen?

Wenn Sie das eigenständige Paket verwenden, wird `TelemetryClient` nicht in den Abhängigkeitseinschleusungscontainer (Dependency Injection, DI) eingeschleust. Sie müssen eine neue Instanz von `TelemetryClient` erstellen und dieselbe Konfiguration verwenden, die der Protokollierungsanbieter verwendet, wie im folgenden Code gezeigt. So wird sichergestellt, dass dieselbe Konfiguration für alle benutzerdefinierten Telemetriedaten sowie für die Telemetriedaten von `ILogger` verwendet wird.

```csharp
public class MyController : ApiController
{
   // This TelemetryClient instance can be used to track additional telemetry through the TrackXXX() API.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Wenn Sie Application Insights mit dem Paket `Microsoft.ApplicationInsights.AspNetCore` aktivieren, ändern Sie diesen Code, um `TelemetryClient` direkt in den Konstruktor abzurufen. Ein Beispiel finden Sie in diesen [häufig gestellten Fragen](./asp-net-core.md#frequently-asked-questions).

### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-where-can-i-see-ilogger-logs-in-application-insights"></a>Welcher Typ von Application Insights-Telemetriedaten wird anhand von ILogger-Protokollen erstellt? Wo kann ich ILogger-Protokolle in Application Insights einsehen?

`ApplicationInsightsLoggerProvider` erfasst `ILogger`-Protokolle und erstellt `TraceTelemetry` daraus. Wenn ein `Exception`-Objekt an die `Log`-Methode in `ILogger` übergeben wird, wird anstelle von `TraceTelemetry` `ExceptionTelemetry` erstellt. 

Diese Telemetrieelemente sind an denselben Orten wie alle anderen `TraceTelemetry`- oder `ExceptionTelemetry`-Elemente für Application Insights zu finden, einschließlich des Azure-Portals, Analysen oder des lokalen Visual Studio-Debuggers.

Wenn Sie immer `TraceTelemetry` senden möchten, verwenden Sie diesen Codeausschnitt:

```csharp
builder.AddApplicationInsights(
    options => options.TrackExceptionsAsExceptionTelemetry = false);
```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Ich habe das SDK nicht installiert und verwende die Azure-Web-Apps-Erweiterung, um Application Insights für meine ASP.NET Core-Anwendungen zu aktivieren. Wie verwende ich den neuen Anbieter? 

Die Application Insights-Erweiterung in Azure-Web-Apps verwendet den neuen Anbieter. Sie können die Filterregeln in der Datei *appsettings.json* für die Anwendung ändern.

## <a name="next-steps"></a>Nächste Schritte

* [Protokollierung in .NET](/dotnet/core/extensions/logging)
* [Protokollierung in ASP.NET Core](/aspnet/core/fundamentals/logging)
* [.NET-Ablaufverfolgungsprotokolle in Application Insights](./asp-net-trace-logs.md)
