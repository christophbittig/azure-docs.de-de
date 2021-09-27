---
title: Verwenden der Java Message Service 2.0-API mit Azure Service Bus Premium
description: Verwenden von Java Message Service (JMS) mit Azure Service Bus
ms.topic: article
ms.date: 09/20/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: df85a53652395e7ecdb55c1471ee81a8e49e048b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615637"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Verwenden der Java Message Service 2.0-API mit Azure Service Bus Premium

Dieser Artikel erläutert die Verwendung der beliebten **JMS 2.0-API (Java Message Service)** für die Interaktion mit Azure Service Bus über das AMQP 1.0-Protokoll (Advanced Message Queueing Protocol).

> [!NOTE]
> Unterstützung für die JMS 2.0-API (Java Message Service) ist nur im **Azure Service Bus Premium-Tarif** verfügbar.
>

## <a name="pre-requisites"></a>Voraussetzungen

### <a name="get-started-with-service-bus"></a>Erste Schritte mit Service Bus

In diesem Leitfaden wird davon ausgegangen, dass Sie bereits über einen Service Bus-Namespace verfügen. Falls nicht, können Sie [den Namespace und die Warteschlange](service-bus-create-namespace-portal.md) im [Azure-Portal](https://portal.azure.com) erstellen. 

Weitere Informationen zum Erstellen von Namespaces und Warteschlangen für Service Bus finden Sie unter [Erste Schritte mit Service Bus-Warteschlangen im Azure-Portal](service-bus-quickstart-portal.md).

### <a name="set-up-a-java-development-environment"></a>Einrichten einer Java-Entwicklungsumgebung

Zum Entwickeln von Java-Anwendungen müssen Sie die entsprechende Entwicklungsumgebung einrichten: 
   * Entweder das JDK (Java Development Kit) oder die JRE (Java Runtime Environment) ist installiert.
   * Das JDK oder die JRE wird dem Buildpfad und den entsprechenden Systemvariablen hinzugefügt.
   * Es wird eine Java-IDE installiert, um das JDK oder die JRE zu verwenden. Beispielsweise Eclipse oder IntelliJ.

Weitere Informationen zum Vorbereiten Ihrer Entwicklerumgebung für Java in Azure finden Sie in [diesem Handbuch](/azure/developer/java/fundamentals/).

## <a name="what-jms-features-are-supported"></a>Welche JMS-Funktionen werden unterstützt?

[!INCLUDE [service-bus-jms-features-list](./includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Herunterladen der JMS-Clientbibliothek (Java Message Service)

Um alle Features zu nutzen, die im Azure Service Bus Premium-Tarif verfügbar sind, muss dem Buildpfad des Projekts die folgende Bibliothek hinzugefügt werden.

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Um dem Buildpfad [azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) hinzuzufügen, verwenden Sie das bevorzugte Tool für die Abhängigkeitsverwaltung für Ihr Projekt, etwa [Maven](https://maven.apache.org/) oder [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Programmieren von Java-Anwendungen

Nachdem die Abhängigkeiten importiert wurden, können die Java-Anwendungen in einer JMS-anbieteragnostischen Weise geschrieben werden.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Herstellen einer Verbindung mit Azure Service Bus mithilfe von JMS

Zum Herstellen einer Verbindung mit Azure Service Bus mithilfe von JMS-Clients benötigen Sie die **Zeichenfolge**, die im [Azure-Portal](https://portal.azure.com) in den „SAS-Richtlinien“ unter **Primäre Verbindungszeichenfolge** verfügbar ist.

1. Instanziieren der `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instanziieren Sie die `ServiceBusJmsConnectionFactory` mit dem entsprechenden `ServiceBusConnectionString`-Element.

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Verwenden Sie die `ConnectionFactory`, um eine `Connection` und dann eine `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    oder einen `JMSContext` (für JMS 2.0-Clients) zu erstellen.

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Obwohl eine JMS-„Sitzung“ und eine Service Bus-„Sitzung“ ähnlich benannt sind, sind sie voneinander vollständig unabhängig.
    >
    > In JMS 1.1 ist die Sitzung ein wesentlicher Baustein der API, der die Erstellung von MessageProducer, MessageConsumer und der Nachricht selbst ermöglicht. Weitere Informationen finden Sie im Tutorial zum [JMS-API-Programmiermodell](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html).
    >
    > In Service Bus handelt es sich bei [Sitzungen](message-sessions.md) um dienst- und clientseitige Konstrukte zum Aktivieren der FIFO-Verarbeitung für Warteschlangen und Abonnements.
    >

### <a name="write-the-jms-application"></a>Schreiben der JMS-Anwendung

Nachdem die `Session` oder der `JMSContext` instanziiert wurde, kann die Anwendung die vertrauten JMS-APIs verwenden, um sowohl Verwaltungs- als auch Datenvorgänge auszuführen.

Der Liste der [unterstützten JMS-Features](how-to-use-java-message-service-20.md#what-jms-features-are-supported) können Sie entnehmen, welche APIs unterstützt werden.

Im Folgenden finden Sie einige Beispielcodeausschnitte für die ersten Schritte mit JMS:

#### <a name="sending-messages-to-a-queue-and-topic"></a>Senden von Nachrichten an eine Warteschlange und ein Thema

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Empfangen von Nachrichten aus einem freigegebenen permanenten Abonnement für ein Thema

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Zusammenfassung

In diesem Leitfaden wurde gezeigt, wie Java-Clientanwendungen, die Java Message Service (JMS) über AMQP 1.0 verwenden, mit Azure Service Bus interagieren können.

Sie können Service Bus AMQP 1.0 auch mit anderen Sprachen verwenden, unter anderem .NET, C, Python und PHP. Komponenten, die mit diesen verschiedenen Sprachen geschrieben wurden, können mit der AMQP 1.0-Unterstützung in Service Bus Nachrichten zuverlässig und bei voller Vertraulichkeit austauschen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Service Bus und Details zu JMS-Entitäten (Java Message Service) finden Sie unter den folgenden Links: 
* [Service Bus: Warteschlangen, Themen und Abonnements](service-bus-queues-topics-subscriptions.md)
* [Service Bus: Java Message Service-Entitäten](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [AMQP 1.0-Unterstützung in Azure Service Bus](service-bus-amqp-overview.md)
* [Entwicklerhandbuch für Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service-API(externes Oracle-Dokument)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Informationen zum Migrieren von ActiveMQ zu Azure Service Bus](migrate-jms-activemq-to-servicebus.md)