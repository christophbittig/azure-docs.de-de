---
title: Pullmodell für Änderungsfeed
description: Erfahren Sie, wie Sie das Pullmodell für den Azure Cosmos DB-Änderungsfeed verwenden, um den Änderungsfeed zu lesen. Erfahren Sie außerdem etwas über die Unterschiede zwischen dem Pullmodell und dem Änderungsfeedprozessor.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/02/2021
ms.reviewer: sngun
ms.openlocfilehash: 06345674b17b790cadf69a2b10383015fdaaa134
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114769"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Pullmodell für den Änderungsfeed in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Mit dem Pullmodell für den Änderungsfeed können Sie den Azure Cosmos DB-Änderungsfeed in Ihrem eigenen Tempo verwenden. Wie Sie bereits beim [Änderungsfeedprozessor](change-feed-processor.md) können Sie das Pullmodell für den Änderungsfeed verwenden, um die Verarbeitung von Änderungen über mehrere Änderungsfeedconsumer zu parallelisieren.

## <a name="comparing-with-change-feed-processor"></a>Vergleich mit dem Änderungsfeedprozessor

In vielen Szenarien können Sie den Änderungsfeed entweder mit dem [Änderungsfeedprozessor](change-feed-processor.md) oder mit dem Pullmodell verarbeiten. Die Fortsetzungstoken des Pullmodells und der Leasecontainer des Änderungsfeedprozessors sind beides „Lesezeichen“ für das zuletzt verarbeitete Element (oder den Batch von Elementen) im Änderungsfeed.

Es ist jedoch nicht möglich, Fortsetzungstoken in einen Leasecontainer (oder umgekehrt) zu konvertieren.

> [!NOTE]
> In den meisten Fällen ist es am einfachsten, zum Lesen des Änderungsfeeds den [Änderungsfeedprozessor](change-feed-processor.md) zu verwenden.

In folgenden Szenarien sollten Sie die Verwendung des Pullmodells in Erwägung ziehen:

- Lesen der Änderungen von einem bestimmten Partitionsschlüssel
- Steuern der Geschwindigkeit, mit der der Client Änderungen für die Verarbeitung empfängt
- Ausführen eines einmaligen Lesevorgangs über die vorhandenen Daten im Änderungsfeed (z. B. für eine Datenmigration)

Im Folgenden sind einige wichtige Unterschiede zwischen dem Änderungsfeedprozessor und dem Pullmodell aufgeführt:

|Funktion  | Change Feed Processor| Pull-Modell |
| --- | --- | --- |
| Verfolgen der aktuellen Position bei der Verarbeitung des Änderungsfeeds | Lease (gespeichert in einem Azure Cosmos DB-Container) | Fortsetzungstoken (im Arbeitsspeicher gespeichert oder manuell persistent gespeichert) |
| Möglichkeit zur Wiederholung vergangener Änderungen | Ja, mit Pushmodell | Ja, mit Pullmodell|
| Pullen zukünftiger Änderungen | Automatische Überprüfung auf Änderungen basierend auf einem vom Benutzer angegebenen `WithPollInterval` | Manuell |
| Verhalten ohne neue Änderungen | Automatisches Warten auf `WithPollInterval` und neue Überprüfung | Der Status muss überprüft und manuell erneut überprüft werden |
| Verarbeiten von Änderungen aus dem gesamten Container | Ja, mit automatischer Parallelisierung über mehrere Threads/Computer hinweg, die denselben Container verwenden| Ja, mit manueller Parallelisierung mithilfe von FeedRange |
| Verarbeiten von Änderungen von nur einem Partitionsschlüssel | Nicht unterstützt | Ja|

> [!NOTE]
> Im Gegensatz zum Lesen mithilfe des Änderungsfeedprozessors müssen Sie Fälle explizit behandeln, bei denen keine neuen Änderungen vorliegen. 

## <a name="consuming-an-entire-containers-changes"></a>Verwenden der Änderungen eines vollständigen Containers

Sie können einen `FeedIterator` erstellen, um den Änderungsfeed mithilfe des Pullmodells zu verarbeiten. Wenn Sie erstmalig einen `FeedIterator` erstellen, müssen Sie einen erforderlichen `ChangeFeedStartFrom`-Wert angeben, der sowohl die Anfangsposition für das Lesen von Änderungen als auch den gewünschten `FeedRange` umfasst. Der `FeedRange` ist ein Bereich von Partitionsschlüsselwerten und gibt die Elemente an, die mit diesem speziellen `FeedIterator` aus dem Änderungsfeed gelesen werden.

Optional können Sie `ChangeFeedRequestOptions` zum Festlegen eines `PageSizeHint` angeben. Diese Eigenschaft legt die Höchstzahl von Elementen fest, die pro Seite empfangen werden können. Wenn Vorgänge in der überwachten Sammlung über gespeicherte Prozeduren ausgeführt werden, wird der Transaktionsbereich beim Lesen von Elementen aus dem Änderungsfeed beibehalten. Dadurch kann die Anzahl der empfangenen Elemente höher als der angegebene Wert sein, sodass die von derselben Transaktion geänderten Elemente als Teil eines atomischen Batches zurückgegeben werden.

