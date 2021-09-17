---
title: Ändern der Größe eines Betriebssystem-Datenträgers, der eine GPT-Partition aufweist
description: Dieser Artikel enthält Anweisungen zum Ändern der Größe eines Betriebssystemdatenträgers mit einer GPT-Partition (GUID-Partitionstabelle) unter Linux.
ms.topic: how-to
author: kailashmsft
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: ca7a018accb0410c2656d7aeb4282b42f2756e39
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688223"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ändern der Größe eines Betriebssystem-Datenträgers, der eine GPT-Partition aufweist

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

> [!NOTE]
> Dieser Artikel ist nur für Betriebssystemdatenträger mit einer GPT-Partition (GUID-Partitionstabelle) relevant.

In diesem Artikel wird beschrieben, wie Sie die Größe eines Betriebssystem-Datenträgers heraufsetzen, der eine GPT-Partition unter Linux aufweist. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Bestimmen, ob der Betriebssystem-Datenträger eine MBR- oder eine GPT-Partition aufweist

Verwenden Sie den Befehl `parted`, um zu ermitteln, ob die Datenträgerpartition mit einer MBR-Partition (Master Boot Record) oder einer GPT-Partition erstellt wurde.

### <a name="mbr-partition"></a>MBR-Partition

In der folgenden Ausgabe zeigt **Partition Table**  den Wert **msdos**. Dieser Wert kennzeichnet eine MBR-Partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-Partition

In der folgenden Ausgabe zeigt **Partition Table**  den Wert **gpt**. Dieser Wert kennzeichnet eine GPT-Partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Wenn Ihr virtueller Computer (VM) eine GPT-Partition für Ihren Betriebssystem-Datenträger aufweist, setzen Sie die Größe des Betriebssystem-Datenträgers herauf.

## <a name="increase-the-size-of-the-os-disk"></a>Heraufsetzen der Größe des Betriebssystem-Datenträgers

Die folgenden Anweisungen betreffen Linux-gestützte Verteilungen.

> [!NOTE]
> Bevor Sie fortfahren, erstellen Sie eine Sicherungskopie Ihrer VM oder eine Momentaufnahme Ihres Betriebssystem-Datenträgers.

### <a name="ubuntu"></a>Ubuntu

So setzen Sie die Größe des Betriebssystemdatenträgers in Ubuntu 16.*x* und 18.*x* herauf:

1. Beenden Sie den virtuellen Computer.
1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
1. Führen Sie einen Neustart der VM aus, und melden Sie sich dann bei der VM als **Root**-Benutzer an.
1. Vergewissern Sie sich, dass der Betriebssystem-Datenträger jetzt ein größeres Dateisystem anzeigt.

Wie im folgenden Beispiel gezeigt, wurde der Betriebssystemdatenträger im Portal auf 100 GB vergrößert. Das auf **/** eingebundene Dateisystem **/dev/sda1** zeigt jetzt 97 GB an.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

So setzen Sie die Größe des Betriebssystem-Datenträgers in SUSE 12 SP4, SUSE SLES 12 for SAP, SUSE SLES 15 und SUSE SLES 15 for SAP herauf:

1. Beenden Sie den virtuellen Computer.
1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
1. Starten Sie den virtuellen Computer neu.

Wenn der virtuelle Computer neu gestartet wurde, führen Sie diese Schritte aus:

1. Greifen Sie als **Root**-Benutzer mit folgendem Befehl auf Ihre VM zu:

   ```
   # sudo -i
   ```

1. Verwenden Sie den folgenden Befehl, um das Paket **growpart** zu installieren, das zum Ändern der Größe der Partition verwendet wird:

   ```
   # zypper install growpart
   ```

