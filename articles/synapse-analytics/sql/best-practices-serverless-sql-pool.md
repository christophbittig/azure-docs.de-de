---
title: Best Practices für serverlose SQL-Pools
description: Empfehlungen und bewährte Methoden für die Arbeit mit serverlosen SQL-Pools.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 655294aaf575dd828c3be6f135984eaf8c851fb6
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479758"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Best Practices für serverlose SQL-Pools in Azure Synapse Analytics

In diesem Artikel finden Sie eine Sammlung von bewährten Methoden für die Verwendung serverloser SQL-Pools. Der serverlose SQL-Pool ist eine Ressource in Azure Synapse Analytics.

Ein serverloser SQL-Pool ermöglicht es Ihnen, Dateien in Ihren Azure-Speicherkonten abzufragen. Es verfügt nicht über lokale Speicher- oder Erfassungsfunktionen. Somit sind alle Dateien, auf die die Abfrage ausgerichtet ist, extern zum serverlosen SQL-Pool. Alles, was mit dem Lesen von Dateien aus dem Speicher zusammenhängt, kann sich auf die Abfrageleistung auswirken.

Einige allgemeine Richtlinien lauten:
- Stellen Sie sicher, dass Ihre Clientanwendungen mit dem serverlosen SQL-Pool zusammengestellt werden.
  - Wenn Sie Clientanwendungen außerhalb von Azure verwenden (z. B. Power BI Desktop SSMS, ADS), stellen Sie sicher, dass Sie den serverlosen Pool in einer Region verwenden, die sich in der Nähe Ihres Clientcomputers befindet.
