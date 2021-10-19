---
title: Verwenden von Wi-Fi-Profilen mit Azure Stack Edge Mini R-Geräten
description: Beschreibt das Erstellen von Wi-Fi-Profilen für Azure Stack Edge Mini R-Geräte in hochsicheren Unternehmensnetzwerken und privaten Netzwerken.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 34fe33f3c1d5be747e96ae71567424026c7c7a2a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669205"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Verwenden von Wi-Fi-Profilen mit Azure Stack Edge Mini R-Geräten

Dieser Artikel beschreibt, wie Sie Profile für drahtlose Netzwerke (Wi-Fi) mit Ihren Azure Stack Edge Mini R-Geräten verwenden.

Wie Sie das Wi-Fi-Profil vorbereiten, hängt von der Art des drahtlosen Netzwerks ab:

- In einem Wi-Fi Protected Access 2 (WPA2) - Personal Network, wie z. B. einem Heimnetzwerk oder einem offenen Wi-Fi-Hotspot, können Sie möglicherweise ein vorhandenes WLAN-Profil mit demselben Kennwort, das Sie auch für andere Geräte verwenden, herunterladen und verwenden.

- In einer hochsicheren Unternehmensumgebung greifen Sie über ein WPA2 - Enterprise-Netzwerk auf Ihr Gerät zu. Bei dieser Art von Netzwerk hat jeder Client-Computer ein eigenes Wi-Fi-Profil und wird über Zertifikate authentifiziert. Sie müssen mit Ihrem Netzwerkadministrator zusammenarbeiten, um die erforderliche Konfiguration zu ermitteln.

Wir werden die Profilanforderungen für beide Netzwerktypen später weiter besprechen.

In jedem Fall ist es sehr wichtig, dass Sie sicherstellen, dass das Profil die Sicherheitsanforderungen Ihrer Organisation erfüllt, bevor Sie das Profil mit Ihrem Gerät testen oder verwenden.

## <a name="about-wi-fi-profiles"></a>Über Wi-Fi-Profile

Ein Wi-Fi-Profil enthält die SSID (Service Set Identifier oder **Netzwerkname**), den Kennwortschlüssel und die Sicherheitsinformationen, die erforderlich sind, um Ihr Azure Stack Edge Mini R-Gerät mit einem Drahtlosnetzwerk zu verbinden.

Das folgende Codebeispiel zeigt die Grundeinstellungen für ein Profil zur Verwendung mit einem typischen drahtlosen Netzwerk:

* `SSID` ist der Netzwerkname.

* `name` ist der benutzerfreundliche Name für die Wi-Fi-Verbindung. Das ist der Name, den die Benutzer sehen, wenn sie die verfügbaren Verbindungen auf ihrem Gerät durchsuchen.

