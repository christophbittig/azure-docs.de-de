---
title: 'Bewährte Methoden für NFS-Read-Ahead unter Linux für Azure NetApp Files: Sitzungsslots und Slottabelleneinträge | Microsoft-Dokumentation'
description: Beschreibt bewährte Methoden für Dateisystemcache und NFS-Read-Ahead unter Linux für Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: f87d2742ab34cdf5b6509e88b403240d388fa373
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233486"
---
# <a name="linux-nfs-read-ahead-best-practices-for-azure-netapp-files"></a>Bewährte Methoden für NFS-Read-Ahead unter Linux für Azure NetApp Files

Dieser Artikel enthält Informationen und bewährte Methoden zum Dateisystemcache für Azure NetApp Files.  

Beim NFS-Read-Ahead werden Blöcke aus einer Datei vorausschauend angefordert, bevor E/A-Anforderungen durch die Anwendung eingehen. Diese Funktion wurde entwickelt, um den sequenziellen Lesedurchsatz des Clients zu verbessern.  Bis vor Kurzem haben alle modernen Linux-Distributionen den Read-Ahead-Wert auf das 15-Fache von `rsize` des bereitgestellten Dateisystems festgelegt.  

Die folgende Tabelle zeigt die standardmäßigen Read-Ahead-Werte für jede angegebene `rsize`-Bereitstellungsoption.

| `rsize` des bereitgestellten Dateisystems | Im Voraus gelesene Blöcke |
|-|-|
| 64 KiB | 960 KiB |
| 256 KiB | 3\.840 KiB |
| 1\.024 KiB | 15.360 KiB |

Mit RHEL 8.3 und Ubuntu 18.04 wurden Änderungen eingeführt, die die Leistung sequenzieller Lesevorgänge auf Clients beeinträchtigen können.  Im Gegensatz zu früheren Versionen legen diese Distributionen den Read-Ahead-Wert unabhängig von der verwendeten `rsize`-Bereitstellungsoption auf 128 KiB fest. Bei einem Upgrade von Releases mit dem größeren Read-Ahead-Wert auf Releases mit dem Standardwert von 128 KiB wurde bei sequenziellen Lesevorgängen ein Leistungsabfall festgestellt. Read-Ahead-Werte können jedoch sowohl dynamisch als auch dauerhaft erhöht werden.  Beispielsweise wurde bei Tests mit SAS GRID der Lesewert von 15.360 KiB im Vergleich zu 3.840 KiB, 960 KiB und 128 KiB als optimal befunden.  Über 15.360 KiB hinaus wurden nicht genügend Tests ausgeführt, um positive oder negative Auswirkungen zu ermitteln.

In der folgenden Tabelle sind die Read-Ahead-Standardwerte für jede zurzeit verfügbare Distribution aufgeführt.

|     Verteilung    |     Freigabe    |     Im Voraus gelesene Blöcke    |
|-|-|-|
|     RHEL    |     8.3    |     128 KiB    |
|     RHEL    |     7.X, 8.0, 8.1, 8.2    |     15 × `rsize`    |
|     SLES    |     12.X – mindestens 15SP2    |     15 × `rsize`    |
|     Ubuntu    |     18.04 – mindestens 20.04    |     128 KiB    |
|     Ubuntu    |     16.04    |     15 × `rsize`    |
|     Debian    |     Bis zu 10 und zugehörige Nebenversionen    |     15 × `rsize`    |


## <a name="how-to-work-with-per-nfs-filesystem-read-ahead"></a>Arbeiten mit Read-Ahead pro NFS   

NFS-Read-Ahead wird am Bereitstellungspunkt für ein NFS definiert. Die Standardeinstellung kann angezeigt und sowohl dynamisch als auch dauerhaft festgelegt werden.  Der Einfachheit halber wurde das folgende von Red Hat geschriebene Bash-Skript zur Verfügung gestellt, um Read-Ahead für ein bereitgestelltes NFS anzuzeigen oder dynamisch festzulegen.

Read-Ahead kann entweder dynamisch pro NFS-Bereitstellung mithilfe des folgenden Skripts oder dauerhaft anhand von `udev`-Regeln definiert werden, wie in diesem Abschnitt gezeigt.  Um Read-Ahead für ein bereitgestelltes NFS anzuzeigen oder festzulegen, können Sie das folgende Skript als Bash-Datei speichern, die Berechtigungen der Datei so ändern, dass sie ausgeführt werden kann (`chmod 544 readahead.sh`), und sie wie gezeigt ausführen. 

## <a name="how-to-show-or-set-read-ahead-values"></a>Anzeigen oder Festlegen von Read-Ahead-Werten   

Führen Sie den folgenden Befehl aus, um den aktuellen Read-Ahead-Wert (in KiB) zu zeigen:  

`$ ./readahead.sh  show <mount-point>`   

Führen Sie den folgenden Befehl aus, um einen neuen Wert für Read-Ahead festzulegen:   

`$ ./readahead.sh  show <mount-point> [read-ahead-kb]`
 
### <a name="example"></a>Beispiel   

```
#!/bin/bash
# set | show readahead for a specific mount point
# Useful for things like NFS and if you do not know / care about the backing device
#
# To the extent possible under law, Red Hat, Inc. has dedicated all copyright
# to this software to the public domain worldwide, pursuant to the
# CC0 Public Domain Dedication. This software is distributed without any warranty.
# See <http://creativecommons.org/publicdomain/zero/1.0/>.
#

E_BADARGS=22
function myusage() {
echo "Usage: `basename $0` set|show <mount-point> [read-ahead-kb]"
}

if [ $# -gt 3 -o $# -lt 2 ]; then
   myusage
   exit $E_BADARGS
fi

MNT=${2%/}
BDEV=$(grep $MNT /proc/self/mountinfo | awk '{ print $3 }')

if [ $# -eq 3 -a $1 == "set" ]; then
   echo $3 > /sys/class/bdi/$BDEV/read_ahead_kb
elif [ $# -eq 2 -a $1 == "show" ]; then
   echo "$MNT $BDEV /sys/class/bdi/$BDEV/read_ahead_kb = "$(cat /sys/class/bdi/$BDEV/read_ahead_kb)
else
   myusage
   exit $E_BADARGS
fi
```

## <a name="how-to-persistently-set-read-ahead-for-nfs-mounts"></a>Dauerhaftes Festlegen von Read-Ahead für NFS-Bereitstellungen

Zum dauerhaften Festlegen von Read-Ahead für NFS-Bereitstellungen können `udev`-Regeln wie folgt geschrieben werden:    

1. Erstellen und testen Sie `/etc/udev/rules.d/99-nfs.rules`:

    `SUBSYSTEM=="bdi", ACTION=="add", PROGRAM="/bin/awk -v bdi=$kernel 'BEGIN{ret=1} {if ($4 == bdi) {ret=0}} END{exit ret}' /proc/fs/nfsfs/volumes", ATTR{read_ahead_kb}="15380"`

2. Wenden Sie die `udev`-Regel an:   

    `$udevadm control --reload`

## <a name="next-steps"></a>Nächste Schritte  

* [Bewährte Methoden in Bezug auf direkte E/A unter Linux für Azure NetApp Files](performance-linux-direct-io.md)
* [Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files](performance-linux-mount-options.md)
* [Best Practices für die Linux-Parallelität](performance-linux-concurrency-session-slots.md)
* [Bewährte Methoden für SKUs für virtuelle Azure-Computer](performance-virtual-machine-sku.md) 
* [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md) 
