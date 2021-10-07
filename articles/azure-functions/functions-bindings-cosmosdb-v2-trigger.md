---
title: Azure Cosmos DB-Trigger für Functions 2.x und höher
description: Erfahren Sie, wie Sie den Azure Cosmos DB-Trigger in Azure Functions verwenden.
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 0bccf556f48cea52c4458ec6afc882c3c6035a71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635183"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB-Trigger für Azure Functions 2.x und höher

Informationen zum partitionsübergreifenden Lauschen auf Einfügungen und Aktualisierungen durch den Azure Cosmos DB-Trigger finden Sie unter [Verwenden der Unterstützung von Änderungsfeeds in Azure Cosmos DB](../cosmos-db/change-feed.md). Der Änderungsfeed veröffentlicht Einfügungen und Updates, keine Löschungen.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die aufgerufen wird, wenn etwas in der angegebenen Datenbank und Sammlung eingefügt oder aktualisiert wird.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

Apps, die die Cosmos DB-[Erweiterung Version 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) oder höher verwenden, verfügen über unterschiedliche Attributeigenschaften, die unten dargestellt werden. Dieses Beispiel bezieht sich auf einen einfachen `ToDoItem`-Typ.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

```cs
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "databaseName",
            containerName: "containerName",
            Connection = "CosmosDBConnectionSetting",
            LeaseContainerName = "leases",
            CreateLeaseContainerIfNotExists = true)]IReadOnlyList<ToDoItem> input, ILogger log)
        {
            if (input != null && input.Count > 0)
            {
                log.LogInformation("Documents modified " + input.Count);
                log.LogInformation("First document Id " + input[0].Id);
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Cosmos DB-Triggerbindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion schreibt Protokollmeldungen, wenn Cosmos DB-Datensätze geändert oder hinzugefügt werden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Der C#-Skriptcode sieht wie folgt aus:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

Diese Funktion wird aufgerufen, wenn etwas in der angegebenen Datenbank und Sammlung eingefügt oder aktualisiert wird.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Verwenden Sie die `@CosmosDBTrigger`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert von Cosmos DB empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Cosmos DB-Triggerbindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion schreibt Protokollmeldungen, wenn Cosmos DB-Datensätze geändert oder hinzugefügt werden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende Beispiel zeigt, wie eine Funktion bei Datenänderungen in Cosmos DB ausgeführt wird.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

In der Datei _run.ps1_ können Sie über den Parameter `$Documents` auf das Dokument zugreifen, das die Funktion auslöst.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt eine Cosmos DB-Triggerbindung in einer Datei namens *function.json* sowie eine [Python-Funktion](functions-reference-python.md), die die Bindung verwendet. Die Funktion schreibt Protokollmeldungen, wenn Cosmos DB-Datensätze geändert werden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dies ist der Python-Code:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie das [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)-Attribut.

Der Attributkonstruktor akzeptiert den Datenbanknamen und den Sammlungsnamen. Weitere Informationen zu diesen Einstellungen und anderen Eigenschaften, die Sie konfigurieren können, finden Sie unter [Trigger: Konfiguration](#configuration). Hier ist ein Beispiel für ein `CosmosDBTrigger`-Attribut in einer Methodensignatur:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

In der [Erweiterungsversion 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wurden einige Einstellungen und Eigenschaften entfernt oder umbenannt. Ausführliche Informationen zu den Änderungen finden Sie unter [Trigger: Konfiguration](#configuration). Hier sehen Sie ein Beispiel für ein `CosmosDBTrigger`-Attribut in einer Methodensignatur, die auf einen einfachen `ToDoItem`-Typ verweist:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "container", Connection = "CosmosDBConnectionSetting")]
        IReadOnlyList<ToDoItem> documents,  
        ILogger log)
    {
        ...
    }
```

