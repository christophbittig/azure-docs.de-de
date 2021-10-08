---
title: Bewährte Methoden zur Ausfallsicherheit
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie Ihren Azure Cache for Redis-Verbindungen resilient machen können.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: a0dd6e3e8f4c2a7645da1ceccf77f7607d2b84b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656947"
---
# <a name="connection-resilience"></a>Verbindungsresilienz

## <a name="retry-commands"></a>Wiederholungsbefehle

Konfigurieren Ihrer Clientverbindungen für Wiederholungsbefehle mit exponentiellem Backoff Weitere Informationen finden Sie in den [Wiederholungsrichtlinien](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis).

## <a name="test-resiliency"></a>Testresilienz

Testen Sie die Resilienz Ihres Systems bei Verbindungsunterbrechungen mithilfe eines [Neustarts](cache-administration.md#reboot), um einen Patch zu simulieren. Weitere Informationen zum Testen der Leistung finden Sie unter [Leistungstests](cache-best-practices-performance.md).

## <a name="configure-appropriate-timeouts"></a>Konfigurieren von entsprechenden Timeouts

Zwei Timeoutwerte sind bei der Verbindungsresilienz wichtig: [Connect timeout](#connect-timeout) (Verbindungstimeout) und [command timeout](#command-timeout) (Befehlstimeout).

### <a name="connect-timeout"></a>Connect timeout

Das `connect timeout` ist die Zeit, die ein Client wartet, bis er eine Verbindung mit dem Redis-Server herstellt. Konfigurieren Sie Ihre Clientbibliothek so, dass sie ein `connect timeout` von fünf Sekunden verwendet, damit das System auch bei hoher CPU-Auslastung genügend Zeit hat, eine Verbindung herzustellen.

Bei einem niedrigeren `connection timeout` ist nicht sichergestellt, dass die Verbindung in diesem Zeitraum hergestellt werden kann. Bei Beeinträchtigungen (hohe Client- oder Server-CPU-Auslastung usw.) hat ein kurzer `connection timeout`-Wert zur Folge, dass bei dem Verbindungsversuch ein Fehler auftritt. Dieses Verhalten macht eine schlechte Situation häufig noch schlimmer. Statt zu helfen, verschlimmern kürzere Timeouts das Problem, weil sie ein Neustarten des Prozesses der Verbindungsherstellung erzwingen und damit zu einer *Verbinden -> Fehler -> Wiederholen*-Schleife führen können.

### <a name="command-timeout"></a>Befehlstimeout

Die meisten Clientbibliotheken verfügen über eine weitere Timeoutkonfiguration für `command timeouts`. Dies ist die Zeitspanne, die der Client auf eine Antwort vom Redis-Server wartet. Wir empfehlen zwar eine anfängliche Einstellung von weniger als fünf Sekunden, aber sie sollten je nach Szenario und der Größe der Werte, die in Ihrem Cache gespeichert sind, den höheren oder niedrigeren `command timeout` festlegen.

Ist das `command timeout` zu kurz, kann die Verbindung instabil wirken. Wenn der `command timeout`-Wert jedoch zu groß ist, muss Ihre Anwendung möglicherweise lange warten, um herauszufinden, ob der Befehl einen Timeout verursacht.

## <a name="avoid-client-connection-spikes"></a>Vermeiden von Spitzen bei Clientverbindungen

Vermeiden Sie es, viele Verbindungen gleichzeitig zu erstellen, wenn sie nach einem Verbindungsverlust erneut hergestellt werden. Ähnlich wie [kurze Verbindungstimeouts](#configure-appropriate-timeouts) zu längeren Ausfällen führen können, kann das gleichzeitige Starten vieler Verbindungsversuche auch die Serverauslastung erhöhen und die Dauer der erfolgreichen Verbindungsherstellung für alle Clients verlängern.

Wenn Sie viele Clientinstanzen erneut verbinden, sollten Sie erwägen, die neuen Verbindungen zu staffeln, um eine starke Spitze bei der Anzahl verbundener Clients zu vermeiden.

> [!NOTE]
> Wenn Sie die Clientbibliothek `StackExchange.Redis` verwenden, legen Sie `abortConnect` in Ihrer Verbindungszeichenfolge auf `false` fest.  Wir empfehlen, `ConnectionMultiplexer` die Neuverbindung zu überlassen. Weitere Informationen finden Sie unter [Bewährte Methoden für StackExchange.Redis](/azure/azure-cache-for-redis/cache-management-faq#stackexchangeredis-best-practices).

## <a name="avoid-leftover-connections"></a>Vermeiden Sie übrig bleibende Verbindungen

Caches weisen Grenzwerte für die Anzahl von Clientverbindungen pro Cacheebene auf. Stellen Sie sicher, dass die Clientanwendung Verbindungen, die sie schließt, neu erstellt und die alten Verbindungen entfernt.

## <a name="advance-maintenance-notification"></a>Benachrichtigungen zu erweiterten Wartungen

Verwenden Sie Benachrichtigungen, um mehr über bevorstehende Wartungen zu erfahren. Weitere Informationen finden Sie unter [Kann ich im Voraus über eine geplante Wartung informiert werden](cache-failover.md#can-i-be-notified-in-advance-of-planned-maintenance).

## <a name="schedule-maintenance-window"></a>Zeitplan für Wartungsfenster

Passen Sie Ihre Cacheeinstellungen an die Wartung an. Weitere Informationen zum Erstellen eines Wartungsfensters, um negative Auswirkungen auf Ihren Cache zu reduzieren, finden Sie unter [Planen von Updates](cache-administration.md#schedule-updates).

## <a name="more-design-patterns-for-resilience"></a>Weitere Entwurfsmuster für Resilienz

Wenden Sie Entwurfsmuster für Resilienz an. Weitere Informationen finden Sie unter [Wie mache ich meine Anwendung resilient](cache-failover.md#how-do-i-make-my-application-resilient).

## <a name="idle-timeout"></a>Leerlauftimeout

Azure Cache for Redis verfügt derzeit über ein Leerlauftimeout von 10 Minuten für Verbindungen, sodass die Einstellung für das Leerlauftimeout in Ihrer Clientanwendung weniger als 10 Minuten betragen sollte. Die meisten gängigen Clientbibliotheken verfügen über eine Konfigurationseinstellung, mit der Clientbibliotheken `PING`-Befehle von Redis automatisch und in regelmäßigen Abständen an einen Redis-Server senden können. Wenn Sie jedoch Clientbibliotheken ohne diese Art von Einstellung verwenden, sind Kundenanwendungen selbst dafür verantwortlich, die Verbindung aktiv zu halten.

## <a name="next-steps"></a>Nächste Schritte

- [Bewährte Methoden für die Entwicklung](cache-best-practices-development.md)
- [Häufig gestellte Fragen zur Azure Cache for Redis-Entwicklung](cache-development-faq.yml)
- [Failover und Patching](cache-failover.md)
