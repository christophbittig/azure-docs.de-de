---
title: Sichern von Azure Database for PostgreSQL mit langfristiger Aufbewahrung mithilfe von Azure PowerShell
description: Erfahren Sie, wie Sie Azure Database for PostgreSQL mithilfe von Azure PowerShell sichern.
ms.topic: conceptual
ms.author: v-amallick
ms.date: 10/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 47beb64c518bbd55090c9cf6cd50068635310d25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096141"
---
# <a name="back-up-azure-postgresql-databases-using-azure-powershell"></a>Sichern von Azure PostgreSQL-Datenbanken mithilfe von Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie eine [Azure PostgreSQL-Datenbank](../postgresql/overview.md#azure-database-for-postgresql---single-server) mit Azure PowerShell sichern.

In diesem Artikel lernen Sie Folgendes:

- Erstellen eines Sicherungstresors

- Erstellen einer Sicherungsrichtlinie

- Konfigurieren einer Sicherung einer Azure PostgreSQL-Datenbank

- Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Informationen zu Azure PostgreSQL-Datenbanken, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](backup-azure-database-postgresql-overview.md#support-matrix).

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Speicherentität in Azure, in der die Sicherungsdaten für verschiedene neue Workloads gespeichert sind, die von Azure Backup unterstützt werden, z. B. Azure Database for PostgreSQL-Server, Azure-Datenträger und Azure-Blobs. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

Bevor Sie einen Sicherungstresor erstellen, wählen Sie die Speicherredundanz der Daten im Tresor aus. Fahren Sie dann mit der Erstellung des Sicherungstresors mit dieser Speicherredundanz und dem Speicherort fort.

In diesem Artikel wird der Sicherungstresor *TestBkpVault* in der Region *westus* unter der Ressourcengruppe *testBkpVaultRG* erstellt. Verwenden Sie den Befehl [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true), um einen Sicherungstresor zu erstellen. Weitere Informationen finden Sie unter [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Erstellen Sie nach dem Erstellen des Tresors eine Sicherungsrichtlinie, um Azure PostgreSQL-Datenbanken zu schützen.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

### <a name="understand-postgresql-backup-policy"></a>Grundlegendes zur PostgreSQL-Sicherungsrichtlinie

Während die Datenträgersicherung mehrere Sicherungen pro Tag bietet und die Blobsicherung eine *fortlaufende* Sicherung ohne Trigger ist, bietet die PostgreSQL-Sicherung Archivschutz. Die Sicherungsdaten, die zuerst an den Tresor gesendet werden, können gemäß einer definierten Regel oder einem *Lebenszyklus* auf die *Archivebene* verschoben werden. Lassen Sie uns das Sicherungsrichtlinienobjekt für PostgreSQL in diesem Kontext anschauen.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (in diesem Fall eine vollständige Datenbanksicherung)
         -  Anfänglicher Datenspeicher (Wo werden die Sicherungen anfänglich gespeichert?)
         -  Trigger (Wie wird die Sicherung ausgelöst?)
            -  Basierend auf dem Zeitplan
            -  Standardmäßige Taggingkriterien (ein Standardtag für alle geplanten Sicherungen, das die Sicherungen mit der Aufbewahrungsregel verknüpft)
   -  Standardaufbewahrungsregel (eine Regel, die standardmäßig auf alle Sicherungen im anfänglichen Datenspeicher angewendet wird)

Dieses Objekt definiert also, welche Art von Sicherungen ausgelöst werden, wie sie ausgelöst werden (über einen Zeitplan), welche Tags sie enthalten, wo sie abgelegt werden (ein Datenspeicher) und den Lebenszyklus der Sicherungsdaten in einem Datenspeicher. Das PowerShell-Standardobjekt für PostgreSQL gibt an, dass jede Woche eine *vollständige* Sicherung ausgelöst werden soll. Diese erreichen den Tresor, in dem sie drei Monate lang gespeichert werden.

Wenn Sie der Richtlinie die *Archivebene* hinzufügen möchten, müssen Sie entscheiden, wann die Daten aus dem Tresor in das Archiv verschoben werden, wie lange die Daten im Archiv verbleiben und welche der geplanten Sicherungen mit dem *Tag* _archivierbar_ versehen werden sollen. Daher müssen Sie eine *Aufbewahrungsregel* hinzufügen, bei der der Lebenszyklus der Sicherungsdaten vom Tresordatenspeicher zum Archivdatenspeicher definiert und festgelegt wird, wie lange die Daten im *Archivdatenspeicher* aufbewahrt werden. Anschließend müssen Sie ein *Tag* hinzufügen, das die geplanten Sicherungen als _zur Archivierung berechtigt_ markiert.

Das resultierende PowerShell-Objekt sieht wie folgt aus:


-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (in diesem Fall eine vollständige Datenbanksicherung)
         -  Anfänglicher Datenspeicher (Wo werden die Sicherungen anfänglich gespeichert?)
         -  Trigger (Wie wird die Sicherung ausgelöst?)
            -  Basierend auf dem Zeitplan
            -  Standardmäßige Taggingkriterien (ein Standardtag für alle geplanten Sicherungen, das die Sicherungen mit der Aufbewahrungsregel verknüpft)
            -  Neue Taggingkriterien für die neue Aufbewahrungsregel mit dem gleichen Namen „X“
   -  Standardaufbewahrungsregel (eine Regel, die standardmäßig auf alle Sicherungen im anfänglichen Datenspeicher angewendet wird)
   -  Eine neue Aufbewahrungsregel namens „X“
      -  Lebenszyklus
         -  Quelldatenspeicher
         -  „Löschen nach“-Zeitraum im Quelldatenspeicher
         -  Kopieren in den Zieldatenspeicher

### <a name="retrieving-the-policy-template"></a>Abrufen der Richtlinienvorlage

Um die inneren Komponenten einer Sicherungsrichtlinie für eine Azure PostgreSQL-Datenbanksicherung zu verstehen, rufen Sie die Richtlinienvorlage mit dem Befehl [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true) ab. Dieser Befehl gibt eine Standardrichtlinienvorlage für einen angegebenen DataSource-Typ zurück. Verwenden Sie diese Richtlinienvorlage, um eine neue Richtlinie zu erstellen.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDatabaseForPostgreSQL
$policyDefn | fl


DatasourceType : {Microsoft.DBforPostgreSQL/servers/databases}
ObjectType     : BackupPolicy
PolicyRule     : {BackupWeekly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : VaultStore
Name                      : BackupWeekly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.ScheduleBasedTriggerCo
                            ntext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Die Richtlinienvorlage besteht aus einem Trigger (der entscheidet, was die Sicherung auslöst) und einem Lebenszyklus (der entscheidet, wann die Sicherung gelöscht/kopiert bzw. verschoben werden soll). Bei der Azure PostgreSQL-Datenbanksicherung wird standardmäßig ein geplanter wöchentlicher Trigger (1 Sicherung alle 7 Tage) verwendet, und jede Sicherung wird für drei Monate aufbewahrt.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2021-08-22T02:00:00+00:00/P1W}
ScheduleTimeZone              : UTC
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P3M
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : VaultStore
TargetDataStoreCopySetting : {}
```

### <a name="modify-the-policy-template"></a>Ändern der Richtlinienvorlage

#### <a name="modify-the-schedule"></a>Ändern des Zeitplans

Die Standardrichtlinienvorlage bietet einmal pro Woche eine Sicherung. Sie können den Zeitplan für die Sicherung auf mehrere Tage pro Woche ändern. Verwenden Sie zum Ändern des Zeitplans den Befehl [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-6.5.0&preserve-view=true).

Im folgenden Beispiel wird die wöchentliche Sicherung so geändert, dass die Sicherung jede Woche an jedem Sonntag, Mittwoch und Freitag erfolgt. Das Datumsarray des Zeitplans enthält die Datumsangaben, und die Wochentage dieser Datumsangaben werden als Tage der Woche betrachtet. Sie müssen auch angeben, dass diese Zeitpläne jede Woche wiederholt werden sollen. Daher ist das Zeitplanintervall „1“, und der Intervalltyp ist „Wöchentlich“.

```azurepowershell-interactive
$schDates = @(
    (
        (Get-Date -Year 2021 -Month 08 -Day 15 -Hour 22 -Minute 0 -Second 0)
    ), 
    (
        (Get-Date -Year 2021 -Month 08 -Day 18 -Hour 22 -Minute 0 -Second 0)
    ),
  (
        (Get-Date -Year 2021 -Month 08 -Day 20 -Hour 22 -Minute 0 -Second 0)
    )
)
$trigger = New-AzDataProtectionPolicyTriggerScheduleClientObject -ScheduleDays $schDates -IntervalType Weekly -IntervalCount 1 
Edit-AzDataProtectionPolicyTriggerClientObject -Schedule $trigger -Policy $policyDefn   
```

#### <a name="add-a-new-retention-rule"></a>Hinzufügen einer neuen Aufbewahrungsregel

Wenn Sie also den _Archivschutz_ hinzufügen möchten, müssen Sie die Richtlinienvorlage wie folgt ändern.

Die Standardvorlage verfügt über einen Lebenszyklus für den anfänglichen Datenspeicher unter der Standardaufbewahrungsregel. In diesem Szenario besagt die Regel, dass die Sicherungsdaten nach drei Monaten gelöscht werden sollen. Sie sollten eine neue Aufbewahrungsregel hinzufügen, die definiert, wann die Daten in den *Archivdatenspeicher* *verschoben* werden. Das heißt, die Sicherungsdaten werden zuerst in den Archivdatenspeicher kopiert und dann im Tresordatenspeicher gelöscht. Außerdem sollte die Regel definieren, wie lange die Daten im *Archivdatenspeicher* aufbewahrt werden. Verwenden Sie den Befehl [New-AzDataProtectionRetentionLifeCycleClientObject](/powershell/module/az.dataprotection/new-azdataprotectionretentionlifecycleclientobject?view=azps-6.5.0&preserve-view=true), um neue Lebenszyklen zu erstellen, und verwenden Sie den Befehl [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-6.5.0&preserve-view=true), um sie den neuen Regeln oder vorhandenen Regeln zu zuordnen.

Im folgenden Beispiel wird eine neue Aufbewahrungsregel mit dem Namen *Monatlich* erstellt, bei der die erste erfolgreiche Sicherung jedes Monats sechs Monate lang im Tresor aufbewahrt, dann in die Archivebene verschoben und 24 Monate lang in der Archivebene aufbewahrt werden soll.

```azurepowershell-interactive
$VaultToArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore VaultStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 6 -TargetDataStore ArchiveStore -CopyOption CopyOnExpiryOption

$OnArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore ArchiveStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 24

Edit-AzDataProtectionPolicyRetentionRuleClientObject -Policy $policyDefn -Name Monthly -LifeCycles $VaultToArchiveLifeCycle, $OnArchiveLifeCycleLifeCycle -IsDefault $false
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>Hinzufügen eines Tags und der relevanten Kriterien

Nachdem eine Aufbewahrungsregel erstellt wurde, müssen Sie ein entsprechendes *Tag* in der *Trigger*-Eigenschaft der Sicherungsrichtlinie erstellen. Verwenden Sie den Befehl [New-AzDataProtectionPolicyTagCriteriaClientObject](/powershell/module/az.dataprotection/new-azdataprotectionpolicytagcriteriaclientobject?view=azps-6.5.0&preserve-view=true), um neue Taggingkriterien zu erstellen, und verwenden Sie den Befehl [Edit-AzDataProtectionPolicyTagClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytagclientobject?view=azps-6.5.0&preserve-view=true), um das vorhandene Tag zu aktualisieren oder ein neues Tag zu erstellen.

Im folgenden Beispiel wird ein neues *Tag* mit den Kriterien (welches die erste erfolgreiche Sicherung des Monats ist) und dem gleichen Namen wie die entsprechende Aufbewahrungsregel erstellt, die angewendet werden soll.

In diesem Beispiel sollte als Taggingkriterium *Monatlich* verwendet werden.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -AbsoluteCriteria FirstOfMonth
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

Wenn der Zeitplan aus mehreren Sicherungen pro Woche besteht (jeden Sonntag, Mittwoch, Donnerstag, wie im obigen Beispiel angegeben), und Sie die Sicherungen von Sonntag und Freitag archivieren möchten, kann das Taggingkriterium wie folgt geändert werden:

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -DaysOfWeek @("Sunday", "Friday")
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

### <a name="create-a-new-postgresql-backup-policy"></a>Erstellen einer neuen PostgreSQL-Sicherungsrichtlinie

Nachdem die Vorlage den Anforderungen entsprechend geändert wurde, verwenden Sie den Befehl [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-6.5.0&preserve-view=true), um eine Richtlinie unter Verwendung der geänderten Vorlage zu erstellen.

```azurepowershell-interactive
$polOss = New-AzDataProtectionBackupPolicy -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Name "TestOSSPolicy" -Policy $policyDefn
```

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Nach dem Erstellen des Tresors und der Richtlinie gibt es drei wichtige Punkte, die Sie für den Schutz einer Azure PostgreSQL-Datenbank beachten müssen.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="postgresql-database-to-be-protected"></a>Zu schützende PostgreSQL-Datenbank

Rufen Sie die Azure Resource Manager-ID (ARM-ID) von der zu schützenden PostgreSQL-Instanz ab. Dies dient als Bezeichner der Datenbank. Wir verwenden ein Beispiel für eine Datenbank namens **empdb11** unter einem PostgreSQL-Server **testposgresql** in der Ressourcengruppe **ossrg** unter einem anderen Abonnement.

```azurepowershell-interactive
$ossId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Der Azure Backup-Dienst speichert den Benutzernamen und das Kennwort nicht, um eine Verbindung mit der PostgreSQL-Datenbank herzustellen. Stattdessen werden die *Schlüssel* vom Sicherungsadministrator per Seeding im Schlüsseltresor gespeichert. Anschließend kann der Sicherungsdienst auf den Schlüsseltresor zugreifen, die Schlüssel lesen und auf die Datenbank zugreifen. Notieren Sie sich den Geheimnisbezeichner des relevanten Schlüssels.

```azurepowershell-interactive
$keyURI = "https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Sicherungstresor

Sie müssen Backup Vault mit dem PostgreSQL-Server verbinden und dann über die im Key Vault vorhandenen Schlüssel auf die Datenbank zugreifen. Daher ist ein Zugriff auf den PostgreSQL-Server und den Key Vault erforderlich. Es wird Zugriff auf die MSI des Sicherungstresors gewährt.

[Lesen Sie die entsprechenden Berechtigungen](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup), die Sie dem MSI des Sicherungstresors auf dem PostgreSQL-Server und dem Azure-Schlüsseltresor, in dem die Schlüssel für die Datenbank gespeichert sind, gewähren sollten.

### <a name="prepare-the-request"></a>Vorbereiten der Anforderung

Nachdem alle relevanten Berechtigungen festgelegt wurden, führen Sie die Konfiguration der Sicherung in zwei Schritten aus.

1. Die relevante Anforderung wird mithilfe des entsprechenden Tresors, der entsprechenden Richtlinie und der entsprechenden PostgreSQL-Datenbank mit dem Befehl [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) vorbereitet.
1. Die Anforderung zum Schutz der Datenbank wird mit dem Befehl [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) gesendet.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDatabaseForPostgreSQL -DatasourceLocation $TestBkpvault.Location -PolicyId $polOss[0].Id -DatasourceId $ossId -SecretStoreURI $keyURI -SecretStoreType AzureKeyVault
ConvertTo-Json -InputObject $instance -Depth 4 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                        Type                                         BackupInstanceName
----                        ----                                          ------------------
ossrg-empdb11       Microsoft.DataProtection/backupVaults/backupInstances ossrg-empdb11
```

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Rufen Sie mit dem Befehl [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) die relevante Sicherungsinstanz ab, für die eine Sicherung ausgelöst werden muss.

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Sie können eine Aufbewahrungsregel angeben, während Sie die Sicherung auslösen. Navigieren Sie zum Anzeigen der Aufbewahrungsregeln in der Richtlinie durch das Richtlinienobjekt für Aufbewahrungsregeln. Im folgenden Beispiel wird die Regel mit dem Namen *default* angezeigt. Diese Regel wird für die bedarfsgesteuerte Sicherung verwendet.

```azurepowershell-interactive
$ossPol.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

## <a name="trigger-an-on-demand-backup"></a>Auslösen einer bedarfsgesteuerten Sicherung

So lösen Sie eine bedarfsgesteuerte Sicherung mithilfe des Befehls [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) aus

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="track-jobs"></a>Nachverfolgen von Aufträgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) nach. Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch _Az.ResourceGraph_ verwenden, um alle Aufträge für alle Sicherungstresore zu überprüfen. Verwenden Sie den Befehl [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true), um die relevanten Aufträge aus allen Sicherungstresoren abzurufen.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen einer Azure PostgreSQL-Datenbank mithilfe von Azure PowerShell](restore-managed-disks-ps.md)
