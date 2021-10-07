---
title: Informationen zu den Preisen von Virtual WAN
titleSuffix: Azure Virtual WAN
description: In diesem Artikel werden allgemeine Fragen zu den Preisen von Virtual WAN beantwortet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: cherylmc
ms.custom: references_pricing
ms.openlocfilehash: 853005cdc9e44357661cdb0b3b50e7f6ba5a0bbf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730093"
---
# <a name="about-virtual-wan-pricing"></a>Informationen zu den Preisen von Virtual WAN

Azure Virtual WAN ist ein Netzwerkdienst, der viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche vereint. Diese Funktionen umfassen Zweigestellenverbindungen (über die Konnektivitätsautomatisierung von Virtual WAN-Partnereinrichtungen wie SD-WAN oder VPN CPE), Site-to-Site-VPN-Konnektivität, Remotebenutzer-VPN (Point-to-Site), private Konnektivität (ExpressRoute), Konnektivität zwischen Clouds (transitive Konnektivität für virtuelle Netzwerke), VPN ExpressRoute-Interkonnektivität, Routing, Azure Firewall und Verschlüsselung für private Konnektivität.

In diesem Artikel werden drei häufig bereitgestellte Szenarios im Zusammenhang mit Azure Virtual WAN sowie typische Preisschätzungen für die Bereitstellungen basierend auf den aufgeführten Preisen beschrieben. Darüber hinaus kann es viele andere Szenarios geben, in denen Virtual WAN nützlich sein kann.

