---
title: Optimieren der Quellenleistung in Zuordnungsdatenflüssen
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Leistung von Quellen von Zuordnungsdatenflüssen in Azure Data Factory- und Azure Synapse Analytics-Pipelines optimieren.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7fef72d1ca52ae04106797c8ce7088b259aad033
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293559"
---
# <a name="optimizing-sources"></a>Optimieren von Quellen

Für jede Quelle – mit Ausnahme von Azure SQL-Datenbank – lautet die Empfehlung, die Option **Aktuelle Partitionierung verwenden** ausgewählt zu lassen. Beim Lesen von allen anderen Quellsystemen werden Daten von Datenflüssen je nach Größe automatisch gleichmäßig partitioniert. Für ca. 128 MB an Daten wird jeweils eine neue Partition erstellt. Wenn die Datengröße zunimmt, steigt die Anzahl von Partitionen.

Alle benutzerdefinierten Partitionierungen werden durchgeführt, *nachdem* die Daten von Spark eingelesen wurden. Dies wirkt sich negativ auf die Leistung Ihres Datenflusses aus. Dies ist nicht zu empfehlen, da die Daten beim Lesen gleichmäßig partitioniert sind. 

> [!NOTE]
> Die Lesegeschwindigkeiten können aufgrund des Durchsatzes Ihres Quellsystems begrenzt sein.

## <a name="azure-sql-database-sources"></a>Azure SQL-Datenbank-Quellen

Azure SQL-Datenbank verfügt über eine eindeutige Partitionierungsoption, die die Bezeichnung „Quellpartitionierung“ hat. Mit dem Aktivieren der Quellpartitionierung können Sie Ihre Lesedauern für Azure SQL-Datenbank verbessern, indem Sie auf dem Quellsystem parallele Verbindungen aktivieren. Geben Sie die Anzahl von Partitionen und die Partitionierung Ihrer Daten an. Verwenden Sie eine Partitionsspalte mit hoher Kardinalität. Sie können auch eine Abfrage eingeben, die zum Partitionierungsschema Ihrer Quelltabelle passt.

> [!TIP]
> Bei der Quellpartitionierung ist der E/A-Vorgang von SQL Server der Engpass. Das Hinzufügen von zu vielen Partitionen kann dazu führen, dass die Quelldatenbank vollständig ausgelastet ist. Im Allgemeinen sind bei Verwendung dieser Option vier oder fünf Partitionen ideal.

:::image type="content" source="media/data-flow/sourcepart3.png" alt-text="Quellpartitionierung":::

### <a name="isolation-level"></a>Isolationsstufe

Die Isolationsstufe des Lesevorgangs auf einem Azure SQL-Quellsystem hat eine Auswirkung auf die Leistung. Wenn Sie die Option „Lesen ohne Commit“ auswählen, ergibt sich die schnellste Leistung, und Datenbanksperren werden verhindert. Weitere Informationen zu SQL-Isolationsstufen finden Sie unter [Grundlegendes zu Isolationsstufen](/sql/connect/jdbc/understanding-isolation-levels).

### <a name="read-using-query"></a>Lesen per Abfrage

Sie können Lesevorgänge aus Azure SQL-Datenbank durchführen, indem Sie eine Tabelle oder SQL-Abfrage verwenden. Wenn Sie eine SQL-Abfrage ausführen, muss diese beendet werden, bevor die Transformation gestartet werden kann. SQL-Abfragen können nützlich sein, um einen Pushdown für Vorgänge durchzuführen, damit diese dann ggf. schneller ausgeführt werden, und um die Menge der von einer SQL Server-Instanz gelesenen Daten zu reduzieren, z. B. SELECT-, WHERE- und JOIN-Anweisungen. Bei einem Pushdown von Vorgängen ist es nicht mehr möglich, die Herkunft und Leistung der Transformationen nachzuverfolgen, bevor die Daten in den Datenfluss gelangen.

## <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics-Quellen

Bei Verwendung von Azure Synapse Analytics ist in den Quelloptionen die Einstellung **Staging aktivieren** vorhanden. Dadurch kann der Dienst mithilfe von ```Staging``` aus Synapse lesen. Dies verbessert die Leseleistung erheblich, da die [Synapse-COPY-Anweisung](/sql/t-sql/statements/copy-into-transact-sql) als leistungsstärkste Funktion zum Massenladen verwendet wird. Beim Aktivieren von ```Staging``` müssen Sie in den Einstellungen für Datenflussaktivitäten einen Azure Blob Storage- oder Azure Data Lake Storage Gen2-Stagingspeicherort angeben.

:::image type="content" source="media/data-flow/enable-staging.png" alt-text="Staging aktivieren":::

## <a name="file-based-sources"></a>Dateibasierte Quellen

Für Datenflüsse werden zwar verschiedene Dateitypen unterstützt, zum Erzielen von optimalen Lese- und Schreibdauern wird aber das native Spark-Format „Parquet“ empfohlen.

Wenn Sie den gleichen Datenfluss für mehrere Dateien ausführen, empfehlen wir Ihnen, die Daten aus einem Ordner zu lesen, Platzhalterpfade zu verwenden oder die Daten aus einer Liste mit Dateien zu lesen. Mit nur einer Datenfluss-Aktivitätsausführung können Ihre gesamten Dateien als Batch verarbeitet werden. Weitere Informationen zum Konfigurieren dieser Einstellungen finden Sie in der Dokumentation zum [Azure Blob Storage-Connector](connector-azure-blob-storage.md#source-transformation) im Abschnitt **Quellentransformation**.

Vermeiden Sie nach Möglichkeit die Verwendung der For-Each-Aktivität, um Datenflüsse für mehrere Dateien auszuführen. Dies führt dazu, dass bei jeder Iteration des For-Each-Vorgangs ein eigener Spark-Cluster gestartet wird. Häufig ist dies nicht nötig, und diese Vorgehensweise kann teuer werden. 

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über die Datenflussleistung](concepts-data-flow-performance.md)
- [Optimieren von Senken](concepts-data-flow-performance-sinks.md)
- [Optimieren von Transformationen](concepts-data-flow-performance-transformations.md)
- [Verwenden von Datenflüssen in Pipelines](concepts-data-flow-performance-pipelines.md)

Lesen Sie die folgenden Artikel zu Datenflüssen in Bezug auf die Leistung:

- [Datenflussaktivität](control-flow-execute-data-flow-activity.md)
- [Überwachen der Datenflussleistung](concepts-data-flow-monitoring.md)
- [Leistung der Integration Runtime](concepts-integration-runtime-performance.md)
