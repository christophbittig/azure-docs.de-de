---
title: 'Dauerhafte Entitäten: Azure Functions'
description: Hier erfahren Sie, was dauerhafte Entitäten sind und wie sie in der Durable Functions-Erweiterung für Azure Functions verwendet werden.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: bdc5ba0c21bc55c04c8407e218fd09eecd726be2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603598"
---
# <a name="entity-functions"></a>Entitätsfunktionen

Entitätsfunktionen definieren Vorgänge zum Lesen und Aktualisieren kleinerer Zustandsteile, bekannt als *dauerhafte Entitäten*. Wie Orchestratorfunktionen verfügen auch Entitätsfunktionen über einen speziellen Triggertyp, und zwar den *Entitätstrigger*. Im Gegensatz zu Orchestratorfunktionen stellen Entitätsfunktionen den Zustand nicht über eine Ablaufsteuerung dar, sondern verwalten den Zustand einer Entität explizit.
Mit Entitäten können Anwendungen horizontal hochskaliert werden, indem die Arbeit auf zahlreiche Entitäten verteilt wird, die jeweils nur eine relativ geringe Größe haben.

> [!NOTE]
> Entitätsfunktionen und zugehörige Funktionen sind nur in [Durable Functions 2.0](durable-functions-versions.md#migrate-from-1x-to-2x) und höher verfügbar. Sie werden derzeit in .NET, JavaScript und Python unterstützt.

## <a name="general-concepts"></a>Allgemeine Konzepte

Entitäten verhalten sich ein wenig wie kleine Dienste, die mithilfe von Nachrichten kommunizieren. Jede Entität hat eine eindeutige Identität und einen internen Zustand (sofern vorhanden). Entitäten führen genau wie Dienste oder Objekte Vorgänge aus, wenn sie dazu aufgefordert werden. Bei der Ausführung eines Vorgangs wird ggf. der interne Zustand der Entität aktualisiert. Ein Vorgang kann auch externe Dienste aufrufen und auf eine Antwort warten. Entitäten kommunizieren mit anderen Entitäten, Orchestrierungen und Clients über Nachrichten, die implizit über zuverlässige Warteschlangen gesendet werden. 

Zur Vermeidung von Konflikten werden alle Vorgänge für eine einzelne Entität seriell (also nacheinander) ausgeführt.

> [!NOTE]
> Wenn eine Entität aufgerufen wird, werden die dazugehörigen Nutzdaten vollständig verarbeitet. Anschließend wird eine neue Ausführung geplant, die aktiviert wird, sobald weitere Eingaben erfolgen. Dies führt dazu, dass in Ihren Entitätsausführungsprotokollen möglicherweise nach den Aufrufen der einzelnen Entitäten eine zusätzliche Ausführung aufgeführt ist. Dabei handelt es sich um ein zu erwartendes Verhalten.

### <a name="entity-id"></a>Entitäts-ID
Der Zugriff auf Entitäten erfolgt über einen eindeutigen Bezeichner: die *Entitäts-ID*. Eine Entitäts-ID ist einfach ein Paar aus Zeichenfolgen, das eine Entitätsinstanz eindeutig identifiziert. Sie besteht aus folgenden Elementen:

* **Entitätsname**: Dies ist ein Name, mit dem der Typ der Entität angegeben wird. Ein Beispiel hierfür ist „Counter“ (Zähler). Dieser Name muss dem Namen der Entitätsfunktion entsprechen, von der die Entität implementiert wird. Die Groß-/Kleinschreibung wird dabei nicht beachtet.
* **Entitätsschlüssel**: Dies ist eine Zeichenfolge, mit der die Entität unter allen anderen Entitäten gleichen Namens eindeutig identifiziert wird. Ein Beispiel hierfür ist eine GUID.

Eine `Counter`-Entitätsfunktion kann beispielsweise verwendet werden, um den Punktestand in einem Onlinespiel vorzuhalten. Jede Instanz des Spiels verfügt über eine eindeutige Entitäts-ID, z. B. `@Counter@Game1`, `@Counter@Game2` usw. Bei allen Vorgängen, die auf eine bestimmte Entität ausgerichtet sind, muss eine Entitäts-ID als Parameter angegeben werden.

### <a name="entity-operations"></a>Entitätsvorgänge ###

Geben Sie Folgendes an, um einen Vorgang für eine Entität aufzurufen:

* **Entitäts-ID**: Dies ist die Zielentität.
* **Name des Vorgangs**: Dies ist eine Zeichenfolge, die den auszuführenden Vorgang angibt. Für die Entität `Counter` können beispielsweise die Vorgänge `add`, `get` oder `reset` unterstützt werden.
* **Vorgangseingabe**: Dies ist ein optionaler Eingabeparameter für den Vorgang. Der Vorgang „add“ akzeptiert beispielsweise eine ganzzahlige Menge als Eingabe.
* **Geplante Zeit**, wobei es sich um einen optionalen Parameter zum Angeben der Übermittlungszeit des Vorgangs handelt. Beispielsweise kann ein Vorgang zuverlässig für die mehrere Tage in der Zukunft liegende Ausführung geplant werden.

Vorgänge können einen Ergebniswert oder ein Fehlerergebnis zurückgeben, z. B. einen JavaScript-Fehler oder eine .NET-Ausnahme. Dieses Ergebnis bzw. dieser Fehler kann von Orchestrierungen berücksichtigt werden, die den Vorgang aufgerufen haben.

Entitätsvorgänge können auch den Zustand der Entität erstellen, lesen, aktualisieren und löschen. Der Zustand der Entität wird immer dauerhaft im Speicher aufbewahrt.

## <a name="define-entities"></a>Definieren von Entitäten

Zum Definieren von Entitäten stehen derzeit zwei unterschiedliche APIs zur Verfügung:

**Funktionsbasierte Syntax**: Hierbei werden Entitäten als Funktionen dargestellt und Vorgänge explizit von der Anwendung verteilt. Diese Syntax eignet sich gut für Entitäten mit einem einfachem Zustand, wenigen Vorgängen oder einem dynamischen Satz von Vorgängen, z. B. in Anwendungsframeworks. Die Nutzung dieser Syntax kann sich allerdings als aufwendig erweisen, da Typfehler zur Kompilierzeit nicht abgefangen werden.

**Klassenbasierte Syntax (nur .NET)** : Hierbei werden Entitäten und Vorgänge durch Klassen und Methoden dargestellt. Bei dieser Syntax ist der Code besser lesbar, und Vorgänge können typsicher aufgerufen werden. Da es sich bei der klassenbasierten Syntax um eine dünne Schicht oberhalb der funktionsbasierten Syntax handelt, können beide Varianten in derselben Anwendung verwendet werden.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Beispiel: Funktionsbasierte Syntax: C#

Der folgende Code ist ein Beispiel für eine einfache Entität vom Typ `Counter`, die als dauerhafte Funktion implementiert wird. Diese Funktion definiert die drei Vorgänge `add`, `reset` und `get`, die jeweils auf einem ganzzahligen Zustand basieren.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Weitere Informationen zur funktionsbasierten Syntax sowie zu deren Verwendung finden Sie unter [Funktionsbasierte Syntax](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Beispiel: Klassenbasierte Syntax – C#

Beim folgenden Beispiel handelt es sich um eine äquivalente Implementierung der Entität `Counter` unter Verwendung von Klassen und Methoden:

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Der Zustand dieser Entität ist ein Objekt vom Typ `Counter`. Dieses enthält ein Feld, in dem der aktuelle Wert des Zählers gespeichert wird. Um dieses Objekt dauerhaft im Speicher aufzubewahren, wird es von der Bibliothek [Json.NET](https://www.newtonsoft.com/json) serialisiert und deserialisiert. 

Weitere Informationen zur klassenbasierten Syntax sowie zu deren Verwendung finden Sie unter [Definieren von Entitätsklassen](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Beispiel: JavaScript-Entität

Dauerhafte Entitäten sind ab JavaScript-Version **1.3.0** des `durable-functions`-npm-Pakets verfügbar. Mit dem folgenden Code wird die Entität `Counter` als dauerhafte Funktion in JavaScript implementiert.

**Counter/function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Counter/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Beispiel: Python-Entität

Mit dem folgenden Code wird die Entität `Counter` als dauerhafte Funktion in Python implementiert.

**Counter/function.json**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__.py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Zugreifen auf Entitäten

Auf Entitäten kann mittels unidirektionaler oder bidirektionaler Kommunikation zugegriffen werden. Mit den folgenden Begriffen werden die beiden Kommunikationsarten unterschieden: 

* Beim **Aufrufen** einer Entität wird die bidirektionale Kommunikation (Roundtrip) verwendet. Sie senden eine Vorgangsmeldung als Nachricht an die Entität und warten dann auf die Antwortnachricht, bevor Sie fortfahren. Mit der Antwortnachricht kann ein Ergebniswert oder ein Fehlerergebnis zurückgegeben werden, z. B. ein JavaScript-Fehler oder eine .NET-Ausnahme. Das Ergebnis oder der Fehler wird dann vom Aufrufer berücksichtigt.
* Beim **Signalisieren** wird für eine Entität die unidirektionale Kommunikation (Fire and Forget) verwendet. Sie senden eine Vorgangsmeldung als Nachricht, warten aber nicht auf eine Antwort. Die Nachricht wird zwar garantiert übermittelt, aber der Absender weiß nicht, wann dies der Fall ist, und er kann auch keinen Ergebniswert und keine Fehler berücksichtigen.

Auf Entitäten kann innerhalb von Clientfunktionen, innerhalb von Orchestratorfunktionen oder innerhalb von Entitätsfunktionen zugegriffen werden. Nicht alle Kommunikationsarten werden von allen Kontexten unterstützt:

* Innerhalb von Clients können Sie für Entitäten die Signalisierung durchführen und den Entitätszustand lesen.
* Innerhalb von Orchestrierungen können Sie für Entitäten die Signalisierung durchführen und Entitäten aufrufen.
* Innerhalb von Entitäten können Sie für Entitäten die Signalisierung durchführen.

In den folgenden Beispielen werden diese unterschiedlichen Arten des Zugriffs auf Entitäten veranschaulicht.

### <a name="example-client-signals-an-entity"></a>Beispiel: Client führt Signalisierung für Entität durch

Wenn Sie über eine normale Azure-Funktion, die auch als Clientfunktion bezeichnet wird, auf Entitäten zugreifen möchten, verwenden Sie die [Entitätsclientbindung](durable-functions-bindings.md#entity-client). Im Beispiel unten wird eine per Warteschlange ausgelöste Funktion zur Signalisierung einer Entität unter Verwendung dieser Bindung veranschaulicht.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Der Einfachheit halber wird in den folgenden Beispielen die lose typisierte Syntax für den Entitätszugriff verwendet. Generell empfehlen wir Ihnen, aufgrund der besseren Typüberprüfung [über Schnittstellen auf Entitäten zuzugreifen](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces).

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

*Signalisierung* bedeutet, dass der Entitäts-API-Aufruf unidirektional und asynchron ist. Einer Clientfunktion ist nicht bekannt, wann der Vorgang durch die Entität verarbeitet wurde. Außerdem kann die Clientfunktion keine Ergebniswerte oder Ausnahmen berücksichtigen. 

### <a name="example-client-reads-an-entity-state"></a>Beispiel: Client liest einen Entitätszustand

Clientfunktionen können auch den Zustand einer Entität abfragen, wie im folgenden Beispiel gezeigt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func

async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    entity_state_result = await client.read_entity_state(entityId)
    entity_state = "No state found"
    if entity_state_result.entity_exists:
      entity_state = str(entity_state_result.entity_state)
    return func.HttpResponse(entity_state)
```

---

Entitätszustandsabfragen werden an den dauerhaften Nachverfolgungsspeicher gesendet und geben den zuletzt gespeicherten Zustand der Entität zurück. Dieser Zustand ist immer ein „committeter“ Zustand. Anders ausgedrückt: Hierbei handelt es sich niemals um einen temporären Übergangszustand während der Vorgangsausführung. Es kann aber sein, dass dieser Zustand im Vergleich zum In-Memory-Zustand der Entität veraltet ist. Der In-Memory-Zustand einer Entität kann nur von Orchestrierungen gelesen werden, wie im folgenden Abschnitt beschrieben.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Beispiel: Orchestrierung führt Signalisierung für Entität durch und ruft sie auf

Orchestratorfunktionen können über APIs für die [Orchestrierungstriggerbindung](durable-functions-bindings.md#orchestration-trigger) auf Entitäten zugreifen. Der unten angegebene Beispielcode zeigt eine Orchestratorfunktion mit Aufruf und Signalisierung einer Entität vom Typ `Counter`.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript unterstützt derzeit nicht die Signalisierung von Entitäten aus einem Orchestrator. Verwenden Sie stattdessen `callEntity`.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Nur Orchestrierungen können Entitäten aufrufen und eine Antwort erhalten. Dabei kann es sich entweder um einen Rückgabewert oder um eine Ausnahme handeln. Clientfunktionen, für die die [Clientbindung](durable-functions-bindings.md#entity-client) verwendet wird, können nur die Signalisierung für Entitäten durchführen.

> [!NOTE]
> Das Aufrufen einer Entität über eine Orchestratorfunktion ist mit dem Aufrufen einer [Aktivitätsfunktion](durable-functions-types-features-overview.md#activity-functions) über eine Orchestratorfunktion vergleichbar. Der Hauptunterschied besteht darin, dass es sich bei Entitätsfunktionen um dauerhafte Objekte mit einer Adresse (Entitäts-ID) handelt. Für Entitätsfunktionen wird das Angeben eines Vorgangsnamens unterstützt. Aktivitätsfunktionen sind dagegen zustandslos und verfügen nicht über ein Vorgangskonzept.

### <a name="example-entity-signals-an-entity"></a>Beispiel: Entität führt Signalisierung für eine Entität durch

Eine Entitätsfunktion kann während der Vorgangsausführung Signale an andere Entitäten und sogar an sich selbst senden.
Das obige Beispiel mit der Entität `Counter` kann beispielsweise so angepasst werden, dass ein entsprechendes Signal an eine Überwachungsentität gesendet wird, wenn der Zähler den Wert 100 erreicht.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python unterstützt aktuell Signale von Entität zu Entität noch nicht. Verwenden Sie stattdessen einen Orchestrator für die Entitätensignalisierung.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Koordination von Entitäten (derzeit nur für .NET)

Es kann vorkommen, dass Vorgänge über mehrere Entitäten hinweg koordiniert werden müssen. Eine Bankinganwendung kann unter Umständen beispielsweise Entitäten enthalten, die einzelne Bankkonten darstellen. Wenn Sie Geld von einem Konto auf ein anderes überweisen, müssen Sie sicherstellen, dass das Ausgangskonto über einen ausreichenden Betrag verfügt. Außerdem müssen Sie dafür sorgen, dass Aktualisierungen sowohl für das Ausgangs- als auch für das Zielkonto als einheitliche Transaktionen durchgeführt werden.

### <a name="example-transfer-funds-c"></a>Beispiel: Überweisung (C#)

Der folgende Beispielcode führt eine Überweisung zwischen zwei Kontoentitäten über eine Orchestratorfunktion durch. Für die Koordinierung von Entitätsaktualisierungen muss mithilfe der `LockAsync`-Methode ein _kritischer Abschnitt_ in der Orchestrierung erstellt werden.

> [!NOTE]
> Der Einfachheit halber wird in diesem Beispiel erneut die zuvor definierte Entität `Counter` verwendet. In einer echten Anwendung empfiehlt es sich, eine ausführlichere Entität vom Typ `BankAccount` zu definieren.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

In .NET gibt `LockAsync` das Element `IDisposable` zurück, mit dem der kritische Abschnitt beim Verwerfen beendet wird. Dieses Ergebnis vom Typ `IDisposable` kann zusammen mit einem Block vom Typ `using` verwendet werden, um eine syntaktische Darstellung des kritischen Abschnitts zu erhalten.

Im obigen Beispiel wurde mithilfe einer Orchestratorfunktion eine Überweisung von einer Quellentität zu einer Zielentität getätigt. Mit der `LockAsync`-Methode wurde sowohl die Ausgangskonto-Entität als auch die Zielkonto-Entität gesperrt. Diese Sperrung sorgt dafür, dass kein anderer Client den Zustand der Konten abfragen oder ändern kann, bis die Orchestrierungslogik den kritischen Abschnitt am Ende der `using`-Anweisung verlassen hat. Durch dieses Verhalten wird eine Überziehung des Ausgangskontos verhindert.

> [!NOTE] 
> Wenn eine Orchestrierung beendet wird – entweder normal oder mit einem Fehler –, werden alle kritischen Abschnitte, die noch aktiv sind, implizit beendet und alle Sperren aufgehoben.

### <a name="critical-section-behavior"></a>Verhalten des kritischen Abschnitts

Mit der `LockAsync`-Methode wird ein kritischer Abschnitt in einer Orchestrierung erstellt. Diese kritischen Abschnitte sorgen dafür, dass andere Orchestrierungen keine sich überschneidenden Änderungen an einem angegebenen Satz von Entitäten vornehmen können. Intern sendet die API `LockAsync` Sperrvorgänge an die Entitäten und wird wieder verfügbar, wenn sie von diesen Entitäten jeweils eine Antwortnachricht vom Typ „Sperre abgerufen“ erhält. „Sperren“ und „Entsperren“ sind integrierte Vorgänge, die von allen Entitäten unterstützt werden.

Für eine Entität im gesperrten Zustand können keine Vorgänge von anderen Clients ausgeführt werden. Dadurch wird sichergestellt, dass immer nur eine Orchestrierungsinstanz eine Entität sperren kann. Wenn ein Aufrufer versucht, einen Vorgang für eine Entität aufzurufen, die durch eine Orchestrierung gesperrt wurde, wird der Vorgang in einer Warteschlange für ausstehende Vorgänge platziert. Ausstehende Vorgänge werden erst verarbeitet, nachdem die Sperre dieser Orchestrierung aufgehoben wurde.

> [!NOTE] 
> Dieses Verhalten unterscheidet sich geringfügig von den Synchronisierungsprimitiven, die in den meisten Programmiersprachen zum Einsatz kommen, z. B. die `lock`-Anweisung in C#. Die `lock`-Anweisung muss in C# beispielsweise von allen Threads verwendet werden, um eine korrekte threadübergreifende Synchronisierung sicherzustellen. Entitäten müssen dagegen nicht von allen Aufrufern explizit gesperrt werden. Wenn ein Aufrufer eine Entität sperrt, werden alle anderen Vorgänge für diese Entität blockiert und in einer Warteschlange hinter dieser Sperre platziert.

Sperren für Entitäten sind dauerhaft und bleiben auch bestehen, wenn der ausführende Prozess recycelt wird. Sperren werden intern als Teil des dauerhaften Zustands einer Entität beibehalten.

Im Gegensatz zu Transaktionen wird für kritische Abschnitte bei Fehlern kein automatischer Änderungsrollback durchgeführt. Stattdessen müssen alle Fehlerbehandlungen, z. B. Rollback oder Wiederholung, explizit codiert werden, indem beispielsweise Fehler oder Ausnahmen abgefangen werden. Dies ist eine bewusste Entscheidung. Ein automatischer Rollback aller Auswirkungen einer Orchestrierung ist im Allgemeinen schwierig bis unmöglich, da Orchestrierungen unter Umständen Aktivitäten ausführen und externe Dienste aufrufen, für die kein Rollback möglich ist. Darüber hinaus kann es vorkommen, dass auch Rollbackversuche nicht erfolgreich sind, was eine weitere Fehlerbehandlung erforderlich macht.

### <a name="critical-section-rules"></a>Regeln für kritische Abschnitte

Im Gegensatz zu Sperrprimitiven auf niedriger Ebene der meisten Programmiersprachen verfügen kritische Abschnitte über einen *garantierten Deadlockschutz*. Zur Verhinderung von Deadlocks werden folgende Einschränkungen erzwungen: 

* Kritische Abschnitte können nicht geschachtelt werden.
* Für kritische Abschnitte können keine Unterorchestrierungen erstellt werden.
* Kritische Abschnitte können nur Entitäten aufrufen, die sie gesperrt haben.
* Kritische Abschnitte können nicht mit mehreren parallelen Aufrufen dieselbe Entität aufrufen.
* Kritische Abschnitte können die Signalisierung nur für Entitäten durchführen, die sie nicht gesperrt haben.

Jeglicher Verstoß gegen diese Regeln hat einen Laufzeitfehler, z. B. `LockingRulesViolationException` in .NET, mit einer Meldung mit Informationen zum jeweiligen Regelverstoß zur Folge.

## <a name="comparison-with-virtual-actors"></a>Vergleich mit virtuellen Akteuren

Viele Features von dauerhaften Entitäten wurden durch das [Actor Model](https://en.wikipedia.org/wiki/Actor_model) (Akteurmodell) inspiriert. Wenn Sie bereits mit Akteuren vertraut sind, kommen Ihnen wahrscheinlich viele der in diesem Artikel beschriebenen Konzepte bekannt vor. Dauerhafte Entitäten ähneln sehr stark [virtuellen Akteuren](https://research.microsoft.com/projects/orleans/) bzw. den „Grains“ des [Orleans-Projekts](http://dotnet.github.io/orleans/). Beispiel:

* Dauerhafte Entitäten sind über eine Entitäts-ID adressierbar.
* Vorgänge mit dauerhaften Entitäten werden seriell ausgeführt, immer einzeln, um Racebedingungen zu verhindern.
* Dauerhafte Entitäten werden implizit erstellt, wenn sie aufgerufen oder signalisiert werden.
* Wenn keine Vorgänge ausgeführt werden, werden dauerhafte Entitäten automatisch aus dem Arbeitsspeicher entladen.

Es gibt aber einige wichtige Unterschiede, die beachtet werden sollten:

* Bei dauerhaften Entitäten hat die Dauerhaftigkeit Vorrang vor der Latenz, sodass sie unter Umständen nicht für Anwendungen mit hohen Latenzanforderungen geeignet sind.
* Dauerhafte Entitäten verfügen nicht über integrierte Timeouts für Nachrichten. Bei Orleans gilt für alle Nachrichten ein konfigurierbares Timeout. Der Standardwert ist 30 Sekunden.
* Zwischen Entitäten gesendete Nachrichten werden zuverlässig und in Reihenfolge übermittelt. Bei Orleans wird eine zuverlässige oder geordnete Übermittlung für Inhalte unterstützt, die über Streams gesendet werden. Sie wird aber nicht für alle Nachrichten zwischen Grains garantiert.
* Anforderung/Antwort-Muster in Entitäten sind auf Orchestrierungen beschränkt. Innerhalb von Entitäten sind genau wie im ursprünglichen Akteurmodell (und im Gegensatz zu Grains bei Orleans) nur unidirektionale Nachrichten zulässig (Stichwort: Signalisierung). 
* Bei dauerhaften Entitäten treten keine Deadlocks auf. Bei Orleans können Deadlocks auftreten, die erst nach dem Nachrichtentimeout aufgelöst werden.
* Dauerhafte Entitäten können in Verbindung mit dauerhaften Orchestrierungen verwendet werden und unterstützen verteilte Sperrmechanismen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entwicklerhandbuch für dauerhafte Entitäten in .NET (Vorschauversion)](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Machen Sie sich mit Aufgabenhubs vertraut.](durable-functions-task-hubs.md)
