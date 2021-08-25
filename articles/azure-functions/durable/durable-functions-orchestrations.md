---
title: Dauerhafte Orchestrierungen – Azure Functions
description: Einführung in das Orchestrierungsfeature für Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 4997666c2dee5b9d71c31579682d3016df5f2738
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195632"
---
# <a name="durable-orchestrations"></a>Dauerhafte Orchestrierungen

Durable Functions ist eine Erweiterung von [Azure Functions](../functions-overview.md). Mit einer *Orchestratorfunktion* können Sie die Ausführung anderer dauerhafter Funktionen innerhalb einer Funktions-App orchestrieren. Orchestratorfunktionen verfügen über folgende Merkmale:

* Orchestratorfunktionen definieren Funktionsworkflows unter Verwendung von prozeduralem Code. Es werden keine deklarativen Schemas oder Designer benötigt.
* Orchestratorfunktionen können andere dauerhafte Funktionen synchron und asynchron aufrufen. Ausgaben aufgerufener Funktionen können zuverlässig in lokalen Variablen gespeichert werden.
* Orchestratorfunktionen sind dauerhaft und zuverlässig. Für den Ausführungsstatus werden automatisch Prüfpunkte erstellt, wenn die Funktion wartet oder angehalten wird. Der lokale Zustand geht nie verloren, wenn der Prozess oder virtuelle Computer neu gestartet wird.
* Orchestratorfunktionen können eine lange Ausführungsdauer haben. Die Gesamtlebensdauer einer *Orchestrierungsinstanz* kann Sekunden, Tage oder Monate betragen oder unbegrenzt sein.

Dieser Artikel enthält eine Übersicht über Orchestratorfunktionen und erläutert, wie sie zur Bewältigung verschiedener Herausforderungen bei der App-Entwicklung beitragen können. Sollten Sie noch nicht mit den Funktionstypen vertraut sein, die in einer Durable Functions-App zur Verfügung stehen, lesen Sie zunächst den Artikel [Durable Functions-Typen und -Features (Azure Functions)](durable-functions-types-features-overview.md).

## <a name="orchestration-identity"></a>Orchestrierungsidentität

Jede *Instanz* einer Orchestrierung verfügt über einen Instanzbezeichner (auch *Instanz-ID* genannt). Standardmäßig handelt es sich bei jeder Instanz-ID um eine automatisch generierte GUID. Eine Instanz-ID kann aber auch ein beliebiger benutzergenerierter Zeichenfolgenwert sein. Jede Orchestrierungsinstanz-ID muss innerhalb eines [Aufgabenhubs](durable-functions-task-hubs.md) eindeutig sein.

Im Anschluss folgen einige Regeln für Instanz-IDs:

* Instanz-IDs müssen zwischen einem und 256 Zeichen lang sein.
* Instanz-IDs dürfen nicht mit `@` beginnen.
* Instanz-IDs dürfen keines der folgenden Zeichen enthalten: `/`, `\`, `#` und `?`.
* Instanz-IDs dürfen keine Steuerzeichen enthalten.

> [!NOTE]
> Im Allgemeinen empfiehlt es sich, nach Möglichkeit automatisch generierte Instanz-IDs zu verwenden. Benutzergenerierte Instanz-IDs sind für Szenarien gedacht, in denen eine 1:1-Zuordnung zwischen einer Orchestrierungsinstanz und einer externen anwendungsspezifischen Entität (beispielsweise eine Bestellung oder ein Dokument) besteht.

