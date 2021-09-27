---
title: Hohe Verfügbarkeit von Azure (große Instanzen) für SAP auf RHEL
description: Erfahren Sie, wie Sie ein SAP HANA-Datenbankfailover mit einem Pacemaker-Cluster in Red Hat Enterprise Linux automatisieren.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 7f5f554f6563c2d0275bca7b6db48f2521379b11
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626606"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Hohe Verfügbarkeit von Azure (große Instanzen) für SAP auf RHEL

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Blacklist*, der von Microsoft nicht mehr verwendet wird. Sobald dieser Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

In diesem Artikel erfahren Sie, wie Sie den Pacemaker-Cluster in RHEL 7 konfigurieren, um ein SAP HANA-Datenbankfailover zu automatisieren. Sie müssen mit Linux, SAP HANA und Pacemaker vertraut sein, um die Schritte in dieser Anleitung ausführen zu können.

In der folgenden Tabelle sind die Hostnamen enthalten, die in diesem Artikel verwendet werden. Die Codeblöcke im Artikel zeigen die Befehle, die ausgeführt werden müssen, sowie die Ausgabe dieser Befehle. Achten Sie genau darauf, auf welchen Knoten in jedem Befehl verwiesen wird.

| Typ | Hostname | Node|
|-------|-------------|------|
|Primärer Host|`sollabdsm35`|Knoten 1|
|Sekundärer Host|`sollabdsm36`|Knoten 2|

## <a name="configure-your-pacemaker-cluster"></a>Konfigurieren Ihres Pacemaker-Clusters


Bevor Sie mit der Konfiguration des Clusters beginnen können, richten Sie den SSH-Schlüsselaustausch ein, um eine Vertrauensstellung zwischen Knoten herzustellen.

