---
title: Azure Event Grid-Ausgabebindung für Azure Functions
description: Erfahren Sie, wie Sie ein Event Grid-Ereignis in Azure Functions senden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: f29302efdf6d2a0c0b12ec15d897efda16cdc368
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444416"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid-Ausgabebindung für Azure Functions

Verwenden Sie die Event Grid-Ausgabebindung, um Ereignisse in ein benutzerdefiniertes Thema zu schreiben. Sie müssen einen gültigen [Zugriffsschlüssel für das benutzerdefinierte Thema](../event-grid/security-authenticate-publishing-clients.md) besitzen.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-event-grid.md).

> [!NOTE]
> Die Event Grid-Ausgabebindung unterstützt keine SAS-Token (Shared Access Signature). Sie müssen den Zugriffsschlüssel des Themas verwenden.

> [!IMPORTANT]
> Die Event Grid-Ausgabebindung ist nur für Functions 2. x und höher verfügbar.

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

### <a name="c-2x-and-higher"></a>C# (ab 2.x)

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Nachricht in ein benutzerdefiniertes Event Grid-Thema schreibt. Dabei wird der Rückgabewert der Methode als Ausgabe verwendet:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Das folgende Beispiel zeigt, wie Sie die `IAsyncCollector`-Schnittstelle verwenden, um einen Nachrichtenbatch zu senden.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

### <a name="version-3x-preview"></a>Version 3.x (Vorschau)

