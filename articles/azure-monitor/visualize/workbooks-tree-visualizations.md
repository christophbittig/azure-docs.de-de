---
title: Strukturvisualisierungen für Azure Monitor-Arbeitsmappen
description: Erfahren Sie mehr über die zahlreichen Strukturvisualisierungen für Azure Monitor-Arbeitsmappen.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: c2c15eaac298795bfb96466959a77d6840e80936
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131598"
---
# <a name="tree-visualizations"></a>Strukturvisualisierungen

Arbeitsmappen unterstützen hierarchische Ansichten über Strukturraster. In Strukturen können einige Zeilen für einen Drilldown auf die nächste Ebene erweitert werden.

Das unten stehende Beispiel zeigt Metriken zur Containerintegrität (Arbeitssatzgröße), die als Strukturraster visualisiert sind. Die Knoten der obersten Ebene sind hier AKS-Knoten (Azure Kubernetes Service), die nächste Ebene sind Pods, und die letzte Ebene sind Container. Beachten Sie, dass Sie die Spalten weiterhin wie in einem Raster formatieren können (Wärmebild, Symbole, Link). Die zugrunde liegende Datenquelle ist in diesem Fall ein Log Analytics-Arbeitsbereich mit AKS-Protokollen.

[![Screenshot einer Ansicht mit Zusammenfassung in einer Kachel](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Hinzufügen eines Strukturrasters
1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, und wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Legen Sie die Visualisierung auf **Raster** fest.
6. Wählen Sie die Schaltfläche **Spalteneinstellungen** aus, um den Einstellungsbereich zu öffnen.
7. Legen Sie unten im Abschnitt **Einstellungen für „Struktur“/„Gruppieren nach“** Folgendes fest:
    * Strukturtyp: `Parent/Child`
    * ID-Feld: `Id`
    * Übergeordnetes ID-Feld: `ParentId`
    * Erweiterung anzeigen für: `Name`
    * Aktivieren Sie das Kontrollkästchen *Oberste Ebene der Struktur erweitern*.
8. Legen Sie im Abschnitt _Spalten_ oben Folgendes fest:
    * _ID_ – Säulenrenderer: `Hidden`
    * _Übergeordnete ID_ – Säulenrenderer: `Hidden`
    * _Anforderungen_ – Säulenrenderer: `Bar`, Farbe: `Blue`, Minimalwert: `0`
9. Wählen Sie unten im Bereich die Schaltfläche **Speichern und schließen** aus.

[![Screenshot der Kachelzusammenfassungsansicht mit der Abfrage und den Einstellungen der obigen Angaben](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Struktureinstellungen

| Einstellung | Erklärung |
|:------------- |:-------------|
| `Id Field` | Die eindeutige ID jeder Zeile im Raster. |
| `Parent Id Field` | Die ID des übergeordneten Elements der aktuellen Zeile. |
| `Show the expander on` | Die Spalte, für welche die Strukturerweiterung angezeigt werden soll. In Strukturrastern wird das Feld mit der ID und der übergeordneten ID normalerweise ausgeblendet, da diese nicht besonders lesbar sind. Stattdessen wird die Erweiterung in einem Feld mit einem besser lesbaren Wert (wie z. B. dem Namen der Entität) angezeigt. |
| `Expand the top level of the tree` | Wenn diese Option aktiviert ist, wird der Strukturraster auf der obersten Ebene erweitert. Die Option ist nützlich, wenn Sie standardmäßig weitere Informationen anzeigen möchten. |

## <a name="grouping-in-a-grid"></a>Gruppieren in einem Raster

Mithilfe von Gruppierungen können Sie hierarchische Ansichten ähnlich den oben genannten mit erheblich einfacheren Abfragen erstellen. Sie verlieren die Aggregation an den inneren Knoten der Struktur, dies ist jedoch für einige Szenarien akzeptabel. Verwenden Sie *Gruppieren nach*, um Strukturansichten zu erstellen, wenn das zugrunde liegende Resultset nicht in ein ordnungsgemäßes freies Formular transformiert werden kann, z. B. Warnungs-, Integritäts- und Metrikdaten.

## <a name="adding-a-tree-using-grouping"></a>Hinzufügen einer Struktur mithilfe einer Gruppierung

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Wählen Sie **Hinzufügen** und dann *Abfrage hinzufügen* aus, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp **Protokoll** aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Legen Sie die Visualisierung auf *Raster* fest.
2. Wählen Sie die Schaltfläche **Spalteneinstellungen** aus, um den Einstellungsbereich zu öffnen.
3. Legen Sie unten im Abschnitt *Einstellungen für „Struktur“/„Gruppieren nach“* Folgendes fest:
    * Strukturtyp: `Group By`
    * Gruppieren nach: `App`
    * Anschließend nach: `None`
    * Aktivieren Sie das Kontrollkästchen *Oberste Ebene der Struktur erweitern*.
4. Legen Sie im Abschnitt *Spalten* oben Folgendes fest:
    * *App* – Säulenrenderer: `Hidden`
    * *Anforderungen* – Säulenrenderer: `Bar`, Farbe: `Blue`, Minimalwert: `0`
5. Wählen Sie unten im Bereich die Schaltfläche **Speichern und schließen** aus.

[![Screenshot der Erstellung einer Strukturvisualisierung in Arbeitsmappen](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie einen [Graphen in Arbeitsmappen](workbooks-graph-visualizations.md) erstellen.
* Erfahren Sie, wie Sie eine [Kachel in Arbeitsmappen](workbooks-tile-visualizations.md) erstellen.
