---
title: Aktualisieren der Konfigurationen für den Recovery Services-Tresor mit der REST-API
description: In diesem Artikel erfahren Sie, wie Sie die Konfiguration eines Tresors mithilfe der REST-API aktualisieren.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6dfa05a3bc26c21da95d60374582f10a1a0b84d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458845"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aktualisieren von Konfigurationen eines Azure Recovery Services-Tresors mithilfe der REST-API

In diesem Artikel erfahren Sie, wie Sie sicherungsbezogene Konfigurationen in einem Azure Recovery Services-Tresor mithilfe der REST-API aktualisieren.

## <a name="soft-delete-state"></a>Zustand des vorläufigen Löschens

Das Löschen von Sicherungen eines geschützten Elements ist ein kritischer Vorgang, der überwacht werden muss. Zum Schutz vor versehentlicher Löschung verfügt der Azure Recovery Services-Tresor über eine Funktion für vorläufiges Löschen. Diese Funktion ermöglicht Ihnen, gelöschte Sicherungen ggf. innerhalb eines bestimmten Zeitfensters nach der Löschung wiederherstellen können.

Es gibt jedoch Szenarien, in denen diese Funktion nicht erforderlich ist. Ein Azure Recovery Services-Tresor, der Sicherungselemente enthält, kann auch dann nicht gelöscht werden, wenn sich die Elemente im Zustand „Vorläufig gelöscht“ befinden. Dies kann problematisch sein, wenn der Tresor umgehend gelöscht werden muss. Ein Beispiel: Bei Bereitstellungsvorgängen werden die erstellten Ressourcen häufig im gleichen Workflow bereinigt. Eine Bereitstellung kann einen Tresor erstellen, Sicherungen für ein Element konfigurieren, eine Testwiederherstellung durchführen und anschließend die Sicherungselemente und den Tresor wieder löschen. Wenn der Tresor nicht gelöscht werden kann, ist unter Umständen die gesamte Bereitstellung nicht erfolgreich. Die umgehende Löschung kann nur durch Deaktivieren des vorläufigen Löschens sichergestellt werden.

Sie müssen sich je nach Szenario genau überlegen, ob Sie das vorläufige Löschen für einen bestimmten Tresor deaktivieren möchten. Weitere Informationen finden Sie im [Artikel zu vorläufigem Löschen](backup-azure-security-feature-cloud.md).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Abrufen des Zustands des vorläufigen Löschens mithilfe der REST-API

Das vorläufige Löschen ist für neu erstellte Recovery Services-Tresore standardmäßig aktiviert. Informationen zum Abrufen/Aktualisieren des Zustands des vorläufigen Löschens für einen Tresor finden Sie in der konfigurationsbezogenen [REST-API-Dokumentation](/rest/api/backup/backup-resource-vault-configs) des Sicherungstresors.

Verwenden Sie den folgenden *GET*-Vorgang, um den aktuellen Zustand des vorläufigen Löschens für einen Tresor abzurufen:

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Der GET-URI verfügt über die Parameter `{subscriptionId}`, `{vaultName}` und `{vaultresourceGroupName}`. In diesem Beispiel ist `{vaultName}` auf „testVault“ und `{vaultresourceGroupName}` auf „testVaultRG“ festgelegt. Da alle erforderlichen Parameter im URI angegeben sind, besteht keine Notwendigkeit eines separaten Anforderungstexts.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Antworten

Die erfolgreiche Antwort für den GET-Vorgang sieht wie folgt aus:

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [BackupResourceVaultConfig](/rest/api/backup/backup-resource-vault-configs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Beispielantwort

Nach Übermittlung der GET-Anforderung wird eine Antwort vom Typ „200“ (erfolgreich) zurückgegeben.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Aktualisieren des Zustands des vorläufigen Löschens mithilfe der REST-API

Verwenden Sie den folgenden *PUT*-Vorgang, um den Zustand des vorläufigen Löschens des Recovery Services-Tresors mithilfe der REST-API zu aktualisieren.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Der PUT-URI verfügt über die Parameter `{subscriptionId}`, `{vaultName}` und `{vaultresourceGroupName}`. In diesem Beispiel ist `{vaultName}` auf „testVault“ und `{vaultresourceGroupName}` auf „testVaultRG“ festgelegt. Wenn wir den URI mit den obigen Werten versehen, sieht er wie folgt aus:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Die folgenden allgemeinen Definitionen werden verwendet, um einen Anforderungstext zu erstellen:

Ausführlichere Informationen finden Sie in der [REST-API-Dokumentation](/rest/api/backup/backup-resource-vault-configs/update#request-body).

|Name  |Erforderlich  |type  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  Optionales ETag       |
|location     |  true       |String         |   Ressourcenspeicherort      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Eigenschaften des Tresors       |
|tags     |         | Object        |     Ressourcentags    |

#### <a name="example-request-body"></a>Beispiel für Anforderungstext

Im folgenden Beispiel wird der Zustand des vorläufigen Löschens in „Disabled“ (Deaktiviert) geändert.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>Antworten für den Patch-Vorgang

Die erfolgreiche Antwort für den PATCH-Vorgang sieht wie folgt aus:

|Name  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [BackupResourceVaultConfig](/rest/api/backup/backup-resource-vault-configs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>Beispielantwort für den PATCH-Vorgang

Nach Übermittlung der PATCH-Anforderung wird eine Antwort vom Typ „200“ (erfolgreich) zurückgegeben.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Sie eine Sicherungsrichtlinie zum Sichern einer Azure-VM in diesem Tresor](backup-azure-arm-userestapi-createorupdatepolicy.md).

Weitere Informationen zu den Azure-REST-APIs finden Sie in den folgenden Dokumenten:

- [Azure Recovery Services-Anbieter – REST-API](/rest/api/recoveryservices/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
