---
title: Verbinden von Azure Percept DK über 5G oder DSL mithilfe eines Quectel RM500 5G-Modems
description: Dieser Artikel beschreibt, wie Sie Azure Percept DK über 5G oder LTE-Netzwerke mithilfe eines Quectel 5G-Modems verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: de4f889883b7c3da4e8129fc66d31aed094ebda6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252419"
---
# <a name="connect-azure-percept-dk-over-5g-or-lte-by-using-a-quectel-rm500-gl-5g-modem"></a>Verbinden von Azure Percept DK über 5G oder DSL mithilfe eines Quectel RM500-GL-5G-Modems  

In diesem Artikel wird erläutert, wie Sie Azure Percept DK über 5G oder LTE mithilfe eines Quectel RM500-GL-5G-Modems verbinden. 

Weitere Informationen zu diesem Development-Kit für das 5G-Modem erhalten Sie von Ihrem lokalen Quectel-Vertriebsteam:

* Für Kunden in Nordamerika: northamerica-sales@quectel.com
* Für globale Kunden: sales@quectel.com

> [!Note] 
> **Informationen zu USB-Kabeln für 5G-Modems**:  
> 5G-Modems erfordern mehr Strom als LTE-Modems, und das falsche USB-Kabel kann dazu führen, dass die bestmöglichen 5G-Datenraten nicht erreicht werden können. Stellen Sie sicher, dass das USB-Kabel die folgenden Standards erfüllt, um für ein 5G-Modem ausreichend konsistente Stromversorgung zu gewährleisten:  
> **Strom:**
> - Die maximale Stromstärke sollte gleich oder größer als 3 Ampere sein.
> - Die Kabellänge sollte kürzer als 1 Meter sein.
> - Bei Verwendung von 5G-Modems sollte nur ein USB A-Anschluss für Azure Percept DK aktiv sein.
>  
> **Durchsatz:**
> - USB 3.1 Gen2
> - USB-IF-zertifiziert

## <a name="prepare-to-connect-azure-percept-dk"></a>Vorbereiten der Verbindungsherstellung für Azure Percept DK
Informationen zum Vorbereiten von Azure Percept DK finden Sie unter [Verbinden von Azure Percept über 5G- oder LTE-Netzwerke per USB-Modem](./connect-over-cellular-usb.md). Beachten Sie unbedingt die Kommentare zu den USB-Kabeln, die verwendet werden sollen. 

### <a name="prepare-the-modem"></a>Vorbereiten des Modems
Bevor Sie beginnen, muss sich Ihr Modem im MBIM-Modus (Mobile Broadband Interface Model) befinden. Für diesen Befehl kann ein nicht dokumentierter, aber standardmäßiger Quectel Attention-Befehl (AT) verwendet werden: `AT+QCFG="usbnet"`.

Die Eigenschaft `usbnet` kann auf vier verschiedene Werte festgelegt werden, von `0` bis `3`:
- `0` für den **NDIS-/PPP-/QMI-Modus** (vom Treiber `qmi_wwan` unterstützt, mit `CONFIG_USB_NET_QMI_WWAN=y|m` aktiviert)
- `1` für den **CDC-Ethernet-Modus** (wird unter Linux unterstützt, wenn `CONFIG_USB_NET_CDCETHER=y|m`)
- `2` für den **MBIM-Modus** (wird unter Linux unterstützt, wenn `CONFIG_USB_NET_CDC_MBIM=y|m`)
- `3` für den **RNDIS-Modus**

Die einfachste Möglichkeit zum Konfigurieren des Modus besteht in der Verbindung des Quectel 5G-Modems mit einem PC und der Verwendung von Terminalsoftware wie TeraTerm oder der eigenen PC-Software von Quectel wie QCOM. Sie können den Geräte-Manager von Windows verwenden, um zu nachzusehen, welcher USB-Anschluss dem Modem zugewiesen ist. Für das COM-Portal sollten folgende Einstellungen gelten:
* **Baudrate:** 115200
* **Stoppbits:** 1
* **Parität:** Kein(e)
* **Bytegröße:** 8
* **Ablaufsteuerung:** Keine Ablaufsteuerung

Hier sind die AT-Befehle:

Um zu überprüfen, welches Quectel-Gerät im USB-Modus derzeit ausgeführt wird, verwenden Sie folgenden Befehl:

```
AT+QCFG="usbnet"
```

Gehen Sie wie folgt vor, um in den Modus 2 zu wechseln:

```
AT+QCFG="usbnet",2
```

Wenn Sie dies mit dem ersten AT-Befehl erneut überprüfen, sollten Sie Folgendes erhalten:

```
+QCFG: "usbnet",2`
```

Nachdem Sie den richtigen USB-Modus festgelegt haben, sollten Sie die Hardwarezurücksetzung mit folgenden Maßnahmen ausführen:

```
AT+CFUN=1,1
```

An diesem Punkt sollte das Modem getrennt und später wieder mit dem USB-Anschluss verbunden werden.


## <a name="use-the-modem-to-connect"></a>Verwenden des Modems zum Herstellen der Verbindung

1. Setzen Sie eine SIM-Karte in das Quectel-Modem ein.

1. Schließen Sie das Quectel-Modem an den USB-Anschluss von Azure Percept DK an. Achten Sie darauf, dass Sie ein korrektes USB-Kabel verwenden.

1. Fahren Sie Azure Percept DK hoch.

1. Stellen Sie sicher, dass ModemManager ausgeführt wird.

      ```
      systemctl status ModemManager
      ```
   
      Wenn Sie erfolgreich sind, erhalten Sie ein Ergebnis, das dem folgenden ähnelt:

      ```
      * ModemManager.service - Modem Manager
         Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
         Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
      [...]
      ```

      Wenn Sie nicht erfolgreich sind, stellen Sie sicher, dass Sie das richtige Image auf Azure Percept DK (5G aktiviert) eingespielt haben.

1. Listen Sie die aktiven Modems auf.

      Wenn Sie die Modems auflisten, sollten Sie sehen, dass das Quectel-Modem erkannt wurde und jetzt von ModemManager verarbeitet wird.

      ```
      mmcli --list-modems
      ```
   
      Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

      ```
      /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
      ```

      Die Modem-ID ist hier `0` und wird in den folgenden Befehlen verwendet (d. h.: `--modem 0`).

1. Rufen Sie die Modemdetails ab.

      Standardmäßig ist das Modem deaktiviert (`Status -> state: disabled`). Führen Sie Folgendes aus, um den Status anzuzeigen:

      ```
      mmcli --modem 0
      ```
      
      Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

      ```
      General  |                    path: /org/freedesktop/ModemManager1/Modem/0
               |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
      -----------------------------------
      Hardware |            manufacturer: Quectel
               |                   model: RM500Q-GL
               |       firmware revision: RM500QGLABR11A04M4G
               |          carrier config: CDMAless-Verizon
               | carrier config revision: 0A010126
               |            h/w revision: RM500Q-GL
               |               supported: gsm-umts, lte, 5gnr
               |                 current: gsm-umts, lte, 5gnr
               |            equipment id: xxxx
      -----------------------------------
      System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
               |                 drivers: option, cdc_mbim
               |                  plugin: quectel
               |            primary port: cdc-wdm0
               |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
               |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
      -----------------------------------
      Numbers  |                     own: +1xxxx
      -----------------------------------
      Status   |          unlock retries: sim-pin2 (3)
               |                   state: disabled
               |             power state: on
               |          signal quality: 0% (cached)
      -----------------------------------
      Modes    |               supported: allowed: 3g; preferred: none
               |                          allowed: 4g; preferred: none
               |                          allowed: 3g, 4g; preferred: 4g
               |                          allowed: 3g, 4g; preferred: 3g
               |                          allowed: 5g; preferred: none
               |                          allowed: 3g, 5g; preferred: 5g
               |                          allowed: 3g, 5g; preferred: 3g
               |                          allowed: 4g, 5g; preferred: 5g
               |                          allowed: 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 5g
               |                          allowed: 3g, 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 3g
               |                 current: allowed: 3g, 4g, 5g; preferred: 5g
      -----------------------------------
      Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
               |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
      -----------------------------------
      IP       |               supported: ipv4, ipv6, ipv4v6
      -----------------------------------
      3GPP     |                    imei: xxxxxxxxxxxxxxx
               |           enabled locks: fixed-dialing
      -----------------------------------
      3GPP EPS |    ue mode of operation: csps-1
               |      initial bearer apn: ims
               |  initial bearer ip type: ipv4v6
      -----------------------------------
      SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
      ```

1. Aktivieren Sie das Modem.

      Bevor Sie eine Verbindung herstellen, schalten Sie die Funkverbindung(en) des Modems ein, indem Sie Folgendes ausführen:

      ```
      mmcli --modem 0 --enable
      ```

      Sie sollten eine Antwort erhalten, die der folgenden ähnelt:

      ```
      successfully enabled the modem
      ```
   
      Nach einiger Zeit sollte sich das Modem bei einem Funkmast registrieren und im Modemstatus `Status -> state: registered` angezeigt werden, nachdem Sie den folgenden Code ausführen:
   
      ```
      mmcli --modem 0
      ```

1. Stellen Sie die Verbindung mithilfe der APN-Informationen (Access Point Name) her.

      In der Regel stellen die Modems den zu verwendenden APN (siehe Informationen zu `3GPP EPS -> initial bearer APN`) zur Verfügung, damit Sie ihn zum Herstellen einer Verbindung verwenden können. Wenn das Modem keinen APN bereitstellt, wenden Sie sich an Ihren Mobiltelefonanbieter, um den zu verwendenden APN zu erhalten. 
      
      Hier ist der ModemManager-Befehl zum Herstellen einer Verbindung, beispielsweise mithilfe des Verizon-APN `APN=vzwinternet`.

      ```
      mmcli --modem 0 --simple-connect="apn=vzwinternet"
      ```

      Auch hier sollten Sie eine Antwort erhalten, die der folgenden ähnelt:

      ```
      successfully connected the modem
      ```

1. Rufen Sie den Modemstatus ab.

      Sie sollten nun den Status `Status -> state: connected` und eine neue Kategorie `Bearer` am Ende der Statusmeldung sehen.
 
      ```
      mmcli -m 0
      ```

      ```
      -----------------------------------
      General  |                    path: /org/freedesktop/ModemManager1/Modem/0
               |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
      -----------------------------------
      Hardware |            manufacturer: Quectel
               |                   model: RM500Q-GL
               |       firmware revision: RM500QGLABR11A04M4G
               |          carrier config: CDMAless-Verizon
               | carrier config revision: 0A010126
               |            h/w revision: RM500Q-GL
               |               supported: gsm-umts, lte, 5gnr
               |                 current: gsm-umts, lte, 5gnr
               |            equipment id: xxx
      -----------------------------------
      System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
               |                 drivers: option, cdc_mbim
               |                  plugin: quectel
               |            primary port: cdc-wdm0
               |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
               |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
      -----------------------------------
      Numbers  |                     own: +1xxxx
      -----------------------------------
      Status   |          unlock retries: sim-pin2 (3)
               |                   state: connected
               |             power state: on
               |             access tech: lte
               |          signal quality: 12% (recent)
      -----------------------------------
      Modes    |               supported: allowed: 3g; preferred: none
               |                          allowed: 4g; preferred: none
               |                          allowed: 3g, 4g; preferred: 4g
               |                          allowed: 3g, 4g; preferred: 3g
               |                          allowed: 5g; preferred: none
               |                          allowed: 3g, 5g; preferred: 5g
               |                          allowed: 3g, 5g; preferred: 3g
               |                          allowed: 4g, 5g; preferred: 5g
               |                          allowed: 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 5g
               |                          allowed: 3g, 4g, 5g; preferred: 4g
               |                          allowed: 3g, 4g, 5g; preferred: 3g
               |                 current: allowed: 3g, 4g, 5g; preferred: 5g
      -----------------------------------
      Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
               |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
               |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
               |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
               |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
               |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
               |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
               |                          eutran-71, utran-19
      -----------------------------------
      IP       |               supported: ipv4, ipv6, ipv4v6
      -----------------------------------
      3GPP     |                    imei: xxxxxxxxxxxxxxx
               |           enabled locks: fixed-dialing
               |             operator id: 311480
               |           operator name: Verizon
               |            registration: home
               |                     pco: 0: (partial) '27058000FF0100'

      -----------------------------------
      3GPP EPS |    ue mode of operation: csps-1
               |     initial bearer path: /org/freedesktop/ModemManager1/Bearer/0
               |      initial bearer apn: ims
               |  initial bearer ip type: ipv4v6
      -----------------------------------
      SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
      -----------------------------------
      Bearer   |                   paths: /org/freedesktop/ModemManager1/Bearer/1

      ```

1. Rufen Sie die Bearerdetails ab.

      Der Bearer, der sich aus dem obigen Schritt `--simple-connect` ergibt, befindet sich im Pfad `/org/freedesktop/ModemManager1/Bearer/1`.

      Dies ist der Bearer, den wir nach Modeminformationen zur aktiven Verbindung abfragen. Der anfängliche Bearer ist nicht an eine aktive Verbindung angefügt und enthält daher keine IP-Informationen.

      ```
      mmcli --bearer 1
      ```

      ```
      --------------------------------
      General            |       path: /org/freedesktop/ModemManager1/Bearer/1
                           |       type: default
      --------------------------------
      Status             |  connected: yes
                           |  suspended: no
                           |  interface: wwan0
                           | ip timeout: 20
      --------------------------------
      Properties         |        apn: fast.t-mobile.com
                           |    roaming: allowed
      --------------------------------
      IPv4 configuration |     method: static
                           |    address: 25.21.113.165
                           |     prefix: 30
                           |    gateway: 25.21.113.166
                           |        dns: 10.177.0.34, 10.177.0.210
                           |        mtu: 1500
      --------------------------------
      Statistics         |   attempts: 1
      ```

      Hier finden Sie Beschreibungen einiger wichtiger Details:
      - `Status -> interface: wwan0`: Listet auf, welche Linux-Netzwerkschnittstelle diesem Modem entspricht
      - `IPv4 configuration`: Stellt die IP-Konfiguration für die oben genannte Schnittstelle bereit, die festgelegt werden soll, damit sie verwendet werden kann

1. Überprüfen Sie den Status der Modemnetzwerkschnittstelle.

      Standardmäßig zeigt die Netzwerkschnittstelle `DOWN` an.

      ```
      ip link show dev wwan0
      ```

      Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
      ```

