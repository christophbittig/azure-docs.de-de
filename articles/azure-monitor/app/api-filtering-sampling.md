---
title: Filterung und Vorverarbeitung im Application Insights-SDK | Microsoft-Dokumentation
description: Schreiben Sie Telemetrieprozessoren und Telemetrieinitialisierer für das SDK, um die Daten zu filtern oder ihnen Eigenschaften hinzuzufügen, bevor die Telemetriedaten an das Application Insights-Portal gesendet werden.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5fe23d6cae1b363cdb0c70cba561953368412361
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645130"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Filtern und Vorverarbeiten von Telemetriedaten im Application Insights-SDK

Sie können Plug-Ins für das Application Insights SDK schreiben und konfigurieren, um anzupassen, wie Telemetriedaten vor dem Senden an den Application Insights-Dienst angereichert und verarbeitet werden.

* [Erstellen von Stichproben](sampling.md) verringert sich der Umfang der Telemetriedaten, ohne Statistiken zu verfälschen. Zusammengehörige Datenpunkte werden dabei zusammengehalten, sodass Sie bei der Diagnose von Problemen zwischen diesen navigieren können. Im Portal wird die Gesamtanzahl multipliziert, um eine Kompensation der Stichproben zu erreichen.
* Durch das Filtern mit Telemetrieprozessoren können Sie Telemetriedaten im SDK herausfiltern, bevor sie an den Server gesendet werden. Sie können beispielsweise den Umfang der Telemetriedaten verringern, indem Sie Anforderungen von Robots ausschließen. Filtern stellt ein grundlegenderes Verfahren zur Senkung des Datenverkehrs dar als das Erstellen von Stichproben. Sie können so besser steuern, was übertragen wird, doch hat dies Auswirkungen auf die Statistik. Sie könnten beispielsweise alle erfolgreichen Anforderungen herausfiltern.
* [Telemetrieinitialisierer](#add-properties) fügen Eigenschaften zu beliebigen von der App gesendeten Telemetriedaten hinzu oder ändern sie, einschließlich Telemetriedaten von Standardmodulen. Sie könnten z. B. berechnete Werte hinzufügen oder Versionsnummern, nach denen Sie die Daten im Portal filtern.
* [SDK-API](./api-custom-events-metrics.md) wird zum Senden benutzerdefinierter Ereignisse und Metriken verwendet.

Vorbereitungen:

* Installieren Sie das für Ihre Anwendung geeignete SDK in Ihrer App: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [Nicht-HTTP/Worker für .NET/.NET Core](worker-service.md) oder [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filterung

Mit dieser Technik können Sie direkt kontrollieren, welche Daten in den Telemetriedatenstrom ein- oder daraus ausgeschlossen werden sollen. Filtern Sie Telemetrieelemente vor dem Senden an Application Insights heraus. Sie können die Filterung zusammen mit der Stichprobenerstellung oder einzeln verwenden.

Zum Filtern von Telemetriedaten schreiben Sie einen Telemetrieprozessor und registrieren ihn bei `TelemetryConfiguration`. Alle Telemetriedaten durchlaufen den Prozessor. Dabei können Sie auswählen, welche Daten aus dem Datenstrom gelöscht und welche an den nächsten Prozessor in der Kette weitergegeben werden sollen. Dies schließt Telemetriedaten aus den Standardmodulen wie etwa dem HTTP-Anforderungssammler und der Abhängigkeitserfassung ein sowie von Ihnen selbst überwachte Telemetriedaten. Sie können z. B. Telemetriedaten zu Anforderungen von Robots oder erfolgreiche Abhängigkeitsaufrufe herausfiltern.

> [!WARNING]
> Die Filterung der vom SDK gesendeten Telemetriedaten mithilfe von Prozessoren kann die im Portal angezeigten Statistiken verfälschen und die Nachverfolgung verwandter Elemente erschweren.
>
> Verwenden Sie stattdessen [Sampling](./sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Erstellen eines Telemetrieprozessors (C#)

1. Implementieren Sie `ITelemetryProcessor`, um einen Filter zu erstellen:

    Telemetrieprozessoren erstellen eine Verarbeitungskette. Beim Instanziieren eines Telemetrieprozessors erhalten Sie einen Verweis auf den nächsten Prozessor in der Kette. Wenn ein Telemetriedatenpunkt an die Verarbeitungsmethode übergeben wird, führt er seine Aufgaben aus und ruft den nächsten Telemetrieprozessor in der Kette auf (oder nicht).

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Fügen Sie den Prozessor hinzu.

**ASP.NET-Apps**

Fügen Sie den folgenden Codeausschnitt in „ApplicationInsights.config“ ein:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Sie können durch die Bereitstellung von öffentlich benannten Eigenschaften in Ihrer Klasse Zeichenfolgenwerte aus der .config-Datei übergeben.

> [!WARNING]
> Achten Sie darauf, dass die Typnamen und sonstige Eigenschaftennamen in der .config-Datei mit den Klassen- und Eigenschaftennamen im Code übereinstimmen. Wenn die .config-Datei auf einen nicht vorhandenen Typ oder eine nicht vorhandene Eigenschaft verweist, könnte das SDK möglicherweise automatisch keine Telemetriedaten mehr senden.
>

Alternativ können Sie den Filter im Code initialisieren. Fügen Sie in einer geeigneten Initialisierungsklasse, z. B. AppStart in `Global.asax.cs`, Ihren Prozessor in die Kette ein:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Nach diesem Punkt erstellte Telemetrieclients verwenden Ihre Prozessoren.

**ASP.NET Core/Workerdienst-Apps**

> [!NOTE]
> Das Hinzufügen eines Prozessors mithilfe von `ApplicationInsights.config` oder `TelemetryConfiguration.Active` ist für ASP.NET Core-Anwendungen sowie für das SDK „Microsoft.ApplicationInsights.WorkerService“ ungültig.

Bei Apps, die mit [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) oder [WorkerService](worker-service.md#adding-telemetry-processors) geschrieben wurden, wird ein neuer Telemetrieprozessor, wie im Beispiel veranschaulicht, durch Anwenden der Erweiterungsmethode `AddApplicationInsightsTelemetryProcessor` auf `IServiceCollection` hinzugefügt. Diese Methode wird in der Methode `ConfigureServices` Ihrer `Startup.cs`-Klasse aufgerufen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Beispielfilter

#### <a name="synthetic-requests"></a>Synthetische Anforderungen

Filtern Sie Bots und Webtests heraus. Mit dem Metrik-Explorer haben Sie die Möglichkeit, synthetische Quellen herauszufiltern. Dabei werden jedoch der Datenverkehr und der Umfang der erfassten Daten verringert, da die Quellen im SDK selbst gefiltert werden.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Fehler bei der Authentifizierung

Filtern Sie Abfragen mit der Antwort „401“ heraus.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Herausfiltern schneller Remoteabhängigkeitsaufrufe

Wenn Sie nur langsame Aufrufe diagnostizieren möchten, filtern Sie die schnellen Aufrufe heraus.

> [!NOTE]
> Durch diese Filterung wird die im Portal angezeigte Statistik verfälscht.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnostizieren von Abhängigkeitsproblemen

[diesem Blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) wird ein Projekt zur Diagnose von Abhängigkeitsproblemen beim automatischen Senden regulärer Pings an Abhängigkeiten beschrieben.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript-Webanwendungen

**Filtern mithilfe von „ITelemetryInitializer“**

1. Erstellen Sie eine Rückruffunktion für einen Telemetrieinitialisierer. Die Rückruffunktion akzeptiert `ITelemetryItem` als Parameter. Dies ist das Ereignis, das gerade verarbeitet wird. Wenn dieser Rückruf `false` zurückgibt, wird das Telemetrieelement herausgefiltert.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Fügen Sie den Rückruf für den Telemetrieinitialisierer hinzu:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Hinzufügen/Ändern von Eigenschaften von: ITelemetryInitializer

Verwenden Sie Telemetrieinitialisierer, um Telemetriedaten mit zusätzlichen Informationen anzureichern oder die von Standardtelemetriemodulen festgelegten Telemetrieeigenschaften zu überschreiben.

Das Application Insights for Web-Paket erfasst beispielsweise Telemetriedaten über HTTP-Anforderungen. Standardmäßig kennzeichnet es jede Anforderung mit einem Antwortcode >=400 als fehlerhaft. Wenn 400 jedoch als erfolgreich behandelt werden soll, können Sie einen Telemetrieinitialisierer angeben, der die Success-Eigenschaft festlegt.

Wenn Sie einen Telemetrieinitialisierer angeben, wird dieser immer aufgerufen, wenn eine der Track*()-Methoden aufgerufen wird. Dies umfasst auch `Track()`-Methoden, die von den Standardtelemetriemodulen aufgerufen werden. Nach Abmachung legen diese Module keine Eigenschaft fest, die bereits durch einen Initialisierer festgelegt wurde. Telemetrieinitialisierer werden vor Telemetrieprozessoren aufgerufen. Daher sind alle von Initialisierern durchgeführten Anreicherungen für Prozessoren sichtbar.

**Definieren des Initialisierers**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET-Apps: Laden des Initialisierers**

In "ApplicationInsights.config":

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Alternativ können Sie den Initialisierer im Code instanziieren, z. B. in „Global.aspx.cs“:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Weitere Informationen zu diesem Beispiel finden Sie [hier](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

**ASP.NET Core/Workerdienst-Apps: Laden des Initialisierers**

> [!NOTE]
> Das Hinzufügen eines Initialisierers mithilfe von `ApplicationInsights.config` oder `TelemetryConfiguration.Active` ist für ASP.NET Core-Anwendungen sowie für das SDK „Microsoft.ApplicationInsights.WorkerService“ ungültig.

Bei Apps, die mit [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) oder [WorkerService](worker-service.md#adding-telemetryinitializers) geschrieben wurden, wird ein neuer Telemetrieinitialisierer, wie im Beispiel veranschaulicht, durch Hinzufügen zum Container für die Abhängigkeitsinjektion hinzugefügt. Dies erfolgt in der `Startup.ConfigureServices`-Methode.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript-Telemetrieinitialisierer
*JavaScript*

Fügen Sie einen Telemetrieinitialisierer mithilfe des Codeausschnitts für den onInit-Rückruf ein:

```html
<script type="text/javascript">
!function(T,l,y){<!-- Removed the Snippet code for brevity -->}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",
crossOrigin: "anonymous",
onInit: function (sdk) {
  sdk.addTelemetryInitializer(function (envelope) {
    envelope.data.someField = 'This item passed through my telemetry initializer';
  });
}, // Once the application insights instance has loaded and initialized this method will be called
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY"
}});
</script>
```

Eine Übersicht über die nicht benutzerdefinierten Eigenschaften, die für das Telemetrieelement verfügbar sind, finden Sie im [Application Insights-Exportdatenmodell](./export-data-model.md).

Sie können beliebig viele Initialisierer hinzufügen. Sie werden in der Reihenfolge aufgerufen, in der sie hinzugefügt werden.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python-Telemetrieprozessoren

Bei Telemetrieprozessoren in OpenCensus Python handelt es sich lediglich um Rückruffunktionen, die aufgerufen werden, um Telemetriedaten zu verarbeiten, bevor sie exportiert werden. Die Rückruffunktion muss einen [Umschlag](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86)-Datentyp als Parameter akzeptieren. Stellen Sie sicher, dass die Rückruffunktion `False` zurückgibt, um die Telemetriedaten aus dem Export herauszufiltern. Das Schema für die Azure Monitor-Datentypen in Umschlägen finden Sie [auf GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Sie können `cloud_RoleName` ändern, indem Sie das `ai.cloud.role`-Attribut im Feld `tags` ändern.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Sie können beliebig viele Prozessoren hinzufügen. Sie werden in der Reihenfolge aufgerufen, in der sie hinzugefügt werden. Wenn ein Prozessor eine Ausnahme auslöst, wirkt sich dies nicht auf die folgenden Prozessoren aus.

### <a name="example-telemetryinitializers"></a>Beispiel: Telemetrieinitialisierer

#### <a name="add-a-custom-property"></a>Hinzufügen einer benutzerdefinierten Eigenschaft

Mit dem folgenden Beispielinitialisierer wird allen überwachten Telemetriedaten eine benutzerdefinierte Eigenschaft hinzugefügt:

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Hinzufügen eines Cloudrollennamens

Mit dem folgenden Beispielinitialisierer wird der Cloudrollenname für alle überwachten Telemetriedaten festgelegt.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Hinzufügen von Informationen aus HttpContext

Der folgende Beispielinitialisierer liest Daten aus [`HttpContext`](/aspnet/core/fundamentals/http-context) und fügt sie an eine `RequestTelemetry`-Instanz an. Der `IHttpContextAccessor` wird automatisch durch die Abhängigkeitsinjektion des Konstruktors bereitgestellt.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor und ITelemetryInitializer

Was ist der Unterschied zwischen Telemetrieprozessoren und Telemetrieinitialisierern?

* Die Einsatzmöglichkeiten überlappen sich teilweise. Beide können verwendet werden, um einer Telemetrie Eigenschaften hinzuzufügen oder diese zu ändern. Es wird jedoch empfohlen, für diesen Zweck Initialisierer zu verwenden.
* Telemetrieinitialisierer werden immer vor Telemetrieprozessoren ausgeführt.
* Telemetrieinitialisierer können mehrmals aufgerufen werden. Laut Konvention legen sie keine Eigenschaften fest, die bereits festgelegt wurden.
* Mit Telemetrieprozessoren können Sie ein Telemetrieelement vollständig ersetzen oder verwerfen.
* Alle registrierten Telemetrieinitialisierer werden für jedes Telemetrieelement garantiert aufgerufen. Bei Telemetrieprozessoren wird mit dem SDK der erste Telemetrieprozessor garantiert aufgerufen. Je nach vorangehenden Telemetrieprozessoren werden die restlichen Prozessoren anschließend aufgerufen oder nicht.
* Mit Telemetrieinitialisierern können Sie Telemetriedaten mit zusätzlichen Eigenschaften anreichern oder die vorhandenen Daten überschreiben. Mit einem Telemetrieprozessor können Telemetriedaten herausgefiltert werden.

## <a name="troubleshoot-applicationinsightsconfig"></a>Behandeln von Problemen mit „ApplicationInsights.config“

* Vergewissern Sie sich, dass der vollqualifizierte Typname und der Assemblyname korrekt sind.
* Vergewissern Sie sich, dass sich die Datei „applicationinsights.config“ in Ihrem Ausgabeverzeichnis befindet und auf dem neuesten Stand ist.

## <a name="reference-docs"></a>Referenz

* [API-Übersicht](./api-custom-events-metrics.md)
* [ASP.NET-Referenz](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK-Code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Nächste Schritte
* [Durchsuchen von Ereignissen und Protokollen](./diagnostic-search.md)
* [Stichprobenentnahme](./sampling.md)
* [Problembehandlung](../faq.yml)

