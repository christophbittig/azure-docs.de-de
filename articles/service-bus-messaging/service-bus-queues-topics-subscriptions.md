---
title: 'Azure Service Bus-Messaging: Warteschlangen, Themen und Abonnements'
description: Dieser Artikel bietet eine Übersicht über Azure Service Bus-Messagingentitäten (Warteschlangen, Themen und Abonnements).
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 8756da7f4000f5ef3b478567934ba99a90b0ab4c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102266"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-Warteschlangen, -Themen und -Abonnements
Azure Service Bus unterstützt einen Satz cloudbasierter, nachrichtenorientierter Middlewaretechnologien, darunter zuverlässiges Message Queuing und dauerhaftes Veröffentlichungs-/Abonnementmessaging. Diese Brokermessagingfunktionen können Sie sich als asynchrone, entkoppelte Messagingfeatures vorstellen, die unter Verwendung der Service Bus-Messagingworkload verschiedene Szenarien wie Veröffentlichung/Abonnement, vorübergehende Entkopplung und Lastenausgleich unterstützen. Die entkoppelte Kommunikation bietet viele Vorteile. Beispielsweise können Clients und Server Verbindungen nach Bedarf herstellen und Vorgänge asynchron ausführen.

Den Kern der Messagingfunktionen in Service Bus bilden die folgenden Messagingentitäten: **Warteschlangen**, **Themen und Abonnements** sowie Regeln/Aktionen.

## <a name="queues"></a>Warteschlangen
Warteschlangen liefern die Nachrichten im **First In, First Out**-Verfahren (FIFO) an einen oder mehrere Consumer. Das bedeutet: Nachrichten werden in der Reihenfolge an die Empfänger gesendet und verarbeitet, in der sie der Warteschlange hinzugefügt wurden. Außerdem empfängt und verarbeitet nur ein einziger Consumer jede Nachricht. Als Hauptvorteil ergibt sich bei der Verwendung von Warteschlangen eine **vorübergehende Entkopplung von Anwendungskomponenten**. Anders ausgedrückt: Die Producer (Absender) und Consumer (Empfänger) müssen die Nachrichten nicht gleichzeitig senden und empfangen. Das ist möglich, da die Nachrichten dauerhaft in der Warteschlange gespeichert werden. Außerdem muss der Producer nicht auf eine Antwort vom Consumer warten, um weiterhin Nachrichten zu verarbeiten und zu senden.

Daraus ergibt als weiterer Vorteil ein **Lastenausgleich**, durch den Producer und Consumer Nachrichten mit unterschiedlichen Raten senden und empfangen können. Bei vielen Anwendungen variiert die Systemauslastung im Lauf der Zeit. Die für jede Arbeitseinheit erforderliche Verarbeitungszeit ist jedoch in der Regel konstant. Durch die Zwischenschaltung einer Warteschlange zwischen Nachrichtenproducer und -consumer reicht es aus, wenn die verbrauchende Anwendung lediglich für die Durchschnittslast anstatt für die Spitzenlast ausgelegt ist. Die Tiefe der Warteschlange erhöht und verringert sich mit der eingehenden Last. Durch diese Funktion sparen Sie direkt Geld, weil Sie weniger Infrastruktur für Ihre Anwendungslast benötigen. Mit zunehmender Last können weitere Arbeitsprozesse hinzugefügt werden, die Nachrichten aus der Warteschlange abrufen. Jede Nachricht wird nur von einem der Arbeitsprozesse verarbeitet. Außerdem ermöglicht dieser pullbasierte Lastenausgleich eine optimale Nutzung der Workercomputer, selbst wenn diese die Nachrichten in der eigenen Verarbeitungsleistung und Maximalgeschwindigkeit abrufen. Dieses Muster nennt man auch **konkurrierende Consumer**.

Die Verwendung von Warteschlangen als Zwischenglied zwischen Nachrichtenproducern und -consumern sorgt für eine inhärente lockere Kopplung zwischen den Komponenten. Da Producer und Consumer voneinander unabhängig sind, kann ein Upgrade für einen Consumer ohne Auswirkungen auf den Producer durchgeführt werden.

