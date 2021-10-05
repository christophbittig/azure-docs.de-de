---
title: Sichern von verwalteten Azure-Datenträgern mithilfe der Azure CLI
description: Hier erfahren Sie, wie Sie verwaltete Azure-Datenträger mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) sichern.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: ce1e4b3f88e844165581c95f74955de04686855b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659692"
---
# <a name="back-up-azure-managed-disks-using-azure-cli"></a>Sichern von verwalteten Azure-Datenträgern mithilfe der Azure CLI

In diesem Artikel wird beschrieben, wie Sie [verwaltete Azure-Datenträger](../virtual-machines/managed-disks-overview.md) mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) sichern.

> [!IMPORTANT]
> Die Unterstützung für die Sicherung und Wiederherstellung von verwalteten Azure-Datenträgern über die Befehlszeilenschnittstelle befindet sich in der Vorschau und ist ab Az-Version 2.15.0 als Erweiterung verfügbar. Die Erweiterung wird automatisch installiert, wenn Sie die **az dataprotection**-Befehle ausführen. [Weitere Informationen zu Erweiterungen](/cli/azure/azure-cli-extensions-overview)

In diesem Artikel lernen Sie Folgendes:

- Erstellen eines Sicherungstresors

- Erstellen einer Sicherungsrichtlinie

- Konfigurieren der Sicherung eines Azure-Datenträgers

- Ausführen eines bedarfsgesteuerten Sicherungsauftrag

Informationen zur regionalen Verfügbarkeit von Azure Disk Backup, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Erstellen eines Sicherungstresors

Ein Sicherungstresor ist eine Speicherentität in Azure, die Sicherungsdaten für verschiedene neuere Workloads speichert, die von Azure Backup unterstützt werden, z. B. Azure Database for PostgreSQL-Server, Blobs in einem Speicherkonto und Azure-Datenträger. Sicherungstresore vereinfachen die Organisation Ihrer Sicherungsdaten und minimieren gleichzeitig den Verwaltungsaufwand. Sicherungstresore basieren auf dem Azure Resource Manager-Modell von Azure, das erweiterte Funktionen bietet, die das Schützen von Sicherungsdaten erleichtern.

