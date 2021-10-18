---
title: Behandeln von Problemen bei der Implementierung von Azure Policy für Key Vault
description: Behandeln von Problemen bei der Implementierung von Azure Policy für Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8b6084f411ec948eb7655c5c7c6b54bf7d2e2c30
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858979"
---
# <a name="troubleshooting-issues-with-implementing-azure-policy-on-key-vault"></a>Behandeln von Problemen bei der Implementierung von Azure Policy für Key Vault

In diesem Artikel erfahren Sie, wie Sie allgemeine Fehler beheben, die beim Einrichten von [Azure Policy für Key Vault](./azure-policy.md) auftreten können, und wie Sie diese beheben können.

## <a name="about-azure-policy-for-key-vault"></a>Informationen zu Azure Policy für Key Vault

[Azure Policy](../../governance/policy/index.yml) ist ein Governancetool, mit dem Benutzer ihre Azure-Umgebung bedarfsorientiert überwachen und verwalten können. Azure Policy bietet die Möglichkeit, Schutzmaßnahmen für Azure-Ressourcen zu platzieren, um sicherzustellen, dass diese mit den zugewiesenen Richtlinienregeln konform sind. Es ermöglicht es Benutzern, eine Überwachung, Echtzeiterzwingung und Wiederherstellung ihrer Azure-Umgebung auszuführen. Die Ergebnisse von Überwachungen, die per Richtlinie durchgeführt werden, stehen Benutzern in einem Konformitätsdashboard zur Verfügung. Es wird angezeigt, welche Ressourcen und Komponenten konform sind und welche nicht.

### <a name="logging"></a>Protokollierung

Um zu überwachen, wie Richtlinienauswertungen durchgeführt werden, können Sie die Key Vault-Protokolle überprüfen. Hierfür können Sie die Protokollierung für Azure Key Vault aktivieren, bei der Informationen im von Ihnen bereitgestellten Azure-Speicherkonto gespeichert werden. Eine detaillierte Anleitung zu dieser Einrichtung finden Sie unter [Aktivieren der Protokollierung in Key Vault](howto-logging.md).

Wenn Sie Protokollierung aktivieren, wird automatisch ein neuer Container mit dem Namen **AzurePolicyEvaluationDetails** erstellt, um richtlinienbezogene Protokollierungsinformationen in Ihrem angegebenen Speicherkonto zu erfassen. 

> [!NOTE]
> Sie sollten den Zugriff auf Überwachungsdaten (insbesondere Protokolldateien) streng regeln, da diese vertrauliche Informationen enthalten können. Erfahren Sie mehr über das Anwenden der [integrierten Azure-Überwachungsrolle](../../azure-monitor/roles-permissions-security.md) und das Einschränken des Zugriffs.
> 
> 

