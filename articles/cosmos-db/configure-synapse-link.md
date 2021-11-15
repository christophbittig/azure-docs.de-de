---
title: Konfigurieren und Verwenden von Azure Synapse Link für Azure Cosmos DB
description: Erfahren Sie, wie Sie Synapse Link für Azure Cosmos DB-Konten aktivieren, einen Container mit aktiviertem Analysespeicher erstellen, für die Azure Cosmos-Datenbank eine Verbindung mit dem Synapse-Arbeitsbereich herstellen und Abfragen ausführen.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
ms.openlocfilehash: 271b0a6c41f37a3ac8efe6e5562af48f3f267692
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425858"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Konfigurieren und Verwenden von Azure Synapse Link für Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link für Azure Cosmos DB](synapse-link.md) ist eine cloudnative hybride Verarbeitungsfunktion für Transaktionen und Analysen (Hybrid Transactional and Analytical Processing, HTAP), mit der Sie in Azure Cosmos DB in Quasi-Echtzeit Analysen für operative Daten ausführen können. Synapse Link sorgt für eine nahtlose Integration zwischen Azure Cosmos DB und Azure Synapse Analytics.

Azure Synapse Link ist für Azure Cosmos DB SQL-API-Container oder für Azure Cosmos DB API für Mongo DB-Sammlungen verfügbar. Führen Sie die folgenden Schritte aus, um mit Azure Synapse Link für Azure Cosmos DB Analyseabfragen durchzuführen:

* [Aktivieren von Synapse Link für Ihre Azure Cosmos DB-Konten](#enable-synapse-link)
* [Erstellen eines für einen Analysespeicher aktivierten Azure Cosmos DB-Containers](#create-analytical-ttl)
* [Optional: Aktualisieren des TTL-Werts des Analysespeichers für einen Azure Cosmos DB-Container](#update-analytical-ttl)
* [Herstellen einer Verbindung mit einem Synapse-Arbeitsbereich für Ihre Azure Cosmos DB-Datenbank](#connect-to-cosmos-database)
* [Abfragen des Analysespeichers mit Synapse Spark](#query-analytical-store-spark)
* [Abfragen des Analysespeichers mit einem serverlosen SQL-Pool](#query-analytical-store-sql-on-demand)
* [Verwenden eines serverlosen SQL-Pools zum Analysieren und Visualisieren von Daten in Power BI](#analyze-with-powerbi)

Sie können sich auch das Lernmodul zur [Konfiguration von Azure Synapse Link für Azure Cosmos DB](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/) ansehen.

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Aktivieren von Azure Synapse Link für Azure Cosmos DB-Konten

> [!NOTE]
> Wenn Sie kundenverwaltete Schlüssel mit Azure Synapse Link verwenden möchten, müssen Sie die verwaltete Identität Ihres Kontos in Ihrer Azure Key Vault-Zugriffsrichtlinie konfigurieren, bevor Sie Synapse Link für Ihr Konto aktivieren. Weitere Informationen finden Sie unter [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe verwalteter Identitäten eines Azure Cosmos DB-Kontos](how-to-setup-cmk.md#using-managed-identity).

> [!NOTE]
> Wenn Sie Full Fidelity Schema für SQL (CORE)-API-Konten verwenden möchten, können Sie das Azure-Portal nicht verwenden, um Synapse Link zu aktivieren. Diese Option kann nicht mehr geändert werden, nachdem Synapse Link in Ihrem Konto aktiviert wurde. Sie müssen Azure CLI oder PowerShell verwenden, um sie festzulegen. Weitere Informationen finden Sie in der [Dokumentation zur Schemadarstellung des Analysespeichers](analytical-store-introduction.md#schema-representation). 

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein vorhandenes Azure Cosmos DB-Konto aus.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie den Bereich **Features**.

1. Wählen Sie in der Liste mit den Features die Option **Synapse Link** aus.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Suchen des Features „Synapse Link“":::

1. Als Nächstes werden Sie aufgefordert, Synapse Link für Ihr Konto zu aktivieren. Wählen Sie **Aktivieren** aus. Dieser Vorgang kann 1 bis 5 Minuten in Anspruch nehmen.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Aktivieren des Features „Synapse Link“":::

1. Ihr Konto ist jetzt für die Verwendung von Synapse Link aktiviert. Im nächsten Abschnitt wird beschrieben, wie Sie für Analysespeicher aktivierte Container erstellen, um mit dem automatischen Replizieren Ihrer operativen Daten aus dem Transaktionsspeicher im Analysespeicher zu beginnen.

> [!NOTE]
> Durch das Aktivieren von Synapse Link wird der Analysespeicher nicht automatisch aktiviert. Nachdem Sie Synapse Link für das Cosmos DB-Konto aktiviert haben, aktivieren Sie den Analysespeicher in Containern bei deren Erstellung, um mit dem Replizieren Ihrer operativen Daten in diesen Speicher beginnen zu können. 

### <a name="azure-cli"></a>Azure CLI

Unter folgenden Links wird gezeigt, wie Sie Synapse Link mithilfe der Azure CLI aktivieren:

* [Erstellen eines neuen Azure Cosmos DB-Kontos mit aktiviertem Synapse Link](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [Aktualisieren eines vorhandenen Azure Cosmos DB-Kontos zum Aktivieren von Synapse Link](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

### <a name="powershell"></a>PowerShell

* [Erstellen eines neuen Azure Cosmos DB-Kontos mit aktiviertem Synapse Link](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [Aktualisieren eines vorhandenen Azure Cosmos DB-Kontos zum Aktivieren von Synapse Link](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


Unter folgenden Links wird gezeigt, wie Sie Synapse Link mithilfe von PowerShell aktivieren:

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Erstellen eines Azure Cosmos-Containers mit Analysespeicher

Beim Erstellen des Containers können Sie den Analysespeicher in einem Azure Cosmos-Container aktivieren. Sie können das Azure-Portal verwenden oder die Eigenschaft `analyticalTTL` bei der Erstellung des Containers konfigurieren, indem Sie die Azure Cosmos DB-SDKs nutzen.

> [!NOTE]
> Derzeit können Sie den Analysespeicher für **neue** Container aktivieren (sowohl in neuen als auch in vorhandenen Konten). Daten aus Ihren vorhandenen Containern können Sie mithilfe von [Azure Cosmos DB-Migrationstools](cosmosdb-migrationchoices.md) in neue Container migrieren.

### <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) oder [Azure Cosmos DB-Explorer](https://cosmos.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie die Registerkarte **Daten-Explorer**.

1. Wählen Sie **Neuer Container** aus, und geben Sie einen Namen für Ihre Datenbank, den Container, den Partitionsschlüssel und die Durchsatzdetails ein. Aktivieren Sie die Option **Analysespeicher**. Nach dem Aktivieren des Analysespeichers wird ein Container erstellt, für den die Eigenschaft `AnalyicalTTL` auf den Standardwert „-1“ (unbegrenzte Aufbewahrung) festgelegt ist. In diesem Analysespeicher wird der gesamte Versionsverlauf der Datensätze aufbewahrt.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Aktivieren des Analysespeichers für Azure Cosmos-Container":::

1. Falls Sie Synapse Link für dieses Konto bisher noch nicht aktiviert haben, werden Sie dazu aufgefordert. Der Grund ist, dass die Erstellung eines für Analysespeicher aktivierten Containers zwingend erforderlich ist. Wählen Sie **Enable Synapse Link** (Synapse Link aktivieren) aus, wenn Sie dazu aufgefordert werden. Dieser Vorgang kann 1 bis 5 Minuten in Anspruch nehmen.

1. Wählen Sie **OK** aus, um einen für Analysespeicher aktivierten Azure Cosmos-Container zu erstellen.

1. Vergewissern Sie sich nach dem Erstellen des Containers, dass der Analysespeicher aktiviert wurde. Klicken Sie hierzu auf **Einstellungen** direkt unterhalb von „Dokumente“ im Daten-Explorer, und überprüfen Sie, ob die Option **Gültigkeitsdauer für den Analysespeicher** aktiviert ist.

### <a name="net-sdk"></a>.NET SDK

Mit dem folgenden Code wird ein Container mit Analysespeicher erstellt, indem das .NET SDK verwendet wird. Legen Sie die Eigenschaft für die analytische Gültigkeitsdauer auf den erforderlichen Wert fest. Eine Liste mit den zulässigen Werten finden Sie im Artikel zu den [für die analytische Gültigkeitsdauer unterstützten Werten](analytical-store-introduction.md#analytical-ttl):

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

Mit dem folgenden Code wird ein Container mit Analysespeicher erstellt, indem das Java V4 SDK verwendet wird. Legen Sie die Eigenschaft `AnalyticalStoreTimeToLiveInSeconds` auf den erforderlichen Wert fest:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 und das Azure Cosmos DB SDK 4.1.0 sind die mindestens erforderlichen Versionen. Darüber hinaus ist das SDK nur mit der SQL-API kompatibel.

Der erste Schritt besteht darin, sicherzustellen, dass Sie mindestens Version 4.1.0 des [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) verwenden:

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
Im nächsten Schritt wird ein Container mit Analysespeicher erstellt, indem das Python SDK von Azure Cosmos DB verwendet wird:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="azure-cli"></a>Azure CLI

Unter folgenden Links wird gezeigt, wie Sie mithilfe der Azure CLI für einen Analysespeicher aktivierte Container erstellen:

* [Azure Cosmos DB-API für MongoDB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [SQL-API für Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)

### <a name="powershell"></a>PowerShell

Unter folgenden Links wird gezeigt, wie Sie mithilfe von PowerShell für einen Analysespeicher aktivierte Container erstellen:

* [Azure Cosmos DB-API für MongoDB](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [SQL-API für Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)


## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Optional: Aktualisieren der Gültigkeitsdauer des Analysespeichers

Nachdem der Analysespeicher mit einem bestimmten TTL-Wert aktiviert wurde, sollten Sie ihn später auf einen anderen gültigen Wert aktualisieren. Für die Aktualisierung des Werts können Sie das Azure-Portal, die Azure CLI, PowerShell oder Cosmos DB-SDKs verwenden. Informationen zu den verschiedenen Konfigurationsoptionen für die analytische Gültigkeitsdauer finden Sie im Artikel zu den [für die analytische Gültigkeitsdauer unterstützten Werten](analytical-store-introduction.md#analytical-ttl).


### <a name="azure-portal"></a>Azure-Portal

Wenn Sie einen für Analysespeicher aktivierten Container über das Azure-Portal erstellt haben, weist dieser für die analytische Gültigkeitsdauer den Standardwert „-1“ auf. Verwenden Sie die folgenden Schritte, um diesen Wert zu aktualisieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) oder [Azure Cosmos DB-Explorer](https://cosmos.azure.com/) an.
1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie die Registerkarte **Daten-Explorer**.
1. Wählen Sie einen vorhandenen Container aus, für den Analysespeicher aktiviert ist. Erweitern Sie ihn, und ändern Sie die folgenden Werte:
   1. Öffnen Sie das Fenster **Scale & Settings** (Skalierung und Einstellungen).
   1. Suchen Sie unter **Einstellung** nach **Analytical Storage Time to Live**.
   1. Wählen Sie **Ein (Standard)** oder **Ein** aus, und legen Sie einen Wert für die Gültigkeitsdauer fest.
   1. Klicken Sie zum Speichern der Änderungen auf **Speichern**.


### <a name="net-sdk"></a>.NET SDK

Der folgende Code veranschaulicht, wie Sie die Gültigkeitsdauer für Analysespeicher aktualisieren, indem Sie das .NET SDK verwenden:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

Der folgende Code veranschaulicht, wie Sie die Gültigkeitsdauer für Analysespeicher aktualisieren, indem Sie das Java V4 SDK verwenden:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Wird derzeit nicht unterstützt.


### <a name="azure-cli"></a>Azure CLI

Unter folgenden Links wird gezeigt, wie Sie den Analyse-TTL-Wert von Containern mithilfe der Azure CLI aktualisieren:

* [Azure Cosmos DB-API für MongoDB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [SQL-API für Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

Unter folgenden Links wird gezeigt, wie Sie den Analyse-TTL-Wert von Containern mithilfe von PowerShell aktualisieren:

* [Azure Cosmos DB-API für MongoDB](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [SQL-API für Azure Cosmos DB](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Herstellen einer Verbindung mit einem Synapse-Arbeitsbereich

Befolgen Sie die Anleitung unter [Herstellen einer Verbindung mit Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md), um aus Azure Synapse Analytics Studio mit Azure Synapse Link auf eine Azure Cosmos DB-Datenbank zuzugreifen.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Abfragen des Analysespeichers mithilfe von Apache Spark für Azure Synapse Analytics

Befolgen Sie die Anleitung im Artikel [Abfragen des Azure Cosmos DB-Analysespeichers mithilfe von Spark 3](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md), um Abfragen mit Synapse Spark 3 durchzuführen. Dieser Artikel enthält einige Beispiele dafür, wie Sie mithilfe von Synapse-Gesten mit dem Analysespeicher interagieren können. Diese Gesten werden angezeigt, wenn Sie mit der rechten Maustaste auf einen Container klicken. Mit Gesten können Sie schnell Code generieren und an Ihre Anforderungen anpassen. Sie eignen sich auch ideal zum Ermitteln von Daten mit nur einem Mausklick.

Befolgen Sie für die Spark 2-Integration die Anleitung im Artikel [Abfragen des Azure Cosmos DB-Analysespeichers mithilfe von Spark 2](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md).

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Abfragen des Analysespeichers mit einem serverlosen SQL-Pool in Azure Synapse Analytics

Mit einem serverlosen SQL-Pool können Sie Daten in Ihren Azure Cosmos DB-Containern abfragen und analysieren, die mit Azure Synapse Link aktiviert wurden. Sie können Daten nahezu in Echtzeit analysieren, ohne dass dadurch die Leistung Ihrer Transaktionsworkloads beeinträchtigt wird. SQL (serverlos) bietet eine vertraute T-SQL-Syntax zum Abfragen von Daten aus dem Analysespeicher und – über die T-SQL-Schnittstelle – integrierte Konnektivität mit einer Vielzahl von BI-Tools und Ad-hoc-Abfragetools. Weitere Informationen finden Sie im Artikel [Abfragen von Azure Cosmos DB-Daten mithilfe eines serverlosen SQL-Pools in Azure Synapse Link (Vorschau)](../synapse-analytics/sql/query-cosmos-db-analytical-store.md).

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Verwenden eines serverlosen SQL-Pools zum Analysieren und Visualisieren von Daten in Power BI

Sie können eine serverlose SQL-Pooldatenbank und Sichten über Synapse Link für Azure Cosmos DB erstellen. Später können Sie die Azure Cosmos-Container abfragen und dann ein Modell mit Power BI über diese Sichten erstellen, um die betreffende Abfrage anzuzeigen. Es gibt keine Auswirkungen auf die Leistung oder Kosten Ihrer Transaktionsworkloads, und auch die Verwaltung von ETL-Pipelines bleibt unverändert. Sie können entweder den Modus [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) oder [Import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) verwenden. Weitere Informationen finden Sie im Artikel zum [Verwenden von Power BI und serverlosem Synapse SQL-Pool zum Analysieren von Azure Cosmos DB-Daten mit Synapse Link (Vorschau)](synapse-link-power-bi.md).

## <a name="configure-custom-partitioning"></a>Konfigurieren der benutzerdefinierten Partitionierung

Mit der benutzerdefinierten Partitionierung können Sie Daten des Analysespeichers in Feldern partitionieren, die häufig als Filter in Analyseabfragen verwendet werden, um die Abfrageleistung zu verbessern.Weitere Informationen finden Sie in den Artikeln zur [Einführung in die benutzerdefinierte Partitionierung](custom-partitioning-analytical-store.md) und [Konfigurieren der benutzerdefinierten Partitionierung](configure-custom-partitioning.md).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Mit der [Azure Resource Manager-Vorlage](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) wird für die SQL-API ein Azure Cosmos DB-Konto erstellt, das Synapse Link-fähig ist. Mit dieser Vorlage wird ein Konto für die Core-API (SQL) in einer Region mit einem Container erstellt, für den die analytische Gültigkeitsdauer aktiviert ist, und es ist eine Option vorhanden, mit der die manuelle oder automatische Skalierung des Durchsatzes festgelegt werden kann. Klicken Sie zum Bereitstellen dieser Vorlage auf der Infoseite auf **Bereitstellen in Azure**.

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Erste Schritte mit Azure Synapse Link: Beispiele

Beispiele für die ersten Schritte mit Azure Synapse Link finden Sie auf [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Sie zeigen End-to-End-Lösungen für IoT- und Einzelhandelsszenarien. Sie können die Beispiele zur Azure Cosmos DB-API für MongoDB auch in demselben Repository unter dem Ordner [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) finden. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* Sie können sich auch das Lernmodul zur [Konfiguration von Azure Synapse Link für Azure Cosmos DB](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/) ansehen.

* [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md)

* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)

* [Grundlegende Konzepte für Apache Spark in Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Laufzeitunterstützung für serverlose SQL-Pools in Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
