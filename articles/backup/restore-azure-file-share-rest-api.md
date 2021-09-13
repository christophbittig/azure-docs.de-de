---
title: Wiederherstellen von Azure-Dateifreigaben mit der REST-API
description: Erfahren Sie, wie Sie die REST-API verwenden, um Azure-Dateifreigaben oder bestimmte Dateien aus einem von Azure Backup erstellten Wiederherstellungspunkt wiederherstellen.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7673379df250892671eb01fd2635cca651f69b04
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457435"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Wiederherstellen von Azure-Dateifreigaben mit der REST-API

In diesem Artikel wird erläutert, wie Sie eine vollständige Dateifreigabe oder bestimmte Dateien von einem von [Azure Backup](./backup-overview.md) erstellten Wiederherstellungspunkt mithilfe der REST-API wiederherstellten können.

Am Ende dieses Artikels erfahren Sie, wie Sie die folgenden Vorgänge mit der REST-API ausführen:

* Anzeigen von Wiederherstellungspunkten für eine gesicherte Azure-Dateifreigabe.
* Wiederherstellen einer vollständigen Azure-Dateifreigabe.
* Wiederherstellen einzelner Dateien oder Ordner.

## <a name="prerequisites"></a>Voraussetzungen

Wir gehen davon aus, dass Sie bereits über eine gesicherte Dateifreigabe verfügen, die Sie wiederherstellen möchten. Wenn nicht, lesen Sie [Sichern von Azure-Dateifreigaben mithilfe der Rest-API](backup-azure-file-share-rest-api.md), um zu erfahren, wie Sie eine Sicherung erstellen.

In diesem Artikel verwenden Sie die folgenden Ressourcen:

* **RecoveryServicesVault:** *azurefilesvault*
* **Ressourcengruppe:** *azurefiles*
* **Speicherkonto:** *afsaccount*
* **Dateifreigabe:** *azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>Abrufen von „ContainerName“ und „ProtectedItemName“

Für den größten Teil der API-Aufrufe im Zusammenhang mit der Wiederherstellung müssen Sie Werte für die URI-Parameter {containerName} und {protectedItemName} übergeben. Verwenden Sie das ID-Attribut im Antworttext des [GET backupprotectableitems](/rest/api/backup/protected-items/get)-Vorgangs, um Werte für diese Parameter abzurufen. In unserem Beispiel lautet die ID der Dateifreigabe, die wir schützen wollen:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Die Werte lassen werden also wie folgt übersetzt:

* {containername} – *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName} – *azurefileshare;azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Abrufen von Wiederherstellungspunkten für die gesicherte Azure-Dateifreigabe

Um gesicherte Dateifreigaben oder Dateien wiederherzustellen, wählen Sie zunächst einen Wiederherstellungspunkt für den Wiederherstellungsvorgang aus. Die verfügbaren Wiederherstellungspunkte eines gesicherten Elements können mithilfe des REST-API-Aufrufs [Recovery Point-List](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) aufgelistet werden. Dabei handelt es sich um einen GET-Vorgang mit allen relevanten Werten.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Legen Sie die URI-Werte wie folgt fest:

* {fabricName}: *Azure*
* {vaultName}: *azurefilesvault*
* {containername}: *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName}: *azurefileshare;azurefiles*
* {ResourceGroupName}: *azurefiles*

Der GET-URI enthält alle erforderlichen Parameter. Weiterer Anforderungstext ist nicht erforderlich.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response-for-fetch-recovery-points"></a>Beispielantwort für das Abrufen von Wiederherstellungspunkten

Wenn der GET-URI übermittelt wird, wird die Antwort „200“ zurückgegeben:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Der Wiederherstellungspunkt wird durch das {name}-Feld in der Antwort oben angegeben.

## <a name="full-share-recovery-using-rest-api"></a>Wiederherstellung einer vollständigen Freigabe mit der REST-API

