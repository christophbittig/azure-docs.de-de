---
title: Azure-Dienste mit Unterstützung für Verfügbarkeitszonen
description: Zur Erstellung hoch verfügbarer und robuster Anwendungen in Azure bieten Verfügbarkeitszonen physisch voneinander getrennte Orte für die Ressourcenausführung.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: prsandhu
ms.reviewer: cnthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 22b6f4570736d891b7cbcb9c0d2c10d6847ee83f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533663"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-Dienste mit Unterstützung für Verfügbarkeitszonen

Die globale Microsoft Azure-Infrastruktur wurde auf allen Ebenen so entworfen und konstruiert, dass für Kunden der höchste Grad an Redundanz und Resilienz erzielt wird. Die Azure-Infrastruktur setzt sich aus geografischen Bereichen, Regionen und Verfügbarkeitszonen zusammen, mit denen sichergestellt wird, dass die Auswirkungen eines Fehlers begrenzt bleiben und die Anwendungen und Daten der Kunden nicht stärker beeinträchtigt werden. Das Konstrukt mit Azure-Verfügbarkeitszonen wurde entwickelt, um eine Software- und Netzwerklösung als Schutz vor Rechenzentrumsfehlern bereitzustellen und für unsere Kunden eine bessere Hochverfügbarkeit (HA) sicherzustellen.

Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig voneinander funktionieren. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region bewirkt die Auswirkungen von Zonenausfällen auf Anwendungen und Daten, z. B. größere Überflutungen, Stürme bzw. Orkane und andere Ereignisse, die eine Störung in Bezug auf den Standortzugriff, die sichere Übertragung, den Betrieb von Versorgungseinrichtungen und die Verfügbarkeit von Ressourcen darstellen können. Verfügbarkeitszonen und die zugehörigen Rechenzentren sind so konzipiert, dass Dienste, Kapazität und Verfügbarkeit bei Kompromittierung einer Zone von den anderen Verfügbarkeitszonen der Region unterstützt werden.

Alle Azure-Verwaltungsdienste sind so konstruiert, dass Sie gegenüber regionsweiten Ausfällen resilient sind. Im Spektrum möglicher Ausfälle besitzen ein oder mehrere Verfügbarkeitszonenausfälle innerhalb einer Region einen geringeren Ausfallradius als der Ausfall einer ganzen Region. Azure kann von einem zonenweiten Ausfall von Verwaltungsdiensten innerhalb der Region wiederhergestellt werden. Azure führt wichtige Wartungsaufgaben immer zonenweise innerhalb einer Region durch, um Ausfälle zu verhindern, die sich auf Kundenressourcen auswirken, die über Verfügbarkeitszonen innerhalb einer Region hinweg bereitgestellt sind.


![Konzeptionelle Ansicht einer Azure-Region mit drei Zonen](./media/az-region/azure-region-availability-zones.png)


Azure-Dienste, die Verfügbarkeitszonen unterstützen, können in drei Kategorien unterteilt werden: **zonale**, **zonenredundante** und **nicht-regionale** Dienste. Kundenworkloads können so kategorisiert werden, dass jedes dieser Architekturszenarios verwendet werden kann, um die erforderliche Anwendungsleistung und -stabilität zu erzielen.

- **Zonale Dienste:** Eine Ressource kann in einer bestimmten selbst ausgewählten Verfügbarkeitszone bereitgestellt werden, um strengere Latenz- oder Leistungsvorgaben zu erfüllen.  Die Resilienz wird quasi von selbst erzielt, indem Anwendungen und Daten in einer oder mehreren Zonen der Region repliziert werden.  Ressourcen können an eine bestimmte Zone angeheftet werden. Beispielsweise können virtuelle Computer, verwaltete Datenträger oder IP-Standardadressen an eine bestimmte Zone angeheftet werden. So können Sie eine bessere Resilienz erzielen, da eine oder mehrere Ressourceninstanzen auf mehrere Zonen verteilt werden.

