---
title: Verwenden von AMQP mit der Java Message Service-API und Azure Service Bus
description: Verwenden des Java Message Service (JMS) mit Azure Service Bus und dem Advanced Message Queuing Protocol (AMQP) 1.0
ms.topic: article
ms.date: 09/20/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 83a19e7a360edfb5c883a1ecc717fc7bf96bfec4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662842"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Verwenden von Java Message Service mit Azure Service Bus und AMQP 1.0

> [!WARNING]
> Dieser Artikel bezieht sich auf die *eingeschränkte Unterstützung* für die JMS 1.1-API (Java Message Service). Diese ist nur im Standard-Tarif von Azure Service Bus verfügbar.
>
> Vollständige Unterstützung für die Java Message Service 2.0-API ist nur für den [Premium-Tarif von Azure Service Bus in der Vorschauversion](how-to-use-java-message-service-20.md) verfügbar. Es wird empfohlen, diesen Tarif zu verwenden.
>

In diesem Artikel wird beschrieben, wie die Messagingfeatures von Service Bus in Java-Anwendungen mit dem verbreiteten API-Standard JMS verwendet werden. Zu diesen Messagingfeatures zählen Warteschlangen und das Veröffentlichen oder Abonnieren von Themen. In einem [verwandten Artikel](service-bus-amqp-dotnet.md) wird erläutert, wie Sie dieselbe Aufgabe mithilfe der .NET-API für Azure Service Bus erledigen. In diesen beiden Artikeln zusammen finden Sie Informationen zum plattformübergreifenden Messaging unter Verwendung des Advance Message Queuing Protocol (AMQP) 1.0.

AMQP 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können.

Unterstützung für AMQP 1.0 in Service Bus bedeutet, dass Sie die Brokermessagingfeatures für Warteschlangen und Veröffentlichen bzw. Abonnieren mithilfe eines effizienten Binärprotokolls auf unterschiedlichen Plattformen nutzen können. Sie können auch Anwendungen erstellen, deren Komponenten mit verschiedenen Sprachen, Frameworks und Betriebssystemen erstellt wurden.

## <a name="get-started-with-service-bus"></a>Erste Schritte mit Service Bus

