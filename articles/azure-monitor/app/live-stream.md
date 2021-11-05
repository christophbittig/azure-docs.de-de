---
title: Diagnose mit Live Metrics Stream – Azure Application Insights
description: Überwachen Sie Ihre Web-App mit benutzerdefinierten Metriken in Echtzeit, und diagnostizieren Sie Probleme mit einem Livefeed zu Fehlern, Ablaufverfolgungen und Ereignissen.
ms.topic: conceptual
ms.date: 10/12/2021
ms.reviewer: sdash
ms.openlocfilehash: 7c587cc9bdfa4fdc0483a99d9248fbd986c048e8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026239"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Überwachung und Diagnose mit einer Latenzzeit von 1 Sekunde

Überwachen Sie Ihre Livewebanwendung in der Produktion mithilfe von Live Metrics Stream (auch QuickPulse) in [Application Insights](./app-insights-overview.md). Wählen und filtern Sie Metriken und Leistungsindikatoren für die Überwachung in Echtzeit, ohne dass der Dienst beeinträchtigt wird. Überprüfen Sie Stapelüberwachungen von fehlerhaften Anforderungen und Ausnahmen. In Kombination mit [Profiler](./profiler.md) und dem [Momentaufnahmedebugger](./snapshot-debugger.md) bietet Live Metrics Stream ein leistungsfähiges und nicht invasives Diagnosetool für Ihre Livewebsite.

Mit Live Metrics Stream haben Sie folgende Möglichkeiten:

* Überprüfen Sie eine veröffentlichte Korrektur durch Überwachen von Leistungs- und Fehlerindikatoren.
* Überwachen Sie die Auswirkungen von Testauslastungen, und diagnostizieren Sie Probleme im laufenden Betrieb.
* Konzentrieren Sie sich auf bestimmte Testsitzungen, oder filtern Sie bekannte Probleme heraus, indem Sie die zu überwachenden Metriken auswählen und filtern.
* Rufen Sie Ausnahmeablaufverfolgungen in Echtzeit ab.
* Experimentieren Sie mit Filtern, um die wichtigsten KPIs zu ermitteln.
* Überwachen Sie Windows-Leistungsindikatoren live.
* Identifizieren Sie mühelos einen Server mit Problemen, und filtern Sie nach allen KPIs/dem Livefeed nur für diesen Server.

![Registerkarte „Livemetriken“](./media/live-stream/live-metric.png)

Livemetriken werden derzeit für ASP.NET-, ASP.NET Core-, Azure Functions-, Java- und Node.js-Apps unterstützt.

## <a name="get-started"></a>Erste Schritte