1. Verwenden Sie den Befehl `lsblk`, um die Partition zu suchen, die im Stammverzeichnis des Dateisystems ( **/** ) eingebunden ist. In diesem Fall sehen Sie, dass Partition 4 des Geräts **sda** unter **/** eingebunden ist:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Ändern Sie die Größe der erforderlichen Partition mit dem Befehl `growpart` und der im vorherigen Schritt bestimmten Partitionsnummer:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Führen Sie erneut den Befehl `lsblk` aus, um zu überprüfen, ob die Partition vergrößert wurde.

   Die folgende Ausgabe zeigt, dass die Größe der Partition **/dev/sda4** in 46,5 GB geändert wurde:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifizieren Sie den Typ des Dateisystems auf dem Betriebssystemdatenträger mithilfe des Befehls `lsblk` mit dem Flag `-f`:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Verwenden Sie die für den Typ des Dateisystems passenden Befehle, um die Größe des Dateisystems zu ändern.
   
   Verwenden Sie für **xfs** diesen Befehl:
   
   ```
   #xfs_growfs /
   ```
   
   Beispielausgabe:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Verwenden Sie für **ext4** diesen Befehl:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Überprüfen Sie die heraufgesetzte Größe des Dateisystems für **df -Th** mit diesem Befehl:
   
   ```
   #df -Thl
   ```
   
   Beispielausgabe:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Im vorherigen Beispiel können wir sehen, dass die Größe des Dateisystems für den Betriebssystem-Datenträger heraufgesetzt wurde.

### <a name="rhel-with-lvm"></a>RHEL mit LVM

1. Greifen Sie als **Root**-Benutzer mit diesem Befehl auf Ihre VM zu:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Verwenden Sie den Befehl `lsblk`, um das logische Volume (LV) zu suchen, das im Stammverzeichnis des Dateisystems ( **/** ) eingebunden ist. In diesem Fall sehen Sie, dass **rootvg-rootlv** unter **/** eingebunden ist. Wenn Sie ein anderes Dateisystem benötigen, ersetzen Sie den LV- und Bereitstellungspunkt in diesem Artikel durchgängig.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Überprüfen Sie, ob in der LVM-Volumegruppe (VG) mit der Stammpartition freier Speicherplatz verfügbar ist. Wenn freier Speicherplatz verfügbar ist, fahren Sie mit Schritt 12 fort.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   In diesem Beispiel gibt die Zeile **Free PE / Size** an, dass in der Volumegruppe 38,02 GB frei sind. Sie müssen die Größe des Datenträgers nicht ändern, bevor Sie der Volumegruppe Speicherplatz hinzufügen.

1. So setzen Sie die Größe des Betriebssystemdatenträgers in RHEL 7.*x* mit LVM herauf:

   1. Beenden Sie den virtuellen Computer.
   1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
   1. Starten Sie den virtuellen Computer.

1. Wenn der Neustart der VM erfolgt ist, führen Sie die folgenden Schritte aus:

   - Installieren Sie das Paket **cloud-utils-growpart**, um den Befehl **growpart** bereitzustellen, der zum Vergrößern des Betriebssystemdatenträgers und des gdisk-Handlers für GPT-Datenträgerlayouts erforderlich ist. Diese Pakete sind auf den meisten Marketplace-Images vorinstalliert.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Ermitteln Sie mit dem Befehl `pvscan`, auf welchem Datenträger und welcher Partition das physische LVM-Volume bzw. die Volumes (PV) in der Volumegruppe mit dem Namen **rootvg** enthalten sind. Notieren Sie sich die Größe und den freien Speicherplatz, die zwischen den Klammern ( **[** und **]** ) angegeben sind.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Überprüfen Sie die Größe der Partition mit `lsblk`. 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Erweitern Sie die Partition mit dieser PV mit `growpart`, dem Gerätenamen und der Partitionsnummer. Dadurch wird die angegebene Partition so erweitert, dass der gesamte freie zusammenhängende Speicherplatz auf dem Gerät verwendet wird.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Überprüfen Sie erneut mit dem Befehl `lsblk`, ob die Größe der Partition wie erwartet geändert wurde. Beachten Sie, dass in diesem Beispiel **sda4** von 63 GB in 95 GB geändert wurde.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Erweitern Sie das PV zur Verwendung der restlichen neu erweiterten Partition:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Überprüfen Sie, ob die neue Größe des PV der erwarteten Größe entspricht, und vergleichen Sie dabei die ursprünglichen Werte für **[Größe/freien Speicherplatz]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Erweitern Sie das gewünschte logische Volume (LV) um die gewünschte Menge. Die Menge muss nicht der gesamte freie Speicherplatz in der Volumegruppe sein. Im folgenden Beispiel wird die Größe von **/dev/mapper/rootvg-rootlv** von 2 GB auf 12 GB heraufgesetzt (um 10 GB). Mit diesem Befehl wird außerdem die Größe des Dateisystems heraufgesetzt.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Beispielausgabe:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. Mit dem Befehl `lvresize` wird automatisch der entsprechende Befehl zum Ändern der Größe für das Dateisystem im LV aufgerufen. Überprüfen Sie mithilfe des folgenden Befehls, ob die Größe des Dateisystems unter **/dev/mapper/rootvg-rootlv** (unter **/** eingebunden) erweitert wurde:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Beispielausgabe:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Um die gleiche Vorgehensweise zum Ändern der Größe jedes beliebigen anderen logischen Volumes zu verwenden, ändern Sie in Schritt 12 den LV-Namen.


### <a name="rhel-raw"></a>RHEL RAW

So setzen Sie die Größe des Betriebssystemdatenträgers in einer RHEL-RAW-Partition herauf:

1. Beenden Sie den virtuellen Computer.
1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
1. Starten Sie den virtuellen Computer.

Wenn der virtuelle Computer neu gestartet wurde, führen Sie diese Schritte aus:

1. Greifen Sie als **Root**-Benutzer auf Ihre VM zu, und verwenden Sie dazu den folgenden Befehl:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Wenn der Neustart der VM erfolgt ist, führen Sie die folgenden Schritte aus:

   - Installieren Sie das Paket **cloud-utils-growpart**, um den Befehl **growpart** bereitzustellen, der zum Vergrößern des Betriebssystemdatenträgers und des gdisk-Handlers für GPT-Datenträgerlayouts erforderlich ist. Dieses Paket ist auf den meisten Marketplace-Images vorinstalliert.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Überprüfen Sie mit dem Befehl **lsblk -f** die Partition und den Dateisystemtyp mit der Stammpartition ( **/** ):

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. Zur Überprüfung müssen Sie zunächst die Partitionstabelle des SDA-Datenträgers mit **gdisk** auflisten. In diesem Beispiel sehen wir einen 48-GB-Datenträger mit Partition 2 bei 29,0 GiB. Der Datenträger wurde im Azure-Portal von 30 auf 48 GB erweitert.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Erweitern Sie die Partition für das Stammverzeichnis, in diesem Fall sda2, mit dem **growpart**-Befehl. Mit diesem Befehl wird die Partition so erweitert, dass der gesamte zusammenhängende Speicherplatz auf dem Datenträger verwendet wird.

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. Geben Sie nun die neue Partitionstabelle mit **gdisk** aus.  Beachten Sie, dass Partition 2 auf 47,0 GiB erweitert wurde:

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Erweitern Sie das Dateisystem auf der Partition mit **xfs_growfs**. Dies eignet sich für ein standardmäßiges vom Marketplace generiertes RedHat-System:

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. Vergewissern Sie sich mit dem Befehl **df**, dass die neue Größe wiedergegeben wird:

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
