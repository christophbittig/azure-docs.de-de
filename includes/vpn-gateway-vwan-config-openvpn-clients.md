---
title: Datei einfügen
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e142cbbb3980ddd491b4d3ac8d35bd9f0e0d6633
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801938"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows-Clients

1. Laden Sie den OpenVPN-Client (mindestens Version 2.4) von der offiziellen [OpenVPN-Website](https://openvpn.net/index.php/open-source/downloads.html) herunter, und installieren Sie ihn.
2. Laden Sie das VPN-Clientprofilpaket vom Azure-Portal herunter, oder verwenden Sie das Cmdlet „New-AzVpnClientConfiguration“ in PowerShell.
3. Entzippen Sie das Profil. Öffnen Sie anschließend in Editor im Ordner „OpenVPN“ die Konfigurationsdatei *vpnconfig.ovpn*.
4. Exportieren Sie das von Ihnen erstellte und hochgeladene Point-to-Site-Clientzertifikat. Verwenden Sie die folgenden Artikellinks:

   * Anweisungen zu [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * Anweisungen zu [Virtual WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrahieren Sie den privaten Schlüssel und den Base64-Fingerabdruck aus der *PFX*-Datei. Hierfür gibt es mehrere Möglichkeiten. Die Verwendung von OpenSSL auf Ihrem Computer ist eine Möglichkeit. Die Datei *profileinfo.txt* enthält den privaten Schlüssel und den Fingerabdruck für die Zertifizierungsstelle sowie das Clientzertifikat. Verwenden Sie unbedingt den Fingerabdruck des Clientzertifikats.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Öffnen Sie *profileinfo.txt* in Editor. Um den Fingerabdruck des Clientzertifikats (untergeordnetes Zertifikat) zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN CERTIFICATE-----“ und „-----END CERTIFICATE-----“ für das untergeordnete Zertifikat, und kopieren Sie ihn. Das untergeordnete Zertifikat finden Sie in der Zeile „subject=/“.
7. Wechseln Sie zur Datei *vpnconfig.ovpn*, die Sie in Schritt 3 in Editor geöffnet haben. Suchen Sie nach dem unten gezeigten Abschnitt, und ersetzen Sie den gesamten Text zwischen „cert“ und „/cert“.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öffnen Sie die Datei *profileinfo.txt* in Editor. Um den privaten Schlüssel zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN PRIVATE KEY-----“ und „-----END PRIVATE KEY-----“, und kopieren Sie ihn.
9. Kehren Sie zur Datei „vpnconfig.ovpn“ in Editor zurück, und suchen Sie nach diesem Abschnitt. Fügen Sie den privaten Schlüssel ein, indem Sie den gesamten Text zwischen „key“ und „/key“ ersetzen.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
11. Kopieren Sie die Datei „vpnconfig.ovpn“ in den Ordner „C:\Programme\OpenVPN\config“.
12. Klicken Sie mit rechten Maustaste auf das Symbol „OpenVPN“, und wählen Sie „Verbinden“ aus.

## <a name="mac-clients"></a><a name="mac"></a>Mac-Clients

1. Laden Sie einen OpenVPN-Client wie z. B. [Tunnelblick](https://tunnelblick.net/downloads.html) herunter, und installieren Sie ihn. 
2. Laden Sie das VPN-Clientprofilpaket vom Azure-Portal herunter, oder verwenden Sie das Cmdlet „New-AzVpnClientConfiguration“ in PowerShell.
3. Entzippen Sie das Profil. Öffnen Sie in einem Text-Editor im Ordner „OpenVPN“ die Konfigurationsdatei „vpnconfig.ovpn“.
4. Füllen Sie den Abschnitt „P2S client certificate“ mit dem öffentlichen Schlüssel für das P2S-Clientzertifikat in Base64 aus. In einem Zertifikat im PEM-Format können Sie die CER-Datei öffnen und den Base64-Schlüssel zwischen den Zertifikatskopfzeilen herauskopieren. Informationen zum Exportieren eines Zertifikats zum Abrufen des codierten öffentlichen Schlüssels finden Sie unter den folgenden Artikellinks:

   * Anweisungen zu [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Anweisungen zu [Virtual WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Füllen Sie den Abschnitt „Private key“ mit dem privaten Schlüssel für das P2S-Clientzertifikat in Base64 aus. Informationen zum Extrahieren eines privaten Schlüssels finden Sie auf der OpenVPN-Website unter [Exportieren des privaten Schlüssels](https://openvpn.net/community-resources/how-to/#pki).
6. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
7. Doppelklicken Sie auf die Profildatei, um das Profil in Tunnelblick zu erstellen.
8. Starten Sie Tunnelblick im Anwendungsordner.
9. Klicken Sie im Systembereich der Taskleiste auf das Symbol von Tunnelblick, und wählen Sie „Verbinden“ aus.

> [!IMPORTANT]
>Für das OpenVPN-Protokoll werden nur iOS 11.0 und höher und MacOS 10.13 und höher unterstützt.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS-Clients

1. Installieren Sie den OpenVPN-Client (Version 2.4 oder höher) aus dem App Store.
2. Laden Sie das VPN-Clientprofilpaket vom Azure-Portal herunter, oder verwenden Sie das Cmdlet „New-AzVpnClientConfiguration“ in PowerShell.
3. Entzippen Sie das Profil. Öffnen Sie in einem Text-Editor im Ordner „OpenVPN“ die Konfigurationsdatei „vpnconfig.ovpn“.
4. Füllen Sie den Abschnitt „P2S client certificate“ mit dem öffentlichen Schlüssel für das P2S-Clientzertifikat in Base64 aus. In einem Zertifikat im PEM-Format können Sie die CER-Datei öffnen und den Base64-Schlüssel zwischen den Zertifikatskopfzeilen herauskopieren. Informationen zum Exportieren eines Zertifikats zum Abrufen des codierten öffentlichen Schlüssels finden Sie unter den folgenden Artikellinks:

   * Anweisungen zu [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Anweisungen zu [Virtual WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Füllen Sie den Abschnitt „Private key“ mit dem privaten Schlüssel für das P2S-Clientzertifikat in Base64 aus. Informationen zum Extrahieren eines privaten Schlüssels finden Sie auf der OpenVPN-Website unter [Exportieren des privaten Schlüssels](https://openvpn.net/community-resources/how-to/#pki).
6. Ändern Sie keine anderen Felder.
7. Senden Sie Profildatei (.ovpn) an Ihr in der Mail-App auf Ihrem iPhone konfiguriertes E-Mail-Konto. 
8. Öffnen Sie die E-Mail in der Mail-App auf dem iPhone, und tippen Sie auf die angefügte Datei.

    ![Öffnen der E-Mail](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Tippen Sie auf **More** (Mehr) wenn die Option **Copy to OpenVPN** (In OpenVPN kopieren) nicht angezeigt wird.

    ![Mehr](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Tippen Sie auf **Copy to OpenVPN** (In OpenVPN kopieren). 

    ![Kopieren in OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Tippen Sie auf **ADD** (Hinzufügen) auf der Seite **Import Profile** (Profil importieren).

    ![Hinzufügen](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Tippen Sie auf **ADD** (Hinzufügen) auf der Seite **Importes Profile** (Importiertes Profil).

    ![Auf „HINZUFÜGEN“ tippen](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Starten Sie die OpenVPN-App, und schieben Sie den Schalter auf der Seite **Profile** nach rechts, um eine Verbindung herzustellen.

    ![Verbinden](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux-Clients

1. Starten Sie eine neue Terminalsitzung. Sie können eine neue Sitzung öffnen, indem Sie STRG+ALT+T gleichzeitig drücken.
2. Geben Sie für die Installation der benötigten Komponenten den folgenden Befehl ein:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Laden Sie das VPN-Profil für das Gateway herunter. Das Profil kann auf der Registerkarte „Point-to-Site-Konfiguration“ im Azure-Portal heruntergeladen werden.
4. Exportieren Sie das P2S-Clientzertifikat, das Sie erstellt und in Ihre P2S-Konfiguration auf dem Gateway hochgeladen haben. Verwenden Sie die folgenden Artikellinks:

   * Anweisungen zu [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * Anweisungen zu [Virtual WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrahieren Sie den privaten Schlüssel und den Base64-Fingerabdruck aus der PFX-Datei. Hierfür gibt es mehrere Möglichkeiten. Die Verwendung von OpenSSL auf Ihrem Computer ist eine Möglichkeit.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Die Datei *profileinfo.txt* enthält den privaten Schlüssel und den Fingerabdruck für die Zertifizierungsstelle sowie das Clientzertifikat. Verwenden Sie unbedingt den Fingerabdruck des Clientzertifikats.

6. Öffnen Sie die Datei *profileinfo.txt* in einem Text-Editor. Um den Fingerabdruck des Clientzertifikats (untergeordnetes Zertifikat) zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN CERTIFICATE-----“ und „-----END CERTIFICATE-----“ für das untergeordnete Zertifikat, und kopieren Sie ihn. Das untergeordnete Zertifikat finden Sie in der Zeile „subject=/“.

7. Öffnen Sie die Datei *vpnconfig.ovpn*, und suchen Sie nach dem unten gezeigten Abschnitt. Ersetzen Sie den gesamten Text zwischen „cert“ und „/cert“.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öffnen Sie die Datei „profileinfo.txt“ in einem Text-Editor. Um den privaten Schlüssel zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN PRIVATE KEY-----“ und „-----END PRIVATE KEY-----“, und kopieren Sie ihn.

9. Öffnen Sie die Datei „vpnconfig.ovpn“ in einem Text-Editor, und suchen Sie nach diesem Abschnitt. Fügen Sie den privaten Schlüssel ein, indem Sie den gesamten Text zwischen „key“ und „/key“ ersetzen.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
11. Um über die Befehlszeile eine Verbindung herzustellen, geben Sie den folgenden Befehl ein:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Um über die grafische Benutzeroberfläche eine Verbindung herzustellen, wechseln Sie zu „Systemeinstellungen“.
13. Klicken Sie auf **+** , um eine neue VPN-Verbindung hinzuzufügen.
14. Wählen Sie unter **VPN hinzufügen**, den Befehl **Aus Datei importieren...** aus.
15. Navigieren Sie zur Profildatei, und doppelklicken Sie darauf, oder wählen Sie **Öffnen** aus.
16. Klicken Sie im Fenster **VPN hinzufügen** auf **Hinzufügen**.
  
    ![Aus Datei importieren](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Sie können eine Verbindung herstellen, indem Sie auf der Seite **Netzwerkeinstellungen** oder unter dem Netzwerksymbol auf der Taskleiste das VPN auf **EIN** festlegen.
