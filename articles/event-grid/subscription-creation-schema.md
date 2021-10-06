---
title: Azure Event Grid-Abonnementschema
description: In diesem Artikel werden die Eigenschaften für das Abonnieren eines Ereignisses mit Azure Event Grid beschrieben. Event Grid-Abonnementschema
ms.topic: reference
ms.date: 09/28/2021
ms.openlocfilehash: 5a0671bea2ef1cb6b7169ca4e2c94128d88f9f94
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236491"
---
# <a name="event-grid-subscription-schema"></a>Event Grid-Abonnementschema

Zum Erstellen eines Event Grid-Abonnements senden Sie eine Anforderung an den Vorgang zum Erstellen eines Ereignisabonnements. Verwenden Sie das folgende Format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Verwenden Sie also beispielsweise Folgendes, um ein Ereignisabonnement für ein Speicherkonto namens `examplestorage` in einer Ressourcengruppe namens `examplegroup` zu erstellen:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Der Name des Ereignisabonnements muss 3 bis 64 Zeichen umfassen und darf nur die Zeichen a–Z, A–Z, 0–9 und „-“ enthalten. Der Artikel beschreibt die Eigenschaften und das Schema für den Hauptteil der Anforderung.
 
## <a name="event-subscription-properties"></a>Eigenschaften für Ereignisabonnements

| Eigenschaft | Typ | Beschreibung |
| -------- | ---- | ----------- |
| destination | Objekt (object) | Das Objekt, das den Endpunkt definiert. |
| filter | Objekt (object) | Ein optionales Feld zum Filtern der Ereignistypen. |

### <a name="destination-object"></a>destination-Objekt

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| endpointType | Zeichenfolge | Die Art des Endpunkts für das Abonnement (Webhook/HTTP, Event Hub oder Warteschlange). | 
| endpointUrl | Zeichenfolge | Die Ziel-URL für Ereignisse in diesem Ereignisabonnement. | 

### <a name="filter-object"></a>filter-Objekt

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| includedEventTypes | array | Übereinstimmung, wenn der Ereignistyp in der Ereignisnachricht eine exakte Übereinstimmung für einen der Ereignistypnamen ist. Fehler, wenn der Ereignisname nicht den registrierten Ereignistypnamen für die Ereignisquelle entspricht. Der Standardwert entspricht allen Ereignistypen. |
| subjectBeginsWith | Zeichenfolge | Ein Präfixabgleichfilter für das Betrefffeld in der Ereignisnachricht. Der Standardwert oder eine leere Zeichenfolge entspricht allen Optionen. | 
| subjectEndsWith | Zeichenfolge | Ein Suffixabgleichfilter für das Betrefffeld in der Ereignisnachricht. Der Standardwert oder eine leere Zeichenfolge entspricht allen Optionen. |
| isSubjectCaseSensitive | Zeichenfolge | Steuert, ob beim Abgleich von Filtern die Groß-/Kleinschreibung beachtet wird. |
| enableAdvancedFilteringOnArrays | boolean | Ermöglicht die Verwendung von Arrays für Schlüssel in der erweiterten Filterung. Weitere Informationen finden Sie unter [Erweiterte Filterung](event-filtering.md#advanced-filtering). |


## <a name="example-subscription-schema"></a>Beispiel für das Abonnementschema

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
