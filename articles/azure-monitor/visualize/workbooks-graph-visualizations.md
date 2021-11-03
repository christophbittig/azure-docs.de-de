---
title: Graphvisualisierungen für Azure Monitor-Arbeitsmappen
description: Erfahren Sie mehr über die zahlreichen Graphvisualisierungen für Azure Monitor-Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: ac1418ee2df60a79be104f1991908dcde0de6c07
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072254"
---
# <a name="graph-visualizations"></a>Graphvisualisierungen

Arbeitsmappen unterstützen das Visualisieren beliebiger Graphs auf der Grundlage von Daten aus Protokollen, um die Beziehungen zwischen überwachten Entitäten aufzuzeigen.

Der unten stehende Graph zeigt Daten, die von einem Computer über diverse Ports an externe Computer ausgehen bzw. von externen Computern eingehen. Sie sind nach Typ gefärbt (Computer, Port, externe IP), und die Kantenstärken entsprechen dem Umfang des dazwischenliegenden Datenflusses. Die zugrunde liegenden Daten stammen aus einer KQL-Abfrage von VM-Verbindungen.

[![Screenshot einer Ansicht mit Zusammenfassung in einer Kachel](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Hinzufügen eines Graphs
1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Klicken Sie auf den Link **Abfrage hinzufügen**, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Legen Sie die Visualisierung auf **Graph** fest.
6. Wählen Sie die Schaltfläche **Grapheinstellungen** aus, um den Bereich mit Einstellungen zu öffnen.
7. Legen Sie in _Layout Fields_ (Layout-Felder) unten Folgendes fest:
    * Knoten-ID: `Id`
    * Quell-ID: `SourceId`
    * Ziel-ID: `TargetId`
    * Kantenbezeichnung: `None`
    * Kantenstärke: `Calls`
    * Knotengröße: `None`
    * Färbungstyp: `Categorical`
    * Feld für Knotenfarbe: `Kind`
    * Farbpalette: `Pastel`
8. Legen Sie oben in _Einstellungen für Knotenformat_ Folgendes fest:
    * _Inhalt oben_ – Spalte verwenden: `Name`, Säulenrenderer: `Text`
    * _Inhalt zentriert_ – Spalte verwenden: `Calls`, Säulenrenderer: `Big Number`, Farbpalette: `None`
    * _Inhalt unten_ – Spalte verwenden: `Kind`, Säulenrenderer: `Text`
9. Wählen Sie unten im Bereich die Schaltfläche _Speichern und schließen_ aus.

[![Screenshot der Kachelzusammenfassungsansicht mit der Abfrage und den Einstellungen der obigen Angaben](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Grapheinstellungen

| Einstellung         | Erklärung                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Wählt eine Spalte aus, die die eindeutige ID der Knoten im Graph bereitstellt. Der Wert der Spalte kann eine Zeichenfolge oder eine Zahl sein. |
| `Source Id`     | Wählt eine Spalte aus, die die IDs der Quellknoten für Kanten im Graph bereitstellt. Werte müssen einem Wert in der Spalte _Knoten-ID_ zugeordnet sein. |
| `Target Id`     | Wählt eine Spalte aus, die die IDs der Zielknoten für Kanten im Graph bereitstellt. Werte müssen einem Wert in der Spalte _Knoten-ID_ zugeordnet sein. |
| `Edge Label`    | Wählt eine Spalte aus, die Kantenbezeichnungen im Graph bereitstellt.                                                            |
| `Edge Size`     | Wählt eine Spalte aus, die die Metrik bereitstellt, auf der die Kantenbreiten basieren.                                |
| `Node Size`     | Wählt eine Spalte aus, die die Metrik bereitstellt, auf der die Knotenbereiche basieren.                                 |
| `Coloring Type` | Wird verwendet, um das Schema für die Knotenfärbung auszuwählen.                                                                            |

## <a name="node-coloring-types"></a>Knotenfärbungstypen

| Färbungstyp | Erläuterung |
|:------------- |:------------|
| `None`        | Alle Knoten haben die gleiche Farbe. |
| `Categorical` | Knoten werden Farben basierend auf dem Wert oder der Kategorie aus einer Spalte im Resultset zugewiesen. Im obigen Beispiel basiert die Färbung auf der Spalte _Kind_ (Art) des Resultsets. Unterstützte Paletten sind `Default`, `Pastel` und `Cool tone`.  |
| `Field Based` | Bei diesem Typ stellt eine Spalte bestimmte RGB-Werte bereit, die für den Knoten verwendet werden. Bietet größtmögliche Flexibilität, erfordert jedoch in der Regel einen größeren Aufwand für die Aktivierung.  |

## <a name="node-format-settings"></a>Einstellungen für Knotenformat

Ersteller von Graphs können die Inhalte für die verschiedenen Teile eines Knotens angeben: oben, links, zentriert, rechts und unten. Graphs können alle von Arbeitsmappen unterstützten Renderer verwenden (Text, große Zahl, Sparklines, Symbol usw.).

## <a name="field-based-node-coloring"></a>Feldbasierte Knotenfärbung

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Klicken Sie auf den Link **Abfrage hinzufügen**, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, und wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Legen Sie die Visualisierung auf *Graph* fest.
6. Wählen Sie die Schaltfläche **Grapheinstellungen** aus, um den Bereich mit Einstellungen zu öffnen.
7. Legen Sie in *Layout Fields* (Layout-Felder) unten Folgendes fest:
    * Knoten-ID: `Id`
    * Quell-ID: `SourceId`
    * Ziel-ID: `TargetId`
    * Kantenbezeichnung: `None`
    * Kantenstärke: `Calls`
    * Knotengröße: `Node`
    * Färbungstyp: `Field Based`
    * Feld für Knotenfarbe: `Color`
8. Geben Sie oben in *Einstellungen für Knotenformat* Folgendes ein.
    * Legen Sie in *Inhalt oben* Folgendes fest:
        * Spalte verwenden: `Name`
        * Säulenrenderer: `Text`
    * Legen Sie in *Inhalt zentriert* Folgendes fest:
        * Spalte verwenden: `Calls`
        * Säulenrenderer: `Big Number`
        * Farbpalette: `None`
    * Legen Sie in *Inhalt unten* Folgendes fest:
        * Spalte verwenden: `Kind`
        * Säulenrenderer: `Text`
9. Wählen Sie unten im Bereich die Schaltfläche **Speichern und schließen** aus.

[![Screenshot der Erstellung einer Graphvisualisierung mit feldbasierter Knotenfärbung](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Graphs unterstützen auch den Renderer „Zusammengesetzter Balken“. Weitere Informationen finden Sie in der [Dokumentation zu zusammengesetzten Balken](workbooks-composite-bar.md).
* Erfahren Sie mehr über die [Datenquellen](workbooks-data-sources.md), die Sie in Arbeitsmappen verwenden können.
