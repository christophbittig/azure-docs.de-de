---
title: 'Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files | Microsoft-Dokumentation'
description: Erfahren Sie mehr zu Einbindungsoptionen und bewährten Methoden zu ihrer Nutzung mit Azure NetApp Files.
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
ms.date: 06/01/2021
ms.author: b-juche
ms.openlocfilehash: 445920e1846e668f0baa3567111f169fd6dd7508
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232518"
---
# <a name="linux-nfs-mount-options-best-practices-for-azure-netapp-files"></a>Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files

Dieser Artikel enthält Informationen zum besseren Verständnis von Einbindungsoptionen sowie bewährte Methoden für deren Verwendung mit Azure NetApp Files.

## `Nconnect` 

Mithilfe der Einbindungsoption `nconnect` können Sie die Anzahl der Verbindungen (Netzwerkdatenflüsse) angeben, die zwischen NFS-Client und NFS-Endpunkt bis zum Grenzwert 16 eingerichtet werden sollen. In der Regel verwendet ein NFS-Client eine einzelne Verbindung zwischen sich selbst und dem Endpunkt.  Durch Steigern der Anzahl von Netzwerkdatenflüssen werden die Obergrenzen für E/A und Durchsatz erheblich erhöht. Tests haben ergeben, dass `nconnect=8` die beste Leistung liefert.  

Wenn Sie eine SAS-GRID-Umgebung mit mehreren Knoten für die Produktion vorbereiten, können Sie eine wiederholbare Verkürzung der Laufzeit um 30  %feststellen, von 8 Stunden auf 5,5 Stunden: 

| Einbindungsoption | Ausführungszeiten des Auftrags |
|-|-|
| Ohne `nconnect` | 8 Stunden |
| `nconnect=8`  | 5,5 Stunden | 

Bei beiden Testreihen wurden der gleiche virtuelle Computer (E32-8_v4) und RHEL 8.3 verwendet, wobei Read-Ahead auf 15 MiB festgelegt war.

Beachten Sie bei Verwenden von `nconnect` die folgenden Regeln:

* `nconnect` wird von Azure NetApp Files für alle wichtigen Linux-Distributionen unterstützt, allerdings nur für neuere Releases:

    | Linux-Release | NFSv3 (Mindestrelease) | NFSv4.1 (Mindestrelease) |
    |-|-|-|
    | Red Hat Enterprise Linux | RHEL 8.3 | RHEL 8.3 |
    | SUSE | SLES 12 SP4 oder SLES 15 SP1 | SLES 15 SP2 |
    | Ubuntu | Ubuntu 18.04 |          |

    > [!NOTE]
    > SLES 15S P2 ist das SUSE-Mindestrelease, von dem `nconnect` von Azure NetApp Files für NFSv4.1 unterstützt wird.  Alle anderen aufgeführten Releases sind die ersten mit dem Feature `nconnect`.

* Alle Einbindungen über einen einzelnen Endpunkt übernehmen die Einstellung von `nconnect` des ersten eingebundenen Exports, wie in den folgenden Szenarien gezeigt: 

    Szenario 1: `nconnect` wird von der ersten Einbindung verwendet. Daher verwenden alle Einbindungen über denselben Endpunkt `nconnect=8`.

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.10.10.10:/volume2 /mnt/volume2`
    * `mount 10.10.10.10:/volume3 /mnt/volume3`

    Szenario 2: `nconnect` wird nicht von der ersten Einbindung verwendet. Daher verwenden keine Einbindungen über denselben Endpunkt `nconnect`, obwohl `nconnect` dafür angeben sein kann.

    * `mount 10.10.10.10:/volume1 /mnt/volume1`
    * `mount 10.10.10.10:/volume2 /mnt/volume2 -o nconnect=8`
    * `mount 10.10.10.10:/volume3 /mnt/volume3 -o nconnect=8`

    Szenario 3: Einstellungen für `nconnect` werden nicht über separate Speicherendpunkte weitergegeben.  `nconnect` wird von der Einbindung verwendet, die von `10.10.10.10` stammt, aber nicht von der von `10.12.12.12` stammenden Einbindung.

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.12.12.12:/volume2 /mnt/volume2`

* `nconnect` kann verwendet werden, um Speicherparallelität von einem beliebigen Client zu erhöhen. 

