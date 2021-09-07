---
title: Informationen zum Routing virtueller Hubs
titleSuffix: Azure Virtual WAN
description: Erfahren Sie mehr über Virtual WAN-Routing virtueller Hubs.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 154680d5f62140b95e7ada3a37678ee3be1c5b24
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350063"
---
# <a name="about-virtual-hub-routing"></a>Informationen zum Routing virtueller Hubs

Die Routingfunktionen in einem virtuellen Hub werden von einem Router bereitgestellt, der das gesamte Routing zwischen den Gateways mit dem Border Gateway Protocol (BGP) verwaltet. Ein virtueller Hub kann mehrere Gateways enthalten, z. B. ein Site-to-Site-VPN-Gateway, ExpressRoute-Gateway, Point-to-Site-Gateway, Azure Firewall. Dieser Router bietet auch Transitkonnektivität zwischen virtuellen Netzwerken, die mit einem virtuellen Hub verbunden sind, und kann einen Gesamtdurchsatz von bis zu 50 GBit/s unterstützen. Diese Routingfunktionen gelten für Kunden von Virtual WAN Standard.

Informationen zur Konfiguration des Routings finden Sie unter [Konfigurieren des Routings für virtuelle Hubs](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Routingkonzepte

In den folgenden Abschnitten werden die Schlüsselkonzepte des Routings für virtuelle Hubs beschrieben.

### <a name="hub-route-table"></a><a name="hub-route"></a>Hub-Routingtabelle

Eine Routingtabelle für virtuelle Hubs kann eine oder mehrere Routen enthalten. Eine Route enthält ihren Namen, eine Bezeichnung, einen Zieltyp, eine Liste von Zielpräfixen und Informationen zum nächsten Hop für ein weiterzuleitendes Paket. Eine **Verbindung** umfasst typischerweise eine Routingkonfiguration, die einer Routingtabelle zugeordnet ist oder auf eine solche verweist.

### <a name="hub-routing-intent-and-policies"></a><a name= "hub-route"></a> Absicht und Richtlinien für das Hubrouting
>[!NOTE]  
> Die Richtlinien für das Hubrouting befinden sich derzeit in der verwalteten Vorschau. 
>  
>Um Zugriff auf diese Vorschauversion zu erhalten, wenden Sie sich an previewinterhub@microsoft.com. Geben Sie hierbei die Virtual WAN-ID, die Abonnement-ID und die Azure-Region an, in der Sie Routingrichtlinien konfigurieren möchten. Sie sollten innerhalb von 24–48 Stunden eine Antwort mit einer Bestätigung der Funktionsfreigabe erhalten. 
>
> Weitere Informationen zum Konfigurieren von Routingabsicht und -richtlinien finden Sie in [diesem Dokument](how-to-routing-policies.md).


Kunden, die Azure Firewall Manager zum Einrichten von Richtlinien für öffentlichen und privaten Datenverkehr nutzen, können ihre Netzwerke jetzt mithilfe von Routingabsicht und Routingrichtlinien deutlich einfacher einrichten.

Über Routingabsicht und Routingrichtlinien können Sie angeben, wie der Virtual WAN-Hub internetgebundenen und privaten Datenverkehr (Point-to-Site, Site-to-Site, ExpressRoute, virtuelle Netzwerkgeräte im Virtual WAN-Hub und im virtuellen Netzwerk) weiterleiten soll. Es gibt zwei Arten von Routingrichtlinien: Richtlinien zum Routing von Internetdatenverkehr und Routingrichtlinien für privaten Datenverkehr. Jeder Virtual WAN-Hub kann höchstens eine Richtlinie für das Routing von Internetdatenverkehr und eine Richtlinie für das Routing von privatem Datenverkehr mit jeweils einer Ressource für den nächsten Hop verwenden. 

Während privater Datenverkehr sowohl Zweigstellen- als auch VNet-Adresspräfixe umfasst, werden diese von Routingrichtlinien im Konzept der Routingabsicht als eine Entität betrachtet.


* **Routingrichtlinie für Internetdatenverkehr**: Wenn eine Routingrichtlinie für Internetdatenverkehr für einen Virtual WAN-Hub konfiguriert ist, leiten alle Zweigstellen- (Benutzer-VPN [Point-to-Site-VPN], Site-to-Site-VPN und ExpressRoute) und VNet-Verbindungen mit diesem Virtual WAN-Hub internetgebundenen Datenverkehr an die Azure Firewall-Ressource oder den Sicherheitsanbieter eines Drittanbieters weiter, die bzw. der im Rahmen der Routingrichtlinie angegeben ist.
 

* **Routingrichtlinie für privaten Datenverkehr**: Wenn eine Routingrichtlinie für privaten Datenverkehr für einen Virtual WAN-Hub konfiguriert ist, wird der **gesamte** Zweigstellen- und VNet-Datenverkehr des Virtual WAN-Hubs (eingehend, ausgehend und zwischen Hubs) an die Azure Firewall-Ressource des nächsten Hops weitergeleitet, die in der Routingrichtlinie für privaten Datenverkehr angegeben wurde. 

Weitere Informationen zum Konfigurieren von Routingabsicht und -richtlinien finden Sie in [diesem Dokument](how-to-routing-policies.md).

### <a name="connections"></a><a name="connection"></a>Verbindungen

Verbindungen sind Resource Manager-Ressourcen, die über eine Routingkonfiguration verfügen. Die vier Arten von Verbindungen sind:

* **VPN-Verbindung:** Verbindet einen VPN-Standort mit einem VPN-Gateway für virtuelle Hubs.
* **ExpressRoute-Verbindung**: Verbindet eine ExpressRoute-Leitung mit einem ExpressRoute-Gateway für virtuelle Hubs.
* **Verbindung für P2S-Konfiguration**: Verbindet eine Benutzer-VPN-Konfiguration (Point-to-Site) mit einem Benutzer-VPN-Gateway für virtuelle Hubs (Point-to-Site).
* **Virtuelle Netzwerkverbindung für Hub**: Verbindet virtuelle Netzwerke mit einem virtuellen Hub.

Sie können die Routingkonfiguration für eine virtuelle Netzwerkverbindung beim Setup einrichten. Standardmäßig werden alle Verbindungen mit der standardmäßigen Routingtabelle verknüpft und an diese übergeben.

### <a name="association"></a><a name="association"></a>Zuordnung

Jede Verbindung ist einer Routingtabelle zugeordnet. Durch die Zuordnung einer Verbindung zu einer Routingtabelle kann der Datenverkehr an das in der Routingtabelle als Routen angegebene Ziel gesendet werden. Die Routingkonfiguration der Verbindung zeigt die zugehörige Routingtabelle an.  Mehrere Verbindungen können derselben Routingtabelle zugeordnet werden. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen sind derselben (standardmäßigen) Routingtabelle zugeordnet.

Standardmäßig sind alle Verbindungen einer **Standardroutingtabelle** in einem virtuellen Hub zugeordnet. Jeder virtuelle Hub verfügt über eine eigene Standardroutingtabelle, die bearbeitet werden kann, um eine oder mehrere statische Routen hinzuzufügen. Statisch hinzugefügte Routen haben Vorrang vor dynamisch gelernten Routen für dieselben Präfixe.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Zuordnung"lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

### <a name="propagation"></a><a name="propagation"></a>Weitergabe

Verbindungen geben Routen dynamisch an eine Routingtabelle weiter. Bei einer VPN-Verbindung, einer ExpressRoute-Verbindung oder einer P2S-Konfigurationsverbindung werden die Routen mithilfe von BGP vom virtuellen Hub an den lokalen Router weitergegeben. Routen können an eine oder mehrere Routingtabellen weitergegeben werden.

Für jeden virtuellen Hub ist auch eine **Keine-Routingtabelle** verfügbar. Die Weitergabe an die Keine-Routingtabelle bedeutet, dass von der Verbindung aus keine Routen weitergegeben werden müssen. VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an denselben Satz von Routingtabellen weiter.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Weitergabe":::

### <a name="labels"></a><a name="labels"></a>Bezeichnungen

Bezeichnungen sind ein Verfahren zum logischen Gruppieren von Routingtabellen. Dies ist insbesondere bei der Weitergabe von Routen von Verbindungen zu mehreren Routingtabellen hilfreich. Die **Standardroutingtabelle** weist beispielsweise eine integrierte Bezeichnung mit dem Namen „Standard“ auf. Wenn Benutzer Verbindungsrouten an die Bezeichnung „Standard“ weitergeben, gilt sie automatisch für alle Standardroutingtabellen über jeden Hub im virtuellen WAN.

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Konfigurieren statischer Routen in einer virtuellen Netzwerkverbindung

Die Konfiguration statischer Routen bietet einen Mechanismus zum Steuern des Datenverkehrs über die IP-Adresse eines nächsten Hops, bei dem es sich um ein virtuelles Netzwerkgerät (NVA) handeln könnte, das in einem an einen virtuellen Hub angefügten Spoke-VNet bereitgestellt wird. Die statische Route besteht aus einem Routennamen, einer Liste von Zielpräfixen und einer IP-Adresse des nächsten Hops.

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>Routingtabellen für bereits vorhandene Routen

Routingtabellen verfügen jetzt über Features für die Zuordnung und Verteilung. Wenn eine Routingtabelle bereits vorhanden ist, verfügt sie nicht über diese Features. Falls bei Ihnen im Rahmen des Hub-Routings bereits vorhandene Routen genutzt werden und Sie diese neuen Funktionen nutzen möchten, sollten Sie Folgendes beachten:

* **Kunden mit Virtual WAN vom Typ „Standard“ mit bereits vorhandenen Routen auf dem virtuellen Hub**:

   Wenn Sie bereits über vorhandene Routen im Routing-Abschnitt für den Hub im Azure-Portal verfügen, müssen Sie diese zuerst löschen und dann versuchen, neue Routentabellen zu erstellen (verfügbar im Abschnitt „Routentabellen“ für den Hub im Azure-Portal).

* **Kunden mit Virtual WAN vom Typ „Basic“ mit bereits vorhandenen Routen auf dem virtuellen Hub**:

   Wenn Sie bereits über vorhandene Routen im Routing-Abschnitt für den Hub im Azure-Portal verfügen, müssen Sie diese zuerst löschen und dann Virtual WAN vom Typ „Basic“ auf „Standard“ **upgraden**. Weitere Informationen finden Sie unter [Upgrade eines Virtual WAN von Basic auf Standard](upgrade-virtual-wan.md).

## <a name="hub-reset"></a><a name="reset"></a>Hubzurücksetzung

Das **Zurücksetzen** von virtuellen Hubs ist nur im Azure-Portal verfügbar. Durch Zurücksetzen kann der Benutzer fehlerhafte Ressourcen wie Routingtabellen, Hubrouter oder die virtuelle Hubressource selbst wieder in den richtigen Bereitstellungszustand versetzen. Sie sollten den Hub zurücksetzen, bevor Sie sich an Microsoft wenden. Mit diesem Vorgang werden keine Gateways in einem virtuellen Hub zurückgesetzt.

## <a name="additional-considerations"></a><a name="considerations"></a>Weitere Überlegungen

Beachten Sie beim Konfigurieren des Virtual WAN-Routings Folgendes:

* Alle Branchverbindungen (Point-to-Site, Site-to-Site und ExpressRoute) müssen der Standardroutingtabelle zugeordnet werden. Auf diese Weise erlernen alle Branches die gleichen Präfixe.
* Alle Branchverbindungen müssen ihre Routen an dieselbe Gruppe von Routingtabellen weitergeben. Wenn Sie z. B. festlegen, dass Branches an die Standardroutingtabelle weitergeben sollen, muss diese Konfiguration über alle Branches konsistent sein. Somit können alle Verbindungen, die der Standardroutingtabelle zugeordnet sind, alle Branches erreichen.
* Das Routing zwischen Branches über Azure Firewall wird derzeit nicht unterstützt.
* Wenn Sie Azure Firewall in mehreren Regionen verwenden, müssen alle virtuellen Spoke-Netzwerke derselben Routingtabelle zugeordnet werden. Beispielsweise ist es nicht möglich, dass eine Teilmenge der VNETs Azure Firewall durchlaufen, während andere VNETs Azure Firewall im selben virtuellen Hub umgehen.
* Eine einzelne IP-Adresse für den nächsten Hop kann pro VNET-Verbindung konfiguriert werden.
* Alle Informationen im Zusammenhang mit der Route 0.0.0.0/0 sind auf die Routingtabelle des lokalen Hubs beschränkt. Diese Route wird nicht über Hubs weitergegeben.
## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Konfiguration des Routings finden Sie unter [Konfigurieren des Routings für virtuelle Hubs](how-to-virtual-hub-routing.md).
* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
