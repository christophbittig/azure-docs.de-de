---
title: Azure Monitor
description: Azure Monitor
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dcb4dcd6037f8efcbac33e970566babf10c7d262
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858592"
---
# <a name="monitor-azure-managed-instance-for-apache-cassandra-using-azure-monitor"></a>Überwachen von Azure Managed Instance for Apache Cassandra mithilfe von Azure Monitor

Azure Managed Instance for Apache Cassandra bietet Metriken und Diagnoseprotokolle mithilfe von [Azure Monitor](../azure-monitor/overview.md). 

## <a name="azure-metrics"></a>Azure-Metriken

Sie können die Metriken für Azure Managed Instance for Apache Cassandra visualisieren, indem Sie zu Ihrer Clusterressource navigieren und die Registerkarte „Metriken“ auswählen. Sie können dann aus den verfügbaren Metriken und Aggregationen wählen.

:::image type="content" source="./media/azure-monitor/metrics.png" alt-text="Visualisieren von Metriken":::

## <a name="diagnostic-settings-in-azure"></a>Diagnoseeinstellungen in Azure

Diagnoseeinstellungen in Azure werden zum Sammeln von Ressourcenprotokollen verwendet. Azure-Ressourcenprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche, in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Diese Protokolle werden pro Anforderung erfasst und werden auch als „Datenebenenprotokolle“ bezeichnet. Einige Beispiele für Datenebenenvorgänge sind „delete“, „insert“ und „readFeed“. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp.

Plattformmetriken und das Aktivitätsprotokoll werden automatisch gesammelt, aber Sie müssen eine Diagnoseeinstellung erstellen, um Ressourcenprotokolle zu sammeln oder außerhalb von Azure Monitor weiterzuleiten. Sie können die Diagnoseeinstellungen für Azure Managed Instance for Apache Cassandra-Clusterressourcen aktivieren und Ressourcenprotokolle an die folgenden Quellen senden:
- Log Analytics-Arbeitsbereiche
  - An Log Analytics gesendete Daten können in **Azure-Diagnosetabellen (Legacy)** oder **ressourcenspezifische Tabellen (Vorschau)** geschrieben werden 
- Event Hub
- Speicherkonto
  
> [!NOTE]
> Es wird empfohlen, die Diagnoseeinstellung im ressourcenspezifischen Modus zu erstellen.

## <a name="create-diagnostic-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Erstellen von Diagnoseeinstellungen über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrer Azure Managed Instance for Apache Cassandra-Clusterressource. 

    :::image type="content" source="./media/azure-monitor/cluster.png" alt-text="Cluster auswählen":::
 
1. Öffnen Sie den Bereich **Diagnoseeinstellungen** im **Überwachungsabschnitt**, und wählen Sie dann die Option **Diagnoseeinstellung hinzufügen** aus.

    :::image type="content" source="./media/azure-monitor/settings.png" alt-text="Diagnoseeinstellungen hinzufügen":::


1. Wählen Sie im Bereich **Diagnoseeinstellungen** einen Namen für Ihre Einstellung und dann **Kategoriendetails** aus. Die Kategorie **CassandraAudit** zeichnet Überwachungs- und CQL-Vorgänge auf. Die Kategorie **CassandraLogs** zeichnet Cassandra-Servervorgänge auf. Senden Sie dann Ihre Protokolle an Ihr bevorzugtes Ziel. Wenn Sie Protokolle an einen **Log Analytics-Arbeitsbereich** senden, wählen Sie **Ressourcespezifisch** als Zieltabelle aus. 

    :::image type="content" source="./media/azure-monitor/preferred-categories.png" alt-text="Kategorie auswählen":::

   > [!WARNING]
   > Wenn Sie Protokolle an einen Log Analytics-Arbeitsbereich senden, kann es bis zu **20 Minuten** dauern, bis Protokolle erstmalig angezeigt werden. Bis dahin sind die ressourcenspezifischen Tabellen (siehe unten unter „Azure Managed Instance for Apache Cassandra“) nicht sichtbar.  


1. Sobald die Diagnoseprotokollierung eingerichtet ist und Daten fließen, können Sie zur Registerkarte **Protokolle** wechseln und die verfügbaren Diagnoseprotokolle mithilfe von Azure Data Explorer abfragen. In [diesem Artikel](../azure-monitor/logs/log-query-overview.md) finden Sie weitere Informationen zu Azure Monitor und der Kusto-Abfragesprache. 

    :::image type="content" source="./media/azure-monitor/query.png" alt-text="Abfrageprotokolle":::

## <a name="create-diagnostic-setting-via-azure-cli"></a><a id="create-setting-cli"></a> Erstellen der Diagnoseeinstellung über die Azure CLI
Verwenden Sie den Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create), um eine Diagnoseeinstellung mit der Azure CLI zu erstellen. Beschreibungen der zugehörigen Parameter finden Sie in der Dokumentation zu diesem Befehl.

```azurecli-interactive
    logs='[{"category":"CassandraAudit","enabled":true,"retentionPolicy":{"enabled":true,"days":3}},{"category":"CassandraLogs","enabled":true,"retentionPolicy":{"enabled":true,"days":3}}]'
    resourceId='/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDB/cassandraClusters/{CLUSTER_NAME}'
    workspace='/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}'

    az monitor diagnostic-settings create  --name tvk-doagnostic-logs-cassandra --resource $resourceId --logs  $logs --workspace $workspace --export-to-resource-specific true
```

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
                "category": "CassandraAudit",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "CassandraLogs",
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


## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).