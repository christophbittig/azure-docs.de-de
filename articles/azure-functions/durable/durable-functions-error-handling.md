---
title: Fehlerbehandlung in Durable Functions – Azure
description: Erfahren Sie, wie Sie Fehler in der Durable Functions-Erweiterung für Azure Functions behandeln.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 8ffa633479189ad8391d4c03c59113dc957d77e6
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667133"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Fehlerbehandlung in Durable Functions (Azure Functions)

Durable Function-Orchestrierungen sind im Code implementiert und können die integrierten Funktionen zur Fehlerbehandlung der Programmiersprache nutzen. Daher gibt es eigentlich keine neuen Konzepte, die Sie bei der Einbindung der Fehlerbehandlung und Kompensierung in Ihre Orchestrierungen beachten müssen. Es gibt jedoch einige wenige Verhaltensweisen, die Sie kennen sollten:

## <a name="errors-in-activity-functions"></a>Fehler in Aktivitätsfunktionen

Jede Ausnahme, die in einer Aktivitätsfunktion ausgelöst wird, wird zurück zur Orchestratorfunktion gemarshallt und als `FunctionFailedException` ausgelöst. Sie können Code zur Fehlerbehandlung und zur Kompensierung in der Orchestratorfunktion schreiben, der Ihren Bedürfnissen entspricht.

Betrachten Sie beispielsweise die folgende Orchestratorfunktion, die Guthaben von einem Konto auf ein anderes überträgt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Die vorherigen C#-Beispiele gelten für Durable Functions 2.x. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)
$transferDetails = $Context.Input

Invoke-DurableActivity -FunctionName 'DebitAccount' -Input @{ account = transferDetails.sourceAccount; amount = transferDetails.amount }

try {
    Invoke-DurableActivity -FunctionName 'CreditAccount' -Input @{ account = transferDetails.destinationAccount; amount = transferDetails.amount }
} catch {
    Invoke-DurableActivity -FunctionName 'CreditAccount' -Input @{ account = transferDetails.sourceAccount; amount = transferDetails.amount }
}
```


---

Wenn der erste Funktionsaufruf von **CreditAccount** fehlschlägt, wird dies durch die Orchestratorfunktion kompensiert, indem die Gelder auf das Quellkonto zurücküberwiesen werden.

## <a name="automatic-retry-on-failure"></a>Automatische Wiederholung bei einem Fehler

Wenn Sie Aktivitätsfunktionen oder untergeordnete Orchestrierungsfunktionen aufrufen, können Sie eine Richtlinie für automatische Wiederholungen angeben. Im folgenden Beispiel wird versucht, eine Funktion bis zu 3-mal mit je 5 Sekunden Wartezeit zwischen den einzelnen Wiederholungsversuchen aufzurufen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Die vorherigen C#-Beispiele gelten für Durable Functions 2.x. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$retryOptions = New-DurableRetryOptions `
                    -FirstRetryInterval (New-Timespan -Seconds 5) `
                    -MaxNumberOfAttempts 3

Invoke-DurableActivity -FunctionName 'FlakyFunction' -RetryOptions $retryOptions
```

---

Der Aktivitätsfunktionsaufruf im vorherigen Beispiel nimmt einen Parameter zum Konfigurieren einer automatischen Wiederholungsrichtlinie. Es stehen mehrere Optionen zur Verfügung, um die automatische Wiederholungsrichtlinie anzupassen:

* **Maximale Anzahl von Versuchen**: Die maximale Anzahl von Versuchen. Wenn 1 festgelegt ist, wird kein Wiederholungsversuch durchgeführt.
* **Intervall für erste Wiederholung**: Die abzuwartende Zeitspanne bis zum ersten Wiederholungsversuch.
* **Backoff-Koeffizient**: Der Koeffizient, der verwendet wird, um die Rate für die Erhöhung des Backoffs zu bestimmen. Der Standardwert lautet 1.
* **Max. Wiederholungsintervall**: Die maximale Zeitspanne zwischen den Wiederholungsversuchen.
* **Timeout für Wiederholungsversuche**: Die maximale Zeitspanne für das Ausführen von Wiederholungsversuchen. Das Standardverhalten ist das Wiederholen auf unbestimmte Zeit.
* **Handle**: Es kann ein benutzerdefinierter Rückruf angegeben werden, der bestimmt, ob eine Funktion wiederholt werden soll. 

> [!NOTE]
> Benutzerdefinierte Rückrufe werden zurzeit von Durable Functions in JavaScript (`context.df.RetryOptions`) nicht unterstützt.


## <a name="function-timeouts"></a>Funktion-Timeouts

Vielleicht möchten Sie einen Funktionsaufruf innerhalb einer Orchestratorfunktion verwerfen, wenn der Vorgang zu lange dauert. Die richtige Vorgehensweise ist das Erstellen eines [durable timer](durable-functions-timers.md) (permanenter Timer) mithilfe von `context.CreateTimer` (.NET), `context.df.createTimer` (JavaScript) oder `context.create_timer` (Python) in Verbindung mit `Task.WhenAny` (.NET), `context.df.Task.any` (JavaScript) oder `context.task_any` (Python), wie im folgenden Beispiel gezeigt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Die vorherigen C#-Beispiele gelten für Durable Functions 2.x. Für Durable Functions 1.x müssen Sie `DurableOrchestrationContext` anstelle von `IDurableOrchestrationContext` verwenden. Weitere Informationen zu den Unterschieden zwischen den Versionen finden Sie im Artikel [Durable Functions-Versionen](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
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
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$expiryTime =  New-TimeSpan -Seconds 30

$activityTask = Invoke-DurableActivity -FunctionName 'FlakyFunction'-NoWait
$timerTask = Start-DurableTimer -Duration $expiryTime -NoWait

$winner = Wait-DurableTask -Task @($activityTask, $timerTask) -NoWait

if ($winner -eq $activityTask) {
    Stop-DurableTimerTask -Task $timerTask
    return $True
}
else {
    return $False
}
```

---

> [!NOTE]
> Dieser Mechanismus beendet laufende Aktivitätsausführungsfunktionen nicht. Stattdessen lässt er zu, dass die Orchestratorfunktion das Ergebnis ignoriert und fortfährt. Weitere Informationen finden Sie in der Dokumentation zu [Timern](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Nicht behandelte Ausnahmen

Fällt eine Orchestratorfunktion mit einer nicht behandelten Ausnahme aus, werden die Details der Ausnahme protokolliert, und die Instanz wird mit einem `Failed`-Status abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu „ewigen“ Orchestrierungen](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Learn how to diagnose problems (Informationen zur Diagnose von Problemen)](durable-functions-diagnostics.md)
