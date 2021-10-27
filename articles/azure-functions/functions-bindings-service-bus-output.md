---
title: Azure Service Bus-Ausgabebindungen für Azure Functions
description: Erfahren Sie, wie Sie Azure Service Bus-Nachrichten aus Azure Functions senden.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 0df83e6e335599b4942d53962add9083a2ecce12
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130138278"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus-Ausgabebindung für Azure Functions

Verwendet Azure Service Bus-Ausgabebindung zum Senden von Warteschlangen- oder Themanachrichten.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](functions-bindings-service-bus.md).

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Service Bus-Warteschlangennachricht sendet:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Service Bus-Ausgabebindung in einer Datei vom Typ *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion verwendet einen Timertrigger, um alle 15 Sekunden eine Warteschlangennachricht zu senden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Der folgende C#-Skriptcode erstellt eine einzelne Nachricht:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Der folgende C#-Skriptcode erstellt mehrere Nachrichten:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

Das folgende Beispiel zeigt eine Java-Funktion, die eine Nachricht an die Service Bus-Warteschlange `myqueue` sendet, wenn sie durch eine HTTP-Anforderung ausgelöst wird.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Verwenden Sie die `@QueueOutput`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Funktionsparameter, deren Wert in eine Service Bus-Warteschlange geschrieben wird.  Der Parametertyp sollte `OutputBinding<T>` lauten, wobei „T“ für einen beliebigen nativen Java-Typ eines POJO steht.

Java-Funktionen können auch in ein Service Bus-Thema schreiben. Im folgenden Beispiel wird die `@ServiceBusTopicOutput`-Anmerkung verwendet, um die Konfiguration für die Ausgabebindung zu beschreiben. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Service Bus-Ausgabebindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion verwendet einen Timertrigger, um alle 15 Sekunden eine Warteschlangennachricht zu senden.

Bindungsdaten in der Datei *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Der folgende JavaScript-Skriptcode erstellt eine einzelne Nachricht:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Der folgende JavaScript-Skriptcode mehrere Nachrichten:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende Beispiel zeigt eine Service Bus-Ausgabebindung in einer Datei *function.json* sowie eine [PowerShell-Funktion](functions-reference-powershell.md), die die Bindung verwendet. 

Bindungsdaten in der Datei *function.json*:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Hier ist das PowerShell-Skript, das eine Nachricht als Ausgabe der Funktion erstellt.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt, wie Sie Ausgaben in eine Service Bus-Warteschlange in Python schreiben.

Eine Service Bus-Bindungsdefinition ist in *function.json* definiert, wobei *type* auf `serviceBus` festgelegt ist.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

