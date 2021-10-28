---
title: Problembehandlung der Sicherung von Azure-Dateifreigaben
description: Dieser Artikel enthält Informationen zum Behandeln von Problemen in Verbindung mit dem Schutz Ihrer Azure-Dateifreigaben.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 07b3f8c6fddef10132ac15f1dc3fe076d6d29218
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247757"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Behandeln von Problemen beim Sichern von Azure-Dateifreigaben

Dieser Artikel enthält Informationen zur Problembehandlung, mit denen Sie eventuelle Probleme beheben können, die Ihnen beim Konfigurieren der Sicherung oder beim Wiederherstellen von Azure-Dateifreigaben mit dem Azure Backup-Dienst begegnen können.

## <a name="common-configuration-issues"></a>Allgemeine Konfigurationsprobleme

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Ich konnte mein Speicherkonto zum Konfigurieren der Sicherung für die Azure-Dateifreigabe nicht finden

- Warten Sie, bis die Ermittlung abgeschlossen ist.
- Überprüfen Sie, ob eine Dateifreigabe unter dem Speicherkonto bereits durch einen anderen Recovery Services-Tresor geschützt ist.

  >[!NOTE]
  >Alle Dateifreigaben in einem Speicherkonto können nur unter einem einzelnen Recovery Services-Tresor geschützt werden. Mithilfe [dieses Skripts](scripts/backup-powershell-script-find-recovery-services-vault.md) können Sie nach dem Recovery Services-Tresor suchen, bei dem Ihr Speicherkonto registriert ist.

- Vergewissern Sie sich, dass sich die Dateifreigabe nicht in einem nicht unterstützten Speicherkonto befindet. Sie können die [Unterstützungsmatrix für Sicherungen von Azure-Dateifreigaben](azure-file-share-support-matrix.md) verwenden, um unterstützte Speicherkonten zu finden.
- Stellen Sie sicher, dass das Speicherkonto und der Recovery Services-Tresor in derselben Region vorhanden sind.
- Sorgen Sie dafür, dass die Kombination aus Speicherkontoname und Ressourcengruppenname eine Gesamtlänge von 84 Zeichen bei neuen Speicherkonten und 77 Zeichen bei klassischen Speicherkonten nicht überschreitet.
- Überprüfen Sie die Firewalleinstellungen des Speicherkontos, um sicherzustellen, dass die Ausnahme _Azure-Diensten in der Liste der vertrauenswürdigen Dienste den Zugriff auf dieses Speicherkonto erlauben_ gewährt wird. Unter [diesem](../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) Link finden Sie die Schritte zur Gewährung einer Ausnahme.


### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Im Portal tritt ein Fehler mit dem Hinweis auf, dass die Speicherkonten nicht erfolgreich erkannt wurden.

Wenn Sie über ein (CSP-fähiges) Partnerabonnement verfügen, ignorieren Sie den Fehler. Falls Ihr Abonnement nicht CSP-fähig ist und Ihre Speicherkonten nicht erkannt werden, wenden Sie sich an den Support.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Die Überprüfung oder Registrierung des ausgewählten Speicherkontos war nicht erfolgreich.

Wiederholen Sie die Registrierung. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Die Dateifreigaben im ausgewählten Speicherkonto konnten nicht aufgelistet werden oder wurden nicht gefunden.

- Vergewissern Sie sich, dass das Speicherkonto in der Ressourcengruppe vorhanden ist und seit der letzten Überprüfung oder Registrierung im Tresor nicht gelöscht oder verschoben wurde.
- Vergewissern Sie sich, dass die Dateifreigabe, die Sie schützen möchten, nicht gelöscht wurde.
- Vergewissern Sie sich, dass das Speicherkonto für die Sicherung von Dateifreigaben unterstützt wird. Sie können die [Unterstützungsmatrix für Sicherungen von Azure-Dateifreigaben](azure-file-share-support-matrix.md) verwenden, um unterstützte Speicherkonten zu finden.
- Überprüfen Sie, ob die Dateifreigabe bereits im gleichen Recovery Services-Tresor geschützt wird.
- Überprüfen Sie die Einstellung „Netzwerkrouting“ des Speicherkontos, um sicherzustellen, dass die Routingpräferenz auf „Microsoft-Netzwerkrouting“ festgelegt ist.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Bei der Konfiguration der Dateifreigabesicherung (oder der Schutzrichtlinie) tritt ein Fehler auf.

