---
title: Sichern von Blobs in einem Speicherkonto mithilfe der REST-API für den Schutz von Daten in Azure
description: In diesem Artikel erfahren Sie, wie Sie Sicherungsvorgänge für Blobs mithilfe der REST-API konfigurieren, initiieren und verwalten.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 7c244b94-d736-40a8-b94d-c72077080bbe
ms.openlocfilehash: 709579f814dde3e1972888b0edea18069334b6dd
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598639"
---
# <a name="back-up-blobs-in-a-storage-account-using-azure-data-protection-via-rest-api"></a>Sichern von Blobs in einem Speicherkonto mithilfe der REST-API für den Schutz von Daten in Azure

In diesem Artikel wird beschrieben, wie Sie Sicherungen für Blobs in einem Speicherkonto über die REST-API verwalten. Die Sicherung von Blobs wird auf der Ebene des Speicherkontos konfiguriert. So sind alle Blobs im Speicherkonto mit der operativen Sicherung geschützt.

Informationen zur regionalen Verfügbarkeit von Azure-Blobs, zu unterstützten Szenarien und zu Einschränkungen finden Sie in der [Supportmatrix](blob-backup-support-matrix.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Sicherungstresors](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Erstellen einer Blobsicherungsrichtlinie](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Sobald der Tresor und die Richtlinie erstellt wurden, gibt es drei wichtige Punkte, die der Benutzer beim Schutz aller Azure-Blobs beachten muss.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>Speicherkonto, das die zu schützenden Blobs enthält

Rufen Sie die Azure Resource Manager-ID des Speicherkontos ab, das die zu schützenden Blobs enthält. Diese ID dient als Bezeichner des Speicherkontos. Als Beispiel wird ein Speicherkonto mit dem Namen _msblobbackup_ unter der Ressourcengruppe _RG-BlobBackup_ in einem anderen Abonnement in der Region „USA, Westen“ verwendet.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup"
```

#### <a name="backup-vault"></a>Sicherungstresor

Der Sicherungstresor benötigt Berechtigungen für das Speicherkonto, um Sicherungen für Blobs im Speicherkonto zu aktivieren. Die vom System zugewiesene verwaltete Identität des Tresors wird zum Zuweisen solcher Berechtigungen verwendet. Als Beispiel wird ein Sicherungstresor mit dem Namen „testBkpVault“ in der Region „USA, Westen“ unter der Ressourcengruppe „TestBkpVaultRG“ verwendet.

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

Sie müssen dem Tresor (dargestellt durch die Tresor-MSI) und dem relevanten Speicherkonto über RBAC einige Berechtigungen zuweisen. Dies kann über das Portal, PowerShell oder die REST-API erfolgen. Erfahren Sie mehr über alle [zugehörigen Berechtigungen](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts).

### <a name="prepare-the-request-to-configure-backup"></a>Vorbereiten der Anforderung zum Konfigurieren der Sicherung

Sobald die entsprechenden Berechtigungen für den Tresor und das Speicherkonto festgelegt sowie der Tresor und die Richtlinie konfiguriert sind, kann die Anforderung zum Konfigurieren der Sicherung vorbereitet werden. Nachfolgend sehen Sie den Anforderungstext zum Konfigurieren der Sicherung für alle Blobs in einem Speicherkonto. Die Azure Resource Manager-ID (ARM-ID) des Speicherkontos und die zugehörigen Details werden im Abschnitt „datasourceinfo“ genannt, und die Richtlinieninformationen sind im Abschnitt „policyinfo“ enthalten.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westUS",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>Überprüfen der Anforderung zum Konfigurieren der Sicherung

Mithilfe der [API zum Überprüfen auf Sicherung](/rest/api/dataprotection/backup-instances/validate-for-backup) kann überprüft werden, ob die Anforderung zum Konfigurieren der Sicherung erfolgreich ausgeführt wird oder nicht. Anhand der Antwort kann der Kunde alle erforderlichen Voraussetzungen erfüllen und dann die Anforderung zum Konfigurieren der Sicherung übermitteln.

Die Anforderung zum Überprüfen auf Sicherung ist ein POST-Vorgang, und der URI weist die Parameter `{subscriptionId}`, `{vaultName}` und `{vaultresourceGroupName}` auf.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

In unserem Beispiel ergibt dies Folgendes:

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

Der zuvor erstellte [Anforderungstext](#prepare-the-request-to-configure-backup) wird verwendet, um die Details des zu schützenden Speicherkontos anzugeben.

#### <a name="example-request-body"></a>Beispiel für Anforderungstext

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westUS",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-validate-backup-request"></a>Antworten auf eine Anforderung zum Überprüfen auf Sicherung

Die Anforderung zum Überprüfen auf Sicherung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|202 – Akzeptiert     |         |  Der Vorgang wird asynchron abgeschlossen.      |
|200 – OK     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     Akzeptiert    |
| Andere Statuscodes |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Fehlerantwort mit Beschreibung des Grunds für den Fehler    |

##### <a name="example-responses-for-validate-backup-request"></a>Beispielantworten auf eine Anforderung zum Überprüfen auf Sicherung

###### <a name="error-response"></a>Fehlerantwort

Falls das angegebene Speicherkonto bereits geschützt ist, lautet die Antwort „HTTP 400 (Ungültige Anforderung)“ und gibt eindeutig an, dass das angegebene Speicherkonto in einem Sicherungstresor geschützt ist, und nennt Details.

```http
HTTP/1.1 400 BadRequest
Content-Length: 999
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: f36eb67a-8932-42a8-8aba-c5ee2443aa2e
x-ms-routing-request-id: WESTUS:20210707T124745Z:bcd23af5-fa17-4cd0-9929-a55f141e33ce
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:47:45 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance msblobbackuptemp from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "f36eb67a-8932-42a8-8aba-c5ee2443aa2e"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null
  }
}
```

###### <a name="tracking-response"></a>Nachverfolgen der Antwort

Wenn die Datenquelle nicht geschützt ist, fährt die API mit weiteren Überprüfungen fort und erstellt einen Nachverfolgungsvorgang.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 3e7cacb3-65cd-4b3c-8145-71fe90d57327
x-ms-routing-request-id: CENTRALUSEUAP:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den resultierenden Vorgang mithilfe des Azure-AsyncOperation-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Inprogress",
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "0001-01-01T00:00:00"
}
```