### <a name="create-queues"></a>Erstellen von Warteschlangen
Sie können Warteschlangen über das [Azure-Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), die [Befehlszeilenschnittstelle](service-bus-quickstart-cli.md) oder [Resource Manager-Vorlagen](service-bus-resource-manager-namespace-queue.md) erstellen. Für das Senden und Empfangen von Nachrichten können Sie Clients verwenden, die in [C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [Python](service-bus-python-how-to-use-queues.md) und [JavaScript](service-bus-nodejs-how-to-use-queues.md) geschrieben wurden. 

### <a name="receive-modes"></a>Empfangsmodi
Sie können für den Nachrichtenempfang von Service Bus zwischen zwei Modi wählen.

- **Receive-and-Delete**. In diesem Modus wird eine Nachricht als verarbeitet gekennzeichnet und an die Consumeranwendung zurückgegeben, wenn Service Bus die Anforderung vom Consumer empfängt. Dieser Modus stellt das einfachste Modell dar. Er eignet sich am besten für Szenarien, in denen eine Anwendung es toleriert, wenn eine Nachricht beim Auftreten eines Fehlers nicht verarbeitet wird. Beispiel: Ein Consumer stellt eine Empfangsanforderung aus und stürzt dann ab, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verbraucht markiert, beginnt die Anwendung bei einem Neustart, Nachrichten zu verarbeiten. Sie lässt damit die Meldung aus, die vor dem Absturz verarbeitet wurde.
- **Peek-Lock**. In diesem Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Fehlen von Nachrichten nicht tolerieren können. 
    1. Es wird die nächste zu verarbeitende Nachricht gesucht, diese wird **gesperrt**, um zu verhindern, dass andere Consumer sie erhalten, und dann wird sie an die Anwendung zurückgesendet. 
    1. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat, fordert sie den Service Bus-Dienst auf, die zweite Phase des Empfangsvorgangs abzuschließen. Anschließend wird **die Nachricht vom Dienst als verarbeitet markiert**. 

        Wenn die Anwendung die Nachricht aus irgendeinem Grund nicht verarbeiten kann, kann sie den Service Bus-Dienst auffordern, die Nachricht zu **verwerfen**. Service Bus **entsperrt** die Nachricht und macht sie verfügbar, damit sie erneut empfangen werden kann, und zwar entweder von demselben Consumer oder von einem anderen konkurrierenden Consumer. Darüber hinaus ist der Sperre ein **Timeout** zugeordnet. Wenn die Anwendung die Nachricht nicht vor Ablauf des Sperrtimeouts verarbeiten kann, entsperrt Service Bus die Nachricht und macht sie für den erneuten Empfang verfügbar.

        Falls die Anwendung abstürzt, nachdem die Nachricht verarbeitet wurde, aber bevor der Service Bus-Dienst zum Abschließen der Nachricht aufgefordert wurde, stellt Service Bus die Nachricht erneut an die Anwendung zu, wenn diese neu gestartet wird. Dieser Vorgang wird häufig als **At-Least-Once**-Verarbeitung bezeichnet. Dies bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird. In bestimmten Situationen wird dieselbe Nachricht jedoch möglicherweise erneut zugestellt. Wenn eine doppelte Verarbeitung in Ihrem Szenario nicht zulässig ist, fügen Sie der Anwendung zusätzliche Logik zur Erkennung doppelter Nachrichten hinzu. Weitere Informationen finden Sie unter [Duplikaterkennung](duplicate-detection.md). Das ist die **Exactly-Once**-Verarbeitung, bei der eine Nachricht genau einmal verarbeitet wird.

        > [!NOTE]
        > Weitere Informationen zu diesen beiden Modi finden Sie unter [Abgleichen von Empfangsvorgängen](message-transfers-locks-settlement.md#settling-receive-operations).

## <a name="topics-and-subscriptions"></a>Themen und Abonnements
Eine Warteschlange ermöglicht die Verarbeitung einer Nachricht durch einen einzelnen Consumer. Im Gegensatz zu Warteschlangen bieten Themen und Abonnements eine 1:n-Kommunikation in einem Muster vom Typ **Veröffentlichen/Abonnieren**. Dies ist nützlich für die Skalierung auf eine große Anzahl von Empfängern. Jede veröffentlichte Nachricht wird für jedes beim Thema registrierte Abonnement verfügbar gemacht. Der Herausgeber sendet eine Nachricht an ein Thema, und mindestens ein Abonnent – dies ist von den Filterregeln abhängig, die für diese Abonnements festgelegt sind – erhält eine Kopie der Nachricht. In Abonnements können die zu empfangenden Nachrichten mithilfe zusätzlicher Filter eingeschränkt werden. Herausgeber senden Nachrichten auf die gleiche Weise an ein Thema, wie sie Nachrichten an eine Warteschlange senden. Allerdings erhalten Consumer die Nachrichten nicht direkt vom Thema. Stattdessen erhalten Consumer die Nachrichten über Abonnements des Themas. Ein Themenabonnement ist mit einer virtuellen Warteschlange vergleichbar, die Kopien der an das Thema gesendeten Nachrichten empfängt. Consumer empfangen Nachrichten von einem Abonnement auf die gleiche Weise wie von einer Warteschlange.

Die Sendefunktionalität einer Warteschlange ist mit der eines Themas identisch, und die Empfangsfunktionalität entspricht einem Abonnement. Diese Funktion bedeutet unter anderem, dass die Abonnements das weiter oben in diesem Abschnitt beschriebene Muster hinsichtlich Warteschlangen unterstützen: konkurrierender Verbraucher, vorübergehende Entkopplung, Belastungsausgleich und Lastenausgleich.

### <a name="create-topics-and-subscriptions"></a>Erstellen von Themen und Abonnements
Das Erstellen eines Themas ähnelt dem Erstellen einer Warteschlange (siehe vorheriger Abschnitt). Sie können Themen und Abonnements über das [Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShell](service-bus-quickstart-powershell.md), die [Befehlszeilenschnittstelle](service-bus-tutorial-topics-subscriptions-cli.md) oder [Resource Manager-Vorlagen](service-bus-resource-manager-namespace-topic.md) erstellen. Für das Senden von Nachrichten zu einem Thema und das Empfangen von Nachrichten von Abonnements können Sie Clients verwenden, die in [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [Python](service-bus-python-how-to-use-topics-subscriptions.md) und [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md) geschrieben wurden. 

### <a name="rules-and-actions"></a>Regeln und Aktionen
In vielen Fällen müssen Nachrichten mit bestimmten Merkmalen auf unterschiedliche Weise verarbeitet werden. Um dies zu ermöglichen, können Sie Abonnements so konfigurieren, dass sie nach Nachrichten mit bestimmten Eigenschaften suchen und dann bestimmte Änderungen an diesen Eigenschaften vornehmen. Auch wenn Service Bus-Abonnements alle Nachrichten angezeigt werden, die an das Thema gesendet wurden, können Sie nur eine Teilmenge dieser Nachrichten in die virtuelle Abonnementwarteschlange kopieren. Diese Filterung wird mithilfe von Abonnementfiltern erreicht. Solche Änderungen werden als **Filteraktionen** bezeichnet. Beim Erstellen eines Abonnements können Sie einen Filterausdruck angeben, der auf die Eigenschaften der Nachricht angewandt wird. Die Eigenschaften können Systemeigenschaften (z. B. **Label**) und benutzerdefinierte Anwendungseigenschaften (z. B. **StoreName**) sein. Der SQL-Filterausdruck ist in diesem Fall optional. Ohne SQL-Filterausdruck wird jede für ein Abonnement definierte Filteraktion auf alle Nachrichten in diesem Abonnement angewandt.

Ein voll funktionsfähiges Beispiel finden Sie im [TopicFilters-Beispiel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples/TopicFilters) auf GitHub.

Weitere Informationen zu Filtern finden Sie unter [Themenfilter und -aktionen](topic-filters.md).

## <a name="java-message-service-jms-20-entities"></a>Java Message Service 2.0-Entitäten (JMS)
Auf die folgenden Entitäten kann über die JMS 2.0-API (Java Message Service) zugegriffen werden.

  * Temporäre Warteschlangen
  * Temporäre Themen
  * Freigegebene dauerhafte Abonnements
  * Nicht freigegebene dauerhafte Abonnements
  * Freigegebene nicht dauerhafte Abonnements
  * Nicht freigegebene nicht dauerhafte Abonnements

Erfahren Sie mehr über die [JMS 2.0-Entitäten](java-message-service-20-entities.md) und darüber, wie Sie [diese verwenden](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Beispiele in der Sprache Ihrer Wahl an, um Azure Service Bus-Features zu untersuchen. 

- [Azure Service Bus-Clientbibliothekbeispiele für .NET (neueste Version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für Java (neueste Version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-Clientbibliothekbeispiele für JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus-Clientbibliothekbeispiele für TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Hier finden Sie Beispiele für die älteren .NET- und Java-Clientbibliotheken:
- [Azure Service Bus-Clientbibliothekbeispiele für .NET (Legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Azure Service Bus-Clientbibliothekbeispiele für Java (Legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)