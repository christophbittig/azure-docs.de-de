---
title: 'Azure Functions: Georedundante Notfallwiederherstellung und Hochverfügbarkeit'
description: Verwenden von geografischen Regionen für Redundanz und Failover in Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 27730e2b6c1ed760f43a054901d16b58ca80acd9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645225"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Georedundante Notfallwiederherstellung in Azure Functions

Wenn ganze Azure-Regionen oder Rechenzentren ausfallen, muss Ihr unternehmenskritischer Code in einer anderen Region weiterverarbeitet werden. In diesem Artikel werden einige der Strategien erläutert, mit denen Sie Funktionen für die Wiederherstellung im Katastrophenfall bereitstellen können.

## <a name="basic-concepts"></a>Grundlegende Konzepte

Azure Functions werden in einer Funktions-App in einer bestimmten Region ausgeführt. Es ist keine integrierte Redundanz verfügbar. Um Ausführungsverluste bei Ausfällen zu vermeiden, können Sie dieselben Funktionen redundant einsetzen, um Anwendungen in mehreren Regionen zu betreiben.  

Wenn Sie denselben Funktionscode in mehreren Regionen ausführen, gibt es zwei Muster zu beachten:

| Muster | Beschreibung |
| --- | --- |
|**Aktiv/aktiv** | Die Funktionen in beiden Regionen sind aktiv und verarbeiten Ereignisse, entweder in doppelter Weise oder wechselweise. Wir empfehlen die Verwendung eines Aktiv-/Aktivmusters in Kombination mit [Azure Front Door](../frontdoor/front-door-overview.md) für Ihre kritischen HTTP-gesteuerten Funktionen. |
|**Aktiv/passiv** | Die Funktionen in der Region, die Ereignisse empfängt, werden aktiv ausgeführt, während die gleichen Funktionen in einer zweiten Region inaktiv bleiben.  Wenn eine Ausfallsicherung erforderlich ist, wird die zweite Region aktiviert und übernimmt die Verarbeitung. Wir empfehlen dieses Muster für Ihre ereignisgesteuerten, nicht über HTTP ausgelösten Funktionen, wie z. B. über Service Bus und Event Hub ausgelöste Funktionen.

