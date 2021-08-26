---
title: Analysedatentransformation in einem Zuordnungsdatenfluss
description: Eingebettete Spaltendokumente analysieren
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 05/10/2021
ms.openlocfilehash: 2058fcade3375f680f616374ba8dee73a4e72dbc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639987"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Analysetransformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Analysetransformation zum Analysieren von Spalten in Ihren Daten, die in Dokumentform sind. Aktuell werden die folgenden Typen eingebetteter Dokumente zur Analyse unterstützt: JSON, XML und durch Trennzeichen getrennter Text.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Konfiguration

Im Konfigurationspanel für Analysetransformation wählen Sie zuerst den Datentyp aus, der in den Spalten enthalten ist, die Sie inline analysieren möchten. Die Analysetransformation enthält auch die folgenden Konfigurationseinstellungen.

![Analyseeinstellungen](media/data-flow/data-flow-parse-1.png "Analysieren")

### <a name="column"></a>Column

Ähnlich wie bei abgeleiteten Spalten und Aggregaten ändern Sie an dieser Stelle eine vorhandene Spalte, indem Sie sie in der Dropdownauswahl auswählen. Sie können hier aber auch den Namen einer neuen Spalte eingeben. ADF speichert die analysierten Quelldaten in dieser Spalte. In den meisten Fällen müssen Sie eine neue Spalte definieren, die das eingehende Feld für das eingebettete Dokument analysiert.

### <a name="expression"></a>Ausdruck

Verwenden Sie den Ausdrucks-Generator zum Festlegen der Quelle für Ihre Analyse. Dazu können Sie einfach die Quellspalte mit den eigenständigen Daten, die Sie analysieren möchten, auswählen oder aber komplexe Ausdrücke zum Analysieren erstellen.

#### <a name="example-expressions"></a>Beispielausdrücke

* Quellzeichenfolgendaten: ```chrome|steel|plastic```
  * Ausdruck: ```(desc1 as string, desc2 as string, desc3 as string)```

* Quell-JSON-Daten: ```{"ts":1409318650332,"userId":"309","sessionId":1879,"page":"NextSong","auth":"Logged In","method":"PUT","status":200,"level":"free","itemInSession":2,"registration":1384448}```
  * Ausdruck: ```(level as string, registration as long)```

* Quell-XML-Daten: ```<Customers><Customer>122</Customer><CompanyName>Great Lakes Food Market</CompanyName></Customers>```
  * Ausdruck: ```(Customers as (Customer as integer, CompanyName as string))```

### <a name="output-column-type"></a>Typ der Ausgabespalte

Hier konfigurieren Sie das Zielausgabeschema aus der Analyse, das in eine einzelne Spalte geschrieben wird.

![Analysebeispiel](media/data-flow/data-flow-parse-2.png "Analysebeispiel")

In diesem Beispiel haben wir die Analyse des eingehenden Felds "jsonString" definiert, das Nur-Text ist, aber als JSON-Struktur formatiert wurde. Wir werden die analysierten Ergebnisse als JSON in der neuen Spalte „json“ mit folgendem Schema speichern:

```(trade as boolean, customers as string[])```

Überprüfen Sie auf der Registerkarte „Überprüfen“ und in der Datenvorschau, ob Ihre Ausgabe richtig zugeordnet wird.

## <a name="examples"></a>Beispiele

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Beispiele

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json|XML|delimited',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie die [Vereinfachungstransformation](data-flow-flatten.md) zum Pivotieren von Zeilen zu Spalten.
* Verwenden Sie die [Transformation für abgeleitete Spalten](data-flow-derived-column.md) zum Pivotieren von Spalten zu Zeilen.
