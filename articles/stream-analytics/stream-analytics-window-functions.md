---
title: Einführung in die Azure Stream Analytics-Windowing-Funktionen
description: In diesem Artikel werden die vier Windowing-Funktionen (Rollierend, Springend, Gleitend, Sitzung) beschrieben, die in Azure Stream Analytics-Aufträgen verwendet werden.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 83c574ca578247389f8bc8c53c07847e1f01fb8b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003630"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Einführung in die Stream Analytics-Windowing-Funktionen

Bei Szenarien mit „Time Streaming“ ist das Durchführen von Vorgängen für die Daten in temporalen Fenstern ein häufiges Muster. Stream Analytics verfügt über native Unterstützung für Windowing-Funktionen, sodass Entwickler komplexe Streaming-Verarbeitungsaufträge mit sehr geringem Aufwand erstellen können.

Fünf Arten temporaler Fenster stehen zur Auswahl: Fenster vom Typ [**Rollierend**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**Springend**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**Gleitend**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**Sitzung**](/stream-analytics-query/session-window-azure-stream-analytics) und [**Momentaufnahme**](/stream-analytics-query/snapshot-window-azure-stream-analytics).  Sie verwenden die Fensterfunktionen in der [**GROUP BY**](/stream-analytics-query/group-by-azure-stream-analytics)-Klausel der Abfragesyntax in Ihren Stream Analytics-Aufträgen. Sie können Ereignisse auch über mehrere Fenster hinweg aggregieren, indem Sie die [**Windows()** -Funktion](/stream-analytics-query/windows-azure-stream-analytics) verwenden.

Für alle [Windowing](/stream-analytics-query/windowing-azure-stream-analytics)-Vorgänge werden am **Ende** des Fensters Ergebnisse ausgegeben. Beachten Sie, dass Sie beim Starten eines Stream Analytics-Auftrags die *Startzeit für die Auftragsausgabe*  angeben können, und das System ruft automatisch vorherige Ereignisse in den eingehenden Datenströmen ab, um das erste Fenster zum angegebenen Zeitpunkt auszugeben, z. B. wenn Sie mit der Option *Jetzt* beginnen, werden sofort Daten ausgegeben. Die Ausgabe des Fensters ist ein einzelnes Ereignis, das auf der verwendeten Aggregatfunktion basiert. Das Ausgabeereignis verfügt über den Zeitstempel vom Ende des Fensters, und alle Fensterfunktionen werden mit einer festen Länge definiert. 

