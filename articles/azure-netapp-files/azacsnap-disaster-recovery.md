---
title: Notfallwiederherstellung mithilfe des Tools für anwendungskonsistente Momentaufnahmen in Azure für Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie mithilfe des Tools für anwendungskonsistente Momentaufnahmen in Azure, das Sie mit Azure NetApp Files verwenden können, eine Notfallwiederherstellung durchführen.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 1fbf8052a3852e010fc002116cab131ea4f371b8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438100"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool"></a>Notfallwiederherstellung mithilfe des Tools für anwendungskonsistente Momentaufnahmen in Azure

In diesem Artikel wird erläutert, wie Sie mithilfe des Tools für anwendungskonsistente Momentaufnahmen in Azure, das Sie mit Azure NetApp Files verwenden können, eine Notfallwiederherstellung durchführen.

> [!IMPORTANT]
> Dieser Vorgang gilt nur für **große Azure-Instanzen**.

## <a name="introduction"></a>Einführung

Auf der Plattform für große Azure-Instanzen kann auch ein Standort für die Notfallwiederherstellung konfiguriert werden, in dem Momentaufnahmen von Speichervolumes repliziert werden können.  Wenn Momentaufnahmen in einem solchen Setup ordnungsgemäß konfiguriert wurden, ist es möglich, eine Notfallwiederherstellung an diesem Standort durchzuführen.  Das vorliegende Dokument soll als Leitfaden für die Durchführung einer Notfallwiederherstellung für dieses Setup dienen.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Voraussetzungen für das Setup der Notfallwiederherstellung

Bevor Sie ein Failover für die Notfallwiederherstellung (Disaster Recovery, DR) planen können, müssen folgende Voraussetzungen erfüllt sein:

- Sie haben am DR-Standort einen DR-Knoten bereitgestellt. Es gibt zwei Optionen für die Notfallwiederherstellung: eine normale Notfallwiederherstellung und eine Notfallwiederherstellung für verschiedene Zwecke.
- Die Speicherreplikation funktioniert. Das Microsoft-Betriebsteam führt das Setup der Speicherreplikation zum Zeitpunkt der DR-Bereitstellung automatisch durch. Sie können die Speicherreplikation mithilfe des Befehls `azacsnap -c details --details replication` am DR-Standort überwachen.
- Sie haben Speichermomentaufnahmen am primären Standort eingerichtet und konfiguriert.
- Sie haben für die primäre Instanz eine HANA-Instanz am DR-Standort installiert, die über dieselbe SID verfügt wie die primäre Instanz.
- Sie haben das unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](../virtual-machines/workloads/sap/hana-failover-procedure.md) beschriebene Failoververfahren für die Notfallwiederherstellung gelesen und verstanden.
- Sie haben Speichermomentaufnahmen am DR-Standort eingerichtet und konfiguriert.
- Es wurde eine Konfigurationsdatei (z. B. `DR.json`) mit den Speichervolumes für die Notfallwiederherstellung und den zugehörigen Informationen auf dem DR-Server erstellt.
- Sie haben die folgenden Schritte am Standort für die Notfallwiederherstellung vollständig ausgeführt:
  - Aktivieren der Kommunikation mit dem Speicher
  - Aktivieren der Kommunikation mit SAP HANA

## <a name="set-up-disaster-recovery"></a>Einrichten der Notfallwiederherstellung

Microsoft unterstützt die Replikation auf Speicherebene für die Notfallwiederherstellung. Es gibt zwei Möglichkeiten, die Notfallwiederherstellung einzurichten:

eine **normale** Notfallwiederherstellung und eine Notfallwiederherstellung für **verschiedene Zwecke**. Bei der **normalen** Notfallwiederherstellung verfügen Sie am DR-Standort über eine dedizierte Instanz für das Failover. Im Szenario mit der Notfallwiederherstellung für **verschiedene Zwecke** wird auf der Komponente mit der großen HANA-Instanz am DR-Standort eine weitere HANA-Instanz für QA oder Entwicklung ausgeführt. Da Sie aber über eine vorinstallierte HANA-Instanz im Ruhezustand verfügen, die dieselbe SID aufweist wie die HANA-Instanz, möchten Sie das Failover zu dieser Komponente mit der großen HANA-Instanz ausführen. Das Microsoft-Betriebsteam richtet die Umgebung einschließlich Speicherreplikation für Sie ein, basierend auf den Angaben im Formular der Serviceanfrage zum Zeitpunkt des Onboardings.

