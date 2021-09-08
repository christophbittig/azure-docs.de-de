---
title: Betriebssystemsicherung und -wiederherstellung für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Erfahren Sie, wie die Betriebssystemsicherung und -wiederherstellung für SAP HANA in Azure (große Instanzen) erfolgt.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5685f7932b49f8af57faf159a51a8cb634128337
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217483"
---
# <a name="os-backup-and-restore"></a>Betriebssystemsicherung und -wiederherstellung

In diesem Artikel werden die Schritte zum Sichern und Wiederherstellen eines Betriebssystems auf Dateiebene beschrieben. Das Verfahren unterscheidet sich je nach Parametern wie Typ I oder Typ II, Revision 3 oder höher, Speicherort usw. Erkundigen Sie sich beim Microsoft Operations-Team nach den Werten dieser Parameter für Ihre Ressourcen.

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Sichern und Wiederherstellen des Betriebssystems für Typ-II-SKUs von Revision 3-Stamps

In den nachstehenden Informationen werden die Schritte zum Durchführen einer Betriebssystemsicherung und -wiederherstellung auf Dateiebene für **Typ-II-SKUs** von SAP HANA (große Instanzen) von Revision 3 beschrieben.

>[!Important]
> **Dieser Artikel gilt nicht für Typ-II-SKU-Bereitstellungen in Stamps von SAP HANA (große Instanzen) von Revision 4.** Start-LUNS von SAP HANA (große Instanzen) des Typs II, die in Stamps von Revision 4 von SAP HANA (große Instanzen) bereitgestellt werden, können mit Speichermomentaufnahmen gesichert werden, wie dies bei Typ-I-SKUs bereits in Revision 3-Stamps der Fall ist.


>[!NOTE]
>In den Skripts zum Sichern des Betriebssystems wird die auf dem Server vorinstallierte ReaR-Software verwendet.  

Nach Abschluss der Bereitstellung durch das Microsoft Service Management-Team ist der Server standardmäßig mit zwei geplanten Sicherungen zum Sichern des gesamten Betriebssystems konfiguriert. Die Zeitpläne der Sicherungen können mithilfe des folgenden Befehls geprüft werden:

```
#crontab –l
```
Sie können den Sicherungszeitplan jederzeit mithilfe des folgenden Befehls ändern:
```
#crontab -e
```
### <a name="take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Die Sicherung des Betriebssystem-Dateisystems wird bereits mithilfe eines **cron-Auftrags** geplant. Sie können die Sicherung des Betriebssystems auf Dateisystemebene jedoch auch manuell ausführen. Führen Sie für eine manuelle Sicherung den folgenden Befehl aus:

```
#rear -v mkbackup
```
Auf dem folgenden Bildschirm ist eine beispielhafte manuelle Sicherung dargestellt:

