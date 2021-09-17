---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5f52297778ec6dc30e96aeac00cd8751b6b39582
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638261"
---
1. Navigieren Sie zu **Virtual WAN > VPN-Standorte**, um die Seite **VPN-Standorte** zu öffnen.
1. Klicken Sie auf der Seite **VPN sites** (VPN-Standorte) auf **+Create site** (+ Standort erstellen).
1. Füllen Sie auf der Seite **VPN-Standort erstellen** auf der Registerkarte **Grundlagen** die folgenden Felder aus:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Screenshot: Seite „VPN-Standort erstellen“ mit geöffneter Registerkarte „Grundlagen“" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Region**: Zuvor als „Standort“ bezeichnet. Dies ist der Standort, an dem Sie diese Standortressource erstellen möchten.
    * **Name**: Der Name, den Sie Ihrer lokalen Site geben möchten
    * **Gerätehersteller**: Der Name des VPN-Geräteherstellers (z. B. Citrix, Cisco, Barracuda). Das Hinzufügen des Geräteherstellers kann dem Azure-Team helfen, Ihre Umgebung besser zu verstehen, um in Zukunft weitere Optimierungsmöglichkeiten hinzuzufügen oder Sie bei der Problembehandlung zu unterstützen.
    * **Privater Adressraum**: Dies ist der IP-Adressraum Ihres lokalen Standorts. Der Datenverkehr, der für diesen Adressraum bestimmt ist, wird an Ihre lokale Site geleitet. Dies ist erforderlich, wenn BGP nicht für den Standort aktiviert ist.
    
      >[!NOTE]
      >Wenn Sie den Adressraum nach dem Erstellen des Standorts bearbeiten (indem Sie z. B. einen weiteren Adressraum hinzufügen), kann es 8-10 Minuten dauern, bis die tatsächlichen Routen aktualisiert sind, während die Komponenten neu erstellt werden.
      >
1. Wählen Sie **Links** aus, um Informationen über die physischen Verbindungen an der Zweigstelle hinzuzufügen. Falls Sie über ein CPE-Gerät eines Virtual WAN-Partners verfügen, können Sie sich beim Partner erkundigen, ob diese Informationen im Rahmen des im Partnersystem eingerichteten Uploads von Zweigstelleninformationen an Azure übermittelt werden.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/links.png" alt-text="Screenshot: Seite „VPN-Standort erstellen“ mit geöffneter Registerkarte „Links“" lightbox="./media/virtual-wan-tutorial-site-include/links.png":::

   * **Linkname**: Ein Name, den Sie für die physische Verbindung am VPN-Standort angeben möchten. Beispiel: meinLink1.
   * **Verbindungsgeschwindigkeit**: Die Geschwindigkeit des VPN-Geräts am Zweigstellenstandort. Beispiel: Der Wert 50 entspricht einer Geschwindigkeit von 50 MBit/s für das VPN-Gerät am Zweigstellenstandort.
   * **Link provider name** (Verbindungsanbietername): Der Name des Anbieters der physischen Verbindung am VPN-Standort. Beispiel: ATT, Verizon.
   * **Link IP address/FQDN** (IP-Adresse/FDQN der Verbindung): Die öffentliche IP-Adresse des lokalen Geräts, das diese Verbindung verwendet. Optional können Sie die private IP-Adresse Ihres lokalen VPN-Geräts hinter ExpressRoute angeben. Sie können auch einen vollqualifizierten Domänennamen einbeziehen, z. B. *something.contoso.com*. Der vollqualifizierte Domänenname muss vom VPN-Gateway aufgelöst werden können. Dies ist möglich, wenn der DNS-Server, der diesen vollqualifizierten Domänennamen hostet, über das Internet erreichbar ist. Die IP-Adresse hat Vorrang, wenn sowohl die IP-Adresse als auch der vollqualifizierte Domänenname angegeben sind.

     >[!NOTE]
     >
     >* Unterstützt eine IPv4-Adresse pro vollqualifiziertem Domänennamen. Wenn der vollqualifizierte Domänenname in mehrere IP-Adressen aufgelöst werden soll, übernimmt das VPN-Gateway die erste IP4-Adresse aus der Liste. IPv6-Adressen werden derzeit nicht unterstützt.
     >
     >* Das VPN-Gateway verwaltet einen DNS-Cache, der alle fünf Minuten aktualisiert wird. Das Gateway versucht, vollqualifizierte Domänennamen nur für getrennte Tunnel aufzulösen. Ein Zurücksetzen des Gateways oder eine Konfigurationsänderung können ebenfalls eine Auflösung des vollqualifizierten Domänennamens auslösen.
     >
   * **Link Border Gateway Protocol** (Border Gateway Protocol der Verbindung): Die Vorgehensweise zum Konfigurieren von BGP für eine Virtual WAN-Verbindung ist dieselbe wie beim Konfigurieren von BGP für ein VPN mit einem Azure-Gateway für virtuelle Netzwerke. Ihre lokale BGP-Peeradresse darf nicht der öffentlichen IP-Adresse Ihres VPN-Geräts oder dem VNET-Adressraum des VPN-Standorts entsprechen. Verwenden Sie als BGP-Peer-IP-Adresse eine andere IP-Adresse für das VPN-Gerät. Dabei kann es sich um eine Adresse handeln, die der Loopback-Schnittstelle des Geräts zugewiesen ist. Geben Sie diese Adresse im entsprechenden VPN-Standort an, der den Standort darstellt.  Informationen zu den Voraussetzungen für BGP finden Sie unter [Übersicht über BGP mit Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Sie können eine VPN-Verbindung jederzeit bearbeiten, um die zugehörigen BGP-Parameter zu aktualisieren (Peering-IP-Adresse für die Verbindung und Adressraumnummer).
1. Sie können weitere Verbindungen hinzufügen oder Verbindungen löschen. Pro VPN-Standort werden vier Verbindungen unterstützt. Wenn Sie beispielsweise vier Internetdienstanbieter am Zweigstellenstandort einsetzen, können Sie vier Verbindungen erstellen (einen pro Internetdienstanbieter) und Informationen für jede Verbindung angeben.
1. Nachdem Sie die Felder ausgefüllt haben, wählen Sie **Überprüfen + erstellen** aus, um die Eingaben zu überprüfen. Klicken Sie auf **Erstellen**, um den Standort zu erstellen.
1. Klicken Sie auf der Seite **VPN-Standorte** auf **Hub association: Connected to this hub** (Hubzuordnung: Mit diesem Hub verbunden), um den Filter zu löschen.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Screenshot: Verbindung mit diesem Hub herstellen" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
1. Nach dem Löschen des Filters können Sie Ihren Standort anzeigen.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/sites.png" alt-text="Screenshot: Standort":::
