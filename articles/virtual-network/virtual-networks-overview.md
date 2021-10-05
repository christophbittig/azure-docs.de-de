---
title: Azure Virtual Network
description: Erfahren Sie mehr über die Konzepte und Features von Azure Virtual Network, einschließlich Adressraum, Subnetze, Regionen und Abonnements.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 57d719f5ea56123c4b237e48f07f1e82fd885b3b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624802"
---
# <a name="what-is-azure-virtual-network"></a>Was ist Azure Virtual Network?

Azure Virtual Network (VNET) ist der grundlegende Baustein für Ihr privates Netzwerk in Azure. Mit VNET können zahlreiche Arten von Azure-Ressourcen (beispielsweise virtuelle Azure-Computer) sicher untereinander sowie mit dem Internet und mit lokalen Netzwerken kommunizieren. VNET ähnelt einem herkömmlichen Netzwerk, das Sie in Ihrem Rechenzentrum betreiben, bietet jedoch zusätzliche Vorteile der Infrastruktur von Azure, z. B. Skalierbarkeit, Verfügbarkeit und Isolation.

## <a name="why-use-an-azure-virtual-network"></a>Gründe für die Verwendung eines virtuellen Azure-Netzwerks
Mit virtuellen Azure-Netzwerken können Azure-Ressourcen sicher untereinander sowie mit dem Internet und mit lokalen Netzwerken kommunizieren. Zu den wichtigsten Szenarien, die Sie mit einem virtuellen Netzwerk realisieren können, gehören die Kommunikation von Azure-Ressourcen mit dem Internet, die Kommunikation zwischen Azure-Ressourcen, die Kommunikation mit lokalen Ressourcen, das Filtern von Netzwerkdatenverkehr, das Routing von Netzwerkdatenverkehr und die Integration mit Azure-Diensten.

### <a name="communicate-with-the-internet"></a>Kommunikation mit dem Internet

Alle Ressourcen in einem VNET können standardmäßig in ausgehender Richtung mit dem Internet kommunizieren. Zur Kommunikation in eingehender Richtung muss der entsprechenden Ressource eine öffentliche IP-Adresse oder eine öffentliche Load Balancer-Instanz zugewiesen werden. Die öffentliche IP-Adresse bzw. die öffentliche Load Balancer-Instanz kann auch zum Verwalten der ausgehenden Verbindungen verwendet werden.  Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Ausgehende Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md), [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md) und [Was versteht man unter Azure Load Balancer?](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Wenn Sie nur eine interne Instanz von [Load Balancer Standard](../load-balancer/load-balancer-overview.md) verwenden, ist ausgehende Konnektivität erst verfügbar, wenn Sie definieren, wie [ausgehende Verbindungen](../load-balancer/load-balancer-outbound-connections.md) mit einer öffentlichen IP auf Instanzebene oder einer öffentlichen Load Balancer-Instanz verwendet werden sollen.

### <a name="communicate-between-azure-resources"></a>Kommunikation zwischen Azure-Ressourcen

Azure-Ressourcen können auf eine der folgenden Arten sicher miteinander kommunizieren:

- **Über ein virtuelles Netzwerk**: Sie können virtuelle Computer und verschiedene andere Arten von Azure-Ressourcen in einem virtuellen Netzwerk bereitstellen, wie beispielsweise Azure App Service-Umgebungen, den Azure Kubernetes Service (AKS) und Azure Virtual Machine Scale Sets. Eine vollständige Liste mit Azure-Ressourcen, die Sie in einem virtuellen Netzwerk bereitstellen können, finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](virtual-network-for-azure-services.md).
- **Über einen VNET-Dienstendpunkt**: Erweitern Sie den Bereich privater Adressen und die Identität Ihres virtuellen Netzwerks über eine direkte Verbindung auf Azure-Dienstressourcen wie Azure Storage-Konten und Azure SQL-Datenbank. Mithilfe von Dienstendpunkten können Sie Ihre kritischen Azure-Dienstressourcen auf ein virtuelles Netzwerk beschränken und so schützen. Weitere Informationen finden Sie in der Übersicht über [VNET-Dienstendpunkte](virtual-network-service-endpoints-overview.md).
- **Über VNET-Peering:** Sie können virtuelle Netzwerke mittels VNet-Peering miteinander verbinden und so die Kommunikation zwischen Ressourcen in beiden virtuellen Netzwerken ermöglichen. Die verbundenen virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen befinden. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Kommunikation mit lokalen Ressourcen

