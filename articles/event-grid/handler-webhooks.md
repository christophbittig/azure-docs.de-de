---
title: Webhooks als Ereignishandler für Azure Event Grid-Ereignisse
description: Hier wird beschrieben, wie Sie Webhooks als Ereignishandler für Azure Event Grid-Ereignisse verwenden können. Azure Automation-Runbooks und Logik-Apps werden über Webhooks als Ereignishandler unterstützt.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 4f790c6a4ced30d0a0e34cb594581aae09ed1004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550472"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooks, Automation-Runbooks, Logik-Apps als Ereignishandler für Azure Event Grid-Ereignisse
Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. Sie können aber auch einen beliebigen WebHook für die Behandlung von Ereignissen verwenden. Der WebHook muss zum Behandeln von Ereignissen nicht in Azure gehostet werden. Event Grid unterstützt nur HTTPS-Webhook-Endpunkte.

> [!NOTE]
> - Azure Automation-Runbooks und Logik-Apps werden über Webhooks als Ereignishandler unterstützt. 
> - Sie können **Webhook** zwar als **Endpunkttyp** zum Konfigurieren einer Azure-Funktion als Ereignishandler verwenden, Sie sollten jedoch **Azure-Funktion** als Endpunkttyp verwenden. Weitere Informationen finden Sie unter [Azure-Funktion als Ereignishandler](handler-functions.md).

## <a name="webhooks"></a>webhooks
In den folgenden Artikeln finden Sie eine Übersicht und Beispiele für die Verwendung von Webhooks als Ereignishandler: 

|Titel  |BESCHREIBUNG  |
|---------|---------|
| Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) und dem [Portal](custom-event-quickstart-portal.md) | Zeigt das Senden von benutzerdefinierten Ereignissen an einen WebHook. |
| Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) und dem [Portal](blob-event-quickstart-portal.md) | Zeigt das Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Schnellstart: Senden von Container Registry-Ereignissen](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure CLI zum Senden von Container Registry-Ereignissen. |
| [Übersicht: Empfangen von Ereignissen an einem HTTP-Endpunkt](receive-events.md) | Beschreibt, wie Sie einen HTTP-Endpunkt überprüfen, um Ereignisse von einem Ereignisabonnement zu empfangen, und Ereignisse empfangen und deserialisieren. |


## <a name="azure-automation"></a>Azure-Automatisierung
Ereignisse können mit Azure Automation-Runbooks verarbeitet werden. Die Verarbeitung von Ereignissen mithilfe automatisierter Runbooks wird über Webhooks unterstützt. Sie erstellen einen Webhook für das Runbook und verwenden dann den Webhookhandler. Ein Beispiel finden Sie im folgenden Tutorial: 

|Titel  |BESCHREIBUNG  |
|---------|---------|
|[Tutorial: Azure Automation mit Event Grid und Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Erstellen Sie einen virtuellen Computer, der ein Ereignis gesendet. Das Ereignis löst ein Automation-Runbook, das den virtuellen Computer markiert, sowie eine Nachricht aus, die an einen Microsoft Teams-Kanal gesendet wird. |


## <a name="logic-apps"></a>Logic Apps
Verwenden Sie **Logic Apps**, um Geschäftsprozesse zur Verarbeitung von Event Grid-Ereignissen zu implementieren. In diesem Szenario erstellen Sie nicht explizit einen Webhook. Der Webhook wird automatisch für Sie erstellt, wenn Sie die Logik-App für die Behandlung von Ereignissen aus Event Grid konfigurieren. Sehen Sie sich die Beispiele in den folgenden Tutorials an: 

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Überwachen von Änderungen an virtuellen Computern mit Azure Event Grid und Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Eine Logik-App überwacht die Änderungen an einem virtuellen Computer und sendet E-Mails zu diesen Änderungen. |
| [Tutorial: Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Eine Logik-App sendet jedes Mal eine E-Mail-Benachrichtigung, wenn Ihrer IoT Hub-Instanz ein Gerät hinzugefügt wird. |
| [Tutorial: Reagieren auf Azure Service Bus-Ereignisse, die über Azure Event Grid empfangen wurden, mithilfe von Azure Functions und Azure Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid sendet Nachrichten von einem Service Bus-Thema an eine Funktions-App und an eine Logik-App. |

## <a name="rest-example-for-put"></a>REST-Beispiel (für PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Ereignishandler finden Sie im Artikel zu [Ereignishandlern](event-handlers.md). 
