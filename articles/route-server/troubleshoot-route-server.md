---
title: Beheben von Problemen mit Azure Route Server
description: Hier erfahren Sie, wie Sie Probleme mit Azure Route Server beheben.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 08/30/2021
ms.author: duau
ms.openlocfilehash: 96e71dbd8a03e66644f0dc754d448a345978d940
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255669"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Beheben von Problemen mit Azure Route Server

> [!IMPORTANT]
> Azure Route Server (Vorschau) befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Konnektivitätsprobleme

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Warum verliert mein NVA Internetkonnektivität, nachdem es die Standardroute (0.0.0.0/0) für Azure Route Server angekündigt hat?
Wenn Ihr NVA die Standardroute ankündigt, programmiert Azure Route Server sie für alle VMs im virtuellen Netzwerk, einschließlich des NVA selbst. Diese Standardroute legt das NVA als nächsten Hop für den gesamten Internetdatenverkehr fest. Wenn Ihr NVA Internetkonnektivität benötigt, müssen Sie eine [benutzerdefinierte Route](../virtual-network/virtual-networks-udr-overview.md) (User Defined Route, UDR) konfigurieren, mit der diese Standardroute der NVA außer Kraft gesetzt wird, und die UDR an das Subnetz anfügen, in dem das NVA gehostet wird (siehe Beispiel unten). Andernfalls sendet der NVA-Hostcomputer weiterhin den Internetdatenverkehr, einschließlich des vom NVA gesendeten Datenverkehrs an das NVA selbst.

| Route | Nächster Hop |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>Warum kann ich von meinem NVA die BGP-Peer-IP von Azure Route Server anpingen, aber dieselbe IP-Adresse nicht mehr anpingen, nachdem ich das BGP-Peering zwischen ihnen eingerichtet habe? Weshalb ist das BGP-Peering nicht mehr möglich?

Bei einigen NVAs müssen Sie eine statische Route für das Azure Route Server-Subnetz hinzufügen. Wenn sich beispielsweise Azure Route Server in 10.0.255.0/27 und Ihr NVA in 10.0.1.0/24 befindet, müssen Sie der Routingtabelle auf dem NVA die folgende Route hinzufügen:

| Route | Nächster Hop |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 ist die Standardgateway-IP-Adresse in dem Subnetz, in dem Ihr NVA (oder genauer gesagt eine der NICs) gehostet wird.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Warum geht bei der Bereitstellung von Azure Route Server in einem virtuellen Netzwerk, das bereits über ein ExpressRoute-Gateway und/oder ein Azure-VPN-Gateway verfügt, die Konnektivität mit meinem lokalen Netzwerk über ExpressRoute und/oder Azure-VPN verloren?
Wenn Sie Azure Route Server in einem virtuellen Netzwerk bereitstellen, muss die Steuerungsebene zwischen den Gateways und dem virtuellen Netzwerk aktualisiert werden. Während dieser Aktualisierung wird für einen gewissen Zeitraum die Konnektivität der VMs im virtuellen Netzwerk mit dem lokalen Netzwerk getrennt. Es wird dringend empfohlen, eine Wartung für die Bereitstellung von Azure Route Server in Ihrer Produktionsumgebung zu planen.  

## <a name="control-plane-issues"></a>Probleme mit der Steuerungsebene

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Warum wird das BGP-Peering zwischen meinem NVA und der Azure Route Server-Instanz hoch- und herunterskaliert („Fluktuation“)?

Der Grund für die Fluktuation könnte die BGP-Timereinstellung sein. Standardmäßig ist der Keep-Alive-Timer in Azure Route Server auf 60 Sekunden und der Hold-Down-Timer auf 180 Sekunden festgelegt.

### <a name="why-does-my-on-premises-network-connected-to-azure-vpn-gateway-not-receive-the-default-route-advertised-by-azure-route-server"></a>Warum erhält mein lokales Netzwerk, das mit dem Azure-VPN-Gateway verbunden ist, nicht die vom Azure Route Server angekündigte Standardroute?

