---
title: Senden oder Empfangen von Ereignissen an bzw. von Azure Event Hubs unter Verwendung von JavaScript (aktuelles Paket)
description: Dieser Artikel enthält eine exemplarische Vorgehensweise zum Erstellen einer JavaScript-Anwendung, die unter Verwendung des aktuellen Pakets „azure/event-hubs“ Ereignisse an Azure Event Hubs sendet bzw. von dort empfängt.
ms.topic: quickstart
ms.date: 09/16/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7422c10060d59bf7194cfb941061e8c5bc2b82cf
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273986"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>Senden oder Empfangen von Ereignissen an bzw. von Event Hubs unter Verwendung von JavaScript („azure/event-hubs“)
In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des JavaScript-Pakets **azure/event-hubs** Ereignisse an einen Event Hub senden bzw. von dort empfangen. 


## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie mit Azure Event Hubs noch nicht vertraut sind, lesen Sie vor dem Durcharbeiten dieser Schnellstartanleitung die Informationen unter [Übersicht über Event Hubs](event-hubs-about.md). 

Zum Durchführen dieser Schnellstartanleitung benötigen Sie Folgendes:

- **Microsoft Azure-Abonnement.** Für die Verwendung von Azure-Diensten benötigen Sie ein Abonnement. Das gilt auch für Azure Event Hubs.  Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/free/) registrieren oder beim [Erstellen eines Kontos](https://azure.microsoft.com) Ihre MSDN-Abonnentenvorteile nutzen.
- Node.js ab Version 8.x. Die aktuelle Version mit langfristigem Support (Long-Term Support, LTS) steht [hier](https://nodejs.org) zum Download bereit.  
- Visual Studio Code (empfohlen) oder eine andere integrierte Entwicklungsumgebung (Integrated Development Environment, IDE)  
- Ein aktiver Event Hubs-Namespace und ein aktiver Event Hub. Diese können Sie wie folgt erstellen: 

   1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) einen Namespace vom Typ *Event Hubs*, und rufen Sie anschließend die Verwaltungsanmeldeinformationen ab, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. 
   1. Eine Anleitung zum Erstellen des Namespace und des Event Hubs finden Sie unter [Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals](event-hubs-create.md).
   1. Fahren Sie mit den Schritten in dieser Schnellstartanleitung fort. 
   1. Eine Anleitung zum Abrufen der Verbindungszeichenfolge für Ihren Event Hub-Namespace finden Sie unter [Abrufen der Verbindungszeichenfolge im Portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Notieren Sie sich die Verbindungszeichenfolge zur späteren Verwendung in dieser Schnellstartanleitung.
- **Erstellen Sie einen Event Hubs-Namespace und einen Event Hub**. Verwenden Sie zunächst das [Azure-Portal](https://portal.azure.com), um einen Namespace vom Typ „Event Hubs“ zu erstellen, und beschaffen Sie die Verwaltungsanmeldeinformationen, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. Gehen Sie dann wie im folgenden Artikel beschrieben vor, um die **Verbindungszeichenfolge für den Event Hubs-Namespace** abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Sie verwenden die Verbindungszeichenfolge im weiteren Verlauf dieser Schnellstartanleitung.

### <a name="install-the-npm-package"></a>Installieren des npm-Pakets
Öffnen Sie eine Eingabeaufforderung, die *npm* im Pfad enthält, ändern Sie das Verzeichnis in den Ordner, in dem die Beispiele gespeichert werden sollen, und führen Sie dann den folgenden Befehl aus, um das [npm-Paket für Event Hubs](https://www.npmjs.com/package/@azure/event-hubs) zu installieren:

```shell
npm install @azure/event-hubs
```

Für die empfangende Seite müssen zwei weitere Pakete installiert werden. In dieser Schnellstartanleitung wird Azure Blob Storage zum Speichern von Prüfpunkten verwendet, damit das Programm die bereits gelesenen Ereignisse nicht erneut liest. In einem Blob werden für empfangene Nachrichten in regelmäßigen Abständen Metadatenprüfpunkte erstellt. Dadurch kann der Empfang von Nachrichten später problemlos an der Stelle fortgesetzt werden, an der Sie aufgehört haben.

Führen Sie die folgenden Befehle aus:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Senden von Ereignisse

In diesem Abschnitt wird eine JavaScript-Anwendung erstellt, die Ereignisse an einen Event Hub sendet.

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com).
1. Erstellen Sie eine Datei namens *send.js*, und fügen Sie den folgenden Code ein:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. Ersetzen Sie im Code Folgendes durch echte Werte:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Führen Sie `node send.js` aus, um diese Datei auszuführen. Durch diesen Befehl wird ein Batch mit drei Ereignissen an Ihren Event Hub gesendet.
1. Vergewissern Sie sich im Azure-Portal, dass der Event Hub die Nachrichten erhalten hat. Wechseln Sie im Abschnitt **Metriken** zur Ansicht **Nachrichten**. Aktualisieren Sie die Seite, um das Diagramm zu aktualisieren. Es kann einige Sekunden dauern, bis der Empfang der Nachrichten angezeigt wird.

    [![Überprüfen, ob der Event Hub die Nachrichten empfangen hat](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Der gesamte Quellcode (einschließlich zusätzlicher hilfreicher Kommentare) steht auf der [GitHub-Seite „sendEvents.js“](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/event-hubs/samples/v5/javascript/sendEvents.js) zur Verfügung.

Glückwunsch! Sie haben jetzt Ereignisse an einen Event Hub gesendet.


## <a name="receive-events"></a>Empfangen von Ereignissen
In diesem Abschnitt werden mithilfe eines Azure Blob Storage-Prüfpunkts in einer JavaScript-Anwendung Ereignisse von einem Event Hub empfangen. In einem Azure Storage-Blob werden für empfangene Nachrichten in regelmäßigen Abständen Metadatenprüfpunkte erstellt. Dadurch kann der Empfang von Nachrichten später problemlos an der Stelle fortgesetzt werden, an der Sie aufgehört haben.

> [!WARNING]
> Wenn Sie diesen Code in Azure Stack Hub ausführen, treten Laufzeitfehler auf, es sei denn, Sie verwenden eine bestimmte Storage-API-Version als Ziel. Dies liegt daran, dass das Event Hubs SDK die neueste verfügbare Azure Storage API verwendet, die in Azure verfügbar ist und auf Ihrer Azure Stack Hub-Plattform möglicherweise nicht verfügbar ist. Azure Stack Hub unterstützt möglicherweise eine andere Storage Blob SDK-Version als die üblicherweise in Azure verfügbaren SDKs. Wenn Sie Azure Blob Storage als Prüfpunktspeicher verwenden, überprüfen Sie die [unterstützte Azure Storage-API-Version für Ihren Azure Stack Hub-Build](/azure-stack/user/azure-stack-acs-differences?#api-version), und verwenden Sie diese Version im Code als Ziel. 
>
> Wenn Sie z. B. die Azure Stack Hub-Version 2005 verwenden, ist die Version 2019-02-02 die höchste verfügbare Version für den Storage-Dienst. Standardmäßig verwendet die Clientbibliothek des Event Hubs SDK die höchste verfügbare Version in Azure (2019-07-07 zum Zeitpunkt der Veröffentlichung des SDK). In diesem Fall müssen Sie neben den folgenden Schritten in diesem Abschnitt auch Code für die API-Version 2019-02-02 des Storage-Diensts hinzufügen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie auf GitHub in den Beispielen für [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript/receiveEventsWithApiSpecificStorage.js) und [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript/src/receiveEventsWithApiSpecificStorage.ts). 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Erstellen eines Azure-Speicherkontos und eines Blobcontainers
Führen Sie die folgenden Schritte aus, um ein Azure-Speicherkonto und einen darin befindlichen Blobcontainer zu erstellen:

1. [Erstellen eines Azure-Speicherkontos](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Erstellen eines Blobcontainers im Speicherkonto](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Abrufen der Verbindungszeichenfolge für das Speicherkonto](../storage/common/storage-configure-connection-string.md)

Notieren Sie sich die Verbindungszeichenfolge und den Containernamen zur späteren Verwendung im Empfangscode.

### <a name="write-code-to-receive-events"></a>Schreiben von Code zum Empfangen von Ereignissen

1. Öffnen Sie Ihren bevorzugten Editor, z. B. [Visual Studio Code](https://code.visualstudio.com).
1. Erstellen Sie eine Datei namens *receive.js*, und fügen Sie den folgenden Code ein:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            if (events.length === 0) {
              console.log(`No events received within wait time. Waiting for next interval`);
              return;
            }
          
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. Ersetzen Sie im Code die folgenden Werte durch echte Werte:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Führen Sie `node receive.js` an einer Eingabeaufforderung aus, um diese Datei auszuführen. Im Fenster sollten Meldungen zu empfangenen Ereignissen angezeigt werden.

    > [!NOTE]
    > Der gesamte Quellcode (einschließlich zusätzlicher hilfreicher Kommentare) steht auf der [GitHub-Seite „receiveEventsUsingCheckpointStore.js“](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript/receiveEventsUsingCheckpointStore.js) zur Verfügung.

Glückwunsch! Sie haben erfolgreich Ereignisse von Ihrem Event Hub empfangen. Das Empfängerprogramm empfängt Ereignisse von allen Partitionen der Standardconsumergruppe im Event Hub.

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich diese Beispiele auf GitHub an:

- [JavaScript samples](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/javascript) (JavaScript-Beispiele)
- [TypeScript-Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/typescript)
