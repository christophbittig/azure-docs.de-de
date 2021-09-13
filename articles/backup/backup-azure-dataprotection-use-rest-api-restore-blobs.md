---
title: Wiederherstellen von Blobs in einem Speicherkonto mithilfe der REST-API für den Schutz von Daten in Azure
description: In diesem Artikel erfahren Sie, wie Sie Blobs eines Speicherkontos mithilfe der REST-API wiederherstellen.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 9b8d21e6-3e23-4345-bb2b-e21040996afd
ms.openlocfilehash: 0ba444126026dc77d6e9b963edb6bed3d108c97b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598634"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-data-protection-rest-api"></a>Wiederherstellen von Azure-Blobs für einen bestimmten Zeitpunkt mithilfe der REST-API für den Schutz von Daten in Azure

In diesem Artikel wird beschrieben, wie [Blobs](blob-backup-overview.md) mithilfe von Azure PowerShell für einen beliebigen Zeitpunkt wiederhergestellt werden können.

> [!IMPORTANT]
> Bevor Sie mit der Wiederherstellung von Azure-Blobs mithilfe von Azure PowerShell fortfahren, machen Sie sich mit einigen [wichtigen Punkten](blob-restore.md#before-you-start) vertraut.

In diesem Artikel lernen Sie Folgendes:

- Wiederherstellen von Azure-Blobs für einen bestimmten Zeitpunkt

- Nachverfolgen des Status des Wiederherstellungsvorgangs

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Sicherungstresors](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Erstellen einer Blobsicherungsrichtlinie](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

- [Konfigurieren einer Blobsicherung](backup-azure-dataprotection-use-rest-api-backup-blobs.md)

In den Beispielen wird auf einen vorhandenen Sicherungstresor _TestBkpVault_ unter der Ressourcengruppe _testBkpVaultRG_ verwiesen, in dem Blobs in einem Speicherkonto namens „msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d“ gespeichert sind.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Wiederherstellen von Azure-Blobs in einem Speicherkonto

### <a name="fetching-the-valid-time-range-for-restore"></a>Abrufen des gültigen Zeitbereichs für die Wiederherstellung

Da die operative Sicherung für Blobs fortlaufend erfolgt, gibt es keine eindeutigen Punkte, anhand derer eine Wiederherstellung durchgeführt werden kann. Stattdessen muss der gültige Zeitbereich abgerufen werden, in dem Blobs für einen beliebigen Zeitpunkt wiederhergestellt werden können. In diesem Beispiel suchen wir nach gültigen Zeitbereichen für die Wiederherstellung innerhalb der letzten 30 Tage.

Die wiederherstellbaren Zeitbereiche können mithilfe der API [zum Auffinden wiederherstellbarer Zeitbereiche](/rest/api/dataprotection/restorable-time-ranges/find) aufgelistet werden. Es handelt sich um eine *POST*-API, die einen Vorgang auslöst, um den Bereich fortlaufender Sicherungen für die Blobs im Speicherkonto zu berechnen.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/findRestorableTimeRanges?api-version=2021-01-01
```

In unserem Beispiel ergibt dies Folgendes:

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/findRestorableTimeRanges?api-version=2021-01-01
```

#### <a name="create-the-request-body-to-fetch-valid-time-ranges-for-restore"></a>Erstellen des Anforderungstexts zum Abrufen gültiger Zeitbereiche für die Wiederherstellung

Zum Auslösen eines Vorgangs, mit dem die gültigen Zeitbereiche berechnet werden, sind die folgenden Komponenten eines Anforderungstexts erforderlich:

|**Name**  |**Typ**  |**Beschreibung**  |
|---------|---------|---------|
|sourceDatastoreType     |   [RestoreSourceDataStoreType](/rest/api/dataprotection/restorable-time-ranges/find#restoresourcedatastoretype)      |    Der Datenspeicher, der die wiederherzustellenden Daten enthält.     |
|startTime     |    String     |  Die Startzeit für die Anforderung zum Auflisten von Wiederherstellungsbereichen. Die Angabe erfolgt im ISO 8601-Format.       |
|endTime     |    String     |    Die Endzeit für die Anforderung zum Auflisten von Wiederherstellungsbereichen. Die Angabe erfolgt im ISO 8601-Format.     |

##### <a name="example-request-body-to-fetch-valid-time-range"></a>Beispielanforderungstext zum Abrufen eines gültigen Zeitbereichs

Der folgende Anforderungstext definiert Eigenschaften, die zum Abrufen der Zeitbereiche der fortlaufenden Daten erforderlich sind, die wiederhergestellt werden können. Da sich Blobsicherungen im Speicherkonto befinden, ist der Datenspeicher der „Betriebsspeicher“. Sie können Start- und Endzeit geben, um den Suchvorgang einzugrenzen und den verfügbaren Zeitbereich zurückzugeben.

```json
{
  "sourceDataStoreType": "OperationalStore",
  "startTime": "",
  "endTime": ""
}
```

#### <a name="responses-for-fetch-valid-time-ranges"></a>Antworten zum Abrufen gültiger Zeitbereiche

Nachdem Sie die *POST*-Anforderung übermittelt haben, wird die Antwort „200 (OK)“ mit der Start- und Endzeit des Bereichs zurückgegeben, der innerhalb der in der Anforderung angegebenen Start- und Endzeit wiederhergestellt werden kann.

|**Name**  |**Typ**  |**Beschreibung**  |
|---------|---------|---------|
|200(OK)     |   [AzureBackupFindRestorableTimeRangesResponseResource](/rest/api/dataprotection/restorable-time-ranges/find#azurebackupfindrestorabletimerangesresponseresource)      |    OK     |
|Andere Statuscodes     |    [CloudError](/rest/api/dataprotection/restorable-time-ranges/find#clouderror)     |  Fehlerantwort mit Beschreibung des Grunds für den Fehler.       |

##### <a name="example-response-for-fetch-valid-time-ranges"></a>Beispielantwort zum Abrufen gültiger Zeitbereiche

```http
HTTP/1.1 200 OK
Content-Length: 379
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a2b7c2d9-01f5-499a-b521-55da4862c79a
x-ms-routing-request-id: CENTRALUSEUAP:20210708T184646Z:4996a2bf-2df8-48a7-9b53-a552466a27f7
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 18:46:45 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges",
  "properties": {
    "restorableTimeRanges": [
      {
        "startTime": "2021-07-06T18:46:45.947728Z",
        "endTime": "2021-07-08T18:46:45.9932408Z",
        "objectType": "RestorableTimeRange"
      }
    ],
    "objectType": "AzureBackupFindRestorableTimeRangesResponse"
  }
}
```

### <a name="preparing-the-restore-request"></a>Die Wiederherstellungsanforderung wird vorbereitet

Sobald der Zeitpunkt für die Wiederherstellung in demselben Speicherkonto feststeht, gibt es mehrere Optionen für die Wiederherstellung.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Durchführen einer Zeitpunktwiederherstellung für alle Blobs

Bei dieser Option werden alle Blockblobs im Speicherkonto wiederhergestellt, indem ein Rollback zum ausgewählten Zeitpunkt durchgeführt wird. Bei Speicherkonten, die große Datenmengen enthalten oder eine hohe Änderungsrate aufweisen, kann der Vorgang längere Zeit in Anspruch nehmen.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-all-blobs"></a>Erstellen des Anforderungstexts für eine Zeitpunktwiederherstellung aller Blobs

Hier eine Auflistung der wichtigsten Punkte, die in diesem Szenario zu beachten sind:

- Die Wiederherstellung erfolgt in demselben Speicherkonto, was bedeutet, dass das Zielobjekt für die Wiederherstellung mit der Datenquelle identisch ist. Dies ist unten im Abschnitt mit Informationen zum Wiederherstellungsziel angegeben.
- Es handelt sich um fortlaufende Sicherungen. Daher ist der Wiederherstellungszeitpunkt ein bestimmter Zeitpunkt und kein bestimmter Wiederherstellungspunkt.
- Es werden alle Blobs wiederhergestellt.
- Der Quelldatenspeicher, also der Speicher, in dem sich die Sicherungen befinden, ist dasselbe Speicherkonto. Daher ist der Quelldatenspeicher der „Betriebsspeicher“.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "RestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="restoring-few-containers-to-a-point-in-time"></a>Durchführen einer Zeitpunktwiederherstellung für einige Container

Mit dieser Option können Sie bis zu zehn Container zur Wiederherstellung auswählen, oder eine Teilmenge von Blobs mithilfe eines Präfixabgleichs wiederherstellen. Sie können bis zu 10 lexikografische Bereiche von Blobs innerhalb eines einzelnen Containers oder aus mehreren Containern wiederherstellen, um diese Blobs auf einen Zustand zu einem bestimmten Zeitpunkt zurückzusetzen. Bei Verwendung von Präfixen sind folgende Punkte zu beachten:

- Sie können einen Schrägstrich (/) verwenden, um den Containernamen vom Blobpräfix zu trennen.
- Der Anfang des angegebenen Bereichs wird mit eingeschlossen, das Ende des angegebenen Bereichs wird jedoch ausgeschlossen.

[Erfahren Sie mehr](blob-restore.md#use-prefix-match-for-restoring-blobs) über die Verwendung von Präfixen zum Wiederherstellen von Blobbereichen.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs"></a>Erstellen des Anforderungstexts für eine Zeitpunktwiederherstellung ausgewählter Container oder einiger Blobs

Hier eine Auflistung der wichtigsten Punkte, die in diesem Szenario zu beachten sind:

- Die Wiederherstellung erfolgt in demselben Speicherkonto, was bedeutet, dass das Zielobjekt für die Wiederherstellung mit der Datenquelle identisch ist. Dies ist unten im Abschnitt mit Informationen zum Wiederherstellungsziel angegeben.
- Es handelt sich um fortlaufende Sicherungen. Daher ist der Wiederherstellungszeitpunkt ein bestimmter Zeitpunkt und kein bestimmter Wiederherstellungspunkt.
- Es werden einige Elemente innerhalb des Speicherkontos wiederhergestellt. Dabei kann es sich um Container oder Blobs mit einem Präfixmuster handeln.
- Der Quelldatenspeicher, also der Speicher, in dem sich die Sicherungen befinden, ist dasselbe Speicherkonto. Daher ist der Quelldatenspeicher der „Betriebsspeicher“.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "ItemLevelRestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "restoreCriteria": [
        {
          "objectType": "RangeBasedItemLevelRestoreCriteria",
          "minMatchingValue": "Container1",
          "maxMatchingValue": "Container10-0"
        }
      ],
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="validating-restore-requests"></a>Überprüfen von Wiederherstellungsanforderungen

Nachdem der Anforderungstext vorbereitet wurde, kann er mithilfe der [API zum Überprüfen auf Wiederherstellung](/rest/api/dataprotection/backup-instances/validate-for-restore) überprüft werden. Wie bei der API zum Überprüfen auf Sicherung ist dies ein *POST*-Vorgang.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

In unserem Beispiel ergibt dies Folgendes:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/validateRestore?api-version=2021-01-01"
```

Der Anforderungstext für diese POST-API wird [hier](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body) beschrieben. Dasselbe wurde im Abschnitt oben für die Szenarios zum [Widerherstellen aller Blobs](#constructing-the-request-body-for-point-in-time-restore-of-all-blobs) und [Wiederherstellen einiger Elemente](#constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs) erstellt. Nun wird dasselbe verwendet, um einen Überprüfungsvorgang auszulösen.

##### <a name="response-to-validate-restore-requests"></a>Antwort auf Anforderungen zum Überprüfen der Wiederherstellung

Die Anforderung zum Überprüfen der Wiederherstellung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Überprüfungsanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

###### <a name="example-response-to-restore-validate-request"></a>Beispielantwort auf eine Anforderung zum Überprüfen der Wiederherstellung

Sobald der *POST*-Vorgang übermittelt wurde, lautet die erste Antwort „202 Akzeptiert“ zusammen mit einem Azure-asyncOperation-Header.

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

Verfolgen Sie den Azure-AsyncOperation-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einem Erfolgsstatus als Antwort zurückgegeben.

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

#### <a name="triggering-restore-requests"></a>Auslösen von Wiederherstellungsanforderungen

Das Auslösen des Wiederherstellungsvorgangs erfolgt durch eine ***POST***-API. Alle Details zum Auslösen des Wiederherstellungsvorgangs sind [hier](/rest/api/dataprotection/backup-instances/trigger-restore) dokumentiert.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

In unserem Beispiel ergibt dies Folgendes:

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/restore?api-version=2021-01-01"
```

##### <a name="creating-a-request-body-for-restore-operations"></a>Erstellen eines Anforderungstexts für Wiederherstellungsvorgänge

Nachdem die Anforderungen überprüft wurden, kann derselbe Anforderungstext verwendet werden, um die Wiederherstellungsanforderung mit geringfügigen Änderungen auszulösen.

###### <a name="example-request-body-for-all-blobs-restore"></a>Beispielanforderungstext zum Wiederherstellen aller Blobs

Die einzige Änderung gegenüber dem Anforderungstext zum Überprüfen der Wiederherstellung besteht darin, dass das Objekt „restoreRequest“ am Anfang entfernt wird.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "RestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00Z"
}
```

###### <a name="example-request-body-for-items-or-few-blobs-restore"></a>Beispielanforderungstext zum Wiederherstellen von Elementen oder einigen Blobs

Die einzige Änderung gegenüber dem Anforderungstext zum Überprüfen der Wiederherstellung besteht darin, dass das Objekt „restoreRequest“ am Anfang entfernt wird.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "ItemLevelRestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "restoreCriteria": [
      {
        "objectType": "RangeBasedItemLevelRestoreCriteria",
        "minMatchingValue": "Container1",
        "maxMatchingValue": "Container2"
      }
    ],
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
}
```

#### <a name="response-to-trigger-restore-requests"></a>Antwort auf Anforderungen zum Auslösen der Wiederherstellung

Die Anforderung zum Auslösen der Wiederherstellung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |         |  Status der Wiederherstellungsanforderung       |
|202 – Akzeptiert     |         |     Zulässig    |

##### <a name="example-response-to-trigger-restore-request"></a>Beispielantwort auf eine Anforderung zum Auslösen der Wiederherstellung

Sobald der *POST*-Vorgang übermittelt wurde, lautet die erste Antwort „202 Akzeptiert“ zusammen mit einem Azure-asyncOperation-Header.

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

Verfolgen Sie den Azure-AsyncOperation-Header mit einer einfachen *GET*-Anforderung nach. Wenn die Anforderung erfolgreich ist, wird „200 OK“ mit einer Auftrags-ID zurückgegeben, die für den Abschluss der Wiederherstellungsanforderung weiter nachverfolgt werden sollte.

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

#### <a name="tracking-jobs"></a>Aufträge nachverfolgen

Die Anforderungen zum Auslösen der Wiederherstellung haben den Wiederherstellungsauftrag ausgelöst, und die resultierende Auftrags-ID sollte mithilfe der [API zum Abrufen von Aufträgen](/rest/api/dataprotection/jobs/get) nachverfolgt werden.

Verwenden Sie den einfachen GET-Befehl zum Nachverfolgen der Auftrags-ID (JobId), die in der obigen [Antwort zum Auslösen der Wiederherstellung](#example-response-to-trigger-restore-request) angegeben ist.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "4195ca6c-e02d-11eb-b0b1-70bc105e2242",
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
    "vaultName": "BV-JPE-GRS",
    "backupInstanceFriendlyName": "msblobbackup",
    "policyName": "BlobBackup-Policy",
    "sourceResourceGroup": "RG-BlobBackup",
    "dataSourceName": "msblobbackup",
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-07-08T20%3A48%3A36.6999667Z'\"",
    "sourceSubscriptionID": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "dataSourceLocation": "westus",
    "startTime": "2021-07-08T20:44:19.5467125Z",
    "endTime": "2021-07-08T20:48:35.8297312Z",
    "dataSourceType": "Microsoft.Storage/storageAccounts/blobServices",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "isUserTriggered": true,
    "supportedActions": [
      ""
    ],
    "duration": "PT4M16.2830187S",
    "extendedInfo": {
      "sourceRecoverPoint": {
        "recoveryPointTime": "2021-07-08T00:00:00Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed"
        }
      ]
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
  "name": "c4bd49a1-0645-4eec-b207-feb818962852",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

Der oben gezeigte Auftragsstatus gibt an, dass der Wiederherstellungsauftrag abgeschlossen ist und alle Blobs für den angegebenen Zeitpunkt wiederhergestellt wurden.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Sicherung für Azure-Blobs](blob-backup-overview.md)

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Dokumenten:

- [REST-API des Azure-Datenschutzanbieters](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)