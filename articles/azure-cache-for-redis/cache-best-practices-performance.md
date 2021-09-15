---
title: Bewährte Methoden für Leistungstests
titleSuffix: Azure Cache for Redis
description: Informationen zum Testen der Leistung von Azure Cache for Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 0e2ba661d2eaa6dd3899bdd0cb5e70e8e083526a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227114"
---
# <a name="performance-testing"></a>Leistungstests

1. Verwenden Sie zunächst `redis-benchmark.exe`, um den allgemeinen Durchsatz und die Latenzmerkmale Ihres Cache zu überprüfen, bevor Sie Ihre eigenen Leistungstests schreiben. Weitere Informationen finden Sie unter [Redis-Benchmark](#redis-benchmark-utility).

1. Die für den Test verwendete Client-VM sollte sich *in derselben Region* befinden wie Ihre Redis Cache-Instanz.

1. Stellen Sie sicher, dass die verwendete Client-VM über *mindestens so viel Computeleistung und Bandbreite* wie der getestete Cache verfügt.

1. Keinesfalls sollten Sie die Leistung Ihres Cache nur unter stabilen Bedingungen testen. *Testen Sie außerdem unter Failoverbedingungen*, und messen Sie währenddessen die CPU-/Serverauslastung Ihres Cache. Sie können ein Failover auslösen, indem Sie [den primären Knoten neu starten](cache-administration.md#reboot). Durch Tests unter Failoverbedingungen können Sie den Durchsatz und die Latenz Ihrer Anwendung unter Failoverbedingungen ermitteln. Ein Failover kann im Rahmen eines Updates oder eines ungeplanten Ereignisses erfolgen. Idealerweise sollte die CPU-/Serverauslastungsspitze selbst während eines Failovers nicht mehr als 80 % betragen, da dies Auswirkungen auf die Leistung haben kann.

1. Erwägen Sie die Verwendung von Azure Cache for Redis-Instanzen des Premium-Tarifs. Diese Cachegrößen verfügen über eine bessere Netzwerklatenz und einen höheren Durchsatz, weil sie im Hinblick auf CPU und Netzwerk auf besserer Hardware ausgeführt werden.

   > [!NOTE]
   > Als Referenz haben wir unsere beobachteten Leistungsergebnisse [hier veröffentlicht](./cache-planning-faq.yml#azure-cache-for-redis-performance). Bedenken Sie außerdem, dass SSL/TLS einen gewissen Overhead hinzufügt, wodurch Sie bei Verwendung von Transportverschlüsselung abweichende Latenzen und/oder einen anderen Durchsatz erhalten.

## <a name="redis-benchmark-utility"></a>Hilfsprogramm „Redis-Benchmark“

Dokumentation zu **Redis-Benchmark** finden Sie [hier](https://redis.io/topics/benchmarks).

TLS wird von `redis-benchmark.exe` nicht unterstützt. Sie müssen [den Nicht-TLS-Port über das Portal aktivieren](cache-configure.md#access-ports), bevor Sie den Test ausführen. Eine mit Windows kompatible Version von „redis-benchmark.exe“ finden Sie [hier](https://github.com/MSOpenTech/redis/releases).

## <a name="redis-benchmark-examples"></a>Beispiele für Redis-Benchmark

**Testvorbereitung**: Bereiten Sie die Cache-Instanz mit Daten vor, die zum Testen von Latenz und Durchsatz erforderlich sind:

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024
```

**So testen Sie die Latenz**: Testen von GET-Anforderungen anhand von 1 KB Nutzdaten:

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4
```

**So testen Sie den Durchsatz**: GET-Anforderungen in Pipeline mit 1 KB Nutzdaten:

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
```
