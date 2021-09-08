---
title: Konfigurieren einer Standort-zu-Standort-VPN-Verbindung in vWAN für Azure VMware Solution
description: Erfahren Sie, wie Sie einen VPN-Standort-zu-Standort-Tunnel (IPSec ikev1 and IKEv2) in Azure VMware-Lösungen einrichten.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 06/30/2021
ms.openlocfilehash: f3ed10e2a67e40fe8f1124a88fd8e11b24b71986
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343706"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>Konfigurieren einer Standort-zu-Standort-VPN-Verbindung in vWAN für Azure VMware Solution

In diesem Artikel richten Sie einen Site-to-Site-VPN-Tunnel (IPSec IKEv1 und IKEv2) ein, der im Microsoft Azure Virtual WAN-Hub endet. Der Azure Virtual-Hub enthält das Azure VMware Solution ExpressRoute-Gateway und das VPN-Standort-zu-Standort Gateway. Er verbindet ein lokales VPN-Gerät mit einem Azure VMware Solution-Endpunkt.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagramm zur Site-to-Site-VPN-Tunnelarchitektur." border="false":::

## <a name="prerequisites"></a>Voraussetzungen
Sie müssen über eine öffentliche IP-Adresse verfügen, die auf einem lokalen VPN-Gerät endet.

## <a name="create-an-azure-virtual-wan"></a>Erstellen Sie einen Azure Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-virtual-hub"></a>Erstellen eines virtuellen Hubs

Ein virtueller Hub ist ein virtuelles Netzwerk, das von Virtual WAN erstellt und genutzt wird. Es ist der Kern Ihres Virtual WAN-Netzwerks in einer Region.  Er kann Gateways für Site-to-Site und ExpressRoute enthalten. 

>[!TIP]
>Sie können auch ein [Gateway in einem bestehenden Hub erstellen](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).


[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-vpn-gateway"></a>Erstellen eines VPN-Gateways 

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]


## <a name="create-a-site-to-site-vpn"></a>Erstellen eines Site-to-Site-VPN

1. Öffnen Sie im Azure-Portal das zuvor erstellte virtuelle WAN.

2. Wählen Sie in der **Übersicht** des virtuellen Hubs die VPN- **Konnektivität** >  **(Standort-zu-Standort)**  >  **aus. Erstellen Sie einen neuen VPN-Standort**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Der Screenshot zeigt die Übersichtsseite für den virtuellen Hub, wobei „VPN (Site-to-Site)“ und „Neue VPN-Site erstellen“ ausgewählt sind.":::  
 
3. Geben Sie auf der Registerkarte **Grundeinstellungen** die erforderlichen Informationen ein. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Screenshot der Seite „VPN-Standort erstellen“ mit geöffneter Registerkarte „Grundlagen“." lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png":::

   * **Region**: Zuvor als Standort bezeichnet. Dies ist der Standort, an dem Sie diese Standortressource erstellen möchten.
   
   * **Name**: Der Name, den Sie Ihrer lokalen Site geben möchten.
   
   * **Gerätehersteller**: Der Name des VPN-Geräteherstellers, z. B. Citrix, Cisco oder Barracuda. Dieser erleichtert es dem Azure-Team, Ihre Umgebung besser zu verstehen und in Zukunft weitere Optimierungsmöglichkeiten hinzuzufügen oder Sie bei der Problembehandlung unterstützen.

   * **Privater Adressraum**: Der CIDR-IP-Adressraum Ihres lokalen Standorts. Der Datenverkehr, der für diesen Adressraum bestimmt ist, wird an Ihre lokale Site geleitet. Der CIDR-Block ist nur erforderlich, wenn [BGP](../vpn-gateway/bgp-howto.md) für den Standort nicht aktiviert ist.
    
   >[!NOTE]
   >Wenn Sie den Adressraum nach dem Erstellen des Standorts bearbeiten (indem Sie z. B. einen weiteren Adressraum hinzufügen), kann es 8-10 Minuten dauern, bis die tatsächlichen Routen aktualisiert sind, während die Komponenten neu erstellt werden.


