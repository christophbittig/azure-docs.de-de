---
title: Problembehandlung von AMQP-Fehlern in Azure Service Bus | Microsoft-Dokumentation
description: Enthält eine Liste der AMQP-Fehler, die Sie bei der Verwendung von Azure Service Bus erhalten können, sowie die Ursache für diese Fehler.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0a481f0dccc7a409ada51fb809065fd58db31aed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570947"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP-Fehler in Azure Service Bus
Dieser Artikel beschreibt einige der Fehler, die bei der Verwendung von AMQP mit Azure Service Bus auftreten können. Sie stellen alle das Standardverhalten des Diensts dar. Sie können sie vermeiden, indem Sie Sende-/Empfangsaufrufe für die Verbindung bzw. den Link vornehmen, die automatisch die Verbindung oder den Link wiederherstellen.

## <a name="link-is-closed"></a>Link wurde geschlossen 
Sie erhalten den folgenden Fehler, wenn die AMQP-Verbindung und der Link aktiv sind, aber 10 Minuten lang keine Aufrufe (z.B. zum Senden oder Empfangen) über den Link erfolgen. Der Link wird daher geschlossen. Die Verbindung ist weiterhin geöffnet.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Verbindung wurde geschlossen
Sie erhalten den folgenden Fehler für die AMQP-Verbindung, wenn alle Links in der Verbindung geschlossen wurden, weil es keine Aktivität gab (Leerlauf) und innerhalb von 5 Minuten kein neuer Link erstellt wurde.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link wird nicht erstellt 
Sie erhalten diesen Fehler, wenn eine neue AMQP-Verbindung erstellt wird, aber innerhalb von einer Minute nach der Erstellung der AMQP-Verbindung kein Link erstellt wird.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Nächste Schritte

Klicken Sie auf die folgenden Links, um mehr über AMQP und Service Bus zu erfahren:

* [Übersicht über Service Bus AMQP]
* [AMQP 1.0 – Protokollleitfaden]
* [AMQP in Service Bus für Windows Server]

[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0 – Protokollleitfaden]: service-bus-amqp-protocol-guide.md
[AMQP in Service Bus für Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)