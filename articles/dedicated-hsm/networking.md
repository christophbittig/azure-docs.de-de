---
title: Netzwerkaspekte – Azure Dedicated HSM | Microsoft-Dokumentation
description: Übersicht über Netzwerkaspekte für Azure Dedicated HSM-Bereitstellungen
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cc38ad6ee6dfc958be405db2969bd5c083e1c5c0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122343155"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure-Dienst für dedizierte HSMs – Netzwerke

Der Azure-Dienst für dedizierte HSMs erfordert eine äußerst sichere Netzwerkumgebung. Dies gilt für den Weg von der Azure-Cloud zurück in die IT-Umgebung des Kunden (lokal) mithilfe von verteilten Anwendungen ebenso wie für Szenarien mit Hochverfügbarkeit. Azure-Netzwerke ermöglichen dies, und vier verschiedene Bereiche müssen berücksichtigt werden.

- Erstellen von HSM-Geräten in Ihrem virtuellen Netzwerk (VNET) in Azure
- Herstellen einer Verbindung zwischen lokalen und cloudbasierten Ressourcen für die Konfiguration und Verwaltung von HSM-Geräten
- Erstellen und Verbinden virtueller Netzwerke, um Anwendungsressourcen und HSM-Geräte miteinander zu verbinden
- Regionen übergreifendes Verbinden virtueller Netzwerke zur Kommunikation sowie zum Aktivieren von Hochverfügbarkeitsszenarien

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuelles Netzwerk für Ihre dedizierten HSMs

Dedizierte HSMs sind in einem virtuellen Netzwerk integriert und im eigenen privaten Netzwerk des Kunden in Azure platziert. Dies ermöglicht den Zugriff auf die Geräte von virtuellen Computern oder Computeressourcen im virtuellen Netzwerk aus.  
Weitere Informationen zum Integrieren von Azure-Diensten in das virtuelle Netzwerk und die Funktionen, die es bietet, finden Sie in der Dokumentation [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).

### <a name="virtual-networks"></a>Virtuelle Netzwerke

