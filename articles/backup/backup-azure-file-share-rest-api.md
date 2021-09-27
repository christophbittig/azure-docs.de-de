---
title: Sichern von Azure-Dateifreigaben mit der REST-API
description: Hier erfahren Sie, wie Sie Azure-Dateifreigaben mithilfe der REST-API im Recovery Services-Tresor sichern.
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 152613031eb6c3ba7a0c1078d3d53bcf170ce646
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424166"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Sichern einer Azure-Dateifreigabe mithilfe von Azure Backup über die REST-API

In diesem Artikel wird beschrieben, wie Sie eine Azure-Dateifreigabe mithilfe von Azure Backup über die REST-API sichern.

In diesem Artikel wird davon ausgegangen, dass Sie bereits einen Recovery Services-Tresor und Richtlinien für die Konfiguration des Backups für Ihre Dateifreigabe erstellt haben. Wenn nicht, finden Sie Informationen zum Erstellen neuer Tresore und Richtlinien in den REST-API-Tutorials zum [Erstellen eines Tresors](./backup-azure-arm-userestapi-createorupdatevault.md) und zum [Erstellen einer Richtlinie](./backup-azure-arm-userestapi-createorupdatepolicy.md).

In diesem Artikel verwenden Sie die folgenden Ressourcen:

- **RecoveryServicesVault:** *azurefilesvault*

- **Richtlinie:** *schedule1*

- **Ressourcengruppe:** *azurefiles*

- **Speicherkonto**: *testvault2*

- **Dateifreigabe**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Konfigurieren der Sicherung für eine nicht geschützte Azure-Dateifreigabe über die REST-API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Ermitteln von Speicherkonten mit ungeschützten Azure-Dateifreigaben

Der Tresor muss im Abonnement alle Azure-Speicherkonten mit Dateifreigaben ermitteln, die im Recovery Services-Tresor gesichert werden können. Dies wird über den [„refresh“-Vorgang](/rest/api/backup/protection-containers/refresh) ausgelöst. Es handelt sich um einen asynchronen *POST*-Vorgang, mit dem sichergestellt wird, dass der Tresor die neueste Liste aller ungeschützten Azure-Dateifreigaben im aktuellen Abonnement abruft und „zwischenspeichert“. Sobald die Dateifreigabe „zwischengespeichert“ wurde, kann Recovery Services auf die Dateifreigabe zugreifen und sie schützen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

Der POST-URI enthält die Parameter `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` und `{fabricName}`. In unserem Beispiel lauten die Werte für die verschiedenen Parameter wie folgt:

- `{fabricName}`: *Azure*

- `{vaultName}`: *azurefilesvault*

- `{vaultresourceGroupName}`: *azurefiles*

- $filter=backupManagementType eq 'AzureStorage'

Da alle erforderlichen Parameter im URI angegeben sind, besteht keine Notwendigkeit eines separaten Anforderungstexts.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Antworten auf den Aktualisierungsvorgang

Der „refresh“-Vorgang ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

##### <a name="example-responses-to-the-refresh-operation"></a>Beispielantworten auf den Aktualisierungsvorgang

Nachdem die *POST*-Anforderung gesendet wurde, wird die Antwort „202 (Akzeptiert)“ zurückgegeben.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Verfolgen Sie den resultierenden Vorgang mithilfe des „Location“-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Nachdem alle Azure-Speicherkonten ermittelt wurden, gibt der GET-Befehl die Antwort „204 (Kein Inhalt)“ zurück. Der Tresor kann jetzt jedes Speicherkonto mit Dateifreigaben ermitteln, das innerhalb des Abonnements gesichert werden kann.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Abrufen der Liste der Speicherkonten mit Dateifreigaben, die mit dem Recovery Services-Tresor gesichert werden können

Um zu bestätigen, dass eine „Zwischenspeicherung“ erfolgt, werden alle Azure-Speicherkonten im Abonnement aufgelistet, die Dateifreigaben enthalten und mit dem Recovery Services-Tresor gesichert werden können. Suchen Sie in der Antwort dann nach dem gewünschten Speicherkonto. Dies erfolgt mithilfe des Vorgangs [GET ProtectableContainers](/rest/api/backup/protectable-containers/list).

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

Der *GET*-URI enthält alle erforderlichen Parameter. Es ist kein zusätzlicher Anforderungstext erforderlich.

Beispiel des Antworttexts:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Da wird das Speicherkonto *testvault2* im Antworttext mit dem Anzeigenamen finden können, war der oben ausgeführte Aktualisierungsvorgang erfolgreich. Der Recovery Services-Tresor kann nun erfolgreich Speicherkonten mit ungeschützten Dateifreigaben im selben Abonnement ermitteln.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrieren des Speicherkontos beim Recovery Services-Tresor