- Wiederholen Sie die Konfiguration, um zu prüfen, ob das Problem weiterhin besteht.
- Vergewissern Sie sich, dass die Dateifreigabe, die Sie schützen möchten, nicht gelöscht wurde.
- Wenn Sie mehrere Dateifreigaben auf einmal schützen möchten und für einige der Dateifreigaben ein Fehler auftritt, konfigurieren Sie die Sicherung für die Dateifreigaben, bei denen ein Fehler aufgetreten ist, erneut.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Nach dem Aufheben des Schutzes einer Dateifreigabe kann der Recovery Services-Tresor nicht gelöscht werden

Öffnen Sie im Azure-Portal Ihren **Tresor** und dann **Sicherungsinfrastruktur** > **Speicherkonten** aus. Wählen Sie **Registrierung aufheben** aus, um die Speicherkonten aus dem Recovery Services-Tresor zu entfernen.

>[!NOTE]
>Ein Recovery Services-Tresor kann nur nach dem Aufheben der Registrierung aller Speicherkonten gelöscht werden, die beim Tresor registriert wurden.

## <a name="common-backup-or-restore-errors"></a>Allgemeine Sicherungs- und Wiederherstellungsfehler

>[!NOTE]
>Stellen Sie mit [diesem Dokument](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) sicher, dass Sie über ausreichende Berechtigungen zum Ausführen von Sicherungs- oder Wiederherstellungsvorgängen verfügen.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound: Vorgangsfehler, weil die Dateifreigabe nicht gefunden wurde.

Fehlercode: FileShareNotFound

Fehlermeldung: Vorgangsfehler, weil die Dateifreigabe nicht gefunden wurde.

Vergewissern Sie sich, dass die Dateifreigabe, die Sie schützen möchten, nicht gelöscht wurde.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable: Das Speicherkonto wurde nicht gefunden oder wird nicht unterstützt.

Fehlercode: UserErrorFileShareEndpointUnreachable

Fehlermeldung: Das Speicherkonto wurde nicht gefunden oder wird nicht unterstützt.

- Vergewissern Sie sich, dass das Speicherkonto in der Ressourcengruppe vorhanden ist und seit der letzten Überprüfung nicht gelöscht oder aus der Ressourcengruppe entfernt wurde.

- Vergewissern Sie sich, dass das Speicherkonto für die Sicherung von Dateifreigaben unterstützt wird.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached: Sie haben die maximale Anzahl von Momentaufnahmen für diese Dateifreigabe erreicht. Sie können weitere erstellen, sobald ältere abgelaufen sind.

Fehlercode: AFSMaxSnapshotReached

Fehlermeldung: Sie haben die maximale Anzahl von Momentaufnahmen für diese Dateifreigabe erreicht. Sie können weitere erstellen, sobald ältere abgelaufen sind.

- Dieser Fehler kann auftreten, wenn Sie mehrere bedarfsgesteuerte Sicherungen für eine Dateifreigabe erstellen.
- Pro Dateifreigabe sind maximal 200 Momentaufnahmen zulässig – einschließlich der von Azure Backup erstellten Momentaufnahmen. Ältere geplante Sicherungen (oder Momentaufnahmen) werden automatisch bereinigt. Bedarfsgesteuerte Sicherungen (oder Momentaufnahmen) müssen gelöscht werden, wenn die maximal zulässige Anzahl erreicht wird.

Löschen Sie die bedarfsgesteuerten Sicherungen (Momentaufnahmen von Azure-Dateifreigaben) über das Azure Files-Portal.

>[!NOTE]
> Das Löschen von Momentaufnahmen, die mit Azure Backup erstellt wurden, führt zum Verlust der Wiederherstellungspunkte.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound: Vorgangsfehler, weil das angegebene Speicherkonto nicht mehr vorhanden ist.

Fehlercode: UserErrorStorageAccountNotFound

Fehlermeldung: Vorgangsfehler, weil das angegebene Speicherkonto nicht mehr vorhanden ist.

Vergewissern Sie sich, dass das Speicherkonto noch vorhanden ist und nicht gelöscht wurde.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound: Die angegebenen Speicherkontodetails sind falsch.

Fehlercode: UserErrorDTSStorageAccountNotFound

Fehlermeldung: Die angegebenen Speicherkontodetails sind falsch.

