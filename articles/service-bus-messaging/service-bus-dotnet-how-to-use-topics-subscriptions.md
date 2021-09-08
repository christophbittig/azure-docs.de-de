---
title: Erste Schritte mit Azure Service Bus-Themen und -Abonnements
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Nachrichten an Azure Service Bus-Themen senden, indem Sie das Paket „azure-messaging-servicebus“ verwenden.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/29/2021
ms.custom: contperf-fy22q1
ms.openlocfilehash: 05772a7e4fbb2717dd34cbdaf82ae47162d4f149
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451725"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-its-subscriptions-net"></a>Senden von Nachrichten an ein Azure Service Bus-Thema und Empfangen von Nachrichten von Abonnements (.NET)
In diesem Schnellstart erfahren Sie, wie mithilfe der .NET-Bibliothek [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) Nachrichten an ein Service Bus-Thema gesendet und von einem Abonnement dieses Themas empfangen werden.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie mit dem Dienst noch keine Erfahrung haben, informieren Sie sich über den Abschnitt [Was ist Azure Service Bus?](service-bus-messaging-overview.md), bevor Sie die Schritte in dieser Schnellstartanleitung durchführen. 

- **Azure-Abonnement**. Für die Verwendung von Azure-Diensten benötigen Sie ein Abonnement. Das gilt auch für Azure Service Bus.  Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/free/) registrieren oder beim [Erstellen eines Kontos](https://azure.microsoft.com) Ihre MSDN-Abonnentenvorteile nutzen.
- **Microsoft Visual Studio 2019**. In der Azure Service Bus-Clientbibliothek werden neue Features genutzt, die in C# 8.0 eingeführt wurden.  Die Bibliothek kann zwar auch weiterhin mit älteren Versionen von C# verwendet werden, doch die neue Syntax steht dann nicht zur Verfügung. Wenn Sie die ganze Syntax nutzen möchten, sollten Sie mit dem [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 oder höher kompilieren, wobei die [Sprachversion](/dotnet/csharp/language-reference/configure-language-version#override-a-default) auf `latest` festgelegt sein muss. Bei Verwendung von Visual Studio sind Versionen vor Visual Studio 2019 nicht mit den Tools kompatibel, die zum Erstellen von C# 8.0-Projekten erforderlich sind. Visual Studio 2019 (einschließlich der kostenlosen Community-Edition) kann [hier](https://visualstudio.microsoft.com/vs/) heruntergeladen werden.
- Befolgen Sie die Schritte in diesem [Schnellstart](service-bus-quickstart-topics-subscriptions-portal.md), um ein Service Bus-Thema und Abonnements für das Thema zu erstellen. 

    > [!IMPORTANT]
    > Notieren Sie die Verbindungszeichenfolge für den Namespace, den Themennamen und den Namen eines der Abonnements für das Thema. Sie benötigen beides später in diesem Tutorial.
 
## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
In diesem Abschnitt erfahren Sie, wie Sie eine .NET Core-Konsolenanwendung zum Senden von Nachrichten an ein Service Bus-Thema erstellen. 

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

1. Starten Sie Visual Studio 2019. 
1. Wählen Sie **Neues Projekt erstellen** aus. 
1. Führen Sie im Dialogfeld **Neues Projekt erstellen** die folgenden Schritte aus: Sollte dieses Dialogfeld nicht angezeigt werden, wählen Sie im Menü die Option **Datei** und anschließend **Neu** > **Projekt** aus. 
    1. Wählen Sie die Programmiersprache **C#** aus.
    1. Wählen Sie den Anwendungstyp **Konsole** aus. 
    1. Wählen Sie in der Ergebnisliste die Option **Konsolenanwendung** aus. 
    1. Klicken Sie anschließend auf **Weiter**. 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="Abbildung: Dialogfeld „Neues Projekt erstellen“, in dem „C#“ und „Konsole“ ausgewählt sind":::
1. Geben Sie als Projektnamen **TopicSender** und als Projektmappennamen **ServiceBusTopicQuickStart** ein, und wählen Sie dann **Weiter** aus. 
1. Wählen Sie auf der Seite **Zusätzliche Informationen** die Option **Erstellen** aus, um Projektmappe und Projekt zu erstellen.

### <a name="add-the-service-bus-nuget-package"></a>Hinzufügen des NuGet-Pakets "Service Bus"

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) zu installieren:

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-topic"></a>Hinzufügen von Code zum Senden von Nachrichten an das Thema 

