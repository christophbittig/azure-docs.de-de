---
title: Tabellen mit gemeinsam genutzten Metadaten
description: Azure Synapse Analytics bietet ein Modell mit gemeinsam genutzten Metadaten, das es ermöglicht, über einen serverlosen SQL-Pool und einen dedizierten SQL-Pool auf eine in einem serverlosen Apache Spark-Pool erstellte Tabelle zuzugreifen, ohne die Daten zu duplizieren.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 9852f146651ca6bcb5c1935ca78fce61bca5093f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433312"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics: Gemeinsam genutzte Metadatentabellen


Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Tabellen zwischen den Apache Spark-Pools und dem serverlosen SQL-Pool.

Nach der Erstellung einer Datenbank durch einen Spark-Auftrag können darin Tabellen mit Spark erstellt werden, die Parquet oder CSV als Speicherformat verwenden. Tabellennamen werden in Kleinbuchstaben konvertiert und müssen mit dem Namen in Kleinbuchstaben abgefragt werden. Diese Tabellen stehen umgehend für Abfragen von beliebigen Spark-Pools des Azure Synapse-Arbeitsbereichs zur Verfügung. Darüber hinaus können sie von einem beliebigen Spark-Auftrag verwendet werden (entsprechende Berechtigungen vorausgesetzt).

