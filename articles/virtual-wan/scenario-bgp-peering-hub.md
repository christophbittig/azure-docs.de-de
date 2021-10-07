---
title: BGP-Peering mit einem virtuellen Hub
titleSuffix: Azure Virtual WAN
description: Hier erfahren Sie mehr über das BGP-Peering mit einem virtuellen Azure Virtual WAN-Hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: e570e5f06af814a6d0cbb581275d1c70ebf0df8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780792"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>Szenario: BGP-Peering mit einem virtuellen Hub (Vorschau)

> [!IMPORTANT]
> Das BGP-Peering mit Virtual WAN Hub-Feature befindet sich derzeit in der geschlossenen öffentlichen Vorschau. Wenn Sie dieses Feature ausprobieren möchten, senden Sie zusammen mit der Ressourcen-ID Ihrer Virtual WAN Ressource eine E-Mail an **previewbgpwithvhub@microsoft.com** . Nachdem Sie die Bestätigung der Featureaktivierung erhalten haben, finden Sie auf der folgenden [Dokumentationsseite](create-bgp-peering-hub-portal.md) wichtige Überlegungen und eine ausführliche Anleitung für die Konfiguration. 
>
> Um die Ressourcen-ID zu finden, öffnen Sie das Azure-Portal, navigieren Sie zu Ihrer Virtual WAN-Ressource und klicken Sie auf **Einstellungen > Eigenschaften > Ressourcen-ID**.<br> Beispiel: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>


Der Azure Virtual WAN-Hubrouter, auch Router für virtuelle Hubs genannt, fungiert als Routen-Manager und vereinfacht das Routing innerhalb von und zwischen virtuellen Hubs. Anders ausgedrückt hat ein Router für virtuelle Hubs folgende Aufgaben:

* Vereinfachung der Routingverwaltung, da er die zentrale Routing-Engine darstellt, die mit Gateways wie dem VPN, ExpressRoute, P2S und virtuellen Netzwerkgeräten (Network Virtual Appliances, NVA) kommuniziert 
* Ermöglichen erweiterter Routingszenarios für benutzerdefinierte Routingtabellen, Zuordnung und Routenweitergabe
* Weiterleitung des Datenverkehrs, der zwischen virtuellen Netzwerken oder an virtuelle Netzwerke gesendet wird, die mit einem virtuellen Hub verbunden sind

Der Router für virtuelle Hubs bietet auch die Möglichkeit, selbst eine Peeringverbindung einzugehen, wodurch Routinginformationen direkt über das Routingprotokoll „Border Gateway Protocol“ (BGP) ausgetauscht werden. Ein NVA oder BGP-Endpunkt, der in einem mit einem virtuellen Hub verbundenen virtuellen Netzwerk bereitgestellt wird, kann eine direkte Peeringverbindung mit dem Router für virtuelle Hubs eingehen, wenn dieser das Routingprotokoll BGP unterstützt und sicher stellt, dass die ASN auf dem NVA so eingerichtet ist, dass sie sich von der ASN des virtuellen Hubs unterscheidet.

## <a name="benefits-and-considerations"></a>Vorteile und Überlegungen

**Hauptvorteile**

* Sie müssen die Routingtabelle auf Ihrem NVA nicht mehr manuell aktualisieren, wenn die Adresse Ihres virtuellen Netzwerks aktualisiert wird.
* Sie müssen benutzerdefinierte Routen nicht mehr manuell aktualisieren, wenn Ihr NVA neue Routen ankündigt oder alte Routen ausrangiert.
* NVAs in virtuellen Netzwerken, die mit einem virtuellen Hub verbunden sind, können Routen des virtuellen Hub-Gateways (VPN, ExpressRoute oder verwaltete NVAs) erlernen.
* Sie können mehrere Instanzen Ihres NVA per Peering mit einem Router für virtuelle Hubs verbinden. Sie können BGP-Attribute auf Ihrem NVA konfigurieren und in Abhängigkeit von Ihrem Entwurf (Aktiv/Aktiv oder Aktiv/Passiv) dem Router für virtuelle Hubs mitteilen, welche NVA-Instanz aktiv oder passiv ist.

**Überlegungen**

* Sie können keinen Router für virtuelle Hubs per Peering mit einer Instanz von Azure Route Server verbinden, die in einem virtuellen Netzwerk bereitgestellt wurde.
* Der Router für virtuelle Hubs unterstützt nur eine 16-Bit-ASN (2 Byte).
* Die virtuelle Netzwerkverbindung mit dem NVA-BGP-Verbindungsendpunkt muss immer defaultRouteTable zugeordnet sein und daran weiterleiten. Benutzerdefinierte Routingtabellen werden derzeit nicht unterstützt.
* Der Router für virtuelle Hubs unterstützt Transitkonnektivität zwischen virtuellen Netzwerken, die mit virtuellen Hubs verbunden sind. Dies hat nichts mit diesem Feature für das BGP-Peering zu tun, da Virtual WAN die Transitkonnektivität bereits unterstützt. Beispiele:
  * VNET1: NVA1 verbunden mit virtuellem Hub 1 -> (Transitkonnektivität) -> VNET2: NVA2 verbunden mit virtuellem Hub 1
  * VNET1: NVA1 verbunden mit virtuellem Hub 1 -> (Transitkonnektivität) -> VNET2: NVA2 verbunden mit virtuellem Hub 2
