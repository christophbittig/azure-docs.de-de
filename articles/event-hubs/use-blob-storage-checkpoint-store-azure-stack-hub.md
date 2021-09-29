---
title: Verwenden von Blob Storage als Prüfpunktspeicher in Azure Stack Hub
description: In diesem Artikel wird beschrieben, wie Sie Blob Storage als Prüfpunktspeicher in Event Hubs unter Azure Stack Hub verwenden.
ms.topic: how-to
ms.date: 09/28/2021
ms.openlocfilehash: e3d75d4cd85bbf4e1bc8fc6fd581d510c35b0cb4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213513"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub"></a>Verwenden von Blob Storage als Prüfpunktspeicher: Event Hubs unter Azure Stack Hub
Wenn Sie Azure Blob Storage als Prüfpunktspeicher in einer Umgebung verwenden, die eine andere Version des Storage Blob SDK unterstützt als diejenigen, die in der Regel in Azure verfügbar sind, müssen Sie Code verwenden, um die Version der Speicherdienst-API in die von dieser Umgebung unterstützte Version zu ändern. Wenn Sie z. B. [Event Hubs mit einer Azure Stack Hub-Version 2002](/azure-stack/user/event-hubs-overview) ausführen, ist die höchste verfügbare Version für den Speicherdienst Version 2017-11-09. In diesem Fall müssen Sie Code verwenden, um Version 2017-11-09 der Storage Service-API als Ziel zu nutzen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie in den folgenden Beispielen auf GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript/receiveEventsWithApiSpecificStorage.js) oder [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python: [Synchron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

Wenn Sie den Event Hubs-Empfänger ausführen, der Blob Storage als Prüfpunktspeicher verwendet, ohne die Version anzugeben, die Azure Stack Hub unterstützt, erhalten Sie die folgende Fehlermeldung:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Beispielfehlermeldung in Python
Für Python wird der Fehler `azure.core.exceptions.HttpResponseError` an den Fehlerhandler `on_error(partition_context, error)` von `EventHubConsumerClient.receive()` übergeben. Allerdings wird von der Methode `receive()` keine Ausnahme ausgelöst. `print(error)` gibt die folgenden Ausnahmeinformationen aus:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Die Protokollierung protokolliert zwei Warnungen wie die folgenden:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Nächste Schritte

Informationen zur Partitionierung und Prüfpunktausführung finden Sie im folgenden Artikel: [Ausgleichen der Partitionsauslastung über mehrere Instanzen der Anwendung hinweg](event-processor-balance-partition-load.md)
