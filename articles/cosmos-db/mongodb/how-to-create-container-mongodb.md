---
title: Erstellen eines Containers für die Azure Cosmos DB-API für MongoDB
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal, .NET, Java, Node.js und anderen SDKs einen Container für die Azure Cosmos DB-API für MongoDB erstellen.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-csharp
ms.openlocfilehash: 40458740092c7b462683512e3c419eff417019da
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038522"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Erstellen eines Containers für die Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

In diesem Artikel werden die verschiedenen Möglichkeiten zur Erstellung eines Containers für die Azure Cosmos DB-API für MongoDB erläutert. Hier wird gezeigt, wie ein Container mithilfe des Azure-Portals, mit der Azure CLI, PowerShell oder unterstützten SDKs erstellt wird. In diesem Artikel erfahren Sie, wie Sie einen Container erstellen, den Partitionsschlüssel angeben und den Durchsatz bereitstellen.

Hier werden die verschiedenen Möglichkeiten zur Erstellung eines Containers für die Azure Cosmos DB-API für MongoDB erläutert. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Erstellen des Containers in den Artikeln zur [SQL-API](../how-to-create-container.md), [Cassandra-API](../cassandra/how-to-create-container-cassandra.md), [Gremlin-API](../how-to-create-container-gremlin.md) und [Tabellen-API](../table/how-to-create-container.md).

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Erstellen mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-mongodb-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie **Neuer Container** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden.
   * Geben Sie eine Container-ID ein.
   * Geben Sie einen Shardschlüssel ein.
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Screenshot der Azure Cosmos DB-API für MongoDB, Dialogfeld „Container hinzufügen“":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Erstellen mithilfe des .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB Wire Protocol versteht das Konzept für [Anforderungseinheiten](../request-units.md) nicht. Verwenden Sie zum Erstellen einer neuen Sammlung mit bereitgestelltem Durchsatz das Azure-Portal oder Cosmos DB SDKs für die SQL-API.

Wenn eine Timeoutausnahme zurückgegeben wird, wenn eine Sammlung erstellt wird, führen Sie einen Lesevorgang aus, um zu überprüfen, ob die Sammlung erfolgreich erstellt wurde. Der Lesevorgang gibt eine Ausnahme zurück, bis der Vorgang zum Erstellen der Sammlung erfolgreich war. Eine Liste der von dem Erstellvorgang unterstützten Statuscodes finden Sie im Artikel [HTTP-Statuscodes für Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Erstellen mithilfe der Azure CLI

[Erstellen Sie über die Azure CLI eine Sammlung für Azure Cosmos DB für die MongoDB-API.](../scripts/cli/mongodb/create.md) Eine Auflistung aller Azure CLI-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

[Erstellen Sie über PowerShell eine Sammlung für Azure Cosmos DB für die MongoDB-API.](../scripts/powershell/mongodb/create.md) Eine Auflistung aller PowerShell-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [PowerShell-Beispiele](powershell-samples.md).

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Erstellen eines Containers mithilfe von Azure Resource Manager-Vorlagen

[Erstellen Sie mithilfe einer Resource Manager-Vorlage eine Sammlung für Azure Cosmos DB für die MongoDB-API.](../manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput)

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](../partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](../request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](../set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](../account-databases-containers-items.md)
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)