Ein vollständiges Beispiel für beide Erweiterungsversionen finden Sie unter [Trigger](#example).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie die `@CosmosDBInput`-Anmerkung in der [Runtimebibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, die Daten aus Cosmos DB lesen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `CosmosDBTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf `cosmosDBTrigger` festgelegt sein. |
|**direction** | – | Muss auf `in` festgelegt sein. Dieser Parameter wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der im Code der Funktion verwendete Variablenname, der die Liste der Dokumente mit Änderungen darstellt. |
|**connectionStringSetting** <br> oder <br> **connection**|**ConnectionStringSetting** <br> oder <br> **Connection**| Der Name einer App-Einstellung, die die Verbindungszeichenfolge enthält, die zum Herstellen der Verbindung mit dem überwachten Azure Cosmos DB-Konto verwendet wird. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `Connection` bezeichnet. Der Wert ist der Name einer App-Einstellung, die entweder die Verbindungszeichenfolge enthält, die zum Herstellen einer Verbindung mit dem überwachten Azure Cosmos DB-Konto verwendet wird, oder einen Konfigurationsabschnitt oder ein Präfix, der bzw. das die Verbindung definiert. Siehe [Verbindungen](./functions-reference.md#connections). |
|**databaseName**|**DatabaseName**  | Der Name der Azure Cosmos DB-Datenbank mit der überwachten Sammlung. |
|**collectionName** <br> oder <br> **containerName** |**CollectionName** <br> oder <br> **ContainerName** | Der Name der überwachten Sammlung. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `ContainerName` bezeichnet. |
|**leaseConnectionStringSetting** <br> oder <br> **leaseConnection** | **LeaseConnectionStringSetting** <br> oder <br> **LeaseConnection** | (Optional:) Der Name einer App-Einstellung, die die Verbindungszeichenfolge für das Azure Cosmos DB-Konto enthält, in dem die Leasesammlung enthalten ist. Wenn nicht festgelegt, wird der Wert `connectionStringSetting` verwendet. Dieser Parameter wird automatisch festgelegt, wenn die Bindung im Portal erstellt wird. Die Verbindungszeichenfolge für die Leasesammlung muss über Schreibberechtigungen verfügen. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `LeaseConnection` bezeichnet, und wenn sie nicht festgelegt ist, wird der `Connection`-Wert verwendet. Der Wert ist der Name einer App-Einstellung, die entweder die Verbindungszeichenfolge enthält, die zum Herstellen einer Verbindung mit dem Azure Cosmos DB-Konto mit dem Leasecontainer verwendet wird, oder einen Konfigurationsabschnitt oder ein Präfix, der bzw. das die Verbindung definiert. Siehe [Verbindungen](./functions-reference.md#connections).|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Optional) Der Name der Datenbank, in der die Sammlung zum Speichern von Leases enthalten ist. Wenn nicht festgelegt, wird der Wert der `databaseName`-Einstellung verwendet. Dieser Parameter wird automatisch festgelegt, wenn die Bindung im Portal erstellt wird. |
|**leaseCollectionName** <br> oder <br> **leaseContainerName** | **LeaseCollectionName** <br> oder <br> **LeaseContainerName** | (Optional) Der Name der Sammlung, die zum Speichern von Leases verwendet wird. Wenn nicht festgelegt, wird der Wert `leases` verwendet. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `LeaseContainerName` bezeichnet. |
|**createLeaseCollectionIfNotExists** <br> oder <br> **createLeaseContainerIfNotExists** | **CreateLeaseCollectionIfNotExists** <br> oder <br> **CreateLeaseContainerIfNotExists** | (Optional) Bei Festlegung auf `true` wird die Sammlung von Leases automatisch erstellt, wenn sie nicht bereits vorhanden ist. Standardwert: `false`. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `CreateLeaseContainerIfNotExists` bezeichnet. |
|**leasesCollectionThroughput** <br> oder <br> **leasesContainerThroughput**| **LeasesCollectionThroughput** <br> oder <br> **LeasesContainerThroughput**| (Optional:) Definiert die Anzahl von Anforderungseinheiten, die zugewiesen werden, wenn die Leasesammlung erstellt wird. Diese Einstellung wird nur verwendet, wenn `createLeaseCollectionIfNotExists` auf `true` festgelegt ist. Dieser Parameter wird automatisch festgelegt, wenn die Bindung im Portal erstellt wird. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `LeasesContainerThroughput` bezeichnet. |
|**leaseCollectionPrefix** <br> oder <br> **leaseContainerPrefix**| **LeaseCollectionPrefix** <br> oder <br> **leaseContainerPrefix** | (Optional:) Bei einer Festlegung wird der Wert den in der Leasesammlung für diese Funktion erstellten Leases als Präfix hinzugefügt. Die Verwendung eines Präfix ermöglicht die Nutzung derselben Leasesammlung durch zwei separate Azure-Funktionen über unterschiedliche Präfixe. <br><br> In [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) wird diese Eigenschaft als `LeaseContainerPrefix` bezeichnet. |
|**feedPollDelay**| **FeedPollDelay**| (Optional:) die Verzögerung (in Millisekunden) zwischen den Abfragen an eine Partition nach neuen Änderungen im Feed, nachdem alle aktuellen Änderungen beseitigt wurden. Der Standardwert beträgt 5.000 Millisekunden (oder fünf Sekunden).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Optional) Wenn gesetzt, wird das Intervall in Millisekunden definiert, das eine Aufgabe anstößt, die berechnet, ob Partitionen unter den bekannten Hostinstanzen gleichmäßig verteilt sind. Der Standardwert ist 13000 (13 Sekunden).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Optional) Wenn gesetzt, wird das Intervall in Millisekunden definiert, für das die Lease für eine Lease, die eine Partition darstellt, ausgeführt wird. Wenn die Lease innerhalb dieses Intervalls nicht erneuert wird, läuft sie ab, und der Besitz der Partition wechselt zu einer anderen Instanz. Der Standardwert ist 60000 (60 Sekunden).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Optional) Wenn gesetzt, wird das Erneuerungsintervall in Millisekunden für alle Leases für Partitionen definiert, die aktuell in einer Instanz vorhanden sind. Der Standardwert ist 17000 (17 Sekunden).
|**checkpointInterval**| **CheckpointInterval**| (Optional) Wenn gesetzt, wird das Intervall in Millisekunden zwischen Leaseprüfpunkten definiert. Dies ist standardmäßig immer nach einem erfolgreichen Funktionsaufruf der Fall. <br><br> Diese Eigenschaft ist in [Version 4.x der Erweiterung](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) nicht verfügbar. |
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Optional:) Diese Eigenschaft legt die Höchstzahl von Elementen fest, die pro Funktionsaufruf empfangen werden können. Wenn Vorgänge in der überwachten Sammlung über gespeicherte Prozeduren ausgeführt werden, wird der [Transaktionsbereich](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) beim Lesen von Elementen aus dem Änderungsfeed beibehalten. Dadurch kann die Anzahl der empfangenen Elemente höher als der angegebene Wert sein, sodass die von derselben Transaktion geänderten Elemente als Teil eines atomischen Batches zurückgegeben werden.
|**startFromBeginning**| **StartFromBeginning**| (Optional:) Diese Option weist den Trigger an, Änderungen beginnend vom Anfang des Änderungsverlaufs der Sammlung anstatt ab der aktuellen Zeit zu lesen. Das Lesen von Anfang an funktioniert nur beim ersten Start des Triggers. Bei nachfolgenden Ausführungen sind die Prüfpunkte bereits gespeichert. Wenn die Leases bereits erstellt wurden, hat das Festlegen dieser Option auf `true` keine Auswirkungen. |
|**preferredLocations**| **PreferredLocations**| (Optional) Definiert bevorzugte Standorte (Regionen) für georeplizierte Datenbankkonten im Azure Cosmos DB-Dienst. Werte sollten durch Trennzeichen getrennt sein. Beispiel: „USA, Osten,USA, Süden-Mitte,Europa, Norden“. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Verwendung

