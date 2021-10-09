---
title: Remotearbeit mit Azure-Netzwerkdiensten
description: Auf dieser Seite wird beschrieben, wie Sie die für die Remotearbeit verfügbaren Azure-Netzwerkdienste nutzen können. Außerdem erfahren Sie, wie Sie Datenverkehrsprobleme vermeiden, die bei einer steigenden Anzahl von Remotebenutzern auftreten können.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 2086351f1e66cbd2ef41ac98b2ab86fe7275823b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621355"
---
# <a name="working-remotely-using-azure-networking-services"></a>Remotearbeit mit Azure-Netzwerkdiensten

>[!NOTE]
> In diesem Artikel wird beschrieben, wie Sie die Azure-Netzwerkdienste, Microsoft Network und das Azure-Partnerökosystem nutzen können, um remote zu arbeiten und Netzwerkprobleme zu beheben, die Sie aufgrund der COVID-19-Krise bewältigen müssen.

In diesem Artikel werden die verfügbaren Optionen beschrieben, mit denen Organisationen einen Remotezugriff für ihre Benutzer einrichten oder vorhandene Lösungen erweitern können, um zu Spitzenauslastungszeiten zusätzliche Kapazität bereitzustellen. Netzwerkarchitekten müssen die folgenden Herausforderungen bewältigen:

- Reagieren auf eine höhere Netzwerkauslastung.
- Bereitstellen von zuverlässiger, sicherer Konnektivität für eine größere Anzahl von eigenen Mitarbeitern und Mitarbeitern auf Kundenseite.
- Bereitstellen von Konnektivität für Remotestandorte auf der ganzen Welt.

Nicht bei allen Netzwerken (z. B. private WANs und Kernnetzwerke auf Unternehmensebene) tritt aufgrund von Spitzenlasten durch Remotebenutzer eine Überlastung auf. Die Engpässe treten häufig nur in Breitbandnetzwerken im Heimbereich und bei VPN-Gateways lokaler Unternehmensnetzwerke auf.

Bei der Netzwerkplanung lassen sich diese Engpässe und eine Überlastung des Netzwerks vermeiden, indem für unterschiedliche Datenverkehrstypen unterschiedliche Prioritäten im Netzwerk festgelegt werden. Außerdem empfiehlt sich eine intelligente Umleitung/Verteilung der Last. Telemedizin-Datenverkehr, der in Echtzeit zwischen Patienten und Ärzten übertragen wird, hat z. B. eine hohe Wichtigkeit. Folglich sollten Verzögerungen/Jitter bei dieser Art von Datenverkehr vermieden werden. Bei der Replikation dieses Datenverkehrs zwischen Speichern sind Verzögerungen jedoch weniger relevant. Beim Echtzeit-Datenverkehr muss also der beste Netzwerkpfad mit hoher Servicequalität gewählt werden, bei der Replikation kann eine weniger optimale Route verwendet werden.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Best Practices – das Azure-Netzwerk ist optimal für Elastizität und Hochverfügbarkeit ausgelegt

Azure ist so konzipiert, dass problemlos auf plötzliche Änderungen bei der Ressourcenverwendung reagiert werden kann. Dies ist insbesondere in Phasen der Spitzenauslastung nützlich. Darüber hinaus verwaltet und betreibt Microsoft eins der größten Netzwerke der Welt. Um die Auswirkungen von Ausfällen zu minimieren – von Ausfällen einzelner Netzwerkelemente bis hin zum Ausfall einer gesamten Region –, wurde das Microsoft-Netzwerk für Hochverfügbarkeit entwickelt.

