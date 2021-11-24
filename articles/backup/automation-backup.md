---
title: Automatisierung in Azure Backup
description: Dieser Artikel bietet einen Überblick über die Automatisierungsfunktionen von Azure Backup.
ms.topic: conceptual
ms.date: 11/16/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: ec19a2f887971a3a159b8b6ca155b33cd9af52e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493001"
---
# <a name="automation-in-azure-backup"></a>Automatisierung in Azure Backup

Wenn Sie Ihre Vorgänge in Azure verwalten, kann Ihr Sicherungsbestand größer werden, weil neue, vielfältige Workloads einbezogen werden. Das Volumen Ihres Sicherungsbestands wird also hochskalieren.

Im kleinen Rahmen können die folgenden Aktionen manuell oder einfach programmgesteuert ausgeführt werden: Identifizieren der richtigen zu sichernden Computer, Konfigurieren der Sicherung, Überwachen des Sicherungsstatus, Extrahieren von Daten usw. Im großen Rahmen können solche Aktionen jedoch schwierig, komplex und fehleranfällig sein.

Mit Azure Backup können Sie die meisten Sicherungstasks mit programmgesteuerten Methoden automatisieren. Dieser Artikel beschreibt die verschiedenen Automatisierungsclients, die von Azure Backup unterstützt werden. Außerdem werden einige umfassende Automatisierungsszenarios vorgestellt, die häufig in Sicherungsbereitstellungen auf Unternehmensniveau verwendet werden.

## <a name="automation-methods"></a>Automatisierungsmethoden

Für den Zugriff auf Azure Backup-Funktionalität können Sie die folgenden, von Azure unterstützten Standardautomatisierungsmethoden verwenden:

- PowerShell
- Befehlszeilenschnittstelle (CLI)
- REST-API
- Python SDK
- Go SDK
- Terraform
- ARM-Vorlagen
- Bicep

Sie können Azure Backup auch mit anderen Azure-Diensten wie Logic Apps, Runbooks und Aktionsgruppen verwenden, um umfassende Automatisierungsworkflows einzurichten.

