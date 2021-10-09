---
title: Zeitsynchronisierung für Linux-VMs in Azure
description: Zeitsynchronisierung für virtuelle Linux-Computer.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/03/2021
ms.author: cynthn
ms.openlocfilehash: 324373fc56ae1a57adfb522ca77f06f2c080074c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776517"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Zeitsynchronisierung für Linux-VMs in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen:heavy_check_mark: Einheitliche Skalierungsgruppen 

Die Zeitsynchronisierung ist für die Sicherheit und die Ereigniskorrelation wichtig. Manchmal wird sie für die Implementierung von verteilten Transaktionen verwendet. Die Genauigkeit der Uhrzeit zwischen mehreren Computersystemen wird durch die Synchronisierung erreicht. Die Synchronisierung kann durch mehrere Faktoren beeinträchtigt werden, einschließlich Neustarts und Netzwerkdatenverkehr zwischen der Zeitquelle und dem Computer, der die Uhrzeit abruft. 

Azure wird von einer Infrastruktur unter Windows Server 2016 unterstützt. Windows Server 2016 verfügt über verbesserte Algorithmen zur Korrektur der Uhrzeit und zur Konditionierung der lokalen Uhr zum Synchronisieren mit UTC.  Das Windows Server 2016-Feature für die genaue Uhrzeit hat den VMICTimeSync-Dienst erheblich verbessert, der steuert, wie VMs in Bezug auf die genaue Uhrzeit mit dem Host synchronisiert werden. Die Verbesserungen umfassen eine genauere Anfangszeit beim VM-Start oder bei der VM-Wiederherstellung sowie eine Unterbrechungskorrektur der Latenzzeit. 

