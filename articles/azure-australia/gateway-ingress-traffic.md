---
title: Steuern von eingehendem Datenverkehr für Azure Australien
description: Enthält eine Anleitung zur Steuerung von eingehendem Datenverkehr in Azure Australien, um Anforderungen der australischen Behörden in Bezug auf sichere Internetgateways zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: bc1bef31da6082e33e14ba3e9bd5b3c8b480c130
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253103"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Steuern von eingehendem Datenverkehr für Azure Australien

Ein Kernelement beim Schützen von ICT-Systemen ist die Steuerung des Netzwerkdatenverkehrs. Der Datenverkehr sollte auf den Verkehr beschränkt werden, der für den Betrieb des Systems erforderlich ist, um das Kompromittierungsrisiko zu verringern.

Dieser Leitfaden enthält ausführliche Informationen zur Funktionsweise von eingehendem Netzwerkdatenverkehr in Azure sowie Empfehlungen für die Implementierung von Netzwerksicherheitskontrollen für ein System mit Internetverbindung.

Die Netzwerkkontrollen sind am Verbraucherleitfaden und dem Handbuch zur Informationssicherheit (Information Security Manual, ISM) des Australian Cyber Security Centre (ACSC) ausgerichtet.

## <a name="requirements"></a>Requirements (Anforderungen)

Die allgemeinen Sicherheitsanforderungen für Systeme im Commonwealth sind im ISM festgelegt. Um Commonwealth-Entitäten bei der Implementierung der Netzwerksicherheit zu unterstützen, hat das ACSC [ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung (Implementing Network Segmentation and Segregation)](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm) veröffentlicht, und als Hilfestellung beim Schützen von Systemen in Cloudumgebungen hat das ACSC das Dokument [Cloud Computing Security for Tenants (Cloud Computing-Sicherheit für Mandanten)](https://www.cyber.gov.au/publications/cloud-computing-security-for-tenants) veröffentlicht.

In diesen Leitfäden wird der Kontext für die Implementierung der Netzwerksicherheit und die Steuerung des Datenverkehrs beschrieben, und sie enthalten praktische Empfehlungen für den Entwurf und die Konfiguration von Netzwerken.

Im Microsoft-Leitfaden [Cloud Computing Security for Tenants of Microsoft Azure](https://aka.ms/au-irap) (Cloud Computing-Sicherheit für Mandanten von Microsoft Azure) auf der australischen Seite des Service Trust Portal werden bestimmte Microsoft-Technologien beschrieben, die Sie zum Befolgen der Ratschläge in den Dokumenten des ACSC verwenden können.

Die folgenden wichtigen Anforderungen, die in den Dokumenten des ACSC angegeben sind, sind für die Kontrolle des eingehenden Datenverkehrs in Azure wichtig:

|BESCHREIBUNG|`Source`|
|---|---|
|**Implementieren der Netzwerksegmentierung und -trennung, z. B. eine n-schichtige Architektur mit Nutzung von hostbasierten Firewalls und Netzwerkzugriffssteuerungen des Cloudlösungsanbieters zum Beschränken der ein- und ausgehenden VM-Netzwerkkonnektivität auf die erforderlichen Ports bzw. Protokolle.**| _Cloud Computing for Tenants_ (Cloud Computing für Mandanten)|
|**Implementieren von ausreichend hoher Bandbreite, geringer Latenz und zuverlässiger Netzwerkkonnektivität** zwischen dem Mandanten (einschließlich der Remotebenutzer des Mandanten) und dem Clouddienst, um die Verfügbarkeitsanforderungen des Mandanten zu erfüllen.  | _Cloud Computing for Tenants_ (Cloud Computing für Mandanten)|
|**Anwenden von Technologien nicht nur auf Netzwerkebene**. Jeder Host und jedes Netzwerk sollten nach Möglichkeit auf der niedrigsten Ebene, die eine gute Verwaltung ermöglicht, segmentiert und getrennt werden. In den meisten Fällen gilt die Segmentierung und Trennung von der Sicherungsschicht bis hinauf zur Anwendungsschicht (einschließlich). In empfindlichen Umgebungen kann eine physische Isolation ratsam sein. Hostbasierte und netzwerkweite Maßnahmen sollten sich gegenseitig ergänzen und zentral überwacht werden. Es reicht nicht aus, als Sicherheitsmaßnahme nur eine Firewall oder Sicherheitsappliance zu nutzen. |_ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)_|
|**Nutzen der Prinzipien der geringsten Rechte und der unerlässlichen Informationen**. Wenn ein Host, Dienst oder Netzwerk nicht unbedingt mit einem anderen Host, Dienst oder Netzwerk kommunizieren muss, sollte dies auch nicht zugelassen werden. Wenn ein Host, Dienst oder Netzwerk über bestimmte Ports oder Protokolle nur mit einem anderen Host, Dienst oder Netzwerk kommunizieren muss, sollten alle anderen Ports oder Protokolle deaktiviert werden. Die Anwendung dieser Prinzipien in einem gesamten Netzwerk passt zur Beschränkung der Benutzerrechte und sorgt für eine erhebliche Verbesserung des gesamten Sicherheitsstatus der Umgebung. |_ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)_|
|**Trennen von Hosts und Netzwerken basierend auf ihrer Empfindlichkeit oder Wichtigkeit in Bezug auf Geschäftsvorgänge**. Die Trennung kann erreicht werden, indem verschiedene Hardwarekomponenten oder Plattformen verwendet werden – je nach den jeweiligen Sicherheitsklassifizierungen, Sicherheitsdomänen oder Verfügbarkeits- bzw. Integritätsanforderungen für bestimmte Hosts oder Netzwerke. Achten Sie vor allem darauf, dass Sie Verwaltungsnetzwerke trennen, und erwägen Sie die physische Isolation von Out-of-band-Verwaltungsnetzwerken für empfindliche Umgebungen. |_ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)_|
|**Identifizieren, Authentifizieren und Autorisieren des Zugriffs aller Entitäten auf alle anderen Entitäten**. Für alle Benutzer, Hosts und Dienste sollte der Zugriff ausschließlich auf die anderen Benutzer, Hosts und Dienste beschränkt werden, die zur Durchführung der zugeteilten Aufgaben oder Funktionen erforderlich sind. Alle Legacy- oder lokalen Dienste, durch die die Sicherheit von Identifizierungs-, Authentifizierungs- und Autorisierungsdiensten umgangen oder herabgestuft wird, sollten deaktiviert werden, und die Nutzung sollte genau überwacht werden. |_ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)_|
|**Implementieren einer Zulassungsliste für Netzwerkdatenverkehr anstelle einer Verweigerungsliste**. Lassen Sie den Zugriff nur für unbedenklichen Netzwerkdatenverkehr zu (der also identifiziert, authentifiziert und autorisiert wurde), anstatt den Zugriff für als schädlich bekannten Netzwerkdatenverkehr zu verweigern (z. B. per Blockade einer bestimmten Adresse oder eines Diensts). Bei Verwendung einer Liste akzeptierter Absender ergibt sich eine bessere Sicherheitsrichtlinie als bei einer Sperrliste, und potenzielle Netzwerkangriffe können von Organisationen erheblich besser erkannt und bewertet werden. |_ACSC Protect: Implementing Network Segmentation and Segregation (ACSC Protect: Implementieren der Netzwerksegmentierung und -trennung)_|
|

