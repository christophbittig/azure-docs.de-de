---
title: Behandeln von serverseitigen Problemen bei Azure Cache for Redis
description: Erfahren Sie, wie Sie häufige serverseitige Probleme bei Azure Cache for Redis beheben, z. B. hohe Arbeitsspeicherauslastung, hohe CPU-Auslastung, zeitintensive Befehle oder Bandbreiteneinschränkungen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 9c50ac01eb95672b946daf9916f83743d2156b01
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537401"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Behandeln von serverseitigen Problemen bei Azure Cache for Redis

In diesem Abschnitt wird das Behandeln von Problemen beschrieben, die wegen eines Zustands von Azure Cache for Redis auf dem virtuellen Computer auftreten, der als Host verwendet wird.

- [Hohe Arbeitsspeicherauslastung auf dem Redis-Server](#memory-pressure-on-redis-server)
- [Hohe CPU-Auslastung oder hohe Serverauslastung](#high-cpu-usage-or-server-load)
- [Befehle mit langer Ausführungsdauer](#long-running-commands)
- [Serverseitige Bandbreitenbegrenzung](#server-side-bandwidth-limitation)

> [!NOTE]
> Etliche Problembehandlungsschritte in diesem Leitfaden enthalten Anleitungen zum Ausführen von Redis-Befehlen und zum Überwachen verschiedener Leistungsmetriken. Weitere Informationen und Anleitungen finden Sie in den Artikeln im Abschnitt [Weitere Informationen](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Hohe Arbeitsspeicherauslastung auf dem Redis-Server

Wenn der Arbeitsspeicher auf der Serverseite sehr stark ausgelastet ist, führt dies zu Leistungsproblemen aller Art und unter Umständen auch zu einer verzögerten Verarbeitung von Anforderungen. Wenn es zu hoher Arbeitsspeicherauslastung kommt, kann das System beginnen, Daten auf den Datenträger auszulagern. Diese sogenannten _Seitenfehler_ bewirken eine deutliche Verlangsamung des Systems. Eine zu hohe Speicherauslastung kann verschiedene Ursachen haben:

- Der Cache wird bis fast zu seiner maximalen Kapazität mit Daten gefüllt.
- Redis stellt eine hohe Fragmentierung des Arbeitsspeichers fest. Diese Fragmentierung wird am häufigsten durch das Speichern großer Objekte verursacht, da Redis für kleine Objekte optimiert ist.

Redis macht zwei Statistiken durch den Befehl [INFO](https://redis.io/commands/info) verfügbar, der Ihnen bei der Identifizierung des folgenden Problems helfen kann: „used_memory“ und „used_memory_rss“. Sie können mithilfe des Portals [diese Metriken anzeigen](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer).

Sie können mehrere Änderungen vornehmen, um die Speicherauslastung in einem gesunden Rahmen zu halten:

- [Konfigurieren Sie eine Speicherrichtlinie](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , und legen Sie Ablauffristen für Ihre Schlüssel fest. Diese Richtlinie reicht möglicherweise nicht aus, wenn Fragmentierung vorliegt.
- [Konfigurieren Sie einen Wert für „maxmemory-reserved“](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , der groß genug ist, um die Speicherfragmentierung zu kompensieren.
- Teilen Sie große zwischengespeicherte Objekte in kleinere verknüpfte Objekte auf.
- [Erstellen von Warnungen](cache-how-to-monitor.md#alerts) bei Metriken wie „verwendeter Arbeitsspeicher“, um frühzeitig über mögliche Beeinträchtigungen benachrichtigt zu werden.
- [Skalieren](cache-how-to-scale.md) Sie Ihr System auf einen größeren Cache mit größerer Arbeitsspeicherkapazität hoch.
- [Skalieren](cache-how-to-scale.md) Sie Ihr System auf einen größeren Cache mit größerer Arbeitsspeicherkapazität hoch. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Planung für Azure Cache for Redis](./cache-planning-faq.yml).

## <a name="high-cpu-usage-or-server-load"></a>Hohe CPU-Auslastung oder hohe Serverauslastung

Eine hohe Serverauslastung oder CPU-Auslastung bedeutet, dass der Server Anforderungen nicht rechtzeitig verarbeiten kann. Der Server weist möglicherweise langsame Reaktionszeiten auf und kann mit den Anforderungsraten nicht Schritt halten.

[Überwachen Sie Metriken](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer) wie CPU- oder Serverauslastung. Beobachten Sie die Spitzen bei der CPU-Auslastung, denn sie korrespondieren mit Timeouts.

Sie können mehrere Änderungen vornehmen, um hohe Serverauslastung zu mindern:

- Untersuchen Sie, was CPU-Spitzen verursacht, z. B. [Befehle mit langer Ausführungsdauer](#long-running-commands) oder Seitenfehler aufgrund hoher Arbeitsspeicherauslastung.
- [Erstellen Sie Warnungen](cache-how-to-monitor.md#alerts) bei Metriken wie CPU- oder Serverauslastung, um frühzeitig über mögliche Beeinträchtigungen benachrichtigt zu werden.
- [Skalieren](cache-how-to-scale.md) Sie auf mehr Shards auf, um die Last auf mehrere Redis-Prozesse zu verteilen, oder skalieren Sie auf einen größeren Cache mit mehr CPU-Kernen hoch. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Planung für Azure Cache for Redis](./cache-planning-faq.yml).

## <a name="long-running-commands"></a>Befehle mit langer Ausführungsdauer

Einige Redis-Befehle sind aufwendiger auszuführen als andere. In der [Dokumentation zu den Redis-Befehlen](https://redis.io/commands) wird die Zeitkomplexität jedes einzelnen Befehls angegeben. Da Redis-Befehle in einem Singlethread verarbeitet werden, blockiert ein Befehl, dessen Ausführung eine Weile dauert, alle Folgebefehle. Überprüfen Sie die Befehle, die Sie an Ihren Redis-Server ausgeben, um die Auswirkungen auf die Leistung besser zu verstehen. Beispielsweise wird der Befehl [KEYS](https://redis.io/commands/keys) häufig verwendet, ohne zu wissen, dass es sich um einen O(N)-Vorgang handelt. Sie können KEYSA vermeiden, indem Sie [SCAN](https://redis.io/commands/scan) verwenden, um CPU-Spitzen zu verringern.

Mithilfe des Befehls[SLOWLOG](https://redis.io/commands/slowlog) können Sie speicherintensive Befehle messen, die auf dem Server ausgeführt werden.

## <a name="server-side-bandwidth-limitation"></a>Serverseitige Bandbreitenbegrenzung

Verschiedene Cachegrößen verfügen über unterschiedliche Netzwerkbandbreitenkapazitäten. Wenn der Server die verfügbare Bandbreite überschreitet, werden die Daten nicht so schnell an den Client gesendet wie erwartet. Es könnte bei Anforderungen von Clients zu Timeouts kommen, weil der Server die Daten nicht schnell genug an den Client übertragen kann.

Die Metriken „Cache Read“ (Cachelesevorgänge) und „Cache Write“ (Cacheschreibvorgänge) können verwendet werden, um festzustellen, wie viel Bandbreite auf der Serverseite verwendet wird. Sie können [diese Metriken im Portal anzeigen](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer).

So mindern Sie Situationen, in denen der Netzwerkbandbreitenverbrauch am Rande der maximalen Kapazität liegt

- Ändern Sie das Aufrufverhalten des Clients, um die Beanspruchung des Netzwerks zu reduzieren.
- [Erstellen Sie Warnungen](cache-how-to-monitor.md#alerts) bei Metriken wie „Cache Read“ oder „Cache Write“, um frühzeitig über mögliche Beeinträchtigungen benachrichtigt zu werden.
- [Skalieren](cache-how-to-scale.md) Sie Ihr System auf einen größeren Cache mit größerer Netzwerkbandbreite hoch. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Planung für Azure Cache for Redis](./cache-planning-faq.yml).

## <a name="additional-information"></a>Zusätzliche Informationen

- [Behandeln von clientseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-client.md)
- [Auswählen der richtigen Ebene](cache-overview.md#choosing-the-right-tier)
- [Wie kann ich die Leistung meines Caches messen und testen?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
- [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md)
- [Wie führe ich Redis-Befehle aus?](cache-development-faq.yml#how-can-i-run-redis-commands-)