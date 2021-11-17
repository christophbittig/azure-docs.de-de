---
title: Azure-Dienste, die Verfügbarkeitszonen unterstützen
description: Erfahren Sie, welche Dienste von Verfügbarkeitszonen unterstützt werden, und lernen Sie die Ausfallsicherheit für alle Azure-Dienste kennen.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 2c039bb9cabdd1d4786d4f0d76d591887781e2b5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302119"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-Dienste, die Verfügbarkeitszonen unterstützen

Azure [Regionen und Verfügbarkeitszonen](az-overview.md) sind physisch getrennte Standorte innerhalb jeder Azure-Region, die aufgrund der redundanten Infrastruktur und der logischen Isolierung von Azure-Diensten tolerant gegenüber Ausfällen des Rechenzentrums sind. 

Bei Verfügbarkeitszonen geht es vor allem um Netzwerkredundanz. Azure-Dienste, die Verfügbarkeitszonen unterstützen, sind so konzipiert, dass sie das richtige Maß an Ausfallsicherheit und Flexibilität sowie eine extrem niedrige Latenz bieten. Bei Azure-Diensten, die Verfügbarkeitszonen unterstützen, ist der Nutzen derselbe, unabhängig davon, ob Sie Ihre eigene Ausfallsicherheit entwickeln oder sich für eine automatische Replikation und Verteilung entscheiden. Sie erhalten eine überragende Ausfallsicherheit über hochverfügbare Dienste, unabhängig vom Diensttyp. 

Azure ist bestrebt, eine hohe Ausfallsicherheit für jeden Dienst und jedes Angebot zu ermöglichen. Die Ausführung von Azure-Diensten, die Verfügbarkeitszonen unterstützen, bietet eine vollständig transparente und konsistente Ausfallsicherheit für nahezu alle Szenarien, ohne Unterbrechung.

## <a name="azure-regions-with-availability-zones"></a>Azure-Regionen mit Verfügbarkeitszonen

Azure bietet die umfangreichste globale Ausdehnung aller Cloud-Anbieter und eröffnet rasch neue Regionen und Verfügbarkeitszonen. Die folgenden Regionen unterstützen derzeit Verfügbarkeitszonen.

| Amerika | Europa | Afrika | Asien-Pazifik |
|--------------------|----------------------|---------------------|----------------|
| Brasilien Süd | Frankreich, Mitte | Südafrika, Norden | Australien (Osten) |
| Kanada, Mitte | Deutschland, Westen-Mitte | | Indien, Mitte\* |
| USA (Mitte) | Nordeuropa | | Japan, Osten |
| East US | Norwegen, Osten | | Korea, Mitte |
| USA (Ost) 2 | UK, Süden | | Asien, Südosten |
| USA Süd Mitte | Europa, Westen | | Asien, Osten |
| US Government, Virginia | Schweden | | |
| USA, Westen 2 | | | |
| USA, Westen 3 | | | |

\* Wenn Sie mehr über Verfügbarkeitszonen und die Unterstützung der verfügbaren Dienste in dieser Region erfahren möchten, wenden Sie sich an Ihren Microsoft-Vertriebs- oder Kundenvertreter. Informationen über zukünftige Regionen, die Verfügbarkeitszonen unterstützen werden, finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).

Eine Liste der Azure-Dienste, die Verfügbarkeitszonen nach Azure-Regionen unterstützen, finden Sie in der Dokumentation [Verfügbarkeitszonen](az-overview.md).

## <a name="highly-available-services"></a>Hochverfügbare Dienste

Drei Arten von Azure-Diensten unterstützen Verfügbarkeitszonen: *zonale*, *zonale-redundante* und *immer verfügbare* Dienste. Sie können alle drei dieser Architekturansätze kombinieren, wenn Sie Ihre Ausfallsicherheitsstrategie entwerfen.

