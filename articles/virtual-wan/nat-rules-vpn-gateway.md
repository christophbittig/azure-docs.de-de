---
title: Konfigurieren von VPN-NAT-Regeln für Ihr Gateway
titleSuffix: Azure Virtual WAN
description: Hier erfahren Sie, wie Sie NAT-Regeln für Ihr VWAN-VPN-Gateway konfigurieren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6613952b5d7811c187123963a6e611602aabb5dc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113127688"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway"></a>Konfigurieren von NAT-Regeln für Ihr Virtual WAN-VPN-Gateway

Sie können Ihr Virtual WAN-VPN-Gateway mit statischen 1:1-NAT-Regeln konfigurieren. Eine NAT-Regel bietet einen Mechanismus zum Einrichten einer 1:1-Übersetzung von IP-Adressen. NAT kann verwendet werden, um zwei IP-Netzwerke mit nicht kompatiblen oder überlappenden IP-Adressen miteinander zu verbinden. Hierbei sind Branches, die überlappende IP-Adressen besitzen und auf Azure-VNET-Ressourcen zugreifen möchten, ein typisches Szenario.

Diese Konfiguration verwendet eine Flusstabelle zum Weiterleiten von Datenverkehr von einer externen IP-Adresse (Host-IP) an eine interne IP-Adresse, die einem Endpunkt in einem virtuellen Netzwerk (virtueller Computer, Computer, Container usw.) zugeordnet ist.

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagramm mit der Architektur":::
   
VPN-Geräte müssen Any-to-Any-Datenverkehrsselektoren (Platzhalter) verwenden, um NAT nutzen zu können. Richtlinienbasierte (eingeschränkte) Datenverkehrsselektoren werden in Verbindung mit der NAT-Konfiguration nicht unterstützt.

## <a name="configure-nat-rules"></a><a name="rules"></a>Konfigurieren von NAT-Regeln

Sie können NAT-Regeln für Ihre VPN-Gatewayeinstellungen jederzeit konfigurieren und anzeigen.

> [!NOTE]
> Site-to-Site-NAT wird bei Site-to-Site-VPN-Verbindungen mit richtlinienbasierten Datenverkehrsselektoren nicht unterstützt.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Screenshot: Bearbeiten von Regeln"lightbox="./media/nat-rules-vpn-gateway/edit-rules.png":::
1. Navigieren Sie zu Ihrem virtuellen Hub.
1. Wählen Sie **VPN (Site-to-Site)** aus.
1. Wählen Sie **NAT-Regeln (Bearbeiten)** aus.
1. Sie können auf der Seite **Edit NAT Rule** (NAT-Regel bearbeiten) eine NAT-Regel mit den folgenden Werten **hinzufügen/bearbeiten/löschen**:

   * **Name:** Ein eindeutiger Name für Ihre NAT-Regel
   * **Type:** (Typ) Statisch. Eine statische 1:1-NAT-Verbindung stellt eine 1:1-Beziehung zwischen einer internen Adresse und einer externen Adresse her.
   * **Mode:** (Modus) IngressSnat oder EgressSnat.  
      * Der IngressSnat-Modus (auch als eingehende Quellen-NAT bezeichnet) gilt für Datenverkehr, der in das Site-to-Site-VPN-Gateway des Azure-Hubs wechselt. 
      * Der EgressSnat-Modus (auch als ausgehende Quellen-NAT bezeichnet) gilt für Datenverkehr, der das Site-to-Site-VPN-Gateway des Azure-Hubs verlässt. 
   * **InternalMapping:** (Interne Zuordnung) Ein Adresspräfixbereich der Quell-IPs im internen Netzwerk, dem einer Gruppe externer IPs zugeordnet wird, also in anderen Worten, Ihrem Präfixbereich vor der NAT-Adresse.
   * **ExternalMapping:** (Externe Zuordnung) Ein Adresspräfixbereich der Ziel-IPs im externen Netzwerk, dem Quell-IPs zugeordnet werden, also in anderen Worten, Ihrem Präfixbereich nach der NAT-Adresse.
   * **Link Connection:** (Linkverbindung) Die Verbindungsressource, die einen VPN-Standort virtuell mit dem Site-to-Site-VPN-Gateway des Azure Virtual WAN-Hubs verknüpft.
 
> [!NOTE]
> Wenn das Site-to-Site-VPN-Gateway übersetzte Adresspräfixe (**ExternalMapping**) über BGP ankündigen soll, klicken Sie auf die Schaltfläche **Enable BGP Translation** (BGP-Übersetzung aktivieren). Dadurch lernt die lokale Umgebung automatisch den Bereich nach der NAT-Adresse von Ausgangsregeln, und Azure (Virtual WAN-Hub, verbundene virtuelle Netzwerke, VPN- und ExpressRoute-Branches) lernt automatisch den Bereich nach der NAT-Adresse von Eingangsregeln. Beachten Sie, dass die Einstellung zum **Aktivieren der BGP-Übersetzung** auf alle NAT-Regeln im Site-to-Site-VPN-Gateway des Virtual WAN-Hubs angewendet wird. 

