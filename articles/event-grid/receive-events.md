---
title: Empfangen von Ereignissen von Azure Event Grid an einem HTTP-Endpunkt
description: Beschreibt, wie ein HTTP-Endpunkt überprüft und dann Ereignisse aus Azure Event Grid empfangen und deserialisiert werden.
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f655982203c81ef6ff32a01566e37c346cf78138
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730277"
---
# <a name="receive-events-to-an-http-endpoint"></a>Empfangen von Ereignissen an einem HTTP-Endpunkt

In diesem Artikel wird beschrieben, wie Sie [einen HTTP-Endpunkt überprüfen](webhook-event-delivery.md), um Ereignisse von einem Ereignisabonnement zu empfangen, und dann Ereignisse empfangen und deserialisieren. In diesem Artikel wird zu Demonstrationszwecken eine Azure-Funktion verwendet, doch treffen die gleichen Konzepte unabhängig davon zu, wo die Anwendung gehostet wird.

> [!NOTE]
> Es wird empfohlen, beim Auslösen einer Azure-Funktion mit Event Grid einen [Event Grid-Trigger](../azure-functions/functions-bindings-event-grid.md) zu verwenden. Er ermöglicht eine einfachere und schnellere Integration zwischen Event Grid und Azure Functions. Beachten Sie jedoch, dass der Event Grid-Trigger von Azure Functions nicht das Szenario unterstützt, in dem der gehostete Code den HTTP-Statuscode steuern muss, der an Event Grid zurückgegeben wird. Aufgrund dieser Einschränkung kann Ihr Code, der auf einer Azure-Function ausgeführt wird, beispielsweise keinen 5XX-Fehler zurückgeben, um einen erneuten Versuch der Ereignisübermittlung durch Event Grid zu initiieren.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Funktions-App mit einer durch HTTP ausgelösten Funktion.

## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

