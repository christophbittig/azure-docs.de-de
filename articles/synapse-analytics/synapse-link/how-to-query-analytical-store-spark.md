---
title: Interagieren mit Azure Cosmos DB mithilfe von Apache Spark 2 in Azure Synapse Link
description: Interagieren mit Azure Cosmos DB mithilfe von Apache Spark in Azure Synapse Link
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 11/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 83f6c3a7e88cf42cbb2a2d36ff07ac79e7eb5894
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452203"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-2-in-azure-synapse-link"></a>Interagieren mit Azure Cosmos DB mithilfe von Apache Spark 2 in Azure Synapse Link

> [!NOTE]
> Informationen zu Synapse Link für Cosmos DB unter Verwendung von Spark 3 finden Sie im Artikel [Azure Synapse Link für Azure Cosmos DB in Spark 3](how-to-query-analytical-store-spark-3.md).

In diesem Artikel erfahren Sie, wie Sie unter Verwendung von Synapse Apache Spark 2 mit Azure Cosmos DB interagieren. Synapse Apache Spark bietet vollständige Unterstützung für Scala, Python, SparkSQL und C# und ist essentiell für Analyse-, Datentechnik-, Data-Science-, und Datenerkundungsszenarios in [Azure Synapse Link für Azure Cosmos DB](../../cosmos-db/synapse-link.md).

Die folgenden Funktionen werden für die Interaktion mit Azure Cosmos DB unterstützt:
* Mit Synapse Apache Spark können Sie Daten in Ihren Azure Cosmos DB-Containern mit Azure Synapse Link-Aktivierung in Quasi-Echtzeit analysieren, ohne dass sich dies auf die Leistung Ihrer Transaktionsarbeitsauslastungen auswirkt. Die folgenden beiden Optionen stehen zur Verfügung, um den [Analysespeicher](../../cosmos-db/analytical-store-introduction.md) von Azure Cosmos DB in Spark abzufragen:
    + Laden in Datenrahmen in Spark
    + Spark-Tabelle erstellen
* Synapse Apache Spark ermöglicht es Ihnen außerdem, Daten in Azure Cosmos DB zu erfassen. Es ist wichtig, im Hinterkopf zu behalten, dass Daten immer über den Transaktionsspeicher in Azure Cosmos DB-Container erfasst werden. Wenn Synapse Link aktiviert ist, dann werden alle neuen Einfüge-, Aktualisierungs- und Löschvorgänge automatisch mit dem Analysespeicher synchronisiert.
* Synapse Apache Spark unterstützt außerdem strukturiertes Streaming in Spark mit Azure Cosmos DB als Quelle sowie als Senke. 

Die folgenden Abschnitte führen Sie durch die Syntax der oben genannten Funktionen. Sie können sich auch das Learn-Modul zum [Abfragen von Azure Cosmos DB mit Apache Spark für Azure Synapse Analytics](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/) ansehen. Gesten in Azure Synapse Analytics-Arbeitsbereichen dienen einem einfachen Start der Verwendung. Gesten werden angezeigt, wenn Sie auf einen Azure Cosmos DB-Container auf der Registerkarte **Daten** im Synapse-Arbeitsbereich rechtsklicken. Mit Gesten können Sie schnell Code generieren und an Ihre Anforderungen anpassen. Gesten eignen sich auch ideal zum Ermitteln von Daten mit nur einem Mausklick.

