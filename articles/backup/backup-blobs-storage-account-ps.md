---
title: Sichern von Azure-Blobs in einem Speicherkonto mithilfe von Azure PowerShell
description: Erfahren Sie, wie Sie alle Azure-Blobs in einem Speicherkonto mithilfe von Azure PowerShell sichern.
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: 0f3e7d03abbf4a5bfbb5d5cb533df04a17c71edf
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350456"
---
# <a name="back-up-all-azure-blobs-in-a-storage-account-using-azure-powershell"></a>Sichern aller Azure-Blobs in einem Speicherkonto mithilfe von Azure PowerShell

Dieser Artikel bietet Informationen zum Sichern aller [Azure-Blobs](./blob-backup-overview.md) innerhalb eines Speicherkontos mithilfe von Azure PowerShell.

In diesem Artikel lernen Sie Folgendes:

- Vorbereitung

- Erstellen eines Sicherungstresors

- Erstellen einer Sicherungsrichtlinie

- Konfigurieren einer Sicherung aller Azure-Blobs in Speicherkonten

Informationen zur regionalen Verfügbarkeit von Azure-Blobs, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](blob-backup-support-matrix.md).

> [!IMPORTANT]
> Unterstützung für Azure-Blobs ist ab Version Az 5.9.0 verfügbar.

## <a name="before-you-start"></a>Vorbereitung