* Sie können Ihre eigenen öffentlichen ASNs oder privaten ASNs auf Ihrem virtuellen Netzwerkgerät verwenden. Die von Azure oder IANA reservierten Bereiche können nicht verwendet werden. Die folgenden ASNs sind für Azure oder IANA reserviert:
   * Von Azure reservierte ASNs:
     * Öffentliche ASNs: 8074, 8075, 12076
     * Private ASNs: 65515, 65517, 65518, 65519, 65520
   * Von IANA reservierte ASNs: 23456, 64496-64511, 65535-65551
* Während der Router für virtuelle Hubs BGP-Routen mit Ihrem NVA austauscht und diese an Ihr virtuelles Netzwerk weiterleitet, ermöglicht er die direkte Weiterleitung von Routen aus der lokalen Umgebung über die gehosteten Gateways des virtuellen Hubs (VPN Gateway/ExpressRoute-Gateway/verwaltete NVA-Gateways). 

   Der Router für virtuelle Hubs weist die folgenden Einschränkungen auf:

   | Resource | Begrenzung |
   |---|---|
   |  Anzahl von Routen, die jeder BGP-Peer dem virtuellen Hub anbieten kann| Der Hub kann nur maximal 10.000 Routen (insgesamt) von seinen verbundenen Ressourcen akzeptieren. Wenn ein virtueller Hub beispielsweise über insgesamt 6.000 Routen aus den verbundenen virtuellen Netzwerken, Branches, virtuellen Hubs usw. verfügt, kann ein NVA nur bis zu 4.000 Routen anbieten, wenn ein neues BGP-Peering mit diesem NVA konfiguriert wird. |

## <a name="bgp-peering-scenarios"></a>BGP-Peeringszenarios

In diesem Abschnitt werden Szenarios beschrieben, in denen das BGP-Peeringfeature für die Konfiguration des Routings verwendet werden kann.

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>Transitkonnektivität zwischen VNETs

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="Abbildung zum Routing zwischen VNETs":::

In diesem Szenario ist der virtuelle Hub „Hub 1“ mit mehreren virtuellen Netzwerken verbunden. Das Ziel besteht darin, Routing zwischen den virtuellen Netzwerken VNET1 und VNET5 einzurichten.

### <a name="configuration-steps-without-bgp-peering"></a>Konfigurationsschritte ohne BGP-Peering

Die folgenden Schritte sind erforderlich, wenn auf dem virtuellen Hub kein BGP-Peering verwendet wird:

Konfiguration des virtuellen Hubs

* Konfigurieren Sie in defaultRouteTable von Hub 1 die statische Route für VNET5 (Subnetz 10.2.1.0/24), die auf die VNET2-Verbindung verweist.
* Konfigurieren Sie in der virtuellen Netzwerkverbindung von Hub 1 für VNET2 die statische Route für VNET5, die auf die VNET2-NVA-IP (Subnetz 10.2.0.5) verweist.
* Leiten Sie auf Hub 1 die Routen von Verbindungen für VNET1 und VNET2 an defaultRouteTable weiter, und ordnen Sie sie defaultRouteTable zu.

Konfiguration von virtuellen Netzwerken

* Richten Sie in VNET5 eine benutzerdefinierte Route ein, die auf die VNET2-NVA-IP verweist.

### <a name="configuration-steps-with-bgp-peering"></a>Konfigurationsschritte mit BGP-Peering

In der vorherigen Konfiguration kann die Wartung der statischen Routen und der benutzerdefinierten Route komplex werden, wenn sich die Konfiguration von VNET5 häufig ändert. Diese Herausforderung kann mit dem BGP-Peering mit einem virtuellen Hub bewältigt werden. Außerdem muss die Routingkonfiguration dazu in die folgenden Schritte geändert werden:

Konfiguration des virtuellen Hubs

* Konfigurieren Sie auf Hub 1 das VNET2-NVA als BGP-Peer. Konfigurieren Sie außerdem das VNET2-NVA für ein BGP-Peering mit Hub 1.
* Leiten Sie auf Hub 1 die Routen von Verbindungen für VNET1 und VNET2 an defaultRouteTable weiter, und ordnen Sie sie defaultRouteTable zu.

Konfiguration von virtuellen Netzwerken

* Richten Sie in VNET5 eine benutzerdefinierte Route ein, die auf die VNET2-NVA-IP verweist.

#### <a name="effective-routes"></a>Effektive Routen

