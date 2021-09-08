---
title: Informationen zu Konfigurationen für hochverfügbare Gateways
titleSuffix: Azure VPN Gateway
description: Hier erfahren Sie mehr über Optionen für hochverfügbare Konfigurationen mit Azure VPN Gateway-Instanzen.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/27/2021
ms.author: yushwang
ms.openlocfilehash: 82a1ad0968a80be1ff1780b1cf83ec928a016dbb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346136"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Standortübergreifende Verbindungen und VNet-zu-VNet-Verbindungen mit Hochverfügbarkeit

In diesem Artikel erhalten Sie einen Überblick über Optionen zur Konfiguration der hohen Verfügbarkeit für standortübergreifende Verbindungen und VNet-zu-VNet-Verbindungen mit Azure-VPN-Gateways.

## <a name="about-vpn-gateway-redundancy"></a><a name = "activestandby"></a>Informationen zur VPN Gateway-Redundanz

Jede Azure-VPN-Gateway-Instanz umfasst zwei VM-Instanzen in einer Konfiguration mit aktivem Standbymodus. Für alle geplanten Wartungsarbeiten oder ungeplanten Störungen, die für die aktive Instanz anfallen, wird automatisch zur Standbyinstanz gewechselt (Failover), und die S2S-VPN- oder VNet-zu-VNet-Verbindungen werden wiederhergestellt. Bei diesem Wechsel kommt es zu einer kurzen Unterbrechung. Bei der geplanten Wartung sollte die Verbindung innerhalb von 10 bis 15 Sekunden wiederhergestellt werden. Bei ungeplanten Problemen dauert die Verbindungswiederherstellung länger, schlimmstenfalls zwischen einer und drei Minuten. Für P2S-VPN-Clientverbindungen mit dem Gateway werden die P2S-Verbindungen getrennt, und die Benutzer müssen die Verbindungen von den Clientcomputern neu herstellen.

:::image type="content" source="./media/vpn-gateway-highlyavailable/active-standby.png" alt-text="Das Diagramm zeigt einen lokalen Standort mit Subnetzen, die mit privaten IPs konfiguriert sind, und ein lokales VPN, das für die Verbindung mit in Azure gehosteten Subnetzen über ein Standby-Gateway mit einem aktiven Azure VPN-Gateway verbunden ist." border="false":::

## <a name="highly-available-cross-premises"></a>Standortübergreifende Hochverfügbarkeit

Es gibt einige Optionen, mit denen Sie die Verfügbarkeit für standortübergreifende Verbindungen verbessern können:

* Mehrere lokale VPN-Geräte
* Azure-VPN-Gateway vom Typ „Aktiv/Aktiv“
* Kombination beider Optionen

### <a name="multiple-on-premises-vpn-devices"></a><a name = "activeactiveonprem"></a>Mehrere lokale VPN-Geräte

Sie können mehrere VPN-Geräte in Ihrem lokalen Netzwerk verwenden, um eine Verbindung mit Ihrem Azure-VPN-Gateway herzustellen. Dies ist im folgenden Diagramm dargestellt:

:::image type="content" source="./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png" alt-text="Das Diagramm zeigt mehrere lokale Standorte mit Subnetzen, die mit privaten IP-Adressen konfiguriert sind, und ein lokales VPN, das für die Verbindung mit in Azure gehosteten Subnetzen über ein Standby-Gateway mit einem aktiven Azure-VPN-Gateway verbunden ist." border="false":::

Diese Konfiguration enthält mehrere aktive Tunnel von demselben Azure-VPN-Gateway zu Ihren lokalen Geräten an einem Standort. Es gelten einige Anforderungen und Einschränkungen:

1. Sie müssen mehrere S2S-VPN-Verbindungen von Ihren VPN-Geräten mit Azure herstellen. Wenn Sie für mehrere VPN-Geräte eine Verbindung aus demselben lokalen Netzwerk mit Azure herstellen, müssen Sie für jedes VPN-Gerät ein Gateway für das lokale Netzwerk und eine Verbindung von Ihrem Azure-VPN-Gateway zu jedem Gateway des lokalen Netzwerks erstellen.
1. Die Gateways des lokalen Netzwerks, die Sie für die VPN-Geräte erstellen, müssen in der „GatewayIpAddress“-Eigenschaft über eindeutige öffentliche IP-Adressen verfügen.
1. Für diese Konfiguration wird BGP benötigt. Für jedes Gateway des lokalen Netzwerks, das für ein VPN-Gerät steht, muss in der „BgpPeerIpAddress“-Eigenschaft eine eindeutige BGP-Peer-IP-Adresse angegeben werden.
1. Sie sollten BGP verwenden, um die Präfixe jeweils gegenüber denselben Präfixen des lokalen Netzwerks für das Azure-VPN-Gateway anzukündigen. Der Datenverkehr wird gleichzeitig durch diese Tunnel geleitet.
1. Sie müssen ECMP-Routing (Equal-Cost Multi Path) verwenden.
1. Jede Verbindung wird anhand der maximalen Anzahl von Tunneln für Ihr Azure-VPN-Gateway gezählt: 10 für Basic- und Standard-SKUs und 30 für die HighPerformance-SKU. 