![Stream Analytics-Fensterfunktionen – Konzepte](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Rollierendes Fenster

Funktionen für [**rollierende**](/stream-analytics-query/tumbling-window-azure-stream-analytics) Fenster werden verwendet, um einen Datenstrom in einzelne Zeitsegmente zu unterteilen und dafür eine Funktion durchzuführen, wie im Beispiel unten gezeigt. Die wichtigsten Unterscheidungsmerkmale eines rollierenden Fensters sind, dass sie wiederholt werden, sich nicht überlappen und ein Ereignis nicht zu mehr als einem rollierenden Fenster gehören kann.

![Stream Analytics – Rollierendes Fenster](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

Mit den folgenden Eingabedaten (oben dargestellt):

|Stamp|CreatedAt|TimeZone|
|-|-|-|
|1|2021-10-26T10:15:01|PST|
|5|2021-10-26T10:15:03|PST|
|4|2021-10-26T10:15:06|PST|
|...|...|...|

Die folgende Abfrage:

```SQL
SELECT System.Timestamp() as WindowEndTime, TimeZone, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TimeZone, TumblingWindow(second,10)
```

Rückgabe:

|WindowEndTime|TimeZone|Anzahl|
|-|-|-|
|2021-10-26T10:15:10|PST|5|
|2021-10-26T10:15:20|PST|2|
|2021-10-26T10:15:30|PST|4|


## <a name="hopping-window"></a>Springendes Fenster

Bei den Funktionen für [**springende**](/stream-analytics-query/hopping-window-azure-stream-analytics) Fenster wird für einen festen Zeitraum ein Sprung nach vorn durchgeführt. Sie können sich dies wie rollierende Fenster vorstellen, die sich überlappen können und häufiger als die Fenstergröße ausgegeben werden. Ereignisse können zu Resultsets von mehr als einem springenden Fenstern gehören. Um ein springendes Fenster an ein rollierendes Fenster anzugleichen, passen Sie die Sprunggröße an die Fenstergröße an. 

![Stream Analytics – Springendes Fenster](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

Mit den folgenden Eingabedaten (oben dargestellt):

|Stamp|CreatedAt|Thema|
|-|-|-|
|1|2021-10-26T10:15:01|Streaming|
|5|2021-10-26T10:15:03|Streaming|
|4|2021-10-26T10:15:06|Streaming|
|...|...|...|

Die folgende Abfrage:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, HoppingWindow(second,10,5)
```

Rückgabe:

|WindowEndTime|Thema|Anzahl|
|-|-|-|
|2021-10-26T10:15:10|Streaming|5|
|2021-10-26T10:15:15|Streaming|3|
|2021-10-26T10:15:20|Streaming|2|
|2021-10-26T10:15:25|Streaming|4|
|2021-10-26T10:15:30|Streaming|4|


## <a name="sliding-window"></a>Gleitendes Fenster

Im Gegensatz zu rollierenden oder springenden Fenstern geben [**gleitende**](/stream-analytics-query/sliding-window-azure-stream-analytics) Fenster Ereignisse nur für Zeitpunkte aus, zu denen sich der Inhalt des Fensters tatsächlich ändert. Anders ausgedrückt: wenn ein Ereignis das Fenster betritt oder verlässt. Jedes Fenster verfügt also über mindestens ein Ereignis. Ähnlich wie bei springenden Fenstern können Ereignisse zu mehr als einem gleitenden Fenster gehören.

![Gleitendes 10-Sekunden-Fenster in Stream Analytics](media/stream-analytics-window-functions/sliding-window-updated.png)

Mit den folgenden Eingabedaten (oben dargestellt):

|Stamp|CreatedAt|Thema|
|-|-|-|
|1|2021-10-26T10:15:10|Streaming|
|5|2021-10-26T10:15:12|Streaming|
|9|2021-10-26T10:15:15|Streaming|
|7|2021-10-26T10:15:15|Streaming|
|8|2021-10-26T10:15:27|Streaming|

Die folgende Abfrage:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SlidingWindow(second,10)
HAVING COUNT(*) >=3
```

Rückgabe:

|WindowEndTime|Thema|Anzahl|
|-|-|-|
|2021-10-26T10:15:15|Streaming|4|
|2021-10-26T10:15:20|Streaming|3|

## <a name="session-window"></a>Sitzungsfenster

Mit Funktionen für [**Sitzungsfenster**](/stream-analytics-query/session-window-azure-stream-analytics) werden Ereignisse gruppiert, die zu ähnlichen Zeiten eingehen. Zeiträume, in denen keine Daten anfallen, werden herausgefiltert. Es werden drei Hauptparameter verwendet: Timeout, maximale Dauer und Partitionierungsschlüssel (optional).

![Stream Analytics – Sitzungsfenster](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Ein Sitzungsfenster beginnt, wenn das erste Ereignis eintritt. Wenn ein anderes Ereignis innerhalb des angegebenen Zeitlimits für das letzte erfasste Ereignis eintritt, wird das Fenster erweitert, damit es das neue Ereignis enthält. Falls innerhalb des Zeitlimits keine Ereignisse eintreten, wird das Fenster zum Zeitlimit-Endzeitpunkt geschlossen.

Wenn innerhalb des angegebenen Zeitraums weiter Ereignisse eintreten, wird das Sitzungsfenster so lange erweitert, bis die maximale Dauer erreicht ist. Die Überprüfungsintervalle für die maximale Dauer werden auf die gleiche Größe wie die angegebene maximale Dauer festgelegt. Wenn die maximale Dauer beispielsweise 10 beträgt, werden die Überprüfungen, ob für das Fenster die maximale Dauer überschritten wird, nach dem Muster t = 0, 10, 20, 30 usw. durchgeführt.

Wenn ein Partitionsschlüssel angegeben wird, werden die Ereignisse nach dem Schlüssel gruppiert, und das Sitzungsfenster wird auf jede Gruppe separat angewendet. Diese Partitionierung ist für Fälle nützlich, in denen Sie unterschiedliche Sitzungsfenster für unterschiedliche Benutzer oder Geräte benötigen.

Mit den folgenden Eingabedaten (oben dargestellt):

|Stamp|CreatedAt|Thema|
|-|-|-|
|1|2021-10-26T10:15:01|Streaming|
|2|2021-10-26T10:15:04|Streaming|
|3|2021-10-26T10:15:13|Streaming|
|...|...|...|

Die folgende Abfrage:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SessionWindow(second,5,10)
```

Rückgabe:

|WindowEndTime|Thema|Anzahl|
|-|-|-|
|2021-10-26T10:15:09|Streaming|2|
|2021-10-26T10:15:24|Streaming|4|
|2021-10-26T10:15:31|Streaming|2|
|2021-10-26T10:15:39|Streaming|1|

## <a name="snapshot-window"></a>Momentaufnahmefenster

[**Momentaufnahmefenster**](/stream-analytics-query/snapshot-window-azure-stream-analytics) gruppieren Ereignisse, die denselben Zeitstempel aufweisen. Im Gegensatz zu anderen Fenstertypen, die eine bestimmte Fensterfunktion benötigen (z. B. [SessionWindow()](/stream-analytics-query/session-window-azure-stream-analytics)), können Sie ein Momentaufnahmefenster anwenden, indem Sie der GROUP BY-Klausel „System.Timestamp()“ hinzufügen.

![Stream Analytics-Momentaufnahmefenster](media/stream-analytics-window-functions/stream-analytics-window-functions-snapshot-intro.png)

Mit den folgenden Eingabedaten (oben dargestellt):

|Stamp|CreatedAt|Thema|
|-|-|-|
|1|2021-10-26T10:15:04|Streaming|
|2|2021-10-26T10:15:04|Streaming|
|3|2021-10-26T10:15:04|Streaming|
|...|...|...|

Die folgende Abfrage:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, System.Timestamp()
```

Rückgabe:

|WindowEndTime|Thema|Anzahl|
|-|-|-|
|2021-10-26T10:15:04|Streaming|4|
|2021-10-26T10:15:10|Streaming|2|
|2021-10-26T10:15:13|Streaming|1|
|2021-10-26T10:15:22|Streaming|2|

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)
