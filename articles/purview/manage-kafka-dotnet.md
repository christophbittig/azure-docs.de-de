---
title: Veröffentlichen von Nachrichten in Atlas-Kafka-Themen von Azure Purview und Verarbeiten von Nachrichten aus diesen Themen über Event Hubs unter Verwendung von .NET
description: Dieser Artikel enthält eine exemplarische Vorgehensweise zum Erstellen einer .NET Core-Anwendung, die unter Verwendung des aktuellen Pakets „Azure.Messaging.EventHubs“ Ereignisse an Apache Atlas-Kafka-Themen von Purview sendet bzw. von dort empfängt.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 09/27/2021
ms.openlocfilehash: 670e7425c8b56732a32e1deb0b664bc6544eda0c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209621"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Veröffentlichen von Nachrichten in Atlas-Kafka-Themen von Azure Purview und Verarbeiten von Nachrichten aus diesen Themen über Event Hubs unter Verwendung von .NET 
In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der .NET-Bibliothek **Azure.Messaging.EventHubs** über Event Hub Ereignisse an Atlas-Kafka-Themen von Azure Purview senden bzw. von dort empfangen. 

> [!IMPORTANT]
> Ein verwalteter Event Hub wird im Rahmen der Purview-Kontoerstellung erstellt. Weitere Informationen finden Sie unter [Erstellen eines Purview-Kontos](create-catalog-portal.md). Sie können Nachrichten im Event Hub-Kafka-Thema ATLAS_HOOK veröffentlichen, und Purview verwendet und verarbeitet sie. Purview meldet Entitätsänderungen am Event Hub-Kafka-Thema ATLAS_ENTITIES, und Benutzer können dieses nutzen und verarbeiten. In dieser Schnellstartanleitung wird die neue Bibliothek **Azure.Messaging.EventHubs** verwendet. 


## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie mit Azure Event Hubs noch nicht vertraut sind, lesen Sie vor dem Durcharbeiten dieser Schnellstartanleitung die Informationen unter [Übersicht über Event Hubs](../event-hubs/event-hubs-about.md). 

Zum Durchführen dieser Schnellstartanleitung benötigen Sie Folgendes:

- **Microsoft Azure-Abonnement.** Für die Verwendung von Azure-Diensten benötigen Sie ein Abonnement. Das gilt auch für Azure Event Hubs.  Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/free/) registrieren oder beim [Erstellen eines Kontos](https://azure.microsoft.com) Ihre MSDN-Abonnentenvorteile nutzen.
- **Microsoft Visual Studio 2019**. Die Azure Event Hubs-Clientbibliothek nutzt neue Features, die in C# 8.0 eingeführt wurden.  Die Bibliothek kann zwar auch weiterhin mit älteren Versionen von C# verwendet werden, doch die neue Syntax steht dann nicht zur Verfügung. Um die vollständige Syntax nutzen zu können, wird empfohlen, dass Sie mit dem [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 oder höher kompilieren, wobei die [Sprachversion](/dotnet/csharp/language-reference/configure-language-version#override-a-default) auf `latest` festgelegt ist. Bei Verwendung von Visual Studio sind Versionen vor Visual Studio 2019 nicht mit den Tools kompatibel, die zum Erstellen von C# 8.0-Projekten erforderlich sind. Visual Studio 2019 (einschließlich der kostenlosen Community-Edition) kann [hier](https://visualstudio.microsoft.com/vs/) heruntergeladen werden.

## <a name="publish-messages-to-purview"></a>Veröffentlichen von Nachrichten in Purview 
In diesem Abschnitt erfahren Sie, wie Sie eine .NET Core-Anwendung zum Senden von Ereignissen an Purview über das Event Hub-Kafka-Thema **ATLAS_HOOK** erstellen. 

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

Erstellen Sie im nächsten Schritt in Visual Studio eine C# .NET-Konsolenanwendung:

1. Starten Sie **Visual Studio**.
2. Wählen Sie im Fenster „Start“ die Option **Neues Projekt erstellen** > **Konsolen-App (.NET Framework)** aus. .NET-Version 4.5.2 oder höher ist erforderlich.
3. Geben Sie unter **Projektname** die Zeichenfolge **PurviewKafkaProducer** ein.
4. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

1. Starten Sie Visual Studio 2019. 
1. Wählen Sie **Neues Projekt erstellen** aus. 
1. Führen Sie im Dialogfeld **Neues Projekt erstellen** die folgenden Schritte aus: Sollte dieses Dialogfeld nicht angezeigt werden, wählen Sie im Menü die Option **Datei** und anschließend **Neu** > **Projekt** aus. 
    1. Wählen Sie die Programmiersprache **C#** aus.
    1. Wählen Sie den Anwendungstyp **Konsole** aus. 
    1. Wählen Sie in der Ergebnisliste die Option **Konsolen-App (.NET Core)** aus. 
    1. Klicken Sie anschließend auf **Weiter**. 


### <a name="add-the-event-hubs-nuget-package"></a>Hinzufügen des Event Hubs-NuGet-Pakets

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.EventHubs** und das NuGet-Paket **Azure.Messaging.EventHubs.Producer** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Schreiben von Code zum Senden von Nachrichten an den Event Hub

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Fügen Sie der Klasse `Program` Konstanten für die Event Hubs-Verbindungszeichenfolge und den Event Hub-Namen hinzu. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Den dem Purview-Konto zugeordneten Event Hubs-Namespace erhalten Sie, indem Sie sich die primären/sekundären Verbindungszeichenfolgen des Atlas-Kafka-Endpunkts auf der Registerkarte „Eigenschaften“ des Purview-Kontos ansehen.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Event Hub-Namespace":::

    Zum Senden von Nachrichten an Purview muss der Event Hub-Name **ATLAS_HOOK** lauten.

3. Ersetzen Sie die `Main`-Methode durch die folgende `async Main`-Methode, und fügen Sie `async ProduceMessage` hinzu, um Nachrichten in Purview zu pushen. Weitere Einzelheiten finden Sie in den Codekommentaren. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind.
6. Führen Sie das Programm aus, und warten Sie auf die Bestätigungsmeldung. 

    > [!NOTE]
    > Den gesamten Quellcode mit weiteren informativen Kommentaren finden Sie [auf GitHub in dieser Datei](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md).

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>JSON-Beispielnachricht zum Erstellen einer Entität, um eine SQL-Tabelle mit zwei Spalten zu erstellen

```json
    
    {
    "msgCreatedBy":"nayenama",
    "message":{
        "type":"ENTITY_CREATE_V2",
        "user":"admin",
        "entities":{
            "entities":[
                {
                    "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "temporary":false,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table added via Kafka"
                    },
                    "relationshipAttributes":{
                        "columns":[
                            {
                                "guid":"-1102395743156037",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid":"-1102395743156038",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                    },
                    "guid":"-1102395743156036",
                    "version":0
                }
            ],
            "referredEntities":{
                "-1102395743156037":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                        "precision":23,
                        "length":8,
                        "description":"Sales Order ID",
                        "scale":3,
                        "name":"OrderID",
                        "data_type":"int"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156037",
                    "version":2
                },
                "-1102395743156038":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                        "description":"Sales Order Date",
                        "scale":3,
                        "name":"OrderDate",
                        "data_type":"datetime"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156038",
                    "status":"ACTIVE",
                    "createdBy":"ServiceAdmin",
                    "version":0
                }
            }
        }
    },
    "version":{
        "version":"1.0.0"
    },
    "msgCompressionKind":"NONE",
    "msgSplitIdx":1,
    "msgSplitCount":1
}


``` 

## <a name="consume-messages-from-purview"></a>Nutzen von Nachrichten aus Purview
In diesem Abschnitt erfahren Sie, wie Sie eine .NET Core-Konsolenanwendung schreiben, die unter Verwendung eines Ereignisprozessors Nachrichten von einem Event Hub empfängt. Sie müssen den Event Hub ATLAS_ENTITIES verwenden, um Nachrichten aus Purview zu empfangen. Der Ereignisprozessor vereinfacht das Empfangen von Ereignissen aus Event Hubs durch die Verwaltung persistenter Prüfpunkte und des parallelen Empfangs von diesen Event Hubs. 

> [!WARNING]
> Wenn Sie diesen Code in Azure Stack Hub ausführen, treten Laufzeitfehler auf, es sei denn, Sie verwenden eine bestimmte Storage-API-Version als Ziel. Dies liegt daran, dass das Event Hubs SDK die neueste verfügbare Azure Storage API verwendet, die in Azure verfügbar ist und auf Ihrer Azure Stack Hub-Plattform möglicherweise nicht verfügbar ist. Azure Stack Hub unterstützt möglicherweise eine andere Storage Blob SDK-Version als die üblicherweise in Azure verfügbaren SDKs. Wenn Sie Azure Blob Storage als Prüfpunktspeicher verwenden, überprüfen Sie die [unterstützte Azure Storage-API-Version für Ihren Azure Stack Hub-Build](/azure-stack/user/azure-stack-acs-differences?#api-version), und verwenden Sie diese Version im Code als Ziel. 
>
> Wenn Sie z. B. die Azure Stack Hub-Version 2005 verwenden, ist die Version 2019-02-02 die höchste verfügbare Version für den Storage-Dienst. Standardmäßig verwendet die Clientbibliothek des Event Hubs SDK die höchste verfügbare Version in Azure (2019-07-07 zum Zeitpunkt der Veröffentlichung des SDK). In diesem Fall müssen Sie neben den folgenden Schritten in diesem Abschnitt auch Code für die API-Version 2019-02-02 des Storage-Diensts hinzufügen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie in [diesem Beispiel auf GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Erstellen eines Azure Storage-Kontos und eines Blobcontainers
In dieser Schnellstartanleitung wird Azure Storage als Prüfpunktspeicher verwendet. Führen Sie die folgenden Schritte aus, um ein Azure Storage-Konto zu erstellen: 

1. [Erstellen Sie ein Azure Storage-Konto.](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Erstellen eines Blobcontainers](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Abrufen der Verbindungszeichenfolge für das Speicherkonto](../storage/common/storage-configure-connection-string.md)

    Notieren Sie sich die Verbindungszeichenfolge und den Containernamen. Diese Angaben werden im Empfangscode verwendet. 


### <a name="create-a-project-for-the-receiver"></a>Erstellen eines Projekts für den Empfänger

1. Klicken Sie im Projektmappen-Explorer auf die Projektmappe **EventHubQuickStart** und halten Sie die Maustaste gedrückt (oder klicken Sie mit der rechten Maustaste darauf), zeigen Sie auf **Hinzufügen**, und wählen Sie anschließend **Neues Projekt** aus. 
1. Wählen Sie **Konsolen-App (.NET Core)** und anschließend **Weiter** aus. 
1. Geben Sie unter **Projektname** den Namen **PurviewKafkaConsumer** ein, and wählen Sie **Erstellen** aus. 

### <a name="add-the-event-hubs-nuget-package"></a>Hinzufügen des Event Hubs-NuGet-Pakets

1. Wählen Sie im Menü **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. 
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.EventHubs** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Führen Sie den folgenden Befehl aus, um das NuGet-Paket **Azure.Messaging.EventHubs.Processor** zu installieren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualisieren der Methode „Main“ 

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Fügen Sie der Klasse `Program` Konstanten für die Event Hubs-Verbindungszeichenfolge und den Namen des Event Hubs hinzu. Ersetzen Sie die in Klammern gesetzten Platzhalter durch die entsprechenden Werte, die Sie beim Erstellen des Event Hubs erhalten haben. Ersetzen Sie die in Klammern gesetzten Platzhalter durch die entsprechenden Werte, die Sie beim Erstellen des Event Hubs und des Speicherkontos erhalten haben (Zugriffsschlüssel, primäre Verbindungszeichenfolge). Stellen Sie sicher, dass es sich bei `{Event Hubs namespace connection string}` um die Verbindungszeichenfolge auf Namespaceebene und nicht um die Event Hub-Zeichenfolge handelt.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
    
    Den dem Purview-Konto zugeordneten Event Hubs-Namespace erhalten Sie, indem Sie sich die primären/sekundären Verbindungszeichenfolgen des Atlas-Kafka-Endpunkts auf der Registerkarte „Eigenschaften“ des Purview-Kontos ansehen.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Event Hub-Namespace":::

    Zum Senden von Nachrichten an Purview muss der Event Hub-Name **ATLAS_ENTITIES** lauten.

3. Ersetzen Sie die Methode `Main` durch die folgende Methode vom Typ `async Main`. Weitere Einzelheiten finden Sie in den Codekommentaren. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Fügen Sie der Klasse nun die folgenden Ereignis- und Fehlerhandlermethoden hinzu. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Erstellen Sie das Projekt, und vergewissern Sie sich, dass keine Fehler vorhanden sind.

    > [!NOTE]
    > Den gesamten Quellcode mit weiteren informativen Kommentaren finden Sie [auf GitHub in dieser Datei](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Führen Sie die Empfängeranwendung aus. 

### <a name="sample-message-received-from-purview"></a>Aus Purview empfangene Beispielnachricht

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas unterstützt derzeit die folgenden Vorgangstypen: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. Das Pushen von Nachrichten an Purview ist derzeit standardmäßig aktiviert. Beinhaltet das Szenario das Lesen aus Purview, wenden Sie sich an uns, da diese Aktion in die Zulassungsliste aufgenommen werden muss. (Geben Sie die Abonnement-ID und den Namen des Purview-Kontos an.)


## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die Beispiele auf GitHub an: 

- [Event Hubs-Beispiele auf GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ereignisprozessorbeispiele auf GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas: Einführung in Benachrichtigungen](https://atlas.apache.org/2.0.0/Notifications.html)
