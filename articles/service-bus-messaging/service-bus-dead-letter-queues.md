---
title: Service Bus-Warteschlangen für unzustellbare Nachrichten | Microsoft Docs
description: Beschreibung von Warteschlangen für unzustellbare Nachrichten in Azure Service Bus Service Bus-Warteschlangen und -Themenabonnements verfügen über eine sekundäre Unterwarteschlange, die als „Warteschlange für unzustellbare Nachrichten“ bezeichnet wird.
ms.topic: article
ms.date: 08/30/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: b64500b6fefd8da7fe2b6dda4719e7ed6ae81b94
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258333"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Übersicht über Service Bus-Warteschlangen für unzustellbare Nachrichten

Azure Service Bus-Warteschlangen und -Themenabonnements verfügen über eine sekundäre Unterwarteschlange, die als *Warteschlange für unzustellbare Nachrichten* (Dead Letter Queue, DLQ) bezeichnet wird. Die Warteschlange für unzustellbare Nachrichten muss nicht explizit erstellt werden, und kann nicht gelöscht oder unabhängig von der Hauptentität verwaltet werden.

Dieser Artikel erläutert Warteschlangen für unzustellbare Nachrichten in Service Bus. Ein Großteil der Informationen wird durch das Beispiel [Dead-Letter Queues](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) auf GitHub veranschaulicht.
 
## <a name="the-dead-letter-queue"></a>Die Warteschlange für unzustellbare Nachrichten

Die Warteschlange für unzustellbare Nachrichten dient zum Speichern von Nachrichten, die an keinen Empfänger übermittelt oder nicht verarbeitet werden können. Nachrichten können dann aus dieser Warteschlange entfernt und überprüft werden. Eine Anwendung kann mithilfe eines Operators Probleme beheben und die Nachricht erneut senden, den Fehler protokollieren und korrigierende Maßnahmen ergreifen. 

Im Hinblick auf API und Protokoll ähnelt die Warteschlange für unzustellbare Nachrichten weitgehend anderen Warteschlangen, mit der Ausnahme, dass Nachrichten nur durch die übergeordnete Entität über den Vorgang für unzustellbare Nachrichten gesendet werden können. Darüber hinaus ist die Gültigkeitsdauer nicht relevant, und eine Nachricht kann nicht von einer Warteschlange für unzustellbare Nachrichten aus als unzustellbar gekennzeichnet werden. Die Warteschlange für unzustellbare Nachrichten bietet vollständige Unterstützung für die Peek/Lock-Übermittlung und Transaktionsvorgänge.

Es wird keine automatische Bereinigung der Warteschlange für unzustellbare Nachrichten durchgeführt. Nachrichten verbleiben in dieser Warteschlange, bis Sie sie explizit daraus abrufen und abschließen.


## <a name="dlq-message-count"></a>Nachrichtenanzahl der Warteschlange für unzustellbare Nachrichten
Es ist nicht möglich, auf Themenebene die Anzahl von Nachrichten in der Warteschlange für unzustellbare Nachrichten abzurufen. Der Grund ist, dass sich die Nachrichten nicht auf Themenebene befinden, sofern von Service Bus kein interner Fehler ausgelöst wird. Wenn ein Absender eine Nachricht an ein Thema sendet, wird diese stattdessen innerhalb von Millisekunden an Abonnements für das Thema weitergeleitet und ist daher nicht mehr auf der Themenebene vorhanden. Sie können Nachrichten in der Warteschlange für unzustellbare Nachrichten anzeigen, die dem Abonnement für das Thema zugeordnet ist. Im folgenden Beispiel wird im **Service Bus Explorer** angegeben, dass sich in der Warteschlange für unzustellbare Nachrichten für das Abonnement „test1“ derzeit 62 Nachrichten befinden. 

![Nachrichtenanzahl der Warteschlange für unzustellbare Nachrichten](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Sie können die Anzahl von Nachrichten in der Warteschlange für unzustellbare Nachrichten auch abrufen, indem Sie den folgenden Azure CLI-Befehl verwenden: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show). 

## <a name="moving-messages-to-the-dlq"></a>Verschieben von Nachrichten in die Warteschlange für unzustellbare Nachrichten
Es gibt verschiedene Aktivitäten in Service Bus, durch welche Nachrichten aus dem Messaging-Engine selbst per Push in die Warteschlange für unzustellbare Nachrichten übertragen werden. Eine Anwendung kann Nachrichten auch explizit in die Warteschlange für unzustellbare Nachrichten verschieben. Die folgenden beiden Eigenschaften (Ursache und Beschreibung für unzustellbare Nachricht) werden zu unzustellbaren Nachrichten hinzugefügt. Anwendungen können ihre eigenen Codes für die Eigenschaft „Ursache für unzustellbare Nachricht“ definieren, aber das System legt die folgenden Werte fest:

| Ursache für unzustellbare Nachrichten | Fehlerbeschreibung für unzustellbare Nachrichten |
| --- | --- |
|HeaderSizeExceeded |Das Größenkontingent für diesen Datenstrom wurde überschritten. |
|TTLExpiredException |Die Nachricht ist abgelaufen und wurde in die Warteschlange für unzustellbare Nachrichten verschoben. Weitere Informationen finden Sie im Abschnitt [Gültigkeitsdauer](#time-to-live). |
|Sitzungs-ID ist NULL. |Die für die Sitzung aktivierte Entität erlaubt keine Nachricht, deren Sitzungsbezeichner null ist. |
|MaxTransferHopCountExceeded | Die maximale Anzahl zulässiger Hops beim Weiterleiten zwischen Warteschlangen. Der Wert ist auf 4 festgelegt. |
| MaxDeliveryCountExceededExceptionMessage | Die Nachricht konnte nach der maximalen Anzahl der Übermittlungsversuche nicht verarbeitet werden. Weitere Informationen finden Sie im Abschnitt [Maximale Übermittlungsanzahl](#maximum-delivery-count). |

## <a name="maximum-delivery-count"></a>Maximale Übermittlungsanzahl
Die Anzahl der Übermittlungsversuche von Nachrichten für Service Bus-Warteschlangen und -Abonnements ist begrenzt. Der Standardwert ist 10. Jedes Mal, wenn eine Nachricht unter einer Einsehsperre übermittelt wurde, aber entweder explizit abgebrochen wurde oder die Sperre abgelaufen ist, wird die Übermittlungsanzahl der Nachricht erhöht. Wenn die Übermittlungsanzahl den Grenzwert überschreitet, wird die Nachricht in die Warteschlange für unzustellbare Nachrichten verschoben. Die Ursache für die unzustellbare Nachricht in der Warteschlange wird auf MaxDeliveryCountExceeded festgelegt. Dieses Verhalten kann nicht deaktiviert werden, aber Sie können die maximale Übermittlungsanzahl erhöhen.

## <a name="time-to-live"></a>Gültigkeitsdauer
Wenn Sie unzustellbare Nachrichten für Warteschlangen oder Abonnements aktivieren, werden alle Nachrichten nach ihrem Ablauf in die Warteschlange für unzustellbare Nachrichten verschoben. Der Ursachencode für die unzustellbare Nachricht wird auf TTLExpiredException gesetzt.

Die zurückgestellten Nachrichten werden ebenfalls nicht gelöscht oder in die Warteschlange für unzustellbare Nachrichten verschoben, nachdem sie abgelaufen sind. Dieses Verhalten ist beabsichtigt.

## <a name="errors-while-processing-subscription-rules"></a>Fehler beim Verarbeiten von Regeln für Abonnements
Wenn Sie unzustellbare Nachrichten bei Ausnahmen für die Filterauswertung aktivieren, werden alle Fehler, die während der Ausführung der SQL-Filterregel eines Abonnements auftreten, zusammen mit der verursachenden Nachricht in der Warteschlange für unzustellbare Nachrichten erfasst. Verwenden Sie diese Option nicht in einer Produktionsumgebung, in der nicht alle Nachrichtentypen über Abonnenten verfügen.

## <a name="application-level-dead-lettering"></a>Unzustellbare Nachrichten auf Anwendungsebene
Zusätzlich zu den vom System bereitgestellten Features für unzustellbare Nachrichten können Anwendungen die Warteschlange für unzustellbare Nachrichten verwenden, um nicht annehmbare Nachrichten explizit abzulehnen. Hierzu zählen beispielsweise Nachrichten, die aufgrund eines bestimmten Systemproblems nicht richtig verarbeitet werden können, Nachrichten mit fehlerhaften Nutzlasten oder Nachrichten, bei denen Authentifizierungsfehler auftreten, wenn ein Sicherheitsschema auf Nachrichtenebene verwendet wird.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Unzustellbare Nachrichten bei ForwardTo- oder SendVia-Szenarien
Nachrichten werden in den folgenden Situationen an die Warteschlange für unzustellbare Nachrichten übermittelt:

- Eine Meldung durchläuft mehr als vier Warteschlangen oder Themen, die [miteinander verkettet](service-bus-auto-forwarding.md) sind.
- Die Zielwarteschlange oder das Zielthema wurde deaktiviert oder gelöscht.
- Die Zielwarteschlange oder das Zielthema überschreitet die maximale Entitätsgröße.

## <a name="path-to-the-dead-letter-queue"></a>Pfad zur Warteschlange für unzustellbare Nachrichten
Sie können auf die Warteschlange für unzustellbare Nachrichten zugreifen, indem Sie die folgende Syntax verwenden:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich unter [Aktivieren der Verarbeitung unzustellbarer Nachrichten für eine Warteschlange oder ein Abonnement](enable-dead-letter.md) die verschiedenen Möglichkeiten beim Konfigurieren der Einstellung **Verarbeitung unzustellbarer Nachrichten bei Nachrichtenablauf** an.
