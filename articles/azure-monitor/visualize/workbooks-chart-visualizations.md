---
title: Diagrammvisualisierungen für Azure Monitor-Arbeitsmappen
description: In diesem Artikel erhalten Sie Informationen zu Diagrammvisualisierungen für Azure Monitor-Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 59f42867ec4dce29d136ec96df6e7642b7329911
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129280"
---
# <a name="chart-visualizations"></a>Diagrammvisualisierungen

In Arbeitsmappen können Überwachungsdaten in Diagrammen präsentiert werden. Unterstützte Diagrammtypen sind Liniendiagramm, Balkendiagramm, Balkendiagramm (kategorisch), Flächendiagramm, Punktdiagramm, Kreisdiagramm und Zeitdiagramm. Ersteller können u. a. Höhe, Breite, Farbpalette, Legende, Titel und die Meldung „Keine Daten“ für das Diagramm anpassen. Außerdem können über Diagrammeinstellungen Achsentypen und Datenreihenfarben angepasst werden.

Arbeitsmappen unterstützen Diagramme für Protokolle und Metrikdatenquellen.

## <a name="log-charts"></a>Protokolldiagramme

Azure Monitor-Protokolle liefern Ressourcenbesitzern detaillierte Informationen zur Funktionsweise ihrer Apps und Infrastruktur. Im Gegensatz zu Metriken werden Protokollinformationen nicht standardmäßig gesammelt. Vielmehr ist eine Art Erfassungsonboarding erforderlich. Wenn sie jedoch vorliegen, bieten Protokolle viele Informationen zum Zustand der Ressourcen und der Daten, die hilfreich für Diagnosezwecke sind. Arbeitsmappen ermöglichen das Präsentieren von Protokolldaten als visuelle Diagramme, um von Benutzer analysiert werden zu können.

### <a name="adding-a-log-chart"></a>Hinzufügen eines Diagramms für Protokolldaten

Das Beispiel unten zeigt den Trend von Anforderungen an eine App im Verlauf der vergangenen Tage.

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement **Bearbeiten** klicken.
2. Klicken Sie auf den Link **Abfrage hinzufügen**, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die [KQL](/azure/kusto/query/) für Ihre Analyse ein (z. B. Trend der Anforderungen).
5. Legen Sie für die Visualisierung eine der folgenden Optionen fest: **Fläche**, **Balken**, **Balkendiagramm (kategorisch)** , **Linie**, **Kreis**, **Punkt** oder **Zeit**.
6. Legen Sie ggf. weitere Parameter fest, beispielsweise Zeitraum, Visualisierung, Größe, Farbpalette und Legende.