1. Verwenden Sie die folgenden Befehle, um `/etc/hosts` auf beiden Knoten identisch zu erstellen.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  Erstellen Sie die SSH-Schlüssel, und tauschen Sie sie aus.
    1. Generieren Sie SSH-Schlüssel.

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. Kopieren Sie die Schlüssel für kennwortloses SSH auf die anderen Hosts.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Deaktivieren Sie selinux auf beiden Knoten.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Starten Sie die Server neu, und überprüfen Sie dann mit dem folgenden Befehl den Status von selinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Konfigurieren Sie NTP (Network Time Protocol). Die Zeit und Zeitzonen beider Clusterknoten müssen identisch sein. Verwenden Sie den folgenden Befehl, um `chrony.conf` zu öffnen und den Inhalt der Datei zu überprüfen.
    1. Der Konfigurationsdatei sollte der folgende Inhalt hinzugefügt werden. Ändern Sie die tatsächlichen Werte gemäß Ihrer Umgebung.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Aktivieren Sie den chrony-Dienst. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Aktualisieren des Systems
    1. Installieren Sie zunächst die neuesten Updates auf dem System, bevor Sie mit der Installation des SBD-Geräts beginnen.
    1. Kunden müssen sicherstellen, dass mindestens Version 4.1.1-12.el7_6.26 des Pakets resource-agents-sap-hana installiert ist, wie unter [Supportrichtlinien für RHEL-Hochverfügbarkeitscluster – Verwaltung von SAP HANA in einem Cluster](https://access.redhat.com/articles/3397471) dokumentiert.
    1. Wenn Sie kein umfassendes Update des Systems wünschen, auch wenn es empfohlen wird, aktualisieren Sie mindestens die folgenden Pakete.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. Installieren Sie die SAP HANA- und RHEL-HA-Repositorys.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Installieren Sie die Tools Pacemaker, SBD, OpenIPMI, ipmitool und fencing_sbd auf allen Knoten.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>Konfigurieren von Watchdog

In diesem Abschnitt lernen Sie, Watchdog zu konfigurieren. In diesem Abschnitt werden die gleichen beiden Hosts `sollabdsm35` und `sollabdsm36` verwendet, auf die am Anfang dieses Artikels verwiesen wird.

1. Stellen Sie sicher, dass der Watchdog-Daemon auf keinem System ausgeführt wird.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Der Standard-Linux-Watchdog, der während der Installation installiert wird, ist der itco-Watchdog, der von UCS- und HPE SDFlex-Systemen nicht unterstützt wird. Daher muss dieser Watchdog deaktiviert werden.
    1. Der falsche Watchdog ist auf dem System installiert und geladen:
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Entladen Sie den falschen Treiber aus der Umgebung:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Um sicherzustellen, dass der Treiber beim nächsten Systemstart nicht geladen wird, muss er auf die Sperrliste gesetzt werden. Um die iTCO-Module auf die Sperrliste zu setzen, fügen Sie am Ende der `50-blacklist.conf`-Datei Folgendes hinzu:
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Kopieren Sie die Datei auf den sekundären Host.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Testen Sie, ob der IPMI-Dienst gestartet wurde. Es ist wichtig, dass der IPMI-Timer nicht ausgeführt wird. Die Timerverwaltung erfolgt über den SBD-Pacemaker-Dienst.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Standardmäßig wird das erforderliche Gerät „/dev/watchdog“ nicht erstellt.

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Konfigurieren Sie den IPMI-Watchdog.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Kopieren Sie die Watchdog-Konfigurationsdatei auf den sekundären Host.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Aktivieren Sie den IPMI-Dienst, und starten Sie ihn.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Nun wird der IPMI-Dienst gestartet und das Gerät „/dev/watchdog“ erstellt – der Timer wird jedoch weiterhin angehalten. Später verwaltet SBD den Watchdog-Reset und aktiviert den IPMI-Timer.
7.  Überprüfen Sie, ob „/dev/watchdog“ vorhanden ist, aber nicht verwendet wird.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD-Konfiguration
In diesem Abschnitt lernen Sie, SBD zu konfigurieren. In diesem Abschnitt werden die gleichen beiden Hosts `sollabdsm35` und `sollabdsm36` verwendet, auf die am Anfang dieses Artikels verwiesen wird.

1.  Stellen Sie sicher, dass der iSCSI- oder FC-Datenträger auf beiden Knoten sichtbar ist. In diesem Beispiel wird ein FC-basiertes SBD-Gerät verwendet. Weitere Informationen zum SBD-Fencing finden Sie unter [Entwurfsanleitung für RHEL-Hochverfügbarkeitscluster – Überlegungen zu SBD](https://access.redhat.com/articles/2941601) und unter [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – sbd und fence_sbd](https://access.redhat.com/articles/2800691).
2.  Die LUN-ID muss auf allen Knoten identisch sein.
  
3.  Überprüfen Sie den Multipfadstatus des SBD-Geräts.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Erstellen Sie die SBD-Datenträger, und richten Sie die Umgrenzung des Clustergrundtyps ein. Dieser Schritt muss auf dem ersten Knoten ausgeführt werden.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Kopieren Sie die SBD-Konfiguration nach node2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Überprüfen Sie, ob der SBD-Datenträger auf beiden Knoten sichtbar ist.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Fügen Sie das SBD-Gerät der SBD-Konfigurationsdatei hinzu.

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Clusterinitialisierung
In diesem Abschnitt initialisieren Sie den Cluster. In diesem Abschnitt werden die gleichen beiden Hosts `sollabdsm35` und `sollabdsm36` verwendet, auf die am Anfang dieses Artikels verwiesen wird.

1.  Richten Sie das Benutzerkennwort für den Cluster ein (alle Knoten).
    ```
    passwd hacluster
    ```
2.  Starten Sie PCS auf allen Systemen.
    ```
    systemctl enable pcsd
    ```
  

3.  Stoppen Sie die Firewall, und deaktivieren Sie sie auf allen Knoten.
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Starten Sie den pcsd-Dienst.
    ```
    systemctl start pcsd
    ```

5.  Führen Sie die Clusterauthentifizierung nur über node1 aus.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  Erstellen Sie den Cluster.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Überprüfen Sie den Status des Clusters.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Wenn ein Knoten dem Cluster nicht beitritt, überprüfen Sie, ob die Firewall noch ausgeführt wird.

9. Erstellen und Aktivieren des SBD-Geräts
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. Stoppen Sie den Cluster, und starten Sie die Clusterdienste (auf allen Knoten) neu.

    ```
    pcs cluster stop --all
    ```

11. Starten Sie die Clusterdienste (auf allen Knoten) neu.

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync muss den SBD-Dienst starten.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Starten Sie den Cluster neu (wenn er nicht automatisch von pcsd gestartet wird).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Aktivieren Sie Stonith-Einstellungen.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Überprüfen Sie den neuen Clusterstatus mit jetzt einer Ressource.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Der IPMI-Timer muss nun ausgeführt und das „/dev/watchdog“-Gerät von SBD geöffnet werden.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Überprüfen Sie den SBD-Status.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Testen Sie die SBD-Umgrenzung, indem Sie den Kernel zum Absturz bringen.

    * Lösen Sie den Kernelabsturz aus.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * Der zweite auszuführenden Test besteht darin, einen Knoten mithilfe von PCS-Befehlen zu umgrenzen.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. Für den Rest des SAP HANA-Clusterings können Sie STONITH deaktivieren, indem Sie Folgendes festlegen:

   * pcs property set `stonith-enabled=false`
   * Manchmal ist es einfacher, STONITH während des Setups des Clusters deaktiviert zu lassen, da Sie auf diese Weise unerwartete Neustarts des Systems vermeiden.
   * Dieser Parameter muss für den produktiven Gebrauch auf TRUE festgelegt werden. Wenn dieser Parameter nicht auf TRUE festgelegt ist, wird der Cluster nicht unterstützt.
   * pcs property set `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>HANA-Integration in den Cluster

In diesem Abschnitt integrieren Sie HANA in den Cluster. In diesem Abschnitt werden die gleichen beiden Hosts `sollabdsm35` und `sollabdsm36` verwendet, auf die am Anfang dieses Artikels verwiesen wird.

Die standardmäßige und unterstützte Methode besteht darin, ein leistungsoptimiertes Szenario zu erstellen, das einen direkten Datenbankswitchover ermöglicht. Nur dieses Szenario wird in diesem Dokument beschrieben. In diesem Fall empfiehlt es sich, einen Cluster für das QAS-System und einen separaten Cluster für das PRD-System zu installieren. Nur in diesem Fall ist es möglich, alle Komponenten vor dem Übergang in die Produktion zu testen.


* Dieser Prozess entspricht der RHEL-Beschreibung auf dieser Seite:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Schritte zum Konfigurieren von HSR

 | **Protokollreplikationsmodus**            | **Beschreibung**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Synchron, In-Memory (Standard)** | „Synchron, In-Memory“ (Modus: syncmem) bedeutet, dass der Protokollschreibvorgang als erfolgreich betrachtet wird, wenn der Protokolleintrag auf das Protokollvolume des primären Systems geschrieben wurde und das Senden des Protokolls nach dem Kopieren in den Arbeitsspeicher von der sekundären Instanz bestätigt wurde. Wenn die Verbindung mit dem sekundären System unterbrochen wird, setzt das primäre System die Transaktionsverarbeitung fort und schreibt die Änderungen nur auf den lokalen Datenträger. Datenverluste können auftreten, wenn das primäre und das sekundäre System gleichzeitig ausfallen, während das sekundäre System verbunden ist, oder wenn eine Übernahme ausgeführt wird, während das sekundäre System getrennt ist. Diese Option bietet eine bessere Leistung, weil es nicht erforderlich ist, auf Datenträger-E/A auf der sekundären Instanz zu warten. Sie ist allerdings anfälliger für Datenverlust.                                                                                                                                                                                                                                                                                                                     |
| **Synchron**                     | Synchron (Modus: sync) bedeutet, dass der Protokollschreibvorgang als erfolgreich betrachtet wird, wenn der Protokolleintrag auf das Protokollvolume der primären und der sekundären Instanz geschrieben wurde. Wenn die Verbindung mit dem sekundären System unterbrochen wird, setzt das primäre System die Transaktionsverarbeitung fort und schreibt die Änderungen nur auf den lokalen Datenträger. In diesem Szenario tritt kein Datenverlust auf, solange das sekundäre System verbunden ist. Datenverluste können auftreten, wenn eine Übernahme ausgeführt wird, während das sekundäre System getrennt ist. Darüber hinaus kann dieser Replikationsmodus mit einer Option zur vollständigen Synchronisierung ausgeführt werden. Dies bedeutet, dass der Protokollschreibvorgang erfolgreich ist, wenn der Protokollpuffer in die Protokolldatei der primären und der sekundären Instanz geschrieben wurde. Wenn das sekundäre System getrennt wird (z. B. aufgrund eines Netzwerkfehlers), hält das primäre System außerdem die Transaktionsverarbeitung an, bis die Verbindung mit dem sekundären System wiederhergestellt ist. In diesem Szenario tritt kein Datenverlust auf. Sie können die Option zur vollständigen Synchronisierung mit dem Parameter „\[system\_replication\]/enable\_full\_sync“ nur für die Systemreplikation festlegen. Weitere Informationen zum Aktivieren der Option zur vollständigen Synchronisierung finden Sie unter „Aktivieren der Option zur vollständigen Synchronisierung für die Systemreplikation“.                                                                                                                                                                                                                                                                                                              |
| **Asynchron**                    | Asynchron (Modus: async) bedeutet, dass das primäre System asynchron Wiederholungsprotokollpuffer an das sekundäre System sendet. Das primäre System committet eine Transaktion, wenn sie in die Protokolldatei des primären Systems geschrieben und über das Netzwerk an das sekundäre System gesendet wurde. Es wartet nicht auf die Bestätigung des sekundären Systems. Diese Option bietet eine bessere Leistung, weil nicht auf Protokoll-E/A auf dem sekundären System gewartet werden muss. Die Datenbankkonsistenz ist für alle Dienste auf dem sekundären System garantiert. Diese Option ist jedoch anfälliger für Datenverluste. Datenänderungen können bei der Übernahme verloren gehen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

1.  Dies sind die Aktionen, die auf node1 (primär) ausgeführt werden.
    1. Stellen Sie sicher, dass der Datenbank-Protokollmodus auf „Normal“ festgelegt ist.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. Die SAP HANA-Systemreplikation funktioniert erst, nachdem die erste Sicherung durchgeführt wurde. Der folgende Befehl erstellt eine anfängliche Sicherung im Verzeichnis `/tmp/`. Wählen Sie ein geeignetes Sicherungsdateisystem für die Datenbank aus. 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. Sichern Sie alle Datenbankcontainer dieser Datenbank.
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Aktivieren Sie den HSR-Prozess auf dem Quellsystem.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Überprüfen Sie den Status des primären Systems.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Dies sind die Aktionen, die auf node2 (sekundär) ausgeführt werden.
     1. Stoppen Sie die Datenbank.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Kopieren Sie nur bei SAP HANA 2.0 die SAP HANA-Systemdateien `PKI SSFS_HR2.KEY` und `SSFS_HR2.DAT` vom primären Knoten auf den sekundären Knoten.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Aktivieren Sie den sekundären Knoten als Replikationsstandort.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Starten Sie die Datenbank.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Überprüfen Sie den Status der Datenbank.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Es ist auch möglich, weitere Informationen zum Replikationsstatus zu erhalten:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Beschreibung des Protokollreplikationsmodus

Weitere Informationen zum Protokollreplikationsmodus finden Sie in der [offiziellen SAP-Dokumentation](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>Netzwerkeinrichtung für die HANA-Systemreplikation


Um sicherzustellen, dass der Replikationsdatenverkehr das richtige VLAN für die Replikation verwendet, muss er in der `global.ini` ordnungsgemäß konfiguriert werden. Wenn Sie diesen Schritt überspringen, verwendet HANA das Access-VLAN für die Replikation, was möglicherweise nicht erwünscht ist.


In den folgenden Beispielen wird die Konfiguration der Hostnamensauflösung für die Systemreplikation an einem sekundären Standort veranschaulicht. Drei verschiedene Netzwerke können identifiziert werden:

* Öffentliches Netzwerk mit Adressen im Bereich von 10.0.1.*

* Netzwerk für die interne SAP HANA-Kommunikation zwischen den Hosts an jedem Standort: 192.168.1.*

* Dediziertes Netzwerk für die Systemreplikation: 10.5.1.*

Im ersten Beispiel wurde der `[system_replication_communication]listeninterface`-Parameter auf `.global` festgelegt und nur die Hosts des benachbarten Replikationsstandorts werden angegeben.

Im folgenden Beispiel wurde der `[system_replication_communication]listeninterface`-Parameter auf `.internal` festgelegt, und alle Hosts beider Standorte werden angegeben.

  

Weitere Informationen finden Sie unter [Netzwerkkonfiguration für SAP HANA Systemreplikation](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html).

  

Für die Systemreplikation muss die `/etc/hosts`-Datei nicht bearbeitet werden, interne („virtuelle“) Hostnamen müssen in der `global.ini`-Datei IP-Adressen zugeordnet werden, um ein dediziertes Netzwerk für die Systemreplikation zu erstellen. Die Syntax hierfür sieht wie folgt aus:

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Konfigurieren von SAP HANA in einem Pacemaker-Cluster
In diesem Abschnitt erfahren Sie, wie Sie SAP HANA in einem Pacemaker-Cluster konfigurieren. In diesem Abschnitt werden die gleichen beiden Hosts `sollabdsm35` und `sollabdsm36` verwendet, auf die am Anfang dieses Artikels verwiesen wird.

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:  

* Der Pacemaker-Cluster ist gemäß der Dokumentation konfiguriert und verfügt über eine ordnungsgemäße und funktionierende Umgrenzung.

* Der SAP HANA-Start beim Systemstart ist auf allen Clusterknoten deaktiviert, da Start und Stopp vom Cluster verwaltet werden.

* SAP HANA-Systemreplikation und -Übernahme mithilfe von SAP-Tools funktionieren zwischen Clusterknoten ordnungsgemäß.

* SAP HANA enthält ein Überwachungskonto, das von den Clustern beider Clusterknoten verwendet werden kann.

* Beide Knoten haben die Kanäle „hohe Verfügbarkeit“ und „RHEL for SAP Hana“ (RHEL 6, RHEL 7) abonniert.

  

* Führen Sie bitte alle PCS-Befehle im Allgemeinen nur von einem Knoten aus, da das CIB automatisch von der PCS-Shell aktualisiert wird.

* [Weitere Informationen zur Quorumrichtlinie](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Schritte zum Konfigurieren 
1. Konfigurieren von PCS.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Konfigurieren von corosync.
    Weitere Informationen finden Sie unter [ Wie konfiguriere ich meinen RHEL 7-Hochverfügbarkeitscluster mit Pacemaker und Corosync](https://access.redhat.com/solutions/1293523).
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  Erstellen einer geklonten SAPHanaTopology-Ressource.
    Die SAPHanaTopology-Ressource sammelt den Status und die Konfiguration SAP HANA-Systemreplikation auf jedem Knoten. Für SAPHanaTopology müssen die folgenden Attribute konfiguriert werden.
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 op start timeout=600 \
       op stop timeout=300 \
       op monitor interval=10 timeout=600 \
       clone clone-max=2 clone-node-max=1 interleave=true

       ```

    | Attributname | Beschreibung  |
    |---|---|
    | SID | SAP-Systembezeichner (SID) der SAP HANA-Installation. Muss für alle Knoten identisch sein. |
    | InstanceNumber | 2-stelliger SAP-Instanz-Identifier.|

    * Ressourcenstatus
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       Clone: SAPHanaTopology_HR2_00-clone
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat type=SAPHanaTopology)
         Attributes: InstanceNumber=00 SID=HR2
         Operations: monitor interval=60 timeout=60 (SAPHanaTopology_HR2_00-monitor-interval-60)
                     start interval=0s timeout=180 (SAPHanaTopology_HR2_00-start-interval-0s)
                     stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
       
         
       ```

4.  Erstellen einer primären/sekundären SAPHana-Ressource.
    * Die SAPHana-Ressource ist für das Starten, Beenden und die neue Verortung der SAP HANA verantwortlich. Diese Ressource muss als primäre/sekundäre Clusterressource ausgeführt werden. Die Ressource besitzt die folgenden Attribute.

| Attributname            | Erforderlich? | Standardwert | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | Ja       | Keine          | SAP-Systembezeichner (SID) der SAP HANA-Installation. Muss für alle Knoten identisch sein.                                                                                                                                                                                                                                                                                                                                                                                       |
| InstanceNumber            | Ja       | none          | 2-stelliger SAP-Instanz-Identifier.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | nein        | ja           | Sollte der Cluster den Wechsel zur sekundären Instanz vorziehen, anstatt die primäre Instanz lokal neu zu starten? („nein“: Lokales Neustarten bevorzugen; „Ja“: Übernahme vor Remotestandort bevorzugen)                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | nein        | FALSE         | Soll die erste SAP HANA nach der Übernahme als sekundär registriert werden und DUPLICATE_PRIMARY_TIMEOUT? („false“: Nein, ein manueller Eingriff ist erforderlich. „true“: Ja, das frühere primäre Replikat wird vom Ressourcen-Agent als sekundär registriert).                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | nein        | 7200          | Zeitunterschied (in Sekunden) zwischen primären Zeitstempeln, wenn eine duale primäre Situation auftritt. Wenn der Zeitunterschied kleiner als die Zeitlücke ist, enthält der Cluster eine oder beide Instanzen mit dem Status „WARTEN“. Dies soll einem Administrator die Möglichkeit geben, auf ein Failover zu reagieren. Ein fehlerhaftes früheres primäres Replikat wird registriert, nachdem der Zeitunterschied überschritten wurde. Nach dieser Registrierung beim neuen primären Replikat werden alle Daten von der Systemreplikation überschrieben. |

5.  Erstellen der HANA-Ressource.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=true op start timeout=3600 \
    op stop timeout=3600 \
    op monitor interval=61 role="Slave" timeout=700 \
    op monitor interval=59 role="Master" timeout=700 \
    op promote timeout=3600 \
    op demote timeout=3600 \
    master meta notify=true clone-max=2 clone-node-max=1 interleave=true


    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary
     Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true
     Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)
      Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200 InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2
      Operations: demote interval=0s timeout=320 (SAPHana_HR2_00-demote-interval-0s)
                  monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)
                  monitor interval=121 role=Secondary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-121)
                  monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-119)
                  promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)
                  start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)
                  stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)
   

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Erstellen einer virtuelle IP-Adressressource.
    Der Cluster enthält die virtuelle IP-Adresse, um die primäre Instanz von SAP HANA zu erreichen. Im Folgenden finden Sie einen Beispielbefehl zum Erstellen einer IPaddr2-Ressource mit DER IP-Adresse 10.7.0.84/24.
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Erstellen von Einschränkungen.
    * Für den richtigen Betrieb müssen wir sicherstellen, dass SAPHanaTopology-Ressourcen gestartet werden, bevor die SAPHana-Ressourcen gestartet werden, und dass die virtuelle IP-Adresse auf dem Knoten vorhanden ist, auf dem die primäre Ressource von SAPHana ausgeführt wird. Um dies zu erreichen, müssen die folgenden 2 Einschränkungen erstellt werden.
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Testen des manuellen Verschiebens der SAPHana-Ressource auf einen anderen Knoten

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP HANA-Übernahme nach Cluster)


Verwenden Sie den folgenden Befehl, um die Verschiebung der SAPHana-Ressource von einem Knoten zu einem anderen zu testen. Beachten Sie, dass die Option `--primary` bei der Ausführung des folgenden Befehls nicht verwendet werden sollte, da die SAPHana-Ressource intern funktioniert.

`pcs resource move SAPHana_HR2_00-primary`

Nach jedem Aufruf des Befehls zum Verschieben der PCS-Ressource erstellt der Cluster Ortseinschränkungen, um die Verschiebung der Ressource zu erreichen. Diese Einschränkungen müssen entfernt werden, um in Zukunft ein automatisches Failover zuzulassen.
Um Sie zu entfernen, können Sie den folgenden Befehl verwenden.

```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Melden Sie sich zur Überprüfung bei HANA an.

  * Tiefer gestufter Host:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Höher gestufter Host:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Mit der Option `AUTOMATED_REGISTER=false` ist es nicht möglich, hin und her zu wechseln.

Wenn diese Option auf FALSE festgelegt ist, müssen Sie den Knoten erneut registrieren:
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

Nun fungiert node2, der der primäre Host war, als sekundärer Host.

Legen Sie diese Option auf TRUE fest, um die Registrierung des tiefer gestuften Hosts zu automatisieren.
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

Ob Sie die automatische Registrierung bevorzugen, hängt vom Kundenszenario ab. Das automatische erneute Registrieren des Knotens nach einer Übernahme ist für das Betriebsteam einfacher. Sie können den Knoten jedoch manuell registrieren, um zunächst zusätzliche Tests auszuführen, um sicherzustellen, dass alles wie erwartet funktioniert.

##  <a name="references"></a>References

1. [Automatisierte SAP HANA-Systemreplikation beim Hochskalieren im Pacemaker-Cluster](https://access.redhat.com/articles/3397471)
2. [Unterstützungsrichtlinien für RHEL-Hochverfügbarkeitscluster – Verwaltung von SAP HANA in einem Cluster](https://access.redhat.com/articles/3397471)
3. [Einrichten von Pacemaker unter RHEL in Azure – Azure Virtual Machines](high-availability-guide-rhel-pacemaker.md)
4. [Steuerung von HANA in Azure (große Instanzen) über das Azure-Portal – Azure Virtual Machines](hana-li-portal.md)
