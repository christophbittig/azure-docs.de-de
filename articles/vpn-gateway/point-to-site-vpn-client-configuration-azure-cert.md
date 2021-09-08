---
title: 'Erstellen und Installieren von Clientkonfigurationsdateien für Point-to-Site-VPN: Zertifikatauthentifizierung'
titleSuffix: Azure VPN Gateway
description: Erfahren Sie, wie Sie VPN-Clientkonfigurationsdateien für Windows, Linux (strongSwan) und macOS generieren und installieren. Dieser Artikel gilt für VPN Gateway-P2S-Konfigurationen, die die Zertifikatauthentifizierung nutzen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/15/2021
ms.author: cherylmc
ms.openlocfilehash: efa2c4c120ab54e27126d1f40c433cdc1b66a85e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288614"
---
# <a name="generate-and-install-vpn-client-configuration-files-for-p2s-certificate-authentication"></a>Generieren und Installieren von VPN-Clientkonfigurationsdateien für die P2S-Zertifikatauthentifizierung

Wenn Sie mithilfe von Point-to-Site-VPN und Zertifikatauthentifizierung eine Verbindung mit einem Azure-VNet herstellen, verwenden Sie den VPN-Client, der nativ auf dem Betriebssystem installiert ist, von dem aus Sie eine Verbindung herstellen. Alle erforderlichen Konfigurationseinstellungen für die VPN-Clients sind in einer ZIP-Datei für die VPN-Clientkonfiguration enthalten. Die Einstellungen in der ZIP-Datei helfen Ihnen, die VPN-Clients für Windows, Mac IKEv2 VPN oder Linux einfach zu konfigurieren.

Die von Ihnen generierten VPN-Clientkonfigurationsdateien gelten speziell für die P2S-VPN-Gatewaykonfiguration für das virtuelle Netzwerk. Wenn nach dem Generieren der Dateien Änderungen an der Point-to-Site-VPN-Konfiguration vorgenommen werden, z. B. Änderungen am VPN-Protokolltyp oder am Authentifizierungstyp, müssen Sie neue VPN-Clientkonfigurationsdateien generieren und die neue Konfiguration auf alle VPN-Clients anwenden, die Sie verbinden möchten.

* Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [About Point-to-Site VPN](point-to-site-about.md) (Informationen zu P2S-VPN).
* OpenVPN-Anweisungen finden Sie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway (Point-to-Site) (Vorschau)](vpn-gateway-howto-openvpn.md) sowie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway (Vorschau)](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generieren der VPN-Clientkonfigurationsdateien

Sie können Clientkonfigurationsdateien mithilfe von PowerShell oder mithilfe des Azure-Portals erstellen. Mit beiden Methoden wird die gleiche ZIP-Datei zurückgegeben. Entzippen Sie die Datei, um die folgenden Ordner anzuzeigen:

* **WindowsAmd64** und **WindowsX86**. Diese Ordner enthalten das Windows-32-Bit- bzw. das 64-Bit-Installer-Paket. Das Installer-Paket **WindowsAmd64** gilt nicht nur für Amd, sondern für alle unterstützten 64-Bit-Windows-Clients.
* **Allgemein**: Dieser Ordner enthält allgemeine Informationen zum Erstellen Ihrer eigenen VPN-Clientkonfiguration. Der Ordner „Allgemein“ wird bereitgestellt, wenn für das Gateway IKEv2 oder SSTP und IKEv2 konfiguriert wurde. Wenn nur SSTP konfiguriert ist, ist der Ordner „Allgemein“ nicht vorhanden.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Erstellen von Dateien über das Azure-Portal

1. Navigieren Sie im Azure-Portal zum Gateway für das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen möchten.
1. Wählen Sie auf der Seite des Gateways für virtuelle Netzwerke die Option **Point-to-Site-Konfiguration** aus, um die Seite „Point-to-Site-Konfiguration“ zu öffnen.
1. Wählen Sie oben auf der Seite „Point-to-Site-Konfiguration“ die Option **VPN-Client herunterladen** aus. Dadurch wird keine VPN-Clientsoftware heruntergeladen, sondern das Konfigurationspaket generiert, das zum Konfigurieren von VPN-Clients verwendet wird. Es dauert einige Minuten, bis das Clientkonfigurationspaket generiert wird. Während dieser Zeit sehen Sie möglicherweise keine Anzeichen, bis das Paket generiert wurde.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Herunterladen der VPN-Clientkonfiguration" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/download-client.png":::
1. Nachdem das Konfigurationspaket generiert wurde, zeigt Ihr Browser an, dass eine ZIP-Datei für die Clientkonfiguration verfügbar ist. Sie hat denselben Namen wie Ihr Gateway. Entzippen Sie die Datei, um die Ordner anzuzeigen.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generieren von Dateien mithilfe von PowerShell

1. Beim Generieren von VPN-Clientkonfigurationsdateien ist für „-AuthenticationMethod“ der Wert „EapTls“ angegeben. Erstellen Sie die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Kopieren Sie die URL in Ihren Browser, um die ZIP-Datei herunterzuladen. Entzippen Sie dann die Datei, um die Ordner anzuzeigen.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-macos"></a><a name="installmac"></a>Mac (macOS)

Um eine Verbindung mit Azure herzustellen, müssen Sie den nativen IKEv2-VPN-Client manuell konfigurieren. Azure stellt keine *mobileconfig*-Datei zur Verfügung. Alle Informationen, die Sie für die Konfiguration benötigen, finden Sie im Ordner **Allgemein**. 

Enthält der Download nicht den Ordner „Allgemein“, wurde als Tunneltyp wahrscheinlich nicht IKEv2 ausgewählt. Beachten Sie, dass die Basic-SKU des VPN-Gateways IKEv2 nicht unterstützt. Vergewissern Sie sich auf dem VPN-Gateway, dass die SKU nicht „Basic“ lautet. Wählen Sie dann IKEv2 aus, und generieren Sie die ZIP-Datei erneut, um den Ordner „Allgemein“ zu erhalten.

Der Ordner „Allgemein“ enthält die folgenden Dateien:

* **VpnSettings.xml**. Diese Datei enthält wichtige Einstellungen wie Serveradresse und Tunneltyp. 
* **VpnServerRoot.cer**. Diese Datei enthält das Stammzertifikat, das zum Überprüfen des Azure-VPN-Gateways während der P2S-Verbindungseinrichtung erforderlich ist.

Führen Sie die folgenden Schritte aus, um den nativen VPN-Client auf dem Mac für die Zertifkatauthentifizierung zu konfigurieren. Diese Schritte müssen auf jedem Mac ausgeführt werden, für den Sie eine Verbindung mit Azure herstellen möchten.

### <a name="import-root-certificate-file"></a>Importieren einer Stammzertifikatdatei

1. Kopieren Sie in die Stammzertifikatdatei auf Ihren Mac. Doppelklicken Sie auf das Zertifikat. Das Zertifikat wird entweder automatisch installiert, oder es wird die Seite **Zertifikate hinzufügen** angezeigt.
1. Wählen Sie auf der Seite **Zertifikate hinzufügen** in der Dropdownliste die Option **Anmelden** aus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/login.png" alt-text="Screenshot: Seite „Zertifikate hinzufügen“, wobei „Anmelden“ ausgewählt ist":::
1. Klicken Sie auf **Hinzufügen**, um die Datei zu importieren.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add.png" alt-text="Screenshot: Seite „Zertifikate hinzufügen“, wobei „Hinzufügen“ ausgewählt ist":::

### <a name="verify-certificate-install"></a>Überprüfen der Zertifikatinstallation

Stellen Sie sicher, dass sowohl das Client- als auch das Stammzertifikat installiert sind. Ein Clientzertifikat wird für die Authentifizierung verwendet und ist erforderlich. Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).

1. Öffnen Sie die Anwendung **Keychain Access**.
1. Navigieren Sie zur Registerkarte **Certificates** (Zertifikate).
1. Stellen Sie sicher, dass sowohl das Client- als auch das Stammzertifikat installiert sind.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/keychain.png" alt-text="Screenshot: Keychain Access mit installierten Zertifikaten" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/keychain.png":::
  
### <a name="create-vpn-client-profile"></a>Erstellen des VPN-Clientprofils

1. Navigieren Sie zu **Systemeinstellungen -> Netzwerk**. Wählen Sie auf der Seite „Netzwerk“ die Option **'+'** aus, um ein neues VPN-Clientverbindungsprofil für eine P2S-Verbindung mit dem virtuellen Azure-Netzwerk zu erstellen.
1. Wählen Sie in der Dropdownliste für **Schnittstelle** die Option **VPN** aus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-vpn.png" alt-text="Screenshot: Fenster „Netzwerk“ mit der Option zum Auswählen einer Schnittstelle und ausgewählter Option „VPN“" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-vpn.png":::