Die von Spark erstellten, verwalteten und externen Tabellen werden auch als externe Tabellen mit demselben Namen in der entsprechenden synchronisierten Datenbank im serverlosen SQL-Pool verfügbar gemacht. Unter [Verfügbarmachen einer Spark-Tabelle in SQL](#expose-a-spark-table-in-sql) finden Sie weitere Details zur Tabellensynchronisierung.

Da die Tabellen asynchron mit dem serverlosen SQL-Pool synchronisiert werden, kommt es zu einer kleinen Verzögerung bei der Anzeige.

## <a name="manage-a-spark-created-table"></a>Verwalten einer von Spark erstellten Tabelle

Verwalten Sie von Spark erstellte Datenbanken mithilfe von Spark. Löschen Sie sie beispielsweise über einen Auftrag für serverlose Apache Spark-Pools, und erstellen Sie Tabellen in der Datenbank über Spark.

Objekte in synchronisierten Datenbanken können nicht über einen serverlosen SQL-Pool geändert werden.

## <a name="expose-a-spark-table-in-sql"></a>Verfügbarmachen einer Spark-Tabelle in SQL

### <a name="shared-spark-tables"></a>Gemeinsam genutzte Spark-Tabellen

In Spark stehen zwei Arten von Tabellen zur Verfügung, die von Azure Synapse automatisch in SQL verfügbar gemacht werden:

- Verwaltete Tabellen

  Spark bietet zahlreiche Optionen für die Speicherung von Daten in verwalteten Tabellen. Hierzu zählen beispielsweise TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA und LIBSVM. Diese Dateien werden für gewöhnlich im Verzeichnis `warehouse` gespeichert, in dem die Daten der verwalteten Tabelle gespeichert werden.

- Externe Tabellen

  Spark ermöglicht auch die Erstellung externer Tabellen auf der Grundlage vorhandener Daten – entweder durch Angabe der Option `LOCATION`oder durch Verwendung des Hive-Formats. Solche externen Tabellen können auf einer Vielzahl von Datenformaten basieren (einschließlich Parquet).

Azure Synapse gibt aktuell nur verwaltete und externe Spark-Tabellen mit im Parquet- oder CSV-Format gespeicherten Daten für die SQL-Engines frei. Auf anderen Formaten basierende Tabellen werden nicht automatisch synchronisiert. Solche Tabellen können Sie ggf. explizit selbst als externe Tabelle in Ihrer eigenen SQL-Datenbank synchronisieren, sofern die SQL-Engine das zugrunde liegende Format der Tabelle unterstützt.

### <a name="share-spark-tables"></a>Gemeinsames Nutzen von Spark-Tabellen

Die gemeinsam nutzbaren verwalteten und externen Spark-Tabellen werden in der SQL-Engine als externe Tabellen mit folgenden Eigenschaften verfügbar gemacht:

- Die Datenquelle der externen SQL-Tabelle ist die Datenquelle, die den Ordner des Spark-Tabellenspeicherorts darstellt.
- Die externe SQL-Tabelle hat das Parquet-Dateiformat.
- Für die Zugriffsanmeldeinformationen der externen SQL-Tabelle wird das Pass-Through-Verfahren verwendet.

Da alle Spark-Tabellennamen gültige SQL-Tabellennamen und alle Spark-Spaltennamen gültige SQL-Spaltennamen sind, werden für die externe SQL-Tabelle die Spark-Tabellennamen und -Spaltennamen verwendet.

Spark-Tabellen bieten andere Datentypen als die SQL-Engines von Synapse. In der folgenden Tabelle werden die Spark-Tabellendatentypen den SQL-Typen zugeordnet:

| Spark-Datentyp | SQL-Datentyp | Kommentare |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark**: *LongType* stellt 8-Byte-Ganzzahlen mit Vorzeichen dar. [Referenz](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit` (Parquet), `varchar(6)` (CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark**: *DecimalType* stellt Dezimalzahlen mit Vorzeichen und variabler Genauigkeit dar. Wird intern durch „java.math.BigDecimal“ unterstützt. Ein BigDecimal-Wert besteht aus einem unskalierten Ganzzahlwert mit variabler Genauigkeit und einer 32-Bit-Ganzzahlskalierung. <br> **SQL**: Zahlen mit fester Genauigkeit und mit fester Anzahl von Dezimalstellen. Wenn maximale Genauigkeit verwendet wird, liegen gültige Werte zwischen - 10^38 +1 und 10^38 - 1. Die ISO-Synonyme für decimal lauten dec und dec(p, s) . Die Funktion von numeric ist mit der von decimal identisch. [Referenz](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark** *IntegerType* stellt 4-Byte-Ganzzahlen mit Vorzeichen dar. [Referenz](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark**: *ByteType* stellt 1-Byte-Ganzzahlen mit Vorzeichen [-128 bis 127] dar, und „ShortType“ stellt 2-Byte-Ganzzahlen mit Vorzeichen [-32.768 bis 32.767] dar. <br> **SQL**: „tinyint“ stellt 1-Byte-Ganzzahlen mit Vorzeichen [0, 255] dar, und „smallint“ stellt 2-Byte-Ganzzahlen mit Vorzeichen [-32.768, 32.767] dar. [Referenz](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | Wie oben. |
| `DoubleType`, `Double`                      | `float`               | **Spark**: *DoubleType* stellt Gleitkommazahlen mit doppelter Genauigkeit und 8 Bytes dar. Weitere Informationen zu **SQL** finden Sie auf [dieser Seite](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `FloatType`, `float`, `real`                | `real`                | **Spark**: *FloatType* stellt Gleitkommazahlen mit doppelter Genauigkeit und 4 Bytes dar. Weitere Informationen zu **SQL** finden Sie auf [dieser Seite](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `DateType`, `date`                          | `date`                | **Spark**: *DateType* stellt Werte dar, die sich aus Feldern für Jahr, Monat und Tag ohne Zeitzone zusammensetzen.|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark**: *TimestampType* stellt Werte dar, die sich aus Feldern für Jahr, Monat, Tag, Stunde, Minute und Sekunde mit der lokalen Zeitzone der Sitzung zusammensetzen. Der Zeitstempelwert stellt einen absoluten Zeitpunkt dar.
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* stellt Zeichenfolgenwerte dar. *VarcharType(n)* ist eine Variante von „StringType“, für die eine Längenbeschränkung gilt. Beim Schreiben von Daten tritt ein Fehler auf, wenn die Eingabezeichenfolge die zugelassene Länge überschreitet. Dieser Typ kann nur im Tabellenschema verwendet werden, nicht in Funktionen oder Operatoren.<br> *CharType(n)* ist eine Variante von *VarcharType(n)* mit fester Länge. Beim Lesen der Spalte vom Typ *CharType(n)* werden immer Zeichenfolgenwerte der Länge n zurückgegeben. Beim Vergleich der Zeichentypspalte wird die kürzere Zeichenfolge durch Auffüllen an die längere Zeichenfolge angeglichen. <br> **SQL**: In *Varchar(n)* kann n maximal 8.000 betragen. Handelt es sich um eine partitionierte Spalte, kann n maximal 2.048 betragen. <br> Verwenden Sie die Angabe mit der Sortierung `Latin1_General_100_BIN2_UTF8`. |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL**: In *Varbinary(n)* kann n maximal 8.000 betragen. Handelt es sich um eine partitionierte Spalte, kann n maximal 2.048 betragen. |
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL**: Wird mit der Sortierung `Latin1_General_100_BIN2_UTF8` in JSON serialisiert. |

\* Sortierung auf Datenbankebene: Latin1_General_100_CI_AS_SC_UTF8 \* Sortierung auf Zeichenfolgenspaltenebene: Latin1_General_100_BIN2_UTF8

\*** ArrayType, MapType und StructType werden als JSONs dargestellt.

## <a name="security-model"></a>Sicherheitsmodell

Die Spark-Datenbanken und -Tabellen sowie deren synchronisierte Darstellungen in der SQL-Engine werden auf der zugrunde liegenden Speicherebene geschützt. Da sie derzeit nicht über Berechtigungen für die eigentlichen Objekte verfügen, können die Objekte im Objekt-Explorer angezeigt werden.

Der Sicherheitsprinzipal, von dem eine verwaltete Tabelle erstellt wird, gilt als Besitzer der Tabelle und verfügt über alle Rechte für die Tabelle sowie für die zugrunde liegenden Ordner und Dateien. Der Besitzer der Datenbank wird zudem automatisch zum Mitbesitzer der Tabelle.

Wenn Sie eine externe Spark- oder SQL-Tabelle mit Passthrough-Authentifizierung erstellen, werden die Daten nur auf der Ordner- und Dateiebene geschützt. Bei einer Abfrage für diese externe Tabelle wird die Sicherheitsidentität des Abfrageübermittlers an das Dateisystem übergeben und auf Zugriffsrechte überprüft.

Weitere Informationen zum Festlegen von Berechtigungen für die Ordner und Dateien finden Sie unter [Azure Synapse Analytics: Gemeinsam genutzte Datenbank](database.md).

## <a name="examples"></a>Beispiele

### <a name="create-a-managed-table-in-spark-and-query-from-serverless-sql-pool"></a>Erstellen einer verwalteten Tabelle in Spark und Ausführen von Abfragen über einen serverlosen SQL-Pool

In diesem Szenario verfügen Sie über eine Spark-Datenbank namens `mytestdb`. Informationen finden Sie unter [Erstellen einer Spark-Datenbank mit einem serverlosen SQL-Pool und Herstellen einer Verbindung mit dieser Datenbank](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Führen Sie den folgenden Befehl aus, um mit SparkSQL eine verwaltete Spark-Tabelle zu erstellen:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Mit diesem Befehl wird die Tabelle `myparquettable` in der Datenbank `mytestdb` erstellt. Tabellennamen werden in Kleinbuchstaben konvertiert. Nach einer kurzen Verzögerung wird die Tabelle aus dem serverlosen SQL-Pool angezeigt. Führen Sie beispielsweise die folgende Anweisung über den serverlosen SQL-Pool aus:

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Vergewissern Sie sich, dass `myparquettable` in den Ergebnissen enthalten ist.

>[!NOTE]
>Tabellen mit einem anderen Speicherformat als Parquet oder CSV werden nicht synchronisiert.

Fügen Sie als Nächstes über Spark einige Werte in die Tabelle ein. Verwenden Sie dazu beispielsweise die folgenden C#-Spark-Anweisungen in einem C#-Notebook:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Nun können Sie die Daten wie folgt aus dem serverlosen SQL-Pool lesen:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Als Ergebnis sollte die folgende Zeile zurückgegeben werden:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-in-spark-and-query-from-serverless-sql-pool"></a>Erstellen einer externen Tabelle in Spark und Ausführen von Abfragen über einen serverlosen SQL-Pool

In diesem Beispiel wird eine externe Spark-Tabelle auf der Grundlage der Parquet-Datendateien erstellt, die im vorherigen Beispiel für die verwaltete Tabelle erstellt wurden.

Führen Sie mit SparkSQL beispielsweise Folgendes aus:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Ersetzen Sie den Platzhalter `<storage-name>` durch den verwendeten Namen des ADLS Gen2-Speicherkontos, den Platzhalter `<fs>` durch den verwendeten Dateisystemnamen und den Platzhalter `<synapse_ws>` durch den Namen des für dieses Beispiel verwendeten Synapse-Arbeitsbereichs.

Im vorherigen Beispiel wurde die Tabelle `myextneralparquettable` in der Datenbank `mytestdb` erstellt. Nach einer kurzen Verzögerung wird die Tabelle aus dem serverlosen SQL-Pool angezeigt. Führen Sie beispielsweise die folgende Anweisung über den serverlosen SQL-Pool aus:

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Vergewissern Sie sich, dass `myexternalparquettable` in den Ergebnissen enthalten ist.

Nun können Sie die Daten wie folgt aus dem serverlosen SQL-Pool lesen:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

Als Ergebnis sollte die folgende Zeile zurückgegeben werden:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu gemeinsam genutzten Metadaten von Azure Synapse Analytics finden Sie [hier](overview.md).
- Weitere Informationen zu gemeinsam genutzten Azure Synapse Analytics-Datenbanken für Metadaten finden Sie [hier](database.md).


