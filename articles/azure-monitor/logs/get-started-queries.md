---
title: Erste Schritte mit Protokollabfragen in Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel enthält ein Tutorial zu den ersten Schritten beim Schreiben von Protokollabfragen in Azure Monitor.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: cc06241e948f2f82440d6e41f5a7a26639a54a9a
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418730"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Erste Schritte mit Protokollabfragen in Azure Monitor

> [!NOTE]
> Wenn Sie Daten von mindestens einem virtuellen Computer sammeln, können Sie diese Übung in Ihrer eigenen Umgebung durcharbeiten. Wenn nicht, verwenden Sie die [Demoumgebung](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), die eine Vielzahl von Beispieldaten enthält.  
>
> Wenn Sie bereits wissen, wie Sie Abfragen in Kusto-Abfragesprache (Kusto Query Language, KQL) ausführen, aber schnell nützliche Abfragen auf Grundlage von Ressourcentypen erstellen müssen, finden Sie weitere Informationen im Bereich mit den gespeicherten Beispielabfragen im Artikel [Verwenden von Abfragen in Azure Monitor Log Analytics](../logs/queries.md).

In diesem Tutorial erfahren Sie, wie Sie Protokollabfragen in Azure Monitor schreiben. In diesem Artikel wird Folgendes vermittelt:

- Grundlegendes zur Abfragestruktur.
- Sortieren von Abfrageergebnissen.
- Filtern von Abfrageergebnissen.
- Festlegen eines Zeitbereichs.
- Auswählen der Felder, die in den Ergebnissen enthalten sein sollen.
- Definieren und Verwenden von benutzerdefinierten Feldern.
- Aggregieren und Gruppieren von Ergebnissen.

Ein Tutorial zur Verwendung von Log Analytics im Azure-Portal finden Sie unter [Erste Schritte mit Azure Monitor Log Analytics](./log-analytics-tutorial.md).

Weitere Informationen zu Protokollabfragen in Azure Monitor finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../logs/log-query-overview.md).

Hier finden Sie eine Videoversion dieses Tutorials:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="write-a-new-query"></a>Schreiben einer neuen Abfrage

Abfragen können entweder mit einem Tabellennamen oder dem *search*-Befehl beginnen. Sie sollten mit einem Tabellennamen beginnen, da er einen klaren Gültigkeitsbereich für die Abfrage definiert und die Abfrageleistung und -relevanz der Ergebnisse verbessert.

> [!NOTE]
> Bei der Abfragesprache Kusto, die in Azure Monitor verwendet wird, wird die Groß-/Kleinschreibung berücksichtigt. Sprachschlüsselwörter werden in der Regel in Kleinbuchstaben geschrieben. Verwenden Sie die richtige Schreibweise bei Tabellen- oder Spaltennamen in einer Abfrage, wie im Schemabereich angezeigt wird.

### <a name="table-based-queries"></a>Tabellenbasierte Abfragen

In Azure Monitor werden Protokolldaten in Tabellen bestehend aus mehreren Spalten organisiert. Alle Tabellen und Spalten werden im Schemabereich in Log Analytics im Analytics-Portal angezeigt. Identifizieren Sie eine Tabelle, die Sie interessiert, und untersuchen Sie dann einen Teil der Daten:

```Kusto
SecurityEvent
| take 10
```

Die vorangehende Abfrage gibt in keiner bestimmten Reihenfolge 10 Ergebnisse aus der *SecurityEvent*-Tabelle zurück. Dies ist eine gängige Methode, um eine Tabelle zu untersuchen und sich mit deren Struktur und Inhalt vertraut zu machen. Werfen wir einen Blick darauf, wie sie erstellt wird:

* Die Abfrage beginnt mit dem Tabellennamen *SecurityEvent*, der den Gültigkeitsbereich der Abfrage definiert.
* Der senkrechte Strich (|) trennt Befehle, d. h. die Ausgabe des ersten Befehls ist die Eingabe für den nächsten. Sie können eine beliebige Anzahl von durch senkrechte Striche getrennten Elementen hinzufügen.
* Nach dem senkrechten Strich folgt der **take**-Befehl, der eine bestimmte Anzahl von beliebigen Datensätzen aus der Tabelle zurückgibt.

Wir könnten die Abfrage tatsächlich auch ohne Hinzufügen von `| take 10` ausführen. Der Befehl wäre weiterhin gültig, könnte aber bis zu 10.000 Ergebnisse zurückgeben.

### <a name="search-queries"></a>Suchabfragen

Suchabfragen sind weniger strukturiert und in der Regel besser geeignet für die Suche nach Datensätzen, die einen bestimmten Wert in einer der Spalten enthalten:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Diese Abfrage sucht die *SecurityEvent*-Tabelle für Datensätze, die den Ausdruck „Cryptographic“ enthalten. Von diesen Datensätzen werden 10 Datensätze zurückgegeben und angezeigt. Wenn Sie den Teil `in (SecurityEvent)` auslassen und nur `search "Cryptographic"` ausführen, geht die Suche *alle* Tabellen durch, was mehr Zeit in Anspruch nehmen würde und weniger effizient wäre.