- **Zonale Dienste**: Eine Ressource kann in einer bestimmten, selbstgewählten Verfügbarkeitszone bereitgestellt werden, um strengere Latenz- oder Leistungsanforderungen zu erfüllen. Die Resilienz wird quasi von selbst erzielt, indem Anwendungen und Daten in einer oder mehreren Zonen der Region repliziert werden. Ressourcen können an eine bestimmte Zone angeheftet werden. So können beispielsweise virtuelle Maschinen, verwaltete Datenträger oder Standard-IP-Adressen an eine bestimmte Zone gebunden werden, was die Ausfallsicherheit erhöht, da eine oder mehrere Instanzen von Ressourcen über mehrere Zonen verteilt sind.
- **Zonenredundante Dienste**: Ressourcen werden automatisch über Zonen hinweg repliziert oder verteilt. Beispielsweise replizieren zonenredundante Dienste die Daten über drei Zonen, so dass ein Ausfall in einer Zone die Hochverfügbarkeit der Daten nicht beeinträchtigt. 
- **Ständig verfügbare Dienste**: Ständig verfügbar in allen Azure-Gebieten und widerstandsfähig gegenüber zonen- und regionsweiten Ausfällen. Eine vollständige Liste der stets verfügbaren Dienste, auch als nicht-regionale Dienste bezeichnet, in Azure finden Sie unter [Produkte, die nach Region verfügbar sind](https://azure.microsoft.com/global-infrastructure/services/).

Wie bereits in diesem Artikel erwähnt, sind nicht alle Azure-Dienste durchgängig in allen Regionen verfügbar. Die Angebote werden in drei Kategorien eingeteilt, die ihre _regionale_ Verfügbarkeit widerspiegeln: *Grundlagen*, *Mainstream* und *strategische* Dienste. Die allgemeine Richtlinie von Azure zur Bereitstellung von Diensten in einer bestimmten Region richtet sich in erster Linie nach dem Regionstyp, der Dienstkategorie und der Kundennachfrage. Weitere Informationen finden Sie unter [Azure-Dienste](region-types-service-categories-azure.md).

- **Grundlegende Dienste**: Verfügbar in allen empfohlenen und alternativen Regionen, sobald eine Region allgemein verfügbar ist oder innerhalb von 90 Tagen, nachdem ein neuer Foundational Service allgemein verfügbar wurde.
- **Mainstream-Dienste**: Verfügbar in allen empfohlenen Regionen innerhalb von 90 Tagen nach der allgemeinen Verfügbarkeit einer Region. Mainstream-Dienste sind in alternativen Regionen nachfrageorientiert, und viele sind bereits in einer großen Teilmenge alternativer Regionen bereitgestellt.
- **Strategische Dienste**: Gezielte Dienstleistungsangebote, oft branchenspezifisch oder unterstützt durch maßgeschneiderte Hardware. Strategische Dienste sind nachfrageorientiert und regional verfügbar, und viele sind bereits in einer großen Teilmenge der empfohlenen Regionen bereitgestellt.

Azure-Dienste, die Verfügbarkeitszonen unterstützen, einschließlich zonaler und zonenredundanter Angebote, werden ständig erweitert.

Weitere Informationen zu virtuellen Maschinen der älteren Generation finden Sie unter [Vorherige Generationen von Größen virtueller Maschinen](../virtual-machines/sizes-previous-gen.md).

Die folgenden Tabellen geben einen Überblick über das aktuelle Angebot an zonalen, zonenredundanten und immer verfügbaren Azure-Diensten. Sie listen die Azure-Angebote entsprechend ihrer regionalen Verfügbarkeit auf.

##### <a name="legend"></a>Legende
![Legende mit Symbolen und deren Bedeutung in Bezug auf die Dienstkategorie und die regionale Verfügbarkeit der einzelnen Dienste in der Tabelle.](media/legend.png) 

Im Produktkatalog werden ständig verfügbare Dienste als "nicht regionale" Dienste aufgeführt.

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![Ein Symbol, das diesen Dienst kennzeichnet, ist grundlegend.](media/icon-foundational.svg) Grundlegende Dienste 

| **Produkte**   | **Resilienz**   |
| --- | --- |
| [Azure Application Gateway (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| [Azure Backup](../backup/backup-create-rs-vault.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure: öffentliche IP-Adresse](../virtual-network/ip-services/public-ip-addresses.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Azure SQL-Datenbank ([Universell-Tier](../azure-sql/database/high-availability-sla.md))  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Azure SQL-Datenbank ([Premium &amp; Unternehmenskritische tier](../azure-sql/database/high-availability-sla.md))  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Disk Storage](../storage/common/storage-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure-Lastenausgleich](../load-balancer/load-balancer-standard-availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Storage-Konto](../storage/common/storage-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Speicherung: [Heiße/kalte Azure Blob Storage-Ebenen](../storage/common/storage-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Speicherung: [Verwaltete Festplatten](../virtual-machines/managed-disks-overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| [Dokumentation zu virtuellen Computern](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Av2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Bs-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [DSv2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [DSv3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Dv2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Dv3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [ESv3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Ev3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [F-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [FS-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![Ein Symbol zeigt an, dass es sich um einen Mainstream-Dienst handelt.](media/icon-mainstream.svg) Hauptdienste

| **Produkte**   | **Resilienz**   |
| --- | --- |
| [App Service](../app-service/how-to-zone-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [ Azure App Service-Umgebungen ](../app-service/environment/zone-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Active Directory-Domänendienste](../active-directory-domain-services/overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure API Management](../api-management/zone-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Cognitive Search](../search/search-performance-optimization.md#availability-zones)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services: [Textanalyse](../cognitive-services/text-analytics/index.yml)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Data Factory](../data-factory/index.yml)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Azure Database for MySQL – [Flexible Server](../mysql/flexible-server/concepts-high-availability.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Azure Database for PostgreSQL – [Flexible Server](../postgresql/flexible-server/overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) |
| [Azure-Datenträgerverschlüsselung](../virtual-machines/disks-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure-Funktionen](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Monitor](../azure-monitor/logs/availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Monitor: Application Insights](../azure-monitor/logs/availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Monitor: Log Analytics](../azure-monitor/logs/availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Private Link](../private-link/private-link-overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| Azure SQL: [Virtuelle Maschine](../azure-sql/database/high-availability-sla.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) |
| [Azure Web Application Firewall](../firewall/deploy-availability-zone-powershell.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Container Registry](../container-registry/zone-redundancy.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Azure Event Grid](../event-grid/overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist](media/icon-zone-redundant.svg)  |
| [Azure HDInsight](../hdinsight/hdinsight-use-availability-zones.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| [Azure Network Watcher](../network-watcher/frequently-asked-questions.yml)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Network Watcher: [Traffic Analytics](../network-watcher/frequently-asked-questions.yml)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| [Blob Storage Premium](../storage/common/storage-account-overview.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Speicherung: [ Azure Premium Files](../storage/files/storage-files-planning.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Virtual Machines: [Azure Dedicated Host](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Ddsv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Ddv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Dsv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Dv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Edsv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Edv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Esv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Ev4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [Fsv2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| Virtual Machines: [M-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |
| [Azure Virtual WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Virtuelles WAN: [Azure ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Virtual WAN: [Point-to-site VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Virtual WAN: [VPN-Gateway von Standort zu Standort](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |

### <a name="an-icon-that-signifies-this-service-is-strategic-strategic-services"></a>![Ein Symbol zeigt an, dass es sich um einen strategischen Dienst handelt.](media/icon-strategic.svg) Strategische Dienste

| **Produkte**   | **Resilienz**   |
| --- | --- |
| Azure Red Hat OpenShift  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg) ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist](media/icon-zonal.svg)  |
| Azure Cognitive Services: Anomalieerkennung  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Azure Cognitive Services: Formularerkennung  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonenredundant ist.](media/icon-zone-redundant.svg)  |
| Azure Storage: Ultra Disk  | ![Ein Symbol, das anzeigt, dass dieser Dienst zonal ist.](media/icon-zonal.svg)  |

### <a name="an-icon-that-signifies-this-service-is-non-regional-non-regional-services-always-available-services"></a>![Ein Symbol, das bedeutet, dass dieser Dienst nicht regional verfügbar ist.](media/icon-always-available.svg) Nicht-regionale Dienste (ständig verfügbare Dienste)

| **Produkte**   | **Resilienz**   |
| --- | --- |
| Azure DNS  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Active Directory  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Advanced Threat Protection  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Advisor  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Blueprint  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Bot Services  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Front Door  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Microsoft Defender für IoT  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Front Door  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Information Protection  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Lighthouse  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Managed Applications  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Maps  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Performance Diagnostics  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Policy  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Resource Graph  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Microsoft Sentinel  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Stack  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Stack Edge  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Content Delivery Network  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Cost Management und Abrechnung | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Kunden-Lockbox für Microsoft Azure  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Microsoft Intune  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Peering Service  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure-Portal  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Microsoft Defender für Cloud-Apps  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Microsoft Graph  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Microsoft Defender für Cloud  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |
| Azure Traffic Manager  | ![Ein Symbol, das anzeigt, dass dieser Dienst immer verfügbar ist.](media/icon-always-available.svg) |

Eine Liste der Azure-Dienste, die Verfügbarkeitszonen nach Azure-Regionen unterstützen, finden Sie in der Dokumentation [Verfügbarkeitszonen](az-overview.md).

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>Preisgestaltung für virtuelle Maschinen in Verfügbarkeitszonen

Sie können auf Azure-Verfügbarkeitszonen zugreifen, indem Sie Ihr Azure-Abonnement verwenden. Weitere Informationen finden Sie unter [Bandwidth Preisen](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="next-steps"></a>Nächste Schritte

- [Lösungen für hohe Verfügbarkeit mit Verfügbarkeitszonen](/azure/architecture/high-availability/building-solutions-for-high-availability)
- [Hochverfügbarkeit mit Azure-Diensten](/azure/architecture/framework/resiliency/overview)
- [Entwurfsmuster für Hochverfügbarkeit](/azure/architecture/framework/resiliency/app-design)
