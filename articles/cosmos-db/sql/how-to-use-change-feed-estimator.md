---
title: 'Verwenden des Änderungsfeed-Estimators: Azure Cosmos DB'
description: Hier erfahren Sie, wie Sie mit dem Änderungsfeed-Estimator den Fortschritt Ihres Änderungsfeedprozessors analysieren
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a94ac4dd7b0006f3c1dc5a4fb7e8a95666780f1b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115296"
---
# <a name="use-the-change-feed-estimator"></a>Verwenden des Änderungsfeed-Estimators
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie den Fortschritt Ihrer [Änderungsfeedprozessor](./change-feed-processor.md)-Instanzen beim Lesen des Änderungsfeeds überwachen können.

## <a name="why-is-monitoring-progress-important"></a>Warum ist die Überwachung des Fortschritts wichtig?

Der Änderungsfeedprozessor fungiert als Zeiger, der sich im [Änderungsfeed](../change-feed.md) vorwärts bewegt und die Änderungen an eine Delegatimplementierung übergibt.

Durch die Bereitstellung eines Änderungsfeedprozessors können Änderungen mit einer bestimmten Rate verarbeitet werden, die auf den verfügbaren Ressourcen wie CPU, Arbeitsspeicher, Netzwerk usw. basiert.

Wenn diese Rate langsamer ist als die Rate, mit der Ihre Änderungen in Ihrem Azure Cosmos-Container durchgeführt werden, gerät der Prozessor in den Rückstand.

Mit diesem Szenario können wir ermitteln, ob wir unsere Bereitstellung des Änderungsfeedprozessors skalieren müssen.

## <a name="implement-the-change-feed-estimator"></a>Implementieren des Änderungsfeed-Estimators

### <a name="as-a-push-model-for-automatic-notifications"></a>Als Push-Modell für automatische Benachrichtigungen

Wie der [Änderungsvorschubprozessor](./change-feed-processor.md) funktioniert auch der Änderungsfeed-Estimator als Push-Modell. Der Estimator misst die Differenz zwischen dem letzten verarbeiteten Element (durch den Zustand des Leasescontainers definiert) und der letzten Änderung im Container und überträgt diesen Wert per Pushvorgang an einen Delegat. Das Intervall, in dem die Messung durchgeführt wird, kann auch mit einem Standardwert von fünf Sekunden angepasst werden.

Hier sehen Sie ein Beispiel für die Definition eines Änderungsfeedprozessors:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Die richtige Methode zum Initialisieren eines Estimators zum Messen dieses Prozessors wäre die Verwendung von `GetChangeFeedEstimatorBuilder` wie folgt:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Hier stimmen sowohl `leaseContainer` als auch die Namen von Prozessor und Estimator überein.

Die anderen beiden Parameter sind der Delegat. Dieser erhält eine Zahl, die angibt, **wie viele Änderungen noch vom Prozessor gelesen werden müssen** und in welchem Zeitintervall diese Messung durchgeführt werden soll.

Hier sehen Sie ein Beispiel für einen Delegat, der die Schätzung empfängt:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Sie können diese Schätzung an Ihre Überwachungslösung senden, um Ihren Fortschritt im Laufe der Zeit zu prüfen.

### <a name="as-an-on-demand-detailed-estimation"></a>Als detaillierte Schätzung auf Abruf

Im Gegensatz zum Push-Modell gibt es eine Alternative, bei der Sie die Schätzung auf Anforderung erhalten. Dieses Modell bietet auch ausführlichere Informationen:

* Die geschätzte Verzögerung pro Lease.
* Die-Instanz, welche die einzelnen Lease besitzt und bearbeitet, damit Sie ermitteln können, ob ein Problem in einer Instanz vorliegt.

Wenn Ihr Änderungsfeed-Prozessor wie folgt definiert ist:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

Sie können die Schätzung mit der gleichen Lease-Konfiguration erstellen:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

Und wenn Sie die gewünschte Häufigkeit benötigen, können Sie die detaillierte Schätzung abrufen:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

Jede `ChangeFeedProcessorState` enthält die Lease-und Lag-Informationen sowie die aktuelle Instanz, die es besitzt. 

> [!NOTE]
> Der Änderungsfeed-Estimator muss weder im Rahmen Ihres Änderungsfeedprozessors bereitgestellt werden noch Teil desselben Projekts sein. Er kann unabhängig sein und in einer völlig anderen Instanz ausgeführt werden, was empfehlenswert ist. Er muss lediglich den gleichen Namen und die gleiche Leasekonfiguration verwenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Verwendungsbeispiele auf GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Zusätzliche Beispiele auf GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über den Änderungsfeedprozessor:

* [Änderungsfeedprozessor in Azure Cosmos DB](change-feed-processor.md)
* [Konfigurieren der Startzeit des Änderungsfeedprozessors](./change-feed-processor.md#starting-time)