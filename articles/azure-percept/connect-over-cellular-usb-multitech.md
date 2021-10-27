---
title: 'Verbinden von Azure Percept über LTE mit USB-Modem: MultiTech Multiconnect'
description: Dieser Artikel beschreibt, wie Sie ein Azure Percept DK per MultiTech-USB-Modem über 5G- oder LTE-Netzwerke verbinden.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 336e67de6d178dcff1b57fe75d57f2272386d2c5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007276"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-multitech-multiconnect"></a>Verbinden von Azure Percept über LTE mit USB-Modem: MultiTech Multiconnect 
Hier werden die Schritte zum Verbinden von Azure Percept mithilfe des USB-Modems MultiTech Multiconnect (MTCM-LNA3-B03) beschrieben. 

> [!Note]
> Es gibt mehrere Modelle, und wir haben LNA3 verwendet, das mindestens mit Verizon- und Vodafone-SIM-Karten funktioniert. Wir konnten keine Verbindung mit einem AT&T-Netzwerk herstellen, aber wir untersuchen dies und ändern es, wenn wir die Grundursache finden. Weitere Informationen zu dieser bestimmten Modem-HW erhalten Sie auf der folgenden Seite: https://www.multitech.com/brands/multiconnect-microcell

## <a name="preparation"></a>Vorbereitung
Stellen Sie sicher, dass Sie die unter [Verbinden über USB-Modem](./connect-over-cellular-usb.md) beschriebenen Azure Percept-Vorbereitungen durchgeführt und sich die Kommentare zu USB-Kabeln, die verwendet werden sollen, notiert haben. 

### <a name="preparation-of-the-modem"></a>Vorbereitung des Modems
Für den Einstieg muss sich das Modem im MBIM-Modus befinden. Informationen hierzu finden Sie in der AT-Befehlsreferenz: https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf

Wir verwenden den AT-Befehl `AT#USBCFG=<mode>`, um den richtigen USB-Modus zum Aktivieren der MBIM-Schnittstelle zu konfigurieren.

In der AT-Befehlsreferenz werden alle möglichen Modi aufgeführt, aber wir sind am Modus `3` interessiert. Der Standardwert ist `0`.

Die einfachste Möglichkeit zum Konfigurieren des Modus ist das Herstellen der Verbindung des MultiTech-Modems mit einem PC und der Verwendung von Terminal-SW wie TeraTerm oder Putty PC. Mit dem Windows Geräte-Manager sehen Sie, welcher USB-Anschluss für das Modem zugewiesen ist. Falls mehrere vorhanden sind, müssen Sie möglicherweise testen, welcher auf AT-Befehle antwortet. Die COM-Porteinstellungen sollten wie im folgenden Beispiel sein: Baudrate: 9600 (oder 115200) StopBits: 1 Parity: None ByteSize: 8 Flow Control: No ctrl flow

Hier sind die AT-Befehle: So überprüfen Sie, in welchem USB-Modus das MultiTech-Gerät derzeit ist:
```
AT#USBCFG?
```
Änderung in Modus 3:
```
AT#USBCFG=3
```
Wenn Sie die Überprüfung erneut mithilfe des ersten AT-Befehls ausführen, sollten Sie `#USBCFG: 3` erhalten.

Nachdem Sie den richtigen USB-Modus festgelegt haben, sollten Sie mit folgenden Maßnahmen eine Rücksetzung ausführen:
```
AT#REBOOT
```
An diesem Punkt sollte das Modem getrennt und später wieder mithilfe des obigen Einstellmodus mit dem USB-Anschluss verbunden werden.

## <a name="using-the-modem-to-connect"></a>Verwenden des Modems zum Herstellen der Verbindung
Stellen Sie sicher, dass Sie die unter [Verbinden über USB-Modem](./connect-over-cellular-usb.md) beschriebenen Azure Percept-Vorbereitungen durchgeführt haben.   

**1. SIM-Karte in das MultiTech-Modem einstecken**

**2. MultiTech-Modem in den Azure Percept-USB-A-Port einstecken**

