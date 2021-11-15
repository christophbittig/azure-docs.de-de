---
title: Best Practices für dedizierte SQL-Pools
description: Empfehlungen und Best Practices, die Sie bei der Arbeit mit dedizierten SQL-Pools kennen sollten
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/02/2021
ms.author: martinle
ms.reviewer: wiassaf
ms.openlocfilehash: a6ebaa9f6afe9afe007c5ffb2eb0a164d3f2ac75
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500543"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Best Practices für dedizierte SQL-Pools in Azure Synapse Analytics

Dieser Artikel enthält eine Sammlung von Best Practices, mit denen Sie eine optimale Leistung für dedizierte SQL-Pools in Azure Synapse Analytics erzielen können. Wenn Sie mit einem serverlosen SQL-Pool arbeiten, finden Sie unter [Best Practices für serverlose SQL-Pools in Azure Synapse Analytics](best-practices-serverless-sql-pool.md) spezifische Anleitungen. Im Folgenden finden Sie grundlegende Anleitungen und wichtige Bereiche, auf die Sie sich beim Erstellen Ihrer Lösung konzentrieren sollten. In jedem Abschnitt wird ein Konzept vorgestellt und Sie dann über Links zu Artikeln mit ausführlicheren Informationen geleitet, in denen auf das jeweilige Konzept näher eingegangen wird.

## <a name="dedicated-sql-pools-loading"></a>Laden von dedizierten SQL-Pools

