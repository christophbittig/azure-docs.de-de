---
title: Von Azure Synapse Data Explorer für die Erfassung unterstützte Datenformate.
description: Hier erfahren Sie mehr über die verschiedenen Daten- und Komprimierungsformate, die von Azure Synapse Data Explorer für die Erfassung unterstützt werden.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: f38325e37b548c7f8a99d38eacae26aa24756250
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478616"
---
# <a name="data-formats-supported-by-azure-synapse-data-explorer-for-ingestion-preview"></a>Von Azure Synapse Data Explorer für die Erfassung unterstützten Datenformate (Vorschau)

Bei der Datenerfassung werden Daten einer Tabelle hinzugefügt und für Abfragen im Data Explorer verfügbar gemacht. Mit Ausnahme der Erfassung aus einer Abfrage müssen die Daten bei allen Erfassungsmethoden in einem der unterstützten Formate vorliegen. In der folgenden Tabelle sind die Formate aufgeführt und beschrieben, die von Data Explorer für die Datenerfassung unterstützt werden.

> [!NOTE]
> Stellen Sie vor der Erfassung von Daten sicher, dass Ihre Daten ordnungsgemäß formatiert sind, und definieren Sie die erwarteten Felder. Es wird empfohlen, Ihr bevorzugtes Validierungssteuerelement zu verwenden, um zu überprüfen, ob das Format gültig ist. Die folgenden Validierungssteuerelemente können beispielsweise nützlich sein, um CSV- oder JSON-Dateien zu überprüfen:
>
> * CSV: http://csvlint.io/
> * JSON: https://jsonlint.com/
>
> Weitere Informationen zu möglichen Fehlerursachen bei der Erfassung finden Sie unter [Erfassungsfehler](/azure/data-explorer/kusto/management/ingestionfailures?context=/azure/synapse-analytics/context/context) und [Erfassungsfehlercodes in Data Explorer](/azure/data-explorer/error-codes?context=/azure/synapse-analytics/context/context).

|Format   |Durchwahl   |BESCHREIBUNG|
|---------|------------|-----------|
|ApacheAvro|`.avro`    |Ein [AVRO](https://avro.apache.org/docs/current/)-Format mit Unterstützung von [logischen Typen](https://avro.apache.org/docs/current/spec.html#Logical+Types). Folgende Komprimierungscodecs werden unterstützt: `null`, `deflate` und `snappy`. Die Readerimplementierung des Formats `apacheavro` basiert auf der [offiziellen Apache Avro-Bibliothek](https://github.com/apache/avro).|
|Avro     |`.avro`     |Eine Legacyimplementierung des [AVRO](https://avro.apache.org/docs/current/)-Formats, die auf der [.NET-Bibliothek](https://www.nuget.org/packages/Microsoft.Hadoop.Avro) basiert. Folgende Komprimierungscodecs werden unterstützt: `null` und `deflate`. (Verwenden Sie für `snappy` das Dateiformat `ApacheAvro`.)|
|CSV      |`.csv`      |Eine Textdatei mit kommagetrennten Werten (`,`). Weitere Informationen finden Sie unter [RFC 4180: _Common Format and MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt) (RFC 4180: Allgemeines Format und MIME-Typ für CSV-Dateien).|
|JSON     |`.json`     |Eine Textdatei mit JSON-Objekten getrennt durch `\n` oder `\r\n`. Weitere Informationen finden Sie unter [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Eine Textdatei mit einem JSON-Array mit Eigenschaftenbehältern (die jeweils einen Datensatz darstellen) oder einer beliebigen Anzahl von durch Leerzeichen getrennten Eigenschaftenbehältern, `\n` oder `\r\n`. Jeder Eigenschaftenbehälter kann auf mehrere Zeilen verteilt sein. Dieses Format ist `JSON` vorzuziehen, es sei denn, bei den Daten handelt es sich nicht um Eigenschaftenbehälter.|
|ORC      |`.orc`      |Eine [ORC-Datei](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |Eine [Parquet-Datei](https://en.wikipedia.org/wiki/Apache_Parquet) |
|PSV      |`.psv`      |Eine Textdatei mit durch senkrechte Striche getrennten Werten (<code>&#124;</code>).|
|RAW      |`.raw`      |Eine Textdatei, deren gesamter Inhalt ein einzelner Zeichenfolgenwert ist.|
|SCsv     |`.scsv`     |Eine Textdatei mit durch Semikolons getrennten Werten (`;`).|
|SOHsv    |`.sohsv`    |Eine Textdatei mit SOH-getrennten Werten. (SOH ist der ASCII-Codepunkt 1. Dieses Format wird von Hive in HDInsight verwendet.)|
|TSV      |`.tsv`      |Eine Textdatei mit durch Tabstopps getrennten Werten (`\t`).|
|TSVE     |`.tsv`      |Eine Textdatei mit durch Tabstopps getrennten Werten (`\t`). Als Escapezeichen wird ein umgekehrter Schrägstrich (`\`) verwendet.|
|TXT      |`.txt`      |Eine Textdatei mit durch `\n` getrennten Zeilen. Leere Zeilen werden übersprungen.|
|W3CLOGFILE |`.log`    |Vom W3C standardisiertes Format der [Webprotokolldatei](https://www.w3.org/TR/WD-logfile.html) |

## <a name="supported-data-compression-formats"></a>Unterstützte Datenkomprimierungsformate

Blobs und Dateien können mit einem der folgenden Komprimierungsalgorithmen komprimiert werden:

|Komprimierung|Durchwahl|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Geben Sie die Komprimierung an, indem Sie die Erweiterung dem Namen des Blobs oder der Datei anhängen.

Beispiel:

* `MyData.csv.zip` gibt an, dass ein Blob oder eine Datei als CSV formatiert und mit ZIP (Archiv oder einzelne Datei) komprimiert ist.
* `MyData.json.gz` gibt an, dass ein Blob oder eine Datei als JSON formatiert und mit gzip komprimiert ist.

Blob- oder Dateinamen, die nicht die Formaterweiterungen, sondern nur die Komprimierung (z. B. `MyData.zip`) enthalten, werden ebenfalls unterstützt. In diesem Fall muss das Dateiformat als Erfassungseigenschaft angegeben werden, da es nicht abgeleitet werden kann.

> [!NOTE]
> * Bei einigen Komprimierungsformaten wird die ursprüngliche Dateierweiterung in den komprimierten Datenstrom aufgenommen. Diese Erweiterung wird bei der Bestimmung des Dateiformats generell ignoriert. Kann das Dateiformat nicht anhand des Namens des (komprimierten) Blobs oder der (komprimierten) Datei bestimmt werden, muss es mithilfe der Erfassungseigenschaft `format` angegeben werden.
> * Nicht zu verwechseln mit dem internen Komprimierungscodec (Blockebene), der von den Formaten `Parquet`, `AVRO` und `ORC` verwendet wird. Der Name der internen Komprimierung wird einem Dateinamen in der Regel vor der Dateiformaterweiterung hinzugefügt. Beispiele: `file1.gz.parquet`, `file1.snappy.avro`, usw.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Datenerfassung](data-explorer-ingest-data-overview.md).
- Weitere Informationen zu den [Datenerfassungseigenschaften](data-explorer-ingest-data-properties.md)
