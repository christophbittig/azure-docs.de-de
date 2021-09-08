---
title: Informationen zur Netzwerkadressenübersetzung (NAT) in VPN Gateway
titleSuffix: Azure VPN Gateway
description: Erfahren Sie mehr über die Netzwerkadressenübersetzung (NAT) in einem Azure-VPN, um Netzwerke mit sich überschneidenden Adressräumen zu verbinden.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/22/2021
ms.author: yushwang
ms.openlocfilehash: 2bcf33466b3cbf57a67307961bb1e21e3a810a72
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112759578"
---
# <a name="about-nat-on-azure-vpn-gateway-preview"></a>Informationen zu NAT in Azure VPN Gateway (Vorschau)

Dieser Artikel bietet eine Übersicht über die Unterstützung der Netzwerkadressenübersetzung (Network Address Translation, NAT) in Azure VPN Gateway. NAT definiert den Mechanismus, mit dem eine IP-Adresse in eine andere IP-Adresse in einem IP-Paket übersetzt wird. Es gibt mehrere Szenarien für NAT:

* Verbinden mehrerer Netzwerke mit sich überschneidenden IP-Adressen
* Verbinden von Netzwerken mit privaten IP-Adressen (RFC1918) mit dem Internet (Internet-Breakout)
* Verbinden von IPv6-Netzwerken mit IPv4-Netzwerken (NAT64)

> [!IMPORTANT]
> Die Netzwerkadressenübersetzung in Azure VPN Gateway unterstützt das erste Szenario, um lokale Netzwerke oder Zweigstellen mit einem virtuellen Azure-Netzwerk mit sich überschneidenden IP-Adressen zu verbinden. Internet-Breakout und NAT64 werden **NICHT** unterstützt.

## <a name="overlapping-address-spaces"></a><a name="why"></a>Überlappende Adressräume

Organisationen verwenden für die interne Kommunikation in ihren privaten Netzwerken häufig private IP-Adressen gemäß Definition in RFC1918. Wenn diese Netzwerke per VPN über das Internet oder ein privates WAN verbunden werden, dürfen sich die Adressräume **nicht** überschneiden, da die Kommunikation andernfalls nicht möglich ist. Um zwei oder mehr Netzwerke mit sich überschneidenden IP-Adressen zu verbinden, wird NAT für Gatewaygeräte bereitgestellt, die diese Netzwerke verbinden. 

## <a name="nat-type-static--dynamic"></a><a name="type"></a>NAT-Typ: statisch und dynamisch

Mithilfe von NAT für ein Gatewaygerät werden die Quell- und/oder Ziel-IP-Adressen basierend auf NAT-Richtlinien oder -Regeln übersetzt, um Adressenkonflikte zu vermeiden. Es gibt verschiedene Arten von NAT-Übersetzungsregeln:

* **Statisches NAT**: Regeln für die statische Netzwerkadressenübersetzung definieren eine feste Adresszuordnung. Eine vorgegebene IP-Adresse wird derselben Adresse aus dem Zielpool zugeordnet. Die Zuordnungen für statische Regeln sind zustandslos, da die Zuordnung fest ist.

* **Dynamisches NAT**: Bei Regeln für die dynamische Netzwerkadressenübersetzung kann eine IP-Adresse basierend auf der Verfügbarkeit oder mit einer anderen Kombination aus IP-Adresse und TCP/UDP-Port in verschiedene Ziel-IP-Adressen übersetzt werden. Letzteres wird auch als NAPT (Network Address and Port Translation) bezeichnet. Dynamische Regeln führen zu zustandsbehafteten Übersetzungszuordnungen, die von den Datenverkehrsflüssen zu einem bestimmten Zeitpunkt abhängen.

Ein weiterer Aspekt ist die Größe des Adresspools für die Übersetzung. Wenn die Größe des Zieladresspools mit der des ursprünglichen Adresspools identisch ist, verwenden Sie die statische NAT-Regel, um eine 1:1-Zuordnung in sequenzieller Reihenfolge zu definieren. Wenn der Zieladresspool kleiner ist als der ursprüngliche Adresspool, verwenden Sie eine dynamische NAT-Regel, um die Unterschiede zu berücksichtigen.

