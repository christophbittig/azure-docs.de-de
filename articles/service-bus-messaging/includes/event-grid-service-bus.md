---
title: include file
description: include file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/20/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491d8cb7cc11d0a904ad70fac8d54c9c6e16670d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287336"
---
## <a name="available-event-types"></a>Verfügbare Ereignistypen

Service Bus gibt die folgenden Ereignistypen aus:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Wird ausgelöst, wenn aktive Nachrichten in einer Warteschlange oder in einem Abonnement vorliegen und kein lauschender Empfänger vorhanden ist. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListeners | Wird ausgelöst, wenn aktive Nachrichten in einer Warteschlange für unzustellbare Nachrichten vorliegen und kein lauschender Empfänger vorhanden ist. |
| Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications | Wird alle 30 Sekunden ausgelöst, wenn aktive Nachrichten in einer Warteschlange oder einem Abonnement vorhanden sind, selbst wenn aktive Listener in dieser spezifischen Warteschlange oder des Abonnements vorhanden sind. Eine Auslösung erfolgt ebenfalls, wenn die Anzahl aktiver Nachrichten für die Warteschlange oder das Abonnement von 0 in einen positiven Wert wechselt. |
| Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications | Wird alle 30 Sekunden ausgelöst, wenn aktive Nachrichten in der Entität für unzustellbare Nachrichten einer Warteschlange oder eines Abonnements vorhanden sind, selbst wenn aktive Listener in der Entität für unzustellbare Nachrichten dieser spezifischen Warteschlange oder des Abonnements vorhanden sind. Eine Auslösung erfolgt ebenfalls, wenn die Anzahl unzustellbarer Nachrichten für die Entität für unzustellbare Nachrichten der Warteschlange oder des Abonnements von 0 in einen positiven Wert wechselt. | 

## <a name="example-event"></a>Beispielereignis

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Aktive Nachrichten ohne Listener verfügbar
Dieses Ereignis wird generiert, wenn aktive Nachrichten in einer Warteschlange oder in einem Abonnement vorliegen und keine lauschenden Empfänger vorhanden sind.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listeners"></a>Unzustellbare Nachrichten ohne Listener verfügbar

Das Schema für ein Ereignis mit Warteschlange für unzustellbare Nachrichten sieht ganz ähnlich aus. Sie erhalten mindestens ein Ereignis pro Warteschlange für unzustellbare Nachrichten, die Nachrichten enthält und über keine aktiven Empfänger verfügt.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Aktive Nachrichten verfügbar: periodische Benachrichtigungen
Dieses Ereignis wird regelmäßig ausgelöst, wenn aktive Nachrichten für eine Warteschlange oder ein Abonnement vorhanden sind, auch wenn aktive Listener in dieser bestimmten Warteschlange oder im Abonnement vorhanden sind.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Unzustellbare Nachrichten verfügbar: periodische Benachrichtigungen
Dieses Ereignis wird regelmäßig ausgelöst, wenn unzustellbare Nachrichten für eine Warteschlange oder ein Abonnement vorhanden sind, auch wenn aktive Listener für die Deadletter-Entität in dieser bestimmten Warteschlange oder im Abonnement vorhanden sind.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Aktive Nachrichten ohne Listener verfügbar
Dieses Ereignis wird generiert, wenn aktive Nachrichten in einer Warteschlange oder in einem Abonnement vorliegen und keine lauschenden Empfänger vorhanden sind.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-with-no-listeners"></a>Unzustellbare Nachrichten ohne Listener verfügbar

Das Schema für ein Ereignis mit Warteschlange für unzustellbare Nachrichten sieht ganz ähnlich aus. Sie erhalten mindestens ein Ereignis pro Warteschlange für unzustellbare Nachrichten, die Nachrichten enthält und über keine aktiven Empfänger verfügt.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Aktive Nachrichten verfügbar: periodische Benachrichtigungen
Dieses Ereignis wird regelmäßig ausgelöst, wenn aktive Nachrichten für eine Warteschlange oder ein Abonnement vorhanden sind, auch wenn aktive Listener in dieser bestimmten Warteschlange oder im Abonnement vorhanden sind.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Unzustellbare Nachrichten verfügbar: periodische Benachrichtigungen
Dieses Ereignis wird regelmäßig ausgelöst, wenn unzustellbare Nachrichten für eine Warteschlange oder ein Abonnement vorhanden sind, auch wenn aktive Listener für die Deadletter-Entität in dieser bestimmten Warteschlange oder im Abonnement vorhanden sind.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>Ereigniseigenschaften

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | Beschreibung |
| -------- | ---- | ----------- |
| `topic` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | Ereignisdaten für Blob Storage. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | Beschreibung |
| -------- | ---- | ----------- |
| `source` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| `type` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `time` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `data` | Objekt (object) | Ereignisdaten für Blob Storage. |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | Beschreibung |
| -------- | ---- | ----------- |
| `namespaceName` | Zeichenfolge | Der Service Bus-Namespace, in dem sich die Ressource befindet. |
| `requestUri` | Zeichenfolge | Der URI für die spezifische Warteschlange oder das Abonnement, von der bzw. von dem das Ereignis ausgegeben wird. |
| `entityType` | Zeichenfolge | Die Art der Service Bus-Entität, die Ereignisse ausgibt (Warteschlange oder Abonnement). |
| `queueName` | Zeichenfolge | Die Warteschlange mit aktiven Nachrichten, falls eine Warteschlange abonniert wird. Bei Verwendung von Themen/Abonnements ist der Wert NULL. |
| `topicName` | Zeichenfolge | Das Thema, zu dem das Service Bus-Abonnement mit aktiven Nachrichten gehört. Bei Verwendung einer Warteschlange ist der Wert NULL. |
| `subscriptionName` | Zeichenfolge | Das Service Bus-Abonnement mit aktiven Nachrichten. Bei Verwendung einer Warteschlange ist der Wert NULL. |
