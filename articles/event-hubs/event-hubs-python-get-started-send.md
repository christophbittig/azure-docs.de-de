---
title: Senden oder Empfangen von Ereignissen an bzw. von Azure Event Hubs unter Verwendung von Python (aktuelles Paket)
description: Dieser Artikel enthält eine exemplarische Vorgehensweise zum Erstellen einer Python-Anwendung, die unter Verwendung des aktuellen Pakets „azure-eventhub“ Ereignisse an Azure Event Hubs sendet bzw. von dort empfängt.
ms.topic: quickstart
ms.date: 09/01/2021
ms.openlocfilehash: fb1a385b2ea434314d1cb74fec033b527206b159
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433870"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub"></a>Senden oder Empfangen von Ereignissen an bzw. von Event Hubs unter Verwendung von Python („azure-eventhub“)
In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Python-Pakets **azure-eventhub** Ereignisse an einen Event Hub senden bzw. von dort empfangen.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie mit Azure Event Hubs noch nicht vertraut sind, lesen Sie vor dem Durcharbeiten dieser Schnellstartanleitung die Informationen unter [Übersicht über Event Hubs](event-hubs-about.md). 

Zum Durchführen dieser Schnellstartanleitung benötigen Sie Folgendes:

- **Microsoft Azure-Abonnement.** Für die Verwendung von Azure-Diensten benötigen Sie ein Abonnement. Das gilt auch für Azure Event Hubs.  Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/free/) registrieren oder beim [Erstellen eines Kontos](https://azure.microsoft.com) Ihre MSDN-Abonnentenvorteile nutzen.
- Python 2.7 bzw. 3.6 oder höher mit installiertem und aktualisiertem PIP
- Das Python-Paket für Event Hubs. 

    Um das Paket zu installieren, führen Sie diesen Befehl in einer Eingabeaufforderung aus, in deren Pfad Python enthalten ist:

    ```cmd
    pip install azure-eventhub
    ```

    Installieren Sie das folgende Paket, um die Ereignisse mithilfe von Azure Blob Storage als Prüfpunktspeicher zu empfangen:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Erstellen Sie einen Event Hubs-Namespace und einen Event Hub**. Verwenden Sie zunächst das [Azure-Portal](https://portal.azure.com), um einen Namespace vom Typ „Event Hubs“ zu erstellen, und beschaffen Sie die Verwaltungsanmeldeinformationen, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. Gehen Sie dann wie im folgenden Artikel beschrieben vor, um die **Verbindungszeichenfolge für den Event Hubs-Namespace** abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Sie verwenden die Verbindungszeichenfolge im weiteren Verlauf dieser Schnellstartanleitung.

## <a name="send-events"></a>Senden von Ereignisse
In diesem Abschnitt erstellen Sie ein Python-Skript, um Ereignisse an den Event Hub zu senden, den Sie zuvor erstellt haben.

1. Öffnen Sie Ihren bevorzugten Python-Editor, z.B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie ein Skript mit dem Namen *send.py*. Dieses Skript sendet einen Batch von Ereignissen an den Event Hub, den Sie zuvor erstellt haben.
3. Fügen Sie den folgenden Code in *send.py* ein:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
        # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Der gesamte Quellcode (einschließlich hilfreicher Kommentare) steht auf der [GitHub-Seite „send_async.py“](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py) zur Verfügung.
    

## <a name="receive-events"></a>Empfangen von Ereignissen
In dieser Schnellstartanleitung wird Azure Blob Storage als Prüfpunktspeicher verwendet. Der Prüfpunktspeicher wird verwendet, um Prüfpunkte (d. h. die letzte Leseposition) beizubehalten.  


> [!WARNING]
> Wenn Sie diesen Code in Azure Stack Hub ausführen, treten Laufzeitfehler auf, es sei denn, Sie verwenden eine bestimmte Storage-API-Version als Ziel. Dies liegt daran, dass das Event Hubs SDK die neueste verfügbare Azure Storage API verwendet, die in Azure verfügbar ist und auf Ihrer Azure Stack Hub-Plattform möglicherweise nicht verfügbar ist. Azure Stack Hub unterstützt möglicherweise eine andere Storage Blob SDK-Version als die üblicherweise in Azure verfügbaren SDKs. Wenn Sie Azure Blob Storage als Prüfpunktspeicher verwenden, überprüfen Sie die [unterstützte Azure Storage-API-Version für Ihren Azure Stack Hub-Build](/azure-stack/user/azure-stack-acs-differences?#api-version), und verwenden Sie diese Version im Code als Ziel. 
>
> Wenn Sie z. B. die Azure Stack Hub-Version 2005 verwenden, ist die Version 2019-02-02 die höchste verfügbare Version für den Storage-Dienst. Standardmäßig verwendet die Clientbibliothek des Event Hubs SDK die höchste verfügbare Version in Azure (2019-07-07 zum Zeitpunkt der Veröffentlichung des SDK). In diesem Fall müssen Sie neben den folgenden Schritten in diesem Abschnitt auch Code für die API-Version 2019-02-02 des Storage-Diensts hinzufügen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie in den [synchronen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) und [asynchronen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) Beispielen auf GitHub. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Erstellen eines Azure-Speicherkontos und eines Blobcontainers
Führen Sie die folgenden Schritte aus, um ein Azure-Speicherkonto und einen darin befindlichen Blobcontainer zu erstellen:

1. [Erstellen Sie ein Azure Storage-Konto.](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Erstellen eines Blobcontainers](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Abrufen der Verbindungszeichenfolge für das Speicherkonto](../storage/common/storage-configure-connection-string.md)

Notieren Sie sich die Verbindungszeichenfolge und den Containernamen zur späteren Verwendung im Empfangscode.


### <a name="create-a-python-script-to-receive-events"></a>Erstellen eines Python-Skripts zum Empfangen von Ereignissen

In diesem Abschnitt erstellen Sie ein Python-Skript, um Ereignisse aus ihrem Event Hub zu empfangen:

1. Öffnen Sie Ihren bevorzugten Python-Editor, z.B. [Visual Studio Code](https://code.visualstudio.com/).
2. Erstellen Sie ein Skript mit dem Namen *recv.py*.
3. Fügen Sie den folgenden Code in *recv.py* ein:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Der gesamte Quellcode (einschließlich zusätzlicher hilfreicher Kommentare) steht auf der [GitHub-Seite „recv_with_checkpoint_store_async.py“](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py) zur Verfügung.


### <a name="run-the-receiver-app"></a>Ausführen der Empfänger-App

Um das Skript auszuführen, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diesen Befehl aus:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Ausführen der Sender-App

Um das Skript auszuführen, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält, und führen Sie dann diesen Befehl aus:

```bash
python send.py
```

Die Nachrichten, die an den Event Hub gesendet wurden, sollten im Empfängerfenster angezeigt werden.


## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie Ereignisse asynchron gesendet und empfangen. Beispiele zum synchronen Senden und Empfangen von Ereignissen finden Sie auf der [GitHub-Seite „sync_samples“](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Alle (synchronen und asynchronen) Beispiele auf GitHub finden Sie in der [Azure Event Hubs-Clientbibliothek für Python-Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
