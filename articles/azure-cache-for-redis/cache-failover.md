---
title: Failover und Patching – Azure Cache for Redis
description: Informationen zum Failover, Patchen und Updateprozess für Azure Cache for Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.openlocfilehash: 67b3ad49033a2fb4708b93c65e0d9f7110726cee
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850962"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover und Patching für Azure Cache for Redis

Zum Erstellen resilienter und erfolgreicher Clientanwendungen ist es wichtig, zu verstehen, wie das Failover für den Azure Cache for Redis-Dienst funktioniert. Ein Failover kann Teil geplanter Verwaltungsvorgänge sein oder durch ungeplante Hardware- oder Netzwerkausfälle verursacht werden. Ein Cachefailover erfolgt häufig, wenn der Verwaltungsdienst die Azure Cache for Redis-Binärdateien patcht.

Im vorliegenden Artikel finden Sie Antworten auf die folgenden Fragen:  

- Was ist ein Failover?
- Wie erfolgt das Failover während eines Patchvorgangs?
- Wie erstelle ich eine resiliente Clientanwendung?

## <a name="what-is-a-failover"></a>Was ist ein Failover?

Wir beginnen mit einer Übersicht über das Failover für Azure Cache for Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Kurze Zusammenfassung der Cachearchitektur

Ein Cache wird aus mehreren virtuellen Computern mit separaten und privaten IP-Adressen erstellt. Jeder virtuelle Computer, der auch als Knoten bezeichnet wird, ist mit einem freigegebenen Load Balancer mit einer einzelnen virtuellen IP-Adresse verbunden. Jeder Knoten führt den Redis-Serverprozess aus, und auf jeden Knoten kann über den Hostnamen und die Redis-Ports zugegriffen werden. Jeder Knoten gilt entweder als primärer oder Replikatknoten. Wenn eine Clientanwendung eine Verbindung mit einem Cache herstellt, durchläuft der zugehörige Datenverkehr diesen Load Balancer und wird automatisch an den primären Knoten weitergeleitet.

In einem Basic-Cache ist der einzelne Knoten immer auch der primäre. Ein Standard- oder Premium-Cache umfasst zwei Knoten: einen ausgewählten primären Knoten und einen Replikatknoten. Da Standard- und Premium-Caches mehrere Knoten umfassen, ist ein Knoten möglicherweise nicht verfügbar, während auf dem anderen weiterhin Anforderungen verarbeitet werden. Gruppierte Caches bestehen aus vielen Shards, die jeweils unterschiedliche primäre und Replikatknoten umfassen. Ein Shard kann ausgefallen sein, während die anderen verfügbar bleiben.

> [!NOTE]
> Ein Basic-Cache verfügt nicht über mehrere Knoten und für seine Verfügbarkeit gibt es keine Vereinbarung zum Service Level (SLA). Basic-Caches werden nur zu Entwicklungs- und Testzwecken empfohlen. Verwenden Sie einen Standard- oder Premium-Cache für eine Bereitstellung mit mehreren Knoten, um die Verfügbarkeit zu erhöhen.

### <a name="explanation-of-a-failover"></a>Erläuterung eines Failovers

Ein Failover erfolgt, wenn sich ein Replikatknoten zum primären Knoten heraufstuft und der alte primäre Knoten vorhandene Verbindungen schließt. Nachdem der primäre Knoten wieder verfügbar ist, erkennt er den Rollenwechsel und stuft sich selbst zum Replikatknoten herab. Anschließend stellt er eine Verbindung mit dem neuen primären Knoten her und synchronisiert Daten. Ein Failover kann geplant oder ungeplant sein.

Ein *geplantes Failover* kann zu zwei verschiedenen Zeitpunkten stattfinden:

- Bei Systemupdates, wie z. B. dem Aufspielen von Redis-Patches oder Betriebssystemupgrades.  
- Bei Verwaltungsvorgängen, wie z. B. Skalierungen und Neustarts.

Da die Knoten eine Vorabbenachrichtigung zum Update erhalten, können die Rollen getauscht werden, und der Load Balancer kann schnell entsprechend der Änderung aktualisiert werden. Ein geplantes Failover wird normalerweise in weniger als einer Sekunde abgeschlossen.