> [!IMPORTANT]
> Suchabfragen sind üblicherweise langsamer als tabellengestützte Abfragen, da sie mehr Daten verarbeiten müssen. 

## <a name="sort-and-top"></a>„sort“ und „top“
Zwar können mit **take** einige Datensätze abgerufen werden, allerdings werden die Ergebnisse in keiner bestimmten Reihenfolge ausgewählt und angezeigt. Um eine sortierte Ansicht zu erhalten, können Sie diese nach der bevorzugten Spalte **sortieren**:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Die vorherige Abfrage könnte jedoch zu viele Ergebnisse zurückgeben und außerdem einige Zeit dauern. Die Abfrage sortiert die *gesamte* „SecurityEvent“-Tabelle nach der **TimeGenerated**-Spalte. Das Analytics-Portal beschränkt die Anzeige dann auf lediglich 10.000 Datensätze. Dieser Ansatz ist natürlich nicht optimal.

Die beste Möglichkeit, nur die letzten 10 Datensätze zu erhalten, besteht darin, **top** zu verwenden, das die gesamte Tabelle auf der Serverseite sortiert und dann die ersten Datensätze zurückgibt:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Die Standardsortierreihenfolge ist absteigend, weshalb Sie in der Regel das **desc**-Argument auslassen würden. Die Ausgabe sieht wie folgt aus:

![Screenshot der ersten 10 Datensätze, sortiert in absteigender Reihenfolge.](media/get-started-queries/top10.png)


## <a name="the-where-operator-filtering-on-a-condition"></a>Der „where“-Operator: Filtern nach einer Bedingung
Wie der Name schon angibt, filtern Filter die Daten nach einer bestimmten Bedingung. Dies ist die gängigste Methode, um Abfrageergebnisse auf relevante Informationen zu beschränken.

Verwenden Sie zum Hinzufügen eines Filters zu einer Abfrage den **where**-Operator gefolgt von einer oder mehreren Bedingungen. Die folgende Abfrage gibt beispielsweise nur *SecurityEvent*-Datensätze zurück, bei denen _Level_ _8_ entspricht:

```Kusto
SecurityEvent
| where Level == 8
```

Beim Schreiben von Filterbedingungen können Sie folgende Ausdrücke verwenden:

| Ausdruck | BESCHREIBUNG | Beispiel |
|:---|:---|:---|
| == | Überprüfung auf Gleichheit<br>(mit Berücksichtigung der Groß-/Kleinschreibung) | `Level == 8` |
| =~ | Überprüfung auf Gleichheit<br>(ohne Berücksichtigung der Groß-/Kleinschreibung) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Überprüfung auf Ungleichheit<br>(beide Ausdrücke sind identisch) | `Level != 4` |
| *and*, *or* | Zwischen Bedingungen erforderlich| `Level == 16 or CommandLine != ""` |

Um nach mehreren Bedingungen zu filtern, können Sie einen der folgenden Ansätze verwenden:

Verwenden Sie **and** (und), wie hier gezeigt:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

Übergeben Sie hintereinander mehrere **where**-Elemente per Pipe, wie hier gezeigt:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Werte können unterschiedliche Typen aufweisen. Deshalb müssen sie eventuell umgewandelt werden, damit ein Vergleich für den richtigen Typ ausgeführt werden kann. Die SecurityEvent-Spalte *Level* ist beispielsweise vom Typ „String“. Sie müssen daher eine Umwandlung in einen numerischen Typ wie *Int* oder *long* durchführen, bevor Sie numerische Operatoren für diese verwenden können, wie hier gezeigt: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Festlegen eines Zeitbereichs

### <a name="use-the-time-picker"></a>Verwenden der Zeitauswahl

Die Zeitauswahl wird neben der Schaltfläche **Ausführen** angezeigt und zeigt an, dass Sie nur Datensätze aus den letzten 24 Stunden abfragen. Dies ist der Standardzeitbereich, der auf alle Abfragen angewendet wird. Um nur Datensätze aus der letzten Stunde zu erhalten, wählen Sie _Letzte Stunde_ aus, und führen Sie dann die Abfrage erneut aus.

![Screenshot der Zeitauswahl und ihrer Liste von Zeitbereichsbefehlen.](media/get-started-queries/timepicker.png)


### <a name="add-a-time-filter-to-the-query"></a>Hinzufügen eines Filters zur Abfrage

