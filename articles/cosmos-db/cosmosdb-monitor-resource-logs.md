---
title: Überwachen der Azure Cosmos DB-Daten mithilfe der Azure-Diagnoseeinstellungen
description: Hier erfahren Sie, wie Sie die Azure-Diagnoseeinstellungen verwenden, um die Leistung und Verfügbarkeit der in Azure Cosmos DB gespeicherten Daten zu überwachen.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 2f25cfa8f2c9c70b6cc97dc96d504b41078f5b5f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667670"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Überwachen der Azure Cosmos DB-Daten mithilfe der Diagnoseeinstellungen in Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Diagnoseeinstellungen in Azure werden zum Sammeln von Ressourcenprotokollen verwendet. Azure-Ressourcenprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche, in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Diese Protokolle werden pro Anforderung erfasst und werden auch als „Datenebenenprotokolle“ bezeichnet. Einige Beispiele für Datenebenenvorgänge sind „delete“, „insert“ und „readFeed“. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp.

Plattformmetriken und das Aktivitätsprotokoll werden automatisch gesammelt, aber Sie müssen eine Diagnoseeinstellung erstellen, um Ressourcenprotokolle zu sammeln oder außerhalb von Azure Monitor weiterzuleiten. Sie können die Diagnoseeinstellung für Azure Cosmos DB-Konten aktivieren und Ressourcenprotokolle an die folgenden Quellen senden:
- Log Analytics-Arbeitsbereiche
  - An Log Analytics gesendete Daten können in **Azure-Diagnosetabellen (Legacy)** oder **ressourcenspezifische Tabellen (Vorschau)** geschrieben werden 
- Event Hub
- Speicherkonto
  
> [!NOTE]
> Sie sollten die Diagnoseeinstellung (für alle APIs außer der Tabellen-API) im ressourcenspezifischen Modus erstellen. Befolgen Sie dazu unsere [Anweisungen zum Erstellen der Diagnoseeinstellung über die REST-API](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting). Diese Option bietet zusätzliche Kostenoptimierungen mit einer verbesserten Ansicht für die Verarbeitung von Daten.

## <a name="create-diagnostics-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Erstellen von Diagnoseeinstellungen über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zu Ihrem Azure Cosmos-Konto. Öffnen Sie den Bereich **Diagnoseeinstellungen** im **Überwachungsabschnitt**, und wählen Sie dann die Option **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="./media/monitor-cosmos-db/diagnostics-settings-selection.png" alt-text="„Diagnose“ auswählen":::


3. Geben Sie im Bereich **Diagnoseeinstellungen** Ihre bevorzugten Kategorien in das Formular ein.

### <a name="choose-log-categories"></a>Auswählen von Protokollkategorien

   |Kategorie  |API   | Definition  | Schlüsseleigenschaften   |
   |---------|---------|---------|---------|
   |DataPlaneRequests     |  Alle APIs        |     Protokolliert Back-End-Anforderungen als Vorgänge auf Datenebene. Dabei handelt es sich um Anforderungen zum Erstellen, Aktualisieren, Löschen oder Abrufen von Daten innerhalb des Kontos.   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
   |MongoRequests     |    Mongo    |   Protokolliert vom Benutzer initiierte Anforderungen vom Front-End zum Verarbeiten von Anforderungen an die Azure Cosmos DB-API für MongoDB. Wenn Sie diese Kategorie aktivieren, muss DataPlaneRequests deaktiviert werden.      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
   |CassandraRequests     |   Cassandra      |    Protokolliert vom Benutzer initiierte Anforderungen vom Front-End zum Verarbeiten von Anforderungen an die Azure Cosmos DB-API für Cassandra. Wenn Sie diese Kategorie aktivieren, muss DataPlaneRequests deaktiviert werden.     |     `operationName`, `requestCharge`, `piiCommandText`    |
   |GremlinRequests     |    Gremlin    |     Protokolliert vom Benutzer initiierte Anforderungen vom Front-End zum Verarbeiten von Anforderungen an die Azure Cosmos DB-API für Gremlin. Wenn Sie diese Kategorie aktivieren, muss DataPlaneRequests deaktiviert werden.    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
   |QueryRuntimeStatistics     |   SQL      |     In dieser Tabelle sind die Abfragevorgänge aufgeführt, die für ein SQL-API-Konto ausgeführt wurden. Der Abfragetext und seine Parameter werden standardmäßig verborgen. Auf diese Weise wird verhindert, dass personenbezogene Informationen mit der Volltextabfrageprotokollierung protokolliert werden, die auf Anforderung verfügbar ist.    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
   |PartitionKeyStatistics     |    Alle APIs     |   Protokolliert die Statistiken logischer Partitionsschlüssel, indem die Speichergröße (KB) der Partitionsschlüssel dargestellt wird. Diese Tabelle ist bei der Problembehandlung aufgrund einer ungleichmäßigen Datenverteilung nützlich. Dieses PartitionKeyStatistics-Protokoll wird nur ausgegeben, wenn die folgenden Bedingungen zutreffen: <br/><ul><li> Mindestens 1 % der Dokumente verfügt über denselben logischen Partitionsschlüssel. </li><li> Von allen Schlüsseln werden die ersten drei Schlüssel mit der größten Speichergröße im PartitionKeyStatistics-Protokoll erfasst. </li></ul> Wenn die vorherigen Bedingungen nicht erfüllt sind, sind die Partitionsschlüsselstatistik-Daten nicht verfügbar. Es ist in Ordnung, wenn die oben genannten Bedingungen für Ihr Konto nicht erfüllt sind. Dies deutet in der Regel darauf hin, dass bei Ihnen keine logische Partitionsspeicherschiefe vorliegt. |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
   |PartitionKeyRUConsumption     |   SQL-API    |     Protokolliert den aggregierten RU/s-Verbrauch von Partitionsschlüsseln. Diese Tabelle ist für die Problembehandlung bei heißen Partitionen nützlich. Derzeit meldet Azure Cosmos DB Partitionsschlüssel nur für SQL-API-Konten und für Punktlese-/Punktschreibvorgänge sowie für Vorgänge für gespeicherte Prozeduren.   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
   |ControlPlaneRequests     |   Alle APIs       |    Protokolliert Details zu Vorgängen auf Steuerungsebene, z. B. das Erstellen eines Kontos, das Hinzufügen oder Entfernen einer Region, das Aktualisieren der Einstellungen für die Kontoreplikation usw.     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
   |TableApiRequests     |   Tabelle-API    |     Protokolliert vom Benutzer initiierte Anforderungen vom Front-End zum Verarbeiten von Anforderungen an die Azure Cosmos DB-API für Table. Wenn Sie diese Kategorie aktivieren, muss DataPlaneRequests deaktiviert werden.       |    `operationName`, `requestCharge`, `piiCommandText`     |

