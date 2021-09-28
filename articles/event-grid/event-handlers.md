---
title: Azure Event Grid-Ereignishandler
description: Informationen zu unterstützten Ereignishandlern für Azure Event Grid Azure Automation, Functions, Event Hubs, Hybrid Connections, Logic Apps, Service Bus, Queue Storage, Webhooks.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: e741a3500c9b6a0ecda9a6dcc6e2d3514df45edc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659189"
---
# <a name="event-handlers-in-azure-event-grid"></a>Ereignishandler in Azure Event Grid
Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. Sie können aber auch einen beliebigen Webhook für die Behandlung von Ereignissen verwenden. Der Webhook muss zum Behandeln von Ereignissen nicht in Azure gehostet werden. Event Grid unterstützt nur HTTPS-Webhook-Endpunkte.

## <a name="supported-event-handlers"></a>Unterstützte Ereignishandler
Nachfolgend sind die unterstützten Ereignishandler aufgeführt: 

- [Webhooks](handler-webhooks.md). Azure Automation-Runbooks und Logik-Apps werden über Webhooks unterstützt. 
- [Azure-Funktionen](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [Service Bus-Warteschlangen und -Themen](handler-service-bus.md)
- [Relay-Hybridverbindungen](handler-relay-hybrid-connections.md)
- [Storage-Warteschlangen](handler-storage-queues.md)

## <a name="next-steps"></a>Nächste Schritte
- Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