Dieser Artikel enthält Informationen und Empfehlungen dazu, wie diese Anforderungen für in Azure bereitgestellte Systeme per Infrastructure-as-a-Service (IaaS) und Platform-as-a-Service (PaaS) erfüllt werden können. Es ist auch ratsam, den Artikel [Steuern von ausgehendem Datenverkehr für Azure Australien](gateway-egress-traffic.md) zu lesen, um sich vollständig mit der Steuerung von Netzwerkdatenverkehr in Azure vertraut zu machen.

## <a name="architecture"></a>Aufbau

Wenn Sie am Entwurf oder der Implementierung von Kontrollen in Bezug auf die Netzwerksicherheit und eingehenden Datenverkehr beteiligt sind, müssen Sie als Erstes ein Verständnis entwickeln, wie eingehender Netzwerkdatenverkehr in Azure für IaaS und PaaS funktioniert. Dieser Abschnitt enthält eine Übersicht über die möglichen Einstiegspunkte, über die Netzwerkdatenverkehr eine in Azure gehostete Ressource erreichen kann, und über die Sicherheitskontrollen, die zum Beschränken und Steuern dieses Datenverkehrs verfügbar sind. Diese Komponenten werden in den verbleibenden Abschnitten dieses Leitfadens ausführlich beschrieben.

### <a name="architecture-components"></a>Komponenten der Architektur

In diesem Architekturdiagramm sind die möglichen Pfade angegeben, die für Netzwerkdatenverkehr genutzt werden können, um eine Verbindung mit einem in Azure gehosteten Dienst herzustellen. Diese Komponenten sind je nach Funktion für eingehenden Datenverkehr in die Bereiche Azure, IaaS-Eingang, PaaS-Eingang und Sicherheitskontrolle unterteilt.