1. Wählen Sie **Links** aus, um Informationen über die physischen Verbindungen an der Zweigstelle hinzuzufügen. Falls Sie über ein CPE-Gerät eines Virtual WAN-Partners verfügen, können Sie sich beim Partner erkundigen, ob diese Informationen im Rahmen des im Partnersystem eingerichteten Uploads von Zweigstelleninformationen mit Azure ausgetauscht werden.

   Durch Angeben von Verbindungs- und Anbieternamen können Sie jeweils die einzelnen Gateways unterscheiden, die irgendwann als Teil des Hubs erstellt werden.  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) und die ASN (autonome Systemnummer) müssen innerhalb Ihrer Organisation eindeutig sein. BGP stellt sicher, dass sowohl Azure VMware Solution als auch die lokalen Server ihre Routen über den Tunnel veröffentlichen. Wenn diese Option deaktiviert ist, müssen die Subnetze, die angekündigt werden sollen, manuell verwaltet werden. Wenn Subnetze fehlen, kann HCX das Service Mesh nicht bilden. 
 
   >[!IMPORTANT]
   >Standardmäßig weist Azure automatisch eine private IP-Adresse aus dem Präfixbereich des Gatewaysubnetzes als Azure-BGP-IP-Adresse für das Azure-VPN-Gateway zu. Die benutzerdefinierte Azure-APIPA-BGP-Adresse ist erforderlich, wenn Ihre lokalen VPN-Geräte eine APIPA-Adresse (169.254.0.1 bis 169.254.255.254) als BGP-IP-Adresse verwenden. Azure VPN Gateway wählt die benutzerdefinierte APIPA-Adresse aus, wenn die zugehörige lokale Netzwerkgateway-Ressource (lokales Netzwerk) eine APIPA-Adresse als BGP-Peer-IP-Adresse aufweist. Wenn das lokale Netzwerkgateway eine reguläre IP-Adresse (nicht APIPA) verwendet, wird Azure VPN Gateway auf die private IP-Adresse aus dem Bereich des Gatewaysubnetzes zurückgesetzt.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Screenshot der Seite „VPN-Standort erstellen“ mit geöffneter Registerkarte „Links“." lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-links.png":::

1. Klicken Sie auf **Überprüfen + erstellen**. 

1. Navigieren Sie zum gewünschten virtuellen Hub, und deaktivieren Sie **Hubzuordnung**, um Ihren VPN-Standort mit dem Hub zu verbinden.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Screenshot: Verbindung mit diesem Hub herstellen" lightbox="../../includes/media/virtual-wan-tutorial-site-include/connect.png":::   

## <a name="optional-create-policy-based-vpn-site-to-site-tunnels"></a>(Fakultativ) Erstellen von Richtlinien-basierten VPN-Standort-zu-Standort-Tunneln

>[!IMPORTANT]
>Dies ist ein optionaler Schritt, der nur für richtlinienbasierte VPNs gilt. 

Für [richtlinienbasierte VPN-Setups](../virtual-wan/virtual-wan-custom-ipsec-portal.md) müssen lokale und Azure VMware Solution-Netzwerke angegeben werden, einschließlich der Hubbereiche.  Diese Bereiche werden verwendet, um die Verschlüsselungsdomäne für den lokalen Endpunkt des richtlinienbasierten VPN-Tunnels anzugeben.  Auf der Azure VMware Solution-Seite muss lediglich der Selektor für richtlinienbasierten Datenverkehr aktiviert werden. 

1. Navigieren Sie im Azure-Portal zu Ihrem Virtual WAN-Hubstandort und wählen Sie unter **Konnektivität** die Option **VPN (Site-to-Site)** aus.

2. Wählen Sie den VPN-Standort aus, für den Sie eine benutzerdefinierte IPsec-Richtlinie einrichten möchten.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="Screenshot der vorhandenen VPN-Standorte zum Einrichten benutzerdefinierter IPsec-Richtlinien." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png":::

3. Wählen Sie den Namen Ihres VPN-Standorts aus, anschließend ganz rechts **Mehr** (...) und dann **VPN-Verbindung bearbeiten** aus.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="Screenshot: Kontextmenü für einen vorhandenen VPN-Standort." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png":::

   - Wählen Sie für "IPSec" (Internet Protocol Security) **Benutzerdefiniert** aus.

   - **Aktivieren** Sie den Selektor für richtlinienbasierten Datenverkehr.

   - Geben Sie die Details für **IKE-Phase 1** und **IKE-Phase 2 (IPSec)** an. 

4. Ändern Sie die Standardeinstellung der IPSec-Einstellung in „Benutzerdefiniert“, und passen Sie die IPSec-Richtlinie an. Klicken Sie dann auf **Speichern**.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="Screenshot der vorhandenen VPN-Standorte." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png":::

   Die Datenverkehrsselektoren oder Subnetze, die Teil der richtlinienbasierten Verschlüsselungsdomäne sind, müssen wie folgt angegeben werden:
    
   - Virtueller WAN-Hub`/24`

   - Private Azure VMware Solution-Cloud`/22`

   - Verbundenes virtuelles Azure-Netzwerk (sofern vorhanden)

## <a name="connect-your-vpn-site-to-the-hub"></a>Herstellen einer Verbindung zwischen VPN-Standort und Hub

1. Wählen Sie den Namen Ihres VPN-Standorts aus, und klicken Sie dann auf **VPN-Standorte verbinden**. 

1. Geben Sie im Feld **Vorinstallierter Schlüssel** den Schlüssel ein, der zuvor für den lokalen Endpunkt definiert wurde. 

   >[!TIP]
   >Wenn Sie über keinen zuvor definierten Schlüssel verfügen, können Sie dieses Feld leer lassen. Es wird automatisch ein Schlüssel für Sie generiert. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Screenshot, der den Bereich &quot;Verbundene Sites&quot; für Virtual HUB zeigt, bereit für einen Pre-Shared Key und zugehörigen Einstellungen. "::: 

