---
title: Übersicht zu Durable Functions – Azure
description: Einführung in die Durable Functions-Erweiterung für Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: c9a81c052f44afbb2049442f09e34a0e6bc588f9
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054769"
---
# <a name="what-are-durable-functions"></a>Was ist Durable Functions?

*Durable Functions* ist eine Erweiterung von [Azure Functions](../functions-overview.md), mit der Sie zustandsbehaftete Funktionen in einer serverlosen Compute-Umgebung schreiben können. Mit der Erweiterung können Sie mithilfe des Azure Functions-Programmiermodells zustandsbehaftete Workflows durch Schreiben von [*Orchestratorfunktionen*](durable-functions-orchestrations.md) und zustandsbehaftete Entitäten durch Schreiben von [*Entitätsfunktionen*](durable-functions-entities.md) definieren. Im Hintergrund verwaltet die Erweiterung Zustand, Prüfpunkte und Neustarts für Sie, sodass Sie sich auf Ihre Geschäftslogik konzentrieren können.

## <a name="supported-languages"></a><a name="language-support"></a>Unterstützte Sprachen

Durable Functions unterstützt derzeit die folgenden Sprachen:

* **C#** : sowohl [vorkompilierte Klassenbibliotheken](../functions-dotnet-class-library.md) als auch [ C#-Skript](../functions-reference-csharp.md).
* **JavaScript**: Unterstützung nur für Version 2.x der Azure Functions-Runtime oder höhere Versionen. Erfordert mindestens Version 1.7.0 der Durable Functions-Erweiterung. 
* **Python**: Hierfür ist mindestens Version 2.3.1 der Durable Functions-Erweiterung erforderlich.
* **F#** : vorkompilierte Klassenbibliotheken und F#-Skript. F#-Skript wird nur für Version 1.x der Azure Functions-Runtime unterstützt.
* **PowerShell** wird nur für Version 3.x der Azure Functions-Runtime und PowerShell 7 unterstützt. Es ist die Version 2.x der Bundleerweiterungen erforderlich.

Zum Zugreifen auf die aktuellen Features und Updates empfehlen wir Ihnen, die aktuellen Versionen der Durable Functions-Erweiterung und die sprachspezifischen Durable Functions-Bibliotheken zu verwenden. Lesen Sie die weiteren Informationen zu den [Durable Functions-Versionen](durable-functions-versions.md).