Dieser Schritt ist nur erforderlich, wenn Sie das Speicherkonto noch nicht beim Tresor registriert haben. Sie können den Tresor über den [ProtectionContainers-Register-Vorgang](/rest/api/backup/protection-containers/register) registrieren.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Legen Sie die Variablen für den URI wie folgt fest:

- {resourceGroupName} – *azurefiles*
- {fabricName} – *Azure*
- {vaultName} – *azurefilesvault*
- {containerName}: Dies ist das name-Attribut im Antworttext des Vorgangs „GET ProtectableContainers“.
   In unserem Beispiel ist dies *StorageContainer;Storage;AzureFiles;testvault2*

>[!NOTE]
> Übernehmen Sie immer das name-Attribut der Antwort, und geben Sie es in diese Anforderung ein. Das container-name-Format darf nicht hartcodiert oder erstellt werden. Wenn Sie es erstellen oder hartcodieren, schlägt der API-Aufruf fehl, falls sich das container-name-Format in Zukunft ändert.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Der Text von „Anforderung erstellen“ lautet wie folgt:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"

 }
}
```

Die vollständige Liste mit Definitionen des Anforderungstexts und weitere Einzelheiten finden Sie unter[ProtectionContainers-Register](/rest/api/backup/protection-containers/register#azurestoragecontainer).

Dabei handelt es sich um einen asynchronen Vorgang, der zwei Antworten zurückgibt: „202 – Akzeptiert“, wenn der Vorgang akzeptiert wird, und „200 OK“, wenn der Vorgang abgeschlossen ist.  Um den Vorgangsstatus nachzuverfolgen, verwenden Sie den location-Header, um den aktuellen Status des Vorgangs abzurufen.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Beispiel für Antworttext, wenn der Vorgang abgeschlossen ist:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Sie können anhand des Wertes des Parameters *registrationstatus* im Antworttext überprüfen, ob die Registrierung erfolgreich war. In unserem Fall wird als Status „Registriert für *testvault2*“ angezeigt, der Registrierungsvorgang war also erfolgreich.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Abfragen aller ungeschützten Dateifreigaben unter einem Speicherkonto

Mit dem [Protection Containers-Inquire](/rest/api/backup/protection-containers/inquire)-Vorgang können Sie schützbare Elemente in einem Speicherkonto abfragen. Es handelt sich um einen asynchronen Vorgang, und die Ergebnisse sollten anhand des location-Headers nachverfolgt werden.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Legen Sie die Variablen für den oben stehenden URI wie folgt fest:

- {vaultName} – *azurefilesvault*
- {fabricName} – *Azure*
- {containerName}: Verweist auf das name-Attribut im Antworttext des Vorgangs „GET ProtectableContainers“. In unserem Beispiel ist dies *StorageContainer;Storage;AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Wenn die Anforderung erfolgreich ist, gibt sie den Statuscode „OK“ zurück.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Auswählen der zu sichernden Dateifreigabe

Sie können alle schützbaren Elemente unter dem Abonnement auflisten und die gewünschte Dateifreigabe, die gesichert werden soll, mit dem [GET backupprotectableItems](/rest/api/backup/backup-protectable-items/list)-Vorgang suchen.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Erstellen Sie den URI wie folgt:

- {vaultName} – *azurefilesvault*
- {$filter} – *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Beispiel für eine Antwort:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Die Antwort enthält die Liste aller ungeschützten Azure-Dateifreigaben sowie alle Informationen, die in Azure Recovery Services zum Konfigurieren der Sicherung erforderlich sind.

### <a name="enable-backup-for-the-file-share"></a>Aktivieren der Sicherung für die Dateifreigabe

Nachdem die betreffende Dateifreigabe mit dem Anzeigenamen „identifiziert“ wurde, wählen Sie die Richtlinie für den Schutz aus. Weitere Informationen zu vorhandenen Richtlinien im Tresor finden Sie in der [Liste der Sicherungsrichtlinien](/rest/api/backup/backup-policies/list). Wählen Sie dann die [entsprechende Richtlinie](/rest/api/backup/protection-policies/get) aus, indem Sie auf den Richtliniennamen verweisen. Informationen zum Erstellen von Richtlinien finden Sie im [Tutorial zum Erstellen von Richtlinien](./backup-azure-arm-userestapi-createorupdatepolicy.md).

Beim Aktivieren des Schutzes handelt es sich um einen asynchronen *PUT*-Vorgang, mit dem ein „geschütztes Element“ erstellt wird.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Legen Sie die Variablen **containername** und **protecteditemname** unter Verwendung des ID-Attributs im Antworttext des GET backupprotectableitems-Vorgangs fest.

In unserem Beispiel lautet die ID der Dateifreigabe, die wir schützen wollen:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} – *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} – *azurefileshare;testshare*

Sie können auch auf das **name** -Attribut des Schutzcontainers und auf Antworten zu schützbaren Elementen verweisen.

>[!NOTE]
>Übernehmen Sie immer das name-Attribut der Antwort, und geben Sie es in diese Anforderung ein. Das container-name-Format und das Format des Namens des geschützten Elements dürfen nicht hartcodiert oder erstellt werden. Wenn Sie es erstellen oder hartcodieren, schlägt der API-Aufruf fehl, falls sich das container-name-Format oder das Format des Namens des geschützten Elements in Zukunft ändert.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Erstellen Sie einen Anforderungstext:

Der folgende Anforderungstext definiert zum Erstellen eines geschützten Elements erforderliche Eigenschaften.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

Die **sourceResourceId** ist die **parentcontainerFabricID** als Antwort auf „GET backupprotectableItems“.

Beispiel für eine Antwort

Die Erstellung eines geschützten Elements ist ein asynchroner Vorgang, bei dem ein weiterer Vorgang erstellt wird, der nachverfolgt werden muss. Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

Nachdem Sie die *PUT*-Anforderung für die Erstellung oder Aktualisierung eines geschützten Elements gesendet haben, wird als erste Antwort „202 (Akzeptiert)“ mit einem location-Header zurückgegeben.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Verfolgen Sie anschließend den resultierenden Vorgang mithilfe des location- oder Azure-AsyncOperation-Headers mit einem *GET*-Befehl nach.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Wenn der Vorgang abgeschlossen ist, wird „200 (OK)“ mit dem Inhalt des geschützten Elements im Antworttext zurückgegeben.

Beispiel für Antworttext:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Dies bestätigt, dass der Schutz für die Dateifreigabe aktiviert ist und die erste Sicherung entsprechend dem Richtlinienzeitplan ausgelöst wird.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Auslösen einer bedarfsgesteuerten Sicherung für Dateifreigaben

Nachdem eine Azure-Dateifreigabe für die Sicherung konfiguriert wurde, werden Sicherungen entsprechend dem Richtlinienzeitplan durchgeführt. Sie können warten, bis die erste geplante Sicherung durchgeführt wird, oder jederzeit eine bedarfsgesteuerte Sicherung auslösen.

Das Auslösen einer bedarfsgesteuerten Sicherung ist ein POST-Vorgang.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} und {protectedItemName} entsprechen den oben stehenden beim Aktivieren der Sicherung erstellten Werten. In unserem Beispiel ergibt dies Folgendes:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Erstellen des Anforderungstexts

Zum Auslösen einer bedarfsgesteuerten Sicherung werden die folgenden Komponenten des Anforderungstexts verwendet.

| Name       | type                       | BESCHREIBUNG                       |
| ---------- | -------------------------- | --------------------------------- |
| Eigenschaften | AzurefilesharebackupReques | BackupRequestResource-Eigenschaften |

Die vollständige Liste mit Definitionen des Anforderungstexts und weitere Einzelheiten finden Sie im [Dokument zur REST-API zum Auslösen von Sicherungen für geschützte Elemente](/rest/api/backup/backups/trigger#request-body).

Beispiel für Anforderungstext

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Antworten auf den bedarfsgesteuerten Sicherungsvorgang

Das Auslösen einer bedarfsgesteuerten Sicherung ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Beispielantworten auf den bedarfsgesteuerten Sicherungsvorgang

Nachdem Sie die *POST*-Anforderung für eine bedarfsgesteuerte Sicherung gesendet haben, wird als erste Antwort „202 (Akzeptiert)“ mit einem location- oder Azure-async-Header zurückgegeben.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Verfolgen Sie anschließend den resultierenden Vorgang mithilfe des location- oder Azure-AsyncOperation-Headers mit einem *GET*-Befehl nach.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Wenn der Vorgang abgeschlossen ist, wird „200 (OK)“ mit der ID des resultierenden Sicherungsauftrags im Antworttext zurückgegeben.

#### <a name="sample-response-body"></a>Beispiel für Antworttext

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Da es sich bei dem Sicherungsauftrag um einen Vorgang mit langer Ausführungsdauer handelt, muss er wie im [Dokument zum Überwachen von Aufträgen mit der REST-API](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job) erläutert nachverfolgt werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Wiederherstellen von Azure-Dateifreigaben mit der REST-API](restore-azure-file-share-rest-api.md).
