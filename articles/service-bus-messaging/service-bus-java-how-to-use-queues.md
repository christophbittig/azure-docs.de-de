---
title: Verwenden von Azure Service Bus-Warteschlangen mit Java (azure-messaging-servicebus)
description: In diesem Tutorial wird beschrieben, wie Sie Java verwenden, um Nachrichten an eine Azure Service Bus-Warteschlange zu senden und Antworten zu empfangen. Sie verwenden das neue Paket „azure-messaging-servicebus“.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, mode-api
ms.openlocfilehash: f9bbe3a36c64e9e726081e3ca9d15dd382f0fe9a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013952"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Senden und Empfangen von Nachrichten für Azure Service Bus-Warteschlangen (Java)
In dieser Schnellstartanleitung erstellen Sie eine Java-App zum Senden und Empfangen von Nachrichten für eine Azure Service Bus-Warteschlange. 

> [!IMPORTANT]
> In dieser Schnellstartanleitung wird das neue Paket „azure-messaging-servicebus“ verwendet. Eine Schnellstartanleitung mit dem alten Paket „azure-servicebus“ finden Sie im Artikel zum [Senden und Empfangen von Nachrichten mit „azure-servicebus“](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen. Notieren Sie sich die **Verbindungszeichenfolge** für Ihren Service Bus-Namespace und den Namen der **Warteschlange**, die Sie erstellt haben.
- Installieren Sie das [Azure SDK für Java][Azure SDK for Java]. Wenn Sie Eclipse verwenden, können Sie das [Azure-Toolkit für Eclipse][Azure Toolkit for Eclipse] installieren, in dem das Azure SDK für Java enthalten ist. Sie können dann Ihrem Projekt die **Microsoft Azure-Bibliotheken für Java** hinzufügen. Lesen Sie bei Verwendung von IntelliJ die Informationen unter [Installieren des Azure-Toolkits für IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
In diesem Abschnitt erstellen Sie ein Java-Konsolenprojekt und fügen Code zum Senden von Nachrichten an die von Ihnen weiter oben erstellte Warteschlange hinzu. 

### <a name="create-a-java-console-project"></a>Erstellen eines Java-Konsolenprojekts
Erstellen Sie ein Java-Projekt mit Eclipse oder einem Tool Ihrer Wahl. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Fügen Sie Verweise auf Azure Core- und Azure Service Bus-Bibliotheken hinzu. 

Wenn Sie Eclipse verwenden und eine Java-Konsolenanwendung erstellt haben, konvertieren Sie Ihr Java-Projekt in ein Maven-Projekt: Klicken Sie im Fenster **Paket-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Konfigurieren** -> **In Maven-Projekt konvertieren** aus. Fügen Sie dann Abhängigkeiten zu diesen beiden Bibliotheken hinzu, wie im folgenden Beispiel gezeigt.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Hinzufügen von Code für das Senden von Nachrichten an die Warteschlange
1. Fügen Sie die folgenden `import`-Anweisungen für das Thema der Java-Datei hinzu. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Definieren Sie in der Klasse Variablen für die Verbindungszeichenfolge und den Warteschlangennamen wie unten dargestellt: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<QUEUE NAME>` durch den Namen der Warteschlange.
3. Fügen Sie in der Klasse eine Methode mit dem Namen `sendMessage` zum Senden einer Nachricht an die Warteschlange hinzu. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Fügen Sie in der Klasse eine Methode mit dem Namen `createMessages` zum Erstellen einer Liste mit Nachrichten hinzu. Normalerweise erhalten Sie diese Nachrichten von den verschiedenen Teilen Ihrer Anwendung. Hier erstellen wir eine Liste mit Beispielnachrichten.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Fügen Sie eine Methode mit dem Namen `sendMessageBatch` zum Senden von Nachrichten an die von Ihnen erstellte Warteschlange hinzu. Mit dieser Methode wird ein `ServiceBusSenderClient` für die Warteschlange erstellt, die `createMessages`-Methode zum Abrufen der Liste mit den Nachrichten aufgerufen und mindestens ein Batch vorbereitet. Anschließend werden die Batches an die Warteschlange gesendet. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
In diesem Abschnitt fügen Sie Code hinzu, mit dem Nachrichten aus der Warteschlange abgerufen werden. 

1. Fügen Sie eine Methode mit dem Namen `receiveMessages` hinzu, um Nachrichten von der Warteschlange zu empfangen. Mit dieser Methode wird ein `ServiceBusProcessorClient`-Element für die Warteschlange erstellt, indem ein Handler für die Verarbeitung von Nachrichten und ein anderer für die Verarbeitung von Fehlern angegeben wird. Anschließend wird der Prozessor gestartet, und es wird einige Sekunden lang abgewartet. Die empfangenen Nachrichten werden ausgegeben, und dann wird der Prozessor angehalten und geschlossen.

    > [!IMPORTANT]
    > Ersetzen Sie `QueueTest` in `QueueTest::processMessage` im Code durch den Namen Ihrer Klasse. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Fügen Sie die Methode `processMessage` hinzu, um eine Nachricht zu verarbeiten, die aus dem Service Bus-Abonnement empfangen wurde. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Fügen Sie die Methode `processError` zum Behandeln von Fehlermeldungen hinzu.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. Aktualisieren Sie die `main`-Methode, um die Methoden `sendMessage`, `sendMessageBatch` und `receiveMessages` aufzurufen und `InterruptedException` auszulösen.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Ausführen der App
Wenn Sie die Anwendung ausführen, werden im Konsolenfenster die folgenden Meldungen angezeigt. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Auf der Seite **Übersicht** für den Service Bus-Namespace im Azure-Portal wird die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Unter Umständen müssen Sie ca. eine Minute warten und die Seite dann aktualisieren, damit die neuesten Werte angezeigt werden. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Anzahl ein- und ausgehender Nachrichten" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Wählen Sie auf der Seite **Übersicht** die Warteschlange aus, um zur Seite **Service Bus-Warteschlange** zu navigieren. Auf dieser Seite wird auch die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Darüber hinaus werden noch weitere Informationen wie etwa die **aktuelle Größe** der Warteschlange, die **maximale Größe** und die **Anzahl aktiver Nachrichten** angezeigt. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Warteschlangendetails" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen:

- [Azure Service Bus-Clientbibliothek für Java: Infodatei](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Beispiele bei GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java-API-Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