Mit dem Microsoft-Netzwerk werden die Anforderungen unterschiedlicher Arten von Netzwerkdatenverkehr erfüllt und eine optimale Leistung bereitgestellt. Dies umfasst u. a. Multimedia-Datenverkehr für Skype und Teams, CDN, Big Data-Analysen in Echtzeit, Azure-Speicher, Bing und Xbox – allesamt Anwendungsfälle, bei denen Verzögerungen vermieden werden müssen. Um eine optimale Leistung für verschiedene Arten von Datenverkehr zu gewährleisten, wird die Übertragung des gesamten Datenverkehrs, der an oder über die Ressourcen des Microsoft-Netzwerks übermittelt werden soll, so nah wie möglich am Ursprung des Datenverkehrs übernommen.

>[!NOTE] 
>Bei Verwendung der unten beschriebenen Azure-Netzwerkfeatures wird dieses Verhalten innerhalb des globalen Microsoft-Netzwerks genutzt, um Kunden eine bestmögliche Netzwerkleistung zu bieten. Dabei wird Datenverkehr so früh wie möglich aus den Netzwerken weggeleitet, in denen es zu Spitzenauslastungszeiten zu einer Überlastung kommen könnte.
>

## <a name="enable-employees-to-work-remotely"></a>Remotearbeit für Mitarbeiter

Das Azure-VPN-Gateway unterstützt sowohl P2S- (Point-to-Site) als auch S2S-VPN-Verbindungen (Site-to-Site). Unter Verwendung des Azure-VPN-Gateways können Sie die Verbindungen Ihrer Mitarbeiter skalieren, um sowohl auf Ihre in Azure bereitgestellten Ressourcen als auch auf Ihre lokalen Ressourcen sicher zuzugreifen. Weitere Informationen finden Sie unter [Remotearbeit mit Azure-VPN-Gateway (Point-to-Site)](../vpn-gateway/work-remotely-support.md). 

