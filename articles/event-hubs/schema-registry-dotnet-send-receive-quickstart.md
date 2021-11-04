---
title: Überprüfen des Schemas beim Senden und Empfangen von Ereignissen – AMQP und .NET
description: Dieser Artikel enthält eine exemplarische Vorgehensweise zum Erstellen einer .NET Core-Anwendung, die anhand der Schemaregistrierung Ereignisse an Azure Event Hubs sendet bzw. von dort empfängt.
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: d87fdaff91e46811319b259de01300fb2a70dff3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433859"
---
# <a name="validate-schema-when-sending-and-receiving-events---amqp-and-net"></a>Überprüfen des Schemas beim Senden und Empfangen von Ereignissen – AMQP und .NET 
In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der .NET-Bibliothek **Azure.Messaging.EventHubs** Ereignisse an einen Event Hub mit Schemaüberprüfung senden bzw. von dort empfangen. 

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie mit Azure Event Hubs noch nicht vertraut sind, lesen Sie vor dem Durcharbeiten dieser Schnellstartanleitung die Informationen unter [Übersicht über Event Hubs](event-hubs-about.md). 

Zum Durchführen dieser Schnellstartanleitung benötigen Sie Folgendes:
- Befolgen Sie die Anweisungen aus dem Schnellstart zum [Erstellen eines Event Hubs-Namespace und eines Event Hubs](event-hubs-create.md).
- Befolgen Sie die Anweisungen unter [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md), um eine Verbindungszeichenfolge für Ihren Event Hubs-Namespace abzurufen. Notieren Sie sich die folgenden Einstellungen, die Sie in der aktuellen Schnellstartanleitung verwenden werden:
    - Verbindungszeichenfolge für den Event Hubs-Namespace
    - Name des Event Hubs