Nach Abschluss des Vorgangs wird „200 (OK)“ zurückgegeben, und im Antworttext werden weitere Anforderungen aufgeführt, die erfüllt werden müssen, z. B. Berechtigungen.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Appropriate permissions to perform the operation is missing.",
          "recommendedAction": [
            "Grant appropriate permissions to perform this operation as mentioned at https://aka.ms/UserErrorMissingRequiredPermissions and retry the operation."
          ],
          "code": "UserErrorMissingRequiredPermissions",
          "target": "",
          "innerError": {
            "code": "UserErrorMissingRequiredPermissions",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "3e7cacb3-65cd-4b3c-8145-71fe90d57327"
          }
        }
      }
    ],
    "code": "UserErrorMissingRequiredPermissions",
    "message": "Appropriate permissions to perform the operation is missing."
  },
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "2021-07-07T12:49:22Z"
}
```

Wenn alle Berechtigungen erteilt sind, übermitteln Sie die Überprüfungsanforderung erneut und verfolgen den resultierenden Vorgang nach. Es wird „200 (OK)“ als erfolgreich zurückgegeben, wenn alle Bedingungen erfüllt sind.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "status": "Succeeded",
  "startTime": "2021-07-07T13:03:54.8627251Z",
  "endTime": "2021-07-07T13:04:06Z"
}
```

### <a name="configure-backup-request"></a>Anforderung zum Konfigurieren der Sicherung

Nachdem die Anforderung überprüft wurde, können Sie sie an die [API zum Erstellen einer Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update) übermitteln. Eine Sicherungsinstanz stellt ein Element dar, das mit dem Datenschutzdienst von Azure Backup innerhalb des Sicherungstresors geschützt ist. In diesem Fall ist das Speicherkonto die Sicherungsinstanz, und Sie können denselben Anforderungstext, der oben überprüft wurde, mit geringfügigen Ergänzungen verwenden.

Sie müssen einen eindeutigen Namen für die Sicherungsinstanz festlegen. Daher wird empfohlen, eine Kombination aus dem Ressourcennamen und einem eindeutigen Bezeichner zu verwenden. Als Beispiel wird hier „msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d“ verwendet und als Name der Sicherungsinstanz festgelegt.

Verwenden Sie den folgenden ***PUT***-Vorgang, um eine Sicherungsinstanz zu erstellen oder zu aktualisieren.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

In unserem Beispiel ergibt dies Folgendes:

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>Erstellen der Anforderung zum Konfigurieren der Sicherung

Zum Erstellen einer Sicherungsinstanz werden die folgenden Komponenten des Anforderungstexts verwendet:

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource-Eigenschaften    |

##### <a name="example-request-for-configure-backup"></a>Beispielanforderung zum Konfigurieren der Sicherung