> [!NOTE]
> Eine kurze Übersicht über den Windows-Zeitdienst bietet dieses [allgemeine Übersichtsvideo](https://aka.ms/WS2016TimeVideo).
>
> Weitere Informationen finden Sie unter [Genaue Uhrzeit für Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Übersicht

Die Genauigkeit einer Computeruhr wird daran gemessen, wie nahe die Computeruhr dem Zeitstandard Koordinierte Weltzeit (Coordinated Universal Time, UTC) ist. UTC wird durch ein multinationales Muster von präzisen Atomuhren definiert, die in 300 Jahren nur um eine Sekunde abweichen. Um UTC jedoch direkt lesen zu können, ist spezielle Hardware erforderlich. Stattdessen werden Zeitserver mit UTC synchronisiert, und der Zugriff darauf erfolgt über andere Computer, um Skalierbarkeit und Stabilität zu ermöglichen. Auf jedem Computer wird ein Zeitsynchronisierungsdienst ausgeführt, der weiß, welche Zeitserver zu verwenden sind, und regelmäßig überprüft, ob die Computeruhr korrigiert werden muss, und die Uhrzeit bei Bedarf anpasst. 

Azure-Hosts werden mit internen Microsoft-Zeitservern synchronisiert, die die Uhrzeit von Microsoft-eigenen Stratum 1-Geräten mit GPS-Antennen abfragen. Virtuelle Computer in Azure können zum Übergeben der genauen Uhrzeit an den virtuellen Computer von ihrem Host abhängen (*Hostzeit*), oder der virtuelle Computer kann die Uhrzeit direkt von einem Zeitserver abrufen, oder eine Kombination aus beidem. 

Auf eigenständiger Hardware liest das Linux-Betriebssystem nur die Hosthardwareuhr beim Start. Danach wird die Uhr mithilfe des Interrupt-Zeitgebers im Linux-Kernel verwaltet. In dieser Konfiguration kommt es bei der Uhr im Laufe der Zeit zu Abweichungen. Bei neueren Linux-Distributionen in Azure können VMs den in den Linux-Integrationsdiensten (Linux Integration Services, LIS) enthaltenen VMICTimeSync-Anbieter verwenden, um häufiger Uhrenupdates vom Host abzufragen.

VM-Interaktionen mit dem Host können sich auch auf die Uhr auswirken. Während der [Wartung mit Speicherbeibehaltung](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) werden VMs bis zu 30 Sekunden angehalten. Beispiel: Vor Beginn der Wartung zeigt die VM-Uhr 10:00:00 Uhr an und wird 28 Sekunden angehalten. Nach Wiederaufnahme würde die Uhr des virtuellen Computers auch noch 10:00:00 Uhr anzeigen, was einen Zeitverlust von 28 Sekunden bedeuten würde. Um dies zu korrigieren, überwacht der VMICTimeSync-Dienst die Vorgänge auf dem Host und aktualisiert die Uhrzeit zur Kompensierung auf Linux-VMs.

Ohne Zeitsynchronisierung würden sich auf dem virtuellen Computer Zeitfehler ansammeln. Wenn nur ein virtueller Computer vorhanden ist, sind die Auswirkungen möglicherweise nicht unbedingt erheblich, sofern die Workload keine genaue Zeitmessung erfordert. Aber in den meisten Fällen verfügen wir über mehrere, miteinander verbundene VMs, die Transaktionen anhand der Uhrzeit nachverfolgen, und die Uhrzeit muss in der gesamten Bereitstellung einheitlich sein. Bei unterschiedlichen Uhrzeiten der VMs kann es zu den folgenden Auswirkungen kommen:

- Die Authentifizierung schlägt fehl. Sicherheitsprotokolle wie Kerberos oder von Zertifikaten abhängige Technologien erfordern eine einheitliche Uhrzeit auf allen Systemen.
- Wenn Protokolle (oder andere Daten) zeitlich nicht genau übereinstimmen, lassen sich die Vorgänge in einem System nur sehr schwer nachvollziehen. Das gleiche Ereignis würde so aussehen, als wäre es zu verschiedenen Zeiten aufgetreten, was die Korrelation erschwert.
- Wenn die Uhr ausgeschaltet ist, könnte die Abrechnung falsch erfolgen.


## <a name="configuration-options"></a>Konfigurationsoptionen

Für die Zeitsynchronisierung muss ein Zeitsynchronisierungsdienst auf der Linux-VM ausgeführt werden. Außerdem wird eine Quelle für genaue Zeitinformationen benötigt, mit der die Synchronisierung erfolgen soll.
In der Regel wird ntpd oder chronyd als Zeitsynchronisierungsdienst verwendet. Es können aber auch andere Open-Source-Zeitsynchronisierungsdienste verwendet werden.
Die Quelle präziser Zeitinformationen kann der Azure-Host oder ein externer Zeitdienst sein, auf den über das öffentliche Internet zugegriffen wird.
Der VMICTimeSync-Dienst bietet selbst keine fortlaufende Zeitsynchronisierung zwischen dem Azure-Host und einer Linux-VM, außer nach Pausen für die Hostwartung, wie oben beschrieben. 

In der Vergangenheit wurden Azure Marketplace-Images mit Linux auf zwei Arten konfiguriert:
- Standardmäßig wird kein Zeitsynchronisierungsdienst ausgeführt
- ntpd wird als Zeitsynchronisierungsdienst ausgeführt, und die Synchronisierung erfolgt mit einer externen NTP-Zeitquelle, auf die über das Netzwerk zugegriffen wird. Marketplace-Images mit Ubuntu 18.04 LTS verwenden z. B. **ntp.ubuntu.com**.

Führen Sie den Befehl `ntpq -p` aus, um zu bestätigen, dass ntpd ordnungsgemäß synchronisiert wird.

Ab Anfang 2021 werden die aktuellsten Azure Marketplace-Images mit Linux so geändert, dass sie chronyd als Zeitsynchronisierungsdienst verwenden, wobei chronyd für die Synchronisierung mit dem Azure-Host und nicht mit einer externen NTP-Zeitquelle konfiguriert ist. Die Azure-Hostzeit ist in der Regel die beste Zeitquelle für die Synchronisierung, da sie sehr genau und zuverlässig verwaltet wird und ohne die variablen Netzwerkverzögerungen zugänglich ist, die der Zugriff auf eine externe NTP-Zeitquelle über das öffentliche Internet mit sich bringt.

VMICTimeSync wird parallel verwendet und stellt zwei Funktionen zur Verfügung:
- Sofortiges Aktualisieren der Uhrzeit der Linux-VM nach einem Hostwartungsereignis
- Instanziieren einer PTP-Hardwarezeitquelle (Precision Time Protocol) gemäß IEEE 1588 als „/dev/ptp“-Gerät, das die genaue Uhrzeit vom Azure-Host aus bereitstellt.  chronyd kann für die Synchronisierung mit dieser Zeitquelle konfiguriert werden (dies ist die Standardkonfiguration der neuesten Linux-Images). Linux-Distributionen mit Kernelversion ab 4.11 (oder mindestens Version 3.10.0-693 für RHEL/CentOS 7) unterstützen das „/dev/ptp“-Gerät.  Bei früheren Kernelversionen, die „/dev/ptp“ für die Azure-Hostzeit nicht unterstützen, ist nur eine Synchronisierung mit einer externen Zeitquelle möglich.

Die Standardkonfiguration kann natürlich geändert werden. Ein älteres Image, das für die Verwendung von ntpd und einer externen Zeitquelle konfiguriert ist, kann so geändert werden, dass chronyd und das „/dev/ptp“-Gerät für die Azure-Hostzeit verwendet werden.  Auf ähnliche Weise kann ein Image, das die Azure-Hostzeit über ein „/dev/ptp“-Gerät verwendet, so konfiguriert werden, dass es eine externe NTP-Zeitquelle verwendet, wenn dies für Ihre Anwendung oder Workload erforderlich ist.


## <a name="tools-and-resources"></a>Tools und Ressourcen

Es gibt einige grundlegende Befehle für die Überprüfung der Konfiguration der Zeitsynchronisierung. In der Dokumentation für die jeweilige Linux-Distribution finden Sie weitere Informationen zur besten Möglichkeit zum Konfigurieren der Zeitsynchronisierung für diese Distribution.

### <a name="integration-services"></a>Integrationsdienste

Überprüfen Sie, ob der Integrationsdienst (hv_utils) geladen wird.

```bash
lsmod | grep hv_utils
```
Die Anzeige sollte in etwa der folgenden Ausgabe entsprechen:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

### <a name="check-for-ptp-clock-source"></a>Überprüfen auf PTP-Zeitquelle

In neueren Versionen von Linux ist eine PTP-Zeitquelle (Precision Time Protocol) als Entsprechung zum Azure-Host als Teil des VMICTimeSync-Anbieters verfügbar.
In älteren Versionen von Red Hat Enterprise Linux oder CentOS 7.x können [Linux Integration Services](https://github.com/LIS/lis-next) heruntergeladen und zum Installieren des aktualisierten Treibers verwendet werden. Ist die PTP-Zeitquelle verfügbar, weist das Linux-Gerät die Form „/dev/ptp *x*“ auf. 

Ermitteln Sie, welche PTP-Zeitquellen (Uhren) verfügbar sind.

```bash
ls /sys/class/ptp
```

In diesem Beispiel lautet der zurückgegebene Wert *ptp0*, sodass wir diesen verwenden, um den Uhrnamen zu überprüfen. Überprüfen Sie zur Überprüfung des Geräts den Uhrnamen.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Dies sollte `hyperv` zurückgeben, d. h. den Azure-Host.

Auf Linux-VMs mit aktiviertem beschleunigtem Netzwerkbetrieb werden möglicherweise mehrere PTP-Geräte aufgeführt, da der Mellanox mlx5-Treiber auch ein „/dev/ptp“-Gerät erstellt.
Da die Initialisierungsreihenfolge bei jedem Start von Linux unterschiedlich sein kann, ist das PTP-Gerät, das dem Azure-Host entspricht, entweder „/dev/ptp0“ oder „/dev/ptp1“. Dies macht die Konfiguration von chronyd mit der richtigen Zeitquelle schwierig. Um dieses Problem zu beheben, verfügen die neuesten Linux-Images über eine udev-Regel, die den symbolischen Link „/dev/ptp_hyperv“ für den jeweiligen Eintrag „/dev/ptp“ erstellt, der dem Azure-Host entspricht. chrony sollte so konfiguriert werden, dass dieser symbolische Link anstelle von „/dev/ptp0“ oder „/dev/ptp1“ verwendet wird.

### <a name="chrony"></a>chrony

Unter Ubuntu 19.10 und höher, Red Hat Enterprise Linux und CentOS 8.x wird [chrony](https://chrony.tuxfamily.org/) für die Verwendung einer PTP-Zeitquelle konfiguriert. Anstelle von chrony verwenden ältere Linux-Releases den Network Time Protocol-Daemon (ntpd), der keine PTP-Quellen unterstützt. Um PTP in diesen Releases zu aktivieren, muss chrony manuell installiert und (in „chrony.conf“) mit der folgenden Anweisung konfiguriert werden:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0 stratum 2
```

Wenn der Symlink /dev/ptp_hyperv verfügbar ist, verwenden Sie ihn anstelle von /dev/ptp0, um Verwechslungen mit dem vom Mellanox mlx5-Treiber erstellten Gerät /dev/ptp zu vermeiden.

Die Stratum-Informationen werden nicht automatisch vom Azure-Host an den Linux-Gast übermittelt. Die vorangehende Konfigurationszeile legt fest, dass die Zeitquelle des Azure-Hosts als Stratum 2 behandelt werden soll, was wiederum dazu führt, dass der Linux-Gast sich selbst als Stratum 3 meldet. Sie können die Stratum-Einstellung in der Konfigurationszeile ändern, wenn Sie möchten, dass sich der Linux-Gast anders meldet.

Standardmäßig beschleunigt oder verlangsamt chronyd die Systemuhr, um zeitliche Abweichungen zu beheben. Wenn die Abweichung zu groß wird, kann diese von „chrony“ nicht mehr behoben werden. Dieses Problem kann umgangen werden, indem der `makestep`-Parameter in **/etc/chrony.conf** so geändert wird, dass eine Zeitsynchronisierung erzwungen wird, wenn die Drift den angegebenen Schwellenwert überschreitet.

 ```bash
makestep 1.0 -1
```

Hier erzwingt chrony ein Zeitupdate, wenn die Drift größer als eine Sekunde ist. Starten Sie den chronyd-Dienst neu, um die Änderungen zu übernehmen:

```bash
systemctl restart chronyd
```

Weitere Informationen zu Ubuntu und NTP finden Sie unter [Zeitsynchronisierung](https://ubuntu.com/server/docs/network-ntp).

Weitere Informationen zu Red Hat und NTP finden Sie unter [Konfigurieren von NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Weitere Informationen zu „chrony“ finden Sie unter [Verwenden von Chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

### <a name="systemd"></a>systemd 

Unter SuSE- und Ubuntu-Releases vor 19.10 wird die Zeitsynchronisierung mit [systemd](https://www.freedesktop.org/wiki/Software/systemd/) konfiguriert. Weitere Informationen zu Ubuntu finden Sie unter [Zeitsynchronisierung](https://help.ubuntu.com/lts/serverguide/NTP.html). Weitere Informationen zu SUSE finden Sie im Anschnitt 4.5.8 in den [Versionshinweisen zu SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Genaue Uhrzeit für Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