Die Instanz-ID einer Orchestrierung ist ein erforderlicher Parameter für die meisten [Instanzverwaltungsvorgänge](durable-functions-instance-management.md). Darüber hinaus ist sie wichtig für Diagnosen – etwa beim [Durchsuchen der Nachverfolgungsdaten für die Orchestrierung](durable-functions-diagnostics.md#application-insights) in Application Insights zu Problembehandlungs- oder Analysezwecken. Aus diesem Grund empfiehlt es sich, generierte Instanz-IDs an einem externen Speicherort (etwa in einer Datenbank oder in Anwendungsprotokollen) zu speichern, sodass später problemlos auf sie verwiesen werden kann.

## <a name="reliability"></a>Zuverlässigkeit

Orchestratorfunktionen verwalten ihren Ausführungsstatus zuverlässig mithilfe eines als [Ereignissourcing](/azure/architecture/patterns/event-sourcing) bezeichneten Entwurfsmusters. Anstatt den aktuellen Zustand einer Orchestrierung direkt zu speichern, verwendet das Durable Task Framework einen ausschließlich zum Anfügen bestimmten Speicher, um die vollständige Aktionsreihe zu erfassen, die von der Funktionsorchestrierung ausgeführt wird. Ein reiner Anfügespeicher bietet viele Vorteile im Vergleich mit dem „Abladen“ des gesamten Laufzeitstatus. Zu den Vorteilen zählen die gesteigerte Leistung, Skalierbarkeit und Reaktionsfähigkeit. Sie erhalten außerdem endgültige Konsistenz von Transaktionsdaten und vollständige Überwachungspfade sowie vollständigen Verlauf. Die Überwachungspfade unterstützen zuverlässige kompensierende Aktionen.

Durable Functions verwendet Ereignissourcing transparent. Im Hintergrund gibt der `await`-Operator (C#) oder `yield`-Operator (JavaScript/Python) in einer Orchestratorfunktion die Steuerung des Orchestratorthreads an den Durable Task Framework-Verteiler zurück. Der Verteiler committet dann alle neuen Aktionen, die die Orchestratorfunktion geplant hat (z.B. Aufrufen mindestens einer untergeordneten Funktion oder Planen eines permanenten Timers) in den Speicher. Die transparente Commitaktion aktualisiert den Ausführungsverlauf der Orchestrierungsinstanz, indem alle neuen Ereignisse an den Speicher angefügt werden, ähnlich wie bei einem reinen Anfügeprotokoll. Auf ähnliche Weise erstellt die Commitaktion Nachrichten im Speicher, um die eigentlichen Aufgaben zu planen. An diesem Punkt kann die Orchestratorfunktion aus dem Arbeitsspeicher entladen werden. Standardmäßig verwendet Durable Functions Azure Storage als Laufzeitzustandsspeicher, andere [Speicheranbieter werden jedoch ebenfalls unterstützt](durable-functions-storage-providers.md).

Wenn eine Orchestrierungsfunktion weitere Aufgaben ausführen muss (z.B. wird eine Antwortnachricht empfangen, oder ein permanenter Timer läuft ab), wird der Orchestrator reaktiviert und führt erneut die gesamte Funktion von Beginn an neu aus, um den lokalen Status wiederherzustellen. Wenn der Code während dieser Wiedergabe versucht, eine Funktion aufzurufen (oder eine andere asynchrone Aktion auszuführen), zieht Durable Task Framework den Ausführungsverlauf der aktuellen Orchestrierung zu Rate. Wenn festgestellt wird, dass die [Aktivitätsfunktion](durable-functions-types-features-overview.md#activity-functions) bereits ausgeführt wurde und ein Ergebnis erbracht hat, wird dieses Funktionsergebnis wiedergegeben und der Orchestratorcode weiter ausgeführt. Die Wiedergabe wird fortgesetzt, bis der Funktionscode abgeschlossen ist oder geplante neue asynchrone Arbeit ansteht.

> [!NOTE]
> Damit das Wiedergabemuster ordnungsgemäß und zuverlässig funktioniert, muss der Orchestratorfunktionscode *deterministisch* sein. Nicht deterministischer Orchestratorcode kann zu Laufzeitfehlern oder anderem unerwarteten Verhalten führen. Weitere Informationen zu Codeeinschränkungen für Orchestratorfunktionen finden Sie in der Dokumentation zu [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md).

> [!NOTE]
> Wenn eine Orchestratorfunktion Protokollmeldungen ausgibt, kann das Wiedergabeverhalten zur Ausgabe doppelter Protokollmeldungen führen. Informationen zum Grund für dieses Verhalten sowie zur Umgehung dieses Problems finden Sie im Thema [Protokollierung](durable-functions-diagnostics.md#app-logging).

## <a name="orchestration-history"></a>Orchestrierungsverlauf

Das Ereignissourcingverhalten des Durable Task Frameworks ist eng an den von Ihnen geschriebenen Orchestratorfunktionscode gekoppelt. Angenommen, Sie verfügen über eine Orchestratorfunktion zur Verkettung von Aktivitäten wie etwa die folgende Orchestratorfunktion:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$output = @()

$output += Invoke-DurableActivity -FunctionName 'SayHello' -Input 'Tokyo'
$output += Invoke-DurableActivity -FunctionName 'SayHello' -Input 'Seattle'
$output += Invoke-DurableActivity -FunctionName 'SayHello' -Input 'London'

$output
```
---

Bei jeder `await`-Anweisung (C#) oder `yield`-Anweisung (JavaScript/Python) erstellt das Durable Task Framework einen Prüfpunkt für den Ausführungszustand der Funktion in einem dauerhaften Speicher-Back-End (standardmäßig Azure Table Storage). Dieser Zustand wird als *Orchestrierungsverlauf* bezeichnet.

### <a name="history-table"></a>Verlaufstabelle

Im Allgemeinen führt das Durable Task Framework an jedem Prüfpunkt Folgendes durch:

1. Speichert den Ausführungsverlauf in dauerhaftem Speicher.
2. Die Nachrichten für Funktionen, die der Orchestrator aufrufen möchte, werden in die Warteschlange eingereiht.
3. Nachrichten, die für den Orchestrator selbst bestimmt sind – z.B. Nachrichten des permanenten Timers –, werden in die Warteschlange eingereiht.

Nachdem der Prüfpunktvorgang abgeschlossen ist, kann die Orchestratorfunktion aus dem Arbeitsspeicher entfernt werden, bis weitere Arbeitsschritte damit ausgeführt werden müssen.

> [!NOTE]
> Azure Storage gibt keine Transaktionsgarantie für das Speichern von Daten in Tabellenspeicher und Warteschlangen. Zur Behandlung von Ausfällen nutzt der Anbieter von [Durable Functions Azure Storage](durable-functions-storage-providers.md#azure-storage) Muster vom Typ *Letztliche Konsistenz*. Mit diesen Mustern wird sichergestellt, dass keine Daten verloren gehen, wenn es zu einem Absturz kommt oder während des Prüfpunktprozesses die Verbindung getrennt wird. Alternative Speicheranbieter, z. B. der [Durable Functions MSSQL-Speicheranbieter](durable-functions-storage-providers.md#mssql), bieten möglicherweise stärkere Konsistenzgarantien.

Nach Abschluss des Vorgangs sieht der Verlauf der obigen Funktion in Azure Table Storage in etwa wie in der folgenden Tabelle aus (zur Veranschaulichung gekürzt):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Eingabe | Name             | Ergebnis                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2021-05-05T18:45:28.852Z | NULL  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2021-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2021-05-05T18:45:32.67Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2021-05-05T18:45:32.67Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2021-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2021-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2021-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2021-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2021-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2021-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2021-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2021-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2021-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2021-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2021-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2021-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Abgeschlossen           |

Einige Hinweise zu den Spaltenwerten:

* **PartitionKey**: Enthält die Instanz-ID der Orchestrierung.
* **EventType**: Steht für den Typ des Ereignisses. Es kann sich um einen der folgenden Typen handeln:
  * **OrchestrationStarted**: Die Orchestratorfunktion wurde aus einem Wartezustand fortgesetzt oder wird zum ersten Mal ausgeführt. Die Spalte `Timestamp` wird verwendet, um den deterministischen Wert für die `CurrentUtcDateTime`- (.NET), `currentUtcDateTime`- (JavaScript) und `current_utc_datetime`-APIs (Python) aufzufüllen.
  * **ExecutionStarted**: Die Ausführung der Orchestratorfunktion wurde zum ersten Mal gestartet. Dieses Ereignis enthält auch die Funktionseingabe in der Spalte `Input`.
  * **TaskScheduled**: Eine Aktivitätsfunktion wurde geplant. Der Name der Aktivitätsfunktion wird in der Spalte `Name` erfasst.
  * **TaskCompleted**: Eine Aktivitätsfunktion wurde abgeschlossen. Das Ergebnis der Funktion ist in der Spalte `Result` enthalten.
  * **TimerCreated**: Ein permanenter Timer wurde erstellt. Die Spalte `FireAt` enthält die geplante UTC-Uhrzeit, zu der der Timer abläuft.
  * **TimerFired**: Ein permanenter Timer hat ausgelöst.
  * **EventRaised**: Ein externes Ereignis wurde an die Orchestrierungsinstanz gesendet. In der Spalte `Name` wird der Name des Ereignisses und in der Spalte `Input` die Nutzlast des Ereignisses erfasst.
  * **OrchestratorCompleted**: Die wartende Orchestratorfunktion.
  * **ContinueAsNew**: Die Orchestratorfunktion wurde abgeschlossen und mit einem neuen Zustand neu gestartet. Die Spalte `Result` enthält den Wert, der als Eingabe in der neu gestarteten Instanz verwendet wird.
  * **ExecutionCompleted**: Die Orchestratorfunktion wurde bis zum Abschluss (oder Fehler) ausgeführt. Die Ausgaben der Funktion bzw. die Fehlerdetails werden in der Spalte `Result` gespeichert.
* **Timestamp**: Der UTC-Zeitstempel des Verlaufsereignisses.
* **Name**: Der Name der Funktion, die aufgerufen wurde.
* **Eingabe**: Die Eingabe der Funktion im JSON-Format.
* **Result**: Die Ausgabe der Funktion, also ihr Rückgabewert.

> [!WARNING]
> Die Tabelle ist zwar als Debugtool nützlich, aber Sie sollten keine Abhängigkeiten dafür einrichten. Dies kann sich im Rahmen der Weiterentwicklung der Erweiterung Durable Functions ändern.

Jedes Mal, wenn die Funktion aus einem `await`- (C#) oder `yield`-Zustand (JavaScript/Python) fortgesetzt wird, führt das Durable Task Framework die Orchestratorfunktion von Grund auf neu aus. Bei jeder erneuten Ausführung wird der Ausführungsverlauf herangezogen, um zu ermitteln, ob der aktuelle asynchrone Vorgang ausgeführt wurde.  Wenn ja, gibt das Framework die Ausgabe dieses Vorgangs sofort wieder und fährt mit dem nächsten `await`- (C#) oder `yield`-Element (JavaScript/Python) fort. Dieser Prozess wird fortgesetzt, bis der gesamte Verlauf wiedergegeben wurde. Nachdem der aktuelle Verlauf wiedergegeben wurde, werden die lokalen Variablen wieder auf Ihre vorherigen Werte zurückgesetzt.

## <a name="features-and-patterns"></a>Funktionen und Muster

In den nächsten Abschnitten werden die Features und Muster von Orchestratorfunktionen beschrieben.

### <a name="sub-orchestrations"></a>Untergeordnete Orchestrierungen

Orchestratorfunktionen können nicht nur Aktivitätsfunktionen aufrufen, sondern auch andere Orchestratorfunktionen. Beispielsweise können Sie aus einer Bibliothek mit Orchestratorfunktionen eine größere Orchestrierung erstellen. Alternativ können Sie mehrere Instanzen einer Orchestratorfunktion parallel ausführen.

Weitere Informationen und Beispiele finden Sie im Artikel [Untergeordnete Orchestrierungen in Durable Functions (Azure Functions)](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Permanente Timer

Orchestrierungen können mithilfe *permanenter Timer* Verzögerungen implementieren oder die Timeoutbehandlung für asynchrone Aktionen einrichten. Verwenden Sie permanente Timer in Orchestratorfunktionen anstelle von `Thread.Sleep` und `Task.Delay` (C#) oder `setTimeout()` und `setInterval()` (JavaScript) oder `time.sleep()` (Python).

Weitere Informationen und Beispiele finden Sie im Artikel [Timer in Durable Functions (Azure Functions)](durable-functions-timers.md).

### <a name="external-events"></a>Externe Ereignisse

Orchestratorfunktionen können auf externe Ereignisse warten, um eine Orchestrierungsinstanz zu aktualisieren. Dieses Feature von Durable Functions ist oft bei der Verarbeitung von Benutzerinteraktionen oder anderer externer Rückrufe nützlich.

Weitere Informationen und Beispiele finden Sie im Artikel [Behandeln von externen Ereignissen in Durable Functions (Azure Functions)](durable-functions-external-events.md).

### <a name="error-handling"></a>Fehlerbehandlung

Orchestratorfunktionen können die Fehlerbehandlungsfeatures der Programmiersprache verwenden. Vorhandene Muster wie `try`/`catch` werden in Orchestrierungscode unterstützt.

Orchestratorfunktionen können den Aktivitäts- oder untergeordneten Orchestratorfunktionen, die sie aufrufen, auch Wiederholungsrichtlinien hinzufügen. Sollte bei einer Aktivitäts- oder untergeordneten Orchestratorfunktion ein Ausnahmefehler auftreten, kann die angegebene Wiederholungsrichtlinie die Ausführung automatisch verzögern und eine bestimmte Anzahl von Wiederholungsversuchen durchführen.

> [!NOTE]
> Im Falle eines unbehandelten Ausnahmefehlers in einer Orchestratorfunktion wird die Orchestrierungsinstanz im Zustand `Failed` beendet. Eine fehlerhafte Orchestrierungsinstanz kann nicht wiederholt werden.

Weitere Informationen und Beispiele finden Sie im Artikel [Fehlerbehandlung in Durable Functions (Azure Functions)](durable-functions-error-handling.md).

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritische Abschnitte (Durable Functions 2.x, derzeit nur .NET)

Da es sich bei Orchestrierungsinstanzen um Singlethread-Instanzen handelt, müssen Sie sich keine Gedanken über Racebedingungen *innerhalb* einer Orchestrierung machen. Racebedingung sind jedoch möglich, wenn Orchestrierungen mit externen Systemen interagieren. Um Racebedingungen bei der Interaktion mit externen Systemen entgegenzuwirken, können Orchestratorfunktionen in .NET mithilfe einer Methode vom Typ `LockAsync`*kritische Abschnitte* definieren.

Der folgende Beispielcode zeigt eine Orchestratorfunktion, die einen kritischen Abschnitt definiert. Der kritische Abschnitt wird mithilfe der Methode `LockAsync` gestartet. Diese Methode erfordert die Übergabe mindestens eines Verweises an eine [dauerhafte Entität](durable-functions-entities.md), die dauerhaft den Sperrzustand verwaltet. Der Code im kritischen Abschnitt kann jeweils nur von einer einzelnen Instanz dieser Orchestrierung ausgeführt werden.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

`LockAsync` ruft die dauerhaften Sperren ab und gibt `IDisposable` zurück, um den kritischen Abschnitt beim Verwerfen zu beenden. Dieses Ergebnis vom Typ `IDisposable` kann zusammen mit einem Block vom Typ `using` verwendet werden, um eine syntaktische Darstellung des kritischen Abschnitts zu erhalten. Wenn eine Orchestratorfunktion in einen kritischen Abschnitt eintritt, kann dieser Codeblock nur von einer einzelnen Instanz ausgeführt werden. Alle anderen Instanzen, die versuchen, in den kritischen Abschnitt einzutreten, werden blockiert, bis die vorherige Instanz den kritischen Abschnitt verlässt.

Das Feature „kritischer Abschnitt“ ist auch hilfreich, um Änderungen an dauerhaften Entitäten zu koordinieren. Weitere Informationen zu kritischen Abschnitten finden Sie im Thema [Koordination von Entitäten](durable-functions-entities.md#entity-coordination).

> [!NOTE]
> Kritische Abschnitte stehen ab Durable Functions 2.0 zur Verfügung. Aktuell wird dieses Feature nur von .NET-Orchestrierungen implementiert.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Aufrufen von HTTP-Endpunkten (Durable Functions 2.x)

Von Orchestratorfunktionen dürfen keine E/A-Vorgänge ausgeführt werden, wie unter [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md) beschrieben. Zur Umgehung dieses Problems wird der Code, der E/A-Vorgänge ausführen muss, in der Regel in eine Aktivitätsfunktion eingeschlossen. Orchestrierungen, die mit externen Systemen interagieren, verwenden häufig Aktivitätsfunktionen, um HTTP-Aufrufe durchzuführen und das Ergebnis an die Orchestrierung zurückzugeben.

# <a name="c"></a>[C#](#tab/csharp)

Zur Vereinfachung dieses gängigen Musters können Orchestratorfunktionen die Methode `CallHttpAsync` verwenden, um HTTP-APIs direkt aufzurufen.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if ((int)response.StatusCode == 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das Feature wird derzeit in PowerShell nicht unterstützt.

---

Neben der Unterstützung grundlegender Anforderungs-/Antwortmuster unterstützt die Methode die automatische Behandlung gängiger asynchroner HTTP 202-Abrufmuster sowie die Authentifizierung mit externen Diensten unter Verwendung [verwalteter Identitäten](../../active-directory/managed-identities-azure-resources/overview.md).

Weitere Informationen und ausführliche Beispiele finden Sie im Artikel [HTTP-Features](durable-functions-http-features.md).

> [!NOTE]
> Das direkte Aufrufen von HTTP-Endpunkten über Orchestratorfunktionen ist ab Durable Functions 2.0 möglich.

### <a name="passing-multiple-parameters"></a>Übergeben von mehreren Parametern

Es ist nicht möglich, mehrere Parameter direkt an eine Aktivitätsfunktion zu übergeben. Es wird empfohlen, ein Array von Objekten oder zusammengesetzte Objekte zu übergeben.

# <a name="c"></a>[C#](#tab/csharp)

In .NET können Sie auch Objekte vom Typ [ValueTuple](/dotnet/csharp/tuples) verwenden. Im folgenden Beispiel werden neue Features von [ValueTuple](/dotnet/csharp/tuples) verwendet, die mit [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples) hinzugefügt wurden:

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` -Aktivität

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` -Aktivität

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

#### <a name="orchestrator"></a>Orchestrator

```powershell
param($Context)

$output = @()

$location = @{
    City = 'Seattle'
    State  = 'WA'
}

Invoke-ActivityFunction -FunctionName 'GetWeather' -Input $location

# ...

```
#### <a name="getweather-activity"></a>`GetWeather` -Aktivität

```powershell
param($location)

"Hello $($location.City), $($location.State)!"
# ...
```
---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md)
