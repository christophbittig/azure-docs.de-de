---
title: Aufheben der Bereitstellung des Serverendpunkts der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Leitfaden zum Aufheben der Bereitstellung des Serverendpunkts der Azure-Dateisynchronisierung basierend auf Ihrem Anwendungsfall
author: mtalasila
ms.service: storage
ms.topic: how-to
ms.date: 6/01/2021
ms.author: mtalasila
ms.subservice: files
ms.openlocfilehash: 597a3dfce5d647359b23e732f74dc6949078c000
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798956"
---
# <a name="deprovision-your-azure-file-sync-server-endpoint"></a>Aufheben der Bereitstellung des Serverendpunkts der Azure-Dateisynchronisierung

Beim Entfernen eines Serverendpunkts wird die Synchronisierung zum und vom betreffenden Serverstandort mit dem Cloudendpunkt (Azure-Dateifreigabe) in der gleichen Synchronisierungsgruppe beendet. Vor dem Aufheben der Bereitstellung des Serverendpunkts müssen einige Schritte ausgeführt werden, um die Datenintegrität und -verfügbarkeit aufrechtzuerhalten. In diesem Artikel werden verschiedene Methoden zur Bereitstellungsaufhebung und die entsprechenden Schritte nach Szenario geordnet beschrieben. Führen Sie die Schritte für den Anwendungsfall aus, der am ehesten zutrifft.

Wenn die derzeit synchronisierten Daten dauerhaft verloren gehen dürfen, können Sie die Bereitstellung des Serverendpunkts direkt aufheben.

> [!Warning]
> Versuchen Sie nicht, Synchronisierungsprobleme zu beheben, indem Sie die Bereitstellung eines Serverendpunkts aufheben. Hilfe zur Problembehandlung finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung](./file-sync-troubleshoot.md). Daten können dauerhaft verloren gehen, wenn Sie den Serverendpunkt löschen, ohne dass Server- und Cloudendpunkt vollständig miteinander synchronisiert sind. Das Entfernen eines Serverendpunkts ist ein destruktiver Vorgang. Mehrstufige Dateien auf dem Serverendpunkt werden nicht erneut mit ihren Speicherorten in der Azure-Dateifreigabe verbunden, nachdem der Serverendpunkt neu erstellt wurde. Dies führt zu Synchronisierungsfehlern. Außerdem können mehrstufige Dateien, die sich außerhalb des Namespaces des Serverendpunkts befinden, dauerhaft verloren gehen können. Auf Ihrem Serverendpunkt sind möglicherweise auch dann mehrstufige Dateien vorhanden, wenn das Cloudtiering nie aktiviert wurde.

## <a name="scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-server--vm"></a>Szenario 1: Sie möchten Ihren Serverendpunkt löschen und die Verwendung Ihres lokalen Servers/virtuellen Computers beenden