> [!IMPORTANT]
> Stellen Sie sicher, dass alle Voraussetzungen für das Setup der Notfallwiederherstellung erfüllt sind.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Überwachen der Datenreplikation vom primären Standort zum DR-Standort

Das Microsoft-Betriebsteam verwaltet und überwacht den DR-Link vom primären zum DR-Standort bereits.
Sie können die Datenreplikation von Ihrem primären Server zum DR-Server mithilfe des Momentaufnahmebefehls `azacsnap -c details --details replication` überwachen.

## <a name="perform-a-failover-to-dr-site"></a>Ausführen eines Failovers zum DR-Standort

Führen Sie den Failoverbefehl am DR-Standort aus: `azacsnap -c restore --restore revertvolume`.

> [!IMPORTANT]
> Der Befehl `azacsnap -c restore --restore revertvolume` unterbricht die Speicherreplikation vom Produktionsstandort zum Standort für die Notfallwiederherstellung. Sie müssen sich an das Microsoft-Betriebsteam wenden, um die Replikation wieder einzurichten. Sobald die Replikation erneut aktiviert wurde, werden alle Daten im DR-Speicher für diese SID initialisiert. Der Befehl, der das Failover ausführt, macht die neueste replizierte Speichermomentaufnahme verfügbar. Wenn Sie eine ältere Momentaufnahme wiederherstellen müssen, eröffnen Sie eine Supportanfrage – das Betriebsteam kann Ihnen dabei helfen, eine frühere Momentaufnahme bereitzustellen, die am DR-Standort wiederhergestellt wurde.

Im Folgenden finden Sie die allgemeinen Schritte für ein Failover zur Notfallwiederherstellung:

- Sie müssen die HANA-Instanz am **primären** Standort herunterfahren. Diese Aktion ist nur erforderlich, wenn Sie das Failover auf den Standort für die Notfallwiederherstellung wirklich ausführen, damit Dateninkonsistenzen zu vermieden werden.
- Fahren Sie die HANA-Instanz auf dem DR-Knoten für die Produktions-SID herunter.
- Führen Sie den Befehl `azacsnap -c restore --restore revertvolume` auf dem DR-Knoten mit der wiederherzustellenden SID aus.
  - Dieser Befehl unterbricht den Speicherreplikationslink zwischen dem primären Standort und dem DR-Standort.
  - Der Befehl stellt nur die Volumes „/data“ und „/logbackups“ wieder her. Das Volume „/shared“ wird NICHT wiederhergestellt. Stattdessen wird das vorhandene Volume „/shared“ für die SID am DR-Standort verwendet.
  - Binden Sie die Volumes „/data“ und „/logbackups“ ein, und stellen Sie sicher, dass Sie sie der Datei „fstab“ hinzufügen.
- Stellen Sie die HANA SYSTEMDB-Momentaufnahme wieder her. HANA Studio zeigt nur die aktuelle HANA-Momentaufnahme an, die unter der Speichermomentaufnahme verfügbar ist, die beim Ausführen des Befehls `azacsnap -c restore --restore revertvolume` wiederhergestellt wurde.
- Stellen Sie die Mandantendatenbank wieder her.
- Starten Sie die HANA-Instanz am DR-Standort für die Produktions-SID (Beispiel: in diesem Fall „H80“).
- Führen Sie Tests aus.

### <a name="example-performing-disaster-recovery"></a>Beispiel für die Ausführung der Notfallwiederherstellung

Im folgenden Unterabschnitt werden die einzelnen Schritte für ein Failover zum DR-Standort beschrieben.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Schritt 1: Abrufen der Volumedetails des DR-Knotens

Listen Sie mit dem Befehl `df –h` die Dateisysteme und zugeordneten Volumes auf, um nach dem Failover darauf verweisen zu können.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Schritt 2: Herunterfahren von HANA am primären Standort

Wenn Sie ein vollständiges Failover von Produktionsworkloads ausführen und eine Verbindung mit dem primären Produktionsstandort möglich ist, fahren Sie die SAP HANA-Instanzen herunter, für die ein Failover für die Notfallwiederherstellung ausgeführt wird.

Wenn Sie als root-Benutzer angemeldet sind, zeigt das folgende Beispiel, wie SAP HANA heruntergefahren werden kann.  Ersetzen Sie \<sid\> durch Ihre SAP HANA-ID.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Schritt 3: Herunterfahren von HANA am DR-Standort

