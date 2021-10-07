---
title: REST-API – Azure Event Grid IoT Edge | Microsoft-Dokumentation
description: REST-API in Event Grid in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: d9e634597d82b6cac52237f0c800cdfc33bc883a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635411"
---
# <a name="rest-api"></a>REST-API
Dieser Artikel beschreibt die REST-APIs von Azure Event Grid in IoT Edge.

## <a name="common-api-behavior"></a>Allgemeines API-Verhalten

### <a name="base-url"></a>Basis-URL
Event Grid in IoT Edge macht die folgenden APIs über HTTP (Port 5888) und HTTPS (Port 4438) verfügbar.

* Basis-URL für HTTP: http://eventgridmodule:5888
* Basis-URL für HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Anfordern der Abfragezeichenfolge
Alle API-Anforderungen benötigen den folgenden Abfragezeichenfolgenparameter:

`?api-version=2019-01-01-preview`

### <a name="request-content-type"></a>Anfordern des Inhaltstyps
Alle API-Anforderungen müssen einen **Content-Type** besitzen.

Im Falle von **EventGridSchema** oder **CustomSchema** kann „Content-Type“ einen der folgenden Werte aufweisen:

`Content-Type: application/json`

`Content-Type: application/json; charset=utf-8`

Im Falle von **CloudEventSchemaV1_0** im strukturierten Modus kann „Content-Type“ einen der folgenden Werte aufweisen:

`Content-Type: application/cloudevents+json`
    
`Content-Type: application/cloudevents+json; charset=utf-8`
    
`Content-Type: application/cloudevents-batch+json`
    
`Content-Type: application/cloudevents-batch+json; charset=utf-8`

Im Falle von **CloudEventSchemaV1_0** im binären Modus finden Sie Details in der [Dokumentation](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md).

### <a name="error-response"></a>Fehlerantwort
Alle APIs geben einen Fehler mit der folgenden Nutzlast zurück:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Verwalten von Themen

### <a name="put-topic-create--update"></a>Platzieren (Put) eines Themas (Erstellen/Aktualisieren)

**Anforderung**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Nutzlast**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Antwort:** HTTP 200

**Nutzlast**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Abrufen (Get) eines Themas

**Anforderung**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Antwort:** HTTP 200

**Nutzlast**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Abrufen (Get) aller Themen

**Anforderung**: ``` GET /topics?api-version=2019-01-01-preview ```

**Antwort:** HTTP 200

**Nutzlast**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Löschen eines Themas

**Anforderung**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Antwort:** HTTP 200, leere Nutzdaten

## <a name="manage-event-subscriptions"></a>Verwalten von Ereignisabonnements
Beispiele in diesem Abschnitt verwenden `EndpointType=Webhook;`. Die JSON-Beispiele für `EndpointType=EdgeHub / EndpointType=EventGrid` befinden sich im nächsten Abschnitt. 

### <a name="put-event-subscription-create--update"></a>Platzieren (Put) eines Ereignisabonnements (Erstellen/Aktualisieren)

**Anforderung**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Nutzlast**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Antwort:** HTTP 200

**Nutzlast**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Abrufen (Get) des Ereignisabonnements

**Anforderung**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Antwort:** HTTP 200

**Nutzlast**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Abrufen (Get) von Ereignisabonnements

**Anforderung**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Antwort:** HTTP 200

**Nutzlast**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Löschen eines Ereignisabonnements

**Anforderung**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Antwort:** HTTP 200, keine Nutzdaten


## <a name="publish-events-api"></a>API zum Veröffentlichen von Ereignissen

### <a name="send-batch-of-events-in-event-grid-schema"></a>Senden eines Ereignisbatches (im Event Grid-Schema)

**Anforderung**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Antwort:** HTTP 200, leere Nutzdaten


