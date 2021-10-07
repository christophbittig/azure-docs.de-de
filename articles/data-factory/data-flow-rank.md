---
title: Rangtransformation in einem Zuordnungsdatenfluss
description: Hier erfahren Sie, wie Sie eine Zuordnungsdatenfluss-Rangfolgetransformation verwenden, um eine Rangfolgespalte in Azure Data Factory- oder Synapse Analytics-Pipelines zu generieren.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3af0355a02badb5aaa5d5b13a51b74392c931d8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059874"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Rangtransformation in einem Zuordnungsdatenfluss 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Verwenden Sie die Rangtransformation, um eine geordnete Rangfolge basierend auf den vom Benutzer angegebenen Sortierbedingungen zu generieren. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Konfiguration

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="Rangeinstellungen":::

**Keine Beachtung von Groß-/Kleinschreibung**: Wenn eine Sortierspalte vom Typ „String“ ist, wird die Groß-/Kleinschreibung bei der Rangfolge berücksichtigt. 

**Dicht:** Wenn diese Option aktiviert ist, wird die Rangspalte in dichter Rangfolge ausgeführt. Bei jeder Rangzahl handelt es sich um eine fortlaufende Nummer, und Rangwerte werden nach einem gleichwertigen Objekt nicht übersprungen.

**Rangspalte:** Der Name der generierten Rangspalte. Diese Spalte ist vom Typ „Long“.

**Sortierbedingungen**: Wählen Sie aus, nach welchen Spalten sortiert werden soll und in welcher Reihenfolge die Sortierung erfolgt. Die Reihenfolge bestimmt die Sortierpriorität.

Bei der oben gezeigten Konfiguration werden eingehende Basketballdaten übernommen und eine Rangspalte mit dem Namen „pointsRanking“ erstellt. Die Zeile mit dem höchsten Wert der Spalte *PTS* weist einen *pointsRanking*-Wert von 1 auf.

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Beispiel

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="Rangeinstellungen":::

Der folgende Codeausschnitt zeigt das Datenflussskript für die obige Rangkonfiguration.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Nächste Schritte

Filtern Sie Zeilen basierend auf den Rangwerten mithilfe der [Filtertransformation](data-flow-filter.md).
