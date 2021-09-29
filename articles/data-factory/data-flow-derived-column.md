---
title: Transformation für abgeleitete Spalten in Zuordnungsdatenflüssen
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie mehr über das bedarfsorientierte Transformieren von Daten in Azure Data Factory mithilfe der Transformation für abgeleitete Spalten in Zuordnungsdatenflüssen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 51656330a0d849ed61d7f1cae88f7bbfc0610c49
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058783"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformation für abgeleitete Spalten in Mapping Data Flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Verwenden Sie die Transformation für abgeleitete Spalten, um in Ihrem Datenfluss neue Spalten zu generieren oder vorhandene Felder zu ändern.

## <a name="create-and-update-columns"></a>Erstellen und Aktualisieren von Spalten

Beim Erstellen einer abgeleiteten Spalte können Sie entweder eine neue Spalte generieren oder eine vorhandene Spalte aktualisieren. Geben Sie im Textfeld **Spalte** die Spalte ein, die Sie erstellen. Wenn Sie eine vorhandene Spalte in Ihrem Schema überschreiben möchten, können Sie die Dropdownliste für Spalten verwenden. Um den Ausdruck der abgeleiteten Spalte zu erstellen, klicken Sie auf das Textfeld **Ausdruck eingeben**. Sie können entweder mit dem Eingeben des Ausdrucks beginnen oder den Ausdrucks-Generator öffnen, um die Logik zu erstellen.

:::image type="content" source="media/data-flow/create-derive-column.png" alt-text="Einstellungen für abgeleitete Spalten":::

Wenn Sie weitere abgeleitete Spalten hinzufügen möchten, klicken Sie oberhalb der Spaltenliste auf **Hinzufügen**, oder klicken Sie auf das Pluszeichen („+“) neben einer vorhandenen abgeleiteten Spalte. Klicken Sie entweder auf **Spalte hinzufügen** oder auf **Spaltenmuster hinzufügen**.

:::image type="content" source="media/data-flow/add-derived-column.png" alt-text="Auswahl für neue abgeleitete Spalte":::

### <a name="column-patterns"></a>Spaltenmuster

In Fällen, in denen Ihr Schema nicht explizit definiert ist, oder wenn Sie eine Reihe von Spalten in einem Massenvorgang aktualisieren möchten, sollten Sie Spaltenmuster erstellen. Mithilfe von Spaltenmustern können Sie Spalten anhand von Regeln abgleichen, die auf den Spaltenmetadaten basieren, und abgeleitete Spalten für jede übereinstimmende Spalte erstellen. Weitere Informationen finden Sie unter [Erstellen von Spaltenmustern in der Transformation für abgeleitete Spalten](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate).

:::image type="content" source="media/data-flow/column-pattern-derive.png" alt-text="Spaltenmuster":::

## <a name="building-schemas-using-the-expression-builder"></a>Erstellen von Schemas mit dem Ausdrucks-Generator

Wenn Sie den [Ausdrucks-Generator](concepts-data-flow-expression-builder.md) für Zuordnungsdatenflüsse verwenden, können Sie Ihre abgeleiteten Spalten im Abschnitt **Abgeleitete Spalten** erstellen, bearbeiten und verwalten. Es werden alle in der Transformation erstellten oder geänderten Spalten aufgelistet. Sie können interaktiv auswählen, welche Spalte oder welches Muster Sie bearbeiten möchten, indem Sie auf den Spaltennamen klicken. Wenn Sie eine zusätzliche Spalte hinzufügen möchten, wählen Sie **Neu erstellen** aus, und wählen Sie dann aus, ob Sie eine einzelne Spalte oder ein Muster hinzufügen möchten.

:::image type="content" source="media/data-flow/derive-add-column.png" alt-text="Neue Spalte erstellen":::

Wenn Sie mit komplexen Spalten arbeiten, können Sie Unterspalten erstellen. Klicken Sie hierzu auf das Pluszeichen („+“) neben einer beliebigen Spalte, und wählen Sie **Unterspalte hinzufügen** aus. Weitere Informationen zum Umgang mit komplexen Typen im Datenfluss finden Sie unter [JSON-Verarbeitung mit Mapping Data Flow](format-json.md#mapping-data-flow-properties).

:::image type="content" source="media/data-flow/derive-add-subcolumn.png" alt-text="Hinzufügen einer Unterspalte":::

Weitere Informationen zum Umgang mit komplexen Typen im Datenfluss finden Sie unter [JSON-Verarbeitung mit Mapping Data Flow](format-json.md#mapping-data-flow-properties).

:::image type="content" source="media/data-flow/derive-complex-column.png" alt-text="Hinzufügen einer komplexen Spalte":::

### <a name="locals"></a>Locals

Wenn Sie Logik für mehrere Spalten gemeinsam nutzen oder Ihre Logik aufteilen möchten, können Sie eine lokale Variable in einer Transformation für abgeleitete Spalten erstellen. Dabei handelt es sich um einen Satz von Logik, der nicht in die folgende Transformation weitergeleitet wird. Lokale Variablen können im Ausdrucks-Generator erstellt werden, indem Sie zu **Ausdruckselemente** wechseln und **Lokale** auswählen. Erstellen Sie eine neue Lokale, indem Sie **Neu erstellen** auswählen.

:::image type="content" source="media/data-flow/create-local.png" alt-text="Lokale erstellen":::

Lokale Variablen können auf ein beliebiges Ausdruckselement einer abgeleiteten Spalte verweisen. Dies schließt Funktionen, Eingabeschemas, Parameter und andere lokale Variablen ein. Wenn auf andere lokale Variablen verwiesen wird, ist die Reihenfolge wichtig, weil sich die referenzierte lokale Variable „über“ der aktuellen befinden muss.

:::image type="content" source="media/data-flow/create-local-2.png" alt-text="Lokale 2 erstellen":::

Wenn Sie auf eine lokale Variable in einer abgeleiteten Spalte verweisen möchten, klicken Sie entweder in der Ansicht **Ausdruckselemente** auf die lokale Variable, oder verweisen Sie mit einem Doppelpunkt vor ihrem Namen darauf. Beispielsweise wird auf eine lokale Variable mit dem Namen „local1“ durch `:local1` verwiesen. Um eine lokale Definition zu bearbeiten, zeigen Sie in der Ansicht „Ausdruckselemente“ mit der Maus darauf, und klicken Sie auf das Stiftsymbol.

:::image type="content" source="media/data-flow/using-locals.png" alt-text="Verwenden lokaler Variablen":::

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Beispiel

Das folgende Beispiel ist eine abgeleitete Spalte mit dem Namen `CleanData`, die aus einem eingehenden Datenstrom `MoviesYear` zwei abgeleitete Spalten erstellt. Die erste abgeleitete Spalte ersetzt die Spalte `Rating` durch den Wert für „Rating“ als ganzzahliger Typ. Die zweite abgeleitete Spalte ist ein Muster, das jede Spalte abgleicht, deren Name mit „movies“ beginnt. Für jede übereinstimmende Spalte wird eine Spalte `movie` erstellt, die dem Wert der übereinstimmenden Spalte entspricht und das Präfix „movie_“ erhält. 

In der Benutzeroberfläche sieht diese Transformation wie in der folgenden Abbildung aus:

:::image type="content" source="media/data-flow/derive-script.png" alt-text="Ableitungsbeispiel":::

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Mapping Data Flow expression language (Mapping Data Flow-Ausdruckssprache)](data-flow-expression-functions.md).