Weitere Informationen finden Sie unter [Bewährte Methoden für Linux-Parallelität für Azure NetApp Files](performance-linux-concurrency-session-slots.md).

## <a name="rsize-and-wsize"></a>`Rsize` und `Wsize`
 
Die Flags `rsize` und `wsize` legen die maximale Übertragungsgröße eines NFS-Vorgangs fest.  Wenn `rsize` oder `wsize` beim Einbinden nicht angegeben werden, handeln Client und Server die höchste von beiden unterstützte Größe aus.   Derzeit unterstützen sowohl Azure NetApp Files als auch moderne Linux-Distributionen Lese- und Schreibgrößen von bis zu 1.048.576 Bytes (1 MiB).   Zur Optimierung von Gesamtdurchsatz und Latenz empfiehlt Azure NetApp Files jedoch, sowohl `rsize` als auch `wsize` auf nicht mehr als 262.144 Bytes (256 KiB) festzulegen. Sie werden feststellen, dass sowohl Latenz als auch Durchsatz sinken, wenn `rsize` und `wsize` auf mehr als 256 KiB festgelegt sind. 

Unter [Bereitstellen eines Systems für horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mithilfe von Azure NetApp Files unter SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md#mount-the-azure-netapp-files-volumes) wird beispielsweise der Höchstwert von 256 KiB für `rsize` und `wsize` wie folgt gezeigt:

```
sudo vi /etc/fstab
# Add the following entries
10.23.1.5:/HN1-data-mnt00001 /hana/data/HN1/mnt00001  nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-data-mnt00002 /hana/data/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-log-mnt00001 /hana/log/HN1/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
```
 
Außerdem wird für SAS Viya beispielsweise eine Lese- und Schreibgröße von 256 KiB empfohlen. [SAS GRID](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/606973/highlight/true#M17740) beschränkt `r/wsize` auf 64 KiB, während die Leseleistung durch Erhöhen von Read-Ahead für die NFS-Einbindungen verbessert wird. Ausführliche Informationen finden Sie unter [Bewährte Methoden für NFS-Read-Ahead unter Linux für Azure NetApp Files](performance-linux-nfs-read-ahead.md).

Die folgenden Überlegungen gelten für die Verwendung von `rsize` und `wsize`:

* Zufällige E/A-Vorgangsgrößen sind häufig kleiner als die Einbindungsoptionen `rsize` und `wsize`. Sie werden also in der Tat dadurch nicht eingeschränkt.
* Bei Verwendung des Dateisystemcaches erfolgen sequenzielle E/A-Vorgänge mit der durch die Einbindungsoptionen `rsize` und `wsize` vorgegebenen Größe, es sei denn, die Dateigröße ist kleiner als `rsize` und `wsize`.
* Vorgänge, die den Dateisystemcache umgehen, werden, obwohl sie immer noch durch die Einbindungsoptionen `rsize` und `wsize` eingeschränkt sind, nicht unbedingt so groß ausfallen wie der durch `rsize` oder `wsize` angegebene Höchstwert.  Dieser Aspekt ist wichtig, wenn Sie Workload-Generatoren einsetzen, die die Option `directio` aufweisen.

*Als bewährte Methode für Azure NetApp Files sollten Sie zur Optimierung von Gesamtdurchsatz und Latenz `rsize` und `wsize` nicht größer als auf 262.144 Bytes festlegen.*

## <a name="close-to-open-consistency-and-cache-attribute-timers"></a>Close-to-Open-Konsistenz und Cacheattributtimer   

NFS verwendet ein loses Konsistenzmodell. Die Konsistenz ist lose, da die Anwendung nicht jedes Mal auf den gemeinsamen Speicher zugreifen und Daten abrufen muss, um sie zu verwenden – ein Szenario, das enorme Auswirkungen auf die Anwendungsleistung haben würde.  Es gibt zwei Mechanismen zum Verwalten dieses Prozesses: Cacheattributtimer und Close-to-Open-Konsistenz.

*Wenn der Client im vollständigen Besitz der Daten ist, d. h. sie nicht von mehreren Knoten oder Systemen gemeinsam genutzt werden, ist Konsistenz gewährleistet.* In diesem Fall können Sie die `getattr`-Zugriffsvorgänge auf den Speicher reduzieren und die Anwendung beschleunigen, indem Sie Close-to-Open-Konsistenz (`cto`) deaktivieren (`nocto` als Einbindungsoption) und die Timeouts für die Verwaltung des Attributcaches erhöhen (`actimeo=600` als Einbindungsoption ändert den Zeitgeber in 10 Minuten gegenüber den Standardeinstellungen `acregmin=3,acregmax=30,acdirmin=30,acdirmax=60`). Bei einigen Tests reduziert `nocto` etwa 65-70 % der Zugriffsaufrufe für `getattr`, und die Anpassung von `actimeo` reduziert diese Aufrufe um weitere 20-25 %.

### <a name="how-attribute-cache-timers-work"></a>Funktionsweise von Attributcachetimern  

Die Attribute `acregmin`, `acregmax`, `acdirmin` und `acdirmax` steuern die Kohärenz des Caches. Die beiden vorherigen Attribute steuern, wie lange die Attribute von Dateien als vertrauenswürdig eingestuft werden. Die beiden letztgenannten Attribute steuern, wie lange die Attribute der Verzeichnisdatei selbst vertrauenswürdig sind (Größe, Besitz und Berechtigungen von Verzeichnissen).  Die Attribute `min` und `max` definieren die Mindest- bzw. Höchstdauer, für die Attribute eines Verzeichnisses bzw. einer Datei und der Cacheinhalt einer Datei als vertrauenswürdig eingestuft werden. Zwischen `min` und `max` wird ein Algorithmus verwendet, um die Zeitspanne zu definieren, für die einem Eintrag im Cache vertraut wird.

Betrachten Sie zum Beispiel die Standardwerte für `acregmin` und `acregmax`, 3 bzw. 30 Sekunden.  Die Attribute werden z. B. für die Dateien in einem Verzeichnis wiederholt ausgewertet.  Nach 3 Sekunden wird der NFS-Dienst auf Aktualität abgefragt.  Wenn die Attribute als gültig erachtet werden, verdoppelt der Client die vertrauenswürdige Zeitspanne auf 6 Sekunden, 12 Sekunden, 24 Sekunden und übernimmt dann den Höchstwert 30 Sekunden.  Von diesem Zeitpunkt an, bis die zwischengespeicherten Attribute als veraltet gelten (dann beginnt der Zyklus von vorne), ist die Zeitspanne der Vertrauenswürdigkeit auf 30 Sekunden festgelegt und entspricht dem von `acregmax` angegebenen Wert.

Es gibt andere Fälle, die von ähnlichen Einbindungsoptionen profitieren können, auch wenn die Clients nicht den vollständigen Besitz haben, z. B. wenn die Clients die Daten schreibgeschützt nutzen und die Datenaktualisierung über einen anderen Pfad verwaltet wird.  Bei Anwendungen, die Raster mit Clients verwenden, wie z  B. EDA, Webhosting und Filmrendering, und relativ statische Datasets haben (EDA-Tools oder -Bibliotheken, Webinhalte, Texturdaten), ist das typische Verhalten, dass der Datensatz weitgehend auf den Clients zwischengespeichert wird. Es gibt nur sehr wenige Lese- und keine Schreibvorgänge. Zahlreiche `getattr`-/Zugriffsaufrufe kehren zum Speicher zurück.  Diese Datasets werden in der Regel durch einen anderen Client aktualisiert, der die Dateisysteme einbindet und Inhaltsupdates in regelmäßigen Abständen per Push überträgt.

In diesen Fällen gibt es eine bekannte Verzögerung bei der Erfassung neuer Inhalte, weshalb die Anwendung möglicherweise mit veralteten Daten arbeitet.  In diesen Fällen können `nocto` und `actimeo` angegeben werden, um die Zeitspanne zu steuern, in der veraltete Daten verwaltet werden können.  Bei EDA-Tools und -Bibliotheken beispielsweise funktioniert `actimeo=600` gut, da diese Daten in der Regel nur selten aktualisiert werden.  Für das Hosting kleiner Websites, bei dem Datenaktualisierungen auf Clients zeitgerecht angezeigt werden müssen, während sie ihre Websites bearbeiten, dürfte `actimeo=10` akzeptabel sein. Für große Websites, deren Inhalte per Push in mehrere Dateisysteme übertragen werden, kann `actimeo=60` akzeptabel sein.

Bei diesen Einbindungsoptionen wird die Workload für den Speicher in diesen Fällen erheblich reduziert. (Beispielsweise wurde bei einem kürzlich durchgeführte EDA-Test das IOPS-Volumen des Tools von über 150.000 auf ca. 6.000 reduziert.) Anwendungen können erheblich schneller ausgeführt werden, da sie den Daten im Arbeitsspeicher vertrauen können. (Die Zugriffszeit auf den Arbeitsspeicher beträgt Nanosekunden im Vergleich zu Hunderten von Mikrosekunden für `getattr`/Zugriff in einem schnellen Netzwerk.)

### <a name="close-to-open-consistency"></a>Close-to-Open-Konsistenz 

Close-to-Open-Konsistenz (die Einbindungsoption `cto`) stellt sicher, dass unabhängig vom Zustand des Caches beim Öffnen der Anwendung stets die neuesten Daten für eine Datei präsentiert werden.  

* Wenn ein Verzeichnis durchforstet wird (z. B. `ls`, `ls -l`), erfolgt ein bestimmter Satz von PRC-Aufrufen.  
    Der NFS-Server gibt seine Sicht auf das Dateisystem frei. Solange `cto` von allen NFS-Clients verwendet wird, die auf einen bestimmten NFS-Export zugreifen, sehen alle Clients dieselbe Liste der darin enthaltenen Dateien und Verzeichnisse.  Die Aktualität der Attribute der Dateien im Verzeichnis wird durch die [Attributcachetimer](#how-attribute-cache-timers-work) gesteuert.  Das heißt, solange `cto` verwendet wird, werden Dateien für Remoteclients angezeigt, sobald die Datei erstellt und im Speicher abgelegt wurde.
* Wenn eine Datei geöffnet wird, ist der Inhalt der Datei aus Sicht des NFS-Servers garantiert aktuell.  
    Wenn eine Racebedingung vorliegt, bei der der Inhalt von Computer 1 noch nicht vollständig geleert wurde, sobald eine Datei auf Computer 2 geöffnet wird, erhält Computer 2 nur die Daten, die zum Zeitpunkt des Öffnens auf dem Server vorhanden sind. In diesem Fall ruft Computer 2 keine weiteren Daten aus der Datei ab, bis der Zeitgeber `acreg` erreicht ist und Computer 2 die Kohärenz seines Caches erneut mit dem Server abgleicht.  Dieses Szenario kann auf Computer 2 bei Angabe von `-f` am Ende beobachtet werden, wenn auf Computer 1 weiter in die Datei geschrieben wird.

### <a name="no-close-to-open-consistency"></a>Keine Close-to-Open-Konsistenz  

Ohne Close-to-Open-Konsistenz (`nocto`) vertraut der Client auf die Aktualität seiner aktuellen Sicht auf Datei und Verzeichnis, bis die Cacheattributtimer überschritten worden sind.  

* Wenn ein Verzeichnis durchforstet wird (z. B. `ls`, `ls -l`), erfolgt ein bestimmter Satz von PRC-Aufrufen.  
    Der Client ruft den Server nur dann für eine aktuelle Auflistung der Dateien auf, wenn der Zeitgeberwert `acdir` des Caches überschritten wurde.  In diesem Fall werden kürzlich erstellte Dateien und Verzeichnisse nicht angezeigt und kürzlich entfernte Dateien und Verzeichnisse weiterhin angezeigt.  

* Beim Öffnen einer Datei wird, solange sich die Datei noch im Cache befindet, ihr zwischengespeicherter Inhalt (falls vorhanden) zurückgegeben, ohne auf Konsistenz mit dem NFS-Server zu prüfen.

## <a name="next-steps"></a>Nächste Schritte  

* [Bewährte Methoden in Bezug auf direkte E/A unter Linux für Azure NetApp Files](performance-linux-direct-io.md)
* [Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Bewährte Methoden für Linux-Parallelität für Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Bewährte Methoden für NFS-Read-Ahead unter Linux](performance-linux-nfs-read-ahead.md)
* [Bewährte Methoden für SKUs für virtuelle Azure-Computer](performance-virtual-machine-sku.md) 
* [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md) 