Verwenden Sie diese Wiederherstellungsoption, um die vollständige Dateifreigabe am ursprünglichen oder einem alternativen Speicherort wiederherzustellen.
Das Auslösen der Wiederherstellung ist eine POST-Anforderung, und Sie können diesen Vorgang mit der REST-API [trigger restore](/rest/api/backup/restores/trigger) durchführen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Die Werte {containerName} und {protectedItemName} werden wie [hier](#fetch-containername-and-protecteditemname) angegeben festgelegt, und „recoveryPointID“ ist das {name}-Feld des oben erwähnten Wiederherstellungspunkts.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Erstellen des Anforderungstexts

Zum Auslösen einer Wiederherstellung für eine Azure-Dateifreigabe werden folgende Komponenten des Anforderungstexts angegeben:

Name |  type   |   BESCHREIBUNG
--- | ---- | ----
Eigenschaften | AzureFileShareRestoreRequest | RestoreRequestResource-Eigenschaften

Die vollständige Liste mit Definitionen des Anforderungstexts und weiteren Einzelheiten finden Sie im [Dokument zur REST-API zum Auslösen der Wiederherstellung](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>An ursprünglichem Speicherort wiederherstellen

#### <a name="request-body-example-for-restore-to-original-location"></a>Beispiel für Anforderungstext für das Wiederherstellen am ursprünglichen Speicherort

Der folgende Anforderungstext definiert Eigenschaften, die zum Auslösen der Wiederherstellung einer Azure-Dateifreigabe erforderlich sind.

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Wiederherstellen an einem alternativen Speicherort

Geben Sie die folgenden Parameter für die Wiederherstellung an einem alternativen Standort an:

* **targetResourceId**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **name:** Die Dateifreigabe in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **targetFolderPath**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden.

#### <a name="request-body-example-for-restore-to-alternate-location"></a>Beispiel für den Anforderungstext für das Wiederherstellen an einem alternativen Speicherort

Der folgende Anforderungstext stellt die Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* in der Dateifreigabe *azurefiles1* im Speicherkonto *afaccount1* wieder her.

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Antwort

Das Auslösen einer Wiederherstellung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). In diesem Vorgang wird ein anderer Vorgang erstellt, der separat nachverfolgt werden muss.
Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

#### <a name="response-example"></a>Beispielantwort

Nachdem Sie den *POST*-URI für das Auslösen einer Wiederherstellung gesendet haben, wird als erste Antwort „202 (Akzeptiert)“ mit einem location- oder Azure-async-Header zurückgegeben.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Verfolgen Sie anschließend den resultierenden Vorgang mithilfe des location- oder Azure-AsyncOperation-Headers mit einem GET-Befehl nach.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Wenn der Vorgang abgeschlossen ist, wird „200 (OK)“ mit der ID des resultierenden Wiederherstellungsauftrags im Antworttext zurückgegeben.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Bei einer alternativen Standortwiederherstellung sieht der Antworttext wie folgt aus:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Da es sich beim Sicherungsauftrag um einen Vorgang mit langer Ausführungsdauer handelt, muss er gemäß der Erläuterung im [Dokument zur Auftragsüberwachung mit der REST-API](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job) nachverfolgt werden.

## <a name="item-level-recovery-using-rest-api"></a>Wiederherstellung auf Elementebene mit der REST-API

Sie können diese Wiederherstellungsoption verwenden, um einzelne Dateien oder Ordner am ursprünglichen oder einem alternativen Speicherort wiederherzustellen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Die Werte {containerName} und {protectedItemName} werden wie [hier](#fetch-containername-and-protecteditemname) angegeben festgelegt, und „recoveryPointID“ ist das {name}-Feld des oben erwähnten Wiederherstellungspunkts.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body-for-item-level-recovery-using-rest-api"></a>Erstellen des Anforderungstexts für die Wiederherstellung auf Elementebene mithilfe der REST-API

Zum Auslösen einer Wiederherstellung für eine Azure-Dateifreigabe werden folgende Komponenten des Anforderungstexts angegeben:

Name |  type   |   BESCHREIBUNG
--- | ---- | ----
Eigenschaften | AzureFileShareRestoreRequest | RestoreRequestResource-Eigenschaften

Die vollständige Liste mit Definitionen des Anforderungstexts und weiteren Einzelheiten finden Sie im [Dokument zur REST-API zum Auslösen der Wiederherstellung](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location-for-item-level-recovery-using-rest-api"></a>Wiederherstellen am ursprünglichen Speicherort für die Wiederherstellung auf Elementebene mithilfe der REST-API

Der folgende Anforderungstext soll die Datei *Restoretest.txt* in der Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* wiederherstellen.

Erstellen des Anforderungstexts

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location-for-item-level-recovery-using-rest-api"></a>Wiederherstellen an einem alternativen Speicherort für die Wiederherstellung auf Elementebene mithilfe der REST-API

Der folgende Anforderungstext soll die Datei *Restoretest.txt* in der Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* im Ordner *restoredata* der Dateifreigabe *azurefiles1* im Speicherkonto *afaccount1* wiederherstellen.

Erstellen des Anforderungstexts

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

Die Antwort muss gemäß der Erläuterung im obigen Abschnitt für [vollständige Freigabewiederherstellungen](#full-share-recovery-using-rest-api) verarbeitet werden.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Verwalten von Azure-Dateifreigabensicherungen mit der REST-API](manage-azure-file-share-rest-api.md).
