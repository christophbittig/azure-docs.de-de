---
title: Filter-Transformation in einem Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Mithilfe der Filtertransformation in einem Zuordnungsdatenfluss in Azure Data Factory oder Synapse Analytics können Sie Zeilen herausfiltern.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3cf00a5ea80278973a548a19e9ef0dc82ef877f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060193"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filter-Transformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Filter-Transformationen ermöglicht das Filtern von Zeilen auf der Grundlage einer Bedingung. Der Ausgabestream enthält alle Zeilen, die der Filterbedingung entsprechen. Die Filter-Transformation ähnelt einer WHERE-Klausel in SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Konfiguration

Verwenden Sie den Ausdrucks-Generator für Datenflüsse, um einen Ausdruck für die Filterbedingung einzugeben. Klicken Sie auf das blaue Feld, um den Ausdrucks-Generator zu öffnen. Bei der Filterbedingung muss es sich um eine boolesche Bedingung handeln. Weitere Informationen zur Ausdruckserstellung finden Sie in der [Dokumentation für den Ausdrucks-Generator](concepts-data-flow-expression-builder.md).

:::image type="content" source="media/data-flow/filter1.png" alt-text="Filter-Transformation":::

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Beispiel

Das folgende Beispiel ist eine Filtertransformation mit dem Namen `FilterBefore1960`, bei der der eingehende Stream `CleanData` verwendet wird. Die Filterbedingung ist der Ausdruck `year <= 1960`.

In der Benutzeroberfläche sieht diese Transformation wie in der folgenden Abbildung aus:

:::image type="content" source="media/data-flow/filter1.png" alt-text="Filter-Transformation":::

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Select-Transformation](data-flow-select.md), um Spalten herauszufiltern.