## <a name="example-configurations"></a><a name="examples"></a>Beispielkonfigurationen

### <a name="ingress-snat-bgp-enabled-vpn-site"></a>Eingangs-SNAT (BGP-fähiger VPN-Standort)

**Eingangs-SNAT-Regeln** werden auf Pakete angewendet, die über das Site-to-Site-VPN-Gateway von Virtual WAN in Azure eingehen. In diesem Szenario möchten Sie zwei Site-to-Site-VPN-Branches mit Azure verbinden. VPN-Standort 1 stellt eine Verbindung über Link A und VPN-Standort 2 über Link B her. Jeder Standort weist den gleichen Adressraum 10.30.0.0/24 auf.

In diesem Beispiel wird Standort 1 per NAT mit 127.30.0.0.0/24 verbunden. Die virtuellen Spoke-Netzwerke von Virtual WAN und andere Branches lernen automatisch diesen Bereich nach der NAT-Adresse. 

Das folgende Diagramm zeigt das projizierte Endergebnis:

:::image type="content" source="./media/nat-rules-vpn-gateway/diagram-bgp.png" alt-text="Diagramm der Eingangsmodus-NAT für BGP-fähige Standorte":::

1. Geben Sie eine NAT-Regel an.

   Geben Sie eine NAT-Regel an, um sicherzustellen, dass das Site-to-Site-VPN-Gateway in der Lage ist, zwischen den beiden Branches mit überlappenden Adressräumen (z. B. 10.30.0.0/24) zu unterscheiden. In diesem Beispiel liegt der Schwerpunkt auf Link 1 für VPN-Standort 1.

   Die folgende NAT-Regel kann eingerichtet und Link A zugeordnet werden. Da es sich um eine statische NAT-Regel handelt, enthalten die Adressräume von **InternalMapping** und **ExternalMapping** die gleiche Anzahl von IP-Adressen.

   * **Name:** ingressRule01
   * **Typ:** Statisch
   * **Modus:** IngressSnat
   * **InternalMapping:** 10.30.0.0/24
   * **ExternalMapping:** 172.30.0.0/24
   * **Linkverbindung:** Link A

 2.  Setzen Sie **Aktivieren der BGP-Routenübersetzung** auf „Aktivieren“.

       :::image type="content" source="./media/nat-rules-vpn-gateway/enable-bgp.png" alt-text="Screenshot: Aktivieren der BGP-Übersetzung":::


3. Stellen Sie sicher, dass das Site-to-Site-VPN-Gateway eine Peeringverbindung mit dem lokalen BGP-Peer herstellen kann.

      In diesem Beispiel muss die **Eingangs-NAT-Regel** 10.30.0.132 in 127.30.0.132 übersetzen. Klicken Sie dazu auf „Edit VPN site“ (VPN-Standort bearbeiten), um die BGP-Adresse von Link A des VPN-Standorts so zu konfigurieren, dass sie diese übersetzte BGP-Peeradresse (127.30.0.132) widerspiegelt. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site-bgp.png" alt-text="Screenshot: Ändern der BGP-Peering-IP"lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

 

### <a name="considerations-if-the-vpn-site-connects-via-bgp"></a><a name="considerations"></a>Überlegungen zur Verbindung des VPN-Standorts über BGP
* Die Subnetzgröße für die interne und die externe Zuordnung muss für die statische 1:1-NAT identisch sein.
* Wenn die **BGP-Übersetzung** aktiviert ist, kündigt das Site-to-Site-VPN-Gateway automatisch die **externe Zuordnung** von **Ausgangs-NAT-Regeln** für die lokale Umgebung sowie die **externe Zuordnung** von **Eingangs-NAT-Regeln** für Azure an (Virtual WAN-Hub, verbundene virtuelle Spoke-Netzwerke, verbundenes VPN/ExpressRoute). Wenn die **BGP-Übersetzung** deaktiviert ist, werden übersetzte Routen der lokalen Umgebung nicht automatisch angekündigt. Daher muss der lokale BGP-Sprecher so konfiguriert werden, dass er den Bereich nach der NAT-Adresse (**ExternalMapping**) der **Eingangs-NAT-Regeln** ankündigt, die dieser VPN-Standortlinkverbindung zugeordnet sind. Ebenso muss eine Route für den Bereich nach der NAT-Adresse (**ExternalMapping**) der **Ausgangs-NAT-Regeln** auf dem lokalen Gerät angewendet werden.
* Das Site-to-Site-VPN-Gateway übersetzt automatisch die lokale IP-Adresse des BGP-Peers, **falls** die lokale IP-Adresse des BGP-Peers in der **internen Zuordnung** einer **Eingangs-NAT-Regel** enthalten ist. Daher muss die **BGP-Adresse der Linkverbindung** des VPN-Standorts die NAT-übersetzte Adresse (Teil der externen Zuordnung) widerspiegeln. 

    Wenn die lokale BGP-IP-Adresse beispielsweise 10.30.0.133 lautet und eine **Eingangs-NAT-Regel** vorhanden ist, die 10.30.0.0/24 in 127.30.0.0/24 übersetzt, muss die **BGP-Adresse der Linkverbindung** des VPN-Standorts als die übersetzte Adresse (127.30.0.133) konfiguriert werden.

 