![Aufbau](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure-Komponenten

|Komponente | BESCHREIBUNG|
|---|---|
|**DDoS Protection** | Ein DDoS-Angriff (Distributed Denial of Service) hat das Ziel, die Ressourcen einer Anwendung zu verbrauchen, damit diese für berechtigte Benutzer nicht mehr verfügbar ist. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden. Azure verfügt über die Azure Platform automatisch über DDoS Protection sowie über zusätzliche Funktionen zur Abwehr von Angriffen, die für bestimmte Anwendungen aktiviert werden können, um eine präzisere Steuerung zu ermöglichen.|
| **Traffic Manager** | Azure Traffic Manager ist ein Domain Name System-basiertes Lastenausgleichsmodul (DNS) für Datenverkehr, mit dem Datenverkehr optimal auf Dienste in den Azure-Regionen verteilt und gleichzeitig Hochverfügbarkeit und Reaktionsfähigkeit sichergestellt werden kann. Traffic Manager verwendet DNS, um Clientanforderungen auf Grundlage einer Datenverkehrsrouting-Methode und der Integrität der Endpunkte an den optimalen Endpunkt weiterzuleiten.|
| **ExpressRoute** | ExpressRoute ist eine dedizierte Netzwerkverbindung für die Nutzung von Microsoft-Clouddiensten. Sie wird über einen Konnektivitätsanbieter bereitgestellt und verfügt über eine höhere Zuverlässigkeit, höhere Geschwindigkeiten, geringere Latenzen und eine höhere Sicherheit als reguläre Verbindungen über das Internet. Eine ExpressRoute-Leitung stellt die logische Verbindung zwischen der lokalen Infrastruktur und den Microsoft-Clouddiensten über einen Konnektivitätsanbieter dar.|
| **Privates ExpressRoute-Peering** | Das private ExpressRoute-Peering ist eine Verbindung zwischen der lokalen Umgebung und privaten virtuellen Azure-Netzwerken. Privates Peering ermöglicht den Zugriff auf Azure-Dienste, z. B. virtuelle Computer, die in einem virtuellen Netzwerk bereitgestellt werden. Die Ressourcen und virtuellen Netzwerke, auf die per privatem Peering zugegriffen wird, werden als Erweiterung des Kernnetzwerks einer Organisation angesehen. Privates Peering ermöglicht die bidirektionale Konnektivität zwischen dem lokalen Netzwerk und virtuellen Azure-Netzwerken mit privaten IP-Adressen.|
| **ExpressRoute-Microsoft-Peering** | ExpressRoute-Microsoft-Peering ist eine Verbindung zwischen der lokalen Umgebung und Microsoft sowie öffentlichen Azure-Diensten. Dies umfasst die Konnektivität mit Microsoft 365, Dynamics 365 und Azure-PaaS-Diensten. Das Peering wird über öffentliche IP-Adressen eingerichtet, die im Besitz der Organisation oder des Konnektivitätsanbieters sind. Standardmäßig ist der Zugriff auf Dienste per ExpressRoute-Microsoft-Peering nicht möglich, sondern eine Organisation muss sich für die benötigten Dienste registrieren. Mit diesem Prozess wird dann die Konnektivität mit denselben Endpunkten hergestellt, die über das Internet verfügbar sind.|
|

### <a name="iaas-ingress-components"></a>IaaS-Eingangskomponenten

|Komponente | BESCHREIBUNG|
|---|---|
|**Netzwerkschnittstelle** | Eine Netzwerkschnittstelle ist eine Ressource, die in Azure vorhanden ist. Sie wird an einen virtuellen Computer angefügt. Außerdem wird ihr aus dem Subnetz, dem sie zugeordnet ist, eine private IP-Adresse zugewiesen, die nicht über das Internet geroutet werden kann. Diese IP-Adresse wird über den Azure Resource Manager dynamisch oder statisch zugewiesen.|
|**Subnetz** | Ein Subnetz ist ein IP-Adressbereich, der in einem VNET erstellt wird. Mehrere Subnetze können innerhalb eines VNET erstellt werden, um eine Netzwerksegmentierung zu erzielen.|
| **Virtuelles Netzwerk (VNET)** . | Ein VNET ist in Azure eine grundlegende Ressource, die als Plattform und Grenze für die Bereitstellung von Ressourcen und die Aktivierung der Kommunikation dient. Das VNET befindet sich in einer Azure-Region und definiert den IP-Adressraum und die Routinggrenzen für in das VNET integrierte Ressourcen, z. B. virtuelle Computer.|
| **VNet-Peering** | Das VNET-Peering ist eine Azure-Konfigurationsoption, mit der die direkte Kommunikation zwischen zwei VNETs ohne Gateway für virtuelle Netzwerke ermöglicht wird. Nach dem Peering können die beiden VNETs direkt miteinander kommunizieren, und mit weiteren Konfigurationseinstellungen können die Verwendung von Gateways für virtuelle Netzwerke und andere Übertragungsoptionen gesteuert werden.|
| **Öffentliche IP-Adresse** | Eine öffentliche IP-Adresse ist eine Ressource, bei der eine im Besitz von Microsoft befindliche öffentliche IP-Adresse, die über das Internet geroutet werden kann, aus der angegebenen Region für die Verwendung im virtuellen Netzwerk reserviert wird. Sie kann einer bestimmten Netzwerkschnittstelle zugeordnet werden, damit die Ressource über das Internet, ExpressRoute und PaaS-Systeme zugänglich ist.|
| **ExpressRoute-Gateway** | Ein ExpressRoute-Gateway ist ein Objekt in einem virtuellen Netzwerk, mit dem die Konnektivität und das Routing aus dem virtuellen Netzwerk in lokale Netzwerke per privatem Peering über eine ExpressRoute-Leitung ermöglicht wird.|
| **VPN Gateway** | Ein VPN Gateway ist ein Objekt in einem virtuellen Netzwerk, mit dem ein verschlüsselter Tunnel aus einem virtuellen Netzwerk in ein externes Netzwerk bereitgestellt wird. Für den verschlüsselten Tunnel kann der Modus „Site-to-Site“ für die bidirektionale Kommunikation mit einer lokalen Umgebung, einem anderen virtuellen Netzwerk oder einer Cloudumgebung verwendet werden, oder „Point-to-Site“ für die Kommunikation mit einem einzelnen Endpunkt.|
| **PaaS-VNET-Integration** | Viele PaaS-Funktionen können in einem virtuellen Netzwerk bereitgestellt bzw. darin integriert werden. Einige PaaS-Funktionen können vollständig in ein VNET integriert werden, sodass beispielsweise nur über private IP-Adressen darauf zugegriffen werden kann. Andere Funktionen, z. B. Azure Load Balancer und Azure Application Gateway, können über eine externe Schnittstelle mit einer öffentlichen IP-Adresse und eine interne Schnittstelle mit einer privaten IP-Adresse im virtuellen Netzwerk verfügen. In diesem Fall kann Datenverkehr über die PaaS-Funktion in das virtuelle Netzwerk fließen.|
|

### <a name="paas-ingress-components"></a>PaaS-Eingangskomponenten

|Komponente | BESCHREIBUNG|
|---|---|
|**Hostname** | Azure PaaS-Funktionen werden mit einem eindeutigen öffentlichen Hostnamen identifiziert, der zugewiesen wird, nachdem die Ressource erstellt wurde. Dieser Hostname wird dann in einer öffentlichen DNS-Domäne registriert, in der er in eine öffentliche IP-Adresse aufgelöst werden kann.|
|**Öffentliche IP-Adresse** | Sofern sie nicht im Rahmen einer Konfiguration mit VNET-Integration bereitgestellt werden, wird auf Azure PaaS-Funktionen über eine öffentlichen IP-Adresse zugegriffen, die über das Internet geroutet werden kann. Diese Adresse kann bestimmten Ressourcen zugeordnet werden, z. B. einem öffentlichen Load Balancer, oder einer bestimmten Funktion, die über einen gemeinsamen Einstiegspunkt für mehrere Instanzen verfügt, z. B. Storage oder SQL. Auf diese öffentliche IP-Adresse kann über das Internet, ExpressRoute oder öffentliche IaaS-IP-Adressen per Azure-Backbonenetzwerk zugegriffen werden.|
|**Dienstendpunkte** | Dienstendpunkte stellen eine direkte private Verbindung aus einem virtuellen Netzwerk mit einer bestimmten PaaS-Funktion dar. Dienstendpunkte, die nur für einen Teil der PaaS-Funktionen verfügbar sind, ermöglichen eine bessere Leistung und Sicherheit für Ressourcen in einem VNET mit Zugriff auf PaaS.|
|

### <a name="security-controls"></a>Sicherheitskontrollen

|Komponente | BESCHREIBUNG|
|---|---|
|**Netzwerksicherheitsgruppen (NSGs)** | Mit NSGs wird der Datenverkehr gesteuert, der in Azure in und aus VNET-Ressourcen fließt. NSGs wenden Regeln für die Datenverkehrsflüsse an, die darüber zugelassen oder verweigert werden. Dies umfasst auch Datenverkehr in Azure und zwischen Azure und externen Netzwerken, z. B. lokal oder im Internet. NSGs werden auf Subnetze in einem virtuellen Netzwerk oder auf einzelne Netzwerkschnittstellen angewendet.|
|**PaaS-Firewall** | Viele PaaS-Funktionen, z. B. Storage und SQL, verfügen über eine integrierte Firewall zum Steuern des eingehenden Netzwerkdatenverkehrs für die spezifische Ressource. Sie können Regeln erstellen, um Verbindungen von bestimmten IP-Adressen bzw. virtuellen Netzwerken zuzulassen oder zu verweigern.|
|**PaaS-Authentifizierung und Zugriffssteuerung** | Im Rahmen eines mehrstufigen Sicherheitsansatzes werden mit PaaS-Funktionen mehrere Mechanismen zum Authentifizieren von Benutzern und Kontrollieren der Rechte und des Zugriffs bereitgestellt.|
|**Azure Policy** | Azure Policy ist ein Dienst zum Erstellen, Zuweisen und Verwalten von Richtlinien in Azure. Für diese Richtlinien werden Regeln zum Steuern der Arten von Ressourcen, die bereitgestellt werden können, und der Konfiguration dieser Ressourcen verwendet. Richtlinien können zur Erzwingung von Konformität eingesetzt werden, indem die Bereitstellung von Ressourcen verhindert wird, falls sie die Anforderungen nicht erfüllen, oder zur Überwachung, um den Konformitätsstatus melden zu können.|
|

## <a name="general-guidance"></a>Allgemeine Hinweise

Für das Entwerfen und Erstellen sicherer Lösungen in Azure ist es wichtig, dass Sie mit dem Netzwerkdatenverkehr vertraut sind und ihn kontrollieren, damit nur die identifizierte und autorisierte Kommunikation möglich ist. In dieser Anleitung und den Anleitungen zu den einzelnen Komponenten in den Abschnitten weiter unten werden die Tools und Dienste beschrieben, die zum Anwenden der Prinzipien unter _ACSC Protect: Implementing Network Segmentation and Segregation_ über Azure-Workloads hinweg genutzt werden können. Es wird auch beschrieben, wie Sie eine virtuelle Architektur zum Schützen von Ressourcen erstellen, wenn nicht die gleichen herkömmlichen physischen und Netzwerkkontrollen angewendet werden können, die in einer lokalen Umgebung möglich sind.

### <a name="specific-focus-areas"></a>Bestimmte Schwerpunktbereiche

* Begrenzen der Anzahl von Einstiegspunkten für virtuelle Netzwerke
* Begrenzen der Anzahl von öffentlichen IP-Adressen
* Verwenden eines Hub-and-Spoke-Netzwerkentwurfs für virtuelle Netzwerke (wie in der Microsoft VDC-Dokumentation (Virtual Data Center) beschrieben)
* Verwenden von Produkten mit integrierten Sicherheitsfunktionen für eingehende Verbindungen aus dem Internet (z. B. Application Gateway, API-Gateway, virtuelle Netzwerkgeräte)
* Beschränken von Kommunikationsflüssen an PaaS-Funktionen ausschließlich auf Datenflüsse, die für den Betrieb des Systems erforderlich sind
* Bereitstellen von PaaS in einer integrierten VNET-Konfiguration zur Erzielung einer besseren Trennung und Kontrolle
* Konfigurieren von Systemen für die Nutzung von Verschlüsselungsmechanismen gemäß dem Verbraucherleitfaden und ISM des ACSC
* Verwenden von identitätsbasierten Schutzmaßnahmen, z. B. Authentifizierung und rollenbasierte Zugriffssteuerung von Azure zusätzlich zu herkömmlichen Netzwerksteuerungen
* Implementieren einer ExpressRoute-Leitung für die Konnektivität mit lokalen Netzwerken
* Implementieren von VPNs für administrativen Datenverkehr und Integration in externe Netzwerke
* Nutzen von Azure Policy zum Beschränken auf die Regionen und Ressourcen, die für die Funktionalität des Systems benötigt werden
* Utilize Azure Policy to enforce baseline security configuration for internet-accessible resources

### <a name="additional-resources"></a>Zusätzliche Ressourcen

|Ressource | Link|
|---|---|
|Dokumente zur Konformität mit Bestimmungen und Richtlinien für Australien mit Verbraucherleitfaden|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Virtuelles Azure-Rechenzentrum|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC-Netzwerksegmentierung|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|ACSC-Cloudsicherheit für Mandanten| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC Information Security Manual (ACSC-Handbuch zur Informationssicherheit)|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Leitfaden zu den Komponenten

Dieser Abschnitt enthält weitere Informationen zu den einzelnen Komponenten, die für eingehenden Datenverkehr von in Azure bereitgestellten Systemen relevant sind. In jedem Abschnitt wird der Zweck der jeweiligen Komponente beschrieben, und es sind Links zur Dokumentation und zu Konfigurationsanleitungen vorhanden, die als Hilfe beim Entwerfen und Erstellen von Aktivitäten dienen können.

## <a name="azure"></a>Azure

Die gesamte Kommunikation mit Ressourcen in Azure verläuft über die von Microsoft verwaltete Netzwerkinfrastruktur, über die Konnektivität und Sicherheitsfunktionen bereitgestellt werden. Von Microsoft werden automatisch einige Schutzmaßnahmen getroffen, um die Azure Platform und die Netzwerkinfrastruktur zu schützen. Weitere Funktionen sind in Azure als Dienste verfügbar, mit denen der Netzwerkdatenverkehr kontrolliert und die Netzwerksegmentierung und -trennung ermöglicht werden kann.

### <a name="ddos-protection"></a>DDoS Protection

Über das Internet zugängliche Ressourcen sind anfällig für DDoS-Angriffe. Als Schutz vor diesen Angriffen stellt Azure DDoS-Schutz auf den Ebenen „Basic“ und „Standard“ bereit.

„Basic“ ist im Rahmen der Azure-Plattform automatisch aktiviert und verfügt über Always On-Datenverkehrsüberwachung und Echtzeit-Risikominderung für häufige Angriffe auf Netzwerkebene. Dies sind die gleichen Schutzmaßnahmen wie bei den Onlinediensten von Microsoft.  Das gesamte weltweite Netzwerk von Azure steht für die Verteilung und Abwehr des regionsübergreifenden Angriffsdatenverkehrs zur Verfügung. Schutz wird für öffentliche Azure-IP-Adressen mit IPv4 und IPv6 bereitgestellt.

Bei „Standard“ werden zusätzliche Funktionen zur Angriffsabwehr über die Dienstebene „Basic“ bereitgestellt, die speziell für virtuelle Azure-Netzwerkressourcen optimiert sind. Schutzrichtlinien werden über dedizierte Datenverkehrsüberwachung und Machine Learning-Algorithmen optimiert. Der Schutz wird für öffentliche Azure-IPv4-Adressen bereitgestellt.

|Resource|Link|
|---|---|
|Azure DDoS Protection: Übersicht|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](../ddos-protection/ddos-protection-overview.md)|
|Azure DDoS: Bewährte Methoden|[https://docs.microsoft.com/azure/ddos-protection/fundamental-best-practices](../ddos-protection/fundamental-best-practices.md)|
|Verwalten von DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager wird zum Verwalten von eingehendem Datenverkehr verwendet, indem gesteuert wird, welche Endpunkte einer Anwendung Verbindungen erhalten. Als Schutz vor einem Verlust der Verfügbarkeit für Systeme oder Anwendungen aufgrund von Cybersicherheitsangriffen oder zur Wiederherstellung von Diensten nach der Kompromittierung eines Systems kann Traffic Manager genutzt werden, um Datenverkehr an funktionierende verfügbare Anwendungsinstanzen umzuleiten.

|Resource|Link|
|---|---|
|Traffic Manager – Übersicht | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](../traffic-manager/traffic-manager-overview.md)|
|Leitfaden: Notfallwiederherstellung mit Azure DNS und Traffic Manager | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](../networking/disaster-recovery-dns-traffic-manager.md)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute kann verwendet werden, um einen privaten Pfad einzurichten, der aus einer lokalen Umgebung zu in Azure gehosteten Systemen führt. Mit dieser Verbindung kann eine höhere Zuverlässigkeit und garantierte Leistung mit verbessertem Datenschutz für die Netzwerkkommunikation erzielt werden. ExpressRoute ermöglicht Commonwealth-Entitäten die Kontrolle von eingehendem Datenverkehr aus der lokalen Umgebung und die Definition von spezifischen dedizierten Adressen für die Organisation, die für Firewallregeln für eingehenden Datenverkehr und Zugriffssteuerungslisten verwendet werden können.