Ein *ungeplantes Failover* kann aufgrund eines Hardwarefehlers, eines Netzwerkfehlers oder anderer unerwarteter Ausfälle des primären Knotens erfolgen. Der Replikatknoten stuft sich selbst zum primären Knoten herauf, der Prozess dauert jedoch länger. Ein Replikatknoten muss zunächst erkennen, dass der zugehörige primäre Knoten nicht verfügbar ist, bevor der Failoverprozess gestartet werden kann. Der Replikatknoten muss außerdem überprüfen, ob dieser ungeplante Ausfall nicht vorübergehend oder lokal ist, um ein unnötiges Failover zu vermeiden. Diese Verzögerung bei der Erkennung bedeutet, dass ein ungeplantes Failover normalerweise innerhalb von 10 bis 15 Sekunden abgeschlossen ist.

## <a name="how-does-patching-occur"></a>Wie erfolgt das Patchen?

Der Azure Cache for Redis-Dienst aktualisiert den Cache regelmäßig mit den neuesten Plattformfunktionen und Korrekturen. Zum Patchen eines Caches führt der Dienst die folgenden Schritte aus:

1. Der Verwaltungsdienst wählt einen zu patchenden Knoten aus.
1. Wenn der ausgewählte Knoten ein primärer Knoten ist, stuft sich der zugehörige Replikatknoten kooperativ selbst herauf. Diese Höherstufung gilt als geplantes Failover.
1. Der ausgewählte Knoten wird neu gestartet, um die neuen Änderungen zu übernehmen, und ist als Replikatknoten wieder verfügbar.
1. Der Replikatknoten stellt eine Verbindung mit dem primären Knoten her und synchronisiert Daten.
1. Wenn die Datensynchronisierung abgeschlossen ist, wird der Patchprozess für die verbleibenden Knoten wiederholt.

Da das Patchen ein geplanter Failovervorgang ist, stuft sich der Replikatknoten schnell selbst hoch, um zum primären Knoten zu werden. Dann beginnt der Knoten damit, Anforderungen zu verarbeiten und neue Verbindungen herzustellen. Basic-Caches verfügen über keinen Replikatknoten und sind bis zum Abschluss des Updates nicht verfügbar. Jeder Shard eines gruppierten Caches wird separat gepatcht und schließt keine Verbindungen mit einem anderen Shard.

> [!IMPORTANT]
> Knoten werden einzeln gepatcht, um Datenverluste zu vermeiden. Bei Basic-Caches kommt es zu Datenverlust. Bei gruppierten Caches werden die Shards jeweils einzeln gepatcht.

Mehrere Caches in derselben Ressourcengruppe und Region werden ebenfalls einzeln gepatcht.  Caches, die sich in unterschiedlichen Ressourcengruppen oder Regionen befinden, können gleichzeitig gepatcht werden.