Vergewissern Sie sich, dass das Speicherkonto noch vorhanden ist und nicht gelöscht wurde.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound: Die Ressourcengruppe ist nicht vorhanden.

Fehlercode: UserErrorResourceGroupNotFound

Fehlermeldung: Die Ressourcengruppe ist nicht vorhanden.

Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest: Für diese Dateifreigabe wird bereits ein Sicherungsauftrag ausgeführt.

Fehlercode: ParallelSnapshotRequest

Fehlermeldung: Für diese Dateifreigabe wird bereits ein Sicherungsauftrag ausgeführt.

- Die Dateifreigabesicherung unterstützt keine parallelen Momentaufnahmeanforderungen für dieselbe Dateifreigabe.

- Warten Sie, bis der aktuelle Sicherungsauftrag abgeschlossen ist, und wiederholen Sie den Vorgang. Sollten Sie im Recovery Services-Tresor keinen Sicherungsauftrag finden, überprüfen Sie andere Recovery Services-Tresore im gleichen Abonnement.

### <a name="usererrorstorageaccountinternetroutingnotsupported--storage-accounts-with-internet-routing-configuration-are-not-supported-by-azure-backup"></a>UserErrorStorageAccountInternetRoutingNotSupported: Speicherkonten mit Internetroutingkonfiguration werden von Azure Backup nicht unterstützt.

Fehlercode: UserErrorStorageAccountInternetRoutingNotSupported

Fehlermeldung: Speicherkonten mit Internetroutingkonfiguration werden von Azure Backup nicht unterstützt.

Stellen Sie sicher, dass die Routingpräferenz, die für das Speicherkonto festgelegt ist, auf dem die gesicherte Dateifreigabe gehostet wird, „Microsoft-Netzwerkrouting“ lautet.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling: Fehler bei der Sicherung oder Wiederherstellung einer Dateifreigabe aufgrund von Drosselung des Speicherdiensts. Möglicherweise ist der Speicherdienst mit der Verarbeitung anderer Anforderungen für das angegebene Speicherkonto ausgelastet.

Fehlercode: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Fehlermeldung: Fehler bei der Dateifreigabesicherung/Dateifreigabewiederherstellung aufgrund der Speicherdienstdrosselung. Möglicherweise ist der Speicherdienst mit der Verarbeitung anderer Anforderungen für das angegebene Speicherkonto ausgelastet.

Wiederholen Sie den Sicherungs-/Wiederherstellungsvorgang zu einem späteren Zeitpunkt.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound: Die Zieldateifreigabe wurde nicht gefunden.

Fehlercode: TargetFileShareNotFound

Fehlermeldung: Die Zieldateifreigabe wurde nicht gefunden.

- Vergewissern Sie sich, dass das ausgewählte Speicherkonto vorhanden ist und die Zieldateifreigabe nicht gelöscht wurde.

- Vergewissern Sie sich, dass das Speicherkonto für die Sicherung von Dateifreigaben unterstützt wird.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked: Fehler bei der Sicherung/Wiederherstellung, weil sich das Speicherkonto im gesperrten Zustand befindet.

Fehlercode: UserErrorStorageAccountIsLocked

Fehlermeldung: Fehler bei der Sicherung/Wiederherstellung, weil sich das Speicherkonto im gesperrten Zustand befindet.

Entfernen Sie die Sperre des Speicherkontos, oder verwenden Sie eine **Löschsperre** anstelle einer **Lesesperre**, und wiederholen Sie den Vorgang.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached: Fehler bei der Wiederherstellung, weil die Anzahl fehlerhafter Dateien den Schwellenwert übersteigt.

Fehlercode: DataTransferServiceCoFLimitReached

Fehlermeldung: Fehler bei der Wiederherstellung, weil die Anzahl fehlerhafter Dateien den Schwellenwert übersteigt.

- Die Ursachen für Fehler bei der Wiederherstellung werden in einer Datei aufgeführt. (Den Pfad finden Sie in den Auftragsdetails.) Beheben Sie die Fehler, und wiederholen Sie den Wiederherstellungsvorgang nur für die Dateien, bei denen ein Fehler aufgetreten ist.

