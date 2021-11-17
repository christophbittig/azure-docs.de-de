---
title: Erste Schritte mit Azure Service Bus-Warteschlangen (Azure.Messaging.ServiceBus)
description: In diesem Tutorial erstellen Sie eine .NET Core-Anwendung in C#, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 10/11/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: c7e0ddee8e42f76c034ce79c7200e48fcb8bae04
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555905"
---
# <a name="get-started-with-azure-service-bus-queues-net"></a>Erste Schritte mit Azure Service Bus-Warteschlangen (.NET)
Diese Schnellstartanleitung umfasst folgende Schritte:

1. Erstellen eines Service Bus-Namespace mithilfe des Azure-Portals
2. Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals
3. Schreiben einer .NET Core-Konsolenanwendung, die eine Gruppe von Nachrichten an die Warteschlange sendet
4. Schreiben einer .NET Core-Konsolenanwendung, die diese Nachrichten aus der Warteschlange empfängt

> [!NOTE]
> In dieser Schnellstartanleitung wird Schritt für Schritt ein einfaches Szenario erläutert, bei dem Sie einen Batch von Nachrichten an eine Service Bus-Warteschlange senden und diese Nachrichten empfangen. Weitere vordefinierte .NET-Beispiele für Azure Service Bus finden Sie [auf GitHub im Repository mit dem Azure SDK für .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples). 

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie mit dem Dienst noch keine Erfahrung haben, informieren Sie sich über den Abschnitt [Was ist Azure Service Bus?](service-bus-messaging-overview.md), bevor Sie die Schritte in dieser Schnellstartanleitung durchführen. 

- **Azure-Abonnement**. Für die Verwendung von Azure-Diensten benötigen Sie ein Abonnement. Das gilt auch für Azure Service Bus. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/free/) registrieren.
- **Microsoft Visual Studio 2019**. In der Azure Service Bus-Clientbibliothek werden neue Features genutzt, die in C# 8.0 eingeführt wurden.  Die Bibliothek kann zwar auch weiterhin mit älteren Versionen von C# verwendet werden, doch die neue Syntax steht dann nicht zur Verfügung. Wenn Sie die ganze Syntax nutzen möchten, sollten Sie mit dem .NET Core SDK 3.0 oder höher kompilieren, wobei die Sprachversion auf `latest` festgelegt sein muss. Bei Verwendung von Visual Studio sind Versionen vor Visual Studio 2019 nicht mit den Tools kompatibel, die zum Erstellen von C# 8.0-Projekten erforderlich sind.


[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](./includes/service-bus-create-queue-portal.md)]


## <a name="send-messages-to-the-queue"></a>Senden von Nachrichten an die Warteschlange
In diesem Abschnitt erfahren Sie, wie Sie eine .NET Core-Konsolenanwendung zum Senden von Nachrichten an eine Service Bus-Warteschlange erstellen. 

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

1. Starten Sie Visual Studio 2019. 
1. Wählen Sie **Neues Projekt erstellen** aus. 
1. Führen Sie im Dialogfeld **Neues Projekt erstellen** die folgenden Schritte aus: Sollte dieses Dialogfeld nicht angezeigt werden, wählen Sie im Menü die Option **Datei** und anschließend **Neu** > **Projekt** aus. 
    1. Wählen Sie die Programmiersprache **C#** aus.
    1. Wählen Sie den Anwendungstyp **Konsole** aus. 
    1. Wählen Sie in der Ergebnisliste die Option **Konsolenanwendung** aus. 
    1. Klicken Sie anschließend auf **Weiter**. 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="Abbildung: Dialogfeld „Neues Projekt erstellen“, in dem „C#“ und „Konsole“ ausgewählt sind":::
1. Geben Sie als Projektname **QueueSender** und als Projektmappenname **ServiceBusQueueQuickStart** ein, und wählen Sie **Weiter** aus. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/project-solution-names.png" alt-text="Abbildung: Name für Projektmappe und Projekt im Dialogfeld „Neues Projekt konfigurieren“":::
1. Wählen Sie auf der Seite **Zusätzliche Informationen** die Option **Erstellen** aus, um Projektmappe und Projekt zu erstellen. 

### <a name="add-the-service-bus-nuget-package"></a>Hinzufügen des NuGet-Pakets "Service Bus"

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.ServiceBus** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-queue"></a>Hinzufügen von Code für das Senden von Nachrichten an die Warteschlange