Weitere Informationen zu den verschiedenen Szenarios, die Automatisierungsclients unterstützen, sowie die entsprechenden Dokumentationsverweise finden Sie im Abschnitt [Unterstützte Automatisierungsmethoden nach Vorgangstyp](#supported-automation-methods-by-operation-types).

## <a name="sample-automation-scenarios"></a>Beispiele für Automatisierungsszenarios

In diesem Abschnitt werden einige häufige Anwendungsfälle für die Automatisierung beschrieben, denen Sie als Sicherungsadministrator begegnen können. Sie finden hier auch Anleitungen zu den ersten Schritten.

### <a name="configure-backups"></a>Konfigurieren von Sicherungen

Als Sicherungsadministrator sind Sie damit konfrontiert, dass die Infrastruktur regelmäßig erweitert wird, und müssen sicherstellen, dass sie gemäß den festgelegten Anforderungen geschützt ist. Automatisierungsclients wie PowerShell/CLI helfen Ihnen dabei, alle VM-Details abzurufen, den Sicherungsstatus der einzelnen VMs zu überprüfen und dann geeignete Maßnahmen für ungeschützte VMs zu ergreifen.

Diese Vorgänge müssen jedoch auch im großen Stil leistungsstark ausgeführt werden können. Außerdem müssen Sie sie regelmäßig planen und jede Ausführung überwachen. Um Automatisierungsvorgänge zu vereinfachen, verwendet Azure Backup jetzt Azure Policy und verwaltet den Sicherungsbestand mithilfe von [integrierten, sicherungsspezifischen Azure-Richtlinien](backup-center-govern-environment.md#azure-policies-for-backup).

Nachdem Sie einem Bereich eine Azure-Richtlinie zugewiesen haben, werden alle VMs, die Ihren Kriterien entsprechen, automatisch gesichert, und neuere VMs werden regelmäßig mit der Azure-Richtlinie überprüft und geschützt. Sie können auch einen Konformitätsbericht anzeigen, der Sie bei nicht konformen Ressourcen warnt.

[Weitere Informationen zu integrierten Azure-Richtlinien für Sicherungen](backup-azure-auto-enable-backup.md)

Im folgenden Video wird veranschaulicht, wie Azure Policy bei der Sicherung funktioniert:  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>Exportieren von sicherungsoperativen Daten

Möglicherweise müssen Sie sicherungsoperative Daten für Ihren gesamten Datenbestand extrahieren und regelmäßig in Ihre Überwachungssysteme/Dashboards importieren. Im großen Rahmen (beim Abfragen großer Datensätze) sollten die Daten schnell abgerufen werden. Sie müssen Ressourcen, Abonnements und Mandanten abfragen. Diese Abfragen müssen über einen Client (Azure-Portal/PowerShell/CLI/beliebiges SDK/REST-API) ausgeführt werden. Außerdem sollte das Ausgabeformat flexibel sein (Tabelle im Vergleich zu Array).

Mit Azure Resource Graph (ARG) können Sie diese Vorgänge ausführen und Abfragen im großen Stil durchführen. Azure Backup verwendet ARG als optimierte Methode, um erforderliche Daten mit einer minimalen Abfragemenge abzurufen (eine Abfrage pro Szenario). Beispielsweise kann eine einzelne Abfrage alle fehlerhaften Aufträge für alle Tresore in allen Abonnements und Mandanten abrufen. Außerdem sind die Abfragen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure konform.

Weitere Informationen finden Sie in den [ARG-Beispielabfragen](query-backups-using-azure-resource-graph.md#sample-queries).

### <a name="automate-responsesactions"></a>Automatisieren von Antworten/Aktionen

Die Automatisierung von Antworten auf vorübergehende Sicherungsauftragsfehler hilft Ihnen sicherzustellen, dass Sie über die richtige Anzahl zuverlässiger Sicherungen für die Wiederherstellung verfügen. Automatisierung hilft außerdem, unbeabsichtigte Sicherheitsverletzungen in Ihrer [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) zu vermeiden.

Sie können einen Workflow zum Wiederholen von Sicherungsaufträgen mithilfe einer Kombination aus Azure Automation Runbooks, PowerShell und Azure Resource Graph einrichten. Dies hilft in Szenarios, in denen die Sicherungsaufträge aufgrund eines vorübergehenden Fehlers oder eines geplanten oder ungeplanten Ausfalls fehlgeschlagen sind.

Führen Sie zum Einstieg diese Schritte aus:

1. Erstellen Sie ein Automation-Konto und innerhalb dieses Automation-Kontos ein neues PowerShell-Runbook. [Weitere Informationen](/azure/automation/learn/powershell-runbook-managed-identity).

2. Fügen Sie das folgende Skript in den Runbookkörper ein. 

   Das Skript führt eine ARG-Abfrage aus, um die Liste aller VMs mit aktuellen Auftragsfehlern abzurufen (Sie können der Abfrage einen Filter für _startTime_ hinzufügen), und löst dann eine On-Demand-Sicherung für jede VM aus. Sie können ähnliche Abfragen erstellen, um die Liste aller SQL-Datenbanken, HANA-Datenbanken, Azure Files-Instanzen und anderer Azure-Workloads abzurufen, die gerade gesichert werden.

    ```azurepowershell
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    $connectionResult = Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $connection.TenantID `
    -ApplicationId $connection.ApplicationID `
    -CertificateThumbprint $connection.CertificateThumbprint
    "Login successful.."

    $query = "RecoveryServicesResources
    | where type in~ ('microsoft.recoveryservices/vaults/backupjobs')
    | extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
    | extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
    | extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
    | extend protectedItemName = split(split(properties.backupInstanceId, 'protectedItems')[1],'/')[1]
    | extend vaultId = tostring(split(id, '/backupJobs')[0])
    | extend vaultSub = tostring( split(id, '/')[2])
    | extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'), startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration 
    | where (dataSourceType in~ ('AzureIaasVM'))
    | where jobStatus=='Failed'
    | where operation == 'backup' or operation == 'adhocBackup'
    | project vaultSub, vaultId, protectedItemName, startTime, endTime, jobStatus, operation
    | sort by vaultSub"

    $subscriptions = Get-AzSubscription | foreach {$_.SubscriptionId}
    $result = Search-AzGraph -Subscription $subscriptions -Query $query -First 5
    $result = $result.data
    $prevsub = ""
    foreach($jobresponse in $result)
    {
                if($jobresponse.vaultSub -ne $prevsub)
                {
                            Set-AzContext -SubscriptionId $jobresponse.vaultSub
                            $prevsub = $jobresponse.vaultSub
                }

                $item = Get-AzRecoveryServicesBackupItem -VaultId $jobresponse.vaultId -BackupManagementType AzureVM -WorkloadType AzureVM -Name $jobresponse.protectedItemName

                Backup-AzRecoveryServicesBackupItem -ExpiryDateTimeUTC (get-date).AddDays(10) -Item $item -VaultId $jobresponse.vaultId
    }
    ```

3. Importieren Sie die folgenden Module in das Runbook, um sicherzustellen, dass das Skript ordnungsgemäß ausgeführt wird: `Az.Accounts`, `Az.RecoveryServices`, `Az.Graph`.

   [Weitere Informationen zu Importieren von Modulen in ein Runbook](/azure/automation/shared-resources/modules)

4. [Verknüpfen Sie das Runbook mit einem Zeitplan](/azure/automation/shared-resources/schedules), um das Skript so zu konfigurieren, dass es in regelmäßigen Abständen automatisch ausgeführt wird.

   Im folgenden Video finden Sie eine umfassende exemplarische Vorgehensweise für das Szenario: <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-types"></a>Unterstützte Automatisierungsmethoden nach Vorgangstypen

### <a name="azure-vm"></a>Azure VM

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Unterstützt <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#back-up-azure-vms) | Unterstützt <br><br> [Beispiele](/azure/backup/quick-backup-vm-cli#enable-backup-for-an-azure-vm) | Unterstützt  <br><br>  [Beispiele](/azure/backup/backup-azure-arm-userestapi-backupazurevms) | Unterstützt  <br><br> [Beispiele](/azure/backup/backup-azure-auto-enable-backup) | Unterstützt  <br><br> [Beispiele](/azure/backup/backup-rm-template-samples) |
| Backup | Selektive Datenträgersicherung | Unterstützt  <br><br> [Beispiele](/azure/backup/selective-disk-backup-restore#using-powershell) | Unterstützt  <br><br> [Beispiele](/azure/backup/selective-disk-backup-restore#using-azure-cli) | Unterstützt  <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-backupazurevms#excluding-disks-in-azure-vm-backup) | – | – |
| Backup | Ausführen einer On-Demand-Sicherung | Unterstützt   <br><br> [Beispiele](/azure/backup/quick-backup-vm-powershell#start-a-backup-job) | Unterstützt  <br><br> [Beispiele](/azure/backup/quick-backup-vm-cli#start-a-backup-job) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-backupazurevms#trigger-an-on-demand-backup-for-a-protected-azure-vm) | – | – |
| Restore | Wiederherstellen von Datenträgern in der primären Region | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#restore-an-azure-vm) | Unterstützt  <br><br> [Beispiele](/azure/backup/tutorial-restore-disk#restore-a-vm-disk) | Unterstützt  <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-restoreazurevms) | – | – |
| Restore | Regionsübergreifende Wiederherstellung | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#restore-disks-to-a-secondary-region) | Unterstützt   <br><br> [Beispiele](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#cross-region-restore) | – | – |
| Restore | Selektives Wiederherstellen von Datenträgern | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#restore-selective-disks) | Unterstützt   <br><br> [Beispiele](/azure/backup/selective-disk-backup-restore#restore-disks-with-azure-cli) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks-selectively) | – | – |
| Restore | Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#using-managed-identity-to-restore-disks) | Unterstützt   <br><br> [Beispiele](/azure/backup/tutorial-restore-disk#using-managed-identity-to-restore-disks) | Unterstützt   <br><br> [Beispiele](/rest/api/backup/restores/trigger) | – | – |
| Restore | Wiederherstellen von Dateien | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) | Unterstützt   <br><br> [Beispiele](/azure/backup/tutorial-restore-disk#create-a-vm-from-the-restored-disk) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks) | – | – |
| Verwalten | Überwachen von Aufträgen | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup) | Unterstützt   <br><br> [Beispiele](/azure/backup/tutorial-restore-files) | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#monitoring-a-backup-job) | Unterstützt   <br><br> [Beispiele](/azure/backup/quick-backup-vm-cli#monitor-the-backup-job) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job) | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten beibehalten | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#retain-data) | Unterstützt   <br><br> [Beispiele](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-but-retain-existing-data) | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten löschen | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#delete-backup-data) | Unterstützt   <br><br> [Beispiele](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true) | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-and-delete-data) | – | – |
| Verwalten | Schutz fortsetzen | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#resume-backup)    | Unterstützt    <br><br> [Beispiele](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-backupazurevms#undo-the-deletion) | – | – |

### <a name="sql-in-azure-vm"></a>SQL Server in Azure-VM

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#configure-a-backup-policy) | Unterstützt | Unterstützt | Derzeit nicht hier | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-rm-template-samples) |
| Backup | Aktivieren des automatischen Schutzes | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#enable-autoprotection) | Unterstützt | Unterstützt | – | – |
| Backup | Ausführen einer On-Demand-Sicherung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#on-demand-backup) | Unterstützt | Unterstützt | – | – |
| Restore | Zurücksetzen auf einen eindeutigen vollständigen/differenziellen Wiederherstellungspunkt | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#original-restore-with-distinct-recovery-point) | Unterstützt | Unterstützt | – | – |
| Restore | Wiederherstellen eines bestimmten Zeitpunkts | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#original-restore-with-log-point-in-time) | Unterstützt | Unterstützt | – | – |
| Restore | Regionsübergreifende Wiederherstellung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#alternate-workload-restore-to-a-vault-in-secondary-region) | Unterstützt | Unterstützt | – | – |
| Verwalten | Überwachen von Aufträgen | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#track-azure-backup-jobs) | Unterstützt | Unterstützt | – | – |
| Verwalten | Verwalten von Azure Monitor-Warnungen (Vorschau) | Unterstützt    <br><br> [Beispiele](/azure/azure-monitor/powershell-samples) | Unterstützt | Unterstützt | – | – |
| Verwalten | Verwalten von Azure Monitor-Metriken (Vorschau) | Unterstützt    <br><br> [Beispiele](/azure/azure-monitor/powershell-samples) | Unterstützt | Unterstützt | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#change-policy-for-backup-items) | Unterstützt | Unterstützt | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten beibehalten | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#retain-data) | Unterstützt | Unterstützt | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten löschen | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#delete-backup-data) | Unterstützt | Unterstützt | – | – |
| Verwalten | Aufheben der Instanzregistrierung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#unregister-sql-vm) | Unterstützt | Unterstützt | – | – |
| Verwalten | Erneutes Registrieren der Instanz | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-sql-automation#re-register-sql-vms) | Unterstützt | Unterstützt | – | – |

### <a name="sap-hana-in-azure-vm"></a>SAP HANA in Azure-VM

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Wird derzeit nicht unterstützt. | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-backup-cli#register-and-protect-the-sap-hana-instance) | Unterstützt | Wird derzeit nicht unterstützt. | – |
| Backup | Ausführen einer On-Demand-Sicherung | Wird derzeit nicht unterstützt. | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-backup-cli#trigger-an-on-demand-backup) | Unterstützt | – | Unterstützt – Beispiele   <br><br> [Beispiele](/azure/backup/backup-rm-template-samples) |
| Restore | Zurücksetzen auf einen eindeutigen vollständigen/differenziellen/inkrementellen Wiederherstellungspunkt | Wird derzeit nicht unterstützt. | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database) | Unterstützt | – |    |
| Restore | Wiederherstellen eines bestimmten Zeitpunkts | Wird derzeit nicht unterstützt.  | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database) | Unterstützt | – | – |
| Restore | Regionsübergreifende Wiederherstellung | Wird derzeit nicht unterstützt. | Unterstützt | Unterstützt | – | – |
| Verwalten | Überwachen von Aufträgen | Wird derzeit nicht unterstützt.  | Unterstützt | Unterstützt | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Wird derzeit nicht unterstützt. | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-manage-cli#change-policy) | Unterstützt | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten beibehalten | Wird derzeit nicht unterstützt.  | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-with-retain-data) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten löschen | Wird derzeit nicht unterstützt.  | Unterstützt    <br><br> [Beispiele](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-without-retain-data) | Unterstützt    <br><br> [Beispiele](/rest/api/backup/protected-items/delete) | – | – |
| Verwalten | Aufheben der Instanzregistrierung | Wird derzeit nicht unterstützt.  | Unterstützt | Unterstützt | – | – |
| Verwalten | Erneutes Registrieren der Instanz | Wird derzeit nicht unterstützt.  | Unterstützt | Unterstützt | – | – |

### <a name="azure-files"></a>Azure Files

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-afs-automation#configure-a-backup-policy) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-afs-cli#enable-backup-for-azure-file-shares) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-file-share-rest-api#configure-backup-for-an-unprotected-azure-file-share-using-rest-api) | Wird derzeit nicht unterstützt. | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-rm-template-samples) |
| Backup | Ausführen einer On-Demand-Sicherung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-afs-automation#trigger-an-on-demand-backup) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-afs-cli#trigger-an-on-demand-backup-for-file-share) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-file-share-rest-api#trigger-an-on-demand-backup-for-file-share) | – | – |
| Restore | Wiederherstellen am ursprünglichen oder einem alternativen Speicherort | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-afs-powershell) | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-afs-cli) | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-azure-file-share-rest-api) | – | – |
| Verwalten | Überwachen von Aufträgen | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-powershell#track-backup-and-restore-jobs) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-backup-cli#monitor-jobs) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-azure-file-share-rest-api#monitor-jobs) | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-powershell#modify-the-protection-policy) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-backup-cli#modify-policy) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-azure-file-share-rest-api#modify-policy) | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten beibehalten | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-powershell#stop-protection-and-retain-recovery-points) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-backup-cli#stop-protection-and-retain-recovery-points) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-azure-file-share-rest-api#stop-protection-but-retain-existing-data) | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten löschen | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-powershell#stop-protection-without-retaining-recovery-points) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-afs-backup-cli#stop-protection-without-retaining-recovery-points) | Unterstützt    <br><br> [Beispiele](/azure/backup/manage-azure-file-share-rest-api#stop-protection-and-delete-data) | – | – |

