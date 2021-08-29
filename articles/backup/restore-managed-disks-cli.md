---
title: Wiederherstellen von Azure-Managed Disks über Azure CLI
description: Erfahren Sie, wie Sie Azure Managed Disks mithilfe der Azure CLI wiederherstellen können.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 87ca3597b7ad73aa1ac0ba5cff6c75b5ec880750
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287500"
---
# <a name="restore-azure-managed-disks-using-azure-cli"></a>Wiederherstellen von Azure-Managed Disks mithilfe der Azure CLI

In diesem Artikel wird beschrieben, wie Sie [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) aus einem von Azure Backup erstellten Wiederherstellungspunkt mithilfe der Azure CLI wiederherstellen.

> [!IMPORTANT]
> Die Unterstützung für Azure Managed Disks-Sicherungen und -Wiederherstellungen über die Befehlszeilenschnittstelle befindet sich in der Vorschau und ist als Erweiterung in Az 2.15.0-Version und höher verfügbar. Die Erweiterung wird automatisch installiert, wenn Sie die **az dataprotection**-Befehle ausführen. [Weitere Informationen zu Erweiterungen](/cli/azure/azure-cli-extensions-overview)

Derzeit wird die Wiederherstellungsoption „Wiederherstellung am ursprünglichen Speicherort“ nicht unterstützt, bei der die Wiederherstellung den vorhandenen Quelldatenträger ersetzt, von dem aus die Sicherungen erstellt wurde. Sie können eine Wiederherstellung aus einem Wiederherstellungspunkt vornehmen, um einen neuen Datenträger in derselben Ressourcengruppe des Quelldatenträgers oder in einer anderen Ressourcengruppe zu erstellen. Dies wird als Wiederherstellung an einem alternativen Speicherort (Alternate-Location Recovery, ALR) bezeichnet.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen zum Erstellen eines neuen Datenträgers

- Nachverfolgen des Status des Wiederherstellungsvorgangs

In den Beispielen wird auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_ verwiesen.

## <a name="restore-to-create-a-new-disk"></a>Wiederherstellen zum Erstellen eines neuen Datenträgers

### <a name="setting-up-permissions"></a>Einrichten von Berechtigungen

Der Sicherungstresor verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Um die Wiederherstellung aus einer Sicherung vornehmen, erfordert die verwaltete Identität des Sicherungstresors einen Satz von Berechtigungen für die Ressourcengruppe, in der der Datenträger wiederhergestellt werden soll.

Der Sicherungstresor verwendet eine systemseitig zugewiesene verwaltete Identität, die auf eine Ressource beschränkt und an den Lebenszyklus dieser Ressource gebunden ist. Sie können der verwalteten Identität mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) Berechtigungen erteilen. Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Weisen Sie die relevanten Berechtigungen für die systemseitig zugewiesene verwaltete Identität des Tresors der Zielressourcengruppe zu, in der die Datenträger wieder hergestellt/erstellt werden, wie [hier](restore-managed-disks.md#restore-to-create-a-new-disk)erwähnt.

### <a name="fetching-the-relevant-recovery-point"></a>Der relevante Wiederherstellungspunkt wird abgerufen.

Listen Sie alle Sicherungsinstanzen innerhalb eines Tresors mit dem Befehl [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) auf, und rufen Sie dann die relevante Instanz mit dem Befehl [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) ab. Alternativ können Sie für Szenarien mit großem Umfang Sicherungsinstanzen tresor- und abonnementübergreifend auflisten, indem Sie [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) verwenden.

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Compute/disks",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
        "resourceLocation": "westus",
        "resourceName": "CLITestDisk",
        "resourceType": "Microsoft.Compute/disks",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestDisk",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
            }
          ]
        },
        "policyVersion": null
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]


```

Nachdem die Instanz identifiziert wurde, rufen Sie den relevanten Wiederherstellungspunkt mit dem Befehl [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list) ab.

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493",
"name": "5081ad8f1e6c4548ae89536d0d45c493",
"properties": {
"friendlyName": "0f598ced-cbfe-4169-b962-ee94b0210490",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T09:01:57.708319+00:00",
"expiryTime": "2021-06-15T09:01:57.708319+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "5081ad8f1e6c4548ae89536d0d45c493",
"recoveryPointTime": "2021-06-08T09:01:57.708319+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
},
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/039322cc563049bcbdb77bd695d4c02c",
"name": "039322cc563049bcbdb77bd695d4c02c",
"properties": {
"friendlyName": "af6512b6-aa38-4966-b8e1-660c4eccdc0d",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T05:01:55.426507+00:00",
"expiryTime": "2021-06-15T05:01:55.426507+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "039322cc563049bcbdb77bd695d4c02c",
"recoveryPointTime": "2021-06-08T05:01:55.426507+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
]
```

Die folgende Abfrage gibt beispielsweise den letzten Wiederherstellungspunkt zurück.

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault --query "[0].id"

"/subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493"
```

### <a name="preparing-the-restore-request"></a>Die Wiederherstellungsanforderung wird vorbereitet

Erstellen Sie die ARM-ID des neuen Datenträgers, der mit der Zielressourcengruppe erstellt werden soll, dem die Berechtigungen wie [oben](#setting-up-permissions)beschrieben zugewiesen wurden, und den erforderlichen Datenträgernamen. Wir verwenden ein Beispiel für einen Datenträger mit dem Namen _CLITestDisk2_ unter einer Ressourcengruppe namens _targetrg_ unter einem anderen Abonnement.

```azurecli-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2
```

Verwenden Sie den Befehl [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery), um die Wiederherstellungsanforderung mit allen relevanten Details vorzubereiten.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDisk --restore-location southeastasia --source-datastore OperationalStore --recovery-point-id /subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493 --target-resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2 > restore.json
```

```json
{
  "object_type": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recovery_point_id": "77594ce0470849e79b86a6875b726dca",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk2",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}

```

Sie können auch überprüfen, ob die JSON-Datei erfolgreich neue Ressourcen erstellt, indem Sie den Befehl [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) verwenden.

```azurecli-interactive
az dataprotection backup-instance validate-for-restore -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --restore-request-object restore.json
```

### <a name="trigger-the-restore"></a>Auslösen des Wiederherstellungsvorgangs

Verwenden Sie den Befehl [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger), um die Wiederherstellung mit der oben vorbereiteten Anforderung zu initiieren.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --parameters restore.json
```

## <a name="tracking-job"></a>Aufträge nachverfolgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) nach. Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch Az.ResourceGraph verwenden, um alle Aufträge für alle Sicherungstresore zu überprüfen. Verwenden Sie den Befehl [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph), um den relevanten Auftrag abzurufen, der in jedem Sicherungstresor vorhanden sein kann.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --operation Restore
```

## <a name="next-steps"></a>Nächste Schritte

[Häufig gestellte Fragen zu Azure Disk Backup](./disk-backup-faq.yml)