- Gängige Fehlerursachen beim Wiederherstellen von Dateien:

  - Die Dateien, bei denen ein Fehler aufgetreten ist, werden gerade verwendet.
  - Das übergeordnete Verzeichnis enthält ein Verzeichnis mit dem gleichen Namen wie die Datei, bei der ein Fehler aufgetreten ist.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover: Fehler bei der Wiederherstellung, weil keine Datei wiederhergestellt werden konnte.

Fehlercode: DataTransferServiceAllFilesFailedToRecover

Fehlermeldung: Fehler bei der Wiederherstellung, weil keine Datei wiederhergestellt werden konnte.

- Die Ursachen für Fehler bei der Wiederherstellung werden in einer Datei aufgeführt. (Den Pfad finden Sie in den Auftragsdetails.) Beheben Sie die Fehler, und wiederholen Sie die Wiederherstellungsvorgänge für die Dateien, bei denen ein Fehler aufgetreten ist.

- Gängige Fehlerursachen beim Wiederherstellen von Dateien:

  - Die Dateien, bei denen ein Fehler aufgetreten ist, werden gerade verwendet.
  - Das übergeordnete Verzeichnis enthält ein Verzeichnis mit dem gleichen Namen wie die Datei, bei der ein Fehler aufgetreten ist.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid: Fehler beim Wiederherstellen, da eine der Dateien in der Quelle nicht vorhanden ist.

Fehlercode: DataTransferServiceSourceUriNotValid

Fehlermeldung: Fehler beim Wiederherstellen: Eine der Dateien in der Quelle ist nicht vorhanden.

- Die ausgewählten Elemente sind in den Wiederherstellungspunktdaten nicht vorhanden. Geben Sie die korrekte Dateiliste an, um die Dateien wiederherzustellen.
- Die Dateifreigabemomentaufnahme für den Wiederherstellungspunkt wurde manuell gelöscht. Wiederholen Sie den Wiederherstellungsvorgang mit einem anderen Wiederherstellungspunkt.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked: Ein Wiederherstellungsauftrag wird zurzeit für dasselbe Ziel durchgeführt.

Fehlercode: UserErrorDTSDestLocked

Fehlermeldung: Ein Wiederherstellungsauftrag wird zurzeit für dasselbe Ziel durchgeführt.

- Die Dateifreigabesicherung unterstützt keine parallele Wiederherstellung in der gleichen Zieldateifreigabe.

- Warten Sie, bis die aktuelle Wiederherstellung abgeschlossen ist, und wiederholen Sie den Vorgang. Sollten Sie im Recovery Services-Tresor keinen Wiederherstellungsauftrag finden können, überprüfen Sie andere Recovery Services-Tresore im gleichen Abonnement.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull: Fehler beim Wiederherstellungsvorgang, weil die Zieldateifreigabe voll ist.

Fehlercode: UserErrorTargetFileShareFull

Fehlermeldung: Fehler beim Wiederherstellungsvorgang, weil die Zieldateifreigabe voll ist.

Erhöhen Sie das Größenkontingent der Zieldateifreigabe so, dass es für die Wiederherstellungsdaten ausreicht, und wiederholen Sie anschließend den Wiederherstellungsvorgang.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient: Das Speicherplatzkontingent der Zieldateifreigabe reicht für eine Wiederherstellung nicht aus.

Fehlercode: UserErrorTargetFileShareQuotaNotSufficient

Fehlermeldung: Das Speicherplatzkontingent der Zieldateifreigabe reicht für eine Wiederherstellung nicht aus.

Erhöhen Sie das Größenkontingent der Zieldateifreigabe so, dass es für die Wiederherstellungsdaten ausreicht, und wiederholen Sie anschließend den Vorgang.

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed: Der Wiederherstellungsvorgang war nicht erfolgreich, da beim Ausführen der Wiederherstellungsvorbereitung für mit der Zieldateifreigabe verknüpfte Ressourcen des Dateisynchronisierungsdiensts ein Fehler aufgetreten ist.

Fehlercode: File Sync PreRestoreFailed

Fehlermeldung: Der Wiederherstellungsvorgang war nicht erfolgreich: Beim Ausführen der Wiederherstellungsvorbereitung für mit der Zieldateifreigabe verknüpfte Ressourcen des Dateisynchronisierungsdiensts ist ein Fehler aufgetreten.

