---
title: Transformation für bedingtes Teilen im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Teilen Sie Daten auf verschiedene Datenströme auf, indem Sie die Transformation für bedingtes Teilen in einem Zuordnungsdatenfluss in Azure Data Factory oder Synapse Analytics verwenden.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 15864e8dfb694478f8156d5122608dec88493285
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060288"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformation für bedingtes Teilen im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Bei der Transformation für bedingtes Teilen werden Datenzeilen basierend auf Übereinstimmungsbedingungen an verschiedene Streams geleitet. Die Transformation für bedingtes Teilen ist mit einer CASE-Entscheidungsstruktur in einer Programmiersprache vergleichbar. Die Transformation wertet Ausdrücke aus und leitet dann die Datenzeile basierend auf den Ergebnissen an den angegebenen Datenstrom weiter.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Konfiguration

Mit der Einstellung **Teilen bei** wird bestimmt, ob die Zeile mit den Daten an den ersten übereinstimmenden Stream oder an alle übereinstimmenden Streams geleitet wird.

Verwenden Sie den Ausdrucks-Generator für Datenflüsse, um einen Ausdruck für die Teilungsbedingung einzugeben. Klicken Sie zum Hinzufügen einer neuen Bedingung in einer vorhandenen Zeile auf das Pluszeichen. Darüber hinaus kann für Zeilen, für die sich keine Übereinstimmung mit einer Bedingung ergibt, auch ein Standardstream hinzugefügt werden.

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="Bedingtes Teilen":::

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Beispiel

Das folgende Beispiel ist eine Transformation für bedingtes Teilen mit dem Namen `SplitByYear`, bei der der eingehende Stream `CleanData` verwendet wird. Diese Transformation verfügt über die beiden Teilungsbedingungen `year < 1960` und `year > 1980`. `disjoint` ist „false“, weil die Daten an die erste übereinstimmende Bedingung geleitet werden. Alle Zeilen, die die erste Bedingung erfüllen, werden an den Ausgabestream `moviesBefore1960` geleitet. Alle verbleibenden Zeilen, die die zweite Bedingung erfüllen, werden an den Ausgabestream `moviesAFter1980` geleitet. Für alle anderen Zeilen wird der Standardstream `AllOtherMovies` verwendet.

In der Benutzeroberfläche des Diensts sieht diese Transformation wie in der folgenden Abbildung aus:

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="Bedingtes Teilen":::

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Nächste Schritte

Häufig verwendete Datenflusstransformationen, die zusammen mit dem bedingten Teilen verwendet werden, sind die [Join-Transformation](data-flow-join.md), [Suchtransformation](data-flow-lookup.md) und [Auswahltransformation](data-flow-select.md).
