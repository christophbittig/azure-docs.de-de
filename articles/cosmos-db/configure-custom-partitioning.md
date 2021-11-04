---
title: Konfigurieren der benutzerdefinierten Partitionierung zum Partitionieren von Analysespeicherdaten (Vorschau)
description: Hier erfahren Sie, wie Sie mithilfe von Azure Synapse Link über Azure Synapse Spark-Notebooks die benutzerdefinierte Partitionierung für Azure Cosmos DB auslösen. Zudem werden die Konfigurationsoptionen erläutert.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7848a9b578c6054ef1d3deb29bf4c2bf92b9f68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095008"
---
# <a name="configure-custom-partitioning-to-partition-analytical-store-data-preview"></a>Konfigurieren der benutzerdefinierten Partitionierung zum Partitionieren von Analysespeicherdaten (Vorschau)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Um die benutzerdefinierte Partitionierung verwenden zu können, müssen Sie Azure Synapse Link in Ihrem Azure Cosmos DB-Konto aktivieren. Weitere Informationen finden Sie unter [Konfigurieren von Azure Synapse Link](configure-synapse-link.md). Die Ausführung der benutzerdefinierten Partitionierung kann mithilfe von Azure Synapse Link für Azure Cosmos DB über Azure Synapse Spark-Notebooks ausgelöst werden.

> [!IMPORTANT]
> Das Feature für die benutzerdefinierte Partitionierung befindet sich derzeit in der Public Preview. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Azure Synapse Link sollte für Azure Cosmos DB-Konten aktiviert sein, um die Vorteile der benutzerdefinierten Partitionierung nutzen zu können. Die benutzerdefinierte Partitionierung wird derzeit nur für Azure Synapse Spark 2.0 unterstützt.

Es folgen obligatorische Konfigurationsoptionen, die erforderlich sind, um die Ausführung der benutzerdefinierten Partitionierung auszulösen:

* `spark.cosmos.asns.execute.partitioning`: Boolescher Wert, der die Ausführung der benutzerdefinierten Partitionierung auslöst. Der Standardwert ist „FALSE“.

* `spark.cosmos.asns.partition.keys`: Partitionsschlüssel mit DDL-formatierter Zeichenfolge. Beispiel: *ReadDate-Zeichenfolge*.

* `spark.cosmos.asns.basePath`: Der Basispfad für den partitionierten Speicher im primären Synapse-Speicherkonto.

> [!NOTE]
> Wenn Sie mehrere Partitionsschlüssel auswählen, können Sie auf diese Datensätze aus demselben partitionierten Speicher mit dem basePath zugreifen, der den Schlüssel angibt.

Im Folgenden finden Sie optionale Konfigurationsoptionen, die Sie beim Auslösen der Ausführung der benutzerdefinierten Partitionierung verwenden können:

* `spark.cosmos.asns.merge.partitioned.files`: Boolescher Wert, der das Erstellen einer einzelnen Datei pro Partitionswert pro Ausführung ermöglicht. Der Standardwert ist „false“.

* `spark.cosmos.asns.partitioning.maxRecordsPerFile`: Maximale Anzahl von Datensätzen in einer Datei mit nur einer Partition im partitionierten Speicher. Wenn diese Konfiguration und `spark.cosmos.asns.merge.partitioned.files` angegeben werden, werden neue Dateien erstellt, sobald die Anzahl der Datensätze den maxRecordsPerFile-Wert überschreitet. Diese Konfiguration wird in der Regel nur für die anfängliche Partitionierung für größere Sammlungen benötigt. Der Standardwert ist 1.000.000.

  Wenn Sie maxRecordsPerFile festlegen, aber `spark.cosmos.asns.merge.partitioned.files` nicht konfigurieren, können die Datensätze auf Dateien aufgeteilt werden, bevor sie den maxRecordsPerFile-Wert erreichen. Die Dateiaufteilung hängt auch von der verfügbaren Parallelität im Pool ab.

* `spark.cosmos.asns.partitioning.shuffle.partitions`: Hiermit wird die Parallelität während der partitionierten Schreibvorgängen in den partitionierten Speicher gesteuert. Diese Konfiguration wird nur für die anfängliche Partitionierung für größere Sammlungen benötigt. Sie wird auf die Anzahl der verfügbaren Kerne im Spark-Pool festgelegt. Der Standardwert ist 200. Ein niedrigerer Wert könnte Ressourcen verschwenden, wenn der Pool nicht für andere Workloads verwendet wird. Ein höherer Wert verursacht in der Regel keine Probleme, da einige Tasks frühzeitig abgeschlossen werden und mehr Aufgaben starten können, während die langsamen Aufgaben ausgeführt werden. Wenn der Partitionierungsauftrag schneller abgeschlossen werden soll, besteht eine bewährte Methode darin, die Poolgröße zu erhöhen.

# <a name="python"></a>[Python](#tab/python)

```python
spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.execute.partitioning", "true") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .option("spark.cosmos.asns.merge.partitioned.files", "true") \
    .option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000") \
    .option("spark.cosmos.asns.partitioning.shuffle.partitions", "400") \
    .load()
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
spark.read.
    format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.asns.execute.partitioning", "true").
    option("spark.cosmos.asns.partition.keys", "readDate String").
    option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
    option("spark.cosmos.asns.merge.partitioned.files", "true").
    option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000").
    option("spark.cosmos.asns.partitioning.shuffle.partitions", "400").
    load()
```
---

## <a name="query-execution-with-partitioned-store"></a>Abfrageausführung mit partitioniertem Speicher

Die folgenden beiden Konfigurationen sind erforderlich, um Abfragen mit Partitionierungsspeicherunterstützung auszuführen:

* `spark.cosmos.asns.partition.keys`
* `spark.cosmos.asns.basePath`

Das folgende Beispiel zeigt, wie Sie diese Konfigurationen verwenden können, um den oben genannten partitionierten Speicher abzufragen, und wie die Filterung mithilfe des Partitionsschlüssels die Partitionsbereinigung verwenden kann. Dieser partitionierte Speicher wird mithilfe des Felds „ReadDate“ partitioniert.

# <a name="python"></a>[Python](#tab/python)

```python
df = spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .load()

df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
val df = spark.read.
            format("cosmos.olap").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.asns.partition.keys", "readDate String").
            option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
            load()
val df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```
---

Der obige Filter *ReadDate = '2021-11-01'* entfernt während der Ausführung die Daten aus der Überprüfung, die anderen ReadDate-Werten als *2021-11-01* entsprechen.

> [!NOTE]
> Die Abfrageverbesserungen mit partitioniertem Speicher sind anwendbar, wenn Abfragen für Folgendes ausgeführt werden:
>
> * Spark-Datenrahmen, die aus dem Azure Cosmos DB-Analysespeichercontainer erstellt wurden
> * Spark-Tabellen, die auf den Azure Cosmos DB-Analysespeichercontainer verweisen

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Was ist die benutzerdefinierte Partitionierung](custom-partitioning-analytical-store.md) in Azure Synapse Link für Azure Cosmos DB?
* [Azure Synapse Link für Azure Cosmos DB](synapse-link.md)
* [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md)
* [Erste Schritte mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)
* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
