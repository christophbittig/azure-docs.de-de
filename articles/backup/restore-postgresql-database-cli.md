---
title: Azure PostgreSQL-Datenbanken über Azure CLI wiederherstellen
description: Erfahren Sie, wie Sie Azure PostgreSQL-Datenbanken mit Azure CLI wiederherstellen können.
ms.topic: conceptual
ms.date: 10/25/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: e3b7260e58b738a2a15dae174adfefbc007644a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095620"
---
# <a name="restore-azure-postgresql-databases-using-azure-cli"></a>Azure PostgreSQL-Datenbanken mit Azure CLI wiederherstellen

Dieser Artikel erklärt, wie man [Azure PostgreSQL Datenbanken](/azure/postgresql/overview#azure-database-for-postgresql---single-server) auf einem Azure PostgreSQL Server wiederherstellt, der mit Azure Backup gesichert wurde.

Da es sich um eine PaaS-Datenbank handelt, wird die OLR-Option (Original-Location Recovery) zur Wiederherstellung durch Ersetzen der bestehenden Datenbank (von der die Backups erstellt wurden) nicht unterstützt. Sie können von einem Wiederherstellungspunkt wiederherstellen, um eine neue Datenbank auf demselben Azure PostgreSQL-Server oder auf einem anderen PostgreSQL-Server zu erstellen. Dies wird als Alternate-Location Recovery (ALR) bezeichnet und hilft, sowohl die Quelldatenbank als auch die wiederhergestellte (neue) Datenbank zu erhalten.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen, um eine neue PostgreSQL-Datenbank zu erstellen

- Nachverfolgen des Status des Wiederherstellungsvorgangs

In den Beispielen wird auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_ verwiesen.

## <a name="restore-a-backed-up-postgresql-database"></a>Wiederherstellung einer gesicherten PostgreSQL-Datenbank

### <a name="set-up-permissions"></a>Einrichten von Berechtigungen

Backup Vault verwendet Managed Identity für den Zugriff auf andere Azure-Ressourcen. Zur Wiederherstellung aus einem Backup benötigt die verwaltete Identität von Backup vault eine Reihe von Berechtigungen auf dem Azure PostgreSQL-Server, auf dem die Datenbank wiederhergestellt werden soll.

Um die relevanten Berechtigungen für die vom System zugewiesene verwaltete Identität des Tresors auf dem PostgreSQL-Zielserver zuzuweisen, siehe [Satz von Berechtigungen, die für die Sicherung der Azure PostgreSQL-Datenbank benötigt werden](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore).

Um den Wiederherstellungspunkt als Dateien in einem Speicherkonto wiederherzustellen, benötigt die dem System zugewiesene verwaltete Identität des [Backup-Datenspeichers Zugriff auf das Zielspeicherkonto](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account).

### <a name="fetch-the-relevant-recovery-point"></a>Holen Sie den entsprechenden Wiederherstellungspunkt

Um alle Sicherungsinstanzen innerhalb eines Tresors aufzulisten, verwenden Sie den Befehl [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) und rufen dann die entsprechende Instanz mit dem Befehl [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) auf. Alternativ können Sie für _at-scale_ Szenarien mit dem Befehl [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) Sicherungsinstanzen über Tresore und Abonnements auflisten.

```azurecli
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL -subscriptions "xxxxxxxx-xxxx-xxxx-xxxx"

  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceLocation": "westus",
        "resourceName": "postgres",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceUri": ""
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceLocation": "westus",
        "resourceName": "testpostgresql",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceUri": ""
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "friendlyName": "testpostgresql\\empdb11",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyParameters": null,
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded",
      "validationType": null
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "testBkpVault",
    "zones": null
  }
.
.
.
.
.
```

Nachdem die Instanz identifiziert wurde, rufen Sie den relevanten Wiederherstellungspunkt mit dem Befehl [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list) ab.

```azurecli
az dataprotection recovery-point list --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 -g testBkpVaultRG --vault-name TestBkpVault

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints/9da55e757af94261afa009b43cd3222a",
  "name": "9da55e757af94261afa009b43cd3222a",
  "properties": {
    "friendlyName": "2031fdb43a914114b6ce644eb6fcb5ce",
    "objectType": "AzureBackupDiscreteRecoveryPoint",
    "policyName": "oss-clitest-policy",
    "policyVersion": null,
    "recoveryPointDataStoresDetails": [
      {
        "creationTime": "2021-09-13T15:17:41.209845+00:00",
        "expiryTime": null,
        "id": "beddea84-7b30-42a5-a752-7c75baf96a52",
        "metaData": "{\"objectType\":\"PostgresBackupMetadata\",\"version\":\"1.0\",\"postgresVersion\":\"11\",\"dbName\":\"postgres\",\"serverName\":\"testpostgresql\",\"serverFQDN\":\"testpostgresql.postgres.database.azure.com\",\"usernameUsed\":\"backupadmin@testpostgresql\",\"backupToolPath\":\"postgresql-11.6-1\\\\bin\\\\pg_dump.exe\",\"backupType\":\"Full\",\"backupDumpFormat\":\"CUSTOM\",\"backupToolArgsFormat\":\"--no-acl --no-owner --serializable-deferrable --no-tablespaces --quote-all-identifiers -Fc -d postgres://{0}:{1}@{2}:5432/{3}?sslmode=verify-full&sslrootcert=E:\\\\approot\\\\Plugins\\\\Postgres\\\\..\\\\..\\\\postgres-root.crt\",\"storageUnits\":{\"1\":\"DbBackupDumpData\"},\"streamNamesInFirstStorageUnit\":[\"dbbkpdmpdatastream-1631546260050\"],\"pitId\":\"2031fdb43a914114b6ce644eb6fcb5ce\",\"bytesTransferred\":2063,\"dataSourceSize\":8442527,\"backupToolVersion\":\"11\"}",
        "rehydrationExpiryTime": null,
        "rehydrationStatus": null,
        "state": "COMMITTED",
        "type": "VaultStore",
        "visible": true
      }
    ],
    "recoveryPointId": "9da55e757af94261afa009b43cd3222a",
    "recoveryPointTime": "2021-09-13T15:17:41.209845+00:00",
    "recoveryPointType": "Full",
    "retentionTagName": "default",
    "retentionTagVersion": "637671427933449525"
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
```

Wenn Sie den Wiederherstellungspunkt von der Archivebene abrufen müssen, lautet die Variable _type_ in _recoveryPointDataStoreDetails_ _ArchiveStore_.

### <a name="prepare-the-restore-request"></a>Vorbereiten der Wiederherstellungsanforderung

Es gibt verschiedene Wiederherstellungsoptionen für eine PostgreSQL-Datenbank. Sie können den Wiederherstellungspunkt als andere Datenbank oder als Dateien wiederherstellen. Der Wiederherstellungspunkt kann auch auf der Archivebene liegen.

#### <a name="restore-as-database"></a>Wiederherstellen als Datenbank

Konstruieren Sie die Azure Resource Manager ID (ARM ID) der neu zu erstellenden PostgreSQL-Datenbank (mit dem Ziel-PostgreSQL-Server, dem Berechtigungen zugewiesen wurden, wie oben [ beschrieben](#set-up-permissions)) und den erforderlichen PostgreSQL-Datenbanknamen. Zum Beispiel kann eine PostgreSQL-Datenbank **emprestored21** unter einem PostgreSQL-Zielserver **targetossserver** in der Ressourcengruppe **targetrg** mit einem anderen Abonnement benannt werden.

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

Verwenden Sie den Befehl [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery), um die Wiederherstellungsanforderung mit allen relevanten Details vorzubereiten.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

Für einen archivbasierten Wiederherstellungspunkt müssen Sie Folgendes tun:

1. Rehydrieren vom Archivdatenspeicher in den Tresorspeicher
1. Ändern Sie den Quelldatenspeicher.
1. Fügen Sie weitere Parameter hinzu, um die Priorität der Rehydratation festzulegen.
1. Geben Sie die Dauer an, für die der rehydrierte Wiederherstellungspunkt im Datenspeicher des Tresors aufbewahrt werden soll.
1. Wiederherstellung als Datenbank von diesem Wiederherstellungspunkt aus.

Verwenden Sie den folgenden Befehl, um die Anfrage für alle oben genannten Vorgänge auf einmal vorzubereiten.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### <a name="restore-as-files"></a>Wiederherstellen als Dateien

Holt den URI des Containers innerhalb des Speicherkontos, dem die Berechtigungen wie oben beschrieben [zugewiesen wurden](#set-up-permissions). Beispiel: Ein Container namens **testcontainerrestore** unter einem Speicherkonto **testossstorageaccount** mit einem anderen Abonnement.

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Verwenden Sie den Befehl [az dataprotection backup-instance restore initialize-for-data-recovery-as-files](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery_as_files), um den Wiederherstellungsauftrag mit allen relevanten Details vorzubereiten.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

Ändern Sie bei archivbasierten Wiederherstellungspunkten den Quelldatenspeicher und fügen Sie die Rehydrierungspriorität und die Aufbewahrungsdauer in Tagen für den rehydrierten Wiederherstellungspunkt wie unten beschrieben hinzu:

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

Sie können auch mit dem Befehl [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) überprüfen, ob die JSON-Datei zur Erstellung neuer Ressourcen geeignet ist.

### <a name="trigger-the-restore"></a>Auslösen des Wiederherstellungsvorgangs

Verwenden Sie den Befehl [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger), um den Wiederherstellungsvorgang mit der oben vorbereiteten Anforderung auszulösen.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 --parameters OssRestoreReq.JSON
```

## <a name="tracking-job"></a>Aufträge nachverfolgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) nach. Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch _Az.ResourceGraph_ verwenden, um alle Aufträge über alle Backup-Depots hinweg zu verfolgen. Verwenden Sie den Befehl [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph), um den entsprechenden Auftrag zu erhalten, der sich über alle Backup-Depots erstreckt.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## <a name="next-steps"></a>Nächste Schritte

- [Azure PostgreSQL Backup Übersicht](backup-azure-database-postgresql-overview.md)