In diesem Szenario muss sichergestellt werden, dass die Daten am Cloudendpunkt auf dem neuesten Stand sind. Wenn Sie stattdessen sämtliche Dateien am Serverendpunkt auf dem neuesten Stand halten möchten, sehen Sie sich [Szenario 2: Sie möchten den Serverendpunkt löschen und die Verwendung dieser spezifischen Azure-Dateifreigabe beenden](#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share) an.

Einige Anwendungsfälle, die in diese Kategorie fallen:
-   Migrieren zu einer Azure-Dateifreigabe
-   Einrichten einer serverlosen Umgebung
-   Einstellung eines bestimmten Serverendpunktpfads mit gleichzeitiger Beibehaltung der übrigen Synchronisierungsgruppe

In diesem Szenario müssen vor dem Löschen des Serverendpunkts drei Schritte ausgeführt werden: Entfernen des Benutzerzugriffs, Initiieren einer speziellen VSS-Uploadsitzung und Warten auf den Abschluss der letzten Synchronisierungssitzung.

### <a name="remove-user-access-to-your-server-endpoint"></a>Entfernen des Benutzerzugriffs auf den Serverendpunkt

Vor dem Aufheben der Bereitstellung des Serverendpunkts müssen Sie sicherstellen, dass alle Änderungen vom Server mit der Cloud synchronisiert werden können. Im ersten Schritt zum Synchronisieren der Cloud werden weitere Änderungen an den Dateien und Ordnern am Serverendpunkt unterbunden. 

Das Entfernen des Zugriffs führt zu Ausfallzeiten. Um Ausfallzeiten zu reduzieren, kann der Benutzerzugriff auch an Ihren Cloudendpunkt umgeleitet werden. 

Notieren Sie sich Datum und Uhrzeit der Aufhebung des Benutzerzugriffs, und fahren Sie mit dem nächsten Abschnitt fort.

### <a name="initiate-a-special-volume-snapshot-service-vss-upload-session"></a>Initiieren einer speziellen VSS-Uploadsitzung (Volume Snapshot Service)

Mit der Azure-Dateisynchronisierung wird einmal pro Tag eine temporäre VSS-Momentaufnahme auf dem Server erstellt, um Dateien mit offenen Handles zu synchronisieren. Initiieren Sie eine spezielle Sitzung für den VSS-Upload, um sicherzustellen, dass die neuesten Daten in der letzten Synchronisierungssitzung hochgeladen werden, und um die Fehler pro Element zu reduzieren. Dadurch wird auch eine spezielle Synchronisierungsuploadsitzung ausgelöst, die gestartet wird, nachdem die Momentaufnahme erstellt wurde.  

Öffnen Sie dazu **Taskplaner** auf dem lokalen Server, navigieren Sie zu **Microsoft\StorageSync**, klicken Sie mit der rechten Maustaste auf den Task `VssSyncScheduledTask`, und wählen Sie **Ausführen** aus.

> [!Important]
> Notieren Sie sich Datum und Uhrzeit des Abschlusses dieses Schritts. Sie benötigen sie im nächsten Abschnitt.

![Screenshot der Planung einer VSS-Uploadsitzung](media/file-sync-server-endpoint-delete/vss-task-scheduler.png)

### <a name="wait-for-a-final-sync-upload-session-to-complete"></a>Warten auf den Abschluss der letzten Synchronisierungsuploadsitzung

Damit sichergestellt ist, dass sich die neuesten Daten in der Cloud befinden, müssen Sie warten, bis die letzte Synchronisierungsuploadsitzung abgeschlossen ist. 

Um den Status der Synchronisierungssitzung zu überprüfen, öffnen Sie die **Ereignisanzeige** auf dem lokalen Server. Navigieren Sie zum Telemetrieereignisprotokoll (**Anwendungen und Dienste\Microsoft\FileSync\Agent**). Stellen Sie sicher, dass ein 9102-Ereignis mit „sync direction“ = upload, „HResult“ = 0 und „PerItemErrorCount“ = 0 angezeigt wird, das eingetreten ist, nachdem Sie manuell eine VSS-Uploadsitzung initiiert haben.

![Screenshot der Überprüfung, ob die letzte Synchronisierungssitzung abgeschlossen wurde](media/file-sync-server-endpoint-delete/event-viewer.png)

Wenn „PerItemErrorCount“ größer als 0 ist, konnten einige Dateien nicht synchronisiert werden. Verwenden Sie  **FileSyncErrorsReport.ps1**, um die Dateien anzuzeigen, die nicht synchronisiert wurden. Dieses PowerShell-Skript befindet sich normalerweise unter dem folgenden Pfad auf einem Server, auf dem ein Azure-Dateisynchronisierungs-Agent installiert ist: **C:\Programme\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1**.

Wenn diese Dateien nicht wichtig sind, können Sie den Serverendpunkt löschen. Wenn die Dateien wichtig sind, beheben Sie die Fehler, und warten Sie, bis ein weiteres 9102-Ereignis mit „sync direction“ = upload, „HResult“ = 0 und „PerItemErrorCount“ = 0 angezeigt wird, bevor Sie den Serverendpunkt löschen.

## <a name="scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share"></a>Szenario 2: Sie möchten den Serverendpunkt löschen und die Verwendung dieser spezifischen Azure-Dateifreigabe beenden

In diesem Szenario muss sichergestellt werden, dass die Daten auf dem lokalen Server oder der lokalen VM auf dem neuesten Stand sind. Wenn Sie stattdessen den vollständigen Satz von Dateien am Cloudendpunkt auf dem neuesten Stand halten möchten, sehen Sie sich das [Szenario 1: Sie möchten Ihren Serverendpunkt löschen und die Verwendung Ihres lokalen Servers/virtuellen Computers beenden](#scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-server--vm) an.

In diesem Szenario müssen vor dem Löschen des Serverendpunkts vier Schritte ausgeführt werden: Deaktivieren des Cloudtierings, Abrufen mehrstufiger Dateien, Initiieren der Änderungserkennung in der Cloud und Warten auf den Abschluss der letzten Synchronisierungssitzung.

### <a name="disable-cloud-tiering"></a>Deaktivieren des Cloudtierings
Navigieren Sie zum Abschnitt „Cloudtiering“ unter **Eigenschaften des Serverendpunkts** für den Serverendpunkt, für den die Bereitstellung aufgehoben und das Cloudtiering deaktiviert werden soll.

### <a name="recall-all-tiered-files"></a>Abrufen aller mehrstufigen Dateien
Auch wenn das Cloudtiering deaktiviert ist, müssen Sie alle mehrstufigen Dateien abrufen, um sicherzustellen, dass jede Datei lokal gespeichert wird.

Vergewissern Sie sich vor dem Abrufen der Dateien, ob lokal genügend freier Speicherplatz verfügbar ist, damit alle Dateien gespeichert werden können. Der freie Speicherplatz muss ungefähr der Größe der Azure-Dateifreigabe in der Cloud abzüglich der zwischengespeicherten Größe auf dem Server entsprechen.

Verwenden Sie das PowerShell-Cmdlet **Invoke-StorageSyncFileRecall**, und geben Sie den Parameter **SyncGroupName** an, um alle Dateien abzurufen. 
```powershell
Invoke-StorageSyncFileRecall  -SyncGroupName “samplesyncgroupname”
```
Wenn die Ausführung des Cmdlets abgeschlossen ist, können Sie mit dem nächsten Abschnitt fortfahren.

### <a name="initiate-cloud-change-detection"></a>Initiieren der Änderungserkennung in der Cloud
Durch die Initiierung der Änderungserkennung in der Cloud wird sichergestellt, dass die neuesten Änderungen synchronisiert wurden.

Die Änderungserkennung können Sie mit dem Cmdlet „Invoke-AzStorageSyncChangeDetection“ initiieren: 

```powershell
Invoke-AzStorageSyncChangeDetection -ResourceGroupName "myResourceGroup" -StorageSyncServiceName "myStorageSyncServiceName" -SyncGroupName "mySyncGroupName" -Path "Data","Reporting\Templates" 
```

Dieser Schritt kann eine Weile dauern. 

> [!Important]
> Nachdem diese initiierte Überprüfung der Änderungserkennung in der Cloud abgeschlossen wurde, notieren Sie sich Datum und Uhrzeit des Abschlusses. Sie benötigen diese Angaben im nächsten Abschnitt.

### <a name="wait-for-a-final-sync-session-to-complete"></a>Warten auf den Abschluss der letzten Synchronisierungssitzung
Um sicherzustellen, dass die Daten auf dem lokalen Server auf dem neuesten Stand sind, müssen Sie auf den Abschluss der letzten Synchronisierungsuploadsitzung warten. 

Wechseln Sie dazu auf dem lokalen Server zur **Ereignisanzeige**. Navigieren Sie zum Telemetrieereignisprotokoll (**Anwendungen und Dienste\Microsoft\FileSync\Agent**). Stellen Sie sicher, dass ein 9102-Ereignis mit „sync direction“ = download, „HResult“ = 0 und „PerItemErrorCount“ = 0 angezeigt wird, das nach der abgeschlossenen Überprüfung der Änderungserkennung in der Cloud (Datum/Uhrzeit) eingetreten ist.

![Screenshot der Überprüfung, ob die letzte Synchronisierungssitzung abgeschlossen wurde](media/file-sync-server-endpoint-delete/event-viewer.png)

Wenn „PerItemErrorCount“ größer als 0 ist, konnten einige Dateien nicht synchronisiert werden. Verwenden Sie  **FileSyncErrorsReport.ps1**, um die Dateien anzuzeigen, die nicht synchronisiert wurden. Dieses PowerShell-Skript befindet sich normalerweise unter dem folgenden Pfad auf einem Server, auf dem ein Azure-Dateisynchronisierungs-Agent installiert ist: **C:\Programme\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1**.

Wenn diese Dateien nicht wichtig sind, können Sie den Serverendpunkt löschen. Wenn die Dateien wichtig sind, beheben Sie die Fehler, und warten Sie, bis ein weiteres 9102-Ereignis mit „sync direction“ = download, „HResult“ = 0 und „PerItemErrorCount“ = 0 angezeigt wird, bevor Sie den Serverendpunkt löschen.

## <a name="next-steps"></a>Nächste Schritte
* [Ändern der Topologie der Azure-Dateisynchronisierung](./file-sync-modify-sync-topology.md)