Sie können Ihre lokalen Computer und Netzwerke unter Verwendung einer beliebigen Kombination der folgenden Optionen mit einem virtuellen Netzwerk verbinden:

- **Point-to-Site-VPN (virtuelles privates Netzwerk)** : Wird zwischen einem virtuellen Netzwerk und einem einzelnen Computer in Ihrem Netzwerk eingerichtet. Jeder Computer, der eine Verbindung mit einem virtuellen Netzwerk herstellen möchte, muss eine eigene Verbindung konfigurieren. Dieser Verbindungstyp ist gut geeignet, wenn Azure noch neu für Sie ist, oder wenn Sie ein Entwickler sind, da keine oder nur sehr geringe Änderungen Ihres vorhandenen Netzwerks erforderlich sind. Die Kommunikation zwischen Ihrem Computer und einem virtuellen Netzwerk wird durch einen verschlüsselten Tunnel über das Internet gesendet. Weitere Informationen finden Sie unter [Point-to-Site (VPN über IKEv2 oder SSTP)](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Site-to-Site-VPN**: Wird zwischen Ihrem lokalen VPN-Gerät und einem Azure-VPN-Gateway eingerichtet, das in einem virtuellen Netzwerk bereitgestellt ist. Bei diesem Verbindungstyp können alle lokalen Ressourcen, die von Ihnen autorisiert werden, auf ein virtuelles Netzwerk zugreifen. Die Kommunikation zwischen Ihrem lokalen VPN-Gerät und einem Azure-VPN-Gateway wird durch einen verschlüsselten Tunnel über das Internet gesendet. Weitere Informationen finden Sie unter [Site-to-Site und Multi-Site (IPsec-/IKE-VPN-Tunnel)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Wird zwischen Ihrem Netzwerk und Azure über einen ExpressRoute-Partner eingerichtet. Diese Verbindung ist privat. Der Datenverkehr wird nicht über das Internet übertragen. Weitere Informationen finden Sie unter [ExpressRoute (private Verbindung)](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Filtern des Netzwerkdatenverkehrs

Sie können den Netzwerkdatenverkehr zwischen Subnetzen filtern, indem Sie eine oder beide folgenden Optionen verwenden:

- **Netzwerksicherheitsgruppen:** Netzwerksicherheitsgruppen und Anwendungssicherheitsgruppen können mehrere Sicherheitsregeln für die eingehende und ausgehende Richtung enthalten, mit denen Sie den Datenverkehr in beiden Richtungen nach Quell- und Ziel-IP-Adresse, Port und Protokoll filtern können. Weitere Informationen siehe [Netzwerksicherheitsgruppen](./network-security-groups-overview.md#network-security-groups) und [Anwendungssicherheitsgruppen](./network-security-groups-overview.md#application-security-groups).
- **Virtuelle Netzwerkgeräte**: Ein virtuelles Netzwerkgerät ist ein virtueller Computer, der eine Netzwerkfunktion (Firewall, WAN-Optimierung oder Ähnliches) übernimmt. Eine Liste mit verfügbaren virtuellen Netzwerkgeräten, die Sie in einem virtuellen Netzwerk bereitstellen können, finden Sie im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

### <a name="route-network-traffic"></a>Weiterleiten von Netzwerkdatenverkehr

Azure leitet standardmäßig Datenverkehr zwischen Subnetzen, verbundenen virtuellen Netzwerken, lokalen Netzwerken und dem Internet weiter. Sie können eine oder beide der folgenden Optionen implementieren, um die von Azure erstellten Standardrouten außer Kraft zu setzen:

- **Routingtabellen**: Sie können benutzerdefinierte Routingtabellen mit Routen erstellen, über die gesteuert wird, wohin der Datenverkehr für die einzelnen Subnetze geleitet wird. Weitere Informationen zu Routingtabellen finden Sie [hier](virtual-networks-udr-overview.md#user-defined).
- **BGP-Routen (Border Gateway Protocol)** : Wenn Sie Ihr virtuelles Netzwerk mit Ihrem lokalen Netzwerk über ein Azure-VPN-Gateway oder eine ExpressRoute-Verbindung verbinden, können Sie Ihren virtuellen Netzwerken Ihre lokalen BGP-Routen weitergeben. Weitere Informationen zur Verwendung von BGP mit dem Azure-VPN-Gateway finden Sie [hier](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Informationen zur Verwendung mit ExpressRoute finden Sie [hier](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

### <a name="virtual-network-integration-for-azure-services"></a>Integration virtueller Netzwerke für Azure-Dienste

Wenn Sie Azure-Dienste in ein virtuelles Azure-Netzwerk integrieren, ermöglichen Sie damit den privaten Zugriff auf den Dienst über virtuelle Computer oder Computeressourcen im virtuellen Netzwerk.
Sie können Azure-Dienste mit den folgenden Optionen in Ihr virtuelles Netzwerk integrieren:
- Stellen Sie [dedizierte Instanzen des Diensts](virtual-network-for-azure-services.md) in einem virtuellen Netzwerk bereit. Auf die Dienste kann dann innerhalb des virtuellen Netzwerks und von lokalen Netzwerken aus privat zugegriffen werden.
- Verwenden Sie [Private Link](../private-link/private-link-overview.md), um von Ihrem virtuellen Netzwerk sowie von lokalen Netzwerken aus privat auf eine bestimmte Instanz des Diensts zuzugreifen.
- Sie können auf den Dienst auch unter Verwendung öffentlicher Endpunkte zugreifen, indem Sie ein virtuelles Netzwerk mithilfe von [Dienstendpunkten](virtual-network-service-endpoints-overview.md) auf den Dienst erweitern. Durch Dienstendpunkte können Dienstressourcen an das virtuelle Netzwerk gebunden werden.
 

## <a name="azure-vnet-limits"></a>Einschränkungen von Azure VNET

Es gibt bestimmte Grenzwerte für die Anzahl der Azure-Ressourcen, die Sie bereitstellen können. Die meisten Grenzwerte für Azure-Netzwerke sind auf die Maximalwerte festgelegt. Sie können jedoch [bestimmte Grenzwerte für Netzwerke erhöhen](../azure-portal/supportability/networking-quota-requests.md), wie unter [Grenzwerte für Netzwerke](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) angegeben. 

## <a name="virtual-networks-and-availability-zones"></a>Virtuelle Netzwerke und Verfügbarkeitszonen
Virtuelle Netzwerke und Subnetze umfassen alle Verfügbarkeitszonen in einer Region. Sie müssen sie nicht durch Verfügbarkeitszonen unterteilen, um zonale Ressourcen aufnehmen zu können. Wenn Sie beispielsweise eine zonale VM konfigurieren, müssen Sie das virtuelle Netzwerk bei der Auswahl der Verfügbarkeitszone für den virtuellen Computer nicht berücksichtigen. Dasselbe gilt für andere zonale Ressourcen.

## <a name="pricing"></a>Preise

Die Verwendung von Azure VNET ist kostenlos, es fallen keine Gebühren an. Für Ressourcen wie virtuelle Computer (VMs) und andere Produkte gelten Standardgebühren. Weitere Informationen finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network/) und im [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Nächste Schritte
 - Erfahren Sie mehr über [Azure Virtual Network-Konzepte und bewährte Methoden](concepts-and-best-practices.md).
 - Erstellen Sie als Nächstes ein virtuelles Netzwerk, stellen Sie einige virtuelle Computer darin bereit, und kommunizieren Sie zwischen den virtuellen Computern, um sich mit der Verwendung virtueller Netzwerke vertraut zu machen. Eine entsprechende Anleitung finden Sie unter [Erstellen eines virtuellen Netzwerks im Azure-Portal](quick-create-portal.md).