- Stellen Sie sicher, dass sich der Speicher (Azure Data Lake Cosmos DB) und der serverlose SQL-Pool in der gleichen Region befinden.
- Versuchen Sie, das [Speicherlayout](#prepare-files-for-querying) mithilfe der Partitionierung zu optimieren, und halten Sie Ihre Dateien im Bereich zwischen 100 MB und 10 GB.
- Wenn Sie eine große Anzahl von Ergebnissen zurückgeben, stellen Sie sicher, dass Sie SSMS oder ADS und nicht Synapse Studio verwenden. Synapse Studio ist ein Webtool, das nicht für große Resultmengen konzipiert ist. 
- Wenn Sie Ergebnisse nach Zeichenfolgenspalten filtern, versuchen Sie, einige `BIN2_UTF8`-Sortierungen zu verwenden.
- Versuchen Sie, die Ergebnisse auf Clientseite mithilfe des Power BI-Importmodus oder Azure Analysis Services zwischenzuspeichern, und aktualisieren Sie sie in regelmäßigen Abständen. Die serverlosen SQL-Pools können in Power BI Direct-Query-Modus keine interaktive Erfahrung bieten, wenn Sie komplexe Abfragen verwenden oder große Datenmengen verarbeiten.

## <a name="client-applications-and-network-connections"></a>Clientanwendungen und Netzwerkverbindungen

Stellen Sie sicher, dass die Clientanwendung mit dem nächstmöglichen Synapse-Arbeitsbereich optimal verbunden ist.
- Platzieren Sie eine Clientanwendung und den Synapse-Arbeitsbereich an demselben Ort. Wenn Sie Anwendungen wie Power BI oder Azure Analysis Services verwenden, stellen Sie sicher, dass sie sich in derselben Region befinden, in der Sie Ihren Synapse-Arbeitsbereich platziert haben. Erstellen Sie bei Bedarf separate Arbeitsbereiche, die mit Ihren Clientanwendungen kombiniert werden. Wenn Sie eine Clientanwendung und den Synapse-Arbeitsbereich in verschiedenen Regionen platzieren, kann dies zu einer größeren Latenz und einem langsameren Streaming von Ergebnissen führen.
- Wenn Sie Daten aus Ihrer lokalen Anwendung lesen, stellen Sie sicher, dass sich der Synapse-Arbeitsbereich in der Region befindet, die in der Nähe Ihres Standorts liegt.
- Stellen Sie sicher, dass beim Lesen einer großen Datenmenge keine Probleme mit der Netzwerkbandbreite auftreten.
- Verwenden Sie nicht Synapse-Studio, um eine große Datenmenge zurückzugeben. Synapse-Studio ist ein Webtool, das zum Übertragen von Daten das HTTPS-Protokoll verwendet. Verwenden Sie Azure Data Studio oder SQL Server Management Studio, um eine große Datenmenge zu lesen.

## <a name="storage-and-content-layout"></a>Speicher- und Inhaltslayout

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Anordnen Ihres Speichers und des serverlosen SQL-Pools an demselben Ort

Ordnen Sie Ihr Azure-Speicherkonto bzw. den Cosmos DB-Analysespeicher und den Endpunkt Ihres serverlosen SQL-Pools an demselben Ort an, um die Latenz zu verringern. Speicherkonten und Endpunkte, die während der Einrichtung des Arbeitsbereichs bereitgestellt werden, befinden sich in derselben Region.

Wenn Sie mit einem serverlosen SQL-Pool auf andere Speicherkonten zugreifen, stellen Sie für eine optimale Leistung sicher, dass sie sich in derselben Region befinden. Wenn sie sich nicht in derselben Region befinden, erhöht sich die Wartezeit für die Netzwerkübertragung der Daten zwischen der Remote- und der Endpunktregion.

### <a name="azure-storage-throttling"></a>Azure Storage-Drosselung

Mehrere Anwendungen und Dienste können auf Ihr Speicherkonto zugreifen. Eine Speicherdrosselung tritt auf, wenn der von Anwendungen, Diensten und der Workload eines serverlosen SQL-Pools generierte kombinierte IOPS oder Durchsatz die Grenzen des Speicherkontos überschreitet. Als Folge davon werden Sie eine erhebliche negative Auswirkung auf die Abfrageleistung erleben.

Wenn eine Drosselung erkannt wird, verfügt der serverlose SQL-Pool über eine integrierte Behandlung zu deren Behebung. Beim serverlosen SQL-Pool werden Anforderungen an den Speicher langsamer ausgeführt, bis die Drosselung behoben ist.

> [!TIP]
> Für eine optimale Abfrageausführung sollten Sie das Speicherkonto während der Abfrageausführung nicht mit anderen Workloads belasten.

### <a name="azure-ad-pass-through-performance"></a>Leistung von Azure AD-Pass-Through

Der serverlose SQL-Pool ermöglicht Ihnen den Zugriff auf Dateien im Speicher über Azure Active Directory (Azure AD)-Passthrough oder SAS-Anmeldeinformationen. Die Leistung von Azure AD-Pass-Through kann im Vergleich zu SAS geringer sein.

Um die Leistung zu verbessern, versuchen Sie, SAS-Anmeldeinformationen für den Speicherzugriff zu verwenden.

### <a name="prepare-files-for-querying"></a>Vorbereiten von Dateien für Abfragen

Wenn möglich, können Sie Dateien für eine bessere Leistung vorbereiten:

- Konvertieren Sie umfangreiche CSV- und JSON-Daten in das Parquet-Format. Bei Parquet handelt es sich um ein Spaltenformat. Da es komprimiert ist, sind die Dateien kleiner als CSV- oder JSON-Dateien mit denselben Daten. Beim Lesen von Parquet-Dateien können die Spalten und Zeilen, die in der Abfrage nicht benötigt werden, vom serverlosen SQL-Pool übersprungen werden. Der serverlose SQL-Pool benötigt weniger Zeit und Speicheranforderungen, um es zu lesen.
- Wenn eine Abfrage auf eine einzelne große Datei ausgerichtet ist, lohnt es sich, diese in mehrere kleinere Dateien aufzuteilen.
- Versuchen Sie sicherzustellen, dass die Größe von CSV-Dateien im Bereich zwischen 100 MB und 10 GB liegt.
- Es ist besser, gleich große Dateien für einen einzelnen OPENROWSET-Pfad oder einen Speicherort für die Tabelle zu verwenden.
- Partitionieren Sie Ihre Daten, indem Sie Partitionen in verschiedenen Ordnern oder unter unterschiedlichen Dateinamen speichern. Unter [Verwenden von Dateiname und Dateipfadfunktionen zum Ausrichten auf bestimmte Partitionen](#use-filename-and-filepath-functions-to-target-specific-partitions) finden Sie weitere Informationen.

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Platzieren Ihres analytischen CosmosDB-Speichers und des serverlosen SQL-Pools an demselben Ort

Stellen Sie sicher, dass der analytische CosmosDB-Speicher in derselben Region platziert wird, in dem sich der Synapse-Arbeitsbereich befindet. Regionsübergreifende Abfragen können zu großen Latenzen führen. Verwenden Sie die Regionseigenschaft in der Verbindungszeichenfolge, um explizit den Bereich anzugeben, in dem der analytische Speicher platziert wird (Weitere Informationen finden Sie unter [Abfragen von CosmosDb mit serverlosem SQL](query-cosmos-db-analytical-store.md#overview)):

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV-Optimierungen

### <a name="use-parser_version-20-to-query-csv-files"></a>Verwenden von PARSER_VERSION 2.0 zum Abfragen von CSV-Dateien

Beim Abfragen von CSV-Dateien können Sie einen leistungsoptimierten Parser verwenden. Weitere Informationen finden Sie unter [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Manuelles Erstellen von Statistiken für CSV-Dateien

Der serverlose SQL-Pool verwendet Statistiken, um optimale Abfrageausführungspläne zu generieren. Statistiken werden bei Bedarf automatisch für Spalten in den Parquet-Dateien erstellt. Zurzeit werden Statistiken nicht automatisch für Spalten in CSV-Dateien erstellt, und Sie sollten Statistiken manuell für Spalten erstellen, die Sie in Abfragen verwenden, insbesondere in DISTINCT, JOIN, WHERE, ORDER BY und GROUP BY. Weitere Informationen finden Sie unter [Statistiken im serverlosen SQL-Pool](develop-tables-statistics.md#statistics-in-serverless-sql-pool).


## <a name="data-types"></a>Datentypen

### <a name="use-appropriate-data-types"></a>Verwenden geeigneter Datentypen

Die Datentypen, die Sie in Ihrer Abfrage verwenden, wirken sich auf die Leistung aus. Sie können mit folgenden Maßnahmen eine bessere Leistung erzielen: 

- Verwenden der kleinsten Datengröße, die den größtmöglichen Wert aufnehmen kann.
  - Wenn die maximale Zeichenlänge des Werts 30 Zeichen beträgt, verwenden Sie den Zeichendatentyp der Länge 30.
  - Wenn Zeichenspaltenwerte eine festgelegte Größe haben, verwenden Sie **char** oder **nchar**. Verwenden Sie andernfalls **varchar** oder **nvarchar**.
  - Wenn der Höchstwert für eine ganzzahlige (integer) Spalte 500 ist, verwenden Sie **smallint**, da dies der kleinste Datentyp ist, der diesen Wert aufnehmen kann. Ganzzahlige Datentypen (integer) finden Sie [hier](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Verwenden Sie nach Möglichkeit **varchar** und **char** anstelle von **nvarchar** und **nchar**.
- Verwenden Sie nach Möglichkeit ganzzahlige Datentypen (integer). Die Vorgänge SORT, JOIN und GROUP BY erfolgen schneller mit ganzzahligen Daten als mit Zeichendaten.
- Wenn Sie Schemarückschlüsse verwenden, [überprüfen Sie den abgeleiteten Datentyp](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Abgeleitete Datentypen überprüfen

Mithilfe von [Schemarückschlüssen](query-parquet-files.md#automatic-schema-inference) können Sie schnell Abfragen schreiben und Daten erkunden, ohne das Dateischema zu kennen. Bei diesen Vorteilen gibt es allerdings auch den Nachteil, dass abgeleitete Datentypen möglicherweise größer als die tatsächlichen Datentypen sind. Hierzu kommt es, wenn nicht genügend Informationen in den Quelldateien vorhanden sind, um sicherzustellen, dass geeignete Datentypen verwendet werden. Beispielsweise enthalten Parquet-Dateien keine Metadaten zur maximalen Länge von Zeichenspalten. Daher leitet sie der serverlose SQL-Pool als „varchar(8000)“ ab.

Sie können die resultierenden Datentypen Ihrer Abfrage mithilfe von [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) überprüfen.

Im folgenden Beispiel wird gezeigt, wie Sie abgeleitete Datentypen optimieren können. Die Prozedur wird zum Anzeigen von abgeleiteten Datentypen verwendet: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Das Ergebnis lautet wie folgt:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------|
|0|1|vendor_id|varchar(8000)|8.000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

Sobald Sie die abgeleiteten Datentypen für die Abfrage kennen, können Sie geeignete Datentypen angeben:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Filteroptimierung

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Pushen von Platzhaltern auf niedrigere Ebenen im Pfad

Sie können Platzhalter in Ihrem Pfad verwenden, um [mehrere Dateien und Ordner](query-data-storage.md#query-multiple-files-or-folders) abzufragen. Der serverlose SQL-Pool listet Dateien in Ihrem Speicherkonto auf und beginnt damit beim ersten „*“. Dabei kommt die Storage-API zum Einsatz. Dateien, die nicht mit dem angegebenen Pfad übereinstimmen, werden dabei ausgeschlossen. Das Verringern der ursprünglichen Liste mit Dateien kann die Leistung verbessern, wenn viele Dateien vorhanden sind, die mit dem angegebenen Pfad bis zum ersten Platzhalter übereinstimmen.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Verwenden von Dateiname und Dateipfadfunktionen zum Ausrichten auf bestimmte Partitionen

Daten sind oft in Partitionen organisiert. Sie können den serverlosen SQL-Pool anweisen, bestimmte Ordner und Dateien abzufragen. Dies reduziert die Anzahl der Dateien und die Datenmenge, die die Abfrage zum Lesen und Verarbeiten benötigt. Ein weiterer Vorteil ist, dass Sie bessere Leistungen erzielen.

Im Artikel [Verwenden von Dateimetadaten in Abfragen](query-specific-files.md) finden Sie Beispiele für das Abfragen bestimmter Dateien sowie weitere Informationen zu [Dateiname](query-data-storage.md#filename-function) und [Dateipfad](query-data-storage.md#filepath-function).

> [!TIP]
> Wandeln Sie das Ergebnis der Dateipfad- und Dateinamefunktionen immer in einen geeigneten Datentyp um. Achten Sie, wenn Sie Zeichendatentypen verwenden, unbedingt auf die Verwendung der richtigen Länge.

> [!NOTE]
> Funktionen zur Partitionsentfernung (Dateipfad und Dateiname) werden derzeit nicht für externe Tabellen unterstützt, außer denen, die automatisch für jede in Apache Spark für Azure Synapse Analytics erstellte Tabelle erzeugt werden.

Wenn Ihre gespeicherten Daten nicht partitioniert sind, können Sie sie partitionieren. So können Sie diese Funktionen verwenden, um auf diese Dateien ausgerichtete Abfragen zu optimieren. Wenn Sie in einem serverlosen SQL-Pool [partitionierte Apache Spark für Azure Synapse-Tabellen abfragen](develop-storage-files-spark-tables.md), wird die Abfrage automatisch nur auf die erforderlichen Dateien ausgerichtet.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Verwenden der richtigen Sortierung, um den Prädikatpushdown für Zeichenspalten zu verwenden

In Parquetdateien sind Daten in Zeilengruppen angeordnet. Der serverlose SQL-Pool überspringt Zeilengruppen basierend auf dem in der WHERE-Klausel angegebenen Prädikat und reduziert so die E/A-Vorgänge, was zu einer verbesserten Abfrageleistung führt. 

Beachten Sie, dass der Prädikatpushdown für Zeichenspalten in Parquetdateien nur für die Latin1_General_100_BIN2_UTF8-Sortierung unterstützt wird. Sie können die Sortierung für eine bestimmte Spalte mithilfe der WITH-Klausel angeben. Wenn Sie diese Sortierung nicht mithilfe der WITH-Klausel angeben, wird die Datenbanksortierung verwendet.

## <a name="optimize-repeating-queries"></a>Optimieren von wiederholten Abfragen

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Verwenden von CETAS zum Verbessern von Abfrageleistung und Verknüpfungen

[CETAS](develop-tables-cetas.md) ist eine der wichtigsten Funktionen, die in einem serverlosen SQL-Pool verfügbar sind. CETAS ist ein paralleler Vorgang, der externe Tabellenmetadaten erstellt und die Ergebnisse der SELECT-Abfrage in eine Reihe von Dateien in Ihrem Speicherkonto exportiert.

Sie können CETAS verwenden, um häufig verwendete Teile von Abfragen, z. B. verknüpfte Verweistabellen, zu einem neuen Satz von Dateien zu materialisieren. Als Nächstes können Sie eine Verknüpfung zu dieser einzelnen externen Tabelle herstellen, anstatt gemeinsame Verknüpfungen in mehreren Abfragen zu wiederholen.

Da CETAS Parquet-Dateien generiert, werden Statistiken automatisch erstellt, wenn die erste Abfrage auf diese externe Tabelle ausgerichtet ist, was bei nachfolgenden, auf eine mit CETAS generierte Tabelle ausgerichteten Abfragen zu einer verbesserten Leistung führt.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu allgemeinen Problemen und entsprechende Lösungen finden Sie im Artikel [Problembehandlung bei SQL Analytics in Azure Synapse](resources-self-help-sql-on-demand.md). Wenn Sie nicht mit einem serverlosen SQL-Pool, sondern mit einem dedizierten SQL-Pool arbeiten, finden Sie entsprechende Anleitungen unter [Bewährte Methoden für dedizierte SQL-Pools](best-practices-dedicated-sql-pool.md).
