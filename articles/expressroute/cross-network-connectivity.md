---
title: Netzwerkübergreifende Azure-Verbindungen
description: Auf dieser Seite wird ein Anwendungsszenario für netzwerkübergreifende Verbindungen und Lösungen beschrieben, das auf Azure-Netzwerkfeatures basiert.
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 9e991e31d93e28373df55ae50a3ef7b0a666b879
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353974"
---
# <a name="cross-network-connectivity"></a>Netzwerkübergreifende Verbindungen

Fabrikam Inc. hat eine große Niederlassung und eine umfangreiche Azure-Bereitstellung im Osten der USA. Die Back-End-Konnektivität zwischen den lokalen und Azure-basierten Bereitstellungen wird bei Fabrikam mit ExpressRoute realisiert. Contoso Ltd. verfügt entsprechend über eine Niederlassung und eine Azure-Bereitstellung im Westen der USA (USA, Westen). Die Back-End-Konnektivität zwischen den lokalen und Azure-basierten Bereitstellungen wird bei Contoso mit ExpressRoute realisiert.  

Fabrikam Inc. übernimmt Contoso Ltd. Nach der Fusion möchte Fabrikam die beiden Netzwerke miteinander verbinden. Die folgende Abbildung veranschaulicht das Szenario:

![Das Anwendungsszenario](./media/cross-network-connectivity/premergerscenario.png)

Die gestrichelten Pfeile in der Mitte der obigen Abbildung stellen die gewünschten Netzwerkverbindungen dar. Es sollen drei Arten von übergreifenden Verbindungen verwendet werden: 1) Querverbindung von Fabrikam- und Contoso-VNETs, 2) regionsübergreifende lokale und VNET-Querverbindungen (Verbindung des lokalen Fabrikam-Netzwerks mit dem Contoso-VNET und des lokalen Contoso-Netzwerks mit dem Fabrikam-VNET) und 3) Querverbindung des lokalen Fabrikam- und Contoso-Netzwerks. 

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Contoso Ltd. vor der Übernahme.

