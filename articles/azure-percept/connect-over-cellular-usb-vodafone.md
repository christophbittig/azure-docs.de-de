---
title: Verbinden von Azure Percept über 5G oder LTE mithilfe eines Vodafone-USB-Modems
description: In diesem Artikel wird erläutert, wie Sie das Azure Percept DK über 5G- oder LTE-Netzwerke mithilfe eines Vodafone-USB-Modems verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 6264bd855cdc4ff186f51748bf426573e6316e5c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007251"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-vodafone-usb-connect-4g"></a>Verbinden von Azure Percept über LTE mithilfe eines USB Connect 4G-USB-Modems von Vodafone
Hier finden Sie Schritte zum Verbinden von Azure Percept mithilfe von Vodafone USB Connect 4G v2.
Weitere Informationen zu diesem bestimmten Modem HW erhalten Sie von Vodafone auf der folgenden Seite: https://www.vodafone.com/business/iot/iot-devices/integrated-terminals

## <a name="using-the-modem-to-connect"></a>Verwenden des Modems zum Herstellen der Verbindung
Stellen Sie sicher, dass Sie die Azure Percept DK-Vorbereitungen ab [Herstellen einer Verbindung mithilfe eines USB-Modems](./connect-over-cellular-usb.md) abgeschlossen haben. Es ist keine Vorbereitung für das USB-Modem selbst erforderlich.   

**1. Einstecken einer SIM-Karte in das Vodafone-Modem**

**2. Anschließen des Vodafone-Modems am Azure Percept-USB-A-Anschluss**

**3. Einschalten von Azure Percept**

**4. Herstellen einer SSH-Verbindung mit dem Azure Percept DK**

**5. Sicherstellen, dass ModemManager ausgeführt wird**

Schreiben Sie den folgenden Befehl in Ihre SSH-Eingabeaufforderung:
```
systemctl status ModemManager
```
Wenn alles in Ordnung ist, erhalten Sie etwa Folgendes:

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* 
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. Auflisten aktiver Modems**

Überprüfen Sie anschließend, ob ModemManager Ihr Modem erkennen kann.
```
mmcli --list-modems
```
Sie erhalten etwa Folgendes, wobei die Modem-ID `0` ist, es könnte jedoch auch etwas anderes sein:

 */org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband*

**7. Abrufen von Modemdetails**

Um die Details zum Modemstatus abzurufen, können Sie den folgenden Befehl und die Modem-ID `0` verwenden.
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
Es wird empfohlen, mit der Standardeinstellung zu beginnen: `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`, was festgelegt werden kann, wenn nicht bereits `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'` verwendet wird.

**8. Aktivieren des Modems**

Bevor eine Verbindung hergestellt wird, müssen die Funkgeräte des Modems aktiviert werden.
```
mmcli --modem 0 --enable
```
Sie sollten eine Antwort wie die folgende erhalten: *Modem erfolgreich aktiviert*.

Nach einiger Zeit sollte sich das Modem bei einem Funkmast registrieren, und sie sollten im Modemstatus Folgendes sehen: `Status -> state: registered` bei erneuter Ausführung von:
```
mmcli --modem 0
```

**9. Herstellen der Verbindung mithilfe der APN-Informationen**

Der Zugriffspunktname (Access Point Name=APN) wird von Ihrem Mobilfunkanbieter bereitgestellt, wie hier für Vodafone:
```
mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
```
Wenn alles in Ordnung ist, erhalten Sie *Modem erfolgreich verbunden*.

**10. Abrufen des Modemstatus**

Sie sollten nun `Status -> state: connected` und eine neue Kategorie `Bearer` am Ende der Statusmeldung sehen.
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

**11. Abrufen der Bearerdetails**

Bearerdetails sind erforderlich, um das Betriebssystem mit der Paketdatenverbindung zu verbinden, die das Modem jetzt mit dem Mobilfunknetz hergestellt hat. An diesem Punkt verfügt das Modem also über eine IP-Verbindung, das Betriebssystem ist jedoch noch nicht für ihre Verwendung konfiguriert.

```
mmcli --bearer 0
```
Aufgeführte Bearerdetails:
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

**12. Aufrufen der Netzwerkschnittstelle**

```
sudo ip link set dev wwan0 up
```

**13. Konfigurieren der Netzwerkschnittstelle**

Ersetzen Sie die IP-Adresse (hier 162.177.2.0/22) durch die IP-Adresse Ihres Bearers, indem Sie die Informationen aus den Bearerdetails verwenden:
```
sudo ip address add 162.177.2.0/22 dev wwan0
```

**14. Überprüfen der IP-Informationen**

Die IP-Konfiguration für diese Schnittstelle sollte mit den Details des ModemManager-Bearers übereinstimmen.
```
sudo ip address show dev wwan0
```
Sie sehen, dass Ihre Bearer-IP unten aufgeführt ist:
```
wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
    inet 162.177.2.0/22 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::c012:44ff:fec4:273c/64 scope link 
       valid_lft forever preferred_lft forever
```

**15. Festlegen der Standardroute**

Verwenden Sie erneut die vom Bearer bereitgestellten Informationen, und verwenden Sie das Gateway des Modems (ersetzen Sie 162.177.2.1) als Standardziel für Netzwerkpakete:
```
sudo ip route add default via 162.177.2.1 dev wwan0
```
Nun sollten Sie Azure Percept aktiviert haben, um über das LTE-Modem eine Verbindung zu Azure herzustellen.


**16. Testen der Konnektivität**

Wir führen eine `ping`-Anforderung über die `wwan0`-Schnittstelle aus. Sie können jedoch auch Azure Percept Studio verwenden und überprüfen, ob Telemetrienachrichten empfangen werden (vergewissern Sie sich, dass Sie weder ein Ethernetkabel verwenden noch Wi-Fi aktiviert haben, damit Sie sicher sein können, dass Sie LTE verwenden).
```
ping -I wwan0 8.8.8.8
```
Sie sollten Folgendes erhalten:
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
Allgemeine Informationen finden Sie unter [Verbinden über USB-Modem](./connect-over-cellular-usb.md).
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>Vodafone-Modemregeln zur Minimierung von Enumerationsproblemen

Um zu verhindern, dass das Modem in einem nicht unterstützten Modus enumeriert, wird empfohlen, die folgenden UDEV-Regeln zu verwenden, damit ModemManager unerwünschte Schnittstellen ignoriert.

Zu erstellende Datei: `/usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules` mit folgendem Inhalt:
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
Laden Sie nach der Installation die UDEV-Regeln erneut, und starten Sie ModemManager neu:
```
udevadm control -R
systemctl restart ModemManager
```
## <a name="next-steps"></a>Nächste Schritte
[Verbinden über USB-Modem](./connect-over-cellular-usb.md)

Zurück zum Hauptartikel über 5G oder LTE:

[Verbinden über 5G oder LTE](./connect-over-cellular.md)
