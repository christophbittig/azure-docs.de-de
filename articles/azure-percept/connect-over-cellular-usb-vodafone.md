---
title: Verbinden von Azure Percept DK über 5G und LTE mithilfe eines USB-Modems von Vodafone
description: In diesem Artikel wird erläutert, wie Sie Azure Percept DK über 5G- und LTE-Netzwerke mithilfe eines USB-Modems von Vodafone verbinden können.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 226fc6890253bd4701b400dcd42c420618701630
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252362"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-by-using-a-vodafone-usb-connect-4g-v2-modem"></a>Verbinden von Azure Percept DK über 5G und LTE mithilfe eines USB Connect 4G v2-Modems von Vodafone

In diesem Artikel wird erläutert, wie Sie Azure Percept DK mithilfe eines USB Connect 4G v2-Modems von Vodafone verbinden.

Weitere Informationen zu diesem Modem finden Sie auf der Seite [Integrated Terminals von Vodafone](https://www.vodafone.com/business/iot/iot-devices/integrated-terminals).

## <a name="use-the-modem-to-connect"></a>Verwenden des Modems zum Herstellen der Verbindung

Bevor Sie beginnen, stellen Sie sicher, dass Sie Azure Percept DK für [das Verbinden mithilfe eines USB-Modems](./connect-over-cellular-usb.md) vorbereitet haben. Eine Vorbereitung des USB-Modems selbst ist nicht erforderlich.   

1. Stecken Sie eine SIM-Karte in das Vodafone-Modem ein.

1. Schließen Sie das Vodafone-Modem an den Azure Percept-USB A-Anschluss an.

1. Schalten Sie das Azure Percept DK ein.

1. Stellen Sie eine Verbindung mit dem Azure Percept DK mithilfe des SSH-Netzwerkprotokolls (Secure Shell) her.

1. Stellen Sie sicher, dass ModemManager ausgeführt wird, indem Sie den folgenden Befehl an Ihrer SSH-Eingabeaufforderung eingeben:

    ```
    systemctl status ModemManager
    ```

    Wenn dies erfolgreich ist, erhalten Sie ein Ergebnis, das dem folgenden ähnelt:

    ```
    ModemManager.service - Modem Manager
    Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)
    Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago
    ```

1. Auflisten der aktiven Modems.

    Um sicherzustellen, dass ModemManager Ihr Modem erkennen kann, führen Sie Folgendes aus:

    ```
    mmcli --list-modems
    ```

    Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt. In diesem Fall lautet die Modem-ID `0`, aber Ihr Ergebnis kann abweichen.

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband
    ```

1. Abrufen der Modemdetails.

    Um die Details zum Modemstatus abzurufen, führen Sie den folgenden Befehl aus (wobei die Modem-ID `0` lautet).

    ```
    mmcli --modem 0
    ```

    Standardmäßig ist das Modem deaktiviert (`Status -> state: disabled`).

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
    ```

    Es wird empfohlen, dass Sie mit der Standardeinstellung beginnen: 
    
    `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`. 
    
    Wenn Sie diese Einstellung nicht bereits verwenden, führen Sie Folgendes aus:
    
     `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

1. Aktivieren des Modems.

    Bevor Sie eine Verbindung herstellen, schalten Sie die Funkverbindung(en) des Modems ein, indem Sie folgenden Code ausführen:

    ```
    mmcli --modem 0 --enable
    ```

    Sie sollten etwa folgende Antwort erhalten: „Modem erfolgreich aktiviert“ (successfully enabled the modem).

    Nach einiger Zeit sollte sich das Modem bei einem Funkmast registrieren, und im Modemstatus sollte `Status -> state: registered` angezeigt werden, nachdem Sie den folgenden Code ausgeführt haben:

    ```
    mmcli --modem 0
    ```

1. Stellen Sie die Verbindung mithilfe der APN-Informationen (Access Point Name) her.

    Ihr Mobilfunkanbieter stellt einen APN bereit, z. B. den folgenden APN für Vodafone:

    ```
    mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
    ```

    Sie sollten etwa folgende Antwort erhalten: „Modem erfolgreich verbunden“ (successfully connected the modem).

1. Abrufen des Modemstatus.

    Sie sollten nun den Status `Status -> state: connected` und eine neue Kategorie `Bearer` am Ende der Statusmeldung sehen.

    ```
    mmcli --modem 0
    ```

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0-mobile.
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Numbers  |                  own: xxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (10)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 19% (recent)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 302220
              |        operator name: TELUS
              |         registration: roaming
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
      --------------------------------
      Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
    ```

1. Abrufen der Bearerdetails.

    Sie benötigen die Bearerdetails, um das Betriebssystem mit der Paketdatenverbindung zu verbinden, die das Modem jetzt mit dem Mobilfunknetz hergestellt hat. An diesem Punkt verfügt das Modem über eine IP-Verbindung, aber das Betriebssystem ist noch nicht für deren Verwendung konfiguriert.

    ```
    mmcli --bearer 0
    ```

    Die Bearerdetails werden im folgenden Code aufgeführt:

    ```
      --------------------------------
      General            |       path: /org/freedesktop/ModemManager1/Bearer/0
                        |       type: default
      --------------------------------
      Status             |  connected: yes
                        |  suspended: no
                        |  interface: wwan0
                        | ip timeout: 20
      --------------------------------
      Properties         |        apn: internet4gd.gdsp
                        |    roaming: allowed
      --------------------------------
      IPv4 configuration |     method: static
                        |    address: 162.177.2.0
                        |     prefix: 22
                        |    gateway: 162.177.2.1
                        |        dns: 10.177.0.34, 10.177.0.210
                        |        mtu: 1500
      --------------------------------
      Statistics         |   attempts: 1
    ```

1. Öffnen der Netzwerkschnittstelle.

    ```
    sudo ip link set dev wwan0 up
    ```

1. Konfigurieren der Netzwerkschnittstelle.

    Ersetzen Sie die IP-Adresse (z. B. hier 162.177.2.0/22) durch die IP-Adresse Ihres Bearers, indem Sie die Informationen aus den Bearerdetails verwenden:

    ```
    sudo ip address add 162.177.2.0/22 dev wwan0
    ```

1. Überprüfen der IP-Informationen.

    Die IP-Konfiguration für diese Schnittstelle sollte mit den Details des ModemManager-Bearers übereinstimmen. Führen Sie Folgendes aus:

    ```
    sudo ip address show dev wwan0
    ```

    Ihre Bearer-IP-Adresse wird wie hier gezeigt aufgeführt:

    ```
    wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
        inet 162.177.2.0/22 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::c012:44ff:fec4:273c/64 scope link 
          valid_lft forever preferred_lft forever
    ```

1. Festlegen der Standardroute.

    Verwenden Sie erneut die vom Bearer bereitgestellten Informationen, und verwenden Sie das Gateway des Modems (ersetzen Sie 162.177.2.1) als Standardziel für Netzwerkpakete. Führen Sie Folgendes aus:

    ```
    sudo ip route add default via 162.177.2.1 dev wwan0
    ```

    Azure Percept DK ist jetzt für das Verbinden mit Azure mithilfe des LTE-Modems aktiviert.


1. Testen der Konnektivität.

    In diesem Artikel führen Sie eine `ping`-Anforderung über die `wwan0`-Schnittstelle aus. Sie können aber auch Azure Percept Studio verwenden und überprüfen, ob Telemetrienachrichten eintreffen. Stellen Sie sicher, dass Sie kein Ethernetkabel verwenden und dass WLAN nicht aktiviert ist, damit Sie LTE verwenden. Führen Sie Folgendes aus:

    ```
    ping -I wwan0 8.8.8.8
    ```

    Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

    ```
    PING 8.8.8.8 (8.8.8.8) from 162.177.2.0 wwan0: 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=111 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=92.0 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=88.8 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 4ms
    rtt min/avg/max/mdev = 88.779/97.254/110.964/9.787 ms
    ```

## <a name="debugging"></a>Debuggen

Allgemeine Informationen zum Debuggen finden Sie unter [Verbinden mithilfe eines USB-Modems](./connect-over-cellular-usb.md).
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>Vodafone-Modemregeln zur Minimierung von Enumerationsproblemen

Um zu verhindern, dass das Modem in einem nicht unterstützten Modus enumeriert, empfiehlt es sich, die folgenden userspace/dev-Regeln (UDEV) anzuwenden, damit ModemManager unerwünschte Schnittstellen ignoriert.

Erstellen Sie eine Datei */usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules* mit folgendem Inhalt:

```
ACTION!="add|change|move|bind", GOTO="mm_vodafone_port_types_end"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1bbb", GOTO="mm_vodafone_generic_vendorcheck"
GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_generic_vendorcheck"
SUBSYSTEMS=="usb", ATTRS{bInterfaceNumber}=="?*", ENV{.MM_USBIFNUM}="$attr{bInterfaceNumber}"

# Interface 1 is QDCM (ignored) and interfaces 3 and 4 are MBIM Control and Data.
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="00", ENV{ID_MM_PORT_TYPE_AT_PRIMARY}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="01", ENV{ID_MM_PORT_IGNORE}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="02", ENV{ID_MM_PORT_AT_SECONDARY}="1"

GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_port_types_end"
```

Laden Sie die UDEV-Regeln nach ihrer Installation erneut, und starten Sie ModemManager neu:

```
udevadm control -R
systemctl restart ModemManager
```

## <a name="next-steps"></a>Nächste Schritte

Abhängig vom Mobilfunkgerät, auf das Sie Zugriff haben, können Sie auf zwei Arten eine Verbindung herstellen:

* [Verbinden mithilfe eines USB-Modems](./connect-over-cellular-usb.md)
* [Verbinden mittels 5G oder LTE](./connect-over-cellular.md)
