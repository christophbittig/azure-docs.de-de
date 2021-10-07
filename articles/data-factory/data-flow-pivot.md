---
title: Pivottransformation im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Sie können mithilfe der Zuordnungsdatenfluss-Pivottransformation in Azure Data Factory- und Synapse Analytics-Pipelines Daten aus Zeilen in Spalten pivotieren.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c6aa95864ad12d5776a8e90eb2b1bd8dac1f04a2
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059927"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Pivottransformation im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Verwenden Sie die Pivottransformation, um aus den eindeutigen Zeilenwerten einer einzelnen Spalte mehrere Spalten zu erstellen. Pivotieren ist eine Aggregationstransformation, bei der Sie die Option „Nach Spalten gruppieren“ auswählen und mithilfe von [Aggregatfunktionen](data-flow-expression-functions.md#aggregate-functions) Pivotspalten erstellen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Konfiguration

Für die Pivottransformation sind drei verschiedene Eingaben erforderlich: „Nach Spalten gruppieren“, der Pivotschlüssel und Angaben zum Generieren der pivotierten Spalten.

### <a name="group-by"></a>Gruppieren nach

:::image type="content" source="media/data-flow/pivot2.png" alt-text="Gruppierungsoptionen":::

Wählen Sie aus, über welche Spalten die pivotierten Spalten aggregiert werden sollen. Die Ausgabedaten gruppieren alle Zeilen mit denselben Werten für „Gruppieren nach“ in eine Zeile. Die in der pivotierten Spalte ausgeführte Aggregation erfolgt in jeder Gruppe.

Dieser Abschnitt ist optional. Wenn keine Gruppieren nach-Spalten ausgewählt werden, wird der gesamte Datenstrom aggregiert und nur eine Zeile ausgegeben.

### <a name="pivot-key"></a>Pivotschlüssel

:::image type="content" source="media/data-flow/pivot3.png" alt-text="Pivotschlüssel":::

Der Pivotschlüssel ist die Spalte, deren Zeilenwerte in neue Spalten pivotiert werden. Standardmäßig erstellt die Pivottransformation eine neue Spalte für jeden eindeutigen Zeilenwert.

Im Abschnitt **Wert** können Sie bestimmte Zeilenwerte eingeben, die pivotiert werden sollen. Nur die in diesem Abschnitt eingegebenen Zeilenwerte werden pivotiert. Durch Aktivieren von **NULL-Wert** wird eine pivotierte Spalte für die NULL-Werte in der Spalte erstellt.

### <a name="pivoted-columns"></a>Pivotierte Spalten

:::image type="content" source="media/data-flow/pivot4.png" alt-text="Pivotierte Spalten":::

Generieren Sie für jeden eindeutigen Pivotschlüsselwert, der zu einer Spalte transformiert wird, einen aggregierten Zeilenwert für jede Gruppe. Sie können mehrere Spalten pro Pivotschlüssel erstellen. Jede Pivotspalte muss mindestens eine [Aggregatfunktion](data-flow-expression-functions.md#aggregate-functions) enthalten.

**Spaltennamensmuster**: Wählen Sie aus, wie der Spaltenname jeder Pivotspalte formatiert werden soll. Der ausgegebene Spaltenname ist eine Kombination aus dem Pivotschlüsselwert, dem Spaltenpräfix sowie optionalen Zeichen für Präfix, Suffix und Zeichen in der Mitte. 

**Spaltenanordnung**: Wenn Sie mehr als eine Pivotspalte pro Pivotschlüssel generieren, wählen Sie aus, wie die Spalten angeordnet werden sollen. 

**Spaltenpräfix**: Wenn Sie mehr als eine Pivotspalte pro Pivotschlüssel generieren, geben Sie ein Spaltenpräfix für jede Spalte ein. Diese Einstellung ist optional, wenn Sie nur über eine pivotierte Spalte verfügen.

## <a name="help-graphic"></a>Hilfegrafik

Die folgende Hilfegrafik zeigt, wie die verschiedenen Pivotkomponenten miteinander interagieren:

:::image type="content" source="media/data-flow/pivot5.png" alt-text="Hilfegrafik für Pivotfunktion":::

## <a name="pivot-metadata"></a>Pivotmetadaten

Wenn in der Pivotschlüsselkonfiguration keine Werte angegeben sind, werden die pivotierten Spalten dynamisch zur Laufzeit generiert. Die Anzahl von pivotierten Spalten entspricht der Anzahl von eindeutigen Pivotschlüsselwerte multipliziert mit der Anzahl von Pivotspalten. Da diese Zahl veränderlich ist, werden die Spaltenmetadaten auf der Benutzeroberfläche auf der Registerkarte **Untersuchen** nicht angezeigt, und es erfolgt keine Spaltenweitergabe. Verwenden Sie zum Transformieren dieser Spalten die [Spaltenmuster](concepts-data-flow-column-pattern.md)-Funktionen des Zuordnungsdatenflusses. 

Wenn bestimmte Pivotschlüsselwerte festgelegt wurden, werden die pivotierten Spalten in den Metadaten angezeigt. Spaltennamen stehen Ihnen in der Zuordnung für Untersuchung und Senke zur Verfügung.

### <a name="generate-metadata-from-drifted-columns"></a>Generieren von Metadaten aus Driftspalten

Pivot generiert neue Spaltennamen dynamisch basierend auf Zeilenwerten. Sie können diese neuen Spalten zu den Metadaten hinzufügen, auf die später im Datenfluss verwiesen werden kann. Verwenden Sie hierzu die Schnellaktion [Abweichende zuordnen](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) in der Datenvorschau. 

:::image type="content" source="media/data-flow/newpivot1.png" alt-text="Pivotspalten":::

### <a name="sinking-pivoted-columns"></a>Senken für pivotierte Spalten

Pivotierte Spalten sind zwar dynamisch, können aber trotzdem in Ihren Zieldatenspeicher geschrieben werden. Aktivieren Sie in Ihren Senkeneinstellungen die Option **Schemaabweichung zulassen**. So können Sie in Spalten schreiben, die in den Metadaten nicht enthalten sind. Die neuen dynamischen Namen werden nicht in Ihren Spaltenmetadaten angezeigt, die Option zur Schemaabweichung ermöglicht Ihnen aber trotzdem die Bereitstellung der Daten.

### <a name="rejoin-original-fields"></a>Erneutes Verknüpfen der ursprünglichen Felder

Die Pivottransformation wird nur für die Gruppieren nach-Spalten und die pivotierten Spalten ausgeführt. Wenn die Ausgabedaten auch andere Eingabespalten enthalten sollen, verwenden Sie ein [Selbstverknüpfungsmuster](data-flow-join.md#self-join).

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Beispiel

Den im Konfigurationsabschnitt gezeigten Screenshots liegt das folgende Datenflussskript zugrunde:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, die [Entpivotierungstransformation](data-flow-unpivot.md) durchzuführen, um Spaltenwerte in Zeilenwerte umzuwandeln. 