1. Ersetzen Sie den Code in **Program.cs** durch den folgenden Code. Im Folgenden sind die wichtigsten Schritte aus dem Code aufgeführt.  
    1. Erstellen eines [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)-Objekts unter Verwendung der Verbindungszeichenfolge für den Namespace 
    1. Aufrufen der [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender)-Methode für das `ServiceBusClient`-Objekt, um ein [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender)-Objekt für das angegebene Service Bus-Thema zu erstellen     
    1. Erstellen eines [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch)-Objekts mithilfe von [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)
    1. Hinzufügen von Nachrichten zum Batch mithilfe von [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). 
    1. Senden des Nachrichtenbatches an das Service Bus-Thema mithilfe der Methode [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync)
    
        Weitere Informationen finden Sie in den Codekommentaren.

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace TopicSender
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of your Service Bus topic
                static string topicName = "<TOPIC NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the sender used to publish messages to the topic
                static ServiceBusSender sender;
        
                // number of messages to be sent to the topic
                private const int numOfMessages = 3;
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
                    sender = client.CreateSender(topicName);
        
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
                        // Use the producer client to send the batch of messages to the Service Bus topic
                        await sender.SendMessagesAsync(messageBatch);
                        Console.WriteLine($"A batch of {numOfMessages} messages has been published to the topic.");
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
1. Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie `<TOPIC NAME>` durch den Namen Ihres Service Bus-Themas. 
1. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind. 
1. Führen Sie das Programm aus, und warten Sie auf die Bestätigungsmeldung.
    
    ```bash
    A batch of 3 messages has been published to the topic
    ```
