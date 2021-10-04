---
title: Transformation zum Sortieren in einem Zuordnungsdatenfluss
description: Hier erfahren Sie mehr über die Transformation zum Sortieren von Zuordnungsdaten in Azure Data Factory- und Synapse Analytics-Pipelines.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8781836be58403dfef130f55fa698b281360e641
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059881"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformation zum Sortieren in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Mit der Transformation zum Sortieren können Sie die eingehenden Zeilen im aktuellen Datenstrom sortieren. Sie können einzelne Spalten auswählen und in aufsteigender oder absteigender Reihenfolge sortieren.

> [!NOTE]
> Zuordnungsdatenflüsse werden auf Spark-Clustern ausgeführt, die Daten auf mehrere Knoten und Partitionen verteilen. Wenn Sie Ihre Daten in einer nachfolgenden Transformation neu partitionieren möchten, geht Ihre Sortierung möglicherweise aufgrund dieser Umverteilung von Daten verloren. Die beste Möglichkeit, die Sortierreihenfolge in Ihrem Datenfluss zu verwalten, besteht darin, eine einzelne Partition auf der Registerkarte „Optimieren“ für die Transformation festzulegen und die Sortiertransformation so nah wie möglich an der Senke zu platzieren.

## <a name="configuration"></a>Konfiguration

:::image type="content" source="media/data-flow/sort.png" alt-text="Sortiereinstellungen":::

**Keine Beachtung von Groß-/Kleinschreibung**: Legen Sie fest, ob die Groß-/Kleinschreibung beim Sortieren von Zeichenfolgen- oder Textfeldern ignoriert werden soll.

**Nur innerhalb von Partitionen sortieren**: Wenn Datenflüsse auf Spark ausgeführt werden, wird jeder Datenfluss in Partitionen unterteilt. Mit dieser Einstellung werden Daten nur innerhalb der eingehenden Partitionen und nicht im gesamten Datenstrom sortiert. 

**Sortierbedingungen**: Wählen Sie aus, welche Spalten sortiert werden sollen und in welcher Reihenfolge die Sortierung erfolgt. Die Reihenfolge bestimmt die Sortierpriorität. Legen Sie fest, ob Nullen am Anfang und Ende des Datenstroms gezeigt werden.

### <a name="computed-columns"></a>Berechnete Spalten

Um einen Spaltenwert vor der Sortierung zu ändern oder zu extrahieren, zeigen Sie auf die Spalte und wählen „Berechnete Spalte“ aus. Dadurch wird für die Sortierung kein Spaltenwert verwendet, sondern der Ausdrucks-Generator geöffnet, um einen Ausdruck für den Sortiervorgang zu erstellen.

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Beispiel

:::image type="content" source="media/data-flow/sort.png" alt-text="Sortiereinstellungen":::

Der nachfolgende Codeausschnitt zeigt das Datenflussskript für die obige Konfiguration der Sortierung.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Nächste Schritte

Nach dem Sortieren können Sie die [Transformation für das Aggregieren](data-flow-aggregate.md) verwenden.