1. Fügen Sie in **Program.cs** am Beginn der Namespacedefinition die folgenden `using`-Anweisungen vor der Klassendeklaration hinzu:

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. Deklarieren Sie in der `Program`-Klasse direkt vor der `Main`-Methode die folgenden statischen Eigenschaften. 

    Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die primäre Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<QUEUE NAME>` durch den Namen der Warteschlange.

    ```csharp

    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus queue
    static string queueName = "<QUEUE NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the sender used to publish messages to the queue
    static ServiceBusSender sender;

    // number of messages to be sent to the queue
    private const int numOfMessages = 3;

    ```
3. Ersetzen Sie den Code in der `Main`-Methode durch den folgenden Code. Ausführliche Informationen zum Code finden Sie in den Codekommentaren. Im Folgenden sind die wichtigsten Schritte aus dem Code aufgeführt.  
    1. Erstellen eines [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)-Objekts unter Verwendung der primären Verbindungszeichenfolge für den Namespace. 
    1. Ruft die [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender)-Methode für das [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)-Objekt auf, um ein [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender)-Objekt für die angegebene Service Bus-Warteschlange zu erstellen.     
    1. Erstellt ein [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch)-Objekt mithilfe der [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)-Methode.
    1. Hinzufügen von Nachrichten zum Batch mithilfe von [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). 
    1. Senden des Nachrichtenbatches an die Service Bus-Warteschlange mithilfe der [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync)-Methode

        ```csharp
        static async Task Main()
        {
            // The Service Bus client types are safe to cache and use as a singleton for the lifetime
            // of the application, which is best practice when messages are being published or read
            // regularly.
            //
            // Create the clients that we'll use for sending and processing messages.
            client = new ServiceBusClient(connectionString);
            sender = client.CreateSender(queueName);
    
            // create a batch 
            using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
    
            for (int i = 1; i <= numOfMessages; i++)
            {
                // try adding a message to the batch
                if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                {
                    // if it is too large for the batch
                    throw new Exception($"The message {i} is too large to fit in the batch.");
                }
            }
    
            try
            {
                // Use the producer client to send the batch of messages to the Service Bus queue
                await sender.SendMessagesAsync(messageBatch);
                Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
            }
            finally
            {
                // Calling DisposeAsync on client types is required to ensure that network
                // resources and other unmanaged objects are properly cleaned up.
                await sender.DisposeAsync();
                await client.DisposeAsync();
            }
    
            Console.WriteLine("Press any key to end the application");
            Console.ReadKey();
        }    
        ```    
1. Die Datei „Program.cs“ sollte nun wie folgt aussehen: 
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace QueueSender
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";

            // name of your Service Bus queue
            static string queueName = "<QUEUE NAME>";
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the sender used to publish messages to the queue
            static ServiceBusSender sender;
    
            // number of messages to be sent to the queue
            private const int numOfMessages = 3;
    
            static async Task Main()
            {
                // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                // of the application, which is best practice when messages are being published or read
                // regularly.
                //
                // Create the clients that we'll use for sending and processing messages.
                client = new ServiceBusClient(connectionString);
                sender = client.CreateSender(queueName);
    
                // create a batch 
                using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
    
                for (int i = 1; i <= numOfMessages; i++)
                {
                    // try adding a message to the batch
                    if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                    {
                        // if it is too large for the batch
                        throw new Exception($"The message {i} is too large to fit in the batch.");
                    }
                }
    
                try
                {
                    // Use the producer client to send the batch of messages to the Service Bus queue
                    await sender.SendMessagesAsync(messageBatch);
                    Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
                }
                finally
                {
                    // Calling DisposeAsync on client types is required to ensure that network
                    // resources and other unmanaged objects are properly cleaned up.
                    await sender.DisposeAsync();
                    await client.DisposeAsync();
                }
    
                Console.WriteLine("Press any key to end the application");
                Console.ReadKey();
            }
        }
    }   
    ``` 
1. Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die primäre Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<QUEUE NAME>` durch den Namen der Warteschlange.
1. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind. 
1. Führen Sie das Programm aus, und warten Sie auf die Bestätigungsmeldung.
    
    ```bash
    A batch of 3 messages has been published to the queue
    ```
1. Gehen Sie im Azure-Portal wie folgt vor:
    1. Navigieren Sie zu Ihrem Service Bus-Namespace. 
    1. Wählen Sie auf der Seite **Übersicht** die Warteschlange unten im mittleren Bereich aus. 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/select-queue.png" alt-text="Abbildung: Seite „Service Bus-Namespace“ im Azure-Portal, auf der die Warteschlange ausgewählt ist" lightbox="./media/service-bus-dotnet-get-started-with-queues/select-queue.png":::
    1. Beachten Sie die Werte im Abschnitt **Essentials**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Abbildung: Anzahl der empfangenen Nachrichten und Größe der Warteschlange" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Achten Sie auf die folgenden Werte:
    - Unter **Anzahl aktiver Nachrichten** ist für die Warteschlange nun der Wert **3** angegeben. Jedes Mal, wenn Sie diese Absender-App ausführen, ohne die Nachrichten abzurufen, erhöht sich dieser Wert um 3.
    - Die **aktuelle Größe** der Warteschlange nimmt jedes Mal inkrementell zu, wenn der Warteschlange durch die App eine Nachricht hinzugefügt wird.
    - Im unteren Abschnitt **Metriken** im Diagramm **Nachrichten** können Sie sehen, dass für die Warteschlange drei eingehende Nachrichten vorhanden sind. 

## <a name="receive-messages-from-the-queue"></a>Empfangen von Nachrichten aus der Warteschlange
In diesem Abschnitt erstellen Sie eine .NET Core-Konsolenanwendung für den Empfang von Nachrichten von der Warteschlange. 

### <a name="create-a-project-for-the-receiver"></a>Erstellen eines Projekts für den Empfänger

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ServiceBusQueueQuickStart**, zeigen Sie auf **Hinzufügen**, und wählen Sie anschließend **Neues Projekt** aus. 
1. Wählen Sie zuerst **Konsolenanwendung**, dann **Weiter** aus. 
1. Geben Sie unter **Projektname** den Namen **QueueReceiver** ein, und wählen Sie **Erstellen** aus. 
1. Klicken Sie im Fenster **Projektmappen-Explorer** mit der rechten Maustaste auf **QueueReceiver**, und wählen Sie **Als Startprojekt festlegen** aus. 

### <a name="add-the-service-bus-nuget-package"></a>Hinzufügen des NuGet-Pakets "Service Bus"

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Bestätigen Sie im Fenster **Paket-Manager-Konsole**, dass **QueueReceiver** als **Standardprojekt** ausgewählt wurde. Ist dies nicht der Fall, wählen Sie in der Dropdownliste **QueueReceiver** aus.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/package-manager-console.png" alt-text="Screenshot des ausgewählten QueueReceiver-Projekts in der Paket-Manager-Konsole":::
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.ServiceBus** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-the-code-to-receive-messages-from-the-queue"></a>Hinzufügen von Code für den Empfang von Nachrichten aus der Warteschlange
In diesem Abschnitt fügen Sie Code hinzu, mit dem Nachrichten aus der Warteschlange abgerufen werden.

1. Fügen Sie in **Program.cs** am Beginn der Namespacedefinition die folgenden `using`-Anweisungen vor der Klassendeklaration hinzu:

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. Deklarieren Sie in der `Program`-Klasse direkt vor der `Main`-Methode die folgenden statischen Eigenschaften. 

    Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die primäre Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<QUEUE NAME>` durch den Namen der Warteschlange.

    ```csharp
    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus queue
    static string queueName = "<QUEUE NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the processor that reads and processes messages from the queue
    static ServiceBusProcessor processor;
    ```
3. Fügen Sie der `Program`-Klasse die folgenden Methoden hinzu, mit denen empfangene Nachrichten und Fehler verarbeitet werden. 

    ```csharp
    // handle received messages
    static async Task MessageHandler(ProcessMessageEventArgs args)
    {
        string body = args.Message.Body.ToString();
        Console.WriteLine($"Received: {body}");

        // complete the message. messages is deleted from the queue. 
        await args.CompleteMessageAsync(args.Message);
    }

    // handle any errors when receiving messages
    static Task ErrorHandler(ProcessErrorEventArgs args)
    {
        Console.WriteLine(args.Exception.ToString());
        return Task.CompletedTask;
    }
    ```
