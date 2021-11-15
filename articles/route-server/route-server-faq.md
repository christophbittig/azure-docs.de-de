---
title: Häufig gestellte Fragen zu Azure Route Server
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zu Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 47584994586e735647be4116fb49de610e5f97f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453267"
---
# <a name="azure-route-server-faq"></a>Häufig gestellte Fragen zu Azure Route Server

## <a name="what-is-azure-route-server"></a>Was ist Azure Route Server?

Azure Route Server ist ein vollständig verwalteter Dienst, mit dem Sie das Routing zwischen Ihrem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) und Ihrem virtuellen Netzwerk auf einfache Weise verwalten können.

### <a name="is-azure-route-server-just-a-vm"></a>Ist Azure Route Server einfach nur ein virtueller Computer?

Nein. Azure Route Server ist ein Dienst, der mit hoher Verfügbarkeit entworfen wurde. Wenn er in einer Azure-Region bereitgestellt wird, die [Verfügbarkeitszonen](../availability-zones/az-overview.md) unterstützt, verfügt er über Redundanz auf Zonenebene.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Wie viele Routenserver kann ich in einem virtuellen Netzwerk erstellen?

Sie können lediglich einen einzelnen Routenserver in einem virtuellen Netzwerk erstellen. Er muss in einem dedizierten Subnetz namens *RouteServerSubnet* bereitgestellt werden.

### <a name="does-azure-route-server-support-virtual-network-peering"></a>Unterstützt Azure Route Server das Peering virtueller Netzwerke?

Ja, wenn Sie ein virtuelles Netzwerk, das Azure Route Server hostet, mit einem anderen virtuellen Netzwerk peeren und für das zweite virtuelle Netzwerk „Remotegateway verwenden“ aktivieren, ermittelt Azure Route Server die Adressräume dieses virtuellen Netzwerks und sendet diese an alle gepeerten NVAs. Außerdem werden die Routen von den NVAs in der Routingtabelle der virtuellen Computer im gepeerten virtuellen Netzwerk programmiert. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Welche Routingprotokolle werden von Azure Route Server unterstützt?

Azure Route Server unterstützt nur BGP (Border Gateway Protocol). Ihr NVA muss externes BGP mit mehreren Hops unterstützen, da Sie Azure Route Server in einem dedizierten Subnetz in Ihrem virtuellen Netzwerk bereitstellen müssen. Beim Konfigurieren von BGP auf Ihrem NVA muss sich die von Ihnen gewählte [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) von derjenigen unterscheiden, die von Azure Route Server verwendet wird.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Leitet Azure Route Server den Datenverkehr zwischen meinem NVA und meinen VMs weiter?

Nein. Azure Route Server tauscht nur BGP-Routen mit Ihrem NVA aus. Der Datenverkehr fließt direkt von der NVA zum virtuellen Zielcomputer und direkt vom virtuellen Computer zur NVA.

### <a name="does-azure-route-server-store-customer-data"></a>Speichert Azure Route Server Kundendaten?
Nein. Azure Route Server tauscht nur BGP-Routen mit Ihrem virtuellen Netzwerkgerät aus und überträgt Sie dann an Ihr virtuelles Netzwerk.

### <a name="why-does-azure-route-server-require-a-public-ip-address"></a>Warum erfordert Azure Route Server eine öffentliche IP-Adresse?

Azure Route Server muss die Konnektivität mit dem Back-End-Dienst sicherstellen, der die Route Server-Konfiguration verwaltet, da eine solche öffentliche IP-Adresse erforderlich ist. 

### <a name="does-azure-route-server-support-ipv6"></a>Unterstützt Azure Route Server IPv6?

Nein. IPv6-Unterstützung wird zu einem späteren Zeitpunkt hinzugefügt. 

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Was passiert, wenn Azure Route Server die gleiche Route von mehreren NVAs empfängt?

Wenn die Route die gleiche AS-Pfadlänge aufweist, werden von Azure Route Server mehrere Kopien der Route (mit jeweils einem anderen nächsten Hop) zu den virtuellen Computern im virtuellen Netzwerk programmiert. Wenn die VMs Datenverkehr an das Ziel dieser Route senden, führen die VM-Hosts ein ECMP-Routing (Equal-Cost Multi Path) aus. Wenn jedoch eine NVA die Route mit einer kürzeren AS-Pfadlänge sendet als andere NVAs, wird von Azure Route Server nur die Route, bei der der nächste Hop auf diese NVA festgelegt ist, zu den virtuellen Computern im virtuellen Netzwerk programmiert.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Werden von Azure Route Server die BGP-Communitys der empfangenen Route beibehalten?

Ja. Von Azure Route Server wird die Route ohne Änderungen an den BGP-Communitys verteilt.

### <a name="what-is-the-bgp-timer-setting-of-azure-route-server"></a>Wie ist die BGP-Timereinstellung von Azure Route Server?
Der Keep-Alive-Timer ist auf 60 Sekunden und der Hold-Down-Timer auf 180 Sekunden festgelegt.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Welche autonomen Systemnummern (ASNs) kann ich verwenden?

Sie können Ihre eigenen öffentlichen ASNs oder privaten ASNs auf Ihrem virtuellen Netzwerkgerät verwenden. Die von Azure oder IANA reservierten Bereiche können nicht verwendet werden.
Die folgenden ASNs sind für Azure oder IANA reserviert:

* Von Azure reservierte ASNs:
    * Öffentliche ASNs: 8074, 8075, 12076
    * Private ASNs: 65515, 65517, 65518, 65519, 65520
* [Von IANA reservierte](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) ASNs:
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kann ich 32-Bit-ASNs (4 Bytes) verwenden?

Nein, Azure Route Server unterstützt nur 16-Bit-ASNs (2 Bytes).

### <a name="can-i-associate-a-user-defined-route-udr-to-the-routeserversubnet"></a>Kann ich dem RouteServerSubnet eine benutzerdefinierte Route (User Defined Route, UDR) zuordnen?

Nein, Azure Route Server unterstützt die Konfiguration benutzerdefinierter Routen im RouteServerSubnet nicht.

### <a name="can-i-associate-a-network-security-group-nsg-to-the-routeserversubnet"></a>Kann ich dem RouteServerSubnet eine Netzwerksicherheitsgruppe (NSG) zuordnen?

Nein, Azure Route Server unterstützt die Zuordnung von NSGs zum RouteServerSubnet nicht.

### <a name="can-i-peer-two-route-servers-in-two-peered-virtual-networks-and-enable-the-nvas-connected-to-the-route-servers-to-talk-to-each-other"></a>Kann ich zwei Routenserver in zwei virtuellen Netzwerken mit Peering verbinden und den mit den Routenservern verbundenen NVAs ermöglichen, miteinander zu kommunizieren? 

***Topologie: NVA1 -> RouteServer1 -> (über VNet-Peering) -> RouteServer2 -> NVA2***

Nein, Azure Route Server leitet keinen Datenverkehr weiter. Richten Sie zum Aktivieren der Transitkonnektivität über das NVA eine direkte Verbindung (z. B. einen IPsec-Tunnel) zwischen den NVAs ein, und verwenden Sie die Routenserver für die dynamische Routenweitergabe. 

### <a name="can-i-use-azure-route-server-to-direct-traffic-between-subnets-in-the-same-virtual-network-to-flow-inter-subnet-traffic-through-the-nva"></a>Kann ich Azure Route Server verwenden, um Datenverkehr zwischen Subnetzen im gleichen virtuellen Netzwerk so zu leiten, dass dieser über das NVA geführt wird?

Nein. Systemrouten für Datenverkehr im Zusammenhang mit virtuellen Netzwerken, virtuellen Netzwerken über Peerings oder VNET-Dienstendpunkten sind die bevorzugten Routen, auch wenn BGP-Routen spezifischer sind. Da Route Server BGP zum Ankündigen von Routen verwendet, wird dies derzeit nicht standardmäßig unterstützt. Sie müssen weiterhin UDRs verwenden, um das Außerkraftsetzen der Routen zu erzwingen, und Sie können BGP nicht verwenden, um schnell ein Failover für diese Routen durchzuführen. Sie müssen weiterhin eine Drittanbieterlösung verwenden, um die UDRs in einer Failoversituation über die API zu aktualisieren, oder eine Azure Load Balancer-Instanz mit dem Modus für Hochverfügbarkeitsports verwenden, um Datenverkehr weiterzuleiten.

Sie können Route Server weiterhin verwenden, um Datenverkehr zwischen Subnetzen in verschiedenen virtuellen Netzwerken mithilfe des NVA weiterzuleiten. Der einzig mögliche Entwurf, der funktionieren könnte, sieht folgendermaßen aus: Es ist ein Subnetz pro virtuellem „Spoke“-Netzwerk vorhanden, und alle virtuellen Netzwerke sind per Peering mit einem virtuellen „Hub“-Netzwerk verbunden. Dieser Entwurf weist jedoch starke Einschränkungen auf, und es müssen Überlegungen zur Skalierung und die maximalen Grenzwerte von Azure für virtuelle Netzwerke und Subnetze berücksichtigt werden.

## <a name="route-server-limits"></a><a name = "limitations"></a>Grenzwerte für Route Server

Azure Route Server weist die folgenden Grenzwerte auf (pro Bereitstellung).

| Resource | Begrenzung |
|----------|-------|
| Anzahl unterstützter BGP-Peers | 8 |
| Anzahl von Routen, die jeder BGP-Peer für Azure Route Server ankündigen kann | 1000 |
| Anzahl von Routen, die Azure Route Server für ein ExpressRoute- oder VPN-Gateway ankündigen kann | 200 |
| Anzahl der virtuellen Computer im virtuellen Netzwerk (einschließlich gepeerter virtueller Netzwerke), die von Azure Route Server unterstützt werden | 2000 |

Die Anzahl der VMs, die Azure Route Server unterstützen kann, ist nicht fest begrenzt. Dies hängt davon ab, wie die Route Server-Infrastruktur innerhalb einer Azure-Region bereitgestellt wird.

Wenn Ihr NVA mehr Routen als den Grenzwert ankündigt, wird die BGP-Sitzung gelöscht. Wenn die BGP-Sitzung zwischen dem Gateway und Azure Route Server gelöscht wird, verlieren Sie die Verbindung zwischen Ihrem lokalen Netzwerk und Azure. Weitere Informationen finden Sie unter [Diagnose des Routingproblems einer Azure-VM](../virtual-network/diagnose-network-routing-problem.md).


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Azure Route Server konfigurieren](quickstart-configure-route-server-powershell.md).