1. Wenn Sie eine Firewall im Hub einsetzen und dieser der nächste Hop ist, setzen Sie die Option **Propagate Default Route** auf **Enable**. 

   Wenn diese Option aktiviert ist, propagiert der Virtual WAN Hub eine Verbindung nur dann, wenn der Hub die Standardroute bereits bei der Einrichtung einer Firewall im Hub gelernt hat oder wenn eine andere angeschlossene Site das Forced Tunneling aktiviert hat. Der Ursprung der Standardroute liegt nicht im Virtual WAN-Hub.  

1. Wählen Sie **Verbinden**. Nach ein paar Minuten zeigt die Seite den Verbindungs- und Konnektivitätsstatus an.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Screenshot, dass die Standort-zu-Standort-Verbindung und Konnektivitätsstatus anzeigt." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

   **Verbindungsstatus:** Status der Azure-Ressource für die Verbindung, mit der der VPN-Standort mit dem VPN-Gateway des Azure-Hubs verbunden wird. Nachdem dieser Vorgang auf der Steuerungsebene erfolgreich abgeschlossen wurde, wird die Konnektivität zwischen Azure-VPN-Gateway und lokalem VPN-Gerät hergestellt.

   **Konnektivitätsstatus**: Der tatsächliche Status der Konnektivität (Datenpfad) zwischen dem VPN-Gateway von Azure im Hub und dem VPN-Standort. Der Status kann wie folgt lauten:

    * **Unbekannt**: Dieser Status tritt normalerweise auf, wenn die Back-End-Systeme aktiv sind und der Übergang in einen anderen Status erfolgt.
    * **Verbindung wird hergestellt**: Das Azure-VPN-Gateway versucht, den tatsächlichen lokalen VPN-Standort zu erreichen.
    * **Verbunden**: Die Konnektivität zwischen dem Azure-VPN-Gateway und dem lokalen VPN-Standort wurde hergestellt.
    * **Getrennt**: Wird in der Regel angezeigt, wenn die Verbindung aus irgendeinem Grund getrennt wurde (lokal oder in Azure).



1. Laden Sie die VPN-Konfigurationsdatei herunter, und wenden Sie sie auf den lokalen Endpunkt an.  
   
   1. Klicken Sie oben auf der Seite „VPN (Site-to-Site)“ auf **VPN-Konfiguration** herunterladen. Azure erstellt ein Speicherkonto in der Ressourcengruppe „microsoft-network-\[Standort\]“, wobei „Standort“ der Standort des WAN ist. Nachdem Sie die Konfiguration auf Ihre VPN-Geräte angewendet haben, können Sie dieses Speicherkonto löschen.

   1. Nachdem die Datei erstellt wurde, wählen Sie den Link aus, um sie herunterzuladen. 

   1. Wenden Sie die Konfiguration auf Ihr lokales VPN-Gerät an.

   Weitere Informationen zur Konfigurationsdatei finden Sie unter [Informationen zur VPN-Gerätekonfigurationsdatei](../virtual-wan/virtual-wan-site-to-site-portal.md#about-the-vpn-device-configuration-file).

1. Fügen Sie das Azure VMware Solution-ExpressRoute-Gateway in den Virtual WAN-Hub ein. 

   >[!IMPORTANT]
   >Vor dem Patchen der Plattform muss zunächst ein Private Cloud erstellt werden. 


   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

1. Verbinden Sie Azure VMware Solution und das VPN-Gateway im Virtual WAN-Hub. Sie verwenden den Autorisierungs-Schlüssel und die expressroute-ID (Peer Circuit URI) aus dem vorherigen Schritt.

   1. Wählen Sie das expressroute-Gateway, und wählen Sie dann **Autorisierungsschlüssel einlösen** aus.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Der Screenshot der ExpressRoute-Seite für die private Cloud mit ausgewählter Option „Autorisierungsschlüssel einlösen“.":::

   1. Fügen Sie den Autorisierungsschlüssel in das Feld **Autorisierungsschlüssel** ein.

   1. Fügen Sie die ExpressRoute-ID in das Feld **Peer Circuit URI** ein. 

   1. Wählen Sie den Checkbox **Diesen ExpressRoute-Kreis automatisch dem Hub zuordnen** aus. 

   1. Wählen Sie **Hinzufügen** aus, um die Verbindung zu erstellen. 

1. Testen Sie Ihre Verbindung, indem Sie [ein NSX-T-Segment erstellen](./tutorial-nsx-t-network-segment.md) und eine VM im Netzwerk bereitstellen. Pingen Sie den lokalen Endpunkt und den Azure VMware Solution-Endpunkt.

   >[!NOTE]
   >Warten Sie ca. 5 Minuten, bevor Sie die Konnektivität auf einem Client hinter Ihrer ExpressRoute-Leitung testen, z. B. einer VM im VNET, das Sie zuvor erstellt haben.
