---
title: Häufig gestellte Fragen zum in der Azure Cosmos DB integrierten Cache
description: Häufig gestellte Fragen über in der Azure Cosmos DB integrierten Cache.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: a8d1dc69ef64726f55dffd20221a19c3f3114964
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595140"
---
# <a name="azure-cosmos-db-integrated-cache-frequently-asked-questions"></a>Häufig gestellte Fragen zum in der Azure Cosmos DB integrierten Cache
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der in Azure Cosmos DB integrierte Cache ist ein In-Memory-Cache, der in Azure Cosmos DB eingebunden ist. Dieser Artikel beantwortet häufig gestellte Fragen über den integrierten Cache von Azure Cosmos DB.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="why-does-the-integrated-cache-require-a-dedicated-gateway"></a>Warum ist für den integrierten Cache ein dediziertes Gateway erforderlich?

Wenn Sie eine Verbindung zu Azure Cosmos DB im Gateway-Modus hergestellt haben, haben Sie das Standard-Gateway verwendet. Während das Azure Cosmos DB-Back-End (Ihr bereitgestellter Durchsatz und Speicher) über dedizierte Kapazität pro Container verfügt, wird das Standard-Gateway von vielen Kunden gemeinsam genutzt. Es ist für viele Kunden praktisch, ein Standard-Gateway gemeinsam zu nutzen, da die von den einzelnen Kunden verbrauchten Computerressourcen minimal sind. Da der integrierte Cache spezifisch für Ihr Azure Cosmos DB-Konto ist und erhebliche CPU- und Speicheranforderungen stellt, ist ein dedizierter Gateway-Knoten erforderlich.

### <a name="what-is-a-dedicated-gateway"></a>Was ist ein dediziertes Gateway?

Ein [dediziertes Gateway](dedicated-gateway.md) ist eine serverseitige Rechenleistung, die ein Front-End für Daten in einem Azure Cosmos DB-Konto ist. Wenn Sie eine Verbindung mit dem Endpunkt Ihres dedizierten Gateways herstellen, sendet Ihre Anwendung eine Anforderung an das dedizierte Gateway, das die Anforderung dann an verschiedene Back-End-Partitionen weiterleitet.

### <a name="does-using-the-dedicated-gateway-offer-any-other-performance-benefits-over-using-the-standard-gateway"></a>Bietet die Verwendung des dedizierten Gateways gegenüber der Verwendung des Standard-Gateways andere Leistungsvorteile?

Im Allgemeinen haben Anforderungen, die vom dedizierten Gateway geroutet werden, eine etwas geringere und konsistentere Latenz als Anforderungen, die vom Standard-Gateway geroutet werden. Selbst Anforderungen, die den integrierten Cache nicht verwenden, haben immer noch eine etwas geringere Latenz als das Standard-Gateway.

### <a name="what-kind-of-latency-should-i-expect-from-the-integrated-cache"></a>Welche Art von Latenz sollte ich vom integrierten Cache erwarten?

Eine vom integrierten Cache verarbeitete Anforderung ist schneller, da die zwischengespeicherten Daten im In-Memory auf dem dedizierten Gateway und nicht auf dem Back-End gespeichert werden. Für zwischengespeicherte Punktlesevorgänge sollten Sie eine Latenz von 2–4 ms erwarten.

Bei zwischengespeicherten Abfragen hängt die Latenz von der Abfrage ab. Der Abfrage-Cache funktioniert, indem er die Antwort der Abfrage-Engine für eine bestimmte Abfrage zwischenspeichert. Diese Antwort wird dann zur Verarbeitung an das SDK zurückgesendet. Für einfache Abfragen ist minimale Arbeit im SDK erforderlich, und Latenzen von 2–4 ms sind typisch. Komplexere Abfragen mit `GROUP BY` oder `DISTINCT` erfordern jedoch eine höhere Verarbeitung im SDK, sodass die Latenz auch mit dem Abfragecache höher sein kann.

Wenn Sie zuvor eine Verbindung mit Azure Cosmos DB im direkten Modus hergestellt und zur Verbindung mit dem dedizierten Gateway gewechselt haben, kann es bei einigen Anforderungen zu einem geringfügigen Latenzanstieg kommen. Für die Verwendung des Gateway-Modus muss eine Anforderung an das Gateway (in diesem Fall das dedizierte Gateway) gesendet und dann entsprechend an das Back-End geroutet werden. Der direkte Modus ermöglicht dem Client, wie der Name schon sagt, eine direkte Kommunikation mit dem Back-End, wodurch ein zusätzlicher Hop entfernt wird. 

Wenn Ihre App zuvor den direkten Modus verwendet hat, sind die Latenzvorteile des integrierten Caches nur in den folgenden Szenarien von Bedeutung:

- Punktlesevorgangslatenz für große Elemente (> 16 KB)
- Hohe Anforderungseinheit oder komplexe Abfragen

Wenn Ihre App zuvor den Gateway-Modus mit dem Standard-Gateway verwendet hat, bietet der integrierte Cache in fast allen Szenarien eine Verringerung der Latenz. 

### <a name="does-the-azure-cosmos-db-availability-sla-extend-to-the-dedicated-gateway-and-integrated-cache"></a>Erstreckt sich die Azure Cosmos DB SLA-Verfügbarkeit auf das dedizierte Gateway und den integrierten Cache?

Sobald die Funktion allgemein verfügbar ist, wird die SLA/SLO-Verfügbarkeit für das dedizierte Gateway (und somit den integrierten Cache) bestehen. Für Szenarien, die Hochverfügbarkeit erfordern, sollten Sie die 3-fache Anzahl der benötigten dedizierten Gateway-Instanzen bereitstellen. Wenn beispielsweise ein dedizierter Gateway-Knoten in der Produktion benötigt wird, sollten Sie zwei zusätzliche dedizierte Gateway-Knoten bereitstellen, um mögliche Ausfallzeiten oder Ausfälle zu berücksichtigen.

### <a name="the-integrated-cache-is-only-available-for-sql-core-api-right-now-are-you-planning-on-releasing-it-for-other-apis-as-well"></a>Der integrierte Cache ist derzeit nur für die SQL (Core) API verfügbar. Planen Sie die Freigabe auch für andere APIs?

Die Erweiterung des integrierten Caches über die SQL-API hinaus ist auf der Planungsliste vorgesehen, aber über die erste öffentliche Vorschau des integrierten Caches hinaus.

### <a name="what-consistency-does-the-integrated-cache-support"></a>Welche Konsistenz unterstützt der integrierte Cache?

Der integrierte Cache unterstützt sowohl Sitzungskonsistenz als auch letztliche Konsistenz. Sie können auch die optionale [MaxIntegratedCacheStaleness](integrated-cache.md#maxintegratedcachestaleness) konfigurieren, die eine Obergrenze für zwischengespeicherte Daten festlegt.

## <a name="next-steps"></a>Nächste Schritte

- [Integrierter Cache](integrated-cache.md)
- [Konfigurieren des integrierten Caches](how-to-configure-integrated-cache.md)
- [Dediziertes Gateway](dedicated-gateway.md)
- Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    - Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
    - Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)