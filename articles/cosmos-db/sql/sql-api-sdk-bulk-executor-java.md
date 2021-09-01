---
title: 'Azure Cosmos DB: Java-API, SDK und Ressourcen für BulkExecutor'
description: Wichtige Informationen zur Java-API und zum SDK für BulkExecutor, einschließlich Veröffentlichungsdaten, Deaktivierungsdaten und Änderungen an den einzelnen Versionen des Azure Cosmos DB-BulkExecutor-Java-SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9798c7cd870bc05901e47cc1bcf410bd6d5a42a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115411"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-BulkExecutor-Bibliothek: Informationen zum Download
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

| | Links/Hinweise |
|---|---|
|**Beschreibung**|Die BulkExecutor-Bibliothek ermöglicht Clientanwendungen die Ausführung von Massenvorgängen in Azure Cosmos DB-Konten. Die BulkExecutor-Bibliothek stellt die Namespaces „BulkImport“ und „BulkUpdate“ bereit. Das BulkImport-Modul kann Dokumente auf optimierte Weise per Massenimport erfassen, sodass der für eine Sammlung bereitgestellte Durchsatz maximal genutzt wird. Das BulkUpdate-Modul kann vorhandene Daten in Azure Cosmos-Containern per Massenvorgang in Form von Patches aktualisieren.|
|**SDK-Download**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bulk Executor-Bibliothek auf GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-Dokumentation**| [Java-API-Referenzdokumentation](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Erste Schritte**|[Erste Schritte mit dem Java SDK für die BulkExecutor-Bibliothek](bulk-executor-java.md)|
|**Unterstützte Mindestlaufzeit**|[Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Versionshinweise
### <a name="2123"></a><a name="2.12.3"></a>2.12.3

* Korrektur der Wiederholungsrichtlinie, wenn `GoneException` von `IllegalStateException` umschlossen ist: Diese Änderung ist erforderlich, um sicherzustellen, dass der Gatewaycache auf 410 aktualisiert wird, damit der Spark-Connector (für Spark 2.4) eine benutzerdefinierte Wiederholungsrichtlinie verwenden kann, damit Abfragen während der Partitionsteilung erfolgreich sind.

### <a name="2122"></a><a name="2.12.2"></a>2.12.2

* Es wurde ein Problem behoben, das dazu führt, dass Dokumente bei vorübergehenden Fehlern nicht immer importiert werden.

### <a name="2121"></a><a name="2.12.1"></a>2.12.1

* Upgrade zur Verwendung der neuesten Version des Cosmos Core SDK.

### <a name="2120"></a><a name="2.12.0"></a>2.12.0

* Verbesserte Verarbeitung des RU-Budgets, das über den Spark-Connector für den Massenvorgang bereitgestellt wird. Ein erster einmaliger Massenimport wird vom Spark-Connector mit baseBatchSize durchgeführt, und der RU-Verbrauch für den obigen Batchimport wird erfasst.
  Ein miniBatchSizeAdjustmentFactor wird basierend auf dem obigen RU-Verbrauch berechnet, und die Minibatchgröße wird entsprechend angepasst. Basierend auf der verstrichenen Zeit und der verbrauchten RU für jeden Batchimport wird eine Ruhezeit berechnet, um den RU-Verbrauch pro Sekunde zu begrenzen, und wird verwendet, um den Thread vor dem nächsten Batchimport anzuhalten.

### <a name="2110"></a><a name="2.11.0"></a>2.11.0

* Korrektur eines Fehlers, der Massenaktualisierungen verhindert, wenn ein geschachtelter Partitionsschlüssel verwendet wird

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Korrektur für „DocumentAnalyzer.java“, damit Schlüsselwerte geschachtelter Partitionen ordnungsgemäß aus JSON-Code extrahiert werden.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Funktionen in Massenlöschungsvorgängen hinzugefügt, mit denen Wiederholungsversuche für bestimmte Fehler ausgeführt werden und die auch eine Liste der Fehler, die wiederholt werden könnten, an den Benutzer zurückgeben.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Update für Cosmos SDK Version 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Korrektur für mergeAll zum Fortsetzen nach „ID“ und Partitionsschlüsselwert, sodass alle gepatchten Dokumenteigenschaften, die nach „ID“ und dem Partitionsschlüsselwert stehen, der aktualisierten Elementliste hinzugefügt werden.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Startgrad der Parallelität auf 1 aktualisiert und Debugprotokolle für minibatch hinzugefügt.