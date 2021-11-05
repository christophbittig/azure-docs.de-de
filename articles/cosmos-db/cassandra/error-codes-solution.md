---
title: Serverdiagnose für die Cassandra-API für Azure Cosmos DB
description: In diesem Artikel werden einige häufig in der Cassandra-API für Azure Cosmos DB angezeigte Fehlercodes und die entsprechende Problembehandlung mit Log Analytics erläutert.
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: troubleshooting
ms.date: 10/12/2021
ms.custom: template-how-to
ms.openlocfilehash: bbef336e16623113c45929710e49101bddfdf64e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095920"
---
# <a name="server-diagnostics-for-azure-cosmos-db-cassandra-api"></a>Serverdiagnose für die Cassandra-API für Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Log Analytics ist ein Tool im Azure-Portal, mit dem Sie Serverdiagnosen für Ihr Cassandra-API-Konto ausführen können. Führen Sie Protokollabfragen für Daten aus, die in Azure Monitor Logs gesammelt wurden, und analysieren die jeweiligen Ergebnisse interaktiv. Mithilfe von aus Log Analytics-Abfragen abgerufenen Datensätzen können verschiedene Erkenntnisse zu Ihren Daten bereitgestellt werden.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie einen [Log Analytics-Arbeitsbereich](../../azure-monitor/logs/quick-create-workspace.md).
- Erstellen Sie [Diagnoseeinstellungen](../cosmosdb-monitor-resource-logs.md).
- Starten Sie [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) in Ihrem Cassandra-API-Konto.

## <a name="use-log-analytics"></a>Verwenden von Log Analytics
Wenn Sie Log Analytics vollständig eingerichtet haben, können Sie beginnen, Ihre Protokolle zu untersuchen, um weitere Erkenntnisse zu gewinnen.

### <a name="explore-data-plane-operations"></a>Untersuchen von Vorgängen auf Datenebene
In der Tabelle CDBCassandraRequests können Sie Vorgänge auf Datenebene speziell für Ihr Cassandra-API-Konto anzeigen. Im Folgenden sehen Sie eine Beispielabfrage, mit der die n (10) Anforderungen mit dem höchsten Verbrauch angezeigt und detaillierte Informationen zu den einzelnen ausgeführten Anforderungen abgerufen werden.

```Kusto
CDBCassandraRequests
| where RequestCharge  > 0
| project DatabaseName, CollectionName, DurationMs, OperationName, ActivityId, ErrorCode, RequestCharge, PIICommandText 
| order by RequestCharge
| take 10
```

