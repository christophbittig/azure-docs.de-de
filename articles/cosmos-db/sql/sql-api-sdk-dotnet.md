---
title: 'Azure Cosmos DB: SQL .NET-API, -SDK und -Ressourcen'
description: Erhalten Sie alle Informationen zu SQL .NET-API und -SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-dotnet
ms.openlocfilehash: a52d3e5690a043e3f886dd0a87db5ffba155528f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493360"
---
# <a name="azure-cosmos-db-net-sdk-v2-for-sql-api-download-and-release-notes-deprecated"></a>Azure Cosmos DB .NET SDK v2 für SQL-API: Download und Versionshinweise (veraltet)
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

| | Links |
|---|---|
|**SDK-Download**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API-Dokumentation**|[.NET API-Referenzdokumentation](/dotnet/api/overview/azure/cosmosdb)|
|**Beispiele**|[.NET-Codebeispiele](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Erste Schritte**|[Erste Schritte mit dem Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Web-App-Tutorial**|[Entwicklung von Webanwendungen mit Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuelles unterstütztes Framework**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Am 31. August 2024 wird das Azure Cosmos DB .NET SDK v2 eingestellt. Das SDK und alle Anwendungen, die es verwenden, sind weiterhin funktionsfähig. Azure Cosmos DB stellt jedoch keine weitere Wartung und Unterstützung für dieses SDK bereit. Es wird empfohlen, diesen Anweisungen zu folgen, um zur neuesten SDK-Version (.NET SDK v3) zu migrieren.
>

> [!NOTE]
> Wenn Sie .NET Framework verwenden, sehen Sie sich die neueste Version 3.x des [.NET SDK](sql-api-sdk-dotnet-standard.md) an, das .NET Standard als Ziel verwendet.

## <a name="release-history"></a><a name="release-history"></a> Releaseverlauf

Der Releaseverlauf wird im Azure Cosmos DB .NET SDK-Quellrepository verwaltet. Eine detaillierte Liste der Featurereleases und behobenen Fehler in jedem Release finden Sie in der [Dokumentation zum SDK-Änderungsprotokoll](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md).

Da Version 3 des Azure Cosmos DB .NET SDK aktualisierte Features und verbesserte Leistung enthält, wird die Version 2.x dieses SDK am 31. August 2024 eingestellt.Sie müssen für Ihr SDK bis zu diesem Datum ein Update auf Version 3 durchführen. Es wird empfohlen, die Anweisungen zur Migration zum Azure Cosmos DB .NET SDK Version 3 zu befolgen.

## <a name="recommended-version"></a><a name="recommended-version"></a> Empfohlene Version

Die 2.x.x-Version umfasst verschiedene .NET SDK-Unterversionen. **Die empfohlene Mindestversion ist 2.16.2**.

## <a name="known-issues"></a><a name="known-issues"></a> Bekannte Probleme

Im Folgenden finden Sie eine Liste bekannter Probleme, die sich auf die [empfohlene Mindestversion](#recommended-version) auswirken:

| Problem | Auswirkung | Minderung | Nachverfolgungslink |
| --- | --- | --- | --- |
| Wenn Sie den direkten Modus mit einem Konto mit mehreren Schreibstandorten verwenden, erkennt das SDK möglicherweise nicht, wenn dem Konto eine Region hinzugefügt wird. Der Hintergrundprozess zum [Aktualisieren der Kontoinformationen](troubleshoot-sdk-availability.md#adding-a-region-to-an-account) kann nicht gestartet werden. |Wenn dem Konto, das Teil der „PreferredLocations“ ist, eine neue Region mit höherer Ordnung als die aktuelle Region hinzugefügt wird, erkennt das SDK die neue verfügbare Region nicht. |Starten Sie die Anwendung neu. |https://github.com/Azure/azure-cosmos-dotnet-v2/issues/852 |

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
