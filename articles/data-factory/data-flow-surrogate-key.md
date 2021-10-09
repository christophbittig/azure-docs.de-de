---
title: Transformation für Ersatzschlüssel im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie den Mapping-Datenfluss Surrogate Key Transformation verwenden, um sequenzielle Schlüsselwerte in Azure Data Factory und Synapse Analytics zu erzeugen.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: d125923d676b0adfd280a1882a39cacb74749a82
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059813"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformation für Ersatzschlüssel im Zuordnungsdatenfluss 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Verwenden Sie die Transformation für Ersatzschlüssel, um einen inkrementell geänderten Schlüsselwert für jede Datenzeile hinzuzufügen. Dieses Vorgehen ist nützlich, wenn Sie Dimensionstabellen in einem analytischen Datenmodell mit Sternschema entwerfen. In einem Sternschema erfordert jedes Mitglied in Dimensionstabellen einen eindeutigen Schlüssel, bei dem es sich nicht um einen Geschäftsschlüssel handelt.

## <a name="configuration"></a>Konfiguration

:::image type="content" source="media/data-flow/surrogate.png" alt-text="Transformation für Ersatzschlüssel":::

**Schlüsselspalte**: Der Name der Spalte mit dem generierten Ersatzschlüssel.

**Startwert**: Die niedrigste Schlüsselwert, der generiert wird.

## <a name="increment-keys-from-existing-sources"></a>Inkrementelle Schlüssel aus vorhandenen Quellen

Um Ihre Sequenz von einem Wert aus zu starten, der in einer Quelle vorhanden ist, wird empfohlen, eine Cachesenke zum Speichern dieses Werts und eine Transformation für abgeleitete Spalten zum gemeinsamen Hinzufügen der beiden Werte zu verwenden. Verwenden Sie eine zwischengespeicherte Suche, um die Ausgabe abzurufen, und fügen Sie sie an den generierten Schlüssel an. Weitere Informationen finden Sie unter [Cachesenken](data-flow-sink.md#cache-sink) und [zwischengespeicherten Suchen](concepts-data-flow-expression-builder.md#cached-lookup).

:::image type="content" source="media/data-flow/cached-lookup-example.png" alt-text="Ersatzschlüsselsuche":::

### <a name="increment-from-existing-maximum-value"></a>Inkrement des vorhandenen Maximalwerts

Ein Seeding für den Schlüsselwert mit dem vorherigen Maximalwert lässt sich mit zwei Methoden durchführen, je nachdem, wo sich Ihre Quelldaten befinden.

#### <a name="database-sources"></a>Datenbankquellen

Verwenden Sie eine SQL-Abfrageoption, um „MAX()“ aus Ihrer Quelle auszuwählen. Beispiel: `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

:::image type="content" source="media/data-flow/surrogate-key-max-database.png" alt-text="Ersatzschlüsselabfrage":::

#### <a name="file-sources"></a>Dateiquellen

Wenn sich der vorherige Maximalwert in einer Datei befindet, verwenden Sie die `max()`-Funktion in der Aggregattransformation, um den vorherigen Maximalwert abzurufen:

:::image type="content" source="media/data-flow/surrogate-key-max-file.png" alt-text="Ersatzschlüsseldatei":::

In beiden Fällen müssen Sie in eine Cachesenke schreiben und den Wert suchen. 


## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Beispiel

:::image type="content" source="media/data-flow/surrogate.png" alt-text="Transformation für Ersatzschlüssel":::

Der nachfolgende Codeausschnitt zeigt das Datenflussskript für die obige Konfiguration des Ersatzschlüssels.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Nächste Schritte

Diese Beispiele verwenden die Transformationen [Verbinden](data-flow-join.md) und [Abgeleitete Spalten](data-flow-derived-column.md) Transformationen.
