---
title: Verwenden externer Tabellen mit Synapse SQL
description: Lesen oder Schreiben von Datendateien mit externen Tabellen in Synapse SQL
services: synapse-analytics
author: ma77b
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 07/23/2021
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14341d2c623ab465e054c83b7b47a100a52f8ece
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054626"
---
# <a name="use-external-tables-with-synapse-sql"></a>Verwenden externer Tabellen mit Synapse SQL

Eine externe Tabelle verweist auf Daten in Hadoop, Azure Storage Blob oder Azure Data Lake Store. Externe Tabellen werden verwendet, um Daten aus Dateien zu lesen oder Daten in Dateien in Azure Storage zu schreiben. Mit Synapse SQL können Sie externe Tabellen verwenden, um externe Daten unter Verwendung eines dedizierten oder serverlosen SQL-Pools zu lesen.

Je nach Art der externen Datenquelle können zwei Arten von externen Tabellen verwendet werden:
- Externe Hadoop-Tabellen zum Lesen und Exportieren von Daten in verschiedenen Datenformaten wie CSV, Parquet und ORC. Die in dedizierten SQL-Pools verfügbaren externen Hadoop-Tabellen sind in serverlosen SQL-Pools nicht verfügbar.
- Native externe Tabellen zum Lesen und Exportieren von Daten in verschiedenen Datenformaten wie CSV und Parquet. Native externe Tabellen sind in serverlosen SQL-Pools verfügbar und befinden sich in dedizierten SQL-Pools in der **öffentlichen Vorschauphase**.

In der folgenden Tabelle sind die wichtigsten Unterschiede zwischen externen Hadoop-Tabellen und nativen externen Tabellen aufgeführt:

| Art der externen Tabelle | Hadoop | Systemeigenes Format |
| --- | --- | --- |
| Dedizierter SQL-Pool | Verfügbar | Parquet-Tabellen sind als **Public Preview** verfügbar. |
| Serverloser SQL-Pool | Nicht verfügbar | Verfügbar |
| Unterstützte Formate | Trennzeichen/CSV, Parquet, ORC, Hive RC und RC | Serverloser SQL-Pool: Trennzeichen/CSV, Parquet und [Delta Lake (Vorschau)](query-delta-lake-format.md)<br/>Dedizierte SQL-Pool: Parquet |
| Ordnerpartitionsentfernung | Nein | Nur für partitionierte Tabellen, die aus Apache Spark-Pools im Synapse-Arbeitsbereich in serverlose SQL-Pools synchronisiert werden |
| Benutzerdefiniertes Format für Speicherort | Yes | Ja, mit Platzhaltern wie `/year=*/month=*/day=*` |
| Rekursive Ordnerüberprüfung | No | Nur in serverlosen SQL-Pools, wenn `/**` am Ende des Pfads zum Speicherort angegeben wurde |
| Pushdown für Speicherfilter | No | Ja im serverlosen SQL-Pool. Für den Zeichenfolgen-Pushdown muss die Sortierung `Latin1_General_100_BIN2_UTF8` für die `VARCHAR`-Spalten verwendet werden. |
| Speicherauthentifizierung | Speicherzugriffsschlüssel (Storage Access Key, SAK), AAD-Passthrough, verwaltete Identität, benutzerdefinierte Azure AD-Anwendungsidentität | Shared Access Signature (SAS), AAD-Passthrough, verwaltete Identität |

> [!NOTE]
> Native externe Tabellen im Delta Lake-Format befinden sich in der öffentlichen Vorschauphase. Weitere Informationen finden Sie unter [Abfragen von Delta Lake-Dateien (Preview) mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics](query-delta-lake-format.md). [CETAS](develop-tables-cetas.md) bietet keine Unterstützung für den Export von Inhalten in das Delta Lake-Format.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Externe Tabellen im dedizierten SQL-Pool und serverlosen SQL-Pool

Externe Tabellen können für Folgendes verwendet werden:

- Abfragen von Azure Blob Storage und Azure Data Lake Gen2 mit Transact-SQL-Anweisungen
- Speichern von Abfrageergebnissen in Dateien in Azure Blob Storage oder Azure Data Lake Storage mithilfe von [CETAS](develop-tables-cetas.md)
- Importieren von Daten aus Azure Blob Storage und Azure Data Lake Storage und Speichern der Daten in einem dedizierten SQL-Pool (nur Hadoop-Tabellen in einem dedizierten Pool)

