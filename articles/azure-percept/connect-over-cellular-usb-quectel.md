---
title: Verbinden von Azure Percept über 5G oder LTE mithilfe eines Quectel RM500-USB-Modems
description: Dieser Artikel beschreibt, wie Sie ein Azure Percept DK per Quectel-USB-Modem über 5G- oder LTE-Netzwerke verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 52914140f80bad9c8bd8bb57bec0938211c25e71
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007254"
---
# <a name="connect-azure-percept-over-5g-or-lte-with-usb-modem-quectel-5g-rm500-gl"></a>Verbinden von Azure Percept über 5G oder LTE mithilfe eines Quectel 5G-RM500-GL-USB-Modems 

Hier sind die Schritte zum Verbinden Ihrer Azure Percept mithilfe des Quectel RM500-GL 5G-Modems beschrieben. Weitere Informationen zu diesem 5G-Modem Developer Kit erhalten Sie vom lokalen Quectel-Vertriebsteam:

northamerica-sales@quectel.com – für Na-Kunden

sales@quectel.com – für globale Kunden

> [!Note] 
> ca. 5G-Modems und USB-Kabel. Dies gilt nur für 5G-Modems. 5G-Modems erfordern mehr Leistung als MODEM-Modems. Außerdem kann das USB-Kabel zu einem Engpass für bestmögliche 5G-Datenraten werden. Um eine ausreichende und konsistente Stromversorgung für ein 5G-Modem zu gewährleisten, sollten Sie sicherstellen, dass das USB-Kabel nicht zu lang ist und mindestens 3 A Strom aushalten kann. Um den maximalen Durchsatz zu erzielen, sollten Sie USB 3.1 Gen2-Kabel verwenden und sicherstellen, dass ein USB-IF-Logo mit der Zertifizierung angezeigt wird. Zur Zusammenfassung: **Für Power:**
> - Die maximale Stromstärke sollte gleich oder größer als 3 Ampere sein
> - Weniger als 1 m lang
> - Bei Verwendung von 5G-Modems sollte nur ein USB A-Anschluss Azure Percept DK aktiv sein.
>  
> **Zum Durchsatz:**
> - USB 3.1 Gen2
> - USB-IF-zertifiziert

## <a name="preparation"></a>Vorbereitung
Stellen Sie sicher, dass Sie die unter [Verbinden über USB-Modem](./connect-over-cellular-usb.md) beschriebenen Azure Percept-Vorbereitungen durchgeführt und sich die Kommentare zu USB-Kabeln, die verwendet werden sollen, notiert haben. 

### <a name="preparation-of-the-modem"></a>Vorbereitung des Modems
Für den Einstieg muss sich das Modem im MBIM-Modus befinden. Für diesen Befehl kann ein nicht dokumentierter, aber standardmäßiger Quectel AT-Befehl verwendet werden: `AT+QCFG="usbnet"`.

Die Eigenschaft `usbnet` kann auf vier verschiedene Werte festgelegt werden, von `0` bis `3`:
- `0` für den **NDIS-/PPP-/QMI-Modus** (vom Treiber `qmi_wwan` unterstützt, mit `CONFIG_USB_NET_QMI_WWAN=y|m` aktiviert)
- `1` für den **CDC-Ethernet-Modus** (wird unter Linux unterstützt, wenn `CONFIG_USB_NET_CDCETHER=y|m`)
- `2` für den **MBIM-Modus** (wird unter Linux unterstützt, wenn `CONFIG_USB_NET_CDC_MBIM=y|m`)
- `3` für den **RNDIS-Modus**

Die einfachste Möglichkeit zum Konfigurieren des Modus besteht in der Verbindung des Quectel 5G-Modems mit einem PC und der Verwendung von Terminal-SW wie TeraTerm oder der eigenen PC-Software von Quectel wie QCOM. Mit Windows Geräte-Manager können Sie sehen, welcher USB-Anschluss für AT-Befehle zugewiesen ist, und diesen verwenden. Die COM-Porteinstellungen sollten wie im folgenden Beispiel sein: Baudrate: 115200 StopBits: 1 Parity: None ByteSize: 8 Flow Control: No STRG flow

Und hier sind die AT-Befehle: So überprüfen Sie, welches Quectel-Gerät im USB-Modus ist:
```
AT+QCFG="usbnet"
```
Ändern Sie in Modus 2:
```
AT+QCFG="usbnet",2
```
Wenn Sie die Überprüfung erneut mithilfe des Befehls „First at Command“ ausführen, sollten Sie „+QCFG: „usbnet“,2“ erhalten.

Nachdem Sie den richtigen USB-Modus festgelegt haben, sollten Sie die Hardwarezurücksetzung mit folgenden Maßnahmen ausführen:
```
AT+CFUN=1,1
```
An diesem Punkt sollte das Modem getrennt und später wieder mit dem USB-Anschluss verbunden werden.


## <a name="using-the-modem-to-connect"></a>Verwenden des Modems zum Herstellen der Verbindung

**1. Setzen Sie eine SIM-Karte in das Quectel-Modem.**

**2. Schließen Sie das Quectel-Modem an Azure Percept USB-Anschluss an. Denken Sie daran, ein ordnungsgemäßes USB-Kabel zu verwenden!**

**3. Einschalten von Azure Percept**

**4. Sicherstellen, dass ModemManager ausgeführt wird**