[![Screenshot eines Diagramms für Protokolldaten im Bearbeitungsmodus](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parameter des Diagramms für Protokolldaten

| Parameter | Erklärung | Beispiel |
| ------------- |:-------------|:-------------|
| `Query Type` | Der Typ der zu verwendenden Abfrage | Protokoll, Azure Resource Graph usw. |
| `Resource Type` | Der Typ der Zielressource | Application Insights, Log Analytics oder Azure |
| `Resources` | Mehrere Ressourcen, von denen der Metrikwert abgerufen werden soll | MyApp1 |
| `Time Range` | Das Zeitfenster für die Anzeige des Diagramms für Protokolldaten | Letzte Stunde, Letzte 24 Stunden usw. |
| `Visualization` | Die zu verwendende Visualisierung | Fläche, Balken, Linie, Kreis, Punkt, Zeit, Balkendiagramm (kategorisch) |
| `Size` | Die vertikale Größe des Steuerelements | Klein, mittel, groß oder vollständig |
| `Color palette` | Im Diagramm zu verwendende Farbpalette. Wird im Modus mit mehreren Metriken oder segmentierten Modus ignoriert. | Blau, grün, rot usw. |
| `Legend` | Die für die Legende zu verwendende Aggregationsfunktion | Summe oder Durchschnitt der Werte oder Max, Min, Erster Wert, Letzter Wert |
| `Query` | Beliebige KQL-Abfrage, die Daten in dem von der Diagrammvisualisierung erwarteten Format zurückgibt | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="time-series-charts"></a>Zeitreihendiagramme

Zeitreihendiagramme wie Bereichs-, Balken-, Linien-, Punkt- und Zeitdiagramme können mithilfe des Steuerelements für Abfragen in Workbooks einfach erstellt werden. Entscheidend ist, über Zeit- und Metrikinformationen im Resultset zu verfügen.

#### <a name="simple-time-series"></a>Einfache Zeitreihen

Die Abfrage unten gibt eine Tabelle mit zwei Spalten zurück: *timestamp* und *Requests*. Das Steuerelement für Abfragen verwendet *timestamp* als X-Achse und *Requests* als Y-Achse.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Screenshot eines einfachen Liniendiagramms für ein Zeitreihenprotokoll](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Zeitreihen mit mehreren Metriken

Die Abfrage unten gibt eine Tabelle mit drei Spalten zurück: *timestamp*, *Requests* und *Users*. Das Steuerelement für Abfragen verwendet *timestamp* als X-Achse und *Requests* & *Users* als eigene Reihe auf der Y-Achse.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Screenshot einer Zeitreihe mit einem Liniendiagramm mit einem Protokoll mehrerer Metriken](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Segmentierte Zeitreihen

Die Abfrage unten gibt eine Tabelle mit drei Spalten zurück: *timestamp*, *Requests* und *RequestName*. *RequestName* ist dabei eine kategorische Spalten mit den Namen der Anforderungen. Das Steuerelement für Abfragen verwendet hier *timestamp* als X-Achse und fügt eine Reihe pro Wert von *RequestName* hinzu.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Screenshot eines segmentierten Liniendiagramms für ein Zeitreihenprotokoll](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Summarize vs. make-series

In den Beispielen des vorherigen Abschnitts wurde der `summarize`-Operator verwendet, da dieser einfacher zu verstehen ist. summarize verfügt jedoch über eine große Einschränkung, da die Ergebniszeile ausgelassen wird, wenn sich keine Elemente im Bucket befinden. Dies kann dazu führen, dass das Diagrammzeitfenster verschoben wird, je nachdem, ob die leeren Buckets vor oder nach dem Zeitraum liegen.

In der Regel empfiehlt es sich eher, den `make-series`-Operator zu verwenden, um Zeitreihendaten zu erstellen. Dieser verfügt über die Möglichkeit, Standardwerte für leere Buckets anzugeben.

Die folgende Abfrage verwendet den `make-series`-Operator.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

Die Abfrage unten zeigt ein ähnliches Diagramm mit dem `summarize`-Operator:

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Und dies, obwohl sich das zugrunde liegende Resultset unterscheidet. Benutzer müssen für die Visualisierung einfach nur Bereich, Linie, Balken oder Zeit festlegen, Workbooks übernimmt den Rest.

[![Screenshot eines aus einer Zeitreihenabfrage erstellten Protokollliniendiagramms](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Kategorisches Balkendiagramm oder Histogramm

Kategorische Diagramme ermöglichen es Benutzern, eine Dimension oder eine Spalte auf der X-Achse eines Diagramms darzustellen, was insbesondere für Histogramme hilfreich ist. Im Beispiel unten wird die Verteilung der Anforderungen nach jeweiligem Ergebniscode angezeigt.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Die Abfrage gibt zwei Spalten zurück: Die Metrik *Requests* und die Kategorie *Result*. Jeder Wert der Spalte *Result* erhält im Diagramm einen eigenen Balken. Die Höhe ist dabei proportional zur Metrik *Requests*.

[![Screenshot eines kategorischen Balkendiagramms für Anforderungen nach Ergebniscode](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Kreisdiagramme

Kreisdiagramme ermöglichen die Visualisierung von Zahlenverhältnissen. Im Beispiel unten wird das Verhältnis der Anforderungen nach jeweiligem Ergebniscode angezeigt.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Die Abfrage gibt zwei Spalten zurück: Die Metrik *Requests* und die Kategorie *Result*. Jeder Wert der Spalte *Result* erhält im Kreis ein eigenes Segment. Die Größe ist dabei proportional zur Metrik *Requests*.

[![Screenshot eines Kreisdiagramms mit Segmenten, die für Ergebniscode stehen](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Metrikdiagramme

Die meisten Azure-Ressourcen geben Metrikdaten zu Zustand und Integrität aus, z. B. zur CPU-Auslastung, zu Speicherverfügbarkeit, zur Anzahl an Datenbanktransaktionen und zu Fehlern bei App-Anforderungen. Workbooks ermöglicht die Visualisierung dieser Daten als Zeitreihendiagramme.

### <a name="adding-a-metric-chart"></a>Hinzufügen eines Diagramms für Metrikdaten

Das folgende Beispiel zeigt die Anzahl an Transaktionen in einem Speicherkonto im Verlauf der vergangenen Stunde. Dies ermöglicht dem Speicherbesitzer, den Transaktionstrend zu erkennen und nach Anomalien im Verhalten zu suchen.

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement **Bearbeiten** klicken.
2. Klicken Sie auf den Link **Metrik hinzufügen**, um der Arbeitsmappe ein Metriksteuerelement hinzuzufügen.
3. Wählen Sie einen Ressourcentyp (z. B. Speicherkonto), die Zielressourcen, den Namespace und Name für die Metrik sowie die zu verwendende Aggregation aus.
4. Legen Sie ggf. weitere Parameter fest, beispielsweise Zeitraum, „Aufteilen nach“, Visualisierung, Größe und Farbpalette.

[![Screenshot eines Diagramms für Metrikdaten im Bearbeitungsmodus](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Parameter des Diagramms für Metrikdaten

| Parameter | Erklärung | Beispiel |
| ------------- |:-------------|:-------------|
| `Resource Type` | Der Typ der Zielressource | Speicher oder VM. |
| `Resources` | Mehrere Ressourcen, von denen der Metrikwert abgerufen werden soll | MyStorage1 |
| `Namespace` | Der Namespace mit der Metrik | Speicher > Blob |
| `Metric` | Die zu visualisierende Metrik | Speicher > Blob > Transaktionen |
| `Aggregation` | Auf die Metrik anzuwendende Aggregationsfunktion | Summe, Anzahl, Durchschnitt usw. |
| `Time Range` | Zeitfenster für die Anzeige der Metrik | Letzte Stunde, Letzte 24 Stunden usw. |
| `Visualization` | Die zu verwendende Visualisierung | Fläche, Balken, Linie, Punkt, Raster |
| `Split By` | Optionales Aufteilen der Metrik in einer Dimension | Transaktionen nach Geotyp |
| `Size` | Die vertikale Größe des Steuerelements | Klein, mittel oder groß |
| `Color palette` | Im Diagramm zu verwendende Farbpalette. Wird ignoriert, wenn der `Split by`-Parameter verwendet wird. | Blau, grün, rot usw. |

### <a name="examples"></a>Beispiele

Unten sehen Sie nach API-Name aufgeteilte Transaktionen als Liniendiagramm:

[![Screenshot eines Metrikliniendiagramms für nach API-Name aufgeteilte Speichertransaktionen](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Unten sehen Sie nach Antworttyp aufgeteilte Transaktionen als großes Balkendiagramm:

[![Screenshot eines großen Metrikbalkendiagramms für nach Antworttyp aufgeteilte Speichertransaktionen](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Unten sehen Sie die durchschnittliche Latenz als Punktdiagramm:

[![Screenshot eines Metrikpunktdiagramms für die Speicherlatenz](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Diagrammeinstellungen

Ersteller können Diagrammeinstellungen verwenden, um die für die Diagrammachsen verwendeten Felder sowie die Achseneinheiten, eine benutzerdefinierte Formatierung, Bereiche, Gruppierungsverhalten, Legenden und Datenreihenfarben anzupassen.

### <a name="the-settings-tab"></a>Registerkarte „Settings“ (Einstellungen)

Die Registerkarte „Einstellungen“ steuert Folgendes:

- Die Achseneinstellungen, einschließlich der zu verwendenden Felder, sowie die benutzerdefinierte Formatierung, die es Benutzern ermöglicht, das Zahlenformat für die Achsenwerte und benutzerdefinierte Bereiche festzulegen
- Die Gruppierungseinstellungen, einschließlich der zu verwendenden Felder, sowie die Einschränkungen vor Erstellung einer Gruppe der Kategorie „Sonstige“
- Legendeneinstellungen, einschließlich der unten anzuzeigenden Metriken (Reihenname, Farben und Zahlen), und/oder eine Legende (Reihenname und Farbe)

![Screenshot der Diagrammeinstellungen](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Benutzerdefinierte Formatierung

Zu den Formatierungsoptionen für Zahlen gehören die folgenden:

| Formatierungsoption             | Erläuterung                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Die Einheiten für die Spalte. Dies sind verschiedene Optionen u. a. für Prozentsatz, Anzahl, Zeit, Byte, Anzahl/Zeit und Byte/Zeit. Beispielsweise kann die Einheit für den Wert 1.234 auf Millisekunden festgelegt werden, und er wird dann als 1,234 s gerendert.                                  |
| `Style`                      | Das zu rendernde Format: Dezimalwert, Währung, Prozentwert                                               |
| `Show group separator`       | Kontrollkästchen zum Anzeigen von Gruppentrennzeichen. Rendert 1.234 in den USA als 1,234.                                    |
| `Minimum integer digits`     | Mindestanzahl zu verwendender Vorkommastellen (Standardwert: 1)                                                   |
| `Minimum fractional digits`  | Mindestanzahl zu verwendender Nachkommastellen (Standardwert: 0)                                                |
| `Maximum fractional digits`  | Maximale Anzahl zu verwendender Nachkommastellen                                                            |
| `Minimum significant digits` | Mindestanzahl zu verwendender signifikanter Stellen (Standardwert: 1)                                               |
| `Maximum significant digits` | Maximale Anzahl zu verwendender signifikanter Stellen                                                           |

![Screenshot der X-Achseneinstellungen](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Die Registerkarte „Reiheneinstellungen“

Mithilfe der Registerkarte „Reiheneinstellungen“ können Sie die Bezeichnungen und Farben anpassen, die für eine Reihe im Diagramm angezeigt werden.

- Das `Series name`-Feld wird verwendet, um nach Übereinstimmungen einer Reihe in den Daten zu suchen. Wird eine gefunden, werden die Anzeigebezeichnung und die Farbe angezeigt.
- Das `Comment`-Feld ist hilfreich für Vorlagenersteller, da dieser Kommentar von Übersetzern verwendet werden kann, um die Anzeigebezeichnungen zu lokalisieren.

![Screenshot der Registerkarte „Reiheneinstellungen“](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Kachelvisualisierungen](workbooks-tile-visualizations.md)
- Weitere Informationen zu [Interaktiven Arbeitsmappen](workbooks-interactive.md)