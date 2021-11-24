---
title: Überwachen von Azure Cache for Redis-Daten mithilfe von Diagnoseeinstellungen
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie Diagnoseeinstellungen verwenden, um die mit Ihrer Azure Cache for Redis-Instanz verbundenen IP-Adressen zu überwachen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 11/3/2021
ms.custom: template-how-to
ms.openlocfilehash: 8fd6ae4eb82c7b8cb1439f621f26eef7860bcc0d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340222"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>Überwachen von Azure Cache for Redis-Daten mithilfe von Diagnoseeinstellungen

Diagnoseeinstellungen in Azure werden zum Sammeln von Ressourcenprotokollen verwendet. Azure-Ressourcenprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche, in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Diese Protokolle werden pro Anforderung erfasst und werden auch als „Datenebenenprotokolle“ bezeichnet. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp.

Azure Cache for Redis verwendet Azure-Diagnoseeinstellungen, um Informationen zu allen Clientverbindungen mit Ihrem Cache zu protokollieren. Dank der Protokollierung und anschließenden Analyse dieser Diagnoseeinstellung können Sie besser verstehen, wer eine Verbindung mit Ihren Caches herstellt und welchen Zeitstempel diese Verbindungen aufweisen. Mit diesen Protokoll-Daten können Sie das Ausmaß einer Sicherheitsverletzung ermitteln und Sicherheitsüberprüfungen durchführen.

Nach der Konfiguration beginnt Ihr Cache, eingehende Clientverbindungen nach IP-Adresse zu protokollieren. Außerdem wird die Anzahl der Verbindungen protokolliert, die von jeder eindeutigen IP-Adresse stammen. Die Protokolle sind nicht kumulativ. Sie stellen Zeitpunktmomentaufnahmen dar, die in Intervallen von 10 Sekunden erstellt wurden.

Sie können die Diagnoseeinstellungen für Azure Cache for Redis-Instanzen aktivieren und Ressourcenprotokolle an die folgenden Ziele senden:

- Der **Log-Analytics-Arbeitsbereich** muss sich nicht in derselben Region wie die überwachte Ressource befinden.
- **Speicherkonto**: Muss sich in derselben Region wie der Cache befinden.
- **Event Hub**: Diagnoseeinstellungen können nicht auf Event Hub-Ressourcen zugreifen, wenn virtuelle Netzwerke aktiviert sind. Aktivieren Sie die Einstellung **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** in Event Hub, um Zugriff auf Ihre Event Hub-Ressourcen zu gewähren. Der Event Hub muss sich in derselben Region wie der Cache befinden.

Weitere Informationen zu Diagnoseanforderungen finden Sie unter [Diagnoseeinstellungen](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD).

Wenn Sie Diagnoseprotokolle an eines der beiden Ziele senden, werden Ihnen die normalen Datentarife für die Nutzung von Speicherkonten und Event Hub berechnet. Sie werden unter Azure Monitor und nicht unter Azure Cache for Redis abgerechnet. Beim Senden von Protokollen an **Log Analytics** wird Ihnen nur die Log-Analytics-Datenerfassung in Rechnung gestellt.

Weitere Preisinformationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Erstellen von Diagnoseeinstellungen über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrem Azure Cache for Redis-Konto. Öffnen Sie den Bereich **Diagnoseeinstellungen** auf der linken Seite unter **Überwachungsabschnitt**. Wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="„Diagnose“ auswählen":::

