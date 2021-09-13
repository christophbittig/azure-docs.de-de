---
title: Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files | Microsoft-Dokumentation
description: Hier werden bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files beschrieben.
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
ms.openlocfilehash: d0d097a13fa24fb62f70648a34edd27e3b5a0636
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203857"
---
# <a name="linux-filesystem-cache-best-practices-for-azure-netapp-files"></a>Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files

Dieser Artikel enthält Informationen über bewährte Methoden zum Dateisystemcache für Azure NetApp Files.  

## <a name="filesystem-cache-tunables"></a>Abstimmbare Dateisystemcache-Werte

Sie müssen sich mit den folgenden Faktoren über abstimmbare Dateisystemcache-Werte auskennen:  

* Durch das Leeren eines geänderten Puffers bleiben die Daten in einem fehlerfreien Zustand für zukünftige Leseläufe verwendbar, bis hohe Arbeitsspeicherauslastung zur Entfernung führt.  
* Es gibt drei Trigger für einen asynchronen Leerungsvorgang:
    * Zeitbasiert: Wenn ein Puffer das durch diesen abstimmbaren Wert definierte Alter erreicht, muss er für die Bereinigung (d. h. Leeren oder Schreiben in den Speicher) gekennzeichnet werden.
    * Hohe Arbeitsspeicherauslastung: Weitere Informationen finden Sie unter [`vm.dirty_ratio | vm.dirty_bytes`](#vmdirty_ratio--vmdirty_bytes).
    * Schließen: Wenn ein Dateihandle geschlossen wird, werden alle geänderten Puffer asynchron in den Speicher geleert.

Diese Faktoren werden von vier abstimmbaren Werten gesteuert. Jeder abstimmbare Wert kann dynamisch und dauerhaft mit `tuned` oder `sysctl` in der `/etc/sysctl.conf`-Datei abgestimmt werden. Die Abstimmung dieser Variablen verbessert die Leistung für Anwendungen.  

> [!NOTE]
> In diesem Artikel erläuterte Informationen wurden bei SAS GRID- und SAS Viya-Validierungsübungen entdeckt. Daher basieren die abstimmbaren Werte auf Erkenntnissen, die aus den Validierungsübungen gewonnen wurden. Viele Anwendungen profitieren auf ähnliche Weise von der Optimierung dieser Parameter.

### `vm.dirty_ratio | vm.dirty_bytes` 

Diese beiden abstimmbaren Werte definieren, wieviel RAM für Daten zur Verfügung gestellt wird, die geändert, aber noch nicht in den nicht flüchtigen Speicher geschrieben wurden.  Wenn ein abstimmbarer Wert festgelegt wird, wird der andere abstimmbare Wert automatisch auf 0 (null) gesetzt. RedHat rät davon ab, einen der beiden abstimmbare Werten manuell auf 0 (null) zu setzen.  Die Option `vm.dirty_ratio` (die Standardeinstellung der beiden) wird von Redhat je nach Betriebssystem entweder auf 20 % oder 30 % des physischen Arbeitsspeichers festgelegt. Unter Berücksichtigung des Arbeitsspeicherbedarfs moderner Systeme ist dies ein erheblicher Teil. Unabhängig von der Arbeitsspeichergröße sollte die Einstellung `vm.dirty_bytes` anstelle von `vm.dirty_ratio` berücksichtigt werden, um eine konsistentere Erfahrung zu gewährleisten.  Bei der laufenden Arbeit mit SAS GRID wurde beispielsweise 30 MiB als geeignete Einstellung für eine optimale Gesamtleistung gemischter Workloads ermittelt. 

### `vm.dirty_background_ratio | vm.dirty_background_bytes` 

Diese abstimmbaren Werte definieren den Ausgangspunkt, an dem der Linux-Rückschreibmechanismus beginnt, geänderte Blöcke in nicht flüchtigen Speicher zu leeren. Redhat verwendet standardmäßig 10 % des physischen Arbeitsspeichers, was in einem großen Speichersystem eine erhebliche Menge an Daten für den Beginn des Leerens darstellt. Bei SAS GRID wurde beispielsweise immer empfohlen, `vm.dirty_background` auf eine Größe von 1/5 von `vm.dirty_ratio` oder `vm.dirty_bytes` festzulegen. Unter Berücksichtigung dessen, wie aggressiv die `vm.dirty_bytes`-Einstellung für SAS GRID festgelegt wird, wird hier kein bestimmter Wert festgelegt.  

### `vm.dirty_expire_centisecs` 

Dieser abstimmbare Wert definiert, wie alt ein geänderter Puffer sein kann, bevor er für das asynchrone Ausschreiben markiert werden muss. Nehmen Sie die CAS-Workload von SAS Viya als Beispiel. Bei einer kurzlebigen schreibdominanten Workload wurde festgestellt, dass dieser Wert mit 300 Hundertstelsekunden (3 Sekunden) optimal war, wobei die Standardeinstellung bei 3000 Hundertstelsekunden (30 Sekunden) liegt.  

SAS Viya unterteilt CAS-Daten in mehrere kleine Blöcke mit jeweils wenigen Megabytes.  Anstatt diese Dateihandles nach dem Schreiben von Daten in die einzelnen Shards zu schließen, bleiben die Handles geöffnet, und die Puffer darin werden von der Anwendung im Arbeitsspeicher zugeordnet.  Ohne Schließen erfolgt keine Leerung, bis entweder eine hohe Arbeitsspeicherauslastung entsteht oder 30 Sekunden vergangen sind. Das Warten auf hohe Arbeitsspeicherauslastung hat sich ebenso wie das Warten auf das Ende eines langen Timerablaufs als suboptimal erwiesen. Im Gegensatz zu SAS GRID, wo nach dem besten Gesamtdurchsatz gesucht wurde, suchte SAS Viya nach einer Optimierung der Schreibbandbreite.  

### `vm.dirty_writeback_centisecs` 

Der Kernelflusherthread ist für das asynchrone Leeren von geänderten Puffern zwischen den einzelnen Leerungsthread-Ruhezuständen zuständig.  Dieser abstimmbare Wert definiert die Dauer der Ruhezustände zwischen Leerungen.  Unter Berücksichtigung des von SAS Viya verwendeten 3-Sekunden-`vm.dirty_expire_centisecs`-Werts legt SAS diesen abstimmbaren Wert auf 100 Hundertstelsekunden (1 Sekunde) statt auf den Standardwert von 500 Hundertstelsekunden (5 Sekunden) fest, um die beste Gesamtleistung zu ermitteln.

## <a name="impact-of-an-untuned-filesystem-cache"></a>Auswirkungen eines nicht angepassten Dateisystemcaches

Unter Berücksichtigung der standardmäßigen VHD-Optimierungen und der RAM-Menge in modernen Systemen verlangsamt das Rückschreiben möglicherweise andere speichergebundene Vorgänge aus der Perspektive des spezifischen Clients, der diese gemischte Workload steuert.  Die folgenden Symptome können von einem nicht optimierten Linux-Computer mit vielen Schreibvorgängen und hoher Cacheauslastung erwartet werden.  

* `ls`-Verzeichnisauflistungen dauern so lange, dass der Computer nicht zu reagieren scheint.
* Der Lesedurchsatz für das Dateisystem verringert sich im Vergleich zum Schreibdurchsatz erheblich.
* `nfsiostat` meldet Schreiblatenzen **mindestens in Sekunden**.

Dieses Verhalten tritt möglicherweise nur auf *dem Linux-Computer* auf, der die gemischte Workload mit vielen Schreibzugriffen ausführt.  Darüber hinaus wird die Erfahrung für alle NFS-Volumes beeinträchtigt, die an einem einzelnen Speicherendpunkt eingebunden sind.  Wenn die Einbindungen von zwei oder mehr Endpunkten stammen, weisen dieses Verhalten nur die Volumes auf, die einen Endpunkt gemeinsam nutzen.

Mit dem Festlegen der Dateisystemcache-Parameter, wie in diesem Abschnitt beschrieben, konnten die Probleme behoben werden.

## <a name="monitoring-virtual-memory"></a>Überwachung des virtuellen Arbeitsspeichers

Um die Abläufe bezüglich des virtuellen Arbeitsspeichers und Rückschreibens zu verstehen, sehen Sie sich den folgenden Codeausschnitt und die folgende Ausgabe an.  *Dirty* stellt die Menge des geänderten Arbeitsspeichers im System dar und *writeback* die Menge des Arbeitsspeichers, die aktiv in den Speicher geschrieben wird.  

`# while true; do echo "###" ;date ; egrep "^Cached:|^Dirty:|^Writeback:|file" /proc/meminfo; sleep 5; done`

Die folgende Ausgabe stammt aus einem Experiment, bei dem das Verhältnis von `vm.dirty_ratio` und `vm.dirty_background` auf 2 % bzw. 1 % des physischen Arbeitsspeichers festgelegt wurde.  In diesem Fall begann die Leerung bei 3,8 GiB, 1 % des 384-GiB-Arbeitsspeichersystems.  Das Rückschreiben ähnelte dem Schreibdurchsatz in NFS. 

```
Cons
Dirty:                                    1174836 kB
Writeback:                         4 kB
###
Dirty:                                    3319540 kB
Writeback:                         4 kB
###
Dirty:                                    3902916 kB        <-- Writes to stable storage begins here
Writeback:                         72232 kB   
###
Dirty:                                    3131480 kB
Writeback:                         1298772 kB   
``` 

## <a name="next-steps"></a>Nächste Schritte  

* [Bewährte Methoden in Bezug auf direkte E/A unter Linux für Azure NetApp Files](performance-linux-direct-io.md)
* [Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files](performance-linux-mount-options.md)
* [Bewährte Methoden für Linux-Parallelität für Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Bewährte Methoden für NFS-Read-Ahead unter Linux](performance-linux-nfs-read-ahead.md)
* [Bewährte Methoden für SKUs für virtuelle Azure-Computer](performance-virtual-machine-sku.md) 
* [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md) 