#### <a name="error-codes-and-possible-solutions"></a>Fehlercodes und mögliche Lösungen
|Statuscode | Fehlercode           | BESCHREIBUNG  |
|------------|----------------------|--------------|
| 200 | -1 | Erfolgreich |
| 400 | 8704 | Die Abfrage ist korrekt, verfügt jedoch über eine ungültige Syntax. |
| 400 | 8192 | Die übermittelte Abfrage enthält einen Syntaxfehler. Überprüfen Sie Ihre Abfrage. |
| 400 | 8960 | Die Abfrage ist aufgrund eines Konfigurationsproblems ungültig. |
| 401 |8448 | Der protokollierte Benutzer oder die protokollierte Benutzerin verfügt nicht über die richtigen Berechtigungen, um die Abfrage auszuführen. |
| 403 | 8448 | Antwort, dass die Anforderung unzulässig ist, da der Benutzer oder die Benutzerin möglicherweise nicht über die zum Ausführen der Anforderung erforderlichen Berechtigungen verfügt |
| 404 | 5376 | Ausnahme ohne Timeout, die während einer Schreibanforderung auftritt, da keine Antwort gefunden wurde |
| 405 | 0 | Hierbei handelt es sich um einen serverseitigen Cassandra-Fehler. Dieser Fehler tritt selten auf. Erstellen Sie ein Supportticket. |
| 408 | 4608 | Timeout während einer Leseanforderung |
| 408 | 4352 | Ausnahme nach Timeout während einer serviceRequest-Schreibanforderung |
| 409 | 9216 | Es wurde versucht, einen bereits vorhandenen Keyspace oder eine bereits vorhandene Tabelle zu erstellen. |
| 412 | 5376 | Es ist ein Fehler bei einer Vorbedingung aufgetreten. Um Datenintegrität zu gewährleisten, stellen wir sicher, dass die auf der Leseantwort basierende Schreibanforderung richtig ist. Für die Schreibanforderung wird eine Ausnahme ohne Timeout zurückgegeben. |
| 413 | 5376 | Diese Ausnahme ohne Timeout während einer Schreibanforderung ist darauf zurückzuführen, dass die Nutzdatenmenge möglicherweise zu groß ist. Derzeit gilt hierfür eine Obergrenze von 2 MB pro Zeile. |
| 417 | 9472 | Diese Ausnahme wird ausgelöst, wenn eine vorbereitete Anweisung nicht auf dem Serverknoten zwischengespeichert wurde. Sie sollte nur vorübergehend aktiv sein bzw. nichts blockieren. |
| 423 | 5376 | Es liegt eine Sperre aufgrund einer Schreibanforderung vor, die gerade verarbeitet wird. |
| 429 | 4097| Dies ist eine Überladungsausnahme, die auf einen Mangel an Anforderungseinheiten oder eine hohe Anforderungsrate zurückzuführen ist. Wahrscheinlich benötigen Sie mehr Anforderungseinheiten, um die Anforderung mit höherem Volumen zu verarbeiten. Bei der nativen Version von Cassandra kann dies so interpretiert werden, dass die CPU einer der VMs nicht ausreicht. Es wird empfohlen, das aktuelle Datenmodell zu überprüfen, um sicherzustellen, dass Sie über keine übermäßige Datenschiefe verfügen, die möglicherweise heiße Partitionen verursacht. |
| 449 | 5376 | Dies ist eine Ausnahme aufgrund einer parallelen Ausführung. Sie tritt auf, um sicherzustellen, dass für eine Zeile zur selben Zeit nicht mehr als ein Schreibupdate ausgeführt wird. |
| 500 | 0 | Server cassandraError: Unerwarteter Fehler. Dieser Fehlercode deutet auf einen Fehler auf Serverseite hin. |
| 503 | 4096 | Service unavailable. (Dienst nicht verfügbar.) |
|   | 256 | Dieser Fehler ist möglicherweise auf ungültige Anmeldeinformationen für die Verbindung zurückzuführen. Überprüfen Sie Ihre Anmeldeinformationen für die Verbindung. |
|   | 10 | Eine Clientnachricht hat eine Protokollverletzung ausgelöst. Ein Beispiel hierfür ist eine Abfragenachricht, die vor der Startnachricht gesendet wurde. |

### <a name="troubleshoot-query-consumption"></a>Problembehandlung für den Verbrauch von Abfragen
Die Tabelle CDBPartitionKeyRUConsumption enthält Details zum Verbrauch von Anforderungseinheiten (Request Unit, RU) für logische Schlüssel in den einzelnen Regionen innerhalb der jeweiligen physischen Partitionen.

```Kusto
CDBPartitionKeyRUConsumption 
| summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
| render columnchart
 ```

### <a name="explore-control-plane-operations"></a>Untersuchen von Vorgängen auf Steuerungsebene
Die Tabelle CBDControlPlaneRequests enthält Details zu Vorgängen auf Steuerungsebene, insbesondere für Cassandra-API-Konten. 

```Kusto
CDBControlPlaneRequests
| where TimeGenerated > now(-6h)
| where  ApiKind == "Cassandra"
| where OperationName in ("Create", "Upsert", "Delete", "Execute")
| summarize by OperationName
 ```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).
- Erfahren Sie, wie Sie [vom nativen Apache Cassandra zur Cassandra-API von Azure Cosmos DB migrieren](migrate-data-databricks.md).