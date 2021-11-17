---
title: Dediziertes Azure Cosmos DB-Gateway
description: Ein dediziertes Gateway ist Compute als Front-End für Ihr Azure Cosmos DB-Konto. Wenn Sie eine Verbindung mit dem dedizierten Gateway herstellen, leitet es Anforderungen weiter und speichert Daten zwischen.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: tisande
ms.openlocfilehash: 2c2681f1d6e9abf66375d8a6a9d0f43683474dcc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060413"
---
# <a name="azure-cosmos-db-dedicated-gateway---overview-preview"></a>Dediziertes Azure Cosmos DB-Gateway – Übersicht (Vorschau)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ein dediziertes Gateway ist serverseitiges Compute als Front-End für Ihr Azure Cosmos DB-Konto. Wenn Sie eine Verbindung mit dem dedizierten Gateway herstellen, leitet es Anforderungen weiter und speichert Daten zwischen. Wie der bereitgestellte Durchsatz wird das dedizierte Gateway stündlich abgerechnet.

## <a name="overview"></a>Übersicht

Sie können ein dediziertes Gateway bereitstellen, um die Leistung im großen Stil zu verbessern. Der häufigste Grund für die Bereitstellung eines dedizierten Gateways ist Zwischenspeicherung. Wenn Sie ein dediziertes Gateway bereitstellen, wird automatisch ein [integrierter Cache](integrated-cache.md) innerhalb des dedizierten Gateways konfiguriert. Punktlesevorgänge und Abfragen, die den integrierten Cache betreffen, nutzen keine Ihrer RUs. Durch die Bereitstellung eines dedizierten Gateways mit einem integrierten Cache können Workloads mit hoher Leselast die Kosten für Azure Cosmos DB senken.

Das dedizierte Gateway ist in Azure Cosmos DB integriert. Wenn Sie [ein dediziertes Gateway bereitstellen](how-to-configure-integrated-cache.md), verfügen Sie über einen vollständig verwalteten Knoten, der Anforderungen an Back-End-Partitionen weiterleitet. Das Herstellen einer Verbindung mit Azure Cosmos DB mit dem dedizierten Gateway bietet eine geringere und besser vorhersagbare Latenz als das Herstellen einer Verbindung mit Azure Cosmos DB mit dem Standardgateway. Selbst Cachefehler werden beim Vergleich des dedizierten Gateways mit dem Standardgateway zu Latenzverbesserungen führen.

Es sind nur minimale Codeänderungen erforderlich, damit Ihre Anwendung ein dediziertes Gateway verwenden kann. Sowohl neue als auch vorhandene Azure Cosmos DB-Konten können ein dediziertes Gateway bereitstellen, um die Leseleistung zu verbessern.

> [!NOTE]
> Haben Sie Feedback zum dedizierten Gateway? Teilen Sie uns Ihre Meinung mit! Sie können Feedback direkt an das Azure Cosmos DB-Entwicklungsteam senden: cosmoscachefeedback@microsoft.com

## <a name="connection-modes"></a>Verbindungsmodi

Es gibt drei Möglichkeiten, eine Verbindung mit einem Azure Cosmos DB-Konto herzustellen:

- [Direkter Modus](#connect-to-azure-cosmos-db-using-direct-mode)
- [Gatewaymodus mit dem Standardgateway](#connect-to-azure-cosmos-db-using-gateway-mode)
- [Gatewaymodus mit dem dedizierten Gateway](#connect-to-azure-cosmos-db-using-the-dedicated-gateway) (nur für SQL-API-Konten verfügbar)

### <a name="connect-to-azure-cosmos-db-using-direct-mode"></a>Herstellen der Verbindung mit Azure Cosmos DB mit dem direkten Modus

Wenn Sie eine Verbindung mit Azure Cosmos DB im direkten Modus herstellen, stellt Ihre Anwendung eine direkte Verbindung mit dem Azure Cosmos DB-Back-End her. Auch wenn Sie über viele physische Partitionen verfügen, wird das Anforderungsrouting vollständig clientseitig verarbeitet. Der direkte Modus bietet eine geringe Latenz, da Ihre Anwendung direkt mit dem Azure Cosmos DB-Back-End kommunizieren kann und keinen zwischengeschalteten Netzwerkhop benötigt.

Grafische Darstellung der Verbindung im direkten Modus:

:::image type="content" source="./media/dedicated-gateway/direct-mode.png" alt-text="Abbildung der Funktionsweise des direkten Modus von Cosmos DB" border="false":::

### <a name="connect-to-azure-cosmos-db-using-gateway-mode"></a>Herstellen der Verbindung mit Azure Cosmos DB mit dem Gatewaymodus

Wenn Sie eine Verbindung Azure Cosmos DB im Gatewaymodus herstellen, stellt Ihre Anwendung zuerst eine Verbindung mit einem Front-End-Knoten her, der das Weiterleiten der Anforderung an die entsprechenden Back-End-Knoten verarbeitet. Da der Gatewaymodus einen zusätzlichen Netzwerkhop umfasst, können Sie im Vergleich zum direkten Modus eine etwas höhere Latenz beobachten. 

Wenn Sie eine Verbindung mit Azure Cosmos DB im Gatewaymodus herstellen, können Sie eine der folgenden Optionen verwenden:

* **Standardgateway**: Während das Back-End, das Ihren bereitgestellten Durchsatz und Speicher umfasst, über dedizierte Kapazität pro Container verfügt, wird das Standardgateway von vielen Azure Cosmos-Konten gemeinsam genutzt. Es ist für viele Kunden praktisch, ein Standardgateway gemeinsam zu nutzen, da die von den einzelnen Kunden verbrauchten Computeressourcen klein sind.
* **Dediziertes Gateway**: In diesem Gateway verfügen sowohl das Back-End als auch das Gateway über dedizierte Kapazität. Der integrierte Cache erfordert ein dediziertes Gateway, da er einen erheblichen CPU- und Speicherbedarf aufweist, der spezifisch für Ihr Azure Cosmos-Konto ist.

### <a name="connect-to-azure-cosmos-db-using-the-dedicated-gateway"></a>Herstellen der Verbindung mit Azure Cosmos DB mit dem dedizierten Gateway

Sie müssen eine Verbindung mit Azure Cosmos DB mithilfe des dedizierten Gateways herstellen, um den integrierten Cache verwenden zu können. Das dedizierte Gateway verfügt über einen anderen Endpunkt als den Standardendpunkt, der mit Ihrem Azure Cosmos DB-Konto bereitgestellt wird. Wenn Sie eine Verbindung mit dem Endpunkt Ihres dedizierten Gateways herstellen, sendet Ihre Anwendung eine Anforderung an das dedizierte Gateway, das die Anforderung dann an verschiedene Back-End-Knoten weiterleitet. Wenn möglich, stellt der integrierte Cache das Ergebnis zur Verfügung.

Abbildung der Gatewaymodusverbindung mit einem dedizierten Gateway:

:::image type="content" source="./media/dedicated-gateway/dedicated-gateway-mode.png" alt-text="Abbildung der Funktionsweise des dedizierten Cosmos DB-Gateways" border="false":::
 
## <a name="provisioning-the-dedicated-gateway"></a>Bereitstellen des dedizierten Gateways

Ein dedizierter Gatewaycluster kann in API-Kernkonten (SQL) bereitgestellt werden. Ein dedizierter Gatewaycluster kann bis zu fünf Knoten enthalten, und Sie können Knoten jederzeit hinzufügen oder entfernen. Alle dedizierten Gatewayknoten in Ihrem Konto [verwenden dieselbe Verbindungszeichenfolge](how-to-configure-integrated-cache.md#configuring-the-integrated-cache).

Dedizierte Gatewayknoten sind voneinander unabhängig. Wenn Sie mehrere dedizierte Gatewayknoten bereitstellen, kann jeder einzelne Knoten jede beliebige Anforderung weiterleiten. Darüber hinaus verfügt jeder Knoten über einen separaten integrierten Cache. Die zwischengespeicherten Daten innerhalb jedes Knotens hängen von den Daten ab, die vor kurzem über diesen spezifischen Knoten [geschrieben oder gelesen](integrated-cache.md#item-cache) wurden. Anders ausgedrückt: Wenn ein Element oder eine Abfrage auf einem Knoten zwischengespeichert wird, wird es bzw. sie nicht unbedingt auf den anderen Knoten zwischengespeichert.

Für die Entwicklung empfiehlt es sich, mit einem Knoten zu beginnen, aber für die Produktion sollten Sie drei oder mehr Knoten bereitstellen, um Hochverfügbarkeit zu erzielen. [Erfahren Sie, wie Sie einen dedizierten Gatewaycluster mit einem integrierten Cache bereitstellen.](how-to-configure-integrated-cache.md) Durch die Bereitstellung mehrerer dedizierter Gatewayknoten kann der dedizierte Gatewaycluster weiterhin Anforderungen weiterleiten und zwischengespeicherte Daten verarbeiten, auch wenn einer der dedizierten Gatewayknoten nicht verfügbar ist.

Da es sich um eine öffentliche Vorschauversion handelt, verfügt das dedizierte Gateway nicht über eine Verfügbarkeits-SLA. Im Allgemeinen sollten Sie jedoch eine vergleichbare Verfügbarkeit wie für den Rest Ihres Azure Cosmos DB-Kontos erwarten.

Das dedizierte Gateway ist in den folgenden Größen verfügbar:

| **SKU-Name** | **vCPU** | **Arbeitsspeicher**  |
| ------------ | -------- | ----------- |
| **D4s**      | **4**    | **16 GB** |
| **D8s**      | **8**    | **32 GB** |
| **D16s**     | **16**   | **64 GB** |

> [!NOTE]
> Nach dem Erstellen können Sie die Größe der dedizierten Gatewayknoten nicht mehr ändern. Sie können jedoch Knoten hinzufügen oder entfernen.

## <a name="dedicated-gateway-in-multi-region-accounts"></a>Dediziertes Gateway in Konten mit mehreren Regionen

Wenn Sie einen dedizierten Gatewaycluster in Konten mit mehreren Regionen bereitstellen, werden in jeder Region identische dedizierte Gatewaycluster bereitgestellt. Betrachten Sie z. B. ein Azure Cosmos DB-Konto in den Regionen „USA, Osten“ und „Europa, Norden“. Wenn Sie einen dedizierten Gatewaycluster mit zwei D8-Knoten in diesem Konto bereitstellen, verfügen Sie insgesamt über vier D8-Knoten – zwei in „USA, Osten“ und zwei in „Europa, Norden“. Sie müssen nicht explizit dedizierte Gateways in jeder Region konfigurieren, und Ihre Verbindungszeichenfolge bleibt unverändert. Es gibt auch keine Änderungen an bewährten Methoden für das Ausführen von Failovern.

> [!NOTE]
> Sie können einen dedizierten Gatewaycluster nicht in Konten mit aktivierten Verfügbarkeitszonen bereitstellen.

Wie Knoten innerhalb eines Clusters sind regionsübergreifende dedizierte Gatewayknoten unabhängig. Es ist möglich, dass sich die zwischengespeicherten Daten in jeder Region unterscheiden, abhängig von den letzten Lese- oder Schreibvorgängen in dieser Region.

## <a name="limitations"></a>Einschränkungen

Für das dedizierte Gateway gelten während der öffentlichen Vorschau die folgenden Einschränkungen:

- Dedizierte Gateways werden nur für SQL-API-Konten unterstützt.
- Sie können ein dediziertes Gateway nicht in Azure Cosmos DB-Konten mit [IP-Firewalls](how-to-configure-firewall.md) oder [konfigurierten](how-to-configure-private-endpoints.md) Private Link-Verbindungen bereitstellen.
- Sie können ein dediziertes Gateway nicht in einem Azure Cosmos DB-Konto in einem [virtuellen Netzwerk (VNET)](how-to-configure-vnet-service-endpoint.md) bereitstellen.
- Sie können ein dediziertes Gateway nicht in Azure Cosmos DB-Konten mit [Verfügbarkeitszonen](high-availability.md#availability-zone-support) bereitstellen.
- Sie können die [rollenbasierte Zugriffssteuerung (RBAC)](how-to-setup-rbac.md) nicht verwenden, um Datenebenenanforderungen zu authentifizieren, die über das dedizierte Gateway weitergeleitet werden.

## <a name="supported-regions"></a>Unterstützte Regionen

Das dedizierte Gateway befindet sich in der öffentlichen Vorschau und wird noch nicht in jeder Azure-Region unterstützt. Während der gesamten öffentlichen Vorschau werden wir neue Funktionen hinzufügen. Es gelten keine Regionseinschränkungen mehr, wenn das dedizierte Gateway allgemein verfügbar ist.

Aktuelle Liste der unterstützten Azure-Regionen:

| **Amerika** | **Europa und Afrika**  | **Asien-Pazifik**  |
| ------------ | -------- | ----------- | 
| Brasilien Süd      | Frankreich, Mitte    | Australien, Mitte |
| Kanada, Mitte  | Frankreich, Süden    | Australien, Mitte 2 |
| Kanada, Osten     | Deutschland, Norden   | Australien, Südosten |
| USA (Mitte)     | Deutschland, Westen-Mitte   | Indien, Mitte |
| East US     | Nordeuropa   | Asien, Osten |
| USA (Ost) 2     | Schweiz, Norden   | Japan, Westen |
| USA Nord Mitte     | UK, Süden   | Korea, Mitte |
| USA Süd Mitte     | UK, Westen   | Korea, Süden |
| USA, Westen-Mitte     | Europa, Westen   | Asien, Südosten |
| USA (Westen)     |   | VAE, Mitte |
| USA, Westen 2     |    | Indien, Westen |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung dedizierter Gateways finden Sie in den folgenden Artikeln:

- [Integrierter Cache](integrated-cache.md)
- [Konfigurieren des integrierten Caches](how-to-configure-integrated-cache.md)
- [Häufig gestellte Fragen zum integrierten Cache](integrated-cache-faq.md)
- Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    - Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
    - Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)