Bevor Sie einen Sicherungstresor erstellen, wählen Sie die Speicherredundanz der Daten im Tresor aus. Anschließend erstellen Sie den Sicherungstresor mit der ausgewählten Speicherredundanz und dem angegebenen Speicherort. In diesem Artikel erstellen wir einen Sicherungstresor namens _TestBkpVault_ in der Region „USA, Westen“ (_westus_) unter der Ressourcengruppe _testBkpVaultRG_. Verwenden Sie den Befehl [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create), um einen Sicherungstresor zu erstellen. Weitere Informationen finden Sie unter [Erstellen eines Sicherungstresors](./backup-vault-overview.md#create-a-backup-vault).

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

Nach der Erstellung des Tresors erstellen wir eine Sicherungsrichtlinie zum Schutz der Azure-Datenträger.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Um die einzelnen Komponenten einer Sicherungsrichtlinie für Azure Disk Backup zu verstehen, rufen Sie die Richtlinienvorlage mit dem Befehl [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) ab. Dieser Befehl gibt eine Standardrichtlinienvorlage für einen angegebenen DataSource-Typ zurück. Verwenden Sie diese Richtlinienvorlage, um eine neue Richtlinie zu erstellen.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk

{
  "datasourceTypes": [
    "Microsoft.Compute/disks"
  ],
  "name": "DiskPolicy",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Incremental",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "OperationalStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupHourly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
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
            "duration": "P7D",
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

Die Richtlinienvorlage besteht aus einem Trigger (der entscheidet, was die Sicherung auslöst) und einem Lebenszyklus (der entscheidet, wann die Sicherung gelöscht/kopiert bzw. verschoben werden soll). Die Standardwerte in Azure Disk Backup sind ein geplanter 4-stündlicher Trigger (PT4H) und eine Beibehaltungsdauer von sieben Tagen für jede Sicherung.

**Geplanter Trigger:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        }

```

**Standardmäßiger Aufbewahrungslebenszyklus:**

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ]
```

Azure Disk Backup ermöglicht mehrere Sicherungen pro Tag. Wenn Sie häufigere Sicherungen benötigen, wählen Sie die **stündliche** Sicherungshäufigkeit mit der Möglichkeit, Sicherungen mit Intervallen von alle 4, 6, 8 oder 12 Stunden zu erstellen. Die Sicherungen werden basierend auf dem unter **Zeit** ausgewählten Intervall geplant.

Wenn Sie z. B. **Alle vier Stunden** auswählen, werden die Sicherungen im Intervall von ungefähr 4 Stunden durchgeführt, sodass die Sicherungen gleichmäßig über den Tag verteilt sind. Wenn eine Sicherung pro Tag ausreichend ist, wählen Sie als Sicherungshäufigkeit **Täglich** aus. Bei der Sicherungshäufigkeit „Täglich“ können Sie die Uhrzeit angeben, zu der die Sicherungen erstellt werden.

>[!IMPORTANT]
>Die Uhrzeit gibt die Startzeit der Sicherung an und nicht die Zeit, zu der die Sicherung abgeschlossen ist.

Die zum Abschließen des Sicherungsvorgangs erforderliche Zeit hängt von verschiedenen Faktoren ab, u. a. von der Datenträgergröße und dem Churn zwischen aufeinanderfolgenden Sicherungen. Azure Disk Backup ist jedoch ein Sicherungsdienst ohne Agents, der [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) verwendet und sich nicht auf die Leistung der Anwendung in der Produktion auswirkt.

   >[!NOTE]
   >Obwohl der ausgewählte Tresor möglicherweise die Einstellung für globale Redundanz aufweist, unterstützt Azure Disk Backup derzeit nur den Momentaufnahmen-Datenspeicher. Alle Sicherungen werden in einer Ressourcengruppe in Ihrem Abonnement gespeichert und nicht in den Sicherungstresorspeicher kopiert.

Weitere Informationen zur Richtlinienerstellung finden Sie unter [Erstellen der Sicherungsrichtlinie für Azure-Datenträger](backup-managed-disks.md#create-backup-policy).

Nachdem die Vorlage als JSON-Datei heruntergeladen wurde, können Sie sie wie gewünscht zur Planung und Aufbewahrung bearbeiten. Erstellen Sie dann eine neue Richtlinie mit der resultierenden JSON-Datei. Wenn Sie die Häufigkeit (in Stunden) oder die Aufbewahrungsdauer bearbeiten möchten, verwenden Sie die Befehle [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_trigger_set) und/oder [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_retention_rule_set). Sobald die Richtlinien-JSON alle erforderlichen Werte enthält, erstellen Sie mit dem Befehl [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) eine neue Richtlinie aus dem Richtlinienobjekt.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n mypolicy --policy policy.json

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy",
"name": "mypolicy",
"properties": {
"datasourceTypes": [
"Microsoft.Compute/disks"
],
"objectType": "BackupPolicy",
"policyRules": [
{
"backupParameters": {
"backupType": "Incremental",
"objectType": "AzureBackupParams"
},
"dataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"name": "BackupHourly",
"objectType": "AzureBackupRule",
"trigger": {
"objectType": "ScheduleBasedTriggerContext",
"schedule": {
"repeatingTimeIntervals": [
"R/2020-04-05T13:00:00+00:00/PT4H"
]
},
"taggingCriteria": [
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
"isDefault": true,
"lifecycles": [
{
"deleteAfter": {
"duration": "P7D",
"objectType": "AbsoluteDeleteOption"
},
"sourceDataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"targetDataStoreCopySettings": null
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

Nach dem Erstellen des Tresors und der Richtlinie gibt es drei wichtige Punkte, die Sie für den Schutz eines Azure-Datenträgers beachten müssen.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="disk-to-be-protected"></a>Zu schützender Datenträger

Rufen Sie die ARM-ID und den Speicherort des zu schützenden Datenträgers ab. Dies dient als Bezeichner des Datenträgers. In diesem Beispiel verwenden wir einen Datenträger mit dem Namen _CLITestDisk_ unter einer Ressourcengruppe namens _diskrg_ in einem anderen Abonnement.

```azurecli-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
```

#### <a name="snapshot-resource-group"></a>Ressourcengruppe für Momentaufnahme

Die Datenträgermomentaufnahmen werden in einer Ressourcengruppe innerhalb Ihres Abonnements gespeichert. Als Richtlinie empfehlen wir, eine dedizierte Ressourcengruppe als Momentaufnahmen-Datenspeicher für den Azure Backup-Dienst zu erstellen. Mit einer dedizierten Ressourcengruppe lassen sich die Zugriffsrechte auf die Ressourcengruppe beschränken, was Sicherheit gewährleistet und eine einfache Verwaltung der Sicherungsdaten ermöglicht. Notieren Sie sich die Arm-ID für die Ressourcengruppe, in der Sie die Datenträger-Momentaufnahmen platzieren möchten.

```azurecli-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Sicherungstresor

Die Sicherungstresore erfordern Berechtigungen für den Datenträger und die Momentaufnahmen-Ressourcengruppe, um Momentaufnahmen auszulösen und ihren Lebenszyklus zu verwalten. Die vom System zugewiesene verwaltete Identität des Tresors wird zum Zuweisen solcher Berechtigungen verwendet. Verwenden Sie den Befehl [az dataprotection backup-vault update](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_update), um die systemseitig zugewiesene verwaltete Identität für den Recovery Services-Tresor zu aktivieren.

```azurecli-interactive
az dataprotection backup-vault update -g testBkpVaultRG --vault-name TestBkpVault --type SystemAssigned
```

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

Sie müssen dem Tresor (dargestellt durch die Tresor-MSI) und dem relevanten Datenträger und/oder der Datenträgerressourcengruppe über die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) einige Berechtigungen zuweisen. Dazu können Sie das Azure-Portal oder die Befehlszeilenschnittstelle verwenden. Informationen zum Zuweisen verwandter Berechtigungen finden Sie unter [Voraussetzungen zum Konfigurieren der Sicherung verwalteter Datenträger](backup-managed-disks-ps.md#assign-permissions).

### <a name="prepare-the-request"></a>Vorbereiten der Anforderung

Nachdem alle relevanten Berechtigungen festgelegt wurden, führen Sie die Konfiguration der Sicherung in zwei Schritten aus. Als Erstes bereiten wir die relevante Anforderung mit dem entsprechenden Tresor, der Richtlinie, dem Datenträger und der Momentaufnahmen-Ressourcengruppe mithilfe des Befehls [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) vor. Der initialize-Befehl gibt eine JSON-Datei zurück, und anschließend müssen Sie den Wert der Momentaufnahmen-Ressourcengruppe aktualisieren. Danach senden wir die Anforderung zum Schutz des Datenträgers mit dem Befehl [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create).

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureDisk  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk" > backup_instance.json
```

Öffnen Sie die JSON-Datei, und bearbeiten Sie die **ID der Momentaufnahmen-Ressourcengruppe** in der ``` resource_group_id ``` im Abschnitt ```data_store_parameters_list```.

```json
{
  "backup_instance_name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "data_source_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "data_source_set_info": null,
    "object_type": "BackupInstance",
    "policy_info": {
      "policy_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policy_parameters": {
        "data_store_parameters_list": [
          {
            "data_store_type": "OperationalStore",
            "object_type": "AzureOperationalStoreParameters",
            "resource_group_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
          }
        ]
      }
    }
  }
}
```

> [!NOTE]
> Der Name der Sicherungsinstanz wird von Clients generiert und ist daher ein eindeutiger Wert. Er basiert auf dem Namen der Datenquelle und einer eindeutigen GUID. Nachdem Sie die Sicherungsinstanzen aufgelistet haben, sollten Sie den Namen der Sicherungsinstanz und den Namen der relevanten Datenquelle überprüfen können.

Verwenden Sie die bearbeitete JSON-Datei, um eine Sicherungsinstanz des verwalteten Azure-Datenträgers zu erstellen.

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json


{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "currentProtectionState": "ProtectionConfigured",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resourceLocation": "southeastasia",
      "resourceName": "CLITestDisk",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
    },
    "dataSourceSetInfo": null,
    "friendlyName": "CLITestDisk",
    "objectType": "BackupInstance",
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/sarath-rg"
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
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

Nach dem Erstellen der Sicherungsinstanz können Sie eine bedarfsgesteuerte Sicherung auslösen, falls Sie nicht auf den geplanten Trigger der Richtlinie warten möchten.

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Listen Sie alle Sicherungsinstanzen innerhalb eines Tresors mit dem Befehl [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) auf, und rufen Sie dann die relevante Instanz mit dem Befehl [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) ab. Für umfangreiche Szenarien können Sie Sicherungsinstanzen alternativ mit dem Befehl [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) tresor- und abonnementübergreifend auflisten.

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
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

Sie können eine Aufbewahrungsregel angeben, während Sie die Sicherung auslösen. Gehen Sie zum Anzeigen der Aufbewahrungsregeln in der Richtlinie die Richtlinien-JSON für Aufbewahrungsregeln durch. Im folgenden Beispiel wird die Regel mit dem Namen _default_ angezeigt und für die bedarfsgesteuerte Sicherung verwendet.

```JSON
{
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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
```

Lösen Sie mit dem Befehl [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) eine bedarfsgesteuerte Sicherung aus.

```azurecli-interactive
az dataprotection backup-instance adhoc-backup --name "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166" --rule-name "Default" --resource-group "000pikumar" --vault-name "PratikPrivatePreviewVault1"
```

## <a name="tracking-jobs"></a>Aufträge nachverfolgen

Verfolgen Sie alle Aufträge mithilfe des Befehls [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) nach. Sie können alle Aufträge auflisten und ein bestimmtes Auftragsdetail abrufen.

Sie können auch „Az.ResourceGraph“ verwenden, um sämtliche Aufträge für alle Sicherungstresore nachzuverfolgen. Verwenden Sie den Befehl [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph), um den relevanten Auftrag abzurufen, der in jedem Sicherungstresor vorhanden sein kann.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --status Completed
```

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Azure-Managed Disks mithilfe der Azure CLI](restore-managed-disks-cli.md)
