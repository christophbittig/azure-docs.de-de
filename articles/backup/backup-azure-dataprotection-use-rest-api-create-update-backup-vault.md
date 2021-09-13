---
title: Erstellen Azure Backup-Richtlinie für Blobs mithilfe der REST-API
description: In diesem Artikel erfahren Sie, wie Sie mithilfe der REST-API eine Richtlinie zum Sichern von Blobs in einem Speicherkonto erstellen.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 93861379-5bec-4ed5-95d2-46f534a115fd
ms.openlocfilehash: 9b32e69bcd96d48dcd1321a69132790a93b1220b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598638"
---
# <a name="create-azure-backup-vault-using-rest-api"></a>Erstellen eines Azure Backup-Tresors mithilfe der REST-API

Die neue Datenschutzplattform von Azure Backup bietet erweiterte Funktionen für die Sicherung und Wiederherstellung neuerer Workloads, z. B. Blobs in Speicherkonten, verwaltete Datenträger und die PaaS-Plattform des PostGre SQL-Servers. Das Ziel ist es, den Verwaltungsaufwand zu minimieren und gleichzeitig die Organisation von Sicherungen zu beschleunigen. Ein „Sicherungstresor“ ist der Grundbaustein der Datenschutzplattform und unterscheidet sich vom Recovery Services-Tresor.

Die Schritte zum Erstellen eines Azure Backup-Tresors mit der REST-API finden Sie in der Dokumentation zur [REST-API zum Erstellen von Tresoren](/rest/api/dataprotection/backup-vaults/create-or-update). Nutzen Sie dieses Dokument als Referenz zum Erstellen eines Tresors mit dem Namen „testBkpVault“ in der Region „USA, Westen“ unter der Ressourcengruppe „TestBkpVaultRG“.

Verwenden Sie den folgenden *PUT*-Vorgang, um einen Azure Backup-Tresor zu erstellen oder zu aktualisieren:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/testBkpVault?api-version=2021-01-01
```

## <a name="create-a-request"></a>Erstellen einer Anforderung

Zum Erstellen der *PUT*-Anforderung ist der `{subscription-id}`-Parameter erforderlich. Wenn Sie über mehrere Abonnements verfügen, lesen Sie [Verwenden mehrerer Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli): Sie definieren zusammen mit dem `api-version`-Parameter einen `{resourceGroupName}` und `{vaultName}` für Ihre Ressourcen. In diesem Artikel wird `api-version=2021-01-01` verwendet.

Die folgenden Header sind erforderlich:

| Anforderungsheader   | BESCHREIBUNG |
|------------------|-----------------|
| *Content-Type:*  | Erforderlich. Legen Sie diese Option auf `application/json` fest. |
| *Authorization:* | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-[Zugriffstoken](/rest/api/azure/#authorization-code-grant-interactive-clients) fest. |

Weitere Informationen zum Erstellen der Anforderung finden Sie unter [Komponenten einer REST-API-Anforderung/Antwort](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Die folgenden allgemeinen Definitionen werden verwendet, um einen Anforderungstext zu erstellen:

|Name  |Erforderlich  |type  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  Optionales ETag       |
|location     |  true       |String         |   Ressourcenspeicherort      |
|properties     |   true      | [BackupVault](/rest/api/dataprotection/backup-vaults/create-or-update#backupvault)        |  Eigenschaften des Tresors       |
|Identity     |         |  [DPPIdentityDetails](/rest/api/dataprotection/backup-vaults/create-or-update#dppidentitydetails)       |    Gibt den eindeutigen Systembezeichner für jede Azure-Ressource an.     |
|tags     |         | Object        |     Ressourcentags    |

Beachten Sie, dass der Tresorname und Ressourcengruppenname im PUT-URI bereitgestellt werden. Im Anforderungstext wird der Standort definiert.

## <a name="example-request-body"></a>Beispiel für Anforderungstext

Der folgende Beispieltext wird verwendet, um in „West US“ (USA, Westen) einen Tresor zu erstellen. Geben Sie den Standort an.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "None"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

Wenn Sie einen Sicherungstresor erstellen und auch eine systemseitig zugewiesene Identität generieren möchten, sollte der folgende Anforderungstext angegeben werden.

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "systemAssigned"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

## <a name="responses"></a>Antworten

Das Erstellen eines Sicherungstresors ist ein [asynchroner Vorgang](../azure-resource-manager/management/async-operations.md). Das bedeutet, dass in diesem Vorgang ein anderer Vorgang erstellt wird, der separat nachverfolgt werden muss.
Es gibt zwei erfolgreiche Antworten für den Vorgang, um einen Sicherungstresor zu erstellen oder zu aktualisieren:

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)      | OK        |
|201 – Erstellt     | [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)        |   Erstellt      |
| Andere Statuscodes  |  [CloudError](/rest/api/dataprotection/backup-vaults/create-or-update#clouderror)

Weitere Informationen zu REST-API-Antworten finden Sie unter [Verarbeiten der Antwortnachricht](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Beispielantwort

Die komprimierte Antwort *201 – Erstellt* aus dem vorherigen Beispielanforderungstext zeigt, dass eine *id* zugewiesen wurde und *provisioningState* auf *Succeeded* festgelegt ist:

```json
{
    "eTag": null,
    "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/TestBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/testBkpVault",
    "identity": {
      "principalId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenantId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "SystemAssigned"
    },
    "location": "westUS",
    "name": "testBkpVault",
    "properties": {
      "provisioningState": "Succeeded",
      "storageSettings": [
        {
          "datastoreType": "VaultStore",
          "type": "GeoRedundant"
        }
      ]
    },
    "resourceGroup": "TestBkpVaultRG",
    "systemData": null,
    "tags": {},
    "type": "Microsoft.DataProtection/backupVaults"
  }
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Sie eine Sicherungsrichtlinie zum Sichern von Blobs in diesem Tresor](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md).

Weitere Informationen zu den Azure-REST-APIs finden Sie in den folgenden Dokumenten:

- [Rest-API des Azure-Datenschutzanbieters](/rest/api/dataprotection/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
