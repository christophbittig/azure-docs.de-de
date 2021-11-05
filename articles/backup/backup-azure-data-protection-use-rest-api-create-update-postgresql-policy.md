---
title: Erstellen von Sicherungsrichtlinien für Azure PostgreSQL-Datenbanken mithilfe der Datenschutz-REST-API
description: In diesem Artikel erfahren Sie, wie Sie Sicherungsrichtlinien für Azure PostgreSQL-Datenbanken mithilfe der REST-API erstellen und verwalten.
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: v-amallick
ms.assetid: 759ee63f-148b-464c-bfc4-c9e640b7da6b
ms.openlocfilehash: 2caa9d89ae69d9710f42dc4b3c958d6fa8873098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095535"
---
# <a name="create-azure-data-protection-backup-policies-for-azure-postgresql-databases-using-rest-api"></a>Erstellen von Azure Data Protection-Sicherungsrichtlinien für Azure PostgreSQL-Datenbanken mithilfe der Datenschutz-REST-API

Eine Sicherungsrichtlinie regelt die Aufbewahrung und den Zeitplan für Ihre Sicherungen. Die Azure PostgreSQL-Datenbanksicherung bietet eine langfristige Aufbewahrung und unterstützt eine Sicherung pro Tag.

Sie können eine vorhandene Sicherungsrichtlinie wiederverwenden, um die Sicherung für PostgreSQL-Datenbanken in einem Tresor zu konfigurieren oder [eine Sicherungsrichtlinie für einen Azure Recovery Services-Tresor mithilfe der REST-API zu erstellen](/rest/api/dataprotection/backup-policies/create-or-update).

## <a name="understanding-postgresql-backup-policy"></a>Grundlegendes zur PostgreSQL-Sicherungsrichtlinie

Während die Datenträgersicherung mehrere Sicherungen pro Tag bietet und die Blobsicherung eine *kontinuierliche* Sicherung ohne Trigger ist, bietet die PostgreSQL-Sicherung Archivschutz. Die Sicherungsdaten, die zuerst an den Tresor gesendet werden, können gemäß einer Regel oder einem *Lebenszyklus* auf die *Archivebene* verschoben werden. In diesem Zusammenhang möchten wir das Sicherungsrichtlinienobjekt für PostgreSQL verstehen.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (In diesem Fall eine vollständige Datenbanksicherung)
         -  Anfänglicher Datenspeicher (Wo die Sicherungen anfänglich gespeichert werden)
         -  Trigger (Wie die Sicherung ausgelöst wird)
            -  Zeitplanbasiert
            -  Standardtaggingkriterien (Ein Standardtag für alle geplanten Sicherungen – dieses Tag verknüpft die Sicherungen mit der Aufbewahrungsregel)
   -  Standardaufbewahrungsregel (Eine Regel, die standardmäßig auf alle Sicherungen im anfänglichen Datenspeicher angewendet wird)

Dieses Objekt definiert also, welche Art von Sicherungen ausgelöst werden, wie sie ausgelöst werden (über einen Zeitplan), mit was sie markiert werden, wo sie abgelegt werden (ein Datenspeicher) und den Lebenszyklus der Sicherungsdaten in einem Datenspeicher. Das PowerShell-Standardobjekt für PostgreSQL besagt, dass jede Woche eine *vollständige* Sicherung ausgelöst wird, die dann in den Tresor gelangt, wo sie drei Monate lang gespeichert wird.