Versuchen Sie, die Daten zu einem späteren Zeitpunkt wiederherzustellen. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress: Für die Zieldateifreigabe wird zurzeit die Änderungserkennung des Azure-Dateisynchronisierungsdiensts ausgeführt. Die Änderungserkennung wurde durch eine vorhergehende Wiederherstellung in die Zieldateifreigabe ausgelöst.

Fehlercode: AzureFileSyncChangeDetectionInProgress

Fehlermeldung: Für die Zieldateifreigabe wird zurzeit die Änderungserkennung des Azure-Dateisynchronisierungsdiensts ausgeführt. Die Änderungserkennung wurde durch eine vorhergehende Wiederherstellung in die Zieldateifreigabe ausgelöst.

Verwenden Sie eine andere Zieldateifreigabe. Alternativ können Sie warten, bis die Änderungserkennung für den Azure-Dateisynchronisierungsdienst für die Zieldateifreigabe abgeschlossen wurde. Wiederholen Sie anschließend die Wiederherstellung.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored: Mindestens eine Datei konnte nicht erfolgreich wiederhergestellt werden. Weitere Informationen finden Sie in der Liste fehlerhafter Dateien im oben angegebenen Pfad.

Fehlercode: UserErrorAFSRecoverySomeFilesNotRestored

Fehlermeldung: Mindestens eine Datei konnte nicht erfolgreich wiederhergestellt werden. Weitere Informationen finden Sie in der Liste fehlerhafter Dateien im oben angegebenen Pfad.

- Die Ursachen für Fehler bei der Wiederherstellung werden in einer Datei aufgeführt. (Den Pfad finden Sie in den Auftragsdetails.) Beheben Sie die Ursachen, und wiederholen Sie den Wiederherstellungsvorgang nur für die Dateien, bei denen ein Fehler aufgetreten ist.
- Gängige Fehlerursachen beim Wiederherstellen von Dateien:

  - Die Dateien, bei denen ein Fehler aufgetreten ist, werden gerade verwendet.
  - Das übergeordnete Verzeichnis enthält ein Verzeichnis mit dem gleichen Namen wie die Datei, bei der ein Fehler aufgetreten ist.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound: Die diesem Wiederherstellungspunkt entsprechende Momentaufnahme der Azure-Dateifreigabe wurde nicht gefunden.

Fehlercode: UserErrorAFSSourceSnapshotNotFound

Fehlermeldung: Die diesem Wiederherstellungspunkt entsprechende Momentaufnahme der Azure-Dateifreigabe wurde nicht gefunden.

- Vergewissern Sie sich, dass die Momentaufnahme der Dateifreigabe, die dem Wiederherstellungspunkt entspricht, den Sie für die Wiederherstellung verwenden möchten, noch vorhanden ist.

  >[!NOTE]
  >Wenn Sie eine von Azure Backup erstellte Dateifreigabe-Momentaufnahme löschen, werden die entsprechenden Wiederherstellungspunkte unbrauchbar. Wir empfehlen, keine Momentaufnahmen zu löschen, um die garantierte Wiederherstellung sicherzustellen.

- Versuchen Sie, einen anderen Wiederherstellungspunkt auszuwählen, um Ihre Daten wiederherzustellen.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget: Für die gleiche Zieldateifreigabe wird ein weiterer Wiederherstellungsauftrag ausgeführt.

Fehlercode: UserErrorAnotherRestoreInProgressOnSameTarget

Fehlermeldung: Für dieselbe Zieldateifreigabe wird zurzeit ein anderer Wiederherstellungsauftrag ausgeführt.

Verwenden Sie eine andere Zieldateifreigabe. Alternativ können Sie den Vorgang abbrechen oder warten, bis der andere Wiederherstellungsvorgang abgeschlossen wurde.

## <a name="common-modify-policy-errors"></a>Häufige Fehler beim Ändern von Richtlinien

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation: Für dieses Element wird derzeit ein anderer Vorgang zum Konfigurieren von Schutzeinstellungen ausgeführt.

Fehlercode: BMSUserErrorConflictingProtectionOperation

Fehlermeldung: Für dieses Element wird derzeit ein anderer Vorgang zum Konfigurieren von Schutzeinstellungen ausgeführt.

Warten Sie, bis der vorhergehende Vorgang zum Ändern der Richtlinie abgeschlossen ist, und versuchen Sie es zu einem späteren Zeitpunkt noch einmal.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked: Für das ausgewählte Element wird derzeit ein anderer Vorgang ausgeführt.

