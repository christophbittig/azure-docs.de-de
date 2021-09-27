---
title: Azure Key Vault als Event Grid-Quelle
description: Beschreibt die Eigenschaften und das Schema, die für Azure Key Vault-Ereignisse im Azure Event Grid verfügbar sind
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: fccd93b97455a673da5a3b92dcd54cf494f0cecb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612774"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault als Event Grid-Quelle

Dieser Artikel enthält Informationen zu den Eigenschaften und zum Schema für Ereignisse in [Azure Key Vault](../key-vault/index.yml). Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).


## <a name="available-event-types"></a>Verfügbare Ereignistypen

Ein Azure Key Vault Konto generiert die folgenden Ereignistypen:

| Vollständiger Ereignisname | Anzeigename des Ereignisses | BESCHREIBUNG |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Certificate New Version Created (Neue Zertifikatversion erstellt) | Wird ausgelöst, wenn ein neues Zertifikat oder eine neue Zertifikatversion erstellt wird. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificate Near Expiry (Zertifikat läuft bald ab) | Wird ausgelöst, wenn die aktuelle Version des Zertifikats abläuft. (Das Ereignis wird 30 Tage vor dem Ablaufdatum ausgelöst.) |
| Microsoft.KeyVault.CertificateExpired | Zertifikat abgelaufen | Wird ausgelöst, wenn die aktuelle Version des Zertifikats abgelaufen ist. |
| Microsoft.KeyVault.KeyNewVersionCreated | Key New Version Created (Neue Schlüsselversion erstellt) | Wird ausgelöst, wenn ein neuer Schlüssel oder eine neue Schlüsselversion erstellt wird. |
| Microsoft.KeyVault.KeyNearExpiry | Key Near Expiry (Schlüssel läuft bald ab) | Wird ausgelöst, wenn die aktuelle Version eines Schlüssels demnächst abläuft. (Das Ereignis wird 30 Tage vor dem Ablaufdatum ausgelöst.) |
| Microsoft.KeyVault.KeyExpired | Schlüssel abgelaufen | Wird ausgelöst, wenn die aktuelle Version eines Schlüssels abgelaufen ist. |
| Microsoft.KeyVault.SecretNewVersionCreated | Secret New Version Created (Neue Version des Geheimnisses erstellt) | Wird ausgelöst, wenn ein neues Geheimnis oder eine neue Geheimnisversion erstellt wird. |
| Microsoft.KeyVault.SecretNearExpiry | Secret Near Expiry (Geheimnis läuft demnächst ab) | Wird ausgelöst, wenn die aktuelle Version eines Geheimnisses demnächst abläuft. (Das Ereignis wird 30 Tage vor dem Ablaufdatum ausgelöst.) |
| Microsoft.KeyVault.SecretExpired | Secret Expired (Geheimnis abgelaufen) | Wird ausgelöst, wenn die aktuelle Version eines Geheimnisses abgelaufen ist. |
| Microsoft.KeyVault.VaultAccessPolicyChanged | Geänderte Tresorzugriffsrichtlinie | Wird ausgelöst, wenn eine Zugriffsrichtlinie für Key Vault geändert wird. Enthält ein Szenario, in dem das Key Vault-Berechtigungsmodell in/aus rollenbasierter Azure-Zugriffssteuerung geändert wird.   |

## <a name="event-examples"></a>Ereignisbeispiele

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

Das folgende Beispiel zeigt das Schema für **Microsoft.KeyVault.SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "VaultName":"sample-kv",
         "ObjectType":"Secret",
         "ObjectName":"newsecret",
         "Version":"ee059b2bb5bc48398a53b168c6cdcb10",
         "NBF":"1559081980",
         "EXP":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Das folgende Beispiel zeigt das Schema für **Microsoft.KeyVault.SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "VaultName":"sample-kv",
         "ObjectType":"Secret",
         "ObjectName":"newsecret",
         "Version":"ee059b2bb5bc48398a53b168c6cdcb10",
         "NBF":"1559081980",
         "EXP":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Ereigniseigenschaften

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `topic` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | App Configuration-Ereignisdaten. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |


# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `source` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `type` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `time` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | App Configuration-Ereignisdaten. |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---
 

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| ---------- | ----------- |---|
| `id` | Zeichenfolge | Die ID des Objekts, das dieses Ereignis ausgelöst hat. |
| `VaultName` | Zeichenfolge | Der Schlüsseltresorname des Objekts, das dieses Ereignis ausgelöst hat. |
| `ObjectType` | Zeichenfolge | Der Typ des Objekts, das dieses Ereignis ausgelöst hat. |
| `ObjectName` | Zeichenfolge | Der Name des Objekts, das dieses Ereignis ausgelöst hat. |
| `Version` | Zeichenfolge | Die Version des Objekts, das dieses Ereignis ausgelöst hat. |
| `NBF` | number | Das Anfangsdatum in Sekunden seit 1970-01-01t00:00:00Z für das Objekt, das dieses Ereignis ausgelöst hat. |
| `EXP` | number | Das Ablaufdatum in Sekunden seit 1970-01-01t00:00:00Z des Objekts, das dieses Ereignis ausgelöst hat. |

## <a name="tutorials-and-how-tos"></a>Tutorials und Anleitungen
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Überwachen von Key Vault-Ereignissen mit Azure Event Grid](../key-vault/general/event-grid-overview.md) | Übersicht über die Integration von Key Vault in Event Grid. |
| [Tutorial: Erstellen und Überwachen von Key Vault-Ereignissen mit Event Grid](../key-vault/general/event-grid-logicapps.md) | Erfahren Sie, wie Sie Event Grid-Benachrichtigungen für Key Vault einrichten. |


## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Weitere Informationen zu Key Vault finden Sie unter [Was ist Azure Key Vault?](../key-vault/general/overview.md)