1. Rufen Sie die Schnittstelle auf.

      ```
      sudo ip link set dev wwan0 up
      ```

1. Überprüfen Sie die IP-Informationen.

      Standardmäßig zeigt die Schnittstelle `UP,LOWER_UP` ohne IP-Informationen an.

      ```
      sudo ip address show dev wwan0
      ```

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. Senden Sie eine DHCP-Anforderung.

      Dieses Feature ist spezifisch für das Quectel-Modul, aber nicht darauf beschränkt. In der Regel müssen die IP-Informationen manuell auf die Schnittstelle oder über einen Netzwerk-Manager-Daemon festgelegt werden, der ModemManager unterstützt (z. B. NetworkManager). Hier können wir jedoch einfach dhclient auf dem Quectel-Modem verwenden:

      ```
      sudo dhclient wwan0
      ```

1. Überprüfen Sie die IP-Informationen.

      Die IP-Konfiguration für diese Schnittstelle sollte mit den Details des ModemManager-Bearers übereinstimmen.

      ```
      sudo ip address show dev wwan0
      ```

      Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
            valid_lft forever preferred_lft forever
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. Überprüfen Sie die Schnittstellenrouten.

      Beachten Sie, dass der DHCP-Client auch eine Standardroute für Pakete festgelegt hat, die die Schnittstelle `wwan0` durchgehen.

      ```
      ip route show dev wwan0
      ```

      Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

      ```
      default via 25.21.113.166 
      25.21.113.164/30 proto kernel scope link src 25.21.113.165
      ```

      Sie haben nun mithilfe des Quectel-Modems eine Verbindung mit Azure Percept DK hergestellt.


1. Testen Sie die Verbindung.

      Führen Sie eine `ping`-Anforderung über die `wwan0`-Schnittstelle aus.

      ```
      ping -I wwan0 8.8.8.8
      ```

      Sie sollten ein Ergebnis erhalten, das dem folgenden ähnelt:

      ```
      PING 8.8.8.8 (8.8.8.8) from 25.21.113.165 wwan0: 56(84) bytes of data.
      64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=137 ms
      64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=114 ms
      ^C
      --- 8.8.8.8 ping statistics ---
      2 packets transmitted, 2 received, 0% packet loss, time 2ms
      rtt min/avg/max/mdev = 113.899/125.530/137.162/11.636 ms
      ```

## <a name="debugging"></a>Debuggen

Allgemeine Informationen zum Debuggen finden Sie unter [Verbinden mit einem USB-Modem](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Nächste Schritte

Abhängig vom Mobilfunkgerät, auf das Sie Zugriff haben, können Sie auf zwei Arten eine Verbindung herstellen:

* [Herstellen einer Verbindung mithilfe eines USB-Modems](./connect-over-cellular-usb.md)
* [Herstellen einer Verbindung mit 5G oder LTE](./connect-over-cellular.md)