Fehlercode: BMSUserErrorObjectLocked

Fehlermeldung: Für das ausgewählte Element wird derzeit ein anderer Vorgang ausgeführt.

Warten Sie, bis dieser andere Vorgang abgeschlossen ist, und versuchen Sie es zu einem späteren Zeitpunkt noch einmal.

## <a name="common-soft-delete-related-errors"></a>Häufige Fehler beim vorläufigen Löschen

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState: Dieser Wiederherstellungspunkt ist nicht verfügbar, da die diesem Punkt zugeordnete Momentaufnahme in einer Dateifreigabe gespeichert ist, die sich im vorläufig gelöschten Zustand befindet.

Fehlercode: UserErrorRestoreAFSInSoftDeleteState

Fehlermeldung: Dieser Wiederherstellungspunkt ist nicht verfügbar, da die diesem Punkt zugeordnete Momentaufnahme in einer Dateifreigabe gespeichert ist, die sich im vorläufig gelöschten Zustand befindet.

Sie können keinen Wiederherstellungsvorgang durchführen, wenn sich die Dateifreigabe im vorläufig gelöschten Zustand befindet. Löschen Sie die Dateifreigabe aus dem Dateiportal, oder verwenden Sie das [Skript zum Rückgängigmachen des Löschvorgangs](scripts/backup-powershell-script-undelete-file-share.md), und versuchen Sie dann, die Wiederherstellung auszuführen.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState: Die aufgeführten Wiederherstellungspunkte sind nicht verfügbar, da die zugeordnete Dateifreigabe mit den Wiederherstellungspunkt-Momentaufnahmen dauerhaft gelöscht wurde.

Fehlercode: UserErrorRestoreAFSInDeleteState

Fehlermeldung: Die aufgeführten Wiederherstellungspunkte sind nicht verfügbar, da die zugeordnete Dateifreigabe mit den Wiederherstellungspunkt-Momentaufnahmen dauerhaft gelöscht wurde.

Überprüfen Sie, ob die gesicherte Dateifreigabe gelöscht wurde. Wenn sie sich im vorläufig gelöschten Zustand befand, überprüfen Sie, ob die Beibehaltungsdauer für vorläufiges Löschen abgelaufen ist und sie nicht wiederhergestellt wurde. In beiden Fällen verlieren Sie alle Momentaufnahmen dauerhaft und können die Daten nicht wiederherstellen.

>[!NOTE]
> Wir empfehlen, die gesicherte Dateifreigabe nicht zu löschen bzw. (wenn sie sich im vorläufig gelöschten Zustand befindet) den Löschvorgang vor dem Ende der Beibehaltungsdauer nicht rückgängig zu machen, um zu vermeiden, dass alle Wiederherstellungspunkte verloren gehen.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState: Fehler bei der Sicherung, da sich die Azure-Dateifreigabe im vorläufig gelöschten Zustand befindet.

Fehlercode: UserErrorBackupAFSInSoftDeleteState

Fehlermeldung: Fehler bei der Sicherung, da sich die Azure-Dateifreigabe im vorläufig gelöschten Zustand befindet.

Löschen Sie die Dateifreigabe über das **Azure Files-Portal** oder mit dem [Skript zum Rückgängigmachen des Löschvorgangs](scripts/backup-powershell-script-undelete-file-share.md), um die Sicherung fortzusetzen und das dauerhafte Löschen von Daten zu verhindern.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState: Fehler bei der Sicherung, da die zugeordnete Azure-Dateifreigabe dauerhaft gelöscht wurde.

Fehlercode: UserErrorBackupAFSInDeleteState

Fehlermeldung: Fehler bei der Sicherung, da die zugeordnete Azure-Dateifreigabe dauerhaft gelöscht wurde.

Überprüfen Sie, ob die gesicherte Dateifreigabe dauerhaft gelöscht wurde. Beenden Sie in diesem Fall die Sicherung für diese Dateifreigabe, um wiederholte Sicherungsfehler zu vermeiden. Weitere Informationen zum Beenden des Schutzes finden Sie unter [Beendigung des Schutzes für eine Azure-Dateifreigabe](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Sichern von Azure-Dateifreigaben finden Sie in den folgenden Artikeln:

- [Sichern von Azure-Dateifreigaben](backup-afs.md)
- [Fragen zum Sichern von Azure Files](backup-azure-files-faq.yml)