1. Wählen Sie im Bereich **Diagnoseeinstellungen** die Option **ConnectedClientList** aus **Kategoriedetails** aus.

   |Category  | Definition  | Schlüsseleigenschaften   |
   |---------|---------|---------|
   |ConnectedClientList |  IP-Adressen und Anzahl der mit dem Cache verbundenen Clients, die in regelmäßigen Abständen protokolliert werden. | `connectedClients` und geschachtelt in: `ip`, `count`, `privateLinkIpv6` |

   Weitere Informationen zu anderen Bereichen finden Sie unter [Ressourcenprotokolle](#resource-logs).

1. Nachdem Sie Ihre **Kategoriendetails** ausgewählt haben, senden Sie Ihre Protokolle an Ihr bevorzugtes Ziel. Wählen Sie die Informationen auf der rechten Seite aus.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="Auswählen von „Ressourcenspezifisch“":::

## <a name="create-diagnostic-setting-via-rest-api"></a> Erstellen der Diagnoseeinstellung über die REST-API

Verwenden Sie die Azure Monitor-REST-API, um eine Diagnoseeinstellung über die interaktive Konsole zu erstellen. Weitere Informationen finden Sie unter [Account Filters – Create Or Update](/rest/api/monitor/diagnostic-settings/create-or-update) (Erstellen oder Aktualisieren von Kontofiltern).

### <a name="request"></a>Anforderung

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>Header

   | Parameter/Header | Wert/Beschreibung |
   |---------|---------|
   | `name` | Der Name Ihrer Diagnoseeinstellung. |
   | `resourceUri` | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | `api-version` | 2017-05-01-preview |
   | `Content-Type` | Anwendung/json |

### <a name="body"></a>Text

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "workspaceId": "/subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Erstellen der Diagnoseeinstellung über die Azure CLI

Verwenden Sie den Befehl `az monitor diagnostic-settings create`, um eine Diagnoseeinstellung mit der Azure CLI zu erstellen. Weitere Informationen zu Befehls- und Parameterbeschreibungen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und -Metriken an verschiedene Ziele](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest&branch=main&preserve-view=true#az_monitor_diagnostic_settings_create).

```azurecli
az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace
    --workspace /subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace
```

## <a name="resource-logs"></a>Ressourcenprotokolle

Diese Felder und Eigenschaften werden in der Protokollkategorie `ConnectedClientList` angezeigt. In **Azure Monitor** werden Protokolle in der Tabelle `ACRConnectedClientList` unter dem Ressourcenanbieternamen von `MICROSOFT.CACHE` gesammelt.

| Azure Storage-Feld oder -Eigenschaft | Eigenschaft von Azure Monitor-Protokolle | BESCHREIBUNG |
| --- | --- | --- |
| `time` | `TimeGenerated` | Der Zeitstempel für den Zeitpunkt, zu dem das Protokoll im UTC generiert wurde |
| `location` | `Location` | Der Speicherort (Region), in dem auf Azure Cache für die Redis-Instanz zugegriffen wurde. |
| `category` | – | Verfügbare Protokoll-Kategorien: `ConnectedClientList`. |
| `resourceId` | `_ResourceId` | Der Azure-Cache für die Redis-Ressource, für welche die Protokolle aktiviert sind.|
| `operationName` | `OperationName` | Der Redis-Vorgang, der dem Protokolldatensatz zugeordnet ist. |
| `properties` | – | Die Inhalte dieser Felder werden in den folgenden Zeilen beschrieben. |
| `tenant` | `CacheName` | Der Name Ihres Azure-Caches für die Redis-Instanz. |
| `roleInstance` | `RoleInstance` | Die Rolleninstanz, die die Clientliste protokolliert hat. |
| `connectedClients.ip` | `ClientIp` | Die IP-Adresse des Redis-Clients. |
| `connectedClients.privateLinkIpv6` | `PrivateLinkIpv6` | Die private Link-IPv6-Adresse des Redis-Clients (falls zutreffend). |
| `connectedClients.count` | `ClientCount` | Die Anzahl der Redis-Clientverbindungen von der zugeordneten IP-Adresse. |

### <a name="sample-storage-account-log"></a>Ein Beispiel von einem Speicherkontoprotokoll

Wenn Sie Ihre Protokolle an ein Speicherkonto senden, sieht der Inhalt der Protokolle wie hier aus.

```json
{
    "time": "2021-08-05T21:04:58.0466086Z",
    "location": "canadacentral",
    "category": "ConnectedClientList",
    "properties": {
        "tenant": "mycache", 
        "connectedClients": [
            {
                "ip": "192.123.43.36", 
                "count": 86
            },
            {
                "ip": "10.1.1.4",
                "privateLinkIpv6": "fd40:8913:31:6810:6c31:200:a01:104", 
                "count": 1
            }
        ],
        "roleInstance": "1"
    },
    "resourceId": "/SUBSCRIPTIONS/E6761CE7-A7BC-442E-BBAE-950A121933B5/RESOURCEGROUPS/AZURE-CACHE/PROVIDERS/MICROSOFT.CACHE/REDIS/MYCACHE", 
    "Level": 4,
    "operationName": "Microsoft.Cache/ClientList"
}
```

## <a name="log-analytics-queries"></a>Log-Analytics-Abfragen

Hier sind einige grundlegende Abfragen, die als Modelle verwendet werden sollten.

- Azure Cache für Redis-Clientverbindungen pro Stunde innerhalb des angegebenen IP-Adressbereichs:

```kusto
let IpRange = "10.1.1.0/24";
ACRConnectedClientList
// For particular datetime filtering, add '| where TimeGenerated between (StartTime .. EndTime)'
| where ipv4_is_in_range(ClientIp, IpRange)
| summarize ConnectionCount = sum(ClientCount) by TimeRange = bin(TimeGenerated, 1h)
```

- Eindeutige Redis-Client-IP-Adressen, die mit dem Cache verbunden sind:

```kusto
ACRConnectedClientList
| summarize count() by ClientIp
```

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).
