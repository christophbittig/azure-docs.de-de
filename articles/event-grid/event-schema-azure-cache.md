---
title: Azure Cache for Redis als Event Grid-Quelle
description: Hier werden die Eigenschaften beschrieben, die für Azure Cache for Redis-Ereignisse mit Azure Event Grid bereitgestellt werden.
ms.topic: conceptual
ms.date: 09/15/2021
author: curib
ms.author: cauribeg
ms.openlocfilehash: fa890dd62ce6c5695128e666556cd00f9a08711b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626929"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure Cache for Redis als Event Grid-Quelle

In diesem Artikel werden die Eigenschaften und das Schema für Azure Cache for Redis-Ereignisse beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). 

## <a name="available-event-types"></a>Verfügbare Ereignistypen
Diese Ereignisse werden ausgelöst, wenn ein Client durch Aufrufen von Azure Cache for Redis-REST-APIs einen Export-, Import- oder Skalierungsvorgang ausführt. Das Patchingereignis wird durch ein Redis-Update ausgelöst.

 |Ereignisname |BESCHREIBUNG|
 |----------|-----------|
 |**Microsoft.Cache.ExportRDBCompleted** |Wird ausgelöst, wenn Cachedaten exportiert werden. |
 |**Microsoft.Cache.ImportRDBCompleted** |Wird ausgelöst, wenn Cachedaten importiert werden. |
 |**Microsoft.Cache.PatchingCompleted** |Wird ausgelöst, wenn das Patchen abgeschlossen ist. |
 |**Microsoft.Cache.ScalingCompleted** |Wird ausgelöst, wenn das Skalieren abgeschlossen ist. |

## <a name="example-event"></a>Beispielereignis
Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt. In diesem Abschnitt wird jeweils anhand eines Beispiels gezeigt, wie diese Daten für die Azure Cache for Redis-Ereignisse aussehen:

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

### <a name="microsoftcachepatchingcompleted-event"></a>Ereignis „Microsoft.Cache.PatchingCompleted“

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Ereignis „Microsoft.Cache.ImportRDBCompleted“

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Ereignis „Microsoft.Cache.ExportRDBCompleted“

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft.Cache.ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)


### <a name="microsoftcachepatchingcompleted-event"></a>Ereignis „Microsoft.Cache.PatchingCompleted“

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.PatchingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "PatchingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "PatchingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Ereignis „Microsoft.Cache.ImportRDBCompleted“

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ImportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ImportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ImportRDBCompleted",
    "eventTime": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Ereignis „Microsoft.Cache.ExportRDBCompleted“

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ExportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ExportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ExportRDBCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft.Cache.ScalingCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ScalingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ScalingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ScalingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
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
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | Azure Cache for Redis-Ereignisdaten. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |


# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)


Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | Beschreibung |
| -------- | ---- | ----------- |
| `source` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `type` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `time` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | Azure Cache for Redis-Ereignisdaten. |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---


Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `timestamp` | Zeichenfolge | Der Zeitpunkt, an dem das Ereignis aufgetreten ist. |
| `name` | Zeichenfolge | Der Name des Ereignisses. |
| `status` | Zeichenfolge | Der Status des Ereignisses. Erfolgreich oder nicht erfolgreich. |

## <a name="quickstarts"></a>Schnellstarts

Wenn Sie Azure Cache for Redis-Ereignisse ausprobieren möchten, können Sie die folgenden Schnellstartartikel verwenden:

|Gewünschtes Tool:    |In diesem Artikel finden Sie weitere Informationen: |
|--|-|
|Azure-Portal    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit dem Azure-Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit der Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).