> [!IMPORTANT]
> * Die Netzwerkadressenübersetzung in Azure VPN Gateway unterstützt nur statische 1:1-NAT-Regeln. Dynamische NAT-Regeln werden nicht unterstützt.
> * Die Netzwerkadressenübersetzung wird für die folgenden SKUs unterstützt: VpnGw2~5, VpnGw2AZ~5AZ.
> * NAT wird nur für standortübergreifende IPsec-Verbindungen unterstützt. VNet-zu-VNet-Verbindungen oder P2S-Verbindungen werden nicht unterstützt.

## <a name="nat-mode-ingress--egress"></a><a name="mode"></a>NAT-Modus: eingehend und ausgehend

Jede NAT-Regel definiert eine Adresszuordnung oder eine Übersetzungsbeziehung für den entsprechenden Netzwerkadressraum:

* Eingehend: Eine Regel vom Typ **IngressSNAT** ordnet einen lokalen Netzwerkadressraum einem übersetzten Adressraum zu, um eine Adressenüberschneidung zu vermeiden.

* Ausgehend: Eine Regel vom Typ **EgressSNAT** ordnet den Azure-VNet-Adressraum einem anderen übersetzten Adressraum zu. 

Für jede NAT-Regel geben die folgenden zwei Felder die Adressräume vor und nach der Übersetzung an:

* **Interne Zuordnungen**: Dies ist der Adressraum **vor** der Übersetzung. Bei einer Eingangsregel entspricht dieses Feld dem ursprünglichen Adressraum des lokalen Netzwerks. Bei einer Ausgangsregel ist dies der ursprüngliche VNet-Adressraum.

* **Externe Zuordnungen**: Dies ist der Adressraum **nach** der Übersetzung für lokale Netzwerke (eingehend) oder das VNet (ausgehend). Wenn verschiedene Netzwerke mit einem Azure-VPN-Gateway verbunden sind, dürfen sich die Adressräume für sämtliche **externe Zuordnungen** nicht gegenseitig oder mit den Netzwerken überschneiden, die ohne NAT verbunden sind.

## <a name="nat-and-routing"></a><a name="routing"></a>NAT und Routing

Sobald eine NAT-Regel für eine Verbindung definiert wurde, ändert sich der effektive Adressraum für die Verbindung mit der Regel. Wenn BGP für das Azure-VPN-Gateway aktiviert ist, wählen Sie die Option „BGP-Routenübersetzung aktivieren“ aus, um die für Verbindungen mit NAT-Regeln erlernten und angekündigten Routen automatisch zu konvertieren:

* Gelernte Routen: Die Zielpräfixe der Routen, die über eine Verbindung mit den IngressSNAT-Regeln gelernt wurden, werden von den Präfixen für die interne Zuordnung (Prä-NAT) in die Präfixe der externen Zuordnung (Post-NAT) dieser Regeln übersetzt.

* Angekündigte Routen: Das Azure-VPN-Gateway kündigt die Präfixe für die externe Zuordnung (Post-NAT) der EgressSNAT-Regeln für den VNet-Adressraum und die gelernten Routen mit Post-NAT-Adresspräfixen von anderen Verbindungen an.

* Überlegungen zur BGP-Peer-IP-Adresse für ein lokales Netzwerk mit Netzwerkadressenübersetzung:
   * APIPA-Adresse (169.254.0.1 bis 169.254.255.254): Es wird keine NAT-Regel benötigt. Geben Sie die APIPA-Adresse direkt im Gateway des lokalen Netzwerks an.
   * Nicht-APIPA-Adresse: Geben Sie die **übersetzte** bzw. **Post-NAT**-IP-Adresse für das Gateway des lokalen Netzwerks an. Verwenden Sie die **übersetzten** bzw. **Post-NAT**-Azure-BGP-IP-Adressen zum Konfigurieren der lokalen VPN-Router an. Stellen Sie sicher, dass die NAT-Regeln für die beabsichtigte Übersetzung definiert sind.

