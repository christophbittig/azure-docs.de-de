---
title: " Problembehandlung mit Diagnoseabfragen"
titleSuffix: Azure Cosmos DB
description: Erfahren Sie, wie Sie Diagnoseprotokolle für die Problembehandlung von Daten abfragen, die in Azure Cosmos DB gespeichert sind.
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/12/2021
ms.author: esarroyo
ms.openlocfilehash: e24ac58841c7ca786cb82e538930e66d081b1e9c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045173"
---
# <a name="troubleshoot-issues-with-diagnostics-queries"></a> Problembehandlung mit Diagnoseabfragen
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel erfahren Sie, wie Sie einfache Abfragen schreiben, um Probleme mit Ihrem Azure Cosmos DB-Konto mithilfe von Diagnoseprotokollen zu beheben, die an **AzureDiagnostics-Tabellen (Legacy)** und **ressourcenspezifische Tabellen (Vorschauversion)** gesendet werden.

Bei AzureDiagnostics-Tabellen werden alle Daten in eine einzelne Tabelle geschrieben, und Benutzer müssen angeben, welche Kategorie sie abfragen möchten.

Bei ressourcenspezifischen Tabellen werden Daten in einzelne Tabellen für die jeweilige Kategorie der Ressource geschrieben (nicht verfügbar für die Tabellen-API). Wir empfehlen diesen Modus, da die Bearbeitung der Daten erheblich erleichert wird, eine bessere Erkennbarkeit der Schemas ermöglicht wird und die Leistung in Bezug auf Erfassungslatenz sowie Abfragezeiten verbessert wird.

## <a name="azurediagnostics-queries"></a><a id="azure-diagnostics-queries"></a> AzureDiagnostics-Abfragen

- Abfragen von Vorgängen, deren Ausführung länger als 3 Millisekunden dauert:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

- Abfragen des Benutzer-Agents, der die Vorgänge ausführt:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

- Abfragen der Vorgänge mit langer Ausführungsdauer:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
- Abrufen von Partitionsschlüsselstatistiken zum Auswerten der Schiefe in den drei wichtigsten Partitionen für ein Datenbankkonto:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

- Wie erhalte ich die Anforderungskosten für ressourcenintensive Abfragen?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- Wie finde ich heraus, welche Vorgänge die meisten RU/s in Anspruch nehmen?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

- Abrufen aller Abfragen, die mehr als 100 RU/s verbrauchen und mit Daten aus **DataPlaneRequests** und **QueryRunTimeStatistics** verknüpft sind.

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- Wie erhalte ich die Anforderungsgebühren und die Ausführungsdauer einer Abfrage?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```

- Wie erhalte ich die Verteilung für verschiedene Vorgänge?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

- Was ist der maximale Durchsatz, den eine Partition verbraucht?
   
   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

- Wie erhalte ich die Informationen über den RU/s-Verbrauch pro Sekunde der Partitionsschlüssel?

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- Abrufen der Anforderungskosten für einen bestimmten Partitionsschlüssel

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

- Wie erhalte ich die wichtigsten Partitionsschlüssel mit den meisten verbrauchten RU/s in einem bestimmten Zeitraum?

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
   ```

- Wie erhalte ich die Protokolle für die Partitionsschlüssel, deren Speichergröße größer als 8 GB ist?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