Es wird derselbe Anforderungstext wie zum Überprüfen der Sicherungsanforderung verwendet, jedoch mit einem eindeutigen Namen, wie [oben](#configure-backup) erwähnt.

```json
{
  "name": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "objectType": "BackupInstance",
    "datasourceinfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    }
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>Antworten auf eine Anforderung zum Konfigurieren der Sicherung

Die Anforderung zum Erstellen einer Sicherungsinstanz ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Es werden zwei Antworten zurückgegeben: „201 (Erstellt)“, wenn die Sicherungsinstanz erstellt wurde und der Schutz konfiguriert wird, und anschließend „200 (OK)“, wenn diese Konfiguration abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|201 – Erstellt   |   [Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  Die Sicherungsinstanz wurde erstellt, und der Schutz wird konfiguriert.      |
|200 – OK     |    [Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     Der Schutz ist konfiguriert.    |
| Andere Statuscodes |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Fehlerantwort mit Beschreibung des Grunds für den Fehler    |

##### <a name="example-responses-to-configure-backup-request"></a>Beispielantworten auf eine Anforderung zum Konfigurieren der Sicherung

Nachdem Sie die *PUT*-Anforderung zum Erstellen einer Sicherungsinstanz übermittelt haben, lautet die erste Antwort „201 (Erstellt)“ mit einem Azure-asyncOperation-Header. Beachten Sie, dass der Anforderungstext alle Eigenschaften der Sicherungsinstanz enthält.

```http
HTTP/1.1 201 Created
Content-Length: 1149
Content-Type: application/json
Expires: -1
Pragma: no-cache
Retry-After: 15
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 5d9ccf1b-7ac1-456d-8ae3-36c93c0d2427
x-ms-routing-request-id: CENTRALUSEUAP:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "name": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances",
  "properties": {
    "friendlyName": "msblobbackup",
    "dataSourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceUri": "",
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceLocation": "westus",
      "objectType": "Datasource"
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "protectionStatus": {
      "status": "ConfiguringProtection"
    },
    "currentProtectionState": "ConfiguringProtection",
    "provisioningState": "Provisioning",
    "objectType": "BackupInstance"
  }
}
```

Verfolgen Sie dann den resultierenden Vorgang mithilfe des Azure-AsyncOperation-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
```

Sobald der Vorgang abgeschlossen ist, wird „200 (OK)“ mit der Erfolgsmeldung im Antworttext zurückgegeben.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "status": "Succeeded",
  "startTime": "2021-07-07T17:02:19.0611871Z",
  "endTime": "2021-07-07T17:02:20Z"
}
```

> [!IMPORTANT]
> Wenn ein Speicherkonto für die Blobsicherung konfiguriert wird, beeinflusst dies einige Funktionen, z. B. Änderungsfeed und Löschsperre. [Weitere Informationen](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)

### <a name="stop-protection-and-delete-data"></a>Beenden des Schutzes und Löschen der Daten

Wenn Sie den Schutz für ein Speicherkonto entfernen und auch die Sicherungsdaten löschen möchten, führen Sie wie [hier](/rest/api/dataprotection/backup-instances/delete) beschrieben einen Löschvorgang durch.

Das Beenden des Schutzes und das Löschen der Daten ist ein *DELETE*-Vorgang.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

In unserem Beispiel ergibt dies Folgendes:

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>Antworten auf einen Vorgang zum Beenden des Schutzes und Löschen der Daten

Der *DELETE*-Vorgang für den Schutz ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Löschanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

##### <a name="example-responses-for-delete-protection"></a>Beispielantworten auf einen Vorgang zum Beenden des Schutzes und Löschen der Daten

Sobald Sie die *DELETE*-Anforderung übermittelt haben, lautet die erste Antwort „202 Akzeptiert“ zusammen mit einem Azure-asyncOperation-Header.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-deletes: 14999
x-ms-correlation-request-id: fee7a361-b1b3-496d-b398-60fed030d5a7
x-ms-routing-request-id: CENTRALUSEUAP:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den Azure-AsyncOperation-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einem Erfolgsstatus als Antwort zurückgegeben.

```http
GET "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01"

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "status": "Succeeded",
  "startTime": "2021-07-08T07:13:30.23815Z",
  "endTime": "2021-07-08T07:13:46Z"
}
```

## <a name="next-steps"></a>Nächste Schritte

[Wiederherstellen von Daten aus einer Sicherung von Azure-Blobs](backup-azure-arm-userestapi-restoreazurevms.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Dokumenten:

- [REST-API des Azure-Datenschutzanbieters](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