Azure VPN Gateway kann zwar die Standardroute von seinen BGP-Peers einschließlich Azure Route Server empfangen, aber die [Standardroute wird anderen Peers nicht angekündigt](../vpn-gateway/vpn-gateway-vpn-faq.md#what-address-prefixes-will-azure-vpn-gateways-advertise-to-me). 

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Warum empfängt mein NVA keine Routen von Azure Route Server, obwohl das BGP-Peering aktiv ist?

Die von Azure Route Server verwendete ASN lautet 65515. Konfigurieren Sie unbedingt eine andere ASN für Ihr NVA, damit eine „eBGP“-Sitzung zwischen Ihrem NVA und Azure Route Server eingerichtet werden kann, sodass die Routenverteilung automatisch erfolgen kann. In Ihrer BGP-Konfiguration muss unbedingt „Multihop“ aktiviert werden, da sich Ihr NVA und Azure Route Server in unterschiedlichen Subnetzen des virtuellen Netzwerks befinden.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>Das BGP-Peering zwischen meinem NVA und Azure Route Server ist aktiv. Ich kann sehen, dass die Routen zwischen ihnen richtig ausgetauscht werden. Warum sind die NVA-Routen nicht in der gültigen Routingtabelle meines virtuellen Computers enthalten? 

* Wenn sich Ihr virtueller Computer in demselben VNet befindet wie Ihr NVA und Azure Route Server:

     Azure Route Server macht zwei BGP-Peer-IPs verfügbar, die auf zwei VMs gehostet werden, die für das Senden der Routen an alle anderen VMs in Ihrem virtuellen Netzwerk zuständig sind. Jedes Ihrer virtuellen Netzwerkgeräte muss für die beiden VMs zwei identische BGP-Sitzungen einrichten (z. B. dieselbe ASN und denselben AS-Pfad verwenden und dieselben Routen ankündigen), damit Ihre VMs im virtuellen Netzwerk konsistente Routinginformationen von Azure Route Server erhalten können. Siehe folgendes Diagramm.

    ![Diagramm eines virtuellen Netzwerkgeräts mit Route Server.](./media/faq/network-virtual-appliances.png)

    Wenn Sie über zwei oder mehr Instanzen des NVAs verfügen, *können* Sie verschiedene AS-Pfade für dieselbe Route von verschiedenen NVA-Instanzen ankündigen, wenn Sie eine NVA-Instanz als aktiv und die andere als passiv festlegen möchten.

* Wenn sich Ihr virtueller Computer in einem anderen virtuellen Netzwerk befindet als dem, in dem Ihr NVA und Azure Route Server gehostet werden: Überprüfen Sie, ob das VNet-Peering zwischen den beiden VNets aktiviert ist *und* ob „Remote-Route Server verwenden“ für das VNet Ihres virtuellen Computers aktiviert ist.

### <a name="why-is-the-equal-cost-multi-path-ecmp-function-of-my-expressroute-turned-off-after-i-deploy-azure-route-server-to-the-virtual-network"></a>Warum ist Equal-Cost Multi-Path-Funktion (ECMP) meiner ExpressRoute-Verbindung deaktiviert, nachdem ich den Azure Route Server im virtuellen Netzwerk bereitgestellt habe?

Wenn Sie dieselben Routen aus Ihrem lokalen Netzwerk über mehrere ExpressRoute-Verbindungen an Azure ankündigen, ist ECMP normalerweise standardmäßig für den Datenverkehr aktiviert, der für diese Routen von Azure zurück zu Ihrem Standort bestimmt ist. Nach der Bereitstellung des Routenservers gehen die Informationen mit mehreren Pfaden jedoch beim BGP-Austausch zwischen ExpressRoute und Azure Route Server verloren, und der Datenverkehr von Azure durchläuft daher nur eine der ExpressRoute-Verbindungen. Diese Einschränkung wird in der zukünftigen Version von Azure Route Server aufgehoben.  

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [eine Azure Route Server-Instanz konfigurieren](quickstart-configure-route-server-powershell.md).