> [!NOTE]
> In Verbindung mit der Anweisung [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context) werden in einer externen Tabelle ausgewählte Daten in eine Tabelle innerhalb des **dedizierten** SQL-Pools importiert. Zusätzlich zur [COPY-Anweisung](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) sind externe Tabellen hilfreich beim Laden von Daten. 
> 
> Ein Tutorial zum Laden finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Externe Tabellen können mithilfe der folgenden Schritte in Synapse SQL-Pools erstellt werden:

1. Verwenden Sie [CREATE EXTERNAL DATA SOURCE](#create-external-data-source), um auf einen externen Azure-Speicher zu verweisen, und geben Sie die Anmeldeinformationen für den Zugriff auf den Speicher an.
2. Beschreiben Sie das Format von CSV- oder Parquet-Dateien mithilfe von [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).
3. Verwenden Sie [CREATE EXTERNAL TABLE](#create-external-table) für die Dateien in der Datenquelle mit demselben Dateiformat.

### <a name="security"></a>Sicherheit

Der Benutzer muss über die Berechtigung `SELECT` für eine externe Tabelle verfügen, um die Daten lesen zu können.
Externe Tabellen greifen auf den zugrunde liegenden Azure-Speicher mithilfe der datenbankweit gültigen Anmeldeinformationen zu, die in der Datenquelle mit den folgenden Regeln definiert wurden:
- Eine Datenquelle ohne Anmeldeinformationen ermöglicht externen Tabellen den Zugriff auf öffentlich verfügbare Dateien im Azure-Speicher.
- Eine Datenquelle kann über Anmeldeinformationen verfügen, die externen Tabellen den Zugriff nur auf die Dateien im Azure-Speicher mithilfe des SAS-Tokens oder der verwalteten Identität für den Arbeitsbereich ermöglichen. Entsprechende Beispiele finden Sie im Artikel [Develop storage files storage access control](develop-storage-files-storage-access-control.md#examples) (Entwickeln der Speicherzugriffssteuerung für Speicherdateien).

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

Externe Datenquellen dienen zum Herstellen einer Verbindung mit Speicherkonten. Die vollständige Dokumentation finden Sie [hier](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="syntax-for-create-external-data-source"></a>Syntax für „CREATE EXTERNAL DATA SOURCE“

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

Externe Datenquellen mit `TYPE=HADOOP` sind nur in dedizierten SQL-Pools verfügbar.

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="native"></a>[Systemeigenes Format](#tab/native)

Externe Datenquellen ohne `TYPE=HADOOP` sind in serverlosen SQL-Pools allgemein verfügbar und befinden sich in dedizierten Pools in der öffentlichen Vorschauphase.

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argumente für „CREATE EXTERNAL DATA SOURCE“

#### <a name="data_source_name"></a>data_source_name

Gibt den benutzerdefinierten Namen für die Datenquelle an. Dieser Name muss innerhalb der Datenbank eindeutig sein.

#### <a name="location"></a>Standort
„LOCATION = `'<prefix>://<path>'`“: Dient zum Angeben des Konnektivitätsprotokolls und des Pfads der externen Datenquelle. Die folgenden Muster können am Speicherort verwendet werden:

| Externe Datenquelle        | Speicherort-Präfix | Location path (Pfad zum Speicherort)                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Storage Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Storage Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

Mit dem Präfix `https:` können Sie Unterordner im Pfad verwenden.

#### <a name="credential"></a>Anmeldeinformationen
CREDENTIAL = `<database scoped credential>` sind optionale Anmeldeinformationen, die zur Authentifizierung beim Azure-Speicher verwendet werden. Externe Datenquellen ohne Anmeldeinformationen können auf das öffentliche Speicherkonto zugreifen oder die Azure AD-Identität des Aufrufers verwenden, um auf Dateien im Speicher zuzugreifen. 
- Im dedizierten SQL-Pool können datenbankweit gültige Anmeldeinformationen eine benutzerdefinierte Anwendungsidentität, eine verwaltete Identität für den Arbeitsbereich oder einen SAK-Schlüssel angeben. 
- Im serverlosen SQL-Pool können datenbankweit gültige Anmeldeinformationen eine verwaltete Identität für den Arbeitsbereich oder einen SAS-Schlüssel angeben. 

#### <a name="type"></a>TYPE
TYPE = `HADOOP` gibt an, dass Java-basierte Technologie für den Zugriff auf zugrunde liegende Dateien verwendet werden soll. Dieser Parameter kann nicht im serverlosen SQL-Pool verwendet werden, der einen integrierten nativen Reader verwendet.

### <a name="example-for-create-external-data-source"></a>Beispiel für „CREATE EXTERNAL DATA SOURCE“

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

Im folgenden Beispiel wird eine externe Hadoop-Datenquelle im dedizierten SQL-Pool für Azure Data Lake Gen2 erstellt, die auf das Dataset für New York verweist:

```sql
CREATE DATABASE SCOPED CREDENTIAL [ADLS_credential]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

Im folgenden Beispiel wird eine externe Datenquelle für Azure Data Lake Gen2 erstellt, die auf das öffentlich verfügbare Dataset für New York verweist:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/',
       TYPE = HADOOP)
```

#### <a name="native"></a>[Systemeigenes Format](#tab/native)

Im folgenden Beispiel wird eine externe Datenquelle im serverlosen oder dedizierten SQL-Pool für Azure Data Lake Gen2 erstellt, auf die mithilfe von SAS-Anmeldeinformationen zugegriffen werden kann:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

Im folgenden Beispiel wird eine externe Datenquelle für Azure Data Lake Gen2 erstellt, die auf das öffentlich verfügbare Dataset für New York verweist:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Erstellt ein Objekt für ein externes Dateiformat, das externe, in Azure Blob Storage oder Azure Data Lake Storage gespeicherte Daten definiert. Das Erstellen eines externen Dateiformats ist eine Voraussetzung für die Erstellung einer externen Tabelle. Die vollständige Dokumentation finden Sie [hier](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Durch Erstellen eines externen Dateiformats wird das tatsächliche Layout der Daten angegeben, auf die von einer externen Tabelle verwiesen wird.

### <a name="syntax-for-create-external-file-format"></a>Syntax für „CREATE EXTERNAL FILE FORMAT“

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

### <a name="arguments-for-create-external-file-format"></a>Argumente für „CREATE EXTERNAL FILE FORMAT“

„file_format_name“: Dient zum Angeben eines Namens für das externe Dateiformat.

„FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT]“: Dient zum Angeben des Formats der externen Daten.

- PARQUET: Dient zum Angeben eines Parquet-Formats.
- „DELIMITEDTEXT“: Dient zum Angeben eines Textformats mit Spaltentrennzeichen (auch Feldabschlusszeichen genannt).

„FIELD_TERMINATOR = *field_terminator*“: Nur für durch Trennzeichen getrennte Textdateien relevant. Das Feldabschlusszeichen gibt mindestens ein Zeichen an, welches das Ende der einzelnen Felder (Spalten) in der durch Trennzeichen getrennten Textdatei markiert. Als Standardzeichen wird der senkrechte Strich (ꞌ|ꞌ) verwendet.

Beispiele:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter*: Dient zum Angeben des Feldabschlusszeichens für Zeichenfolgendaten in der durch Trennzeichen getrennten Textdatei. Das Zeichenfolgen-Trennzeichen umfasst mindestens ein Zeichen und ist in einfache Anführungszeichen gesetzt. Der Standardwert ist eine leere Zeichenfolge ("").

Beispiele:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int*: Dient zum Angeben der Zeilennummer, die zuerst gelesen wird, und gilt für alle Dateien. Wenn Sie diesen Wert auf „2“ festlegen, wird beim Laden der Daten in allen Dateien jeweils die erste Zeile (Kopfzeile) übersprungen. Zeilen werden basierend auf dem Vorhandensein von Zeilenabschlusszeichen (/ r/n, r, /n) übersprungen.

USE_TYPE_DEFAULT = { TRUE | **FALSE** }: Gibt an, wie fehlende Werte in durch Trennzeichen getrennten Textdateien behandelt werden sollen, wenn Daten aus der Textdatei abgerufen werden.

TRUE: Beim Abrufen von Daten aus der Textdatei werden fehlende Werte jeweils unter Verwendung des Standardwerts für den Datentyp der entsprechenden Spalte in der externen Tabellendefinition gespeichert. Ersetzen Sie einen fehlenden Wert beispielsweise durch:

- 0, wenn die Spalte als numerische Spalte definiert ist. Dezimalspalten werden nicht unterstützt und führen zu einem Fehler.
- Leere Zeichenfolge (""), wenn die Spalte eine Zeichenfolgenspalte ist.
- 1900-01-01, wenn die Spalte eine Datumsspalte ist.

FALSE: Fehlende Werte werden als NULL-Werte gespeichert. Alle NULL-Werte, die durch Verwendung des Worts NULL in der durch Trennzeichen getrennten Textdatei gespeichert werden, werden als Zeichenfolge „NULL“ importiert.

Encoding = {'UTF8' | 'UTF16'}: Vom serverlosen SQL-Pool können UTF8- und UTF16-codierte, durch Trennzeichen getrennte Textdateien gelesen werden.

DATA_COMPRESSION = *data_compression_method*: Dieses Argument dient zum Angeben der Datenkomprimierungsmethode für die externen Daten. 

Der PARQUET-Dateiformattyp unterstützt folgende Komprimierungsmethoden:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Beim Lesen aus externen PARQUET-Tabellen wird dieses Argument ignoriert, beim Schreiben in externe Tabellen mithilfe von [CETAS](develop-tables-cetas.md) aber verwendet.

Der Dateiformattyp DELIMITEDTEXT unterstützt die folgende Komprimierungsmethode:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

„PARSER_VERSION = 'parser_version'“ dient zum Angeben der beim Lesen von CSV-Dateien zu verwendenden Parserversion. Folgende Parserversionen stehen zur Verfügung: `1.0` und `2.0`. Diese Option ist nur in serverlosen SQL-Pools verfügbar.

### <a name="example-for-create-external-file-format"></a>Beispiel für „CREATE EXTERNAL FILE FORMAT“

Im folgenden Beispiel wird ein externes Dateiformat für Zensusdateien erstellt:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

Der Befehl „CREATE EXTERNAL TABLE“ erstellt eine externe Tabelle für Synapse SQL, um den Zugriff auf Daten zu ermöglichen, die in Azure Blob Storage oder Azure Data Lake Storage gespeichert sind. 

### <a name="syntax-for-create-external-table"></a>Syntax für „CREATE EXTERNAL TABLE“

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
        [, TABLE_OPTIONS = N'{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}' ]
        [, <reject_options> [ ,...n ] ] 
    )
[;] 

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]

<reject_options> ::=  
{  
    | REJECT_TYPE = value,  
    | REJECT_VALUE = reject_value,  
    | REJECT_SAMPLE_VALUE = reject_sample_value,
    | REJECTED_ROW_LOCATION = '/REJECT_Directory'
}   
```

### <a name="arguments-create-external-table"></a>Argumente für „CREATE EXTERNAL TABLE“

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Ein- bis dreiteiliger Name der Tabelle, die erstellt werden soll. Bei einer externen Tabelle werden im Synapse SQL-Pool lediglich die Tabellenmetadaten gespeichert. Es werden keine tatsächlichen Daten in die Synapse SQL-Datenbank verschoben oder darin gespeichert.

<column_definition>, ...*n* ]

CREATE EXTERNAL TABLE unterstützt das Konfigurieren von Spaltenname, Datentyp und Sortierung. Sie können DEFAULT CONSTRAINT nicht für externe Tabellen verwenden.

>[!IMPORTANT]
>Die Spaltendefinitionen, einschließlich der Datentypen und der Anzahl der Spalten, müssen mit den Daten in den externen Dateien übereinstimmen. Wenn ein Konflikt besteht, werden die Zeilen der Datei beim Abfragen der tatsächlichen Daten zurückgewiesen. Weitere Informationen zur Steuerung des Verhaltens bei abgelehnten Zeilen finden Sie unter den Reject-Optionen.

Beim Lesen aus Parquet-Dateien können Sie die zu lesenden Spalten angeben und die übrigen Spalten überspringen.


LOCATION = '*folder_or_filepath*'

Dient zum Angeben des Ordners oder des Dateipfads und Dateinamens für die tatsächlichen Daten in Azure Blob Storage. Der Speicherort beginnt im Stammordner. Der Stammordner ist der in der externen Datenquelle angegebene Datenspeicherort.

![Rekursive Daten für externe Tabellen](./media/develop-tables-external-tables/folder-traversal.png)

Im Gegensatz zu externen Hadoop-Tabellen geben native externe Tabellen keine Unterordner zurück, es sei denn, Sie geben „/**“ am Ende des Pfads an. In diesem Beispiel werden von einer Abfrage des serverlosen SQL-Pools Zeilen aus „mydata.txt“ zurückgegeben, wenn „LOCATION='/webdata/'“ angegeben wird. „mydata2.txt“ und „mydata3.txt“ werden nicht zurückgegeben, da sie sich in einem Unterordner befinden. Von Hadoop-Tabellen werden alle Dateien in einem beliebigen Unterordner zurückgegeben.

Dateien, deren Name mit einem Unterstrich (_) oder Punkt (.) beginnt, werden sowohl bei externen Hadoop-Tabellen als auch bei nativen externen Tabellen übersprungen.


DATA_SOURCE = *external_data_source_name*

Gibt den Namen der externen Datenquelle an, die den Speicherort der externen Daten enthält. Verwenden Sie zum Erstellen einer externen Datenquelle [CREATE EXTERNAL DATA SOURCE](#create-external-data-source).


FILE_FORMAT = *external_file_format_name*

Gibt den Namen des externen Dateiformatobjekts an, das den Dateityp und die Komprimierungsmethode der externen Daten speichert. Verwenden Sie zum Erstellen eines externen Dateiformats [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

Reject-Optionen 

> [!NOTE]
> Das Feature für abgelehnte Zeilen befindet sich in der Public Preview.
> Beachten Sie, dass das Feature für abgelehnte Zeilen nur für Textdateien mit Trennzeichen und PARSER_VERSION 1.0 funktioniert.

Sie können Reject-Parameter angeben, die bestimmen, wie der Dienst *modifizierte* Datensätze behandelt, die aus der externen Datenquelle abgerufen werden. Ein Datensatz gilt als „dirty“ (modifiziert), wenn die tatsächlichen Datentypen nicht den Spaltendefinitionen der externen Tabelle entsprechen.

Wenn Sie die Reject-Optionen nicht angeben oder ändern, verwendet der Dienst Standardwerte. Diese Informationen über die Reject-Parameter werden als zusätzliche Metadaten gespeichert, wenn Sie eine externe Tabelle mit der CREATE EXTERNAL TABLE-Anweisung erstellen. Wenn eine zukünftige SELECT- oder SELECT INTO SELECT-Anweisung Daten aus der externen Tabelle auswählt, wird der Dienst die Reject-Optionen verwenden, um die Anzahl der Zeilen zu bestimmen, die zurückgewiesen werden können, bevor die tatsächliche Abfrage fehlschlägt. Die Abfrage gibt (Teil-) Ergebnisse zurück, bis der Reject-Schwellenwert überschritten wird. Daraufhin wird eine entsprechende Fehlermeldung ausgelöst.


REJECT_TYPE = **value** 

Dies ist derzeit der einzige unterstützte Wert. Dies verdeutlicht, dass die Option REJECT_VALUE als Literalwert angegeben ist.

value 

REJECT_VALUE ist ein Literalwert. Die Abfrage schlägt fehl, wenn die Anzahl der abgelehnten Zeilen *reject_value* überschreitet.

Die SELECT-Abfrage schlägt beispielsweise bei „REJECT_VALUE = 5“ und „REJECT_TYPE = value“ fehl, nachdem fünf Zeilen abgelehnt wurden.


REJECT_VALUE = *reject_value* 

Gibt die Anzahl von Zeilen an, die abgelehnt werden können, bevor für die Abfrage ein Fehler auftritt.

Wenn REJECT_TYPE = Wert, muss *reject_value* eine ganze Zahl zwischen 0 und 2.147.483.647 sein.


REJECTED_ROW_LOCATION = *Verzeichnis*

Gibt das Verzeichnis in der externen Datenquelle an, in das die abgelehnten Zeilen und die entsprechende Fehlerdatei geschrieben werden sollen. Ist das angegebene Verzeichnis nicht vorhanden, wird es vom Dienst für Sie erstellt. Es wird ein untergeordnetes Verzeichnis mit dem Namen „_rejectedrows“ erstellt. Mit dem „_ “-Zeichen wird sichergestellt, dass das Verzeichnis für andere Datenverarbeitungsvorgänge übergangen wird, es sei denn, es ist explizit im LOCATION-Parameter angegeben. In diesem Verzeichnis befindet sich ein Ordner, der ausgehend von der Zeit der Lastübermittlung im Format „JahrMonatTag_StundeMinuteSekunde_Anweisungs-ID“ erstellt wurde (z. B. 20180330-173205-559EE7D2-196D-400A-806D-3BF5D007F891). Sie können die Anweisungs-ID verwenden, um den Ordner mit der Abfrage zu korrelieren, von der er generiert wurde. In diesen Ordner werden zwei Dateien geschrieben: die Datei „error.json“ und die Datendatei. 

Die Datei error.json enthält ein JSON-Array mit den aufgetretenen Fehlern im Zusammenhang mit abgelehnten Zeilen. Jedes Element, das einen Fehler darstellt, enthält die folgenden Attribute:

| attribute | BESCHREIBUNG                                                  |
| --------- | ------------------------------------------------------------ |
| Fehler     | Der Grund, warum die Zeile abgelehnt wird.                                  |
| Zeile       | Die Ordinalzahl der abgelehnten Zeile in der Datei.                         |
| Column    | Die Ordinalzahl der abgelehnten Spalte.                              |
| Wert     | Der Wert der abgelehnten Spalte. Wenn der Wert größer als 100 Zeichen ist, werden nur die ersten 100 Zeichen angezeigt. |
| Datei      | Der Pfad zur Datei, zu der die Zeile gehört.                            |

#### <a name="table_options"></a>TABLE_OPTIONS

TABLE_OPTIONS = *JSON-Optionen*: Gibt die Optionen an, die beschreiben, wie die zugrunde liegenden Dateien gelesen werden sollen. Derzeit ist nur die Option `"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]` verfügbar. Durch diese Option wird die externe Tabelle angewiesen, Aktualisierungen der zugrunde liegenden Dateien zu ignorieren, auch wenn dies unter Umständen zu inkonsistenten Lesevorgängen führt. Verwenden Sie diese Option nur in Sonderfällen, in denen Sie häufig Dateien angefügt haben. Diese Option steht im serverlosen SQL-Pool für das CSV-Format zur Verfügung.

### <a name="permissions-create-external-table"></a>Berechtigungen für „CREATE EXTERNAL TABLE“

Sie benötigen geeignete Anmeldeinformationen mit Berechtigungen zum Auflisten und Lesen, um etwas in einer externen Tabelle auswählen zu können.

### <a name="example-create-external-table"></a>Beispiel für „CREATE EXTERNAL TABLE“

Im folgenden Beispiel wird eine externe Tabelle erstellt und die erste Zeile zurückgegeben:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Erstellen und Abfragen externer Tabellen auf der Grundlage einer Datei in Azure Data Lake

Mithilfe der Data Lake-Erkundungsfunktionen von Synapse Studio können Sie nun mit einem einfachen Rechtsklick auf die Datei eine externe Tabelle unter Verwendung eines Synapse SQL-Pools erstellen und abfragen. Die 1-Klick-Geste zum Erstellen externer Tabellen aus dem ADLS Gen2-Speicherkonto wird nur für Parquet-Dateien unterstützt. 

### <a name="prerequisites"></a>Voraussetzungen

- Für den Zugriff auf den Arbeitsbereich müssen Sie mindestens über die Zugriffsrolle `Storage Blob Data Contributor` für das ADLS Gen2-Konto verfügen. Alternativ werden Zugriffssteuerungslisten benötigt, mit denen Sie die Dateien abfragen können.

- Sie müssen mindestens über [Berechtigungen zum Erstellen](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true) und Abfragen externer Tabellen im Synapse SQL-Pool (dediziert oder serverlos) verfügen.

Wählen Sie im Datenbereich die Datei aus, auf deren Grundlage Sie die externe Tabelle erstellen möchten:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Daraufhin wird ein Dialogfenster geöffnet. Wählen Sie die Option für den dedizierten oder den serverlosen SQL-Pool aus, geben Sie einen Namen für die Tabelle ein, und wählen Sie „Skript öffnen“ aus:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Das SQL-Skript wird automatisch generiert, und das Schema wird aus der Datei abgeleitet:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Führen Sie das Skript aus. Durch das Skript wird automatisch eine Abfrage vom Typ „Top 100 auswählen“ ausgeführt:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Die externe Tabelle ist nun erstellt. Zur späteren Erkundung ihres Inhalts kann der Benutzer sie direkt über den Datenbereich abfragen:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Nächste Schritte

Im [Artikel zu CETAS](develop-tables-cetas.md) erfahren Sie, wie Sie die Abfrageergebnisse in einer externen Tabelle in Azure Storage speichern. Oder Sie können damit beginnen, [Apache Spark für externe Azure Synapse-Tabellen](develop-storage-files-spark-tables.md) abzufragen.