Einen Leitfaden zum Laden von dedizierten SQL-Pools finden Sie unter [Bewährte Methoden zum Laden von Daten für Data Warehousing](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzieren von Kosten durch das Pausieren und Skalieren

Weitere Informationen zur Kostensenkung durch Anhalten und Skalieren finden Sie im Artikel [Verwalten von Computeressourcen](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?context=/azure/synapse-analytics/context/context).

## <a name="maintain-statistics"></a>Verwalten von Statistiken

Dedizierte SQL-Pools können so konfiguriert werden, dass Statistiken für Spalten automatisch erkannt und erstellt werden.  Die vom Optimierer erstellten Abfragepläne sind nur so gut wie die verfügbaren Statistiken.  

Wir empfehlen, dass Sie AUTO_CREATE_STATISTICS für Ihre Datenbanken aktivieren und die Statistiken täglich oder nach jedem Ladevorgang aktualisieren, um sicherzustellen, dass Statistiken für in Ihren Abfragen verwendete Spalten immer auf dem neuesten Stand sind.

Um die Zeit für die Pflege der Statistik zu verkürzen, sollten Sie auswählen, welche Spalten Statistiken aufweisen oder am häufigsten aktualisiert werden müssen. Beispielsweise können Sie Datumsspalten aktualisieren, wenn täglich neue Werte hinzugefügt werden. Konzentrieren Sie sich auf die Verwendung von Statistiken für Spalten in Verknüpfungen, für in der WHERE-Klausel verwendete Spalten und für Spalten in GROUP BY.

Weitere Informationen zu Statistiken finden Sie in den Artikeln [Verwalten von Tabellenstatistiken](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) und [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance"></a>Optimieren der Abfrageleistung

- [Leistungsoptimierung mit materialisierten Sichten](../sql-data-warehouse/performance-tuning-materialized-views.md)
- [Leistungsoptimierung mit einem sortierten gruppierten Columnstore-Index](../sql-data-warehouse/performance-tuning-ordered-cci.md)
- [Leistungsoptimierung mit Zwischenspeichern von Resultsets](../sql-data-warehouse/performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Gruppieren von INSERT-Anweisungen in Batches

Das einmalige Laden einer kleinen Tabelle mit einer INSERT-Anweisung wie `INSERT INTO MyLookup VALUES (1, 'Type 1')` kann je nach Ihren Anforderungen den besten Ansatz darstellen. Falls Sie im Laufe eines Tages aber Tausende oder Millionen von Zeilen laden müssen, ist es wahrscheinlich, dass Singleton-INSERT-Anweisungen nicht optimal sind.

Eine Möglichkeit, dieses Problem zu beheben, besteht darin, einen Prozess zu entwickeln, der in eine Datei schreibt. Dann entwickeln Sie einen weiteren Prozess, der diese Datei regelmäßig lädt. Weitere Informationen finden Sie im Artikel [INSERT](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Verwenden von PolyBase zum schnellen Laden und Exportieren von Daten

Dedizierte SQL-Pools unterstützen das Laden und Exportieren von Daten mit mehreren Tools, z. B. Azure Data Factory, PolyBase und BCP.  Für kleinere Datenmengen, bei denen die Leistung nicht entscheidend ist, können auch beliebige andere Tools für Ihre Zwecke ausreichen.  

> [!NOTE]
> PolyBase ist die beste Wahl, wenn Sie große Datenvolumen laden oder exportieren oder eine höhere Leistung benötigt wird.

PolyBase-Ladevorgänge können per CTAS oder INSERT INTO ausgeführt werden. CTAS reduziert die Transaktionsprotokollierung und ist der schnellste Weg zum Laden Ihrer Daten. Azure Data Factory unterstützt auch PolyBase-Ladevorgänge und kann eine ähnliche Leistung wie CTAS erreichen. PolyBase unterstützt verschiedene Dateiformate, z. B. Gzip-Dateien.

Wenn Sie bei der Verwendung von Gzip-Textdateien den Durchsatz maximieren möchten, teilen Sie sie in 60 oder mehr Dateien auf, um die Parallelität Ihres Ladevorgangs zu maximieren. Erwägen Sie das gleichzeitige Laden von Daten, um einen besseren Gesamtdurchsatz zu erzielen. Zusätzliche Informationen für diesen Abschnitt sind in den folgenden Artikeln enthalten:

- [Laden von Daten](../sql-data-warehouse/design-elt-data-loading.md?context=/azure/synapse-analytics/context/context)
- [Anleitung für die Verwendung von PolyBase](data-loading-best-practices.md)
- [Lademuster und -strategien für dedizierte SQL-Pools](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Laden von Daten mit Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?context=/azure/synapse-analytics/context/context)
- [Verschieben von Daten mit Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?context=/azure/synapse-analytics/context/context)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)

## <a name="load-then-query-external-tables"></a>Laden und Abfragen von externen Tabellen

PolyBase ist für Abfragen nicht optimal. PolyBase-Tabellen für dedizierte SQL-Pools unterstützen derzeit nur Azure-Blobdateien und Azure Data Lake Storage. Diese Dateien werden nicht durch Computeressourcen gestützt. Infolgedessen können dedizierte SQL-Pools diese Arbeit nicht auslagern und müssen die gesamte Datei in `tempdb` laden, damit die Daten gelesen werden können.

Wenn Sie über mehrere auf diese Daten gerichtete Abfragen verfügen, sollten Sie diese Daten besser einmal laden, und die Abfragen sollten die lokale Tabelle verwenden. Weitere Hinweise zu PolyBase finden Sie im Artikel [Anleitung für die Verwendung von PolyBase](data-loading-best-practices.md).

## <a name="hash-distribute-large-tables"></a>Verwenden der Hashverteilung für große Tabellen

Für Tabellen wird standardmäßig die Roundrobin-Verteilung eingesetzt. Diese Standardeinstellung erleichtert es Benutzern, mit dem Erstellen von Tabellen zu beginnen, ohne entscheiden zu müssen, wie die Tabellen verteilt werden sollen. Roundrobin-Tabellen können für einige Workloads ausreichend leistungsfähig sein. Aber in den meisten Fällen bietet eine Verteilungsspalte eine bessere Leistung.  

Das häufigste Beispiel für den Fall, in dem eine von einer Spalte verteilte Tabelle eine höhere Leistung als eine Roundrobin-Tabelle aufweist, ist die Verknüpfung von zwei großen Faktentabellen.  

Wenn Sie z. B. eine Tabelle mit Bestellungen verwenden, die nach „order_id“ verteilt wird, und eine Tabelle mit Transaktionen, die ebenfalls nach „order_id“ verteilt wird, gilt Folgendes: Wenn Sie die Tabelle mit den Bestellungen per „order_id“ mit der Tabelle mit den Transaktionen verknüpfen, wird diese Abfrage zu einer Pass-Through-Abfrage. Datenverschiebungsvorgänge werden dann beseitigt. Weniger Schritte führen zu einer schnelleren Abfrage. Außerdem führen weniger Datenverschiebungen zu schnelleren Abfragen.

> [!TIP]
> Beim Laden einer verteilten Tabelle sollten Ihre eingehenden Daten nicht nach dem Verteilungsschlüssel sortiert werden. Dadurch werden Ihre Ladevorgänge verlangsamt.

Über die unten angegebenen Artikellinks erhalten Sie weitere Details zur Leistungsverbesserung durch Auswahl einer Verteilungsspalte. Außerdem finden Sie Informationen zum Definieren einer verteilten Tabelle in der WITH-Klausel ihrer CREATE TABLE-Anweisung:

- [Tabellenübersicht](develop-tables-overview.md)
- [Verteilen von Tabellen in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)
- [Auswählen von Tabellenverteilungen](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Vermeiden von übermäßiger Partitionierung

Das Partitionieren von Daten kann für die Verwaltung Ihrer Daten durch Partitionswechsel oder die Optimierung von Scans durch eine Partitionsbeseitigung zwar effektiv sein, aber eine zu hohe Zahl von Partitionen kann Ihre Abfragen verlangsamen.  Oft funktioniert eine Partitionierungsstrategie mit hoher Granularität, die für SQL Server gut geeignet ist, bei einem dedizierten SQL-Pool nicht gut.  

Die Verwendung von zu vielen Partitionen kann die Effektivität von gruppierten Columnstore-Indizes reduzieren, wenn jede Partition weniger als 1 Million Zeilen enthält. Dedizierte SQL-Pools partitionieren Ihre Daten automatisch in 60 Datenbanken. Wenn Sie also eine Tabelle mit 100 Partitionen erstellen, wird das Ergebnis 6000 Partitionen sein. Jede Workload ist anders, sodass es ratsam ist, mit der Partitionierung zu experimentieren. So können Sie ermitteln, was für Ihre Workload am besten funktioniert.  

Eine zu erwägende Option ist die Verwendung einer Granularität, die niedriger ist als die, die Sie mit SQL Server implementiert haben. Versuchen Sie es z. B. anstelle von täglichen Partitionen mit wöchentlichen oder monatlichen Partitionen.

Weitere Informationen zur Partitionierung finden Sie im Artikel [Tabellenpartitionierung](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context).

## <a name="minimize-transaction-sizes"></a>Verringern von Transaktionsgrößen

INSERT-, UPDATE- und DELETE-Anweisungen werden in einer Transaktion ausgeführt. Wenn bei Ihnen ein Fehler auftritt, muss ein Rollback ausgeführt werden. Es ist ratsam, die Transaktionsgrößen nach Möglichkeit zu verringern, um das Potenzial für einen langen Rollbackvorgang zu reduzieren.  Das Minimieren von Transaktionsgrößen kann durch Unterteilen von INSERT-, UPDATE- und DELETE-Anweisungen in Teile erfolgen. Wenn Sie z. B. über eine INSERT-Anweisung verfügen, die voraussichtlich eine Stunde dauert, können Sie die INSERT-Anweisung in vier Teile unterteilen. Jede Ausführung wird dann auf 15 Minuten gekürzt.  

> [!TIP]
> Nutzen Sie spezielle Fälle mit minimaler Protokollierung, z. B. CTAS, TRUNCATE, DROP TABLE oder INSERT zum Leeren von Tabellen, um das Rollbackrisiko zu verringern.  

Eine weitere Möglichkeit zum Beseitigen von Rollbacks ist die Verwendung von Vorgängen vom Typ „Nur Metadaten“, z.B. ein Partitionswechsel für die Datenverwaltung.  Anstatt z. B. eine DELETE-Anweisung auszuführen, um alle Zeilen in einer Tabelle zu löschen, in der das Bestelldatum (order_date) im Oktober 2001 lag, könnten Sie Ihre Daten monatlich partitionieren. Dann können Sie die Partition mit Daten für eine leere Partition aus einer anderen Tabelle austauschen (siehe ALTER TABLE-Beispiele).  

Erwägen Sie bei nicht partitionierten Tabellen die Verwendung eines CTAS-Vorgangs, um die beizubehaltenden Daten in eine Tabelle zu schreiben, statt DELETE zu verwenden.  Wenn für einen CTAS-Vorgang die gleiche Zeit benötigt wird, kann dieser viel sichererer ausgeführt werden, da er mit minimaler Transaktionsprotokollierung verbunden ist und bei Bedarf schnell abgebrochen werden kann.

Weitere Informationen zum Inhalt dieses Abschnitts finden Sie in den folgenden Artikeln:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)
- [Grundlegendes zu Transaktionen](develop-transactions.md)
- [Optimieren von Transaktionen](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)
- [Tabellenpartitionierung](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Reduzieren von Abfrageergebnisgrößen

Durch die Reduzierung der Größe von Abfrageergebnissen können Sie Probleme auf der Clientseite vermeiden, die durch große Abfrageergebnisse verursacht werden.  Sie können Ihre Abfrage bearbeiten, um die Anzahl von zurückgegebenen Zeilen zu reduzieren. Einige Tools zur Abfragegenerierung ermöglichen es Ihnen, jeder Abfrage „Top N“-Syntax hinzuzufügen.  Sie können das Abfrageergebnis auch mit CETAS in eine temporäre Tabelle exportieren und dann den PolyBase-Export für die kompatible Verarbeitung verwenden.

## <a name="use-the-smallest-possible-column-size"></a>Verwenden der kleinstmöglichen Spaltengröße

Wenn Sie Ihre DDL definieren, verwenden Sie den kleinsten Datentyp, der Ihre Daten unterstützt, da dies die Abfrageleistung verbessert.  Diese Empfehlung ist besonders wichtig für CHAR- und VARCHAR-Spalten.  Wenn der längste Wert in einer Spalte 25 Zeichen lang ist, sollten Sie die Spalte VARCHAR(25) definieren.  Vermeiden Sie es, für alle Zeichenspalten eine hohe Standardlänge zu definieren.  Definieren Sie darüber hinaus Spalten als VARCHAR, sofern dies ausreicht, anstatt NVARCHAR zu verwenden.

In den Artikeln [Tabellenübersicht](develop-tables-overview.md), [Tabellendatentypen](develop-tables-data-types.md) und [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) finden Sie eine ausführlichere Übersicht über die wesentlichen Konzepte, die für die oben genannten Informationen relevant sind.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Verwenden temporärer Heaptabellen für vorübergehende Daten

Wenn Sie Daten in dedizierten SQL-Pools temporär anordnen, wird der Gesamtprozess im Allgemeinen durch Heaptabellen beschleunigt.  Wenn Sie Daten nur laden, um sie vor der Ausführung von weiteren Transformationen bereitzustellen, wird der Ladevorgang der Tabelle auf die Heaptabelle schneller sein, als wenn Sie Daten in eine gruppierte Columnstore-Tabelle laden.  

Beim Laden von Daten in eine temporäre Tabelle wird der Ladevorgang außerdem viel schneller als beim Laden einer Tabelle in einen dauerhaften Speicher durchgeführt.  Temporäre Tabellen beginnen mit einem „#“ und sind nur für die Sitzung zugänglich, in der sie erstellt wurden. Folglich funktionieren sie möglicherweise nur in eingeschränkten Szenarien. Heaptabellen werden in der WITH-Klausel von CREATE TABLE definiert.  Denken Sie beim Verwenden einer temporären Tabelle daran, auch für die temporäre Tabelle Statistiken zu erstellen.

Weitere Informationen finden Sie in den Artikeln [Temporäre Tabellen](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) und [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Optimieren von gruppierten Columnstore-Tabellen

Gruppierte Columnstore-Indizes sind eine der effizientesten Möglichkeiten zum Speichern Ihrer Daten in einem dedizierten SQL-Pool.  Tabellen werden in einem dedizierten SQL-Pool standardmäßig als gruppierter Columnstore erstellt.  Die Verwendung einer guten Segmentqualität ist wichtig, um für Abfragen in Columnstore-Tabellen die beste Leistung zu erzielen.  Wenn Zeilen bei hohem Arbeitsspeicherdruck in Columnstore-Tabellen geschrieben werden, kann die Qualität von Columnstore-Segmenten leiden.  

Die Segmentqualität kann anhand der Anzahl von Zeilen in einer komprimierten Zeilengruppe gemessen werden. Eine ausführliche Anleitung zur Erkennung und Verbesserung der Segmentqualität für gruppierte Columnstore-Tabellen finden Sie im Artikel [Tabellenindizes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context) unter [Ursachen für eine schlechte Qualität des Columnstore-Index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#causes-of-poor-columnstore-index-quality).  

Weil qualitativ hochwertige Columnstore-Segmente wichtig sind, ist es eine gute Idee, Benutzer-IDs, die zur mittleren oder großen Ressourcenklasse gehören, zum Laden von Daten zu verwenden. Wenn Sie [Data Warehouse-Einheiten](resource-consumption-models.md) auf niedrigerer Ebene verwenden, sollten Sie dem ladenden Benutzer eine größere Ressourcenklasse zuweisen.

Bei Columnstore-Tabellen werden Daten im Allgemeinen erst dann in ein komprimiertes Columnstore-Segment gepusht, wenn mehr als eine Million Zeilen pro Tabelle vorhanden sind. Jede Tabelle eines dedizierten SQL-Pools ist in 60 Tabellen partitioniert. Daher sind Columnstore-Tabellen für eine Abfrage nur dann von Vorteil, wenn die Tabelle mehr als 60 Millionen Zeilen aufweist.  

> [!TIP]
> Bei Tabellen mit weniger als 60 Millionen Zeilen ist ein Columnstore-Index möglicherweise nicht die optimale Lösung.  

Wenn Sie Ihre Daten partitionieren, muss jede Partition eine Million Zeilen aufweisen, um von einem gruppierten Columnstore-Index profitieren zu können.  Für eine Tabelle mit 100 Partitionen muss sie mindestens 6 Milliarden Zeilen enthalten, um von einem gruppierten Columnstore zu profitieren (60 Verteilungen *100 Partitionen* 1 Million Zeilen).  

Wenn Ihre Tabelle nicht 6 Milliarden Zeilen aufweist, haben Sie zwei Hauptoptionen. Reduzieren Sie entweder die Anzahl der Partitionen, oder verwenden Sie stattdessen eine Heaptabelle.  Außerdem kann sich folgendes Experiment lohnen: Ermitteln Sie, ob eine bessere Leistung erzielt werden kann, indem eine Heaptabelle mit sekundären Indizes anstelle einer Columnstore-Tabelle verwendet wird.

Beim Abfragen einer Columnstore-Tabelle werden die Abfragen schneller ausgeführt, wenn Sie nur die benötigten Spalten auswählen.  Weitere Informationen zu Tabellen- und Columnstore-Indizes finden Sie in den folgenden Artikeln:
- [Ursachen für eine schlechte Qualität des Columnstore-Index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)
- [Columnstore-Indizes: Übersicht](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)
- [Neuerstellen von Columnstore-Indizes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuild-indexes-to-improve-segment-quality) 
- [Leistungsoptimierung mit einem sortierten gruppierten Columnstore-Index](../sql-data-warehouse/performance-tuning-ordered-cci.md)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Verwenden einer größeren Ressourcenklasse zum Verbessern der Abfrageleistung

SQL-Pools nutzen Ressourcengruppen zum Zuordnen von Arbeitsspeicher zu Abfragen. Anfänglich werden alle Benutzer der kleinen Ressourcenklasse zugewiesen, bei der pro Verteilung 100 MB Arbeitsspeicher gewährt werden.  Es gibt immer 60 Verteilungen. Jede Verteilung erhält mindestens 100 MB. Die gesamte systemweite Speicherzuweisung beträgt 6.000 MB oder fast 6 GB.  

Bestimmte Abfragen, z.B. große Verknüpfungen oder Ladevorgänge für gruppierte Columnstore-Tabellen, profitieren von größeren Arbeitsspeicherzuordnungen.  Für einige Abfragen, z. B. reine Scans, ergibt sich kein Vorteil. Die Verwendung größerer Ressourcenklassen wirkt sich auf die Parallelität aus. Sie sollten also diese Fakten im Hinterkopf behalten, bevor Sie alle Ihre Benutzer in eine große Ressourcenklasse verschieben.

Weitere Informationen zu Ressourcenklassen finden Sie im Artikel [Ressourcenklassen für die Workloadverwaltung](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Verwenden einer kleineren Ressourcenklasse zur Steigerung der Parallelität

Wenn Sie eine lange Verzögerung bei Benutzerabfragen feststellen, werden Ihre Benutzer möglicherweise in größeren Ressourcenklassen ausgeführt. Dieses Szenario fördert die Verwendung von Parallelitätsslots, was dazu führen kann, dass andere Abfragen in die Warteschlange gestellt werden.  Führen Sie `SELECT * FROM sys.dm_pdw_waits` aus, um zu ermitteln, ob Zeilen zurückgegeben werden. So können Sie ermitteln, ob Benutzerabfragen in eine Warteschlange eingereiht werden.

Weitere Informationen finden Sie in den Artikeln [Ressourcenklassen für die Workloadverwaltung](../sql-data-warehouse/resource-classes-for-workload-management.md) und [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Verwenden von DMVs zum Überwachen und Optimieren von Abfragen

Dedizierte SQL-Pools verfügen über mehrere DMVs, mit denen die Abfrageausführung überwacht werden kann.  Unten im Artikel zur Überwachung werden Sie Schritt für Schritt durch das Anzeigen einer ausgeführten Abfrage geführt.  Die Verwendung der Option LABEL für Abfragen kann hilfreich sein, um Abfragen für diese DMVs schnell finden zu können. Weitere ausführliche Informationen finden Sie in den Artikeln, die in der nachfolgenden Liste aufgeführt sind:

- [Überwachen Ihrer Workload mit dynamischen Verwaltungssichten](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu allgemeinen Problemen und entsprechende Lösungen finden Sie im Artikel [Problembehandlung](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?context=/azure/synapse-analytics/context/context).

Wenn Sie Informationen benötigen, die in diesem Artikel nicht enthalten sind, können Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) für Azure Synapse](/answers/topics/azure-synapse-analytics.html) Fragen an andere Benutzer und die Produktgruppe „Azure Synapse Analytics“ stellen.  

Wir überwachen dieses Forum aktiv, um sicherzustellen, dass Ihre Frage entweder von einem anderen Benutzer oder einem Mitarbeiter beantwortet wird.  Falls Sie Ihre Fragen lieber über Stack Overflow stellen möchten, können Sie dazu auch das [Stack Overflow-Forum für Azure Synapse Analytics](https://stackoverflow.com/questions/tagged/azure-synapse) nutzen.

