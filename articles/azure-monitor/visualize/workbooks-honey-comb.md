---
title: 'Azure Monitor-Arbeitsmappe: Wabenförmige Visualisierungen'
description: Hier erfahren Sie mehr über die wabenförmigen Visualisierungen für Azure Monitor-Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: ba24669f04966d9ee3cfa5af4cd677e83ad3ea7a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256608"
---
# <a name="honey-comb-visualizations"></a>Wabenförmige Visualisierungen

Wabenförmige Visualisierungen ermöglichen die Ansicht von Metriken oder Kategorien mit hoher Dichte, die optional als Cluster gruppiert werden können. Sie sind hilfreich beim visuellen Identifizieren von Hotspots und bei weiteren Untersuchungen.

Die folgende Abbildung zeigt die CPU-Auslastung virtueller Computer in zwei Abonnements. Jede Zelle stellt einen virtuellen Computer und die Farbe bzw. Bezeichnung die durchschnittliche CPU-Auslastung dar (stark ausgelastete Computer in Rot). Die virtuellen Computer werden nach Abonnement gruppiert.

[![Screenshot: CPU-Auslastung der virtuellen Computer in zwei Abonnements](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Hinzufügen einer Wabe

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement Bearbeiten klicken.
2. Klicken Sie unten auf **Hinzufügen** und dann auf *Abfrage hinzufügen*, um der Arbeitsmappe ein Steuerelement für Protokollabfragen hinzuzufügen.
3. Wählen Sie „Protokolle“ als *Datenquelle* und „Log Analytics“ als *Ressourcentyp* aus. Zeigen Sie für *Ressource* auf einen Arbeitsbereich mit einem Leistungsprotokoll für virtuelle Computer.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Führen Sie die Abfrage aus.
6. Legen Sie die *Visualisierung* auf „Graph“ fest.
7. Wählen Sie **Grapheinstellungen** aus.
    1. Legen Sie in *Layout Fields* (Layout-Felder) unten Folgendes fest:
        1. Graphtyp: **Hive-Cluster**.
        2. Knoten-ID: `Id`
        3. Gruppieren nach: `None`
        4. Knotengröße: 100.
        5. Abstand zwischen Hexagons: `5`
        6. Färbungstyp: **Wärmebild**
        7. Feld für Knotenfarbe: `CouterValue`
        8. Farbpalette: `Red to Green`.
        9. Mindestwert: `100`.
        10. Maximalwert: `2000`
    2. Legen Sie oben in *Einstellungen für Knotenformat* Folgendes fest:
        1. Inhalt oben:
            1. Spalte verwenden: `Computer`
            2. Säulenrenderer: `Text`
        9. Inhalt zentriert:
            1. Spalte verwenden: `CounterValue`
            2. Säulenrenderer: `Big Number`
            3. Farbpalette: `None`
            4. Aktivieren Sie das Kontrollkästchen *Benutzerdefiniertes Zahlenformat*.
            5. Einheiten: `Megabytes`
            6. Maximale Nachkommastellen: `1`
8. Klicken Sie unten im Bereich auf die Schaltfläche **Speichern und schließen**.

[![Screenshot: Abfragesteuerung, Grapheinstellungen und Honigwabe mit der obigen Abfrage und den Einstellungen](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Layouteinstellungen für wabenförmige Visualisierung

| Einstellung | Erklärung |
|:------------- |:-------------|
| `Node Id` | Hiermit wird eine Spalte ausgewählt, die die eindeutige ID der Knoten bereitstellt. Der Wert der Spalte kann eine Zeichenfolge oder eine Zahl sein. |
| `Group By Field` | Hiermit wird die Spalte ausgewählt, in der die Knoten gruppiert werden sollen. |
| `Node Size` | Hiermit wird die Größe der hexagonalen Zellen festgelegt. Die Einstellung wird mit der `Margin between hexagons`-Eigenschaft verwendet, um das Aussehen des wabenförmigen Diagramms anzupassen. |
| `Margin between hexagons` | Hiermit wird die Lücke zwischen den hexagonalen Zellen festgelegt. Die Einstellung wird mit der `Node size`-Eigenschaft verwendet, um das Aussehen des wabenförmigen Diagramms anzupassen. |
| `Coloring type` | Hiermit wird das Farbschema für den Knoten festgelegt. |
| `Node Color Field` | Wählt eine Spalte aus, die die Metrik bereitstellt, auf der die Knotenbereiche basieren. |

## <a name="node-coloring-types"></a>Knotenfärbungstypen

| Färbungstyp | Erläuterung |
|:------------- |:-------------|
| `None` | Alle Knoten haben die gleiche Farbe. |
| `Categorical` | Knoten werden Farben basierend auf dem Wert oder der Kategorie aus einer Spalte im Resultset zugewiesen. Im obigen Beispiel basiert die Färbung auf der Spalte _Kind_ (Art) des Resultsets. Unterstützte Paletten sind `Default`, `Pastel` und `Cool tone`.  |
| `Heatmap` | Bei diesem Typ werden die Zellen auf der Grundlage einer Metrikspalte und einer Farbpalette gefärbt. Dies bietet eine einfache Möglichkeit, Metriken über Zellen hinweg hervorzuheben. |
| `Thresholds` | Bei diesem Typ werden Zellfarben durch Schwellenwertregeln festgelegt (z. B. _CPU > 90 % => Rot, 60 % > CPU > 90 % => Gelb, CPU < 60 % => Grün_). |
| `Field Based` | Bei diesem Typ stellt eine Spalte bestimmte RGB-Werte bereit, die für den Knoten verwendet werden. Bietet größtmögliche Flexibilität, erfordert jedoch in der Regel einen größeren Aufwand für die Aktivierung.  |
      
## <a name="node-format-settings"></a>Einstellungen für Knotenformat

Ersteller von Honigwaben können die Inhalte für die verschiedenen Teile eines Knotens angeben: oben, links, zentriert, rechts und unten. Ersteller können alle von Arbeitsmappen unterstützten Renderer verwenden (Text, große Zahl, Sparklines, Symbol usw.).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie einen [Renderer für zusammengesetzte Balken](workbooks-composite-bar.md) erstellen.
- Informieren Sie sich über das [Importieren von Azure Monitor-Protokolldaten in Power BI](../logs/log-powerbi.md).