- **Zonenredundante Dienste**: Ressourcen werden automatisch zonenübergreifend repliziert oder verteilt. Bei ZRS (zonenredundanter Speicher) werden die Daten beispielsweise in drei Zonen repliziert, damit der Ausfall einer Zone keine Auswirkungen auf die Hochverfügbarkeit der Daten hat.  

- **Nicht regionale Dienste**: Dienste sind immer in Azure-Geografien verfügbar und sowohl resilient gegen zonenweite als auch regionsweite Ausfälle. 


Um eine umfassende Geschäftskontinuität in Azure zu erreichen, erstellen Sie die Anwendungsarchitektur über die Kombination von Verfügbarkeitszonen mit Azure-Regionspaaren. Sie können Anwendungen und Daten mithilfe von Verfügbarkeitszonen für hohe Verfügbarkeit innerhalb einer Azure-Region synchron replizieren und für Notfallwiederherstellungen über Azure-Regionen asynchron replizieren. Weitere Informationen finden Sie unter [Entwickeln von Lösungen für Hochverfügbarkeit mit Verfügbarkeitszonen](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Azure-Dienste mit Unterstützung für Verfügbarkeitszonen

 - Die VMs älterer Generationen sind nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../virtual-machines/sizes-previous-gen.md).
 - Wie bereits in [Regionen und Verfügbarkeitszonen in Azure](az-overview.md) erwähnt, sind einige Dienste nicht regional. Bei diesen Diensten besteht keine Abhängigkeit von einer bestimmten Azure-Region. Deshalb sind sie sowohl resilient gegen zonenweite als auch regionsweite Ausfälle.  Eine Liste nicht-regionaler Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Azure-Regionen mit Verfügbarkeitszonen
 

| Amerika           | Europa               | Afrika              | Asien-Pazifik   |
|--------------------|----------------------|---------------------|----------------|
| Brasilien Süd       | Frankreich, Mitte       | Südafrika, Norden  | Australien (Osten) |
| Kanada, Mitte     | Deutschland, Westen-Mitte |                     | Indien, Mitte* |
| USA (Mitte)         | Nordeuropa         |                     | Japan, Osten     |
| East US            | Norwegen, Osten          |                     | Korea, Mitte  |
| USA (Ost) 2          | UK, Süden             |                     | Asien, Südosten |
| USA Süd Mitte   | Europa, Westen          |                     |                |
| US Government, Virginia    | Schweden*              |                     |                |
| USA, Westen 2          |                      |                     |                |
| USA, Westen 3          |                      |                     |                |

\* Weitere Informationen zur Unterstützung von Verfügbarkeitszonen und verfügbaren Diensten in diesen Regionen erhalten Sie von Ihrem Microsoft-Vertriebs- oder Kundenbeauftragten. Informationen zu den Regionen, die zukünftig Verfügbarkeitszonen unterstützen werden, finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Azure-Dienste mit Unterstützung für Verfügbarkeitszonen

- VMs älterer Generationen werden im Folgenden nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../virtual-machines/sizes-previous-gen.md).

- Einige Dienste sind nicht regional. Weitere Informationen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](az-overview.md). Bei diesen Diensten besteht keine Abhängigkeit von einer bestimmten Azure-Region. Deshalb sind sie resilienter gegen zonenweite oder regionsweite Ausfälle.  Eine Liste nicht-regionaler Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Zonenresiliente Dienste 

:globe_with_meridians: Nicht regionale Dienste: Dienste sind immer in Azure-Geografien verfügbar und sowohl resilient gegen zonenweite als auch regionsweite Ausfälle.

:large_blue_diamond:   Resilient gegenüber zonenweiten Ausfällen 

**Grundlegende Dienste**