Sie können auch einen eigenen Zeitbereich definieren, indem Sie einen Zeitfilter zur Abfrage hinzufügen. Der Zeitfilter sollte idealerweise unmittelbar nach dem Tabellennamen platziert werden: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Im vorherigen Zeitfilter bedeutet `ago(30m)` „vor 30 Minuten“, was bedeutet, dass diese Abfrage nur Datensätze aus den letzten 30 Minuten zurückgibt (z. B. ausgedrückt als 30 m). Andere Zeiteinheiten umfassen Tage (z. B. 2d) und Sekunden (z. B. 10s).


## <a name="use-project-and-extend-to-select-and-compute-columns"></a>Verwenden von „project“ und „extend“ zum Auswählen und Berechnen von Spalten

Mit **project** können Sie bestimmte Spalten zur Einbeziehung in die Ergebnisse auswählen:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Das Beispiel oben generiert die folgende Ausgabe:

![Screenshot der Ergebnisliste für die Abfrage von „project“.](media/get-started-queries/project.png)

Sie können über **project** auch Spalten umbenennen und neue definieren. Im nächsten Beispiel wird mit **project** Folgendes ausgeführt:

* Wählen Sie nur die ursprünglichen Spalten *Computer* und *TimeGenerated* aus.
* Anzeigen der Spalte *Aktivität* als *EventDetails*.
* Erstellen Sie eine neue Spalte mit dem Namen *EventCode*. Die Funktion **substring()** wird verwendet, um nur die ersten vier Zeichen aus dem Feld „Activity“ abzurufen.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

Sie können **extend** verwenden, um alle ursprünglichen Spalten im Resultset beizubehalten und zusätzliche zu definieren. Die folgende Abfrage verwendet **extend** zum Hinzufügen der Spalte *EventCode*. Diese Spalte wird möglicherweise nicht am Ende der Tabellenergebnisse angezeigt. In diesem Fall müssen Sie zum Anzeigen die Details eines Datensatzes erweitern.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="use-summarize-to-aggregate-groups-of-rows"></a>Verwenden von „summarize“ zum Aggregieren von Zeilengruppen
Verwenden Sie **summarize**, um Gruppen von Datensätzen entsprechend einer oder mehrerer Spalten zu identifizieren und Aggregationen auf diese anzuwenden. Am häufigsten wird **summarize** mit *count* verwendet, womit die Anzahl von Ergebnissen in jeder Gruppe zurückgegeben wird.

Die folgende Abfrage überprüft alle *Perf*-Datensätze aus der letzten Stunde, gruppiert diese nach *ObjectName* und zählt die Datensätze in jeder Gruppe: 

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Manchmal ist es sinnvoll, Gruppen nach mehreren Dimensionen zu definieren. Jede eindeutige Kombination der folgenden Werte definiert eine separate Gruppe:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Auch die Ausführung von mathematischen oder statistischen Berechnungen für jede Gruppe ist ein häufiger Anwendungsfall. Im folgenden Beispiel wird der durchschnittliche *CounterValue* für jeden Computer berechnet:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Die Ergebnisse dieser Abfrage sind jedoch leider bedeutungslos, da wir verschiedene Leistungsindikatoren gemischt haben. Um die Ergebnisse aussagekräftiger zu gestalten, berechnen Sie den Mittelwert separat für jede Kombination von *CounterName* und *Computer*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Zusammenfassen nach einer Zeitspalte
Die Gruppierung von Ergebnissen kann auch auf einer Zeitspalte oder einem anderen kontinuierlichen Wert basieren. Durch das bloße Zusammenfassen von `by TimeGenerated` würden Gruppen für jede einzelne Millisekunde für den Zeitbereich erstellt werden, da es sich um eindeutige Werte handelt. 

Zum Erstellen von Gruppen, die auf kontinuierlichen Werten basieren, wird empfohlen, den Bereich mittels **bin** in verwaltbare Einheiten zu unterteilen. Die folgende Abfrage analysiert *Perf*-Datensätze, die den freien Arbeitsspeicher (*MBytes*) auf einem bestimmten Computer ermitteln. Sie berechnet den Durchschnittswert für jeden Zeitraum von einer Stunde über die letzten sieben Tage:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Um die Ausgabe übersichtlicher zu gestalten, können Sie das jeweilige Zeitdiagramm auswählen, das den verfügbaren Arbeitsspeicher im Laufe der Zeit darstellt:

![Screenshot der die Werte einer Abfrage des Arbeitsspeichers im Laufe der Zeit zeigt.](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwendung von Zeichenfolgendaten in einer Protokollabfrage finden Sie unter [Arbeiten mit Zeichenfolgen in Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Weitere Informationen zum Aggregieren von Daten in einer Protokollabfrage finden Sie unter [Erweiterte Aggregationen in Azure Monitor-Protokollabfragen](/azure/data-explorer/write-queries#advanced-aggregations).
- Unter [Joins in Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins) erfahren Sie, wie Sie Daten aus mehreren Tabellen verknüpfen.
- Eine Dokumentation zur gesamten Kusto-Abfragesprache finden Sie in der [KQL-Sprachreferenz](/azure/kusto/query/).
