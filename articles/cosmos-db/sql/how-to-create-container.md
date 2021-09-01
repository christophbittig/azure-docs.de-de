---
title: Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal, .NET, Java, Python, Node.js und anderen SDKs mithilfe der SQL-API einen Container in Azure Cosmos DB erstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f45236f4ab035e03e3736811995d3dc62df7b55
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115344"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In diesem Artikel werden die verschiedenen Möglichkeiten beim Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API erläutert. Es wird gezeigt, wie Sie einen Container über das Azure-Portal, die Azure CLI, PowerShell oder unterstützte SDKs erstellen. In diesem Artikel erfahren Sie, wie Sie einen Container erstellen, den Partitionsschlüssel angeben und den Durchsatz bereitstellen.

In diesem Artikel werden die verschiedenen Möglichkeiten beim Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API erläutert. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Erstellen des Containers in den Artikeln zur [API für MongoDB](../mongodb/how-to-create-container-mongodb.md), [Cassandra-API](../cassandra/how-to-create-container-cassandra.md), [Gremlin-API](../graph/how-to-create-container-gremlin.md) und [Tabellen-API](../table/how-to-create-container.md).

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Erstellen eines Containers über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie **Neuer Container** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden.
   * Geben Sie eine **Container-ID** ein.
   * Geben Sie einen Wert für den **Partitionsschlüssel** ein (z. B. `/ItemID`).
   * Wählen Sie **Autoskalierung** oder **Manuell** für den Durchsatz aus, und geben Sie den erforderlichen **Containerdurchsatz** ein (z. B. 1000 RU/s). Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="../media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Screenshot des Daten-Explorers mit hervorgehobener Option „Neue Sammlung“":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Erstellen eines Containers mithilfe der Azure CLI

[Erstellen Sie einen Container mithilfe der Azure CLI.](manage-with-cli.md#create-a-container) Eine Auflistung aller Azure CLI-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Erstellen eines Containers mithilfe von PowerShell

[Erstellen Sie einen Container mithilfe von PowerShell.](manage-with-powershell.md#create-container) Eine Auflistung aller PowerShell-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [PowerShell-Beispiele](powershell-samples.md).

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Erstellen eines Containers mithilfe des .NET SDK

Wenn eine Timeoutausnahme zurückgegeben wird, wenn eine Sammlung erstellt wird, führen Sie einen Lesevorgang aus, um zu überprüfen, ob die Sammlung erfolgreich erstellt wurde. Der Lesevorgang gibt eine Ausnahme zurück, bis der Vorgang zum Erstellen der Sammlung erfolgreich war. Eine Liste der von dem Erstellvorgang unterstützten Statuscodes finden Sie im Artikel [HTTP-Statuscodes für Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](../partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](../request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](../set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](../account-databases-containers-items.md)