|     Produkte                                                    | Resilienz             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)                                  | :large_blue_diamond:  |
|     [Azure Backup](../backup/backup-create-rs-vault.md#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)                             | :large_blue_diamond:  |
|     [Azure DNS: Azure DNS Private Zones](../dns/private-dns-getstarted-portal.md)                   | :large_blue_diamond:  |
|     [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md)                                       | :large_blue_diamond:  |
|     [Azure: öffentliche IP-Adresse](../virtual-network/public-ip-addresses.md)                                           | :large_blue_diamond:  |
|     Azure SQL-Datenbank ([Tarif „Universell“](../azure-sql/database/high-availability-sla.md))                 | :large_blue_diamond:  |
|     Azure SQL-Datenbank ([Tarife „Premium“ und „Unternehmenskritisch“](../azure-sql/database/high-availability-sla.md))     | :large_blue_diamond:  |
|     [Disk Storage](../storage/common/storage-redundancy.md)                                                | :large_blue_diamond:  |
|     [Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)                                                  | :large_blue_diamond:  |
|     [Schlüsseltresor](../key-vault/general/disaster-recovery-guidance.md)                                                   | :large_blue_diamond:  |
|     [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md)                                               | :large_blue_diamond:  |
|     [Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)                                            | :large_blue_diamond:  |
|     [Speicherkonto](../storage/common/storage-redundancy.md)                                           | :large_blue_diamond:  |
|     Speicher: [Heiße/Kalte Blobspeicherebenen](../storage/common/storage-redundancy.md)                      | :large_blue_diamond:  |
|     Speicher: [Managed Disks](../virtual-machines/managed-disks-overview.md)                                    | :large_blue_diamond:  |
|     [VM-Skalierungsgruppen](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)                               | :large_blue_diamond:  |
|     [Virtual Machines](../virtual-machines/windows/create-powershell-availability-zone.md)                                          | :large_blue_diamond:  |
|     Virtual Machines: [Av2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [Bs-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [DSv2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     Virtual Machines: [DSv3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                            | :large_blue_diamond:  |
|     Virtual Machines: [Dv2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     Virtual Machines: [Dv3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [ESv3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     Virtual Machines: [Ev3-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [F-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [FS-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md)                                         | :large_blue_diamond:  |
|     [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                                             | :large_blue_diamond:  |


**Hauptdienste**


|     Produkte                                                    | Resilienz             |
|-----------------------------------------------------------------|:----------------------------:|
|     [App Service](../app-service/how-to-zone-redundancy.md)                                    | :large_blue_diamond:  |
|     [App Service-Umgebungen](../app-service/environment/zone-redundancy.md)                                    | :large_blue_diamond:  |
|     [Azure Active Directory-Domänendienste](../active-directory-domain-services/overview.md)                      | :large_blue_diamond:  |
|     [Azure API Management](../api-management/zone-redundancy.md)                      | :large_blue_diamond:  |
|     [Azure App Configuration](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)   | :large_blue_diamond:  |    
|     [Azure Batch](/azure/batch/create-pool-availability-zones)                                               | :large_blue_diamond:  |
|     [Azure Bastion](../bastion/bastion-overview.md)                                               | :large_blue_diamond:  |
|     [Azure Cache for Redis](../azure-cache-for-redis/cache-high-availability.md)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](../search/search-performance-optimization.md#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [Textanalyse](../cognitive-services/text-analytics/index.yml)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     [Azure Data Factory](../data-factory/index.yml)                               | :large_blue_diamond:  |
|     Azure Database for MySQL: [Flexibler Server](../mysql/flexible-server/concepts-high-availability.md)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL: [Flexibler Server](../postgresql/flexible-server/overview.md)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](../ddos-protection/ddos-faq.yml)                                       | :large_blue_diamond:  |
|     [Azure-Datenträgerverschlüsselung](../virtual-machines/disks-redundancy.md)                                       | :large_blue_diamond:  |
|     [Azure Firewall](../firewall/deploy-availability-zone-powershell.md)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](../firewall-manager/quick-firewall-policy.md)                                      | :large_blue_diamond:  |
|     [Azure-Funktionen](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)     | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](../aks/availability-zones.md)                              | :large_blue_diamond:  |
|     [Azure Media Services (AMS)](../media-services/latest/concept-availability-zones.md)        | :large_blue_diamond:  |
|     [Azure Monitor](/azure/azure-monitor/logs/availability-zones)        | :large_blue_diamond:  |
|     [Azure Monitor: Application Insights](/azure/azure-monitor/logs/availability-zones)        | :large_blue_diamond:  |
|     [Azure Monitor: Log Analytics](/azure/azure-monitor/logs/availability-zones)        | :large_blue_diamond:  |
|     [Azure Private Link](../private-link/private-link-overview.md)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)                                       | :large_blue_diamond:  |
|     Azure SQL: [Virtual Machine](../azure-sql/database/high-availability-sla.md)                                                                 | :large_blue_diamond:  |
|     [Azure Web Application Firewall](../firewall/deploy-availability-zone-powershell.md)                                                         | :large_blue_diamond:  |
|     [Container Registry](../container-registry/zone-redundancy.md)                                                                               | :large_blue_diamond:  |
|     [Event Grid](../event-grid/overview.md)                                                                                                      | :large_blue_diamond:  |
|     [HDInsight](/azure/hdinsight/hdinsight-use-availability-zones)                                                                               | :large_blue_diamond:  |
|     [Network Watcher](/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                     | :large_blue_diamond:  |
|     Network Watcher: [Traffic Analytics](/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                  | :large_blue_diamond:  |
|     [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)                                                      | :large_blue_diamond:  |
|     [Blob Storage Premium](../storage/blobs/storage-blob-performance-tiers.md)                                        | :large_blue_diamond:  |
|     Storage: [Azure Files Premium](../storage/files/storage-files-planning.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [Azure Dedicated Host](../virtual-machines/windows/create-powershell-availability-zone.md)                     | :large_blue_diamond:  |
|     Virtual Machines: [Ddsv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [Ddv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dsv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [Edsv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     Virtual Machines: [Edv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Esv4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [Ev4-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     Virtual Machines: [Fsv2-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     Virtual Machines: [M-Serie](../virtual-machines/windows/create-powershell-availability-zone.md)                                  | :large_blue_diamond:  |
|     [Virtual WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [Point-to-Site-VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                      | :large_blue_diamond:  |
|     Virtual WAN: [Site-to-Site-VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                       | :large_blue_diamond:  |


**Spezialisierte Dienste**

|     Produkte                                                    | Resilienz             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services: Anomalieerkennung                        | :large_blue_diamond:  |
|     Cognitive Services: Formularerkennung                         | :large_blue_diamond:  |
|     Storage: Disk Ultra                                         | :large_blue_diamond:  |


**Nicht regional**

|     Produkte                                                    | Resilienz             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprint                                            | :globe_with_meridians: |
|     Azure Bot Services                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender für IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Kunden-Lockbox für Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft Azure-Portal                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security Center                                           | :globe_with_meridians: |
|     Traffic Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Preise für VMs in Verfügbarkeitszonen

Azure-Verfügbarkeitszonen sind in Ihrem Azure-Abonnement verfügbar. Weitere Informationen finden Sie unter [Bandbreite – Preisdetails](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Erste Schritte mit Verfügbarkeitszonen

- [Erstellen eines virtuellen Computers](../virtual-machines/windows/create-portal-availability-zone.md)
- [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Erstellen einer zonenredundanten VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Zonenübergreifender Lastenausgleich für virtuelle Computer mithilfe eines Standardlastenausgleichs mit einem zonenredundanten Front-End](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Lastenausgleich für virtuelle Computer innerhalb einer Zone mithilfe eines Standardlastenausgleichs mit einem zonalen Front-End](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zonenredundanter Speicher](../storage/common/storage-redundancy.md)
- [Dienstebene „Universell“ für SQL-Datenbank](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Georedundante Notfallwiederherstellung in Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Georedundante Notfallwiederherstellung in Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Erstellen eines zonenredundanten Gateways für virtuelle Netzwerke](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Hinzufügen einer zonenredundanten Region für Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Erste Schritte mit Azure Cache für Redis-Verfügbarkeitszonen](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Erstellen einer Azure Active Directory Domain Services-Instanz](../active-directory-domain-services/tutorial-create-instance.md)
- [Erstellen eines Azure Kubernetes Service-Clusters (AKS), der Verfügbarkeitszonen verwendet](../aks/availability-zones.md)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Regionen und Verfügbarkeitszonen in Azure](az-overview.md)