Weitere Informationen über den Einsatz in mehreren Regionen finden Sie in der Anleitung [Hochverfügbare Webanwendung für mehrere Regionen](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="redundancy-for-http-trigger-functions"></a>Redundanz für HTTP-Triggerfunktionen

Das Aktiv-/Aktivmuster ist das beste Bereitstellungsmodell für HTTP-Triggerfunktionen. In diesem Fall müssen Sie [Azure Front Door](../frontdoor/front-door-overview.md) verwenden, um Anforderungen zwischen beiden Regionen zu koordinieren. Azure Front Door kann HTTP-Anfragen zwischen Funktionen, die in mehreren Regionen ausgeführt werden, weiterleiten und in regelmäßigen Abständen den Zustand der einzelnen Endpunkte überprüfen. Wenn eine Funktion in einer Region nicht mehr auf Zustandsprüfungen reagiert, nimmt Azure Front Door sie aus der Rotation und leitet den Datenverkehr nur an die verbleibenden gesunden Funktionen weiter.  

![Architektur für Azure Front Door und Funktionen](media/functions-geo-dr/front-door.png)  

## <a name="redundancy-for-non-http-trigger-functions"></a>Redundanz für Nicht-HTTP-Triggerfunktionen

Redundanz für Funktionen, die Ereignisse von anderen Diensten verbrauchen, erfordert ein anderes Muster, das mit dem Failover-Muster der entsprechenden Dienste arbeitet. 

### <a name="activepassive-redundancy-for-non-http-trigger-functions"></a>Aktive/passive Redundanz für Nicht-HTTP-Triggerfunktionen

Aktiv/passiv bietet eine Möglichkeit, dass nur eine einzige Funktion jede Nachricht verarbeitet, stellt aber einen Mechanismus zur Verfügung, um im Notfall ein Failover in eine Sekundärregion durchzuführen. Funktions-Apps arbeiten mit dem Failover-Verhalten der Partnerdienste, wie [Azure Service Bus Geo-Recovery](../service-bus-messaging/service-bus-geo-dr.md) und [Azure Event Hubs Geo-Recovery](../event-hubs/event-hubs-geo-dr.md). Die sekundäre Funktions-App gilt als _passiv_, da der Failover-Dienst, mit dem sie verbunden ist, derzeit nicht aktiv ist, sodass die Funktions-App im Wesentlichen _im Leerlauf_ inaktiv ist.

Betrachten Sie eine Beispieltopologie mit einem Azure Event Hubs-Auslöser. In diesem Fall erfordert das Aktiv-Passiv-Muster die Einbeziehung der folgenden Komponenten:

* Azure Event Hub ist für eine primäre und eine sekundäre Region bereitgestellt.
* [Geografisches Disaster](../service-bus-messaging/service-bus-geo-dr.md) aktiviert, um den primären und sekundären Event Hub zu koppeln. Dadurch wird auch ein _Alias_ erstellt, mit dem Sie eine Verbindung zu Event-Hubs herstellen und von einem primären zu einem sekundären Hub wechseln können, ohne die Verbindungsdaten zu ändern.
* Funktions-Apps werden sowohl in der primären als auch in der sekundären (Failover-)Region eingesetzt, wobei die App in der sekundären Region im Wesentlichen inaktiv ist, da keine Nachrichten dorthin gesendet werden.
* Die Funktions-App triggert auf die *direkte* (Nicht-Alias-)Verbindungszeichenfolge für ihren jeweiligen Event Hub. 
* Verleger für den Event Hub sollten ihre Veröffentlichungen über die Aliasverbindungszeichenfolge ausführen. 

![Beispielarchitektur für aktiv/passiv](media/functions-geo-dr/active-passive.png)

Vor dem Failover leiten Verleger, die an den gemeinsamen Alias senden, zum primären Event Hub weiter. Die primäre Funktions-App lauscht exklusiv auf den primären Event Hub. Die sekundäre Funktions-App ist passiv und befindet sich im Leerlauf. Sobald der Failover eingeleitet wird, werden Verleger, die an den gemeinsamen Alias senden, an den sekundären Event Hub weitergeleitet. Die sekundäre Funktions-App wird nun aktiv und löst automatisch aus.  Ein effektives Failover zu einer sekundären Region kann vollständig vom Event Hub gesteuert werden, wobei die Funktionen nur aktiv werden, wenn der jeweilige Event Hub aktiv ist.

Lesen Sie mehr über Informationen und Überlegungen zum Failover mit [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) und [Event Hubs](../event-hubs/event-hubs-geo-dr.md).

### <a name="activeactive-redundancy-for-non-http-trigger-functions"></a>Aktiv-/Aktivredundanz für Nicht-HTTP-Triggerfunktionen

Für Funktionen, die nicht über HTTP ausgelöst werden, können Sie immer noch aktive/aktive Bereitstellungen erreichen. Sie müssen jedoch berücksichtigen, wie die beiden aktiven Regionen miteinander interagieren oder koordiniert werden. Wenn Sie dieselbe Funktionsanwendung in zwei Regionen bereitstellen, die jeweils auf dieselbe Service-Bus-Warteschlange zugreifen, würden sie als konkurrierende Verbraucher beim Abbau dieser Warteschlange agieren. Dies bedeutet zwar, dass jede Nachricht nur von einer der beiden Instanzen verarbeitet wird, aber auch, dass es immer noch einen einzigen Fehlerpunkt auf der einzelnen Servicebus-Instanz gibt. 

Sie könnten stattdessen zwei Service-Bus-Warteschlangen einrichten, eine in einer primären Region und eine in einer sekundären Region. In diesem Fall könnten Sie zwei Funktions-Apps haben, die jeweils auf die in ihrer Region aktive Servicebus-Warteschlange verweisen. Die Herausforderung bei dieser Topologie besteht darin, wie die Nachrichten der Warteschlange zwischen den beiden Regionen verteilt werden.  Häufig bedeutet dies, dass jeder Herausgeber versucht, eine Nachricht in *beiden* Regionen zu veröffentlichen, und jede Nachricht von beiden aktiven Funktions-App verarbeitet wird. Dadurch entsteht zwar das gewünschte Aktiv/Aktiv-Muster, aber es ergeben sich auch andere Herausforderungen in Bezug auf die Duplizierung von Rechenleistung und die Frage, wann und wie Daten konsolidiert werden. Aufgrund dieser Herausforderungen empfehlen wir die Verwendung des Aktiv-/Passiv-Musters für Nicht-HTTPS-Triggerfunktionen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Azure Front Door](../frontdoor/quickstart-create-front-door.md)
* [Überlegungen zum Event Hubs-Failover](../event-hubs/event-hubs-geo-dr.md#considerations)