|Resource | Link|
|---|---|
|ExpressRoute-Übersicht | [https://docs.microsoft.com/azure/expressroute/](../expressroute/index.yml)|
|ExpressRoute-Konnektivitätsmodelle | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](../expressroute/expressroute-connectivity-models.md)|
|

### <a name="expressroute-private-peering"></a>Privates ExpressRoute-Peering

Privates Peering verfügt über einen Mechanismus zum Erweitern einer lokalen Umgebung in Azure, indem nur private IP-Adressen verwendet werden. Hiermit können Commonwealth-Entitäten virtuelle Azure-Netzwerke und Adressenbereiche in vorhandene lokale Systeme und Dienste integrieren. Beim privaten Peering verfügen Sie über die Sicherheit, dass die ExpressRoute-Kommunikation nur über virtuelle Netzwerke verläuft, die von der Organisation autorisiert sind. Bei Verwendung des privaten Peerings müssen für Commonwealth-Entitäten virtuelle Netzwerkgeräte (Network Virtual Appliances, NVA) anstelle einer Azure VPN Gateway-Instanz implementiert werden, um die sichere VPN-Kommunikation mit Ihren lokalen Netzwerken gemäß ACSC-Verbraucherleitfaden einzurichten.

|Resource | Link|
|---|---|
|Privates ExpressRoute-Peering: Übersicht | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|Privates ExpressRoute-Peering: Schrittanleitung | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](../expressroute/expressroute-howto-routing-portal-resource-manager.md#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute-Microsoft-Peering

Beim Microsoft-Peering wird eine Hochgeschwindigkeitsverbindung mit niedriger Latenz für öffentliche Microsoft-Dienste bereitgestellt, ohne dass das Internet durchlaufen werden muss. Hiermit wird für Verbindungen eine höhere Zuverlässigkeit und Leistung und ein besserer Datenschutz erzielt. Mit Routenfiltern können Commonwealth-Entitäten die Kommunikation auf die Azure-Regionen beschränken, für die dies erforderlich ist. Dies umfasst aber auch Dienste, die von anderen Organisationen gehostet werden, und es kann sein, dass zusätzliche Filter- oder Untersuchungsfunktionen zwischen der lokalen Umgebung und Microsoft benötigt werden.

Commonwealth-Entitäten können die dedizierten öffentlichen IP-Adressen nutzen, die über die Peeringbeziehung eingerichtet werden, um die lokale Umgebung, die in Firewalls und Zugriffssteuerungslisten innerhalb von PaaS-Funktionen verwendet wird, eindeutig zu identifizieren.

Alternativ können Commonwealth-Entitäten das ExpressRoute-Microsoft-Peering als zugrunde liegendes Netzwerk für die Herstellung der VPN-Konnektivität per Azure VPN Gateway nutzen. Bei diesem Modell findet keine aktive Kommunikation vom internen lokalen Netzwerk zu öffentlichen Azure-Diensten per ExpressRoute statt, aber die sichere Konnektivität über private virtuelle Netzwerke wird gemäß den Vorgaben des ACSC-Verbraucherleitfadens erzielt.

|Resource | Link|
|---|---|
|ExpressRoute-Microsoft-Peering: Übersicht | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|ExpressRoute-Microsoft-Peering: Schrittanleitung | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](../expressroute/expressroute-howto-routing-portal-resource-manager.md#msft)|
|

## <a name="iaas-ingress"></a>IaaS-Eingang

Dieser Abschnitt enthält eine Anleitung zum Steuern des eingehenden Datenverkehrs für IaaS-Komponenten. IaaS verfügt über virtuelle Computer und andere Computeressourcen, die in einem virtuellen Netzwerk in Azure bereitgestellt und verwaltet werden können. Damit Datenverkehr von Systemen empfangen werden kann, die per IaaS bereitgestellt werden, muss ein Einstiegspunkt für das virtuelle Netzwerk vorhanden sein. Dies kann über eine öffentliche IP-Adresse, ein Gateway für virtuelle Netzwerke oder eine VNET-Peeringbeziehung eingerichtet werden.

### <a name="network-interface"></a>Netzwerkschnittstelle

Netzwerkschnittstellen sind die Eingangspunkte für den gesamten Datenverkehr, der an einen virtuellen Computer fließt. Sie ermöglichen die Konfiguration von IP-Adressen und können genutzt werden, um Netzwerksicherheitsgruppen anzuwenden oder Datenverkehr über ein virtuelles Netzwerkgerät zu leiten. Die Netzwerkschnittstellen für virtuelle Computer sollten so geplant und konfiguriert werden, dass sie an allgemeinen Zielen für die Netzwerksegmentierung und -trennung ausgerichtet sind.

|Resource | Link|
|---|---|
|Erstellen, Ändern oder Löschen von Netzwerkschnittstellen | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md)|
|IP-Adressierung für Netzwerkschnittstellen | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/ip-services/private-ip-addresses.md)|
|