Bei dieser Konfiguration befindet sich das Azure-VPN-Gateway noch im aktiven Standbymodus. Es kommt also zum gleichen Failover und zur gleichen kurzen Unterbrechung wie [oben](#activestandby) beschrieben. Diese Einrichtung bietet aber Schutz vor Ausfällen oder Unterbrechungen in Ihrem lokalen Netzwerk und für Ihre VPN-Geräte.

### <a name="active-active-vpn-gateways"></a>VPN Gateways vom Typ „Aktiv/Aktiv“

Sie können ein Azure-VPN-Gateway in einer Aktiv/Aktiv-Konfiguration erstellen, bei der beide Instanzen der Gateway-VMs jeweils S2S-VPN-Tunnel zum lokalen VPN-Gerät erstellen. Dies ist im folgenden Diagramm dargestellt:

:::image type="content" source="./media/vpn-gateway-highlyavailable/active-active.png" alt-text="Das Diagramm zeigt einen lokalen Standort mit Subnetzen, die mit privaten IPs konfiguriert sind, und einem lokalen VPN, das für Verbindungen mit in Azure gehosteten Subnetzen mit zwei aktiven Azure VPN-Gateways verbunden ist." border="false":::

Bei dieser Konfiguration verfügt jedes Azure-Gateway über eine eindeutige öffentliche IP-Adresse, und es wird jeweils ein IPsec/IKE-S2S-VPN-Tunnel zu dem lokalen VPN-Gerät erstellt, das für das Gateway des lokalen Netzwerks und die Verbindung angegeben ist. Beachten Sie, dass beide VPN-Tunnel Teil derselben Verbindung sind. Trotzdem müssen Sie Ihr lokales VPN-Gerät so konfigurieren, dass zwei S2S-VPN-Tunnel zu diesen beiden öffentlichen IP-Adressen des Azure-VPN-Gateways akzeptiert bzw. eingerichtet werden.

Da die Azure-Gatewayinstanzen über eine Aktiv/Aktiv-Konfiguration verfügen, wird der Datenverkehr von Ihrem virtuellen Azure-Netzwerk zum lokalen Netzwerk gleichzeitig durch beide Tunnel geleitet. Dies gilt auch, wenn das lokale VPN-Gerät einen Tunnel bevorzugt behandelt. Für einen einzelnen TCP- oder UDP-Flow versucht Azure, beim Senden von Paketen an Ihr lokales Netzwerk denselben Tunnel zu verwenden. Ihr lokales Netzwerk könnte jedoch einen anderen Tunnel verwenden, um Pakete an Azure zu senden.

Wenn für eine Gatewayinstanz ein geplantes Wartungsereignis oder ein ungeplantes Ereignis eintritt, wird die Verbindung des IPsec-Tunnels von dieser Instanz mit dem lokalen VPN-Gerät getrennt. Die entsprechenden Routen auf Ihren VPN-Geräten sollten automatisch entfernt oder zurückgezogen werden, damit für den Datenverkehr die Umschaltung auf den anderen aktiven IPsec-Tunnel durchgeführt wird. Auf Azure-Seite erfolgt automatisch die Umschaltung von der betroffenen Instanz zur aktiven Instanz.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Duale Redundanz: Aktiv/Aktiv-VPN-Gateways für Azure-Netzwerke und lokale Netzwerke

Die zuverlässigste Option ist eine Kombination der Aktiv/Aktiv-Gateways in Ihrem Netzwerk und unter Azure. Dies ist im folgenden Diagramm dargestellt:

:::image type="content" source="./media/vpn-gateway-highlyavailable/dual-redundancy.png" alt-text="Diagramm: Szenario mit dualer Redundanz" border="false":::

Hier führen Sie die Erstellung und Einrichtung des Azure-VPN-Gateways in einer Aktiv/Aktiv-Konfiguration durch und erstellen wie oben beschrieben zwei Gateways des lokalen Netzwerks und zwei Verbindungen für Ihre lokalen VPN-Geräte. Das Ergebnis ist eine vollständig vermaschte Verbindung mit vier IPsec-Tunneln zwischen Ihrem virtuellen Azure-Netzwerk und dem lokalen Netzwerk.

Alle Gateways und Tunnel sind auf der Azure-Seite aktiv. Der Datenverkehr wird also auf alle vier Tunnel gleichzeitig verteilt, aber jeder TCP- oder UDP-Fluss verläuft von der Azure-Seite aus wieder über denselben Tunnel oder Pfad. Auch wenn Sie durch das Aufteilen des Datenverkehrs einen etwas besseren Durchsatz über die IPsec-Tunnel erzielen, ist das Hauptziel dieser Konfiguration die Hochverfügbarkeit. Aufgrund der statistischen Art und Weise der Verteilung ist es außerdem schwierig zu messen, wie sich unterschiedliche Bedingungen für den Anwendungsdatenverkehr auf den Gesamtdurchsatz auswirken.

Für diese Topologie sind zwei Gateways des lokalen Netzwerks und zwei Verbindungen erforderlich, um das lokale VPN-Gerätepaar zu unterstützen, und BGP wird benötigt, um die Zulässigkeit der beiden Verbindungen für dasselbe lokale Netzwerk zu ermöglichen. Diese Anforderungen sind mit den [obigen](#activeactiveonprem)Anforderungen identisch. 

## <a name="highly-available-vnet-to-vnet"></a>VNet-to-VNet mit Hochverfügbarkeit 

Die gleiche Aktiv/Aktiv-Konfiguration kann auch für Azure-VNet-zu-VNet-Verbindungen gelten. Sie können Aktiv/Aktiv-VPN-Gateways für beide virtuellen Netzwerke erstellen und miteinander verbinden, um die gleiche vollständig vermaschte Verbindung mit vier Tunneln zwischen den beiden VNets zu erzielen. Dies ist im folgenden Diagramm dargestellt:

:::image type="content" source="./media/vpn-gateway-highlyavailable/vnet-to-vnet.png" alt-text="Das Diagramm zeigt zwei Azure-Regionen, in denen zwei mit privaten IPs konfigurierte Subnetze gehostet sind, und zwei Azure VPN-Gateways, über die Verbindungen der beiden virtuellen Standorte erfolgen." border="false":::

So wird sichergestellt, dass zwischen den beiden virtuellen Netzwerken immer ein Tunnelpaar für geplante Wartungsereignisse vorhanden ist. Dies führt zu einer weiteren Verbesserung der Verfügbarkeit. Für die gleiche Topologie für die standortübergreifende Konnektivität sind zwar zwei Verbindungen erforderlich, aber für die obige VNet-zu-VNet-Topologie wird nur eine Verbindung für jedes Gateway benötigt. Außerdem ist die Verwendung von BGP optional, sofern kein Transitrouting über die VNet-zu-VNet-Verbindung erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Konfigurieren von Aktiv/Aktiv-Gateways](active-active-portal.md) über das [Azure-Portal](active-active-portal.md) oder mit [PowerShell](vpn-gateway-activeactive-rm-powershell.md).

