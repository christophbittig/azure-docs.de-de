---
title: Filter-Transformation in einem Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Mithilfe der Filter-Transformation in einem Azure Data Factory-Zuordnungsdatenfluss können Sie Zeilen herausfiltern.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 05/26/2020
ms.openlocfilehash: f313791d08c625714432953363e785e6e95b321c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639996"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filter-Transformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Filter-Transformationen ermöglicht das Filtern von Zeilen auf der Grundlage einer Bedingung. Der Ausgabestream enthält alle Zeilen, die der Filterbedingung entsprechen. Die Filter-Transformation ähnelt einer WHERE-Klausel in SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Konfiguration

Verwenden Sie den Ausdrucks-Generator für Datenflüsse, um einen Ausdruck für die Filterbedingung einzugeben. Klicken Sie auf das blaue Feld, um den Ausdrucks-Generator zu öffnen. Bei der Filterbedingung muss es sich um eine boolesche Bedingung handeln. Weitere Informationen zur Ausdruckserstellung finden Sie in der [Dokumentation für den Ausdrucks-Generator](concepts-data-flow-expression-builder.md).

![Filter-Transformation](media/data-flow/filter1.png "Filter-Transformation")

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

Auf der Data Factory-Benutzeroberfläche sieht diese Transformation wie folgt aus:

![Filter-Transformation](media/data-flow/filter1.png "Filter-Transformation")

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Select-Transformation](data-flow-select.md), um Spalten herauszufiltern.