### <a name="subnet"></a>Subnet

Subnetze sind für die Netzwerksegmentierung und -trennung in Azure eine wichtige Komponente. Subnetze können analog verwendet werden, um die Trennung zwischen Systemen zu ermöglichen. Netzwerksicherheitsgruppen können auf Subnetze angewendet werden, um die eingehenden Kommunikationsflüsse auf die Datenströme zu beschränken, die für den Systembetrieb benötigt werden. Subnetze können sowohl als Quell- als auch als Zieladressen für Firewallregeln und Zugriffssteuerungslisten verwendet und für Dienstendpunkte konfiguriert werden, um Konnektivität für PaaS-Funktionen bereitzustellen.

|Resource | Link|
|---|---|
|Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)|
|

### <a name="virtual-network-vnet"></a>Virtual Network (VNet)

VNETs sind einer der grundlegenden Bausteine für Netzwerke in Azure. Mit virtuellen Netzwerken werden ein IP-Adressraum und eine Routinggrenze definiert, die in verschiedenen Systemen genutzt werden können. Virtuelle Netzwerke sind in Subnetze unterteilt, und alle Subnetze eines virtuellen Netzwerks verfügen untereinander über eine gemeinsame direkte Netzwerkroute. Mit Gateways für virtuelle Netzwerke (ExpressRoute oder VPN) können Systeme in einem virtuellen Netzwerk für lokale und externe Umgebungen zugänglich gemacht werden. Die Vertrautheit mit virtuellen Netzwerken und der zugehörigen Konfigurationsparameter und des Routings ist von entscheidender Bedeutung, wenn es darum geht, eingehenden Netzwerkdatenverkehr zu verstehen und zu steuern.