Einzelne Blobs werden als Text und formatiert als JSON-Blob gespeichert. Sehen wir uns einen Beispielprotokolleintrag für eine Schlüsselrichtlinie an: [Für Schlüssel sollte das Ablaufdatum festgelegt sein](azure-policy.md?tabs=keys#secrets-should-have-expiration-date-set-preview). Mit dieser Richtlinie werden alle Schlüssel in Ihren Schlüsseltresoren ausgewertet und die Schlüssel, für die kein Ablaufdatum festgelegt ist, als nicht konform gekennzeichnet.

```json
{
  "ObjectName": "example",
  "ObjectType": "Key",
  "IsComplianceCheck": false,
  "EvaluationDetails": [
    {
      "AssignmentId": "<subscription ID>",
      "AssignmentDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "DefinitionId": "<definition ID>",
      "DefinitionDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "Outcome": "NonCompliant",
      "ExpressionEvaluationDetails": [
        {
          "Result": "True",
          "Expression": "type",
          "ExpressionKind": "Field",
          "ExpressionValue": "Microsoft.KeyVault.Data/vaults/keys",
          "TargetValue": "Microsoft.KeyVault.Data/vaults/keys",
          "Operator&quot;: &quot;Equals"
        },
        {
          "Result": "True",
          "Expression": "Microsoft.KeyVault.Data/vaults/keys/attributes.expiresOn",
          "ExpressionKind": "Field",
          "ExpressionValue": "******",
          "TargetValue": "False",
          "Operator&quot;: &quot;Exists"
        }
      ]
    }
  ]
}
```

In der folgenden Tabelle sind die Feldnamen und Beschreibungen aufgeführt:

| Feldname | BESCHREIBUNG |
| --- | --- |
| **ObjectName** |Name des Objekts |
| **ObjectType** |Der Typ des Schlüsseltresorobjekts, d. h. Zertifikat, Geheimnis oder Schlüssel. |
| **IsComplianceCheck** |TRUE, wenn die Auswertung während der nachts durchgeführten Überwachung erfolgt ist, FALSE, wenn die Auswertung während der Ressourcenerstellung oder -aktualisierung erfolgt ist. |
| **Ergebnis** | Gibt die Richtlinienauswertung zurück. |
| **ExpressionEvaluationDetails** | Details zum Auswertungsfeld, Ausdruckswert. |


### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="key-vault-recovery-blocked-by-azure-policy"></a>Key Vault-Wiederherstellung wird durch Azure Policy blockiert

Einer der Gründe kann sein, dass Ihr Abonnement (oder Ihre Verwaltungsgruppe) über eine Richtlinie verfügt, die die Wiederherstellung blockiert. Die Korrektur besteht darin, die Richtlinie so anzupassen, dass sie nicht angewendet wird, wenn ein Tresor wiederhergestellt wird.

Wenn Sie den Fehlertyp ```RequestDisallowedByPolicy``` für die Wiederherstellung aufgrund einer **integrierten** Richtlinie sehen, stellen Sie sicher, dass Sie die **aktuellste Version** verwenden. 

Wenn Sie eine **benutzerdefinierte Richtlinie** mit Ihrer eigenen Logik erstellt haben, finden Sie hier ein Beispiel für einen Teil einer Richtlinie, der verwendet werden kann, um vorläufiges Löschen zu erfordern. Bei der Wiederherstellung eines vorläufig gelöschten Tresors wird die gleiche API wie beim Erstellen oder Aktualisieren eines Tresors verwendet. Anstatt jedoch die Eigenschaften des Tresors anzugeben, verfügt er über eine einzelne createMode-Eigenschaft mit dem Wert „recover“. Der Tresor wird mit den Eigenschaften wiederhergestellt, die er beim Löschen aufwies. Richtlinien, die Anforderungen blockieren, wenn für sie keine spezifischen Eigenschaften konfiguriert sind, blockieren auch die Wiederherstellung von vorläufig gelöschten Tresoren. Die Korrektur besteht darin, eine Klausel einzufügen, die bewirkt, dass die Richtlinie Anforderungen ignoriert, bei denen createMode den Wert „recover“ aufweist:

Sie werden sehen, dass es eine Klausel gibt, die bewirkt, dass die Richtlinie nur angewendet wird, wenn createMode nicht gleich „recover“ ist:

```

    "policyRule": { 
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.KeyVault/vaults"
          }, 
          {
            "not": {
              "field": "Microsoft.Keyvault/vaults/createMode",
              "equals": "recover"
            }
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "exists": "false"
              },
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "equals": "false"
              }
            ]
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
```

#### <a name="latency-on-azure-policy-assignment-delete-on-key-vault"></a>Latenz beim Löschen einer Azure-Richtlinienzuweisung für Key Vault 

Microsoft.KeyVault.Data: Es kann bis zu 24 Stunden dauern, bis eine gelöschte Richtlinienzuweisung nicht mehr erzwungen wird. 

Entschärfung: Aktualisieren Sie die Auswirkung der Richtlinienzuweisung in „Disabled“ (Deaktiviert).


#### <a name="secret-creation-via-arm-template-missing-out-policy-evaluation"></a>Geheimniserstellung über ARM-Vorlage lässt Richtlinienauswertung aus

Richtlinien auf Datenebene, die die Geheimniserstellung auswerten, gelten nicht für Geheimnisse, die zum Zeitpunkt der Geheimniserstellung über eine [ARM-Vorlage](../secrets/quick-create-template.md?tabs=CLI) erstellt wurden. Nach 24 Stunden, wenn die automatisierte Konformitätsprüfung auftreten würde und die Konformitätsergebnisse überprüft werden können.


## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Problembehandlung mit Azure Policy](../../governance/policy/troubleshoot/general.md)
* Informieren Sie sich über [bekannte Azure Policy-Probleme](https://github.com/azure/azure-policy#known-issues)
