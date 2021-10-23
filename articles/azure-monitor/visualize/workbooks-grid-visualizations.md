---
title: Rastervisualisierungen für Azure Monitor-Arbeitsmappen
description: Erfahren Sie mehr über die zahlreichen Rastervisualisierungen für Azure Monitor-Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 91b0933102c166657c237e7a7c9511a22a28de5c
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132491"
---
# <a name="grid-visualizations"></a>Rastervisualisierungen

Raster und Tabellen werden häufig genutzt, um Benutzern Daten zu präsentieren. In Arbeitsmappen können Benutzer die Spalten des Rasters individuell formatieren, um eine attraktive Benutzeroberfläche für ihre Berichte zu schaffen.

Das unten stehende Beispiel veranschaulicht ein Raster, in dem komplexe Informationen mithilfe von Symbolen, Wärmebildern und Sparkbars präsentiert werden. Die Arbeitsmappe bietet auch eine Sortierfunktion, ein Suchfeld sowie eine Schaltfläche zum Wechseln zur Analyse.

[![Screenshot eines auf Protokolldaten basierenden Rasters](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Hinzufügen eines auf Protokolldaten basierenden Rasters

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement **Bearbeiten** klicken.
2. Klicken Sie auf den Link **Abfrage hinzufügen**, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein (z. B. VMs, deren Arbeitsspeicher unter einem Schwellenwert liegt).
5. Legen Sie die Visualisierung auf **Raster** fest.
6. Legen Sie ggf. weitere Parameter fest, beispielsweise Zeitraum, Größe, Farbpalette und Legende.

[![Screenshot für Abfrage eines auf Protokolldaten basierenden Rasters](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parameter des Diagramms für Protokolldaten

| Parameter | Erklärung | Beispiel |
| ------------- |:-------------|:-------------|
| `Query Type` | Der Typ der zu verwendenden Abfrage | Protokoll, Azure Resource Graph usw. |
| `Resource Type` | Der Typ der Zielressource | Application Insights, Log Analytics oder Azure |
| `Resources` | Mehrere Ressourcen, von denen der Metrikwert abgerufen werden soll | MyApp1 |
| `Time Range` | Das Zeitfenster für die Anzeige des Diagramms für Protokolldaten | Letzte Stunde, Letzte 24 Stunden usw. |
| `Visualization` | Die zu verwendende Visualisierung. | Raster |
| `Size` | Die vertikale Größe des Steuerelements. | Klein, mittel, groß oder vollständig |
| `Query` | Eine beliebige KQL-Abfrage, die Daten in dem von der Diagrammvisualisierung erwarteten Format zurückgibt. | _requests \| summarize Requests = count() by name_ |

## <a name="simple-grid"></a>Einfaches Raster

Arbeitsmappen können KQL-Ergebnisse als einfache Tabelle rendern. Das folgende Raster zeigt die Anzahl der Anforderungen und eindeutigen Benutzer pro Anforderungstyp in einer App.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Screenshot eines auf Protokolldaten basierenden Rasters im Bearbeitungsmodus](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Rasterformatierung

Zwar zeigt eine einfache Tabelle Daten an, doch ist sie schwer zu lesen, und Erkenntnisse sind nicht immer offensichtlich. Durch Formatieren des Rasters können die Daten leichter zu lesen und zu interpretieren sein.

Nachfolgend ist das gleiche Raster aus dem vorherigen Abschnitt mit Wärmebildformatierung zu sehen.

[![Screenshot eines auf Protokolldaten basierenden Rasters mit Spalten in Wärmebildformatierung](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Hier sehen Sie das gleiche Raster mit Balkenformatierung: [![Screenshot eines auf Protokolldaten basierenden Rasters mit Spalten in Balkenformatierung](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Formatieren einer Rasterspalte

1. Wählen Sie auf der Symbolleiste für das Abfragesteuerelement die Schaltfläche **Spalteneinstellung** aus.
2. Wählen Sie unter *Spalteneinstellungen bearbeiten* die zu formatierende Spalte aus.
3. Wählen Sie einen Säulenrenderer (z. B. Wärmebild, Balken, Balken darunter usw.) sowie zugehörige Einstellungen zum Formatieren der Spalte aus.

Im folgenden Beispiel ist die Formatierung der Spalte *Request* „Anforderung“ als Balken zu sehen:

[![Screenshot eines auf Protokolldaten basierenden Rasters mit der Anforderungsspalte im Balkenformat](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Säulenrenderer

| Säulenrenderer | Erläuterung | Zusätzliche Optionen |
|:------------- |:-------------|:-------------|
| `Automatic` | Die Standardeinstellung. Hiermit wird der am besten geeignete Renderer basierend auf dem Spaltentyp verwendet.  |  |
| `Text` | Rendert die Spaltenwerte als Text. | |
| `Right Aligned` | Ähnelt Text, jedoch mit rechtsbündiger Ausrichtung. | |
| `Date/Time` | Rendert eine lesbare Datum/Uhrzeit-Zeichenfolge. | |
| `Heatmap` | Verwendet Farben für die Rasterzellen basierend auf dem jeweiligen Wert. | Farbpalette und minimaler/maximaler Wert für Skalierung. |
| `Bar` | Rendert einen Balken neben der Zelle basierend auf dem Wert der Zelle. | Farbpalette und minimaler/maximaler Wert für Skalierung. |
| `Bar underneath` | Rendert einen Balken nahe dem unteren Rand der Zelle basierend auf dem Wert der Zelle. | Farbpalette und minimaler/maximaler Wert für Skalierung. |
| `Composite bar` | Rendert einen zusammengesetzten Balken anhand der angegebenen Spalten in dieser Zeile. Ausführliche Informationen finden Sie unter [Zusammengesetzter Balken](workbooks-composite-bar.md). | Spalten mit entsprechenden Farben zum Rendern des Balkens und eine Beschriftung, die am oberen Rand des Balkens angezeigt werden soll. |
| `Spark bars` | Rendert einen Sparkbar in der Zelle basierend auf den Werten eines dynamischen Arrays in der Zelle. Ein Beispiel ist die Spalte „Trend“ im oben gezeigten Screenshot. | Farbpalette und minimaler/maximaler Wert für Skalierung. |
| `Spark lines` | Rendert eine Sparkline in der Zelle basierend auf den Werten eines dynamischen Arrays in der Zelle. | Farbpalette und minimaler/maximaler Wert für Skalierung. |
| `Icon` | Rendert Symbole basierend auf den Textwerten in der Zelle. Unterstützte Werte: `cancelled`, `critical`, `disabled`, `error`, `failed`, `info`, `none`, `pending`, `stopped`, `question`, `success`, `unknown`, `warning` `uninitialized`, `resource`, `up`, `down`, `left`, `right`, `trendup`, `trenddown`, `4`, `3`, `2`, `1`, `Sev0`, `Sev1`, `Sev2`, `Sev3`, `Sev4`, `Fired`, `Resolved`, `Available`, `Unavailable`, `Degraded`, `Unknown` und `Blank`.|  |
| `Link` | Rendert einen Link, der beim Klicken darauf eine konfigurierbare Aktion ausführt. Verwenden Sie diese Option, wenn Sie möchten, dass das Element *nur* ein Link ist.  Alle anderen Typen können *auch* ein Link sein. Verwenden Sie dazu die Einstellung `Make this item a link`. Weitere Informationen finden Sie im folgenden Abschnitt [Linkaktionen](#link-actions). |  |
| `Location` | Rendert einen benutzerfreundlichen Azure-Regionsnamen basierend auf einer Regions-ID. |  |
| `Resource type` | Rendert eine benutzerfreundliche Ressourcentyp-Zeichenfolge basierend auf einer Ressourcentyp-ID.  |  |
| `Resource` | Rendert einen benutzerfreundlichen Ressourcennamen und einen Link basierend auf einer Ressourcen-ID.  | Option zum Anzeigen des Symbols für den Ressourcentyp  |
| `Resource group` | Rendert einen benutzerfreundlichen Ressourcengruppennamen und einen Link basierend auf einer Ressourcengruppen-ID. Wenn der Wert der Zelle keine Ressourcengruppe ist, wird er in eine konvertiert.  | Option zum Anzeigen des Symbols für die Ressourcengruppe  |
| `Subscription` | Rendert einen benutzerfreundlichen Abonnementnamen und einen Link basierend auf einer Abonnement-ID. Wenn der Wert der Zelle kein Abonnement ist, wird er in eines konvertiert.  | Option zum Anzeigen des Abonnementsymbols  |
| `Hidden` | Blendet die Spalte im Raster aus. Dies ist nützlich, wenn die Standardabfrage mehr Spalten als benötigt zurückgibt, „project-away“ jedoch nicht erwünscht ist. |  |

### <a name="link-actions"></a>Linkaktionen

Wenn der `Link`-Renderer ausgewählt oder das Kontrollkästchen *Dieses Element als Link festlegen* aktiviert ist, kann der Autor eine Linkaktion konfigurieren, die bei Auswahl der Zelle ausgeführt wird. Dadurch wird der Benutzer in der Regel zu einer anderen Ansicht mit Kontext aus der Zelle geführt, oder es wird eine URL geöffnet.

### <a name="custom-formatting"></a>Benutzerdefinierte Formatierung

In Arbeitsmappen können Benutzer auch die Zahlenformatierung für Zellwerte festlegen. Dazu wird auf das Kontrollkästchen *Benutzerdefinierte Formatierung* (falls verfügbar) geklickt.

| Formatierungsoption | Erläuterung |
|:------------- |:-------------|
| `Units` | Die Einheiten für die Spalte. Dies sind verschiedene Optionen für Prozentsatz, Anzahl, Zeit, Byte, Anzahl/Zeit, Bytes/Zeit usw. Beispielsweise kann die Einheit für den Wert 1234 auf Millisekunden festgelegt werden, und er wird dann als 1,234 s gerendert. |
| `Style` | Das zu rendernde Format: Dezimalwert, Währung, Prozentwert. |
| `Show group separator` | Kontrollkästchen zum Anzeigen von Gruppentrennzeichen. Rendert 1.234 in den USA als 1,234. |
| `Minimum integer digits` | Mindestanzahl zu verwendender Vorkommastellen (Standardwert: 1) |
| `Minimum fractional digits` | Mindestanzahl zu verwendender Nachkommastellen (Standardwert: 0)  |
| `Maximum fractional digits` | Maximale Anzahl zu verwendender Nachkommastellen |
| `Minimum significant digits` | Mindestanzahl zu verwendender signifikanter Stellen (Standardwert: 1) |
| `Maximum significant digits` | Maximale Anzahl zu verwendender signifikanter Stellen. |
| `Custom text for missing values` | Wenn ein Datenpunkt keinen Wert aufweist, wird dieser benutzerdefinierte Text anstelle eines leeren Werts angezeigt. |

### <a name="custom-date-formatting"></a>Benutzerdefinierte Datumsformatierung

Wenn der Autor für eine Spalte den Datum/Uhrzeit-Renderer festgelegt hat, kann er mithilfe des Kontrollkästchens *Benutzerdefinierte Datumsformatierung* Optionen für die benutzerdefinierte Datumsformatierung angeben.

| Formatierungsoption | Erläuterung |
|:------------- |:-------------|
| `Style` | Option zum Rendern eines Datums im kurzen, langen, vollständigen Format oder einer Uhrzeit im kurzen oder langen Zeitformat. |
| `Show time as` | Ermöglicht dem Autor die Wahl zwischen der Anzeige der Uhrzeit als Ortszeit (Standard) oder als UTC-Zeit. Je nach ausgewähltem Datumsformat werden die UTC-/Zeitzoneninformationen möglicherweise nicht angezeigt. |

## <a name="custom-column-width-setting"></a>Benutzerdefinierte Einstellung der Spaltenbreite

Der Autor kann die Breite einer beliebigen Spalte im Raster mithilfe des Felds *Benutzerdefinierte Spaltenbreite* unter *Spalteneinstellungen* anpassen.

![Screenshot der Spalteneinstellungen mit rot umrahmtem Feld „Benutzerdefinierte Spaltenbreite“](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Wenn das Feld leer bleibt, wird die Breite automatisch anhand der Anzahl der Zeichen in der Spalte und der Anzahl der sichtbaren Spalten bestimmt. Die Standardeinheit ist „ch“ (Zeichen).

Wenn Sie die blaue Schaltfläche **(Aktuelle Breite)** in der Beschriftung auswählen, wird das Textfeld mit der aktuellen Breite der ausgewählten Spalte aufgefüllt. Wenn im Feld für die benutzerdefinierte Breite ein Wert ohne Maßeinheit angegeben ist, wird die Standardeinheit verwendet.

Folgende Maßeinheiten stehen zur Verfügung:

| Unit of measure (Maßeinheit) | Definition           |
|:--------------------|:---------------------|
| ch                  | Zeichen (Standard) |
| px                  | Pixel               |
| fr                  | Bruchteileinheiten     |
| %                   | Prozentwert           |

Eingabeüberprüfung: Wenn die Überprüfung fehlschlägt, wird unter dem Feld eine rote Anleitungsmeldung angezeigt, doch kann der Benutzer die Breite dennoch anwenden. Wenn ein Wert in der Eingabe vorhanden ist, wird er analysiert. Falls keine gültige Maßeinheit gefunden wird, wird die Standardeinheit verwendet.

Es gibt keine minimale/maximale Breite, da dies im Ermessen des Autors liegt. Das Feld für die benutzerdefinierte Spaltenbreite ist für verborgene Spalten deaktiviert.

## <a name="examples"></a>Beispiele

### <a name="spark-lines-and-bar-underneath"></a>Sparklines und Balken darunter

Das folgende Beispiel zeigt die Anzahl von Anforderungen und deren Trend nach Anforderungsname.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Screenshot eines auf Protokolldaten basierenden Rasters mit einem Balken darunter und einer Sparkline](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Wärmebild mit gemeinsamen Skalen und benutzerdefinierter Formatierung

Dieses Beispiel zeigt verschiedene Metriken der Anforderungsdauer und deren Anzahl. Der Wärmebildrenderer verwendet die in den Einstellungen festgelegten Mindestwerte oder berechnet einen minimalen und maximalen Wert für die Spalte und weist der Zelle eine Hintergrundfarbe aus der ausgewählten Palette zu, die auf dem Wert der Zelle im Verhältnis zum minimalen und maximalen Wert der Spalte basiert.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Screenshot eines auf Protokolldaten basierenden Rasters mit einem Wärmebild, das eine gemeinsame Skala für alle Spalten aufweist](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

Im obigen Beispiel wird eine gemeinsame Palette (grün oder rot) und Skala für die Farben der Spalten (Mittel, Median, p80, p95 und p99) verwendet. Eine separate Palette (blau) wird für die Anforderungsspalte verwendet.

#### <a name="shared-scale"></a>Gemeinsame Skala

So erhalten Sie eine gemeinsame Skala:

1. Wählen Sie mithilfe regulärer Ausdrücke mehrere Spalten aus, auf die eine Einstellung angewendet werden soll. Legen Sie beispielsweise den Spaltennamen auf `Mean|Median|p80|p95|p99` fest, um alle auszuwählen.
2. Löschen Sie die Standardeinstellungen für die einzelnen Spalten.

Dadurch wird die neue Einstellung für mehrere Spalten angewendet und eine gemeinsame Skala eingefügt.

[![Screenshot einer Einstellung für das auf Protokolldaten basierende Raster zum Erstellen einer gemeinsamen Skala für alle Spalten](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Symbole zur Darstellung des Status

Das folgende Beispiel zeigt den benutzerdefinierten Status von Anforderungen basierend auf der Dauer „p95“.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Screenshot eines auf Protokolldaten basierenden Rasters mit einem Wärmebild, das eine gemeinsame Skala für alle Spalten aufweist und mithilfe der obigen Abfrage erstellt wurde](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Folgende Symbolnamen werden unterstützt: `cancelled`, `critical`, `disabled`, `error`, `failed`, `info`, `none`, `pending`, `stopped`, `question`, `success`, `unknown`, `warning` `uninitialized`, `resource`, `up`, `down`, `left`, `right`, `trendup`, `trenddown`, `4`, `3`, `2`, `1`, `Sev0`, `Sev1`, `Sev2`, `Sev3`, `Sev4`, `Fired`, `Resolved`, `Available`, `Unavailable`, `Degraded`, `Unknown` und `Blank`.

### <a name="using-thresholds-with-links"></a>Verwenden von Schwellenwerten mit Links

Die folgenden Anweisungen zeigen, wie Sie Schwellenwerte mit Links verwenden, um Symbole zuzuweisen und verschiedene Arbeitsmappen zu öffnen. Mit jedem Link im Raster wird eine andere Arbeitsmappenvorlage für diese Application Insights-Ressource geöffnet.

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie das Symbolleistenelement *Bearbeiten* auswählen.
2. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus.
3. Ändern Sie die *Datenquelle* in „JSON“ und die *Visualisierung* in „Raster“.
4. Geben Sie die folgende Abfrage ein.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Führen Sie die Abfrage aus.
6. Wählen Sie **Spalteneinstellungen** aus, um die Einstellungen zu öffnen.
7. Wählen Sie unter *Spalten* die Option „Name“ aus.
8. Wählen Sie unter *Säulenrenderer* die Option „Schwellenwerte“ aus.
9.  Geben Sie die folgenden *Schwellenwerteinstellungen* ein, und wählen Sie sie aus.
   
    | Operator | Wert   | Symbole   |
    |----------|---------|---------|
    | ==       | warning | Warnung |
    | ==       | error   | Fehler  |

    ![Screenshot der Registerkarte „Spalteneinstellungen bearbeiten“ mit den oben angegebenen Einstellungen](./media/workbooks-grid-visualizations/column-settings.png)

    Behalten Sie die Standardzeile unverändert bei. Sie können einen beliebigen Text eingeben. Die Spalte „Text“ nimmt das Zeichenfolgenformat als Eingabe an und füllt sie mit dem Spaltenwert und der Einheit (falls angegeben). Wenn es sich beim Spaltenwert beispielsweise um eine Warnung handelt, kann der Text „{0} {1} Link!“ lauten und wird als „Warnung Link!“ angezeigt.
10. Aktivieren Sie das Kontrollkästchen *Dieses Element als Link festlegen*.
    1. Wählen Sie unter *Zu öffnende Ansicht* die Option „Arbeitsmappe (Vorlage)“ aus.
    2. Wählen Sie unter *Linkwert stammt aus* die Option „Link“ aus.
    3. Aktivieren Sie das Kontrollkästchen *Link in Kontextblatt öffnen*.
    4. Wählen Sie unter *Einstellungen für Arbeitsmappenlinks* die folgenden Einstellungen aus:
        1. Wählen Sie unter *Vorlagen-ID stammt aus* die Option „Spalte“ aus.
        2. Wählen Sie unter *Spalte* die Option „Link“ aus.

    ![Screenshot der Linkeinstellungen mit den oben angegebenen Einstellungen](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Wählen Sie unter *Spalten* die Option „Link“ aus. Klicken Sie unter „Einstellungen“ neben *Säulenrenderer* auf **(Spalte ausblenden)** .
1. Um den Anzeigenamen der Spalte „Name“ zu ändern, wählen Sie die Registerkarte **Beschriftungen** aus. Geben Sie in der Zeile mit „Name“ als *Spalten-ID* unter „*Spaltenbeschriftung“ den gewünschten Namen ein.
2. Wählen Sie **Übernehmen** aus.

![Screenshot von Schwellenwerten im Raster mit den oben angegebenen Einstellungen](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Bruchteileinheiten und Prozentsätze

Die Bruchteileinheit (fr) ist eine häufig verwendete dynamische Maßeinheit in verschiedenen Rastertypen. Wenn sich die Fenstergröße/Auflösung ändert, ändert sich auch die fr-Breite.

Der folgende Screenshot zeigt eine Tabelle mit acht Spalten, die alle die gleiche Breite von 1fr aufweisen. Wenn sich die Fenstergröße ändert, ändert sich die Breite der einzelnen Spalten proportional.

[![Screenshot von Spalten im Raster mit dem Wert „1fr“ für die Spaltenbreite](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Die folgende Abbildung zeigt dieselbe Tabelle, jedoch mit dem Unterschied, dass die erste Spalte auf eine Breite von 50 % festgelegt ist. Dadurch wird die Spalte dynamisch auf die Hälfte der Gesamtbreite des Rasters festgelegt. Wenn Sie die Größe des Fensters ändern, wird die Breite von 50 % beibehalten, es sei denn, die Fenstergröße wird zu klein. Diese dynamischen Spalten weisen eine Mindestbreite entsprechend ihres Inhalts auf. Die verbleibenden 50 % des Rasters werden auf die insgesamt acht Bruchteileinheiten aufgeteilt. Die Spalte „Kind“ (Art) im folgenden Screenshot ist auf „2fr“ festgelegt, sodass sie ein Viertel des verbleibenden Bereichs belegt. Da die anderen Spalten jeweils auf „1fr“ festgelegt sind, nehmen Sie jeweils ein Achtel der rechten Hälfte des Rasters ein.

[![Screenshot von Spalten im Raster mit einem Wert für die Spaltenbreite von 50 % und jeweils „1fr“ für die restlichen Spalten](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Das Kombinieren von Breiten mit fr-, %-, px- und ch-Werten ist möglich und funktioniert ähnlich wie in den vorherigen Beispielen. Bei den Breiten, die mit den statischen Einheiten (ch und px) festgelegt sind, handelt es sich um feste Konstanten, die sich auch bei einer Änderung des Fensters oder der Auflösung nicht ändern. Die mit % festgelegten Spalten nehmen ihren prozentualen Anteil basierend auf der Gesamtbreite des Rasters ein (aufgrund vorheriger Mindestbreiten möglicherweise nicht exakt). Die mit fr festgelegten Spalten verteilen sich basierend auf der Anzahl der ihnen zugewiesenen Bruchteileinheiten auf den verbleibenden Rasterbereich.

[![Screenshot von Spalten im Raster mit einer Reihe verschiedener Einheiten für die Breite](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie eine [Struktur in Arbeitsmappen](workbooks-tree-visualizations.md) erstellen.
* Erfahren Sie, wie Sie [Textparameter in Arbeitsmappen](workbooks-text.md) erstellen.
