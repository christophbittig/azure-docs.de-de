---
title: Übersicht über Azure Service Bus-Drosselung | Microsoft-Dokumentation
description: 'Übersicht über Service Bus-Drosselung: Tarif „Standard“ und „Premium“'
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0607cd96b68de960829d80f00765586213a55d2d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609173"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Drosselungsvorgänge für Azure Service Bus

Cloudnative Lösungen erwecken den Eindruck, dass unbegrenzt Ressourcen zur Verfügung stehen, die gemäß Ihrer jeweiligen Arbeitsauslastung skaliert werden können. Dies ist für die Cloud zwar eher zutreffend als für lokale Systeme, aber auch für die Cloud gelten gewisse Einschränkungen.

Diese Einschränkungen können sowohl für den Tarif „Standard“ als auch für „Premium“ – wie in diesem Artikel beschrieben – zur Drosselung der Anforderungen von Clientanwendungen führen. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Drosselung für Tarif „Standard“ in Azure Service Bus

Im Tarif „Standard“ von Azure Service Bus wird eine mehrinstanzenfähige Einrichtung mit einem Preismodell mit nutzungsbasierter Bezahlung verwendet. Hier werden die zugeordneten Ressourcen von mehreren Namespaces in demselben Cluster gemeinsam verwendet. Der Tarif „Standard“ ist die empfohlene Wahl für Entwickler-, Test- und Qualitätssicherungsumgebungen in Verbindung mit Produktionssystemen mit geringem Durchsatz.

In der Vergangenheit hat Azure Service Bus nur über grobe Drosselungslimits verfügt, die strikt an der Ressourcennutzung ausgerichtet waren. Es gibt aber die Möglichkeit, die Drosselungslogik zu verfeinern und ein vorhersagbares Drosselungsverhalten für alle Namespaces zu erzielen, von denen diese Ressourcen gemeinsam genutzt werden.

Zur Sicherstellung einer möglichst fairen Nutzung und Verteilung von Ressourcen für alle Azure Service Bus-Standardnamespaces, für die dieselben Ressourcen verwendet werden, wurde die Drosselungslogik geändert und auf ein Guthabenverfahren umgestellt.

> [!NOTE]
> Beachten Sie hierbei, dass die Drosselung für Azure Service Bus oder andere cloudnative Dienste ***nicht neu*** ist.
>
> Bei der auf Guthaben basierenden Drosselung wird lediglich die Art und Weise verfeinert, wie Ressourcen von verschiedenen Namespaces in einer mehrinstanzenfähigen Umgebung im Tarif „Standard“ gemeinsam genutzt werden. So kann für alle Namespaces, die die Ressourcen gemeinsam verwenden, eine faire Nutzung erzielt werden.

### <a name="what-is-credit-based-throttling"></a>Was ist die auf Guthaben basierende Drosselung?

Bei der auf Guthaben basierenden Drosselung wird die Anzahl von Vorgängen beschränkt, die für einen Namespace in einem bestimmten Zeitraum durchgeführt werden können. 

Hier ist der Workflow für die auf Guthaben basierende Drosselung angegeben: 

  * Zu Beginn jedes Zeitraums wird für jeden Namespace ein bestimmtes Guthaben bereitgestellt.
  * Alle Vorgänge, die von den sendenden oder empfangenden Clientanwendungen durchgeführt werden, führen jeweils zu einer Verringerung des Guthabens (werden also vom verfügbaren Guthabenwert subtrahiert).
  * Wenn das Guthaben aufgebraucht ist, werden die nachfolgenden Vorgänge bis zum Beginn des nächsten Zeitraums gedrosselt.
  * Das Guthaben wird zu Beginn des nächsten Zeitraums wieder aufgefüllt.

### <a name="what-are-the-credit-limits"></a>Was sind die Guthabenlimits?

Die Guthabenlimits sind derzeit auf „1.000 pro Sekunde (pro Namespace)“ festgelegt.

Nicht alle Vorgänge werden gleich erstellt. Hier sind die Guthabenkosten für die einzelnen Vorgänge angegeben: 