Die folgende Tabelle zeigt einige Einträge aus den effektiven Routen von Hub 1 in defaultRouteTable. Beachten Sie die Route für VNET5 (Subnetz 10.2.1.0/24), und dies bestätigt, dass VNET1 und VNET5 miteinander kommunizieren können werden.

| Zielpräfix |  Nächster Hop| Origin | ASN-Pfad|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | ID der VNET-Verbindung | - |
| 10.2.1.0/24 |Verbindungs-ID des BGP-Peers für das NVA  | Verbindungs-ID des BGP-Peers für das NVA |  65510|
|  10.4.1.0/24 | Hub 2 | Hub 2 | - |

Wird das Routing auf diese Weise mithilfe des Features konfiguriert, entfällt die Notwendigkeit statischer Routeneinträge auf dem virtuellen Hub. Daher ist die Konfiguration einfacher, und Routingtabellen werden dynamisch aktualisiert, wenn sich die Konfiguration in verbundenen virtuellen Netzwerken (z. B. VNET5) ändert.

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet"></a>Branchkonnektivität zu VNETs

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="Abbildung zum Branch-zu-VNET-Routing":::

In diesem Szenario wurde am lokalen Standort namens „NVA Branch 1“ ein VPN so konfiguriert, das es auf dem VNET2-NVA endet. Ziel ist es, das Routing zwischen NVA Branch 1 und dem virtuellen Netzwerk VNET1 zu konfigurieren.

### <a name="configuration-steps-without-bgp-peering"></a>Konfigurationsschritte ohne BGP-Peering

Die folgenden Schritte sind erforderlich, wenn auf dem virtuellen Hub kein BGP-Peering verwendet wird:

Konfiguration des virtuellen Hubs

* Konfigurieren Sie in defaultRouteTable von Hub 1 die statische Route für NVA Branch 1, die auf die VNET2-Verbindung verweist.
* Konfigurieren Sie in der virtuellen Netzwerkverbindung von Hub 1 für VNET2 die statische Route für NVA Branch 1, die auf die VNET2-NVA-IP (10.2.0.5) verweist.
* Leiten Sie auf Hub 1 die Routen von Verbindungen für VNET1 und VNET2 an defaultRouteTable weiter, und ordnen Sie sie defaultRouteTable zu.

Konfiguration von virtuellen Netzwerken

* BGP-Peering zwischen dem VNET2-NVA und NVA Branch 1 und Routenankündigungen für VNET1 vom VNET2-NVA an NVA Branch 1

### <a name="configuration-steps-with-bgp-peering"></a>Konfigurationsschritte mit BGP-Peering

Im Laufe der Zeit können sich die Zielpräfixe in NVA Branch 1 ändern, oder es können viele Standorte wie NVA Branch 1 dazukommen, die eine Verbindung mit VNET1 benötigen. Dies würde dazu führen, dass die statischen Routen auf Hub 1 und in der VNET2-Verbindung aktualisiert werden müssen, was sehr umständlich sein kann. In solchen Fällen können das BGP-Peering mit einem virtuellen Hub und die unten angegebenen Konfigurationsschritte für die Routingkonnektivität helfen.

Konfiguration des virtuellen Hubs

* Konfigurieren Sie auf Hub 1 das VNET2-NVA als BGP-Peer. Konfigurieren Sie außerdem das VNET2-NVA für ein BGP-Peering mit Hub 1.
* Leiten Sie auf Hub 1 die Routen von Verbindungen für VNET1 und VNET2 an defaultRouteTable weiter, und ordnen Sie sie defaultRouteTable zu.

Konfiguration von virtuellen Netzwerken

* BGP-Peering zwischen dem VNET2-NVA und NVA Branch 1 und Routenankündigungen für VNET1 vom VNET2-NVA an NVA Branch 1

#### <a name="effective-routes"></a>Effektive Routen

Die folgende Tabelle zeigt einige Einträge aus den effektiven Routen von Hub 1 in defaultRouteTable. Beachten Sie, dass die Route für NVA Branch 1 (Subnetz 192.168.1.0/24) über das BGP-Peering mit dem NVA erlernt wird.

 | Zielpräfix |  Nächster Hop| Origin | ASN-Pfad|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | ID der VNET-Verbindung | - |
| 192.168.1.0/24 | Verbindungs-ID des BGP-Peers für das NVA  | Verbindungs-ID des BGP-Peers für das NVA |  65510|

Für die Verwaltung von Netzwerkänderungen in NVA Branch 1 oder die Herstellung einer Verbindung zwischen neuen Standorten wie NVA Branch 1 ist auf Hub 1 keine zusätzliche Konfiguration erforderlich, da die Routingtabellen über das BGP-Peering zwischen Hub 1 und dem NVA dynamisch aktualisiert werden. So werden die Konfiguration und die Wartung vereinfacht.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren des BGP-Peerings mit einem virtuellen Hub](create-bgp-peering-hub-portal.md)
