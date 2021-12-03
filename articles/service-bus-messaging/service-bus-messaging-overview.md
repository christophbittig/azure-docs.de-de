---
title: Übersicht über Azure Service Bus-Messaging | Microsoft-Dokumentation
description: Dieser Artikel enthält eine allgemeine Übersicht über Azure Service Bus (ein vollständig verwalteter Nachrichtenbroker für die Unternehmensintegration).
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: 8aa59315dcb196cf7f5c5b107e8cf575a5eb4f86
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373116"
---
# <a name="what-is-azure-service-bus"></a>Was ist Azure Service Bus?
Bei Microsoft Azure Service Bus handelt es sich um einen vollständig verwalteten Nachrichtenbroker für Unternehmen mit Nachrichtenwarteschlangen und Publish/Subscribe-Themen. Service Bus wird verwendet, um Anwendungen und Dienste voneinander zu entkoppeln und so die folgenden Vorteile zu erzielen:

- Übergreifender Lastenausgleich für konkurrierende Worker
- Sicherheit beim Routing und der Übertragung von Daten sowie Kontrolle über Dienst- und Anwendungsgrenzen hinweg
- Koordinierung von Transaktionsausgaben mit hohen Anforderungen an die Zuverlässigkeit 

> [!NOTE]
> Einen Vergleich der Azure-Messagingdienste finden Sie unter [Auswählen zwischen Azure-Messagingdiensten – Event Grid, Event Hubs und Service Bus](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json). 

## <a name="overview"></a>Übersicht
Die Datenübertragung zwischen verschiedenen Anwendungen und Diensten erfolgt mithilfe von **Nachrichten**. Eine Nachricht ist ein Container, der über Metadaten verfügt und Daten enthält. Bei den Daten kann es sich um eine beliebige Art von Informationen handeln, z. B. strukturierte Daten in den folgenden gängigen Formaten: JSON, XML, Apache Avro, Nur-Text.

Im Anschluss finden Sie einige gängige Messagingszenarien:

* **Messaging**: Übertragung von Geschäftsdaten (beispielsweise Verkäufe/Bestellungen, Journale oder Bestandsbewegungen)
* **Entkoppelung von Anwendungen**: Höhere Zuverlässigkeit und bessere Skalierbarkeit von Anwendungen und Diensten. Producer und Consumer müssen nicht gleichzeitig online bzw. immer verfügbar sein. Die [Last wird verteilt](/azure/architecture/patterns/queue-based-load-leveling), damit ein Dienst nicht aufgrund von Datenverkehrsspitzen überlastet wird. 
* **Lastenausgleich:** Ermöglicht es, dass mehrere [konkurrierende Consumer](/azure/architecture/patterns/competing-consumers) gleichzeitig Daten aus einer Warteschlange auslesen und die Consumer dann jeweils auf sichere Weise die exklusive Eigentümerschaft für bestimmte Nachrichten erhalten. 
* **Themen und Abonnements**: Ermöglichen 1:*n*-Beziehungen zwischen [Herausgebern und Abonnenten](/azure/architecture/patterns/publisher-subscriber), damit Abonnenten bestimmte Nachrichten aus einem Datenstrom mit veröffentlichten Nachrichten auswählen können.
* **Transaktionen**: Ermöglichen Ihnen die Durchführung mehrerer Vorgänge im Rahmen einer atomischen Transaktion. Beispielsweise können die folgenden Vorgänge im Rahmen einer Transaktion durchgeführt werden.  

    1. Abrufen einer Nachricht aus einer Warteschlange
    2. Posten von Ergebnissen in einer oder mehreren anderen Warteschlangen
    3. Verschieben der Eingabenachricht aus der ursprünglichen Warteschlange 
    
    Die Ergebnisse werden für nachgeschaltete Consumer erst im Erfolgsfall sichtbar, z. B. der erfolgreiche Abgleich der Eingabenachricht. Dies ermöglicht die Verwendung einer Semantik mit einmaliger Verarbeitung. Dieses Transaktionsmodell ist eine stabile Grundlage für das Muster für [Kompensierende Transaktionen](/azure/architecture/patterns/compensating-transaction) im weiteren Lösungskontext. 
* **Nachrichtensitzungen**: Hiermit wird eine umfassende Koordinierung von Workflows und Multiplex-Übertragungen implementiert, für die eine strikte Nachrichtensortierung oder -verzögerung erforderlich ist.