Wenn Sie der Richtlinie die *Archivebene* hinzufügen möchten, müssen Sie entscheiden, wann die Daten vom Tresor ins Archiv verschoben werden, wie lange die Daten im Archiv bleiben und welche der geplanten Sicherungen als *archivierbar* gekennzeichnet werden sollen. Daher müssen Sie eine *Aufbewahrungsregel* hinzufügen, bei der der Lebenszyklus der Sicherungsdaten vom Tresordatenspeicher zum Archivdatenspeicher definiert wird und festgelegt wird, wie lange die Daten im *Archivdatenspeicher* aufbewahrt werden. Anschließend müssen Sie ein „Tag“ hinzufügen, das die geplanten Sicherungen als _zur Archivierung berechtigt_ markiert. Das resultierende PowerShell-Objekt sieht wie folgt aus:

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (In diesem Fall eine vollständige Datenbanksicherung)
         -  Anfänglicher Datenspeicher (Wo die Sicherungen anfänglich gespeichert werden)
         -  Trigger (Wie die Sicherung ausgelöst wird)
            -  Zeitplanbasiert
            -  Standardtaggingkriterien (Ein Standardtag für alle geplanten Sicherungen – dieses Tag verknüpft die Sicherungen mit der Aufbewahrungsregel)
            -  Neue Taggingkriterien für die neue Aufbewahrungsregel mit dem gleichen Namen „X“
   -  Standardaufbewahrungsregel (Eine Regel, die standardmäßig auf alle Sicherungen im anfänglichen Datenspeicher angewendet wird)
   -  Eine neue Aufbewahrungsregel mit dem Namen „X“
      -  Lebenszyklus
         -  Quelldatenspeicher
         -  Löschen nach einem festgelegten Zeitraum im Quelldatenspeicher
         - Kopieren in den Zieldatenspeicher

Führen Sie die folgenden Aktionen aus, um eine Richtlinie zum Sichern von PostgreSQL-Datenbanken zu erstellen:

## <a name="create-a-policy"></a>Erstellen einer Richtlinie

>[!IMPORTANT]
>Derzeit wird das Aktualisieren oder Ändern einer vorhandenen Richtlinie nicht unterstützt. Alternativ dazu können Sie eine neue Richtlinie mit den erforderlichen Details erstellen und sie der relevanten Sicherungsinstanz zuweisen.

Verwenden Sie den folgenden *PUT*-Vorgang, um eine Azure Backup-Richtlinie zu erstellen:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

Der `{policyName}` und der `{vaultName}` werden im URI angegeben. Zusätzliche Informationen werden im Anforderungstext bereitgestellt.

## <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Um beispielsweise eine Richtlinie für eine PostgreSQL-Sicherung zu erstellen, benötigt der Anforderungstext die folgenden Komponenten:

|Name  |Erforderlich  |type  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource-Eigenschaften        |