**3. Azure Percept hochfahren**

**4. SSH-Verbindung mit dem Azure Percept DK herstellen**

**5. Sicherstellen, dass ModemManager ausgeführt wird**

Schreiben Sie den folgenden Befehl in Ihre SSH-Eingabeaufforderung:
```
systemctl status ModemManager
```
Wenn alles in Ordnung ist, erhalten Sie etwa Folgendes:

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* 
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. Aktive Modems auflisten**

In diesem Fall sollte angezeigt werden, dass das FIH7160-Modell von ModemManager erkannt wurde.
```
mmcli --list-modems
```
Sie erhalten etwa Folgendes: */org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160*

**7. Modemdetails abrufen**

Die Modem-ID ist hier `0`, was in Ihrem Fall anders sein kann. Die Modem-ID (`--modem 0`.) wird in den ModemManager-Befehlen wie diesem verwendet.
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

**8. Modem aktivieren**

Bevor eine Verbindung hergestellt wird, müssen die Funkkomponenten des Modems EINgeschaltet werden.
```
mmcli --modem 0 --enable
```
Sie sollten etwa folgende Antwort erhalten: *Modem erfolgreich aktiviert*.

Nach einiger Zeit sollte sich das Modem bei einem Funkmast registrieren und im Modemstatus `Status -> state: registered` angezeigt werden, bei erneuter Ausführung:
```
mmcli --modem 0
```

**9. Verbindung mithilfe der APN-Informationen herstellen**

Der Zugriffspunktname (Access Point Name=APN) wird von Ihrem Mobilfunkanbieter bereitgestellt, wie hier für Vodafone:
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"  
```
Wenn alles in Ordnung ist, erhalten Sie die Meldung *Modem erfolgreich verbunden*.

**10. Modemstatus abrufen**

Nun sollte `Status -> state: connected` und eine neue `Bearer`-Kategorie am Ende der Statusmeldung angezeigt werden.
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

**11. Bearerdetails abrufen**

Bearerdetails sind erforderlich, um eine Verbindung des Betriebssystems mit der Paketdatenverbindung herzustellen, die das Modem jetzt mit dem Mobilfunknetz hergestellt hat. An diesem Punkt verfügt das Modem also über eine IP-Verbindung, das Betriebssystem ist jedoch noch nicht für ihre Verwendung konfiguriert.
```
mmcli --bearer 0
```
Aufgeführte Bearerdetails:
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

**12. Netzwerkschnittstelle-Bring-up durchführen**

```
sudo ip link set dev wwan0 up
```

**13. Netzwerkschnittstelle konfigurieren**

Ersetzen Sie die IP-Adresse (hier 100.112.107.46/24) durch die IP-Adresse Ihres Bearers, indem Sie die Informationen aus den Bearerdetails verwenden:
```
sudo ip address add 100.112.107.46/24 dev wwan0
```

**14. IP-Informationen überprüfen**

Die IP-Konfiguration für diese Schnittstelle sollte mit den Details des ModemManager-Bearers übereinstimmen.
```
sudo ip address show dev wwan0
```
Sie sehen, dass Ihre Bearer-IP unten aufgeführt ist:
```
6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
    inet 100.112.107.46/24 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
       valid_lft forever preferred_lft forever
```

**15. Standardroute festlegen**

Verwenden Sie erneut die vom Bearer bereitgestellten Informationen, und verwenden Sie das Gateway des Modems (ersetzen Sie 100.112.107.1) als Standardziel für Netzwerkpakete:
```
sudo ip route add default via 100.112.107.1 dev wwan0
```
Jetzt verfügt Ihre Azure Percept-Instanz über eine Verbindung, die das USB-Modem verwendet!

**16. Konnektivität testen**

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

## <a name="next-steps"></a>Nächste Schritte
[Verbinden über USB-Modem](./connect-over-cellular-usb.md).

Zurück zum Hauptartikel über 5G oder LTE:

[Verbinden über 5G oder LTE](./connect-over-cellular.md).
   

