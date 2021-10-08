---
title: Fenstertransformation im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Fenstertransformation des Zuordnungsdatenflusses in Azure Data Factory- und Synapse Analytics-Pipelines verwenden.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: a913e7a7c5e3ce24df649cb0f1a670650992ac52
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059661"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Fenstertransformation im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

In der Fenstertransformation definieren Sie fensterbasierte Aggregationen von Spalten in Ihren Datenströmen. Im Ausdrucks-Generator können Sie verschiedene Typen von Aggregationen definieren, die auf Daten- oder Zeitfenstern (SQL OVER-Klausel) basieren, wie z.B. LEAD, LAG, NTILE, CUMEDIST, RANK usw.). Ein neues Feld wird in Ihrer Ausgabe generiert, das diese Aggregationen enthält. Sie können auch optionale „Group by“-Felder einschließen.

:::image type="content" source="media/data-flow/windows1.png" alt-text="Screenshot: Ausgewählte Menüoption „Windowing“":::

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over
Legen Sie die Partitionierung von Spaltendaten für Ihre Fenstertransformation fest. Die SQL-Entsprechung ist ```Partition By``` in der Over-Klausel in SQL. Wenn Sie eine Berechnung erstellen möchten, oder einen für die Partitionierung zu verwendenden Ausdruck, können Sie hierzu den Mauszeiger auf den Namen der Spalte setzen und „berechnete Spalte“ auswählen.

:::image type="content" source="media/data-flow/windows4.png" alt-text="Screenshot: Windowing-Einstellungen mit ausgewählter Registerkarte „Over“":::

## <a name="sort"></a>Sortieren
Ein anderer Teil der Over-Klausel ist das Festlegen von ```Order By```. Hiermit wird die Reihenfolge der Datensortierung festgelegt. Sie können auch einen Ausdruck für einen berechneten Wert in diesem Spaltenfeld für die Sortierung erstellen.

:::image type="content" source="media/data-flow/windows5.png" alt-text="Screenshot: Windowing-Einstellungen mit ausgewählter Registerkarte für die Sortierung":::

## <a name="range-by"></a>Range By
Legen Sie als Nächstes den Fensterrahmen als „Unbounded“ oder „Bounded“ fest. Um einen unbegrenzten Fensterrahmen festzulegen, positionieren Sie den Schieberegler an beiden Enden auf „Unbounded“. Wenn Sie eine Einstellung zwischen „Unbounded“ und „Current Row“ auswählen, müssen Sie den Offset-Start- und Endwert festlegen. Beide Werte sind positive ganze Zahlen. Sie können entweder relativen Zahlen oder Werte aus Ihren Daten verwenden.

Der Fensterschieberegler muss zwei Werte festlegen: die Werte vor der aktuellen Zeile und die Werte nach der aktuellen Zeile. Offset-Start- und Endwert entsprechen den beiden Selektoren auf dem Schieberegler.

:::image type="content" source="media/data-flow/windows6.png" alt-text="Screenshot: Windowing-Einstellungen mit ausgewählter Registerkarte für den Bereich":::

## <a name="window-columns"></a>Fensterspalten
Verwenden Sie den Ausdrucks-Generator schließlich, um die Aggregationen zu definieren, die Sie mit Datenfenstern wie RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG usw. verwenden möchten.

:::image type="content" source="media/data-flow/windows7.png" alt-text="Screenshot: Ergebnis der Windowing-Aktion":::

Die vollständige Liste der Aggregat- und Analysefunktionen, die Sie in der Ausdruckssprache für Datenflüsse über den Ausdrucks-Generator verwenden können, finden Sie unter [Datentransformationsausdrücke im Zuordnungsdatenfluss](data-flow-expression-functions.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine einfache Group by-Aggregation suchen, verwenden Sie die [Transformation für das Aggregieren](data-flow-aggregate.md).