| Vorgang | Guthabenkosten|
|-----------|-----------|
| Datenvorgänge (Send, SendAsync, Receive, ReceiveAsync, Peek) |Guthabenwert 1 pro Nachricht |
| Verwaltungsvorgänge (Create, Read, Update, Delete on Queues, Topics, Subscriptions, Filters) | Guthabenwert 10 |

> [!NOTE]
> Beachten Sie, dass beim Senden an ein Thema jede Nachricht anhand von Filtern ausgewertet wird, bevor sie für das Abonnement verfügbar gemacht wird.
> Jede Filterauswertung wird auch auf das Guthabenlimit (d. h. 1 Gutschrift pro Filterauswertung) angerechnet.
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Woher weiß ich, dass bei mir die Drosselung durchgeführt wird?

Wenn die Anforderungen von Clientanwendungen gedrosselt werden, erhält Ihre Anwendung die unten angegebene Serverantwort, die dann protokolliert wird.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Wie kann ich eine Drosselung vermeiden?

Bei der gemeinsamen Nutzung von Ressourcen ist es wichtig, für die verschiedenen Service Bus-Namespaces, von denen diese Ressourcen verwendet werden, eine faire Nutzung zu erzwingen. Mit der Drosselung wird sichergestellt, dass Spitzen einer einzelnen Arbeitsauslastung nicht dazu führen, dass andere Arbeitsauslastung auf denselben Ressourcen gedrosselt werden.

Wie weiter unten in diesem Artikel erwähnt, besteht bei einer Drosselung kein Risiko, weil in die Client-SDKs (und andere PaaS-Angebote in Azure) die Standardwiederholungsrichtlinie integriert ist. Alle gedrosselten Anforderungen werden mit exponentiellem Backoff wiederholt und letztendlich übermittelt, nachdem das Guthaben aufgefüllt wurde.

Es ist klar, dass einige Anwendungen unter Umständen sensibel auf eine Drosselung reagieren. In diesem Fall empfehlen wir Ihnen, [für Ihren derzeitigen Service Bus-Standardnamespace die Migration zu Premium durchzuführen](service-bus-migrate-standard-premium.md). 

Bei der Migration können Sie Ihrem Service Bus-Namespace dedizierte Ressourcen zuordnen und diese entsprechend zentral hochskalieren, wenn Ihre Arbeitsauslastung eine Spitze aufweist, und die Wahrscheinlichkeit einer Drosselung verringern. Wenn sich Ihre Arbeitsauslastung wieder normalisiert hat, können Sie die Ressourcen, die Ihrem Namespace zugeordnet sind, dann auch wieder zentral herunterskalieren.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Drosselung im Tarif „Premium“ von Azure Service Bus

Im [Tarif „Premium“ von Azure Service Bus](service-bus-premium-messaging.md) werden jedem Namespace, der vom Kunden eingerichtet wird, dedizierte Ressourcen in Form von Messagingeinheiten zugeordnet. Für diese dedizierten Ressourcen sind der Durchsatz und die Latenz vorhersagbar, und sie werden für Fälle empfohlen, in denen Produktionssysteme mit hohem Durchsatz oder erhöhter Sensibilität genutzt werden.

Darüber hinaus ermöglicht der Tarif „Premium“ Kunden auch das zentrale Hochskalieren ihrer Durchsatzkapazität, falls es zu Spitzen bei der Arbeitsauslastung kommt.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Wie funktioniert die Drosselung für den Tarif „Premium“ von Service Bus?

Bei der exklusiven Ressourcenzuordnung für Service Bus Premium basiert die Drosselung allein auf den Einschränkungen der Ressourcen, die dem Namespace zugeordnet sind.

Falls die Anzahl von Anforderungen so hoch ist, dass sie von den aktuellen Ressourcen nicht mehr verarbeitet werden können, werden die Anforderungen gedrosselt.

### <a name="how-will-i-know-that-im-being-throttled"></a>Woher weiß ich, dass bei mir die Drosselung durchgeführt wird?