Es ist wichtig, dass SAP HANA am DR-Standort heruntergefahren wird, bevor Sie die Volumes wiederherstellen.

Wenn Sie als root-Benutzer angemeldet sind, zeigt das folgende Beispiel, wie SAP HANA heruntergefahren werden kann.  Ersetzen Sie \<sid\> durch Ihre SAP HANA-ID.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Stellen Sie sicher, dass die HANA-Instanzen am DR-Standort offline sind, bevor Sie Volumes wiederherstellen.

#### <a name="step-4-restore-the-volumes"></a>Schritt 4: Wiederherstellen der Volumes

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_Ausgabe des Befehls für das DR-Failover_**:

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Die Schritte am Ende der Konsolenanzeige müssen ausgeführt werden, um die Speichervorbereitung für ein DR-Failover abzuschließen.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Schritt 5: Aufheben der Bereitstellung unnötiger Dateisysteme

Führen Sie den Befehl `umount` aus, um die Bereitstellung von nicht benötigten Dateisystemen und Volumes aufzuheben.

```bash
umount <Mount point>
```

Heben Sie die Bereitstellungspunkte für Daten- und Protokollsicherungen auf. Möglicherweise verfügen Sie in einem Szenario mit Aufskalierung über mehrere Bereitstellungspunkte für Daten.

#### <a name="step-6-configure-the-mount-points"></a>Schritt 6: Konfigurieren der Bereitstellungspunkte

Bearbeiten Sie die Datei `/etc/fstab`, und kommentieren Sie die Einträge für Daten- und Protokollsicherungen für die primäre SID (in diesem Beispiel „H80“) aus. Fügen Sie die Einträge für die neuen Bereitstellungspunkte hinzu, die aus den DR-Volumes des primären Standorts erstellt wurden. Die neuen Einträge für Bereitstellungspunkte werden in der Befehlsausgabe angegeben.

- Kommentieren Sie die vorhandenen, am DR-Standort ausgeführten Bereitstellungspunkte mit dem Zeichen `#` aus:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Fügen Sie `/etc/fstab` die folgenden Zeilen hinzu.
  > Dies sollte die dieselbe Ausgabe des Befehls sein.

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Schritt 7: Bereitstellen der Wiederherstellungsvolumes

Führen Sie den Befehl `mount –a` aus, um alle Bereitstellungspunkte bereitzustellen.

```bash
mount -a
```

Wenn Sie jetzt `df –h` ausführen, sollten Sie sehen, dass die `*_dp`-Volumes bereitgestellt wurden.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Schritt 8: Wiederherstellen von SYSTEMDB

Klicken Sie in HANA Studio mit der rechten Maustaste auf die SYSTEMDB-Instanz, und wählen Sie „Sicherung und Wiederherstellung“ und danach „Systemdatenbank wiederherstellen“ aus.

Informationen zum Wiederherstellen einer Datenbank, insbesondere der SYSTEMDB, aus einer Momentaufnahme finden Sie im Leitfaden.

#### <a name="step-9-recover-the-tenant-database"></a>Schritt 9: Wiederherstellen der Mandantendatenbank

Klicken Sie in HANA Studio mit der rechten Maustaste auf die SYSTEMDB-Instanz, und wählen Sie „Sicherung und Wiederherstellung“ und danach „Mandantendatenbank wiederherstellen“ aus.

Informationen zum Wiederherstellen von Datenbanken, insbesondere der TENANT-Datenbank(en), aus einer Momentaufnahme finden Sie im Leitfaden.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Ausführen von `azacsnap -c backup` am DR-Standort

Wenn Sie momentaufnahmebasierte Sicherungen am DR-Standort ausführen, muss der in der Konfigurationsdatei `azacsnap` am DR-Standort konfigurierte HANA-Servername mit dem Namen des Produktionsservers identisch sein.

> [!IMPORTANT]
> Durch Ausführen von `azacsnap -c backup` können Speichermomentaufnahmen am DR-Standort erstellt werden. Diese werden nicht automatisch an einem anderen Standort repliziert.  Arbeiten Sie mit dem Microsoft-Betriebsteam zusammen, um zu erfahren, wie Sie Dateien oder Daten wieder im ursprünglichen Produktionsstandort bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

- [Abrufen von Momentaufnahmedetails](azacsnap-cmd-ref-details.md)
- [Erstellen einer Sicherung](azacsnap-cmd-ref-backup.md)
