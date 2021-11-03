---
title: Verwenden von Azure Service Bus-Themen und -Abonnements mit Java (azure-messaging-servicebus)
description: In dieser Schnellstartanleitung schreiben Sie Java-Code mit dem Paket „azure-messaging-servicebus“, um Nachrichten an ein Azure Service Bus-Thema zu senden und dann Nachrichten von Abonnements für dieses Thema zu empfangen.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom: mode-api
ms.openlocfilehash: ec2374a2881eb8888b40158848bf024c1292fa1d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083610"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Senden von Nachrichten an ein Azure Service Bus-Thema und Empfangen von Nachrichten von Abonnements für das Thema (Java)
In dieser Schnellstartanleitung schreiben Sie Java-Code mit dem Paket „azure-messaging-servicebus“, um Nachrichten an ein Azure Service Bus-Thema zu senden und dann Nachrichten von Abonnements für dieses Thema zu empfangen.

> [!IMPORTANT]
> In dieser Schnellstartanleitung wird das neue Paket „azure-messaging-servicebus“ verwendet. Eine Schnellstartanleitung mit dem alten Paket „azure-servicebus“ finden Sie im Artikel zum [Senden und Empfangen von Nachrichten mit „azure-servicebus“](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Befolgen Sie die Schritte im [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md). Notieren Sie sich die Verbindungszeichenfolge, den Themennamen und einen Abonnementnamen. Für diese Schnellstartanleitung verwenden Sie nur ein Abonnement. 
- Installieren Sie das [Azure SDK für Java][Azure SDK for Java]. Wenn Sie Eclipse verwenden, können Sie das [Azure-Toolkit für Eclipse][Azure Toolkit for Eclipse] installieren, in dem das Azure SDK für Java enthalten ist. Sie können dann Ihrem Projekt die **Microsoft Azure-Bibliotheken für Java** hinzufügen. Lesen Sie bei Verwendung von IntelliJ die Informationen unter [Installieren des Azure-Toolkits für IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
In diesem Abschnitt erstellen Sie ein Java-Konsolenprojekt und fügen Code zum Senden von Nachrichten an das von Ihnen erstellte Thema hinzu. 

### <a name="create-a-java-console-project"></a>Erstellen eines Java-Konsolenprojekts
Erstellen Sie ein Java-Projekt mit Eclipse oder einem Tool Ihrer Wahl. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Fügen Sie Verweise auf Azure Core- und Azure Service Bus-Bibliotheken hinzu. 

Wenn Sie Eclipse verwenden und eine Java-Konsolenanwendung erstellt haben, konvertieren Sie Ihr Java-Projekt in ein Maven-Projekt: Klicken Sie im Fenster **Paket-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Konfigurieren** -> **In Maven-Projekt konvertieren** aus. Fügen Sie den beiden Bibliotheken anschließend Abhängigkeiten hinzu, wie im folgenden Beispiel gezeigt.

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

### <a name="add-code-to-send-messages-to-the-topic"></a>Hinzufügen von Code zum Senden von Nachrichten an das Thema
1. Fügen Sie die folgenden `import`-Anweisungen für das Thema der Java-Datei hinzu. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Definieren Sie in der Klasse Variablen für die Verbindungszeichenfolge und den Themennamen wie unten dargestellt: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Ersetzen Sie `<NAMESPACE CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace. Ersetzen Sie außerdem `<TOPIC NAME>` durch den Namen des Themas.
3. Fügen Sie in der Klasse eine Methode mit dem Namen `sendMessage` zum Senden einer Nachricht an das Thema hinzu. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. Fügen Sie eine Methode mit dem Namen `sendMessageBatch` zum Senden von Nachrichten an das von Ihnen erstellte Thema hinzu. Mit dieser Methode wird ein `ServiceBusSenderClient` für das Thema erstellt, die `createMessages`-Methode zum Abrufen der Liste mit den Nachrichten aufgerufen und mindestens ein Batch vorbereitet. Anschließend werden die Batches an das Thema gesendet. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
In diesem Abschnitt fügen Sie Code zum Abrufen von Nachrichten aus einem Abonnement für das Thema hinzu. 

1. Fügen Sie eine Methode mit dem Namen `receiveMessages` hinzu, um Nachrichten vom Abonnement zu empfangen. Mit dieser Methode wird ein `ServiceBusProcessorClient`-Element für das Abonnement erstellt, indem ein Handler für die Verarbeitung von Nachrichten und ein anderer für die Verarbeitung von Fehlern angegeben wird. Anschließend wird der Prozessor gestartet, und es wird einige Sekunden lang abgewartet. Die empfangenen Nachrichten werden ausgegeben, und dann wird der Prozessor angehalten und geschlossen.

    > [!IMPORTANT]
    > Ersetzen Sie `ServiceBusTopicTest` in `ServiceBusTopicTest::processMessage` im Code durch den Namen Ihrer Klasse. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
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
1. Aktualisieren Sie die `main`-Methode, um die Methoden `sendMessage`, `sendMessageBatch` und `receiveMessages` aufzurufen und `InterruptedException` auszulösen.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Ausführen der App
Führen Sie das Programm aus. Die Ausgabe sollte in etwa wie folgt aussehen:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Auf der Seite **Übersicht** für den Service Bus-Namespace im Azure-Portal wird die Anzahl **eingehender** und **ausgehender** Nachrichten angezeigt. Unter Umständen müssen Sie ca. eine Minute warten und die Seite dann aktualisieren, damit die neuesten Werte angezeigt werden. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Anzahl ein- und ausgehender Nachrichten" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Navigieren Sie unten im mittleren Bereich zur Registerkarte **Themen**, und wählen Sie das Thema aus, um die Seite **Service Bus-Thema** für Ihr Thema anzuzeigen. Auf dieser Seite sollten im Diagramm **Nachrichten** vier ein- und vier ausgehende Nachrichten angezeigt werden. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Ein- und ausgehende Nachrichten" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Wenn Sie den Aufruf `receiveMessages` in der `main`-Methode auskommentieren und die App dann erneut ausführen, werden auf der Seite **Service Bus-Thema** acht eingehende Nachrichten (vier davon neu), aber nur vier ausgehende Nachrichten angezeigt. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Aktualisierte Themenseite" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Wenn Sie auf dieser Seite ein Abonnement auswählen, gelangen Sie zur Seite **Service Bus-Abonnement**. Auf dieser Seite werden die Anzahl aktiver Nachrichten, die Anzahl unzustellbarer Nachrichten und weitere Informationen angezeigt. In diesem Beispiel sind vier aktive Nachrichten vorhanden, die noch nicht von einem Empfänger empfangen wurden. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Anzahl aktiver Nachrichten" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen:

- [Azure Service Bus-Clientbibliothek für Java: Infodatei](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Beispiele bei GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java-API-Referenz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
