---
title: Bewährte Methoden für Arbeitsspeicherverwaltung
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie Ihren Azure Cache for Redis-Arbeitsspeicher effektiv verwalten.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 613d5dd906795b59465458565a00cd37b7374e65
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115425"
---
# <a name="memory-management"></a>Speicherverwaltung

## <a name="eviction-policy"></a>Entfernungsrichtlinie

Wählen Sie eine [Entfernungsrichtlinie](https://redis.io/topics/lru-cache), die für Ihre Anwendung geeignet ist. Die Standardrichtlinie für Azure Cache for Redis ist `volatile-lru`. Das bedeutet, dass nur Schlüssel, für die eine Gültigkeitsdauer (TTL) festgelegt ist, entfernt werden können.  Wenn keine Schlüssel über einen TTL-Wert verfügen, werden im System keine Schlüssel entfernt.  Wenn das System bei hoher Speicherauslastung das Entfernen beliebiger Schlüssel erlauben soll, sollten Sie die `allkeys-lru`-Richtlinie in Betracht ziehen.

## <a name="keys-expiration"></a>Ablauf von Schlüsseln

Legen Sie einen Ablaufwert für Ihre Schlüssel fest. Bei einem Ablauf werden Schlüssel proaktiv entfernt, anstatt zu warten, bis die Speicherauslastung hoch ist.  Wird das Entfernen aufgrund hoher Speicherauslastung ausgelöst, kann dies mehr Last auf dem Server verursachen. Weitere Informationen finden Sie in der Dokumentation für die Befehle [EXPIRE](https://redis.io/commands/expire) und [EXPIREAT](https://redis.io/commands/expireat).

## <a name="minimize-memory-fragmentation"></a>Minimieren der Speicherfragmentierung

Große Werte können dazu führen, dass der Speicher beim Auslagern fragmentiert wird, was zu einer hohen Speichernutzung und Serverauslastung führen kann.

## <a name="monitor-memory-usage"></a>Überwachen der Arbeitsspeicherauslastung

Fügen Sie eine Überwachung der Speichernutzung hinzu, um sicherzustellen, dass Ihnen nicht der Speicher ausgeht und Sie die Möglichkeit haben, Ihren Cache zu skalieren, bevor Probleme auftreten.

## <a name="configure-your-maxmemory-reserved-setting"></a>Konfigurieren der Einstellung „maxmemory-reserved“

Konfigurieren Sie Ihre [maxmemory-reserved-Einstellung](cache-configure.md#maxmemory-policy-and-maxmemory-reserved), um die Reaktionsfähigkeit des Systems zu verbessern:

* Eine ausreichende Reservierungseinstellung ist besonders wichtig für schreibintensive Workloads oder wenn Sie Werte von 100 KB oder mehr in Ihrem Cache speichern. Beginnen Sie mit 10 % der Größe Ihres Caches und erhöhen Sie den Prozentsatz bei schreibintensiven Lasten.

* Mit der Einstellung  `maxmemory-reserved`  wird die Arbeitsspeichermenge in MB pro Instanz in einem Cluster konfiguriert, die für andere Prozesse als Cacheprozesse reserviert ist, z. B. die Replikation während eines Failovers. Mit dem Festlegen dieses Werts können Sie dafür sorgen, dass Sie bei wechselnden Auslastungen eine konsistentere Redis-Servererfahrung erzielen. Für Workloads, die große Datenmengen schreiben, sollte ein höherer Wert festgelegt werden. Wenn Arbeitsspeicher für Vorgänge dieser Art reserviert ist, ist er nicht für die Zwischenspeicherung von Daten verfügbar.

* Mit der Einstellung  `maxfragmentationmemory-reserved`  wird der Arbeitsspeicher in MB pro Instanz in einem Cluster konfiguriert, der für die Speicherfragmentierung reserviert ist. Wenn Sie diesen Wert einstellen, ist die Erfahrung des Redis-Servers konsistenter, wenn der Cache voll oder fast voll ist und die Fragmentierungsrate hoch ist. Wenn Arbeitsspeicher für Vorgänge dieser Art reserviert ist, ist er nicht für die Zwischenspeicherung von Daten verfügbar.

* Bei der Auswahl eines neuen Speicherreservierungswerts (`maxmemory-reserved`  oder  `maxfragmentationmemory-reserved`) sollte berücksichtigt werden, wie sich diese Änderung auf einen Cache auswirkt, der bereits mit einer großen Datenmenge ausgeführt wird. Beispiel: Wenn Ihr Cache über eine Kapazität von 53 GB verfügt und 49 GB an Daten enthält, ändern Sie den Reservierungswert in 8 GB, um den maximal verfügbaren Arbeitsspeicher für das System auf 45 GB zu verringern. Wenn entweder der aktuelle Wert für  `used_memory`  oder der Wert für  `used_memory_rss`  höher als der neue Grenzwert von 45 GB ist, muss das System Daten entfernen, bis sowohl  `used_memory`  als auch  `used_memory_rss`  unter 45 GB liegen. Durch die Entfernung können sich Serverauslastung und Arbeitsspeicherfragmentierung erhöhen. Weitere Informationen zu Cachemetriken, etwa  `used_memory`  und  `used_memory_rss`, finden Sie unter  [Verfügbare Metriken und Berichtsintervalle](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).