### <a name="azure-blobs"></a>Azure-Blobs

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-blobs-storage-account-ps) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-blobs-storage-account-cli) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-blobs) | Wird derzeit nicht unterstützt. | Unterstützt |
| Restore | Wiederherstellen von Blobs | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-blobs-storage-account-ps) | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-blobs-storage-account-cli) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs) | – | – |
| Verwalten | Überwachen von Aufträgen | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-blobs-storage-account-ps#tracking-job) | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-blobs-storage-account-cli#tracking-job) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs#tracking-jobs) | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten beibehalten | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten löschen | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Schutz fortsetzen | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | – | – |

### <a name="azure-disks"></a>Azure Disks

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Unterstützt   <br><br> [Beispiele](/azure/backup/backup-managed-disks-ps) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-managed-disks-cli) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-disks) | Wird derzeit nicht unterstützt. | Unterstützt |
| Backup | Ausführen einer On-Demand-Sicherung | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-managed-disks-ps#run-an-on-demand-backup) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-managed-disks-cli#run-an-on-demand-backup) |  – | – |
| Restore | Wiederherstellen auf einem neuen Datenträger | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-managed-disks-ps) | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-managed-disks-cli) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks) | – | – |
| Verwalten | Überwachen von Aufträgen | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-managed-disks-ps#tracking-job) | Unterstützt    <br><br> [Beispiele](/azure/backup/restore-managed-disks-cli#tracking-job) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks#track-jobs) | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | Wird derzeit nicht unterstützt. | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten beibehalten | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Schutz beenden und Sicherungsdaten löschen | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Schutz fortsetzen | Unterstützt | Unterstützt | Unterstützt | – | – |

