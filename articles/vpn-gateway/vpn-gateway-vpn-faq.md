---
title: Häufig gestellte Fragen zu VPN Gateway
description: Erfahren Sie mehr über häufig gestellte Fragen zu standortübergreifenden VPN Gateway-Verbindungen, Hybridkonfigurationsverbindungen und virtuellen Netzwerkgateways. Diese häufig gestellten Fragen enthalten umfassende Informationen zu Point-to-Site-, Site-to-Site- und VNet-zu-VNet-Konfigurationseinstellungen.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: yushwang
ms.openlocfilehash: 0551cf435b732b621ad5493914251afd67f18882
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707141"
---
# <a name="vpn-gateway-faq"></a>Häufig gestellte Fragen zum VPN-Gateway

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>Herstellen einer Verbindung mit virtuellen Netzwerken

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Kann ich Verbindungen zwischen virtuellen Netzwerken in verschiedenen Azure-Regionen herstellen?

Ja. Es gelten keine regionsbedingten Einschränkungen. Verbindungen können sowohl zwischen virtuellen Netzwerken in der gleichen Region als auch zwischen virtuellen Netzwerken in unterschiedlichen Azure-Regionen hergestellt werden.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Kann ich Verbindungen zwischen virtuellen Netzwerken in unterschiedlichen Abonnements herstellen?

Ja.

### <a name="can-i-specify-private-dns-servers-in-my-vnet-when-configuring-vpn-gateway"></a>Kann ich beim Konfigurieren von VPN Gateway private DNS-Server in meinem VNet angeben?

Wenn Sie beim Erstellen Ihres VNets einen oder mehrere DNS-Server angegeben haben, werden diese in VPN Gateway verwendet. Achten Sie bei der Angabe eines DNS-Servers darauf, dass dieser die für Azure erforderlichen Domänennamen auflösen kann.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Kann ich eine Verbindung mit mehreren Standorten eines einzelnen virtuellen Netzwerks herstellen?

