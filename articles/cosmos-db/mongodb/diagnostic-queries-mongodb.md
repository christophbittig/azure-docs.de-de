---
title: Behandeln von Problemen mit erweiterten Diagnoseabfragen für die Mongo-API
titleSuffix: Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Diagnoseprotokolle zur Problembehandlung bei Daten, die in Azure Cosmos DB gespeichert sind, für die MongoDB-API abfragen.
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
author: StefArroyo
ms.openlocfilehash: 73dba970aea4171a99ff499300d15c63c3bbb380
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430589"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-mongodb-api"></a>Behandeln von Problemen bei erweiterten Diagnoseabfragen für die MongoDB-API

[!INCLUDE[appliesto-all-apis-except-table](../includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL-API (Core-API)](../cosmos-db-advanced-queries.md)
> * [MongoDB-API](diagnostic-queries-mongodb.md)
> * [Cassandra-API](../cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin-API](../queries-gremlin.md)
>

In diesem Artikel wird erläutert, wie Sie komplexere Abfragen schreiben, um mithilfe von Diagnoseprotokollen, die an **Azure-Diagnose-Tabellen (Legacy)** und **ressourcenspezifische Tabellen (Vorschau)** gesendet werden, Probleme bei Ihrem Azure Cosmos DB-Konto zu beheben.

Bei Azure-Diagnose-Tabellen werden alle Daten in eine einzige Tabelle geschrieben. Benutzer geben an, welche Kategorie sie abfragen möchten. Wenn Sie die Volltextabfrage Ihrer Anforderung anzeigen möchten, lesen Sie den Artikel [Überwachen von Azure Cosmos DB-Daten mithilfe von Diagnoseeinstellungen in Azure](../cosmosdb-monitor-resource-logs.md#full-text-query). Darin erfahren Sie, wie Sie dieses Feature aktivieren können.

Bei [ressourcenspezifischen Tabellen](../cosmosdb-monitor-resource-logs.md#create-setting-portal) werden Daten in einzelne Tabellen für die jeweilige Kategorie der Ressource geschrieben. Wir empfehlen diesen Modus aus folgenden Gründen:

- Er vereinfacht die Arbeit mit den Daten erheblich. 
- Er ermöglicht eine bessere Erkennbarkeit der Schemas.
- Er verbessert die Leistung sowohl im Hinblick auf Erfassungslatenz als auch auf Abfragezeiten.

## <a name="common-queries"></a>Allgemeine Abfragen
Allgemeine Abfragen werden in den ressourcenspezifischen und Azure-Diagnose-Tabellen angezeigt.

### <a name="top-n10-request-unit-ru-consuming-requests-or-queries-in-a-specific-time-frame"></a>Top N(10) Request Unit (RU), die Anforderungen oder Abfragen in einem bestimmten Zeitrahmen verbrauchen

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)
   ```Kusto
   //Enable full-text query to view entire query text
   CDBMongoRequests
   | where TimeGenerated > ago(24h)
   | project PIICommandText, ActivityId, DatabaseName , CollectionName, RequestCharge
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests"
   | where TimeGenerated > ago(24h)
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s, requestCharge_s
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-or-16500-in-a-specific-time-window"></a>Gedrosselte Anforderungen (statusCode = „429“ oder „16500“) in einem bestimmten Zeitfenster 

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)
   ```Kusto
   CDBMongoRequests
   | where TimeGenerated > ago(24h)
   | where ErrorCode == "429" or ErrorCode == "16500"
   | project DatabaseName, CollectionName, PIICommandText, OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests" and TimeGenerated > ago(24h)
   | where ErrorCode == "429" or ErrorCode == "16500"
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="timed-out-requests-statuscode--50-in-a-specific-time-window"></a>Anforderungen mit Zeitüberschreitung (statusCode = „50“) in einem bestimmten Zeitfenster 

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)
   ```Kusto
   CDBMongoRequests
   | where TimeGenerated > ago(24h)
   | where ErrorCode == "50"
   | project DatabaseName, CollectionName, PIICommandText, OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests" and TimeGenerated > ago(24h)
   | where ErrorCode == "50"
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-large-response-lengths-payload-size-of-the-server-response"></a>Abfragen mit langer Antwort (Nutzdatengröße der Serverantwort)

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)
   ```Kusto
   CDBMongoRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | summarize max(ResponseLength) by PIICommandText, RequestCharge, DurationMs, OperationName, TimeGenerated
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where Category == "MongoRequests"
   //specify collection and database
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   | summarize max(responseLength_s) by piiCommandText_s, OperationName, duration_s, requestCharge_s
   | order by max_responseLength_s desc
   ```    
---

### <a name="ru-consumption-by-physical-partition-across-all-replicas-in-the-replica-set"></a>RU-Verbrauch nach physischer Partition (für alle Replikate in der Replikatgruppe)

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)
   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by toint(PartitionKeyRangeId)
   | render columnchart
   ```

# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

### <a name="ru-consumption-by-logical-partition-across-all-replicas-in-the-replica-set"></a>RU-Verbrauch nach logischer Partition (für alle Replikate in der Replikatgruppe)

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)
   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
   | render columnchart  
   ```
# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)
   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>Nächste Schritte 
* Weitere Informationen zum Erstellen von Diagnoseeinstellungen für Azure Cosmos DB finden Sie unter [Erstellen von Diagnoseeinstellungen](../cosmosdb-monitor-resource-logs.md).
* Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Azure CLI oder PowerShell finden Sie unter [Erstellen von Diagnoseeinstellungen zum Sammeln von Plattformprotokollen und Metriken in Azure](../../azure-monitor/essentials/diagnostic-settings.md).
