---
title: Worum handelt es sich bei der BareMetal-Infrastruktur in Azure?
description: Hier finden Sie eine Übersicht über die BareMetal-Infrastruktur in Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 744378fc00bc443fdae3babefdf626eacbf486e7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154867"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Worum handelt es sich bei der BareMetal-Infrastruktur in Azure?

Zur Erfüllung Ihrer geschäftlichen Anforderungen bietet Microsoft Azure eine Cloudinfrastruktur mit einer breiten Palette an integrierten Clouddiensten. Manchmal müssen Dienste jedoch unter Umständen auf Bare-Metal-Servern ohne Virtualisierungsebene ausgeführt werden. Möglicherweise benötigen Sie Root-Zugriff und die Kontrolle über das Betriebssystem (Operating System, OS). Für diese Fälle bietet Azure eine BareMetal-Infrastruktur für mehrere hochwertige und unternehmenskritische Anwendungen.

Die BareMetal-Infrastruktur besteht aus dedizierten BareMetal-Instanzen (Compute-Instanzen). Es stehen folgende Funktionen zur Verfügung:
- Hochleistungsspeicher entsprechend der Anwendung (NFS, ISCSI und Fibre Channel) Speicher kann auch von BareMetal-Instanzen gemeinsam genutzt werden, um Features wie Cluster mit horizontaler Skalierung oder Hochverfügbarkeitspaare mit STONITH zu ermöglichen. 
- Mehrere funktionsspezifische virtuelle LANs (VLANs) in einer isolierten Umgebung 
 
Darüber hinaus verfügt diese Umgebung über spezielle VLANs, auf die Sie zugreifen können, wenn Sie virtuelle Computer (Virtual Machines, VMs) in einem oder mehreren virtuellen Azure-Netzwerken (VNETs) in Ihrem Azure-Abonnement ausführen möchten. Die gesamte Umgebung wird als Ressourcengruppe in Ihrem Azure-Abonnement dargestellt.

Die BareMetal-Infrastruktur wird in über 30 SKUs angeboten. Dadurch stehen Server mit zwei bis 24 Sockets und 1,5 TB bis 24 TB Arbeitsspeicher zur Auswahl. Ein großer Teil der SKUs ist auch mit Octane-Arbeitsspeicher erhältlich. Azure bietet die größte Bandbreite an Bare-Metal-Instanzen in einer Hyperscalecloud.

## <a name="why-baremetal-infrastructure"></a>Gründe für eine BareMetal-Infrastruktur  

Einige Workloads im Unternehmen umfassen Technologien, die einfach nicht für die Ausführung in einer typischen virtualisierten Cloudumgebung gedacht sind. Sie erfordern eine spezielle Architektur, zertifizierte Hardware oder Sondergrößen. Die Technologien bieten zwar hochentwickelte Datenschutz- und Geschäftskontinuitätsfeatures, diese sind jedoch nicht für die virtualisierte Cloud konzipiert. Sie reagieren empfindlicher auf Wartezeiten und Noisy Neighbors und erfordern mehr Kontrolle über Change Management- und Wartungsaktivitäten.

Die BareMetal-Infrastruktur wurde für einen gewissen Teil solcher Anwendungen entwickelt, zertifiziert und getestet. Azure war der erste Anbieter solcher Lösungen und bietet als Marktführer das größte Portfolio und die ausgereiftesten Systeme.

### <a name="baremetal-benefits"></a>Bare-Metal-Vorteile  

Die BareMetal-Infrastruktur ist für kritische Workloads vorgesehen, die eine Zertifizierung erfordern, um Ihre Unternehmensanwendungen auszuführen. Die Bare-Metal-Instanzen sind ausschließlich für Sie dediziert, und Sie haben Vollzugriff (Root-Zugriff) auf das Betriebssystem (OS). Sie können die Betriebssystem- und Anwendungsinstallation gemäß Ihren Anforderungen verwalten. Aus Sicherheitsgründen werden die Instanzen in Ihrem Azure Virtual Network (VNet) ohne Internetkonnektivität bereitgestellt. Nur Dienste, die auf Ihren virtuellen Computern (virtual machines, VMs) ausgeführt werden, sowie andere Azure-Dienste im gleichen Netzwerk der Ebene 2 können mit Ihren Bare-Metal-Instanzen kommunizieren.  