* Das Profil ist so konfiguriert, dass der Computer automatisch mit dem drahtlosen Netzwerk verbunden wird, wenn er sich in Reichweite des Netzwerks (`connectionMode` = `auto`) befindet.

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Weitere Informationen zu Wi-Fi Profileinstellungen finden Sie **unter Enterprise-Profil** in [Hinzufügen der Wi-Fi Einstellungen für Windows 10 und neuere Geräte](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile). Weitere Informationen finden Sie unter [Konfigurieren von Cisco Wi-Fi-Profil](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Um drahtlose Verbindungen auf einem Azure Stack Edge Mini R-Gerät zu aktivieren, konfigurieren Sie den Wi-Fi-Port auf Ihrem Gerät und fügen dann das/die Wi-Fi-Profil(e) zum Gerät hinzu. In einem Unternehmensnetzwerk laden Sie außerdem Zertifikate auf das Gerät. Sie können dann über die lokale Web-Benutzeroberfläche des Geräts eine Verbindung zu einem drahtlosen Netzwerk herstellen. Weitere Informationen finden Sie unter [Verwalten der WLAN-Konnektivität auf Ihrem Azure Stack Edge Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Profil für WPA2 - Persönliches Netzwerk

In einem Wi-Fi Protected Access 2 (WPA2) - Persönliches Netzwerk, wie z. B. einem Heimnetzwerk oder einem offenen Wi-Fi-Hotspot, können mehrere Geräte das gleiche Profil und das gleiche Passwort verwenden. In Ihrem Heimnetzwerk verwenden Ihr Mobiltelefon und Ihr Laptop das gleiche WLAN-Profil und Kennwort, um sich mit dem Netzwerk zu verbinden.

Zum Beispiel kann ein Windows 10-Client ein Laufzeitprofil für Sie generieren. Wenn Sie sich beim drahtlosen Netzwerk anmelden, werden Sie zur Eingabe des Wi-Fi-Passworts aufgefordert, und sobald Sie dieses Passwort eingegeben haben, sind Sie verbunden. In dieser Umgebung ist kein Zertifikat erforderlich.

Bei dieser Art von Netzwerk können Sie möglicherweise ein Wi-Fi-Profil von Ihrem Laptop exportieren und es dann zu Ihrem Azure Stack Edge Mini R-Gerät hinzufügen. Anweisungen finden Sie weiter unten unter [Exportieren eines Wi-Fi Profils](#export-a-wi-fi-profile).

> [!IMPORTANT]
> Bevor Sie ein Wi-Fi-Profil für Ihr Azure Stack Edge Mini R-Gerät erstellen, wenden Sie sich an Ihren Netzwerkadministrator, um sich über die Sicherheitsanforderungen der Organisation für drahtlose Netzwerke zu informieren. Sie sollten kein Wi-Fi-Profil auf Ihrem Gerät testen oder verwenden, bis Sie wissen, dass das drahtlose Netzwerk die Anforderungen erfüllt.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profile für WPA2 - Unternehmensnetzwerk

In einem Wireless Protected Access 2 (WPA2)-Unternehmensnetzwerk müssen Sie mit Ihrem Netzwerkadministrator zusammenarbeiten, um das benötigte Wi-Fi-Profil und Zertifikat zu erhalten, um Ihr Azure Stack Edge Mini R-Gerät mit dem Netzwerk zu verbinden.

Für hochsichere Netzwerke kann das Azure-Gerät Protected Extensible Authentication Protocol (PEAP) mit Extensible Authentication Protocol-Transport Layer Security (EAP-TLS) verwenden. PEAP mit EAP-TLS verwendet Maschinenauthentifizierung: Client und Server verwenden Zertifikate, um ihre Identität gegenseitig zu verifizieren.

> [!NOTE]
> * Die Benutzerauthentifizierung mit PEAP Microsoft Challenge Handshake Authentication Protocol Version 2 (PEAP MSCHAPv2) wird auf Azure Stack Edge Mini R-Geräten nicht unterstützt.
> * Für den Zugriff auf die Azure Stack Edge Mini R-Funktionalität ist eine EAP-TLS-Authentifizierung erforderlich. Eine drahtlose Verbindung, die Sie mit Active Directory eingerichtet haben, funktioniert nicht.

Der Netzwerkadministrator erstellt für jeden Computer ein eindeutiges Wi-Fi-Profil und ein Client-Zertifikat. Der Netzwerkadministrator entscheidet, ob er für jedes Gerät ein eigenes Zertifikat oder ein gemeinsames Zertifikat verwendet.

Wenn Sie an mehr als einem physischen Standort am Arbeitsplatz arbeiten, muss der Netzwerkadministrator möglicherweise mehr als ein standortspezifisches Wi-Fi-Profil und Zertifikat für Ihre drahtlosen Verbindungen bereitstellen.

In einem Unternehmensnetzwerk empfehlen wir, die Einstellungen in den Wi-Fi-Profilen, die Ihr Netzwerkadministrator bereitstellt, nicht zu ändern. Die einzige Anpassung, die Sie eventuell vornehmen möchten, ist die Einstellung der automatischen Verbindung. Weitere Informationen finden Sie unter [Grundlegendes Profil](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) in Wi-Fi Einstellungen für Windows 10 und neuere Geräte.

In einer hochsicheren Unternehmensumgebung können Sie möglicherweise ein vorhandenes Drahtlosnetzwerkprofil als Vorlage verwenden:

* Sie können das Profil des drahtlosen Firmennetzwerks von Ihrem Arbeitscomputer herunterladen. Anweisungen finden Sie weiter unten unter [Exportieren eines Wi-Fi Profils](#export-a-wi-fi-profile).

* Wenn andere in Ihrer Organisation bereits eine Verbindung zu ihren Azure Stack Edge Mini R-Geräten über ein drahtloses Netzwerk herstellen, können sie das Wi-Fi-Profil von ihrem Gerät herunterladen. Anweisungen finden Sie unter [Wi-Fi-Profil herunterladen](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Exportieren eines WLAN-Profils

Gehen Sie folgendermaßen vor, um ein Profil für die Wi-Fi-Schnittstelle auf Ihrem Computer zu exportieren:

1. Stellen Sie sicher, dass der Computer, den Sie zum Exportieren des WLAN-Profils verwenden, eine Verbindung mit dem WLAN herstellen kann, das Ihr Gerät verwenden wird.

1. Um die drahtlos Profile auf Ihrem Computer anzuzeigen, öffnen Sie im **Startmenü** die **Eingabeaufforderung** (cmd.exe), und geben Sie den folgenden Befehl ein:

   `netsh wlan show profiles`

   Die Ausgabe sieht in etwa wie folgt aus:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

1. Um ein Profil zu exportieren, geben Sie den folgenden Befehl ein:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>" key=clear`

   Der folgende Befehl speichert z. B. das ContosoFTINET-Profil im XML-Format im Ordner Downloads für den Benutzer namens `gusp`.

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads key=clear

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Zertifikat, Wi-Fi-Profil zum Gerät hinzufügen

Wenn Sie die benötigten Wi-Fi-Profile und -Zertifikate haben, führen Sie die folgenden Schritte aus, um Ihr Azure Stack Edge Mini R-Gerät für drahtlose Verbindungen zu konfigurieren:

1. Für ein WPA2 - Enterprise-Netzwerk laden Sie die benötigten Zertifikate auf das Gerät hoch, indem Sie die Anleitung in [Zertifikate hochladen](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Laden Sie das/die Wi-Fi-Profil(e) auf das Mini R-Gerät hoch und stellen Sie dann eine Verbindung her, indem Sie der Anleitung in [Hinzufügen, mit Wi-Fi-Profil verbinden](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie das [Netzwerk für Azure Stack Edge Mini R konfigurieren](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Erfahren Sie, [wie Sie Wi-Fi auf Ihrem Azure Stack Edge Mini R verwalten können](azure-stack-edge-mini-r-manage-wifi.md).