Das folgende Beispiel zeigt eine Functions 3.x-[C#-Funktion](functions-dotnet-class-library.md) für die Bindung an `CloudEvent`:

```cs
using System.Threading.Tasks;
using Azure.Messaging;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class CloudEventBindingFunction
    {
        [FunctionName("CloudEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<CloudEvent> eventCollector)
        {
            CloudEvent e = new CloudEvent("IncomingRequest", "IncomingRequest", await req.ReadAsStringAsync());
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

Das folgende Beispiel zeigt eine Functions 3.x-[C#-Funktion](functions-dotnet-class-library.md) für die Bindung an `EventGridEvent`:

```cs
using System.Threading.Tasks;
using Azure.Messaging.EventGrid;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class EventGridEventBindingFunction
    {
        [FunctionName("EventGridEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<EventGridEvent> eventCollector)
        {
            EventGridEvent e = new EventGridEvent(await req.ReadAsStringAsync(), "IncomingRequest", "IncomingRequest", "1.0.0");
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt die Event Grid-Ausgabebindungsdaten in der Datei *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Der folgende C#-Skriptcode erstellt ein einzelnes Ereignis:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Der folgende C#-Skriptcode erstellt mehrere Ereignisse:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

Das folgende Beispiel zeigt eine Java-Funktion, die eine Nachricht in ein benutzerdefiniertes Event Hub-Thema schreibt. Die Funktion verwendet die `setValue`-Methode der Bindung, um eine Zeichenfolge auszugeben.

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<String> outputEvent,
            final ExecutionContext context) {
        context.getLogger().info("Java EventGrid trigger processed a request." + content);
        final String eventGridOutputDocument = "{\"id\": \"1807\", \"eventType\": \"recordInserted\", \"subject\": \"myapp/cars/java\", \"eventTime\":\"2017-08-10T21:03:07+00:00\", \"data\": {\"make\": \"Ducati\",\"model\": \"Monster\"}, \"dataVersion\": \"1.0\"}";
        outputEvent.setValue(eventGridOutputDocument);
    }
}
```

Sie können auch eine POJO-Klasse verwenden, um EventGrid-Nachrichten zu senden.

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<EventGridEvent> outputEvent,
            final ExecutionContext context) {
        context.getLogger().info("Java EventGrid trigger processed a request." + content);

        final EventGridEvent eventGridOutputDocument = new EventGridEvent();
        eventGridOutputDocument.setId("1807");
        eventGridOutputDocument.setEventType("recordInserted");
        eventGridOutputDocument.setEventTime("2017-08-10T21:03:07+00:00");
        eventGridOutputDocument.setDataVersion("1.0");
        eventGridOutputDocument.setSubject("myapp/cars/java");
        eventGridOutputDocument.setData("{\"make\": \"Ducati\",\"model\":\"monster\"");

        outputEvent.setValue(eventGridOutputDocument);
    }
}

class EventGridEvent {
    private String id;
    private String eventType;
    private String subject;
    private String eventTime;
    private String dataVersion;
    private String data;

    public String getId() {
        return id;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }

    public String getDataVersion() {
        return dataVersion;
    }

    public void setDataVersion(String dataVersion) {
        this.dataVersion = dataVersion;
    }

    public String getEventTime() {
        return eventTime;
    }

    public void setEventTime(String eventTime) {
        this.eventTime = eventTime;
    }

    public String getSubject() {
        return subject;
    }

    public void setSubject(String subject) {
        this.subject = subject;
    }

    public String getEventType() {
        return eventType;
    }

    public void setEventType(String eventType) {
        this.eventType = eventType;
    }

    public void setId(String id) {
        this.id = id;
    }  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt die Event Grid-Ausgabebindungsdaten in der Datei *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Der folgende JavaScript-Code erstellt ein einzelnes Ereignis:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Der folgende JavaScript-Code erstellt mehrere Ereignisse:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Im folgenden Beispiel wird veranschaulicht, wie Sie eine Funktion so konfigurieren, dass eine Event Grid-Ereignismeldung ausgegeben wird. Der Abschnitt, in dem `type` auf `eventGrid` festgelegt ist, konfiguriert die Werte, die zum Einrichten einer Event Grid-Ausgabebindung erforderlich sind.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

Verwenden Sie in Ihrer Funktion `Push-OutputBinding`, um ein Ereignis über die Event Grid-Ausgabebindung an ein benutzerdefiniertes Thema zu senden.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt eine Triggerbindung in einer Datei *function.json* sowie eine [Python-Funktion](functions-reference-python.md), die die Bindung verwendet. Anschließend sendet sie ein Ereignis an das benutzerdefinierte Thema, wie durch `topicEndpointUri` angegeben.

Bindungsdaten in der Datei *function.json*:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Hier sehen Sie das Python-Beispiel zum Senden eines Ereignisses an ein benutzerdefiniertes Thema durch Festlegen von `EventGridOutputEvent`:

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie für [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs).

Der Konstruktor des Attributs nimmt den Namen einer App-Einstellung an, die den Namen des benutzerdefinierten Themas enthält, sowie den Namen einer App-Einstellung, die den Themenschlüssel enthält. Weitere Informationen zu diesen Einstellungen finden Sie unter [Ausgabe: Konfiguration](#configuration). Dieses Beispiel zeigt ein Attribut `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Beispiel](#example).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie für Java-Klassen das [EventGridAttribute](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/EventGridOutput.java)-Attribut.

Der Konstruktor des Attributs nimmt den Namen einer App-Einstellung an, die den Namen des benutzerdefinierten Themas enthält, sowie den Namen einer App-Einstellung, die den Themenschlüssel enthält. Weitere Informationen zu diesen Einstellungen finden Sie unter [Ausgabe: Konfiguration](#configuration). Dieses Beispiel zeigt ein Attribut `EventGridOutput`:

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<String> outputEvent, final ExecutionContext context) {
            ...
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `EventGrid` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf „eventGrid“ festgelegt werden. |
|**direction** | – | Muss auf „out“ festgelegt werden. Dieser Parameter wird automatisch festgelegt, wenn Sie die Bindung im Azure Portal erstellen. |
|**name** | – | Der Variablenname, der in Funktionscode verwendet wird, der das Ereignis darstellt. |
|**topicEndpointUri** |**TopicEndpointUri** | Der Name einer App-Einstellung, die den URI für das benutzerdefinierte Thema enthält, z. B. `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Der Name einer App-Einstellung, die einen Zugriffsschlüssel für das benutzerdefinierte Thema enthält. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie den Wert der Konfigurationseigenschaft `TopicEndpointUri` auf den Namen einer App-Einstellung festlegen, die den URI des benutzerdefinierten Themas enthält. Geben Sie den URI des benutzerdefinierten Themas nicht direkt in dieser Eigenschaft an.

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

Senden Sie Nachrichten mithilfe eines Methodenparameters wie `out EventGridEvent paramName`. Um mehrere Nachrichten zu schreiben, können Sie `ICollector<EventGridEvent>` oder `IAsyncCollector<EventGridEvent>` anstelle von `out EventGridEvent` verwenden.

### <a name="additional-types"></a>Zusätzliche Typen 
Apps, die mindestens Version 3.0.0 der Event Grid-Erweiterung nutzen, verwenden den Typ `EventGridEvent` aus dem Namespace [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent). Darüber hinaus können Sie eine Bindung an den Typ `CloudEvent` aus dem Namespace [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) erstellen.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Senden Sie Nachrichten mithilfe eines Methodenparameters wie `out EventGridEvent paramName`. In C#-Skripts ist `paramName` der Wert, der in der Eigenschaft `name` von *function.json* angegeben ist. Um mehrere Nachrichten zu schreiben, können Sie `ICollector<EventGridEvent>` oder `IAsyncCollector<EventGridEvent>` anstelle von `out EventGridEvent` verwenden.

### <a name="additional-types"></a>Zusätzliche Typen 
Apps, die mindestens Version 3.0.0 der Event Grid-Erweiterung nutzen, verwenden den Typ `EventGridEvent` aus dem Namespace [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent). Darüber hinaus können Sie eine Bindung an den Typ `CloudEvent` aus dem Namespace [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) erstellen.

# <a name="java"></a>[Java](#tab/java)

Senden Sie einzelne Nachrichten durch Aufrufen eines Methodenparameters wie `out EventGridOutput paramName`, und schreiben Sie mehrere Nachrichten mit `ICollector<EventGridOutput>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Greifen Sie mithilfe von `context.bindings.<name>` auf das Ausgabeereignis zu, wobei `<name>` der in der Eigenschaft `name` von *function.json* angegebene Wert ist.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Greifen Sie mithilfe des Cmdlets `Push-OutputBinding` auf das Ausgabeereignis zu, um ein Ereignis an die Event Grid-Ausgabebindung zu senden.

# <a name="python"></a>[Python](#tab/python)

Es gibt zwei Optionen für das Ausgeben einer Event Hub-Nachricht aus einer Funktion:
- **Rückgabewert**: Legen Sie die Eigenschaft `name` in *function.json* auf `$return` fest. Mit dieser Konfiguration wird der Rückgabewert der Funktion als EventGrid-Nachricht beibehalten.
- **Imperativ**: Übergeben Sie einen Wert an die [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none)-Methode des Parameters, der als [Out](/python/api/azure-functions/azure.functions.out)-Typ deklariert ist. Der an `set` übergebene Wert wird als EventGrid-Nachricht beibehalten.

---

## <a name="next-steps"></a>Nächste Schritte

* [Versenden eines Event Grid-Ereignisses](./functions-bindings-event-grid-trigger.md)
