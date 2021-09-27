---
title: Behandeln von AMQP-Fehlern in Azure Event Hubs | Microsoft-Dokumentation
description: Enthält eine Liste der AMQP-Fehler, die bei der Verwendung von Azure Event Hubs auftreten können, sowie die Ursache für diese Fehler.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 7e73c85f65a6ea9edef36ab810110e576b7ea0c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649395"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>AMQP-Fehler in Azure Event Hubs
In diesem Artikel werden einige der Fehler beschrieben, die bei der Verwendung von AMQP mit Azure Event Hubs auftreten können. Sie stellen alle das Standardverhalten des Diensts dar. Sie können sie vermeiden, indem Sie Sende-/Empfangsaufrufe für die Verbindung bzw. den Link vornehmen, die automatisch die Verbindung oder den Link wiederherstellen.

## <a name="link-is-closed"></a>Link wurde geschlossen 
Sie erhalten den folgenden Fehler, wenn die AMQP-Verbindung und der Link aktiv sind, aber 30 Minuten lang keine Aufrufe (z. B. zum Senden oder Empfangen) über den Link erfolgen. Der Link wird daher geschlossen. Die Verbindung ist weiterhin geöffnet.

„AMQP:link: detach-forced: Der Link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' wurde vom Broker aufgrund von Fehlern beim Herausgeber getrennt (link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:30:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp: 2/16/2018 11:10:40 PM“

## <a name="connection-is-closed"></a>Verbindung wurde geschlossen
Sie erhalten den folgenden Fehler für die AMQP-Verbindung, wenn alle Links in der Verbindung geschlossen wurden, da es keine Aktivität gab (Leerlauf) und innerhalb von 5 Minuten kein neuer Link erstellt wurde.

„Error{condition=amqp:connection:forced, description='Die Verbindung war länger als die zulässigen 300.000 ms im Leerlauf und wird vom Container getrennt 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}“

## <a name="link-isnt-created"></a>Link wird nicht erstellt 
Sie erhalten diesen Fehler, wenn eine neue AMQP-Verbindung erstellt wird, aber innerhalb von einer Minute nach der Erstellung der AMQP-Verbindung kein Link erstellt wird.

„Error{condition=amqp:connection:forced, description='Die Verbindung war länger als die zulässigen 60.000 ms im Leerlauf und wird vom Container getrennt 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}“

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu AMQP finden Sie im [Leitfaden zum Protokoll AMQP 1.0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
