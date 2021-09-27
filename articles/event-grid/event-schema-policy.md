---
title: Azure Policy als Event Grid-Quelle
description: In diesem Artikel wird beschrieben, wie Azure Policy als Event Grid-Ereignisquelle verwendet wird. Er enthält das Schema sowie Links zu Tutorials und Artikeln mit Vorgehensweisen.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/15/2021
ms.openlocfilehash: 3b5c984ca1374c50a312665c3f5e3bb063ed8feb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589775"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy als Event Grid-Quelle

In diesem Artikel werden die Eigenschaften und das Schema für [Azure Policy](../governance/policy/index.yml)-Ereignisse angegeben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](./event-schema.md). Außerdem erhalten Sie eine Liste mit Schnellstarts und Tutorials, die Azure Policy als Ereignisquelle verwenden.

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure Policy gibt die folgenden Ereignistypen aus:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | Wird ausgelöst, wenn ein Zustand der Richtlinienkonformität erstellt wird. |
| Microsoft.PolicyInsights.PolicyStateChanged | Wird ausgelöst, wenn ein Status der Richtlinienkonformität geändert wird. |
| Microsoft.PolicyInsights.PolicyStateDeleted | Wird ausgelöst, wenn ein Richtlinienkonformitätszustand gelöscht wird. |

## <a name="example-event"></a>Beispielereignis

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Das folgende Beispiel zeigt das Schema eines „Richtlinienzustand erstellt“-Ereignisses: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Das Schema für ein „Richtlinienzustand geändert“-Ereignis ist ähnlich: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Das folgende Beispiel zeigt das Schema eines „Richtlinienzustand erstellt“-Ereignisses: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Das Schema für ein „Richtlinienzustand geändert“-Ereignis ist ähnlich: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Ereigniseigenschaften

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `topic` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Die vollständig qualifizierte ID der Ressource, für die die Änderung des Konformitätsstatus gilt, einschließlich des Ressourcennamens und des Ressourcentyps. Verwendet das Format `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>`. |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | Azure Policy-Ereignisdaten. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `source` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Die vollständig qualifizierte ID der Ressource, für die die Änderung des Konformitätsstatus gilt, einschließlich des Ressourcennamens und des Ressourcentyps. Verwendet das Format `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>`. |
| `type` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `time` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | Azure Policy-Ereignisdaten. |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `timestamp` | Zeichenfolge | Die Zeit (in UTC), zu der die Ressource von Azure Policy gescannt wurde. Verwenden Sie zum Sortieren von Ereignissen diese Eigenschaft anstelle der Eigenschaften der obersten Ebene `eventTime` oder `time`. |
| `policyAssignmentId` | Zeichenfolge | Die Ressourcen-ID der Richtlinienzuweisung. |
| `policyDefinitionId` | Zeichenfolge | Die Ressourcen-ID der Richtliniendefinition. |
| `policyDefinitionReferenceId` | Zeichenfolge | Die Verweis-ID für die Richtliniendefinition innerhalb der Initiativendefinition, wenn die Richtlinienzuweisung für eine Initiative vorgesehen ist. Kann leer sein. |
| `complianceState` | Zeichenfolge | Der Konformitätszustand der Ressource in Bezug auf die Richtlinienzuweisung. |
| `subscriptionId` | Zeichenfolge | Die Abonnement-ID der Ressource. |
| `complianceReasonCode` | Zeichenfolge | Der Konformitätsursachencode. Kann leer sein. |

## <a name="next-steps"></a>Nächste Schritte

- Eine exemplarische Vorgehensweise für das Routing von Azure Policy-Zustandsänderungsereignissen finden Sie unter [Verwenden von Event Grid für Benachrichtigungen über Richtlinienzustandsänderungen](../governance/policy/tutorials/route-state-change-events.md).
- Eine Übersicht über die Integration von Azure Policy in Event Grid finden Sie unter [Reagieren auf Azure Policy-Ereignisse mithilfe von Event Grid](../governance/policy/concepts/event-overview.md).
- Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](./overview.md).
- Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](./subscription-creation-schema.md).