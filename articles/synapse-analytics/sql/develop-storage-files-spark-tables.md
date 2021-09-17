---
title: Synchronisieren von Apache Spark für externe Tabellendefinitionen im serverlosen SQL-Pool
description: Übersicht über das Abfragen von Spark-Tabellen mithilfe des serverlosen SQL-Pools
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 642024d9554b51bc60df90cf3d5a7bdd799440b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432088"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Synchronisieren von Apache Spark für externe Azure Synapse-Tabellendefinitionen im serverlosen SQL-Pool

Der serverlose SQL-Pool kann Metadaten aus Apache Spark automatisch synchronisieren. Für jede in serverlosen Apache Spark-Pools vorhandene Datenbank wird eine Datenbank im serverlosen SQL-Pool erstellt. 

Für jede externe Spark-Tabelle, die auf Parquet oder CSV basiert und sich in Azure Storage befindet, wird in der Datenbank im serverlosen SQL-Pool eine externe Tabelle erstellt. Aus diesem Grund können Sie Ihre Spark-Pools herunterfahren und dennoch externe Spark-Tabellen über den serverlosen SQL-Pool abfragen.

Wenn Tabelle in Spark partitioniert ist, werden die Dateien im Speicher in Ordner organisiert. Der serverlose SQL-Pool verwendet Partitionsmetadaten und bezieht nur relevante Ordner und Dateien in Ihre Abfrage ein.

Die Synchronisierung von Metadaten wird für jeden im Azure Synapse-Arbeitsbereich bereitgestellten serverlosen Apache Spark-Pool automatisch konfiguriert. Sie können mit dem Abfragen von externen Spark-Tabellen sofort beginnen.

Jede externe Spark Parquet oder CSV-Tabelle, die sich in Azure Storage befindet, wird durch eine externe Tabelle in einem dbo-Schema dargestellt, das einer Datenbank im serverlosen SQL-Pool entspricht. 

Führen Sie bei externen Spark-Tabellen eine Abfrage in einer externen „[spark_table]“ aus. Bevor Sie das folgende Beispiel ausführen, stellen Sie sicher, dass Sie ordnungsgemäßen [Zugriff auf das Speicherkonto haben](develop-storage-files-storage-access-control.md), in dem sich die Dateien befinden.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Zuordnung von Apache Spark-Datentypen zu SQL-Datentypen

| Spark-Datentyp | SQL-Datentyp | Kommentare |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark**: *LongType* stellt 8-Byte-Ganzzahlen mit Vorzeichen dar. [Referenz](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit` (Parquet), `varchar(6)` (CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark**: *DecimalType* stellt Dezimalzahlen mit Vorzeichen und variabler Genauigkeit dar. Wird intern durch „java.math.BigDecimal“ unterstützt. Ein BigDecimal-Wert besteht aus einem unskalierten Ganzzahlwert mit variabler Genauigkeit und einer 32-Bit-Ganzzahlskalierung. <br> **SQL**: Zahlen mit fester Genauigkeit und mit fester Anzahl von Dezimalstellen. Wenn maximale Genauigkeit verwendet wird, liegen gültige Werte zwischen - 10^38 +1 und 10^38 - 1. Die ISO-Synonyme für decimal lauten dec und dec(p, s) . Die Funktion von numeric ist mit der von decimal identisch. [Referenz](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark** *IntegerType* stellt 4-Byte-Ganzzahlen mit Vorzeichen dar. [Referenz](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark**: *ByteType* stellt 1-Byte-Ganzzahlen mit Vorzeichen [-128 bis 127] dar, und ShortType stellt 2-Byte-Ganzzahlen mit Vorzeichen [-32768 bis 32767] dar. <br> **SQL**: Tinyint stellt 1-Byte-Ganzzahlen mit Vorzeichen [0, 255] dar, und smallint stellt 2-Byte-Ganzzahlen mit Vorzeichen [-32768, 32767] dar. [Referenz](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | Wie oben. |
| `DoubleType`, `Double`                      | `float`               | **Spark**: *DoubleType* stellt Gleitkommazahlen mit doppelter Genauigkeit und 8 Bytes dar. Weitere Informationen zu **SQL** finden Sie auf [dieser Seite](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `FloatType`, `float`, `real`                | `real`                | **Spark**: *FloatType* stellt Gleitkommazahlen mit doppelter Genauigkeit und 4 Bytes dar. Weitere Informationen zu **SQL** finden Sie auf [dieser Seite](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `DateType`, `date`                          | `date`                | **Spark**: *DateType* stellt Werte dar, die sich aus Feldern für Jahr, Monat und Tag ohne Zeitzone zusammensetzen.|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark**: *TimestampType* stellt Werte dar, die sich aus Feldern für Jahr, Monat, Tag, Stunde, Minute und Sekunde mit der lokalen Zeitzone der Sitzung zusammensetzen. Der Zeitstempelwert stellt einen absoluten Zeitpunkt dar.
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* stellt Zeichenfolgenwerte dar. *VarcharType(n)* ist eine Variante von StringType, für die eine Längenbeschränkung gilt. Beim Schreiben von Daten tritt ein Fehler auf, wenn die Eingabezeichenfolge die zugelassene Länge überschreitet. Dieser Typ kann nur im Tabellenschema verwendet werden, nicht in Funktionen oder Operatoren.<br> *CharType(n)* ist eine Variante von *VarcharType(n)* mit fester Länge. Beim Lesen der Spalte vom Typ *CharType(n)* werden immer Zeichenfolgenwerte der Länge n zurückgegeben. Beim Vergleich der Zeichentypspalte wird die kürzere Zeichenfolge durch Auffüllen an die längere Zeichenfolge angeglichen. <br> **SQL**: In *Varchar(n)* kann n maximal 8.000 betragen. Handelt es sich um eine partitionierte Spalte, kann n maximal 2.048 betragen. <br> Verwenden Sie die Angabe mit der Sortierung `Latin1_General_100_BIN2_UTF8`. |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL**: In *Varbinary(n)* kann n maximal 8.000 betragen. Handelt es sich um eine partitionierte Spalte, kann n maximal 2.048 betragen. |
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL**: Wird in JSON mit Sortierung `Latin1_General_100_BIN2_UTF8` serialisiert. |

\* Sortierung auf Datenbankebene: Latin1_General_100_CI_AS_SC_UTF8 \* Sortierung auf Zeichenfolgenspaltenebene: Latin1_General_100_BIN2_UTF8

\*** ArrayType, MapType und StructType werden als JSONs dargestellt.

## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Steuerung des Speicherzugriffs](develop-storage-files-storage-access-control.md) erfahren Sie mehr über die Steuerung des Speicherzugriffs.
