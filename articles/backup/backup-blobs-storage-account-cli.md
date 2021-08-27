---
title: Sichern von Azure-Blobs mithilfe der Azure CLI
description: Erfahren Sie, wie Sie Azure-Blobs mithilfe der Azure CLI sichern.
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: e8ffd1d369df816bd1020b0b2ec2c1696e6c433e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122342938"
---
# <a name="back-up-azure-blobs-in-a-storage-account-using-azure-cli"></a>Sichern von Azure-Blobs in einem Speicherkonto mithilfe der Azure CLI

In diesem Artikel beschrieben, wie Sie [Azure-Blobs](./blob-backup-overview.md) mithilfe der Azure CLI sichern.

> [!IMPORTANT]
> Die Unterstützung für Sicherungen und Wiederherstellungen von Azure-Blobs über die Befehlszeilenschnittstelle befindet sich in der Vorschau und ist als Erweiterung in Az 2.15.0-Version und höher verfügbar. Die Erweiterung wird automatisch installiert, wenn Sie die **az dataprotection**-Befehle ausführen. [Weitere Informationen zu Erweiterungen](/cli/azure/azure-cli-extensions-overview)

In diesem Artikel lernen Sie Folgendes:

- Vorbereitung

- Erstellen eines Sicherungstresors

- Erstellen einer Sicherungsrichtlinie

- Konfigurieren der Sicherung eines Azure-Blobs

- Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Informationen zur regionalen Verfügbarkeit von Azure-Blobs, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](blob-backup-support-matrix.md).

## <a name="before-you-start"></a>Vorbereitung

Sehen Sie sich die [Voraussetzungen](./blob-backup-configure-manage.md#before-you-start) und die [Supportmatrix](./blob-backup-support-matrix.md) an, bevor Sie beginnen.

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Speicherentität in Azure, die Sicherungsdaten für verschiedene neuere Workloads speichert, die von Azure Backup unterstützt werden, z. B. Azure Database for PostgreSQL Server, Blobs in einem Speicherkonto und Azure-Datenträger. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

Bevor Sie einen Sicherungstresor erstellen, wählen Sie die Speicherredundanz der Daten im Tresor aus. Fahren Sie dann mit der Erstellung des Sicherungstresors mit dieser Speicherredundanz und dem Speicherort fort. In diesem Artikel wird der Sicherungstresor _TestBkpVault_ in der Region _westus_ unter der Ressourcengruppe _testBkpVaultRG_ erstellt. Verwenden Sie den Befehl [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create), um einen Azure Backup-Datenspeicher zu erstellen. Weitere Informationen finden Sie unter [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

> [!IMPORTANT]
> Obwohl die Sicherungsspeicherredundanz des Tresors angezeigt wird, gilt die Redundanz nicht für die operative Sicherung von Blobs. Dies liegt daran, dass die Sicherung erfolgt und keine Daten im Sicherungstresor gespeichert werden. Hier ist der Sicherungstresor die Verwaltungsentität, die Sie bei der Verwaltung des Schutzes von Blockblobs in Ihren Speicherkonten unterstützt.

Nach dem Erstellen eines Tresors erstellen wir nun eine Azure Backup-Richtlinie zum Schutz von Azure-Blobs in einem Speicherkonto.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

> [!IMPORTANT]
> Lesen Sie [diesen Abschnitt](blob-backup-configure-manage.md#before-you-start), bevor Sie die Richtlinie erstellen und Sicherungen für Azure-Blobs konfigurieren.

Um die einzelnen Komponenten einer Azure Backup-Richtlinie für eine Azure-Blobsicherung zu verstehen, rufen Sie die Richtlinienvorlage mit dem Befehl [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) ab. Dieser Befehl gibt eine Standardrichtlinienvorlage für einen angegebenen DataSource-Typ zurück. Verwenden Sie diese Richtlinienvorlage, um eine neue Richtlinie zu erstellen.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob

{
  "datasourceTypes": [
    "Microsoft.Storage/storageAccounts/blobServices"
  ],
  "name": "BlobPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

Die Richtlinienvorlage umfasst einen Lebenszyklus (der entscheidet, wann die Sicherung gelöscht/kopiert bzw. verschoben werden soll). Da die operative Sicherung für Blobs fortlaufend erfolgt, benötigen Sie keinen Zeitplan für die Durchführung von Sicherungen.

```json
"policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
```

> [!NOTE]
> Eine Wiederherstellung über einen längeren Zeitraum kann dazu führen, dass die Wiederherstellungsvorgänge länger dauern. Außerdem hängt die erforderliche Zeit zum Wiederherstellen einer Gruppe von Daten von der Anzahl der Schreib- und Löschvorgänge ab, die während des Wiederherstellungszeitraums vorgenommen werden. Beispielsweise dauert bei einem Konto mit 1 Million Objekten, in dem pro Tag 3.000 Objekte hinzugefügt und 1.000 Objekte gelöscht werden, die Wiederherstellung zu einem 30 Tage in der Vergangenheit liegenden Zeitpunkt ungefähr zwei Stunden.<br><br>Für ein Konto mit einer solchen Änderungsrate empfiehlt sich weder ein Aufbewahrungszeitraum von mehr als 90 Tagen noch eine Wiederherstellung auf einen Zeitpunkt, der mehr als 90 Tage in der Vergangenheit liegt.

Sobald der JSON-Code der Richtlinie alle erforderlichen Werte aufweist, erstellen Sie mit dem Befehl [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) eine neue Richtlinie aus dem Richtlinienobjekt.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n BlobBackup-Policy --policy policy.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
    "name": "BlobBackup-Policy",
    "properties": {
      "datasourceTypes": [
        "Microsoft.Storage/storageAccounts/blobServices"
      ],
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P2D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              },
              "targetDataStoreCopySettings": []
            }
          ],
          "name": "Default",
          "objectType": "AzureRetentionRule"
        }
      ]
    },
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
  }
