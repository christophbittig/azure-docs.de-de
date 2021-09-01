---
title: 'Apache Spark 2-OLTP-Connector von Azure Cosmos DB für die SQL-API: Versionshinweise und Ressourcen'
description: Hier finden Sie Informationen zum Apache Spark 2-OLTP-Connector von Azure Cosmos DB für die SQL-API – einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 318faba31bec6b08c81d5d9f1d8741ddb65b5292
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115328"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Apache Spark 2-OLTP-Connector von Azure Cosmos DB für die Core-API (SQL): Versionshinweise und Ressourcen
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3-OLTP-Connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2-OLTP-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Sie können Big Data-Analysen mit dem Apache Spark 2-OLTP-Connector von Azure Cosmos DB für Core (SQL) beschleunigen. Der Spark-Connector ermöglicht die Ausführung von [Spark](https://spark.apache.org/)-Aufträgen für Daten, die in Azure Cosmos DB gespeichert sind. Batch- und Streamverarbeitung werden unterstützt.

Sie können den Connector mit [Azure Databricks](https://azure.microsoft.com/services/databricks) oder [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) verwenden, die beide verwaltete Spark-Cluster in Azure bieten. Die folgende Tabelle zeigt die unterstützten Versionen:

| Komponente | Version |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3.*x*, 2.2.*x* und 2.1.*x* |
| Scala | 2.11 |
| Azure Databricks (Laufzeitversion) | Höher als 3.4 |

> [!WARNING]
> Dieser Connector unterstützt die Haupt-(SQL)-API von Azure Cosmos DB.
> Für die Cosmos DB-API für MongoDB verwenden Sie den [MongoDB-Connector für Spark](https://docs.mongodb.com/spark-connector/master/).
> Für die Cosmos DB-Cassandra-API verwenden Sie den [Cassandra Spark-Connector](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Ressourcen

| Resource | Link |
|---|---|
| **SDK-Download** | [Herunterladen der neuesten JAR-Datei](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API-Dokumentation** | [Spark Connector-Referenz]() |
|**Am SDK mitwirken** | [Azure Cosmos DB-Connector für Apache Spark auf GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Erste Schritte** | [Beschleunigen von Big Data-Analysen mit dem Apache Spark-Connector für Azure Cosmos DB](./create-sql-api-spark.md) <br> [Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka und Azure Cosmos DB](../../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Releaseverlauf

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Neue Funktionen
- Fügt die neue Konfigurationsoption `changefeedstartfromdatetime` hinzu, mit der die Startzeit für die Verarbeitung des Änderungsfeeds angegeben wird. Weitere Informationen finden Sie unter [Konfigurationsoptionen](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references).

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
- Korrigiert eine Regression, die zu einer übermäßigen Speicherauslastung für die Executor großer Resultsets (z. B. mit Millionen Zeilen) und damit letztendlich zum Fehler `java.lang.OutOfMemoryError: GC overhead limit exceeded` geführt hat.

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Problem bei einem Streamingprüfpunkt-Edge behoben, bei dem `ID` das Pipezeichen (|) mit angewendeter `ChangeFeedMaxPagesPerBatch`-Konfiguration umfasst.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Neue Funktionen
* Unterstützung für Massenaktualisierungen bei Verwendung geschachtelter Partitionsschlüssel hinzugefügt.
* Unterstützung von Dezimal- und Gleitkommadatentypen bei Schreibvorgängen in Azure Cosmos DB hinzugefügt.
* Unterstützung von Zeitstempeltypen mit Long-Wert (Unix-Epoche) hinzugefügt.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Problem einer Typumwandlungsausnahme behoben, die bei Verwendung der `WriteThroughputBudget`-Konfiguration aufgetreten ist.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Neue Funktionen
* Fehlerinformationen für Massenfehler zu Ausnahme und Protokoll hinzugefügt

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Probleme im Zusammenhang mit dem Streamingprüfpunkt behoben

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Zum Verringern unnötiger Informationen wurde ein Problem behoben, bei dem die Protokollstufe einer Meldung unbeabsichtigterweise auf der Stufe „FEHLER“ verblieben ist.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Fehler behoben, der beim strukturierten Streaming während der Partitionsaufteilung aufgetreten ist. Der Fehler könnte zu fehlenden Änderungsfeeddaten oder NULL-Ausnahmen bei Prüfpunktschreibvorgängen führen.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Fehler behoben, der dazu führte, dass ein benutzerdefiniertes, für „readStream“ angegebenes Schema ignoriert wurde.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Regression korrigiert (JAR-Datei ohne Shading enthält alle Abhängigkeiten mit Shading), durch die sich die Buildzeit um 50 Prozent erhöhte.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen
* Abhängigkeitsproblem korrigiert, das dazu führte, dass der direkte Transport über TCP nicht erfolgreich war und der Fehler „RequestTimeoutException“ auftrat.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Neue Funktionen
* Weniger Metadatenaufrufe dank verbesserter Verbindungsverwaltung und verbessertem Verbindungspooling.

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Erfahren Sie mehr über [Apache Spark](https://spark.apache.org/).