- Wie kann ich die P99- oder P50-Replikationslatenzen für Vorgänge, Anforderungsgebühren oder die Länge der Antwort abrufen?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s), percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s), percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s), count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
- Wie rufe ich Controlplane-Protokolle ab?
 
   Denken Sie daran, das Flag zu aktivieren, wie es im Artikel [Deaktivieren des schlüsselbasierten Metadatenschreibzugriffs](audit-control-plane-logs.md#disable-key-based-metadata-write-access) beschrieben wird, und führen Sie die Vorgänge mithilfe von Azure PowerShell, der Azure CLI oder Azure Resource Manager aus.
 
   ```kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="resource-specific-queries"></a><a id="resource-specific-queries"></a> Ressourcenspezifische Abfragen

- Abfragen von Vorgängen, deren Ausführung länger als 3 Millisekunden dauert:

    ```kusto
    CDBDataPlaneRequests 
    | where toint(DurationMs) > 3
    | summarize count() by ClientIpAddress, TimeGenerated
    ```

- Abfragen des Benutzer-Agents, der die Vorgänge ausführt:

   ```kusto
    CDBDataPlaneRequests
    | summarize count() by OperationName, UserAgent
   ```

- Abfragen der Vorgänge mit langer Ausführungsdauer:

   ```kusto
    CDBDataPlaneRequests
    | project TimeGenerated , DurationMs 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
   ```
    
- Abrufen von Partitionsschlüsselstatistiken zum Auswerten der Schiefe in den drei wichtigsten Partitionen für ein Datenbankkonto:

   ```kusto
    CDBPartitionKeyStatistics
    | project RegionName, DatabaseName, CollectionName, PartitionKey, SizeKb
   ```

- Wie erhalte ich die Anforderungskosten für ressourcenintensive Abfragen?

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 10.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- Wie finde ich heraus, welche Vorgänge die meisten RU/s in Anspruch nehmen?

    ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize max(ResponseLength), max(RequestLength), max(RequestCharge), count = count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```

- Abrufen aller Abfragen, die mehr als 100 RU/s verbrauchen und mit Daten aus **DataPlaneRequests** und **QueryRunTimeStatistics** verknüpft sind.

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 100.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- Wie erhalte ich die Anforderungsgebühren und die Ausführungsdauer einer Abfrage?

   ```kusto
    CDBQueryRuntimeStatistics
    | join kind= inner (
    CDBDataPlaneRequests
    ) on $left.ActivityId == $right.ActivityId
    | project DatabaseName, CollectionName, OperationName , QueryText, RequestCharge, DurationMs, bin(TimeGenerated, 1min)
   ```

- Wie erhalte ich die Verteilung für verschiedene Vorgänge?

   ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize count = count()  by OperationName, RequestResourceType, bin(TimeGenerated, 1h)
   ```

- Was ist der maximale Durchsatz, den eine Partition verbraucht?

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2h) 
   | summarize max(RequestCharge) by bin(TimeGenerated, 1h), PartitionId
   ```

- Wie erhalte ich die Informationen über den RU/s-Verbrauch pro Sekunde der Partitionsschlüssel?

   ```kusto
   CDBPartitionKeyRUConsumption 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- Wie erhalte ich die Anforderungskosten für einen bestimmten Partitionsschlüssel?

   ```kusto
   CDBPartitionKeyRUConsumption  
   | where parse_json(PartitionKey)[0] == "2" 
   ```

- Wie erhalte ich die wichtigsten Partitionsschlüssel mit den meisten verbrauchten RU/s in einem bestimmten Zeitraum? 

   ```kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= datetime("02/12/2021, 11:20:00.000 PM") and TimeGenerated <= datetime("05/12/2021, 11:30:00.000 PM") 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey 
   | order by total desc
   ```

- Wie erhalte ich die Protokolle für die Partitionsschlüssel, deren Speichergröße größer als 8 GB ist?

   ```kusto
   CDBPartitionKeyStatistics
   | where todouble(SizeKb) > 800000
   ``` 

- Wie kann ich die P99- oder P50-Replikationslatenzen für Vorgänge, Anforderungsgebühren oder die Länge der Antwort abrufen?

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(ResponseLength), 50), percentile(todouble(ResponseLength), 99), max(ResponseLength), percentile(todouble(RequestCharge), 50), percentile(todouble(RequestCharge), 99), max(RequestCharge), percentile(todouble(DurationMs), 50), percentile(todouble(DurationMs), 99), max(DurationMs),count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```
 
- Wie rufe ich Controlplane-Protokolle ab?
 
   Denken Sie daran, das Flag zu aktivieren, wie es im Artikel [Deaktivieren des schlüsselbasierten Metadatenschreibzugriffs](audit-control-plane-logs.md#disable-key-based-metadata-write-access) beschrieben wird, und führen Sie die Vorgänge mithilfe von Azure PowerShell, der Azure CLI oder Azure Resource Manager aus.
 
   ```kusto  
   CDBControlPlaneRequests
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>Nächste Schritte 
* Weitere Informationen zum Erstellen von Diagnoseeinstellungen für Cosmos DB finden Sie im Artikel zum [Erstellen von Diagnoseeinstellungen](cosmosdb-monitor-resource-logs.md).

* Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).