---
title: Migrieren einer Anwendung für die Verwendung des Azure Cosmos DB Java SDK V4 (com.azure.cosmos)
description: Hier erfahren Sie, wie Sie Ihre vorhandene Java-Anwendung von der Verwendung der älteren Azure Cosmos DB Java SDKs auf das neuere Java SDK 4.0 (com.azure.cosmos-Paket) für die Core-API (SQL) aktualisieren.
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/15/2021
ms.reviewer: sngun
ms.openlocfilehash: 4e08834237d396ae1a2ae3990b3e8d21f75ffaca
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290491"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrieren einer Anwendung für die Verwendung des Azure Cosmos DB Java SDK V4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Weitere Informationen zu diesem SDK finden Sie in den [Versionshinweisen](sql-api-sdk-java-v4.md) zu Azure Cosmos DB Java SDK V4, im [Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-cosmos), in den [Tipps zur Leistungssteigerung](performance-tips-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK V4 und im [Leitfaden zur Problembehandlung](troubleshoot-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK V4.
>

> [!IMPORTANT]  
> Da Azure Cosmos DB Java SDK v4 über einen um bis zu 20 % höheren Durchsatz, einen TCP-basierten direkten Modus und Unterstützung für die neuesten Back-End-Dienstfeatures verfügt, empfehlen wir Ihnen, bei der nächsten Gelegenheit ein Upgrade auf v4 durchzuführen. Lesen Sie weiter, um mehr zu erfahren.
>

Aktualisieren Sie auf das neueste Azure Cosmos DB Java SDK, um das Beste zu nutzen, was Azure Cosmos DB zu bieten hat – einen verwalteten, nicht relationalen Datenbankdienst mit wettbewerbsfähiger Leistung, einer Verfügbarkeit von fünf Neunen, einzigartiger Ressourcenkontrolle und mehr. In diesem Artikel wird erläutert, wie Sie Ihre vorhandene Java-Anwendung von der Verwendung der älteren Azure Cosmos DB Java SDKs auf das neuere Azure Cosmos DB Java SDK 4.0 für die Core-API (SQL) aktualisieren. Azure Cosmos DB Java SDK V4 entspricht dem Paket `com.azure.cosmos`. Sie können die Anweisungen in diesem Dokument verwenden, wenn Sie Ihre Anwendung von einer der folgenden Azure Cosmos DB Java SDKs migrieren: 

* Sync Java SDK 2.x.x
* Async Java SDK 2.x.x
* Java SDK 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Zuordnungen von Azure Cosmos DB Java SDK und Paket

In der folgenden Tabelle werden verschiedene Azure Cosmos DB Java SDKs, der Paketname und die Veröffentlichungsinformationen aufgeführt:

