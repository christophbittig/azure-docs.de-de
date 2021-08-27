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
ms.openlocfilehash: c7c2123b84aa6e3da362df9ce47d1e7780e8a856
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441758"
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

> [!NOTE]
> Das Hinzufügen, Löschen oder Ändern von externen Spark-Tabellenbefehlen für eine Spalte wird in externen Tabellen im serverlosen SQL-Pool nicht widergespiegelt.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Zuordnung von Apache Spark-Datentypen zu SQL-Datentypen

| Spark-Datentyp | SQL-Datentyp               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| ShortType      | SMALLINT                    |
| IntegerType     | INT                         |
| LongType        | BIGINT                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | Decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (in JSON)\** |
| MapType         | varchar(max)\* (in JSON)\** |
| StructType      | varchar(max)\* (in JSON)\** |

\* Die verwendete Sortierung ist Latin1_General_100_BIN2_UTF8.

\*** ArrayType, MapType und StructType werden als JSONs dargestellt.



## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Steuerung des Speicherzugriffs](develop-storage-files-storage-access-control.md) erfahren Sie mehr über die Steuerung des Speicherzugriffs.
