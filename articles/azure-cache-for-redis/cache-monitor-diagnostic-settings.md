---
title: Überwachen von Azure Cache for Redis-Daten mithilfe von Diagnoseeinstellungen
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie Diagnoseeinstellungen verwenden, um die mit Ihrer Azure Cache for Redis-Instanz verbundenen IP-Adressen zu überwachen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 2662ba2f2e87f79b034ae8138bba31fc9358d8d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236169"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>Überwachen von Azure Cache for Redis-Daten mithilfe von Diagnoseeinstellungen

Diagnoseeinstellungen in Azure werden zum Sammeln von Ressourcenprotokollen verwendet. Azure-Ressourcenprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche, in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Diese Protokolle werden pro Anforderung erfasst und werden auch als „Datenebenenprotokolle“ bezeichnet. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp.

Azure Cache for Redis verwendet Azure-Diagnoseeinstellungen, um Informationen zu allen Clientverbindungen mit Ihrem Cache zu protokollieren. Die Protokollierung und anschließende Analyse dieser Diagnoseeinstellung hilft Ihnen zu verstehen, wer eine Verbindung zu Ihren Caches herstellt und welchen Zeitstempel diese Verbindungen aufweisen. Diese Daten könnten zu Zwecken der Sicherheitsüberprüfung sowie dazu verwendet werden, das Ausmaß einer Sicherheitsverletzung zu ermitteln.

Nach der Konfiguration beginnt Ihr Cache, eingehende Clientverbindungen nach IP-Adresse zu protokollieren. Außerdem wird die Anzahl der Verbindungen protokolliert, die von jeder eindeutigen IP-Adresse stammen. Die Protokolle sind nicht kumulativ. Sie stellen Zeitpunktmomentaufnahmen dar, die in Intervallen von 10 Sekunden erstellt wurden.

Sie können die Diagnoseeinstellungen für Azure Cache for Redis-Instanzen aktivieren und Ressourcenprotokolle an die folgenden Ziele senden:

- **Event Hub**: Diagnoseeinstellungen können nicht auf Event Hub-Ressourcen zugreifen, wenn virtuelle Netzwerke aktiviert sind. Aktivieren Sie die Einstellung **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** in Event Hub, um Zugriff auf Ihre Event Hub-Ressourcen zu gewähren. Der Event Hub muss sich in derselben Region wie der Cache befinden.
- **Speicherkonto**: Muss sich in derselben Region wie der Cache befinden.

Weitere Informationen zu Diagnoseanforderungen finden Sie unter [Diagnoseeinstellungen](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD).

Wenn Sie Diagnoseprotokolle an eines der beiden Ziele senden, werden Ihnen die normalen Datentarife für die Nutzung von Speicherkonten und Event Hub berechnet. Sie werden unter Azure Monitor und nicht unter Azure Cache for Redis abgerechnet.
Weitere Preisinformationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Erstellen von Diagnoseeinstellungen über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu Ihrem Azure Cache for Redis-Konto. Öffnen Sie den Bereich **Diagnoseeinstellungen** auf der linken Seite unter **Überwachungsabschnitt**. Wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="„Diagnose“ auswählen":::

1. Wählen Sie im Bereich **Diagnoseeinstellungen** die Option **ConnectedClientList** aus **Kategoriedetails** aus.

   |Category  | Definition  | Schlüsseleigenschaften   |
   |---------|---------|---------|
   |ConnectedClientList |  IP-Adressen und Anzahl der mit dem Cache verbundenen Clients, die in regelmäßigen Abständen protokolliert werden. | `connectedClients` und geschachtelt in: `ip`, `count`, `privateLinkIpv6` |
  
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
   | name | Der Name Ihrer Diagnoseeinstellung. |
   | resourceUri | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | api-version | 2017-05-01-preview |
   | Content-Type | Anwendung/json |

### <a name="body"></a>Text

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
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

Verwenden Sie den Befehl `az monitor diagnostic-settings create`, um eine Diagnoseeinstellung mit der Azure CLI zu erstellen. Weitere Informationen zu diesem Befehl und Parameterbeschreibungen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../azure-monitor/essentials/diagnostic-settings.md).

```azurecli

az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace


```

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md).