| Java-SDK| Veröffentlichungsdatum | Gebündelte APIs   | Maven-JAR  | Java-Paketname  |API-Referenz   | Versionsinformationen  | Datum der Außerbetriebnahme |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|--------|
| Async 2.x.x  | Juni 2018    | Async(RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Versionsanmerkungen](sql-api-sdk-async-java.md) | - |
| Sync 2.x.x     | September 2018    | Synchronisierung   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Versionsanmerkungen](sql-api-sdk-java.md)  | 29. Februar 2024 |
| 3.x.x    | Juli 2019    | Async(Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - | - |
| 4,0   | Juni 2020   | Async(Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | [API](/java/api/overview/azure/cosmosdb) | - | - |

## <a name="sdk-level-implementation-changes"></a>Änderungen an der Implementierung auf SDK-Ebene

Im Folgenden finden Sie die wichtigsten Unterschiede bei der Implementierung zwischen verschiedenen SDKs:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava wurde in Azure Cosmos DB Java SDK-Versionen 3.x.x und 4.0 durch Reactor ersetzt

Wenn Sie mit der asynchronen oder reaktiven Programmierung nicht vertraut sind, finden Sie im [Einführungsleitfaden zu Reactor-Mustern](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) eine Einführung in die asynchrone Programmierung und Projekt Reactor. Dieser Leitfaden ist möglicherweise hilfreich, wenn Sie in der Vergangenheit Azure Cosmos DB Sync Java SDK 2.x.x oder Azure Cosmos DB Java SDK 3.x.x Sync API verwendet haben.

Wenn Sie Azure Cosmos DB Async Java SDK 2.x.x verwendet haben und eine Migration zum 4.0 SDK beabsichtigen, finden Sie in der [Gegenüberstellung von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) Empfehlungen für die Konvertierung von RxJava-Code zur Verwendung von Reactor.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK V4 verfügt in asynchronen und synchronen APIs über einen Modus für die direkte Konnektivität

Wenn Sie Azure Cosmos DB Sync Java SDK 2.x.x, ist der Modus für die direkte Konnektivität auf Basis von TCP (im Gegensatz zu HTTP) in Azure Cosmos DB Java SDK 4.0 für asynchrone und synchrone APIs implementiert.

## <a name="api-level-changes"></a>Änderungen auf API-Ebene

Im Folgenden sind die Änderungen auf API-Ebene in Azure Cosmos DB Java SDK 4.x.x im Vergleich zu früheren SDKs (Java SDK 3.x.x, Async Java SDK 2.x.x, und Sync Java SDK 2.x.x) aufgeführt:

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Namenskonventionen des Azure Cosmos DB Java SDK":::

* Im Azure Cosmos DB Java SDK 3.x.x und 4.0 werden die Clientressourcen als `Cosmos<resourceName>` bezeichnet. Beispiel: `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. In Version 2.x.x verfügen die Azure Cosmos DB Java SDKs jedoch nicht über ein einheitliches Benennungsschema.

* Azure Cosmos DB Java SDK 3.x.x und 4.0 bieten synchrone und asynchrone APIs.

  * **Java SDK 4.0** : Alle Klassen gehören zur synchronen API, sofern dem Klassennamen kein `Async` nach `Cosmos` nachgestellt ist.

  * **Java SDK 3.x.x**: Alle Klassen gehören zur asynchronen API, sofern dem Klassennamen kein `Async` nach `Cosmos` nachgestellt ist.

  * **Async Java SDK 2.x.x**: Die Klassennamen ähneln dem Sync Java SDK 2.x.x, der Name beginnt jedoch mit *Async*.

### <a name="hierarchical-api-structure"></a>Hierarchische API-Struktur

Azure Cosmos DB Java SDK 4.0 und 3.x.x enthalten erstmals eine hierarchische API-Struktur, in der Clients, Datenbanken und Container verschachtelt angeordnet sind, wie der folgende Codeausschnitt für das 4.0-SDK veranschaulicht:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

In Version 2.x.x des Azure Cosmos DB Java SDK werden alle Vorgänge für Ressourcen und Dokumente über die Clientinstanz durchgeführt.

### <a name="representing-documents"></a>Darstellen von Dokumenten

Im Azure Cosmos DB Java SDK 4.0 stellen benutzerdefinierte POJOs und `JsonNodes` die beiden Optionen zum Lesen und Schreiben der Dokumente aus Azure Cosmos DB dar.

Im Azure Cosmos DB Java SDK 3.x.x wird das `CosmosItemProperties`-Objekt von der öffentlichen API verfügbar gemacht und als Dokumentdarstellung bereitgestellt. Diese Klasse wird in Version 4.0 nicht mehr öffentlich verfügbar gemacht.

### <a name="imports"></a>Importe

* Die Pakete des Azure Cosmos DB Java SDK 4.0 beginnen mit `com.azure.cosmos`.
* Pakete des Azure Cosmos DB Java SDK 3.x.x beginnen mit `com.azure.data.cosmos`.
* Pakete der Sync-API des Azure Cosmos DB Java SDK 2.x.x beginnen mit `com.microsoft.azure.documentdb`.

* Azure Cosmos DB Java SDK 4.0 platziert mehrere Klassen in das verschachtelte Paket `com.azure.cosmos.models`. Einige dieser Pakete lauten wie folgt:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Die Async API enthält Entsprechungen für alle oben aufgeführten Pakete.
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` usw.

### <a name="accessors"></a>Accessoren

Azure Cosmos DB Java SDK 4.0 macht `get`- und `set`-Methoden für den Zugriff auf Instanzelemente verfügbar. Beispielsweise verfügt die `CosmosContainer`-Instanz über die Methoden `container.getId()` und `container.setId()`.

Damit unterscheidet es sich vom Azure Cosmos DB Java SDK 3.x.x, das eine Fluent-Schnittstelle verfügbar macht. Beispielsweise verfügt eine `CosmosSyncContainer`-Instanz über `container.id()`, die zum Abrufen und Festlegen des `id`-Werts überladen wird.

## <a name="code-snippet-comparisons"></a>Vergleich von Codeausschnitten

### <a name="create-resources"></a>Erstellen von Ressourcen

Der folgende Codeausschnitt zeigt die Unterschiede bei der Erstellung von Ressourcen zwischen den Async APIs 4.0 und 3.x.x, der 2.x.x Sync API und der 2.x.x Async API:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
// Create Async client.
// Building an async client is still a sync operation.
AsyncDocumentClient client = new Builder()
    .withServiceEndpoint("your.hostname")
    .withMasterKeyOrResourceToken("yourmasterkey")
    .withConsistencyLevel(ConsistencyLevel.Eventual)
    .build();
// Create database with specified name
Database database = new Database();
database.setId("YourDatabaseName");
client.createDatabase(database, new RequestOptions())
      .flatMap(databaseResponse -> {
          // Collection properties - name and partition key
          DocumentCollection documentCollection = new DocumentCollection();
          documentCollection.setId("YourContainerName");
          documentCollection.setPartitionKey(new PartitionKeyDefinition("/id"));
          // Create collection
          return client.createCollection(databaseResponse.getResource().getSelfLink(), documentCollection, new RequestOptions());
}).subscribe();

```

---

### <a name="item-operations"></a>Elementvorgänge

Der folgende Codeausschnitt zeigt die Unterschiede bei der Ausführung von Elementvorgängen zwischen den Async APIs 4.0 und 3.x.x, der 2.x.x Sync API und der 2.x.x Async API:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
// Collection is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<Document> docs = generateManyDocs(number_of_docs);
// Insert many docs into collection...
Observable.from(docs)
    .flatMap(doc -> client.createDocument(createdCollection.getSelfLink(), doc, new RequestOptions(), false))
    .subscribe(); // ...Subscribing triggers stream execution.
```

---

### <a name="indexing"></a>Indizierung

Der folgende Codeausschnitt zeigt die Unterschiede bei der Indizierungserstellung zwischen den Async APIs 4.0 und 3.x.x, der 2.x.x Sync API und der 2.x.x Async API:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.None
// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).subscribe();
```

---

### <a name="stored-procedures"></a>Gespeicherte Prozeduren

Der folgende Codeausschnitt zeigt die Unterschiede bei der Erstellung gespeicherter Prozeduren zwischen den Async APIs 4.0 und 3.x.x, der 2.x.x Sync API und der 2.x.x Async API:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
logger.info("Creating stored procedure...\n");
String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client
    .createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
    .toBlocking()
    .single()
    .getResource();
// ...
logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));
StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null)
    .toBlocking().single();
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```

---

### <a name="change-feed"></a>Änderungsfeed

Der folgende Codeausschnitt zeigt die Unterschiede bei der Ausführung von Änderungsfeedvorgängen zwischen den Async APIs 4.0 und 3.x.x:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

* Diese Funktion wird ab der Sync-API des Java SDK v2 nicht mehr unterstützt. 

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

* Diese Funktion wird ab der Async-API des Java SDK v2 nicht mehr unterstützt. 

---

### <a name="container-level-time-to-livettl"></a>Gültigkeitsdauer (TTL) auf Containerebene

Der folgende Codeausschnitt zeigt die Unterschiede beim Erstellen einer Gültigkeitsdauer für Daten im Container zwischen den Async APIs 4.0 und 3.x.x, der 2.x.x Sync API und der 2.x.x Async API:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
DocumentCollection collection = new DocumentCollection();
// Create a new container with TTL enabled with default expiration value
collection.setDefaultTimeToLive(90 * 60 * 60 * 24);
collection = client
    .createCollection(database.getSelfLink(), documentCollection, new RequestOptions())
    .toBlocking()
    .single()
    .getResource();
```

---

### <a name="item-level-time-to-livettl"></a>Gültigkeitsdauer (TTL) auf Elementebene

Der folgende Codeausschnitt zeigt die Unterschiede beim Erstellen einer Gültigkeitsdauer für ein Element zwischen den Async APIs 4.0 und 3.x.x, der 2.x.x Sync API und der 2.x.x Async API:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync-API](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true).toBlocking().single();
Document responseDocument = documentResourceResponse.getResource();
```

---

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Java-App](create-sql-api-java.md) zum Verwalten von Azure Cosmos DB-SQL-API-Daten mit dem V4-SDK
* Weitere Informationen über [Reactor-basierte Java-SDKs](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)
* Weitere Informationen über die Konvertierung von asynchronem RxJava-Code in asynchronen Reactor-Code mit der [Gegenüberstellung von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)