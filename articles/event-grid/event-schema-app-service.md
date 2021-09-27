---
title: Azure App Service als Event Grid-Quelle
description: In diesem Artikel wird beschrieben, wie Azure App Service als Event Grid-Ereignisquelle verwendet wird. Er enthält das Schema sowie Links zu Tutorials und Artikeln mit Vorgehensweisen.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: jafreebe
ms.openlocfilehash: 92667242b52ee58383f68e5b032eb5580cad2826
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647440"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service als Event Grid-Quelle

In diesem Artikel werden die Eigenschaften und das Schema für Azure App Service-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). Außerdem erhalten Sie eine Liste mit Schnellstarts und Tutorials, die Azure App Service als Ereignisquelle verwenden.

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure App Service gibt die folgenden Ereignistypen aus:

|    Ereignistyp                                             |    BESCHREIBUNG                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    Wird ausgelöst, wenn eine Sicherung gestartet wurde.                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    Wird ausgelöst, wenn eine Sicherung abgeschlossen wurde.                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    Wird ausgelöst, wenn eine Sicherung fehlgeschlagen ist.                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    Wird ausgelöst, wenn eine Wiederherstellung auf Grundlage einer Sicherung gestartet wurde.        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    Wird ausgelöst, wenn eine Wiederherstellung auf Grundlage einer Sicherung abgeschlossen wurde.      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    Wird ausgelöst, wenn eine Wiederherstellung auf Grundlage einer Sicherung fehlgeschlagen ist.         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    Wird ausgelöst, wenn ein Slottausch gestartet wurde.                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    Wird ausgelöst, wenn ein Slottausch abgeschlossen wurde.                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    Wird ausgelöst, wenn ein Slottausch fehlgeschlagen ist.                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    Wird ausgelöst, wenn ein Slottausch mit Vorschau gestartet wurde.           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    Wird ausgelöst, wenn ein Slottausch mit Vorschau abgebrochen wurde.    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    Wird ausgelöst, wenn eine Website neu gestartet wurde.                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    Wird ausgelöst, wenn eine Website angehalten wurde.                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    Wird ausgelöst, wenn die App-Einstellungen einer Website geändert wurden.             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    Wird ausgelöst, wenn ein App Service-Plan aktualisiert wurde.                 |

## <a name="properties-common-to-all-events"></a>Eigenschaften, die allen Ereignissen gemeinsam sind

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt.
In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen. Jedes Ereignis weist die folgenden Daten auf oberster Ebene auf:

|     Eigenschaft          |     type     |     Beschreibung                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    Zeichenfolge    |    Vollständiger Ressourcenpfad zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt.                                      |
|    `subject`            |    Zeichenfolge    |    Vom Herausgeber definierter Pfad zum Ereignisbetreff.                                                                                              |
|    `eventType`          |    Zeichenfolge    |    Einer der registrierten Ereignistypen für die Ereignisquelle.                                                                                  |
|    `eventTime`          |    Zeichenfolge    |    Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters.                                                                         |
|    `id`                 |    Zeichenfolge    |    Eindeutiger Bezeichner für das Ereignis.                                                                                                            |
|    `data`               |    Objekt (object)    |    Ereignisdaten für Blob Storage.                                                                                                                    |
|    `dataVersion`        |    Zeichenfolge    |    Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion.                                                          |
|    `metadataVersion`    |    Zeichenfolge    |    Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt.    |

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt.
In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen. Jedes Ereignis weist die folgenden Daten auf oberster Ebene auf:

|     Eigenschaft          |     type     |     Beschreibung                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    Zeichenfolge    |    Vollständiger Ressourcenpfad zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt.                                      |
|    `subject`            |    Zeichenfolge    |    Vom Herausgeber definierter Pfad zum Ereignisbetreff.                                                                                              |
|    `type`          |    Zeichenfolge    |    Einer der registrierten Ereignistypen für die Ereignisquelle.                                                                                  |
|    `time`          |    Zeichenfolge    |    Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters.                                                                         |
|    `id`                 |    Zeichenfolge    |    Eindeutiger Bezeichner für das Ereignis.                                                                                                            |
|    `data`               |    Objekt (object)    |    Ereignisdaten für Blob Storage.                                                                                                                    |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---

## <a name="example-events"></a>Beispielereignisse

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

Das Datenobjekt enthält die folgenden Eigenschaften:

|    Eigenschaft                |    type      |    BESCHREIBUNG                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    Objekt (object)    |    Detail einer Aktion für die App                                                                                       |
|    `action`                  |    Zeichenfolge    |    Typ der Aktion des Vorgangs                                                                                   |
|    `name`                    |    Zeichenfolge    |    Name der Website, auf der das Ereignis aufgetreten ist                                                                          |
|    `clientRequestId`         |    Zeichenfolge    |    Die Clientanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.         |
|    `correlationRequestId`    |    Zeichenfolge    |    Die Korrelationsanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.    |
|    `requestId`               |    Zeichenfolge    |    Die Anforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.                |
|    `address`                 |    Zeichenfolge    |    HTTP-Anforderungs-URL für diesen Vorgang                                                                                |
|    `verb`                    |    Zeichenfolge    |    HTTP-Verb für diesen Vorgang                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Das Datenobjekt enthält die folgenden Eigenschaften:

|    Eigenschaft                |    type      |    BESCHREIBUNG                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    Objekt (object)    |    Detail einer Aktion für die App                                                                                       |
|    `action`                  |    Zeichenfolge    |    Typ der Aktion des Vorgangs                                                                                   |
|    `name`                    |    Zeichenfolge    |    Name der Website, auf der das Ereignis aufgetreten ist                                                                          |
|    `clientRequestId`         |    Zeichenfolge    |    Die Clientanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.         |
|    `correlationRequestId`    |    Zeichenfolge    |    Die Korrelationsanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.    |
|    `requestId`               |    Zeichenfolge    |    Die Anforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.                |
|    `address`                 |    Zeichenfolge    |    HTTP-Anforderungs-URL für diesen Vorgang                                                                                |
|    `verb`                    |    Zeichenfolge    |    HTTP-Verb für diesen Vorgang                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Das Datenobjekt enthält die folgenden Eigenschaften:

|    Eigenschaft                |    type      |    BESCHREIBUNG                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    Objekt (object)    |    Detail einer Aktion für die App                                                                                       |
|    `action`                 |    Zeichenfolge    |    Typ der Aktion des Vorgangs                                                                                   |
|    `name`                    |    Zeichenfolge    |    Name der Website, auf der das Ereignis aufgetreten ist                                                                          |
|    `clientRequestId`         |    Zeichenfolge    |    Die Clientanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.         |
|    `correlationRequestId`    |    Zeichenfolge    |    Die Korrelationsanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.    |
|   `requestId`               |    Zeichenfolge    |    Die Anforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.                |
|    `address`                 |    Zeichenfolge    |    HTTP-Anforderungs-URL für diesen Vorgang                                                                                |
|    `verb`                    |    Zeichenfolge    |    HTTP-Verb für diesen Vorgang                                                                                       |
|    `sourceSlot`              |    Zeichenfolge    |    Der Quellslot des Tauschs                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Das Datenobjekt enthält die folgenden Eigenschaften:

|    Eigenschaft                |    type      |    BESCHREIBUNG                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    Objekt (object)    |    Detail einer Aktion für die App                                                                                       |
|    `action`                 |    Zeichenfolge    |    Typ der Aktion des Vorgangs                                                                                   |
|    `name`                    |    Zeichenfolge    |    Name der Website, auf der das Ereignis aufgetreten ist                                                                          |
|    `clientRequestId`         |    Zeichenfolge    |    Die Clientanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.         |
|    `correlationRequestId`    |    Zeichenfolge    |    Die Korrelationsanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.    |
|    `requestId`               |    Zeichenfolge    |    Die Anforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.                |
|    `address`                 |    Zeichenfolge    |    HTTP-Anforderungs-URL für diesen Vorgang                                                                                |
|    `verb`                    |    Zeichenfolge    |    HTTP-Verb für diesen Vorgang                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Das Datenobjekt weist die folgenden Eigenschaften auf:

|    Eigenschaft                |    type      |    BESCHREIBUNG                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    Objekt (object)    |    Detail einer Aktion für die App                                                                                       |
|    `action`                  |    Zeichenfolge    |    Typ der Aktion des Vorgangs                                                                                   |
|    `name`                    |    Zeichenfolge    |    Name der Website, auf der das Ereignis aufgetreten ist                                                                          |
|    `clientRequestId`         |    Zeichenfolge    |    Die Clientanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.         |
|    `correlationRequestId`    |    Zeichenfolge    |    Die Korrelationsanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.    |
|    `requestId`               |    Zeichenfolge    |    Die Anforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.                |
|    `address`                 |    Zeichenfolge    |    HTTP-Anforderungs-URL für diesen Vorgang                                                                                |
|    `verb`                    |    Zeichenfolge    |    HTTP-Verb für diesen Vorgang                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

Das Datenobjekt weist die folgenden Eigenschaften auf:

|    Eigenschaft                         |    type      |    BESCHREIBUNG                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    Objekt (object)    |    Detail einer Aktion für den App Service-Plan                                                                          |
|    `stampKind`                        |    Zeichenfolge    |    Art der Umgebung, in der sich der App Service-Plan befindet                                                                     |
|    `action`                           |    Zeichenfolge    |    Typ der Aktion für den App Service-Plan                                                                            |
|    `status`                           |    Zeichenfolge    |    Status des Vorgangs für den App Service-Plan                                                                   |
|    `sku`                              |    Objekt (object)    |    SKU des App Service-Plans                                                                                       |
|    `name`                             |    Zeichenfolge    |    Name des App Service-Plans                                                                                      |
|    `Tier`                             |    Zeichenfolge    |    Dienstebene des App Service-Plans                                                                                      |
|    `Size`                             |    Zeichenfolge    |    Größe des App Service-Plans                                                                                      |
|    `Family`                           |    Zeichenfolge    |    Familie des App Service-Plans                                                                                        |
|    `Capacity`                         |    Zeichenfolge    |    Kapazität des App Service-Plans                                                                                      |
|    `action`                           |    Zeichenfolge    |    Typ der Aktion des Vorgangs                                                                                   |
|    `name`                             |    Zeichenfolge    |    Name der Website, auf der das Ereignis aufgetreten ist                                                                          |
|    `clientRequestId`                  |    Zeichenfolge    |    Die Clientanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.         |
|    `correlationRequestId`             |    Zeichenfolge    |    Die Korrelationsanforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.    |
|    `requestId`                        |    Zeichenfolge    |    Die Anforderungs-ID, die vom App-Dienst für den Website-API-Vorgang generiert wird, der das Ereignis ausgelöst hat.                |
|    `address`                         |    Zeichenfolge    |    HTTP-Anforderungs-URL für diesen Vorgang                                                                                |
|    `verb`                             |    Zeichenfolge    |    HTTP-Verb für diesen Vorgang                                                                                       |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).