1. Befolgen Sie die sprachspezifischen Richtlinien zum Aktivieren von Livemetriken.
   * [ASP.NET](./asp-net.md): Livemetriken sind standardmäßig aktiviert.
   * [ASP.NET Core](./asp-net-core.md): Livemetriken sind standardmäßig aktiviert.
   * [.NET-/.NET Core-Konsole bzw. -Worker](./worker-service.md): Livemetriken sind standardmäßig aktiviert.
   * [.NET-Anwendungen](#enable-livemetrics-using-code-for-any-net-application): Aktivieren Sie diese mithilfe von Code.
    * [Java](./java-in-process-agent.md): Livemetriken sind standardmäßig aktiviert.
   * [Node.js](./nodejs.md#live-metrics)

2. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Application Insights-Ressource für Ihre App und anschließend Live Stream.

3. [Sichern Sie den Steuerkanal](#secure-the-control-channel), wenn Sie sensible Daten (z.B. Kundennamen) in Ihren Filtern verwenden möchten.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Aktivieren von Livemetriken für beliebige .NET-Anwendungen mithilfe von Code

Obwohl Livemetriken standardmäßig aktiviert sind, wenn das Onboarding anhand der empfohlenen Anweisungen für .NET-Anweisungen erfolgt, wird im Folgenden veranschaulicht, wie Sie diese manuell einrichten.

1. Installieren Sie das NuGet-Paket [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector).
2. Im Folgenden finden Sie Beispielcode für die Konsolen-App zum Einrichten von Livemetriken.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Obwohl das obige Beispiel für eine Konsolen-App vorgesehen ist, kann derselbe Code in allen .NET-Anwendungen verwendet werden. Wenn weitere Telemetriemodule aktiviert sind, die automatisch Telemetriedaten erfassen, müssen Sie dafür sorgen, dass die Konfiguration für die Initialisierung dieser Module auch für Livemetrikmodule verwendet wird.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Wie unterscheidet sich Live Metrics Stream von Metrik-Explorer und Analytics?

| |Live Stream | Metrik-Explorer und Analytics |
|---|---|---|
|**Latenz**|Daten werden innerhalb einer Sekunde angezeigt.|Aggregation innerhalb mehrerer Minuten|
|**Keine Beibehaltung**|Daten werden beibehalten, solange sie im Diagramm angezeigt werden, und dann verworfen.|[Daten werden 90 Tage lang beibehalten.](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Bedarfsgesteuert**|Daten werden nur gestreamt, während der Bereich „Livemetriken“ geöffnet ist. |Daten werden gesendet, sobald das SDK installiert und aktiviert wird.|
|**Free**|Keine Gebühren für Live Stream-Daten|[Gebührenpflichtig](./pricing.md)
|**Stichproben**|Alle ausgewählten Metriken und Indikatoren werden übertragen. Für Fehler und Stapelüberwachungen werden Stichproben erstellt. |Für Ereignisse können [Stichproben](./api-filtering-sampling.md) erstellt werden.|
|**Steuerkanal**|Filtersteuersignale werden an das SDK gesendet. Es empfiehlt sich, diesen Kanal zu sichern.|Unidirektionale Kommunikation zum Portal|

## <a name="select-and-filter-your-metrics"></a>Auswählen und Filtern der Metriken

(Verfügbar mit ASP.NET, ASP.NET Core und Azure Functions (v2).)

Sie können benutzerdefinierte KPIs live überwachen, indem Sie im Portal beliebige Filter auf Application Insights-Telemetrie anwenden. Klicken Sie auf das Filtersteuerelement, das angezeigt wird, wenn Sie den Mauszeiger über eines der Diagramme bewegen. Das folgende Diagramm stellt eine benutzerdefinierte KPI für die Anforderungsanzahl mit Filtern für die Attribute „URL“ und „Dauer“ dar. Überprüfen Sie Ihre Filter in der Streamvorschau, die jederzeit einen Livefeed von Telemetriedaten anzeigt, die den von Ihnen angegeben Kriterien entsprechen.

![Filter Anforderungsrate](./media/live-stream/filter-request.png)

Sie können einen anderen Wert als die Anzahl überwachen. Die Optionen hängen von der Typ des Streams ab. Bei diesem kann es sich um eine beliebige Application Insights-Telemetrie handeln: Anforderungen, Abhängigkeiten, Ausnahmen, Ablaufverfolgungen, Ereignisse oder Metriken. Sie können auch eine eigene [benutzerdefinierte Messung](./api-custom-events-metrics.md#properties) verwenden:

![Abfrage-Generator für Anforderungsrate mit benutzerdefinierter Metrik](./media/live-stream/query-builder-request.png)

Neben Application Insights-Telemetrie können Sie auch einen beliebigen Windows-Leistungsindikator überwachen, indem Sie diese Streamoptionen auswählen und den Namen des Leistungsindikators angeben.

Livemetriken werden an zwei Punkten aggregiert: lokal auf jedem Server und übergreifend auf allen Servern. Sie können die Standardeinstellung durch die Auswahl anderer Optionen in der entsprechenden Dropdownliste ändern.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Beispieltelemetrie: Benutzerdefinierte Livediagnoseereignisse
Der Livefeed von Ereignissen zeigt standardmäßig Beispiele für fehlgeschlagene Anforderungen und Abhängigkeitsaufrufe, Ausnahmen, Ereignisse und Ablaufverfolgungen an. Klicken Sie jederzeit auf das Filtersymbol, um die angewendeten Kriterien anzuzeigen.

![Filterschaltfläche](./media/live-stream/filter.png)

Wie bei Metriken können Sie beliebige Kriterien für alle Application Insights-Telemetrietypen angeben. In diesem Beispiel wählen Sie bestimmte Anforderungsfehler und Ereignisse aus.

![Abfrage-Generator](./media/live-stream/query-builder.png)

> [!NOTE]
> Aktuell muss für meldungsbasierte Ausnahmekriterien die Meldung für äußere Ausnahmen verwendet werden. Verwenden Sie im vorangehenden Beispiel zum Herausfiltern mit der Meldung für innere Ausnahmen (folgt auf das Trennzeichen „<--“) „Der Client wurde getrennt.“ eine Meldung, die keine Kriterien vom Typ „Fehler beim Lesen des Anforderungsinhalts“ enthält.

Klicken Sie auf ein Element im Livefeed, um die Details dazu anzuzeigen. Sie können den Feed anhalten, indem Sie auf **Anhalten** klicken, einfach nach unten scrollen oder auf ein Element klicken. Der Livefeed wird fortgesetzt, wenn Sie wieder nach oben scrollen oder auf den Zähler der Elemente klicken, die im angehaltenen Zustand erfasst wurden.

![Screenshot des Fensters „Beispieltelemetrie“ mit einer ausgewählten Ausnahme und im unteren Teil des Fensters angezeigten Details zur Ausnahme.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtern nach Serverinstanz

Wenn Sie eine bestimmte Serverrolleninstanz überwachen möchten, können Sie nach Server filtern. Klicken Sie zum Filtern unter *Server* auf den gewünschten Servernamen.

![Auswahl der Live-Fehler](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Sichern des Steuerkanals

> [!NOTE]
> Derzeit können Sie einen authentifizierten Kanal nur über die manuelle Instrumentierung (SDK) einrichten und Server nicht über die Azure-Dienstintegration (oder automatische Instrumentierung) authentifizieren.

Die von Ihnen im Portal für Livemetriken angegebenen benutzerdefinierten Filterkriterien werden an die Livemetrikkomponente des Application Insights SDK zurückgesendet. Der Filter können potenziell vertrauliche Informationen wie z.B. Kunden-IDs enthalten. Zum Sichern des Kanals können Sie neben dem Instrumentierungsschlüssel auch einen geheimen API-Schlüssel verwenden.

### <a name="create-an-api-key"></a>Erstellen eines API-Schlüssels

![API-Schlüssel > API-Schlüssel erstellen](./media/live-stream/api-key.png)
![Registerkarte „API-Schlüssel erstellen“ Aktivieren von „SDK-Steuerungskanal authentifizieren“ und anschließendes Klicken auf „Schlüssel generieren“](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Hinzufügen eines API-Schlüssels zur Konfiguration

### <a name="aspnet"></a>ASP.NET

Fügen Sie den API-Authentifizierungsschlüssel in der Datei „applicationinsights.config“ dem QuickPulseTelemetryModule-Element hinzu:

```xml
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Befolgen Sie für [ASP.NET Core-Anwendungen](./asp-net-core.md) die hier folgenden Anweisungen.

Ändern Sie die `ConfigureServices`-Methode in der Datei „Startup.cs“ wie folgt:

Fügen Sie den folgenden Namespace hinzu.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ändern Sie dann die `ConfigureServices`-Methode wie unten beschrieben.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Weitere Informationen zum Konfigurieren von ASP.NET Core-Anwendungen finden Sie im Leitfaden zum [Konfigurieren von Telemetriemodulen in ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

Befolgen Sie für [WorkerService-Anwendungen](./worker-service.md) die hier folgenden Anweisungen.

Fügen Sie den folgenden Namespace hinzu.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Fügen Sie als Nächstes die folgende Zeile vor dem Aufruf von `services.AddApplicationInsightsTelemetryWorkerService` hinzu.

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Weitere Informationen zum Konfigurieren von WorkerService-Anwendungen finden Sie im Leitfaden zum [Konfigurieren von Telemetriemodulen in WorkerService-Anwendungen](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>Azure Function-Apps

Für Azure-Funktions-Apps (v2) kann der Kanal über eine Umgebungsvariable mit einem API-Schlüssel abgesichert werden.

Erstellen Sie einen API-Schlüssel in Ihrer Application Insights-Ressource, und wechseln Sie zu **Einstellungen > Konfiguration** für Ihre Funktions-App. Wählen Sie **Neue Anwendungseinstellung** aus, und geben Sie den Namen `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` und einen Wert entsprechend Ihrem API-Schlüssel ein.

Wenn Sie jedoch alle verbundenen Server erkennen und ihnen vertrauen, können Sie die benutzerdefinierte Filter ohne den authentifizierten Kanal ausprobieren. Diese Option steht sechs Monate lang zur Verfügung. Diese Überschreibung muss einmal für jede neue Sitzung, oder wenn ein neuer Server online geschaltet wird, durchgeführt werden.

![Authentifizierungsoptionen für Livemetriken](./media/live-stream/live-stream-auth.png)

> [!NOTE]
> Es wird dringend empfohlen, dass Sie den authentifizierten Kanal vor dem Eingeben von potenziell vertraulichen Informationen wie der Kunden-ID in den Filterkriterien einrichten.
>

## <a name="supported-features-table"></a>Tabelle unterstützter Features

| Sprache                         | Grundlegende Metriken       | Leistungsmetriken | Benutzerdefinierte Filterung    | Beispieltelemetrie    | CPU-Aufteilung nach Prozessen |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Unterstützt (V2.7.2+) | Unterstützt (V2.7.2+) | Unterstützt (V2.7.2+) | Unterstützt (V2.7.2+) | Unterstützt (V2.7.2+)  |
| .NET Core (target=.NET Framework)| Unterstützt (V2.4.1+) | Unterstützt (V2.4.1+) | Unterstützt (V2.4.1+) | Unterstützt (V2.4.1+) | Unterstützt (V2.4.1+)  |
| .NET Core (target=.NET Core)     | Unterstützt (V2.4.1+) | Unterstützt*          | Unterstützt (V2.4.1+) | Unterstützt (V2.4.1+) | **Nicht unterstützt**    |
| Azure Functions v2               | Unterstützt           | Unterstützt           | Unterstützt           | Unterstützt           | **Nicht unterstützt**    |
| Java                             | Unterstützt (V2.0.0+) | Unterstützt (V2.0.0+) | **Nicht unterstützt**   | Unterstützt (V3.2.0 und höher) | **Nicht unterstützt**    |
| Node.js                          | Unterstützt (V1.3.0+) | Unterstützt (V1.3.0+) | **Nicht unterstützt**   | Unterstützt (V1.3.0+) | **Nicht unterstützt**    |

Grundlegende Metriken umfassen Anforderung, Abhängigkeit und Ausnahmerate. Leistungsmetriken (Leistungsindikatoren) umfassen Arbeitsspeicher und CPU. Beispieltelemetriedaten zeigen einen Datenstrom ausführlicher Informationen zu nicht erfolgreichen Anforderungen und Abhängigkeiten, Ausnahmen, Ereignissen und Ablaufverfolgungen.

 \* PerfCounters-Unterstützung unterscheidet sich geringfügig von .NET Core-Versionen, die nicht für .NET Framework bestimmt sind:

- PerfCounters-Metriken werden unterstützt, wenn sie in Azure App Service für Windows ausgeführt werden. (AspNetCore SDK Version 2.4.1 oder höher)
- PerfCounters werden unterstützt, wenn die App auf einem BELIEBIGEN Windows-Computer (VM oder Clouddienst oder lokal usw.) ausgeführt wird (AspNetCore SDK Version 2.7.1 oder höher), aber für Apps, die für .NET Core 2.0 oder höher bestimmt sind.
- PerfCounters werden unterstützt, wenn die App auf einem beliebigen Computer (Linux, Windows, App Service für Linux, Container usw.) in der aktuellen Version (z. B. ASP.NET Core SDK 2.8.0 oder höher) ausgeführt wird, sofern diese mit .NET Core 2.0 oder höher kompatibel ist.

## <a name="troubleshooting"></a>Problembehandlung

Live Metrics Stream verwendet andere IP-Adressen als sonstige Application Insights-Telemetriedaten. Stellen Sie sicher, dass [diese IP-Adressen](./ip-addresses.md) in Ihrer Firewall geöffnet sind. Überprüfen Sie auch, ob die [ausgehenden Ports für Live Metrics Stream](./ip-addresses.md#outgoing-ports) in der Firewall Ihrer Server geöffnet sind.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen der Verwendung mit Application Insights](./usage-overview.md)
* [Verwenden der Diagnosesuche](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Snapshot Debugger](./snapshot-debugger.md)