Der Trigger erfordert eine zweite Sammlung, die er zum Speichern von _Leases_ auf den Partitionen verwendet. Sowohl die überwachte Sammlung als auch die, die die Leases enthält, muss verfügbar sein, damit der Trigger funktioniert.

>[!IMPORTANT]
> Falls mehrere Funktionen für die Verwendung eines Cosmos DB-Triggers für die gleiche Sammlung konfiguriert sind, muss jede dieser Funktionen eine dedizierte Leasesammlung verwenden oder für jede Funktion ein anderes `LeaseCollectionPrefix` angeben. Andernfalls wird nur eine der Funktionen ausgelöst. Weitere Informationen zu diesem Präfix finden Sie im [Konfigurationsabschnitt](#configuration).

Der Trigger gibt nicht an, ob ein Dokument aktualisiert oder eingefügt wurde, er stellt das Dokument lediglich bereit. Wenn Sie Aktualisierungen und Einfügungen unterschiedlich verarbeiten müssen, können Sie dazu Zeitstempelfelder für Einfügung oder Aktualisierung implementieren.

## <a name="next-steps"></a>Nächste Schritte

- [Lesen eines Azure Cosmos DB-Dokuments (Eingabebindung)](./functions-bindings-cosmosdb-v2-input.md)
- [Speichern der Änderungen an einem Azure Cosmos DB-Dokument (Ausgabebindung)](./functions-bindings-cosmosdb-v2-output.md)