> [!IMPORTANT]
> Die in diesem Artikel gezeigten Preise sind nur für Beispielzwecke vorgesehen. 
>  * Die Preise können sich jederzeit ändern. Aktuelle Preisinformationen finden Sie auf der Seite [Virtual WAN – Preise](https://azure.microsoft.com/pricing/details/virtual-wan/). 
>  * Gebühren zwischen Hubs (Hub-to-Hub) werden auf der Preisseite für Virtual WAN nicht angezeigt, da die Preise den Gebühren für regionsübergreifende Datenübertragungen (gleicher Kontinent oder interkontinental) unterliegen. Weitere Informationen finden Sie unter [Bandbreite – Preise](https://azure.microsoft.com/pricing/details/bandwidth/).
>

## <a name="pricing-components"></a><a name="pricing"></a>Preiskomponenten

Die folgende Abbildung zeigt die typischen Datenrouten in einem Netzwerk mit Virtual WAN und die verschiedenen Preiskomponenten pro Stunde und GB.

:::image type="content" source="./media/pricing-concepts/data-routes.png" alt-text="Abbildung der Datenrouten" lightbox="./media/pricing-concepts/data-routes.png":::

| Wert | Szenario| Stündliche Kosten | Kosten pro GB |
| --- | --- | --- | --- |
| 1 | Datenübertragung von einem Spoke-VNet an einen Site-to-Site-VPN-Branch über einen vWAN-Standardhub in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + S2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + S2S-VPN-Verbindungseinheit (0,05 USD/Stunde) = 0,561 USD/Stunde|VNet-Peering (ausgehend) (0,01 USD/GB) + Standard ausgehende Zone 1 (0,087 USD/GB) = 0,097 USD/GB|
| 1'|Datenübertragung von einem Site-to-Site-VPN-Branch an ein Spoke-VNet über einen vWAN-Standardhub in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + S2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + S2S-VPN-Verbindungseinheit (0,05 USD/Stunde) = 0,561 USD/Stunde|VNet-Peering (eingehend) (0,01 USD/GB) = 0,01 USD/GB|
|2|Datenübertragung von einem Site-to-Site-VPN-Branch über einen vWAN-Standardhub an einen anderen Site-to-Site-VPN-Branch in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + S2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + S2S-VPN-Verbindungseinheit (2 × 0,05 USD/Stunde) = 0,611 USD/Stunde|Standard ausgehende Zone 1 (0,087 USD/GB) = 0,087 USD/GB|
|2'|Datenübertragung von einem Site-to-Site-VPN-Branch über einen vWAN-Standardhub an ExpressRoute, verbunden mit einem Rechenzentrum/Hauptsitz in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + S2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + S2S-VPN-Verbindungseinheit (0,05 USD/Stunde) + ExpressRoute-Skalierungseinheit (0,42 USD/Stunde) + ExpressRoute-Verbindungseinheit (0,05 USD/Stunde) = 1,03 USD/Stunde|Ausgehender ExpressRoute-Datenverkehr, der nach den Verbrauchseinheiten für Zone 1 berechnet wird (0,025 USD/GB) = 0,025 USD/GB|
|3|Datenübertragung von einem Site-to-Site-VPN-Branch über einen vWAN-Standardhub an ExpressRoute, verbunden mit einem Rechenzentrum/Hauptsitz in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + S2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + S2S-VPN-Verbindungseinheit (0,05 USD/Stunde) + ExpressRoute-Skalierungseinheit (0,42 USD/Stunde) + ExpressRoute-Verbindungseinheit (0,05 USD/Stunde) = 1,03 USD/Stunde|Ausgehender ExpressRoute-Datenverkehr, der nach den Verbrauchseinheiten für Zone 1 berechnet wird (0,025 USD/GB) = 0,025 USD/GB|
|4|Datenübertragung von einem Spoke-VNet an ein mit ExpressRoute verbundenes Rechenzentrum/einen Hauptsitz über einen vWAN-Standardhub in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + ExpressRoute-Skalierungseinheit (0,42 USD/Stunde) + ExpressRoute-Verbindungseinheit (0,05 USD/Stunde) = 0,72 USD/Stunde|VNet-Peering (ausgehend) (0,01 USD/GB) + ausgehender ExpressRoute-Datenverkehr, der nach den Verbrauchseinheiten für Zone 1 berechnet wird (0,025 USD/GB) = 0,035 USD/GB|
|4'|Datenübertragung von einem mit ExpressRoute verbundenen Rechenzentrum/Hauptsitz an ein Spoke-VNet über einen vWAN-Standardhub in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) + ExpressRoute-Skalierungseinheit (0,42 USD/Stunde) + ExpressRoute-Verbindungseinheit (0,05 USD/Stunde) = 0,72 USD/Stunde|VNet-Peering (eingehend) (0,01 USD/GB) = 0,01 USD/GB|
|4"|Datenübertragung von einem mit ExpressRoute verbundenen Rechenzentrum/Hauptsitz an ein Remote-Spoke-VNet über einen vWAN-Standardhub in der Region „Europa“|Bereitstellungsstunden (2 × 0,25 USD/Stunde) + ExpressRoute-Skalierungseinheit (0,42 USD/Stunde) + ExpressRoute-Verbindungseinheit (0,05 USD/Stunde) = 0,97 USD/Stunde|VNet-Peering (eingehend) (0,01 USD/GB) + Hubdatenverarbeitung (Europa) (0,02 USD/GB) + regionsübergreifende Datenübertragung („USA, Osten“ nach „Europa“) (0,05 USD/GB) = 0,08 USD/GB|
|5|Datenübertragung von einem Spoke-VNet an ein anderes Spoke-VNet über den vWAN-Standardhub in der Region „USA, Osten“|Bereitstellungsstunden (0,25 USD/Stunde) = 0,25 USD/Stunde|VNet-Peering (ausgehend + eingehend) (2 × 0,01 USD/GB) + Hubdatenverarbeitung (0,02 USD/GB) = 0,04 USD/ GB|
|6|Datenübertragung von einem Spoke-VNet, das mit einem Hub in der Region „USA, Osten“ verbunden ist, an ein anderes Spoke-VNet in der Region „Europa“ (einer anderen Region), das mit einem Hub in der Region „Europa“ verbunden ist|Bereitstellungsstunden (2 × 0,25 USD/Stunde) = 0,50 USD/Stunde|VNet-Peering (ausgehend + eingehend) (2 × 0,01 USD/GB) + Hubdatenverarbeitung (2 × 0,02 USD/GB) + regionsübergreifende Datenübertragung („USA, Osten“ nach „Europa“) (0,05 USD/GB) = 0,11 USD/ GB|
|7|Datenübertragung von einem Spoke-VNet an ein Benutzer-VPN (Point-to-Site) über einen vWAN-Standardhub in der Region „Europa“|Bereitstellungsstunden (0,25 USD/Stunde) + P2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + P2S-VPN-Verbindungseinheit (0,0125 USD/Stunde) = 0,524 USD/Stunde|VNet-Peering (ausgehend) (0,01 USD/GB) + Standard ausgehende Zone 1 (0,087 USD/GB) = 0,097 USD/GB|
|7'|Datenübertragung von einem Benutzer-VPN (Point-to-Site) an ein Spoke-VNet über einen vWAN-Standardhub in der Region „Europa“|Bereitstellungsstunden (0,25 USD/Stunde) + P2S-VPN-Skalierungseinheit (0,261 USD/Stunde) + P2S-VPN-Verbindungseinheit (0,0125 USD/Stunde) = 0,524 USD/Stunde|VNet-Peering (eingehend) (0,01 USD/GB) = 0,01 USD/GB|
|8|Datenübertragung von einem SD-WAN-Branch über ein virtuelles Netzwerkgerät im Hub in der Region „Singapur“ an einen anderen SD-WAN-Branch in derselben Region|Bereitstellungsstunden vWAN-Standardhub (0,25 USD/Stunde) + NVA-Infrastruktureinheit (0,25 USD/Stunde) = 0,50 USD/Stunde<br> *Es können zusätzliche Lizenzierungsgebühren von Drittanbietern anfallen.|Standard ausgehende Zone 2 (0,12 USD/GB) = 0,12 USD/GB|

## <a name="common-topology-scenarios"></a><a name="topologies"></a>Allgemeine Topologieszenarios

### <a name="microsoft-global-backbone-wan"></a><a name="global"></a>Globales Backbone-WAN von Microsoft

In diesem Szenario erstellen Sie global vollständig mit dem Internet verbundene, automatische, regionale Hubs, die als Microsoft-Backbone für die globale Datenverkehrskonnektivität dienen. Die typische Konnektivität umfasst Endpunkte wie Branches (Site-to-Site-VPN oder SD-WAN), Remotebenutzer (Point-to-Site-VPN) und private Verbindungen (ExpressRoute). Dies hängt davon ab, ob das Backbone-WAN von Microsoft Datenverkehr global übertragen kann.

**Abbildung zu Szenario 1: Globales Backbone-WAN von Microsoft**

:::image type="content" source="./media/pricing-concepts/global-backbone.png" alt-text="Abbildung des globalen Backbone-WAN von Microsoft" lightbox="./media/pricing-concepts/global-backbone.png":::

#### <a name="alternatives"></a>Alternativen

* Sie können auch einen geschützten vWAN-Hub (einschließlich Firewall) verwenden, um zu einem zentralen Punkt für Routing- und Sicherheitsanforderungen für jede globale Region zu werden.

### <a name="software-defined-wide-area-network-sd-wan"></a><a name="sdwan"></a>Softwaredefiniertes SD-WAN (Wide Area Network)

In diesem Szenario verwenden Speicher, die auf die SD-WAN-Technologie umgestellt werden, vWAN-Hubs für die automatisierte Speicherbeendigung, um auf Ressourcen in Azure und wieder lokal (Rechenzentrum) zuzugreifen. Die Speicher sind über VPN-Tunnel verbunden, um Datenverkehr sicher über das Internet über den Hub an das lokale Rechenzentrum zu senden oder auf freigegebene Apps in Azure zuzugreifen.

**Abbildung zu Szenario 2: Softwaredefiniertes SD-WAN (Wide Area Network)**

:::image type="content" source="./media/pricing-concepts/sd-wan.png" alt-text="Abbildung des SD-WAN" lightbox="./media/pricing-concepts/sd-wan.png":::

#### <a name="alternatives"></a>Alternativen

* Sie können ein virtuelles Netzwerkgerät eines Drittanbieters im Hub verwenden und dieses mit den Einzelhandelsketten und Zentralen verbinden.

* Sie können auch einen geschützten vWAN-Hub (einschließlich Firewall) verwenden, um zu einem zentralen Punkt für Routing- und Sicherheitsanforderungen zu werden.

### <a name="remote-user-connectivity"></a><a name="remote"></a>Remotebenutzerkonnektivität

In diesem Szenario werden Remotebenutzersitzungen auf vWAN-Hubs beendet. Dies können Remotebenutzersitzungen und/oder Azure Virtual Desktop-Sitzungen aus virtuellen Netzwerken sein. Derzeit werden im Zusammenhang mit jedem Hub 100.000 Benutzer*innen unterstützt.

Die folgende Abbildung zeigt ein Point-to-Site-VPN über VNet-Verbindungen für verschlüsselten Datenverkehr über Tunnel zwischen den Spoke-VNETs und dem vWAN-Hub. Sie können auch VNet-Peeringverbindungen zwischen verschiedenen Spoke-VNets für die direkte Konnektivität verwenden. Ein Beispiel hierfür ist der Einsatz zwischen gemeinsam genutzten VNets und VDI-Spoke-VNets.

**Abbildung zu Szenario 3: Remotebenutzerkonnektivität**

:::image type="content" source="./media/pricing-concepts/remote.png" alt-text="Abbildung der Remotebenutzerkonnektivität" lightbox="./media/pricing-concepts/remote.png":::

## <a name="data-flow-calculations"></a>Datenflussberechnungen

> [!IMPORTANT]
> Die in diesem Artikel gezeigten Preise dienen nur zu Beispielzwecken und können sich ändern. Die aktuellen Preise finden Sie auf der Seite [Virtual WAN – Preise](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

### <a name="microsoft-global-backbone-wan-calculation"></a>Berechnung zum globalen Backbone-WAN von Microsoft

In diesem Szenario wurde davon ausgegangen, dass wie im Folgenden gezeigt insgesamt 8 TB Daten über die vWAN-Hubs durch das globale Netzwerk fließen. Die Gesamtkosten für die Datenübertragung belaufen sich hierfür auf 600 US-Dollar (Summe aller Datenflusskosten in der Abbildung unten, unter der Annahme der Verbindungseinheitengebühren für ExpressRoute), und die Gesamtkosten für den Hub (3 Skalierungseinheiten + 3 Verbindungseinheiten (ER) + 3 Hubbereitstellungen) betragen 1.534 US-Dollar.

**Abbildung zu Szenario 1: Berechnung zum globalen Backbone-WAN von Microsoft**

:::image type="content" source="./media/pricing-concepts/global-backbone-pricing.png" alt-text="Abbildung zu den Preisen für das Microsoft-Backbone" lightbox="./media/pricing-concepts/global-backbone-pricing.png":::

| Wert | Berechnung |
| --- | --- |
|S2S-VPN-Hub „Singapur“ |(1 S2S-VPN-Skalierungseinheit (0,361 USD/Stunde) + 1 Verbindungseinheit (0,05 USD/Stunde)) × 730 Stunden = 300 USD pro Monat|
|ExpressRoute-Hub „USA, Osten“ |(1 ER-Skalierungseinheit (0,42 USD/Stunde) + 1 Verbindungseinheit (0,05 USD/Stunde)) × 730 Stunden = 343 USD pro Monat|
|ExpressRoute-Hub „Europa“|(1 ER-Skalierungseinheit (0,42 USD/Stunde) + 1 Verbindungseinheit (0,05 USD/Stunde)) × 730 Stunden = 343 USD pro Monat|
|Kosten für die Standardhubbereitstellung |3 Hubs × 730 Stunden × 0,25 USD/Stunde = 548 USD pro Monat|
|**Gesamt**|**1.534 USD** pro Monat |

### <a name="software-defined-wide-area-network-sd-wan-calculation"></a>Berechnung zum softwaredefinierten SD-WAN (Wide Area Network)

In diesem Szenario wurde davon ausgegangen, dass wie im Folgenden gezeigt insgesamt 12 TB Daten durch den vWAN-Hub in der Region „USA, Osten“ fließen. Die Gesamtkosten für die Datenübertragung belaufen sich auf 434 US-Dollar (Summe aller unten gezeigten Datenflusskosten, einschließlich Gebühren für die Hubverarbeitung, Peering, Bandbreite und Datenübertragungskosten für ER-Verbrauchseinheiten), und die Gesamtkosten für den Hub (2 Skalierungseinheiten + 3 Verbindungseinheiten (2 S2S, 1 ER) + 1 Hubbereitstellung) betragen 863 US-Dollar.

**Abbildung zu Szenario 2: Berechnung zum softwaredefinierten SD-WAN (Wide Area Network)**

:::image type="content" source="./media/pricing-concepts/sd-wan-pricing.png" alt-text="Abbildung zu den Preisen für das SD-WAN" lightbox="./media/pricing-concepts/sd-wan-pricing.png":::

| Wert | Berechnung |
| --- | --- |
|S2S-VPN (Branches) |(1 S2S-VPN-Skalierungseinheit (0,361 USD/Stunde) + 2 Verbindungseinheiten (2 × 0,05 USD/Stunde)) × 730 Stunden = 337 USD pro Monat|
|ExpressRoute-Hub (Hauptsitz) |(1 ER-Skalierungseinheit (0,42 USD/Stunde) + 1 Verbindungseinheit (0,05 USD/Stunde)) × 730 Stunden = 343 USD pro Monat|
|Kosten für die Standardhubbereitstellung|1 Hub × 730 Stunden × 0.25/Stunde = 183 USD pro Monat|
|**Gesamt**|**863 USD** pro Monat|

### <a name="remote-user-connectivity-calculation"></a>Berechnung zur Remotebenutzerkonnektivität

In diesem Szenario wurde davon ausgegangen, dass insgesamt 15 TB Daten durch das Netzwerk in der Region „USA, Osten“ fließen. Die Gesamtkosten für die Datenübertragung belaufen sich auf 405 US-Dollar (einschließlich Hubverarbeitungsgebühren, Peering, Bandbreite und Datenübertragungskosten für ER-Verbrauchseinheiten), und die Gesamtkosten für den Hub (2 Skalierungseinheiten + 2 Verbindungseinheiten + 2 Hubbereitstellungen) betragen 708 US-Dollar.

**Abbildung zu Szenario 3: Berechnung zur Remotebenutzerkonnektivität**
    
:::image type="content" source="./media/pricing-concepts/remote-pricing.png" alt-text="Abbildung zu den Preisen für Remotebenutzer" lightbox="./media/pricing-concepts/remote-pricing.png":::

| Wert | Berechnung |
| --- | --- |
|ExpressRoute-Hub (Hauptsitz) |(1 ER-Skalierungseinheit (0,42 USD/Stunde) + 1 Verbindungseinheit (0,05 USD/Stunde)) × 730 Stunden = 343 USD pro Monat |
|Kosten für die Standardhubbereitstellung| 2 Hubs × 730 Stunden × 0.25 USD/Stunde = 365 USD pro Monat |
|**Gesamt** |**708 USD** pro Monat |

## <a name="pricing-faq"></a>Preise FAQ

> [!IMPORTANT]
> Die in diesem Artikel gezeigten Preise dienen nur zu Beispielzwecken und können sich ändern. Die aktuellen Preise finden Sie auf der Seite [Virtual WAN – Preise](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Was ist eine Skalierungseinheit?

Eine **Skalierungseinheit** stellt die Einheit für die aggregierte Kapazität von Site-to-Site (S2S), Point-to-Site (P2S) und ExpressRoute (ER) in einem virtuellen Hub dar. Beispiel:

* **1 S2S-VPN-Skalierungseinheit** impliziert ein VPN-Gateway mit einer Gesamtkapazität von 500 MBit/s (aus Gründen der Resilienz werden duale Instanzen bereitgestellt) in einem virtuellen Hub, das 0,361 USD/Stunde kostet.

* **1 ER-Skalierungseinheit** impliziert ein Gateway mit insgesamt 2 GBit/s in einem virtuellen Hub, das 0,42 USD/Stunde kostet.

* **5 ER-Skalierungseinheiten** implizieren ein Gateway mit insgesamt 10 GBit/s in einem VNet eines virtuellen Hubs mit einem Preis von 0,42 USD × 5/Stunde. Von der 6. bis zur 10. Skalierungseinheit wird der ER-Preis in Schritten von 0,25 USD/Stunde erhöht.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Was ist eine Verbindungseinheit?

Eine **Verbindungseinheit** gilt für beliebige lokale/Nicht-Microsoft-Endpunkte, die eine Verbindung mit Azure-Gateways herstellen. Bei einem Site-to-Site-VPN impliziert dieser Wert Verzweigungen. Bei einem Benutzer-VPN (Point-to-Site) impliziert dieser Wert Remotebenutzer. Bei ExpressRoute impliziert dieser Wert Verbindungen von ExpressRoute-Leitungen.<br>Beispiel:

* Eine Branchverbindung mit einem Azure-VPN in einem virtuellen Hub kostet 0,05 USD/Stunde. Demnach kosten 100 Branchverbindungen mit einer Verbindung mit einem virtuellen Azure-Hub 0,05 USD × 100/Stunde.

* Zwei ExpressRoute-Leitungsverbindungen mit einem virtuellen Hub kosten 0,05 USD × 2/Stunde.

* Drei Remotebenutzerverbindungen mit dem virtuellen Azure-Hub-P2S-Gateway kosten 0,03 USD × 3/Stunde.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Wie werden die Gebühren für die Datenübertragung berechnet?

* In Azure eingehender Datenverkehr wird nicht berechnet. Von Azure ausgehender Datenverkehr (über VPN-, ExpressRoute- oder Point-to-Site-Benutzer-VPN-Verbindungen) unterliegt den [Standardgebühren für Azure-Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/) oder im Fall von ExpressRoute den [ExpressRoute-Preisen](https://azure.microsoft.com/pricing/details/expressroute/).

* Peeringgebühren fallen an, wenn ein mit einem vWAN-Hub verbundenes VNet Daten sendet oder empfängt. Weitere Informationen finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network/).

* Bei Datenübertragungen zwischen einem Virtual WAN-Hub und einem Virtual Remote-WAN-Remotehub oder -VNET in einer anderen Region als der Quellhub fallen Datenübertragungsgebühren für den Datenverkehr an, der vom Hub ausgeht. Beispiel: Ausgehender Datenverkehr von einem Hub in „USA, Osten“ an einen Hub in „USA, Westen“ wird mit 0,02 USD/GB in Rechnung gestellt. Es fallen keine Kosten für den eingehenden Datenverkehr am Hub in „USA, Westen“ an. Für den gesamten Datenverkehr zwischen den Hubs fallen Gebühren für die regionsübergreifende Datenübertragung (bzw. die Datenübertragung innerhalb eines Kontinents oder die interkontinentale Übertragung) an. Weitere Informationen erhalten Sie in der [Preisübersicht für Azure-Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Worin besteht der Unterschied zwischen einer Standard-Hubgebühr und einer Standard-Hubverarbeitungsgebühr?

Virtual WAN ist in zwei Varianten verfügbar:

* Eine Möglichkeit ist ein **virtuelles WAN vom Typ „Basic“** , in dem Benutzer*innen mehrere Hubs bereitstellen und VPN-Site-to-Site-Verbindungen nutzen können. Ein virtuelles WAN vom Typ „Basic“ verfügt nicht über erweiterte Funktionen wie vollständig mit dem Internet verbundene Hubs, ExpressRoute-Konnektivität, Benutzer-VPN-/Point-to-Site-VPN-Konnektivität, transitive VNet-zu-VNet-Verbindungen, VPN- und ExpressRoute-Transitverbindungen oder Azure Firewall. Für Hubs in einem grundlegenden virtuellen WAN fällt keine Grundgebühr oder Datenverarbeitungsgebühr an.

* Ein **virtuelles WAN vom Typ „Standard“** bietet erweiterte Funktionen wie vollständig mit dem Internet verbundene Hubs, ExpressRoute-Konnektivität, Benutzer-VPN-/Point-to-Site-VPN-Konnektivität, transitive VNet-zu-VNet-Konnektivität, VPN- und ExpressRoute-Transitverbindungen und Azure Firewall. Das gesamte Routing virtueller Hubs erfolgt über einen Router, der mehrere Dienste in einem virtuellen Hub ermöglicht. Für den Hub besteht eine Grundgebühr von 0,25 USD/Stunde. Außerdem fallen für die Datenverarbeitung auf dem Router des virtuellen Hubs für VNET-zu-VNET-Transitverbindungen Gebühren an. Wie unter [Preiskomponenten](#pricing) gezeigt gilt die Gebühr für die Datenverarbeitung im Router des virtuellen Hubs nicht für Branch-zu-Branch-Übertragungen (Szenario 2, 2', 3) oder VNet-zu-Branch-Übertragungen über denselben vWAN-Hub (Szenario 1, 1'). 

## <a name="next-steps"></a>Nächste Schritte

* Aktuelle Preise finden Sie unter [Preise für Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).


