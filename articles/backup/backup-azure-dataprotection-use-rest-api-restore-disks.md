---
title: Wiederherstellen von Azure-Datenträgern mit der Azure-REST-API zum Schutz von Daten
description: In diesem Artikel erfahren Sie, wie Sie Azure-Datenträger mithilfe der Azure-REST-API für den Schutz von Daten wiederherstellen.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 30f4e7ff-2a55-4a85-be44-55feedc24607
ms.openlocfilehash: d8898b407fdcbea154d9282ff7964a3d0fa0264c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621014"
---
# <a name="restore-azure-disks-using-azure-data-protection-rest-api"></a>Wiederherstellen von Azure-Datenträgern mit der Azure-REST-API zum Schutz von Daten

In diesem Artikel wird beschrieben, wie Sie [Datenträger](disk-backup-overview.md) über Azure Backup wiederherstellen.

>[!Note]
>- Derzeit wird die Wiederherstellung am ursprünglichen Speicherort (Original-Location Recovery, OLR) nicht unterstützt. Bei dieser Wiederherstellungsoption wird der vorhandene Quelldatenträger, von dem die Sicherungen erstellt wurden, ersetzt.
>- Sie können eine Wiederherstellung aus einem Wiederherstellungspunkt vornehmen, um einen neuen Datenträger in derselben Ressourcengruppe des Quelldatenträgers oder in einer anderen Ressourcengruppe zu erstellen. Dies wird als Wiederherstellung an einem alternativen Speicherort (Alternate-Location Recovery, ALR) bezeichnet.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen zum Erstellen eines neuen Datenträgers

