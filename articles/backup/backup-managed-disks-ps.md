---
title: Sichern von Azure-Managed Disks über Azure PowerShell
description: Erfahren Sie, wie Sie Azure Managed Disks über das Azure-Portal sichern können.
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aab90bdd5a8ee4ecafd0dbfec81052da6b39f69
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264596"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Sichern von Azure-Managed Disks über Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie [Azure Managed Disk](../virtual-machines/managed-disks-overview.md) überAzure PowerShell sichern.

In diesem Artikel lernen Sie Folgendes:

- Erstellen eines Sicherungstresors

- Erstellen einer Sicherungsrichtlinie

- Konfigurieren der Sicherung eines Azure-Datenträgers

- Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Informationen zur regionalen Verfügbarkeit von Azure Disk Backup, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Speicherentität in Azure, die Sicherungsdaten für verschiedene neuere Workloads enthält, die von Azure Backup unterstützt werden, z. B. Azure Database for PostgreSQL Server und Azure-Datenträger. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

Bevor Sie einen Sicherungstresor erstellen, wählen Sie die Speicherredundanz der Daten im Tresor aus. Fahren Sie dann mit der Erstellung des Sicherungstresors mit dieser Speicherredundanz und dem Speicherort fort. In diesem Artikel wird der Sicherungstresor „TestBkpVault“ in der Region „USA, Westen“ unter der Ressourcengruppe „testBkpVaultRG“ erstellt. Verwenden Sie den Befehl [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true), um einen Sicherungstresor zu erstellen. Erfahren Sie mehr über das [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

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

Nach der Erstellung des Tresors erstellen wir eine Sicherungsrichtlinie zum Schutz der Azure-Datenträger.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Um die inneren Komponenten einer Sicherungsrichtlinie für die Azure-Datenträgersicherung zu verstehen, rufen Sie die Richtlinienvorlage mithilfe des Befehls [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true)ab. Dieser Befehl gibt eine Standardrichtlinienvorlage für einen angegebenen DataSource-Typ zurück. Verwenden Sie diese Richtlinienvorlage, um eine neue Richtlinie zu erstellen.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


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

Die Richtlinienvorlage besteht aus einem Trigger (der entscheidet, was die Sicherung auslöst) und einem Lebenszyklus (der entscheidet, wann die Sicherung gelöscht/kopiert bzw. verschoben werden soll). In Azure Disk Backup ist der Standardwert für „Trigger“ eine geplante 4-stündliche Auslösung (PT4H) und zum Beibehalten der einzelnen Sicherungen für 7 Tage.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure Disk Backup ermöglicht mehrere Sicherungen pro Tag. Wenn Sie häufigere Sicherungen benötigen, wählen Sie die **stündliche** Sicherungshäufigkeit mit der Möglichkeit, Sicherungen mit Intervallen von alle 4, 6, 8 oder 12 Stunden zu erstellen. Die Sicherungen werden basierend auf dem unter **Zeit** ausgewählten Intervall geplant. Wenn Sie z. B. **Alle vier Stunden** auswählen, werden die Sicherungen im Intervall von ungefähr 4 Stunden durchgeführt, sodass die Sicherungen gleichmäßig über den Tag verteilt sind. Wenn eine Sicherung pro Tag ausreichend ist, wählen Sie als Sicherungshäufigkeit **Täglich** aus. Bei der Sicherungshäufigkeit „Täglich“ können Sie die Uhrzeit angeben, zu der die Sicherungen erstellt werden. Es ist wichtig zu beachten, dass mit der Uhrzeit die Startzeit der Sicherung angegeben wird und nicht die Zeit, zu der die Sicherung abgeschlossen ist. Die für den Abschluss des Sicherungsvorgangs erforderliche Zeit hängt von verschiedenen Faktoren ab, einschließlich der Datenträgergröße und des Churns zwischen aufeinanderfolgenden Sicherungen. Azure Disk Backup ist jedoch ein agentloser Sicherungsdienst, der [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) verwendet, die sich nicht auf die Leistung der Anwendung in der Produktion auswirkt.

   >[!NOTE]
   > Obwohl der ausgewählte Tresor möglicherweise die Einstellung für die globale Redundanz aufweist, unterstützt Azure Disk Backup derzeit nur den Momentaufnahmen-Datenspeicher. Alle Sicherungen werden in einer Ressourcengruppe in Ihrem Abonnement gespeichert und nicht in den Sicherungstresorspeicher kopiert.

Weitere Informationen zur Richtlinienerstellung finden Sie unter [Erstellen der Sicherungsrichtlinie für Azure-Datenträger](backup-managed-disks.md#create-backup-policy).

Wenn Sie die stündliche Häufigkeit oder die Beibehaltungsdauer bearbeiten möchten, verwenden Sie die Befehle [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) und/oder [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true). Sobald das Richtlinienobjekt alle gewünschten Werte aufweist, erstellen Sie mit [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true)eine neue Richtlinie aus dem Richtlinienobjekt.

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Nachdem der Tresor und die Richtlinie erstellt wurden, gibt es drei wichtige Punkte, die der Benutzer für den Schutz eines Azure-Datenträgers beachten muss.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="disk-to-be-protected"></a>Zu schützender Datenträger

Rufen Sie die ARM-ID des zu schützenden Datenträgers ab. Dies dient als Bezeichner des Datenträgers. Wir verwenden ein Beispiel für einen Datenträger mit dem Namen „PSTestDisk“ unter der Ressourcengruppe „diskrg“ unter einem anderen Abonnement.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Ressourcengruppe für Momentaufnahme

Die Datenträgermomentaufnahmen werden in einer Ressourcengruppe innerhalb Ihres Abonnements gespeichert. Als Richtlinie wird empfohlen, eine dedizierte Ressourcengruppe als Momentaufnahmen-Datenspeicher zu erstellen, der vom Azure Backup-Dienst verwendet werden soll. Mit einer dedizierten Ressourcengruppe lassen sich die Zugriffsrechte auf die Ressourcengruppe beschränken, was Sicherheit gewährleistet und eine einfache Verwaltung der Sicherungsdaten ermöglicht. Notieren Sie sich die Arm-ID für die Ressourcengruppe, in der Sie die Datenträger-Momentaufnahmen platzieren möchten.

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Sicherungstresor

Die Sicherungs tresore erfordern Berechtigungen auf dem Datenträger und die Momentaufnahme-Ressourcengruppe, um Momentaufnahmen zu erstellen und ihren Lebenszyklus zu verwalten. Die vom System zugewiesene verwaltete Identität des Tresors wird zum Zuweisen solcher Berechtigungen verwendet. Verwenden Sie den Befehl [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true), um die systemseitig zugewiesene verwaltete Identität für den Recovery Services-Tresor zu aktivieren.

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

Der Benutzer muss über RBAC für den Tresor (dargestellt durch die Tresor-MSI) und den relevanten Datenträger und/oder die Datenträger-RG einige Berechtigungen zuweisen. Diese können über das Portal oder PowerShell ausgeführt werden.

Der Sicherungstresor verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Um das Backup von verwalteten Datenträgern zu konfigurieren, erfordert die verwaltete Identität vom Backup Tresor eine Reihe von Berechtigungen für die Quellfestplatten und Ressourcengruppen, auf denen Momentaufnahmen erstellt und verwaltet werden.

Eine vom System zugewiesene verwaltete Identität ist auf eine pro Ressource beschränkt und ist an den Lebenszyklus dieser Ressource gebunden. Sie können der verwalteten Identität mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Berechtigungen erteilen. Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Um das Backup von verwalteten Datenträgern zu konfigurieren, müssen die folgenden Voraussetzungen erfüllt sein:

- Weisen Sie die Rolle **Festplatten-Backup-Leser** der verwalteten Identität vom Backup Tresor auf der Quellfestplatte zu, die gesichert werden muss.

  1. Wechseln Sie zu dem Datenträger, der gesichert werden muss.
  1. Wechseln Sie zur **Zugriffssteuerung (IAM)** , und wählen Sie anschließend **Rollenzuweisung hinzufügen** aus.
  1. Wählen Sie im rechten Kontextbereich **Festplatten-Backup-Leser** in der Dropdown-Liste **Rolle** aus.
  1. Wählen Sie die verwaltete Identität des Backup Tresors aus und klicken Sie auf **Speichern**.
  
     >[!Tip]
     >Geben Sie den Namen des Backup Tresors ein, um die verwaltete Identität des Tresors auszuwählen.

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-inline.png" alt-text="Screenshot, der den Prozess der Zuweisung der Rolle &quot;Festplatten-Backup-Leser&quot; zur verwalteten Identität des Backup Tresors auf der zu sichernden Quellfestplatte zeigt." lightbox="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-expanded.png":::

- Weisen Sie die Rolle **Mitwirkender für Datenträgermomentaufnahmen** der verwalteten Identität des Backup Tresors in der Ressourcengruppe zu, in der Backups erstellt und vom Azure Backup-Dienst verwaltet werden. Die Datenträgermomentaufnahmen werden in einer Ressourcengruppe innerhalb Ihres Abonnements gespeichert. Damit der Azure Backup Dienst Momentaufnahmen erstellen, speichern und verwalten kann, müssen Sie Berechtigungen für den Backup Tresor bereitstellen.

  1. Wechseln Sie zur Ressourcengruppe. Sie Ressourcengruppe ist z. B. _SnapshotRG_ und befindet sich im selben Abonnement wie der zu sichernde Datenträger.
  1. Wechseln Sie zur **Zugriffssteuerung (IAM)** , und wählen Sie anschließend **Rollenzuweisung hinzufügen** aus.
  1. Wählen Sie im rechten Kontextbereich **Mitwirkender für Datenträgermomentaufnahmen** in der Dropdown-Liste **Role** aus. 
  1. Wählen Sie die verwaltete Identität des Backup Tresors und klicken Sie auf **Speichern**.
  
     >[!Tip]
     >Geben Sie den Namen des Sicherungstresors ein, um die verwaltete Identität des Tresors auszuwählen.

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-inline.png" alt-text="Screenshot, der den Prozess der Zuweisung der Rolle Mitwirkender für Datenträgermomentaufnahmen zur verwalteten Identität des Backup Tresors in der Ressourcengruppe zeigt." lightbox="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-expanded.png":::

- Vergewissern Sie sich, dass die verwaltete Identität des Sicherungstresors über den richtigen Satz an Rollenzuweisungen für den Quelldatenträger und die Ressourcengruppe, die als Momentaufnahmen-Datenspeicher verfügt, fungiert.

  1. Gehen Sie zu **Backup Tresor** -> **Identität** und wählen Sie **Azure Rollenzuweisungen**.
 
     :::image type="content" source="./media/backup-managed-disks-ps/select-azure-role-assignments-inline.png" alt-text="Screenshot von der Auswahl der Azure-Rollenzuweisungen." lightbox="./media/backup-managed-disks-ps/select-azure-role-assignments-expanded.png":::

  1. Überprüfen Sie, ob die Rolle, der Ressourcenname und der Ressourcentyp korrekt sind.
 
     :::image type="content" source="./media/backup-managed-disks-ps/verify-role-assignment-details-inline.png" alt-text="Screenshot von der Überprüfung der Rolle, des Ressourcennamens und des Ressourcentyps." lightbox="./media/backup-managed-disks-ps/verify-role-assignment-details-expanded.png":::

>[!Note]
>Während die Rollenzuweisungen im Portal korrekt wiedergegeben werden, kann es etwa 15-30 Minuten dauern, bis die Berechtigung auf die verwaltete Identität des Backup Tresors angewendet wird.

### <a name="prepare-the-request"></a>Vorbereiten der Anforderung

Nachdem alle relevanten Berechtigungen festgelegt wurden, wird die Konfiguration der Sicherung in zwei Schritten ausgeführt. Zuerst bereiten wir die relevante Anforderung mithilfe des entsprechenden Tresors, der Richtlinie, der Festplatte und der Momentaufnahme-Ressourcengruppe mit dem Befehl [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) vor. Anschließend senden wir die Anforderung zum Schutz des Datenträgers mit dem Befehl [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Rufen Sie die relevante Sicherungsinstanz ab, für die der Benutzer eine Sicherung mithilfe von [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) auslösen möchte.

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Sie können eine Aufbewahrungsregel angeben, während Sie die Sicherung auslösen. Navigieren Sie zum Anzeigen der Aufbewahrungsregeln in der Richtlinie durch das Richtlinienobjekt für Aufbewahrungsregeln. Im folgenden Beispiel wird die Regel mit dem Namen „Default“ angezeigt, und wir verwenden diese Regel für die bedarfsgesteuerte Sicherung.

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


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

Löst eine bedarfsgesteuerte Sicherung mithilfe des [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true)-Befehls aus.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Aufträge nachverfolgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch Az.ResourceGraph verwenden, um alle Aufträge für alle Sicherungstresore zu überprüfen. Verwenden Sie den Befehl [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true), um den relevanten Auftrag zu erhalten, der in jedem Sicherungstresor vorhanden sein kann.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellen von Azure-Managed Disks über Azure PowerShell](restore-managed-disks-ps.md)