Sehen Sie sich die [Voraussetzungen](./blob-backup-configure-manage.md#before-you-start) und die [Supportmatrix](./blob-backup-support-matrix.md) an, bevor Sie beginnen.

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Speicherentität in Azure, die Sicherungsdaten für verschiedene neuere Workloads enthält, die von Azure Backup unterstützt werden, z. B. Azure Database for PostgreSQL-Server und Azure-Blobs. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

Bevor Sie einen Sicherungstresor erstellen, wählen Sie die Speicherredundanz der Daten im Tresor aus. Fahren Sie dann mit der Erstellung des Sicherungstresors mit dieser Speicherredundanz und dem Speicherort fort. In diesem Artikel wird der Sicherungstresor _TestBkpVault_ in der Region _westus_ unter der Ressourcengruppe _testBkpVaultRG_ erstellt. Verwenden Sie den Befehl [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.9.0&preserve-view=true), um einen Sicherungstresor zu erstellen. Erfahren Sie mehr über das [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

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

Nach der Erstellung des Tresors erstellen wir eine Sicherungsrichtlinie zum Schutz der Azure-Blobs.

> [!IMPORTANT]
> Obwohl die Sicherungsspeicherredundanz des Tresors angezeigt wird, gilt die Redundanz nicht für die operative Sicherung von Blobs, weil die Sicherung lokal erfolgt und keine Daten im Sicherungstresor gespeichert werden. Hier ist der Sicherungstresor die Verwaltungsentität, die Sie bei der Verwaltung des Schutzes von Blockblobs in Ihren Speicherkonten unterstützt.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

> [!IMPORTANT]
> Lesen Sie [diesen Abschnitt](blob-backup-configure-manage.md#before-you-start), bevor Sie mit der Erstellung der Richtlinie und dem Konfigurieren von Sicherungen für Azure-Blobs fortfahren.

Um die einzelnen Komponenten einer Sicherungsrichtlinie für eine Azure-Blobsicherung zu verstehen, rufen Sie die Richtlinienvorlage mit dem Befehl [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.9.0&preserve-view=true) ab. Dieser Befehl gibt eine Standardrichtlinienvorlage für einen angegebenen DataSource-Typ zurück. Verwenden Sie diese Richtlinienvorlage, um eine neue Richtlinie zu erstellen.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureBlob
$policyDefn | fl


DatasourceType : {Microsoft.Storage/storageAccounts/blobServices}
ObjectType     : BackupPolicy
PolicyRule     : {Default}

$policyDefn.PolicyRule | fl

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api202101.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Die Richtlinienvorlage umfasst einen Lebenszyklus (der entscheidet, wann die Sicherung gelöscht/kopiert bzw. verschoben werden soll). Da die operative Sicherung für Blobs fortlaufend erfolgt, benötigen Sie keinen Zeitplan für die Durchführung von Sicherungen.

```azurepowershell-interactive
$policyDefn.PolicyRule.Lifecycle | fl


DeleteAfterDuration        : P30D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

> [!NOTE]
> Eine Wiederherstellung über einen längeren Zeitraum kann dazu führen, dass die Wiederherstellungsvorgänge länger dauern. Außerdem hängt die erforderliche Zeit zum Wiederherstellen einer Gruppe von Daten von der Anzahl der Schreib- und Löschvorgänge ab, die während des Wiederherstellungszeitraums vorgenommen werden. Beispielsweise dauert bei einem Konto mit 1 Million Objekten, in dem pro Tag 3.000 Objekte hinzugefügt und 1.000 Objekte gelöscht werden, die Wiederherstellung zu einem 30 Tage in der Vergangenheit liegenden Zeitpunkt ungefähr zwei Stunden.<br><br>Für ein Konto mit einer solchen Änderungsrate empfiehlt sich weder ein Aufbewahrungszeitraum von mehr als 90 Tagen noch eine Wiederherstellung auf einen Zeitpunkt, der mehr als 90 Tage in der Vergangenheit liegt.

Sobald das Richtlinienobjekt alle gewünschten Werte aufweist, erstellen Sie mit [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.9.0&preserve-view=true) eine neue Richtlinie aus dem Richtlinienobjekt.

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name blobBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
blobBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$blobBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "blobBkpPolicy"
```

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Sobald der Tresor und die Richtlinie erstellt wurden, gibt es drei wichtige Punkte, die der Benutzer beim Schutz aller Azure-Blobs beachten muss.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>Speicherkonto, das die zu schützenden Blobs enthält

Rufen Sie die Azure Resource Manager-ID des Speicherkontos ab, das die zu schützenden Blobs enthält. Diese ID dient als Bezeichner des Speicherkontos. Wir verwenden ein Beispiel für ein Speicherkonto namens _PSTestSA_ unter der Ressourcengruppe _blobrg_ in einem anderen Abonnement.

```azurepowershell-interactive
$SAId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/PSTestSA"
```

#### <a name="backup-vault"></a>Sicherungstresor

Der Sicherungstresor benötigt Berechtigungen für das Speicherkonto, um Sicherungen für Blobs im Speicherkonto zu aktivieren. Die vom System zugewiesene verwaltete Identität des Tresors wird zum Zuweisen solcher Berechtigungen verwendet.

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

Sie müssen dem Tresor (dargestellt durch die Tresor-MSI) und dem relevanten Speicherkonto über RBAC einige Berechtigungen zuweisen. Diese können über das Portal oder PowerShell ausgeführt werden. Erfahren Sie mehr über alle [zugehörigen Berechtigungen](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts).

### <a name="prepare-the-request"></a>Vorbereiten der Anforderung

Nachdem alle relevanten Berechtigungen festgelegt wurden, wird die Konfiguration der Sicherung in zwei Schritten ausgeführt. Zuerst bereiten wir die relevante Anforderung mithilfe des entsprechenden Tresors, der Richtlinie und des Speicherkontos mit dem Befehl [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) vor. Anschließend senden wir die Anforderung zum Schutz der Blobs innerhalb des Speicherkontos mit dem Befehl [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true).

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureBlob -DatasourceLocation $TestBkpvault.Location -PolicyId $blobBkpPol[0].Id -DatasourceId $SAId 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166
```

> [!IMPORTANT]
> Wenn ein Speicherkonto für die Blobsicherung konfiguriert wird, beeinflusst dies einige Funktionen, z. B. Änderungsfeed und Löschsperre. [Weitere Informationen](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Blobs mithilfe von Azure PowerShell](restore-blobs-storage-account-ps.md)