---
title: Erste Schritte mit der Teilaktualisierung von Dokumenten in Azure Cosmos DB
description: Dieser Artikel enthält Beispiele für die Verwendung einer Teilaktualisierung von Dokumenten mit .NET-, Java- und Node-SDKs.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/25/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ceebe9f537dcf2f496594d6a6c63972fcb7af92
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434102"
---
# <a name="azure-cosmos-db-partial-document-update-getting-started"></a>Teilaktualisierung von Dokumenten in Azure Cosmos DB: Erste Schritte
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel enthält Beispiele für die Verwendung der Teilaktualisierung von Dokumenten mit den SDKs für .NET, Java und Node sowie häufige Fehler, die auftreten können. Codebeispiele für die folgenden Szenarios wurden bereitgestellt:

- Ausführen eines einzelnen Patchvorgangs
- Kombinieren mehrerer Patchvorgänge
- Bedingte Patchsyntax basierend auf dem Filterprädikat
- Ausführen eines Patchvorgangs als Teil einer Transaktion

## <a name="net"></a>.NET

Sie können Version *3.22.1-preview* des Azure Cosmos DB-.NET-SDK v3 aus dem [NuGet-Katalog](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.22.1-preview) herunterladen.

> [!NOTE]
> Ein vollständige Beispiel für eine teilweise Dokumentaktualisierung finden Sie im [Repository mit den .NET v3-Beispielen](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/3fa885fdd84e2f8852d2a1d5c75c56b642b5bba3/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs) auf GitHub.

**Ausführen eines einzelnen Patchvorgangs**

```csharp
ItemResponse<SalesOrder> response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/TotalDue", 0) });

SalesOrder updated = response.Resource;
```

**Kombinieren mehrerer Patchvorgänge**

```csharp
List<PatchOperation> patchOperations = new List<PatchOperation>();
patchOperations.Add(PatchOperation.Add("/nonExistentParent/Child", "bar"));
patchOperations.Add(PatchOperation.Remove("/cost"));
patchOperations.Add(PatchOperation.Increment("/taskNum", 6));
patchOperations.Add(patchOperation.Set("/existingPath/newproperty",value));

container.PatchItemAsync<item>(
                id: 5,
                partitionKey: new PartitionKey("task6"),
                patchOperations: patchOperations );
```

**Bedingte Patchsyntax basierend auf dem Filterprädikat**

```csharp
PatchItemRequestOptions patchItemRequestOptions = new PatchItemRequestOptions
{
    FilterPredicate = "from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))"
};
response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/ShippedDate", DateTime.UtcNow) },
    patchItemRequestOptions);

SalesOrder updated = response.Resource;
```

**Ausführen eines Patchvorgangs als Teil einer Transaktion**


```csharp
List<PatchOperation> patchOperationsUpdateTask = new List<PatchOperation>()
            {
                PatchOperation.Add("/children/1/pk", "patched"),
                PatchOperation.Remove("/description"),
                PatchOperation.Add("/taskNum", 8),
                PatchOperation.Replace("/taskNum", 12)
            };

TransactionalBatchPatchItemRequestOptions requestOptionsFalse = new TransactionalBatchPatchItemRequestOptions()
            {
                FilterPredicate = "from c where c.taskNum = 3"
            };

TransactionalBatchInternal transactionalBatchInternalFalse = (TransactionalBatchInternal)containerInternal.CreateTransactionalBatch(new Cosmos.PartitionKey(testItem.pk));
transactionalBatchInternalFalse.PatchItem(id: testItem1.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.PatchItem(id: testItem2.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.ExecuteAsync());
```

## <a name="java"></a>Java

