---
title: 'Herstellen einer Verbindung mit einem VNet unter Verwendung eines Point-to-Site-VPN und der Zertifikatauthentifizierung: Portal'
titleSuffix: Azure VPN Gateway
description: Erfahren Sie, wie Sie Windows-, macOS- und Linux-Clients anhand von VPN Gateway Point-to-Site-Verbindungen und selbstsignierten oder von einer CA ausgestellten Zertifikaten sicher mit einem VNet verbinden.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: 7986b592496ee558b76202ecf32f5121f6b30e37
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514615"
---
# <a name="configure-a-point-to-site-vpn-connection-using-azure-certificate-authentication-azure-portal"></a>Konfigurieren einer Point-to-Site-VPN-Verbindung unter Verwendung der Azure-Zertifikatauthentifizierung: Azure-Portal

Dieser Artikel enthält Informationen zum sicheren Verbinden von einzelnen Clients, auf denen Windows, Linux oder macOS ausgeführt wird, mit einem Azure-VNET. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Sie können anstelle einer Site-to-Site-VPN-Verbindung auch P2S verwenden, wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen. Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol) oder IKEv2. Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Herstellen einer Verbindung zwischen einem Computer und einem Azure VNet: Diagramm der Point-to-Site-Verbindung.":::

Weitere Informationen zu Point-to-Site-VPN finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md). Informationen zum Erstellen dieser Konfiguration mithilfe von Azure PowerShell finden Sie unter [Konfigurieren eines Point-to-Site-VPN mit Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

### <a name="example-values"></a><a name="example"></a>Beispielwerte

Sie können die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen:

**VNET**

* **VNet-Name:** VNet1
* **Adressraum:** 10.1.0.0/16<br>In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden.
* **Subnetzname:** FrontEnd
* **Subnetzadressbereich:** 10.1.0.0/24
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe:** TestRG1
* **Standort:** East US

**Gateway für virtuelle Netzwerke**

* **Name des Gateways des virtuellen Netzwerks:** VNet1GW
* **Gatewaytyp**: VPN
* **VPN-Typ:** routenbasiert
* **SKU:** VpnGw2
* **Generation:** Generation2
* **Adressbereich für Gatewaysubnetz**: 10.1.255.0/27
* **Öffentliche IP-Adresse:** VNet1GWpip

**Verbindungstyp und Clientadressenpool**

* **Verbindungstyp:** Punkt-zu-Standort
* **Clientadresspool:** 172.16.201.0/24<br>VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNet herstellen, erhalten eine IP-Adresse aus dem Clientadresspool.

## <a name="create-a-vnet"></a><a name="createvnet"></a>Erstellen eines VNET

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vpn-gateway"></a><a name="creategw"></a>Erstellen des VPN-Gateways

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

>[!NOTE]
>Die Basic-Gateway-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung. Wenn Sie planen, Verbindungen von Mac-Clients mit Ihrem virtuellen Netzwerk zuzulassen, sollten Sie nicht die Basic-SKU verwenden.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

Der Bereitstellungsstatus wird auf der Übersichtsseite für Ihr Gateway angezeigt. Nach der Erstellung des Gateways können Sie die zugewiesene IP-Adresse unter dem Virtual Network im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>Generieren von Zertifikaten

Zertifikate werden von Azure zum Authentifizieren von Clients verwendet, die eine Verbindung mit einem VNET über eine Point-to-Site-VPN-Verbindung herstellen. Nachdem Sie ein Stammzertifikat abgerufen haben, müssen Sie die Informationen des öffentlichen Schlüssels in Azure [hochladen](#uploadfile). Das Stammzertifikat wird dann von Azure als „vertrauenswürdig“ für die Verbindung mit dem virtuellen Netzwerk über P2S betrachtet. Sie generieren darüber hinaus Clientzertifikate aus dem vertrauenswürdigen Stammzertifikat und installieren sie dann auf den einzelnen Clientcomputern. Mit dem Clientzertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET initiiert.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Generieren eines Stammzertifikats

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Generieren von Clientzertifikaten

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="add-the-vpn-client-address-pool"></a><a name="addresspool"></a>Hinzufügen des VPN-Clientadresspools

Der Clientadresspool ist ein Bereich privater IP-Adressen, die Sie angeben. Den Clients, die eine Verbindung über ein P2S-VPN herstellen, wird dynamisch eine IP-Adresse aus diesem Bereich zugewiesen. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. Wenn Sie mehrere Protokolle konfigurieren und SSTP eines der Protokolle ist, wird der konfigurierte Adresspool gleichmäßig auf die konfigurierten Protokolle aufgeteilt.

1. Navigieren Sie nach Erstellung des Gateways für virtuelle Netzwerke auf dem Blatt des Gateways für virtuelle Netzwerke zum Abschnitt **Einstellungen**. Klicken Sie unter **Einstellungen** auf **Punkt-zu-Standort-Konfiguration**. Wählen Sie **Jetzt konfigurieren** aus, um die Konfigurationsseite zu öffnen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Seite „Point-to-Site-Konfiguration“." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Fügen Sie auf der Seite **Point-to-Site-Konfiguration** im Feld **Adresspool** den privaten IP-Adressbereich hinzu, den Sie verwenden möchten. VPN-Clients wird aus dem von Ihnen angegebenen Bereich dynamisch eine IP-Adresse zugewiesen. Der Wert der kleinstmöglichen Subnetzmaske beträgt 29 Bit für die Aktiv/Passiv-Konfiguration und 28 Bit für die Aktiv/Aktiv-Konfiguration.
1. Fahren Sie mit dem nächsten Abschnitt fort, um die Authentifizierungs- und Tunneltypen zu konfigurieren.

## <a name="specify-tunnel-type-and-authentication-type"></a><a name="type"></a>Angeben des Tunnel- und Authentifizierungstyps

In diesem Abschnitt geben Sie den Tunnel- und Authentifizierungstyp an. Wenn auf der Seite „Point-to-Site-Konfiguration“ weder der Tunnel- noch der Authentifizierungstyp angezeigt wird, verwendet Ihr Gateway die Basic-SKU. Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung. Wenn Sie diese Einstellungen verwenden möchten, müssen Sie das Gateway löschen und mit einer anderen Gateway-SKU neu erstellen.

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tunneltyp

Wählen Sie auf der Seite **Point-to-Site-Konfiguration** den **Tunneltyp** aus. Beachten Sie beim Auswählen des Tunneltyps Folgendes:

* Der strongSwan-Client unter Android und Linux und der native IKEv2-VPN-Client unter iOS und macOS verwenden zur Verbindungsherstellung nur den IKEv2-Tunneltyp.
* Windows-Clients probieren zunächst IKEv2. Wenn keine Verbindung hergestellt wird, greifen sie auf SSTP zurück.
* Sie können den OpenVPN-Client verwenden, um eine Verbindung mit dem OpenVPN-Tunneltyp herzustellen.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Authentifizierungsart

Wählen Sie unter **Authentifizierungstyp** die Option **Azure-Zertifikat** aus.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication.png" alt-text="Screenshot: Authentifizierungstyp mit ausgewähltem Azure-Zertifikat." :::

## <a name="upload-root-certificate-public-key-information"></a><a name="uploadfile"></a>Hochladen von Informationen des öffentlichen Schlüssels des Stammzertifikats

In diesem Abschnitt laden Sie die Daten des öffentlichen Stammzertifikats in Azure hoch. Nach dem Hochladen der Daten des öffentlichen Zertifikats kann Azure sie verwenden, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist.

1. Navigieren Sie im Abschnitt **Stammzertifikat** zur Seite **Gateway für virtuelle Netzwerke > Point-to-Site-Konfiguration**. Dieser Abschnitt ist nur sichtbar, wenn Sie **Azure-Zertifikat** als Authentifizierungstyp ausgewählt haben.
1. Vergewissern Sie sich, dass Sie das Stammzertifikat in den vorherigen Schritten als **X.509-CER-Datei mit Base-64-Codierung** exportiert haben. Das Zertifikat muss in diesem Format exportiert werden, damit Sie es mit einem Text-Editor öffnen können. Sie müssen den privaten Schlüssel nicht exportieren.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/base-64.png" alt-text="Screenshot: Export als X.509-CER-Datei mit Base-64-Codierung." :::
1. Öffnen Sie das Zertifikat mit einem Text-Editor (beispielsweise mit dem Windows-Editor). Stellen Sie beim Kopieren der Zertifikatsdaten sicher, dass Sie den Text als fortlaufende Zeile ohne Wagenrückläufe oder Zeilenvorschübe kopieren. Es kann erforderlich sein, Ihre Ansicht im Text-Editor so zu ändern, dass die Symbole bzw. alle Zeichen angezeigt werden, damit die Wagenrückläufe bzw. Zeilenvorschübe sichtbar sind. Kopieren Sie nur den folgenden Abschnitt als fortlaufende Zeile:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Screenshot: Stammzertifikatinformationen in Editor." border="false":::
1. Sie können im Abschnitt **Stammzertifikat** bis zu 20 vertrauenswürdige Stammzertifikate hinzufügen.

   * Fügen Sie die Zertifikatdaten im Feld **Daten des öffentlichen Zertifikats** ein. 
   * **Benennen** Sie das Zertifikat. 

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/root-certificate.png" alt-text="Screenshot des Felds „Zertifikatdaten“." :::
1. Wählen Sie oben auf der Seite die Option **Speichern** aus, um alle Konfigurationseinstellungen zu speichern.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save-configuration.png" alt-text="Screenshot der P2S-Konfiguration mit ausgewählter Option „Speichern“" :::

## <a name="install-exported-client-certificate"></a><a name="installclientcert"></a>Installieren eines exportierten Clientzertifikats

Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde.

Stellen Sie sicher, dass das Clientzertifikat zusammen mit der gesamten Zertifikatkette als PFX-Datei exportiert wurden (Standardeinstellung). Andernfalls sind die Stammzertifikatinformationen nicht auf dem Clientcomputer vorhanden, und der Client kann nicht ordnungsgemäß authentifiziert werden.

Die Installationsschritte finden Sie unter [Installieren eines Clientzertifikats für Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="configure-settings-for-vpn-clients"></a><a name="clientconfig"></a>Konfigurieren von Einstellungen für VPN-Clients

Um eine P2S-Verbindung mit dem Gateway des virtuellen Netzwerks herzustellen, verwendet jeder Computer den VPN-Client, der nativ als Teil des Betriebssystems installiert ist. Sie können beispielsweise in den VPN-Einstellungen Ihres Windows-Computers VPN-Verbindungen hinzufügen, ohne einen separaten VPN-Client zu installieren. Sie konfigurieren jeden VPN-Client mithilfe eines Clientkonfigurationspakets. Das Clientkonfigurationspaket enthält Einstellungen, die für das von Ihnen erstellte VPN-Gateway spezifisch sind. 

Schritte zum Erstellen und Installieren von VPN-Clientkonfigurationsdateien finden Sie unter [Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit Azure-Zertifikatauthentifizierung](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect-to-azure"></a><a name="connect"></a>Herstellen einer Verbindung mit Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>So stellen Sie eine Verbindung von einem Windows-VPN-Client her

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>So stellen Sie eine Verbindung von einem Mac-VPN-Client her

Suchen Sie im Dialogfeld „Netzwerk“ nach dem gewünschten Clientprofil, geben Sie die Einstellungen aus der Datei [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) an, und wählen Sie dann **Verbinden** aus. Ausführliche Anweisungen finden Sie unter [Generieren und Installieren von VPN-Clientkonfigurationsdateien – macOS](./point-to-site-vpn-client-configuration-azure-cert.md#installmac). 

Wenn beim Herstellen einer Verbindung Probleme auftreten, stellen Sie sicher, dass das Gateway des virtuellen Netzwerks nicht die Basic-SKU verwendet. Die Basic-SKU wird für Mac-Clients nicht unterstützt.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="Screenshot: Schaltfläche „Verbinden“" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>So überprüfen Sie Ihre Verbindung

Diese Anweisungen gelten für Windows-Clients.

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all* aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die erhaltene IP-Adresse eine der Adressen aus dem Clientadresspool des Punkt-zu-Standort-VPN ist, den Sie in Ihrer Konfiguration angegeben haben. Die Ergebnisse sehen in etwa wie in diesem Beispiel aus:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Diese Anweisungen gelten für Windows-Clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Stellen Sie sicher, dass das VPN-Clientkonfigurationspaket generiert wurde, nachdem die IP-Adressen des DNS-Server für das VNET angegeben wurden. Wenn Sie die IP-Adressen des DNS-Servers aktualisiert haben, generieren Sie ein neues VPN-Clientkonfigurationspaket und installieren es.

* Überprüfen Sie mit „ipconfig“ die IPv4-Adresse, die dem Ethernet-Adapter auf dem Computer zugewiesen ist, von dem aus Sie die Verbindung herstellen. Wenn sich die IP-Adresse im Adressbereich des VNETs befindet, mit dem Sie die Verbindung herstellen, oder im Adressbereich von „VPNClientAddressPool“ liegt, wird dies als sich überschneidender Adressraum bezeichnet. Falls sich Ihr Adressraum auf diese Weise überschneidet, kommt der Netzwerkdatenverkehr nicht bei Azure an, sondern verbleibt im lokalen Netzwerk.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>So können Sie vertrauenswürdige Stammzertifikate hinzufügen oder entfernen

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über diesen Stamm ein Zertifikat generiert wurde, nicht authentifiziert werden und somit auch keine Verbindung herstellen. Wenn Sie für einen Client die Authentifizierung und Verbindungsherstellung durchführen möchten, müssen Sie ein neues Clientzertifikat installieren, das aus einem für Azure vertrauenswürdigen (hochgeladenen) Stammzertifikat generiert wurde.

Sie können Azure bis zu 20 vertrauenswürdige CER-Stammzertifikatdateien hinzufügen. Anweisungen finden Sie unter [Hochladen eines vertrauenswürdigen Stammzertifikats](#uploadfile).

So entfernen Sie ein vertrauenswürdiges Stammzertifikat

1. Navigieren Sie zur Seite **Point-to-Site-Konfiguration-Konfiguration** des Gateways für virtuelle Netzwerke.
1. Suchen Sie auf der Seite im Abschnitt **Stammzertifikat** nach dem Zertifikat, das Sie entfernen möchten.
1. Wählen Sie neben dem Zertifikat die Auslassungspunkte und anschließend **Entfernen** aus.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>So sperren Sie ein Clientzertifikat

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Das ist nicht dasselbe wie das Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin für die Authentifizierung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

Sie können ein Clientzertifikat sperren, indem Sie den Fingerabdruck der Sperrliste hinzufügen.

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Kopieren Sie ihn in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht.
1. Navigieren Sie zur Seite **Punkt-zu-Standort-Konfiguration** des Gateways für virtuelle Netzwerke. Dies ist die Seite, die Sie zum [Hochladen eines vertrauenswürdigen Stammzertifikats](#uploadfile) verwendet haben.
1. Geben Sie im Abschnitt **Gesperrte Zertifikate** einen Anzeigenamen für das Zertifikat ein. (Dabei muss es sich nicht um den allgemeinen Namen des Zertifikats handeln.)
1. Kopieren Sie die Fingerabdruck-Zeichenfolge, und fügen Sie sie in das Feld **Fingerabdruck** ein.
1. Der Fingerabdruck wird überprüft und automatisch der Sperrliste hinzugefügt. Auf dem Bildschirm wird eine Meldung mit dem Hinweis angezeigt, dass die Liste aktualisiert wird. 
1. Nach Abschluss der Aktualisierung kann das Zertifikat nicht mehr für die Verbindungsherstellung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

## <a name="point-to-site-faq"></a><a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

Häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen](vpn-gateway-vpn-faq.md#P2S).

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/network-overview.md).

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