![ExpressRoute-Routingtabelle vor der Übernahme (Contoso)](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

Die folgende Tabelle enthält die bestehenden Routen einer VM unter dem Contoso-Abonnement vor der Übernahme. Laut Tabelle ist die VM im VNET neben den Standardkomponenten auch über den VNET-Adressraum und das lokale Contoso-Netzwerk informiert.

![VM-Routen vor der Übernahme (Contoso)](./media/cross-network-connectivity/contosovm-routes-premerger.png)

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Fabrikam Inc. vor der Übernahme.

![ExpressRoute-Routingtabelle vor der Übernahme (Fabrikam)](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

Die folgende Tabelle enthält die bestehenden Routen einer VM unter dem Fabrikam-Abonnement vor der Übernahme. Laut Tabelle ist die VM im VNET neben den Standardkomponenten auch über den VNET-Adressraum und das lokale Fabrikam-Netzwerk informiert.

![VM-Routen vor der Übernahme (Fabrikam)](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

In diesem Artikel gehen wir Schritt für Schritt vor. Es wird beschrieben, wie Sie die gewünschten Querverbindungen mit den folgenden Azure-Netzwerkfeatures erzielen:

* [Peering in virtuellen Netzwerken][Virtual network peering] 
* [ExpressRoute-Verbindungen für virtuelle Netzwerke][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Herstellen von Querverbindungen für VNETs

Durch das Peering virtueller Netzwerke (VNET-Peering) wird beim Verbinden von zwei virtuellen Netzwerken die optimale und beste Netzwerkleistung erzielt. Beim VNET-Peering wird das Peering von zwei VNETs unterstützt, die sich beide in derselben Azure-Region (VNET-Peering) oder in zwei unterschiedlichen Azure-Regionen (globales VNET-Peering) befinden. 

Wir konfigurieren nun das globale VNET-Peering zwischen den VNETs in den Azure-Abonnements von Contoso und Fabrikam. Informationen zur Erstellung des VNET-Peerings zwischen den virtuellen Netzwerken finden Sie im Artikel [Erstellen eines Peerings in virtuellen Netzwerken][Configure VNet peering].

In der folgenden Abbildung ist die Netzwerkarchitektur nach der Konfiguration des globalen VNET-Peerings dargestellt.

![Die Architektur nach dem VNET-Peering](./media/cross-network-connectivity/vnet-peering.png )

In der folgenden Tabelle sind die Routen enthalten, die der VM des Contoso-Abonnements bekannt sind. Achten Sie auf den letzten Eintrag der Tabelle. Dieser Eintrag ist das Ergebnis der Querverbindung der virtuellen Netzwerke.

![VM-Routen nach dem VNET-Peering (Contoso)](./media/cross-network-connectivity/contosovm-routes-peering.png)

In der folgenden Tabelle sind die Routen enthalten, die der VM des Fabrikam-Abonnements bekannt sind. Achten Sie auf den letzten Eintrag der Tabelle. Dieser Eintrag ist das Ergebnis der Querverbindung der virtuellen Netzwerke.

![VM-Routen nach dem VNET-Peering (Fabrikam)](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

Per VNET-Peering werden zwei virtuelle Netzwerke direkt verknüpft (für den Eintrag *VNetGlobalPeering* in den obigen beiden Tabellen ist kein nächster Hop enthalten).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Querverbindung von VNETs in den lokalen Netzwerken

Wir können für eine ExpressRoute-Leitung eine Verbindung mit mehreren virtuellen Netzwerken herstellen. Unter [Einschränkungen bei Abonnements und Diensten][Subscription limits] ist angegeben, für wie viele virtuelle Netzwerke maximal eine Verbindung mit einer ExpressRoute-Leitung hergestellt werden kann. 

Wir stellen eine Verbindung zwischen der ExpressRoute-Leitung von Fabrikam und dem VNET des Contoso-Abonnements sowie entsprechend zwischen der ExpressRoute-Leitung von Contoso und dem VNET des Fabrikam-Abonnements her, um eine Querverbindung zwischen virtuellen Netzwerken und den lokalen Netzwerken zu erzielen. Wir müssen eine Autorisierung erstellen und verwenden, um ein virtuelles Netzwerk mit einer ExpressRoute-Leitung in einem anderen Abonnement zu verbinden.  Lesen Sie den folgenden Artikel: [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung][Connect-ER-VNet].

In der folgenden Abbildung ist die Netzwerkarchitektur nach der Konfiguration der ExpressRoute-Querverbindung mit den virtuellen Netzwerken dargestellt.

![Die Architektur nach der ExpressRoute-Querverbindung](./media/cross-network-connectivity/exr-x-connect.png)

In der folgenden Tabelle ist die Routingtabelle des privaten Peerings der ExpressRoute-Leitung von Contoso Ltd. dargestellt, nachdem per ExpressRoute für die virtuellen Netzwerke eine Querverbindung mit den lokalen Netzwerken hergestellt wurde. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden virtuellen Netzwerken gehören.

![ExpressRoute-Routingtabelle nach Querverbindung von ExR und VNETs (Contoso)](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

In der folgenden Tabelle ist die Routingtabelle des privaten Peerings der ExpressRoute-Leitung von Fabrikam Inc. dargestellt, nachdem per ExpressRoute für die virtuellen Netzwerke eine Querverbindung mit den lokalen Netzwerken hergestellt wurde. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden virtuellen Netzwerken gehören.

![ExpressRoute-Routingtabelle nach Querverbindung von ExR und VNETs (Fabrikam)](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

In der folgenden Tabelle sind die Routen enthalten, die der VM des Contoso-Abonnements bekannt sind. Achten Sie in der Tabelle auf die Einträge vom Typ *Gateway für virtuelle Netzwerke*. Die VM sieht Routen beider lokalen Netzwerke.

![VM-Routen nach Querverbindung von ExR und VNETs (Contoso)](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

In der folgenden Tabelle sind die Routen enthalten, die der VM des Fabrikam-Abonnements bekannt sind. Achten Sie in der Tabelle auf die Einträge vom Typ *Gateway für virtuelle Netzwerke*. Die VM sieht Routen beider lokalen Netzwerke.

![VM-Routen nach Querverbindung von ExR und VNETs (Fabrikam)](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Im Fabrikam- bzw. Contoso-Abonnement können Sie auch über Spoke-VNET-Verbindungen mit dem entsprechenden Hub-VNET verfügen. (In den Architekturdiagrammen dieses Artikels ist das Hub-and-Spoke-Modell nicht dargestellt.) Die Querverbindungen zwischen den Hub-VNET-Gateways und ExpressRoute ermöglicht auch die Kommunikation zwischen Hubs und Spokes im Osten und Westen.
>

## <a name="cross-connecting-on-premises-networks"></a>Herstellen von Querverbindungen zwischen lokalen Netzwerken

ExpressRoute Global Reach ermöglicht die Konnektivität zwischen lokalen Netzwerken, die mit unterschiedlichen ExpressRoute-Leitungen verbunden sind. Wir konfigurieren Global Reach zwischen den ExpressRoute-Leitungen von Contoso und Fabrikam. Da sich die ExpressRoute-Leitungen unter verschiedenen Abonnements befinden, müssen wir eine Autorisierung erstellen und verwenden. Der Artikel [Konfigurieren von ExpressRoute Global Reach][Configure Global Reach] enthält eine Schritt-für-Schritt-Anleitung.

In der folgenden Abbildung ist die Netzwerkarchitektur nach der Konfiguration von Global Reach dargestellt.

![Architektur nach der Konfiguration von Global Reach](./media/cross-network-connectivity/globalreach.png)

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Contoso Ltd. nach der Konfiguration von Global Reach. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden lokalen Netzwerken gehören. 

![ExpressRoute-Routingtabelle nach Global Reach (Contoso)](./media/cross-network-connectivity/contosoexr-rt-gr.png)

Die folgende Tabelle zeigt die Routingtabelle des privaten Peerings der ExpressRoute-Lösung von Fabrikam Inc. nach der Konfiguration von Global Reach. Sie sehen, dass die Routingtabelle über Routen verfügt, die zu beiden lokalen Netzwerken gehören.

![ExpressRoute-Routingtabelle nach Global Reach (Fabrikam)]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Nächste Schritte

Antworten auf weitere Fragen zu VNETs und zum VNET-Peering finden Sie unter [FAQs zu virtuellen Netzwerken][VNet-FAQ]. Antworten auf weitere Fragen zu ExpressRoute und zur virtuellen Netzwerkkonnektivität finden Sie unter [ExpressRoute – FAQ][ER-FAQ].

Global Reach wurde noch nicht in allen Ländern/Regionen eingeführt. Ob Global Reach in den gewünschten Ländern/Regionen verfügbar ist, erfahren Sie unter [ExpressRoute Global Reach][Global Reach].

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: ../virtual-network/virtual-networks-faq.md