### <a name="azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL-Server

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Backup | Erstellen einer Sicherungsrichtlinie und Konfigurieren der Sicherung | Unterstützt | Unterstützt | Unterstützt | Derzeit nicht hier | Unterstützt |
| Backup | Ausführen einer On-Demand-Sicherung | Unterstützt | Unterstützt | Unterstützt | – | – |
| Restore | Wiederherstellen der Datenbank im Zielspeicherkonto | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Ändern der Sicherungsrichtlinie | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Beenden des Schutzes und Löschen der Daten | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Beenden des Schutzes und Aufbewahren von Daten | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Schutz fortsetzen | Unterstützt | Unterstützt | Unterstützt | – | – |

### <a name="vault-level-configurations"></a>Konfigurationen auf Tresorebene

| **Kategorie** | **Vorgang** | **PowerShell** | **BEFEHLSZEILENSCHNITTSTELLE (CLI)** | **REST-API** | **Azure Policy** | **ARM-Vorlage** |
| --- | --- | --- | --- | --- | --- | --- |
| Verwalten | Erstellen eines Recovery Services-Tresors | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-vms-automation#create-a-recovery-services-vault) | Unterstützt    <br><br> [Beispiele](/azure/backup/quick-backup-vm-cli#create-a-recovery-services-vault) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-arm-userestapi-createorupdatevault) | – | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-rm-template-samples) |
| Verwalten | Erstellen des Sicherungstresors | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-blobs-storage-account-ps#create-a-backup-vault) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-blobs-storage-account-cli#create-a-backup-vault) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-dataprotection-use-rest-api-create-update-backup-vault) | – | Unterstützt |
| Verwalten | Verschieben eines Recovery Services-Tresors | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault) | Unterstützt | – | – |
| Verwalten | Verschieben eines Backup-Tresors | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Löschen eines Recovery Services-Tresors | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-powershell) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-cli) | Unterstützt    <br><br> [Beispiele](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-azure-resource-manager) | – | – |
| Verwalten | Löschen eines Backup-Tresors | Unterstützt | Hier | Hier | – | – |
| Verwalten | Konfigurieren von Diagnoseeinstellungen | Unterstützt | Unterstützt | Unterstützt | Unterstützt    <br><br> [Beispiele](/azure/backup/azure-policy-configure-diagnostics) | Unterstützt |
| Verwalten | Verwalten von Azure Monitor-Warnungen (Vorschau) | Unterstützt | Unterstützt | Unterstützt | – | – |
| Verwalten | Verwalten von Azure Monitor-Metriken (Vorschau) | Unterstützt | Unterstützt | Unterstützt | – | – |
| Sicherheit | Aktivieren privater Endpunkte für einen Recovery Services-Tresor | Unterstützt | Unterstützt | Unterstützt | Nur Überwachungsrichtlinie derzeit unterstützt | Unterstützt |
| Sicherheit | Aktivieren von kundenseitig verwalteten Schlüsseln für einen Recovery Services-Tresor | Unterstützt | Unterstützt | Unterstützt | Nur Überwachungsrichtlinie derzeit unterstützt | Unterstützt |
| Sicherheit | Aktivieren des vorläufigen Löschens für eine Recovery Services-Tresor | Unterstützt | Unterstützt | Unterstützt | Wird derzeit nicht unterstützt. | Unterstützt |
| Resilienz | Aktivieren der regionsübergreifenden Wiederherstellung für einen Recovery Services-Tresor | Unterstützt | Unterstützt | Unterstützt | Wird derzeit nicht unterstützt. | Unterstützt |
