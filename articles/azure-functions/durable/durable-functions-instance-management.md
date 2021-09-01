---
title: Verwalten von Instanzen in Durable Functions – Azure
description: Es wird beschrieben, wie Instanzen in der Erweiterung Durable Functions für Azure Functions verwaltet werden.
author: cgillum
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 04c3e9f1a5c5a1a23a618f3274057a5e03a9f0e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356075"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Verwalten von Instanzen in Durable Functions in Azure

Orchestrierungen in Durable Functions sind zustandsbehaftete Funktionen mit langer Ausführungszeit, die mithilfe integrierter Verwaltungs-APIs gestartet, abgefragt und beendet werden können. Auch einige andere Instanzverwaltungs-APIs werden durch die [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) von Durable Functions verfügbar gemacht, zum Beispiel das Senden externer Ereignisse an Instanzen oder das Löschen des Instanzverlaufs. In diesem Artikel werden alle unterstützten Instanzverwaltungsvorgänge detailliert erläutert.

## <a name="start-instances"></a>Starten von Instanzen

Die Methoden `StartNewAsync` (.NET), `startNew` (JavaScript) oder `start_new` (Python) für die [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) starten eine neue Orchestrierungsinstanz. Intern schreibt diese Methode eine Meldung über den [Durable Functions-Speicheranbieter](durable-functions-storage-providers.md) und wird dann zurückgegeben. Diese Meldung löst asynchron die Ausführung der [Orchestrierungsfunktion](durable-functions-types-features-overview.md#orchestrator-functions) aus, deren Namen angegeben wurde.

Die Parameter zum Starten einer neuen Orchestrierungsinstanz lauten wie folgt:

* **Name**: Der Name der zu planenden Orchestratorfunktion.
* **Eingabe**: Alle JSON-serialisierbaren Daten, die als Eingabe an die Orchestratorfunktion übergeben werden sollen.
* **InstanceId**: (Optional) Die eindeutige ID der Instanz. Ohne Angabe dieses Parameters verwendet die Methode eine zufällige ID.

> [!TIP]
> Verwenden Sie nach Möglichkeit einen zufälligen Bezeichner für die Instanz-ID. Zufällige Instanz-IDs gewährleisten eine gleichmäßige Lastenverteilung, wenn Sie Orchestratorfunktionen über mehrere virtuelle Computer hinweg skalieren. Wenn die ID aus einer externen Quelle stammen muss oder bei der Implementierung des [Singleton-Orchestrator](durable-functions-singletons.md)-Musters sollten Sie nicht zufällige Instanz-IDs verwenden.

Der folgende Code ist eine Beispielfunktion, die eine neue Orchestrierungsinstanz startet:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Sofern nicht anders angegeben, verwenden die Beispiele auf dieser Seite den HTTP-Trigger mit der folgenden „function.json“-Datei.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Dieses Beispiel gilt für Durable Functions, Version 2.x. Verwenden Sie in Version 1.x `orchestrationClient` anstelle von `durableClient`.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>Sofern nicht anders angegeben, verwenden die Beispiele auf dieser Seite den HTTP-Trigger mit der folgenden „function.json“-Datei.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Dieses Beispiel gilt für Durable Functions, Version 2.x. Verwenden Sie in Version 1.x `orchestrationClient` anstelle von `durableClient`.

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können eine Instanz auch direkt starten, indem Sie den [`func durable start-new`-Befehl](../functions-core-tools-reference.md#func-durable-start-new) in den Core Tools verwenden, der die folgenden Parameter annimmt:

* **`function-name` (erforderlich)** : Der Name der zu startenden Funktion.
* **`input` (optional)** : Die Eingabe für die Funktion, entweder inline oder über eine JSON-Datei. Fügen Sie bei Dateien dem Pfad zur Datei mit `@` ein Präfix hinzu, z.B. `@path/to/file.json`.
* **`id` (optional)** : Die ID der Orchestrierungsinstanz. Ohne Angabe dieses Parameters verwendet der Befehl eine zufällige ID.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert ist AzureWebJobsStorage.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert ist DurableFunctionsHub. Sie können diesen Wert auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festlegen.

> [!NOTE]
> Bei Core Tools-Befehlen wird davon ausgegangen, dass Sie sie im Stammverzeichnis einer Funktions-App ausführen. Wenn Sie den `connection-string-setting`- und `task-hub-name`-Parameter explizit angeben, können Sie die Befehle von jedem beliebigen Verzeichnis aus ausführen. Sie können diese Befehle zwar ausführen, ohne dass ein Funktions-App-Host ausgeführt wird, aber möglicherweise stellen Sie fest, dass Sie einige Auswirkungen nur dann beobachten können, wenn der Host ausgeführt wird. Beispielsweise reiht der Befehl `start-new` eine Startmeldung in die Warteschlange für den Zielaufgabenhub ein, die Orchestrierung wird jedoch nicht ausgeführt, bis ein Funktions-App-Hostprozess ausgeführt wird, der die Nachricht verarbeiten kann.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Der folgende Befehl startet die Funktion HelloWorld und übergibt dieser den Inhalt der Datei `counter-data.json`:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Abfragen von Instanzen

Nach dem Starten neuer Orchestrierungsinstanzen müssen Sie wahrscheinlich deren Runtimestatus abfragen, um zu ermitteln, ob diese derzeit ausgeführt werden, erfolgreich ausgeführt wurden oder ein Fehler aufgetreten ist.

Die Methoden `GetStatusAsync` (.NET), `getStatus` (JavaScript) oder `get_status` (Python) für die [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) fragen den Status einer Orchestrierungsinstanz ab.

Hierfür werden `instanceId` (erforderlich), `showHistory` (optional), `showHistoryOutput` (optional) und `showInput` (optional) als Parameter verwendet.

* **`showHistory`** : Bei Festlegung auf `true` enthält die Antwort den Ausführungsverlauf.
* **`showHistoryOutput`** : Bei Festlegung auf `true` enthält der Ausführungsverlauf Aktivitätsausgaben.
* **`showInput`** : Bei Festlegung auf `false` enthält die Antwort nicht die Eingabe der Funktion. Standardwert: `true`.

Die Methode gibt ein Objekt mit den folgenden Eigenschaften zurück:

* **Name**: Der Name der Orchestratorfunktion.
* **InstanceId**: Die Instanz-ID der Orchestrierung (muss mit der Eingabe `instanceId` identisch sein).
* **CreatedTime**: Der Zeitpunkt, zu dem die Ausführung der Orchestratorfunktion gestartet wurde.
* **LastUpdatedTime**: Der Zeitpunkt, zu dem der letzte Prüfpunkt für die Orchestrierung erstellt wurde.
* **Eingabe**: Die Eingabe der Funktion als JSON-Wert. Dieses Feld wird nicht aufgefüllt, wenn `showInput` FALSE ist.
* **CustomStatus**: Der benutzerdefinierter Orchestrierungsstatus im JSON-Format.
* **Ausgabe**: Die Ausgabe der Funktion als JSON-Wert (wenn die Funktion abgeschlossen wurde). Wenn ein Fehler bei der Orchestratorfunktion auftritt, enthält diese Eigenschaft die Fehlerdetails. Wenn die Orchestratorfunktion beendet wurde, enthält diese Eigenschaft den Grund für die Beendigung (sofern vorhanden).
* **RuntimeStatus**: Einer der folgenden Werte:
  * **Pending**: Die Instanz wurde geplant, ihre Ausführung aber noch nicht gestartet.
  * **Wird ausgeführt:** Die Instanz wurde gestartet.
  * **Completed**: Die Instanz wurde normal abgeschlossen.
  * **ContinuedAsNew**: Die Instanz hat sich selbst mit einem neuen Verlauf neu gestartet. Der Status ist ein vorübergehender Status.
  * **Fehler:** Bei der Instanz ist ein Fehler aufgetreten.
  * **Beendet:** Die Instanz wurde plötzlich beendet.
* **History**: Der Ausführungsverlauf der Orchestrierung. Dieses Feld wird nur gefüllt, wenn `showHistory` auf `true` festgelegt ist.

> [!NOTE]
> Ein Orchestrator wird erst mit `Completed` gekennzeichnet, wenn alle seine geplanten Aufgaben abgeschlossen wurden _und_ der Orchestrator die Anweisung zum Zurückgeben ausgeführt hat. Das bedeutet, es reicht nicht aus, dass ein Orchestrator seine `return`-Anweisung erreicht, damit er mit `Completed` gekennzeichnet wird. Dies ist insbesondere in Fällen relevant, in denen `WhenAny` verwendet wird. Diese Orchestratoren führen oft die `return`-Anweisung aus, bevor alle geplanten Aufgaben durchgeführt wurden.

Diese Methode gibt `null` (.NET), `undefined` (JavaScript) oder `None` (Python) zurück, wenn die Instanz nicht vorhanden ist.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Es ist auch möglich, den Status der Orchestrierung direkt über den [`func durable get-runtime-status`-Befehl](../functions-core-tools-reference.md#func-durable-get-runtime-status) in den Core Tools abzurufen.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum persistenten Speichern des Runtimestatus verwendet wird.

Für den Befehl `durable get-runtime-status` werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`show-input` (optional)** : Bei Festlegung auf `true` enthält die Antwort die Eingabe der Funktion. Standardwert: `false`.
* **`show-output` (optional)** : Bei Festlegung auf `true` enthält die Antwort die Ausgabe der Funktion. Standardwert: `false`.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Der folgende Befehl ruft den Status (einschließlich Ein- und Ausgaben) einer Instanz mit der Orchestrierungsinstanz-ID 0ab8c55a66644d68a3a8b220b12d209c ab. Dabei wird vorausgesetzt, dass Sie den Befehl `func` im Stammverzeichnis der Funktions-App ausführen:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Sie können den Befehl `durable get-history` zum Abrufen des Ausführungsverlaufs einer Orchestrierungsinstanz verwenden. Hierfür werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in „host.json“ festgelegt werden.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Abfragen aller Instanzen

Sie können die Methoden [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync) (.NET), [getStatusAll](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--) (JavaScript) oder `get_status_all` (Python) verwenden, um die Status aller Orchestrierungsinstanzen in Ihrem [Aufgabenhub](durable-functions-task-hubs.md) abzufragen. Diese Methode gibt eine Liste der Objekte zurück, die die Orchestrierungsinstanzen darstellen, die den Abfrageparametern entsprechen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Es ist auch möglich, Instanzen direkt abzufragen, indem Sie den [`func durable get-instances`-Befehl](../functions-core-tools-reference.md#func-durable-get-instances) in den Core Tools verwenden.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Für den Befehl `durable get-instances` werden die folgenden Parameter verwendet:

* **`top` (optional)** : Dieser Befehl unterstützt Paging. Dieser Parameter entspricht der Anzahl der pro Anforderung abgerufenen Instanzen. Der Standardwert ist 10.
* **`continuation-token` (optional)** : Ein Token, das angibt, welche Seite oder welcher Abschnitt der Instanzen abgerufen werden soll. Jede Ausführung von `get-instances` gibt ein Token an den nächsten Satz von Instanzen zurück.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Abfragen von Instanzen mit Filtern

Was ist, wenn Sie nicht alle Informationen benötigen, die eine standardmäßige Instanzenabfrage bieten kann? Wenn Sie z.B. nur den Zeitpunkt der Erstellung der Orchestrierung oder den Laufzeitstatus der Orchestrierung erfahren möchten? Sie können die Abfrage durch Anwenden von Filtern eingrenzen.

Mit den Methoden [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET) oder [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) können Sie eine Liste von Orchestrierungsinstanzen abrufen, die vordefinierten Filtern entsprechen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2021, 3, 10, 10, 1, 0),
        new Date(2021, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2021, 3, 10, 10, 1, 0),
        datetime(2021, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

In den Azure Functions Core Tools können Sie auch den Befehl `durable get-instances` mit Filtern verwenden. Zusätzlich zu den oben genannten Parametern `top`, `continuation-token`, `connection-string-setting` und `task-hub-name` können Sie drei Filterparameter (`created-after`, `created-before` und `runtime-status`) verwenden.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Im Folgenden sind die Parameter für den Befehl `durable get-instances` angegeben.

* **`created-after` (optional)** : Ruft die nach diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen ab. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`created-before` (optional)** : Ruft die vor diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen ab. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`runtime-status` (optional)** : Rufen Sie die Instanzen mit einem bestimmten Status ab (z.B. „Ausgeführt“ oder „Abgeschlossen“). Es können mehrere Status angegeben werden (Leerzeichen als Trennzeichen).
* **`top` (optional)** : Die Anzahl pro Anforderung abgerufener Instanzen. Der Standardwert ist 10.
* **`continuation-token` (optional)** : Ein Token, das angibt, welche Seite oder welcher Abschnitt der Instanzen abgerufen werden soll. Jede Ausführung von `get-instances` gibt ein Token an den nächsten Satz von Instanzen zurück.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Wenn Sie keine Filter angeben (`created-after`, `created-before` oder `runtime-status`), ruft der Befehl einfach unabhängig von Laufzeitstatus oder Erstellungszeit `top`-Instanzen ab.

```bash
func durable get-instances --created-after 2021-03-10T13:57:31Z --created-before  2021-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Beenden von Instanzen

Wenn die Ausführung einer Orchestrierungsinstanz zu lange dauert oder Sie sie aus beliebigem Grund vor dem Abschluss unterbrechen müssen, können Sie sie beenden.

Sie können die Methoden `TerminateAsync` (.NET), `terminate` (JavaScript) oder `terminate` (Python) der [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) zum Beenden von Instanzen verwenden. Die beiden Parameter sind eine `instanceId` und eine `reason`-Zeichenfolge, die in Protokolle und den Instanzenstatus geschrieben werden.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "Found a bug";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Found a bug";
    return client.terminate(instanceId, reason);
};
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Found a bug"
    return client.terminate(instance_id, reason)
```

---

Eine beendete Instanz wechselt schließlich in den Zustand `Terminated`. Dieser Übergang erfolgt jedoch nicht sofort. Stattdessen wird der Beendigungsvorgang im Aufgabenhub zusammen mit anderen Vorgängen für diese Instanz in eine Warteschlange eingereiht. Sie können die [Instanzabfrage](#query-instances)-APIs verwenden, um zu erfahren, wann eine beendete Instanz tatsächlich den Zustand `Terminated` erreicht hat.

> [!NOTE]
> Die Instanzbeendigung wird momentan nicht weitergegeben. Aktivitätsfunktionen und untergeordnete Orchestrierungen werden unabhängig davon, ob Sie die Orchestrierungsinstanz beendet haben, durch die sie aufgerufen wurden, bis zum Ende ausgeführt.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können eine Orchestrierungsinstanz auch direkt mit dem [`func durable terminate`-Befehl](../functions-core-tools-reference.md#func-durable-terminate) der Core Tools beenden.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Für den Befehl `durable terminate` werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der zu beendenden Orchestrierungsinstanz.
* **`reason` (optional)** : Der Grund für die Beendigung.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

Der folgende Befehl beendet die Orchestrierungsinstanz mit der ID 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Found a bug"
```

## <a name="send-events-to-instances"></a>Senden von Ereignissen an Instanzen

In einigen Szenarios müssen Orchestratorfunktionen auf externe Ereignisse warten und lauschen. Das ist beispielsweise bei Szenarios wie [Überwachung](durable-functions-overview.md#monitoring) oder [menschlicher Interaktion](durable-functions-overview.md#human) nützlich.

Sie können Ereignisbenachrichtigungen an aktuell ausgeführte Instanzen senden, indem Sie die Methode `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript) oder `raise_event` (Python) des [Orchestrierungsclients](durable-functions-bindings.md#orchestration-client) verwenden. Instanzen, die diese Ereignisse verarbeiten können, warten auf einen Aufruf von `WaitForExternalEvent` (.NET) oder auf das Ergebnis eines `waitForExternalEvent`- (JavaScript) oder `wait_for_external_event`-Tasks (Python).

Die Parameter für `RaiseEventAsync` (.NET) und `raiseEvent` (JavaScript) lauten wie folgt:

* **InstanceId**: Die eindeutige ID der Instanz.
* **EventName**: Der Name des zu sendenden Ereignisses.
* **EventData**: Eine JSON-serialisierbare Nutzlast, die an die Instanz gesendet werden soll.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> Ist keine Orchestrierungsinstanz mit der angegebenen Instanz-ID vorhanden, wird die Ereignisnachricht verworfen. Wenn eine Instanz vorhanden ist, aber noch nicht auf das Ereignis wartet, wird das Ereignis im Instanzstatus gespeichert, bis es für den Empfang und die Verarbeitung bereit ist.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können ein Ereignis für eine Orchestrierungsinstanz auch direkt mit dem [`func durable raise-event`-Befehl](../functions-core-tools-reference.md#func-durable-raise-event) der Azure Core Tools auslösen.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Für den Befehl `durable raise-event` werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`event-name`** : Der Name des auszulösenden Ereignisses.
* **`event-data` (optional)** : Die an die Orchestrierungsinstanz zu sendenden Daten. Dies kann der Pfad zu einer JSON-Datei sein, oder Sie können die Daten direkt in der Befehlszeile eingeben.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Der Standardwert lautet `DurableFunctionsHub`. Der Wert kann auch über „durableTask:HubName“ in [host.json](durable-functions-bindings.md#host-json) festgelegt werden.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Warten auf den Abschluss der Orchestrierung

Bei lange andauernden Orchestrierungen möchten Sie vielleicht warten und die Ergebnisse einer Orchestrierung erhalten. In diesen Fällen ist es auch sinnvoll, ein Timeout für die Orchestrierung definieren zu können. Wenn das Timeout überschritten wird, sollte der Status der Orchestrierung anstelle der Ergebnisse zurückgegeben werden.

Die Methode `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET), `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) oder `wait_for_completion_or_create_check_status_response` (Python) kann verwendet werden, um synchron die tatsächliche Ausgabe einer Orchestrierungsinstanz abzurufen. Standardmäßig verwenden diese Methoden einen Standardwert von 10 Sekunden für `timeout` und von einer Sekunde für `retryInterval`.  

Hier ist ein Beispiel für eine HTTP-Triggerfunktion angegeben, mit dem die Nutzung dieser API veranschaulicht wird:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

Rufen Sie die Funktion mit der folgenden Zeile auf. Verwenden Sie 2 Sekunden für das Timeout und 0,5 Sekunden für das Wiederholungsintervall:

```bash
curl -X POST "http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5"
```

Je nach Zeitraum, der zum Abrufen der Antwort aus der Orchestrierungsinstanz erforderlich ist, gibt es zwei Fälle:

* Die Orchestrierungsinstanzen werden innerhalb des definierten Zeitraums (hier: 2 Sekunden) abgeschlossen, und die Antwort ist die tatsächliche Orchestrierungsinstanzausgabe, die synchron bereitgestellt wird:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:14:29 GMT
Transfer-Encoding: chunked

[
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
]
```

* Die Orchestrierungsinstanzen können nicht innerhalb des definierten Timeouts abgeschlossen werden, und die Antwort ist die Standardantwort, die unter [Ermittlung der HTTP-API-URL](durable-functions-http-api.md) beschrieben ist:

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:13:51 GMT
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
Retry-After: 10
Transfer-Encoding: chunked

{
    "id": "d3b72dddefce4e758d92f4d411567177",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
}
```

> [!NOTE]
> Das Format der Webhook-URLs kann in Abhängigkeit davon variieren, welche Version des Azure Functions-Hosts Sie ausführen. Das obige Beispiel gilt für den Azure Functions 3.0-Host.

## <a name="retrieve-http-management-webhook-urls"></a>Abrufen von HTTP-Management-Webhook-URLs

Sie können ein externes System zum Überwachen oder zum Auslösen von Ereignissen bei einer Orchestrierung verwenden. Externe Systeme können mit Durable Functions über die Webhook-URLs kommunizieren, die Teil der in [HTTP-APIs in Durable Functions (Azure Functions)](durable-functions-http-features.md#http-api-url-discovery) beschriebenen Standardantwort sind. Der Zugriff auf die Webhook-URLs kann auch programmgesteuert mithilfe der [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) erfolgen. Die Methode `CreateHttpManagementPayload` (.NET) oder `createHttpManagementPayload` (JavaScript) kann verwendet werden, um ein serialisierbares Objekt abzurufen, das diese Webhook-URLs enthält.

Die Methoden `CreateHttpManagementPayload` (.NET) und `createHttpManagementPayload` (JavaScript) haben einen Parameter:

* **instanceId**: Die eindeutige ID der Instanz.

Die Methoden geben ein Objekt mit den folgenden Zeichenfolgeneigenschaften zurück:

* **Id:** Die Instanz-ID der Orchestrierung (muss mit der Eingabe `InstanceId` identisch sein).
* **StatusQueryGetUri**: Status-URL der Orchestrierungsinstanz
* **SendEventPostUri**: URL der Orchestrierungsinstanz für die „Ereignisauslösung“
* **TerminatePostUri**: URL der Orchestrierungsinstanz für die „Beendigung“
* **PurgeHistoryDeleteUri**: URL der Orchestrierungsinstanz für den „Bereinigungsverlauf“

Funktionen können Instanzen dieser Objekte an externe Systeme senden, um Ereignisse in den entsprechenden Orchestrierungen zu überwachen oder auszulösen, wie in den folgenden Beispielen gezeigt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie `DurableActivityContext` anstelle von `IDurableActivityContext` verwenden, Sie müssen das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>Zurückspulen von Instanzen (Vorschau)

Wenn bei einer Orchestrierung aus unerwartetem Grund Fehler auftreten, können Sie die Instanz mithilfe einer für diesen Zweck erstellten API auf einen fehlerfreien früheren Zustand *zurückspulen*.

> [!NOTE]
> Diese API ist nicht als Ersatz für ordnungsgemäße Richtlinien zur Fehlerbehandlung und Wiederholung gedacht. Sie soll nur in Fällen verwendet werden, in denen Orchestrierungsinstanzen aus unerwarteten Gründen ausfallen. Weitere Informationen zur Fehlerbehandlung und zu Wiederholungsrichtlinien finden Sie im Artikel [Fehlerbehandlung](durable-functions-error-handling.md).

Verwenden Sie die Methode `RewindAsync` (.NET) oder `rewind` (JavaScript) der [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client), um die Orchestrierung erneut in den Zustand *Ausgeführt* zu versetzen. Diese Methode führt auch die Aktivität oder die Ausführungsfehler der untergeordneten Orchestrierung, die den Orchestrierungsfehler verursacht hat, erneut aus.

Stellen Sie sich als Beispiel einen Workflow vor, der eine Reihe von [menschlichen Genehmigungen](durable-functions-overview.md#human) umfasst. Angenommen, eine Reihe von Aktivitätsfunktionen benachrichtigt jemanden, dass seine Genehmigung erforderlich ist, und wartet in Echtzeit auf die Antwort. Stellen Sie sich vor, dass nachdem alle Genehmigungsaktivitäten Antworten erhalten haben oder abgelaufen sind, aufgrund einer Fehlkonfiguration einer Anwendung, z.B. einer ungültige Datenbank-Verbindungszeichenfolge, ein Fehler bei einer anderen Aktivität auftritt. Das Ergebnis ist ein Orchestrierungsfehler tief im Workflow. Mit der `RewindAsync`-API (.NET) oder `rewind`-API (JavaScript) kann ein Anwendungsadministrator den Konfigurationsfehler beheben und die fehlerhafte Orchestrierung zum Zustand unmittelbar vor dem Fehler zurückspulen. Keiner der Schritte, die menschliche Interaktion erforderten, muss erneut genehmigt werden, und die Orchestrierung kann jetzt erfolgreich abgeschlossen werden.

> [!NOTE]
> Die Funktion *Zurückspulen* unterstützt kein Zurückspulen von Orchestrierungsinstanzen, die permanente Timer verwenden.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Dieses Feature wird derzeit in Python nicht unterstützt.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können eine Orchestrierungsinstanz auch direkt mit dem [`func durable rewind`-Befehl](../functions-core-tools-reference.md#func-durable-rewind) der Core Tools zurückspulen.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Für den Befehl `durable rewind` werden die folgenden Parameter verwendet:

* **`id` (erforderlich)** : Die ID der Orchestrierungsinstanz.
* **`reason` (optional)** : Der Grund für das Zurückspulen der Orchestrierungsinstanz.
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Standardmäßig wird der Aufgabenhubname in der Datei [host.json](durable-functions-bindings.md#host-json) verwendet.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Löschen des Instanzverlaufs

Um alle Daten im Zusammenhang mit einer Orchestrierung zu entfernen, können Sie den Instanzverlauf löschen. Beispielsweise möchten Sie möglicherweise alle Azure Table-Zeilen und große Nachrichtenblobs löschen, die einer abgeschlossenen Instanz zugeordnet sind. Hierfür können Sie die Methode `PurgeInstanceHistoryAsync` (.NET), `purgeInstanceHistory` (JavaScript) oder `purge_instance_history` (Python) des [Orchestrierungsclientobjekts](durable-functions-bindings.md#orchestration-client) verwenden.

Diese Methode verfügt über zwei Überladungen. Bei der ersten Überladung wird der Verlauf durch die ID der Orchestrierungsinstanz bereinigt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Die „function.json“-Konfiguration finden Sie unter [Starten von Instanzen](#javascript-function-json).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

Das nächste Beispiel zeigt eine von einem Timer ausgelöste Funktion, die den Verlauf für alle Orchestrierungsinstanzen löscht, die nach Ablauf der angegebenen Zeitspanne abgeschlossen wurden. In diesem Fall werden die Daten für alle Instanzen entfernt, die vor mindestens 30 Tagen abgeschlossen wurden. Diese Beispielfunktion wird einmal täglich um 12:00 Uhr UTC ausgeführt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")] TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> Der vorherige C#-Code ist für Durable Functions 2.x vorgesehen. Für Durable Functions 1.x müssen Sie das `OrchestrationClient`-Attribut anstelle des `DurableClient`-Attributs verwenden, und Sie müssen den `DurableOrchestrationClient`-Parametertyp anstelle von `IDurableOrchestrationClient` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die `purgeInstanceHistoryBy`-Methode kann verwendet werden, um den Instanzverlauf für mehrere Instanzen bedingt zu bereinigen.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Dieses Beispiel gilt für Durable Functions, Version 2.x. Verwenden Sie in Version 1.x `orchestrationClient` anstelle von `durableClient`.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> Damit der Vorgang zum Bereinigen des Verlaufs erfolgreich ist, muss der Laufzeitstatus der Zielinstanz **Abgeschlossen**, **Beendet** oder **Fehlgeschlagenen** sein.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Sie können den Verlauf einer Orchestrierungsinstanz mit dem [`func durable purge-history`-Befehl](../functions-core-tools-reference.md#func-durable-purge-history) der Core Tools bereinigen. Ähnlich wie im zweiten C#-Beispiel im vorherigen Abschnitt wird der Verlauf für alle Orchestrierungsinstanzen gelöscht, die während eines angegebenen Zeitintervalls erstellt wurden. Sie können gelöschte Instanzen weiter nach dem Laufzeitstatus filtern.

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Der Befehl `durable purge-history` verfügt über mehrere Parameter:

* **`created-after` (optional)** : Löscht den Verlauf der nach diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`created-before` (optional)** : Löscht den Verlauf der vor diesem Datum/dieser Uhrzeit (UTC) erstellten Instanzen. Es werden nach ISO 8601 formatierte datetime-Werte akzeptiert.
* **`runtime-status` (optional)** : Löschen Sie den Verlauf von Instanzen mit einem bestimmten Status (z.B. „Ausgeführt“ oder „Abgeschlossen“). Es können mehrere Status angegeben werden (Leerzeichen als Trennzeichen).
* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Standardmäßig wird der Aufgabenhubname in der Datei [host.json](durable-functions-bindings.md#host-json) verwendet.

Der folgende Befehl löscht den Verlauf aller fehlerhaften Instanzen, die vor dem 14. November 2021 um 19:35 Uhr UTC erstellt wurden.

```bash
func durable purge-history --created-before 2021-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Löschen eines Aufgabenhubs

Mithilfe des [`func durable delete-task-hub`-Befehls](../functions-core-tools-reference.md#func-durable-delete-task-hub) in den Core Tools können Sie alle Speicherartefakte löschen, die einem bestimmten Aufgabenhub zugeordnet sind, einschließlich Azure Storage-Tabellen, -Abfragen und -Blobs. 

> [!NOTE]
> Core Tools-Befehle werden derzeit nur unterstützt, wenn der [Azure Storage-Standardanbieter](durable-functions-storage-providers.md) zum Beibehalten des Runtimestatus verwendet wird.

Der Befehl `durable delete-task-hub` verfügt über zwei Parameter:

* **`connection-string-setting` (optional)** : Der Name der Anwendungseinstellung mit der zu verwendenden Speicherverbindungszeichenfolge. Der Standardwert lautet `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Der Name des zu verwendenden Durable Functions-Aufgabenhubs. Standardmäßig wird der Aufgabenhubname in der Datei [host.json](durable-functions-bindings.md#host-json) verwendet.

Der folgende Befehl löscht alle Azure Storage-Daten, die dem Aufgabenhub `UserTest` zugeordnet sind.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Versioning in Durable Functions (Azure Functions)](durable-functions-versioning.md) (Versionsverwaltung in Durable Functions [Azure Functions])

> [!div class="nextstepaction"]
> [Integrierte in HTTP API-Referenz für Instanzverwaltung](durable-functions-http-api.md)
