---
title: Event Hub als Ereignishandler für Azure Event Grid-Ereignisse
description: Hier wird beschrieben, wie Sie Event Hubs als Ereignishandler für Azure Event Grid-Ereignisse verwenden können.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 590364d759fe6f628cfaa306d162d00f575c0622
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354431"
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

## <a name="delivery-properties"></a>Übermittlungseigenschaften
Mit Ereignisabonnements können Sie HTTP-Header einrichten, die in übermittelte Ereignisse eingeschlossen werden. Diese Funktion ermöglicht es Ihnen, benutzerdefinierte Header festzulegen, die für ein Ziel erforderlich sind. Sie können benutzerdefinierte Header für die Ereignisse festlegen, die an Azure Event Hubs übermittelt werden.

Wenn Sie Ereignisse für eine bestimmte Partition innerhalb eines Event Hub veröffentlichen müssen, legen Sie die `ParitionKey`-Eigenschaft für Ihr Ereignisabonnement fest, um den Partitionsschlüssel zur Identifizierung der Event Hub-Zielpartition anzugeben.

| Headername | Headertyp |
| :-- | :-- |
|`PartitionKey` | statischen |

Weitere Informationen finden Sie unter [Benutzerdefinierte Übermittlungseigenschaften](delivery-properties.md). 

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Ereignishandler finden Sie im Artikel zu [Ereignishandlern](event-handlers.md). 
