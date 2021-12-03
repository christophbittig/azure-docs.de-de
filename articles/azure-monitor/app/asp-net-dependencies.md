---
title: Abhängigkeitsnachverfolgung in Azure Application Insights | Microsoft Docs
description: Überwachen Sie Abhängigkeitsaufrufe von Ihrer lokalen oder Microsoft Azure-Webanwendung mit Application Insights.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a2052b4b4d5822d583101d27e873bf19ceeca49c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486121"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Abhängigkeitsnachverfolgung in Azure Application Insights 

Eine *Abhängigkeit* ist eine Komponente, die von Ihrer Anwendung aufgerufen wird. In der Regel handelt es sich um einen Dienst, der über HTTP oder eine Datenbank oder ein Dateisystem aufgerufen wird. [Application Insights](./app-insights-overview.md) misst Sie die Dauer von Abhängigkeitsaufrufen, gibt an, ob sie durchgeführt werden können, und stellt zusätzliche Informationen wie den Namen der Abhängigkeit etc. bereit. Sie können bestimmte Abhängigkeitsaufrufe untersuchen und sie mit Anforderungen und Ausnahmen in Zusammenhang setzen.

## <a name="automatically-tracked-dependencies"></a>Automatisch nachverfolgte Abhängigkeiten

Application Insights-SDKs für .NET und .NET Core werden mit `DependencyTrackingTelemetryModule` geliefert – ein Telemetriemodul, das Abhängigkeiten automatisch erfasst. Diese Abhängigkeitserfassung ist für [ASP.NET](./asp-net.md)- und [ASP.NET Core](./asp-net-core.md)-Anwendungen automatisch aktiviert, wenn diese gemäß der verknüpften offiziellen Dokumentation konfiguriert werden. `DependencyTrackingTelemetryModule` ist im Lieferumfang [dieses](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-Pakets enthalten und wird bei Verwendung eines der NuGet-Pakete `Microsoft.ApplicationInsights.Web` oder `Microsoft.ApplicationInsights.AspNetCore` automatisch geladen.

 Folgende Abhängigkeiten werden von `DependencyTrackingTelemetryModule` derzeit automatisch verfolgt:

|Abhängigkeiten |Details|
|---------------|-------|
|Http/Https | Lokale oder Remote-HTTP/HTTPS-Aufrufe |
|WCF-Aufrufe| Wird nur dann automatisch nachverfolgt, wenn auf HTTP/HTTPS basierende Bindungen verwendet werden.|
|SQL | Aufrufe mit `SqlClient`. Informationen zum Erfassen von SQL-Abfragen finden Sie [hier](#advanced-sql-tracking-to-get-full-sql-query).  |
|[Azure-Speicher (Blob, Tabelle und Warteschlange)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Aufrufe mit Azure Storage-Client. |
|[EventHub-Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 und höher. |
|[ServiceBus-Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 und höher. |
|Azure Cosmos DB | Wird nur dann automatisch nachverfolgt, wenn HTTP/HTTPS verwendet wird. Der TCP-Modus wird von Application Insights nicht erfasst. |

Wenn eine Abhängigkeit fehlt oder ein anderes SDK verwendet wird, stellen Sie sicher, dass sie in der Liste [automatisch erfasster Abhängigkeiten](./auto-collect-dependencies.md) enthalten ist. Falls Ihre Abhängigkeit nicht automatisch erfasst wird, können Sie sie mit einem [TrackDependency-Aufruf](./api-custom-events-metrics.md#trackdependency) trotzdem manuell nachverfolgen.

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Einrichten einer automatischen Abhängigkeitsüberwachung in Konsolen-Apps

Um Abhängigkeiten in .NET-Konsolen-Apps automatisch nachzuverfolgen, installieren Sie das NuGet-Paket `Microsoft.ApplicationInsights.DependencyCollector`, und initialisieren Sie `DependencyTrackingTelemetryModule` wie folgt:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Für .NET Core-Konsolen-Apps wird „TelemetryConfiguration.Active“ nicht mehr verwendet. Weitere Informationen finden Sie in der [Dokumentation zum Workerdienst](./worker-service.md) und in der [Dokumentation zur ASP.NET Core-Überwachung](./asp-net-core.md)

### <a name="how-automatic-dependency-monitoring-works"></a>Funktionsweise der automatischen Abhängigkeitsüberwachung

Abhängigkeiten werden über eine der folgenden Methoden automatisch gesammelt:

* Über die Bytecodeinstrumentierung um ausgewählte Methoden (InstrumentationEngine entweder aus StatusMonitor oder der Azure-Web-App-Erweiterung)
* EventSource-Rückrufe
* DiagnosticSource-Rückrufe (in den neuesten.NET-/.NET Core-SDKs)

## <a name="manually-tracking-dependencies"></a>Manuelle Nachverfolgung von Abhängigkeiten

Es folgen einige Beispiele für Abhängigkeiten, die nicht automatisch erfasst werden und daher eine manuelle Nachverfolgung erfordern.

* Azure Cosmos DB wird nur dann automatisch nachverfolgt, wenn [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) verwendet wird. Der TCP-Modus wird von Application Insights nicht erfasst.
* Redis

Abhängigkeiten, die nicht automatisch vom SDK erfasst werden, können Sie manuell mithilfe der [TrackDependency-API](api-custom-events-metrics.md#trackdependency) nachverfolgen, die von den Standardmodulen zur automatischen Erfassung verwendet wird.

Beispiel: Wenn Sie Ihren Code mit einer Assembly erstellen, die Sie nicht selbst geschrieben haben, könnten Sie die Zeit aller Aufrufe ermitteln, um herauszufinden, welchen Beitrag sie an Ihren Reaktionszeiten hat. Um diese Daten in den Abhängigkeitsdiagrammen in Application Insights anzuzeigen, senden Sie sie mit `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternativ stellt `TelemetryClient` die Erweiterungsmethoden `StartOperation` und `StopOperation` bereit, die zum manuellen Nachverfolgen von Abhängigkeiten verwendet werden können, wie [hier](custom-operations-tracking.md#outgoing-dependencies-tracking) gezeigt.

Wenn Sie das Standardmodul für die Nachverfolgung von Abhängigkeiten deaktivieren möchten, entfernen Sie für ASP.NET-Anwendungen den Verweis auf DependencyTrackingTelemetryModule in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Befolgen Sie für ASP.NET Core-Anwendungen die [hier](asp-net-core.md#configuring-or-removing-default-telemetrymodules) aufgeführten Anweisungen.

## <a name="tracking-ajax-calls-from-web-pages"></a>Nachverfolgen von AJAX-Aufrufen von Webseiten

Für Webseiten erfasst das JavaScript SDK von Application Insights AJAX-Aufrufe automatisch als Abhängigkeiten.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Erweiterte SQL-Nachverfolgung zum Abrufen vollständiger SQL-Abfragen

> [!NOTE]
> Azure Functions erfordert separate Einstellungen zum Aktivieren der SQL-Textsammlung. Legen Sie dazu in [host.json](../../azure-functions/functions-host-json.md#applicationinsights) die Einstellungen `"EnableDependencyTracking": true,` und `"DependencyTrackingOptions": { "enableSqlCommandTextInstrumentation": true }` in `applicationInsights` fest.

Für SQL-Aufrufe wird der Name des Servers und der Datenbank immer erfasst und als Name der erfassten `DependencyTelemetry` gespeichert. Es gibt ein zusätzliches Feld namens „data“, das den vollständigen Text der SQL-Abfrage enthalten kann.

Bei ASP.NET Core-Anwendungen ist es jetzt erforderlich, die SQL-Textsammlung mithilfe von folgender Option zu abonnieren
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

Für ASP.NET-Anwendungen wird der vollständige SQL-Abfragetext mithilfe der Bytecodeinstrumentierung erfasst, für die eine Instrumentierungs-Engine verwendet werden muss, oder indem das NuGet-Paket [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) anstelle der System.Data.SqlClient-Bibliothek verwendet wird. Die plattformspezifischen Schritte zum Aktivieren der vollständigen SQL-Abfragesammlung werden im Folgenden beschrieben:

| Plattform | Erforderliche Schritte zum Abrufen der vollständigen SQL-Abfrage |
| --- | --- |
| Azure-Web-App |In der Systemsteuerung Ihrer Web-App [öffnen Sie das Application Insights-Blatt](../../azure-monitor/app/azure-web-apps.md), und aktivieren Sie SQL-Befehle unter .NET. |
| IIS-Server (Azure-VM, lokal usw.) | Verwenden Sie entweder das NuGet-Paket [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) oder das Statusmonitor-PowerShell-Modul, um die [Instrumentierungs-Engine zu installieren](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) und IIS neu zu starten. |
| Azure Cloud Service | Hinzufügen der [Starttask zum Installieren des Statusmonitors](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Ihre App sollte durch die Installation der NuGet-Pakete für [ASP.NET](./asp-net.md)- oder [ASP.NET Core-Anwendungen](./asp-net-core.md) zur Buildzeit in das ApplicationInsights-SDK integriert werden |
| IIS Express | Verwenden Sie das NuGet-Paket [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient).
| Azure-Webaufträge | Verwenden Sie das NuGet-Paket [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient).

Zusätzlich zu den oben genannten plattformspezifischen Schritten ist eine **explizite Aktivierung erforderlich, um die SQL-Befehlssammlung zu aktivieren**, indem Sie die Datei „applicationInsights.config“ wie folgt ändern:

```xml
<TelemetryModules>
  <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
    <EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
  </Add>
```

In den oben genannten Fällen können Sie die ordnungsgemäße Installation der Instrumentierungs-Engine überprüfen, indem Sie sicherstellen, dass die SDK-Version der erfassten `DependencyTelemetry` „rddp“ lautet. „rdddsd“ oder „rddf“ weisen darauf hin, dass Abhängigkeiten über DiagnosticSource- oder EventSource-Rückrufe gesammelt werden und die vollständige SQL-Abfrage daher nicht erfasst wird.

## <a name="where-to-find-dependency-data"></a>Hier finden Sie Abhängigkeitsdaten

* [Anwendungszuordnung](app-map.md) visualisiert Abhängigkeiten zwischen Ihrer App und angrenzenden Komponenten.
* Die [Transaktionsdiagnose](transaction-diagnostics.md) zeigt einheitliche, korrelierte Serverdaten.
* [Die Registerkarte „Browser“](javascript.md) enthält AJAX-Aufrufe von Browsern Ihrer Benutzer.
* Navigieren Sie zu langsamen oder fehlgeschlagenen Aufrufen, um ihre Abhängigkeitsaufrufe zu überprüfen.
* [Analyse](#logs-analytics) kann verwendet werden, um Abhängigkeitsdaten abzufragen.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Diagnostizieren langsamer Anforderungen

Jedes Anforderungsereignis bezieht sich auf Abhängigkeitsaufrufe, Ausnahmen und andere Ereignisse, die nachverfolgt werden, während Ihre App die Anforderung verarbeitet. Wenn einige Anforderungen also eine schlechte Leistung zeigen, können Sie herausfinden, ob es an langsamen Antworten einer Abhängigkeit liegt.

### <a name="tracing-from-requests-to-dependencies"></a>Ablaufverfolgung von Anforderungen bis Abhängigkeiten

Öffnen Sie die Registerkarte **Leistung**, und navigieren Sie zur Registerkarte **Abhängigkeiten** im oberen Bereich (neben „Vorgänge“).

Klicken Sie unter „Gesamt“ auf einen **Abhängigkeitsnamen**. Nach der Auswahl einer Abhängigkeit wird rechts ein Diagramm mit der Verteilung der Dauer für diese Abhängigkeit angezeigt.

![Klicken Sie auf der Registerkarte „Leistung“ im oberen Bereich auf die Registerkarte „Abhängigkeiten“ und dann im Diagramm auf den Namen einer Abhängigkeit](./media/asp-net-dependencies/2-perf-dependencies.png)

Klicken Sie unten rechts auf die blaue Schaltfläche **Stichproben**, und klicken Sie dann auf eine Stichprobe, um die End-to-End-Transaktionsdetails anzuzeigen.

![Klicken auf eine Stichprobe, um die End-to-End-Transaktionsdetails anzuzeigen](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Erstellen eines Profils Ihrer Livewebsite

Sie möchten wissen, was am längsten gedauert hat? Der [Application Insights-Profiler](../../azure-monitor/app/profiler.md) verfolgt HTTP-Aufrufe zu Ihrer Livewebsite zurück und zeigt an, welche Funktionen im Code die meiste Zeit in Anspruch genommen haben.

## <a name="failed-requests"></a>Anforderungsfehler

Anforderungsfehler können auch fehlgeschlagenen Aufrufen von Abhängigkeiten zugeordnet werden.

Sie können links zur Registerkarte **Fehler** navigieren und dann im oberen Bereich auf die Registerkarte **Abhängigkeiten** klicken.

![Klicken Sie auf das Diagramm mit Anforderungsfehlern](./media/asp-net-dependencies/4-fail.png)

Hier können Sie die Fehler bei der Anzahl der Abhängigkeiten anzeigen. Um weitere Details zu einem Fehler abzurufen, versuchen Sie, in der Tabelle im unteren Bereich auf den Namen einer Abhängigkeit zu klicken. Sie können unten rechts auf die blaue Schaltfläche **Abhängigkeiten** klicken, um die End-to-End-Transaktionsdetails abzurufen.

## <a name="logs-analytics"></a>Protokolle (Analytics)

Sie können Abhängigkeiten in der [Abfragesprache Kusto](/azure/kusto/query/) verfolgen. Hier einige Beispiele.

* Suchen fehlgeschlagener Abhängigkeitsaufrufe:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Suchen von AJAX-Aufrufen:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Suchen von mit Anforderungen verbundenen Abhängigkeitsaufrufen:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Suchen von mit Seitenaufrufen verbundenen AJAX-Aufrufen:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Wie meldet die automatische Abhängigkeitserfassung fehlerhafte Aufrufe an Abhängigkeiten?*

* Bei fehlerhaften Abhängigkeitsaufrufen wird das Feld „success“ auf FALSE festgelegt. `DependencyTrackingTelemetryModule` gibt keine Auskunft über `ExceptionTelemetry`. Das vollständige Datenmodell für Abhängigkeiten wird [hier](data-model-dependency-telemetry.md) beschrieben.

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Wie berechne ich die Erfassungslatenz für meine Abhängigkeitstelemetrie?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Wie ermittle ich die Uhrzeit, zu der der Abhängigkeitsaufruf initiiert wurde?*

In der Log Analytics-Abfrageansicht stellt `timestamp` den Moment dar, in dem der TrackDependency()-Aufruf initiiert wurde. Dies erfolgt sofort nach dem Empfang der Antwort auf den Abhängigkeitsaufruf. Um die Uhrzeit des Beginns des Abhängigkeitsaufrufs zu berechnen, subtrahieren Sie den aufgezeichneten `timestamp`-Wert des Abhängigkeitsaufrufs von `duration`.

## <a name="open-source-sdk"></a>Open Source SDK
Wie jedes Application Insights-SDK ist auch das Modul zur Abhängigkeitserfassung ein Open Source-Modul. Lesen Sie den Code, tragen Sie zum Code bei, oder melden Sie Probleme im [offiziellen GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nächste Schritte

* [Ausnahmen](./asp-net-exceptions.md)
* [Daten zu Seiten und Benutzern](./javascript.md)
* [Verfügbarkeit](./monitor-web-app-availability.md)

