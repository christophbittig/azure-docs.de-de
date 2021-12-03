---
title: Verbinden von Azure Percept DK über LTE mithilfe eines MultiTech MultiConnect-USB-Modems
description: Dieser Artikel erläutert, wie Sie Azure Percept DK über 5G- oder LTE-Netzwerke mithilfe eines MultiTech MultiConnect-USB-Modems verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5000c7be25a82069da0585a40252c91262a7ea12
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252438"
---
# <a name="connect-azure-percept-dk-over-lte-by-using-a-multitech-multiconnect-usb-modem"></a>Verbinden von Azure Percept DK über LTE mithilfe eines MultiTech MultiConnect-USB-Modems 

In diesem Artikel wird erläutert, wie Sie Ihr Azure Percept DK mit einem MultiTech MultiConnect-USB-Modem (MTCM-LNA3-B03) verbinden. 

> [!Note]
> Das MultiTech MultiConnect-USB-Modem ist in einer Vielzahl von Modellen erhältlich. In diesem Artikel haben wir das Modell LNA3 verwendet, das unter anderem mit SIM-Karten von Verizon und Vodafone funktioniert. Derzeit können wir keine Verbindung mit einem AT&T-Netzwerk herstellen, aber wir untersuchen das Problem und aktualisieren diesen Artikel, falls und sobald wir die Grundursache finden. Weitere Informationen zum MultiTech MultiConnect-USB-Modem finden Sie auf der [MultiTech](https://www.multitech.com/brands/multiconnect-microcell)-Site.

## <a name="prepare-to-connect-azure-percept-dk"></a>Vorbereiten der Verbindungsherstellung für Azure Percept DK
Informationen zum Vorbereiten von Azure Percept DK finden Sie unter [Verbinden von Azure Percept über 5G- oder LTE-Netzwerke per USB-Modem](./connect-over-cellular-usb.md). Beachten Sie unbedingt die Kommentare zu den USB-Kabeln, die verwendet werden sollten. 

### <a name="prepare-the-modem"></a>Vorbereiten des Modems
Bevor Sie beginnen, muss sich Ihr Modem im MBIM-Modus (Mobile Broadband Interface Model) befinden. Informationen zum Vorbereiten des Modems finden Sie im [Attention (AT)-Befehlsreferenz-Leitfaden von Telit-Drahtloslösungen](https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf).

In diesem Artikel verwenden wir zum Aktivieren der MBIM-Schnittstelle den AT-Befehl `AT#USBCFG=<mode>`, um den richtigen USB-Modus zu konfigurieren.

Im AT-Befehlsreferenz-Leitfaden werden alle möglichen Modi aufgeführt, doch für diesen Artikeln sind wir am Modus `3` interessiert. Der Standardmodus ist `0`.

Die einfachste Möglichkeit zum Konfigurieren des Modus besteht im Verbinden des MultiTech-Modems mit einem PC und der Verwendung von Terminalsoftware wie TeraTerm oder PuTTY. Sie können den Geräte-Manager von Windows verwenden, um festzustellen, welcher USB-Anschluss dem Modem zugewiesen ist. Wenn mehrere Ports vorhanden sind, müssen Sie möglicherweise testen, welcher auf AT-Befehle reagiert. Die Einstellungen für COM-Ports sollten wie folgt aussehen:
* **Baudrate**: 9600 (oder 115200)
* **Stoppbits**: 1
* **Parität**: Keine
* **Bytegröße**: 8
* **Ablaufsteuerung**: Keine Ablaufsteuerung

Hier sehen Sie die AT-Befehle:

Um zu überprüfen, in welchem USB-Modus das MultiTech-Gerät derzeit ausgeführt wird, verwenden Sie folgenden Befehl:

```
AT#USBCFG?
```

Gehen Sie wie folgt vor, um in den Modus 3 zu wechseln:

```
AT#USBCFG=3
```

Wenn Sie dies mit dem ersten AT-Befehl erneut überprüfen, sollten Sie Folgendes erhalten: `#USBCFG: 3`

Nachdem Sie den richtigen USB-Modus festgelegt haben, sollten Sie auf folgende Weise eine Zurücksetzung vornehmen:

```
AT#REBOOT
```

An diesem Punkt sollte das Modem getrennt und später wieder mithilfe des zuvor festgelegten Modus mit dem USB-Anschluss verbunden werden.

## <a name="use-the-modem-to-connect"></a>Verwenden des Modems zum Herstellen der Verbindung

Stellen Sie sicher, dass Sie die im Artikel [Verbinden mithilfe eines USB-Modems](./connect-over-cellular-usb.md) dargelegten Vorbereitungen für Azure Percept DK durchgeführt haben.   

1. Stecken Sie eine SIM-Karte in das MultiTech-Modem ein.

1. Schließen Sie das MultiTech-Modem an den Azure Percept DK-USB-A-Port an.

1. Schalten Sie das Azure Percept DK ein.

1. Stellen Sie eine Verbindung mit dem Azure Percept DK mithilfe des SSH-Netzwerkprotokolls (Secure Shell) her.

1. Stellen Sie sicher, dass ModemManager ausgeführt wird, indem Sie den folgenden Befehl an Ihrer SSH-Eingabeaufforderung eingeben:

    ```
    systemctl status ModemManager
    ```
    Wenn dies erfolgreich ist, erhalten Sie ein Ergebnis, das dem folgenden ähnelt:

    *ModemManager.service – Modem Manager* *Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* *Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

1. Auflisten der aktiven Modems.

    Um sicherzustellen, dass ModemManager Ihr Modem erkennen kann, führen Sie Folgendes aus:

    ```
    mmcli --list-modems
    ```

    Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160
    ```

1. Abrufen der Modemdetails.

    In diesem Fall lautet die Modem-ID `0`, aber Ihr Ergebnis kann abweichen. Die Modem-ID (`--modem 0`) wird in den ModemManager-Befehlen wie folgt verwendet:
    
    ```
    mmcli --modem 0
    ```
    
    Standardmäßig ist das Modem deaktiviert (`Status -> state: disabled`).

    ```
    --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
      --------------------------------
      Hardware |         manufacturer: Telit
              |                model: FIH7160
              |    firmware revision: 20.00.525
              |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
              |              drivers: cdc_acm, cdc_mbim
              |               plugin: telit
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
              |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
              |                       wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 3g, 4g; preferred: none
              |              current: allowed: 3g, 4g; preferred: none
      --------------------------------
      Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
              |                       eutran-13, eutran-17
              |              current: utran-2, eutran-2
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

    Ihr Mobilfunkanbieter stellt einen APN bereit, z. B. den folgenden APN für Verizon:

    ```
    mmcli --modem 0 --simple-connect="apn=vzwinternet"  
    ```

    Sie sollten etwa folgende Antwort erhalten: „Modem erfolgreich aktiviert“ (successfully enabled the modem).

1. Abrufen des Modemstatus.

    Sie sollten nun den Status `Status -> state: connected` und eine neue Kategorie `Bearer` am Ende der Statusmeldung sehen.

    ```
    mmcli --modem 0
    ```

    ```
    --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
      --------------------------------
      Hardware |         manufacturer: Telit
              |                model: FIH7160
              |    firmware revision: 20.00.525
              |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
              |              drivers: cdc_acm, cdc_mbim
              |               plugin: telit
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
              |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
              |                       wwan0 (net)
      --------------------------------
      Numbers  |                  own: +1xxxxxxxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 16% (recent)
      --------------------------------
      Modes    |            supported: allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 3g, 4g; preferred: none
              |              current: allowed: 3g, 4g; preferred: none
      --------------------------------
      Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
              |                       eutran-13, eutran-17
              |              current: utran-2, eutran-2
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 311480
              |        operator name: Verizon
              |         registration: home
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
      --------------------------------
      Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
    ```

1. Abrufen der Bearerdetails.

    Sie benötigen Bearerdetails, um das Betriebssystem mit der Paketdatenverbindung zu verbinden, die das Modem jetzt mit dem Mobilfunknetz hergestellt hat. An diesem Punkt verfügt das Modem über eine IP-Verbindung, aber das Betriebssystem ist noch nicht für deren Verwendung konfiguriert.
  
    ```
    mmcli --bearer 0
    ```

    Die Bearerdetails werden im folgenden Code aufgeführt:

    ```
    ------------------------------------
      General            |           path: /org/freedesktop/ModemManager1/Bearer/0
                        |           type: default
      ------------------------------------
      Status             |      connected: yes
                        |      suspended: no
                        |      interface: wwan0
                        |     ip timeout: 20
      ------------------------------------
      Properties         |            apn: vzwinternet
                        |        roaming: allowed
      ------------------------------------
      IPv4 configuration |         method: static
                        |        address: 100.112.107.46
                        |         prefix: 24
                        |        gateway: 100.112.107.1
                        |            dns: 198.224.166.135, 198.224.167.135
      ------------------------------------
      Statistics         |       duration: 119
                        |       attempts: 1
                        | total-duration: 119
    ```

1. Öffnen der Netzwerkschnittstelle.

    ```
    sudo ip link set dev wwan0 up
    ```

1. Konfigurieren der Netzwerkschnittstelle.

    Ersetzen Sie die IP-Adresse (z. B. hier 100.112.107.46/24) durch die IP-Adresse Ihres Bearers, indem Sie die Informationen aus den Bearerdetails verwenden:

    ```
    sudo ip address add 100.112.107.46/24 dev wwan0
    ```

1. Überprüfen der IP-Informationen.

    Die IP-Konfiguration für diese Schnittstelle sollte mit den Details des ModemManager-Bearers übereinstimmen. Führen Sie Folgendes aus:

    ```
    sudo ip address show dev wwan0
    ```

    Ihre Bearer-IP-Adresse wird wie hier gezeigt aufgeführt:

    ```
    6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
        inet 100.112.107.46/24 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
          valid_lft forever preferred_lft forever
    ```

1. Festlegen der Standardroute.

    Verwenden Sie erneut die vom Bearer bereitgestellten Informationen, und verwenden Sie das Gateway des Modems (ersetzen Sie 100.112.107.1) als Standardziel für Netzwerkpakete. Führen Sie Folgendes aus:

    ```
    sudo ip route add default via 100.112.107.1 dev wwan0
    ```

    Azure Percept DK ist jetzt mit dem USB-Modem verbunden.

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

## <a name="next-steps"></a>Nächste Schritte

Abhängig vom Mobilfunkgerät, auf das Sie Zugriff haben, können Sie auf zwei Arten eine Verbindung herstellen:

* [Verbinden mithilfe eines USB-Modems](./connect-over-cellular-usb.md)
* [Verbinden mittels 5G oder LTE](./connect-over-cellular.md)
