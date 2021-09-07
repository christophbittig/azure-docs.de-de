---
title: Behandeln von Problemen mit erweiterten Diagnoseabfragen für die Cassandra-API
titleSuffix: Azure Cosmos DB
description: Es wird beschrieben, wie Sie für die Cassandra-API Diagnoseprotokolle zur Problembehandlung für Daten abfragen, die in Azure Cosmos DB gespeichert sind.
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: a9bd63d48e4cfd5c197e050b071d2a9dea927ebf
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560927"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-cassandra-api"></a>Behandeln von Problemen mit erweiterten Diagnoseabfragen für die Cassandra-API

[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL-API (Core-API)](cosmos-db-advanced-queries.md)
> * [MongoDB-API](queries-mongo.md)
> * [Cassandra-API](queries-cassandra.md)
> * [Gremlin-API](queries-gremlin.md)


In diesem Artikel erfahren Sie, wie Sie komplexere Abfragen schreiben, um Probleme mit Ihrem Azure Cosmos DB-Konto mithilfe von Diagnoseprotokollen zu beheben, die an **AzureDiagnostics-Tabellen (Legacy)** und **ressourcenspezifische Tabellen (Vorschauversion)** gesendet werden.

Bei AzureDiagnostics-Tabellen werden alle Daten in eine einzelne Tabelle geschrieben, und Benutzer müssen angeben, welche Kategorie sie abfragen möchten. Wenn Sie die Volltextabfrage Ihrer Anforderung anzeigen möchten, erfahren Sie in [diesem Artikel](cosmosdb-monitor-resource-logs.md#full-text-query), wie Sie das Feature aktivieren.

Bei [ressourcenspezifischen Tabellen](cosmosdb-monitor-resource-logs.md#create-setting-portal) werden Daten für jede Kategorie der Ressource in einzelne Tabellen geschrieben. Wir empfehlen diesen Modus, da die Bearbeitung der Daten erheblich erleichert wird, eine bessere Erkennbarkeit der Schemas ermöglicht wird und die Leistung in Bezug auf Erfassungslatenz sowie Abfragezeiten verbessert wird.

## <a name="common-queries"></a>Allgemeine Abfragen

- N (10) Anforderungen/Abfragen mit dem höchsten RU-Verbrauch in einem bestimmten Zeitrahmen

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBCassandraRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(1h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

- Gedrosselte Anforderungen (statusCode = 429) in einem bestimmten Zeitfenster 

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBCassandraRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

- Abfragen mit großer Antwortlänge (Nutzdatengröße der Serverantwort)

# <a name="resource-specific"></a>[Modus „Ressourcenspezifisch“](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBCassandraRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by PIICommandText
   | order by max_ResponseLength desc
   ```

# <a name="azure-diagnostics"></a>[Azure-Diagnose](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by piiCommandText_s
   | order by max_responseLength_s1 desc
   ```    
---

- RU-Verbrauch nach physischer Partition (für alle Replikate in der Replikatgruppe)

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
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

- RU-Verbrauch nach logischer Partition (für alle Replikate in der Replikatgruppe)

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
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>Nächste Schritte 
* Weitere Informationen zum Erstellen von Diagnoseeinstellungen für Cosmos DB finden Sie im Artikel zum [Erstellen von Diagnoseeinstellungen](cosmosdb-monitor-resource-logs.md).

* Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).