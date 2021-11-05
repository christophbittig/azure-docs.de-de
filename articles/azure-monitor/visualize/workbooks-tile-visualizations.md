---
title: Kachelvisualisierungen für Azure Monitor-Arbeitsmappen
description: Erfahren Sie mehr über die zahlreichen Kachelvisualisierungen für Azure Monitor-Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: a2900b055f95372080d73b80e794b3b5d909db06
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069955"
---
# <a name="tile-visualizations"></a>Kachelvisualisierungen

Kacheln sind eine hilfreiche Möglichkeit zum Präsentieren von Zusammenfassungsdaten in Arbeitsmappen. Die Abbildung unten veranschaulicht einen allgemeinen Anwendungsfall für Kacheln mit einer Zusammenfassung auf App-Ebene über einem detaillierten Raster.

[![Screenshot einer Ansicht mit Zusammenfassung in einer Kachel](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

In Kacheln in Arbeitsmappen können ein Titel, Untertitel, großer Text, Symbole, metrikbasierte Farbverläufe, Sparklines/Sparkbars, Fußzeile usw. angezeigt werden.

## <a name="adding-a-tile"></a>Hinzufügen einer Kachel

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Legen Sie die Größe auf **Voll** fest.
6. Legen Sie die Visualisierung auf **Kacheln** fest.
7. Wählen Sie die Schaltfläche **Kacheleinstellungen** aus, um den Bereich der Einstellungen zu öffnen.
    1. Legen Sie unter *Titel* Folgendes fest:
        * Spalte verwenden: `name`.
    2. Legen Sie unter *Links* Folgendes fest:
        * Spalte verwenden: `Requests`.
        * Säulenrenderer: `Big Number`.
        * Farbpalette: `Green to Red`.
        * Mindestwert: `0`.
    3. Legen Sie unter *Unten* Folgendes fest:
        * Spalte verwenden: `appName`.
8. Wählen Sie unten im Bereich die Schaltfläche **Speichern und schließen** aus.

[![Screenshot der Kachelzusammenfassungsansicht mit den oben angegebenen Einstellungen für Abfrage und Kacheln](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Die Kacheln im Lesemodus:

[![Screenshot der Kachelzusammenfassungsansicht im Lesemodus](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Sparklines in Kacheln

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Fügen Sie einen Zeitbereichsparameter mit dem Namen `TimeRange` hinzu.
    1. Wählen Sie **Hinzufügen** und *Parameter hinzufügen* aus.
    2. Wählen Sie im Parametersteuerelement die Option **Parameter hinzufügen** aus.
    3. Geben Sie im Feld *Parametername* die Zeichenfolge `TimeRange` ein, und wählen Sie `Time range picker` als *Parametertyp* aus.
    4. Wählen Sie oben im Bereich **Speichern** und dann im Parametersteuerelement **Bearbeitung abgeschlossen** aus.
3. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus, um ein Protokollabfrage-Steuerelement unter dem Parametersteuerelement hinzuzufügen.
4. Wählen Sie für den Abfragetyp **Protokoll** aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
5. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Wählen Sie **Run Query** (Abfrage ausführen) aus. (Achten Sie darauf, `TimeRange` auf einen Wert Ihrer Wahl festzulegen, bevor Sie die Abfrage ausführen.)
7. Legen Sie *Visualisierung* auf „Kacheln“ fest.
8. Legen Sie *Größe* auf „Voll“ fest.
9. Wählen Sie **Kacheleinstellungen** aus.
    1. Legen Sie unter *Kachel* Folgendes fest:
        * Spalte verwenden: `name`.
    2. Legen Sie unter *Subtile* (Unterkachel) Folgendes fest:
        *  Spalte verwenden: `appNAme`.
    3. Legen Sie unter *Links* Folgendes fest:
        *  Spalte verwenden: `Requests`.
        * Säulenrenderer: `Big Number`.
        * Farbpalette: `Green to Red`.
        * Mindestwert: `0`.
    4. Legen Sie unter *Unten* Folgendes fest:
        * Spalte verwenden: `Trend`.
        * Säulenrenderer: `Spark line`.
        * Farbpalette: `Green to Red`.
        * Mindestwert: `0`.
10. Wählen Sie unten im Bereich **Speichern und schließen** aus.

![Screenshot der Kachelvisualisierung mit einer Sparkline](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Kachelgrößen

Der Ersteller hat die Möglichkeit, die Kachelbreite in den Kacheleinstellungen festzulegen.

* `fixed` (Standard)

    Im Standardverhalten weisen Kacheln die gleiche feste Breite auf: ca. 160 Pixel zzgl. Leerraum um die Kacheln.

    ![Screenshot von Kacheln mit fester Breite](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Jede Kachel wird entsprechend dem Inhalt verkleinert oder vergrößert, die Kacheln sind jedoch auf die Breite der Kachelansicht beschränkt (ohne horizontales Scrollen).

    ![Screenshot von Kacheln mit automatischer Breite](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Jede Kachel weist immer die vollständige Breite der Kachelansicht mit einer Kachel pro Zeile auf.

     ![Screenshot von Kacheln in voller Breite](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Nächste Schritte

* Kacheln unterstützen auch den Renderer „Zusammengesetzter Balken“. Weitere Informationen finden Sie [Dokumentation zu zusammengesetzten Balken](workbooks-composite-bar.md).
* Weitere Informationen zu Zeitparametern wie `TimeRange` finden Sie in der [Dokumentation zu Arbeitsmappen-Zeitparametern](workbooks-time.md).