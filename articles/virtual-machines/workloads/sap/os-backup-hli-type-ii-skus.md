---
title: Betriebssystemsicherung und -wiederherstellung von SAP HANA unter Azure-SKUs (große Instanzen) des Typs II | Microsoft-Dokumentation
description: Durchführen von Betriebssystemsicherungen und -wiederherstellungen für SAP HANA unter Azure-SKUs (große Instanzen) des Typs II
services: virtual-machines-linux
documentationcenter: ''
author: jaawasth
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: jaawasth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 245c371a002ebfdad4dd0765ba3d283c81c86f03
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631610"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Sichern und Wiederherstellen des Betriebssystems für Typ-II-SKUs von Revision 3-Stamps

Dieses Dokument beschreibt die Schritte zum Durchführen einer Betriebssystemsicherung und -wiederherstellung auf Dateiebene für die **Typ-II-SKUs** von SAP HANA (große Instanzen) von Revision 3. 

>[!Important]
> **Dieser Artikel gilt nicht für Typ-II-SKU-Bereitstellungen in Stamps von SAP HANA (große Instanzen) von Revision 4.** Start-LUNS von Einheiten von SAP HANA (große Instanzen) des Typs II, die in Stamps von Revision 4 von SAP HANA (große Instanzen) eingesetzt werden, können mit Speichermomentaufnahmen gesichert werden, wie dies bei Typ-I-SKUs bereits in Revision 3-Stamps der Fall ist.


>[!NOTE]
> * Die Sicherungsskripts des Betriebssystems verwenden das Hilfsprogramm „xfsdump“.  
> * Dieses Dokument unterstützt die vollständige Sicherung des Stammdateisystems und **keine inkrementellen** Sicherungen.
> * Stellen Sie sicher, dass beim Erstellen einer Sicherung keine Dateien in dasselbe System geschrieben werden.  Andernfalls sind Dateien, die während der Sicherung geschrieben werden, möglicherweise nicht in der Sicherung enthalten.
> * Die ReaR-Sicherung ist für Typ-II-SKUs der großen HANA-Instanzen von Revision 3 veraltet.
> * Wir haben diese Prozedur intern mit mehreren Szenarien der Betriebssystembeschädigung getestet. Da Sie als Kunde jedoch die alleinige Verantwortung für das Betriebssystem tragen, empfehlen wir Ihnen, es gründlich zu testen, bevor Sie sich für Ihr Szenario auf diese Dokumentation verlassen.
> * Wir haben diesen Prozess unter dem SLES-Betriebssystem getestet.
> * Upgrades von Hauptversionen wie SLES 12.x auf SLES 15x werden nicht unterstützt.
> * Für eine Betriebssystemwiederherstellung mit diesem Prozess benötigen Sie die Unterstützung von Microsoft, da für die Wiederherstellung der Konsolenzugriff erforderlich ist. Erstellen Sie ein Supportticket bei Microsoft, um Unterstützung bei der Wiederherstellung zu erhalten.


## <a name="how-to-take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Durchführen einer manuellen Sicherung:

1. Installieren Sie das Sicherungstool.
   ```
   zypper in xfsdump
   ```

2. Erstellen Sie eine vollständige Sicherung. 
   ```
   xfsdump -l 0 -f /data1/xfs_dump /
   ```

   Auf dem folgenden Bildschirm ist eine beispielhafte manuelle Sicherung dargestellt:
   
    [![Sicherungsaufzeichnung](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png)](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png#lightbox)


3. Wichtig: Speichern Sie eine Kopie der Sicherung auch auf NFS-Volumes, falls die Partition „data1“ ebenfalls beschädigt wird.
   ```
   cp /data1/xfs_dump /osbackup/
   ```

4. Zum Ausschließen regulärer Verzeichnisse und Dateien von der Sicherungsdatei markieren Sie Dateien mit „chattr“.
   * chattr -R +d Verzeichnis
   * chattr +d Datei
   * „xfsdump“ mit Option „-e“ ausführen
   * Hinweis: Es ist nicht möglich, NFS-Dateisysteme [NTFS] auszuschließen.




## <a name="how-to-restore-a-backup"></a>Wiederherstellen einer Sicherung

>[!NOTE]
> * Dieser Schritt erfordert die Einbindung des Microsoft Operations-Teams.
> * Für eine Betriebssystemwiederherstellung mit diesem Prozess ist die Unterstützung von Microsoft nötig, da für die Wiederherstellung der Konsolenzugriff erforderlich ist. Erstellen Sie ein Supportticket bei Microsoft, um Unterstützung bei der Wiederherstellung zu erhalten.
> * Wir werden das vollständige Dateisystem wiederherstellen:

1. Binden Sie die Betriebssystem-ISO in das System ein.

2. Wechseln Sie in den Wiederherstellungsmodus.

3. Binden Sie die Partition „data1“ (oder das NFS-Volume, auf dem die Sicherungsdatei gespeichert ist) im Lese-/Schreibmodus ein.
   ```
   mount -o rw /dev/md126p4 /mnt1
   ```
4. Binden Sie das Stammverzeichnis im Lese-/Schreibmodus ein.
   ```
   mount -o rw /dev/md126p2 /mnt2
   ```
5. Stellen Sie das Dateisystem wieder her.
   ```
   xfsrestore -f /mnt1/xfs_dump /mnt2
   ```
   [![Screenshot der Wiederherstellung](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png)](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png#lightbox)
6. Starten Sie das System neu.
   ```
   reboot
   ```

Sollten nach der Überprüfung Fehler angezeigt werden, wenden Sie sich an den Betriebssystemanbieter und Microsoft, um Zugriff auf die Konsole zu erhalten.

## <a name="post-restore-check"></a>Überprüfung nach der Wiederherstellung

1. Stellen Sie sicher, dass für das System vollständige Attribute wiederhergestellt wurden.
   * Das Netzwerk funktioniert.
   * NFS-Volumes sind eingebunden.
2. Stellen Sie sicher, dass RAID konfiguriert ist. Ersetzen Sie die entsprechenden Angaben durch Ihr RAID-Gerät.
   ```
   mdadm -D /dev/md126
   ```
   [![RAID-Status](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png)](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png#lightbox)

3. Stellen Sie sicher, dass RAID-Datenträger synchronisiert werden und sich die Konfiguration in einem fehlerfreien Zustand befindet.
   * Die Synchronisierung von RAID-Datenträgern nimmt einige Zeit in Anspruch. Die Synchronisierung kann einige Minuten dauern, bevor sie zu 100 % abgeschlossen ist.

4. Starten Sie HANA DB, und vergewissern Sie sich, dass HANA wie erwartet ausgeführt wird.

5. Stellen Sie sicher, dass HANA angezeigt wird und keine Fehler auftreten.
   ```
   hdbinfo
   ```
   [![HANA-Status](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png)](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png#lightbox)

6. Sollten nach der Überprüfung Fehler angezeigt werden, wenden Sie sich an den Betriebssystemanbieter und Microsoft, um Zugriff auf die Konsole zu erhalten.