Die Patch-API befindet sich derzeit in der Vorschau von Version 4.20.0 des Java-SDK. Bei allgemeiner Verfügbarkeit wird sie in Version 4.21.0 verfügbar sein. Um Patchvorgänge verwenden zu können, fügen Sie Version *4.20.0* des [Azure Cosmos DB-Java-SDK v4](sql-api-sdk-java-v4.md) der Liste der Abhängigkeiten in Ihrer Datei `pom.xml` hinzu oder laden es direkt aus [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) herunter.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.20.0</version>
</dependency>
```

> [!NOTE]
> Das vollständige Beispiel finden Sie im [Repository mit den Java SDK v4-Beispielen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/) auf GitHub.

**Ausführen eines einzelnen Patchvorgangs**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true);

CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Kombinieren mehrerer Patchvorgänge**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true)
                     .replace("/parents/0/familyName", "Doe");
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Bedingte Patchsyntax basierend auf dem Filterprädikat**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
                                                                   .add("/vaccinated", true);
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();
options.setFilterPredicate("from f where f.registered = true");

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**Ausführen eines Patchvorgangs als Teil einer Transaktion**

```java
CosmosBatch batch = CosmosBatch.createCosmosBatch(new PartitionKey(family.getLastName()));
batch.createItemOperation(family);

CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create().add("/registered", false);
batch.patchItemOperation(family.getId(), cosmosPatchOperations);

CosmosBatchResponse response = container.executeCosmosBatch(batch);
if (response.isSuccessStatusCode()) {
    // if transactional batch succeeds   
}
```

## <a name="node"></a>Node

**Ausführen eines einzelnen Patchvorgangs**

```javascript
const patchSource = itemDefList[1];

const replaceOperation: PatchOperation[] = 
    [{ 
      op: "replace", 
      path: "/lastName", 
      value: "Martin" 
    }];

const { resource: patchSource1 } = await container.item(patchSource.lastName).patch(replaceOperation); 
console.log(`Patched ${patchSource1.lastName} to new ${patchSource1.lastName}.`); 
```

**Kombinieren mehrerer Patchvorgänge**

```javascript
const multipleOperations: PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/aka", 
      value: "MeFamily" 
    }, 
    { 
      op: "add", 
      path: "/years", 
      value: 12 
    }, 
    { 
      op: "replace", 
      path: "/lastName", 
      value: "Jose" 
    }, 
    { 
      op: "remove", 
      path: "/parents" 
    }, 
    { 
      op: "set", 
      path: "/children/firstName", 
      value: "Anderson" 
    }, 
    { 
      op: "incr", 
      path: "/years", 
      value: 5 
    } 
  ]; 

const { resource: patchSource2  } = await container.item(patchSource.id).patch(multipleOperations); 
 ```

**Bedingte Patchsyntax basierend auf dem Filterprädikat**

```javascript
const operations : PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/newImproved", 
      value: "it works" 
    } 
  ]; 

const condition = "from c where NOT IS_DEFINED(c.newImproved)"; 

const { resource: patchSource3 } = await container 
    .item(patchSource.id) 
    .patch({ condition, operations }); 

console.log(`Patched ${patchSource3} to new ${patchSource3}.`); 
```

## <a name="support-for-server-side-programming"></a>Unterstützung für die serverseitige Programmierung

Vorgänge für die Teilaktualisierung von Dokumenten können auch [serverseitig](stored-procedures-triggers-udfs.md) mithilfe von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen ausgeführt werden.


```javascript
        this.patchDocument = function (documentLink, patchSpec, options, callback) { 
                if (arguments.length < 2) { 
                    throw new Error(ErrorCodes.BadRequest, sprintf(errorMessages.invalidFunctionCall, 'patchDocument', 2, arguments.length)); 
                }
                if (patchSpec === null || !(typeof patchSpec === "object" || Array.isArray(patchSpec))) { 
                    throw new Error(ErrorCodes.BadRequest, errorMessages.patchSpecMustBeObjectOrArray); 
                } 

                var documentIdTuple = validateDocumentLink(documentLink, false); 
                var collectionRid = documentIdTuple.collId; 
                var documentResourceIdentifier = documentIdTuple.docId; 
                var isNameRouted = documentIdTuple.isNameRouted; 

                patchSpec = JSON.stringify(patchSpec); 
                var optionsCallbackTuple = validateOptionsAndCallback(options, callback); 

                options = optionsCallbackTuple.options; 
                callback = optionsCallbackTuple.callback; 

                var etag = options.etag || ''; 
                var indexAction = options.indexAction || ''; 

                return collectionObjRaw.patch( 
                    collectionRid, 
                    documentResourceIdentifier, 
                    isNameRouted, 
                    patchSpec, 
                    etag, 
                    indexAction, 
                    function (err, response) { 
                        if (callback) { 
                            if (err) { 
                                callback(err); 
                            } else { 
                                callback(undefined, JSON.parse(response.body), response.options); 
                            } 
                        } else { 
                            if (err) { 
                                throw err; 
                            } 
                        } 
                    } 
                ); 
            }; 
