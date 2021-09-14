---
title: Wiederherstellen virtueller Azure-Computer mithilfe der REST-API
description: In diesem Artikel erfahren Sie, wie Sie Wiederherstellungsvorgänge der Sicherung von Azure-VMs mit der REST-API verwalten.
ms.topic: conceptual
ms.date: 08/26/2021
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: f82adee9690c0114fef17640672c7326cffc8481
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966075"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Wiederherstellen virtueller Azure-Computer mit der REST-API

Nachdem die Sicherung eines virtuellen Azure-Computers mit Azure Backup abgeschlossen ist, können Sie vollständige virtuelle Azure-Computer oder Datenträger über die gleiche Sicherungskopie wiederherstellen. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Azure-Computer oder Datenträger mit der REST-API wiederherstellen.

Für jeden Wiederherstellungsvorgang muss zunächst der entsprechende Wiederherstellungspunkt angegeben werden.

## <a name="select-recovery-point"></a>Auswählen eines Wiederherstellungspunkts

Die verfügbaren Wiederherstellungspunkte eines Sicherungselements können mithilfe der [REST-API zum Auflisten von Wiederherstellungspunkten](/rest/api/backup/recovery-points/list) aufgelistet werden. Dies ist ein einfacher *GET*-Vorgang mit allen relevanten Werten.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

