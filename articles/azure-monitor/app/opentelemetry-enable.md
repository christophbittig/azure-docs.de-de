---
title: Aktivieren von Azure Monitor OpenTelemetry für .NET-, Node.js- und Python-Anwendungen
description: Enthält Anleitungen zum Aktivieren von Azure Monitor für Anwendungen mithilfe von OpenTelemetry
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: ae52a8977247903574b9d23fda795e5fd8b7ea3d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054427"
---
# <a name="enable-azure-monitor-opentelemetry-exporter-for-net-nodejs-and-python-applications-preview"></a>Aktivieren von Azure Monitor OpenTelemetry Exporter für .NET-, Node.js- und Python-Anwendungen (Vorschau)

In diesem Artikel wird beschrieben, wie das OpenTelemetry-basierte Azure Monitor-Vorschauangebot aktiviert und konfiguriert wird. Wenn Sie die Anweisungen in diesem Artikel befolgen, können Sie OpenTelemetry-Ablaufverfolgungen an Azure Monitor Application Insights senden. Weitere Informationen zu OpenTelemetry finden Sie in der [OpenTelemetry-Übersicht](opentelemetry-overview.md) oder in den [Häufig gestellten Fragen zu OpenTelemetry](/azure/azure-monitor/faq#opentelemetry).

> [!IMPORTANT]
> Azure Monitor OpenTelemetry Exporter für .NET-, Node.js- und Python-Anwendungen befindet sich derzeit in der VORSCHAU.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="limitations-of-preview-release"></a>Einschränkungen der Vorschauversion

### <a name="net"></a>[.NET](#tab/net)

Überlegen Sie sorgfältig, ob diese Vorschauversion für Sie geeignet ist. Sie **ermöglicht nur verteilte Ablaufverfolgung** und _schließt Folgendes aus_:
 - Metrik-API (benutzerdefinierte Metriken, [vorab aggregierte Metriken](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [Livemetriken](live-stream.md)
 - Protokollierungs-API (Konsolenprotokolle, Protokollierungsbibliotheken usw.)
 - Automatische Erfassung nicht behandelter Ausnahmen
 - [Profiler](profiler-overview.md)
 - [Momentaufnahmedebugger](snapshot-debugger.md)
 - [Offlinedatenträgerspeicher und Wiederholungslogik](telemetry-channels.md#built-in-telemetry-channels)
 - [Azure AD-Authentifizierung](azure-ad-authentication.md)
 - [Stichproben](sampling.md)
 - Automatisches Auffüllen mit Daten von Cloudrollenname und Cloudrolleninstanz in Azure-Umgebungen
 - Automatisches Auffüllen mit Daten von Benutzer-ID und authentifizierter Benutzer-ID bei Verwendung des [Application Insights JavaScript SDK](javascript.md)
 - Automatisches Auffüllen mit Daten von Benutzer-IP-Adressen (zum Bestimmen von Standortattributen)
 - Möglichkeit zum Überschreiben des [Vorgangsnamens](correlation.md#data-model-for-telemetry-correlation)
 - Möglichkeit zum manuellen Festlegen der Benutzer-ID oder authentifizierten Benutzer-ID
 - Weitergabe des Vorgangsnamens an Abhängigkeitstelemetrie
 - Unterstützung von [Instrumentierungsbibliotheken](#instrumentation-libraries) in Azure Functions

Diejenigen Benutzer, die den vollen Funktionsumfang benötigen, sollten das vorhandene Application Insights [ASP.NET](asp-net.md) oder [ASP.NET Core](asp-net-core.md) SDK verwenden, bis das OpenTelemetry-basierte Angebot ausgereift ist.

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Überlegen Sie sorgfältig, ob diese Vorschauversion für Sie geeignet ist. Sie ermöglicht nur verteilte Ablaufverfolgung und _schließt Folgendes aus_:
 - Metrik-API (benutzerdefinierte Metriken, [vorab aggregierte Metriken](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [Livemetriken](live-stream.md)
 - Protokollierungs-API (Konsolenprotokolle, Protokollierungsbibliotheken usw.)
 - Automatische Erfassung nicht behandelter Ausnahmen
 - [Azure AD-Authentifizierung](azure-ad-authentication.md)
 - [Stichproben](sampling.md)
 - Automatisches Auffüllen mit Daten von Cloudrollenname und Cloudrolleninstanz in Azure-Umgebungen
 - Automatisches Auffüllen mit Daten von Benutzer-ID und authentifizierter Benutzer-ID bei Verwendung des [Application Insights JavaScript SDK](javascript.md)
 - Automatisches Auffüllen mit Daten von Benutzer-IP-Adressen (zum Bestimmen von Standortattributen)
 - Möglichkeit zum Überschreiben des [Vorgangsnamens](correlation.md#data-model-for-telemetry-correlation)
 - Möglichkeit zum manuellen Festlegen der Benutzer-ID oder authentifizierten Benutzer-ID
 - Weitergabe des Vorgangsnamens an Abhängigkeitstelemetrie

Diejenigen Benutzer, die den vollen Funktionsumfang benötigen, sollten das vorhandene [Application Insights Node.js SDK](nodejs.md) verwenden, bis das OpenTelemetry-basierte Angebot ausgereift ist.

> [!WARNING] 
> Derzeit funktioniert dieser Exporter nur für Node.js-Umgebungen. Verwenden Sie [das Application Insights JavaScript SDK](javascript.md) für Web-/Browserszenarien.

### <a name="python"></a>[Python](#tab/python)

Überlegen Sie sorgfältig, ob diese Vorschauversion für Sie geeignet ist. Sie **ermöglicht nur verteilte Ablaufverfolgung** und _schließt Folgendes aus_:
 - Metrik-API (benutzerdefinierte Metriken, [vorab aggregierte Metriken](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [Livemetriken](live-stream.md)
 - Protokollierungs-API (Konsolenprotokolle, Protokollierungsbibliotheken usw.)
 - Automatische Erfassung nicht behandelter Ausnahmen
 - Offlinedatenträgerspeicher und Wiederholungslogik
 - [Azure AD-Authentifizierung](azure-ad-authentication.md)
 - [Stichproben](sampling.md)
 - Automatisches Auffüllen mit Daten von Cloudrollenname und Cloudrolleninstanz in Azure-Umgebungen
 - Automatisches Auffüllen mit Daten von Benutzer-ID und authentifizierter Benutzer-ID bei Verwendung des [Application Insights JavaScript SDK](javascript.md)
 - Automatisches Auffüllen mit Daten von Benutzer-IP-Adressen (zum Bestimmen von Standortattributen)
 - Möglichkeit zum Überschreiben des [Vorgangsnamens](correlation.md#data-model-for-telemetry-correlation)
 - Möglichkeit zum manuellen Festlegen der Benutzer-ID oder authentifizierten Benutzer-ID
 - Weitergabe des Vorgangsnamens an Abhängigkeitstelemetrie
 - Unterstützung von [Instrumentierungsbibliotheken](#instrumentation-libraries) in Azure Functions

Diejenigen Benutzer, die den vollen Funktionsumfang benötigen, sollten das vorhandene [Application Insights Python-OpenCensus SDK](opencensus-python.md) verwenden, bis das OpenTelemetry-basierte Angebot ausgereift ist.

---

## <a name="get-started"></a>Erste Schritte

Führen Sie die Schritte in diesem Abschnitt aus, und Sie können Ihre Anwendung mit OpenTelemetry instrumentieren.

### <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement ([kostenloses Azure-Abonnement erstellen](https://azure.microsoft.com/free/))
- Application Insights-Ressource: [Erstellen einer Application Insights-Ressource](create-workspace-resource.md#create-workspace-based-resource)

### <a name="net"></a>[.NET](#tab/net)

- Anwendung mit einer offiziell unterstützten Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet) oder [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) >= `.NET Framework 4.6.1`.


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Anwendung mit einer offiziell [unterstützten Version](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments) der Node.js-Runtime.
  - [Von OpenTelemetry unterstützte Runtimes](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [Von Azure Monitor OpenTelemetry Exporter unterstützte Runtimes](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

### <a name="python"></a>[Python](#tab/python)

- Python-Anwendung ab Version 3.6


---

### <a name="install-the-client-libraries"></a>Installieren der Clientbibliotheken

#### <a name="net"></a>[.NET](#tab/net)

Installieren Sie das neueste [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter)-NuGet-Paket:

```dotnetcli
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter 
```

Wenn Sie eine Fehlermeldung wie „There are no versions available for the package 'Azure.Monitor.OpenTelemetry.Exporter'“ (Es sind keine Versionen für das Paket 'Azure.Monitor.OpenTelemetry.Exporter' verfügbar) erhalten, liegt das höchstwahrscheinlich daran, dass die NuGet-Paketquellen nicht festgelegt wurden. Sie können versuchen, die Quelle mit der Option `-s` anzugeben:

```dotnetcli
# Install the latest package with NuGet package source specified
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter -s https://api.nuget.org/v3/index.json
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installieren Sie diese Pakete:

- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)
- [@opentelemetry/sdk-trace-node](https://www.npmjs.com/package/@opentelemetry/sdk-trace-node)
- [@azure/monitor-opentelemetry-exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)

```sh
npm install @opentelemetry/sdk-trace-base
npm install @opentelemetry/sdk-trace-node
npm install @azure/monitor-opentelemetry-exporter
```

Die folgenden Pakete werden auch für einige spezifische Szenarien verwendet, die weiter unten in diesem Artikel beschrieben werden.

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/instrumentation-http](https://www.npmjs.com/package/@opentelemetry/instrumentation-http)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/instrumentation-http
```

#### <a name="python"></a>[Python](#tab/python)

Installieren Sie das neueste [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/)-Pypi-Paket.

```sh
pip install azure-monitor-opentelemetry-exporter 
```

---

### <a name="enable-azure-monitor-application-insights"></a>Aktivieren Sie Azure Monitor Application Insights.

#### <a name="add-opentelemetry-instrumentation-code"></a>Fügen Sie OpenTelemetry-Instrumentierungscode hinzu.

##### <a name="net"></a>[.NET](#tab/net)

Der folgende Code veranschaulicht das Aktivieren von OpenTelemetry in einer C#-Konsolenanwendung durch Einrichten von OpenTelemetry TracerProvider. Dieser Code muss beim Anwendungsstart enthalten sein. Für ASP.NET Core wird dies in der Regel in der Klasse `ConfigureServices`Anwendungsmethode`Startup` durchgeführt. Für ASP.NET-Anwendungen erfolgt dies in der Regel in `Global.asax.cs`.

```csharp
using System.Diagnostics;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class Program
{
    private static readonly ActivitySource MyActivitySource = new ActivitySource(
        "OTel.AzureMonitor.Demo");

    public static void Main()
    {
        using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddAzureMonitorTraceExporter(o =>
            {
                o.ConnectionString = "<Your Connection String>";
            })
            .Build();

        using (var activity = MyActivitySource.StartActivity("TestActivity"))
        {
            activity?.SetTag("CustomTag1", "Value1");
            activity?.SetTag("CustomTag2", "Value2");
        }

        System.Console.WriteLine("Press Enter key to exit.");
        System.Console.ReadLine();
    }
}
```

> [!NOTE]
> Die Klassen `Activity` und `ActivitySource` aus dem `System.Diagnostics`-Namespace stellen die OpenTelemetry-Konzepte von `Span` bzw. `Tracer` dar. Und Sie erstellen `ActivitySource` direkt mit dem Konstruktor dieses Elements, anstatt `TracerProvider` zu verwenden (jede [`ActivitySource`](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace/customizing-the-sdk#activity-source) muss explizit mit `TracerProvider` über `AddSource()` verbunden werden). Dies liegt daran, dass Teile der OpenTelemetry-Ablaufverfolgungs-API direkt in die .NET-Runtime integriert sind. [Weitere Informationen](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Api/README.md#introduction-to-opentelemetry-net-tracing-api)

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Der folgende Code veranschaulicht das Aktivieren von OpenTelemetry in einer einfachen Node.js-Anwendung.

```typescript
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { BatchSpanProcessor, Span } = require("@opentelemetry/sdk-trace-base");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
provider.register();

// Create an exporter instance
const exporter = new AzureMonitorTraceExporter({
  instrumentationKey: "<Your Connection String>"
});

// Add the exporter to the provider
provider.addSpanProcessor(
  new BatchSpanProcessor(exporter, {
    bufferTimeout: 15000,
    bufferSize: 1000
  })
);
// Create a span. A span must be closed.
const parentSpan = tracer.startSpan("main");
for (let i = 0; i < 10; i += 1) {
   doWork(parentSpan);
}
// Be sure to end the span.
parentSpan.end();

function doWork(parent: Span) {
  // Start another span. In this example, the main method already started a
  // span, so that'll be the parent span, and this will be a child span.
  const ctx = opentelemetry.trace.setSpan(opentelemetry.context.active(), parent);
  const span = tracer.startSpan("doWork", undefined, ctx);
  // simulate some random work.
  for (let i = 0; i <= Math.floor(Math.random() * 40000000); i += 1) {
    // empty
  }
  // Set attributes to the span.
  span.setAttribute("key", "value");
  // Annotate our span to capture metadata about our operation
  span.addEvent("invoking doWork");
  span.end();
}
```

##### <a name="python"></a>[Python](#tab/python)

Der folgende Code veranschaulicht das Aktivieren von OpenTelemetry in einer einfachen Python-Anwendung.

```python
import os
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)
span_processor = BatchSpanProcessor(exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("hello"):
    print("Hello, World!")

```

---

> [!TIP]
> Fügen Sie [Instrumentierungsbibliotheken](#instrumentation-libraries) hinzu, um Telemetriedaten über gängige Frameworks/Bibliotheken hinweg automatisch zu sammeln.

#### <a name="set-application-insights-connection-string"></a>Festlegen der Application Insights-Verbindungszeichenfolge

Ersetzen Sie den Platzhalter `<Your Connection String>` im Code oben durch die Verbindungszeichenfolge aus IHRER Application Insights-Ressource.

:::image type="content" source="media/opentelemetry/connection-string.png" alt-text="Screenshot: Application Insights-Verbindungszeichenfolge.":::

#### <a name="confirm-data-is-flowing"></a>Bestätigen des Datenflusses

Führen Sie Ihre Anwendung aus, und öffnen Sie die Registerkarte der Application Insights-Ressource im Azure-Portal. Es kann einige Minuten dauern, bis Daten im Portal angezeigt werden.

> [!NOTE]
> Wenn Sie die Anwendung nicht ausführen oder Daten nicht wie erwartet abrufen können, navigieren Sie zu [Problembehandlung](#troubleshooting).

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Screenshot der Registerkarte „Übersicht“ von Application Insights mit hervorgehobenen Serveranforderungen und der Serverantwortzeit":::

> [!IMPORTANT]
> Wenn Sie über zwei oder mehr Dienste verfügen, die Telemetriedaten an dieselbe Application Insights-Ressource ausgeben, müssen Sie [Cloudrollennamen festlegen](#set-cloud-role-name-and-cloud-role-instance), um sie ordnungsgemäß in der Anwendungszuordnung darzustellen.

> [!NOTE]
> Im Rahmen der Verwendung der Application Insights-Instrumentierung sammeln und senden wir Diagnosedaten an Microsoft. Diese Daten helfen uns, Application Insights auszuführen und zu verbessern. Sie haben die Möglichkeit, die Sammlung nicht wesentlicher Daten zu deaktivieren. [Weitere Informationen](./statsbeat.md).

## <a name="set-cloud-role-name-and-cloud-role-instance"></a>Festlegen von Cloudrollennamen und Cloudrolleninstanz

Sie können [Cloudrollennamen](app-map.md#understanding-cloud-role-name-within-the-context-of-the-application-map) und die Cloudrolleninstanz über [Ressourcenattribute](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/sdk.md#resource-sdk) festlegen. Dadurch werden der Cloudrollenname und die Cloudrolleninstanz von ihrem Standardwert auf einen Wert aktualisiert, der für Ihr Team sinnvoll ist. Diese Angaben werden in der Anwendungszuordnung als Name unterhalb eines Knotens angezeigt. Der Cloudrollenname verwendet die Attribute `service.namespace` und `service.name`, obwohl er auf `service.name` zurückfällt, wenn `service.namespace` nicht festgelegt ist. Die Cloudrolleninstanz verwendet den `service.instance.id`-Attributwert.

### <a name="net"></a>[.NET](#tab/net)

```csharp
// Setting Role name and Role instance
var resourceAttributes = new Dictionary<string, object> {
    { "service.name", "my-service" },
    { "service.namespace", "my-namespace" },
    { "service.instance.id", "my-instance" }};
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);
// Done setting Role name and Role instance

// Set ResourceBuilder on the provider
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .SetResourceBuilder(resourceBuilder)
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>";
        })
        .Build();
```


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```typescript
...
import { NodeTracerProvider, NodeTracerConfig } from "@opentelemetry/sdk-trace-node";
import { Resource } from "@opentelemetry/resources";
import { SemanticResourceAttributes } from "@opentelemetry/semantic-conventions";

// ----------------------------------------
// Setting Role name and role instance
// ----------------------------------------
const config: NodeTracerConfig = {
        resource: new Resource({
            [SemanticResourceAttributes.SERVICE_NAME]: "my-helloworld-service",
            [SemanticResourceAttributes.SERVICE_NAMESPACE]: "my-namespace",
            [SemanticResourceAttributes.SERVICE_INSTANCE_ID]: "my-instance",
        }),
    };
// ----------------------------------------
// Done setting Role name and role instance
// ----------------------------------------
const provider = new NodeTracerProvider(config);
...
```

### <a name="python"></a>[Python](#tab/python)

```python
...
from opentelemetry.sdk.resources import SERVICE_NAME, SERVICE_NAMESPACE, SERVICE_INSTANCE_ID, Resource
trace.set_tracer_provider(
    TracerProvider(
        resource=Resource.create(
            {
                SERVICE_NAME: "my-helloworld-service",
# ----------------------------------------
# Setting Role name and role instance
# ----------------------------------------
                SERVICE_NAMESPACE: "my-namespace",
                SERVICE_INSTANCE_ID: "my-instance",
# ----------------------------------------------
# Done setting Role name and role instance
# ----------------------------------------------
            }
        )
    )
)
...
```

---

Referenz: [Semantikkonventionen für Ressourcen](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/semantic_conventions/README.md)

## <a name="sampling"></a>Stichproben

Sampling wird zwar in OpenTelemetry unterstützt, derzeit aber nicht in Azure Monitor OpenTelemetry Exporter.

> [!WARNING]
> Durch das Aktivieren von Sampling in OpenTelemetry werden standard- und protokollbasierte Metriken extrem ungenau, was sich negativ auf alle Application Insights-Funktionen auswirkt. Darüber hinaus führt das Aktivieren von Sampling zusammen mit den vorhandenen Application Insights SDKs zu fehlerhaften Ablaufverfolgungen.

## <a name="instrumentation-libraries"></a>Instrumentierungsbibliotheken
<!-- Microsoft has tested and validated that the following instrumentation libraries will work with the **Preview** Release. -->
Die folgenden Bibliotheken wurden für das Arbeiten mit der Vorschauversion überprüft und funktionieren:

> [!WARNING]
> Instrumentierungsbibliotheken basieren auf experimentellen OpenTelemetry-Spezifikationen. Die **Vorschauunterstützung** von Microsoft besteht darin, sicherzustellen, dass die unten aufgeführten Bibliotheken Daten an Azure Monitor Application Insights ausgeben, aber es ist möglich, dass Breaking Changes oder experimentelle Zuordnungen einige Datenelemente blockieren.

### <a name="http"></a>HTTP

#### <a name="net"></a>[.NET](#tab/net)

- [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md) Version: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNet/1.0.0-rc7)
- [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md) Version: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNetCore/1.0.0-rc7)
- [HTTP-Clients](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md) Version: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.Http/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http/README.md) Version: [0.26.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-http/v/0.26.0)


#### <a name="python"></a>[Python](#tab/python)

- [Django](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-django) Version: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-django/0.24b0/)
- [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) Version: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-flask/0.24b0/)
- [Anforderungen](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-requests) Version: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-requests/0.24b0/)

---

### <a name="database"></a>Datenbank

#### <a name="net"></a>[.NET](#tab/net)

- [SQL-Client](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.SqlClient/README.md) Version: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.SqlClient/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [mysql](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/plugins/node/opentelemetry-instrumentation-mysql) Version: [0.25.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-mysql/v/0.25.0)

#### <a name="python"></a>[Python](#tab/python)

- [Psycopg2](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-psycopg2) Version: [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/0.24b0/)

---

> [!NOTE]
> Das **Vorschauangebot** umfasst nur Instrumentierungen, die HTTP- und Datenbankanforderungen verarbeiten. Weitere Informationen finden Sie unter [Semantische Konventionen für OpenTelemetry](https://github.com/open-telemetry/opentelemetry-specification/tree/main/specification/trace/semantic_conventions).

## <a name="modify-telemetry"></a>Ändern der Telemetrie

### <a name="add-span-attributes"></a>Hinzufügen von span-Attributen

Span-Attribute können mit einer der folgenden beiden Methoden hinzugefügt werden.
1. Verwenden von Optionen, die von [Instrumentierungsbibliotheken](#instrumentation-libraries) bereitgestellt werden.
2. Hinzufügen eines benutzerdefinierten Span-Prozessors.

Zu diesen Attributen kann das Hinzufügen einer benutzerdefinierten Eigenschaft zu Ihrer Telemetrie gehören. Sie können auch Attribute verwenden, um optionale Felder im Application Insights-Schema festzulegen, z. B. Client-IP.

> [!TIP]
> Der Vorteil der Verwendung von „von Instrumentierungsbibliotheken bereitgestellten Optionen“ (sofern verfügbar) besteht darin, dass der gesamte Kontext verfügbar ist, was bedeutet, dass Benutzer zusätzliche Attribute hinzufügen oder filtern können. Die Anreicherungsoption der HttpClient-Instrumentierungsbibliothek umfasst z. B. das Gewähren von Benutzerzugriff auf httpRequestMessage selbst, um beliebige Elemente auszuwählen und als Attribut zu speichern.

#### <a name="add-custom-property"></a>Hinzufügen einer benutzerdefinierten Eigenschaft

Alle [Attribute](#add-span-attributes), die activity/span hinzugefügt werden, werden als benutzerdefinierte Eigenschaften exportiert. Sie füllen das Feld _customDimensions_ in den Anforderungs- und/oder Abhängigkeitstabellen in Application Insights mit Daten auf.

##### <a name="net"></a>[.NET](#tab/net)

1. Viele Instrumentierungsbibliotheken bieten eine Anreicherungsoption. Anleitungen dazu finden Sie in den Infodateien der einzelnen Instrumentierungsbibliotheken.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#enrich)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#enrich)
    - [HttpClient und HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#enrich)

2. Verwenden eines benutzerdefinierten Prozessors:

> [!TIP]
> Fügen Sie den unten gezeigten Prozessor VOR dem Azure Monitor Exporter hinzu.

```csharp
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddProcessor(new ActivityEnrichingProcessor())
        .AddAzureMonitorTraceExporter(o =>
        {
                o.ConnectionString = "<Your Connection String>"
        })
        .Build();
```

Fügen Sie Ihrem Projekt `ActivityEnrichingProcessor.cs` mit dem unten aufgeführten Code hinzu.

```csharp
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class ActivityEnrichingProcessor : BaseProcessor<Activity>
{
    public override void OnEnd(Activity activity)
    {
        // The updated activity will be available to all processors which are called after this processor.
        activity.DisplayName = "Updated-" + activity.DisplayName;
        activity.SetTag("CustomDimension1", "Value1");
        activity.SetTag("CustomDimension2", "Value2");
    }
}
```


##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Verwenden eines benutzerdefinierten Prozessors:

> [!TIP]
> Fügen Sie den unten gezeigten Prozessor VOR dem Azure Monitor Exporter hinzu.

```typescript
import { AzureMonitorTraceExporter } from "@azure/monitor-opentelemetry-exporter";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";
import { ReadableSpan, SimpleSpanProcessor, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class SpanEnrichingProcessor implements SpanProcessor{
    forceFlush(): Promise<void>{
        return Promise.resolve();
    }
    shutdown(): Promise<void>{
        return Promise.resolve();
    }
    onStart(_span: Span): void{}
    onEnd(span: ReadableSpan){
        span.attributes["CustomDimension1"] = "value1";
        span.attributes["CustomDimension2"] = "value2";
    }
}

const provider = new NodeTracerProvider();
const azureExporter = new AzureMonitorTraceExporter();
provider.addSpanProcessor(new SpanEnrichingProcessor());
provider.addSpanProcessor(new SimpleSpanProcessor(azureExporter));

```

##### <a name="python"></a>[Python](#tab/python)

Verwenden eines benutzerdefinierten Prozessors:

> [!TIP]
> Fügen Sie den unten gezeigten Prozessor VOR dem Azure Monitor Exporter hinzu.

```python
...
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
span_processor = BatchSpanProcessor(exporter)
span_enrich_processor = SpanEnrichingProcessor()
trace.get_tracer_provider().add_span_processor(span_enrich_processor)
trace.get_tracer_provider().add_span_processor(span_processor)
...
```

Fügen Sie Ihrem Projekt `SpanEnrichingProcessor.py` mit dem unten aufgeführten Code hinzu.

```python
from opentelemetry.sdk.trace import SpanProcessor

class SpanEnrichingProcessor(SpanProcessor):

    def on_end(self, span):
        span._name = "Updated-" + span.name
        span._attributes["CustomDimension1"] = "Value1"
        span._attributes["CustomDimension2"] = "Value2"

```
---

#### <a name="set-user-ip"></a>Festlegen der Benutzer-IP

Sie können das Feld _client_IP_ für Anforderungen auffüllen, indem Sie das `http.client_ip`-Attribut für activity/span festlegen. Application Insights verwendet die IP-Adresse zum Generieren von Benutzerstandortattributen und [verwirft sie dann standardmäßig](ip-collection.md#default-behavior).

##### <a name="net"></a>[.NET](#tab/net)

Verwenden Sie das [Beispiel zum Hinzufügen einer benutzerdefinierten Eigenschaft](#add-custom-property), ersetzen Sie dabei aber die folgenden Codezeilen in `ActivityEnrichingProcessor.cs`:

```C#
// only applicable in case of activity.Kind == Server
activity.SetTag("http.client_ip", "<IP Address>");
```

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Verwenden Sie das [Beispiel zum Hinzufügen einer benutzerdefinierten Eigenschaft](#add-custom-property), ersetzen Sie dabei aber die folgenden Codezeilen:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.HTTP_CLIENT_IP] = "<IP Address>";
    }
}
```

##### <a name="python"></a>[Python](#tab/python)

Verwenden Sie das [Beispiel zum Hinzufügen einer benutzerdefinierten Eigenschaft](#add-custom-property), ersetzen Sie dabei aber die folgenden Codezeilen in `SpanEnrichingProcessor.py`:

```python
span._attributes["http.client_ip"] = "<IP Address>"
```

---
<!--

#### Set user ID or authenticated user ID

You can populate the _user_Id_ or _user_Authenticatedid_ field for requests by setting `xyz` or `xyz` attribute on activity/span. User ID is an anonymous user identifier and Authenticated User ID is a known user identifier.

> [!IMPORTANT]
> Consult applicable privacy laws before setting Authenticated User ID.

##### [.NET](#tab/net)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```C#
Placeholder
```

##### [Node.js](#tab/nodejs)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.ENDUSER_ID] = "<User ID>";
    }
}
```

##### [Python](#tab/python)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```python
span._attributes["enduser.id"] = "<User ID>"
```

---
-->

### <a name="filter-telemetry"></a>Filtern von Telemetriedaten

Sie können folgende Möglichkeiten verwenden, um Telemetriedaten herauszufiltern, bevor Sie Ihre Anwendung verlassen.

#### <a name="net"></a>[.NET](#tab/net)

1. Viele Instrumentierungsbibliotheken bieten eine Filteroption. Anleitungen dazu finden Sie in den Infodateien der einzelnen Instrumentierungsbibliotheken.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#filter)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#filter)
    - [HttpClient und HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#filter)

2. Verwenden eines benutzerdefinierten Prozessors:
    
    ```csharp
    using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddProcessor(new ActivityFilteringProcessor())
            .AddAzureMonitorTraceExporter(o =>
            {
                    o.ConnectionString = "<Your Connection String>"
            })
            .Build();
    ```
    
    Fügen Sie Ihrem Projekt `ActivityFilteringProcessor.cs` mit dem unten aufgeführten Code hinzu.
    
    ```csharp
    using System.Diagnostics;
    using OpenTelemetry;
    using OpenTelemetry.Trace;
    
    public class ActivityFilteringProcessor : BaseProcessor<Activity>
    {
        public override void OnStart(Activity activity)
        {
            // prevents all exporters from exporting internal activities
            if (activity.Kind == ActivityKind.Internal)
            {
                activity.IsAllDataRequested = false;
            }
        }
    }
    ```


3. Wenn eine bestimmte Quelle nicht explizit mit `AddSource("ActivitySourceName")` hinzugefügt wird, wird keine der Aktivitäten exportiert, die mit dieser Quelle erstellt wurden.
    
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```C#
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Option zum Ausschließen einer URL, die von vielen HTTP-Instrumentierungsbibliotheken bereitgestellt wird.

    Nachfolgend finden Sie ein Beispiel dafür, wie Sie eine bestimmte URL von der Nachverfolgung mithilfe der [http/https-Instrumentierungsbibliothek](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http) ausschließen können.
    
    ```typescript
    ...
    import { HttpInstrumentation, HttpInstrumentationConfig } from "@opentelemetry/instrumentation-http";
    
    ...
    const httpInstrumentationConfig: HttpInstrumentationConfig = {
        ignoreIncomingPaths: [new RegExp(/dc.services.visualstudio.com/i)]
    };
    const httpInstrumentation = new HttpInstrumentation(httpInstrumentationConfig);
    provider.register();
    registerInstrumentations({
        instrumentations: [
            httpInstrumentation,
        ]
    });
    
    ```

2. Verwenden eines benutzerdefinierten Prozessors. Sie können einen benutzerdefinierten span-Prozessor verwenden, um bestimmte Spannen vom Export auszuschließen. Um Spannen zu markieren, die nicht exportiert werden sollen, legen Sie ihr `TraceFlag` auf `DEFAULT` fest.
Verwenden Sie das [Beispiel zum Hinzufügen einer benutzerdefinierten Eigenschaft](#add-custom-property), ersetzen Sie dabei aber die folgenden Codezeilen:

    ```typescript
    ...
    import { SpanKind, TraceFlags } from "@opentelemetry/api";
    
    class SpanEnrichingProcessor implements SpanProcessor{
        ...
    
        onEnd(span: ReadableSpan) {
            if(span.kind == SpanKind.INTERNAL){
                span.spanContext().traceFlags = TraceFlags.NONE;
            }
        }
    }
    ```

#### <a name="python"></a>[Python](#tab/python)

1. Option zum Ausschließen einer URL, die von vielen HTTP-Instrumentierungsbibliotheken bereitgestellt wird.

    Nachfolgend finden Sie ein Beispiel dafür, wie Sie eine bestimmte URL von der Nachverfolgung mithilfe der [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask)-Instrumentierung ausschließen können.
    
    ```python
    ...
    import flask
    
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    
    # You may also populate OTEL_PYTHON_FLASK_EXCLUDED_URLS env variable
    # List will consist of comma delimited regexes representing which URLs to exclude
    excluded_urls = "client/.*/info,healthcheck"
    
    FlaskInstrumentor().instrument(excluded_urls=excluded_urls) # Do this before flask.Flask
    app = flask.Flask(__name__)
    ...
    ```

2. Verwenden eines benutzerdefinierten Prozessors. Sie können einen benutzerdefinierten span-Prozessor verwenden, um bestimmte Spannen vom Export auszuschließen. Um Spannen zu markieren, die nicht exportiert werden sollen, legen Sie ihr `TraceFlag` auf `DEFAULT` fest.
    
    ```python
    ...
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    trace.set_tracer_provider(TracerProvider())
    span_processor = BatchSpanProcessor(exporter)
    span_filter_processor = SpanFilteringProcessor()
    trace.get_tracer_provider().add_span_processor(span_filter_processor)
    trace.get_tracer_provider().add_span_processor(span_processor)
    ...
    ```
    
    Fügen Sie Ihrem Projekt `SpanFilteringProcessor.py` mit dem unten aufgeführten Code hinzu.
    
    ```python
    from opentelemetry.trace import SpanContext, SpanKind, TraceFlags
    from opentelemetry.sdk.trace import SpanProcessor
    
    class SpanFilteringProcessor(SpanProcessor):
    
        # prevents exporting spans from internal activities
        def on_start(self, span):
            if span._kind is SpanKind.INTERNAL:
                span._context = SpanContext(
                    span.context.trace_id,
                    span.context.span_id,
                    span.context.is_remote,
                    TraceFlags.DEFAULT,
                    span.context.trace_state,
                )
    
    ```
    
    <!-- For more information, see [GitHub Repo](link). -->
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```python
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

---

## <a name="enable-otlp-exporter"></a>Aktivieren des OTLP-Exporters

Möglicherweise möchten Sie neben Azure Monitor Exporter auch den OTLP-Exporter (OpenTelemetry Protocol) aktivieren, um Ihre Telemetriedaten an zwei Speicherorte zu senden.

> [!NOTE]
> Der OTLP-Exporter wird nur als Beispiel gezeigt. Wir unterstützen offiziell weder den OTLP-Exporter noch irgendwelche Komponenten oder Angebote von Drittanbietern, die diesem nachgeschaltet sind. Wir empfehlen Ihnen, ein Issue in [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector) für OpenTelemetry-Probleme außerhalb des Azure-Supports zu öffnen.

#### <a name="net"></a>[.NET](#tab/net)

1. Installieren Sie das [OpenTelemetry.Exporter.OpenTelemetryProtocol](https://www.nuget.org/packages/OpenTelemetry.Exporter.OpenTelemetryProtocol/)-Paket zusammen mit [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) in Ihrem Projekt.

2. Fügen Sie den folgenden Codeausschnitt hinzu. In diesem Beispiel wird davon ausgegangen, dass Sie über einen OpenTelemetry-Collector mit einem aktuell ausgeführten OTLP-Empfänger verfügen. Weitere Informationen finden Sie [hier](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/examples/Console/TestOtlpExporter.cs) in diesem Beispiel.

```csharp
// sends data to Application Insights as well as OTLP
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>"
        })
        .AddOtlpExporter()
        .Build();
```


#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Installieren Sie das [OpenTelemetry Collector Exporter](https://www.npmjs.com/package/@opentelemetry/exporter-otlp-http)-Paket zusammen mit [Azure Monitor OpenTelemetry Exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter) in Ihrem Projekt.


```sh
    npm install @opentelemetry/exporter-otlp-http
    npm install @azure/monitor-opentelemetry-exporter
```

2. Fügen Sie den folgenden Codeausschnitt hinzu. In diesem Beispiel wird davon ausgegangen, dass Sie über einen OpenTelemetry-Collector mit einem aktuell ausgeführten OTLP-Empfänger verfügen. Weitere Informationen finden Sie [hier](https://github.com/open-telemetry/opentelemetry-js/tree/main/examples/otlp-exporter-node) in diesem Beispiel.

```typescript
const { BasicTracerProvider, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');

const provider = new BasicTracerProvider();
const azureMonitorExporter = new AzureMonitorTraceExporter({
  instrumentationKey: os.environ["APPLICATIONINSIGHTS_CONNECTION_STRING"]
});
const otlpExporter = new OTLPTraceExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(azureMonitorExporter));
provider.addSpanProcessor(new SimpleSpanProcessor(otlpExporter));
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

1. Installieren Sie die [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/)- und [opentelemetry-exporter-otlp](https://pypi.org/project/opentelemetry-exporter-otlp/)-Pakete.

2. Fügen Sie den folgenden Codeausschnitt hinzu. In diesem Beispiel wird davon ausgegangen, dass Sie über einen OpenTelemetry-Collector mit einem aktuell ausgeführten OTLP-Empfänger verfügen. Weitere Informationen finden Sie in dieser [INFODATEI](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry-exporter/samples/traces#collector).

```python
from opentelemetry import trace 

from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__) 

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
span_processor = BatchSpanProcessor(otlp_exporter) 
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("test"):
    print("Hello world!")
```

---

## <a name="troubleshooting"></a>Problembehandlung

### <a name="enable-diagnostic-logging"></a>Aktivieren der Diagnoseprotokollierung

#### <a name="net"></a>[.NET](#tab/net)

Azure Monitor Exporter verwendet EventSource für die eigene interne Protokollierung. Die Exporter-Protokolle sind für alle EventListener verfügbar, indem sie die Quelle namens „OpenTelemetry-AzureMonitor-Exporter“ abonnieren. Detaillierte Schritte zur Problembehandlung finden Sie unter [ OpenTelemetry-Problembehandlung](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/src/OpenTelemetry#troubleshooting).

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Azure Monitor Exporter verwendet die OpenTelemetry-API-Protokollierung für interne Protokolle. Diese kann mit dem folgenden Code aktiviert werden. 

```typescript
const { diag, DiagConsoleLogger, DiagLogLevel } = require("@opentelemetry/api");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
diag.setLogger(new DiagConsoleLogger(), DiagLogLevel.ALL);
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

Azure Monitor Exporter verwendet die Python-[Standardprotokollierungsbibliothek](https://docs.python.org/3/library/logging.html) für die eigene interne Protokollierung. OpenTelemetry-API- und Azure Monitor Exporter-Protokolle werden in der Regel im Schweregrad WARNUNG oder FEHLER für unregelmäßige Aktivitäten und INFO für reguläre/erfolgreiche Aktivitäten protokolliert. Die Python-Standardprotokollierungsbibliothek verwendet standardmäßig den Schweregrad WARNUNG. Daher müssen Sie den Schweregrad ändern, um Protokolle unter diesem Schweregrad anzeigen zu können. Im Folgenden finden Sie ein Beispiel für die Ausgabe von Protokollen mit ALLEN Schweregraden an die Konsole UND eine Datei.

```python
...
import logging

logging.basicConfig(format="%(asctime)s:%(levelname)s:%(message)s", level=logging.DEBUG)

logger = logging.getLogger(__name__)
file = logging.FileHandler("example.log")
stream = logging.StreamHandler()
logger.addHandler(file)
logger.addHandler(stream)
...

```

---

### <a name="known-issues"></a>Bekannte Probleme

Zu den bekannten Problemen bei Azure Monitor OpenTelemetry Exporter zählen: 

- Der Vorgangsname fehlt für Abhängigkeitstelemetrie, was sich negativ auf die Angaben auf der Registerkarte für Fehler und Leistung auswirken kann.
- Das Gerätemodell fehlt bei Anforderungs- und Abhängigkeitstelemetriedaten, was sich negativ auf die Gerätekohortenanalyse auswirkt.
- Der Name des Datenbankservers wird im Abhängigkeitsnamen ausgelassen, wodurch Tabellen mit demselben Namen auf verschiedenen Servern falsch aggregiert werden.

## <a name="support"></a>Support

- Lesen Sie die Schritte zur Problembehandlung in diesem Artikel.
- Öffnen Sie ein [Azure-Supportticket](https://azure.microsoft.com/support/create-ticket/), wenn Azure-Supportprobleme vorliegen.

### <a name="net"></a>[.NET](#tab/net)

Wenden Sie sich bei OpenTelemetry-Problemen direkt an die [OpenTelemetry .NET-Community](https://github.com/open-telemetry/opentelemetry-dotnet).

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Wenden Sie sich bei OpenTelemetry-Problemen direkt an die [OpenTelemetry JavaScript-Community](https://github.com/open-telemetry/opentelemetry-js).

### <a name="python"></a>[Python](#tab/python)

Wenden Sie sich bei OpenTelemetry-Problemen direkt an die [OpenTelemetry Python-Community](https://github.com/open-telemetry/opentelemetry-python).

---

## <a name="opentelemetry-feedback"></a>OpenTelemetry-Feedback

- Füllen Sie die [Kundenfeedbackumfrage](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform) der OpenTelemetry-Community aus.
- Erzählen Sie Microsoft ein wenig von sich, indem Sie unserer [OpenTelemetry Early Adopter-Community](https://aka.ms/AzMonOTel/) beitreten.
- Interagieren Sie mit anderen Azure Monitor-Benutzern in der [Technischen Community von Microsoft](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).

## <a name="next-steps"></a>Nächste Schritte

### <a name="net"></a>[.NET](#tab/net)

- Überprüfen Sie den Quellcode im [GitHub-Repository „Azure Monitor Exporter“](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter).
- Um das NuGet-Paket zu installieren, nach Updates zu suchen oder Versionshinweise anzuzeigen, besuchen Sie die Seite [NuGet-Paket „Azure Monitor Exporter“](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter/).
- Lernen Sie Azure Monitor Application Insights und OpenTelemetry mit der [Azure Monitor-Beispielanwendung](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Tracing.Customization) besser kennen.
- Weitere Informationen zu OpenTelemetry und seiner Community finden Sie im [OpenTelemetry .NET GitHub-Repository](https://github.com/open-telemetry/opentelemetry-dotnet).
- [Aktivieren Sie die Web-/Browserbenutzerüberwachung](javascript.md), um Nutzungsfunktionen zu aktivieren.


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Überprüfen Sie den Quellcode im [GitHub-Repository „Azure Monitor Exporter“](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter).
- Um das NPM-Paket zu installieren, nach Updates zu suchen oder Versionshinweise anzuzeigen, besuchen Sie die Seite [NPM-Paket „Azure Monitor Exporter“](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter).
- Lernen Sie Azure Monitor Application Insights und OpenTelemetry mit der [Azure Monitor-Beispielanwendung](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter/samples) besser kennen.
- Weitere Informationen zu OpenTelemetry und seiner Community finden Sie im [OpenTelemetry JavaScript GitHub-Repository](https://github.com/open-telemetry/opentelemetry-js).
- [Aktivieren Sie die Web-/Browserbenutzerüberwachung](javascript.md), um Nutzungsfunktionen zu aktivieren.

### <a name="python"></a>[Python](#tab/python)

- Überprüfen Sie den Quellcode im [GitHub-Repository „Azure Monitor Exporter“](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry-exporter/README.md).
- Um das PyPI-Paket zu installieren, nach Updates zu suchen oder Versionshinweise anzuzeigen, besuchen Sie die Seite [PyPI-Paket „Azure Monitor Exporter“](https://pypi.org/project/azure-monitor-opentelemetry-exporter/).
-  Lernen Sie Azure Monitor Application Insights und OpenTelemetry mit der [Azure Monitor-Beispielanwendung](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python) besser kennen.
- Weitere Informationen zu OpenTelemetry und seiner Community finden Sie im [OpenTelemetry Python GitHub-Repository](https://github.com/open-telemetry/opentelemetry-python).
- [Aktivieren Sie die Web-/Browserbenutzerüberwachung](javascript.md), um Nutzungsfunktionen zu aktivieren.

---
