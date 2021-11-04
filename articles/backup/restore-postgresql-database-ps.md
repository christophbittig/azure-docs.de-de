---
title: Wiederherstellen von Azure PostgreSQL-Datenbanken über Azure PowerShell
description: Erfahren Sie, wie Sie Azure PostgreSQL-Datenbanken mithilfe von Azure PowerShell wiederherstellen.
ms.topic: conceptual
ms.date: 03/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 6b271f823d8c0547a0fd48861e463bfcc1513e97
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095619"
---
# <a name="restore-azure-postgresql-databases-using-azure-powershell"></a>Wiederherstellen von Azure PostgreSQL-Datenbanken mithilfe von Azure PowerShell

In diesem Artikel wird erläutert, wie Sie von Azure Backup gesicherte [Azure PostgreSQL-Datenbanken](/azure/postgresql/overview#azure-database-for-postgresql---single-server) auf einem Azure PostgreSQL-Server wiederherstellen.

Als PaaS-Datenbank wird die Option „Wiederherstellung am ursprünglichen Speicherort“ (Original-Location Recovery, OLR) nicht unterstützt. Bei dieser Wiederherstellungsoption wird die vorhandene Datenbank, von der die Sicherungen erstellt wurde, ersetzt. Sie können die Wiederherstellung von einem Wiederherstellungspunkt aus ausführen, um eine neue Datenbank auf demselben Azure PostgreSQL-Server oder einem anderen PostgreSQL-Server zu erstellen. Dies wird als „Wiederherstellung an einem alternativen Speicherort“ (Alternate-Location Recovery, ALR) bezeichnet, bei der sowohl die Quelldatenbank als auch die wiederhergestellte (neue) Datenbank erhalten bleiben.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen zum Erstellen einer neuen PostgreSQL-Datenbank

- Nachverfolgen des Status des Wiederherstellungsvorgangs

In den Beispielen wird auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_ verwiesen.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-postgresql-database"></a>Wiederherstellen zum Erstellen einer neuen PostgreSQL-Datenbank

### <a name="set-up-permissions"></a>Einrichten von Berechtigungen

Der Sicherungstresor verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Für die Wiederherstellung aus einer Sicherung erfordert die verwaltete Identität des Sicherungstresors einen Satz von Berechtigungen auf dem Azure PostgreSQL-Server, auf dem die Datenbank wiederhergestellt werden soll.

Informationen zum Zuweisen der relevanten Berechtigungen für die systemseitig zugewiesene verwaltete Identität des Tresors auf dem PostgreSQL-Zielserver finden Sie unter [Satz der erforderlichen Berechtigungen zum Sichern der Azure PostgreSQL-Datenbank](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore).

Um den Wiederherstellungspunkt als Dateien in einem Speicherkonto wiederherzustellen, [benötigt die systemseitig zugewiesene verwaltete Identität des Sicherungstresors Zugriff auf das Zielspeicherkonto](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account).

### <a name="fetching-the-relevant-recovery-point"></a>Der relevante Wiederherstellungspunkt wird abgerufen.

Rufen Sie mithilfe des Befehls [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) alle Instanzen ab, und identifizieren Sie die relevante Instanz.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Sie können auch **Az.Resourcegraph** und den Befehl [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) verwenden, um Wiederherstellungspunkte über Instanzen in vielen Tresoren und Abonnements hinweg zu durchsuchen.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured
```

Um die Suchkriterien zu filtern, verwenden Sie die PowerShell-Clientsuchfunktionen, wie unten dargestellt:

```azurepowershell-interactive
Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured | Where-Object { $_.BackupInstanceName -match "empdb11"}
```

Rufen Sie den entsprechenden Wiederherstellungspunkt ab, sobald die Instanz identifiziert wurde.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

Wenn Sie den Wiederherstellungspunkt aus der Archivebene abrufen müssen, fügen Sie wie folgt einen Clientfilter hinzu:

```azurepowershell-interactive
Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName | Where-Object {$_.Property.RecoveryPointDataStoresDetail[0].Type -match "Archive" }
```

### <a name="prepare-the-restore-request"></a>Vorbereiten der Wiederherstellungsanforderung

Es gibt verschiedene Wiederherstellungsoptionen für eine PostgreSQL-Datenbank. Sie können den Wiederherstellungspunkt als andere Datenbank oder als Dateien wiederherstellen. Der Wiederherstellungspunkt kann sich auch auf der Archivebene befinden.

#### <a name="restore-as-database"></a>Wiederherstellen als Datenbank

Erstellen Sie die ARM-ID (Azure Resource Manager-ID) der neuen PostgreSQL-Datenbank, die erstellt werden soll (mit dem PostgreSQL-Zielserver dem wie [oben](#set-up-permissions) beschrieben Berechtigungen zugewiesen wurden), sowie den Namen der erforderlichen PostgreSQL-Datenbank. Beispielsweise kann eine PostgreSQL-Datenbank unter einem PostgreSQL-Zielserver **targetossserver** in der Ressourcengruppe **targetrg** mit einem anderen Abonnement **emprestored21** genannt werden.

```azurepowershell-interactive
$targetOssId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21
```

Verwenden Sie den Befehl [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true), um die Wiederherstellungsanforderung mit allen relevanten Details vorzubereiten.

```azurepowershell-interactive
$OssRestoreReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault
```

Für einen archivbasierten Wiederherstellungspunkt müssen Sie folgende Schritte ausführen:

1. Reaktivieren aus dem Archivdatenspeicher in den Tresorspeicher.
1. Ändern Sie den Quelldatenspeicher.
1. Fügen Sie weitere Parameter hinzu, um die Reaktivierungspriorität anzugeben.
1. Geben Sie an, für welche Dauer der reaktivierten Wiederherstellungspunkt im Tresordatenspeicher aufbewahrt werden soll.
1. Führen Sie die Wiederherstellung als Datenbank von diesem Wiederherstellungspunkt aus.

Verwenden Sie den folgenden Befehl, um die Anforderung für alle oben genannten Vorgänge gleichzeitig vorzubereiten.

```azurepowershell-interactive
$OssRestoreFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault -RehydrationDuration 12 -RehydrationPriority Standard
```

#### <a name="restore-as-files"></a>Wiederherstellen als Dateien

Rufen Sie den URI des Containers innerhalb des Speicherkontos ab, dem wie [oben](#set-up-permissions) beschrieben Berechtigungen zugewiesen wurden. Beispiel: Ein Container namens **testcontainerrestore** unter einem Speicherkonto **testossstorageaccount** mit einem anderen Abonnement.

```azurepowershell-interactive
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Verwenden Sie den Befehl [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true), um die Wiederherstellungsanforderung mit allen relevanten Details vorzubereiten.

```azurepowershell-interactive
$OssRestoreAsFilesReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" 
```

Ändern Sie für einen archivbasierten Wiederherstellungspunkt den Quelldatenspeicher, und fügen Sie die Reaktivierungspriorität und die Aufbewahrungsdauer des reaktivierten Wiederherstellungspunkts in Tagen hinzu, wie unten erwähnt:

```azurepowershell-interactive
$OssRestoreAsFilesFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" -RehydrationDuration "14" -RehydrationPriority Standard
```

### <a name="trigger-the-restore"></a>Auslösen des Wiederherstellungsvorgangs

Verwenden Sie den Befehl [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true), um die Wiederherstellung mit der oben vorbereiteten Anforderung zu initiieren.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $OssRestoreReq
```

## <a name="tracking-job"></a>Aufträge nachverfolgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch *Az.ResourceGraph* verwenden, um sämtliche Aufträge für alle Sicherungstresore nachzuverfolgen. Verwenden Sie den Befehl [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true), um den relevanten Auftrag über den gesamten Sicherungstresor hinweg zu erhalten.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -Operation OnDemandBackup
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure PostgreSQL-Sicherung](backup-azure-database-postgresql-overview.md)