Wenn Sie die Entwicklung in .NET vornehmen, [fügen Sie der Funktion eine Abhängigkeit](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) für das [NuGet-Paket](https://www.nuget.org/packages/Azure.Messaging.EventGrid) `Azure.Messaging.EventGrid` hinzu.

SDKs für andere Sprachen stehen über den Verweis auf [Veröffentlichungs-SDKs](./sdk-overview.md#data-plane-sdks) zur Verfügung. Diese Pakete enthalten die Modelle für native Ereignistypen wie z.B. `EventGridEvent`, `StorageBlobCreatedEventData` und `EventHubCaptureFileCreatedEventData`.

## <a name="endpoint-validation"></a>Endpunktüberprüfung

Als Erstes sollten Ereignisse des Typs `Microsoft.EventGrid.SubscriptionValidationEvent` behandelt werden. Jedes Mal, wenn ein neues Ereignisabonnement erstellt wird, sendet Event Grid ein Überprüfungsereignis an den Endpunkt mit einem `validationCode`-Element in der Datennutzlast. Der Endpunkt muss diesen als Echo im Antworttext zurückgeben, um [nachzuweisen, dass der Endpunkt gültig ist und Sie der Besitzer sind](webhook-event-delivery.md). Wenn Sie einen [Event Grid-Trigger](../azure-functions/functions-bindings-event-grid.md) statt einer durch WebHook ausgelösten Funktion verwenden, erfolgt die Endpunktüberprüfung automatisch. Wenn Sie einen API-Dienst eines Drittanbieters (etwa [Zapier](https://zapier.com/home) oder [IFTTT](https://ifttt.com/)) verwenden, können Sie den Überprüfungscode unter Umständen nicht programmgesteuert wiederholen. Für diese Dienste können Sie das Abonnement mithilfe einer Überprüfungs-URL manuell überprüfen, die im Abonnementüberprüfungsereignis gesendet wird. Kopieren Sie diese URL in der Eigenschaft `validationUrl`, und senden Sie entweder über einen REST-Client oder Ihren Webbrowser eine GET-Anforderung.

In C# wird die `ParseMany()`-Methode verwendet, um eine `BinaryData`-Instanz mit mindestens einem Ereignis in ein Array von `EventGridEvent` zu deserialisieren. Wenn Sie im Voraus wissen, dass Sie nur ein einzelnes Ereignis deserialisieren, können Sie stattdessen die `Parse`-Methode verwenden.

Verwenden Sie zum programmgesteuerten Wiederholen des Überprüfungscodes den folgenden Code.

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Testen der Überprüfungsantwort

Testen Sie die Funktion der Überprüfungsantwort, indem Sie das Beispielereignis in das Testfeld für die Funktion einfügen:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Wenn Sie auf „Ausführen“ klicken, sollte die Ausgabe den Status „200 OK“ aufweisen und `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` im Text enthalten sein:

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Überprüfungsanforderung":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Überprüfungsausgabe":::

## <a name="handle-blob-storage-events"></a>Behandeln von Blob Storage-Ereignissen

Erweitern wir nun die Funktion auf die Behandlung des `Microsoft.Storage.BlobCreated`-Systemereignisses:

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Testen der Behandlung durch Blobs erstellter Ereignisse

Testen Sie die neue Funktionalität der Funktion, indem Sie ein [Blob Storage-Ereignis](./event-schema-blob-storage.md#example-event) in das Testfeld einfügen und ausführen:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Die Blob-URL sollte im Funktionsprotokoll ausgegeben werden:

![Ausgabeprotokoll](./media/receive-events/blob-event-response.png)

Sie können dies auch testen, indem Sie ein Blob Storage-Konto oder ein Speicherkonto des Typs „Universell V2“ (GPv2) erstellen, [ein Ereignisabonnement hinzufügen](../storage/blobs/storage-blob-event-quickstart.md) und den Endpunkt auf die Funktions-URL festlegen:

![Funktions-URL](./media/receive-events/function-url.png)

## <a name="handle-custom-events&quot;></a>Behandeln benutzerdefinierter Ereignisse

Abschließend wird die Funktion noch einmal erweitert, sodass auch benutzerdefinierte Ereignisse behandelt werden können. 

Fügen Sie einen Test für das Ereignis `Contoso.Items.ItemReceived` hinzu. Der resultierende Code sollte wie folgt aussehen:

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName(&quot;Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
                // Handle the custom contoso event
                else if (eventGridEvent.EventType == "Contoso.Items.ItemReceived")
                {
                    var contosoEventData = eventGridEvent.Data.ToObjectFromJson<ContosoItemReceivedEventData>();
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {contosoEventData.ItemSku}");
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testen der Behandlung benutzerdefinierter Ereignisse

Testen Sie zum Schluss, ob die Funktion jetzt den benutzerdefinierten Ereignistyp verarbeiten kann:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Sie können diese Funktionalität auch live testen, indem Sie [ein benutzerdefiniertes Ereignis mit CURL vom Portal senden](./custom-event-quickstart-portal.md) oder mithilfe von Diensten oder Anwendungen, die an einen Endpunkt posten können (z. B. [Postman](https://www.getpostman.com/)), [in einem benutzerdefinierten Thema posten](./post-to-custom-topic.md). Erstellen Sie ein benutzerdefiniertes Thema und ein Ereignisabonnement, wobei der Endpunkt als Funktions-URL festgelegt ist.

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Nächste Schritte

* Erkunden Sie die [Azure Event Grid-SDKs für Verwaltung und Veröffentlichung](./sdk-overview.md).
* Erfahren Sie, wie Sie [in einem benutzerdefinierten Thema posten](./post-to-custom-topic.md).
* Probieren Sie eines der umfassenden Tutorials zu Event Grid und Functions aus, z. B. [Ändern der Größe von in Blob Storage hochgeladenen Bildern](resize-images-on-storage-blob-upload-event.md).