Verbindungen mit mehreren Standorten können mit Windows PowerShell und den REST-APIs von Azure hergestellt werden. Weitere Informationen finden Sie im Abschnitt [Mehrere Standorte und VNet-zu-VNet-Verbindungen](#V2VMulti) .

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>Gibt es eine zusätzliche Gebühr für das Einrichten eines VPN-Gateways als aktiv/aktiv?

Nein.

### <a name="what-are-my-cross-premises-connection-options"></a>Welche Optionen stehen mir bei standortübergreifenden Verbindungen zur Verfügung?

Die folgenden standortübergreifende Verbindungen werden unterstützt:

* Standort zu Standort: VPN-Verbindung über IPsec (IKE v1 und IKE v2). Für diese Art von Verbindung wird ein VPN-Gerät oder RRAS benötigt. Weitere Informationen finden Sie unter [Erstellen einer Site-to-Site-Verbindung im Azure-Portal](./tutorial-site-to-site-portal.md).
* Point-to-Site: VPN-Verbindung über SSTP (Secure Socket Tunneling-Protokoll) oder IKE v2. Für diese Verbindung wird kein VPN-Gerät benötigt. Weitere Informationen finden Sie unter [Konfigurieren einer Point-to-Site-Verbindung mit einem VNet über das Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* VNet zu VNet: Dieser Verbindungstyp entspricht der Standort-zu-Standort-Konfiguration. VNet zu VNet ist eine VPN-Verbindung über IPsec (IKE v1 und IKE v2). Für diese Verbindung wird kein VPN-Gerät benötigt. Weitere Informationen finden Sie unter [Konfigurieren einer VNet-zu-VNet-Verbindung über das Azure-Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Mehrere Standorte: Hierbei handelt es sich um eine Variante der Standort-zu-Standort-Konfiguration, mit der Sie mehrere lokale Standorte mit einem virtuellen Netzwerk verbinden können. Weitere Informationen finden Sie unter [Hinzufügen einer Site-to-Site-Verbindung (S2S) zu einem VNet mit einer vorhandenen VPN-Gatewayverbindung](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute: ExpressRoute ist eine private Azure-Verbindung mit Ihrem WAN (keine VPN-Verbindung über das öffentliche Internet). Weitere Informationen finden Sie unter [ExpressRoute – Technische Übersicht](../expressroute/expressroute-introduction.md) sowie unter [ExpressRoute – FAQ](../expressroute/expressroute-faqs.md).

Weitere Informationen zu VPN Gateway-Verbindungen finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Was ist der Unterschied zwischen einer Standort-zu-Standort- und einer Punkt-zu-Standort-Verbindung?

**Site-to-Site**-Konfigurationen (IPsec/IKE-VPN-Tunnel) betreffen Verbindungen zwischen Ihrem lokalen Standort und Azure. Dies bedeutet, dass Sie eine Verbindung zwischen einem beliebigen lokalen Computer und einem beliebigen virtuellen Computer oder einer Rolleninstanz in Ihrem virtuellen Netzwerk herstellen können (abhängig von der Routingkonfiguration und den Berechtigungen). Diese Option eignet sich hervorragend für eine ständig verfügbare, standortübergreifende Verbindung sowie für Hybridkonfigurationen. Dieser Verbindungstyp basiert auf einer (hardware- oder softwarebasierten) IPsec-VPN-Appliance, die am Rand des Netzwerks bereitgestellt werden muss. Zum Erstellen dieser Art von Verbindung müssen Sie über eine externe IPv4-Adresse verfügen.

Mithilfe von **Point-to-Site**-Konfigurationen (VPN über SSTP) können Sie ortsunabhängig eine Verbindung zwischen einem einzelnen Computer und einer beliebigen Ressource in Ihrem virtuellen Netzwerk herstellen. Hierbei kommt der in Windows enthaltene VPN-Client zum Einsatz. Im Rahmen der Punkt-zu-Standort-Konfiguration installieren Sie ein Zertifikat und ein VPN-Clientkonfigurationspaket mit den Einstellungen, die es Ihrem Computer ermöglichen, eine Verbindung mit einem beliebigen virtuellen Computer oder einer beliebigen Rolleninstanz innerhalb des virtuellen Netzwerks herzustellen. Dieser Verbindungstyp eignet sich hervorragend, wenn Sie eine Verbindung mit einem virtuellen Netzwerk herstellen möchten, sich aber nicht vor Ort befinden. Er ist auch eine gute Wahl, wenn Ihnen keine VPN-Hardware oder keine extern ausgerichtete IPv4-Adresse zur Verfügung steht (beides Voraussetzungen für eine Standort-zu-Standort-Verbindung).

Sie können Ihr virtuelles Netzwerk für die parallele Verwendung von Site-to-Site- und Point-to-Site-Verbindungen konfigurieren. Hierzu müssen Sie Ihre Site-to-Site-Verbindung mit einem routenbasierten VPN-Typ für Ihr Gateway erstellen. Routenbasierte VPN-Typen werden im klassischen Bereitstellungsmodell als dynamische Gateways bezeichnet.

## <a name="privacy"></a><a name="privacy"></a>Datenschutz

### <a name="does-the-vpn-service-store-or-process-customer-data"></a>Werden vom VPN-Dienst Kundendaten gespeichert oder verarbeitet?

Nein.

## <a name="virtual-network-gateways"></a><a name="gateways"></a>Gateways für virtuelle Netzwerke

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Ist ein VPN-Gateway ein virtuelles Netzwerkgateway?

Ein VPN-Gateway ist eine Art virtuelles Netzwerkgateway. Es sendet verschlüsselten Datenverkehr zwischen Ihrem virtuellen Netzwerk und Ihrem lokalen Standort über eine öffentliche Verbindung. Über ein VPN Gateway können Sie auch Datenverkehr zwischen virtuellen Netzwerken senden. Bei der Erstellung eines VPN-Gateways verwenden Sie für „-GatewayType“ den Wert „Vpn“. Weitere Informationen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Was ist ein richtlinienbasiertes Gateway mit statischem Routing?

Richtlinienbasierte Gateways implementieren richtlinienbasierte VPNs verwendet. Bei richtlinienbasierten VPNs werden Pakete verschlüsselt und durch IPsec-Tunnel geleitet. Grundlage hierfür sind Kombinationen aus Adresspräfixen zwischen Ihrem lokalen Netzwerk und dem Azure-VNet. Die Richtlinie (auch Datenverkehrsselektor genannt) wird in der Regel als Zugriffsliste in der VPN-Konfiguration definiert.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Was ist ein routebasiertes Gateway mit dynamischem Routing?

Routenbasierte Gateways implementieren die routenbasierten VPNs. Bei routingbasierten VPNs werden Pakete auf der Grundlage der Routen der IP-Weiterleitungs- oder -Routingtabelle an die entsprechenden VPN-Tunnelschnittstellen weitergeleitet. An den Tunnelschnittstellen werden die Pakete dann ver- bzw. entschlüsselt. Die Richtlinie oder Datenverkehrsauswahl für routenbasierte VPNs wird im Any-to-Any-Format (bzw. als Platzhalter) konfiguriert.

### <a name="can-i-specify-my-own-policy-based-traffic-selectors"></a>Kann ich meine eigenen richtlinienbasierten Datenverkehrsselektoren angeben?

Ja. Datenverkehrsselektoren können mithilfe des PowerShell-Befehls [New-AzIpsecTrafficSelectorPolicy](/powershell/module/az.network/new-azipsectrafficselectorpolicy) über das Attribut *trafficSelectorPolicies* für eine Verbindung definiert werden. Achten Sie darauf, dass die Option [Use policy based traffic selectors](vpn-gateway-connect-multiple-policybased-rm-ps.md#enablepolicybased) (Richtlinienbasierte Datenverkehrsselektoren verwenden) aktiviert ist, damit der angegebene Datenverkehrsselektor wirksam wird.

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>Kann ich mein richtlinienbasiertes VPN-Gateway zu einem routenbasierten aktualisieren?

Nein. Ein Gatewaytyp kann nicht von „richtlinienbasiert“ in „routenbasiert“ oder umgekehrt geändert werden. Wenn Sie den Gatewaytyp ändern möchten, müssen Sie das Gateway löschen und neu erstellen. Dieser Vorgang dauert etwa 60 Minuten. Wenn Sie ein neues Gateway erstellen, können Sie die IP-Adresse des ursprünglichen Gateways nicht beibehalten.

1. Löschen Sie alle Verbindungen, die dem Gateway zugeordnet sind.

1. Befolgen Sie zum Löschen des Gateways die Anleitung in einem der folgenden Artikel:

   * [Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
   * [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Azure PowerShell – klassisch](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. Erstellen Sie ein neues Gateway mit dem gewünschten Gatewaytyp, und schließen Sie das VPN-Setup ab. Eine Anleitung hierzu finden Sie im [Tutorial: Erstellen einer Site-to-Site-Verbindung im Azure-Portal](./tutorial-site-to-site-portal.md#VNetGateway).

### <a name="do-i-need-a-gatewaysubnet"></a>Benötige ich ein Gatewaysubnetz?

Ja. Das Gatewaysubnetz enthält die IP-Adressen, die von den Diensten des virtuellen Netzwerkgateways verwendet werden. Sie müssen ein Gatewaysubnetz für Ihr VNet erstellen, um ein virtuelles Netzwerkgateway konfigurieren zu können. Alle Gatewaysubnetze müssen den Namen „GatewaySubnet“ besitzen, damit sie einwandfrei funktionieren. Verwenden Sie für Ihr Gatewaysubnetz keinen anderen Namen. Zudem dürfen keine VMs oder anderen Komponenten im Gatewaysubnetz bereitgestellt werden.

Bei der Gatewayerstellung geben Sie die Anzahl der im Subnetz enthaltenen IP-Adressen an. Die IP-Adressen im Gatewaysubnetz werden dem Gatewaydienst zugeordnet. Die Anzahl von IP-Adressen, die den Gatewaydiensten zugeordnet werden müssen, ist abhängig von der jeweiligen Konfiguration. Stellen Sie sicher, dass Ihr Gatewaysubnetz über genügend IP-Adressen verfügt, und berücksichtigen Sie dabei auch zukünftiges Wachstum sowie mögliche neue Verbindungskonfigurationen. Sie können zwar ein Gatewaysubnetz mit einer Größe von nur /29 erstellen, aber es empfiehlt sich, ein Gatewaysubnetz mit einer Größe von mindestens /27 (/27, /26, /25 usw.) zu erstellen. Sehen Sie sich die Anforderungen für die Konfiguration an, die Sie erstellen möchten, und vergewissern Sie sich, dass Ihr Gatewaysubnetz diese Anforderungen erfüllt.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Kann ich virtuelle Computer oder Rolleninstanzen in meinem Gatewaysubnetz bereitstellen?

Nein.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Kann ich die IP-Adresse meines VPN-Gateways vor dessen Erstellung erhalten?

Sowohl zonenredundante als auch zonale Gateways (Gateway-SKUs mit _AZ_ im Namen) basieren auf einer Azure-Ressource mit öffentlicher IP-Adresse und _Standard-SKU_. Ressourcen mit öffentlichen IP-Adressen und Azure-Standard-SKU müssen eine statische Zuordnungsmethode verwenden. Daher erhalten Sie die öffentliche IP-Adresse für Ihr VPN-Gateway, sobald Sie die Ressource mit öffentlicher IP-Adresse und Standard-SKU erstellen, die Sie für die Ressource verwenden möchten.

Für nicht zonenredundante und nicht zonale Gateways (Gateway-SKUs _ohne_ _AZ_ im Namen) können Sie die IP-Adresse des VPN-Gateways nicht vor dem Erstellen erhalten. Nur wenn Sie das VPN-Gateway löschen und anschließend neu erstellen, ändert sich die IP-Adresse.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Kann ich eine statische öffentliche IP-Adresse für mein VPN-Gateway anfordern?

Wie oben angegeben basieren sowohl zonenredundante als auch zonale Gateways (Gateway-SKUs mit _AZ_ im Namen) auf einer Azure-Ressource mit öffentlicher IP-Adresse und _Standard-SKU_. Ressourcen mit öffentlichen IP-Adressen und Azure-Standard-SKU müssen eine statische Zuordnungsmethode verwenden.

Für nicht zonenredundante und nicht zonale Gateways (Gateway-SKUs _ohne_ _AZ_ im Namen) wird nur die dynamische Zuweisung von IP-Adressen unterstützt. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die IP-Adresse des VPN-Gateways ändert sich nur, wenn das Gateway gelöscht und dann neu erstellt wird. Die öffentliche IP-Adresse des VPN-Gateways ändert sich nicht bei Größenänderungen, beim Zurücksetzen oder bei anderen internen Wartungs- und Upgradeprozessen Ihres VPN-Gateways.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Wie wird mein VPN-Tunnel authentifiziert?

Beim Azure-VPN erfolgt die Authentifizierung mittels vorinstalliertem Schlüssel (Pre-Shared Key, PSK). Der vorinstallierte Schlüssel wird bei der Erstellung des VPN-Tunnels generiert. Der automatisch generierte PSK kann mit dem PowerShell-Cmdlet „Set Pre-Shared Key“ oder der REST-API angepasst werden.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Kann ich mit der API „Set Pre-Shared Key“ mein richtlinienbasiertes VPN mit statischem Routing-Gateway konfigurieren?

Ja. Mit der API „Set Pre-Shared Key“ und dem PowerShell-Cmdlet können Sie sowohl richtlinienbasierte (statische) Azure-VPNs als auch routenbasierte (dynamische) VPNs konfigurieren.

### <a name="can-i-use-other-authentication-options"></a>Kann ich andere Authentifizierungsoptionen verwenden?

Für die Authentifizierung können nur vorinstallierte Schlüssel (Pre-Shared Keys, PSKs) verwendet werden.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Wie kann ich angeben, welcher Datenverkehr über das VPN-Gateway abgewickelt werden soll?

#### <a name="resource-manager-deployment-model"></a>Ressourcen-Manager-Bereitstellungsmodell

* PowerShell: Verwenden Sie „AddressPrefix“, um Datenverkehr für das lokale Netzwerkgateway anzugeben.
* Azure-Portal: Navigieren Sie zum lokalen Netzwerkgateway und dann zu „Konfiguration“ > „Adressraum“.

#### <a name="classic-deployment-model"></a>Klassisches Bereitstellungsmodell

* Azure-Portal: Navigieren Sie zum klassischen virtuellen Netzwerk und dann zu „VPN-Verbindungen“ > „Standort-zu-Standort-VPN-Verbindungen“ > „Name des lokalen Standorts“ > „Lokaler Standort“ > „Clientadressraum“.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Kann ich NAT-T für meine VPN-Verbindungen verwenden?

Ja, NAT Traversal (NAT-T) wird unterstützt. Azure VPN Gateway setzt KEINE NAT-ähnliche Funktionalität für die inneren Pakete ein, die bei den IPSec-Tunneln ein-/ausgehen.  Stellen Sie in dieser Konfiguration sicher, dass das lokale Gerät den IPSec-Tunnel initiiert.

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Kann ich in Azure einen eigenen VPN-Server einrichten und damit eine Verbindung mit meinem lokalen Netzwerk herstellen?

Ja. Sie können in Azure eigene VPN-Gateways oder -Server bereitstellen (entweder über Azure Marketplace oder durch Erstellung eines eigenen VPN-Routers). Sie müssen in Ihrem virtuellen Netzwerk benutzerdefinierte Routen konfigurieren, um sicherzustellen, dass der Datenverkehr zwischen Ihren lokalen Netzwerken und den Subnetzen Ihres virtuellen Netzwerks richtig weitergeleitet wird.

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>Warum sind auf meinem Gateway für virtuelle Netzwerke bestimmte Ports geöffnet?

Sie sind für die Kommunikation mit der Azure-Infrastruktur erforderlich. Sie werden von Azure-Zertifikaten geschützt (gesperrt). Ohne die richtigen Zertifikate werden externe Entitäten, einschließlich der Kunden dieser Gateways, sich nicht auf diese Endpunkte auswirken.

Ein Gateway für virtuelle Netzwerke ist im Grunde ein mehrfach vernetztes Gerät mit einem Netzwerkadapter, der das private Netzwerk des Kunden nutzt, und einem Netzwerkadapter für das öffentliche Netzwerk. Azure-Infrastrukturentitäten können aus Compliance-Gründen keine privaten Netzwerke von Kunden nutzen, sodass sie öffentliche Endpunkte für die Kommunikation der Infrastruktur nutzen müssen. Die öffentlichen Endpunkte werden in regelmäßigen Abständen mit der Azure-Sicherheitsüberwachung überprüft.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Weitere Informationen zu Gatewaytypen, Anforderungen und Durchsatz

Weitere Informationen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>Standort-zu-Standort-Verbindungen und VPN-Geräte

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Was muss ich bei der Wahl eines VPN-Geräts berücksichtigen?

Wir haben in Zusammenarbeit mit Geräteherstellern eine Reihe von VPN-Geräten für standardmäßige Standort-zu-Standort-Verbindungen getestet. Eine Liste mit kompatiblen VPN-Geräten, entsprechenden Konfigurationsanweisungen oder -beispielen und Gerätespezifikationen finden Sie im Artikel [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md). Alle Geräte der als kompatibel angegebenen Gerätefamilien sollten mit Virtual Network verwendet werden können. Hilfreiche Informationen zur Konfiguration des VPN-Gerätes finden Sie im entsprechenden Konfigurationsbeispiel oder unter dem Link für die entsprechende Gerätefamilie.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Wo finde ich die Konfigurationseinstellungen für VPN-Geräte?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Wie kann ich Beispiele für die Konfiguration von VPN-Geräten bearbeiten?

Informationen zur Bearbeitung von Gerätekonfigurationsbeispielen finden Sie unter [Bearbeiten von Gerätekonfigurationsbeispielen](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Wo finde ich IPsec- und IKE-Parameter?

Informationen zu IPsec/IKE-Parametern finden Sie unter [Parameter](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Warum fällt mein richtlinienbasierter VPN-Tunnel aus, wenn kein Datenverkehr stattfindet?

Dabei handelt es sich um einen Standardvorgang bei richtlinienbasierten (auch als „statisches Routing“ bezeichnet) VPN-Gateways. Wenn im Tunnel länger als 5 Minuten kein Datenverkehr stattfindet, wird der Tunnel geschlossen. Bei erneut einsetzendem Datenverkehr wird der Tunnel umgehend wiederhergestellt. Die Richtung des Datenverkehrs ist dabei unerheblich.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Kann ich VPN-Softwarelösungen verwenden, um eine Verbindung mit Azure herzustellen?

Für die standortübergreifende Standort-zu-Standort-Konfiguration werden Routing- und RAS-Server unter Windows Server 2012 unterstützt.

Auch andere VPN-Softwarelösungen können mit unserem Gateway verwendet werden, sofern sie über branchenübliche IPsec-Implementierungen verfügen. Konfigurations- und Supportinformationen erhalten Sie vom Anbieter der Software.

## <a name="point-to-site---certificate-authentication"></a><a name="P2S"></a>Point-to-Site – Zertifikatauthentifizierung

Dieser Abschnitt gilt für das Ressourcen-Manager-Bereitstellungsmodell.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site---radius-authentication"></a><a name="P2SRADIUS"></a>Point-to-Site – RADIUS-Authentifizierung

Dieser Abschnitt gilt für das Ressourcen-Manager-Bereitstellungsmodell.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>VNet-zu-VNet- und Multi-Site-Verbindungen

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Wie aktiviere ich das Routing zwischen meiner Standort-zu-Standort-VPN-Verbindung und meinem ExpressRoute?

Wenn Sie das Routing zwischen Ihrer Filiale, die mit ExpressRoute verbunden ist, und Ihrer Filiale, die mit einer Standort-zu-Standort-VPN-Verbindung verbunden ist, aktivieren möchten, müssen Sie den [Azure Route Server](../route-server/expressroute-vpn-support.md) einrichten.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Kann ich mit dem Azure-VPN Gateway Datenverkehr zwischen meinen lokalen Standorten oder an ein anderes virtuelles Netzwerk übertragen?

**Ressourcen-Manager-Bereitstellungsmodell**<br>
Ja. Weitere Informationen finden Sie im Abschnitt [BGP](#bgp).

**Klassisches Bereitstellungsmodell**<br>
Datenverkehr kann bei Verwendung des klassischen Bereitstellungsmodells über das Azure-VPN-Gateway übertragen werden, die Übertragung basiert jedoch auf statisch definierten Adressräumen aus der Netzwerkkonfigurationsdatei. BGP wird bei Azure Virtual Networks und VPN-Gateways, für die das klassische Bereitstellungsmodell verwendet wird, noch nicht unterstützt. Ohne BGP müssen die Adressräume für die Übertragung manuell definiert werden. Dies ist jedoch sehr fehleranfällig und wird daher nicht empfohlen.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Generiert Azure für alle meine VPN-Verbindungen für das gleiche virtuelle Netzwerk den gleichen vorinstallierten IPsec-/IKE-Schlüssel?

Nein. Azure generiert für unterschiedliche VPN-Verbindungen standardmäßig unterschiedliche vorinstallierte Schlüssel. Mit der REST-API oder dem PowerShell-Cmdlet „Set VPN Gateway Key“ können Sie jedoch den Schlüsselwert nach Ihren Vorstellungen festlegen. Der Schlüssel MUSS aus druckbaren ASCII-Zeichen bestehen.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Erhalte ich durch mehr Standort-zu-Standort-VPNs mehr Bandbreite als bei einem einzelnen virtuellen Netzwerk?

Nein. Alle VPN-Tunnel (einschließlich Punkt-zu-Standort-VPNs) verwenden das gleiche Azure-VPN Gateway und die gleiche verfügbare Bandbreite.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Kann ich mithilfe eines VPNs mit mehreren Standorten mehrere Tunnel zwischen meinem virtuellen Netzwerk und meinem lokalen Standort konfigurieren?

Ja. Sie müssen aber BGP für beide Tunnel zu demselben Standort konfigurieren.

### <a name="does-azure-vpn-gateway-honor-as-path-prepending-to-influence-routing-decisions-between-multiple-connections-to-my-on-premises-sites"></a>Werden bei Azure VPN Gateway vorangestellte AS-Pfade zum Beeinflussen von Routingentscheidungen zwischen mehreren Verbindungen mit meinen lokalen Standorten berücksichtigt?

Ja, das Azure-VPN-Gateway nutzt den vorangestellten AS-Pfad für Routingentscheidungen, wenn BGP aktiviert ist. Bei der BGP-Pfadauswahl wird ein kürzerer AS-Pfad bevorzugt.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Kann ich Punkt-zu-Standort-VPNs mit meinem virtuellen Netzwerk und mehreren VPN-Tunneln kombinieren?

Ja. VPNs vom Typ „Punkt zu Standort“ (P2S) können mit den VPN Gateways kombiniert werden, die Verbindungen mit mehreren lokalen Standorten und anderen virtuellen Netzwerken herstellen.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Kann ich eine Verbindung zwischen einem virtuellen Netzwerk mit IPsec-VPNs und meiner ExpressRoute-Verbindung herstellen?

Ja, das wird unterstützt. Weitere Informationen finden Sie unter [Konfigurieren von gleichzeitig vorhandenen ExpressRoute- und Standort-zu-Standort-VPN-Verbindungen](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec-/IKE-Richtlinie

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="bgp-and-routing"></a><a name="bgp"></a>BGP und Routing

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

### <a name="can-i-configure-forced-tunneling"></a>Kann ich die Tunnelerzwingung konfigurieren?

Ja. Weitere Informationen finden Sie unter [Konfigurieren der Tunnelerzwingung](vpn-gateway-about-forced-tunneling.md).

## <a name="nat"></a><a name="nat"></a>NAT

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>Standortübergreifende Konnektivität und virtuelle Computer

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Wenn sich mein virtueller Computer in einem virtuellen Netzwerk befindet und ich über eine standortübergreifende Verbindung verfüge, wie sollte ich dann die Verbindung mit dem virtuellen Computer herstellen?

Hier haben Sie mehrere Möglichkeiten: Wenn RDP für Ihren virtuellen Computer aktiviert ist, können Sie die Verbindung mit dem virtuellen Computer über die private IP-Adresse herstellen. Geben Sie in diesem Fall die private IP-Adresse und den Port an, mit dem Sie eine Verbindung herstellen möchten (in der Regel 3389). Auf Ihrem virtuellen Computer müssen Sie den Port für den Datenverkehr konfigurieren.

Außerdem kann die Verbindung mit dem virtuellen Computer von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk über die private IP-Adresse hergestellt werden. Wenn Sie die Verbindung an einem Standort außerhalb Ihres virtuellen Netzwerks herstellen möchten, kann über die private IP-Adresse keine RDP-Verbindung hergestellt werden. Wenn Sie also beispielsweise ein virtuelles Netzwerk mit Punkt-zu-Standort-Verbindung konfiguriert haben und die Verbindung nicht von Ihrem Computer aus herstellen, ist eine Verbindung mit dem virtuellen Computer auf Basis der privaten IP-Adresse nicht möglich.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Wenn sich mein virtueller Computer in einem virtuellen Netzwerk mit standortübergreifender Verbindung befindet, wird dann der gesamte Datenverkehr meines virtuellen Computers über diese Verbindung abgewickelt?

Nein. Nur der Datenverkehr mit einer IP-Zieladresse, die innerhalb der angegebenen lokalen Netzwerk-IP-Adressbereiche des virtuellen Netzwerks liegt, wird über das Gateway des virtuellen Netzwerks abgewickelt. Datenverkehr mit einer IP-Zieladresse im virtuellen Netzwerk bleibt innerhalb des virtuellen Netzwerks. Anderer Datenverkehr wird über den Load Balancer an die öffentlichen Netzwerke oder (bei erzwungener Tunnelung) über das Azure-VPN-Gateway gesendet.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Wie führe ich die Problembehandlung für die RDP-Verbindung mit einem virtuellen Computer durch?

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]

## <a name="virtual-network-faq"></a><a name="faq"></a>FAQs zu virtuellen Netzwerken

Weitere Informationen zu virtuellen Netzwerken finden Sie in den [Häufig gestellten Fragen zu virtuellen Netzwerken](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu VPN Gateway finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).
* Weitere Informationen zu VPN Gateway-Konfigurationseinstellungen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md).

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