![Vorgehensweise](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="restore-a-backup"></a>Wiederherstellen einer Sicherung

Sie können eine vollständige Sicherung oder eine einzelne Datei aus einer Sicherung wiederherstellen. Führen Sie zum Wiederherstellen den folgenden Befehl aus:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Nach der Wiederherstellung wird die Datei im aktuellen Arbeitsverzeichnis wiederhergestellt.

Der folgende Befehl zeigt die Wiederherstellung der Datei */etc/fstab* aus der Sicherungsdatei *backup.tar.gz*:
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Sie müssen die Datei an den gewünschten Speicherort kopieren, nachdem sie aus der Sicherung wiederhergestellt wurde.

Der folgende Screenshot zeigt die Wiederherstellung einer vollständigen Sicherung.

![Screenshot mit einem Eingabeaufforderungsfenster, das die Wiederherstellung zeigt](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="install-the-rear-tool-and-change-the-configuration"></a>Installieren des ReaR-Tools und Ändern der Konfiguration 

Die Relax-and-Recover-Pakete (ReaR) sind in den **Typ-II-SKUs** von SAP HANA (große Instanzen) **vorinstalliert**. Sie müssen hierfür keine Maßnahmen ergreifen. Sie können direkt mit der Verwendung des ReaR-Tools zum Sichern des Betriebssystems beginnen.

In Situationen, in denen Sie die Pakete selbst installieren müssen, können Sie das ReaR-Tool über die folgenden Schritte installieren und konfigurieren.

Verwenden Sie die folgenden Befehle, um die **ReaR**-Sicherungspakete zu installieren:

Verwenden Sie für das Betriebssystem **SLES** den folgenden Befehl:
```
#zypper install <rear rpm package>
```
Verwenden Sie für das Betriebssystem **RHEL** den folgenden Befehl: 

```
#yum install rear -y
```
Um das ReaR-Tool zu konfigurieren, müssen Sie die Parameter **OUTPUT_URL** und **BACKUP_URL** in der Datei */etc/rear/local.conf* aktualisieren.

```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Der folgende Screenshot zeigt die Wiederherstellung einer vollständigen Sicherung: ![Screenshot mit dem Eingabeaufforderungsfenster, das die Wiederherstellung mithilfe des ReaR-Tools zeigt](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)


## <a name="os-backup-and-restore-for-all-other-skus"></a>Betriebssystemsicherung und -wiederherstellung für alle anderen SKUs

In den nachstehenden Informationen werden die Schritte zum Durchführen einer Betriebssystemsicherung und -wiederherstellung auf Dateiebene für alle SKUs aller Revisionen mit Ausnahme von **Typ-II-SKUs** von SAP HANA (große Instanzen) von Revision 3 beschrieben.

### <a name="take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Holen Sie sich die neuesten Microsoft-Momentaufnahmetools für SAP HANA, wie in einer Reihe von Artikeln erklärt, beginnend mit [Tool für konsistente Momentaufnahmen in Azure-Anwendungen?](../../../azure-netapp-files/azacsnap-introduction.md), und konfigurieren und testen Sie sie wie in diesen Artikeln beschrieben:

- [Konfigurieren des Tools für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Testen des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-test.md) 

Für eine regelmäßige Ausführung über `crontab`, wie unter [Sichern mithilfe des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md) beschrieben. 

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Installieren des Tools für konsistente Momentaufnahmen](../../../azure-netapp-files/azacsnap-installation.md)
- [Konfigurieren des Tools für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Testen des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Sichern mithilfe des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Abrufen von Details mithilfe des Tools für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Löschen mit dem Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Durchführen der Wiederherstellung mit dem Tool für konsistente Momentaufnahmen in Azure-Anwendungen](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Notfallwiederherstellung mithilfe des Tools für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Problembehandlung für das Tool für anwendungskonsistente Momentaufnahmen in Azure](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Tipps und Tricks zur Verwendung des Tools für konsistente Momentaufnahmen in Azure-Anwendungen für Azure NetApp Files](../../../azure-netapp-files/azacsnap-tips.md)


### <a name="restore-a-backup"></a>Wiederherstellen einer Sicherung

Der Wiederherstellungsvorgang kann nicht vom Betriebssystem selbst ausgeführt werden. Sie müssen ein Supportticket beim Microsoft Operations-Team erstellen. Für den Wiederherstellungsvorgang muss sich die HLI-Instanz (HANA Large Instance [große Instanz]) im ausgeschalteten Zustand befinden. Planen Sie dies entsprechend ein.

### <a name="managed-os-snapshots"></a>Verwaltete Momentaufnahmen des Betriebssystems

Azure kann automatisch Betriebssystemsicherungen für Ihre HLI-Ressourcen erstellen. Diese Sicherungen werden einmal täglich erstellt. Azure behält jeweils die letzten drei dieser Sicherungen bei. Diese Sicherungen sind standardmäßig für alle Kunden in den folgenden Regionen aktiviert:
- USA (Westen)
- Australien (Osten)
- Australien, Südosten
- USA Süd Mitte
- USA (Ost) 2

Diese Einrichtung ist in den folgenden Regionen teilweise verfügbar:
- East US
- Nordeuropa
- Europa, Westen

Die Häufigkeit oder Aufbewahrungsdauer der von dieser Einrichtung durchgeführten Sicherungen kann nicht geändert werden. Wenn Sie für Ihre HLI-Ressourcen eine andere Strategie für die Sicherung des Betriebssystems benötigen, können Sie diese Funktion deaktivieren, indem Sie ein Supportticket beim Microsoft Operations-Team erstellen. Konfigurieren Sie dann die Microsoft-Momentaufnahmetools für SAP HANA, um Betriebssystemsicherungen zu erstellen. Befolgen Sie dazu die Anweisungen weiter oben im Abschnitt [Erstellen einer manuellen Sicherung](#take-a-manual-backup).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie kdump für SAP HANA (große Instanzen) aktiviert wird.

> [!div class="nextstepaction"]
> [kdump für SAP HANA in Azure (große Instanzen)](hana-large-instance-enable-kdump.md)