```
systemctl status ModemManager
```
Das Ergebnis sollte in etwa wie folgt aussehen:
```
* ModemManager.service - Modem Manager
   Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
   Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
[...]
```
Falls nicht, sollten Sie sicherstellen, dass Sie das richtige Bild in Ihrem Azure Percept (5G aktiviert) angezeigt haben.

**5. Auflisten aktiver Modems**

Wenn Sie die Modems auflisten, sollten Sie sehen, dass das Quectel-Modem erkannt wurde und jetzt von ModemManager verarbeitet wird.
```
mmcli --list-modems
```
Nun erhalten Sie eine Rückgabe wie diese:
```
 /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
```
Die Modem-ID ist hier `0` und wird in den folgenden Befehlen verwendet (d. h.: `--modem 0`.)

**6. Abrufen von Modemdetails**

Standardmäßig ist das Modem deaktiviert (`Status -> state: disabled`), siehe Status:
```
mmcli --modem 0
```
Das Ergebnis sollte jetzt in etwa wie folgt aussehen:
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

**7. Aktivieren des Modems**

Bevor eine Verbindung hergestellt wird, müssen die Funkgeräte des Modems aktiviert werden.
```
mmcli --modem 0 --enable
```
Sie sollten nun Folgendes erhalten:
```
successfully enabled the modem
```
Nach einiger Zeit sollte sich das Modem bei einem Funkmast registrieren, und sie sollten im Modemstatus Folgendes sehen: `Status -> state: registered` Sie können dies folgendermaßen erneut überprüfen:
```
mmcli --modem 0
```

**8. Herstellen der Verbindung mithilfe der APN-Informationen**

In der Regel stellen die Modems den zu verwendenden APN (siehe Informationen zu `3GPP EPS -> initial bearer APN`) zur Verfügung, damit Sie ihn zum Herstellen einer Verbindung verwenden können. Wenden Sie sich andernfalls an Ihren Mobilfunkanbieter, damit der APN verwendet werden kann. Hier sehen Sie den ModemManager-Befehl zum Herstellen einer Verbindung, z. B. mithilfe der Verizon-APN `APN=vzwinternet`
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"
```
Auch hier sollten Sie dies erhalten:
```
successfully connected the modem
```

**9. Abrufen des Modemstatus**

Sie sollten nun `Status -> state: connected` und eine neue Kategorie `Bearer` am Ende der Statusmeldung sehen.
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

**10. Abrufen der Bearerdetails**

Der Bearer, der sich aus den obigen `--simple-connect` ergibt, befindet sich hier unter dem folgenden Pfad: `/org/freedesktop/ModemManager1/Bearer/1`.
Dies ist der, für den wir die Modeminformationen zur aktiven Verbindung abfragen. Der anfängliche Bearer ist nicht an eine aktive Verbindung angefügt und enthält daher keine IP-Informationen.
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
Einige Details zu:
- `Status -> interface: wwan0`: Listet auf, welche Linux-Netzwerkschnittstelle diesem Modem entspricht.
- `IPv4 configuration`: Stellt die IP-Konfiguration für die oben genannte Schnittstelle bereit, die festgelegt werden soll, damit sie verwendet werden kann.

**11. Überprüfen des Status der Modem-Netzwerkschnittstelle**

Die Netzwerkschnittstelle sollte standardmäßig `DOWN` sein.
```
ip link show dev wwan0
```
Ergebnis:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
```

**12. Einrichten der Schnittstelle**

```
sudo ip link set dev wwan0 up
```

**13. Überprüfen der IP-Informationen**

Die Schnittstelle sollte standardmäßig als `UP,LOWER_UP` ohne IP-Informationen angezeigt werden.
```
sudo ip address show dev wwan0
```
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**14. Senden einer DHCP-Anforderung**

Dies ist ein featurespezifisches, aber nicht beschränktes Quectel-Modul. In der Regel müssen die IP-Informationen manuell auf die Schnittstelle oder über einen Netzwerk-Manager-Daemon festgelegt werden, der ModemManager unterstützt (z. B. NetworkManager). Hier können wir jedoch einfach dhclient auf dem Quectel-Modem verwenden:
```
sudo dhclient wwan0
```

**15. Überprüfen der IP-Informationen**

Die IP-Konfiguration für diese Schnittstelle sollte mit den Details des ModemManager-Bearers übereinstimmen.
```
sudo ip address show dev wwan0
```
Und Ergebnisse:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**16. Überprüfen von Schnittstellenrouten**

Beachten Sie, dass der DHCP-Client auch eine Standardroute für Pakete festgelegt hat, die die Schnittstelle `wwan0` durchgehen.
```
ip route show dev wwan0
```
Beispiel:
```
default via 25.21.113.166 
25.21.113.164/30 proto kernel scope link src 25.21.113.165
```
Nun haben Sie also mithilfe des Quectel-Modems eine Verbindung mit Azure hergestellt.


**17. Test-Connectivity**

Wir führen eine `ping`-Anforderung über die `wwan0`-Schnittstelle aus.
```
ping -I wwan0 8.8.8.8
```
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
Siehe [Verbinden über USB-Modem](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Nächste Schritte
[Verbinden über USB-Modem](./connect-over-cellular-usb.md).

Zurück zum Hauptartikel über 5G oder LTE:

[Verbinden über 5G oder LTE](./connect-over-cellular.md).  