Das Ziel von Durable Functions ist die Unterstützung aller [Azure Functions-Sprachen](../supported-languages.md). In der [Liste der Durable Functions-Probleme](https://github.com/Azure/azure-functions-durable-extension/issues) finden Sie den aktuellen Status der Arbeit, um zusätzliche Sprachen zu unterstützen.

Wie bei Azure Functions sind Vorlagen verfügbar, um Sie bei der Entwicklung von Durable Functions mit [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) und dem [Azure-Portal](durable-functions-create-portal.md) zu unterstützen.

## <a name="application-patterns"></a>Anwendungsmuster

Der primäre Anwendungsfall für Durable Functions ist die Vereinfachung komplexer, zustandsbehafteter Koordinationsanforderungen in serverlosen Anwendungen. In den folgenden Abschnitten werden typische Anwendungsmuster beschrieben, die von Durable Functions profitieren können:

* [Funktionsverkettung](#chaining)
* [Auffächern nach außen/innen](#fan-in-out)
* [Asynchrone HTTP-APIs](#async-http)
* [Überwachung](#monitoring)
* [Benutzerinteraktion](#human)
* [Aggregator (zustandsbehaftete Entitäten)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Muster 1: Funktionsverkettung

Beim Muster der Funktionsverkettung wird eine Abfolge von Funktionen in einer bestimmten Reihenfolge ausgeführt. Bei diesem Muster wird die Ausgabe einer Funktion als Eingabe einer weiteren Funktion verwendet.

![Ein Diagramm des Funktionsverkettungsmusters](./media/durable-functions-concepts/function-chaining.png)

Mithilfe von Durable Functions können Sie das Funktionsverkettungsmuster präzise wie im folgenden Beispiel dargestellt implementieren.

In diesem Beispiel sind die Werte `F1`, `F2`, `F3` und `F4` die Namen weiterer Funktionen in der gleichen Funktions-App. Sie können die Ablaufsteuerung mithilfe normaler imperativer Codierungskonstrukte implementieren. Der Code wird von oben nach unten ausgeführt. Der Code kann bestehende sprachliche Ablaufsteuerungssemantik wie Bedingungsanweisungen und Schleifen umfassen. Sie können Logik zur Fehlerbehandlung in `try`/`catch`/`finally`-Blöcken einschließen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

Sie können den Parameter `context` verwenden, um andere Funktionen anhand des Namens aufzurufen, Parameter zu übergeben und Funktionsausgaben zurückzugeben. Bei jedem Aufruf von `await` im Code erstellt das Framework von Durable Functions Prüfpunkte für den Status der aktuellen Funktionsinstanz. Wenn der Prozess oder der virtuelle Computer mitten in der Ausführung neu gestartet wird, wird die Funktionsinstanz ab dem vorherigen Aufruf von `await` fortgesetzt. Weitere Informationen finden Sie im nächsten Abschnitt, Muster 2: Auffächern auswärts/einwärts.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Sie können das Objekt `context.df` verwenden, um andere Funktionen anhand des Namens aufzurufen, Parameter zu übergeben und Funktionsausgaben zurückzugeben. Bei jedem Aufruf von `yield` im Code erstellt das Framework von Durable Functions Prüfpunkte für den Status der aktuellen Funktionsinstanz. Wenn der Prozess oder der virtuelle Computer mitten in der Ausführung neu gestartet wird, wird die Funktionsinstanz ab dem vorherigen Aufruf von `yield` fortgesetzt. Weitere Informationen finden Sie im nächsten Abschnitt, Muster 2: Auffächern auswärts/einwärts.

> [!NOTE]
> Das Objekt `context` in JavaScript stellt den gesamten [Funktionskontext](../functions-reference-node.md#context-object) dar. Verwenden Sie für den Zugriff auf den Durable Functions-Kontext die Eigenschaft `df` im Hauptkontext.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

Sie können das Objekt `context` verwenden, um andere Funktionen anhand des Namens aufzurufen, Parameter zu übergeben und Funktionsausgaben zurückzugeben. Bei jedem Aufruf von `yield` im Code erstellt das Framework von Durable Functions Prüfpunkte für den Status der aktuellen Funktionsinstanz. Wenn der Prozess oder der virtuelle Computer mitten in der Ausführung neu gestartet wird, wird die Funktionsinstanz ab dem vorherigen Aufruf von `yield` fortgesetzt. Weitere Informationen finden Sie im nächsten Abschnitt, Muster 2: Auffächern auswärts/einwärts.

> [!NOTE]
> Das `context`-Objekt in Python stellt den Orchestrierungskontext dar. Verwenden Sie für den Zugriff auf den Azure Functions-Hauptkontext die Eigenschaft `function_context` im Orchestrierungskontext.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-DurableActivity -FunctionName 'F1'
$Y = Invoke-DurableActivity -FunctionName 'F2' -Input $X
$Z = Invoke-DurableActivity -FunctionName 'F3' -Input $Y
Invoke-DurableActivity -FunctionName 'F4' -Input $Z
```

Sie können den Befehl `Invoke-DurableActivity` verwenden, um andere Funktionen anhand des Namens aufzurufen, Parameter zu übergeben und Funktionsausgaben zurückzugeben. Jedes Mal, wenn der Code `Invoke-DurableActivity` ohne den Switch `NoWait` aufruft, erstellt das Durable Functions-Framework Prüfpunkte zum Status der aktuellen Funktionsinstanz. Wenn der Prozess oder der virtuelle Computer mitten in der Ausführung neu gestartet wird, wird die Funktionsinstanz ab dem vorherigen Aufruf von `Invoke-DurableActivity` fortgesetzt. Weitere Informationen finden Sie im nächsten Abschnitt, Muster 2: Auffächern auswärts/einwärts.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Muster 2: Auffächern auswärts/einwärts

Beim Muster Auffächern auswärts/einwärts werden mehrere Funktionen parallel ausgeführt und anschließend auf den Abschluss aller gewartet. Häufig werden die von den Funktionen zurückgegebenen Ergebnisse einer Aggregation unterzogen.

![Ein Diagramm des Musters „Auffächern auswärts/einwärts“](./media/durable-functions-concepts/fan-out-fan-in.png)

Bei normalen Funktionen kann das Auffächern auswärts erfolgen, indem die Funktion mehrere Nachrichten an eine Warteschlange sendet. Das Auffächern zurück nach innen ist wesentlich schwieriger. Für das Auffächern nach innen schreiben Sie in einer normalen Funktion Code, der nachverfolgt, wann die von der Warteschlange ausgelösten Funktionen enden und speichern dann ihre Ausgaben.

Die Erweiterung Durable Functions wird diesem Muster mit relativ einfachem Code gerecht:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Das Auffächern nach außen ist auf mehrere Instanzen der `F2`-Funktion verteilt. Die Arbeit wird mithilfe einer dynamischen Aufgabenliste nachverfolgt. `Task.WhenAll` wird aufgerufen, um zu warten, bis alle aufgerufenen Funktionen beendet sind. Anschließend werden die Ausgaben der `F2`-Funktion aus der dynamischen Aufgabenliste aggregiert und an die `F3`-Funktion übergeben.

Die automatische Prüfpunkterstellung, die beim Aufruf von `await` für `Task.WhenAll` erfolgt, stellt sicher, dass ein potentieller Absturz oder Neustart während der Ausführung keinen Neustart bereits abgeschlossener Aufgaben erfordert.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Das Auffächern nach außen ist auf mehrere Instanzen der `F2`-Funktion verteilt. Die Arbeit wird mithilfe einer dynamischen Aufgabenliste nachverfolgt. Die API `context.df.Task.all` wird aufgerufen, um zu warten, bis alle aufgerufenen Funktionen beendet sind. Anschließend werden die Ausgaben der `F2`-Funktion aus der dynamischen Aufgabenliste aggregiert und an die `F3`-Funktion übergeben.

Die automatische Prüfpunkterstellung, die beim Aufruf von `yield` für `context.df.Task.all` erfolgt, stellt sicher, dass ein potentieller Absturz oder Neustart während der Ausführung keinen Neustart bereits abgeschlossener Aufgaben erfordert.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

Das Auffächern nach außen ist auf mehrere Instanzen der `F2`-Funktion verteilt. Die Arbeit wird mithilfe einer dynamischen Aufgabenliste nachverfolgt. Die API `context.task_all` wird aufgerufen, um zu warten, bis alle aufgerufenen Funktionen beendet sind. Anschließend werden die Ausgaben der `F2`-Funktion aus der dynamischen Aufgabenliste aggregiert und an die `F3`-Funktion übergeben.

Die automatische Prüfpunkterstellung, die beim Aufruf von `yield` für `context.task_all` erfolgt, stellt sicher, dass ein potentieller Absturz oder Neustart während der Ausführung keinen Neustart bereits abgeschlossener Aufgaben erfordert.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-DurableActivity -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-DurableActivity -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-DurableActivity -FunctionName 'F3' -Input $Total
```

Das Auffächern nach außen ist auf mehrere Instanzen der `F2`-Funktion verteilt. Beachten Sie die Verwendung des Switches `NoWait` für den Aufruf der `F2`-Funktion: Dieser Switch ermöglicht es dem Orchestrator, `F2` weiterhin aufzurufen, ohne dass auf den Abschluss von Aktivitäten gewartet werden muss. Die Arbeit wird mithilfe einer dynamischen Aufgabenliste nachverfolgt. Der Befehl `Wait-ActivityFunction` wird aufgerufen, um zu warten, bis alle aufgerufenen Funktionen beendet sind. Anschließend werden die Ausgaben der `F2`-Funktion aus der dynamischen Aufgabenliste aggregiert und an die `F3`-Funktion übergeben.

Die automatische Prüfpunkterstellung, die beim Aufruf von `Wait-ActivityFunction` erfolgt, stellt sicher, dass ein potentieller Absturz oder Neustart während der Ausführung keinen Neustart bereits abgeschlossener Aufgaben erfordert.

---

> [!NOTE]
> In seltenen Fällen kann es im Fenster zu einem Absturz kommen, nachdem eine Aktivitätsfunktion abgeschlossen wurde und bevor ihr Abschluss im Orchestrierungsverlauf gespeichert wurde. In dem Fall wird die Aktivitätsfunktion von Anfang an erneut ausgeführt, wenn der Prozess wieder hergestellt wurde.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Muster 3: Asynchrone HTTP-APIs

Das asynchrone HTTP-API-Muster ist geeignet, um den Status von Vorgängen mit langer Ausführungsdauer mit externen Clients zu koordinieren. Ein gängiges Verfahren zum Implementieren dieses Musters besteht darin, die Aktion mit langer Ausführungsdauer von einem HTTP-Endpunkt auslösen zu lassen. Leiten Sie dann den Client zu einem Statusendpunkt um, den der Client abfragt, um herauszufinden, wenn der Vorgang abgeschlossen ist.

![Ein Diagramm des HTTP-API-Musters](./media/durable-functions-concepts/async-http-api.png)

Durable Functions bietet **integrierte Unterstützung** für dieses Muster und vereinfacht oder entfernt den Code, den Sie für die Interaktion mit langen Funktionsausführungen schreiben. Die Schnellstartbeispiele für Durable Functions ([C#](durable-functions-create-first-csharp.md) und [JavaScript](quickstart-js-vscode.md)) zeigen beispielsweise einen einfachen REST-Befehl, den Sie zum Starten neuer Orchestratorfunktionsinstanzen verwenden können. Nach dem Start einer Instanz macht die Erweiterung Webhook-HTTP-APIs verfügbar, die den Status der Orchestratorfunktion abfragen. 

Das folgende Beispiel zeigt REST-Befehle zum Starten eines Orchestrators und zum Abfragen seines Status. Zur besseren Übersichtlichkeit werden im Beispiel einige Protokolldetails weggelassen.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Da der Status von der Durable Functions-Runtime für Sie verwaltet wird, müssen Sie keinen eigenen Mechanismus zur Statusnachverfolgung implementieren.

Die Durable Functions-Erweiterung macht integrierte HTTP-APIs verfügbar, die Orchestrierungen mit langer Ausführungsdauer verwalten. Sie können dieses Muster auch selbst implementieren, indem Sie Ihre eigenen Funktionstrigger (wie etwa HTTP, eine Warteschlange oder Azure Event Hubs) und die [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) verwenden. Sie können beispielsweise eine Warteschlangennachricht verwenden, um die Beendigung auszulösen. Sie könnten anstelle der integrierten HTTP-APIs, die einen generierten Schlüssel für die Authentifizierung verwenden, auch einen HTTP-Trigger verwenden, der durch eine Azure Active Directory-Authentifizierungsrichtlinie geschützt ist.

Weitere Informationen finden Sie im Artikel [HTTP-Features](durable-functions-http-features.md), in dem erläutert wird, wie Sie asynchrone Prozesse mit langer Ausführungszeit über HTTP mithilfe der Durable Functions-Erweiterung verfügbar machen können.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Muster 4: Überwachen

Das Überwachen-Muster bezieht sich auf einen flexiblen, wiederkehrenden Vorgang in einem Workflow. Ein Beispiel besteht im Abfragen, bis bestimmte Bedingungen erfüllt sind. Sie können einen normalen [Timertrigger](../functions-bindings-timer.md) für ein einfaches Szenario verwenden, beispielsweise einen periodischen Bereinigungsauftrag. Sein Intervall ist jedoch statisch, und die Verwaltung der Instanzlebensdauer wird komplex. Mithilfe von Durable Functions können Sie flexible Wiederholungsintervalle erstellen, die Lebensdauer von Aufgaben verwalten und mehrere Überwachungsprozesse aus einer einzelnen Orchestrierung erstellen.

Ein Beispiel für das Überwachen-Muster besteht in der Umkehrung des früheren asynchronen HTTP-API-Szenarios. Anstatt einen Endpunkt für einen externen Client freizugeben, um einen lang laufenden Vorgang zu überwachen, belegt der lang laufende Monitor einen externen Endpunkt und wartet dann auf einen Zustandswechsel.

![Ein Diagramm des Überwachen-Musters](./media/durable-functions-concepts/monitor.png)

Mit ein paar Codezeilen können Sie Durable Functions dazu verwenden, mehrere Monitore zu erstellen, die beliebige Endpunkte beobachten. Die Monitore können die Ausführung beenden, wenn eine Bedingung erfüllt ist, oder eine andere Funktion kann den langlebigen Orchestrierungsclient verwenden, um die Monitore zu beenden. Sie können das `wait`-Intervall eines Monitors auf der Grundlage einer spezifischen Bedingung (z.B. exponentielles Backoff) ändern. 

Der folgende Code implementiert einen einfachen Monitor:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$output = @()

$jobId = $Context.Input.JobId
$machineId = $Context.Input.MachineId
$pollingInterval = New-TimeSpan -Seconds $Context.Input.PollingInterval
$expiryTime = $Context.Input.ExpiryTime

while ($Context.CurrentUtcDateTime -lt $expiryTime) {
    $jobStatus = Invoke-DurableActivity -FunctionName 'GetJobStatus' -Input $jobId
    if ($jobStatus -eq "Completed") {
        # Perform an action when a condition is met.
        $output += Invoke-DurableActivity -FunctionName 'SendAlert' -Input $machineId
        break
    }

    # Orchestration sleeps until this time.
    Start-DurableTimer -Duration $pollingInterval
}

# Perform more work here, or let the orchestration end.

$output
```

---

Wenn eine Anforderung empfangen wird, wird eine neue Orchestrierungsinstanz für diese Auftrags-ID erstellt. Die Instanz fragt den Status ab, bis eine Bedingung erfüllt ist und die Schleife beendet wird. Ein permanenter Timer steuert das Abrufintervall. Anschließend können weitere Arbeitsschritte ausgeführt werden, oder die Orchestrierung wird beendet. Falls `expiryTime` von `nextCheck` überschritten wird, wird der Monitor beendet.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Muster 5: Benutzerinteraktion

Viele automatisierte Prozesse enthalten eine Form der Benutzerinteraktion. Das Einbeziehen von Menschen in einen automatisierten Prozess ist schwierig, da Personen nicht im gleichen hohen Maß verfügbar und reaktionsfähig sind wie Clouddienste. Ein automatisierter Prozess kann diese Interaktion mithilfe von Zeitlimits und Kompensationslogik ermöglichen.

Ein Genehmigungsprozess ist ein Beispiel für einen Geschäftsprozesses, der Benutzerinteraktion umfasst. Beispielsweise kann für eine Spesenabrechnung, die einen bestimmten Betrag überschreitet, die Genehmigung eines Vorgesetzten erforderlich sein. Wenn der Vorgesetzte die Spesenabrechnung nicht innerhalb von 72 Stunden genehmigt (vielleicht weil er im Urlaub ist), wird ein Eskalationsverfahren wirksam, um die Genehmigung von einer anderen Person (z.B. dem Vorgesetzten des Vorgesetzten) zu erhalten.

![Ein Diagramm des Musters „Benutzerinteraktion“](./media/durable-functions-concepts/approval.png)

Sie können das Muster aus diesem Beispiel mithilfe einer Orchestrierungsfunktion implementieren. Der Orchestrator verwendet einen [permanenten Timer](durable-functions-timers.md), um die Genehmigung anzufordern. Der Orchestrator führt die Eskalation aus, wenn das Timeout auftritt. Der Orchestrator wartet auf ein [externes Ereignis](durable-functions-external-events.md), beispielsweise eine Benachrichtigung, die durch eine Benutzerinteraktion generiert wird.

In diesen Beispielen wird ein Genehmigungsprozess erstellt, um das Muster der Benutzerinteraktion zu veranschaulichen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Rufen Sie zum Erstellen des permanenten Timers `context.CreateTimer` auf. Die Benachrichtigung wird von `context.WaitForExternalEvent` empfangen. Anschließend wird `Task.WhenAny` aufgerufen, um zu entscheiden, ob eine Eskalation erfolgt (Timeout tritt zuerst auf) oder die Genehmigung verarbeitet wird (Genehmigung wird vor dem Timeout empfangen).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Rufen Sie zum Erstellen des permanenten Timers `context.df.createTimer` auf. Die Benachrichtigung wird von `context.df.waitForExternalEvent` empfangen. Anschließend wird `context.df.Task.any` aufgerufen, um zu entscheiden, ob eine Eskalation erfolgt (Timeout tritt zuerst auf) oder die Genehmigung verarbeitet wird (Genehmigung wird vor dem Timeout empfangen).

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

Rufen Sie zum Erstellen des permanenten Timers `context.create_timer` auf. Die Benachrichtigung wird von `context.wait_for_external_event` empfangen. Anschließend wird `context.task_any` aufgerufen, um zu entscheiden, ob eine Eskalation erfolgt (Timeout tritt zuerst auf) oder die Genehmigung verarbeitet wird (Genehmigung wird vor dem Timeout empfangen).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$output = @()

$duration = New-TimeSpan -Seconds $Context.Input.Duration
$managerId = $Context.Input.ManagerId

$output += Invoke-DurableActivity -FunctionName "RequestApproval" -Input $managerId

$durableTimeoutEvent = Start-DurableTimer -Duration $duration -NoWait
$approvalEvent = Start-DurableExternalEventListener -EventName "ApprovalEvent" -NoWait

$firstEvent = Wait-DurableTask -Task @($approvalEvent, $durableTimeoutEvent) -Any

if ($approvalEvent -eq $firstEvent) {
    Stop-DurableTimerTask -Task $durableTimeoutEvent
    $output += Invoke-DurableActivity -FunctionName "ProcessApproval" -Input $approvalEvent
}
else {
    $output += Invoke-DurableActivity -FunctionName "EscalateApproval"
}

$output
```
Rufen Sie zum Erstellen des permanenten Timers `Start-DurableTimer` auf. Die Benachrichtigung wird von `Start-DurableExternalEventListener` empfangen. Anschließend wird `Wait-DurableTask` aufgerufen, um zu entscheiden, ob eine Eskalation erfolgt (Timeout tritt zuerst auf) oder die Genehmigung verarbeitet wird (Genehmigung wird vor dem Timeout empfangen).

---

Ein externer Client kann die Ereignisbenachrichtigung über die [integrierten HTTP-APIs](durable-functions-http-api.md#raise-event) an eine wartende Orchestratorfunktion senden:

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Ein Ereignis kann auch mithilfe des langlebigen Orchestrierungsclients von einer anderen Funktion in der gleichen Funktions-App ausgelöst werden:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell

Send-DurableExternalEvent -InstanceId $InstanceId -EventName "ApprovalEvent" -EventData "true"

``````

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Muster 6: Aggregator (zustandsbehaftete Entitäten)

Beim sechsten Muster geht es um Aggregierung von Ereignisdaten über einen bestimmten Zeitraum in einer einzigen, adressierbaren *Entität*. In diesem Muster können die aggregierten Daten aus mehreren Quellen stammen, in Batches geliefert werden und über lange Zeiträume verteilt sein. Der Aggregator muss möglicherweise Aktionen für Ereignisdaten durchführen, wenn er diese empfängt, und es kann sein, dass externe Daten die aggregierten Daten abfragen müssen.

![Aggregatordiagramm](./media/durable-functions-concepts/aggregator.png)

Das Schwierige an der Implementierung dieses Musters mit normalen, zustandslosen Funktionen ist die Tatsache, dass das Steuern der Parallelität zur Herausforderung wird. Sie müssen sich nicht nur um mehrere Threads kümmern, die gleichzeitig dieselben Daten anpassen, sondern Sie müssen auch sicherstellen, dass der Aggregator immer nur auf einer VM ausgeführt wird.

Sie können [dauerhafte Entitäten](durable-functions-entities.md) verwenden, um dieses Muster problemlos als einzelne Funktion zu implementieren.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Dauerhafte Entitäten können auch als Klassen in .NET modelliert werden. Dieses Modell ist bei einer festen Liste von Vorgängen hilfreich, die recht groß wird. Beim folgenden Beispiel handelt es sich um eine äquivalente Implementierung der `Counter`-Entität unter Verwendung von .NET-Klassen und -Methoden.

```csharp
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

```python
import logging
import json

import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableOrchestrationContext):

    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
        context.set_result(current_value)
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    
    context.set_state(current_value)

main = df.Entity.create(entity_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dauerhafte Entitäten werden in PowerShell derzeit nicht unterstützt.

---

Clients können *Vorgänge* mithilfe der [Entitätsclientbindung](durable-functions-bindings.md#entity-client) für eine Entity-Funktion in eine Warteschlange einreihen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableEntityClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Außerdem stehen dynamisch generierte Proxys in .NET für signalisierende Entitäten auf typsichere Weise zur Verfügung. Zusätzlich zur Signalisierung können Clients auch den Zustand einer Entitätsfunktion mithilfe [typsicherer Methoden](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) für die Orchestrierungsclientbindung abfragen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    entity_id = df.EntityId("Counter", "myCounter")
    instance_id = await client.signal_entity(entity_id, "add", 1)
    return func.HttpResponse("Entity signaled")
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dauerhafte Entitäten werden in PowerShell derzeit nicht unterstützt.

---

Entitätsfunktionen stehen ab [Durable Functions 2.0](durable-functions-versions.md) für C#, JavaScript und Python zur Verfügung.

## <a name="the-technology"></a>Die Technologie

Im Hintergrund baut die Durable Functions-Erweiterung auf dem [Durable Task Framework](https://github.com/Azure/durabletask) auf, einer Open-Source-Bibliothek auf GitHub, die zum Erstellen von Workflows im Code verwendet wird. Wie Azure Functions die serverlose Weiterentwicklung von Azure WebJobs ist, so ist Durable Functions die serverlose Weiterentwicklung von Durable Task Framework. Microsoft und andere Unternehmen verwenden das Durable Task Framework intensiv zum Automatisieren unternehmenswichtiger Prozesse. Es ist wie geschaffen für die serverlose Azure Functions-Umgebung.

## <a name="code-constraints"></a>Codeeinschränkungen

Damit eine zuverlässige und lange Ausführung gewährleistet ist, gelten für Orchestratorfunktionen einige Programmierregeln, die beachtet werden müssen. Weitere Informationen finden Sie im Artikel [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md).

## <a name="billing"></a>Abrechnung

Durable Functions wird genau wie Azure Functions in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/). Beim Ausführen von Orchestratorfunktionen im [Nutzungsplan](../consumption-plan.md) von Azure Functions sind einige Verhaltensweisen zu beachten, die bei der Abrechnung auftreten. Weitere Informationen zu diesen Verhaltensweisen finden Sie im Artikel [Abrechnung von Durable Functions](durable-functions-billing.md).

## <a name="jump-right-in"></a>Sofort loslegen

Sie können die ersten Schritte mit Durable Functions in weniger als 10 Minuten durchführen, indem Sie eines dieser sprachspezifischen Schnellstarttutorials abschließen:

* [C# mit Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript mit Visual Studio Code](quickstart-js-vscode.md)
* [Python unter Verwendung von Visual Studio Code](quickstart-python-vscode.md)
* [PowerShell unter Verwendung von Visual Studio Code](quickstart-powershell-vscode.md)

In diesen Schnellstarts erstellen und testen Sie lokal eine dauerhafte Funktion vom Typ „Hallo Welt“. Anschließend veröffentlichen Sie den Funktionscode in Azure. Mit der von Ihnen erstellten Funktion werden Aufrufe anderer Funktionen orchestriert und miteinander verkettet.

## <a name="publications"></a>Veröffentlichungen

Durable Functions wird in Zusammenarbeit mit Microsoft Research entwickelt. Daher erstellt das Durable Functions-Team aktiv Forschungsberichte und Artefakte, u. a.:

* [Durable Functions: Semantik für zustandsbehaftete serverlose Anwendungen](https://www.microsoft.com/en-us/research/uploads/prod/2021/10/DF-Semantics-Final.pdf) _(OOPSLA'21)_
* [Serverlose Workflows mit Durable Functions und Netherite](https://arxiv.org/pdf/2103.00033.pdf) _(Vorabdruck)_

## <a name="learn-more"></a>Weitere Informationen

Im folgenden Video werden die Vorteile von Durable Functions aufgezeigt:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Eine ausführlichere Erläuterung von Durable Functions und der zugrunde liegenden Technologie finden Sie im folgenden Video (es bezieht sich auf .NET, aber die Konzepte gelten aber auch für andere unterstützte Sprachen):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Da es sich bei Durable Functions um eine fortgeschrittene Erweiterung für [Azure Functions](../functions-overview.md) handelt, ist sie nicht für alle Anwendungen geeignet. Einen Vergleich mit anderen Azure-Orchestrierungstechnologien finden Sie unter [Vergleich zwischen Azure Functions und Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Durable Functions-Typen und -Features (Azure Functions)](durable-functions-types-features-overview.md)