|Resource | Link|
|---|---|
|Übersicht über virtuelle Netzwerke | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md)|
|Planen virtueller Netzwerke: Schrittanleitung | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|
Erstellen eines virtuellen Netzwerks: Schnellstart | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)|
|

### <a name="vnet-peering"></a>VNET-Peering

Das VNET-Peering wird verwendet, um einen direkten Kommunikationspfad zwischen zwei virtuellen Netzwerken zu erhalten. Nachdem das Peering eingerichtet wurde, verfügen Hosts in einem virtuellen Netzwerk über einen direkten Hochgeschwindigkeits-Routingpfad zu Hosts in einem anderen virtuellen Netzwerk. Netzwerksicherheitsgruppen gelten weiterhin für den Datenverkehr. Es können normale und erweiterte Konfigurationsparameter verwendet werden, um zu definieren, ob die Kommunikation über Gateways für virtuelle Netzwerke oder aus anderen externen Systemen zulässig ist.

|Resource | Link|
|---|---|
|VNET-Peering: Übersicht |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md)|
|Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="public-ip-on-vnet"></a>Öffentliche IP-Adresse im VNET

Öffentliche IP-Adressen werden verwendet, um einen eingehenden Kommunikationspfad für Dienste zu erhalten, die in einem virtuellen Netzwerk bereitgestellt werden. Commonwealth-Entitäten sollten die Zuordnung von öffentlichen IP-Adressen sorgfältig planen und diese nur Ressourcen zuordnen, wenn dies wirklich erforderlich ist. Die allgemeine Vorgehensweise beim Entwerfen besteht darin, dass öffentliche IP-Adressen Ressourcen mit integrierten Sicherheitsfunktionen, z. B. Application Gateway oder virtuelle Netzwerkgeräte, zugeordnet werden, um für ein virtuelles Netzwerk einen sicheren und kontrollierten öffentlichen Einstiegspunkt bereitzustellen.

|Resource | Link|
|---|---|
|Öffentliche IP-Adressen: Übersicht | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses)|
|Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/ip-services/virtual-network-public-ip-address.md)|
|

### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

ExpressRoute-Gateways stellen einen Eingangspunkt aus der lokalen Umgebung dar und sollten bereitgestellt werden, um die Anforderungen in Bezug auf Sicherheit, Verfügbarkeit, Finanzen und Leistung zu erfüllen. ExpressRoute-Gateways verfügen über eine definierte Netzwerkbandbreite, und nach der Bereitstellung fallen Nutzungskosten an. Virtuelle Netzwerke können nur über ein ExpressRoute-Gateway verfügen. Es kann aber mit mehreren ExpressRoute-Leitungen verbunden und von mehreren virtuellen Netzwerken per VNET-Peering verwendet werden, damit Bandbreite und Konnektivität von mehreren virtuellen Netzwerken gemeinsam genutzt werden können. Beim Konfigurieren des Routings zwischen lokalen Umgebungen und virtuellen Netzwerken mit ExpressRoute-Gateways sollte die End-to-End-Konnektivität sichergestellt werden, indem bekannte kontrollierte Netzwerkeingangspunkte verwendet werden. Commonwealth-Entitäten, die ein ExpressRoute-Gateway nutzen, müssen auch virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) bereitstellen, um für VPN-Konnektivität mit der lokalen Umgebung zu sorgen. So können die Vorgaben des ACSC-Verbraucherleitfadens eingehalten werden.

|Resource | Link|
|---|---|
|ExpressRoute-Gateway: Übersicht | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md)|
|Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway stellt einen Netzwerkeingangspunkt aus einem externen Netzwerk dar, um sichere Site-to-Site- oder Point-to-Site-Verbindungen zu ermöglichen. VPN Gateway-Instanzen verfügen über eine definierte Netzwerkbandbreite, und nach der Bereitstellung fallen Nutzungskosten an. Commonwealth-Entitäten, die VPN Gateway nutzen, sollten sicherstellen, dass die Konfiguration gemäß dem ACSC-Verbraucherleitfaden erfolgt. Virtuelle Netzwerke können nur über eine VPN Gateway-Instanz verfügen. Es können aber mehrere Tunnel konfiguriert werden, und die Nutzung durch mehrere virtuelle Netzwerke per VNET-Peering ist möglich, sodass mehrere virtuelle Netzwerke die Bandbreite und Konnektivität gemeinsam nutzen können. VPN Gateway-Instanzen können über das Internet oder ExpressRoute per Microsoft-Peering eingerichtet werden.

|Resource | Link|
|---|---|
|VPN Gateway: Übersicht | [https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|Planung und Entwurf für VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
|VPN Gateway-Konfiguration für australische Behörden|[Erforderliche IPsec-Konfiguration für australische Behörden](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS-VNET-Integration

Mit der Nutzung von PaaS kann eine bessere Funktionalität und Verfügbarkeit erzielt und der Verwaltungsaufwand reduziert werden, aber es müssen angemessene Schutzmaßnahmen getroffen werden. Erzwingen Sie zur Erhöhung der Kontrolle die Netzwerksegmentierung. Es können auch viele PaaS-Funktionen in ein virtuelles Netzwerk integriert werden, um für Anwendungen und Dienste einen sicheren Einstiegspunkt in eingehender Richtung zu schaffen.

Zum Bereitstellen eines sicheren Einstiegspunkts können PaaS-Funktionen, z. B. Application Gateway, mit einer externen öffentlichen Schnittstelle und einer internen privaten Schnittstelle für die Kommunikation mit Anwendungsdiensten konfiguriert werden. So wird verhindert, dass Anwendungsserver mit öffentlichen IP-Adressen konfiguriert und in externen Netzwerken verfügbar gemacht werden müssen.

Für die Integration von PaaS in eine System- oder Anwendungsarchitektur werden von Microsoft mehrere Mechanismen zum Bereitstellen von PaaS in einem virtuellen Netzwerk angeboten. Durch die Bereitstellungsmethodik wird der Zugriff in eingehender Richtung aus externen Netzwerken beschränkt, z. B. aus dem Internet, während die Konnektivität und Integration für interne Systeme und Anwendungen sichergestellt ist. Beispiele hierfür sind App Service-Umgebungen, verwaltete SQL-Instanzen usw.

|Resource | Link|
|---|---|
|Integration virtueller Netzwerke für Azure-Dienste | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md)|
|Integrieren Ihrer App in ein virtuelles Azure-Netzwerk: Schrittanleitung | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/overview-vnet-integration.md)|
|