Wenn Sie Secure Sockets Tunneling Protocol (SSTP) verwenden, ist die Anzahl von gleichzeitigen Verbindungen auf 128 Verbindungen beschränkt. Um eine größere Anzahl von Verbindungen bereitzustellen, sollten Sie zu OpenVPN oder IKEv2 wechseln. Weitere Informationen finden Sie unter [Wechsel zu OpenVPN oder IKEv2 von SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Für den Zugriff auf Ihre in Azure bereitgestellten Ressourcen können Remoteentwickler anstelle einer VPN-Verbindung die Azure Bastion-Lösung verwenden. So erhalten sie sicheren Shellzugriff (RDP oder SSH), ohne dass die öffentlichen IP-Adressen der VMs benötigt werden, auf die zugegriffen wird. Weitere Informationen finden Sie unter [Remotearbeit mit Azure Bastion](../bastion/work-remotely-support.md).

Zum Aggregieren umfangreicher VPN-Verbindungen, zur Unterstützung von Any-to-Any-Verbindungen zwischen Ressourcen an unterschiedlichen lokalen Standorten weltweit (in unterschiedlichen regionalen virtuellen Hub-and-Spoke-Netzwerken) und zum Optimieren der Nutzung mehrerer Breitbandnetzwerke im Heimbereich kann Azure Virtual WAN verwendet werden. Weitere Informationen finden Sie unter [Azure Virtual WAN und Remotearbeit](../virtual-wan/work-remotely-support.md).

Eine weitere Möglichkeit, um Remotemitarbeiter zu unterstützen, ist die Bereitstellung einer Virtual Desktop Infrastructure (VDI), die in Ihrem virtuellen Azure-Netzwerk gehostet und mit Azure Firewall geschützt wird. Azure Virtual Desktop (AVD) zum Beispiel ist ein Desktop- und Appvirtualisierungsdienst, der in Azure ausgeführt wird. Mit Azure Virtual Desktop können Sie eine skalierbare und flexible Umgebung in Ihrem Azure-Abonnement einrichten, ohne dass Sie zusätzliche Gateway-Server betreiben müssen. Sie sind lediglich für die virtuellen AVD-Maschinen in Ihrem virtuellen Netzwerk verantwortlich. Weitere Informationen finden Sie unter [Unterstützung von Remotearbeit in Azure Firewall](../firewall/remote-work-support.md). 

Azure verfügt zudem über eine Vielzahl von Ökosystempartnern. Auch mit den virtuellen Netzwerkgeräten unserer Partner in Azure lässt sich die VPN-Konnektivität skalieren. Weitere Informationen finden Sie unter [Remotearbeit: Aspekte in Bezug auf virtuelle Netzwerkgeräte für die Remotearbeit](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Erweitern von Mitarbeiterverbindungen für den Zugriff auf global verteilte Ressourcen

Mit den folgenden Azure-Diensten können Sie Mitarbeitern einen Zugriff auf Ihre global verteilten Ressourcen ermöglichen. Dabei können sich Ihre Ressourcen in beliebigen Azure-Regionen, in lokalen Netzwerken oder sogar in anderen öffentlichen oder privaten Clouds befinden. 

- **Peering in virtuellen Azure-Netzwerken**: Wenn Sie Ihre Ressourcen in mehreren Azure-Regionen bereitstellen und/oder die Konnektivität von Remotemitarbeitern mithilfe mehrerer virtueller Netzwerke aggregieren, können Sie mithilfe von Peering in virtuellen Netzwerken Verbindungen zwischen den verschiedenen virtuellen Azure-Netzwerken herstellen. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken][VNet-peer].

- **Azure-VPN-basierte Lösung**: Für Remotemitarbeiter, die über ein P2S- oder S2S-VPN mit Azure verbunden sind, können Sie den Zugriff auf lokale Netzwerke ermöglichen, indem Sie ein S2S-VPN zwischen Ihren lokalen Netzwerken und dem Azure-VPN-Gateway konfigurieren. Weitere Informationen finden Sie unter [Erstellen einer Site-to-Site-Verbindung im Azure-Portal][S2S].

- **ExpressRoute**: Mithilfe des privaten Peerings von ExpressRoute können Sie private Verbindungen zwischen Ihren Azure-Bereitstellungen und Ihrer lokalen Infrastruktur oder Ihrer Infrastruktur in einer Co-Location-Einrichtung herstellen. Darüber hinaus ermöglicht ExpressRoute mittels Microsoft-Peering einen Zugriff auf öffentliche Endpunkte im Microsoft-Netzwerk von Ihrem lokalen Netzwerk aus. ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Sie bieten bei geringerer oder konstanter Latenz sichere Konnektivität, Zuverlässigkeit und einen höheren Durchsatz als herkömmliche Verbindungen über das Internet. Weitere Informationen finden Sie unter [ExpressRoute-Übersicht][ExR]. Durch die Arbeit mit Ihrem vorhandenen Netzwerkanbieter, der bereits Teil unseres [ExpressRoute-Partnerökosystems][ExR-eco] ist, lässt sich der Zeitaufwand für das Herstellen von Verbindungen mit hoher Bandbreite mit dem Microsoft-Netzwerk verringern.  Unter Verwendung von [ExpressRoute Direct][ExR-D] können Sie Ihr lokales Netzwerk direkt mit dem Microsoft-Backbone verbinden. ExpressRoute Direct bietet duale Konnektivität mit 100 oder 10 GBit/s. 

- **Azure Virtual WAN**: Azure Virtual WAN ermöglicht eine nahtlose Interoperabilität zwischen Ihren VPN-Verbindungen und ExpressRoute-Verbindungen. Wie bereits erwähnt, unterstützt Azure Virtual WAN auch Any-to-Any-Verbindungen zwischen Ressourcen an unterschiedlichen lokalen Standorten weltweit, in unterschiedlichen regionalen virtuellen Hub-and-Spoke-Netzwerken

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Skalieren der Kundenkonnektivität auf Front-End-Ressourcen

In Zeiten, in denen eine größere Anzahl von Personen online ist, steigt auf einer Vielzahl von Unternehmenswebsites der durch Kunden verursachte Datenverkehr. Mit Azure Application Gateway lässt sich diese erhöhte Front-End-Arbeitslast verwalten. Weitere Informationen finden Sie unter [Unterstützung von hohem Datenverkehr mit Application Gateway](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft-Unterstützung für Datenverkehr in mehreren Clouds

Microsoft kann globale Konnektivität für Ihre Bereitstellungen in anderen öffentlichen Clouds bereitstellen. Für diese Zwecke können Azure Virtual WAN, ein VPN oder ExpressRoute eingesetzt werden. Um die Konnektivität von Azure auf andere Clouds zu erweitern, können Sie ein S2S-VPN zwischen den beiden Clouds konfigurieren. Die Konnektivität zwischen Azure und anderen öffentlichen Clouds kann auch über ExpressRoute hergestellt werden. Die Oracle-Cloud ist Teil des ExpressRoute-Partnerökosystems. Sie können [eine direkte Verbindung zwischen Azure und Oracle Cloud Infrastructure einrichten][Az-OCI]. Die meisten Dienstanbieter, die Teil des ExpressRoute-Partnerökosystems sind, bieten zudem private Verbindungen mit anderen öffentlichen Clouds. Wenn Sie mit diesen Dienstanbietern arbeiten, können Sie private Verbindungen zwischen Ihren Bereitstellungen in Azure und anderen Clouds über ExpressRoute herstellen.

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln wird erläutert, wie verschiedene Azure-Netzwerkfeatures für die Skalierung eingesetzt werden können, damit Benutzer remote arbeiten können:

| **Artikel** | **Beschreibung** |
| --- | --- |
| [Remotearbeit mit Azure-VPN-Gateway (Point-to-Site)](../vpn-gateway/work-remotely-support.md) | Überprüfen Sie die verfügbaren Optionen zur Einrichtung des Remotezugriffs für Benutzer oder zur Ergänzung ihrer vorhandenen Lösungen durch zusätzliche Kapazität für Ihre Organisation.|
| [Azure Virtual WAN und Remotearbeit](../virtual-wan/work-remotely-support.md) | Verwenden Sie Azure Virtual WAN, um die Anforderungen Ihrer Organisation an die Remotekonnektivität zu erfüllen.|
| [Unterstützung von hohem Datenverkehr mit Application Gateway](../application-gateway/high-traffic-support.md) | Verwenden Sie Application Gateway mit der Web Application Firewall (WAF) für eine skalierbare und sichere Methode zum Verwalten von Datenverkehr an Ihre Webanwendungen. |
| [Remotearbeit: Aspekte in Bezug auf virtuelle Netzwerkgeräte für die Remotearbeit](../vpn-gateway/nva-work-remotely-support.md)|Überprüfen Sie den Leitfaden zur Nutzung von virtuellen Netzwerkgeräten in Azure zur Bereitstellung von Lösungen für den Remotezugriff. |
| [Wechsel zu OpenVPN oder IKEv2 von SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md) | Überschreiten Sie den Grenzwert von 128 gleichzeitigen Verbindungen von SSTP durch den Übergang zum OpenVPN-Protokoll oder IKEv2.|
| [Remotearbeit mit Azure Bastion](../bastion/work-remotely-support.md) | Stellen Sie sichere und problemlose RDP/SSH-Konnektivität zu virtuellen Computern innerhalb des virtuellen Azure-Netzwerks bereit, direkt im Azure-Portal, ohne die Verwendung einer öffentlichen IP-Adresse. |
| [Verwenden von Azure ExpressRoute, um Hybridkonnektivität für Remotebenutzer bereitzustellen](../expressroute/work-remotely-support.md) | Verwenden Sie ExpressRoute für Hybridkonnektivität, um Benutzern in Ihrer Organisation die Remotearbeit zu ermöglichen.|
| [Unterstützung von Remotearbeit in Azure Firewall](../firewall/remote-work-support.md)|Schützen Sie Ihre virtuellen Azure-Netzwerkressourcen mit Azure Firewall. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md