Da die vollständige Datensynchronisierung ausgeführt wird, bevor der Prozess wiederholt wird, ist es unwahrscheinlich, dass bei der Verwendung eines Standard- oder Premium-Caches Datenverluste auftreten. Sie können Datenverlusten weiter vorbeugen, indem Sie die Daten [exportieren](cache-how-to-import-export-data.md#export) und die [Datenpersistenz](cache-how-to-premium-persistence.md) aktivieren.

## <a name="additional-cache-load"></a>Zusätzliche Cacheauslastung

Bei jedem Failover müssen in den Standard- und Premium-Caches Daten von einem auf den anderen Knoten repliziert werden. Diese Replikation führt zu einer gewissen Erhöhung der Auslastung von Serverspeicher und Server-CPU. Wenn die Cache-Instanz bereits stark ausgelastet ist, können Clientanwendungen eine höhere Latenz aufweisen. Im Extremfall treten in Clientanwendungen möglicherweise Timeoutausnahmen auf. Um die Auswirkungen der höheren Auslastung zu minimieren, [konfigurieren](cache-configure.md#memory-policies) Sie die `maxmemory-reserved`-Einstellung des Caches.

## <a name="how-does-a-failover-affect-my-client-application"></a>Wie wirkt sich ein Failover auf meine Clientanwendung aus?

Clientanwendungen können einige Fehler von ihrer Azure Cache For Redis-Anwendung erhalten. Die Anzahl der Fehler, die eine Clientanwendung verzeichnet, hängt davon ab, wie viele Vorgänge zum Zeitpunkt des Failovers in dieser Verbindung ausstanden. Bei jeder Verbindung, die über den Knoten weitergeleitet wird, dessen Verbindungen geschlossen wurden, treten Fehler auf.

Viele Clientbibliotheken können bei Verbindungsabbrüchen verschiedene Arten von Fehlern auslösen, darunter:

- Timeoutausnahmen
- Verbindungsausnahmen
- Socketausnahmen

Anzahl und Typ der Ausnahmen hängen davon ab, wo sich die Anforderung im Codepfad befindet, wenn der Cache die Verbindungen schließt. Beispielsweise tritt bei einem Vorgang, bei dem eine Anforderung gesendet wird, für die zum Zeitpunkt des Failovers jedoch noch keine Antwort zurückgegeben wurde, möglicherweise eine Timeoutausnahme auf. Bei neuen Anforderungen für das geschlossene Verbindungsobjekt treten Verbindungsausnahmen auf, bis die Verbindung erfolgreich wiederhergestellt wird.

Die meisten Clientbibliotheken versuchen, erneut eine Verbindung mit dem Cache herzustellen, wenn sie entsprechend konfiguriert sind. Durch unvorhergesehene Fehler können die Bibliotheksobjekte jedoch gelegentlich in einen nicht wiederherstellbaren Zustand gesetzt werden. Wenn Fehler länger als eine vorkonfigurierte Zeitspanne andauern, sollte das Verbindungsobjekt neu erstellt werden. In Microsoft .NET und anderen objektorientierten Sprachen kann die Neuerstellung der Verbindung ohne Neustart der Anwendung durch Verwendung eines [ForceReconnect-Musters](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis) erreicht werden.

### <a name="can-i-be-notified-in-advance-of-planned-maintenance"></a>Kann ich im Voraus über eine geplante Wartung informiert werden?

Azure Cache for Redis veröffentlicht Benachrichtigungen auf einem Kanal mit Veröffentlichungs- und Abonnementarchitektur (pub/sub) namens [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) etwa 30 Sekunden vor geplanten Aktualisierungen. Bei den Benachrichtigungen handelt es um Laufzeitbenachrichtigungen.

Die Benachrichtigungen gelten für Anwendungen, die Leistungsschutzschalter verwenden, um den Cache zu umgehen, oder Anwendungen, die Befehle puffern. Beispielsweise könnte der Cache während geplanter Updates umgangen werden.

Der `AzureRedisEvents`-Kanal ist kein Mechanismus, der Sie Tage oder Stunden im Voraus benachrichtigen kann. Der Kanal kann Clients über anstehende geplante Serverwartungsereignisse benachrichtigen, die sich auf die Serververfügbarkeit auswirken können.

Viele beliebte Redis-Clientbibliotheken unterstützen das Abonnieren von Pub-/Sub-Kanälen. Das Empfangen von Benachrichtigungen vom `AzureRedisEvents`-Kanal ist in der Regel eine einfache Ergänzung Ihrer Clientanwendung.

Sobald Ihre Anwendung `AzureRedisEvents` abonniert hat, erhält sie 30 Sekunden, bevor ein Knoten von einem Wartungsereignis betroffen ist, eine Benachrichtigung. Die Benachrichtigung enthält Details zum bevorstehenden Ereignis und gibt an, ob es sich auf einen primären Knoten oder auf einen Replikatknoten auswirkt.

Eine weitere Benachrichtigung wird Minuten später gesendet, nachdem der Wartungsvorgang abgeschlossen wurde.

Ihre Anwendung verwendet den Inhalt in der Benachrichtigung, um Maßnahmen zu ergreifen, um die Verwendung des Caches während der Wartung zu vermeiden. Ein Cache kann ein Leistungsschutzschalter-Muster implementieren, bei dem der Datenverkehr während des Wartungsvorgangs den Cache umgeht. Stattdessen wird Datenverkehr direkt an einen persistenten Speicher gesendet. Die Benachrichtigung ist nicht dazu gedacht, einer Person Zeit zu geben, um gewarnt zu werden und selbst Maßnahmen zu ergreifen.

In den meisten Fällen muss Ihre Anwendung weder `AzureRedisEvents` abonnieren noch auf Benachrichtigungen reagieren. Stattdessen wird empfohlen, die [integrierte Resilienz](#build-in-resiliency) zu implementieren.

Mit einer ausreichenden Resilienz können Anwendungen jeden kurzen Verbindungsverlust oder die Nichtverfügbarkeit des Caches, wie sie bei der Knotenwartung auftritt, problemlos bewältigen. Es ist auch möglich, dass Ihre Anwendung aufgrund von Netzwerkfehlern oder anderen Ereignissen unerwartet und ohne Warnung von `AzureRedisEvents` die Verbindung mit dem Cache verliert.

Wir empfehlen das Abonnieren von `AzureRedisEvents` nur in einigen wenigen bemerkenswerten Fällen:

- Für Anwendungen mit extremen Leistungsanforderungen, bei denen auch geringfügige Verzögerungen vermieden werden müssen. In solchen Szenarien könnte der Datenverkehr nahtlos zu einem Sicherungscache umgeleitet werden, bevor die Wartung des aktuellen Caches beginnt.
- Für Anwendungen, die Daten explizit aus Replikaten und nicht aus primären Knoten lesen. Während der Wartung eines Replikationsknotens könnte die Anwendung vorübergehend dazu übergehen, Daten von primären Knoten zu lesen.
- Für Anwendungen, die nicht riskieren können, dass Schreibvorgänge unbemerkt fehlschlagen oder ohne Bestätigung erfolgreich sind, was passieren kann, wenn Verbindungen zu Wartungszwecken geschlossen werden. Wenn diese Fälle zu einem gefährlichen Datenverlust führen würden, kann die Anwendung proaktiv Schreibbefehle anhalten oder umleiten, bevor die Wartung beginnt.

### <a name="client-network-configuration-changes"></a>Änderungen der Clientnetzwerkkonfiguration

Bestimmte Änderungen der clientseitigen Netzwerkkonfiguration können Fehler vom Typ „Keine Verbindung verfügbar“ auslösen. Dies betrifft beispielsweise folgende Änderungen:

- Austauschen der virtuellen IP-Adresse einer Clientanwendung zwischen Staging- und Produktionsslots
- Skalieren der Größe oder Anzahl der Instanzen der Anwendung

Diese Änderungen können zu einem Verbindungsproblem führen, das weniger als eine Minute dauert. Möglicherweise wird dabei die Verbindung Ihrer Clientanwendung mit anderen externen Netzwerkressourcen sowie dem Azure Cache for Redis-Dienst getrennt.

## <a name="build-in-resiliency"></a>Erstellen von Resilienz

Failover lassen sich nicht ganz vermeiden. Schreiben Sie Ihre Clientanwendungen daher so, dass sie gegenüber Verbindungsunterbrechungen und nicht erfolgreichen Anforderungen resilient sind. Von den meisten Clientbibliotheken wird zwar automatisch erneut eine Verbindung mit dem Cacheendpunkt hergestellt, aber nur wenige versuchen, nicht erfolgreiche Anforderungen zu wiederholen. Je nach Anwendungsszenario ist es möglicherweise sinnvoll, Wiederholungslogik mit Backoff zu verwenden.

### <a name="how-do-i-make-my-application-resilient"></a>Wie mache ich meine Anwendung resilient?

Die folgenden Entwurfsmustern helfen Ihnen bei der Erstellung robuster Clients. Das gilt insbesondere für die Trennschalter- und Wiederholungsmuster:

- [Resilienz](/azure/architecture/framework/resiliency/reliability-patterns#resiliency)
- [Wiederholungsanleitung für Azure-Dienste](/azure/architecture/best-practices/retry-service-specific) (bewährte Methoden für Cloudanwendungen)
- [Implementieren von Wiederholungen mit exponentiellem Backoff](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)

Verwenden Sie zum Testen der Resilienz einer Clientanwendung einen [Neustart](cache-administration.md#reboot) als manuellen Auslöser für Verbindungsunterbrechungen.

Darüber hinaus empfiehlt sich die [Planung von Updates](cache-administration.md#schedule-updates) für einen Cache, um Redis-Runtimepatches während bestimmter wöchentlicher Zeitfenster anzuwenden. Bei diesen Zeitfenstern handelt es sich normalerweise um Zeiträume, in denen der Datenverkehr der Clientanwendung gering ist.

Weitere Informationen finden Sie unter [Verbindungsresilienz](cache-best-practices-connection.md).

## <a name="next-steps"></a>Nächste Schritte

- [Planen von Updates](cache-administration.md#schedule-updates) für den Cache
- Testen der Anwendungsresilienz durch einen [Neustart](cache-administration.md#reboot)
- [Konfigurieren](cache-configure.md#memory-policies) von Speicherreservierungen und -richtlinien
