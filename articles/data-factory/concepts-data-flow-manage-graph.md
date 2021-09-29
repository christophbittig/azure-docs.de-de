---
title: Verwalten des Graphen des Zuordnungsdatenflusses
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie den Graphen des Zuordnungsdatenflusses effektiv verwalten und bearbeiten können.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: da9b3148506e437f7bf5d90fc0fbe44e0a2d27f0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812110"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Verwalten des Graphen des Zuordnungsdatenflusses

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zuordnungsdatenflüsse werden mithilfe einer Entwurfsoberfläche, dem sog. Datenflussgraphen, erstellt. Im Graphen ist die Transformationslogik von links nach rechts aufgebaut. Weitere Datenströme werden von oben nach unten hinzugefügt. Wählen Sie zum Hinzufügen einer neuen Transformation unten rechts in einer vorhandenen Transformation das Pluszeichen aus.

:::image type="content" source="media/data-flow/canvas-2.png" alt-text="Canvas":::

Wenn Ihre Datenflüsse komplexer werden, gehen Sie wie folgt vor, um den Datenflussgraphen effektiv zu nutzen und zu verwalten. 

## <a name="moving-transformations"></a>Verschieben von Transformationen

In Zuordnungsdatenflüssen wird zusammenhängende Transformationslogik als **Datenstrom** bezeichnet. Das Feld **Eingehender Datenstrom** bestimmt, welcher Datenstrom die aktuelle Transformation mit Informationen versorgt. Jede Transformation hat je nach Aufgabe einen oder zwei eingehende Datenströme und stellt einen Ausgangsdatenstrom dar. Das Ausgabeschema der eingehenden Datenströme bestimmt, auf welche Spaltenmetadaten die aktuelle Transformation verweisen kann.

:::image type="content" source="media/data-flow/move-nodes.png" alt-text="Knoten verschieben":::

Im Gegensatz zur Canvas für Pipelines werden Datenflusstransformationen nicht mithilfe eines Drag & Drop-Modells bearbeitet. Um den eingehenden Datenstrom zu ändern oder eine Transformation zu verschieben, wählen Sie in der Dropdownliste **Eingehender Datenstrom** einen anderen Wert aus. Wenn Sie dies tun, werden alle nachgeordneten Transformationen zusammen mit der bearbeiteten Transformation verschoben. Der Graph wird automatisch entsprechend dem neuen logischen Datenfluss aktualisiert. Wenn Sie den eingehenden Datenstrom in eine Transformation ändern, die bereits eine nachgeordnete Transformation hat, wird eine neue Verzweigung oder ein paralleler Datenstrom erstellt. Erfahren Sie mehr zu [neuen Verzweigungen im Zuordnungsdatenfluss](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Ausblenden und Anzeigen des Diagramms

Beim Bearbeiten Ihrer Transformation können Sie das Konfigurationspanel so erweitern, dass es die gesamte Canvas einnimmt und der Graph ausgeblendet wird. Klicken Sie auf der rechten Seite der Canvas auf das nach oben zeigende Chevron.

:::image type="content" source="media/data-flow/hide-graph.png" alt-text="Diagramm ausblenden":::

Wenn das Diagramm ausgeblendet ist, können Sie zwischen Transformationen innerhalb eines Datenstroms wechseln, indem Sie auf **Weiter** oder **Zurück** klicken. Klicken Sie auf das nach unten zeigende Chevron, um den Graphen anzuzeigen.

:::image type="content" source="media/data-flow/show-graph.png" alt-text="Graphen anzeigen":::

## <a name="searching-for-transformations"></a>Suchen nach Transformationen

Um eine Transformation in Ihrem Graphen schnell zu finden, klicken Sie über der Zoomeinstellung auf das Symbol **Suchen**.

:::image type="content" source="media/data-flow/search-1.png" alt-text="Screenshot: Schaltfläche „Suchen“":::

Sie können nach Name oder Beschreibung der Transformation suchen, um eine Transformation zu finden.

:::image type="content" source="media/data-flow/search-2.png" alt-text="Screenshot: Textfeld für die Suche":::

## <a name="hide-reference-nodes"></a>Ausblenden von Referenzknoten

Wenn der Datenfluss Transformationen des Typs „Join“, „Lookup“, „Exists“ oder „Union“ aufweist, enthält der Datenfluss Referenzknoten für alle eingehenden Datenströme. Wenn Sie den eingenommenen vertikalen Platzbedarf minimieren möchten, können Sie die Referenzknoten minimieren. Klicken Sie dazu mit der rechten Maustaste auf die Canvas, und wählen Sie **Referenzknoten ausblenden** aus.

:::image type="content" source="media/data-flow/hide-reference-nodes.png" alt-text="Ausblenden von Referenzknoten":::

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie nach Fertigstellen der Datenflusslogik den [Debugmodus](concepts-data-flow-debug-mode.md), und testen Sie sie in einer Datenvorschau.