Vor der Bereitstellung eines dedizierten HSM-Geräts müssen Kunden zunächst ein virtuelles Netzwerk in Azure erstellen oder eines verwenden, das bereits im Abonnement des Kunden vorhanden ist. Das virtuelle Netzwerk definiert den Sicherheitsbereich für das dedizierte HSM-Gerät. Weitere Informationen zum Erstellen virtueller Netzwerke finden Sie in der [Dokumentation zu virtuellen Netzwerken](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Subnetze

Subnetze unterteilen das virtuelle Netzwerk in getrennte Adressräume, die von den Azure-Ressourcen, die Sie darin platzieren, verwendet werden können. Dedizierte HSMs werden in einem Subnetz des virtuellen Netzwerks bereitgestellt. Jedes dedizierte HSM-Gerät, das im Subnetz des Kunden bereitgestellt wird, erhält eine private IP-Adresse aus diesem Subnetz. Das Subnetz, in dem das HSM-Gerät bereitgestellt wird, muss explizit an den Dienst delegiert werden: Microsoft.HardwareSecurityModules/dedicatedHSMs. Dadurch werden bestimmte Berechtigungen für den HSM-Dienst für die Bereitstellung im Subnetz gewährt. Die Delegierung an dedizierte HSMs erzwingt bestimmte Richtlinieneinschränkungen im Subnetz. Netzwerksicherheitsgruppen (NSGs) und benutzerdefinierte Routen (User-Defined Routes, UDRs) werden in delegierten Subnetzen derzeit nicht unterstützt. Daher kann ein Subnetz, sobald es an dedizierte HSMs delegiert wird, nur zum Bereitstellen von HSM-Ressourcen verwendet werden. Bei der Bereitstellung anderer Kundenressourcen im Subnetz tritt ein Fehler auf.  Es gibt keine Anforderung, wie groß oder klein das Subnetz für Dedicated HSM sein sollte, jedoch nutzt jedes HSM-Gerät eine private IP-Adresse, sodass sichergestellt werden sollte, dass das Subnetz groß genug ist, um so viele HSM-Geräte aufzunehmen, wie für die Bereitstellung erforderlich sind.

### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

Eine Voraussetzung der aktuellen Architektur ist die Konfiguration eines [ExpressRoute-Gateways im Kundensubnetz](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md), wo ein HSM-Gerät platziert werden muss, um die Integration des HSM-Geräts in Azure zu ermöglichen. Dieses [ExpressRoute-Gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) kann nicht für die Verbindung zwischen lokalen Standorten und Kunden-HSM-Geräten in Azure genutzt werden.

## <a name="connecting-your-on-premises-it-to-azure"></a>Herstellen der Verbindung Ihrer lokalen IT mit Azure

Wenn Sie cloudbasierte Ressourcen erstellen, ist eine private Verbindung zurück zu lokalen IT-Ressourcen eine typische Anforderung. Im Fall von Dedicated HSM geht es dabei hauptsächlich um die HSM-Clientsoftware zur Konfiguration der HSM-Geräte sowie um Aktivitäten wie z. B. Sicherungen und das Entnehmen von Protokollen aus HSMs für die Analyse. Eine wichtige Entscheidung ist hier die Art der Verbindung, da Optionen zur Verfügung stehen.  Die flexibelste Option ist Site-to-Site-VPN, da wahrscheinlich mehrere lokale Ressourcen die sichere Kommunikation mit Ressourcen (einschließlich HSMs) in der Azure-Cloud benötigen. Dies erfordert, dass eine Kundenorganisation über ein VPN-Gerät verfügt, um die Verbindung zu erleichtern. Eine Point-to-Site-VPN-Verbindung kann verwendet werden, wenn es nur einen einzigen lokalen Endpunkt wie z.B. eine einzige Arbeitsstation für die Verwaltung gibt.
Weitere Informationen zu Konnektivitätsoptionen finden Sie unter [Planungsoptionen für VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Zu diesem Zeitpunkt ist ExpressRoute keine Option für die Verbindung mit lokalen Ressourcen. Es ist auch zu beachten, dass das ExpressRoute-Gateway bei Verwendung wie oben beschrieben nicht für Verbindungen mit einer lokalen Infrastruktur geeignet ist.

### <a name="point-to-site-vpn"></a>Punkt-zu-Standort-VPN

Ein Point-to-Site-VPN ist die einfachste Form einer sicheren Verbindung mit einem einzelnen lokalen Endpunkt. Dies ist möglicherweise relevant, wenn Sie beabsichtigen, nur eine einzige Arbeitsstation für die Verwaltung dedizierter HSMs auf Azure-Basis einzusetzen.

### <a name="site-to-site-vpn"></a>Site-to-Site-VPN-Verbindung

Ein virtuelles Site-to-Site-VPN ermöglicht die sichere Kommunikation zwischen dedizierten HSMs auf Azure-Basis und Ihrer lokalen IT. Ein Grund dafür ist eine Sicherungsfunktion für die lokalen HSMs und die Notwendigkeit einer Verbindung zwischen den beiden für die Ausführung der Sicherung.

## <a name="connecting-virtual-networks"></a>Herstellen einer Verbindung zwischen virtuellen Netzwerken

Eine typische Bereitstellungsarchitektur für das dedizierte HSM beginnt mit einem einzelnen virtuellen Netzwerk und einem entsprechenden Subnetz, wo die HSM-Geräte erstellt und bereitgestellt werden. In der gleichen Region könnten sich zusätzliche virtuelle Netzwerke und Subnetze für Anwendungskomponenten befinden, die das dedizierte HSM nutzen. Um die Kommunikation zwischen diesen Netzwerken zu ermöglichen, verwenden wir das Peering in virtuellen Netzwerken.

### <a name="virtual-network-peering"></a>Peering in virtuellen Netzwerken

Wenn mehrere virtuelle Netzwerke innerhalb einer Region gegenseitig auf die Ressourcen der anderen zugreifen müssen, kann Peering in virtuellen Netzwerken zum Erstellen sicherer Kommunikationskanäle zwischen ihnen verwendet werden.  Das Peering virtueller Netzwerke ermöglicht nicht nur sichere Kommunikation, sondern stellt auch Verbindungen mit geringer Latenz und hoher Bandbreite zwischen den Ressourcen in Azure sicher.

![Netzwerkpeering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Herstellen Azure-Regionen übergreifender Verbindungen

Die HSM-Geräte haben die Möglichkeit, über Softwarebibliotheken Datenverkehr an ein alternatives HSM umzuleiten. Die Umleitung des Datenverkehrs empfiehlt sich, wenn Geräte ausfallen oder der Zugriff auf ein Gerät ausfällt. Ausfallszenarien auf regionaler Ebene können verringert werden, indem HSMs in anderen Regionen bereitgestellt und die Regionen übergreifende Kommunikation zwischen virtuellen Netzwerken aktiviert wird.

### <a name="cross-region-ha-using-vpn-gateway"></a>Regionen übergreifende HOCHVERFÜGBARKEIT mit VPN Gateway

Für global verteilte Anwendungen oder regionale Failoverszenarien mit hoher Verfügbarkeit müssen virtuelle Netzwerke Regionen übergreifend verbunden werden. Mit dem Azure-Dienst für dedizierte HSMs kann Hochverfügbarkeit mit VPN Gateway erreicht werden, das einen sicheren Tunnel zwischen den beiden virtuellen Netzwerken bereitstellt. Weitere Informationen zu VNET-to-VNET-Verbindungen mit VPN Gateway finden Sie im Artikel [Was ist VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Globales VNET-Peering ist in regionsübergreifenden Konnektivitätsszenarien mit dedizierten HSMs zu diesem Zeitpunkt nicht verfügbar, und VPN Gateway sollte stattdessen verwendet werden. 

![Das Diagramm zeigt zwei Regionen, die durch zwei VPN-Gateways verbunden sind. Jede Region enthält virtuelle Netzwerke mit Peering.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Netzwerkeinschränkungen
> [!NOTE]
> Eine Einschränkung des Dedicated HSM-Diensts bei Verwendung der Subnetzdelegierung besteht in auferlegten Einschränkungen, die beim Entwerfen der Zielnetzwerkarchitektur für eine HSM-Bereitstellung berücksichtigt werden sollten. Die Verwendung der Subnetzdelegierung bedeutet, dass Netzwerksicherheitsgruppen, benutzerdefinierte Routen (UDRs) und globales VNET-Peering für Dedicated HSM nicht unterstützt werden. In den folgenden Abschnitten finden Sie Hilfe zu alternativen Methoden, um dasselbe oder ein ähnliches Ergebnis für diese Funktionen zu erzielen. 

Die HSM-NIC, die sich im VNet des Dedicated HSM befindet, kann keine Netzwerksicherheitsgruppen oder benutzerdefinierte Routen verwenden. Dies bedeutet, dass es nicht möglich ist, Standardverweigerungsrichtlinien aus Sicht des Dedicated HSM-VNets festzulegen, und dass andere Netzwerksegmente in Zulassungslisten aufgenommen werden müssen, um Zugriff auf den Dedicated HSM-Dienst zu erhalten. 

Durch das Hinzufügen der Proxylösung für virtuelle Netzwerkgeräte (Network Virtual Appliances, NVA) kann auch eine NVA-Firewall im Transit-/DMZ-Hub logisch vor der HSM-NIC platziert werden und so die erforderliche Alternative zu Netzwerksicherheitsgruppen und benutzerdefinierten Routen bereitstellen.

### <a name="solution-architecture"></a>Lösungsarchitektur
Dieser Netzwerkentwurf erfordert die folgenden Elemente:
1.  Ein Transit- oder DMZ-Hub-VNet mit einer NVA-Proxyebene. Im Idealfall sind zwei oder mehr virtuelle Netzwerkgeräte (NVAs) vorhanden. 
2.  Eine ExpressRoute-Verbindung mit aktiviertem privatem Peering und eine Verbindung mit dem Transit-Hub-VNet.
3.  VNet-Peering zwischen dem Transit-Hub-VNet und dem Dedicated HSM-VNet.
4.  Eine NVA-Firewall oder Azure Firewall kann bereitgestellt werden, um DMZ-Dienste im Hub als Option anzubieten. 
5.  Zusätzliche Workload-Spoke-VNets können mit dem Hub-VNet gepeert werden. Der Gemalto-Client kann über das Hub-VNet auf den Dedicated HSM-Dienst zugreifen.

![Das Diagramm zeigt ein DMZ-Hub-VNet mit einer NVA-Proxyebene für die Umgehung von Netzwerksicherheitsgruppen und benutzerdefinierten Routen.](media/networking/network-architecture.png)

Durch das Hinzufügen der Proxylösung für virtuelle Netzwerkgeräte (Network Virtual Appliances, NVA) kann auch eine NVA-Firewall im Transit-/DMZ-Hub logisch vor der HSM-NIC platziert werden und so die erforderlichen Standardverweigerungsrichtlinien bereitstellen. In unserem Beispiel verwenden wir zu diesem Zweck die Azure Firewall und benötigen die folgenden Elemente:
1. Eine Azure Firewall, die im Subnetz „AzureFirewallSubnet“ im DMZ-Hub-VNet bereitgestellt ist.
2. Eine Routingtabelle mit einer benutzerdefinierten Route, die an den privaten Endpunkt des Azure ILB gerichteten Datenverkehr an die Azure Firewall weiterleitet. Diese Routingtabelle wird auf das GatewaySubnet angewendet, in dem sich das [virtuelle ExpressRoute-Gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) befindet.
3. Netzwerksicherheitsregeln in der Azure Firewall, um die Weiterleitung zwischen einem vertrauenswürdigen Quellbereich und dem privaten Endpunkt des Azure ILB, der an TCP-Port 1792 lauscht, zuzulassen. Mit dieser Sicherheitslogik wird die erforderliche „Standardverweigerungs“richtlinie für den Dedicated HSM-Dienst hinzugefügt. Dies bedeutet, dass nur vertrauenswürdige Quell-IP-Adressbereiche in den Dedicated HSM-Dienst zugelassen werden. Alle anderen Bereiche werden getrennt.  
4. Eine Routingtabelle mit einer benutzerdefinierten Route, die an den lokalen Standort gerichteten Datenverkehr an die Azure Firewall weiterleitet. Diese Routingtabelle wird auf das NVA-Proxysubnetz angewendet. 
5. Eine Netzwerksicherheitsgruppe, die auf das Proxy-NVA-Subnetz angewendet wird, um nur dem Subnetzbereich der Azure Firewall als Quelle zu vertrauen und nur die Weiterleitung an die IP-Adresse der HSM-NIC über den TCP-Port 1792 zuzulassen. 

> [!NOTE]
> Da die NVA-Proxyebene die Client-IP-Adresse bei der Weiterleitung an die HSM-NIC einer SNAT unterzieht, sind keine benutzerdefinierten Routen zwischen dem HSM-VNet und dem DMZ Hub-VNet erforderlich.  

### <a name="alternative-to-udrs"></a>Alternativen zu benutzerdefinierten Routen (UDRs)
Die oben erwähnte NVA-Ebenenlösung funktioniert als Alternative zu UDRs. Es gibt aber einige wichtige Punkte zu beachten.
1.  Die Netzwerkadressenübersetzung (NAT) sollte auf dem virtuellen Netzwerkgerät konfiguriert sein, damit der Rückgabedatenverkehr ordnungsgemäß weitergeleitet werden kann.
2. Kunden sollten die Überprüfung der Client-IP in der Luna HSM-Konfiguration deaktivieren, damit VNA für NAT verwendet wird. Die folgenden Befehle dienen als Beispiele.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  Stellen Sie UDRs für eingehenden Datenverkehr in der NVA-Ebene bereit. 
4. Gemäß dem Entwurf initiieren HSM-Subnetze keine ausgehende Verbindungsanforderung an die Plattformebene.

### <a name="alternative-to-using-global-vnet-peering"></a>Alternative zur Verwendung von globalem VNET-Peering
Es gibt eine Reihe von Architekturen, die Sie als Alternative zum globalen VNET-Peering verwenden können.
1.  Verwenden einer [VNET-zu-VNET-VPN Gateway-Verbindung](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 
2.  Verbinden Sie das HSM-VNET mit einem anderen VNET über eine ER-Verbindung. Dies funktioniert am besten, wenn ein direkter lokaler Pfad oder ein VPN-VNET erforderlich ist. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>HSM mit direkter ExpressRoute-Konnektivität
![Diagramm zeigt HSM mit direkter ExpressRoute-Konnektivität.](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen](faq.yml)
- [Unterstützungsmöglichkeiten](supportability.md)
- [Hochverfügbarkeit](high-availability.md)
- [Physische Sicherheit](physical-security.md)
- [Überwachung](monitoring.md)
- [Bereitstellungsarchitektur](deployment-architecture.md)
