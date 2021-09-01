---
title: Bindungen für Durable Functions – Azure
description: Erfahren Sie, wie Sie Trigger und Bindungen für die Durable Functions-Erweiterung für Azure Functions verwenden.
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: azfuncdf
ms.openlocfilehash: 097527dbbf4363365e609a1f5aac1d851eb5dc60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355630"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindungen für Durable Functions (Azure Functions)

Mit der Erweiterung [Durable Functions](durable-functions-overview.md) (Langlebige Funktionen) werden drei neue Triggerbindungen eingeführt, mit denen die Ausführung von Orchestrator-, Entitäts- und Aktivitätsfunktionen gesteuert wird. Außerdem wird eine Ausgabebindung eingeführt, die als Client für die Laufzeit von Durable Functions dient.

## <a name="orchestration-trigger"></a>Orchestrierungstrigger

Mit einem Orchestrierungstrigger können Sie [langlebige Orchestratorfunktionen](durable-functions-types-features-overview.md#orchestrator-functions) erstellen. Dieser Trigger wird ausgeführt, wenn eine neue Orchestrierungsinstanz geplant ist und eine vorhandene Orchestrierungsinstanz ein Ereignis empfängt. Beispiele für Ereignisse, die Orchestratorfunktionen auslösen können, sind dauerhafte Timerablauf, Aktivitätsfunktionsantworten und Ereignisse, die von externen Clients ausgelöst werden.

Wenn Sie Funktionen in .NET erstellen, wird der Orchestrierungstrigger mit dem [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET-Attribut konfiguriert.

Wenn Sie Orchestratorfunktionen in Skriptsprachen schreiben (z. B. JavaScript-, Python- oder PowerShell-Skripts), wird der Orchestrierungstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` ist der Name der Orchestrierung, den Clients verwenden müssen, wenn sie neue Instanzen dieser Orchestratorfunktion starten möchten. Diese Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird der Name der Funktion verwendet.

Intern wird der konfigurierte permanente Speicher von dieser Triggerbindung nach neuen Orchestrierungsereignissen wie Orchestrierungsstartereignissen, dauerhaften Ablaufereignissen für Timer, Aktivitätsfunktionsantwortereignissen und externen Ereignissen, die von anderen Funktionen ausgelöst werden, abruft.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Orchestrierungstrigger angegeben:

* **Single-Threading**: Ein einzelner Verteilerthread wird für die gesamte Ausführung von Orchestratorfunktionen auf einer einzelnen Hostinstanz verwendet. Daher sollten Sie unbedingt sicherstellen, dass der Orchestratorfunktionscode effizient ist und keine E/A-Vorgänge durchführt. Außerdem sollten Sie sicherstellen, dass dieser Thread nur dann asynchrone Arbeitsschritte ausführt, wenn auf Aufgabentypen gewartet wird, die sich auf Durable Functions beziehen.
* **Behandlung von nicht verarbeitbaren Nachrichten**: Orchestrierungstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Orchestrierungstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Rückgabewerte werden in JSON-Code serialisiert und in der Tabelle mit dem Orchestrierungsverlauf im Azure-Tabellenspeicher beibehalten. Diese Rückgabewerte können von der Orchestrierungsclientbindung abgefragt werden, die weiter unten beschrieben wird.

> [!WARNING]
> Für Orchestratorfunktionen sollten niemals andere Eingabe- oder Ausgabebindungen als die Orchestrierungstriggerbindung verwendet werden. Wenn dies nicht beachtet wird, können sich unter Umständen Probleme mit der Erweiterung für langlebige Aufgaben ergeben, da sich diese Bindungen ggf. nicht an die Regeln für Single-Threading und E/A-Abläufe halten. Wenn Sie andere Bindungen verwenden möchten, fügen Sie diese einer Aktivitätsfunktion hinzu, die von der Orchestratorfunktion aufgerufen wird. Weitere Informationen zu Codeeinschränkungen für Orchestratorfunktionen finden Sie im Thema [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md).

> [!WARNING]
> JavaScript- und Python-Orchestratorfunktionen sollten nie als `async` deklariert werden.

### <a name="trigger-usage"></a>Triggerverwendung

Die Orchestrierungstriggerbindung unterstützt sowohl Ein- als auch Ausgaben. Hier sind einige Dinge aufgeführt, die Sie in Bezug auf die Eingabe- und Ausgabeverarbeitung wissen sollten:

* **Eingaben**: Orchestrierungstrigger können mit Eingaben aufgerufen werden, auf die über das Kontexteingabeobjekt zugegriffen wird. Alle Eingaben müssen JSON-serialisierbar sein.
* **Ausgaben**: Orchestrierungstrigger unterstützen sowohl Ausgabewerte als auch Eingaben. Der Rückgabewert der Funktion wird verwendet, um den Ausgabewert zuzuweisen, und er muss per JSON serialisierbar sein.

### <a name="trigger-sample"></a>Triggerbeispiel

Der folgende Beispielcode zeigt, wie eine sehr einfache Orchestratorfunktion vom Typ „Hello World“ aussehen könnte:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    // ... do some work ...
    return $"Hello {name}!";
}
```

> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    // ... do some work ...
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Die `durable-functions` Bibliothek übernimmt den Aufruf der `context.done`-Methode, wenn die Generatorfunktion beendet wird.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    # Do some work
    return f"Hello {name}!"

main = df.Orchestrator.create(orchestrator_function)
```

---

Die meisten Orchestratorfunktionen rufen Aktivitätsfunktionen auf. In diesem „Hello World“-Beispiel wird veranschaulicht, wie eine Aktivitätsfunktion aufgerufen wird:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Der vorherige Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    result = yield context.call_activity('SayHello', name)
    return result

main = df.Orchestrator.create(orchestrator_function)
```

---

## <a name="activity-trigger"></a>Aktivitätstrigger

Mit dem Aktivitätstrigger können Sie Funktionen erstellen, die von Orchestratorfunktionen aufgerufen werden, die als [Aktivitätsfunktionen](durable-functions-types-features-overview.md#activity-functions) bezeichnet werden.

Wenn Sie Funktionen in .NET erstellen, wird der Aktivitätstrigger mit dem .NET-Attribut [ActivityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.activitytriggerattribute) konfiguriert.

Wenn Sie JavaScript, Python oder PowerShell verwenden, wird der Aktivitätstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` ist der Name der Aktivität. Dieser Wert ist der Name, der von Orchestratorfunktionen verwendet wird, um diese Aktivitätsfunktion aufzurufen. Diese Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird der Name der Funktion verwendet.

Intern werden mit dieser Triggerbindung neue Aktivitätsausführungsereignisse vom konfigurierten permanenten Speicher abgefragt.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Aktivitätstrigger aufgeführt:

* **Threading**: Im Gegensatz zum Orchestrierungstrigger gelten für Aktivitätstrigger keine Einschränkungen in Bezug auf das Threading oder den E/A-Ablauf. Sie können wie reguläre Funktionen behandelt werden.
* **Behandlung von nicht verarbeitbaren Nachrichten**: Aktivitätstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Aktivitätstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Rückgabewerte werden in JSON-Code serialisiert und im konfigurierten Orchestrierungsverlauf beibehalten.

### <a name="trigger-usage"></a>Triggerverwendung

Die Aktivitätstriggerbindung unterstützt sowohl Ein- als auch Ausgaben – genau wie der Orchestrierungstrigger. Hier sind einige Dinge aufgeführt, die Sie in Bezug auf die Eingabe- und Ausgabeverarbeitung wissen sollten:

* **Eingaben**: Aktivitätstrigger können mit Eingaben aus einer Orchestratorfunktion aufgerufen werden. Alle Eingaben müssen JSON-serialisierbar sein.
* **Ausgaben**: Aktivitätsfunktionen unterstützen sowohl Ausgabewerte als auch Eingaben. Der Rückgabewert der Funktion wird verwendet, um den Ausgabewert zuzuweisen, und er muss per JSON serialisierbar sein.
* **Metadaten**: .NET-Aktivitätsfunktionen können an einen `string instanceId`-Parameter gebunden werden, um die Instanz-ID der aufrufenden Orchestrierung abzurufen.

### <a name="trigger-sample"></a>Triggerbeispiel

Der folgende Beispielcode zeigt, wie eine sehr einfache `SayHello` Aktivitätsfunktion aussehen könnte:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Der Standardparametertyp für die .NET-Bindung `ActivityTriggerAttribute` ist [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext) (oder [DurableActivityContext](/dotnet/api/microsoft.azure.webjobs.durableactivitycontext?view=azure-dotnet-legacy&preserve-view=true) für Durable Functions v1). .NET-Aktivitätstrigger unterstützen aber auch die direkte Bindung an JSON-serialisierbare Typen (z. B. primitive Typen). Die gleiche Funktion kann daher wie folgt vereinfacht werden:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript-Bindungen können auch als zusätzliche Parameter übergeben werden. Die gleiche Funktion kann daher wie folgt vereinfacht werden:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
def main(name: str) -> str:
    return f"Hello {name}!"
```

---

### <a name="using-input-and-output-bindings"></a>Verwenden von Eingabe- und Ausgabebindungen

Sie können neben der Aktivitätstriggerbindung auch reguläre Eingabe- und Ausgabebindungen verwenden. Beispielsweise können Sie die Eingabe in die Aktivitätsbindung verwenden und eine Nachricht mithilfe der EventHub-Ausgabebindung an einen EventHub senden:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestrierungsclient

Mit der Bindung des Orchestrierungsclients können Sie Funktionen schreiben, die mit Orchestratorfunktionen interagieren. Diese Funktionen werden oft als [Client-Funktionen](durable-functions-types-features-overview.md#client-functions) bezeichnet. Beispielsweise können Sie für Orchestrierungsinstanzen folgende Aktionen durchführen:

* Starten
* Abfragen des Status
* Beenden
* Senden von Ereignissen an die Instanzen während der Ausführung
* Löschen des Instanzverlaufs

Wenn Sie .NET verwenden, können Sie eine Bindung an den Orchestrierungsclient mithilfe des Attributs [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) ([OrchestrationClientAttribute](/dotnet/api/microsoft.azure.webjobs.orchestrationclientattribute?view=azure-dotnet-legacy&preserve-view=true) in Durable Functions v1.x) erstellen.

Wenn Sie Skriptsprachen wie z. B. JavaScript, Python oder PowerShell verwenden, wird der dauerhafte Clienttrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`: Wird in Szenarien verwendet, in denen mehrere Funktionen-Apps dasselbe Speicherkonto nutzen, aber voneinander isoliert werden müssen. Wenn Sie hier nichts angeben, wird der Standardwert aus `host.json` verwendet. Dieser Wert muss mit dem Wert übereinstimmen, der von den Zielorchestratorfunktionen verwendet wird.
* `connectionName`: Der Name einer App-Einstellung, die eine Speicherkonto-Verbindungszeichenfolge enthält. Das Speicherkonto, für das diese Verbindungszeichenfolge steht, muss dem Speicherkonto entsprechen, das von den Zielorchestratorfunktionen verwendet wird. Wenn nichts angegeben ist, wird die Speicherkonto-Standardverbindungszeichenfolge für die Funktionen-App verwendet.

> [!NOTE]
> In den meisten Fällen ist es empfehlenswert, diese Eigenschaften wegzulassen und das Standardverhalten zu nutzen.

### <a name="client-usage"></a>Clientnutzung

In .NET-Funktionen richten Sie normalerweise eine Bindung an [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient) ([DurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclient?view=azure-dotnet-legacy&preserve-view=true) in Durable Functions v1.x) ein, sodass Sie Vollzugriff auf alle Orchestrierungsclient-APIs erhalten, die von Durable Functions unterstützt werden. In anderen Sprachen müssen Sie das sprachspezifische SDK verwenden, um Zugriff auf ein Clientobjekt zu erhalten.

Hier ist ein Beispiel für eine per Warteschlange ausgelöste Funktion angegeben, mit der eine „HelloWorld“-Orchestrierung gestartet wird.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**__init__.py**
```python
import json
import azure.functions as func
import azure.durable_functions as df

async def main(msg: func.QueueMessage, starter: str) -> None:
    client = df.DurableOrchestrationClient(starter)
    payload = msg.get_body().decode('utf-8')
    instance_id = await client.start_new("HelloWorld", client_input=payload)
```

---

Weitere Informationen zum Starten von Instanzen finden Sie unter [Instanzverwaltung](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Entitätstrigger

Entitätstrigger ermöglichen es Ihnen, [Entitätsfunktionen](durable-functions-entities.md) zu erstellen. Dieser Trigger unterstützt die Verarbeitung von Ereignissen für eine bestimmte Entitätsinstanz.

> [!NOTE]
> Entitätstrigger sind ab Durable Functions 2.x verfügbar.

Intern werden mit dieser Triggerbindung neue Entitätsvorgänge, die ausgeführt werden müssen, vom konfigurierten permanenten Speicher abgefragt.

Wenn Sie Funktionen in .NET erstellen, wird der Entitätstrigger mit dem .NET-Attribut [EntityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.entitytriggerattribute) konfiguriert.

Wenn Sie JavaScript, Python oder PowerShell verwenden, wird der Entitätstrigger mit dem folgenden JSON-Objekt im `bindings`-Array der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "entityName": "<Optional - name of the entity>",
    "type": "entityTrigger",
    "direction": "in"
}
```

Standardmäßig ist der Name einer Entität der Name der Funktion.

### <a name="trigger-behavior"></a>Triggerverhalten

Hier sind einige Hinweise zum Entitätstrigger aufgeführt:

* **Singlethread**: Ein einzelner Verteilerthread wird zum Verarbeiten von Vorgängen für eine bestimmte Entität verwendet. Wenn mehrere Nachrichten gleichzeitig an eine einzelne Entität gesendet werden, werden die Vorgänge einzeln verarbeitet.
* **Behandlung von nicht verarbeitbaren Nachrichten**: Entitätstrigger weisen keine Unterstützung für nicht verarbeitbare Nachrichten auf.
* **Sichtbarkeit von Nachrichten**: Nachrichten des Entitätstriggers werden aus der Warteschlange entfernt und bleiben für eine konfigurierbare Dauer unsichtbar. Die Sichtbarkeit dieser Nachrichten wird automatisch erneuert, solange die Funktionen-App ausgeführt wird und fehlerfrei ist.
* **Rückgabewerte**: Entitätsfunktionen unterstützen keine Rückgabewerte. Es gibt bestimmte APIs, die zum Speichern des Zustands oder zum Zurückgeben von Werten an Orchestrierungen verwendet werden können.

Alle Zustandsänderungen, die während der Ausführung an einer Entität vorgenommen werden, werden nach Abschluss der Ausführung automatisch persistent gespeichert.

Weitere Informationen und Beispiele zum Definieren und Interagieren mit Entitätstriggern finden Sie in der Dokumentation zu [dauerhaften Entitäten](durable-functions-entities.md).

## <a name="entity-client"></a>Entitätsclient

Die Entitätsclientbindung ermöglicht es Ihnen, [Entitätsfunktionen](#entity-trigger) asynchron auszulösen. Diese Funktionen werden manchmal auch als [Clientfunktionen](durable-functions-types-features-overview.md#client-functions)bezeichnet.

Wenn Sie vorkompilierte .NET-Funktionen verwenden, können Sie eine Bindung mit dem Client der Entität erstellen, indem Sie das .NET-Attribut [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) verwenden.

> [!NOTE]
> `[DurableClientAttribute]` kann auch verwendet werden, um eine Bindung mit dem [Orchestrierungsclient](#orchestration-client) herzustellen.

Wenn Sie Skriptsprachen zum Entwickeln verwenden (wie z. B. C#, JavaScript oder Python), wird der Entitätstrigger vom folgenden JSON-Objekt im `bindings`-Array in der Datei *function.json* definiert:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`: Wird in Szenarien verwendet, in denen mehrere Funktionen-Apps dasselbe Speicherkonto nutzen, aber voneinander isoliert werden müssen. Wenn Sie hier nichts angeben, wird der Standardwert aus `host.json` verwendet. Dieser Wert muss mit dem Wert übereinstimmen, der von den Zielentitätsfunktionen verwendet wird.
* `connectionName`: Der Name einer App-Einstellung, die eine Speicherkonto-Verbindungszeichenfolge enthält. Das Speicherkonto, für das diese Verbindungszeichenfolge steht, muss dem Speicherkonto entsprechen, das von den Zielentitätsfunktionen verwendet wird. Wenn nichts angegeben ist, wird die Speicherkonto-Standardverbindungszeichenfolge für die Funktionen-App verwendet.

> [!NOTE]
> In den meisten Fällen ist es empfehlenswert, die optionalen Eigenschaften nicht zu verwenden und das Standardverhalten zu nutzen.

Weitere Informationen und Beispiele zur Interaktion mit Entitäten als Client finden Sie in der Dokumentation zu [Dauerhaften Entitäten](durable-functions-entities.md#access-entities).

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Einstellungen für „host.json“

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Integrierte in HTTP API-Referenz für Instanzverwaltung](durable-functions-http-api.md)