### <a name="ingress-snat-vpn-site-with-statically-configured-routes"></a>Eingangs-SNAT (VPN-Standort mit statisch konfigurierten Routen)

**Eingangs-SNAT-Regeln** werden auf Pakete angewendet, die über das Site-to-Site-VPN-Gateway von Virtual WAN in Azure eingehen. In diesem Szenario möchten Sie zwei Site-to-Site-VPN-Branches mit Azure verbinden. VPN-Standort 1 stellt eine Verbindung über Link A und VPN-Standort 2 über Link B her. Jeder Standort weist den gleichen Adressraum 10.30.0.0/24 auf.

In diesem Beispiel wird VPN-Standort 1 per NAT mit 127.30.0.0.0/24 verbunden. Da der VPN-Standort nicht über BGP mit dem Site-to-Site-VPN-Gateway verbunden ist, unterscheiden sich die Konfigurationsschritte geringfügig vom Beispiel eines BGP-fähigen Standorts. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram-static.png" alt-text="Diagramm der Konfigurationen für VPN-Standorte, die statisches Routing verwenden":::


1. Geben Sie eine NAT-Regel an.

    Geben Sie eine NAT-Regel an, um sicherzustellen, dass das Site-to-Site-VPN-Gateway in der Lage ist, zwischen den beiden Branches mit demselben Adressraum 10.30.0.0/24 zu unterscheiden. In diesem Beispiel liegt der Schwerpunkt auf Link 1 für VPN-Standort 1.

    Die folgende NAT-Regel kann eingerichtet und Link A des VPN-Standorts 1 zugeordnet werden. Da es sich um eine statische NAT-Regel handelt, enthalten die Adressräume von **InternalMapping** und **ExternalMapping** die gleiche Anzahl von IP-Adressen.

    * **Name**: IngressRule01
    * **Typ**: Statisch
    * **Modus**: IngressSnat
    * **InternalMapping**: 10.30.0.0/24
    * **ExternalMapping**: 172.30.0.0/24
    * **Linkverbindung**: Link A

2. Bearbeiten Sie das Feld „Privater Adressraum“ von VPN-Standort 1, um sicherzustellen, dass das Site-to-Site-VPN-Gateway den Bereich nach der NAT-Adresse (172.30.0.0/24) lernt.

   * Navigieren Sie zu der Ressource „Virtueller Hub“, die das Site-to-Site-VPN-Gateway enthält. Wählen Sie auf der Seite des virtuellen Hubs unter Konnektivität die Option VPN (Site-to-Site) aus.

   * Wählen Sie den VPN-Standort aus, der über Link A mit dem Virtual WAN-Hub verbunden ist. Wählen Sie „Standort bearbeiten“ aus, und geben Sie „172.30.0.0/24“ als privaten Adressraum für den VPN-Standort ein. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/vpn-site-static.png" alt-text="Screenshot: Bearbeiten des privaten Adressraums eines VPN-Standorts" lightbox="./media/nat-rules-vpn-gateway/vpn-site-static.png":::

### <a name="considerations-if-vpn-sites-are-statically-configured-not-connected-via-bgp"></a><a name="considerationsnobgp"></a>Überlegungen zur statischen Konfiguration von VPN-Standorten (nicht über BGP verbunden)
* Die Subnetzgröße für die interne und die externe Zuordnung muss für die statische 1:1-NAT identisch sein.
* Bearbeiten Sie den VPN-Standort im Azure-Portal, um die Präfixe in **ExternalMapping** der **Eingangs-NAT-Regeln** im Feld „Privater Adressraum“ hinzuzufügen. 
* Für Konfigurationen mit **Ausgangs-NAT-Regeln** muss eine Routenrichtlinie oder statische Route mit **ExternalMapping** der **Ausgangs-NAT-Regel** auf das lokale Gerät angewendet werden.

### <a name="packet-flow"></a>Paketfluss

