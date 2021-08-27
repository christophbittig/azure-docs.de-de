---
title: Wiederherstellen von Azure-Blobs mithilfe von Azure CLI
description: Erfahren Sie, wie Sie Azure-Blobs mithilfe der Azure CLI für beliebige Zeitpunkte wiederherstellen.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 620b98d40c66b8af2360559c6f71db5ef6c07114
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2021
ms.locfileid: "113644309"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-cli"></a>Durchführen von Zeitpunktwiederherstellungen für Azure-Blobs mithilfe der Azure CLI

In diesem Artikel wird beschrieben, wie [Blobs](blob-backup-overview.md) mithilfe von Azure PowerShell für einen beliebigen Zeitpunkt wiederhergestellt werden können.

> [!IMPORTANT]
> Die Unterstützung für Sicherungen und Wiederherstellungen von Azure-Blobs über die Befehlszeilenschnittstelle befindet sich in der Vorschau und ist als Erweiterung in Az 2.15.0-Version und höher verfügbar. Die Erweiterung wird automatisch installiert, wenn Sie die **az dataprotection**-Befehle ausführen. [Weitere Informationen zu Erweiterungen](/cli/azure/azure-cli-extensions-overview)

> [!IMPORTANT]
> Bevor Sie Azure-Blobs mithilfe von Azure Backup wiederherstellen, machen Sie sich mit einigen [wichtigen Punkten](blob-restore.md#before-you-start) vertraut.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen von Azure-Blobs für einen bestimmten Zeitpunkt

- Nachverfolgen des Status des Wiederherstellungsvorgangs

In den Beispielen wird auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_ verwiesen.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Wiederherstellen von Azure-Blobs in einem Speicherkonto

### <a name="fetching-the-valid-time-range-for-restore"></a>Abrufen des gültigen Zeitbereichs für die Wiederherstellung

Da die operative Sicherung für Blobs fortlaufend erfolgt, gibt es keine eindeutigen Punkte, anhand derer eine Wiederherstellung durchgeführt werden kann. Stattdessen muss der gültige Zeitbereich abgerufen werden, in dem Blobs für einen beliebigen Zeitpunkt wiederhergestellt werden können. In diesem Beispiel suchen wir nach gültigen Zeitbereichen für die Wiederherstellung innerhalb der letzten 30 Tage.

Zunächst müssen wir die ID der relevanten Sicherungsinstanz abrufen. Listen Sie alle Sicherungsinstanzen innerhalb eines Tresors mit dem Befehl [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) auf, und rufen Sie dann die relevante Instanz mit dem Befehl [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) ab. Alternativ können Sie für Szenarien mit großem Umfang Sicherungsinstanzen tresor- und abonnementübergreifend auflisten, indem Sie den Befehl [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) verwenden.

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureBlob --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"

[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestSA",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": ""
            }
          ]
        },
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "rg-bv",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]
```

Rufen Sie nach der Identifizierung der Instanz den relevanten Wiederherstellungsbereich mit dem Befehl [az dataprotection restorable-time-range find](/cli/azure/dataprotection/restorable-time-range?view=azure-cli-latest&preserve-view=true#az_dataprotection_restorable_time_range_find) ab.

```azurecli-interactive
az dataprotection restorable-time-range find --start-time 2021-05-30T00:00:00 --end-time 2021-05-31T00:00:00 --source-data-store-type OperationalStore -g testBkpVaultRG --vault-name TestBkpVault --backup-instances CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036

{
  "id": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
  "name": null,
  "properties": {
    "objectType": "AzureBackupFindRestorableTimeRangesResponse",
    "restorableTimeRanges": [
      {
        "endTime": "2021-05-31T00:00:00.0000000Z",
        "objectType": "RestorableTimeRange",
        "startTime": "2021-06-13T18:53:44.4465407Z"
      }
    ]
  },
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges"
}
```

### <a name="preparing-the-restore-request"></a>Die Wiederherstellungsanforderung wird vorbereitet

Sobald der Zeitpunkt für die Wiederherstellung feststeht, gibt es mehrere Optionen für die Wiederherstellung.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Durchführen einer Zeitpunktwiederherstellung für alle Blobs

Bei dieser Option können Sie alle Blockblobs im Speicherkonto wiederherstellen, indem Sie ein Rollback zum ausgewählten Zeitpunkt durchführen. Bei Speicherkonten, die große Datenmengen enthalten oder eine hohe Änderungsrate aufweisen, kann der Vorgang längere Zeit in Anspruch nehmen. Verwenden Sie den Befehl [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery), um alle Blockblobs wiederherzustellen. Der Speicherort für die Wiederherstellung und die Zielressourcen-ID sind identisch mit dem geschützten Speicherkonto.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z > restore.json
```

#### <a name="restoring-selected-containers"></a>Wiederherstellen ausgewählter Container

Bei dieser Option können Sie bis zu 10 Container durchsuchen und auswählen, die wiederhergestellt werden sollen. Verwenden Sie den Befehl [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery), um ausgewählte Container wiederherzustellen.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
      {
        "max_matching_value": "container1-0",
        "min_matching_value": "container1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2-0",
        "min_matching_value": "container2",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2 > restore.json
```

#### <a name="restoring-containers-using-a-prefix-match"></a>Wiederherstellen von Containern mithilfe eines Präfixabgleichs

Bei dieser Option können Sie eine Teilmenge von Blobs über einen Präfixabgleich wiederherstellen. Sie können bis zu 10 lexikografische Bereiche von Blobs innerhalb eines einzelnen Containers oder aus mehreren Containern wiederherstellen, um diese Blobs auf einen Zustand zu einem bestimmten Zeitpunkt zurückzusetzen. Beachten Sie dabei Folgendes:

- Sie können einen Schrägstrich (/) verwenden, um den Containernamen vom Blobpräfix zu trennen.
- Der Anfang des angegebenen Bereichs wird mit eingeschlossen, das Ende des angegebenen Bereichs wird jedoch ausgeschlossen.

[Erfahren Sie mehr](blob-restore.md#use-prefix-match-for-restoring-blobs) über die Verwendung von Präfixen zum Wiederherstellen von Blobbereichen.

Verwenden Sie den Befehl [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery), um ausgewählte Container wiederherzustellen.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
       {
        "max_matching_value": "container1/text4",
        "min_matching_value": "container1/text1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2/text41",
        "min_matching_value": "container2/text4",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}



az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41 > restore.json
```

### <a name="trigger-the-restore"></a>Auslösen des Wiederherstellungsvorgangs

Verwenden Sie den Befehl [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger), um die Wiederherstellung mit der oben vorbereiteten Anforderung zu initiieren.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036 --restore-request-object restore.json
```

## <a name="tracking-job"></a>Aufträge nachverfolgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) nach. Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch Az.ResourceGraph verwenden, um alle Aufträge für alle Sicherungstresore zu überprüfen. Verwenden Sie den Befehl [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph), um den relevanten Auftrag abzurufen, der in jedem Sicherungstresor vorhanden sein kann.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureBlob --operation Restore
```

## <a name="next-steps"></a>Nächste Schritte

[Supportmatrix für Azure-Blobsicherung](blob-backup-support-matrix.md)