**Nutzlastfeldbeschreibungen**
- ```Id``` ist obligatorisch. Dies kann ein beliebiger Zeichenfolgenwert sein, der vom Aufrufer ausgefüllt wird. Event Grid führt KEINE Erkennung von Duplikaten durch und erzwingt KEINE Semantiken für dieses Feld.
- ```Topic``` ist optional, aber wenn angegeben, muss es dem „topic_name“ aus der Anforderungs-URL entsprechen.
- ```Subject``` ist obligatorisch; kann ein beliebiger Zeichenfolgenwert sein.
- ```EventType``` ist obligatorisch; kann ein beliebiger Zeichenfolgenwert sein.
- ```EventTime``` ist obligatorisch; wird nicht überprüft, sollte aber ein ordnungsgemäßer DateTime-Wert sein.
- ```DataVersion``` ist obligatorisch.
- ```MetadataVersion``` ist optional; falls angegeben, MUSS es sich um eine Zeichenfolge mit dem Wert ```"1"``` handeln.
- ```Data``` ist optional und kann ein beliebiges JSON-Token sein (Zahl, Zeichenfolge, boolescher Wert, Array, Objekt).

### <a name="send-batch-of-events-in-custom-schema"></a>Senden eines Ereignisbatches (im benutzerdefinierten Schema)

**Anforderung**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Antwort:** HTTP 200, leere Nutzdaten


**Nutzlasteinschränkungen**
- MUSS ein Array von Ereignissen sein.
- Jeder Arrayeintrag MUSS ein JSON-Objekt sein.
- Keine anderen Einschränkungen (außer der Nutzlastgröße).

## <a name="examples"></a>Beispiele

### <a name="set-up-topic-with-eventgrid-schema"></a>Einrichten eines Themas mit dem EventGrid-Schema
Richtet ein Thema ein, das erfordert, dass Ereignisse in **eventgridschema** veröffentlicht werden.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Einrichten eines Themas mit dem benutzerdefinierten Schema
Richtet ein Thema ein, das erfordert, dass Ereignisse in `customschema` veröffentlicht werden.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Einrichten eines Themas mit dem Cloudereignisschema
Richtet ein Thema ein, das erfordert, dass Ereignisse in `cloudeventschema` veröffentlicht werden.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Einrichten von WebHooks als Ziel, Ereignisse, die in „eventgridschema“ übermittelt werden sollen
Verwenden Sie diesen Zieltyp, um Ereignisse an ein beliebiges anderes Modul (das einen HTTP-Endpunkt hostet) oder an einen HTTP-adressierbaren Endpunkt im Netzwerk/Internet zu senden.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Einschränkungen für das `endpointUrl`-Attribut:
- Es darf nicht Null sein.
- Es muss eine absolute URL sein.
- Wenn „outbound__webhook__httpsOnly“ in den EventGridModule-Einstellungen auf „true“ festgelegt ist, darf es nur HTTPS sein.
- Wenn „outbound__webhook__httpsOnly“ auf „false“ festgelegt ist, kann es HTTP oder HTTPS sein.

Einschränkungen für die Eigenschaft `eventDeliverySchema`:
- Sie muss dem Eingabeschema des Abonnententhemas entsprechen.
- Sie kann Null sein. Ihr Standardwert ist das Eingabeschema des Themas.

### <a name="set-up-iot-edge-as-destination"></a>Einrichten von IoT Edge als Ziel

Verwenden Sie dieses Ziel, um Ereignisse an den IoT Edge Hub zu senden und sie dem Edge Hub-Subsystem für Routing/Filterung/Weiterleitung zu unterziehen.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Einrichten der Event Grid-Cloud als Ziel

Verwenden Sie dieses Ziel, um Ereignisse an Event Grid in der Cloud (Azure) zu senden. Zunächst müssen Sie ein Benutzerthema in der Cloud einrichten, an das Ereignisse gesendet werden sollen, bevor Sie ein Ereignisabonnement auf dem Edge erstellen.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- Es darf nicht Null sein.
- Es muss eine absolute URL sein.
- Der Pfad `/api/events` muss im Pfad der Anforderungs-URL definiert sein.
- Muss `api-version=2018-01-01` in der Abfragezeichenfolge aufweisen.
- Wenn „outbound__eventgrid__httpsOnly“ in den EventGridModule-Einstellungen auf „true“ (Standardeinstellung) festgelegt ist, darf es nur HTTPS sein.
- Wenn „outbound__eventgrid__httpsOnly“ auf „false“ festgelegt ist, kann es HTTP oder HTTPS sein.
- Wenn „outbound__eventgrid__allowInvalidHostnames“ auf „false“ (Standardeinstellung) festgelegt ist, muss es auf einen der folgenden Endpunkte abzielen:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Darf nicht Null sein.

