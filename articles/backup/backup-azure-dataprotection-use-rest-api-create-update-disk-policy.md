---
title: Erstellen von Sicherungsrichtlinien für Datenträger mithilfe der Datenschutz-REST-API
description: In diesem Artikel erfahren Sie, wie Sie Sicherungsrichtlinien für Datenträger mithilfe der REST-API erstellen und verwalten.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: ecc107c0-311c-42d0-a094-654d7ee30443
ms.openlocfilehash: 0df0b23d921076959718e79d5674aa8d561639e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620976"
---
# <a name="create-azure-data-protection-backup-policies-for-disks-using-rest-api"></a>Erstellen von Azure Data Protection-Sicherungsrichtlinien für Datenträger mithilfe der REST-API

Eine Sicherungsrichtlinie regelt die Aufbewahrung und den Zeitplan für Ihre Sicherungen. Azure Disk Backup ermöglicht mehrere Sicherungen pro Tag.

Sie können die Sicherungsrichtlinie wiederverwenden, um die Sicherung für mehrere Azure-Datenträger in einem Tresor zu konfigurieren, oder [eine Sicherungsrichtlinie für einen Azure Recovery Services-Tresor mithilfe der REST-API erstellen](/rest/api/dataprotection/backup-policies/create-or-update).

Führen Sie die folgenden Aktionen aus, um eine Richtlinie zum Sichern von Datenträgern zu erstellen:

## <a name="create-a-policy"></a>Erstellen einer Richtlinie

>[!IMPORTANT]
>Derzeit wird das Aktualisieren oder Ändern einer vorhandenen Richtlinie nicht unterstützt. Alternativ dazu können Sie eine neue Richtlinie mit den erforderlichen Details erstellen und sie der relevanten Sicherungsinstanz zuweisen.

Verwenden Sie den folgenden *PUT*-Vorgang, um eine Azure Backup-Richtlinie zu erstellen:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

Der `{policyName}` und der `{vaultName}` werden im URI angegeben. Zusätzliche Informationen werden im Anforderungstext angegeben.

## <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Um z. B. eine Richtlinie für Datenträgersicherungen zu erstellen, muss der Anforderungstext folgende Komponenten enthalten:

|Name  |Erforderlich  |type  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource-Eigenschaften        |

Die vollständige Liste von Definitionen im Anforderungstext finden Sie im Dokument [Schutzrichtlinien – Erstellen oder Aktualisieren](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Beispiel für Anforderungstext

Die Richtlinie besagt Folgendes:

- Geplanter Trigger für alle 4 Stunden (PT4H). In diesem Fall werden die Sicherungen im Intervall von ungefähr 4 Stunden durchgeführt, sodass die Sicherungen gleichmäßig über den Tag verteilt sind.
- Sie können das Triggerintervall auf alle 4, 6, 8 oder 12 Stunden festlegen. Verwenden Sie **P1D**, um eine Sicherung einmal pro Tag zu planen. Sicherungen werden einmal pro Tag zum festgelegten Zeitpunkt ausgelöst.
- Der Datenspeicher ist _Betriebsspeicher_, weil die Sicherungen lokal erfolgen und keine Daten im Sicherungstresor gespeichert werden. Im Betriebsspeicher wird jede Sicherungsinstanz sieben Tage lang gespeichert (P7D).

```json
{
"properties": {
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
}
```

>[!IMPORTANT]
>Als Zeitformat wird nur DateTime unterstützt. Nur die Uhrzeitangabe wird nicht unterstützt. Die Uhrzeit gibt die Startzeit der Sicherung an und nicht die Zeit, zu der die Sicherung abgeschlossen ist.

Die zum Abschließen des Sicherungsvorgangs erforderliche Zeit hängt von verschiedenen Faktoren ab, u. a. von der Datenträgergröße und dem Churn zwischen aufeinanderfolgenden Sicherungen. Azure Disk Backup ist jedoch ein Sicherungsdienst ohne Agents, der [inkrementelle Momentaufnahmen](../virtual-machines/disks-incremental-snapshots.md) verwendet und sich nicht auf die Leistung der Anwendung in der Produktion auswirkt.

Weitere Informationen zur Richtlinienerstellung finden Sie unter [Erstellen der Sicherungsrichtlinie für Azure-Datenträger](backup-managed-disks.md#create-backup-policy).

## <a name="responses"></a>Antworten

Die Erstellung/Aktualisierung der Sicherungsrichtlinie ist ein synchroner Vorgang und gibt „OK“ zurück, sobald der Vorgang erfolgreich war.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  OK       |

### <a name="example-responses"></a>Beispielantworten

Wenn der Vorgang abgeschlossen ist, wird 200 (OK) mit dem Richtlinieninhalt im Antworttext zurückgegeben.

```json
{
    "id": "/subscriptions/73307177-bb00-4801-bd11-894b2f2d5162/resourceGroups/RG-BV/providers/Microsoft.DataProtection/backupVaults/BV-JPE-GRS/backupPolicies/DiskBackupPolicy-03",
    "name": "DiskBackupPolicy-03",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Incremental",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-07-01T19:00:00+00:00/P1D"
                        ],
                      },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "OperationalStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupDaily",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P7D"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "OperationalStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": true,
                "name": "Default",
                "objectType": "AzureRetentionRule"
            }
        ],
        "datasourceTypes": [
            "Microsoft.Compute/disks"
        ],
        "objectType": "BackupPolicy"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren des Schutzes für Azure-Datenträger](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Artikeln:

- [Verwenden der REST-API für den Schutz von Daten in Azure](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