1. Gehen Sie im Azure-Portal wie folgt vor:
    1. Navigieren Sie zu Ihrem Service Bus-Namespace. 
    1. Wechseln Sie auf der Seite **Übersicht** unten im mittleren Bereich zur Registerkarte **Themen**, und wählen Sie das Service Bus-Thema aus. Im folgenden Beispiel ist dies `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Auswählen des Themas":::
    1. Auf der Seite **Service Bus-Thema** unten im Abschnitt **Metriken** können Sie im Diagramm **Nachrichten** sehen, dass für das Thema drei eingehende Nachrichten vorhanden sind. Wenn der Wert nicht angezeigt wird, sollten Sie einige Minuten warten und die Seite dann aktualisieren, damit das aktualisierte Diagramm angezeigt wird. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="An das Thema gesendete Nachrichten" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Wählen Sie im unteren Bereich das Abonnement aus. Im folgenden Beispiel ist dies **S1**. Auf der Seite **Service Bus-Abonnement** wird für **Anzahl aktiver Nachrichten** der Wert **3** angezeigt. Das Abonnement hat die drei Nachrichten empfangen, die Sie an das Thema gesendet haben, aber sie wurden noch nicht von einem Empfänger abgerufen. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Vom Abonnement empfangene Nachrichten" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    
## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
In diesem Abschnitt erstellen Sie eine .NET Core-Konsolenanwendung für den Empfang von Nachrichten aus dem Abonnement des Service Bus-Themas. 

### <a name="create-a-project-for-the-receiver"></a>Erstellen eines Projekts für den Empfänger

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe **ServiceBusTopicQuickStart**, zeigen Sie auf **Hinzufügen**, und wählen Sie anschließend **Neues Projekt** aus. 
1. Wählen Sie zuerst **Konsolenanwendung**, dann **Weiter** aus. 
1. Geben Sie **SubscriptionReceiver** als **Projektnamen** ein, und wählen Sie **Weiter** aus. 
1. Wählen Sie auf der Seite **Zusätzliche Informationen** die Option **Erstellen** aus. 
1. Klicken Sie im Fenster **Projektmappen-Explorer** mit der rechten Maustaste auf **SubscriptionReceiver**, und wählen Sie **Als Startprojekt festlegen** aus. 

### <a name="add-the-service-bus-nuget-package"></a>Hinzufügen des NuGet-Pakets "Service Bus"

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Vergewissern Sie sich im Fenster **Paket-Manager-Konsole**, dass **SubscriptionReceiver** als **Standardprojekt** ausgewählt wurde. Falls dies nicht der Fall ist, wählen Sie in der Dropdownliste **SubscriptionReceiver** aus.
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.ServiceBus** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-receive-messages-from-the-subscription"></a>Hinzufügen von Code für das Empfangen von Nachrichten aus dem Abonnement
1. Ersetzen Sie den Code in **Program.cs** durch den folgenden Code. Im Folgenden sind die wichtigsten Schritte aus dem Code aufgeführt.
    Hier sind die wesentlichen Schritte aus dem Code aufgeführt:
    1. Erstellen eines [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)-Objekts unter Verwendung der Verbindungszeichenfolge für den Namespace 
    1. Aufrufen der [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor)-Methode für das `ServiceBusClient`-Objekt zum Erstellen eines [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)-Objekts für die angegebene Service Bus-Warteschlange 
    1. Angeben von Handlern für die Ereignisse [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) und [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) des `ServiceBusProcessor`-Objekts 
    1. Starten der Verarbeitung von Nachrichten durch Aufrufen von [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) für das `ServiceBusProcessor`-Objekt 
    1. Aufrufen von [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) für das `ServiceBusProcessor`-Objekt, wenn der Benutzer eine Taste zum Beenden der Verarbeitung drückt 

        Weitere Informationen finden Sie in den Codekommentaren.

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace SubscriptionReceiver
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of the Service Bus topic
                static string topicName = "<SERVICE BUS TOPIC NAME>";
            
                // name of the subscription to the topic
                static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the processor that reads and processes messages from the subscription
                static ServiceBusProcessor processor;
        
                // handle received messages
                static async Task MessageHandler(ProcessMessageEventArgs args)
                {
                    string body = args.Message.Body.ToString();
                    Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");
        
                    // complete the message. messages is deleted from the subscription. 
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
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());
        
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
1. Ersetzen Sie die Platzhalter durch die richtigen Werte:
    - `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace
    - `<TOPIC NAME>` durch den Namen Ihres Service Bus-Themas
    - `<SERVICE BUS - TOPIC SUBSCRIPTION NAME>` durch den Namen des Abonnements für das Thema 
1. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind.
1. Führen Sie die Empfängeranwendung aus. Nun sollten die empfangenen Nachrichten angezeigt werden. Drücken Sie eine beliebige Taste, um Empfänger und Anwendung zu beenden. 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1 from subscription: S1
    Received: Message 2 from subscription: S1
    Received: Message 3 from subscription: S1
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. Sehen Sie sich erneut die Informationen im Portal an. 
    - Auf der Seite **Service Bus-Thema** werden im Diagramm **Nachrichten** drei ein- und drei ausgehende Nachrichten angezeigt. Falls diese Zahlen nicht angezeigt werden, sollten Sie einige Minuten warten und die Seite dann aktualisieren, damit der aktuelle Stand im Diagramm angezeigt wird. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Gesendete und empfangene Nachrichten" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
    - Auf der Seite **Service Bus-Abonnement** wird für **Anzahl aktiver Nachrichten** der Wert „0“ angezeigt. Dies liegt daran, dass ein Empfänger Nachrichten von diesem Abonnement empfangen und den Vorgang für die Nachrichten abgeschlossen hat. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="„Anzahl aktiver Nachrichten“ aufseiten des Abonnements" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
        


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen:

- [Azure Service Bus-Clientbibliothek für .NET: Infodatei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [.NET-Beispiele für Azure Service Bus auf GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET-API-Referenz](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