In diesem Artikel wird davon ausgegangen, dass Sie bereits über einen Service Bus-Namespace verfügen, der eine Warteschlange mit dem Namen `basicqueue` enthält. Andernfalls können Sie [den Namespace und die Warteschlange](service-bus-create-namespace-portal.md) im [Azure-Portal](https://portal.azure.com) erstellen. Weitere Informationen zum Erstellen von Namespaces und Warteschlangen für Service Bus finden Sie unter [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionierte Warteschlangen und Themen unterstützen zudem AMQP. Weitere Informationen finden Sie unter [Partitionierte Messagingentitäten](service-bus-partitioning.md) und [AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Herunterladen der AMQP 1.0-JMS-Clientbibliothek

Informationen zum Herunterladen der neuesten Version der AMQP 1.0-JMS-Clientbibliothek Apache Qpid finden Sie auf der [Downloadwebsite für Apache Qpid](https://qpid.apache.org/download.html).

Folgende JAR-Dateien aus dem Apache Qpid-JMS-AMQP 1.0-Verteilungsarchiv müssen der Java-Umgebungsvariable „CLASSPATH“ hinzugefügt werden, wenn Sie JMS-Anwendungen mit Service Bus erstellen und ausführen:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> Die JAR-Namen und -Versionen für JMS haben sich möglicherweise geändert. Weitere Informationen finden Sie unter [Qpid-JMS-AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Programmieren von Java-Anwendungen

### <a name="java-naming-and-directory-interface"></a>Java Naming and Directory Interface (Java Benennungs- und Verzeichnisschnittstelle)

JMS verwendet die Java Naming and Directory Interface (JNDI), um eine Trennung zwischen logischen und physischen Namen umzusetzen. Mit JNDI werden zwei Arten von JMS-Objekten aufgelöst: **ConnectionFactory** und **Destination**. JNDI verwendet ein Anbietermodell, das Sie mit verschiedenen Verzeichnisdiensten verbinden können, um Namensauflösungsfunktionen zu implementieren. Die Apache Qpid-JMS-AMQP 1.0-Bibliothek enthält einen einfachen JNDI-Anbieter, der mit einer Eigenschaftendatei im folgenden Format konfiguriert wird:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>Einrichten des JNDI-Kontexts und Konfigurieren des ConnectionFactory-Objekts

Die Verbindungszeichenfolge, auf die verwiesen wird, ist in den SAS-Richtlinien im [Azure-Portal](https://portal.azure.com) unter **Primäre Verbindungszeichenfolge** angegeben.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurieren von Zielwarteschlangen für Producer und Consumer

Der Eintrag zum Definieren eines Ziels in der Qpid-Eigenschaftendatei für den JNDI-Anbieter hat das folgende Format:

So erstellen Sie eine Zielwarteschlange für den Producer
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

So erstellen Sie eine Zielwarteschlange für den Consumer
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Schreiben der JMS-Anwendung

Für die Verwendung von JMS mit Service Bus werden keine speziellen APIs oder Optionen benötigt. Es gibt jedoch einige Einschränkungen, die weiter unten erläutert werden. Wie bei allen JMS-Anwendungen müssen Sie zuerst die JNDI-Umgebung konfigurieren, um ein **ConnectionFactory**-Objekt und Ziele auflösen zu können.

#### <a name="configure-the-jndi-initialcontext-object"></a>Konfigurieren des JNDI-Objekts InitialContext

Zur Konfiguration der JNDI-Umgebung wird eine Hashtabelle mit Konfigurationsinformationen an den Konstruktor der Klasse „javax.naming.InitialContext“ übergeben. Obligatorisch sind in der Hashtabelle zwei Elemente: der Klassenname der InitialContext-Factory und die Anbieter-URL. Der folgende Code zeigt, wie Sie die JNDI-Umgebung mithilfe des JNDI-Anbieters für Qpid mit der Eigenschaftendatei **servicebus.properties** konfigurieren.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Einfache JMS-Anwendung mit einer Service Bus-Warteschlange

Das folgende Beispielprogramm sendet JMS-Textnachrichten an eine Service Bus-Warteschlange mit dem logischen JNDI-Namen „QUEUE“ und empfängt die zurückkommenden Nachrichten.

Den gesamten Quellcode und die Konfigurationsinformationen finden Sie in den [Azure Service Bus-Beispielen des Schnellstarts zu JMS-Warteschlangen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart).

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.azure.core.amqp.implementation.ConnectionStringProperties;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string properties is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringProperties csb = new ConnectionStringProperties(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSharedAccessKeyName(), csb.getSharedAccessKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSharedAccessKeyName(), csb.getSharedAccessKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Ausführen der Anwendung

Übergeben Sie die **Verbindungszeichenfolge** aus den freigegebenen Zugriffsrichtlinien, um die Anwendung auszuführen.
Die folgende Ausgabe entsteht durch Ausführen der Anwendung:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-Disposition und Service Bus-Vorgangszuordnung

Hier sehen Sie die Umwandlung einer AMQP-Disposition in einen Service Bus-Vorgang:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS-Themen im Vergleich zu Service Bus-Themen

Durch die Verwendung von Service Bus-Themen und -Abonnements über die JMS-API werden grundlegende Sende- und Empfangsfunktionen bereitgestellt. Dies ist praktisch, wenn Sie Anwendungen von anderen Nachrichtenbrokern mit JMS-konformen APIs portieren, obwohl die Service Bus-Themen sich von den JMS-Themen unterscheiden und einige Anpassungen erfordern.

Service Bus-Themen leiten Nachrichten in benannte, freigegebene und dauerhafte Abonnements weiter, die über die Azure Resource Manager-Benutzeroberfläche, die Azure-Befehlszeilentools oder das Azure-Portal verwaltet werden. Jedes Abonnement kann bis zu 2.000 Auswahlregeln aufweisen, wobei jede Regel eine Filterbedingung und bei SQL-Filtern auch eine Metadaten-Transformationsaktion aufweisen kann. Für jede Übereinstimmung einer Filterbedingung wird die Eingabenachricht ausgewählt, die in das Abonnement kopiert werden soll.  

Das Empfangen von Nachrichten aus Abonnements ist identisch mit dem Empfangen von Nachrichten aus Warteschlangen. Jedes Abonnement hat eine Warteschlange für unzustellbare Nachrichten sowie die Möglichkeit, Nachrichten automatisch an eine andere Warteschlange oder andere Themen weiterzuleiten.

Über JMS-Themen können Clients dynamisch nicht dauerhafte und dauerhafte Abonnenten erstellen, die optional ein Filtern von Nachrichten mit Nachrichtenselektoren ermöglichen. Diese nicht freigegebenen Elemente werden von Service Bus nicht unterstützt. Die Syntax der SQL-Filterregeln für Service Bus ist der von JMS unterstützten Nachrichtenselektorsyntax ähnlich.

Die Herausgeberseite eines JMS-Themas ist mit Service Bus kompatibel, wie in diesem Beispiel gezeigt, dynamische Abonnenten sind dies aber nicht. Die folgenden topologiebezogenen JMS-APIs werden mit Service Bus nicht unterstützt.

## <a name="unsupported-features-and-restrictions"></a>Nicht unterstützte Funktionen und Einschränkungen

Bei der Verwendung von JMS über AMQP 1.0 mit Service Bus gelten die folgenden Einschränkungen:

* Pro Sitzung ist nur ein **MessageProducer**- oder **MessageConsumer**-Objekt erlaubt. Falls Sie mehrere **MessageProducer**- oder **MessageConsumer**-Objekte in einer Anwendung benötigen, müssen Sie für diese jeweils eine eigene Sitzung erstellen.
* Flüchtige Themenabonnements werden momentan nicht unterstützt.
* **MessageSelector**-Objekte werden derzeit nicht unterstützt.
* Verteilte Transaktionen werden nicht unterstützt, aber Sitzungen mit Transaktionen werden unterstützt.

Service Bus trennt die Steuerungsebene von der Datenebene und unterstützt daher viele der dynamischen Topologiefunktionen von JMS nicht.

| Nicht unterstützte Methode          | Ersetzen durch                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Erstellen eines Themenabonnement, das den Nachrichtenselektor portiert.                                |
| createDurableConsumer       | Erstellen eines Themenabonnement, das den Nachrichtenselektor portiert.                                |
| createSharedConsumer        | Service Bus-Themen können immer freigegeben werden. Weitere Informationen finden Sie im Abschnitt „JMS-Themen im Vergleich zu Service Bus-Themen“.                                    |
| createSharedDurableConsumer | Service Bus-Themen können immer freigegeben werden. Weitere Informationen finden Sie im Abschnitt „JMS-Themen im Vergleich zu Service Bus-Themen“.                                      |
| createTemporaryTopic        | Erstellen eines Themas über Verwaltungs-API, Tools oder das Portal, wobei *AutoDeleteOnIdle* auf einen Ablaufzeitraum festgelegt ist. |
| createTopic                 | Erstellen eines Themas über Verwaltungs-API, Tools oder das Portal.                                         |
| unsubscribe                 | Löschen des Themas über Verwaltungs-API, Tools oder das Portal.                                            |
| createBrowser               | Nicht unterstützt. Verwenden Sie die Peek()-Funktion der Service Bus-API.                         |
| createQueue                 | Erstellen einer Warteschlange über Verwaltungs-API, Tools oder das Portal.                                           | 
| createTemporaryQueue        | Erstellen einer Warteschlange über Verwaltungs-API, Tools oder das Portal, wobei *AutoDeleteOnIdle* auf einen Ablaufzeitraum festgelegt ist. |
| receiveNoWait               | Verwenden Sie die vom Service Bus-SDK bereitgestellte receive()-Methode, und legen Sie das Timeout auf einen sehr niedrigen Wert oder auf null fest. |

## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurde gezeigt, wie die Brokermessagingfeatures von Service Bus wie Warteschlangen und das Veröffentlichen oder Abonnieren von Themen in Java mit der verbreiteten JMS-API und AMQP 1.0 verwendet werden.

Sie können Service Bus-AMQP 1.0 auch in anderen Sprachen verwenden, z. B. .NET, C, Python und PHP. Komponenten, die mit diesen unterschiedlichen Sprachen geschrieben wurden, können mit der AMQP 1.0-Unterstützung in Service Bus Nachrichten zuverlässig und bei voller Vertraulichkeit austauschen.

## <a name="next-steps"></a>Nächste Schritte

* [AMQP 1.0-Unterstützung in Azure Service Bus](service-bus-amqp-overview.md)
* [Verwenden von AMQP 1.0 mit der .NET-API für Service Bus](./service-bus-amqp-dotnet.md)
* [Entwicklerhandbuch für Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)