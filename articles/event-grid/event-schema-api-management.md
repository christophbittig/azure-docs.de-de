---
title: API Management als Event Grid-Quelle
description: In diesem Artikel wird beschrieben, wie Azure API Management als Event Grid-Ereignisquelle verwendet wird. Er enthält das Schema sowie Links zu Artikeln mit Vorgehensweisen.
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 07/12/2021
ms.openlocfilehash: c14107561886a9e29c2d95c5d04847274afdf4e3
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733947"
---
# <a name="azure-api-management-as-an-event-grid-source-preview"></a>Azure API Management als Event Grid-Quelle (Vorschau)

In diesem Artikel werden die Eigenschaften und das Schema für [Azure API Management](../api-management/index.yml)-Ereignisse beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](./event-schema.md). Außerdem erhalten Sie Links zu Artikeln zur Verwendung von API Management als Ereignisquelle.

## <a name="available-event-types"></a>Verfügbare Ereignistypen

API Management gibt die folgenden Ereignistypen aus:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.APIManagement.UserCreated | Wird ausgelöst, wenn ein Benutzer erstellt wird. |
| Microsoft.APIManagement.UserUpdated | Wird ausgelöst, wenn ein Benutzer aktualisiert wird. |
| Microsoft.APIManagement.UserDeleted | Wird ausgelöst, wenn ein Benutzer gelöscht wird. |
| Microsoft.APIManagement.APICreated | Wird ausgelöst, wenn eine API erstellt wird. |
| Microsoft.APIManagement.APIUpdated | Wird ausgelöst, wenn eine API aktualisiert wird. |
| Microsoft.APIManagement.APIDeleted | Wird ausgelöst, wenn eine API gelöscht wird. |
| Microsoft.APIManagement.ProductCreated | Wird ausgelöst, wenn ein Produkt erstellt wird. |
| Microsoft.APIManagement.ProductUpdated | Wird ausgelöst, wenn ein Produkt aktualisiert wird. |
| Microsoft.APIManagement.ProductDeleted | Wird ausgelöst, wenn ein Produkt gelöscht wird. |
| Microsoft.APIManagement.ReleaseCreated | Wird ausgelöst, wenn ein API-Release erstellt wird. |
| Microsoft.APIManagement.ReleaseUpdated | Wird ausgelöst, wenn ein API-Release aktualisiert wird. |
| Microsoft.APIManagement.ReleaseDeleted | Wird ausgelöst, wenn ein API-Release gelöscht wird. |
| Microsoft.APIManagement.SubscriptionCreated | Wird ausgelöst, wenn ein Abonnement erstellt wird. |
| Microsoft.APIManagement.SubscriptionUpdated | Wird ausgelöst, wenn ein Abonnement aktualisiert wird. |
| Microsoft.APIManagement.SubscriptionDeleted | Wird ausgelöst, wenn ein Abonnement gelöscht wird. |

## <a name="example-event"></a>Beispielereignis

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Das folgende Beispiel zeigt das Schema eines Produkterstellungsereignisses: Das Schema anderer Ereignisse zur Erstellung von API Management-Ressourcen ist ähnlich.

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "eventType": "Microsoft.ApiManagement.ProductCreated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Das folgende Beispiel zeigt das Schema eines Produkterstellungsereignisses: Das Schema anderer Ereignisse zur Erstellung von API Management-Ressourcen ist ähnlich. 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "Type": "Microsoft.ApiManagement.ProductCreated",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Das folgende Beispiel zeigt das Schema eines Benutzerlöschungsereignisses: Das Schema anderer Ereignisse zur Löschung von API Management-Ressourcen ist ähnlich.

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "eventType": "Microsoft.ApiManagement.UserDeleted",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Das folgende Beispiel zeigt das Schema eines Benutzerlöschungsereignisses: Das Schema anderer Ereignisse zur Löschung von API Management-Ressourcen ist ähnlich. 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "Type": "Microsoft.ApiManagement.UserDeleted",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

Das folgende Beispiel zeigt das Schema eines API-Aktualisierungsereignisses: Die `data`-Eigenschaft enthält sowohl das `updatedProperies`-Array als auch den `resourceUri`.  Das Schema anderer Ereignisse zur Aktualisierung von API Management-Ressourcen ist ähnlich. 
```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "updatedProperties": [
      "path"
    ],
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "eventType": "Microsoft.ApiManagement.APIUpdated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-12T23:13:44.9048323Z"
}]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Das folgende Beispiel zeigt das Schema eines API-Aktualisierungsereignisses: Die `data`-Eigenschaft enthält sowohl das `updatedProperies`-Array als auch den `resourceUri`.  Das Schema anderer Ereignisse zur Aktualisierung von API Management-Ressourcen ist ähnlich. 

```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "updatedProperties": [
      "path"
    ],
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "Type": "Microsoft.ApiManagement.APIUpdated",
  "Time": "2021-07-12T23:13:44.9048323Z",
  "specversion":1.0
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
| `data` | Objekt (object) | API Management-Ereignisdaten. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `source` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Die vollständig qualifizierte ID der Ressource, für die die Änderung des Konformitätsstatus gilt, einschließlich des Ressourcennamens und des Ressourcentyps. Verwendet das Format `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>`. |
| `type` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `time` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | API Management-Ereignisdaten. |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `resourceUri` | Zeichenfolge | Der URI der API Management-Ressource, die das Ereignis ausgelöst hat. |
| `updatedProperties` | string[] | Liste der Eigenschaften, die in der API Management-Ressource aktualisiert wurden, die ein Aktualisierungsereignis ausgelöst hat. |

## <a name="tutorials-and-how-tos"></a>Tutorials und Vorgehensweisen

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Senden von Ereignissen von API Management an Event Grid](../api-management/how-to-event-grid.md)| Abonnieren von API Management Ereignissen mithilfe von Event Grid |

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](./overview.md).
- Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](./subscription-creation-schema.md).


