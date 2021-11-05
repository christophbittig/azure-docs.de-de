---
title: Sichern der Azure-Datenbank für PostgreSQL mit Langzeitaufbewahrung mit Azure CLI
description: Erfahren Sie, wie Sie Azure Database for PostgreSQL mit Azure CLI sichern können.
ms.topic: conceptual
ms.date: 10/24/2021
ms.custom: devx-track-azurecli
ms.author: v-amallick
ms.openlocfilehash: ad6602c5a7b9307643703aef87f29738cd6d99fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096140"
---
# <a name="back-up-azure-postgresql-databases-using-azure-cli"></a>Sichern von Azure PostgreSQL-Datenbanken mit Azure CLI

Dieser Artikel erklärt, wie man eine [Azure PostgreSQL Datenbank](../postgresql/overview.md#azure-database-for-postgresql---single-server) mit Azure CLI sichert.

In diesem Artikel lernen Sie Folgendes:

-  Erstellen eines Sicherungstresors
-  Erstellen einer Sicherungsrichtlinie
-  Konfigurieren Sie eine Sicherung einer Azure PostgreSQL-Datenbank
-  Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Informationen zu den unterstützten Szenarien und Einschränkungen von SQL-Datenbanken finden Sie in der [Unterstützungsmatrix](backup-azure-database-postgresql-overview.md#support-matrix).

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Backup Vault ist eine Speichereinheit in Azure, die Backup-Daten für verschiedene neue Arbeitslasten speichert, die von Azure Backup unterstützt werden, z. B. Azure Database für PostgreSQL-Server, Blobs in einem Speicherkonto und Azure Disks. Backup-Tresore helfen bei der Organisation Ihrer Backup-Daten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

Bevor Sie einen Sicherungstresor erstellen, wählen Sie die Speicherredundanz der Daten im Tresor aus. Anschließend erstellen Sie den Sicherungstresor mit der ausgewählten Speicherredundanz und dem angegebenen Speicherort.

In diesem Artikel erstellen wir einen Sicherungstresor namens _TestBkpVault_ in der Region „USA, Westen“ (_westus_) unter der Ressourcengruppe _testBkpVaultRG_. Verwenden Sie den Befehl [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create), um einen Sicherungstresor zu erstellen. Weitere Informationen finden Sie unter [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

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

Nachdem der Tresor erstellt wurde, erstellen wir eine Backup-Richtlinie zum Schutz der Azure PostgreSQL-Datenbanken.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

### <a name="understanding-postgresql-backup-policy"></a>Verständnis der PostGreSQL-Backup-Richtlinie

Während das Disk-Backup mehrere Backups pro Tag bietet und das Blob-Backup ein _kontinuierliches_ Backup ohne Trigger ist, bietet das PostgreSQL-Backup Archivschutz. Die zunächst in den Tresor gesendeten Sicherungsdaten können dann gemäß einer definierten Regel oder einem _Lebenszyklus_ in die _Archiv_-Ebene verschoben werden. In diesem Zusammenhang wollen wir das Sicherungsrichtlinienobjekt für PostgreSQL verstehen.

- PolitikRegel
   -  Sicherungsregel
      - BackupParameter
        -  BackupType (in this case a full database backup).
        -  Ursprünglicher Datenspeicher (Wo werden die Sicherungen zunächst gespeichert)
        -  Auslöser (Wie die Sicherung ausgelöst wird)
           -  Zeitplan basiert
           -  Standard-Kennzeichnungskriterien (Ein Standard-Kennzeichen für alle geplanten Backups. Dieses Tag verknüpft die Backups mit der Aufbewahrungsregel)
   -  Standard-Aufbewahrungsregel (eine Regel, die standardmäßig auf alle Sicherungen auf dem ursprünglichen Datenspeicher angewendet wird)

Dieses Objekt legt also fest, welche Art von Backups ausgelöst werden, wie sie ausgelöst werden (über einen Zeitplan), mit welchen Tags sie versehen werden, wo sie landen (ein Datenspeicher) und wie der Lebenszyklus der Backup-Daten in einem Datenspeicher aussieht. Das Standard-PowerShell-Objekt für PostgreSQL besagt, dass jede Woche eine *vollständige* Sicherung ausgelöst wird, die dann in den Tresor gelangt, wo sie drei Monate lang gespeichert wird.

Wenn Sie die Schicht *Archiv* zur Richtlinie hinzufügen möchten, müssen Sie entscheiden, wann die Daten vom Tresor ins Archiv verschoben werden, wie lange die Daten im Archiv verbleiben und welche der geplanten Backups als _archivierbar_ gekennzeichnet werden sollen. Daher müssen Sie eine *Aufbewahrungsregel* hinzufügen, in der der Lebenszyklus der Sicherungsdaten vom Tresor-Datenspeicher zum Archiv-Datenspeicher definiert wird, und wie lange sie im *Archiv-Datenspeicher* verbleiben werden. Dann müssen Sie ein *Tag* hinzufügen, das die geplanten Backups als _für die Archivierung geeignet_ markiert.

Das resultierende PowerShell-Objekt sieht wie folgt aus:

-  PolitikRegel
   -  Sicherungsregel
      - BackupParameter
        -  BackupType (in this case a full database backup).
        -  Ursprünglicher Datenspeicher (Wo werden die Sicherungen zunächst gespeichert)
        -  Auslöser (Wie die Sicherung ausgelöst wird)
           -  Zeitplan basiert
           -  Standard-Kennzeichnungskriterien (Ein Standard-Kennzeichen für alle geplanten Backups. Dieses Tag verknüpft die Backups mit der Aufbewahrungsregel)
           -  Neue Kennzeichnungskriterien für die neue Aufbewahrungsregel mit dem gleichen Namen 'X'
   -  Standard-Aufbewahrungsregel (eine Regel, die standardmäßig auf alle Sicherungen auf dem ursprünglichen Datenspeicher angewendet wird)
   -  Eine neue Zurückbehaltungsregel mit dem Namen 'X'.
      -  Lebenszyklus
         -  Quell-Datenspeicher
         -  Löschen nach Zeitraum im Quelldatenspeicher
         -  Kopieren auf den Zieldatenspeicher

### <a name="retrieve-the-policy-template"></a>Abrufen der Richtlinienvorlage

Um die inneren Komponenten einer Backup-Richtlinie für Azure PostgreSQL-Datenbanksicherung zu verstehen, rufen Sie die Richtlinienvorlage mit dem Befehl [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) ab. Dieser Befehl gibt eine Standardrichtlinienvorlage für einen angegebenen DataSource-Typ zurück. Verwenden Sie diese Richtlinienvorlage, um eine neue Richtlinie zu erstellen.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDatabaseForPostgreSQL
{
  "datasourceTypes": [
    "Microsoft.DBforPostgreSQL/servers/databases"
  ],
  "name": "OssPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Full",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "VaultStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupWeekly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}
```

Die Richtlinienvorlage besteht aus einem Trigger (der entscheidet, was die Sicherung auslöst) und einem Lebenszyklus (der entscheidet, wann die Sicherung gelöscht/kopiert bzw. verschoben werden soll). In Azure PostgreSQL-Datenbank-Backup ist der Standardwert für den Trigger ein geplanter wöchentlicher Trigger (1 Backup alle 7 Tage) und die Aufbewahrung jedes Backups für drei Monate.

**Geplanter Trigger:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

**Standardlebenszyklus der Aufbewahrungsregel:**

```json
 {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
```

### <a name="modifying-the-policy-template"></a>Ändern der Richtlinienvorlage

> [!IMPORTANT]
> In Azure PowerShell können _Objekte_ als Bereitstellungsorte verwendet werden, um alle Änderungen durchzuführen. In Azure CLI müssen wir Dateien verwenden, da es keinen Begriff für *Objekte* gibt. Jeder Bearbeitungsvorgang sollte in eine neue Datei umgeleitet werden, wobei der Inhalt aus der Eingabedatei gelesen und in die Ausgabedatei umgeleitet wird. Sie können die Datei später nach Bedarf umbenennen, wenn Sie sie in einem Skript verwenden.

#### <a name="modify-the-schedule"></a>Ändern Sie den Zeitplan

Die Standardrichtlinienvorlage bietet einmal pro Woche eine Sicherung an. Sie können den Zeitplan so ändern, dass die Sicherung an mehreren Tagen pro Woche erfolgt. Um den Zeitplan zu ändern, verwenden Sie den Befehl [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest#az_dataprotection_backup_policy_trigger_set&preserve-view=true).

Im folgenden Beispiel wird die wöchentliche Sicherung so geändert, dass sie an jedem Sonntag, Mittwoch und Freitag jeder Woche erfolgt. Das Array "Schedule Date" enthält die Daten, und die Wochentage dieser Daten werden als Wochentage übernommen. Sie müssen auch angeben, dass diese Zeitpläne jede Woche wiederholt werden sollen. Das Zeitplanintervall ist also "1" und der Intervalltyp ist "Wöchentlich".

```azurecli
az dataprotection backup-policy trigger create-schedule --interval-type Weekly --interval-count 1 --schedule-days 2021-08-15T22:00:00 2021-08-18T22:00:00 2021-08-20T22:00:00
[
  "R/2021-08-15T22:00:00+00:00/P1W",
  "R/2021-08-18T22:00:00+00:00/P1W",
  "R/2021-08-20T22:00:00+00:00/P1W"
]

az dataprotection backup-policy trigger set --policy .\OSSPolicy.json  --schedule R/2021-08-15T22:00:00+00:00/P1W R/2021-08-18T22:00:00+00:00/P1W R/2021-08-20T22:00:00+00:00/P1W > EditedOSSPolicy.json
```

#### <a name="add-a-new-retention-rule"></a>Hinzufügen einer neuen Aufbewahrungsregel

Wenn Sie den Schutz *archiv* hinzufügen möchten, müssen Sie die Richtlinienvorlage wie folgt ändern.

Die Standardvorlage hat einen Lebenszyklus für den ursprünglichen Datenspeicher unter der Standardaufbewahrungsregel. In diesem Szenario besagt die Regel, dass die Sicherungsdaten nach drei Monaten zu löschen sind. Sie sollten eine neue Aufbewahrungsregel hinzufügen, die festlegt, wann die Daten *in den *Archiv*-Datenspeicher verschoben* werden, d.h. die Sicherungsdaten werden zuerst in den Archiv-Datenspeicher kopiert und dann im Tresor-Datenspeicher gelöscht. Außerdem sollte die Regel festlegen, wie lange die Daten im Datenspeicher *Archiv* aufbewahrt werden. Verwenden Sie den Befehl [az dataprotection backup-policy retention-rule create-lifecycle](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_create_lifecycle&preserve-view=true), um neue Lebenszyklen zu erstellen, und verwenden Sie den Befehl [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_set&preserve-view=true), um sie mit den neuen Regeln oder den vorhandenen Regeln zu verknüpfen.

Im folgenden Beispiel wird eine neue Aufbewahrungsregel mit dem Namen *Monatlich* erstellt, bei der die erste erfolgreiche Sicherung eines jeden Monats sechs Monate lang im Tresorraum aufbewahrt, dann in die Archivschicht verschoben und 24 Monate lang in der Archivschicht aufbewahrt werden soll.

```azurecli
az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 6 --retention-duration-type Months --source-datastore VaultStore --target-datastore ArchiveStore --copy-option CopyOnExpiryOption > VaultToArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 24 --retention-duration-type Months -source-datastore ArchiveStore > OnArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule set --lifecycles .\VaultToArchiveLifeCycle.JSON .\OnArchiveLifeCycle.JSON --name Monthly --policy .\EditedOSSPolicy.JSON > AddedRetentionRulePolicy.JSON
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>Hinzufügen eines Tags und der entsprechenden Kriterien

Sobald eine Aufbewahrungsregel erstellt ist, müssen Sie einen entsprechenden *Tag* in der Eigenschaft *Trigger* der Backup-Richtlinie erstellen. Verwenden Sie den Befehl [az dataprotection backup-policy tag create-absolute-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_absolute_criteria&preserve-view=true), um ein neues Tagging-Kriterium zu erstellen und verwenden Sie den Befehl [az dataprotection backup-policy tag set](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_set&preserve-view=true), um das vorhandene Tag zu aktualisieren oder ein neues Tag zu erstellen.

Das folgende Beispiel erstellt einen neuen *Tag* zusammen mit den Kriterien (das erste erfolgreiche Backup des Monats) mit demselben Namen wie die entsprechende anzuwendende Aufbewahrungsregel.

In diesem Beispiel sollten die Tag-Kriterien *Monatlich* heißen.

```azurecli
az dataprotection backup-policy tag create-absolute-criteria --absolute-criteria FirstOfMonth > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

Angenommen, der Zeitplan sieht mehrere Backups pro Woche vor (jeden Sonntag, Mittwoch und Donnerstag, wie im obigen Beispiel angegeben) und Sie möchten die Backups von Sonntag und Freitag archivieren, dann können die Tagging-Kriterien mit dem Befehl [az dataprotection backup-policy tag create-generic-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_generic_criteria&preserve-view=true) wie folgt geändert werden.

```azurecli
az dataprotection backup-policy tag create-generic-criteria --days-of-week Sunday Friday > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

### <a name="create-a-new-postgresql-backup-policy"></a>Erstellen Sie eine neue PostgreSQL-Sicherungsrichtlinie

Sobald die Vorlage entsprechend den Anforderungen geändert wurde, verwenden Sie den Befehl [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest#az_dataprotection_backup_policy_create&preserve-view=true), um eine Richtlinie mit der geänderten Vorlage zu erstellen.

```azurecli
az dataprotection backup-policy create --backup-policy-name FinalOSSPolicy --policy AddedRetentionRuleAndTag.JSON --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Sobald der Tresor und die Richtlinie erstellt sind, gibt es drei wichtige Punkte, die Sie beachten müssen, um eine Azure PostgreSQL-Datenbank zu schützen.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="postgresql-database-to-be-protected"></a>Zu schützende PostGreSQL-Datenbank

Holen Sie die Azure Resource Manager ID (ARM ID) des zu schützenden PostgreSQL. Dieser dient als Bezeichner der Datenbank. Als Beispiel wird eine Datenbank mit dem Namen **empdb11** unter einem PostgreSQL-Server **testposgresql** verwendet, die in der Ressourcengruppe **ossrg** unter einem anderen Abonnement vorhanden ist.

Das folgende Beispiel verwendet bash.

```azurecli
ossId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Der Azure Backup Service speichert nicht den Benutzernamen und das Passwort für die Verbindung mit der PostgreSQL-Datenbank. Stattdessen muss der Backup-Administrator die *Schlüssel* in den Schlüsseltresor einspeisen, und dann greift der Backup-Dienst auf den Schlüsseltresor zu, liest die Schlüssel und greift dann auf die Datenbank zu. Notieren Sie sich die geheime Kennung des betreffenden Schlüssels.

Das folgende Beispiel verwendet bash.

```azure-cli
keyURI="https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Sicherungstresor

Backup Vault muss sich mit dem PostgreSQL-Server verbinden und dann über die im Key Vault vorhandenen Schlüssel auf die Datenbank zugreifen. Daher ist ein Zugriff auf den PostgreSQL-Server und den Key Vault erforderlich. Es wird Zugriff auf die MSI des Backup-Datenspeichers gewährt.

[Lesen Sie die entsprechenden Berechtigungen](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup), die Sie dem MSI des Backup-Datenspeichers auf dem PostgreSQL-Server und dem Azure-Schlüsseltresor, in dem die Schlüssel für die Datenbank gespeichert sind, gewähren sollten.

### <a name="prepare-the-request"></a>Vorbereiten der Anforderung

Nachdem alle relevanten Berechtigungen festgelegt wurden, führen Sie die Konfiguration der Sicherung in zwei Schritten aus.

1. Wir bereiten die entsprechende Anfrage vor, indem wir den entsprechenden Tresor, die Richtlinie und die PostgreSQL-Datenbank mit dem Befehl [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_initialize&preserve-view=true) verwenden.
1. Die Anforderung zum Schutz der Datenbank wird mit dem Befehl [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_create&preserve-view=true) gestellt.

```azurecli
az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQL -l <vault-location> --policy-id <policy_arm_id>  --secret-store-type AzureKeyVault --secret-store-uri $keyURI > OSSBkpInstance.JSON

az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
```

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Sie müssen eine Aufbewahrungsregel angeben, während Sie die Sicherung auslösen. Um die Aufbewahrungsregeln in der Richtlinie anzuzeigen, navigieren Sie durch die JSON-Datei der Richtlinie für Aufbewahrungsregeln. Im folgenden Beispiel gibt es zwei Aufbewahrungsregeln mit den Namen **Standard** und **Monatlich**. Wir verwenden die Regel **Monatlich** für die On-Demand-Sicherung.

```azurecli
az dataprotection backup-policy show  -g ossdemorg --vault-name ossdemovault-1 --subscription e3d2d341-4ddb-4c5d-9121-69b7e719485e --name osspol5
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/resourceGroups/ossdemorg/providers/Microsoft.DataProtection/backupVaults/ossdemovault-1/backupPolicies/osspol5",
  "name": "osspol5",
  "properties": {
    "datasourceTypes": [
      "Microsoft.DBforPostgreSQL/servers/databases"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "backupParameters": {
          "backupType": "Full",
          "objectType": "AzureBackupParams"
        },
        "dataStore": {
          "dataStoreType": "VaultStore",
          "objectType": "DataStoreInfoBase"
        },
        "name": "BackupWeekly",
        "objectType": "AzureBackupRule",
        "trigger": {
          "objectType": "ScheduleBasedTriggerContext",
          "schedule": {
            "repeatingTimeIntervals": [
              "R/2020-04-04T20:00:00+00:00/P1W",
              "R/2020-04-01T20:00:00+00:00/P1W"
            ],
            "timeZone": "UTC"
          },
          "taggingCriteria": [
            {
              "criteria": [
                {
                  "absoluteCriteria": [
                    "FirstOfMonth"
                  ],
                  "daysOfMonth": null,
                  "daysOfTheWeek": null,
                  "monthsOfYear": null,
                  "objectType": "ScheduleBasedBackupCriteria",
                  "scheduleTimes": null,
                  "weeksOfTheMonth": null
                }
              ],
              "isDefault": false,
              "tagInfo": {
                "eTag": null,
                "id": "Monthly_",
                "tagName": "Monthly"
              },
              "taggingPriority": 15
            },
            {
              "criteria": null,
              "isDefault": true,
              "tagInfo": {
                "eTag": null,
                "id": "Default_",
                "tagName": "Default"
              },
              "taggingPriority": 99
            }
          ]
        }
      },
      {
        "isDefault": false,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P10Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
              "objectType": "DataStoreInfoBase"
            },
            "targetDataStoreCopySettings": []
          }
        ],
        "name": "Monthly",
        "objectType": "AzureRetentionRule"
      },
      {
        "isDefault": true,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P1Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
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
  "resourceGroup": "ossdemorg",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="trigger-an-on-demand-backup-using-command"></a>Auslösen eines On-Demand-Backups mit dem Befehl

Lösen Sie mit dem Befehl [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) eine bedarfsgesteuerte Sicherung aus.

```azurecli
az dataprotection backup-instance adhoc-backup --name "ossrg-empdb11" --rule-name "Monthly" --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="track-jobs"></a>Nachverfolgen von Aufträgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) nach. Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch _Az.ResourceGraph_ verwenden, um alle Aufträge über alle Backup-Depots hinweg zu verfolgen. Verwenden Sie den Befehl [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph), um die relevanten Aufträge zu finden, die sich in den Backup-Depots befinden.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --status Completed
```

## <a name="next-steps"></a>Nächste Schritte

- [Wiederherstellung der Azure PostgreSQL-Datenbank mit Azure CLI](restore-postgresql-database-cli.md)
