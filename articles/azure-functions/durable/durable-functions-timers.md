---
title: Timer in Durable Functions – Azure
description: Es wird beschrieben, wie Sie permanente Timer in der Erweiterung Durable Functions für Azure Functions implementieren.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 4fcaf265af6eea6d9bef47858742192103777682
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349999"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timer in Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) bieten *permanente Timer* für die Verwendung in Orchestratorfunktionen zum Implementieren von Verzögerungen oder zum Einrichten von Timeouts für asynchrone Aktionen. Permanente Timer sollten in Orchestratorfunktionen anstelle von `Thread.Sleep` und `Task.Delay` (C#) bzw. `setTimeout()` und `setInterval()` (JavaScript) oder `time.sleep()` (Python) verwendet werden.

Sie erstellen einen dauerhaften Timer, indem Sie die [`CreateTimer`- (.NET)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.createtimer), die [`createTimer`- (JavaScript)](/javascript/api/durable-functions/durableorchestrationcontext#createTimer_Date_) oder die [`create_timer`- Methode (Python)](/python/api/azure-functions-durable/azure.durable_functions.durableorchestrationcontext#create-timer-fire-at--datetime-datetime-----azure-durable-functions-models-task-task) der [Orchestrierungstriggerbindung](durable-functions-bindings.md#orchestration-trigger) aufrufen. Die Methode gibt einen Task zurück, der an einem bestimmten Datum zu einer bestimmten Zeit abgeschlossen wird.

## <a name="timer-limitations"></a>Timereinschränkungen

Wenn Sie einen Timer erstellen, der um 16:30 Uhr abläuft, stellt das zugrunde liegende Durable Task Framework eine Nachricht in die Warteschlange, die nur um 16:30 Uhr sichtbar ist. Bei Ausführung im Verbrauchstarif von Azure Functions stellt der neu sichtbare Timer sicher, dass die Funktions-App auf einem entsprechenden virtuellen Computer aktiviert wird.

> [!NOTE]
> * Ab [Version 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) von Durable Extension sind dauerhafte Timer für .NET-Apps unbegrenzt. Für JavaScript-, Python- und PowerShell-Apps sowie für .NET-Apps, die frühere Versionen der Erweiterung verwenden, sind dauerhafte Timer auf sieben Tage beschränkt. Wenn Sie eine ältere Erweiterungsversion oder eine Nicht-.NET Language Runtime verwenden und eine Verzögerung von mehr als sieben Tagen benötigen, verwenden Sie die Timer-APIs in einer `while`-Schleife, um eine längere Verzögerung zu simulieren.
> * Verwenden Sie beim Berechnen der Startzeit für dauerhafte Timer immer `CurrentUtcDateTime` anstelle von `DateTime.UtcNow` in .NET oder `currentUtcDateTime` anstelle von `Date.now` oder `Date.UTC` in JavaScript. Weitere Informationen finden Sie im Artikel [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md).

## <a name="usage-for-delay"></a>Verwendung für Verzögerung

Das folgende Beispiel veranschaulicht, wie Sie permanente Timer für die Ausführungsverzögerung verwenden. Im Beispiel wird 10 Tage lang täglich eine Abrechnungsbenachrichtigung ausgegeben.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Die vorherigen C#-Beispiele gelten für Durable Functions 2.x. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const { DateTime } = require("luxon");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = DateTime.fromJSDate(context.df.currentUtcDateTime, {zone: 'utc'}).plus({ days: 1 });
        yield context.df.createTimer(deadline.toJSDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

for ($num = 0 ; $num -le 9 ; $num++){    
    $expiryTime =  New-TimeSpan -Days 1
    $timerTask = Start-DurableTimer -Duration $expiryTime
    Invoke-DurableActivity -FunctionName 'SendBillingEvent'
}
```
---

> [!WARNING]
> Vermeiden Sie Endlosschleifen in Orchestratorfunktionen. Informationen zum sicheren und effizienten Implementieren von Endlosschleifenszenarien finden Sie unter [Eternal orchestrations in Durable Functions (Azure Functions)](durable-functions-eternal-orchestrations.md) (Ewige Orchestrierungen in Durable Functions [Azure Functions]).

## <a name="usage-for-timeout"></a>Verwendung für das Timeout

In diesem Beispiel wird veranschaulicht, wie mit permanenten Timern Timeouts implementiert werden.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Die vorherigen C#-Beispiele gelten für Durable Functions 2.x. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const { DateTime } = require("luxon");

module.exports = df.orchestrator(function*(context) {
    const deadline = DateTime.fromJSDate(context.df.currentUtcDateTime, {zone: 'utc'}).plus({ seconds: 30 });

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toJSDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
param($Context)

$expiryTime =  New-TimeSpan -Seconds 30

$activityTask = Invoke-DurableActivity -FunctionName 'GetQuote'-NoWait
$timerTask = Start-DurableTimer -Duration $expiryTime -NoWait

$winner = Wait-DurableTask -Task @($activityTask, $timerTask) -Any

if ($winner -eq $activityTask) {
    Stop-DurableTaskTimer -Task $timerTask
    return $True
}
else {
    return $False
}
```

---

> [!WARNING]
> Verwenden Sie `CancellationTokenSource` (.NET), oder rufen Sie `cancel()` für den zurückgegebenen `TimerTask` auf (JavaScript), um einen permanenten Timer abzubrechen, wenn der Code nicht auf den Abschluss wartet. Das Durable Task Framework ändert den Status einer Orchestrierung nicht in „abgeschlossen“, solange nicht alle ausstehenden Vorgänge abgeschlossen oder abgebrochen werden.

Dieser Abbruchmechanismus beendet nicht die Ausführung von Aktivitätsfunktionen oder untergeordneten Orchestrierungen. Stattdessen lässt er zu, dass die Orchestratorfunktion das Ergebnis ignoriert und fortfährt. Wenn Ihre Funktions-App den Verbrauchstarif verwendet, wird Ihnen weiterhin jeglicher Zeit- und Arbeitsspeicherverbrauch der abgebrochenen Aktivitätsfunktion in Rechnung gestellt. Standardmäßig gilt für Funktionen, die im Rahmen des Verbrauchstarifs ausgeführt werden, ein Timeout von fünf Minuten. Wenn dieses Limit überschritten wird, wird der Azure Functions-Host neu gestartet, um jegliche Ausführung zu beenden und eine unkontrollierte Abrechnungssituation zu verhindern. Der [Funktionstimeout ist konfigurierbar](../functions-host-json.md#functiontimeout).

Ein ausführlicheres Beispiel zum Implementieren von Timeouts in Orchestratorfunktionen finden Sie im Artikel [Human interaction and Timeouts – Phone Verification](durable-functions-phone-verification.md) (Menschliche Interaktion und Timeouts – Telefonüberprüfung).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Behandeln von externen Ereignissen in Durable Functions (Azure Functions)](durable-functions-external-events.md)
