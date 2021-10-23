---
title: Renderer für zusammengesetzte Balken in der Azure Monitor-Arbeitsmappe
description: Erfahren Sie mehr über Visualisierungen mit dem Renderer für zusammengesetzte Balken in der Azure Monitor-Arbeitsmappe.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.openlocfilehash: 1b2946d4def2004d9d620eb253c733fdf31c39cf
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132529"
---
# <a name="composite-bar-renderer"></a>Renderer für zusammengesetzte Balken

Die Arbeitsmappe ermöglicht das Rendern von Daten mithilfe eines zusammengesetzten Balkens – eines Balkens, der aus mehreren Balken besteht.

Die folgende Abbildung zeigt den zusammengesetzten Balken für den Datenbankstatus, der angibt, wie viele Server online, offline und im Wiederherstellungszustand sind.

![Screenshot des zusammengesetzten Balkens für den Datenbankstatus.](./media/workbooks-composite-bar/database-status.png)

Der Renderer für zusammengesetzte Balken wird für Raster-, Kachel- und Diagrammvisualisierungen unterstützt.

## <a name="adding-composite-bar-renderer"></a>Hinzufügen des Renderers für zusammengesetzte Balken

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie das Symbolleistenelement *Bearbeiten* auswählen.
2. Wählen Sie *Hinzufügen* und dann *Abfrage hinzufügen* aus.
3. Legen Sie für die *Datenquelle* „JSON“ und die *Visualisierung* „Raster“ fest.
4. Fügen Sie die folgenden JSON-Daten hinzu.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Führen Sie die Abfrage aus.
6. Wählen Sie **Spalteneinstellungen** aus, um die Einstellungen zu öffnen.
7. Wählen Sie in *Spalten* „Gesamt“ aus, und wählen Sie „Zusammengesetzter Balken“ für *Säulenrenderer* aus.
8. Legen Sie unter *Einstellungen für zusammengesetzte Balken* die folgenden Einstellungen fest.

| Spaltenname | Color        |
|-------------|--------------|
| online      | Grün        |
| Wiederherstellen  | Gelb       |
| Offline     | Rot (hell) |

9. Fügen Sie eine Bezeichnung hinzu:`["online"] of ["total"] are healthy`
10. In den Spalteneinstellungen für online, offline und Wiederherstellung können Sie den Säulenrenderer auf „Ausgeblendet“ festlegen (optional).
11. Wählen Sie oben *Bezeichnungen* aus, und aktualisieren Sie die Bezeichnung für die gesamte Spalte mit „Datenbankstatus“ (optional).
12. Wählen Sie **Anwenden** aus.

Die Einstellungen für zusammengesetzte Balken sehen wie im folgenden Screenshot aus:

![Screenshot der Einstellungen für zusammengesetzte Balken mit den oben beschriebenen Einstellungen.](./media/workbooks-composite-bar/composite-bar-settings.png)

Der zusammengesetzte Balken mit den oben aufgeführten Einstellungen:

![Screenshot des zusammengesetzten Balkens.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Einstellungen für zusammengesetzte Balken

Wählen Sie den Spaltennamen und die zugehörige Farbe aus, um diese Spalte in dieser Farbe als Teil des zusammengesetzten Balkens zu rendern. Sie können Zeilen nach oben und unten einfügen, löschen und verschieben.

### <a name="label"></a>Bezeichnung

Die Bezeichnung des zusammengesetzten Balkens wird am oberen Rand des zusammengesetzten Balkens angezeigt. Sie können eine Mischung aus statischem Text, Spalten und Parametern verwenden.  Wenn die Bezeichnung leer ist, wird der Wert der aktuellen Spalten als Bezeichnung angezeigt. Wenn das Bezeichnungsfeld wie im vorherigen Beispiel schwarz gelassen wird, wird der Wert der gesamten Spalten angezeigt.

Beziehen Sie sich auf Spalten mit `["columnName"]`.

Beziehen Sie sich auf Parameter mit `{paramName}`.

Sowohl bei Spalten- als auch Parameternamen wird die Groß-/Kleinschreibung beachtet. Sie können Bezeichnungen auch zu einem Link machen, indem Sie „Dieses Element zu einem Link machen“ auswählen und dann Linkeinstellungen hinzufügen.

### <a name="aggregation"></a>Aggregation

Aggregationen sind für „Struktur/Gruppieren nach“-Visualisierungen nützlich. Die Daten für eine Spalte für die Gruppenzeile werden durch den Aggregationssatz für diese Spalte festgelegt. Es gibt drei Arten von Aggregationen, die für zusammengesetzte Balken anwendbar sind: „Keine“, „Summe“ und „Erben“.

So fügen Sie „Gruppieren nach“-Einstellungen hinzu:

1. Wechseln Sie in den Spalteneinstellungen zu der Spalte, der Sie Einstellungen hinzufügen möchten.
2. Wählen Sie in *„Struktur/Gruppieren nach“-Einstellungen* unter *Strukturtyp* die Option **Gruppieren nach** aus.
3. Wählen Sie das Feld aus, nach dem Sie gruppieren möchten.

![Screenshot der „Gruppieren nach“-Einstellungen.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Keine

Keine Aggregation bedeutet, dass für diese Spalte keine Ergebnisse für die Gruppenzeilen angezeigt werden.

![Screenshot des zusammengesetzten Balkens ohne Aggregation.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>SUM

Wenn Aggregation als „Summe“ festgelegt wird, zeigt die Spalte in der Gruppenzeile den zusammengesetzten Balken unter Verwendung der Summe der zum Rendern verwendeten Spalten an. In der Bezeichnung wird auch die Summe der Spalten verwendet, auf die damit verwiesen wird.

Im Beispiel ist für online, offline und Wiederherstellen die maximale Aggregation festgelegt, und die Aggregation für die Gesamtspalte ist die Summe.

![Screenshot des zusammengesetzten Balkens mit Summenaggregation.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Erben

Wenn Aggregation als „Erben“ festgelegt wird, zeigt die Spalte in der Gruppenzeile den zusammengesetzten Balken unter Verwendung der vom Benutzer für die zum Rendern verwendeten Spalten festgelegte Aggregation an. In den in der Bezeichnung verwendeten Spalten wird auch der vom Benutzer festgelegte Aggregationssatz verwendet. Wenn der aktuelle Säulenrenderer ein zusammengesetzter Balken ist und in der Bezeichnung darauf verwiesen wird (wie „Gesamt“ im obigen Beispiel), wird „Summe“ als Aggregation für diese Spalte verwendet.

Im Beispiel ist für online, offline und Wiederherstellen die maximale Aggregation festgelegt, und die Aggregation für die Gesamtspalte ist Erben.

![Screenshot des zusammengesetzten Balkens mit Erbaggregation.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Sortieren

Bei Rastervisualisierungen funktioniert das Sortieren der Zeilen für die Spalte mit dem Renderer für zusammengesetzte Balken auf Basis des Werts, der die Summe der Spalten ist, die zum dynamischen Rendern des Computers des zusammengesetzten Balkens verwendet werden. In den vorherigen Beispielen ist der für die Sortierung verwendete Wert die Summe der Online-, Wiederherstellungs- und Offlinespalten für die jeweilige Zeile.

## <a name="tiles-visualization"></a>Kachelvisualisierung

1. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus.
2. Ändern Sie die Datenquelle in JSON, und geben Sie die Daten aus dem [vorherigen Beispiel](#adding-composite-bar-renderer) ein.
3. Ändern Sie die Visualisierung in *Kacheln*.
4. Führen Sie die Abfrage aus.
5. Wählen Sie **Kacheleinstellungen** aus.
6. Wählen Sie *Links* in den Kachelfeldern aus.
7. Geben Sie unter *Feldeinstellungen* die folgenden Einstellungen ein.
    1. Spalte verwenden: „Server“.
    2. Säulenrenderer: „Text“.
8. Wählen Sie *Unten* in den Kachelfeldern aus.
9. Geben Sie unter *Feldeinstellungen* die folgenden Einstellungen ein.
    1. Spalte verwenden: „Gesamt“.
    2. Säulenrenderer: „Zusammengesetzter Balken“.
    3. Legen Sie unter „Einstellungen für zusammengesetzte Balken“ die folgenden Einstellungen fest.

    | Spaltenname | Color        |
    |-------------|--------------|
    | online      | Grün        |
    | Wiederherstellen  | Gelb       |
    | Offline     | Rot (hell) |

    4. Bezeichnung hinzufügen: `["online"] of ["total"] are healthy`.
10. Klicken Sie auf **Übernehmen**.

Kacheleinstellungen für zusammengesetzte Balken:

![Screenshot der Kacheleinstellungen für zusammengesetzte Balken mit den oben beschriebenen Einstellungen](./media/workbooks-composite-bar/tiles-settings.png)

Die Kachelansicht mit zusammengesetzten Balken mit den obigen Einstellungen sieht wie folgt aus:

![Screenshot der Kacheln des zusammengesetzten Balkens.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Visualisierung von Graphen

Befolgen Sie die Anweisungen unten, um einen Renderer für zusammengesetzte Balken für die Visualisierung von Graphen (Typ Hive-Cluster) zu erstellen.

1. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus.
2. Ändern Sie die Datenquelle in JSON, und geben Sie die Daten aus dem [vorherigen Beispiel](#adding-composite-bar-renderer) ein.
3. Ändern Sie die Visualisierung in *Graphe*.
4. Führen Sie die Abfrage aus.
5. Wählen Sie **Grapheinstellungen** aus.
6. Wählen Sie *Mitteninhalt* in „Knotenformateinstellungen“ aus.
7. Geben Sie unter *Feldeinstellungen* die folgenden Einstellungen ein.
    1. Spalte verwenden: „Gesamt“.
    2. Säulenrenderer: „Zusammengesetzter Balken“.
    3. Legen Sie unter *Einstellungen für zusammengesetzte Balken* die folgenden Einstellungen fest.

    |Spaltenname  |     Color    |
    |-------------|--------------|
    | online      | Grün        |
    | Wiederherstellen  | Gelb       |
    | Offline     | Rot (hell) |

   4. Bezeichnung hinzufügen: `["online"] of ["total"] are healthy`.
9. Geben Sie unter *Layouteinstellungen* die folgenden Einstellungen ein.
    1. Graphtyp: **Hive-Cluster**.
    2. Knoten-ID-Auswahl: „Server“.
    3. Feld „Gruppieren nach“: „Keine“.
    4. Knotengröße: 100.
    5. Abstand zwischen Hexagons: 5.
    6. Färbungstyp: **Keine**.
1. Klicken Sie auf **Übernehmen**.
    
Grapheinstellungen für zusammengesetzte Balken:

![Screenshot der Grapheinstellungen für zusammengesetzte Balken mit den oben beschriebenen Einstellungen.](./media/workbooks-composite-bar/graphs-settings.png)

Die Graphansicht mit zusammengesetzten Balken mit den obigen Einstellungen sieht wie folgt aus:

![Screenshot der Graphen mit zusammengesetzten Balken mit Hive-Clustern.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Nächste Schritte

* [Stellen](../visualize/workbooks-automate.md) Sie Arbeitsmappen mit Azure Resource Manager bereit.
* [Steuern](./workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.