Es gibt verschiedene Möglichkeiten, die Drosselung in Azure Service Bus Premium zu identifizieren: 
  * **Gedrosselte Anforderungen** werden in den [Azure Monitor-Anforderungsmetriken](monitor-service-bus-reference.md#request-metrics) angezeigt, um ermitteln zu können, wie viele Anforderungen gedrosselt wurden.
  * Eine hohe **CPU-Auslastung** ist ein Hinweis darauf, dass die derzeitige Ressourcenzuordnung hoch ist und Anforderungen ggf. gedrosselt werden, falls die aktuelle Arbeitsauslastung nicht reduziert wird.
  * Eine hohe **Arbeitsspeicherauslastung** ist ein Hinweis darauf, dass die derzeitige Ressourcenzuordnung hoch ist und Anforderungen ggf. gedrosselt werden, falls die aktuelle Arbeitsauslastung nicht reduziert wird.

### <a name="how-can-i-avoid-being-throttled"></a>Wie kann ich eine Drosselung vermeiden?

Da der Service Bus Premium-Namespace bereits über dedizierte Ressourcen verfügt, können Sie die Wahrscheinlichkeit einer Drosselung reduzieren. Führen Sie hierfür das zentrale Hochskalieren für die Anzahl von Messagingeinheiten durch, die Ihrem Namespace zugeordnet sind, falls es zu einer Spitze für die Arbeitsauslastung kommt (bzw. diese antizipiert wird).

Zum zentralen Hoch- bzw. Herunterskalieren können Sie [Runbooks](../automation/automation-create-alert-triggered-runbook.md) erstellen, die durch Änderungen der obigen Metriken ausgelöst werden können.

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="how-does-throttling-affect-my-application"></a>Wie wirkt sich die Drosselung auf meine Anwendung aus?

Die Drosselung einer Anforderung ist ein Hinweis darauf, dass der Dienst ausgelastet ist, weil mehr Anforderungen anstehen, als durch die Ressourcen zugelassen sind. Wenn nach einigen Augenblicken der gleiche Vorgang wiederholt wird, kann die Anforderung berücksichtigt werden, nachdem der Dienst seine aktuelle Arbeitsauslastung abgearbeitet hat.

Da die Drosselung das erwartete Verhalten jedes cloudnativen Diensts ist, haben wir die Wiederholungslogik direkt in das Azure Service Bus SDK integriert. Die Standardeinstellung ist eine automatische Wiederholung mit einem exponentiellen Backoff, um sicherzustellen, dass nicht jedes Mal dieselbe Anforderung gedrosselt wird.

Die Standardwiederholungslogik gilt für jeden Vorgang.

### <a name="does-throttling-result-in-data-loss"></a>Führt die Drosselung zu Datenverlust?

Azure Service Bus ist auf Persistenz ausgelegt. Wir stellen sicher, dass für alle an Service Bus gesendeten Daten ein Commit in den Speicher erfolgt, bevor der Dienst die erfolgreiche Durchführung der Anforderung bestätigt.

Wenn die Anforderung von Service Bus bestätigt wurde („ACK“), bedeutet dies, dass die Anforderung von Service Bus erfolgreich verarbeitet wurde. Falls von Service Bus ein Fehler („NACK“) zurückgegeben wird, bedeutet dies, dass die Anforderung von Service Bus nicht verarbeitet werden konnte und die Clientanwendung die Anforderung wiederholen muss.

Eine Drosselung einer Anforderung bedeutet, dass die Anforderung vom Dienst aufgrund von Ressourceneinschränkungen derzeit nicht akzeptiert und verarbeitet werden kann. Dies ist **kein** Hinweis auf einen Datenverlust, sondern die Anforderung wurde von Service Bus lediglich noch nicht verarbeitet. In diesem Fall wird durch die Verwendung der Standardwiederholungsrichtlinie des Service Bus SDK sichergestellt, dass die Anforderung schließlich verarbeitet wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele für die Verwendung von Service Bus-Messaging finden Sie in den folgenden erweiterten Themen:

* [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
* [Schnellstart: Senden und Empfangen von Nachrichten mit dem Azure-Portal und .NET](service-bus-quickstart-portal.md)
* [Tutorial: Aktualisieren des Bestands über das Azure-Portal und mithilfe von Themen/Abonnements](service-bus-tutorial-topics-subscriptions-portal.md)