- Nachverfolgen des Status des Wiederherstellungsvorgangs

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Sicherungstresors](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Erstellen einer Sicherungsrichtlinie für Datenträger](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

- [Konfigurieren einer Datenträgersicherung](backup-azure-dataprotection-use-rest-api-backup-disks.md)

In diesem Beispiel beziehen wir uns auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_, in der ein Azure-Datenträger _msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed_ heißt.

## <a name="restore-to-create-a-new-azure-disk"></a>Wiederherstellen zum Erstellen eines neuen Azure-Datenträgers

### <a name="set-up-permissions"></a>Einrichten von Berechtigungen

Der Sicherungstresor verwendet die verwaltete Identität für den Zugriff auf andere Azure-Ressourcen. Um die Wiederherstellung aus einer Sicherung vorzunehmen, erfordert die verwaltete Identität des Sicherungstresors eine Reihe von Berechtigungen für die Ressourcengruppe, in der der Datenträger wiederhergestellt werden soll.

Der Sicherungstresor verwendet eine systemseitig zugewiesene verwaltete Identität, die auf eine Ressource beschränkt und an den Lebenszyklus dieser Ressource gebunden ist. Um der verwalteten Identität Berechtigungen zu erteilen, verwenden Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC). Eine verwaltete Identität ist ein spezieller Dienstprinzipal, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Informieren Sie sich ausführlicher über [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Weisen Sie die relevanten Berechtigungen für die systemseitig zugewiesene verwaltete Identität des Tresors der Zielressourcengruppe zu, in der die Datenträger wiederhergestellt bzw. erstellt werden sollen. [Weitere Informationen](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetch-the-list-of-recovery-points"></a>Abrufen der Liste der Wiederherstellungspunkte

Um alle verfügbaren Wiederherstellungspunkte für eine Sicherungsinstanz aufzulisten, verwenden Sie die API zum [Auflisten von Wiederherstellungspunkten](/rest/api/dataprotection/recovery-points/list).

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints?api-version=2021-01-01
```

#### <a name="responses-for-list-of-recovery-points"></a>Antworten für die Liste der Wiederherstellungspunkte

Nachdem Sie die *GET*-Anforderung übermittelt haben, wird die Antwort als 200 (OK) und die Liste aller diskreten Wiederherstellungspunkte mit allen relevanten Details zurückgegeben.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   OK      |
|Andere Statuscodes     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     Die Fehlerantwort beschreibt den Grund für den Vorgangsfehler.    |

##### <a name="example-response-for-list-of-recovery-points"></a>Beispielantwort für die Liste der Wiederherstellungspunkte

```http
HTTP/1.1 200 OK
Content-Length: 7550
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: f01e2448-bdc5-4971-aee4-2edd1945c719
x-ms-routing-request-id: CENTRALUSEUAP:20210830T173435Z:0063423e-8b5e-493e-bb2e-74b7c7947c6c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 17:34:34 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/a3d02fc3ab8a4c3a8cc26688c26d3356",
      "name": "a3d02fc3ab8a4c3a8cc26688c26d3356",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
        "recoveryPointTime": "2021-08-30T10:02:11.6354913Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "119ac243-a789-4a41-be24-0461bceb3888",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-30T10:02:11.6354913Z",
            "expiryTime": "2021-09-06T10:02:11.6354913Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/8f76ebc4c54847c09455d5785a150ce2",
      "name": "8f76ebc4c54847c09455d5785a150ce2",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "8f76ebc4c54847c09455d5785a150ce2",
        "recoveryPointTime": "2021-08-29T10:01:50.7749008Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "1ac5aa6b-3583-464f-9604-7490c04c2b22",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-29T10:01:50.7749008Z",
            "expiryTime": "2021-09-05T10:01:50.7749008Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    .
    .
    .
    .
}
```

Wählen Sie die relevanten Wiederherstellungspunkte aus der obigen Liste aus, und bereiten Sie die Wiederherstellungsanforderung vor. Wir wählen für die Wiederherstellung einen Wiederherstellungspunkt namens _a3d02fc3ab8a4c3a8cc26688c26d3356_ aus der obigen Liste aus.

### <a name="prepare-the-restore-request"></a>Vorbereiten der Wiederherstellungsanforderung

Erstellen Sie die ARM-ID (Azure Resource Manager) des neuen Datenträgers, der mit der Zielressourcengruppe (mit den wie [oben](#set-up-permissions) beschriebenen zugewiesenen Berechtigungen) erstellt werden soll, sowie den erforderlichen Datenträgernamen.

Wir verwenden beispielsweise einen Datenträger namens _APITestDisk2_ unter einer Ressourcengruppe _targetrg_, die sich in derselben Region wie der zu sichernde Datenträger, aber unter einem anderen Abonnement befindet.

#### <a name="construct-the-request-body-for-restore-request"></a>Erstellen des Anforderungstexts für die Wiederherstellungsanforderung

Der folgende Anforderungstext enthält die Wiederherstellungspunkt-ID und die Details zum Wiederherstellungsziel.

```json
{
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

#### <a name="validate-restore-requests"></a>Überprüfen von Wiederherstellungsanforderungen

Nachdem der Anforderungstext vorbereitet wurde, überprüfen Sie ihn mithilfe der [API zum Überprüfen auf Wiederherstellung](/rest/api/dataprotection/backup-instances/validate-for-restore). Wie bei der API zum Überprüfen auf Sicherung ist dies ein *POST*-Vorgang.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

In unserem Beispiel ergibt die API Folgendes:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/validateRestore?api-version=2021-01-01"
```

Weitere Informationen zum Anforderungstext für diese POST-API finden Sie [hier](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body). 

##### <a name="request-body-to-validate-restore-request"></a>Anforderungstext zum Überprüfen der Wiederherstellungsanforderung

Wir haben einen Abschnitt dieses Texts im [obigen Abschnitt](#construct-the-request-body-for-restore-request) erstellt. Jetzt fügen wir den Objekttyp hinzu und verwenden ihn, um einen Überprüfungsvorgang auszulösen.

```json

{
  "objectType": "ValidateRestoreRequestObject",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>Antwort auf Anforderungen zum Überprüfen der Wiederherstellung

Die _Anforderung zum Überprüfen der Wiederherstellung_ ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Somit wird in diesem Vorgang ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.

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
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den _Azure-AsyncOperation_-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einem Erfolgsstatus als Antwort zurückgegeben.

```http
 GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:59:35.0060264Z",
  "endTime": "2021-07-08T20:59:57Z"
}
```

#### <a name="trigger-restore-requests"></a>Anforderungen zum Auslösen der Wiederherstellung

Das Vorgang zum Auslösen der Wiederherstellung ist eine ***POST***-API. Weitere Informationen zum Auslösen der Wiederherstellung finden Sie [hier](/rest/api/dataprotection/backup-instances/trigger-restore).

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

Die API ergibt beispielsweise Folgendes:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/restore?api-version=2021-01-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>Erstellen eines Anforderungstexts für Wiederherstellungsvorgänge

Nachdem die Anforderungen überprüft wurden, verwenden Sie denselben Anforderungstext mit geringfügigen Änderungen, um die _Wiederherstellungsanforderung_ auszulösen.

###### <a name="example-request-body-for-restore"></a>Beispiel für einen Anforderungstext zur Wiederherstellung

Die einzige Änderung gegenüber dem Anforderungstext zum Überprüfen der Wiederherstellung besteht darin, dass das Objekt _restoreRequest_ am Anfang entfernt und der Objekttyp geändert wird.

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "sourceDataStoreType": "OperationalStore",
  "restoreTargetInfo": {
    "datasourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "datasourceType": "Microsoft.Compute/disks",
      "resourceName": "APITestDisk2",
      "resourceType": "Microsoft.Compute/disks",
      "resourceLocation": "westUS",
      "objectType": "Datasource"
    },
    "restoreLocation": "westUS",
    "recoveryOption": "FailIfExists",
    "objectType": "RestoreTargetInfo"
  }
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
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Verfolgen Sie den _Azure-AsyncOperation_-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einer Auftrags-ID zurückgegeben, die für den Abschluss der Wiederherstellungsanforderung weiter nachverfolgt werden sollte.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01

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

Durch die _Anforderungen zum Auslösen der Wiederherstellung_ wurde der Wiederherstellungsauftrag ausgelöst. Verwenden Sie zum Nachverfolgen der resultierenden Auftrags-ID die [GET Jobs-API](/rest/api/dataprotection/jobs/get).

Verwenden Sie den einfachen GET-Befehl zum Nachverfolgen der _JobId_, die in der obigen [Antwort zum Auslösen der Wiederherstellung](#example-response-to-trigger-restore-request) angegeben ist.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "2881cc22-f527-4af4-9b34-46c6c7b72076-Ibz",
    "subscriptionId": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
    "vaultName": "testBkpVault",
    "backupInstanceFriendlyName": "msdiskbackup",
    "policyName": "DiskBackup-Policy",
    "sourceResourceGroup": "RG-DiskBackup",
    "dataSourceSetName": null,
    "dataSourceName": "msdiskbackup",
    "sourceDataStoreName": null,
    "destinationDataStoreName": null,
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-08-26T07%3A18%3A16.157629Z'\"",
    "sourceSubscriptionID": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "dataSourceLocation": "westUS",
    "startTime": "2021-08-26T07:12:09.940517Z",
    "endTime": "2021-08-26T07:18:15.6815066Z",
    "dataSourceType": "Microsoft.Compute/disks",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "restoreType": null,
    "isUserTriggered": true,
    "rehydrationPriority": null,
    "supportedActions": [
      ""
    ],
    "duration": "PT6M5.7409896S",
    "progressUrl": null,
    "errorDetails": null,
    "extendedInfo": {
      "backupInstanceState": null,
      "dataTransferedInBytes": null,
      "targetRecoverPoint": null,
      "sourceRecoverPoint": {
        "recoveryPointID": "3a512290ec6b43d6b9a644869f4a3b38",
        "recoveryPointTime": "2021-08-25T09:03:11.6889015Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed",
          "taskProgress": null,
          "additionalDetails": null
        }
      ],
      "additionalDetails": null
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/3bc62c80-913f-47fa-b829-b7df476682be",
  "name": "3bc62c80-913f-47fa-b829-b7df476682be",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

Der obige Auftragsstatus gibt an, dass der Wiederherstellungsauftrag abgeschlossen ist und die Datenträger im angegebenen Abonnement und in der angegebenen Zielressourcengruppe wiederhergestellt wurden.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Azure Disk Backup](disk-backup-overview.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Artikeln:

- [Rest-API des Azure-Datenschutzanbieters](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)