```

## <a name="troubleshooting"></a>Problembehandlung

Im Folgenden finden Sie eine Liste der häufigen Fehler, die bei der Verwendung dieses Features auftreten können:

| **Fehlermeldung** | **Beschreibung** |
| ------------ | -------- |
| Ungültige Patchanforderung: Überprüfen Sie die Syntax der Patchspezifikation.| Die Syntax des Patchvorgangs ist ungültig. Überprüfen Sie die [Spezifikation](partial-document-update.md#partial-document-update-specification).
| Ungültige Patchanforderung: Die Systemeigenschaft `SYSTEM_PROPERTY` kann nicht gepatcht werden. | Das Patchen von systemgenerierten Eigenschaften wie  `_id`,  `_ts`,  `_etag` und  `_rid` wird nicht unterstützt. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Teilaktualisierung von Dokumenten](partial-document-update-faq.yml#is-partial-document-update-supported-for-system-generated-properties-). 
| Die Anzahl der Patchvorgänge darf 10 nicht überschreiten. | Es gibt ein Limit von 10 Patchvorgängen, die in einer einzelnen Patchspezifikation hinzugefügt werden können. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Teilaktualisierung von Dokumenten](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-).
| Für Operation(`PATCH_OPERATION_INDEX`): Index(`ARRAY_INDEX`), für den der Vorgang ausgeführt werden soll, liegt außerhalb der Arraygrenzen. | Der Index des Arrayelements, das gepatcht werden soll, liegt außerhalb des gültigen Bereichs. 
| Für Operation(`PATCH_OPERATION_INDEX`): Der zu ersetzende Node(`PATH`) wurde zuvor in der Transaktion entfernt.| Der Pfad, den Sie patchen möchten, ist nicht vorhanden.
| Für Operation(`PATCH_OPERATION_INDEX`): Der zu entfernende Node(`PATH`) ist nicht vorhanden. Hinweis: Möglicherweise wurde der Knoten ebenfalls früher in der Transaktion entfernt.  | Der Pfad, den Sie patchen möchten, ist nicht vorhanden.
| Für Operation(`PATCH_OPERATION_INDEX`): Der zu entfernende Node(`PATH`) ist nicht vorhanden. | Der Pfad, den Sie patchen möchten, ist nicht vorhanden.
| Für Operation(`PATCH_OPERATION_INDEX`): Node(`PATH`) ist nicht numerisch.| Der Inkrementvorgang kann nur für ganze Zahlen und Gleitkommazahlen verwendet werden. Weitere Informationen finden Sie unter [Unterstützte Vorgänge](partial-document-update.md#supported-operations).
| Für Operation(`PATCH_OPERATION_INDEX`): Durch das Hinzufügen eines Vorgangs kann nur ein untergeordnetes Objekt eines vorhandenen Knotens (Array oder Objekt) erstellt werden, und das rekursive Erstellen eines Pfads ist nicht möglich. Es wurde kein Pfad nach `PATH` gefunden. | Untergeordnete Pfade können einem Objekt- oder Arrayknotentyp hinzugefügt werden. Außerdem sollte zum Erstellen des untergeordneten Elements `n` das untergeordnete Element `n-1` vorhanden sein. 
| Für Operation(`PATCH_OPERATION_INDEX`): Mit einem angegebenen Vorgang kann nur ein untergeordnetes Objekt eines vorhandenen Knotens (Array oder Objekt) erstellt werden, und das rekursive Erstellen eines Pfads ist nicht möglich. Es wurde kein Pfad nach `PATH` gefunden. | Untergeordnete Pfade können einem Objekt- oder Arrayknotentyp hinzugefügt werden. Außerdem sollte zum Erstellen des untergeordneten Elements `n` das untergeordnete Element `n-1` vorhanden sein. 

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der konzeptionellen Übersicht über die [Teilaktualisierung von Dokumenten](partial-document-update.md).