> [!IMPORTANT]
> Sie müssen einige Einschränkungen im analytischen Schema beachten, die zu unerwartetem Verhalten bei Datenladevorgängen führen können.
> Beispiel: Nur die ersten 1.000 Eigenschaften aus dem Transaktionsschema sind im analytischen Schema verfügbar, Eigenschaften mit Leerzeichen sind nicht verfügbar usw. Treten unerwartete Ergebnisse auf, finden Sie weitere Details unter [Schemaeinschränkungen](../../cosmos-db/analytical-store-introduction.md#schema-constraints).

## <a name="query-azure-cosmos-db-analytical-store"></a>Abfragen von Azure Cosmos DB-Analysespeicher

Bevor Sie Informationen zu den zwei verfügbaren Optionen erhalten, mit denen der Analysespeicher in Azure Cosmos DB abgefragt werden kann, mit denen Ladevorgänge für Datenrahmen in Spark ausgeführt und Spark-Tabellen erstellt werden können, sollten Sie sich die Unterschiede genauer ansehen, damit Sie die Option auswählen können, die Ihre Anforderungen am besten erfüllt.

Ein Unterschied liegt darin, ob die zugrunde liegenden Datenänderungen im Azure Cosmos DB-Container automatisch in der in Spark durchgeführten Analyse widergespiegelt werden sollten oder nicht. Wenn für den Analysespeicher eines Containers entweder ein Datenrahmen in Spark registriert oder eine Tabelle in Spark erstellt wird, werden die Metadaten zur aktuellen Momentaufnahme der Daten im Analysespeicher in Spark abgerufen, um eine effiziente Weitergabe für anschließende Analysen zu ermöglichen. Denken Sie daran, dass die tatsächlichen Daten nicht aus dem Analysespeicher des zugrunde liegenden Containers abgerufen werden, da Spark eine verzögerte Auswertungsrichtlinie befolgt, es sei denn, eine Aktion wird für den Datenrahmen in Spark durchgeführt oder für die Tabelle in Spark wird eine SparkSQL-Abfrage ausgeführt.

Im Fall des **Ladens für einen Datenrahmen in Spark** werden die abgerufenen Metadaten für die Lebensdauer der Spark-Sitzung zwischengespeichert, und folglich werden Aktionen, die anschließend für den Datenrahmen aufgerufen werden, für die Momentaufnahme des Analysespeichers zum Zeitpunkt der Erstellung des Datenrahmens bewertet.

Andererseits werden die Metadaten des Analysespeichers im Fall des **Erstellens einer Spark-Tabelle** nicht in Spark zwischengespeichert und für jede SparkSQL-Abfrageausführung für die Spark-Tabelle neu geladen.

Folglich können Sie entscheiden, ob für den Datenrahmen in Spark geladen werden soll oder ob eine Spark-Tabelle erstellt werden soll. Dies basiert darauf, ob Sie Ihre Spark-Analyse jeweils für eine festgelegte Momentaufnahme des Analysespeichers oder für eine aktuelle Momentaufnahme des Analysespeichers bewerten möchten.

Wenn Ihre analytischen Abfragen häufig verwendete Filter enthalten, können Sie basierend auf diesen Feldern partitionieren, um die Abfrageleistung zu verbessern. Sie können in regelmäßigen Abständen einen Partitionierungsauftrag über ein Azure Synapse Spark-Notebook ausführen, um die Partitionierung für einen Analysespeicher auszulösen. Der partitionierte Speicher verweist auf das primäre ADLS Gen2-Speicherkonto, das mit Ihrem Azure Synapse-Arbeitsbereich verknüpft ist. Weitere Informationen finden Sie in den Artikeln [Benutzerdefinierte Partitionierung in Azure Synapse Link für Azure Cosmos DB (Vorschauversion)](../../cosmos-db/custom-partitioning-analytical-store.md) und [Konfigurieren der benutzerdefinierten Partitionierung zum Partitionieren von Analysespeicherdaten (Vorschau)](../../cosmos-db/configure-custom-partitioning.md).

> [!NOTE]
> Wenn Sie die Azure Cosmos DB-API von Mongo DB-Konten abfragen möchten, finden Sie [hier](../../cosmos-db/analytical-store-introduction.md#analytical-schema) weitere Informationen zur Schemadarstellung mit vollständiger Genauigkeit im Analysespeicher und den dabei zu verwendenden Namen für erweiterte Eigenschaften.

> [!NOTE]
> Beachten Sie, dass für alle `options` in den nachstehenden Befehlen die Groß-/Kleinschreibung beachtet werden muss. Beispielsweise müssen Sie `Gateway` verwenden, für `gateway` wird ein Fehler zurückgegeben.

### <a name="load-to-spark-dataframe"></a>Laden in Datenrahmen in Spark

In diesem Beispiel erstellen Sie einen Datenrahmen in Spark, der auf den Analysespeicher in Azure Cosmos DB verweist. Auf dieser Grundlage können Sie zusätzliche Analysen durchführen, indem Sie Spark-Aktionen für den Datenrahmen aufrufen. Dieser Vorgang wirkt sich nicht auf den Transaktionsspeicher aus.

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark-Tabelle erstellen

In diesem Beispiel erstellen Sie eine Tabelle in Spark, die auf den Analysespeicher in Azure Cosmos DB verweist. Auf dieser Grundlage können Sie zusätzliche Analysen durchführen, indem Sie SparkSQL-Abfragen für die Tabelle aufrufen. Dieser Vorgang wirkt sich weder auf den Transaktionsspeicher aus, noch führt er zu Datenverschiebungen jeglicher Art. Wenn Sie diese Spark-Tabelle löschen möchten, wirkt sich dies nicht auf den zugrunde liegenden Azure Cosmos DB-Container (und den entsprechenden Analysespeicher) aus. 

Dieses Szenario ist praktisch, wenn Sie Spark-Tabellen über Drittanbietertools wiederverwenden und den Zugriff auf die zugrunde liegenden Daten für die Laufzeit ermöglichen möchten.

Die Syntax für das Erstellen einer Spark-Tabelle ist folgende:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Wenn Sie über Szenarios verfügen, in denen sich das Schema des zugrunde liegenden Azure Cosmos DB-Containers im Laufe der Zeit ändert, und wenn das aktualisierte Schema automatisch in Abfragen für die Spark-Tabelle widergespiegelt werden soll, können Sie dies festlegen, indem die `spark.cosmos.autoSchemaMerge`-Option in den Optionen für Spark-Tabellen auf `true` festgelegt wird.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Schreiben eines Spark-Datenrahmens in Azure Cosmos DB-Container

In diesem Beispiel schreiben Sie einen Spark-Datenrahmen in einen Azure Cosmos DB-Container. Dieser Vorgang wirkt sich auf die Leistung transaktionaler Arbeitsauslastungen aus und benötigt Anforderungseinheiten, die im Azure Cosmos DB-Container oder in der freigegebenen Datenbank bereitgestellt werden.

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Streamingdatenrahmen aus Container laden
Bei dieser Geste verwenden Sie die Spark-Streamingfunktion, um Daten aus einem Container in einen Datenrahmen zu laden. Die Daten werden im primären Data Lake-Konto (und Dateisystem) gespeichert, das Sie mit dem Arbeitsbereich verbunden haben. 
> [!NOTE]
> Wenn Sie Verweise auf externe Bibliotheken in Synapse Apache Spark hinzufügen möchten, finden Sie [hier](../spark/apache-spark-azure-portal-add-libraries.md) weitere Informationen. Wenn Sie beispielsweise einen Spark-Datenrahmen in einem Container der Cosmos DB-API für Mongo DB erfassen möchten, können Sie den Mongo DB-Connector für Spark [hier](https://docs.mongodb.com/spark-connector/master/) nutzen.

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Laden von Streamingdatenrahmen aus Azure Cosmos DB-Containern
In diesem Beispiel verwenden Sie die Funktion für strukturiertes Streaming in Spark, um Daten aus einem Azure Cosmos DB-Container in einen Streamingdatenrahmen in Spark zu laden. Dafür nutzen Sie die Änderungsfeedfunktion in Azure Cosmos DB. Die von Spark verwendeten Prüfpunktdaten werden im primären Data Lake-Konto (und Dateisystem) gespeichert, das Sie mit dem Arbeitsbereich verbunden haben.

Wurde der Ordner */localReadCheckpointFolder* nicht erstellt (siehe Beispiel unten), wird er automatisch erstellt. Dieser Vorgang wirkt sich auf die Leistung transaktionaler Arbeitsauslastungen aus und benötigt Anforderungseinheiten, die im Azure Cosmos DB-Container oder in der freigegebenen Datenbank bereitgestellt werden.

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Schreiben von Streamingdatenrahmen in Azure Cosmos DB-Container
In diesem Beispiel schreiben Sie einen Streamingdatenrahmen in einen Azure Cosmos DB-Container. Dieser Vorgang wirkt sich auf die Leistung transaktionaler Arbeitsauslastungen aus und benötigt Anforderungseinheiten, die im Azure Cosmos DB-Container oder in der freigegebenen Datenbank bereitgestellt werden. Wurde der Ordner */localWriteCheckpointFolder* nicht erstellt (siehe Beispiel unten), wird er automatisch erstellt. 

Die Syntax in **Python** sieht folgendermaßen aus:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "Gateway")\
        .start()

streamQuery.awaitTermination()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "Gateway").
            start()

query.awaitTermination()
```


## <a name="next-steps"></a>Nächste Schritte

* [Beispiele für die ersten Schritte mit Azure Synapse Link auf GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Von Azure Synapse Link unterstützte Features für Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Herstellen einer Verbindung mit Synapse Link für Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* Sehen Sie sich das Learn-Modul zum [Abfragen von Azure Cosmos DB mit Apache Spark für Azure Synapse Analytics](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/) an.