4. Nachdem Sie Ihre **Kategoriendetails** ausgewählt haben, senden Sie Ihre Protokolle an Ihr bevorzugtes Ziel. Wenn Sie Protokolle an einen **Log Analytics-Arbeitsbereich** senden, wählen Sie **Ressourcespezifisch** als Zieltabelle aus.

    :::image type="content" source="./media/monitor-cosmos-db/diagnostics-resource-specific.png" alt-text="Auswählen von „Ressourcenspezifisch“":::

## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> Erstellen der Diagnoseeinstellung über die REST-API
Verwenden Sie die [Azure Monitor-REST-API](/rest/api/monitor/diagnosticsettings/createorupdate), um eine Diagnoseeinstellung über die interaktive Konsole zu erstellen.
> [!Note]
> Sie sollten die **logAnalyticsDestinationType**-Eigenschaft auf **Dediziert** setzen, um ressourcenspezifische Tabellen zu aktivieren.

### <a name="request"></a>Anforderung

   ```HTTP
   PUT
   https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
   ```

### <a name="headers"></a>Header

   |Parameter/Header  | Wert/Beschreibung  |
   |---------|---------|
   |name     |  Der Name Ihrer Diagnoseeinstellung.      |
   |resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
   |api-version     |    2017-05-01-preview     |
   |Content-Type     |    Anwendung/json     |

### <a name="body"></a>Text

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "DataPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "QueryRuntimeStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyRUConsumption",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ControlPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Erstellen der Diagnoseeinstellung über die Azure CLI
Verwenden Sie den Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create), um eine Diagnoseeinstellung mit der Azure CLI zu erstellen. Beschreibungen der zugehörigen Parameter finden Sie in der Dokumentation zu diesem Befehl.

> [!Note]
> Wenn Sie die SQL-API verwenden, sollten Sie die **export-to-resource-specific**-Eigenschaft auf **true** setzen.

   ```azurecli-interactive
   az monitor diagnostic-settings create --resource /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/ --name {DIAGNOSTIC_SETTING_NAME} --export-to-resource-specific true --logs '[{"category": "QueryRuntimeStatistics","categoryGroup": null,"enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]' --workspace /subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}"
   ```
## <a name="enable-full-text-query-for-logging-query-text"></a><a id="full-text-query"></a> Aktivieren der Volltextabfrage zur Protokollierung von Abfragetext

> [!Note]
> Die Aktivierung dieses Features kann zu zusätzlichen Protokollierungskosten führen. Preisinformationen finden Sie unter [Azure Monitor Preise](https://azure.microsoft.com/pricing/details/monitor/). Sie sollten dieses Feature nach der Problembehandlung deaktivieren.

Azure Cosmos DB bietet erweiterte Protokollierung für eine detaillierte Problembehandlung. Wenn Sie die Volltextabfrage aktivieren, können Sie die verschleierte Abfrage für alle Anforderungen in Ihrem Azure Cosmos DB-Konto anzeigen.  Außerdem erteilen Sie Azure Cosmos DB die Berechtigung, auf diese Daten zuzugreifen und sie in Ihren Protokollen anzuzeigen. 

1. Navigieren Sie zum Aktivieren dieses Features in Ihrem Cosmos DB-Konto zum `Features`-Blatt.
   
   :::image type="content" source="./media/monitor-cosmos-db/full-text-query-features.png" alt-text="Navigieren zum Blatt „Features“":::

2. Wählen Sie `Enable` aus. Diese Einstellung wird dann in den nächsten Minuten angewendet. Alle neu erfassten Protokolle verfügen für jede Anforderung über den Volltext- oder PIICommand-Text.
   
    :::image type="content" source="./media/monitor-cosmos-db/select-enable-full-text.png" alt-text="Auswählen des Aktivierens von Volltext":::

Informationen zum Abfragen mit diesem neu aktivierten Feature finden Sie unter [Erweiterte Abfragen](cosmos-db-advanced-queries.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Details zum Abfragen von ressourcenspezifischen Tabellen finden Sie in den Informationen zur [Problembehandlung mit ressourcenspezifischen Tabellen](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries).

* Weitere Informationen zum Abfragen von Azure-Diagnosetabellen finden Sie in den Informationen zur Problembehandlung mit [Azure-Diagnosetabellen](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries).

* Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).
