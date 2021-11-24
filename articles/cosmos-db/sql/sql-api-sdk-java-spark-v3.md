---
title: 'Apache Spark 3-OLTP-Connector von Azure Cosmos DB für die SQL-API (Vorschauversion): Versionshinweise und Ressourcen'
description: Hier finden Sie Informationen zum Apache Spark 3-OLTP-Connector von Azure Cosmos DB für die SQL-API, z. B. Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/12/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: abb10485baebc0b7379ec650fd9eeb95ebadf326
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491277"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Apache Spark 3-OLTP-Connector von Azure Cosmos DB für die Core-API (SQL): Versionshinweise und Ressourcen
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

**OLTP Spark-Connector von Azure Cosmos DB** verfügt über Apache Spark-Unterstützung für Azure Cosmos DB per SQL-API. Azure Cosmos DB ist ein global verteilter Datenbankdienst, der Entwicklern durch eine Vielzahl von Standard-APIs wie SQL, MongoDB, Cassandra, Graph und Tabellen die Arbeit mit Daten ermöglicht.

Wenn Sie Feedback oder Verbesserungsvorschläge haben, erstellen Sie ein Issue in unserem [SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-java/issues/new).

## <a name="documentation-links"></a>Dokumentationslinks

* [Erste Schritte](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
* [Katalog-API](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
* [Konfigurationsparameterreferenz](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)

## <a name="version-compatibility"></a>Versionskompatibilität

| Connector     | Spark-Mindestversion | Java-Mindestversion | Unterstützte Scala-Versionen | Unterstützte Databricks-Runtimes |
| ------------- | --------------------- | -------------------- | -----------------------  | ----------------------------- |
| 4.4.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*, 9.\*                    |
| 4.3.1         | 3.1.1                 | 8                    | 2,12                     | 8.\*, 9.\*                    |
| 4.3.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*, 9.\*                    |
| 4.2.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.1.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0         | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0-beta.3  | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0-beta.2  | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |
| 4.0.0-beta.1  | 3.1.1                 | 8                    | 2,12                     | 8.\*                          |

## <a name="download"></a>Herunterladen

Sie können die Maven-Koordinate der JAR-Datei verwenden, um den Spark-Connector automatisch über Databricks Runtime 8 von Maven zu installieren: `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.4.0`

Sie können die Integration auch im Cosmos DB-Spark-Connector in Ihr SBT-Projekt integrieren:

```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.4.0"
```

Der Azure Cosmos DB-Spark-Connector ist im [zentralen Maven-Repository verfügbar](https://search.maven.org/search?q=g:com.azure.cosmos.spark).

Wenn Sie auf einen Fehler stoßen oder eine Änderung eines Features vorschlagen möchten, [reichen Sie ein Issue ein](https://github.com/Azure/azure-sdk-for-java/issues/new). 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Erfahren Sie mehr über [Apache Spark](https://spark.apache.org/).