In *_\_init_\_.py* können Sie eine Nachricht als Ausgabe in die Warteschlange schreiben, indem Sie einen Wert an die `set`-Methode übergeben.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie die [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Der Konstruktor des Attributs nimmt den Namen der Warteschlange oder des Themas und des Abonnements an. Sie können auch die Zugriffsrechte der Verbindung angeben. Wie Sie die Einstellung für die Zugriffsrechte auswählen, wird im Abschnitt [Ausgabe: Konfiguration](#configuration) erläutert. Das folgende Beispiel zeigt, wie das Attribut auf den Rückgabewert der Funktion angewendet wird:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Sie können die Eigenschaft `Connection` festlegen, um den Namen einer App-Einstellung anzugeben, die die zu verwendende Service Bus-Verbindungszeichenfolge enthält, wie im folgenden Beispiel gezeigt:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabe – Beispiel](#example).

Mit dem Attribut `ServiceBusAccount` können Sie das zu verwendende Service Bus-Konto auf Klassen-, Methoden- oder Parameterebene angeben.  Weitere Informationen finden Sie unter [Trigger: Attribute](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Die Anmerkungen `ServiceBusQueueOutput` und `ServiceBusTopicOutput` sind zum Schreiben einer Nachricht als Funktionsausgabe verfügbar. Der mit diesen Anmerkungen ergänzte Parameter muss als `OutputBinding<T>` deklariert werden, wobei `T` der Typ ist, der dem Typ der Nachricht entspricht.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribute werden von PowerShell nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `ServiceBus` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** | – | Muss auf „serviceBus“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen.|
|**direction** | – | Muss auf „out“ festgelegt werden. Diese Eigenschaft wird automatisch festgelegt, wenn Sie den Trigger im Azure Portal erstellen. |
|**name** | – | Der Name der Variablen, die die Warteschlangen- oder Themanachricht im Funktionscode darstellt. Legen Sie diesen Wert auf „$return“ fest, um auf den Rückgabewert der Funktion zu verweisen. |
|**queueName**|**QueueName**|Name der Warteschlange.  Legen Sie diesen nur fest, wenn Warteschlangennachrichten gesendet werden (nicht für ein Thema).
|**topicName**|**TopicName**|Name des Themas. Legen Sie diesen nur fest, wenn Themanachrichten gesendet werden (nicht für eine Warteschlange).|
|**connection**|**Connection**|Der Name einer App-Einstellung oder -Einstellungssammlung, die angibt, wie eine Verbindung mit Service Bus hergestellt wird Siehe [Verbindungen](#connections).|
|**accessRights** (nur v1)|**zugreifen**|Zugriffsberechtigungen für die Verbindungszeichenfolge. Verfügbare Werte sind `manage` und `listen`. Die Standardeinstellung ist `manage`, d.h. heißt, dass die `connection` die Berechtigung **Manage** hat. Wenn Sie eine Verbindungszeichenfolge verwenden, die nicht über die Berechtigung **Manage** verfügt, legen Sie `accessRights` auf „listen“ fest. Andernfalls versucht die Functions-Runtime ggf. erfolglos Vorgänge auszuführen, die Verwaltungsrechte erfordern. In Version 2.x und höheren Versionen von Azure Functions ist diese Eigenschaft nicht verfügbar, da die aktuelle Version des Service Bus SDK Verwaltungsvorgänge nicht unterstützt.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-service-bus-connections](../../includes/functions-service-bus-connections.md)]

## <a name="usage"></a>Verwendung

In Azure Functions 1.x erstellt die Runtime die Warteschlange, falls sie nicht vorhanden ist und Sie `accessRights` auf `manage` festgelegt haben. In Functions-Version 2.x und höheren Versionen muss die Warteschlange oder das Thema bereits vorhanden sein. Wenn Sie eine nicht vorhandene Warteschlange oder ein nicht vorhandenes Thema angeben, tritt bei der Funktion ein Fehler auf. 

# <a name="c"></a>[C#](#tab/csharp)

Verwenden Sie die folgenden Parametertypen für die Ausgabebindung:

* `out T paramName` - `T` kann jeder JSON-serialisierbare Typ sein. Wenn der Parameterwert NULL ist, wenn die Funktion beendet wird, erstellt Functions die Nachricht mit einem NULL-Objekt.
* `out string`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `out byte[]`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `out BrokeredMessage`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht (für Functions 1.x).
* `out Message`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht (für Functions 2.x und höher).
* `ICollector<T>` oder `IAsyncCollector<T>` (für asynchrone Methoden): zum Erstellen mehrerer Nachrichten. Beim Aufrufen der `Add` -Methode wird eine Nachricht erstellt.

Beim Arbeiten mit C# Funktionen:

* Asynchrone Funktionen benötigen anstelle eines `out`-Parameters einen Rückgabewert oder `IAsyncCollector`.

* Um auf die Sitzungs-ID zuzugreifen, erstellen Sie eine Bindung an einen [`Message`](/dotnet/api/microsoft.azure.servicebus.message)-Typ und verwenden die Eigenschaft `sessionId`.

### <a name="additional-types"></a>Zusätzliche Typen 

Apps, die mindestens Version 5.0.0 der Service Bus-Erweiterung nutzen, verwenden den `ServiceBusMessage`-Typ in [Azure.Messaging.Service Bus](/dotnet/api/azure.messaging.servicebus.servicebusmessage) anstelle des Typs im Namespace [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message). In dieser Version wird Unterstützung des Legacytyps `Message` zugunsten der folgenden Typen aufgegeben:

- [ServiceBusMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessage)

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Verwenden Sie die folgenden Parametertypen für die Ausgabebindung:

* `out T paramName` - `T` kann jeder JSON-serialisierbare Typ sein. Wenn der Parameterwert NULL ist, wenn die Funktion beendet wird, erstellt Functions die Nachricht mit einem NULL-Objekt.
* `out string`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `out byte[]`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht.
* `out BrokeredMessage`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht (für Functions 1.x).
* `out Message`: Wenn der Parameterwert NULL ist, sobald die Funktion beendet wird, erstellt Functions keine Nachricht (für Functions 2.x und höher).
* `ICollector<T>` oder `IAsyncCollector<T>`: Zum Erstellen mehrerer Nachrichten. Beim Aufrufen der `Add` -Methode wird eine Nachricht erstellt.

Beim Arbeiten mit C# Funktionen:

* Asynchrone Funktionen benötigen anstelle eines `out`-Parameters einen Rückgabewert oder `IAsyncCollector`.

* Um auf die Sitzungs-ID zuzugreifen, erstellen Sie eine Bindung an einen [`Message`](/dotnet/api/microsoft.azure.servicebus.message)-Typ und verwenden die Eigenschaft `sessionId`.

### <a name="additional-types"></a>Zusätzliche Typen 
Apps, die mindestens Version 5.0.0 der Service Bus-Erweiterung nutzen, verwenden den Typ `ServiceBusMessage` in [Azure.Messaging.Service Bus](/dotnet/api/azure.messaging.servicebus.servicebusmessage) anstelle des Typs im Namespace [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message). In dieser Version wird Unterstützung des Legacytyps `Message` zugunsten der folgenden Typen aufgegeben:

- [ServiceBusMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessage)

# <a name="java"></a>[Java](#tab/java)

Verwenden Sie das [Azure Service Bus SDK](../service-bus-messaging/index.yml) anstelle der integrierten Ausgabebindung.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Greifen Sie auf die Warteschlange oder das Thema mithilfe von `context.bindings.<name from function.json>` zu. Sie können `context.binding.<name>` eine Zeichenfolge, ein Bytearray oder ein JavaScript-Objekt (deserialisiert in JSON) zuweisen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Die Ausgabe an den Service Bus ist über das `Push-OutputBinding`-Cmdlet verfügbar. Dort übergeben Sie Argumente, die dem Namen entsprechen, der durch den Namensparameter der Bindung in der Datei *function.json* festgelegt wird.

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie das [Azure Service Bus SDK](../service-bus-messaging/index.yml) anstelle der integrierten Ausgabebindung.

---

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| Service Bus | [Service Bus-Fehlercodes](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus-Grenzwerte](../service-bus-messaging/service-bus-quotas.md) |

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Funktion, wenn eine Warteschlangen- oder Themennachricht von Service Bus empfangen wird (Trigger)](./functions-bindings-service-bus-trigger.md)