- **Führen Sie den [.NET-Schnellstart](event-hubs-dotnet-standard-getstarted-send.md)** aus, um sich mit dem Senden und Empfangen von Ereignissen an bzw. von Event Hubs über .NET vertraut zu machen. Wenn Sie den .NET-Schnellstart bereits durchgeführt haben, können Sie diesen Schritt überspringen. 
- **Befolgen Sie die Anweisungen unter [Erstellen von Schemas mithilfe der Schemaregistrierung](create-schema-registry.md)** , um eine Schemagruppe und ein Schema zu erstellen. Befolgen Sie beim Erstellen eines Schemas die Anweisungen unter [Erstellen eines Schemas](#create-a-schema) im aktuellen Schnellstartartikel. 
- **Microsoft Visual Studio 2019**. Die Azure Event Hubs-Clientbibliothek nutzt neue Features, die in C# 8.0 eingeführt wurden.  Die Bibliothek kann zwar auch weiterhin mit älteren Versionen von C# verwendet werden, doch die neue Syntax steht dann nicht zur Verfügung. Wenn Sie die vollständige Syntax nutzen möchten, sollten Sie die Kompilierung mithilfe des [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 oder höher durchführen und die [Sprachversion](/dotnet/csharp/language-reference/configure-language-version#override-a-default) auf `latest` festlegen. Bei Verwendung von Visual Studio sind Versionen vor Visual Studio 2019 nicht mit den Tools kompatibel, die zum Erstellen von C# 8.0-Projekten erforderlich sind. Visual Studio 2019 (einschließlich der kostenlosen Community-Edition) kann [hier](https://visualstudio.microsoft.com/vs/) heruntergeladen werden.


## <a name="create-a-schema"></a>Erstellen eines Schemas 
1. Erstellen Sie im Schemaregistrierungsportal eine Schemagruppe namens **contoso-sg**. Verwenden Sie Avro als Serialisierungstyp, und geben Sie **Keine** für den Kompatibilitätsmodus an. 
1. Erstellen Sie in dieser Schemagruppe ein neues Avro-Schema mit dem Schemanamen ``Microsoft.Azure.Data.SchemaRegistry.example.Order``. Verwenden Sie den folgenden Schemainhalt. 

    ```json 
    {
      "namespace": "Microsoft.Azure.Data.SchemaRegistry.example",
      "type": "record",
      "name": "Order",
      "fields": [
        {
          "name": "id",
          "type": "string"
        },
        {
          "name": "amount",
          "type": "double"
        },
        {
          "name": "description",
          "type": "string"
        }
      ]
    } 
    ```

## <a name="produce-events-to-event-hubs-with-schema-validation"></a>Erstellen von Ereignissen für Event Hubs mit Schemaüberprüfung


### <a name="create-console-application-for-event-producer"></a>Erstellen einer Konsolenanwendung für den Ereignisersteller 

1. Starten Sie Visual Studio 2019. 
1. Wählen Sie **Neues Projekt erstellen** aus. 
1. Führen Sie im Dialogfeld **Neues Projekt erstellen** die folgenden Schritte aus: Sollte dieses Dialogfeld nicht angezeigt werden, wählen Sie im Menü die Option **Datei** und anschließend **Neu** > **Projekt** aus. 
    1. Wählen Sie die Programmiersprache **C#** aus.
    1. Wählen Sie den Anwendungstyp **Konsole** aus. 
    1. Wählen Sie in der Ergebnisliste die Option **Konsolenanwendung** aus. 
    1. Klicken Sie anschließend auf **Weiter**. 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="Abbildung: Dialogfeld „Neues Projekt“":::
1. Geben Sie **OrderProducer** als Projektnamen und **SRQuickStart** als Projektmappennamen ein, und klicken Sie anschließend auf **OK**, um das Projekt zu erstellen. 


### <a name="add-the-event-hubs-nuget-package"></a>Hinzufügen des Event Hubs-NuGet-Pakets

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.EventHubs** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. Authentifizieren Sie Erstelleranwendungen, um über Visual Studio eine Verbindung mit Azure herzustellen, wie [hier](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio) gezeigt.  

### <a name="code-generation-using-the-avro-schema"></a>Codegenerierung mithilfe des Avro-Schemas  
1. Sie können denselben Schemainhalt verwenden und die Avro-Schemadatei ``Order.avsc`` im Projekt „OrderProducer“ erstellen. 
1. Anschließend können Sie mithilfe dieser Schemadatei Code für .NET generieren. Sie können ein beliebiges externes Codegenerierungstool wie [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/) für die Codegenerierung verwenden. (Beispielsweise können Sie `` avrogen -s .\Order.avsc `` ausführen, um Code zu generieren.) 
1. Nachdem Sie Code generiert haben, sollten die entsprechenden C#-Typen in Ihrem Projekt verfügbar sein. Für das obige Avro-Schema werden die C#-Typen im Namespace ``Microsoft.Azure.Data.SchemaRegistry.example`` generiert. 


### <a name="write-code-to-serialize-and-send-events-to-the-event-hub"></a>Schreiben von Code zum Serialisieren und Senden von Ereignissen an den Event Hub 

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    using System.Threading.Tasks;
    ```
1. Sie können auch die generierten Typen importieren, die sich auf das Schema ``Order`` beziehen, wie unten gezeigt. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Fügen Sie der Klasse `Program` Konstanten für die Event Hubs-Verbindungszeichenfolge und den Namen des Event Hubs hinzu.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

    > [!NOTE]
    > Ersetzen Sie Platzhalterwerte durch die Verbindungszeichenfolge für Ihren Namespace, den Namen des Event Hubs und die Schemagruppe.
3. Fügen Sie der Klasse `Program` die folgende statische Eigenschaft hinzu. Informationen dazu finden Sie in den Codekommentaren. 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. Ersetzen Sie die Methode `Main` durch die folgende Methode vom Typ `async Main`. Weitere Einzelheiten finden Sie in den Codekommentaren. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName); 
    
            // Create a schema registry client that you can use to serialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();
            
            // Create a new order object using the generated type/class 'Order'. 
            var sampleOrder = new Order { id = "12345", amount = 55.99, description = "This is a sample order." };
    
            using var memoryStream = new MemoryStream();
            // Create an Avro object serializer using the Schema Registry client object. 
            var producerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = true });
    
            // Serialize events data to the memory stream object. 
            producerSerializer.Serialize(memoryStream, sampleOrder, typeof(Order), CancellationToken.None);
    
            byte[] _memoryStreamBytes;
            _memoryStreamBytes = memoryStream.ToArray();
    
            // Create event data with serialized data and add it to an event batch. 
            eventBatch.TryAdd(new EventData(_memoryStreamBytes));
    
            // Send serilized event data to event hub. 
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 1 order has been published.");
        }
    ```
5. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind.
6. Führen Sie das Programm aus, und warten Sie auf die Bestätigungsmeldung. 

    ```csharp
    A batch of 1 order has been published.
    ```
1. Im Azure-Portal können Sie überprüfen, ob der Event Hub die Ereignisse empfangen hat. Wechseln Sie im Abschnitt **Metriken** zur Ansicht **Nachrichten**. Aktualisieren Sie die Seite, um das Diagramm zu aktualisieren. Es kann einige Sekunden dauern, bis angezeigt wird, dass die Nachrichten empfangen wurden. 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="Abbildung: Seite im Azure-Portal zum Überprüfen, ob der Event Hub die Ereignisse empfangen hat" lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::




## <a name="consume-events-from-event-hubs-with-schema-validation"></a>Nutzen von Ereignissen aus Event Hubs mit Schemaüberprüfung
In diesem Abschnitt erfahren Sie, wie Sie eine .NET Core-Konsolenanwendung schreiben, die Ereignisse von einem Event Hub empfängt und die Schemaregistrierung zum Deserialisieren von Ereignisdaten verwendet. 


### <a name="create-consumer-application"></a>Erstellen einer Consumeranwendung

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **SRQuickStart**, zeigen Sie auf **Hinzufügen**, und wählen Sie anschließend **Neues Projekt** aus. 
1. Wählen Sie zuerst **Konsolenanwendung**, dann **Weiter** aus. 
1. Geben Sie unter **Projektname** den Namen **OrderConsumer** ein, and wählen Sie **Erstellen** aus. 
1. Klicken Sie im Fenster **Projektmappen-Explorer** mit der rechten Maustaste auf **OrderConsumer**, und wählen Sie **Als Startprojekt festlegen** aus. 

### <a name="add-the-event-hubs-nuget-package"></a>Hinzufügen des Event Hubs-NuGet-Pakets

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Bestätigen Sie im Fenster **Paket-Manager-Konsole**, dass **OrderConsumer** als **Standardprojekt** ausgewählt wurde. Ist dies nicht der Fall, wählen Sie **OrderConsumer** in der Dropdownliste aus.


1. Führen Sie den folgenden Befehl aus, um das erforderliche NuGet-Paket zu installieren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Messaging.EventHubs.Processor
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. Authentifizieren Sie Erstelleranwendungen, um über Visual Studio eine Verbindung mit Azure herzustellen, wie [hier](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio) gezeigt. 



### <a name="code-generation-using-the-avro-schema"></a>Codegenerierung mithilfe des Avro-Schemas  
1. Sie können denselben Schemainhalt verwenden und die Avro-Schemadatei ``Order.avsc`` im Projekt ``OrderProducer`` erstellen. 
1. Anschließend können Sie mithilfe dieser Schemadatei Code für .NET generieren. Zu diesem Zweck können Sie ein beliebiges externes Codegenerierungstool wie [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/) verwenden. (Beispielsweise können Sie `` avrogen -s .\Order.avsc `` ausführen, um Code zu generieren.) 
1. Nachdem Sie Code generiert haben, sollten die entsprechenden C#-Typen in Ihrem Projekt verfügbar sein. Für das obige Avro-Schema werden die C#-Typen im Namespace ``Microsoft.Azure.Data.SchemaRegistry.example`` generiert. 


### <a name="write-code-to-receive-events-and-deserialize-them-using-schema-registry"></a>Schreiben von Code zum Empfangen und Deserialisieren von Ereignissen mithilfe der Schemaregistrierung


1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Sie können auch die generierten Typen importieren, die sich auf das Schema ``Order`` beziehen, wie unten gezeigt. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Fügen Sie der Klasse `Program` Konstanten für die Event Hubs-Verbindungszeichenfolge und den Namen des Event Hubs hinzu.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

3. Fügen Sie der Klasse `Program` die folgenden statischen Eigenschaften hinzu. 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. Ersetzen Sie die Methode `Main` durch die folgende Methode vom Typ `async Main`. Weitere Einzelheiten finden Sie in den Codekommentaren. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. Fügen Sie nun die folgende Ereignishandlermethode hinzu, die Ereignisdeserialisierungslogik in der Schemaregistrierung umfasst. 
    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Create a schema registry client that you can use to deserialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Retrieve event data and convert it to a byte array. 
            byte[] _memoryStreamBytes = eventArgs.Data.Body.ToArray();
            using var consumerMemoryStream = new MemoryStream(_memoryStreamBytes);
    
            var consumerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = false });
            consumerMemoryStream.Position = 0;
    
            // Deserialize event data and create order object using schema registry. 
            Order sampleOrder = (Order)consumerSerializer.Deserialize(consumerMemoryStream, typeof(Order), CancellationToken.None);
            Console.WriteLine("Received - Order ID: " + sampleOrder.id);                 
    
            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);   
        }

    ```

 
1. Fügen Sie der Klasse nun die folgenden Fehlerhandlermethoden hinzu. 

    ```csharp
        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
  
    ```
1. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind.


6. Führen Sie die Empfängeranwendung aus. 
1. Daraufhin sollte eine Nachricht mit dem Hinweis angezeigt werden, dass die Ereignisse empfangen wurden. 

    ```bash
    Received - Order ID: 12345
    ```
    Bei diesen Ereignissen handelt es sich um die drei Ereignisse, die Sie zuvor durch Ausführen des Sendeprogramms an den Event Hub gesendet haben. 


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der [.NET-Clientbibliothek der Azure-Schemaregistrierung](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/schemaregistry/Azure.Data.SchemaRegistry). 
