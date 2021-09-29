---
title: Event Hub als Ereignishandler für Azure Event Grid-Ereignisse
description: Hier wird beschrieben, wie Sie Event Hubs als Ereignishandler für Azure Event Grid-Ereignisse verwenden können.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 6ba4dec31a93b01804779cd9f614e64854fe6cce
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215033"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event Hub als Ereignishandler für Azure Event Grid-Ereignisse
Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses eine Maßnahme. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. **Azure Event Hubs** ist einer dieser Dienste. 

Verwenden Sie **Event Hubs**, wenn Ihre Lösung Ereignisse von Event Grid schneller abruft, als die Ereignisse verarbeitet werden können. Sobald sich die Ereignisse in einem Event Hub befinden, kann Ihre Anwendung Ereignisse aus dem Event Hub gemäß einem eigenen Zeitplan verarbeiten. Sie können die Ereignisverarbeitung in Anpassung an die eingehenden Ereignisse skalieren.

## <a name="tutorials"></a>Tutorials
Hierzu folgende Beispiele: 

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Weiterleiten benutzerdefinierter Ereignisse an Azure Event Hubs mit der Azure CLI und Event Grid](custom-event-to-eventhub.md) | Sendet ein benutzerdefiniertes Ereignis an einen Event Hub, damit es durch eine Anwendung verarbeitet wird. |
| [Resource Manager-Vorlage: Erstellen eines benutzerdefinierten Event Grid-Themas und Senden von Ereignissen an einen Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| Eine Resource Manager-Vorlage, mit der ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an Azure Event Hubs. |

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST-Beispiele (für PUT)


### <a name="event-hub"></a>Event Hub

```json
{
  "properties": 
  {
    "destination": 
    {
      "endpointType": "EventHub",
      "properties": 
      {
        "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event Hub: Bereitstellung mit verwalteter Identität

```json
{
  "properties": {
    "deliveryWithResourceIdentity": 
    {
      "identity": 
      {
        "type": "SystemAssigned"
      },
      "destination": 
      {
        "endpointType": "EventHub",
        "properties": 
        {
          "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
        }
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Ereignishandler finden Sie im Artikel zu [Ereignishandlern](event-handlers.md). 
