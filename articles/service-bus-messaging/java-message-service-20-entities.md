---
title: 'Azure Service Bus Messaging: Java-Messagingdienstentitäten'
description: Dieser Artikel bietet eine Übersicht über Azure Service Bus-Messagingentitäten, auf die über die Java-Messagingdienst-API zugegriffen werden kann.
ms.topic: article
ms.date: 09/27/2021
ms.openlocfilehash: 6900deff355f10e4274a5268bd21da189a9daf80
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154525"
---
# <a name="java-message-service-jms-20-entities"></a>Java Message Service 2.0-Entitäten (JMS)

Clientanwendungen, die eine Verbindung mit Azure Service Bus Premium herstellen und die [JMS-Bibliothek von Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) verwenden, können die unten aufgeführten Entitäten nutzen.

## <a name="queues"></a>Warteschlangen

Warteschlangen in JMS sind semantisch vergleichbar mit den herkömmlichen [Service Bus-Warteschlangen](service-bus-queues-topics-subscriptions.md#queues).

Verwenden Sie die folgenden Methoden in der `JMSContext`-Klasse, um eine Warteschlange zu erstellen:

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Themen

Themen in JMS sind semantisch vergleichbar mit den herkömmlichen [Service Bus-Themen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Verwenden Sie die folgenden Methoden in der `JMSContext`-Klasse, um ein Thema zu erstellen:

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Temporäre Warteschlangen

Wenn eine Clientanwendung eine temporäre Entität erfordert, die für die Lebensdauer der Anwendung vorhanden ist, kann sie temporäre Warteschlangen verwenden. Diese Entitäten werden mit dem Muster [Anforderung/Antwort](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) verwendet.

Verwenden Sie die folgenden Methoden in der `JMSContext`-Klasse, um eine temporäre Warteschlange zu erstellen:

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Temporäre Themen

Ebenso wie temporäre Warteschlangen gibt es auch temporäre Themen, um das Veröffentlichen/Abonnieren über eine temporäre Entität zu ermöglichen, die für die gesamte Lebensdauer der Anwendung vorhanden ist.

Verwenden Sie die folgenden Methoden in der `JMSContext`-Klasse, um ein temporäres Thema zu erstellen:

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java Message Service-Abonnements (JMS)

Auch wenn diese semantisch [Abonnements](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) ähnlich sind (sie also für ein Thema vorhanden sind und Veröffentlichungs-/ Abonniersemantik ermöglichen), führt die Java Message Service-Spezifikation die Konzepte der Attribute **Shared** (freigegeben), **Unshared** (nicht freigegeben), **Durable (dauerhaft) und **Non-durable** (nicht dauerhaft) für das jeweilige Abonnement ein.

> [!NOTE]
> Die unten genannten Abonnements sind im Premium-Tarif von Azure Service Bus für Clientanwendungen verfügbar, die über die [JMS-Bibliothek von Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) eine Verbindung mit Azure Service Bus herstellen.
>
> Nur dauerhafte Abonnements können mit dem Azure-Portal erstellt werden.
>

### <a name="shared-durable-subscriptions"></a>Freigegebene dauerhafte Abonnements

Ein freigegebenes dauerhaftes Abonnement wird verwendet, wenn alle zu einem Thema veröffentlichten Nachrichten von einer Anwendung empfangen und verarbeitet werden sollen, und zwar unabhängig davon, ob die Anwendung das Abonnement jederzeit aktiv nutzt.

Jede Anwendung, die für den Empfang von Service Bus authentifiziert ist, kann von freigegebenen dauerhaften Abonnement Nachrichten empfangen.

Verwenden Sie zum Erstellen eines freigegebenen dauerhaften Abonnements die folgenden Methoden für die `JMSContext`-Klasse:

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Das freigegebene dauerhafte Abonnement ist weiterhin vorhanden, es sei denn, es wird mithilfe der `unsubscribe`-Methode für die `JMSContext` Klasse gelöscht.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Nicht freigegebene dauerhafte Abonnements

Wie ein freigegebenes dauerhaftes Abonnement wird ein nicht freigegebenes dauerhaftes Abonnement verwendet, wenn alle zu einem Thema veröffentlichten Nachrichten von einer Anwendung empfangen und verarbeitet werden sollen, und zwar unabhängig davon, ob die Anwendung das Abonnement aktiv nutzt.

Da es sich hierbei jedoch um ein nicht freigegebenes Abonnement handelt, kann nur die Anwendung, die das Abonnement erstellt hat, daraus Nachrichten empfangen.

Verwenden Sie zum Erstellen eines nicht freigegebenen dauerhaften Abonnements die folgenden Methoden aus der `JMSContext`-Klasse: 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Das `noLocal`-Feature wird zurzeit nicht unterstützt und somit ignoriert.
>

Das nicht freigegebene dauerhafte Abonnement ist weiterhin vorhanden, es sei denn, es wird mithilfe der `unsubscribe`-Methode für die `JMSContext` Klasse gelöscht.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Freigegebene nicht dauerhafte Abonnements

Ein freigegebenes nicht dauerhaftes Abonnement wird verwendet, wenn mehrere Clientanwendungen Nachrichten von einem einzigen Abonnement empfangen und verarbeiten müssen, und zwar nur so lange, wie sie aktiv Nachrichten von diesem Abonnement nutzen/empfangen.

Da das Abonnement nicht dauerhaft ist, wird es nicht persistent gespeichert. Nachrichten werden von diesem Abonnement nicht empfangen, wenn keine aktiven Consumer für das Abonnement vorhanden sind.

Um ein freigegebenes nicht dauerhaftes Abonnement zu erstellen, erstellen Sie ein `JmsConsumer`-Element, wie in den unten genannten Methoden aus der `JMSContext`-Klasse gezeigt:

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Das freigegebene nicht dauerhafte Abonnement ist so lange vorhanden, wie aktive Consumer Nachrichten von ihm empfangen.

### <a name="unshared-non-durable-subscriptions"></a>Nicht freigegebene nicht dauerhafte Abonnements

Ein nicht freigegebenes nicht dauerhaftes Abonnement wird verwendet, wenn die Clientanwendung Nachrichten von einem einzigen Abonnement empfangen und verarbeiten muss, und zwar nur so lange, wie sie aktiv Nachrichten von diesem Abonnement nutzt. Für dieses Abonnement darf nur ein Consumer vorhanden sein, d. h. der Client, der das Abonnement erstellt hat.

Da das Abonnement nicht dauerhaft ist, wird es nicht persistent gespeichert. Nachrichten werden von diesem Abonnement nicht empfangen, wenn kein aktiver Consumer für das Abonnement vorhanden ist.

Um ein nicht freigegebenes und nicht dauerhaftes Abonnement zu erstellen, erstellen Sie ein `JMSConsumer`-Element, wie in den unten genannten Methoden aus der `JMSContext`-Klasse gezeigt:

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Das `noLocal`-Feature wird zurzeit nicht unterstützt und somit ignoriert.
>

Das nicht freigegebene nicht dauerhafte Abonnement ist so lange vorhanden, wie ein aktiver Consumer Nachrichten von ihm empfängt.

### <a name="message-selectors"></a>Nachrichtenselektoren

Ebenso wie **Filter und Aktionen** für reguläre Service Bus-Abonnements vorhanden sind, sind **Nachrichtenselektoren** für JMS-Abonnements vorhanden.

Nachrichtenselektoren können für jedes der JMS-Abonnements eingerichtet werden und sind als Filterbedingung für die Nachrichtenheadereigenschaften vorhanden. Es werden nur Nachrichten zugestellt, deren Headereigenschaften mit dem Nachrichtenselektorausdruck übereinstimmen. Ein Wert von NULL oder eine leere Zeichenfolge gibt an, dass kein Nachrichtenselektor für das JMS-Abonnement bzw. den -Consumer vorhanden ist.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Weitere Konzepte für JMS 2.0-Abonnements (Java Message Service)

### <a name="client-scoping"></a>Clientbereich

Gemäß der Spezifikation in der JMS 2.0-API (Java Message Service) kann der *Bereich von Abonnements auf bestimmte Clientanwendungen festgelegt* werden, die durch die entsprechende `clientId` angegeben werden.

Nachdem der Bereich für das Abonnement festgelegt wurde, ist der **Zugriff nur möglich** für Clientanwendungen mit derselben Client-ID. 

Alle Versuche, auf ein Abonnement, dessen Bereich auf eine bestimmte Client-ID (z. B. clientId1) festgelegt wurde, aus einer Anwendung mit einer anderen Client-ID (z. B. clientId2) heraus zuzugreifen, führen zur Erstellung eines weiteren Abonnements, dessen Bereich auf die andere Client-ID (clientId2) festgelegt ist.

> [!NOTE]
> Die Client-ID kann NULL oder leer sein, sie muss jedoch mit der in der JMS-Clientanwendung festgelegten Client-ID identisch sein. Aus Sicht des Azure Service Bus haben eine Client-ID mit dem Wert NULL und eine leere Client-ID dasselbe Verhalten.
>
> Wenn die Client-ID leer oder auf NULL festgelegt ist, ist der Zugriff nur für Clientanwendungen möglich, deren Client-ID ebenfalls leer oder auf NULL festgelegt ist.
>

### <a name="shareability"></a>Freigabe

Bei **freigegebenen** Abonnements können mehrere Clients/Consumer (d. h. JMSConsumer-Objekte) Nachrichten von diesen empfangen.

>[!NOTE]
> Auf freigegebene Abonnements, deren Bereich auf eine bestimmte Client-ID festgelegt ist, kann trotzdem von mehreren Clients/Consumern (d. h. JMSConsumer-Objekten) zugegriffen werden, alle Clientanwendungen müssen jedoch dieselbe Client-ID aufweisen.
>
 

Bei **nicht freigegebenen** Abonnements kann nur ein Client/Consumer (d. h. ein JMSConsumer-Objekt) Nachrichten von diesen empfangen. Wenn ein `JMSConsumer` für ein nicht freigegebenes Abonnement erstellt wird, obwohl bereits ein aktiver `JMSConsumer` auf Nachrichten von diesem Abonnement lauscht, wird eine `JMSException` ausgelöst.


### <a name="durability"></a>Beständigkeit

**Dauerhafte** Abonnements werden beibehalten und sammeln weiterhin Nachrichten aus dem Thema, unabhängig davon, ob eine Anwendung (`JMSConsumer`) Nachrichten daraus verarbeitet.

**Nicht dauerhafte** Abonnements werden nicht beibehalten und sammeln Nachrichten aus dem Thema, sofern eine Anwendung (`JMSConsumer`) Nachrichten daraus verarbeitet. 

## <a name="representation-of-client-scoped-subscriptions"></a>Darstellung von clientbezogenen Abonnements

Da es möglich sein muss, dass die clientbezogenen (JMS-)Abonnements zusammen mit vorhandenen [Abonnements](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) verwendet werden, folgt die Darstellung der clientbezogenen (JMS-)Abonnements dem folgenden Format.

   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**D** (bei dauerhaften Abonnements)
   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**ND** (bei nicht dauerhaften Abonnements)

Dabei ist **$** das Trennzeichen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele für die Verwendung von Service Bus-Messaging finden Sie in den folgenden erweiterten Themen:

* [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
* [Verwenden der Java Message Service 2.0-API mit Azure Service Bus Premium](how-to-use-java-message-service-20.md)



