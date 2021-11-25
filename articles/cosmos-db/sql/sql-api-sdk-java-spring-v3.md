---
title: Versionshinweise und Ressourcen zu Spring Data Azure Cosmos DB v3 für die SQL-API
description: Hier finden Sie Informationen zu Spring Data Azure Cosmos DB v3 für die SQL-API, z. B. Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 5cf49696bb162bff92acc52db1e2ab95cee3927c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487707"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v3 für die Core (SQL)-API: Versionshinweise und Ressourcen
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

Mit Spring Data Azure Cosmos DB Version 3 für Core (SQL) können Entwickler Azure Cosmos DB in Spring-Anwendungen nutzen. Bei Spring Data Azure Cosmos DB wird die Spring Data-Schnittstelle für die Bearbeitung von Datenbanken und Sammlungen, die Verwendung von Dokumenten und die Ausgabe von Abfragen verfügbar gemacht. Für dasselbe Maven-Artefakt werden sowohl synchrone als auch asynchrone (reaktive) APIs unterstützt. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB ist abhängig vom Spring Data-Framework.
> 
> Die Versionen 3.0.0 bis 3.4.0 von Azure Spring Data Cosmos unterstützen die Spring Data-Versionen 2.2 und 2.3.
> 
> Die Versionen 3.5.0 und höher von Azure Spring Data Cosmos unterstützen die Spring Data-Versionen 2.4.3 und höher.
>

Das [Spring-Framework](https://spring.io/projects/spring-framework) ist ein Programmier- und Konfigurationsmodell, mit dem die Java-Anwendungsentwicklung optimiert wird. Spring optimiert die „Verknüpfung“ von Anwendungen mithilfe von Abhängigkeitsinjektion. Spring ist bei vielen Entwicklern beliebt, weil dadurch das Erstellen und Testen von Anwendungen vereinfacht wird. Mit [Spring Boot](https://spring.io/projects/spring-boot) wird diese „Verknüpfung“ auf die Entwicklung von Webanwendungen und Microservices ausgedehnt. [Spring Data](https://spring.io/projects/spring-data) ist ein Programmiermodell und -framework für den Zugriff auf Datenspeicher wie Azure Cosmos DB, im Kontext einer Spring- bzw. Spring Boot-Anwendung. 

Sie können Spring Data Azure Cosmos DB in Ihren [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/)-Anwendungen verwenden.

> [!IMPORTANT]  
> Diese Versionshinweise gelten für Version 3 von Spring Data Azure Cosmos DB. Sie können [Versionshinweise zu Version 2 hier](sql-api-sdk-java-spring-v2.md) finden. 
>
> Spring Data Azure Cosmos DB unterstützt nur die SQL-API.
>
> In diesen Artikeln finden Sie Informationen zu Spring Data für andere Azure Cosmos DB-APIs:
> * [Spring Data für Apache Cassandra mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>

## <a name="get-started-fast"></a>Schneller Einstieg

  Nutzen Sie zur schnellen Einrichtung von Spring Data Azure Cosmos DB unsere [Anleitung für Spring Boot Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). Spring Boot Starter wird für die ersten Schritte mit dem Spring Data Azure Cosmos DB-Connector empfohlen.

  Alternativ können Sie der Datei `pom.xml` wie folgt die Spring Data Azure Cosmos DB-Abhängigkeit hinzufügen:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Nützliche Inhalte

| Inhalt | Link |
|---|---|
|**SDK-Download**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API-Dokumentation** | [Java-API-Referenzdokumentation](/java/api/overview/azure/spring-data-cosmos-readme?view=azure-java-stable&preserve-view=true) |
|**Am SDK mitwirken** | [Azure SDK für das zentrale Java-Repository auf GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Erste Schritte** | [Schnellstart: Erstellen einer Spring Data Azure Cosmos DB v3-App zum Verwalten von Azure Cosmos DB-SQL-API-Daten](./create-sql-api-spring-data.md) <br> [GitHub-Repository mit Schnellstartcode](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Einfache Codebeispiele** | [Azure Cosmos DB SQL-API: Spring Data Azure Cosmos DB v3-Beispiele](sql-api-spring-data-sdk-samples.md) <br> [GitHub-Repository mit Beispielcode](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Leistungstipps**| [Leistungstipps für das Azure Cosmos DB Java SDK v4](performance-tips-java-sdk-v4-sql.md) (anwendbar auf Spring Data)| 
| **Problembehandlung** | [Behandeln von Problemen bei der Verwendung des Azure Cosmos DB Java SDK v4 mit SQL-API-Konten](troubleshoot-java-sdk-v4-sql.md) (anwendbar auf Spring Data) | 
| **Azure Cosmos DB-Workshops und -Labs** |[Homepage der Cosmos DB-Workshops](https://aka.ms/cosmosworkshop)

> [!IMPORTANT]
> * Das Release 3.5.0 unterstützt Spring Boot 2.4.3 und höher.

## <a name="release-history"></a>Releaseverlauf
Der Releaseverlauf wird im Repository „azure-sdk-for-java“ verwaltet. Eine ausführliche Liste der Releases finden Sie in der [Datei „changelog“](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md).

## <a name="recommended-version"></a>Empfohlene Version

Es wird dringend empfohlen, Version 3.10.0 oder höher zu verwenden.

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Spring Data Azure Cosmos DB unterstützt Java JDK 8 und Java JDK 11.
* Spring Data 2.3 wird aktuell unterstützt, Spring Data 2.4 nicht.

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Informieren Sie sich genauer über das [Spring-Framework](https://spring.io/projects/spring-framework).

Informieren Sie sich genauer über [Spring Boot](https://spring.io/projects/spring-boot).

Informieren Sie sich genauer über [Spring Data](https://spring.io/projects/spring-data).
