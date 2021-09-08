---
title: Bewährte Methoden für die Entwicklung
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie Code für Azure Cache for Redis entwickeln.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 1a91b5e11707d8f17effa6b2d711aaaa1a97afed
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115426"
---
# <a name="development"></a>Entwicklung

## <a name="connection-resilience-and-server-load"></a>Verbindungsresilienz und Serverauslastung

Berücksichtigen Sie beim Entwickeln von Clientanwendungen die relevanten bewährten Methoden für die [Verbindungsresilienz](cache-best-practices-connection.md) und die [Verwaltung der Serverlast](cache-best-practices-server-load.md).

## <a name="consider-more-keys-and-smaller-values"></a>Berücksichtigen Sie mehr Schlüssel und kleinere Werte

Redis funktioniert am besten mit kleineren Werten. Unterteilen Sie größere Datenblöcke in kleinere Blöcke, um die Daten auf mehrere Schlüssel zu verteilen. Weitere Informationen zur idealen Wertgröße finden Sie in diesem [Artikel](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/).

In dieser Diskussion zu Redis sind einige Überlegungen aufgelistet, die Sie unbedingt berücksichtigen sollten. Ein Beispielproblem, das durch große Werte verursacht werden kann, finden Sie unter [Große Anforderungs- oder Antwortgröße](cache-troubleshoot-client.md#large-request-or-response-size).

## <a name="key-distribution"></a>Schlüsselverteilung

Wenn Sie das Redis-Clustering verwenden möchten, lesen Sie zuerst [Bewährte Methoden für Redis-Clustering mit Schlüsseln](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/).

## <a name="use-pipelining"></a>Verwenden Sie die Pipeline-Funktionen

Versuchen Sie, einen Redis-Client auszuwählen, der [Redis-Pipelining](https://redis.io/topics/pipelining) unterstützt. Pipelining hilft Ihnen dabei, das Netzwerk effizient zu nutzen und den bestmöglichen Durchsatz zu erzielen.

## <a name="avoid-expensive-operations"></a>Vermeiden aufwändiger Vorgänge

Einige der Redis-Vorgänge, wie z.B. der Befehl [KEYS](https://redis.io/commands/keys), sind speicherintensiv und sollte vermieden werden. Einige Überlegungen zu Befehlen mit zeitintensiver Ausführungsdauer finden Sie unter [Befehle mit zeitintensiver Ausführungsdauer](cache-troubleshoot-server.md#long-running-commands)

## <a name="choose-an-appropriate-tier"></a>Wählen Sie eine passende Ebene aus
Verwenden Sie den Standard- oder Premium-Tarif für Produktionssysteme.  Verwenden Sie die Basic-Ebene nicht in der Produktion. Der Basic-Tarif ist ein System mit einem einzelnen Knoten, ohne Datenreplikation und ohne SLA. Verwenden Sie mindestens einen C1-Cache. C0-Caches sind nur für einfache Entwicklungs-/Testszenarien vorgesehen, weil:

- sie sich einen CPU-Kern teilen
- sie wenig Arbeitsspeicher verwenden
- sie anfällig für Probleme vom Typ *Noisy Neighbor* (laute Nachbarn) sind

Wir empfehlen Leistungstests durchzuführen, um die richtige Ebene zu wählen und die Verbindungseinstellungen zu überprüfen. Weitere Informationen finden Sie unter [Leistungstests](cache-best-practices-performance.md).

## <a name="client-in-same-region-as-cache"></a>Der Client ist in derselben Region wie der Cache

Platzieren Sie Ihre Cache-Instanz und Ihre Anwendung in der gleichen Region. Eine Verbindung mit einem Cache in einer anderen Region herzustellen, kann zu einer beträchtlichen Latenz führen und die Zuverlässigkeit reduzieren.  

Sie können zwar von außerhalb von Azure eine Verbindung herstellen, das ist jedoch nicht empfehlenswert, *vor allem bei Verwendung von Redis als Cache*.  Wenn Sie Redis Server nur als einen Schlüssel/Wert-Speicher verwenden, ist Latenz möglicherweise kein Hauptaspekt.

## <a name="use-tls-encryption"></a>Verwenden der TLS-Verschlüsselung

Azure Cache for Redis erfordert standardmäßig mit TLS verschlüsselte Kommunikationen. Derzeit werden die TLS-Versionen 1.0, 1.1 und 1.2 unterstützt. Die Unterstützung von TLS 1.0 und 1.1 wird jedoch branchenweit eingestellt werden. Verwenden Sie daher nach Möglichkeit TLS 1.2.

Wenn TLS von Ihrer Clientbibliothek oder Ihrem Tool nicht unterstützt wird, kann die Aktivierung unverschlüsselter Verbindungen über das [Azure-Portal](cache-configure.md#access-ports) oder über [Verwaltungs-APIs](/rest/api/redis/redis/update) vorgenommen werden. Wenn verschlüsselte Verbindungen nicht möglich sind, empfiehlt es sich, den Cache und die Clientanwendung in ein virtuelles Netzwerk einzubinden. Weitere Informationen zu den im virtuellen Netzwerkcache-Szenario verwendeten Ports finden Sie in dieser [Tabelle](cache-how-to-premium-vnet.md#outbound-port-requirements).

## <a name="client-library-specific-guidance"></a>Spezifische Anleitungen für die Clientbibliothek

* [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
* [Java – Welchen Client soll ich verwenden?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
* [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
* [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
* [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
* [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
* [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
* [ASP.NET-Sitzungszustandsanbieter](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)
