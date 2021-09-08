---
title: Bereitstellen des Durchsatzes in der Azure Cosmos DB-API für MongoDB-Ressourcen
description: Hier erfahren Sie, wie Sie den Durchsatz für Container, Datenbank und automatische Skalierung in der Azure Cosmos DB-API für MongoDB-Ressourcen bereitstellen. Dazu verwenden Sie Azure-Portal, die CLI, PowerShell und verschiedene andere SDKs.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: b30ac109a8186f39e29ff96ba797d0b8c98ea41c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033364"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Bereitstellen des Durchsatzes für Container, Datenbank und automatische Skalierung in der Azure Cosmos DB-API für MongoDB-Ressourcen
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

In diesem Artikel wird erläutert, wie Sie den Durchsatz in der Azure Cosmos DB-API für MongoDB bereitstellen. Sie können Standard- (manuellen) oder automatsch skalierten Durchsatz für einen Container oder eine Datenbank bereitstellen und gemeinsam für die Container in der Datenbank verwenden. Der Durchsatz kann über das Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mithilfe der Azure Cosmos DB SDKs bereitgestellt werden.

Wenn Sie eine andere API verwenden, finden Sie Informationen zum Bereitstellen des Durchsatzes in den Artikeln [SQL-API](../how-to-provision-container-throughput.md), [Cassandra-API](../cassandra/how-to-provision-throughput-cassandra.md) und [Gremlin-API](../how-to-provision-throughput-gremlin.md).

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-mongodb-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie **Neue Sammlung** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden. Wählen Sie die Option **Datenbankdurchsatz bereitstellen** aus, wenn Sie den Durchsatz auf Datenbankebene bereitstellen möchten.
   * Geben Sie eine Sammlungs-ID ein.
   * Geben Sie einen Partitionsschlüsselwert ein (etwa `/ItemID`).
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Screenshot von Daten-Explorer beim Erstellen einer neuen Sammlung mit Durchsatz auf Datenbankebene":::

> [!Note]
> Verwenden Sie beim Bereitstellen des Durchsatzes für einen Container in einem Azure Cosmos-Konto, das mit der Azure Cosmos DB-API für MongoDB konfiguriert wurde, `/myShardKey` als Partitionsschlüsselpfad.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-Vorlagen können verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure Resource Manager-Vorlagen für Azure Cosmos DB](resource-manager-template-samples.md).

## <a name="azure-cli"></a>Azure CLI

Die Azure CLI kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Beispiele finden Sie unter [Azure PowerShell-Beispiele für Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Durchsatzbereitstellung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](../request-units.md)
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)