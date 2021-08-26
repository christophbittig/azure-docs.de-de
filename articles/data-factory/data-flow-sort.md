---
title: Transformation zum Sortieren in einem Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: 'Azure Data Factory Mapping-Daten: Transformation zum Sortieren'
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 04/14/2020
ms.openlocfilehash: 88253393820892f20544f5cbf6a83b21e24e8ec8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641022"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformation zum Sortieren in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mit der Transformation zum Sortieren können Sie die eingehenden Zeilen im aktuellen Datenstrom sortieren. Sie können einzelne Spalten auswählen und in aufsteigender oder absteigender Reihenfolge sortieren.

> [!NOTE]
> Zuordnungsdatenflüsse werden auf Spark-Clustern ausgeführt, die Daten auf mehrere Knoten und Partitionen verteilen. Wenn Sie Ihre Daten in einer nachfolgenden Transformation neu partitionieren möchten, geht Ihre Sortierung möglicherweise aufgrund dieser Umverteilung von Daten verloren. Die beste Möglichkeit, die Sortierreihenfolge in Ihrem Datenfluss zu verwalten, besteht darin, eine einzelne Partition auf der Registerkarte „Optimieren“ für die Transformation festzulegen und die Sortiertransformation so nah wie möglich an der Senke zu platzieren.

## <a name="configuration"></a>Konfiguration

![Sortiereinstellungen](media/data-flow/sort.png "Sortieren")

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

![Sortiereinstellungen](media/data-flow/sort.png "Sortieren")

Der nachfolgende Codeausschnitt zeigt das Datenflussskript für die obige Konfiguration der Sortierung.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Nächste Schritte

Nach dem Sortieren können Sie die [Transformation für das Aggregieren](data-flow-aggregate.md) verwenden.
