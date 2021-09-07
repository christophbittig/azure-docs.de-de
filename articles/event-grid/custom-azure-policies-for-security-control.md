---
title: Konfigurieren benutzerdefinierter Azure-Richtlinien auf Event Grid-Ressourcen zur Verbesserung des Sicherheitsstatus
description: In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Azure-Richtlinien definieren, um Sicherheitskontrollen zu erzwingen.
ms.topic: how-to
author: jfggdl
ms.author: jafernan
ms.date: 06/24/2021
ms.openlocfilehash: a9018150d75556f729db593ec1a76fb6aa174798
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992336"
---
# <a name="use-custom-azure-policies-to-enforce-security-controls"></a>Verwenden von benutzerdefinierten Azure-Richtlinien zum Erzwingen von Sicherheitskontrollen

In diesem Artikel werden Beispiele für benutzerdefinierte Azure-Richtlinien beschrieben, mit denen Sie die Ziele steuern, die in den Ereignisabonnements von Event Grid konfiguriert werden können. Mit [Azure Policy](../governance/policy/overview.md) können Sie Organisationsstandards und die Einhaltung gesetzlicher Bestimmungen für verschiedene Aspekte erzwingen, z. B. in den Bereichen Sicherheit, Kosten, Ressourcenkonsistenz, Verwaltung usw. Am wichtigsten sind hierbei die Kompatibilitätsstandards für den Sicherheitsbereich, die es Ihnen ermöglichen, einen guten Sicherheitsstatus für Ihre Organisation zu erzielen. Als Hilfe für Ihre Sicherheitskontrollen können Sie mit den Richtlinien in diesem Artikel die Datenexfiltration oder die Bereitstellung von Ereignissen für unberechtigte Endpunkte oder Azure-Dienste verhindern.

> [!NOTE]
> Azure Event Grid verfügt über integrierte Richtlinien für Konformitätsbereiche und Sicherheitskontrollen, die sich auf verschiedene Konformitätsstandards beziehen. Sie finden diese integrierten Richtlinien unter dem [Vergleichstest für die Azure-Sicherheit](security-controls-policy.md#azure-security-benchmark) von Event Grid.

Zur Verhinderung der Datenexfiltration kann es für Organisationen ratsam sein, die Anzahl von Zielen einzuschränken, für die von Event Grid Ereignisse bereitgestellt werden können. Hierfür können Richtlinien zugewiesen werden, die eine Erstellung oder Aktualisierung von [Ereignisabonnements](concepts.md#event-subscriptions) ermöglichen, für die eines der Ziele angegeben ist, die in der Richtlinie als genehmigt aufgeführt sind. Die Richtlinienauswirkung, mit der verhindert wird, dass eine Ressourcenanforderung erfolgreich ist, lautet [Deny](../governance/policy/concepts/effects.md#deny) (Verweigern).

In den folgenden Abschnitten werden Beispiele für Richtliniendefinitionen beschrieben, mit denen die Verwendung einer Liste mit zulässigen Zielen erzwungen wird. Sie möchten nach [Eigenschaftenaliasen](../governance/policy/concepts/definition-structure.md#aliases) suchen, die das Element ```destination``` enthalten und als ```field``` verwenden. Hiermit können Sie beim Definieren einer Richtlinie einen Vergleich mit einer Liste mit zulässigen Zielen durchführen.

Sie finden die für Event Grid definierten Eigenschaftenaliase (Verwendung des Namespace ```Microsoft.EventGrid```), indem Sie CLI- oder PowerShell-Befehle ausführen, die in [diesem Artikelabschnitt](../governance/policy/concepts/definition-structure.md#aliases) beschrieben sind.

Weitere Informationen zum Definieren von Richtlinien finden Sie im Artikel mit der [Azure Policy-Definitionsstruktur](../governance/policy/concepts/definition-structure.md).

 
## <a name="define-an-azure-policy-with-a-list-of-allowed-destinations-for-a-webhook-destination"></a>Definieren einer Azure-Richtlinie mit einer Liste mit zulässigen Zielen für ein Webhookziel

Mit der folgenden Richtliniendefinition werden Webhook-Endpunktziele eingeschränkt, die in einem Ereignisabonnement für ein Systemthema konfiguriert sind.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/systemTopics/eventSubscriptions/destination.WebHook.endpointUrl",
        "in": "[parameters('allowedDestinationEndpointURLs')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedDestinationEndpointURLs": {
      "type": "Array",
      "metadata": {
        "description": "Allowed event destination endpoint URLs.",
        "displayName": "The list of allowed webhook endpoint destinations to which send events"
      },
        "allowedValues": [
          "https://www.your-valid-destination-goes-here-1.com",
          "https://www.your-valid-destination-goes-here-2.com",
          "https://www.your-valid-destination-goes-here-3.com"
        ]
    }
  }
}
```

## <a name="define-an-azure-policy-with-a-list-of-allowed-azure-service-resource-destinations"></a>Definieren einer Azure-Richtlinie mit einer Liste mit zulässigen Ressourcenzielen des Azure-Diensts

Mit der folgenden Richtliniendefinition wird ein bestimmtes Event Hubs-Ziel eingeschränkt, das in einem Ereignisabonnement für ein benutzerdefiniertes Thema konfiguriert ist. Sie können einen ähnlichen Ansatz für andere Arten von [unterstützten Azure-Dienstzielen](event-handlers.md) verwenden.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/eventSubscriptions/destination.EventHub.resourceId",
        "in": "[parameters('allowedResourceDestinations')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedResourceDestinations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed event delivery destinations.",
        "displayName": "Allowed event delivery destinations"
      },
        "allowedValues": [
          "/subscriptions/<your-event-subscription>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<event-hubs-namespace-name>/eventhubs/<your-event-hub-name>"
        ]
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Azure Policy finden Sie in den folgenden Artikeln: 
    - [Was ist Azure Policy?](../governance/policy/overview.md)
    - [Struktur von Azure Policy-Definitionen](../governance/policy/concepts/definition-structure.md)
    - [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)
    - [Erläuterungen zum Bereich in Azure Policy](../governance/policy/concepts/scope.md)
    - [Verwenden der Azure Policy-Erweiterung für Visual Studio Code](../governance/policy/how-to/extension-for-vscode.md)
    - [Programmgesteuertes Erstellen von Richtlinien](../governance/policy/how-to/programmatically-create.md)
- Weitere Informationen zu Azure Event Grid finden Sie in den Artikeln im Abschnitt „Konzepte“, z. B. zur [Event Grid-Terminologie](concepts.md).