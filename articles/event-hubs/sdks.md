---
title: 'Azure Event Hubs: Client-SDKs | Microsoft-Dokumentation'
description: In diesem Artikel erhalten Sie Informationen zu den Client-SDKs für Azure Event Hubs.
ms.topic: article
ms.date: 09/21/2021
ms.openlocfilehash: b1a802e5d9829f533187f477bb28037c22c02f3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215888"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs: Client-SDKs
In diesem Artikel erhalten Sie die folgenden Informationen zu von Azure Event Hubs unterstützten SDKs: 

- Speicherort der Pakete, die Sie in Ihren Anwendungen verwenden können 
- GitHub-Speicherort, an dem Sie Quellcode, Beispiele, Infodateien, Änderungsprotokolle, gemeldete Issues und Informationen dazu finden, wie neue Issues gemeldet werden 
- Links zu Schnellstarttutorials 

## <a name="client-sdks"></a>Client-SDKs
In der folgenden Tabelle werden alle aktuell verfügbaren Azure Event Hubs-Laufzeitclients beschrieben. Der Schwerpunkt dieser Bibliotheken liegt auf dem **Senden und Empfangen von Nachrichten** von einem Event Hub.

| Sprache | Paket | Verweis | 
| -------- | ------- | --------------- | 
| . NET Standard | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-event-hubs-go)</li><li>[Tutorial](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-event-hubs-c)</li><li>[Tutorial](event-hubs-c-getstarted-send.md)</li></ul> |

In der folgenden Tabelle sind ältere Azure Event Hubs-Laufzeitclients aufgeführt. Diese Pakete können zwar kritische Fehlerbehebungen erhalten, werden aber nicht aktiv weiterentwickelt. Wir empfehlen stattdessen die Verwendung der neuesten SDKs, die in der obigen Tabelle aufgeführt sind.

| Sprache | Paket | Verweis | 
| -------- | ------- | --------------- | 
| . NET Standard  | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) (**Legacy**) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) (**Legacy**) | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET-Framework | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) (**Legacy**) | |
|   Java   | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(Legacy)** | <ul><li>[GitHub-Speicherort](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |

## <a name="management-sdks"></a>Verwaltungs-SDKs
Hier finden Sie eine Liste der derzeit verfügbaren verwaltungsspezifischen Bibliotheken. Keine dieser Bibliotheken enthält Runtimevorgänge. Sie dienen ausschließlich zur **Verwaltung von Event Hubs-Entitäten**.

- [.NET Standard](/dotnet/api/microsoft.azure.management.eventhub)
- [Java](/java/api/com.microsoft.azure.management.eventhub)
- [Python](/python/api/azure-mgmt-eventhub)
- [JavaScript](/javascript/api/@azure/arm-eventhub/)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](./event-hubs-about.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.yml)