## <a name="paas-ingress"></a>PaaS-Eingang

PaaS-Funktionen ermöglichen eine bessere Funktionalität und eine vereinfachte Verwaltung, erhöhen aber auch die Komplexität bei der Erfüllung der Anforderungen in Bezug auf die Netzwerksegmentierung und -trennung. PaaS-Funktionen werden normalerweise mit öffentlichen IP-Adressen konfiguriert und sind über das Internet zugänglich.  Beim Erstellen von Systemen mit PaaS-Funktionen sollte mit Bedacht vorgegangen werden, um alle erforderlichen Kommunikationsflüsse zwischen den Komponenten im System und den erstellten Netzwerksicherheitsregeln zu ermitteln, damit nur die gewünschte Kommunikation zulässig ist. Im Rahmen eines tiefgreifenden Sicherheitsansatzes sollten PaaS-Funktionen mit Verschlüsselung, Authentifizierung und geeigneten Zugriffssteuerungen und -berechtigungen konfiguriert werden.  

### <a name="hostname"></a>Hostname

PaaS-Funktionen werden mit Hostnamen eindeutig identifiziert, um zuzulassen, dass mehrere Instanzen desselben Diensts unter derselben öffentlichen IP-Adresse gehostet werden. Eindeutige Hostnamen werden angegeben, wenn Ressourcen erstellt werden und in DNS-Domänen enthalten sind, die sich im Besitz von Microsoft befinden. Die spezifischen Hostnamen für autorisierte Dienste können in Sicherheitstools mit Funktionen für die Filterung auf Anwendungsebene verwendet werden. Bestimmte Dienste können bei Bedarf auch mit benutzerdefinierten Domänen konfiguriert werden.

|Resource | Link|
|---|---|
|Viele öffentliche Namespaces, die von Azure-Diensten verwendet werden, können mit PowerShell abgerufen werden, indem der Befehl „Get-AzureRMEnvironment“ ausgeführt wird. | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](/powershell/module/azurerm.profile/get-azurermenvironment)|
|Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst | App Services und andere Dienste können über benutzerdefinierte Domänen verfügen: [https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](../cloud-services/cloud-services-custom-domain-name-portal.md)|
|

### <a name="public-ip-for-paas"></a>Öffentliche IP-Adresse für PaaS

Öffentliche IP-Adressen für PaaS-Funktionen werden basierend auf der Region zugeordnet, in der der Dienst gehostet oder bereitgestellt wird. Wenn Sie geeignete Netzwerksicherheitsregeln und eine Routingtopologie für die Netzwerksegmentierung und -trennung erstellen möchten, um virtuelle Azure-Netzwerke, PaaS und die ExpressRoute- und Internetkonnektivität abzudecken, müssen Sie mit der Zuordnung von öffentlichen IP-Adressen und den Regionen vertraut sein. Azure ordnet IP-Adressen dem Pool zu, über den jede Azure-Region verfügt. Microsoft stellt die in den Regionen jeweils verwendeten Adressen als Download bereit, der regelmäßig kontrolliert und aktualisiert wird. Die in den einzelnen Regionen verfügbaren Dienste ändern sich zudem häufig, wenn neue Dienste veröffentlicht oder Dienste in größerem Umfang bereitgestellt werden. Commonwealth-Entitäten sollten dieses Material regelmäßig prüfen und können die Automatisierung nutzen, um die Systeme auf dem erforderlichen Stand zu halten. Die bestimmten IP-Adressen für einige Dienste, die in den einzelnen Regionen gehostet werden, erhalten Sie beim Microsoft-Support.

|Resource | Link|
|---|---|
|IP-Bereiche für Microsoft Azure-Rechenzentren | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Azure-Dienste nach Region | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Dienstendpunkte

Über VNET-Dienstendpunkte wird eine private eingehende Hochgeschwindigkeits-Netzwerkverbindung für Subnetze in einem virtuellen Netzwerk bereitgestellt, um bestimmte PaaS-Funktionen nutzen zu können. Für die vollständige Netzwerksegmentierung und -trennung für die PaaS-Funktion muss diese konfiguriert werden, damit nur aus den erforderlichen virtuellen Netzwerken Verbindungen akzeptiert werden. Nicht alle PaaS-Funktionen unterstützen eine Kombination von Firewallregeln, die Dienstendpunkte und herkömmliche Regeln auf Basis von IP-Adressen enthalten. Es sollte also mit Bedacht vorgegangen werden, um den Kommunikationsfluss zu verstehen, der für die Anwendungsfunktionalität und -verwaltung erforderlich ist, damit sich die Implementierung dieser Sicherheitskontrollen nicht auf die Dienstverfügbarkeit auswirkt.

|Resource | Link|
|---|---|
|Übersicht über Dienstendpunkte | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](../virtual-network/virtual-network-service-endpoints-overview.md)
|Lernprogramm |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

## <a name="security"></a>Sicherheit

Die Implementierung von Kontrollen zur Netzwerksegmentierung und -trennung für IaaS- und PaaS-Funktionen wird durch das direkte Schützen der Funktionen und das Implementieren von kontrollierten Kommunikationspfaden aus den Systemen erreicht, die mit der Funktion kommunizieren.

Das Entwerfen und Erstellen von Lösungen in Azure umfasst den Prozess der Erstellung einer logischen Architektur, um Netzwerkressourcen in der gesamten Azure-Umgebung verinnerlichen, steuern und überwachen zu können. Diese logische Architektur ist auf der Azure Platform softwaredefiniert und nimmt den Platz einer physischen Netzwerktopologie ein, die in herkömmlichen Netzwerkumgebungen implementiert wird.