Wenn Sie bereits mit anderen Nachrichtenbrokern wie Apache ActiveMQ vertraut sind, werden Ihnen die Service Bus-Konzepte bekannt vorkommen, weil sie ähnlich sind. Da es sich bei Service Bus um ein PaaS-Angebot (Platform-as-a-Service) handelt, besteht ein wichtiger Unterschied darin, dass Sie sich nicht um die folgenden Aktionen kümmern müssen. Azure übernimmt die Durchführung dieser Aufgaben für Sie. 

- Achten auf Hardwarefehler 
- Sicherstellen der Patches für die Betriebssysteme oder die Produkte
- Anordnen von Protokollen und Verwalten des Speicherplatzes
- Verarbeiten von Sicherungsvorgängen
- Ausführen von Failovern auf einen Reservecomputer

## <a name="concepts"></a>Konzepte
In diesem Abschnitt werden die grundlegenden Konzepte von Service Bus erläutert.

### <a name="queues"></a>Warteschlangen
Nachrichten werden an **Warteschlangen** gesendet und daraus empfangen. Warteschlangen dienen zum Speichern von Nachrichten, bis die empfangende Anwendung empfangs- und verarbeitungsbereit ist.

![Warteschlange](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Nachrichten in Warteschlangen werden bei ihrem Eingang sortiert und mit einem Zeitstempel versehen. Nachdem die Nachricht vom Broker akzeptiert wurde, wird sie immer dauerhaft im dreifach redundanten Speicher vorgehalten – verteilt auf die Verfügbarkeitszonen, falls die Nutzung in Zonen für den Namespace aktiviert ist. Von Service Bus werden Nachrichten niemals im Arbeitsspeicher oder im flüchtigen Speicher belassen, nachdem sie dem Client als akzeptiert gemeldet wurden.

Nachrichten werden im **Pull-Modus** (also nur auf Anforderung) zugestellt. Im Gegensatz zum Busy-Polling-Modell einiger anderer Cloudwarteschlangen ist der Pullvorgang langlebig und wird erst abgeschlossen, nachdem eine Nachricht verfügbar ist. 

> [!NOTE]
> Einen Vergleich von Service Bus-Warteschlangen mit Storage-Warteschlangen finden Sie unter [Storage-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](service-bus-azure-and-service-bus-queues-compared-contrasted.md).

### <a name="topics"></a>Themen

Nachrichten können auch unter Verwendung von **Themen** gesendet und empfangen werden. Themen sind in Veröffentlichungs-/Abonnementszenarien hilfreich. Bei der Punkt-zu-Punkt-Kommunikation werden dagegen häufig Warteschlangen verwendet.

![Thema](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Themen können über mehrere, unabhängige Abonnements verfügen, die an das Thema angefügt sind und ansonsten genauso wie Warteschlangen auf der Empfängerseite funktionieren. Ein Abonnent eines Themas kann eine Kopie jeder Nachricht erhalten, die an das Thema gesendet wird. Bei Abonnements handelt es sich um benannte Entitäten. Abonnements sind standardmäßig dauerhafter Natur, aber sie können auch so konfiguriert werden, dass sie ablaufen und dann automatisch gelöscht werden. Über die JMS-API (Java Message Service) ermöglicht Service Bus Premium Ihnen auch die Erstellung von flüchtigen Abonnements, die nur für die Dauer der Verbindung bestehen.

Sie können Regeln für ein Abonnement definieren. Eine Abonnementregel verfügt über einen **Filter**, um für die Nachricht eine Bedingung für das Kopieren in das Abonnement zu definieren, und über eine optionale **Aktion**, mit der die Metadaten der Nachricht geändert werden können. Weitere Informationen finden Sie unter [Themenfilter und -aktionen](topic-filters.md). Dieses Feature ist in den folgenden Szenarien nützlich:

- Sie möchten nicht, dass ein Abonnement alle Nachrichten empfängt, die an ein Thema gesendet werden.
- Sie möchten Nachrichten mit zusätzlichen Metadaten kennzeichnen, wenn sie ein Abonnement durchlaufen.

> [!NOTE]
> Weitere Informationen zu Warteschlangen und Themen finden Sie unter [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md).


### <a name="namespaces"></a>Namespaces
Ein Namespace ist ein Container für alle Messagingkomponenten (Warteschlangen und Themen). Ein einzelner Namespace kann mehrere Warteschlangen und Themen enthalten, und Namespaces fungieren häufig als Anwendungscontainer. 

Ein Namespace ist damit vergleichbar, was in der Terminologie anderer Broker als Server bezeichnet wird, aber die Konzepte sind nicht völlig äquivalent. Ein Service Bus-Namespace ist Ihr eigenes Kapazitätssegment eines großen Clusters, der aus Dutzenden von aktiven virtuellen Computern besteht. Optional kann er über drei [Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md) reichen. So profitieren Sie von allen Vorteilen in Bezug auf die Verfügbarkeit und Stabilität, die sich aus der umfassenden Ausführung des Nachrichtenbrokers ergeben. Zudem müssen Sie sich nicht mit den zugrunde liegenden komplexen Zusammenhängen beschäftigen. Service Bus steht für serverloses Messaging.

## <a name="advanced-features"></a>Erweiterte Funktionen

Service Bus verfügt auch über erweiterte Features für komplexere Messagingszenarien. Die wichtigsten dieser Features werden in den folgenden Abschnitten beschrieben:

### <a name="message-sessions"></a>Nachrichtensitzungen

Mithilfe von Sitzungen können Sie in Service Bus eine FIFO-Garantie (First In – First Out) implementieren. [Nachrichtensitzungen](message-sessions.md) ermöglichen die gemeinsame und geordnete Verarbeitung unbegrenzter Sequenzen verwandter Nachrichten. 

### <a name="auto-forwarding"></a>Automatische Weiterleitung

Mit der Funktion [Automatische Weiterleitung](service-bus-auto-forwarding.md) können Sie eine Warteschlange oder ein Abonnement mit einer weiteren Warteschlange oder einem Thema aus dem selben Namespace verketten. Wenn die automatische Weiterleitung aktiviert ist, entfernt Service Bus die Nachrichten automatisch, die in der ersten Warteschlange oder dem Abonnement (Quelle) platziert wurden, und fügt sie in die zweite Warteschlange oder das Thema (Ziel) ein.

### <a name="dead-lettering"></a>Unzustellbare Nachrichten

Service Bus unterstützt eine [Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) (Dead-Letter Queue, DLQ). Darin werden Nachrichten gespeichert, die an keinen Empfänger übermittelt oder nicht verarbeitet werden können. Nachrichten aus der DLQ können entfernt und untersucht werden.

### <a name="scheduled-delivery"></a>Zeitgesteuerte Zustellung

Sie können Nachrichten [zur verzögerten Verarbeitung](message-sequencing.md#scheduled-messages) an eine Warteschlange oder an ein Thema senden. Sie können einen Auftrag beispielsweise so planen, dass er zu einem bestimmten Zeitpunkt für die Verarbeitung durch ein System verfügbar wird.

### <a name="message-deferral"></a>Nachrichtenverzögerung

Wenn eine Warteschlangen- oder ein Abonnementclient eine Nachricht erhält, die verarbeitet werden soll, aber die Verarbeitung aufgrund von besonderen Umständen innerhalb der Anwendung zu diesem Zeitpunkt nicht möglich ist, kann die Entität das [Abrufen der Nachricht auf später verschieben](message-deferral.md). Die Nachricht bleibt in der Warteschlange oder im Abonnement, wird jedoch zurückgestellt.

### <a name="batching"></a>Batchverarbeitung

Durch die [clientseitige Batchverarbeitung](service-bus-performance-improvements.md#client-side-batching) kann ein Warteschlangen- oder Themenclient das Senden einer Nachricht für einen bestimmten Zeitraum verzögern. Wenn der Client während dieses Zeitraums weitere Nachrichten sendet, werden die Nachrichten in einem einzigen Batch übertragen. 

### <a name="transactions"></a>Transaktionen

Eine [Transaktion](service-bus-transactions.md) gruppiert mehrere Vorgänge in einem Ausführungsbereich. Service Bus unterstützt Gruppierungsvorgänge für eine einzelne Nachrichtenentität (Warteschlange, Thema, Abonnement) innerhalb eines Transaktionsbereichs.

### <a name="filtering-and-actions"></a>Filtern und Aktionen

Abonnenten können definieren, welche Nachrichten von einem Thema empfangen werden sollen. Diese Nachrichten werden in Form von [benannten Abonnementregeln](topic-filters.md) angegeben. Für jede übereinstimmende Regelbedingung erzeugt das Abonnement eine Kopie der Nachricht, die für jede übereinstimmende Regel anders kommentiert werden kann.

### <a name="auto-delete-on-idle"></a>Automatisches Löschen nach Leerlauf

[Automatisches Löschen nach Leerlauf](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) ermöglicht das Angeben eines Leerlaufintervalls, nach dem die Warteschlange automatisch gelöscht wird. Die Mindestdauer ist fünf Minuten.

### <a name="duplicate-detection"></a>Duplikaterkennung

Sollte ein Fehler dazu führen, dass der Client das Ergebnis eines Sendevorgangs nicht mit Bestimmtheit ermitteln kann, sorgt die [Duplikaterkennung](duplicate-detection.md) für Klarheit: Der Absender kann die gleiche Nachricht erneut senden, und die Warteschlange oder das Thema verwirft mögliche Duplikate.

### <a name="shared-access-signature-sas-role-based-access-control-and-managed-identities"></a>Shared Access Signature (SAS), rollenbasierte Zugriffssteuerung und verwaltete Identitäten

Service Bus unterstützt Sicherheitsprotokolle wie [Shared Access Signatures](service-bus-sas.md) (SAS), die [rollenbasierte Zugriffssteuerung](service-bus-role-based-access-control.md) (Role Based Access Control, RBAC) und [verwaltete Identitäten für Azure-Ressourcen](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Georedundante Notfallwiederherstellung

Sollte eine Azure-Region oder ein Azure-Datencenter ausfallen, kann die Datenverarbeitung dank [georedundanter Notfallwiederherstellung](service-bus-geo-dr.md) in einer anderen Region oder in einem anderen Datencenter fortgesetzt werden.

### <a name="security"></a>Sicherheit

Service Bus unterstützt die Standardprotokolle [AMQP 1.0 (Advance Message Queueing Protocol)](service-bus-amqp-overview.md) und [HTTP/REST](/rest/api/servicebus/).

> [!NOTE]
> Weitere Informationen zu diesen Features finden Sie unter [Azure Service Bus: Erweiterte Features](advanced-features-overview.md).

## <a name="compliance-with-standards-and-protocols"></a>Sicherstellen der Konformität mit Standards und Protokollen

Das primäre Verbindungsprotokoll für Service Bus ist [Advanced Messaging Queueing Protocol (AMQP) 1.0](service-bus-amqp-overview.md) (offener ISO/IEC-Standard). Es ermöglicht Kunden das Schreiben von Anwendungen für die Verwendung mit Service Bus und lokalen Brokern wie ActiveMQ oder RabbitMQ. Der [Protokollleitfaden zu AMQP](service-bus-amqp-protocol-guide.md) enthält ausführliche Informationen, die Ihnen weiterhelfen, falls Sie eine Abstraktion dieser Art erstellen möchten.

[Service Bus Premium](service-bus-premium-messaging.md) ist vollständig mit der [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md)-API für Java/Jakarta EE konform. Darüber hinaus wird von Service Bus Standard auch die JMS 1.1-Unterversion für Warteschlangen unterstützt. JMS ist eine gängige Abstraktion für Nachrichtenbroker und kann mit vielen Anwendungen und Frameworks integriert werden, z. B. das häufig verwendete Spring-Framework. Für den Wechsel von anderen Brokern zu Azure Service Bus müssen Sie lediglich die Topologie mit den Warteschlangen und Themen neu erstellen und die Abhängigkeiten und die Konfiguration für den Clientanbieter ändern. Ein Beispiel finden Sie im [Migrationsleitfaden für ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="client-libraries"></a>Clientbibliotheken

Vollständig unterstützte Service Bus-Clientbibliotheken sind über das Azure SDK verfügbar.

- [Azure Service Bus für .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-Bibliotheken für Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus-Anbieter für Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Azure Service Bus-Module für JavaScript und TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-Bibliotheken für Python](/python/api/overview/azure/servicebus?preserve-view=true)

Das [primäre Protokoll von Azure Service Bus ist AMQP 1.0](service-bus-amqp-overview.md). Es kann über jeden AMQP 1.0-konformen Protokollclient genutzt werden. Einige Open-Source-AMQP-Clients verfügen über Beispiele, mit denen die Service Bus-Interoperabilität explizit demonstriert wird. Im [AMQP 1.0-Protokollleitfaden](service-bus-amqp-protocol-guide.md) erfahren Sie, wie Sie die Features von Service Bus direkt mit AMQP 1.0-Clients verwenden.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integration

Service Bus kann vollständig mit vielen Microsoft- und Azure-Diensten integriert werden, z. B.:

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logik-Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure-Funktionen](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Service Bus-Messaging finden Sie in folgenden Artikeln:

- [Auswahl von Azure-Messagingdiensten: Event Grid, Event Hubs und Service Bus](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
- [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
- Schnellstartanleitungen: [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) oder [JMS](service-bus-java-how-to-use-jms-api-amqp.md)
- [Service Bus-Preise](https://azure.microsoft.com/pricing/details/service-bus/) 
- [Premium-Messaging](service-bus-premium-messaging.md)