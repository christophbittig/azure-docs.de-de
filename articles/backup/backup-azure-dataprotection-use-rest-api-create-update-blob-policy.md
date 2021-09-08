---
title: Erstellen von Sicherungsrichtlinien für Blobs mithilfe der Datenschutz-REST-API
description: In diesem Artikel erfahren Sie, wie Sie Sicherungsrichtlinien für Blobs mithilfe der REST-API erstellen und verwalten.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 472d6a4f-7914-454b-b8e4-062e8b556de3
ms.openlocfilehash: 96c76eb592b0fb7b94aa8da6c4f975878dc8c913
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471560"
---
# <a name="create-azure-data-protection-backup-policies-for-blobs-using-rest-api"></a>Erstellen von Azure Data Protection-Sicherungsrichtlinien für Blobs mithilfe der REST-API

> [!IMPORTANT]
> Lesen Sie [diesen Abschnitt](blob-backup-configure-manage.md#before-you-start), bevor Sie mit der Erstellung der Richtlinie und dem Konfigurieren von Sicherungen für Azure-Blobs fortfahren.

Eine Sicherungsrichtlinie regelt in der Regel die Aufbewahrung und den Zeitplan für Ihre Sicherungen. Da die operative Sicherung für Blobs kontinuierlicher Natur ist, benötigen Sie keinen Zeitplan für die Durchführung von Sicherungen. Die Richtlinie ist im Grunde genommen erforderlich, um den Aufbewahrungszeitraum festzulegen. Sie können die Sicherungsrichtlinie wiederverwenden, um die Sicherung für mehrere Speicherkonten in einem Tresor zu konfigurieren.

>[!NOTE]
>Eine Wiederherstellung über einen längeren Zeitraum kann dazu führen, dass die Wiederherstellungsvorgänge länger dauern. Zudem ist die erforderliche Zeit zum Wiederherstellen einer Gruppe von Daten von der Anzahl der Schreib- und Löschvorgänge abhängig, die während des Wiederherstellungszeitraums vorgenommen werden. Beispielsweise dauert bei einem Konto mit 1 Million Objekten, in dem pro Tag 3.000 Objekte hinzugefügt und 1.000 Objekte gelöscht werden, die Wiederherstellung zu einem 30 Tage in der Vergangenheit liegenden Zeitpunkt ungefähr zwei Stunden. Bei einem Konto mit einer solchen Änderungsrate sollte weder ein Aufbewahrungszeitraum von mehr als 90 Tagen noch eine Wiederherstellung zu einem so weit in der Vergangenheit liegenden Zeitpunkt angewendet werden.

Die Schritte zum Erstellen einer Sicherungsrichtlinie für einen Azure Recovery Services-Tresor sind in der Dokumentation [Schutzrichtlinien – Erstellen oder Aktualisieren](/rest/api/dataprotection/backup-policies/create-or-update) beschrieben. Nutzen Sie dieses Dokument als Referenz zum Erstellen einer Richtlinie für Blobs in einem Speicherkonto.

## <a name="create-a-policy"></a>Erstellen einer Richtlinie

> [!IMPORTANT]
> Derzeit wird das Aktualisieren oder Ändern einer vorhandenen Richtlinie nicht unterstützt. Alternativ können Sie eine neue Richtlinie mit den erforderlichen Details erstellen und sie der relevanten Sicherungsinstanz zuweisen.

Verwenden Sie den folgenden *PUT*-Vorgang, um eine Azure Backup-Richtlinie zu erstellen:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

Der `{policyName}` und der `{vaultName}` werden im URI angegeben. Zusätzliche Informationen werden im Anforderungstext angegeben.

## <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Um z. B. eine Richtlinie für Blobsicherungen zu erstellen, werden im Folgenden die Komponenten des Anforderungstexts angegeben.

|Name  |Erforderlich  |type  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource-Eigenschaften        |

Die vollständige Liste von Definitionen im Anforderungstext finden Sie im Dokument [Schutzrichtlinien – Erstellen oder Aktualisieren](/rest/api/dataprotection/backup-policies/create-or-update).

### <a name="example-request-body"></a>Beispiel für Anforderungstext

Im folgenden Anforderungstext wird eine Sicherungsrichtlinie für Blobsicherungen definiert.

Die Richtlinie besagt Folgendes:

- Die Beibehaltungsdauer beträgt 30 Tage.
- Der Datenspeicher ist „Betriebsspeicher“, da die Sicherungen lokal sind und keine Daten im Sicherungstresor gespeichert werden.

```json
{
  "properties": {
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "name": "Default",
        "objectType": "AzureRetentionRule",
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
        ]
      }
    ]
  }
}
```

> [!IMPORTANT]
> Als Zeitformat wird nur DateTime unterstützt. Das Time-Format allein wird nicht unterstützt.

## <a name="responses"></a>Antworten

Die Erstellung/Aktualisierung der Sicherungsrichtlinie ist ein synchroner Vorgang und gibt „OK“ zurück, sobald der Vorgang erfolgreich war.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  OK       |

### <a name="example-responses"></a>Beispielantworten

Wenn der Vorgang abgeschlossen ist, wird 200 (OK) mit dem Richtlinieninhalt im Antworttext zurückgegeben.

```json
{
  "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups//TestBkpVaultRG/providers/Microsoft.RecoveryServices/vaults/testBkpVault/backupPolicies/TestBlobPolicy",
  "name": "TestBlobPolicy",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
  "properties": {
    "policyRules": [
      {
        "lifecycles": [
          {
            "deleteAfter": {
              "objectType": "AbsoluteDeleteOption",
              "duration": "P30D"
            },
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
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie den Schutz für Blobs in einem Speicherkonto.

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Dokumenten:

- [Verwenden der REST-API für den Schutz von Daten in Azure](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