Informationen zur Erstellung von `{containerName}` und `{protectedItemName}` finden Sie [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` ist „Azure“.

Der *GET*-URI enthält alle erforderlichen Parameter. Ein zusätzlicher Anforderungstext ist nicht erforderlich.

### <a name="responses"></a>Antworten

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Beispielantwort

Wenn der *GET*-URI übermittelt wird, wird die Antwort „200 (OK)“ zurückgegeben.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Der Wiederherstellungspunkt wird durch das Feld `{name}` in der obigen Antwort angegeben.

## <a name="restore-operations"></a>Wiederherstellungsvorgänge

Nachdem Sie den [relevanten Wiederherstellungspunkt](#select-recovery-point) ausgewählt haben, lösen Sie als Nächstes den Wiederherstellungsvorgang aus.

***Alle Wiederherstellungsvorgänge für das Sicherungselement werden mit derselben *POST*-API ausgeführt. Nur der Anforderungstext mit den Wiederherstellungsszenarien wird geändert.***

> [!IMPORTANT]
> Alle Details zu den verschiedenen Wiederherstellungsoptionen und deren Abhängigkeiten können Sie [hier](./backup-azure-arm-restore-vms.md#restore-options) finden. Sehen Sie dort nach, bevor Sie diese Vorgänge auslösen.

Das Auslösen von Wiederherstellungsvorgängen ist eine *POST*-Anforderung. Weitere Informationen zur API finden Sie in der [REST-API für „Wiederherstellung auslösen“](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Informationen zur Erstellung von `{containerName}` und `{protectedItemName}` finden Sie [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` ist „Azure“ und `{recoveryPointId}` ist das Feld `{name}` des [oben](#example-response) erwähnten Wiederherstellungspunkts.

Nachdem Sie den Wiederherstellungspunkt abgerufen haben, müssen Sie den Anforderungstext für das relevante Wiederherstellungsszenario erstellen. In den folgenden Abschnitten wird der Anforderungstext für die einzelnen Szenarien erläutert.

- [Datenträger wiederherstellen](#restore-disks)
- [Datenträger ersetzen](#replace-disks-in-a-backed-up-virtual-machine)
- [Als neuen virtuellen Computer wiederherstellen](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Antwort wiederherstellen

Das Auslösen eines Wiederherstellungsvorgangs ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.

Er gibt zwei Antworten zurück: „202 (Akzeptiert)“, wenn ein anderer Vorgang erstellt wird, und dann „200 (OK)“, wenn dieser Vorgang abgeschlossen ist.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|202 – Akzeptiert     |         |     Zulässig    |

#### <a name="example-responses"></a>Beispielantworten

Nachdem Sie den *POST*-URI für das Auslösen der Datenträgerwiederherstellung gesendet haben, wird als erste Antwort „202 (Akzeptiert)“ mit einem location- oder Azure-async-Header zurückgegeben.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Verfolgen Sie anschließend den resultierenden Vorgang mithilfe des location- oder Azure-AsyncOperation-Headers mit einem einfachen *GET*-Befehl nach.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Wenn der Vorgang abgeschlossen ist, wird „200 (OK)“ mit der ID des resultierenden Wiederherstellungsauftrags im Antworttext zurückgegeben.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Da es sich beim Wiederherstellungsauftrag um einen zeitintensiven Vorgang handelt, sollte er entsprechend der Erläuterung im [Dokument zur Auftragsüberwachung mit der REST-API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job) nachverfolgt werden.

### <a name="restore-disks"></a>Wiederherstellen von Datenträgern

Wenn die Erstellung eines virtuellen Computers anhand der Sicherungsdaten angepasst werden muss, können Sie Datenträger einfach in einem ausgewählten Speicherkonto wiederherstellen und – basierend auf diesen Datenträgern – einen virtuellen Computer erstellen, der deren Anforderungen erfüllt. Das Speicherkonto sollte sich in derselben Region wie der Recovery Services-Tresor befinden und sollte nicht zonenredundant sein. Die Datenträger und die Konfiguration des gesicherten virtuellen Computers („vmconfig.json“) werden im angegebenen Speicherkonto gespeichert. Wie [oben](#restore-operations) erläutert, wird der relevante Anforderungstext für Datenträgerwiederherstellung unten mitgeteilt.

#### <a name="create-request-body"></a>Erstellen des Anforderungstexts

Zum Auslösen einer Datenträgerwiederherstellung auf der Grundlage einer Azure-VM-Sicherung werden im Folgenden die Komponenten des Anforderungstexts angegeben.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Die vollständige Liste mit Definitionen des Anforderungstexts und weitere Einzelheiten finden Sie im [Dokument zur REST-API zum Auslösen der Wiederherstellung](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Beispielanforderung

Der folgende Anforderungstext definiert Eigenschaften, die zum Auslösen einer Datenträgerwiederherstellung erforderlich sind.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Selektives Wiederherstellen von Datenträgern

Wenn Sie [Datenträger selektiv sichern](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup), wird die aktuelle Liste der gesicherten Datenträger in der [Wiederherstellungspunktübersicht](#select-recovery-point) und der [ausführlichen Antwort](/rest/api/backup/recovery-points/get) bereitgestellt. Sie können Datenträger auch selektiv wiederherstellen. Weitere Informationen hierzu finden Sie [hier](selective-disk-backup-restore.md#selective-disk-restore). Wenn Sie einen Datenträger aus der Liste der gesicherten Datenträger selektiv wiederherstellen möchten, suchen Sie die LUN des Datenträgers in der Antwort des Wiederherstellungspunkts, und fügen Sie die **restoreDiskLunList**-Eigenschaft dem [Anforderungstext oben](#example-request) hinzu, wie unten gezeigt.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Sobald Sie die Antwort wie [oben](#responses) erläutert nachverfolgen und der zeitintensive Vorgang abgeschlossen ist, sind die Datenträger und die Konfiguration des gesicherten virtuellen Computers („VMConfig.json“) im angegebenen Speicherkonto zu finden.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Ersetzen von Datenträgern in einem gesicherten virtuellen Computer

Während mit „Datenträger wiederherstellen“ Datenträger aus dem Wiederherstellungspunkt erstellt werden, werden mit „Datenträger ersetzen“ die aktuellen Datenträger des gesicherten virtuellen Computers durch die Datenträger aus dem Wiederherstellungspunkt ersetzt. Wie [oben](#restore-operations) erläutert, wird der relevante Anforderungstext für Datenträgerersetzung unten mitgeteilt.

#### <a name="create-request-body"></a>Erstellen des Anforderungstexts

Zum Auslösen einer Datenträgerersetzung auf der Grundlage einer Azure-VM-Sicherung werden im Folgenden die Komponenten des Anforderungstexts angegeben.

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Die vollständige Liste mit Definitionen des Anforderungstexts und weitere Einzelheiten finden Sie im [Dokument zur REST-API zum Auslösen der Wiederherstellung](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Beispielanforderung

Der folgende Anforderungstext definiert Eigenschaften, die zum Auslösen einer Datenträgerwiederherstellung erforderlich sind.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Wiederherstellen als ein anderer virtueller Computer

Wie [oben](#restore-operations) erläutert, definiert der folgende Anforderungstext Eigenschaften, die zum Auslösen der Wiederherstellung eines virtuellen Computers erforderlich sind.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
     }
 }
```

Die Antwort muss gemäß der Erläuterung im obigen Abschnitt zum [Wiederherstellen von Datenträgern](#responses) verarbeitet werden.

## <a name="cross-region-restore"></a>Regionsübergreifende Wiederherstellung

Wenn die regionsübergreifende Wiederherstellung (CRR) für den Tresor aktiviert ist, mit dem Sie Ihre virtuellen Computer geschützt haben, werden die Sicherungsdaten in die sekundäre Region repliziert. Sie können die Sicherungsdaten verwenden, um einen Wiederherstellungsvorgang durchzuführen. Führen Sie die folgenden Schritte aus, um einen Wiederherstellungsvorgang in der sekundären Region mithilfe der REST-API auszulösen:

### <a name="select-recovery-point-in-secondary-region"></a>Wählen Sie den Wiederherstellungspunkt in der sekundären Region aus

Sie können die verfügbaren Wiederherstellungspunkte eines Sicherungselements in der sekundären Region mithilfe der [REST-API zum Auflisten von Wiederherstellungspunkten für CRR auflisten](/rest/api/backup/recovery-points-crr/list). Dies ist ein einfacher GET-Vorgang mit allen relevanten Werten.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2018-12-20

```

Informationen zur Erstellung von `{containerName}` und `{protectedItemName}` finden Sie [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` ist „Azure“.

Der *GET*-URI enthält alle erforderlichen Parameter. Ein zusätzlicher Anforderungstext ist nicht erforderlich.

>[!NOTE]
>Verwenden Sie zum Abrufen von Wiederherstellungspunkten in der sekundären Region die API-Version 2018-12-20 wie im obigen Beispiel.

#### <a name="responses"></a>Antworten

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points-crr/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Beispielantwort

Wenn der *GET*-URI übermittelt wird, wird die Antwort „200 (OK)“ zurückgegeben.

```http
Headers:
Pragma                        : no-cache
X-Content-Type-Options        : nosniff
x-ms-request-id               : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-client-request-id        : 4344a9c2-70d8-482d-b200-0ca9cc498812,4344a9c2-70d8-482d-b200-0ca9cc498812
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 149
x-ms-correlation-request-id   : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-routing-request-id       : SOUTHINDIA:20210731T112441Z:bfc4a4e6-c585-46e0-8e38-f11a86093701
Cache-Control                 : no-cache
Date                          : Sat, 31 Jul 2021 11:24:40 GMT
Server                        : Microsoft-IIS/10.0
X-Powered-By                  : ASP.NET

Body:
{
  "value": [
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932895704780058094",
      "name": "932895704780058094",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-31T09:24:34.687561Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932891484644960954",
      "name": "932891484644960954",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-30T09:20:01.8355052Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
.....
```

Der Wiederherstellungspunkt wird durch das Feld `{name}` in der obigen Antwort angegeben.

### <a name="get-access-token"></a>Abrufen des Zugriffstokens

Um eine regionsübergreifende Wiederherstellung durchzuführen, benötigen Sie ein Zugriffstoken, um Ihre Anforderung für den Zugriff auf replizierte Wiederherstellungspunkte in der sekundären Region zu autorisieren. Führen Sie die folgenden Schritte aus, um ein Zugriffstoken abzurufen:

#### <a name="step-1"></a>Schritt 1:

Verwenden Sie die [AAD-Eigenschaften API](/rest/api/backup/aad-properties/get) um AAD-Eigenschaften für die sekundäre Region zu erhalten (*westus* im nachfolgenden Beispiel):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/westus/backupAadProperties?api-version=2018-12-20
```

##### <a name="response-example"></a>Antwortbeispiel

Die Antwort wird im folgenden Format zurückgeben:

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

#### <a name="step-2"></a>Schritt 2:

Nutzen Sie das [Zugriffstoken](/rest/api/backup/recovery-points-get-access-token-for-crr/get-access-token), um Ihre Anforderung für den Zugriff auf replizierte Wiederherstellungspunkte in der sekundären Region zu autorisieren:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/accessToken?api-version=2018-12-20
```

Fügen Sie für den Anforderungstext den Inhalt der Antwort ein, die von der AAD-Eigenschaften-API im vorherigen Schritt zurückgegeben wurde.

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

##### <a name="response-example"></a>Antwortbeispiel

Die Antwort wird im folgenden Format zurückgeben:

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/26083826328862",
  "name": "932879774590051503",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
    "properties": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "resourceName": "testVault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    }
}
```

### <a name="restore-disks-to-the-secondary-region"></a>Wiederherstellen von Datenträgern in einer sekundären Region

Verwenden Sie die [regionenübergreifende Wiederherstellungsauslösungs-API](/rest/api/backup/cross-region-restore/trigger), um ein Element in der sekundären Region wiederherzustellen.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/{azureRegion}/backupCrossRegionRestore?api-version=2018-12-20
```

Der Anforderungstext sollte zwei Teile aufweisen:

1. ***crossRegionRestoreAccessDetails** _: Fügen Sie den _properties*-Block der Antwort aus der API-Zugriffstokenanforderung ein, die im vorherigen Schritt ausgeführt wurde, um dieses Segment des Anforderungstexts auszufüllen.

1. ***restoreRequest** _: Um das Segment _restoreRequest* des Anforderungstexts auszufüllen, müssen Sie die zuvor abgerufene Wiederherstellungspunkt-ID zusammen mit der Azure Resource Manager(ARM)-ID des Quell-VMs sowie die Details des Speicherkontos in der sekundären Region übergeben, das als Stagingspeicherort verwendet werden soll. Um eine Wiederherstellung des Datenträgers vorzunehmen, spezifizieren Sie *RestoreDisks* als den Wiederherstellungstyp.

Es folgt ein Beispiel für einen Anforderungstext zum Wiederherstellen der Datenträger eines VM in der sekundären Region:

```json
 {
  "crossRegionRestoreAccessDetails": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "azurefiles",
        "resourceName": "azurefilesvault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    },
    "restoreRequest": {
        "affinityGroup": "",
        "createNewCloudService": false,
        "encryptionDetails": {
            "encryptionEnabled": false
        },
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "932879774590051503",
        "recoveryType": "RestoreDisks",
        "sourceResourceId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Compute/virtualMachines/testVM",
        "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1",
        "storageAccountId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Storage/storageAccounts/testStorageAccount",
        "region": "westus",
        "affinityGroup": "",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "restoreDiskLunList": []
    }
}
```

Ähnlich wie beim Wiederherstellungsvorgang der primären Region ist dies ein asynchroner Vorgang, der [separat nachverfolgt](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-response) werden muss.



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Azure Backup-REST-APIs finden Sie in den folgenden Dokumenten:

- [Azure Recovery Services-Anbieter – REST-API](/rest/api/recoveryservices/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