TopicName:
- Wenn das „Subscription.EventDeliverySchema“ auf „EventGridSchema“ festgelegt ist, wird der Wert aus diesem Feld in das Themenfeld jedes Ereignisses eingefügt, bevor es an Event Grid in der Cloud weitergeleitet wird.
- Wenn das „Subscription.EventDeliverySchema“ auf „CustomEventSchema“ festgelegt ist, wird diese Eigenschaft ignoriert, und die benutzerdefinierte Ereignisnutzlast wird genau so weitergeleitet, wie Sie empfangen wurde.

## <a name="set-up-event-hubs-as-a-destination"></a>Einrichten von Event Hubs als Ziel

Legen Sie zum Veröffentlichen in einem Event Hub `endpointType` auf `eventHub` fest, und geben Sie Folgendes an:

* connectionString: Die Verbindungszeichenfolge für den jeweiligen Event Hub, den Sie als Ziel verwenden, die über eine SAS-Richtlinie generiert wird.

    >[!NOTE]
    > Die Verbindungszeichenfolge muss für die Entität spezifisch sein. Die Verwendung einer Namespace-Verbindungszeichenfolge funktioniert nicht. Sie können eine entitätsspezifische Verbindungszeichenfolge generieren, indem Sie zum jeweiligen Event Hub navigieren, in dem Sie im Azure-Portal veröffentlichen möchten, und dann auf **SAS-Richtlinien** klicken, um eine neue entitätsspezifische Verbindungszeichenfolge zu generieren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Einrichten von Service Bus-Warteschlangen als Ziel

Legen Sie zum Veröffentlichen in einer Service Bus-Warteschlange `endpointType` auf `serviceBusQueue` fest, und geben Sie Folgendes an:

* connectionString: Die Verbindungszeichenfolge für die jeweilige Service Bus-Warteschlange, die Sie als Ziel verwenden, die über eine SAS-Richtlinie generiert wird.

    >[!NOTE]
    > Die Verbindungszeichenfolge muss für die Entität spezifisch sein. Die Verwendung einer Namespace-Verbindungszeichenfolge funktioniert nicht. Generieren Sie eine entitätsspezifische Verbindungszeichenfolge, indem Sie zu der jeweiligen Service Bus-Warteschlange navigieren, in der Sie im Azure-Portal veröffentlichen möchten. Klicken Sie dann auf **SAS-Richtlinien**, um eine neue entitätsspezifische Verbindungszeichenfolge zu generieren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Einrichten von Service Bus-Themen als Ziel

Legen Sie zum Veröffentlichen in einem Service Bus-Thema `endpointType` auf `serviceBusTopic` fest, und geben Sie Folgendes an:

* connectionString: Die Verbindungszeichenfolge für das jeweilige Service Bus-Thema, das Sie als Ziel verwenden, die über eine SAS-Richtlinie generiert wird.

    >[!NOTE]
    > Die Verbindungszeichenfolge muss für die Entität spezifisch sein. Die Verwendung einer Namespace-Verbindungszeichenfolge funktioniert nicht. Generieren Sie eine entitätsspezifische Verbindungszeichenfolge, indem Sie zum jeweiligen Service Bus-Thema navigieren, in dem Sie im Azure-Portal veröffentlichen möchten, und klicken Sie dann auf **SAS-Richtlinien**, um eine neue entitätsspezifische Verbindungszeichenfolge zu generieren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Einrichten von Storage-Warteschlangen als Ziel

Legen Sie zum Veröffentlichen in einer Storage-Warteschlange `endpointType` auf `storageQueue` fest, und geben Sie Folgendes an:

* queueName: Der Name der Storage-Warteschlange, in der Sie veröffentlichen.
* connectionString: Die Verbindungszeichenfolge für das Speicherkonto, in dem sich die Storage-Warteschlange befindet.

  >[!NOTE]
  > Im Gegensatz zu Event Hubs, Service Bus-Warteschlangen und Service Bus-Themen ist die für Storage-Warteschlangen verwendete Verbindungszeichenfolge nicht entitätsspezifisch. Stattdessen muss dies die Verbindungszeichenfolge für das Speicherkonto sein.

  ```json
  {
    "properties": {
      "destination": {
        "endpointType": "storageQueue",
        "properties": {
          "queueName": "<your-storage-queue-name>",
          "connectionString": "<your-storage-account-connection-string>"
        }
      }
    }
  }
  ```
