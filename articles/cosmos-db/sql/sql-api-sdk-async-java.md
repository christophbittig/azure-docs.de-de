---
title: 'Azure Cosmos DB: SQL Async Java-API, SDK und Ressourcen'
description: Wichtige Informationen zur SQL Async Java-API und zum SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 209fc857296556124cb5fa2003fb0c578dfe3317
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397564"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK für die SQL-API: Versionshinweise und Ressourcen
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
> * [REST](/rest/api
> * [REST-Ressourcenanbieter](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Das Async Java SDK für die SQL-API unterscheidet sich vom Java SDK für die SQL-API dahingehend, dass es asynchrone Vorgänge mit Unterstützung der [Netty-Bibliothek](https://netty.io/) bereitstellt. Das bereits vorhandene [Java SDK für die SQL-API](sql-api-sdk-java.md) unterstützt asynchrone Vorgänge nicht. 

> [!IMPORTANT]  
> Dies ist *nicht* das neueste Java SDK für Azure Cosmos DB! Erwägen Sie die Verwendung des [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) für Ihr Projekt. Befolgen Sie für ein Upgrade die Anweisungen in den Anleitungen [Migrieren zum Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) und [Gegenüberstellung von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
>

> [!IMPORTANT]  
> Am 31. August 2024 wird das Async Java SDK v2.x von Azure Cosmos DB eingestellt. Das SDK und alle Anwendungen, die es verwenden, **sind weiterhin funktionsfähig**. Azure Cosmos DB stellt jedoch keine weitere Wartung und Unterstützung für dieses SDK bereit.
> Es wird empfohlen, die obigen Anweisungen zur Migration zum Azure Cosmos DB Java SDK v4 zu befolgen.
>

| | Links |
|---|---|
| **SDK-Download** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-Dokumentation** |[Java-API-Referenzdokumentation](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**Am SDK mitwirken** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Erste Schritte** | [Erste Schritte mit dem Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Codebeispiel** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Leistungstipps**| [GitHub-Infodatei](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Unterstützte Mindestlaufzeit**|[JDK 8](/java/azure/jdk/) | 

## <a name="release-history"></a>Releaseverlauf

Der Releaseverlauf wird im Azure Cosmos DB Java SDK-Quellrepository verwaltet. Eine detaillierte Liste der Featurereleases und behobenen Fehler in jedem Release finden Sie in der [Dokumentation zum SDK-Änderungsprotokoll](https://github.com/Azure/azure-cosmosdb-java/blob/master/changelog/README.md).

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).