Mit der erstellten logischen Architektur müssen die Funktionen bereitgestellt werden, die für die Nutzung benötigt werden, aber es muss auch für die erforderliche Transparenz und Kontrolle in Bezug auf die Sicherheit und Integrität gesorgt sein.

Die Erreichung dieses Ziels basiert auf der Implementierung der benötigten Tools für die Netzwerksegmentierung und -trennung, aber auch auf dem Schutz und der Erzwingung der Netzwerktopologie und der Implementierung dieser Tools.

Die in diesem Leitfaden enthaltenen Informationen können genutzt werden, um für eingehenden Datenverkehr die Quellen zu identifizieren, die zulässig sein müssen. Außerdem erhalten Sie Informationen zu den Möglichkeiten einer weiteren Kontrolle oder Einschränkung.

### <a name="network-security-groups-nsgs"></a>Netzwerksicherheitsgruppen

Netzwerksicherheitsgruppen werden verwendet, um den ein- und ausgehenden Datenverkehr anzugeben, der für ein Subnetz oder eine bestimmte Netzwerkschnittstelle zulässig sein soll. Beim Konfigurieren von Netzwerksicherheitsgruppen sollten Commonwealth-Entitäten einen Genehmigungslistenansatz verfolgen, bei dem Regeln konfiguriert werden. Hierbei kann der erforderliche Datenverkehr mit einer Standardregel zugelassen werden, während der gesamte Datenverkehr verweigert wird, der nicht einer bestimmten Zulassungsanweisung entspricht. Gehen Sie beim Planen und Konfigurieren von Netzwerksicherheitsgruppen mit Bedacht vor, um sicherzustellen, dass der gesamte erforderliche ein- und ausgehende Datenverkehr richtig erfasst wird. Dies umfasst auch das Identifizieren und Verinnerlichen aller privaten IP-Adressbereiche, die in virtuellen Azure-Netzwerken und der lokalen Umgebung genutzt werden, sowie bestimmte Microsoft-Dienste, z. B. Azure Load Balancer, und PaaS-Verwaltungsanforderungen. Personen, die am Entwurf und der Implementierung von Netzwerksicherheitsgruppen beteiligt sind, sollten auch mit der Nutzung von Diensttags und Anwendungssicherheitsgruppen vertraut sein, um differenzierte, dienstbezogene und anwendungsspezifische Sicherheitsregeln erstellen zu können.

|Resource | Link|
|---|---|
|Übersicht über die Netzwerksicherheit | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)
|Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

## <a name="paas-firewall"></a>PaaS-Firewall

Eine PaaS-Firewall ist eine Funktion für die Steuerung des Netzwerkzugriffs, die auf bestimmte PaaS-Dienste angewendet werden kann. Sie ermöglicht, dass die Filterung von IP-Adressen oder für bestimmte virtuelle Netzwerke so konfiguriert wird, dass eingehender Datenverkehr auf die spezifische PaaS-Instanz beschränkt ist. Für PaaS-Funktionen mit einer Firewall sollten Richtlinien für die Steuerung des Netzwerkzugriffs so konfiguriert werden, dass nur der erforderliche eingehende Datenverkehr basierend auf Anwendungsanforderungen zugelassen wird.  

|Resource | Link|
|---|---|
|Azure SQL-Datenbank- und Azure Synapse Analytics-IP-Firewallregelen | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](../azure-sql/database/firewall-configure.md)|
|Sicherheit des Speichernetzwerks | [https://docs.microsoft.com/azure/storage/common/storage-network-security](../storage/common/storage-network-security.md)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS-Authentifizierung und -Zugriffssteuerung

Je nach PaaS-Funktion und ihrem Zweck ist die Verwendung von Netzwerkkontrollen unter Umständen nicht möglich oder praktikabel. Im Rahmen des mehrstufigen Sicherheitsmodells für PaaS werden von Azure viele verschiedene Mechanismen für die Authentifizierung und Zugriffssteuerung bereitgestellt, um den Zugriff auf einen Dienst auch dann einzuschränken, wenn Netzwerkdatenverkehr zulässig ist. Typische Authentifizierungsmechanismen für PaaS-Funktionen sind Azure Active Directory, Authentifizierung auf Anwendungsebene und gemeinsam verwendete Schlüssel oder Zugriffssignaturen. Nachdem ein Benutzer sicher identifiziert wurde, können mit Rollen die Aktionen gesteuert werden, die vom Benutzer durchgeführt werden können. Diese Tools können als Alternative oder als zusätzliches Mittel zum Beschränken des Zugriffs auf Dienste verwendet werden.

|Resource | Link|
|---|---|
|Steuern und Gewähren des Datenbankzugriffs für SQL-Datenbank und Azure Synapse Analytics | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](../azure-sql/database/logins-create-manage.md)|
|Autorisierung für Azure Storage-Dienste | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy ist eine wichtige Komponente zur Erzwingung und Aufrechterhaltung der Integrität der logischen Architektur einer Azure-Umgebung. Aufgrund der Vielzahl von Diensten und verfügbaren Pfaden für eingehenden Netzwerkdatenverkehr über Azure-Dienste ist es von entscheidender Bedeutung, dass Commonwealth-Entitäten mit den Ressourcen, die in ihrer Umgebung vorhanden sind, und den verfügbaren Eingangspunkten des Netzwerks vertraut sind. Um sicherzustellen, dass in der Azure-Umgebung keine unberechtigten Eingangspunkte des Netzwerks erstellt werden, sollten Commonwealth-Entitäten Azure Policy verwenden, um die bereitstellbaren Ressourcentypen und die Konfiguration dieser Ressourcen zu steuern. Praktische Beispiele sind die Beschränkung auf autorisierte und genehmigte Ressourcen und die Durchsetzung der Regel, dass für Storage eine HTTPS-Verschlüsselung erforderlich ist und Netzwerksicherheitsgruppen Subnetzen hinzugefügt werden müssen.

|Resource | Link|
|---|---|
|Azure Policy: Übersicht | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|Zulässige Ressourcentypen: Beispiel für Richtlinie | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)
|Sicherstellen von HTTPS-Datenverkehr für Speicherkonten: Beispiel für Richtlinie|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](../governance/policy/samples/index.md)_
|Erzwingen der Nutzung eines Subnetzes für Netzwerksicherheitsgruppen: Beispiel für Richtlinie| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Informationen zur Verwaltung des Datenverkehrsflusses aus Ihrer Azure-Umgebung in andere Netzwerke mit Ihren Gatewaykomponenten in Azure finden Sie im Artikel [Steuern von ausgehendem Datenverkehr für Azure Australien](gateway-egress-traffic.md).