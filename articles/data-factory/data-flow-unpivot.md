---
title: Entpivotierungstransformation im Mappingdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie mehr über die Entpivotierungstransformation für Zuordnungsdatenflüsse in Azure Data Factory und Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 28c89622702435f178e241125545f8f0ac86eb18
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059585"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Entpivotierungstransformation im Mappingdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Verwenden Sie die Entpivotisierung in einem Zuordnungsdatenfluss, um ein nicht normalisiertes Dataset in eine stärker normalisierte Version zu ändern, indem Werte aus mehreren Spalten eines einzelnen Datensatzes in mehrere Datensätze mit den gleichen Werten in einer einzelnen Spalte erweitert werden.

:::image type="content" source="media/data-flow/unpivot1.png" alt-text="Screenshot: Ausgewählte Menüoption „Entpivotieren“":::

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Gruppierung aufheben nach

:::image type="content" source="media/data-flow/unpivot5.png" alt-text="Screenshot: Einstellungen für das Entpivotieren mit ausgewählter Registerkarte „Gruppierung aufheben“":::

Legen Sie zuerst die Spalten fest, nach denen Sie die Gruppierung für Ihre Entpivotierungsaggregation aufheben möchten. Legen Sie eine oder mehrere Spalten für das Aufheben der Gruppierung mit dem Pluszeichen (+) neben der Spaltenliste fest.

## <a name="unpivot-key"></a>Entpivotierungsschlüssel

:::image type="content" source="media/data-flow/unpivot6.png" alt-text="Screenshot: Einstellungen für das Entpivotieren mit ausgewählter Registerkarte für den Entpivotierungsschlüssel":::

Der Entpivotierungsschlüssel ist die Spalte, die der Dienst von einer Spalte in eine Zeile pivotiert. Standardmäßig wird jeder eindeutige Wert im Dataset für dieses Feld in eine Zeile pivotiert. Sie können jedoch die Werte aus dem Dataset optional eingeben, die in Zeilenwerte pivotiert werden sollen.

## <a name="unpivoted-columns"></a>Entpivotierte Spalten

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="Screenshot: Einstellungen für das Entpivotieren mit ausgewählter Registerkarte „Datenvorschau“":::

Wählen Sie abschließend den Spaltennamen zum Speichern der Werte für entpivotierte Spalten aus, die in Zeilen transformiert werden.

(Optional) Zeilen mit NULL-Werten können verworfen werden.

Im oben stehenden Beispiel ist „SumCost“ der ausgewählte Spaltenname.

:::image type="content" source="media/data-flow/unpivot3.png" alt-text="Bild der Spalten „PO“ (Bestellung), „Vendor“ (Lieferant) und „Fruit“ (Obst) vor und nach einer Entpivotierungstransformation, bei der die Spalte „Fruit“ als Entpivotierungsschlüssel verwendet wird.":::

Wenn Sie die Spaltenanordnung auf „Normal“ festlegen, werden alle neuen entpivotierten Spalten aus einem einzelnen Wert gruppiert. Durch Festlegen der Spaltenanordnung auf „Lateral“ werden die neuen entpivotierten Spalten gruppiert, die aus einer vorhandenen Spalte generiert wurde.

:::image type="content" source="media/data-flow//unpivot7.png" alt-text="Screenshot: Ergebnis der Transformation":::

Im letzten Resultset der entpivotierten Daten werden die Spaltensummen angezeigt, die jetzt in separaten Zeilenwerten entpivotiert sind.

## <a name="next-steps"></a>Nächste Schritte

Verwenden der [Pivotierungstransformation](data-flow-pivot.md) zum Pivotieren von Zeilen zu Spalten