Der `FeedIterator` ist in zwei Varianten verfügbar. Zusätzlich zu den unten aufgeführten Beispielen, die Entitätsobjekte zurückgeben, können Sie die Antwort auch mit `Stream`-Unterstützung abrufen. Datenströme ermöglichen Ihnen, Daten zu lesen, ohne sie zuvor deserialisieren und auf Clientressourcen speichern zu müssen.

Im Folgenden finden Sie ein Beispiel für das Abrufen eines `FeedIterator`, der Entitätsobjekte zurückgibt (in diesem Fall ein `User`-Objekt):

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Im Folgenden finden Sie ein Beispiel für das Abrufen eines `FeedIterator`, der einen `Stream` zurückgibt:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Wenn Sie keinen `FeedRange` für einen `FeedIterator` angeben, können Sie den Änderungsfeed eines vollständigen Containers in Ihrem eigenen Tempo verarbeiten. Im folgenden Beispiel wird das Lesen aller Änderungen ab dem aktuellen Zeitpunkt begonnen:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    FeedResponse<User> response = await iteratorForTheEntireContainer.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else 
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

Da es sich beim Änderungsfeed im Grunde um eine unbegrenzte Liste von Elementen handelt, die alle zukünftigen Schreib- und Updatevorgänge enthält, ist der Wert von `HasMoreResults` immer TRUE. Wenn Sie versuchen, den Änderungsfeed zu lesen, und keine neuen Änderungen vorliegen, erhalten Sie eine Antwort mit dem Status `NotModified`. Im obigen Beispiel wird es so gehandhabt, dass 5 Sekunden gewartet wird, bevor nochmal auf Änderungen geprüft wird.

## <a name="consuming-a-partition-keys-changes"></a>Verwenden der Änderungen eines Partitionsschlüssels

In einigen Fällen möchten Sie möglicherweise nur die Änderungen eines bestimmten Partitionsschlüssels verarbeiten. Sie können einen `FeedIterator` für einen bestimmten Partitionsschlüssel abrufen und die Änderungen auf die gleiche Weise wie für einen vollständigen Container verarbeiten.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    FeedResponse<User> response = await iteratorForThePartitionKey.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Verwenden von FeedRange für die Parallelisierung

Beim [Änderungsfeedprozessor](change-feed-processor.md) wird die Arbeit automatisch auf mehrere Consumer verteilt. Beim Pullmodell für den Änderungsfeed können Sie `FeedRange` verwenden, um die Verarbeitung des Änderungsfeeds zu parallelisieren. Ein `FeedRange` stellt einen Bereich von Partitionsschlüsselwerten dar.

Das folgende Beispiel zeigt, wie Sie eine Liste von Bereichen für Ihren Container abrufen:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Wenn Sie eine Liste von FeedRanges für Ihren Container abrufen, erhalten Sie einen `FeedRange` pro [physischer Partition](../partitioning-overview.md#physical-partitions).

Mithilfe eines `FeedRange` können Sie dann einen `FeedIterator` erstellen, um die Verarbeitung des Änderungsfeeds parallel auf mehrere Computer oder Threads zu verteilen. Im Gegensatz zum vorherigen Beispiel, in dem gezeigt wurde, wie Sie einen `FeedIterator` für den gesamten Container oder einen einzelnen Partitionsschlüssel abrufen, können Sie mithilfe von FeedRanges mehrere FeedIterators abrufen, über die der Änderungsfeed parallel verarbeitet werden kann.

Wenn Sie FeedRanges verwenden möchten, benötigen Sie einen Orchestratorprozess, der sie abruft und auf diese Computer verteilt. Diese Verteilung könnte wie folgt erfolgen:

* Verwenden von `FeedRange.ToJsonString` und Verteilen dieses Zeichenfolgenwerts. Die Consumer können diesen Wert mit `FeedRange.FromJsonString` verwenden.
* Wenn die Verteilung bereits bearbeitet wird: Übergeben eines Verweises auf das `FeedRange`-Objekt

Das folgende Beispiel zeigt, wie Sie vom Anfang des Änderungsfeeds des Containers lesen und dafür zwei hypothetische, getrennte Computer verwenden, die parallel lesen:

Computer 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    FeedResponse<User> response = await iteratorA.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

Computer 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    FeedResponse<User> response = await iteratorA.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

## <a name="saving-continuation-tokens"></a>Speichern von Fortsetzungstoken

Sie können die Position Ihres `FeedIterator` speichern, indem Sie das Fortsetzungstoken abrufen. Ein Fortsetzungstoken ist ein Zeichenfolgenwert, der die letzten verarbeiteten Änderungen Ihres FeedIterators nachverfolgt und dem `FeedIterator` eine spätere Fortsetzung an dieser Stelle ermöglicht. Der folgende Code liest den Änderungsfeed seit der Erstellung des Containers. Wenn keine weiteren Änderungen mehr verfügbar sind, wird ein Fortsetzungstoken persistent gespeichert, sodass der Änderungsfeed später weiter verarbeitet werden kann.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
    FeedResponse<User> response = await iterator.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        continuation = response.ContinuationToken;
        // Stop the consumption since there are no new changes
        break;
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}

// Some time later when I want to check changes again
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

Solange der Cosmos-Container vorhanden ist, läuft das Fortsetzungstoken eines FeedIterators nie ab.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Änderungsfeeds](../change-feed.md)
* [Verwenden des Änderungsfeedprozessors](change-feed-processor.md)
* [Auslösen von Azure Functions](change-feed-functions.md)