> [!NOTE]
> Die gelernten Routen für Verbindungen ohne IngressSNAT-Regeln werden nicht konvertiert. Die für Verbindungen ohne EgressSNAT-Regeln angekündigten VNet-Routen werden ebenfalls nicht konvertiert.
>

## <a name="nat-example"></a><a name="example"></a>NAT-Beispiel

Das folgende Diagramm zeigt ein Beispiel für Azure-VPN-NAT-Konfigurationen:

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="NAT-Diagramm" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

Das Diagramm zeigt ein Azure-VNet und zwei lokale Netzwerke, alle mit dem Adressraum 10.0.1.0/24. Erstellen Sie die folgenden Regeln, um diese beiden Netzwerke mit dem Azure-VNet und dem VPN-Gateway zu verbinden:

* IngressSNAT-Regel 1: Diese Regel übersetzt den lokalen Adressraum 10.0.1.0/24 in 100.0.2.0/24.

* IngressSNAT-Regel 2: Diese Regel übersetzt den lokalen Adressraum 10.0.1.0/24 in 100.0.3.0/24.

* EgressSNAT-Regel 1: Diese Regel übersetzt den VNet-Adressraum 10.0.1.0/24 in 100.0.1.0/24.

Im Diagramm umfasst jede Verbindungsressource die folgenden Regeln:

* Verbindung 1 (VNet Zweigstelle 1):
    * IngressSNAT-Regel 1
    * EgressSNAT-Regel 1

* Verbindung 2 (VNet Zweigstelle 2)
    * IngressSNAT-Regel 2
    * EgressSNAT-Regel 1

Basierend auf den Regeln, die den Verbindungen zugeordnet sind, finden Sie hier die Adressräume für jedes Netzwerk:

| Netzwerk  | Ursprünglich    | Übersetzt   |
| ---      | ---         | ---          |
| VNet     | 10.0.1.0/24 | 100.0.1.0/24 |
| Zweigstelle 1 | 10.0.1.0/24 | 100.0.2.0/24 |
| Verzweigung 2 | 10.0.1.0/24 | 100.0.3.0/24 |

Das nachstehende Diagramm zeigt ein IP-Paket von Zweigstelle 1 zum VNet, vor und nach der NAT-Übersetzung:

:::image type="content" source="./media/nat-overview/nat-packet.png" alt-text="Diagramm: vor und nach der NAT-Übersetzung" lightbox="./media/nat-overview/nat-packet.png" border="false":::

> [!IMPORTANT]
> Eine einzelne SNAT-Regel definiert die Übersetzung für **beide Richtungen** eines bestimmten Netzwerks:
>
> * Eine IngressSNAT-Regel definiert die Übersetzung der Quell-IP-Adressen, die aus dem lokalen Netzwerk beim Azure-VPN-Gateway **eingehen**. Außerdem wird die Übersetzung der Ziel-IP-Adressen verarbeitet, die das VNet verlassen und an dasselbe lokale Netzwerk gehen.
> * Eine EgressSNAT-Regel definiert die Übersetzung der Quell-IP-Adressen, die das Azure-VPN-Gateway verlassen und an die lokalen Netzwerke gehen. Außerdem wird die Übersetzung der Ziel-IP-Adressen für Pakete verarbeitet, die über diese Verbindungen mit der EgressSNAT-Regel beim VNet eingehen.
> * In beiden Fällen werden keine **DNAT**-Regeln benötigt.

## <a name="nat-configuration"></a><a name="config"></a>NAT-Konfiguration

Um die NAT-Konfiguration wie oben gezeigt zu implementieren, erstellen Sie zunächst die NAT-Regeln in Ihrem Azure-VPN-Gateway und anschließend die Verbindungen mit den entsprechenden zugeordneten NAT-Regeln. Die Schritte zum Konfigurieren von NAT für standortübergreifende Verbindungen finden Sie unter [Konfigurieren von NAT für Azure-VPN-Gateways](nat-howto.md).

## <a name="nat-faq"></a><a name="faq"></a>Häufig gestellte Fragen zu NAT

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Die Schritte zum Konfigurieren von NAT für standortübergreifende Verbindungen finden Sie unter [Konfigurieren von NAT für Azure-VPN-Gateways](nat-howto.md).