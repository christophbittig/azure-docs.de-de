---
title: Überwachen von Python-Anwendungen mit Azure Monitor | Microsoft-Dokumentation
description: Enthält eine Anleitung zum Verknüpfen von OpenCensus Python mit Azure Monitor.
ms.topic: conceptual
ms.date: 10/12/2021
ms.reviewer: mbullwin
ms.custom: devx-track-python
author: lzchen
ms.author: lechen
ms.openlocfilehash: 12c455eb2cd3114b043e19ddd37ca3497143914b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244979"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Einrichten von Azure Monitor für Ihre Python-Anwendung

Azure Monitor unterstützt verteilte Ablaufverfolgung, Metrikerfassung und Protokollierung für Python-Anwendungen.

Die von Microsoft unterstützte Lösung zum Nachverfolgen und Exportieren von Daten für Ihre Python-Anwendungen ist das [OpenCensus Python SDK](#introducing-opencensus-python-sdk) über die [Azure Monitor-Exportprogramme](#instrument-with-opencensus-python-sdk-with-azure-monitor-exporters).

Alle anderen Telemetrie-SDKs für Python werden NICHT UNTERSTÜTZT und von Microsoft nicht als Telemetrielösung empfohlen.

Sie haben vielleicht bemerkt, dass OpenCensus mit [OpenTelemetry](https://opentelemetry.io/) zusammengeführt wird. Wir empfehlen jedoch weiterhin OpenCensus, während OpenTelemetry allmählich ausreift.

> [!NOTE]
> Eine Vorschau zum [OpenTelemetry-basierten Python-Angebot](opentelemetry-enable.md?tabs=python) ist verfügbar. [Weitere Informationen](opentelemetry-overview.md)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Python-Installation. In diesem Artikel wird [Python 3.7.0](https://www.python.org/downloads/release/python-370/) verwendet, doch können wahrscheinlich auch andere Versionen mit kleineren Änderungen verwendet werden. Das OpenCensus Python SDK unterstützt nur Python v2.7 und v3.4 oder höher.
- Erstellen Sie eine Application Insights-[Ressource](./create-new-resource.md). Ihnen wird ein eigener Instrumentierungsschlüssel (iKey) für Ihre Ressource zugewiesen.

## <a name="introducing-opencensus-python-sdk"></a>Einführung in das OpenCensus Python SDK

[OpenCensus ist](https://opencensus.io) eine Reihe von Open-Source-Bibliotheken, die die Erfassung von verteilter Ablaufverfolgung, Metriken und Protokollierungtelemetrie ermöglichen. Durch die Verwendung von [Azure Monitor-Exportprogrammen](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) können Sie diese erfassten Telemetriedaten an Application Insights senden. In diesem Artikel werden der Prozess zur Einrichtung von OpenCensus und Azure Monitor-Exportprogrammen für Python und das Senden Ihrer Überwachungsdaten an Azure Monitor beschrieben.

## <a name="instrument-with-opencensus-python-sdk-with-azure-monitor-exporters"></a>Instrumentieren mit dem OpenCensus Python SDK mit Azure Monitor-Exportprogrammen

Installieren Sie OpenCensus Azure Monitor Exporters:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> Beim Befehl `python -m pip install opencensus-ext-azure` wird davon ausgegangen, dass Sie eine `PATH`-Umgebungsvariable für die Python-Installation festgelegt haben. Wenn Sie diese Variable nicht konfiguriert haben, müssen Sie den vollständigen Verzeichnispfad zum Speicherort Ihrer ausführbaren Python-Datei angeben. Das Ergebnis ist ein Befehl, der Folgendem ähnelt: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`

Das SDK verwendet drei Azure Monitor-Exportprogramme, um unterschiedliche Arten von Telemetriedaten an Azure Monitor zu senden. Dies sind Ablaufverfolgung, Metriken und Protokolle. Weitere Informationen zu diesen Telemetrietypen finden Sie in der [Übersicht über die Datenplattform](../data-platform.md). Befolgen Sie die nachstehenden Anweisungen, um diese Telemetrietypen über die drei Exportprogramme zu senden.

## <a name="telemetry-type-mappings"></a>Zuordnungen von Telemetriedatentypen

Im Folgenden finden Sie die Zuordnungen der von OpenCensus bereitgestellten Exportprogramme zu den in Azure Monitor angezeigten Typen von Telemetriedaten.

| Basis für Einblicke | Telemetriedatentyp in Azure Monitor    | Erklärung                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Protokolle                    | traces, exceptions, customEvents   | Protokolltelemetrie, Ausnahmentelemetrie, Ereignistelemetrie |
| Metriken                 | customMetrics, performanceCounters | Benutzerdefinierte Metriken, Leistungsindikatoren                |
| Ablaufverfolgung                 | requests, dependencies             | Eingehende Anforderungen, ausgehende Anforderungen                |

### <a name="logs"></a>Protokolle

1. Zuerst generieren wir einige lokale Protokolldaten.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Der Code fordert fortlaufend zur Eingabe eines Werts auf. Es wird ein Protokolleintrag für jeden eingegebenen Wert ausgegeben.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Das Eingeben von Werten ist zu Demonstrationszwecken zwar hilfreich, wir möchten jedoch eigentlich die Protokolldaten für Azure Monitor ausgeben. Übergeben Sie die Verbindungszeichenfolge direkt an das Exportprogramm. Sie können sie auch in einer Umgebungsvariablen (`APPLICATIONINSIGHTS_CONNECTION_STRING`) angeben. Ändern Sie Ihren Code aus dem vorherigen Schritt basierend auf dem folgenden Codebeispiel:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Das Exportprogramm sendet Protokolldaten an Azure Monitor. Sie finden die Daten unter `traces`. 

    > [!NOTE]
    > In diesem Kontext ist `traces` nicht mit `tracing` identisch. Hier bezieht sich `traces` auf den Typ der Telemetrie, der bei der Verwendung von `AzureLogHandler` in Azure Monitor angezeigt wird. `tracing` bezieht sich jedoch auf ein Konzept in OpenCensus und betrifft die [verteilte Ablaufverfolgung](./distributed-tracing.md).

    > [!NOTE]
    > Die Stammprotokollierung wird mit der Stufe „Warnung“ konfiguriert. Dies bedeutet, dass alle von Ihnen gesendeten Protokolle mit einem niedrigeren Schweregrad ignoriert und daher nicht an Azure Monitor gesendet werden. Weitere Informationen finden Sie in der [Dokumentation](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Sie können Ihren Protokollnachrichten auch benutzerdefinierte Eigenschaften im Schlüsselwortargument *extra* hinzufügen, indem Sie das Feld „custom_dimensions“ verwenden. Diese Eigenschaften werden als Schlüssel-Wert-Paare in `customDimensions` in Azure Monitor angezeigt.
    > [!NOTE]
    > Damit dieses Feature funktioniert, müssen Sie im Feld „custom_dimensions“ ein Wörterbuch übergeben. Wenn Sie Argumente eines anderen Typs übergeben, werden diese von der Protokollierung ignoriert.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

> [!NOTE]
> Im Rahmen der Verwendung der Application Insights-Instrumentierung sammeln und senden wir Diagnosedaten an Microsoft. Diese Daten helfen uns, Application Insights auszuführen und zu verbessern. Sie haben die Möglichkeit, die Sammlung nicht wesentlicher Daten zu deaktivieren. [Weitere Informationen](./statsbeat.md).

#### <a name="configure-logging-for-django-applications"></a>Konfigurieren der Protokollierung für Django-Anwendungen

Sie können die Protokollierung wie oben beschrieben explizit im Anwendungscode für Ihre Django-Anwendungen konfigurieren, oder Sie können dies in der Protokollierungskonfiguration von Django angeben. Dieser Code kann in jede Datei eingefügt werden, die Sie für die Konfiguration von Django-Einstellungen verwenden. Informationen zum Konfigurieren von Django-Einstellungen finden Sie unter [Django-Einstellungen](https://docs.djangoproject.com/en/3.0/topics/settings/). Weitere Informationen zum Konfigurieren der Protokollierung finden Sie unter [Django-Protokollierung](https://docs.djangoproject.com/en/3.0/topics/logging/).

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Stellen Sie sicher, dass Sie die Protokollierung mit demselben Namen verwenden, der in der Konfiguration angegeben ist.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Senden von Ausnahmen

Telemetriedaten vom Typ `exception` werden von OpenCensus Python nicht automatisch nachverfolgt und gesendet. Sie werden per `AzureLogHandler` unter Verwendung von Ausnahmen über die Python-Protokollierungsbibliothek gesendet. Genau wie bei der normalen Protokollierung können benutzerdefinierte Eigenschaften hinzugefügt werden.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Da Ausnahmen explizit protokolliert werden müssen, kann der Benutzer entscheiden, wie nicht behandelte Ausnahmen protokolliert werden sollen. Diese Entscheidung wird durch OpenCensus nicht eingeschränkt, solange eine explizite Protokollierung von Ausnahmetelemetriedaten erfolgt.

#### <a name="send-events"></a>Senden von Ereignissen

Sie können Telemetriedaten vom Typ `customEvent` genauso wie Telemetriedaten vom Typ `trace` senden, außer dass Sie stattdessen `AzureEventHandler` verwenden.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Stichproben

Informationen zur Stichprobenerstellung in OpenCensus finden Sie unter [Stichprobenerstellung in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Protokollkorrelation

Ausführliche Informationen dazu, wie Sie die Protokolle um Daten im Ablaufverfolgungskontext erweitern können, finden Sie unter [Integration von Protokollen](./correlation.md#log-correlation) für OpenCensus Python.

#### <a name="modify-telemetry"></a>Ändern der Telemetrie

Einzelheiten dazu, wie Sie nachverfolgte Telemetrie ändern können, bevor sie an Azure Monitor gesendet wird, finden Sie unter [OpenCensus Python-Telemetrieprozessoren](./api-filtering-sampling.md#opencensus-python-telemetry-processors).


### <a name="metrics"></a>Metriken

„OpenCensus.stats“ unterstützt vier Aggregationsmethoden, bietet jedoch teilweise Unterstützung für Azure Monitor:

- **Count:** Die Anzahl der Messpunkte. Der Wert ist kumulativ. Er kann nur erhöht werden und wird beim Neustart auf 0 zurückgesetzt. 
- **Sum:** Die Summe der Messpunkte. Der Wert ist kumulativ. Er kann nur erhöht werden und wird beim Neustart auf 0 zurückgesetzt. 
- **LastValue:** Behält den zuletzt aufgezeichneten Wert bei und löscht alle übrigen Werte.
- **Distribution:** Histogrammverteilung der Messpunkte. Diese Methode wird **vom Azure-Exportprogramm NICHT unterstützt**.

### <a name="count-aggregation-example"></a>Beispiel für die Aggregation der Anzahl

1. Zuerst generieren wir einige lokale Metrikdaten. Wir erstellen eine einfache Metrik, um nachzuverfolgen, wie häufig der Benutzer die **EINGABETASTE** betätigt.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Bei wiederholter Ausführung des Codes werden Sie aufgefordert, die **EINGABETASTE** zu drücken. Es wird eine Metrik erstellt, um die Anzahl von Betätigungen der **EINGABETASTE** nachzuverfolgen. Bei jedem Eintrag wird der Wert erhöht, und die Metrikinformationen werden in der Konsole angezeigt. Die Informationen umfassen den aktuellen Wert und den aktuellen Zeitstempel für den Aktualisierungszeitpunkt der Metrik.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Das Eingeben von Werten ist zu Demonstrationszwecken zwar hilfreich, aber wir möchten eigentlich die Metrikdaten für Azure Monitor ausgeben. Übergeben Sie die Verbindungszeichenfolge direkt an das Exportprogramm. Sie können sie auch in einer Umgebungsvariablen (`APPLICATIONINSIGHTS_CONNECTION_STRING`) angeben. Ändern Sie Ihren Code aus dem vorherigen Schritt basierend auf dem folgenden Codebeispiel:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. Das Exportprogramm sendet die Metrikdaten in einem festen Intervall an Azure Monitor. Die Standardeinstellung ist alle 15 Sekunden. Wir verfolgen nur eine einzelne Metrik. Diese Metrikdaten werden also mit dem jeweiligen Wert und Zeitstempel in jedem Intervall gesendet. Der Wert ist kumulativ. Er kann nur erhöht werden und wird beim Neustart auf 0 zurückgesetzt. Sie finden die Daten unter `customMetrics`, die `customMetrics`-Eigenschaften „valueCount“, „valueSum“, „valueMin“, „valueMax“ und „valueStdDev“ werden effektiv jedoch nicht verwendet.

### <a name="setting-custom-dimensions-in-metrics"></a>Festlegen benutzerdefinierter Dimensionen in Metriken

Mit dem Opencensus Python SDK können Sie Ihrer Metriktelemetrie mithilfe von `tags` benutzerdefinierte Dimensionen hinzufügen. Bei diesen Tags handelt es sich im Grunde um ein Wörterbuch aus Schlüssel-Wert-Paaren. 

1. Fügen Sie die Tags, die Sie verwenden möchten, in die Tagzuordnung ein. Die Tagzuordnung fungiert als eine Art „Pool“ aller verfügbaren Tags, die Sie verwenden können.

    ```python
    ...
    tmap = tag_map_module.TagMap()
    tmap.insert("url", "http://example.com")
    ...
    ```

1. Geben Sie für eine bestimmte `View` die Tags an, die Sie beim Aufzeichnen von Metriken mit dieser Ansicht über den Tagschlüssel verwenden möchten.

    ```python
    ...
    prompt_view = view_module.View("prompt view",
                                "number of prompts",
                                ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                                prompt_measure,
                                aggregation_module.CountAggregation())
    ...
    ```

1. Achten Sie darauf, die Tagzuordnung bei der Aufzeichnung in der Messzuordnung zu verwenden. Die in der `View` angegebenen Tagschlüssel müssen in der Tagzuordnung enthalten sein, die für die Aufzeichnung verwendet wird.

    ```python
    ...
    mmap = stats_recorder.new_measurement_map()
    mmap.measure_int_put(prompt_measure, 1)
    mmap.record(tmap) # <-- pass the tag map in here
    ...
    ```

1. Unterhalb der Tabelle `customMetrics` umfassen alle Metrikdatensätze, die mithilfe von `prompt_view` ausgegeben werden, benutzerdefinierte Dimensionen `{"url":"http://example.com"}`.

1. Um Tags mit unterschiedlichen Werten unter Verwendung derselben Schlüsseln zu generieren, erstellen Sie neue Tagzuordnungen für diese.

    ```python
    ...
    tmap = tag_map_module.TagMap()
    tmap2 = tag_map_module.TagMap()
    tmap.insert("url", "http://example.com")
    tmap2.insert("url", "https://www.wikipedia.org/wiki/")
    ...
    ```

#### <a name="performance-counters"></a>Leistungsindikatoren

Standardmäßig sendet das Exportprogramm für Metriken eine Reihe von Leistungsindikatoren an Azure Monitor. Sie können dies deaktivieren, indem Sie das Flag `enable_standard_metrics` im Konstruktor des Tools zum Exportieren von Metriken auf `False` festlegen.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Die folgenden Leistungsindikatoren werden derzeit gesendet:

- Verfügbarer Arbeitsspeicher (Bytes)
- CPU – Prozessorzeit (%)
- Rate eingehender Anforderungen (pro Sekunde)
- Durchschnittliche Ausführungsdauer eingehender Anforderungen (Millisekunden)
- Prozess – CPU-Auslastung (%)
- Prozess – Private Bytes (Bytes)

Diese Metriken sollten in `performanceCounters` angezeigt werden. Weitere Informationen finden Sie unter [Systemleistungsindikatoren in Application Insights](./performance-counters.md).

#### <a name="modify-telemetry"></a>Ändern der Telemetrie

Informationen dazu, wie Sie nachverfolgte Telemetrie ändern können, bevor sie an Azure Monitor gesendet wird, finden Sie unter [OpenCensus Python-Telemetrieprozessoren](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

### <a name="tracing"></a>Ablaufverfolgung

> [!NOTE]
> In OpenCensus bezieht sich `tracing` auf die [verteilte Ablaufverfolgung](./distributed-tracing.md). `AzureExporter` sendet Telemetrie zu `requests` und `dependency` an Azure Monitor.

1. Zuerst generieren wir lokal einige Ablaufverfolgungsdaten. Geben Sie in Python IDLE oder einem Editor Ihrer Wahl den folgenden Code ein:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Bei wiederholter Ausführung des Codes werden Sie aufgefordert, einen Wert einzugeben. Mit jedem Eintrag wird der Wert in die Shell gedruckt. Das OpenCensus Python-Modul generiert ein entsprechendes `SpanData`-Element. Das OpenCensus-Projekt definiert eine [Ablaufverfolgung als Struktur von Spannen (Spans)](https://opencensus.io/core-concepts/tracing/).
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Das Eingeben von Werten ist zu Demonstrationszwecken zwar hilfreich, aber wir möchten eigentlich `SpanData` für Azure Monitor ausgeben. Übergeben Sie die Verbindungszeichenfolge direkt an das Exportprogramm. Sie können sie auch in einer Umgebungsvariablen (`APPLICATIONINSIGHTS_CONNECTION_STRING`) angeben. Ändern Sie Ihren Code aus dem vorherigen Schritt basierend auf dem folgenden Codebeispiel:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Wenn Sie das Python-Skript jetzt ausführen, sollten Sie weiterhin zur Eingabe von Werten aufgefordert werden, doch wird nur der Wert in die Shell gedruckt. Das erstellte `SpanData`-Element wird an Azure Monitor gesendet. Sie finden die ausgegebenen Span-Daten unter `dependencies`. Weitere Informationen zu ausgehenden Anforderungen finden Sie unter [Nachverfolgen von Abhängigkeiten mit OpenCensus Python](./opencensus-python-dependency.md).
Weitere Informationen zu eingehenden Anforderungen finden Sie unter [Nachverfolgen eingehender Anforderungen mit OpenCensus Python](./opencensus-python-request.md).

#### <a name="sampling"></a>Stichproben

Informationen zur Stichprobenerstellung in OpenCensus finden Sie unter [Stichprobenerstellung in OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Korrelation der Ablaufverfolgung

Weitere Informationen zur Telemetriekorrelation in den Ablaufverfolgungsdaten finden Sie unter [Telemetriekorrelation in OpenCensus Python](./correlation.md#telemetry-correlation-in-opencensus-python).

#### <a name="modify-telemetry"></a>Ändern der Telemetrie

Weitere Informationen dazu, wie Sie nachverfolgte Telemetrie ändern können, bevor sie an Azure Monitor gesendet wird, finden Sie unter [OpenCensus Python-Telemetrieprozessoren](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

## <a name="configure-azure-monitor-exporters"></a>Konfigurieren von Azure Monitor-Exportprogrammen

Wie bereits gezeigt, gibt es drei unterschiedliche Azure Monitor-Exportprogramme, die OpenCensus unterstützen. Jedes Programm sendet andere Arten von Telemetriedaten an Azure Monitor. Informationen dazu, welche Arten von Telemetriedaten vom jeweiligen Exportprogramm gesendet werden, finden Sie in der folgenden Liste.

Alle Exportprogramme akzeptieren dieselben Argumente für die Konfiguration, die über die Konstruktoren übergeben werden. Details zu den einzelnen Argumenten sind nachfolgend aufgeführt:

- `connection_string`: Die Verbindungszeichenfolge, mit der eine Verbindung mit der Azure Monitor-Ressource hergestellt wird. Dies hat Vorrang vor `instrumentation_key`.
- `enable_standard_metrics`: Wird für `AzureMetricsExporter` verwendet. Signalisiert dem Exportprogramm, dass [Leistungsindikatormetriken](../essentials/app-insights-metrics.md#performance-counters) automatisch an Azure Monitor gesendet werden sollen. Der Standardwert lautet `True`.
- `export_interval`: Wird verwendet, um die Häufigkeit in Sekunden für den Export anzugeben.
- `instrumentation_key`: Der Instrumentierungsschlüssel, mit dem eine Verbindung mit der Azure Monitor-Ressource hergestellt wird.
- `logging_sampling_rate`: Wird für `AzureLogHandler` verwendet. Stellt eine Stichprobenrate [0,1.0] für das Exportieren von Protokollen bereit. Der Standardwert ist 1.0.
- `max_batch_size`: Gibt die maximale Größe der Telemetriedaten an, die gleichzeitig exportiert werden.
- `proxies`: Gibt eine Folge von Proxys an, die zum Senden von Daten an Azure Monitor verwendet werden sollen. Weitere Informationen finden Sie unter [Proxys](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: Ein Pfad zum Speicherort des lokalen Speicherordners (nicht gesendete Telemetrie). Ab `opencensus-ext-azure` v1.0.3 ist der Standardpfad das temporäre Verzeichnis des Betriebssystems + `opencensus-python` + `your-ikey`. Bei früheren Versionen als v1.0.3 lautet der Standardpfad $USER + `.opencensus` + `.azure` + `python-file-name`.

## <a name="authentication-preview"></a>Authentifizierung (Vorschau)
> [!NOTE]
> Das Authentifizierungsfeature ist ab `opencensus-ext-azure` v1.1b0 verfügbar.

Jede der Azure Monitor-Exportkomponenten unterstützt die Konfiguration des sicheren Sendens von Telemetrienutzdaten über die OAuth-Authentifizierung mit Azure Active Directory (AAD).
Weitere Informationen finden Sie in der [Dokumentation zur Authentifizierung](./azure-ad-authentication.md).

## <a name="view-your-data-with-queries"></a>Anzeigen Ihrer Daten mit Abfragen

Sie können die von Ihrer Anwendung gesendeten Telemetriedaten über die Registerkarte **Protokolle (Analytics)** anzeigen.

![Screenshot des Übersichtsbereichs mit „Protokolle (Analytics)“ in einem roten Kasten](./media/opencensus-python/0010-logs-query.png)

In der Liste unter **Aktiv** ist Folgendes angegeben:

- Für Telemetriedaten, die mit dem Exportprogramm der Azure Monitor-Ablaufverfolgung gesendet werden, werden eingehende Anforderungen unter `requests` angezeigt. Ausgehende bzw. in Bearbeitung befindliche Anforderungen werden unter `dependencies` angezeigt.
- Für Telemetriedaten, die mit dem Exportprogramm für Azure Monitor-Metriken gesendet werden, werden die gesendeten Metriken unter `customMetrics` angezeigt.
- Für Telemetriedaten, die mit dem Exportprogramm für Azure Monitor-Protokolle gesendet werden, werden die Protokolle unter `traces` angezeigt. Ausnahmen werden unter `exceptions` angezeigt.

Ausführlichere Informationen zur Verwendung von Abfragen und Protokollen finden Sie unter [Protokolle in Azure Monitor](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Weitere Informationen zu OpenCensus für Python

* [OpenCensus Python auf GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Anpassung](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor Exporters auf GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus-Integrationen](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor-Beispielanwendungen](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Nächste Schritte

* [Verfolgen eingehender Anforderungen](./opencensus-python-dependency.md)
* [Verfolgen ausgehender Anforderungen](./opencensus-python-request.md)
* [Anwendungszuordnung](./app-map.md)
* [End-to-End-Leistungsüberwachung](../app/tutorial-performance.md)

### <a name="alerts"></a>Alerts

* [Verfügbarkeitstests](./monitor-web-app-availability.md): Erstellen Sie Tests, um sicherzustellen, dass Ihre Website im Web sichtbar ist.
* [Intelligente Diagnose](./proactive-diagnostics.md): Diese Tests werden automatisch ausgeführt, sodass Sie keinerlei Einrichtungsschritte ausführen müssen. Sie werden darüber benachrichtigt, ob für Ihre App eine ungewöhnlich hohe Zahl von Anforderungen mit Fehlern vorliegt.
* [Metrikwarnungen](../alerts/alerts-log.md): Richten Sie Warnungen ein, damit Sie gewarnt werden, wenn für eine Metrik ein Schwellenwert überschritten wird. Sie können diese für benutzerdefinierte Metriken festlegen, die Sie in Ihrer App codieren.
