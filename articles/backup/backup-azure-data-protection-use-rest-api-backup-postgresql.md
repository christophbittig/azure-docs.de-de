---
title: Sichern von Azure Database for PostgreSQL-Instanzen mithilfe der Azure-Datenschutz-REST-API
description: In diesem Artikel erfahren Sie, wie Sie Sicherungsvorgänge für Azure Database for PostgreSQL-Instanzen mithilfe der REST-API konfigurieren, initiieren und verwalten.
ms.topic: conceptual
ms.date: 10/22/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.assetid: 55fa0a81-018f-4843-bef8-609a44c97dcd
ms.openlocfilehash: 7e4aa84c8e45ad15a2faa1c02c6ad938e126c0d0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021518"
---
# <a name="back-up-azure-postgresql-databases-using-azure-data-protection-via-rest-api"></a>Sichern von Azure Database for PostgreSQL-Instanzen unter Verwendung des Azure-Datenschutzes über die REST-API

In diesem Artikel erfahren Sie, wie Sie Sicherungen für Azure Database for PostgreSQL-Instanzen über die REST-API verwalten.

Informationen zu den unterstützten Szenarien für Sicherungen von Azure Database for PostgreSQL-Instanzen sowie zu Einschränkungen und Authentifizierungsmechanismen finden Sie in [dieser Übersicht](backup-azure-database-postgresql-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Sicherungstresors](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Erstellen einer PostgreSQL-Sicherungsrichtlinie](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Sobald der Tresor und die Richtlinie erstellt sind, gibt es drei wichtige Punkte, die Sie beachten müssen, um eine Azure Database for PostgreSQL-Instanz zu schützen.

### <a name="key-entities-involved"></a>Wichtige involvierte Entitäten

#### <a name="postgresql-database-to-be-protected"></a>Zu schützende PostgreSQL-Datenbank

Rufen Sie die Azure Resource Manager-ID (ARM-ID) der zu schützenden PostgreSQL-Datenbank ab. Sie fungiert als Bezeichner der Datenbank. Wir verwenden ein Beispiel für eine Datenbank namens **empdb11** unter einem PostgreSQL-Server namens **testpostgresql** in der Ressourcengruppe **ossdemoRG** unter einem anderen Abonnement. Im folgenden Beispiel wird Bash verwendet:

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Der Azure Backup-Dienst speichert den Benutzernamen und das Kennwort für die Verbindung mit der PostgreSQL-Datenbank nicht. Stattdessen muss der Sicherungsadministrator die *Schlüssel* in den Schlüsseltresor einspeisen. Anschließend greift der Azure Backup-Dienst auf den Schlüsseltresor zu, liest die Schlüssel und greift dann auf die Datenbank zu. Notieren Sie sich den Geheimnisbezeichner des relevanten Schlüssels. Im folgenden Beispiel wird Bash verwendet:

```http
"https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Sicherungstresor

Vom Sicherungstresor muss eine Verbindung mit dem PostgreSQL-Server hergestellt und dann über die im Schlüsseltresor vorhandenen Schlüssel auf die Datenbank zugegriffen werden. Er benötigt also Zugriff auf den PostgreSQL-Server und auf den Schlüsseltresor. Der Zugriff wird der MSI des Sicherungstresors gewährt.

[Machen Sie sich mit den Berechtigungen vertraut](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup), die Sie der MSI des Sicherungstresors für den PostgreSQL-Server und für den Azure-Schlüsseltresor erteilen müssen, in dem die Schlüssel für die Datenbank gespeichert sind.

### <a name="prepare-the-request-to-configure-backup"></a>Vorbereiten der Anforderung zum Konfigurieren der Sicherung

Sobald die entsprechenden Berechtigungen für den Tresor und die PostgreSQL-Datenbank festgelegt und der Tresor und die Richtlinie konfiguriert sind, kann die Anforderung zum Konfigurieren der Sicherung vorbereitet werden. Im Anschluss sehen Sie den Anforderungstext zum Konfigurieren der Sicherung für eine Azure Database for PostgreSQL-Instanz. Die Azure Resource Manager-ID (ARM-ID) der Azure Database for PostgreSQL-Instanz und die zugehörigen Details sind im Abschnitt _datasourceinfo_ angegeben. Die Richtlinieninformationen befinden sich im Abschnitt _policyinfo_.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "empdb11",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westUS",
          "objectType": "Datasource"
      },
      "dataSourceSetInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "testpostgresql",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westUS",
          "objectType": "DatasourceSet"
      },
      "policyInfo": {
          "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
          "policyVersion": ""
      },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>Überprüfen der Anforderung zum Konfigurieren der Sicherung

Verwenden Sie die [API zum Überprüfen auf Sicherung](/rest/api/dataprotection/backup-instances/validate-for-backup), um zu überprüfen, ob die Anforderung zum Konfigurieren der Sicherung erfolgreich ausgeführt wird. Sie können die Antwort verwenden, um die erforderlichen Voraussetzungen zu erfüllen, und dann die Konfiguration für die Sicherungsanforderung übermitteln.

Die Anforderung zum Überprüfen auf Sicherung ist ein _POST_-Vorgang, und der URI enthält die Parameter `{subscriptionId}`, `{vaultName}` und `{vaultresourceGroupName}`.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

Der zuvor erstellte [Anforderungstext](#prepare-the-request-to-configure-backup) wird verwendet, um Details der zu schützenden Azure Database for PostgreSQL-Instanz anzugeben.

#### <a name="example-request-body"></a>Beispiel für Anforderungstext

```json
{
  "backupInstance": {
    "dataSourceInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "empdb11",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westUS",
        "objectType": "Datasource"
    },
    "dataSourceSetInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "testpostgresql",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westUS",
        "objectType": "DatasourceSet"
    },
    "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyVersion": ""
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>Antworten auf eine Anforderung zum Überprüfen auf Sicherung

Die Anforderung zum Überprüfen auf Sicherung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|202 – Akzeptiert     |         |  Der Vorgang wird asynchron abgeschlossen.      |
|200 – OK     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     Akzeptiert    |
| Andere Statuscodes |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Fehlerantwort mit Beschreibung des Grunds für den Fehler    |

##### <a name="example-responses-for-validate-backup-request"></a>Beispielantworten auf eine Anforderung zum Überprüfen auf Sicherung

###### <a name="error-response"></a>Fehlerantwort

Wenn der angegebene Datenträger bereits geschützt ist, wird die Antwort „HTTP 400 (Ungültige Anforderung)“ zurückgegeben, die angibt, dass der angegebene Datenträger in einem Sicherungstresor geschützt ist, und nennt Details.

```http
HTTP/1.1 400 BadRequest
Content-Length: 1012
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 0c99ff0f-6c26-4ec7-899f-205435e89894
x-ms-routing-request-id: WESTUS:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 14:29:49 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "0c99ff0f-6c26-4ec7-899f-205435e89894"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null,
    "details": null
  }
}
```

###### <a name="track-response"></a>Nachverfolgen der Antwort

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
x-ms-routing-request-id: WESTUS:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den resultierenden Vorgang mithilfe des _Azure-AsyncOperation_-Headers mit einem einfachen *GET*-Befehl nach.

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
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
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

Wenn Sie alle Berechtigungen erteilen, übermitteln Sie die Überprüfungsanforderung erneut, und verfolgen Sie den resultierenden Vorgang nach. Sind alle Bedingungen erfüllt, wird als Erfolgsantwort „200 (OK)“ zurückgegeben.

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

Nachdem die Anforderung überprüft wurde, können Sie sie an die [API zum Erstellen einer Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update) übermitteln. Eine Sicherungsinstanz stellt ein Element dar, das mit dem Datenschutzdienst von Azure Backup innerhalb des Sicherungstresors geschützt ist. Hier ist die Azure Database for PostgreSQL-Instanz die Sicherungsinstanz, und Sie können den Anforderungstext, der weiter oben bereits überprüft wurde, mit geringfügigen Ergänzungen verwenden.

Verwenden Sie einen eindeutigen Namen für die Sicherungsinstanz. Wir empfehlen Ihnen daher, eine Kombination aus dem Ressourcennamen und einem eindeutigen Bezeichner zu verwenden. Im folgenden Vorgang wird beispielsweise _testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149_ verwendet und als Name der Sicherungsinstanz markiert.

Verwenden Sie den folgenden ***PUT***-Vorgang, um eine Sicherungsinstanz zu erstellen oder zu aktualisieren.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01
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
  "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>Antworten auf eine Anforderung zum Konfigurieren der Sicherung

Die _Anforderung zum Erstellen einer Sicherungsinstanz_ ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Es werden zwei Antworten zurückgegeben: „201 (Erstellt)“, wenn die Sicherungsinstanz erstellt wurde und der Schutz konfiguriert wird, und anschließend „200 (OK)“, wenn diese Konfiguration abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|201 – Erstellt   |   [Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  Die Sicherungsinstanz wurde erstellt, und der Schutz wird konfiguriert.      |
|200 – OK     |    [Sicherungsinstanz](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     Der Schutz ist konfiguriert.    |
| Andere Statuscodes |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Fehlerantwort mit Beschreibung des Grunds für den Fehler    |

##### <a name="example-responses-to-configure-backup-request"></a>Beispielantworten auf eine Anforderung zum Konfigurieren der Sicherung

Nachdem Sie die *PUT*-Anforderung zum Erstellen einer Sicherungsinstanz übermittelt haben, wird zunächst „201 (Erstellt)“ mit einem Header vom Typ _Azure-AsyncOperation_ zurückgegeben. Beachten Sie, dass der Anforderungstext alle Eigenschaften der Sicherungsinstanz enthält.

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
x-ms-routing-request-id: WESTUS:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
    "properties": {
        "friendlyName": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
        "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        },
        "protectionStatus": {
            "status": "ProtectionConfigured"
        },
        "currentProtectionState": "ProtectionConfigured",
        "provisioningState": "Succeeded",
        "objectType": "BackupInstance"
    },
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

Verfolgen Sie dann den resultierenden Vorgang mithilfe des _Azure-AsyncOperation_-Headers mit einem einfachen *GET*-Befehl nach.

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

### <a name="stop-protection-and-delete-data"></a>Beenden des Schutzes und Löschen der Daten

Wenn Sie den Schutz für eine Azure Database for PostgreSQL-Instanz entfernen und auch die Sicherungsdaten löschen möchten, können Sie einen [Löschvorgang](/rest/api/dataprotection/backup-instances/delete) ausführen.

Das Beenden des Schutzes und das Löschen der Daten ist ein *DELETE*-Vorgang.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>Antworten auf einen Vorgang zum Beenden des Schutzes und Löschen der Daten

Der *DELETE*-Vorgang für den Schutz ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Löschanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

##### <a name="example-responses-for-delete-protection"></a>Beispielantworten auf einen Vorgang zum Beenden des Schutzes und Löschen der Daten

Nachdem Sie die *DELETE*-Anforderung übermittelt haben, wird zunächst „202 (Akzeptiert)“ mit einem Header vom Typ _Azure-AsyncOperation_ zurückgegeben.

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
x-ms-routing-request-id: WESTUS:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den _Azure-AsyncOperation_-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einem Erfolgsstatus als Antwort zurückgegeben.

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

[Wiederherstellen von Daten aus der Sicherung einer Azure Database for PostgreSQL-Instanz](restore-postgresql-database-use-rest-api.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Artikeln:

- [REST-API des Azure-Datenschutzanbieters](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