1. Wähle Sie für **VPN-Typ** aus der Dropdownliste die Option **IKEv2** aus. Geben Sie im Feld **Dienstname** einen Anzeigenamen für das Profil an.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-type.png" alt-text="Screenshot: Fenster „Netzwerk“ mit der Option zum Auswählen einer Schnittstelle und eines VPN-Typs und Eingeben eines Dienstnamens" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-type.png":::

1. Wählen Sie **Erstellen** aus, um das Verbindungsprofil für den VPN-Client zu erstellen.
1. Öffnen Sie im Ordner **Allgemein** die Datei **VpnSettings.xml** mithilfe eines Text-Editors, und kopieren Sie den **VpnServer**-Tagwert.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server-tag.png" alt-text="Screenshot: Geöffnete Datei „VpnSettings.xml“ mit hervorgehobenem Tag „VpnServer“" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/server-tag.png":::

1. Fügen Sie den **VpnServer**-Tagwert in die Felder **Serveradresse** und **Remote-ID** des Profils ein.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/paste-value.png" alt-text="Screenshot: Fenster „Netzwerk“ mit dem eingefügten Wert" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/paste-value.png":::

1. Konfigurieren Sie die Authentifizierungseinstellungen. Es gibt zwei Sätze von Anweisungen. Wählen Sie die Anweisungen aus, die Ihrer Betriebssystemversion entsprechen.

   **Catalina:** 

     * Wählen Sie für **Authentifizierungseinstellungen** die Option **Keine** aus. 
     * Wählen Sie **Zertifikat**  aus, klicken Sie auf **Auswählen**, und wählen Sie das richtige Clientzertifikat aus, das Sie zuvor installiert haben. Klicken Sie dann auf **OK**.
   
        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Screenshot des Fensters „Netzwerk“, in dem „Keine“ für Authentifizierungseinstellungen und ein Zertifikat ausgewählt sind":::

   **Big Sur:**

      * Klicken Sie auf **Authentifizierungseinstellungen**, und wählen Sie dann **Zertifikat** aus. 

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-certificate.png" alt-text="Screenshot: „Authentifizierungseinstellungen“ mit ausgewählter Option „Zertifikat“" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/authentication-certificate.png":::

      * Klicken Sie auf **Select** (Auswählen), um die Seite **Choose An Identity** (Identität auswählen) zu öffnen. Die Seite **Choose An Identity** (Identität auswählen) zeigt eine Liste mit Zertifikaten zur Auswahl an. Wenn Sie nicht sicher sind, welches Zertifikat Sie verwenden sollen, können Sie auf **Show Certificate** (Zertifikat anzeigen) klicken, um weitere Informationen zu den einzelnen Zertifikaten anzuzeigen.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/show-certificate.png" alt-text="Screenshot: Zertifikateigenschaften" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/show-certificate.png":::
      * Wählen Sie das richtige Zertifikat und dann **Weiter** aus.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-identity.png" alt-text="Screenshot: Auswählen einer Identität, wobei Sie ein Zertifikat auswählen können" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-identity.png":::
   
      * Überprüfen Sie auf der Seite **Authentifizierungseinstellungen**, ob das richtige Zertifikat angezeigt wird, und klicken Sie dann auf **OK**.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Screenshot des Dialogfelds „Choose An Identity“ (Identität auswählen), in dem Sie das richtige Zertifikat auswählen können" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/certificate.png":::

1. Geben Sie sowohl für Catalina als auch für Big Sur im Feld **Local ID** (Lokale ID) den Namen des Zertifikats an. In diesem Beispiel lautet er `P2SChildCert`.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/local-id.png" alt-text="Screenshot: Wert der lokalen ID" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/local-id.png":::
1. Wählen Sie **Übernehmen** aus, um alle Änderungen zu speichern. 
1. Wählen Sie **Verbinden** aus, um die P2S-Verbindung mit dem virtuellen Azure-Netzwerk zu starten.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="Screenshot: Schaltfläche „Verbinden“" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

1. Sobald die Verbindung hergestellt ist, wird der Status **Verbunden** angezeigt, und Sie können die IP-Adresse anzeigen, die aus dem Adresspool des VPN-Clients gepullt wurde.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/connected.png" alt-text="Screenshot: Verbunden" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/connected.png":::

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan mit grafischer Benutzeroberfläche)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Installieren von strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generieren von Zertifikaten

