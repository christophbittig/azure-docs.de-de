---
title: Hochverfügbarkeit für Azure Cache for Redis
description: Informationen zu Hochverfügbarkeitsfunktionen und -optionen bei Azure Cache for Redis
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: cauribeg
ms.openlocfilehash: 2fb0440ed6946f9595f1c78c5528fdb3c627de0b
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538535"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Hochverfügbarkeit für Azure Cache for Redis

Azure Cache for Redis verfügt über integrierte Hochverfügbarkeit.  Eine Hochverfügbarkeitsarchitektur wird verwendet, um sicherzustellen, dass Ihre verwaltete Redis-Instanz auch dann funktioniert, wenn sich geplante als auch ungeplante Ausfälle auf die zugrunde liegenden virtuellen Computer (VMs) auswirken. Sie bietet deutlich höhere Prozentsätze als dies beim Hosten von Redis auf einem einzelnen virtuellen Computer möglich ist.

Azure Cache for Redis implementiert Hochverfügbarkeit durch Verwendung mehrerer virtueller Computer (als *Knoten* bezeichnet) für einen Cache. Die Knoten sind so konfiguriert, dass die Datenreplikation und das Failover in koordinierter Weise erfolgen. Hochverfügbarkeit unterstützt außerdem Wartungsvorgänge wie Redis-Softwarepatches. In den Tarifen „Standard“, „Premium“ und „Enterprise“ sind verschiedene Hochverfügbarkeitsoptionen verfügbar:

| Option | BESCHREIBUNG | Verfügbarkeit | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standardreplikation](#standard-replication)| Replizierte Konfiguration mit zwei Knoten in einem Rechenzentrum mit automatischem Failover | 99,9 % (siehe [Details](https://azure.microsoft.com/support/legal/sla/cache/v1_1/)) |✔|✔|-|
| [Zonenredundanz](#zone-redundancy) | Replizierte Konfiguration mit mehreren Knoten in allen Verfügbarkeitszonen mit automatischem Failover | Bis zu 99,99 % (siehe [Details](https://azure.microsoft.com/support/legal/sla/cache/v1_1/)) |-|✔|✔|
| [Georeplikation](#geo-replication) | Verknüpfte Cacheinstanzen in zwei Regionen mit benutzergesteuertem Failover | Bis zu 99,999 % (siehe [Details](https://azure.microsoft.com/support/legal/sla/cache/v1_1/)) |-|✔|Vorschau|

## <a name="standard-replication"></a>Standardreplikation

Eine Azure Cache for Redis-Instanz im Tarif „Standard“ oder „Premium“ wird standardmäßig auf zwei Redis-Servern ausgeführt. Die beiden Server werden auf dedizierten virtuellen Computern gehostet. Bei Open-Source-Redis können Anforderungen zum Schreiben von Daten nur auf einem Server verarbeitet werden. Bei diesem Server handelt es sich um den *primären* Knoten, der andere Server ist der *Replikatknoten*. Nach dem Bereitstellen der Serverknoten weist Azure Cache for Redis diesen die primäre und die Replikatrolle zu. Der primäre Knoten dient normalerweise der Verarbeitung von Schreib- und Leseanforderungen von Redis-Clients. Bei einem Schreibvorgang wird ein neuer Schlüssel und eine Schlüsselaktualisierung in den internen Speicher des Knotens übertragen und sofort eine Antwort an den Client gesendet. Der Vorgang wird asynchron an das Replikat weitergeleitet.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Einrichten der Datenreplikation":::

>[!NOTE]
>Normalerweise kommuniziert ein Redis-Client bei allen Lese- und Schreibanforderungen mit dem primären Knoten in einem Redis-Cache. Bestimmte Redis-Clients können so konfiguriert werden, dass sie vom Replikatknoten lesen.
>
>

Wenn der primäre Knoten in einem Redis-Cache nicht verfügbar ist, wird der Replikatknoten automatisch zum primären Knoten höher gestuft. Dieser Prozess wird als *Failover* bezeichnet. Der Replikatknoten wartet vor dem Höherstufen ausreichend lange, für den Fall, dass der primäre Knoten schnell wiederhergestellt wird. Bei einem Failover stellt Azure Cache for Redis einen neuen virtuellen Computer bereit und verbindet ihn als Replikatknoten mit dem Cache. Der Replikatknoten führt eine vollständige Datensynchronisierung mit dem primären Knoten durch, sodass eine weitere Kopie der Cachedaten vorhanden ist.

Ein primärer Knoten kann als Teil einer geplanten Wartungsaktivität (z. B. Redis-Softwareupdate oder Betriebssystemupdate) außer Betrieb genommen werden. Er kann auch aufgrund ungeplanter Ereignisse wie z. B. Fehlern in der zugrunde liegenden Hardware, Software oder im Netzwerk ausfallen. Unter [Failover und Patching für Azure Cache for Redis](cache-failover.md) finden Sie eine ausführliche Erläuterung zu den verschiedenen Redis-Failovertypen. Eine Azure Cache for Redis-Instanz durchläuft während ihrer Lebensdauer viele Failover. Das Design der Hochverfügbarkeitsarchitektur macht diese Änderungen innerhalb eines Caches für seine Kunden so transparent wie möglich.

Azure Cache for Redis bietet außerdem im Tarif „Premium“ zusätzliche Replikatknoten. Ein [Cache mit mehreren Replikaten](cache-how-to-multi-replicas.md) kann mit bis zu drei Replikatknoten konfiguriert werden. Durch mehrere Replikate verbessert sich im Allgemeinen die Resilienz, da der primäre Knoten durch Knoten abgesichert wird. Auch bei mehreren Replikaten kann eine Azure Cache for Redis-Instanz dennoch durch einen Ausfall eines Rechenzentrums oder einen Ausfall auf Verfügbarkeitszonenebene beeinträchtigt werden. Sie können die Cacheverfügbarkeit erhöhen, indem Sie mehrere Replikate mit [Zonenredundanz](#zone-redundancy) verwenden.

## <a name="zone-redundancy"></a>Zonenredundanz

Zonenredundante Konfigurationen werden von Azure Cache for Redis in den Tarifen „Premium“ und „Enterprise“ unterstützt. Bei einem [zonenredundanten Cache](cache-how-to-zone-redundancy.md) können die zugehörigen Knoten in unterschiedlichen [Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md) derselben Region platziert werden. Ausfälle im Rechenzentrum oder in einer Verfügbarkeitszone werden als Single Point of Failure behoben. Damit wird die Gesamtverfügbarkeit des Caches erhöht.

### <a name="premium-tier"></a>Premium-Tarif

Das folgende Diagramm veranschaulicht die zonenredundante Konfiguration für den Premium-Tarif:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Einrichtung der Zonenredundanz":::

Azure Cache for Redis verteilt Knoten in einem zonenredundanten Cache im Roundrobin-Verfahren auf die ausgewählten Verfügbarkeitszonen. Außerdem wird festgelegt, welcher Knoten anfänglich als primärer Knoten verwendet wird.

Ein zonenredundanter Cache ermöglicht ein automatisches Failover. Wenn der aktuelle primäre Knoten nicht verfügbar ist, wird er durch einen der Replikatknoten ersetzt. Wenn sich der neue primäre Knoten in einer anderen Verfügbarkeitszone befindet, bedeutet dies für die Anwendung möglicherweise eine längere Cacheantwortzeit. Verfügbarkeitszonen sind geografisch getrennt. Durch den Wechsel zwischen Verfügbarkeitszonen ändert sich die physische Entfernung zwischen den Orten, an denen die Anwendung und der Cache gehostet werden. Diese Änderung wirkt sich auf Roundtrip-Netzwerklatenzen zwischen Anwendung und Cache aus. Bei den meisten Anwendungen wird davon ausgegangen, dass die zusätzliche Latenz innerhalb eines akzeptablen Bereichs liegt. Es wird empfohlen, Ihre Anwendung zu testen, um sicherzustellen, dass sie mit einem zonenredundanten Cache ordnungsgemäß ausgeführt wird.

### <a name="enterprise-tiers"></a>Enterprise-Tarife

Ein Cache in einem der Enterprise-Tarife basiert auf einem Redis Enterprise-Cluster. Zur Bildung eines Quorums muss immer eine ungerade Anzahl von Serverknoten vorhanden sein. Standardmäßig umfasst es drei Knoten, die jeweils auf einem dedizierten virtuellen Computer gehostet werden. Ein Enterprise-Cache verfügt über zwei gleich große *Datenknoten* und über einen kleineren *Quorumknoten*. Ein Enterprise Flash-Cache verfügt über drei gleich große Datenknoten. Redis-Daten werden vom Enterprise-Cluster intern in Partitionen unterteilt. Jede Partition verfügt über ein *primäres Element* und über mindestens ein *Replikat*. Jeder Datenknoten enthält mindestens eine Partition. Der Enterprise-Cluster stellt sicher, dass sich das primäre Element und die Replikate einer Partition nie auf dem gleichen Datenknoten befinden. Partitionsdaten von primären Elementen werden asynchron in zugehörigen Replikaten repliziert.

Wenn ein Datenknoten ausfällt oder ein Netzwerk aufgeteilt wird, kommt es zu einem Failover. Dieses ähnelt dem unter [Standardreplikation](#standard-replication) beschriebenen Failover. Der Enterprise-Cluster verwendet ein quorumbasiertes Modell, um zu bestimmen, welche verbleibenden Knoten ein neues Quorum bilden. Außerdem werden Replikatpartitionen innerhalb dieser Knoten nach Bedarf zu primären Partitionen heraufgestuft.

## <a name="geo-replication"></a>Georeplikation

[Georeplikation](cache-how-to-geo-replication.md) ist ein Mechanismus zum Verknüpfen von zwei oder mehr Azure Cache for Redis-Instanzen, die in der Regel zwei Azure-Regionen umfassen.

### <a name="premium-tier"></a>Premium-Tarif

>[!NOTE]
>Georeplikation im Premium-Tarif ist hauptsächlich für die Notfallwiederherstellung vorgesehen.
>
>

Zwei Cache-Instanzen im Premium-Tarif können über [Georeplikation](cache-how-to-geo-replication.md) miteinander verbunden werden, sodass Sie Ihre Cachedaten in einer anderen Region sichern können. Sobald sie miteinander verknüpft sind, wird eine Instanz als „primärer verknüpfter Cache“ benannt und die andere als „sekundärer verknüpfter Cache“. Nur der primäre Cache akzeptiert Lese- und Schreibanforderungen. In den primären Cache geschriebene Daten werden in den sekundären Cache repliziert.

Eine Anwendung greift über separate Endpunkte für die primären und sekundären Caches auf den jeweiligen Cache zu. Die Anwendung muss alle Schreibanforderungen an den primären Cache senden, wenn sie in mehreren Azure-Regionen bereitgestellt ist. Lesen kann sie sowohl aus dem primären als auch aus dem sekundären Cache. Im Allgemeinen möchten Sie, dass die Compute-Instanzen Ihrer Anwendung aus den nächstgelegenen Caches lesen, um Wartezeiten zu verringern. Die Datenübertragung zwischen den beiden Cache-Instanzen ist mittels TLS gesichert.

Die Georeplikation bietet kein automatisches Failover. Der Grund sind Bedenken hinsichtlich zusätzlicher Netzwerkroundtripzeiten zwischen Regionen, wenn der Rest Ihrer Anwendung in der primären Region verbleibt. Zum Verwalten und Starten des Failovers muss die Verknüpfung mit dem sekundären Cache aufgehoben werden. Durch das Aufheben der Verknüpfung wird er zur neuen primären Instanz höhergestuft.

### <a name="enterprise-tiers"></a>Enterprise-Tarife

>[!NOTE]
>Dies ist als Vorschauversion verfügbar.
>
>

Die Enterprise-Tarife unterstützen eine komplexere Form der Georeplikation. Wir nennen sie [aktive Georeplikation](cache-how-to-active-geo-replication.md). Durch die Verwendung konfliktfreier replizierter Datentypen unterstützt die Redis Enterprise-Software Schreibvorgänge in mehrere Cache-Instanzen und übernimmt die Zusammenführung von Änderungen und die Auflösung von Konflikten. Sie können zwei oder mehr Cache-Instanzen im Enterprise-Tarif in unterschiedlichen Azure-Regionen verknüpfen, um einen aktiven georeplizierten Cache zu bilden. 

Eine Anwendung, die einen solchen Cache verwendet, kann über entsprechende Endpunkte aus den geografisch verteilten Cache-Instanzen lesen bzw. darin schreiben. Der Cache sollte dabei immer den zur jeweiligen Compute-Instanz nächstgelegenen Cache verwenden, um Ihnen die niedrigsten Wartezeiten zu liefern. Die Anwendung muss außerdem die Cache-Instanzen überwachen und zu einer anderen Region wechseln, wenn eine der Instanzen nicht mehr verfügbar ist. Weitere Informationen zur Funktionsweise der aktiven Georeplikation finden Sie unter [Aktive/Aktive geografische Verteilung (CRDTs-basiert)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konfigurieren von Hochverfügbarkeitsoptionen von Azure Cache for Redis:

* [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)
* [Hinzufügen von Replikaten zu Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [Aktivieren der Zonenredundanz für Azure Cache for Redis](cache-how-to-zone-redundancy.md)
* [Einrichten der Georeplikation für Azure Cache for Redis](cache-how-to-geo-replication.md)