Vorteile der BareMetal-Infrastruktur:

- Zertifizierte Hardware für spezialisierte Workloads
    - SAP (siehe [SAP-Hinweis 1928533](https://launchpad.support.sap.com/#/notes/1928533). Sie benötigen ein SAP-Konto für den Zugriff.)
    - Oracle (siehe [Oracle-Dokument-ID 948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52). Sie benötigen ein Oracle-Konto für den Zugriff.)
- Bare-Metal-Instanz ohne Hypervisor, Besitz eines einzelnen Mandanten
- Geringe Wartezeit zwischen in Azure gehosteten Anwendungs-VMs und BareMetal-Instanzen (0,35 ms)
- SSD und NVMe (flashbasiert)
    - Bis zu 1 PB/Mandant 
    - IOPS von bis zu 1,2 Millionen/Mandant 
    - Netzwerkbandbreite von 40/100 GB
    - Zugriff über NFS, ISCSI und FC möglich
- Redundanz bei Stromversorgung, Netzteilen, NICs, TORs, Ports, WANs, Speicher und Verwaltung
- Hotspares für den Austausch bei einem Ausfall (ohne Neukonfiguration)
- Mit dem Kunden koordinierte Wartungsfenster
- Momentaufnahmen, Archiv, Spiegelung und Klonen jeweils anwendungsabhängig

## <a name="sku-availability-in-azure-regions"></a>SKU-Verfügbarkeit nach Azure-Regionen

BareMetal Infrastructure bietet verschiedene SKUs, die für spezialisierte Workloads zertifiziert sind. Nutzen Sie die workloadspezifischen SKUs, um Ihre Anforderungen zu erfüllen.

- Große Instanzen: Systeme mit zwei bis vier Sockets  
- Sehr große Instanzen: Systeme mit vier bis zwanzig Sockets 

Die BareMetal-Infrastruktur für spezialisierte Workloads ist in den folgenden Azure-Regionen verfügbar:
- Europa, Westen
- Nordeuropa
- Deutschland, Westen-Mitte (Zonenunterstützung)
- USA, Osten 2 (Zonenunterstützung)
- USA, Osten (Zonenunterstützung)
- USA, Westen (Zonenunterstützung)
- USA, Westen 2 (Zonenunterstützung)
- USA Süd Mitte

>[!NOTE]
>**Zonenunterstützung** bezieht sich auf Verfügbarkeitszonen innerhalb einer Region, in der BareMetal-Instanzen zonenübergreifend bereitgestellt werden können, um hohe Resilienz und Verfügbarkeit zu erzielen. Dies ermöglicht die Unterstützung der Aktiv/Aktiv-Skalierung für mehrere Standorte.

## <a name="managing-baremetal-instances-in-azure"></a>Verwalten von BareMetal-Instanzen in Azure 

Die Anwendungstopologien der BareMetal-Infrastruktur können abhängig von Ihren Anforderungen durchaus komplex sein. Sie können mehrere Instanzen an einem oder mehreren Standorten bereitstellen. Die Instanzen können freigegebenen oder dedizierten Speicher sowie spezielle LAN- und WAN-Verbindungen aufweisen. Daher bietet Azure für die BareMetal-Infrastruktur eine Beratung durch einen Außendienstmitarbeiter (CSA/GBB) zur Zusammenarbeit mit Ihnen. 

Zum Zeitpunkt der Bereitstellung Ihrer Bare-Metal-Infrastruktur sind Betriebssystem, Netzwerke, Speichervolumes, Platzierungen in Zonen und Regionen sowie WAN-Verbindungen zwischen Standorten bereits vorkonfiguriert. Sie können Ihre Betriebssystemlizenzen (BYOL) registrieren, das Betriebssystem konfigurieren und die Anwendungsschicht installieren.

Alle Bare-Metal-Ressourcen sowie deren Status und Attribute werden im Azure-Portal angezeigt. Vor dort aus können Sie die Instanzen auch betreiben sowie Service Requests und Supporttickets erstellen. 

## <a name="operational-model"></a>Betriebsmodell

BareMetal Infrastructure ist mit ISO 27001, ISO 27017, SOC 1 und SOC 2 konform. Außerdem wird das BYOL-Modell (Bring-Your-Own-License, Verwendung Ihrer eigenen Lizenz) für Betriebssystem, spezialisierte Workloads und Anwendungen von Drittanbietern unterstützt.  

Wenn Sie Root-Zugriff und vollständige Kontrolle erhalten, sind Sie für Folgendes verantwortlich:
- Entwerfen und Implementieren von Sicherungs- und Wiederherstellungslösungen, Hochverfügbarkeit und Notfallwiederherstellung
- Lizenzierung, Sicherheit und Unterstützung für das Betriebssystem und die Drittanbietersoftware

Microsoft ist verantwortlich für:
- Bereitstellen der Hardware für spezialisierte Workloads 
- Bereitstellen des Betriebssystems

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagramm: Supportmodell für die BareMetal-Infrastruktur" border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal-Instanzstempel

Im BareMetal-Instanzstempel selbst sind die folgenden Komponenten kombiniert:

- **Computing:** Auf der Intel Xeon-Prozessorgeneration basierende Server, die die erforderliche Rechenleistung bieten und für die spezialisierte Workload zertifiziert sind

- **Netzwerk**: Ein einheitliches Hochgeschwindigkeits-Netzwerkfabric, das die Computing-, Speicher- und LAN-Komponenten miteinander verbindet

- **Speicher:** Eine Infrastruktur, auf die über ein einheitliches Netzwerkfabric zugegriffen wird

Innerhalb der mehrinstanzenfähigen Infrastruktur des BareMetal-Stempels werden Kunden als isolierte Mandanten bereitgestellt. Bei der Bereitstellung des Mandanten benennen Sie ein Azure-Abonnement in Ihrer Azure-Registrierung. Dieses Azure-Abonnement wird für die Abrechnung Ihrer BareMetal-Instanzen verwendet.

>[!NOTE]
>Ein Kunde, der eine BareMetal-Instanz bereitstellt, wird in einem Mandanten isoliert. Ein Mandant wird in der Schicht der Netzwerke, Speicher und von Compute von anderen Mandanten isoliert. Speicher- und Compute-Einheiten, die unterschiedlichen Mandanten zugewiesen wurden, sind füreinander in den BareMetal-Instanzen nicht sichtbar und können nicht miteinander kommunizieren.

## <a name="operating-system"></a>Betriebssystem

Während der Bereitstellung der BareMetal-Instanz können Sie das Betriebssystem auswählen, das auf den Computern installiert werden soll. 

>[!NOTE]
>Beachten Sie dabei, dass BareMetal Infrastructure ein BYOL-Modell nutzt.

Folgende Linux-Betriebssystemversionen sind verfügbar:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage

Die BareMetal-Infrastruktur stellt hochredundanten NFS-Speicher und Fibre Channel-Speicher bereit. Sie bietet eine tiefe Integration für Unternehmensworkloads wie SAP, SQL und Ähnliches. Darüber hinaus stehen anwendungskonsistente Datenschutz- und Datenverwaltungsfunktionen zur Verfügung. Die Self-Service-Verwaltungstools bieten platzsparende Momentaufnahmen sowie Klonfunktionen, differenzierte Replikationsfunktionen und eine zentralisierte Überwachung. Die Infrastruktur ermöglicht RPOs und RTOs im Nullbereich für Datenverfügbarkeit und Geschäftskontinuität. 

Die Speicherinfrastruktur bietet Folgendes:
- Bis zu vier Uplinks mit 100 GB
- Fibre Channel-Uplinks mit bis zu 32 GB
- SSD und NVMe-Laufwerk (jeweils flashbasiert)
- Besonders geringe Wartezeit und hoher Durchsatz
- Skalierung auf bis zu 4 PB Rohspeicher 
- Bis zu 11 Millionen IOPS

Folgende Datenzugriffsprotokolle werden unterstützt: 
- iSCSI 
- NFS (v3 oder v4) 
- Fibre Channel 
- NVMe über FC  

## <a name="networking"></a>Netzwerk

Die Architektur von Azure-Netzwerkdiensten ist ein wichtiger Aspekt einer erfolgreichen Bereitstellung spezialisierter Workloads auf BareMetal-Instanzen. Die Wahrscheinlichkeit ist hoch, dass nicht alle IT-Systeme bereits unter Azure angeordnet sind. Azure verfügt über Netzwerktechnologie, damit Azure für Ihre lokalen Softwarebereitstellungen wie ein virtuelles Rechenzentrum aussieht. Für BareMetal-Instanzen sind folgende Azure-Netzwerkfunktionen erforderlich:

- Virtuelle Azure-Netzwerke, die mit der ExpressRoute-Leitung verbunden sind, die wiederum mit Ihren lokalen Netzwerkressourcen verbunden ist
- Die ExpressRoute-Leitung, die die lokale Umgebung mit Azure verbindet, muss über eine Bandbreite von mindestens 1 GBit/s verfügen.
- Ausdehnung von Active Directory und DNS auf Azure oder vollständige Ausführung in Azure

Mit ExpressRoute können Sie Ihr lokales Netzwerk über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf die Cloud von Microsoft ausdehnen. **ExpressRoute Local** ermöglicht kostengünstige Datenübertragungen zwischen Ihrem lokalen Standort und der gewünschten Azure-Region. Mit **ExpressRoute Premium** können Sie die Konnektivität über geopolitische Grenzen hinweg erweitern. 

BareMetal-Instanzen werden im IP-Adressbereich Ihres Azure-VNET-Servers bereitgestellt.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagramm: Architektur der Azure BareMetal-Infrastruktur" lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Die dargestellte Architektur ist in drei Abschnitte unterteilt:
- **Links:** Zeigt die lokale Infrastruktur des Kunden, in der verschiedene Anwendungen ausgeführt werden. Die Verbindung erfolgt über den Partner oder einen lokalen Edgerouter wie Equinix. Weitere Informationen finden Sie unter [Konnektivitätsanbieter und Standorte: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Mitte:** Zeigt [ExpressRoute](../expressroute/expressroute-introduction.md), das mithilfe Ihres Azure-Abonnements bereitgestellt wurde und Konnektivität mit dem Azure-Edgenetzwerk bereitstellt.
- **Rechts:** Zeigt Azure IaaS und in diesem Fall die Verwendung virtueller Computer zum Hosten Ihrer Anwendungen, die in Ihrem virtuellen Azure-Netzwerk bereitgestellt werden.
- **Unten:** Zeigt die Verwendung des ExpressRoute-Gateways, auf dem [ExpressRoute FastPath](../expressroute/about-fastpath.md) aktiviert wurde, sodass es für die BareMetal-Konnektivität eine sehr niedrige Latenz ermöglicht.   
   >[!TIP]
   >Um dies zu unterstützen, sollte das ExpressRoute-Gateway UltraPerformance aufweisen. Weitere Informationen finden Sie unter [Informationen zu ExpressRoute-Gateways für virtuelle Netzwerke](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie, wie Sie BareMetal-Instanzen über das Azure-Portal identifizieren und mit ihnen interagieren.

> [!div class="nextstepaction"]
> [Herstellen von Verbindungen mit BareMetal-Instanzeinheiten in Azure](connect-baremetal-infrastructure.md)
