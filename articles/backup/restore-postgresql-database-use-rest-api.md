---
title: Wiederherstellen von Azure PostgreSQL-Datenbanken über die Azure-Datenschutz-REST-API
description: In diesem Artikel erfahren Sie, wie Sie Azure PostgreSQL-Datenbanken mithilfe der Azure-Datenschutz-REST-API wiederherstellen.
ms.topic: conceptual
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: eef655cb482bbfe61bcd645f6038616b4321229c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021373"
---
# <a name="restore-azure-postgresql-databases-using-azure-data-protection-rest-api"></a>Wiederherstellen von Azure PostgreSQL-Datenbanken mithilfe der Azure-Datenschutz-REST-API

Dieser Artikel erklärt, wie [Azure PostgreSQL Datenbanken](/azure/postgresql/overview#azure-database-for-postgresql---single-server) auf einem Azure PostgreSQL Server wiederhergestellt werden, der mit Azure Backup gesichert wurde.

Da es sich um eine PaaS-Datenbank handelt, wird die Option zur Wiederherstellung am ursprünglichen Speicherort (Original-Location Recovery, OLR) nicht unterstützt. Bei dieser Option erfolgt die Wiederherstellung durch Ersetzen der bestehenden Datenbank (von der die Sicherungen erstellt wurden). Sie können die Datenbank auf einen Wiederherstellungspunkt zurücksetzen, um eine neue Datenbank auf demselben Azure PostgreSQL-Server oder auf einem anderen PostgreSQL-Server zu erstellen. Dies wird als Wiederherstellung an einem alternativen Speicherort (Alternate-Location Recovery, ALR) bezeichnet und hilft, sowohl die Quelldatenbank als auch die wiederhergestellte (neue) Datenbank zu erhalten.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen, um eine neue PostgreSQL-Datenbank zu erstellen

- Nachverfolgen des Status des Wiederherstellungsvorgangs

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Sicherungstresors](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Erstellen einer Sicherungsrichtlinie für PostgreSQL-Datenbanken](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

- [Konfigurieren einer PostgreSQL-Datenbanksicherung](backup-azure-data-protection-use-rest-api-backup-postgresql.md)

In den Beispielen wird auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_ verwiesen.

## <a name="restore-a-backed-up-postgresql-database"></a>Wiederherstellung einer gesicherten PostgreSQL-Datenbank

### <a name="set-up-permissions"></a>Einrichten von Berechtigungen

Der Backup-Tresor verwendet eine verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Zur Wiederherstellung aus einer Sicherung benötigt die verwaltete Identität des Backup-Tresors verschiedene Berechtigungen auf dem Azure PostgreSQL-Server, auf dem die Datenbank wiederhergestellt werden soll.

Weitere Informationen zum Zuweisen der relevanten Berechtigungen für die systemseitig zugewiesene verwaltete Identität des Tresors auf dem PostgreSQL-Zielserver finden Sie unter [Für die Sicherung einer Azure PostgreSQL-Datenbank erforderliche Berechtigungen](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore).

Um den Wiederherstellungspunkt als Dateien in einem Speicherkonto wiederherzustellen, benötigt die systemseitig zugewiesene verwaltete Identität des Backup-Tresors Zugriff auf das Zielspeicherkonto. Dies wird [hier](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account) beschrieben.

### <a name="fetching-the-relevant-recovery-point"></a>Der relevante Wiederherstellungspunkt wird abgerufen.

Um alle verfügbaren Wiederherstellungspunkte für eine Sicherungsinstanz aufzulisten, verwenden Sie die API zum [Auflisten von Wiederherstellungspunkten](/rest/api/dataprotection/recovery-points/list).

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-07-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints?api-version=2021-07-01
```

#### <a name="responses-for-list-of-recovery-points"></a>Antworten für die Liste der Wiederherstellungspunkte

Nachdem Sie die *GET*-Anforderung übermittelt haben, wird die Antwort 200 (OK) und die Liste aller diskreten Wiederherstellungspunkte mit allen relevanten Details zurückgegeben.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   OK      |
|Andere Statuscodes     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     Die Fehlerantwort beschreibt den Grund für den Vorgangsfehler.    |

##### <a name="example-response-for-list-of-recovery-points"></a>Beispielantwort für die Liste der Wiederherstellungspunkte

```http
HTTP/1.1 200 OK
Content-Length: 53396
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 41f7ef85-f31e-4db7-87ef-115e3ca65b93
x-ms-routing-request-id: SOUTHINDIA:20211022T200018Z:ba3bc1ce-c081-4895-a292-beeeb6eb22cc
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:00:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
        "recoveryPointTime": "2021-10-21T16:31:16.8316716Z",
        "recoveryPointType": "Full",
        "friendlyName": "794ead7c7661410da03997d210d469e7",
        "recoveryPointDataStoresDetails": [
          {
            "id": "9ea7eaf4-eeb8-4c8f-90a7-7f04b60bf075",
            "type": "VaultStore",
            "creationTime": "2021-10-21T16:31:16.8316716Z",
            "expiryTime": "2022-10-21T16:31:16.8316716Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637212748405148394",
        "policyName": "osspol3",
        "policyVersion": null
      },
.
.
.
.
```

Um den Wiederherstellungspunkt aus der Archivebene abzurufen, müssen Sie die Variable _type_ in _recoveryPointDataStoreDetails_ in _ArchiveStore_ ändern.

Wählen Sie die relevanten Wiederherstellungspunkte aus der obigen Liste aus, und bereiten Sie die Wiederherstellungsanforderung vor. Wir wählen für die Wiederherstellung einen Wiederherstellungspunkt namens _794ead7c7661410da03997d210d469e7_ aus der obigen Liste aus.

### <a name="prepare-the-restore-request"></a>Vorbereiten der Wiederherstellungsanforderung

Es gibt verschiedene Wiederherstellungsoptionen für PostgreSQL-Datenbanken. Sie können den Wiederherstellungspunkt als andere Datenbank oder als Dateien wiederherstellen. Der Wiederherstellungspunkt kann auch auf der Archivebene liegen.

#### <a name="restore-as-database"></a>Wiederherstellen als Datenbank

Konstruieren Sie die Azure Resource Manager-ID (ARM-ID) der neu zu erstellenden PostgreSQL-Datenbank (mit dem PostgreSQL-Zielserver, dem wie [oben beschrieben](#set-up-permissions) Berechtigungen zugewiesen wurden) und den erforderlichen PostgreSQL-Datenbanknamen. Beispielsweise kann eine PostgreSQL-Datenbank unter einem PostgreSQL-Zielserver **targetossserver** in der Ressourcengruppe **targetrg** mit einem anderen Abonnement **emprestored21** genannt werden.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

Der folgende Anforderungstext enthält die Wiederherstellungspunkt-ID und die Details zum Wiederherstellungsziel.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

Für einen archivbasierten Wiederherstellungspunkt müssen Sie Folgendes tun:

1. Aktivieren aus dem Archivdatenspeicher in den Tresorspeicher
1. Ändern des Quelldatenspeichers
1. Hinzufügen weiterer Parameter, um die Aktivierungspriorität anzugeben
1. Angeben der Dauer, für die der aktivierte Wiederherstellungspunkt im Tresordatenspeicher beibehalten werden soll

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="restore-as-files"></a>Wiederherstellen als Dateien

Rufen Sie den URI des Containers innerhalb des Speicherkontos ab, dem wie [oben](#set-up-permissions) beschrieben Berechtigungen zugewiesen wurden. Beispiel: Ein Container namens **testcontainerrestore** in einem Speicherkonto **testossstorageaccount** mit einem anderen Abonnement.

```http
"https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

Ändern Sie für archivbasierte Wiederherstellungspunkte den Quelldatenspeicher, und fügen Sie, wie unten erwähnt, die Aktivierungspriorität und die Aufbewahrungsdauer des aktivierten Wiederherstellungspunkts in Tagen hinzu:

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="validate-restore-requests"></a>Überprüfen von Wiederherstellungsanforderungen

Nachdem der Anforderungstext vorbereitet wurde, überprüfen Sie ihn mithilfe der [API zum Überprüfen für die Wiederherstellung](/rest/api/dataprotection/backup-instances/validate-for-restore). Wie bei der API zum Überprüfen auf Sicherung ist dies ein *POST*-Vorgang.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-07-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/ValidateRestore?api-version=2021-07-01"
```

Weitere Informationen zum Anforderungstext für diese POST-API finden Sie [hier](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body).

##### <a name="request-body-to-validate-restore-request"></a>Anforderungstext zum Überprüfen der Wiederherstellungsanforderung

Wir haben einen Abschnitt dieses Texts im [obigen Abschnitt](#create-a-request-body-for-restore-operations) erstellt. Jetzt fügen wir den Objekttyp hinzu und verwenden ihn, um einen Überprüfungsvorgang auszulösen.

```json
{
    "objectType": "ValidateRestoreRequestObject",
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "restoreRequestObject": {
      "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
      "sourceDataStoreType": "VaultStore",
      "restoreTargetInfo": {
        "objectType": "restoreTargetInfo",
        "recoveryOption": "FailIfExists",
        "dataSourceInfo": {
          "objectType": "Datasource",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "resourceName": "emprestored21",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westus",
          "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "dataSourceSetInfo": {
          "objectType": "DatasourceSet",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
          "resourceName": "targetossserver",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westus",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "datasourceAuthCredentials": {
          "objectType": "SecretStoreBasedAuthCredentials",
          "secretStoreResource": {
            "secretStoreType": "AzureKeyVault",
            "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
            "value": null
          }
        },
        "restoreLocation": "westus"
      }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>Antwort auf Anforderungen zum Überprüfen der Wiederherstellung

Die _Anforderung zum Überprüfen der Wiederherstellung_ ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, den Sie separat nachverfolgen müssen.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Überprüfungsanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

###### <a name="example-response-to-restore-validate-request"></a>Beispielantwort auf eine Anforderung zum Überprüfen der Wiederherstellung

Sobald der *POST*-Vorgang übermittelt wurde, gibt er die erste Antwort „202 Akzeptiert“ zusammen mit einem _Azure-asyncOperation_-Header zurück.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den _Azure-AsyncOperation_-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird 200 (OK) mit einer Statusantwort zurückgegeben.

```http
GET https://management.azure.com/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==?api-version=2021-07-01
```

```http
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Inprogress",
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "0001-01-01T00:00:00Z"
}
```

Die Antwort weist auf Fehler hin, die vor dem Übermitteln der Wiederherstellungsanforderung behoben werden müssen. Das folgende Beispiel zeigt, was geschieht, wenn die Zieldatenbank eine niedrigere Version aufweist und daher nicht wiederhergestellt werden kann.

```http
---------- Response (1892 ms) ------------

HTTP/1.1 200 OK
Content-Length: 1236
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 784764f8-941d-4f05-8d8c-c02d2c05f799
x-ms-routing-request-id: SOUTHINDIA:20211022T202725Z:e109a061-a09e-4f13-acd0-9b9833f851ac
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:27:25 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported.",
          "recommendedAction": [
            "Restore to the same or a higher PostgreSQL version from which the backup was taken."
          ],
          "code": "UserErrorRestoreToLowerVersion",
          "target": "",
          "innerError": {
            "code": "InnerErrorCodeUnavailable",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Restoring backup from version:10 of PostgreSQL to 9.5 of PostgreSQL not supported, as the restore server version is lower."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "2a23524f-0217-4bc1-bbe8-1546d2e6204d-Ibz"
          }
        }
      }
    ],
    "code": "UserErrorRestoreToLowerVersion",
    "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported."
  },
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "2021-10-22T20:23:11Z"
}
```

Nachdem wir die Fehler behoben und die Anforderung erneut überprüft haben, wird 200 (OK) mit einer Erfolgsantwort zurückgegeben.

```http
HTTP/1.1 200 OK
Content-Length: 443
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 61d62dd8-8e1a-473c-bcc6-c6a7a19fb035
x-ms-routing-request-id: SOUTHINDIA:20211022T203846Z:89af04a6-4e91-4b64-8998-a369dc763408
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:38:46 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "status": "Succeeded",
  "startTime": "2021-10-22T20:28:24.3820169Z",
  "endTime": "2021-10-22T20:28:49Z"
}
```

#### <a name="trigger-restore-requests"></a>Anforderungen zum Auslösen der Wiederherstellung

Das Vorgang zum Auslösen der Wiederherstellung ist eine ***POST***-API. Weitere Informationen zum Auslösen der Wiederherstellung finden Sie [hier](/rest/api/dataprotection/backup-instances/trigger-restore).

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-07-01
```

Die API ergibt beispielsweise Folgendes:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/restore?api-version=2021-07-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>Erstellen eines Anforderungstexts für Wiederherstellungsvorgänge

Nachdem die Anforderungen überprüft wurden, verwenden Sie denselben Anforderungstext, um die _Wiederherstellungsanforderung_ mit geringfügigen Änderungen auszulösen.

###### <a name="example-request-body-for-restore"></a>Beispiel für einen Anforderungstext zur Wiederherstellung

Die einzige Änderung gegenüber dem _Anforderungstext zum Überprüfen der Wiederherstellung_ besteht darin, dass das Objekt _restoreRequest_ am Anfang entfernt wird.

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "sourceDataStoreType": "VaultStore",
  "restoreTargetInfo": {
    "objectType": "restoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "dataSourceInfo": {
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "resourceName": "emprestored21",
      "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
      "resourceLocation": "westus",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "dataSourceSetInfo": {
      "objectType": "DatasourceSet",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
      "resourceName": "targetossserver",
      "resourceType": "Microsoft.DBforPostgreSQL/servers",
      "resourceLocation": "westus",
      "resourceUri": "",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "datasourceAuthCredentials": {
      "objectType": "SecretStoreBasedAuthCredentials",
      "secretStoreResource": {
        "secretStoreType": "AzureKeyVault",
        "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
        "value": null
      }
    },
    "restoreLocation": "westus"
  },
  "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
}
```

#### <a name="response-to-trigger-restore-requests"></a>Antwort auf Anforderungen zum Auslösen der Wiederherstellung

Die _Anforderung zum Auslösen der Wiederherstellung_ ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Wiederherstellungsanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

##### <a name="example-response-to-trigger-restore-request"></a>Beispielantwort auf eine Anforderung zum Auslösen der Wiederherstellung

Sobald der *POST*-Vorgang übermittelt wurde, gibt er die erste Antwort „202 Akzeptiert“ zusammen mit einem _Azure-asyncOperation_-Header zurück.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den _Azure-AsyncOperation_-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einer Auftrags-ID zurückgegeben, die für den Abschluss der Wiederherstellungsanforderung weiter nachverfolgt werden sollte.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:46:52.4110868Z",
  "endTime": "2021-07-08T20:46:56Z",
  "properties": {
    "jobId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
    "objectType": "OperationJobExtendedInfo"
  }
}
```

#### <a name="track-jobs"></a>Nachverfolgen von Aufträgen

Durch die _Anforderungen zum Auslösen der Wiederherstellung_ wird der Wiederherstellungsauftrag ausgelöst. Verwenden Sie zum Nachverfolgen der resultierenden Auftrags-ID die [GET Jobs-API](/rest/api/dataprotection/jobs/get).

Verwenden Sie den einfachen *GET*-Befehl zum Nachverfolgen der _JobId_, die in der obigen [Antwort zum Auslösen der Wiederherstellung](#example-response-to-trigger-restore-request) angegeben ist.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-07-01
```

Der oben genannte Auftragsstatus gibt an, dass der Wiederherstellungsauftrag abgeschlossen ist.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure PostgreSQL-Sicherungen](backup-azure-database-postgresql-overview.md)