Eine vollständige Liste der Definitionen im Anforderungstext finden Sie im [REST-API-Dokument zur Sicherungsrichtlinie](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Beispiel für Anforderungstext

Die Richtlinie besagt Folgendes:

- Planen Sie eine wöchentliche Sicherung und wählen Sie die Startzeit aus. (Zeit + P1W)
- Der Datenspeicher ist ein _Tresorspeicher_, da die Sicherungen direkt in den Tresor übertragen werden.
- Die Sicherungen werden im Tresor für drei Monate (P3M) aufbewahrt.

```json
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

>[!IMPORTANT]
>Die Zeitformate unterstützen nur _Datum/Uhrzeit_, nur _Uhrzeit_ wird nicht unterstützt. Die Uhrzeit gibt die Startzeit der Sicherung an und nicht die Zeit, zu der die Sicherung abgeschlossen ist.

Wir aktualisieren den obigen **JSON-Code** mit zwei Änderungen: Sicherungen werden an mehreren Wochentagen durchgeführt und ein *Archivdatenspeicher* zur Langzeitaufbewahrung von PostgreSQL-Datenbanksicherungen wird hinzugefügt.

Im folgenden Beispiel wird die wöchentliche Sicherung so verändert, dass sie jeden Sonntag, Mittwoch und Freitag jeder Woche durchgeführt wird. Das Datumsarray für den Zeitplan erwähnt die Datumsangaben. Die Wochentage dieser Datumsangaben werden als Wochentage verwendet. Sie müssen auch angeben, dass diese Zeitpläne jede Woche wiederholt werden sollen. Das Zeitplanintervall ist also *1* und der Intervalltyp ist *Wöchentlich*.

**Geplanter Trigger:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

Wenn Sie den *Archivschutz* hinzufügen möchten, müssen Sie den JSON-Code der Richtlinie wie folgt ändern:

Der obige JSON-Code verfügt über einen Lebenszyklus für den anfänglichen Datenspeicher unter der Standardaufbewahrungsregel. In diesem Szenario besagt die Regel, dass die Sicherungsdaten nach drei Monaten gelöscht werden. Sie sollten eine neue Aufbewahrungsregel hinzufügen, die definiert, wann die Daten in den *Archivdatenspeicher* *verschoben* werden. Das heißt, dass Sicherungsdaten zuerst in den Archivdatenspeicher kopiert und dann im Tresordatenspeicher gelöscht werden. Außerdem sollte die Regel die Dauer definieren, um die Daten im Archivdatenspeicher zu speichern. Wir nennen diese neue Regel **Monatlich**. Sie definiert, dass Sicherungen sechs Monate lang im Tresordatenspeicher aufbewahrt und dann in den Archivdatenspeicher kopiert werden sollen. *Löschen* Sie dann die Daten im Tresordatenspeicher, und speichern Sie sie 24 Monate lang im Archivdatenspeicher. Löschen Sie anschließend die Daten im Archivdatenspeicher.

**Aufbewahrungslebenszyklus:**

```json
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
```

Jedes Mal, wenn Sie eine Aufbewahrungsregel hinzufügen, müssen Sie ein entsprechendes Tag in der *Triggereigenschaft* der Richtlinie hinzufügen. Im folgenden Beispiel wird ein neues *Tag* zusammen mit den Kriterien (die erste erfolgreiche Sicherung des Monats) mit dem exakt gleichen Namen erstellt wie die entsprechende Anwendungsregel, die angewendet werden soll. 

In diesem Beispiel sollten die Tagkriterien *Monatlich* heißen.

**Taggingkriterien:**

```json
{
  "criteria": [
    {
      "absoluteCriteria": [
        "FirstOfMonth"
      ],
      "objectType": "ScheduleBasedBackupCriteria"
    }
  ],
  "isDefault": false,
  "tagInfo": {
    "tagName": "Monthly"
  },
  "taggingPriority": 15
}
```

Nach dem Einschließen aller Änderungen wird der JSON-Code der Richtlinie wie folgt angezeigt:

```json
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
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
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
          },
          {
            "criteria": [
              {
                "absoluteCriteria": [
                  "FirstOfMonth"
                ],
                "objectType": "ScheduleBasedBackupCriteria"
              }
            ],
            "isDefault": false,
            "tagInfo": {
              "tagName": "Monthly"
            },
            "taggingPriority": 15
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

Weitere Informationen zur Richtlinienerstellung finden Sie im Dokument [PostgreSQL-Datenbanksicherungsrichtlinie](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="responses"></a>Antworten

Die Erstellung/Aktualisierung der Sicherungsrichtlinie ist ein synchroner Vorgang und gibt _OK_ zurück, sobald der Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  OK       |

### <a name="example-responses"></a>Beispielantworten

Wenn der Vorgang abgeschlossen ist, wird 200 (OK) mit dem Richtlinieninhalt im Antworttext zurückgegeben.

```json
{
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Full",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-08-15T22:00:00+00:00/P1W",
                            "R/2021-08-18T22:00:00+00:00/P1W",
                            "R/2021-08-20T22:00:00+00:00/P1W"
                        ],
                        "timeZone": "UTC"
                    },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Monthly",
                                "id": "Monthly_"
                            },
                            "taggingPriority": 15,
                            "isDefault": false,
                            "criteria": [
                                {
                                    "absoluteCriteria": [
                                        "FirstOfMonth"
                                    ],
                                    "objectType": "ScheduleBasedBackupCriteria"
                                }
                            ]
                        },
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
                    "dataStoreType": "VaultStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupWeekly",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P6M"
                        },
                        "targetDataStoreCopySettings": [
                            {
                                "dataStore": {
                                    "dataStoreType": "ArchiveStore",
                                    "objectType": "DataStoreInfoBase"
                                },
                                "copyAfter": {
                                    "objectType": "CopyOnExpiryOption"
                                }
                            }
                        ],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    },
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P24M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "ArchiveStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": false,
                "name": "Monthly",
                "objectType": "AzureRetentionRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P3M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
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
            "Microsoft.DBforPostgreSQL/servers/databases"
        ],
        "objectType": "BackupPolicy"
    },
    "id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/DebRG1/providers/Microsoft.DataProtection/backupVaults/DebBackupVault/backupPolicies/OssPolicy1",
    "name": "OssPolicy1",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren des Schutzes für Azure-Datenträger](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Artikeln:

- [Verwenden der REST-API für den Schutz von Daten in Azure](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