4. Ersetzen Sie den Code in der `Main`-Methode durch den folgenden Code. Ausführliche Informationen zum Code finden Sie in den Codekommentaren. Im Folgenden sind die wichtigsten Schritte aus dem Code aufgeführt. 
    1. Erstellen eines [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)-Objekts unter Verwendung der primären Verbindungszeichenfolge für den Namespace. 
    1. Ruft die [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor)-Methode für das [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)-Objekt zum Erstellen eines [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)-Objekts für die angegebene Service Bus-Warteschlange auf. 
    1. Gibt Handler für die Ereignisse [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) und [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) des [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)-Objekts an. 
    1. Startet die Verarbeitung von Nachrichten durch Aufrufen von [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) für das [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)-Objekt. 
    1. Ruft [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) für das [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)-Objekt auf, wenn der Benutzer eine Taste zum Beenden der Verarbeitung drückt. 

        Weitere Informationen finden Sie in den Codekommentaren.

        ```csharp
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
        
                    // Create the client object that will be used to create sender and receiver objects
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
        
                    try
                    {
                        // add handler to process messages
                        processor.ProcessMessageAsync += MessageHandler;
        
                        // add handler to process any errors
                        processor.ProcessErrorAsync += ErrorHandler;
        
                        // start processing 
                        await processor.StartProcessingAsync();
        
                        Console.WriteLine("Wait for a minute and then press any key to end the processing");
                        Console.ReadKey();
        
                        // stop processing 
                        Console.WriteLine("\nStopping the receiver...");
                        await processor.StopProcessingAsync();
                        Console.WriteLine("Stopped receiving messages");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await processor.DisposeAsync();
                        await client.DisposeAsync();
                    }
                }
        ```
4. `Program.cs` sollte wie folgt aussehen:  

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace QueueReceiver
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
            // name of your Service Bus queue
            static string queueName = "<QUEUE NAME>";
    
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the processor that reads and processes messages from the queue
            static ServiceBusProcessor processor;
    
            // handle received messages
            static async Task MessageHandler(ProcessMessageEventArgs args)
            {
                string body = args.Message.Body.ToString();
                Console.WriteLine($"Received: {body}");
    
                // complete the message. messages is deleted from the queue. 
                await args.CompleteMessageAsync(args.Message);
            }
    
            // handle any errors when receiving messages
            static Task ErrorHandler(ProcessErrorEventArgs args)
            {
                Console.WriteLine(args.Exception.ToString());
                return Task.CompletedTask;
            }
    
            static async Task Main()
            {
                // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                // of the application, which is best practice when messages are being published or read
                // regularly.
                //
    
                // Create the client object that will be used to create sender and receiver objects
                client = new ServiceBusClient(connectionString);
    
                // create a processor that we can use to process the messages
                processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
    
                try
                {
                    // add handler to process messages
                    processor.ProcessMessageAsync += MessageHandler;
    
                    // add handler to process any errors
                    processor.ProcessErrorAsync += ErrorHandler;
    
                    // start processing 
                    await processor.StartProcessingAsync();
    
                    Console.WriteLine("Wait for a minute and then press any key to end the processing");
                    Console.ReadKey();
    
                    // stop processing 
                    Console.WriteLine("\nStopping the receiver...");
                    await processor.StopProcessingAsync();
                    Console.WriteLine("Stopped receiving messages");
                }
                finally
                {
                    // Calling DisposeAsync on client types is required to ensure that network
                    // resources and other unmanaged objects are properly cleaned up.
                    await processor.DisposeAsync();
                    await client.DisposeAsync();
                }
            }
        }
    }
    ```
1. Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die primäre Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<QUEUE NAME>` durch den Namen der Warteschlange. 
1. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind.
1. Führen Sie die Empfängeranwendung aus. Nun sollten die empfangenen Nachrichten angezeigt werden. Drücken Sie eine beliebige Taste, um Empfänger und Anwendung zu beenden. 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1
    Received: Message 2
    Received: Message 3
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. Sehen Sie sich erneut die Informationen im Portal an. Warten Sie einige Minuten, und aktualisieren Sie die Seite, wenn für **Aktive Nachrichten** nicht `0` angezeigt wird. 

    - Die Werte für **Anzahl aktiver Nachrichten** und **Aktuelle Größe** lauten jetzt **0**.
    - Im Diagramm **Nachrichten** im unteren Abschnitt **Metriken** können Sie sehen, dass für die Warteschlange drei eingehende und drei ausgehende Nachrichten vorhanden sind. 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Aktive Nachrichten und Größe nach dem Empfang" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen:

- [Azure Service Bus-Clientbibliothek für .NET: Infodatei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Beispiele bei GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET-API-Referenz](/dotnet/api/azure.messaging.servicebus)
