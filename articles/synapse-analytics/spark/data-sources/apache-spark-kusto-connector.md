---
title: Azure Data Explorer (Kusto)
description: Dieser Artikel enthält Informationen dazu, wie Sie den Connector zum Verschieben von Daten zwischen Azure Data Explorer (Kusto) und serverlosen Apache Spark-Pools verwenden.
services: synapse-analytics
ms.author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/19/2020
ms.reviewer: ManojRaheja
author: midesa
ms.openlocfilehash: 82310e14b3eac25be038a7748d1992daef72d83f
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516673"
---
# <a name="azure-data-explorer-kusto-connector-for-apache-spark"></a>Azure Data Explorer-Connector (Kusto) für Apache Spark
Der Azure Data Explorer-Connector (Kusto) für Apache Spark ist für die effiziente Übertragung von Daten zwischen Kusto-Clustern und Spark konzipiert. Dieser Connector ist in Python, Java und .NET verfügbar. Er ist in die Apache Spark 2.4-Runtime von Azure Synapse integriert.

## <a name="authentication"></a>Authentifizierung
Bei Verwendung von Azure Synapse-Notebooks oder Apache Spark-Auftragsdefinitionen erfolgt die Authentifizierung zwischen Systemen nahtlos mit dem verknüpften Dienst. Der Tokendienst stellt eine Verbindung mit Azure Active Directory her, um Sicherheitstoken für den Zugriff auf den Kusto-Cluster zu erhalten.

Bei Azure Synapse-Pipelines wird für die Authentifizierung der Dienstprinzipalname verwendet. Derzeit werden verwaltete Identitäten nicht mit dem Azure Data Explorer-Connector unterstützt.

## <a name="prerequisites"></a>Voraussetzungen 
  - [Verbindung mit Azure Data Explorer:](../../quickstart-connect-azure-data-explorer.md) Sie müssen einen verknüpften Dienst einrichten, um eine Verbindung mit einem vorhandenen Kusto-Cluster herzustellen.

## <a name="limitations"></a>Einschränkungen
  - Der Azure Data Explorer-Connector (Kusto) wird derzeit nur für die Apache Spark 2.4-Runtime von Azure Synapse unterstützt.
  - Der verknüpfte Azure Data Explorer-Dienst kann nur mit dem Dienstprinzipalnamen konfiguriert werden.
  - Innerhalb von Azure Synapse-Notebooks oder Apache Spark-Auftragsdefinitionen verwendet der Azure Data Explorer-Connector AAD-Passthrough für die Verbindung mit dem Kusto-Cluster.


## <a name="use-the-azure-data-explorer-kusto-connector"></a>Verwenden des Azure Data Explorer-Connectors (Kusto)
Der folgende Abschnitt enthält ein einfaches Beispiel für das Schreiben von Daten in eine Kusto-Tabelle und das Lesen von Daten aus einer Kusto-Tabelle. Eine ausführliche Dokumentation finden Sie im [Projekt für den Azure Data Explorer-Connector (Kusto)](https://github.com/Azure/azure-kusto-spark). 

### <a name="read-data"></a>Lesen von Daten

```python
kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .load()

display(kustoDf)
```

Sie können auch Batchlesevorgänge mit erzwungenem Verteilungsmodus und anderen erweiterten Optionen durchführen. Weitere Informationen finden Sie in der [Referenz zu Kusto-Quelloptionen](https://github.com/Azure/azure-kusto-spark/blob/master/connector/src/main/scala/com/microsoft/kusto/spark/datasource/KustoSourceOptions.scala).

```python
crp = sc._jvm.com.microsoft.azure.kusto.data.ClientRequestProperties()
crp.setOption("norequesttimeout",True)
crp.toString()

kustoDf  = spark.read \
            .format("com.microsoft.kusto.spark.synapse.datasource") \
            .option("spark.synapse.linkedService", "<link service name>") \
            .option("kustoDatabase", "<Database name>") \
            .option("kustoQuery", "<KQL Query>") \
            .option("clientRequestPropertiesJson", crp.toString()) \
            .option("readMode", 'ForceDistributedMode') \
            .load()

display(kustoDf) 
```
### <a name="write-data"></a>Schreiben von Daten

```python
df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .mode("Append") \
    .save()
```
Darüber hinaus können Sie auch Batchschreibvorgänge mit Daten ausführen, indem Sie zusätzliche Erfassungseigenschaften bereitstellen. Weitere Informationen zu den unterstützten Erfassungseigenschaften finden Sie im [Referenzmaterial zu Kusto-Erfassungseigenschaften](/azure/data-explorer/ingestion-properties).


 ```python
extentsCreationTime = sc._jvm.org.joda.time.DateTime.now().plusDays(1)
csvMap = "[{\"Name\":\"ColA\",\"Ordinal\":0},{\"Name\":\"ColB\",\"Ordinal\":1}]"
# Alternatively use an existing csv mapping configured on the table and pass it as the last parameter of SparkIngestionProperties or use none


sp = sc._jvm.com.microsoft.kusto.spark.datasink.SparkIngestionProperties(
        False, ["dropByTags"], ["ingestByTags"], ["tags"], ["ingestIfNotExistsTags"], extentsCreationTime, csvMap, None)

df.write \
    .format("com.microsoft.kusto.spark.synapse.datasource") \
    .option("spark.synapse.linkedService", "<link service name>") \
    .option("kustoDatabase", "<Database name>") \
    .option("kustoTable", "<Table name>") \
    .option("sparkIngestionPropertiesJson", sp.toString()) \
    .option("tableCreateOptions","CreateIfNotExist") \
    .mode("Append") \
    .save()
```

## <a name="next-steps"></a>Nächste Schritte
- [Herstellen einer Verbindung mit Azure Data Explorer](../../quickstart-connect-azure-data-explorer.md)
- [Azure Data Explorer (Kusto): Apache Spark-Connector](https://github.com/Azure/azure-kusto-spark)