In den vorherigen Beispielen möchte ein lokales Gerät eine Ressource in einem virtuellen Spoke-Netzwerk erreichen. Der Paketfluss sieht wie folgt aus, wobei die NAT-Übersetzungen in Fettformatierung angezeigt sind.

1. Der Datenverkehr vom lokalen Standort wird initiiert.
   * Quell-IP-Adresse: **10.30.0.4**
   * Ziel-IP-Adresse: 10.200.0.4
1. Der Datenverkehr gelangt in das Site-to-Site-Gateway und wird mithilfe der NAT-Regel übersetzt und dann an das Spoke-Netzwerk gesendet.
   * Quell-IP-Adresse: **172.30.0.4**
   * Ziel-IP-Adresse: 10.200.0.4
1. Die Antwort des Spoke-Netzwerks wird initiiert.
   * Quell-IP-Adresse: 10.200.0.4
   * Ziel-IP-Adresse: **172.30.0.4**
1. Der Datenverkehr gelangt in das Site-to-Site-VPN-Gateway, die Übersetzung wird umgekehrt und an den lokalen Standort gesendet.
   * Quell-IP-Adresse: 10.200.0.4
   * Ziel-IP-Adresse: **10.30.0.4**

### <a name="verification-checks"></a>Überprüfungen

In diesem Abschnitt werden Überprüfungen gezeigt, mit denen Sie sicherstellen können, dass Ihre Konfiguration ordnungsgemäß eingerichtet ist. 

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Überprüfen von DefaultRouteTable, Regeln und Routen

Branches in Virtual WAN werden **DefaultRouteTable** zugeordnet, was bedeutet, dass alle Branchverbindungen Informationen über Routen erhalten, die in „DefaultRouteTable“ ausgefüllt sind. Die NAT-Regel wird mit dem übersetzten Präfix in den effektiven Routen von „DefaultRouteTable“ angezeigt.

Aus dem vorherigen Beispiel:

* **Präfix:** 172.30.0.0/24  
* **Typ des nächsten Hops:** VPN_S2S_Gateway
* **Nächster Hop:** VPN_S2S_Gateway Resource

#### <a name="validate-address-prefixes"></a>Überprüfen von Adresspräfixen

Dieses Beispiel gilt für Ressourcen in virtuellen Netzwerken, die „DefaultRouteTable“ zugeordnet sind.

Die **Effektiven Routen** auf den Netzwerkschnittstellenkarten (NIC) eines beliebigen virtuellen Computers, der sich in einem mit dem Virtual WAN-Hub verbundenen virtuellen Spoke-Netzwerk befindet, sollten auch die Adresspräfixe von **ExternalMapping** enthalten, die in der **Eingangs-NAT-Regel** angegeben sind. 

Das lokale Gerät sollte auch Routen für Präfixe enthalten, die in **ExternalMapping** von **Ausgangs-NAT-Regeln** enthalten sind. 

####  <a name="common-configuration-patterns"></a>Allgemeine Konfigurationsmuster 

> [!NOTE]
> Site-to-Site-NAT wird bei Site-to-Site-VPN-Verbindungen mit richtlinienbasierten Datenverkehrsselektoren nicht unterstützt.

Die folgende Tabelle zeigt allgemeine Konfigurationsmuster, die beim Konfigurieren verschiedener Typen von NAT-Regeln im Site-to-Site-VPN-Gateway auftreten.  

| Typ des VPN-Standorts | Eingangs-NAT-Regeln | Ausgangs-NAT-Regeln
|--- |--- | ---|
|VPN-Standort mit statisch konfigurierten Routen |Bearbeiten Sie „Privater Adressraum“ am VPN-Standort so, dass er **ExternalMapping** der NAT-Regel enthält.| Wenden Sie Routen für **ExternalMapping** der NAT-Regel auf dem lokalen Gerät an.|
|VPN-Standort (BGP-Übersetzung aktiviert)|Fügen Sie die **ExternalMapping**-Adresse des BGP-Peers in die BGP-Adresse der VPN-Standortlinkverbindung ein.  | Keine besonderen Überlegungen. |
| VPN-Standort (BGP-Übersetzung deaktiviert) | Stellen Sie sicher, dass der lokale BGP-Sprecher die Präfixe in **ExternalMapping** der NAT-Regel ankündigt. Fügen Sie außerdem die „ExternalMapping“-Adresse des BGP-Peers in die BGP-Adresse der VPN-Standortlinkverbindung ein.| Wenden Sie Routen für **ExternalMapping** der NAT-Regel auf dem lokalen Gerät an.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Site-to-Site-Konfigurationen finden Sie unter [Konfigurieren einer Site-to-Site-Verbindung für ein Virtual WAN](virtual-wan-site-to-site-portal.md).