Wenn Sie noch keine Zertifikate generiert haben, verwenden Sie die folgenden Schritte:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Installieren und konfigurieren

Die folgenden Anweisungen wurden für Ubuntu 18.0.4 erstellt. Ubuntu 16.0.10 unterstützt die strongSwan-GUI nicht. Wenn Sie Ubuntu 16.0.10 einsetzen möchten, müssen Sie die [Befehlszeile](#linuxinstallcli) verwenden. Die folgenden Beispiele entsprechen abhängig von Ihrer Version von Linux und strongSwan möglicherweise nicht den Bildschirmen, die Ihnen angezeigt werden.

1. Öffnen Sie das **Terminal**, und installieren Sie **strongSwan** und den Netzwerk-Manager, indem Sie den Befehl im Beispiel ausführen.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Wählen Sie **Einstellungen** aus, und wählen Sie dann **Netzwerk** aus. Erstellen Sie über die Schaltfläche **+** eine neue Verbindung.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Screenshot der Seite mit Netzwerkverbindungen" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/edit-connections.png":::

1. Wählen Sie im Dropdownmenü **IPsec/IKEv2 (strongSwan)** aus, und doppelklicken Sie darauf.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Screenshot der Seite zum Hinzufügen eines VPN" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/add-connection.png":::

1. Fügen Sie auf der Seite **VPN hinzufügen** einen Namen für die VPN-Verbindung hinzu.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Screenshot der Auswahl eines Verbindungstyps" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-type.png":::
1. Öffnen Sie die Datei **VpnSettings.xml**, die sich im Ordner **Generic** der heruntergeladenen Clientkonfigurationsdateien befindet. Suchen Sie das Tag **VpnServer**, und kopieren Sie den Namen, der mit „azuregateway“ beginnt und mit „cloudapp.net“ endet.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="Screenshot des Datenkopiervorgangs" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-server.png":::
1. Fügen Sie den Namen im Feld **Adresse** Ihrer neuen VPN-Verbindung im Abschnitt **Gateway** ein. Wählen Sie anschließend das Ordnersymbol am Ende des Felds **Zertifikat**, navigieren Sie zum Ordner **Allgemein**, und wählen Sie dort die Datei **VpnServerRoot**.
1. Wählen Sie im Abschnitt **Client** der Verbindung für **Authentifizierung** die Option **Certificate/private key** aus. Wählen Sie für **Zertifikat** und **Privater Schlüssel** das Zertifikat und den privaten Schlüssel aus, die zuvor erstellt wurden. Wählen Sie in **Optionen** die Option **Innere IP-Adresse anfordern** aus. Klicken Sie anschließend auf **Hinzufügen**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Screenshot der Option „Innere IP-Adresse anfordern“" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/ip-request.png":::

1. Aktivieren Sie die Verbindung (**Ein**).

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Screenshot des Kopiervorgangs" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/turn-on.png":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan mit Befehlszeilenschnittstelle)

### <a name="install-strongswan"></a>Installieren von strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generieren von Zertifikaten

Wenn Sie noch keine Zertifikate generiert haben, verwenden Sie die folgenden Schritte:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installieren und konfigurieren

1. Laden Sie das VPNClient-Paket aus dem Azure-Portal herunter.
1. Entpacken Sie die Datei.
1. Kopieren oder verschieben Sie die Datei **VpnServerRoot.cer** aus dem Ordner **Allgemein** nach **/etc/ipsec.d/cacerts**.
1. Kopieren oder verschieben Sie **cp client.p12** nach **/etc/ipsec.d/private/** . Diese Datei ist das Clientzertifikat für das VPN-Gateway.
1. Öffnen Sie Datei **VpnSettings.xml**, und kopieren Sie den Wert `<VpnServer>`. Dieser Wert wird im nächsten Schritt verwendet.
1. Passen Sie die Werte im folgenden Beispiel an, und fügen Sie das Beispiel dann in die Konfiguration **/etc/ipsec.conf** ein.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Fügen Sie die folgenden Werte in **/etc/ipsec.secrets** ein.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Führen Sie die folgenden Befehle aus:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Nächste Schritte

Kehren Sie zum ursprünglichen Artikel zurück, von dem aus Sie begonnen haben, und schließen Sie dann die P2S-Konfiguration ab.

* [Konfigurationsschritte in PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Konfigurationsschritte im Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