```

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Sobald der Tresor und die Richtlinie erstellt wurden, gibt es zwei wichtige Punkte, die Sie beim Schutz aller Azure-Blobs im Speicherkonto beachten müssen.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="storage-account-that-contains-the-blobs-to-be-protected"></a>Speicherkonto, das die zu schützenden Blobs enthält

Rufen Sie die Azure Resource Manager-ID des Speicherkontos ab, das die zu schützenden Blobs enthält. Diese ID dient als Bezeichner des Speicherkontos. Wir verwenden ein Beispiel für ein Speicherkonto namens _CLITestSA_ unter der Ressourcengruppe _blobrg_ in einem anderen Abonnement in der Region „Asien, Südosten“.

```azurecli-interactive
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
```

#### <a name="backup-vault"></a>Sicherungstresor

Der Sicherungstresor benötigt Berechtigungen für das Speicherkonto, um Sicherungen für Blobs im Speicherkonto zu aktivieren. Die vom System zugewiesene verwaltete Identität des Tresors wird zum Zuweisen solcher Berechtigungen verwendet.

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

Sie müssen dem Tresor (dargestellt durch die Tresor-MSI) und dem relevanten Speicherkonto über RBAC einige Berechtigungen zuweisen. Diese können über das Portal oder PowerShell ausgeführt werden. Erfahren Sie mehr über alle [zugehörigen Berechtigungen](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts).

### <a name="prepare-the-request"></a>Vorbereiten der Anforderung

Nachdem alle relevanten Berechtigungen festgelegt wurden, wird die Konfiguration der Sicherung in zwei Schritten ausgeführt. Zuerst bereiten wir die relevante Anforderung mithilfe des entsprechenden Tresors, der Richtlinie und des Speicherkontos mit dem Befehl [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) vor. Anschließend senden wir die Anforderung zum Schutz des Datenträgers mit dem Befehl [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create).

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureBlob  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" > backup_instance.json
```

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceSetInfo": null,
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
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
  }
```

> [!IMPORTANT]
> Wenn ein Speicherkonto für die Blobsicherung konfiguriert wird, beeinflusst dies einige Funktionen, z. B. Änderungsfeed und Löschsperre. [Weitere Informationen](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Azure-Blobs mithilfe der Azure CLI](restore-blobs-storage-account-cli.md)