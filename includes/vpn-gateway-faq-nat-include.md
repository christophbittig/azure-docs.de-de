---
title: Datei einfügen
description: Datei einfügen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a237122aaeba57d8c3aa99274a0cc41232729eca
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676932"
---
### <a name="is-nat-supported-on-all-azure-vpn-gateway-skus"></a>Wird NAT von allen Azure-VPN-Gateway-SKUs unterstützt?

NAT wird bei „VpnGw2~5“ und „VpnGw2AZ~5AZ“ unterstützt.

### <a name="can-i-use-nat-on-vnet-to-vnet-or-p2s-connections"></a>Kann ich NAT bei VNet-to-VNet- oder P2S-Verbindungen verwenden?

Nein, NAT wird nur für standortübergreifende **IPsec**-Verbindungen unterstützt.

### <a name="how-many-nat-rules-can-i-use-on-a-vpn-gateway"></a>Wie viele NAT-Regeln kann ich auf einem VPN-Gateway verwenden?

Sie können bis zu 100 NAT-Regeln (Ein- und Ausgangsregeln kombiniert) auf einem VPN-Gateway erstellen.

### <a name="is-nat-applied-to-all-connections-on-a-vpn-gateway"></a>Wird NAT auf alle Verbindungen an einem VPN-Gateway angewendet?

NAT wird auf die Verbindungen mit NAT-Regeln angewendet. Wenn eine Verbindung keine NAT-Regel aufweist, wird NAT für diese Verbindung nicht wirksam. Auf demselben VPN-Gateway können einige Verbindungen mit NAT und andere Verbindungen ohne NAT zusammenarbeiten.

### <a name="what-types-of-nat-is-supported-on-azure-vpn-gateways"></a>Welche Arten von NAT werden von Azure-VPN-Gateways unterstützt?

Nur statisches 1:1-NAT. Dynamisches NAT oder NAT64 wird NICHT unterstützt.

### <a name="does-nat-work-on-active-active-vpn-gateways"></a>Funktioniert NAT bei VPN-Gateways vom Typ „Aktiv-Aktiv“?

Ja. NAT funktioniert sowohl bei VPN-Gateways vom Typ „Aktiv-Aktiv“ als auch „Aktiv-Standby“.

### <a name="does-nat-work-with-bgp-connections"></a>Funktioniert NAT mit BGP-Verbindungen?

Ja, Sie können BGP mit NAT verwenden. Hier folgen einige wichtige Überlegungen:

* Wählen Sie auf der Konfigurationsseite für NAT-Regeln die Option **BGP-Routenübersetzung aktivieren** aus, um sicherzustellen, dass die erlernten und angekündigten Routen auf der Grundlage der mit den Verbindungen verbundenen NAT-Regeln in Post-NAT-Adresspräfixe (externe Zuordnungen) übersetzt werden. Sie müssen sicherstellen, dass die lokalen BGP-Router genau die Präfixe ankündigen, die in den IngressSNAT-Regeln definiert sind.

* Wenn der lokale VPN-Router APIPA (169.254.x.x) als BGP-Sprecher/Peer-IP verwendet, verwenden Sie die APIPA-Adresse direkt im Feld für die **BGP-Peer-IP-Adresse** des lokalen Netzwerkgateways. Wenn der lokale VPN-Router eine reguläre Nicht-APIPA-Adresse verwendet und diese mit dem VNet-Adressraum oder anderen lokalen Netzwerkräumen kollidiert, stellen Sie sicher, dass die IngressSNAT-Regel die BGP-Peer-IP in eine eindeutige, nicht überlappende Adresse übersetzt und die Post-NAT-Adresse im Feld **BGP-Peer-IP-Adresse** des lokalen Netzwerkgateways ablegt.

### <a name="do-i-need-to-create-the-matching-dnat-rules-for-the-snat-rule"></a>Muss ich die passenden DNAT-Regeln für die SNAT-Regel erstellen?

Nein. Eine einzelne SNAT-Regel definiert die Übersetzung für **beide Richtungen** eines bestimmten Netzwerks:

* Eine IngressSNAT-Regel definiert die Übersetzung der Quell-IP-Adressen, die aus dem lokalen Netzwerk beim Azure-VPN-Gateway **eingehen**. Außerdem wird die Übersetzung der Ziel-IP-Adressen verarbeitet, die das VNet verlassen und an dasselbe lokale Netzwerk gehen.

* Eine EgressSNAT-Regel definiert die Übersetzung der Quell-IP-Adressen des VNets, die das Azure-VPN-Gateway verlassen und an die lokalen Netzwerke gehen. Außerdem wird die Übersetzung der Ziel-IP-Adressen für Pakete verarbeitet, die über diese Verbindungen mit der EgressSNAT-Regel beim VNet eingehen.

* In beiden Fällen werden **keine DNAT**-Regeln benötigt.

### <a name="what-do-i-do-if-my-vnet-or-local-network-gateway-address-space-has-two-or-more-prefixes-can-i-apply-nat-to-all-of-them-or-just-a-subset"></a>Wie muss ich vorgehen, wenn mein VNet oder mein lokaler Netzwerkgatewayadressraum zwei oder mehr Präfixe aufweist? Kann ich NAT auf alle von ihnen anwenden? Oder nur eine Teilmenge?

Sie müssen für jedes Präfix, das Sie für NAT benötigen, eine NAT-Regel erstellen, da jede NAT-Regel nur ein Adresspräfix für NAT enthalten kann. Wenn der Adressraum des lokalen Netzwerkgateways z. B. aus 10.0.1.0/24 und 10.0.2.0/25 besteht, können Sie zwei Regeln erstellen (siehe unten):

* IngressSNAT-Regel 1: Zuordnung von 10.0.1.0/24 zu 100.0.1.0/24
* IngressSNAT-Regel 2: Zuordnung von 10.0.2.0/25 zu 100.0.2.0/25

Die beiden Regeln müssen mit den Präfixlängen der entsprechenden Adresspräfixe übereinstimmen. Das Gleiche gilt für EgressSNAT-Regeln für den VNet-Adressraum.

> [!IMPORTANT]
> Wenn Sie nur eine Regel mit der obigen Verbindung verknüpfen, wird der andere Adressraum **NICHT** übersetzt.

### <a name="can-i-use-different-egresssnat-rules-to-translate-my-vnet-address-space-to-different-prefixes-to-different-on-premises-networks"></a>Kann ich verschiedene EgressSNAT-Regeln verwenden, um meinen VNet-Adressraum in verschiedene Präfixe für verschiedene lokale Netzwerke zu übersetzen?

Ja, Sie können mehrere EgressSNAT-Regeln für denselben VNet-Adressraum erstellen und die EgressSNAT-Regeln auf verschiedene Verbindungen anwenden. Für Verbindungen ohne EgressSNAT-Regel:

### <a name="can-i-use-the-same-ingresssnat-rule-on-different-connections"></a>Kann ich dieselbe IngressSNAT-Regel für verschiedene Verbindungen verwenden?

Ja, dies wird in der Regel verwendet, wenn die Verbindungen für dasselbe lokale Netz bestimmt sind, um Redundanz bereitzustellen. Sie können nicht dieselbe Eingangsregel verwenden, wenn die Verbindungen für verschiedene lokale Netzwerke bestimmt sind.

### <a name="do-i-need-both-ingress-and-egress-rules-on-a-nat-connection"></a>Benötige ich sowohl Eingangs- als auch Ausgangsregeln für eine NAT-Verbindung?

Sie benötigen sowohl Eingangs- als auch Ausgangsregeln für dieselbe Verbindung, wenn der Adressraum des lokalen Netzwerks mit dem Adressraum des VNet überlappt. Wenn der VNet-Adressraum für alle verbundenen Netzwerke eindeutig ist, benötigen Sie die EgressSNAT-Regel für diese Verbindungen nicht. Sie können die Eingangsregeln verwenden, um Adressüberlappungen zwischen den lokalen Netzwerken zu vermeiden.