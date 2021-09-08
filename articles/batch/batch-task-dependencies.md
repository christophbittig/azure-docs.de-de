---
title: Erstellen von Taskabhängigkeiten zum Ausführen von Tasks
description: Erstellen Sie Tasks, die vom Abschluss anderer Tasks abhängig sind, um Vorgänge vom MapReduce-Typ und ähnliche Big Data-Workloads in Azure Batch zu verarbeiten.
ms.topic: how-to
ms.date: 06/29/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 0cd5e0bc97d37e4daf76bee66c8b4de4698b7aca
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126516"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Erstellen von Taskabhängigkeiten zum Ausführen von Tasks, die von anderen Tasks abhängen

Mit Taskabhängigkeiten in Batch erstellen Sie Tasks, deren Ausführung auf Computeknoten erst nach dem Abschluss von mindestens einem weiteren übergeordneten Task geplant wird. Beispielsweise können Sie einen Auftrag erstellen, der jedes Einzelbild eines 3D-Films mit separaten, parallelen Tasks rendert. Der letzte Task fügt die gerenderten Einzelbilder erst dann zu einem vollständigen Film zusammen, wenn alle Einzelbilder erfolgreich gerendert wurden. Anders ausgedrückt: Der letzte Task ist von den vorherigen übergeordneten Aufgaben abhängig.

Einige Szenarios, in denen Taskabhängigkeiten nützlich sind:

- Workloads vom Typ MapReduce in der Cloud.
- Aufträge, deren Datenverarbeitungstasks als gerichteter azyklischer Graph (DAG) ausgedrückt werden können.
- Prozesse vor und nach dem Rendern, bei denen jeder Task abgeschlossen sein muss, bevor der nächste Task beginnen kann.
- Alle anderen Aufträge, in denen nachgelagerte Tasks von der Ausgabe vorgelagerter Tasks abhängen.

Standardmäßig werden abhängige Tasks erst für die Ausführung eingeplant, nachdem der übergeordnete Task erfolgreich abgeschlossen wurde. Sie können optional eine [Abhängigkeitsaktion](#dependency-actions) bestimmen, um das Standardverhalten zu überschreiben und die abhängigen Tasks auch dann auszuführen, wenn im übergeordneten Task ein Fehler auftritt.

In diesem Artikel wird beschrieben, wie Sie Taskabhängigkeiten mit der [Batch-Bibliothek für .NET](/dotnet/api/microsoft.azure.batch) konfigurieren. Zuerst zeigen wir Ihnen, wie Sie die [Taskabhängigkeit in Ihren Aufträgen aktivieren](#enable-task-dependencies), danach erläutern wir, wie Sie [einen Task mit Abhängigkeiten konfigurieren](#create-dependent-tasks). Es wird auch beschrieben, wie Sie eine Abhängigkeitsaktion bestimmen, um abhängige Tasks auszuführen, wenn im übergeordneten Task ein Fehler auftritt. Zuletzt geht es um die von Batch unterstützten [Abhängigkeitsszenarien](#dependency-scenarios) .

## <a name="enable-task-dependencies"></a>Aktivieren von Taskabhängigkeiten

Um Taskabhängigkeiten in der Batch-Anwendung verwenden zu können, müssen Sie zuerst den Auftrag für die Verwendung von Taskabhängigkeiten konfigurieren. Aktivieren Sie ihn in Batch .NET für Ihr [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob)-Objekt, indem Sie die dazugehörige [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies)-Eigenschaft auf `true` festlegen:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Im vorstehenden Codeausschnitt ist „batchClient“ eine Instanz der Klasse [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="create-dependent-tasks"></a>Erstellen von abhängigen Tasks

Zum Erstellen eines Tasks, der vom Abschluss eines oder mehrerer übergeordneter Tasks abhängig ist, können Sie angeben, dass der Task von anderen Tasks abhängig ist („depends on“). Konfigurieren Sie in Batch .NET die Eigenschaft [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) mit einer Instanz der [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies)-Klasse:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Dieser Codeausschnitt erstellt einen abhängigen Task mit der Task-ID „Flowers“. Der Task „Flowers“ ist von den Tasks „Rain“ und „Sun“ abhängig. Der Task „Flowers“ wird erst für die Ausführung auf einem Computeknoten eingeplant, nachdem die Tasks „Rain“ und „Sun“ erfolgreich abgeschlossen wurden.

> [!NOTE]
> Ein Task wird als erfolgreich abgeschlossen angesehen, wenn er den Zustand abgeschlossen aufweist und der Exitcode den Wert `0` hat. In Batch .NET bedeutet dies, dass ein [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state)-Eigenschaftswert `Completed` und der [TaskExecutionInformation.ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode)-Eigenschaftswert von CloudTask `0` lautet. Informationen zum Ändern dieses Verhaltens finden Sie im Abschnitt [Abhängigkeitsaktionen](#dependency-actions).

## <a name="dependency-scenarios"></a>Abhängigkeitsszenarien

Es gibt drei grundlegende Szenarien für Abhängigkeiten von Tasks, die Sie in Azure Batch verwenden können: 1:1, 1:n und Task-ID-Bereich. Diese drei Szenarien können Sie kombinieren, um ein viertes Szenario zu schaffen, und zwar „m:n“.

| Szenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Beispiel | Abbildung |
|:---:| --- | --- |
|  [1:1](#one-to-one) |*TaskB* hängt von *TaskA* ab. <p/> *TaskB* wird erst ausgeführt, nachdem *TaskA* erfolgreich abgeschlossen wurde |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagramm, das das 1:1-Taskabhängigkeitsszenario zeigt."::: |
|  [1:n](#one-to-many) |*TaskC* hängt sowohl von *TaskA* als auch von *TaskB* ab. <p/> *TaskC* wird erst ausgeführt, nachdem *TaskA* und *TaskB* erfolgreich abgeschlossen wurden. |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagramm, das das 1:n-Taskabhängigkeitsszenario zeigt."::: |
|  [Task-ID-Bereich](#task-id-range) |*TaskD* hängt von einem Taskbereich ab. <p/> *TaskD* wird erst ausgeführt, nachdem die Tasks mit den IDs *1* bis *10* erfolgreich abgeschlossen wurden. |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagramm, das das Task-ID-Taskabhängigkeitsszenario zeigt."::: |

> [!TIP]
> Sie können **m:n**-Beziehungen erstellen, bei denen beispielsweise die Tasks C, D, E und F jeweils von den Tasks A und B abhängen. Dies ist beispielsweise für Fälle mit parallelisierter Vorverarbeitung nützlich, in denen Ihre nachgelagerten Tasks von der Ausgabe mehrerer vorgelagerter Tasks abhängig sind.
> 
> In den Beispielen in diesem Abschnitt wird ein abhängiger Task erst ausgeführt, nachdem die übergeordneten Tasks erfolgreich abgeschlossen wurden. Dieses Verhalten ist das Standardverhalten für einen abhängigen Task. Sie können einen abhängigen Task ausführen, wenn im übergeordneten Task ein Fehler auftritt, indem Sie eine [Abhängigkeitsaktion](#dependency-actions) angeben, um das Standardverhalten zu überschreiben.

### <a name="one-to-one"></a>1:1

In einer 1 : 1-Beziehung hängt ein Task vom erfolgreichen Abschluss eines übergeordneten Tasks ab. Um die Abhängigkeit zu erstellen, geben Sie eine einzelne Task-ID für die statische [TaskDependencies.OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid)-Methode an, wenn Sie die [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson)-Eigenschaft ausfüllen.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>1:n

In einer 1 : n-Beziehung hängt ein Task vom Abschluss mehrerer übergeordneter Tasks ab. Um die Abhängigkeit zu erstellen, geben Sie mehrere spezifische Task-IDs für die statische [TaskDependencies.OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids)-Methode an, wenn Sie die [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson)-Eigenschaft ausfüllen.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

> [!IMPORTANT]
> Bei der Erstellung des abhängigen Tasks tritt ein Fehler auf, wenn die kombinierte Länge der übergeordneten Task-IDs 64.000 Zeichen überschreitet. Um eine große Anzahl von übergeordneten Tasks anzugeben, sollten Sie stattdessen einen Task-ID-Bereich verwenden.

### <a name="task-id-range"></a>Task-ID-Bereich

Bei einer Abhängigkeit von einem Bereich von übergeordneten Tasks hängt ein Task vom Abschluss von Tasks ab, deren IDs innerhalb eines von Ihnen angegebenen Bereichs liegen.

Um die Abhängigkeit zu erstellen, geben Sie die IDs des ersten und letzten Tasks im Bereich für die statische [TaskDependencies.OnIdRange](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange)-Methode an, wenn Sie die [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson)-Eigenschaft ausfüllen.

> [!IMPORTANT]
> Wenn Sie Task-ID-Bereiche für Ihre Abhängigkeiten verwenden, werden nur Tasks mit IDs, die Integerwerte darstellen, durch den Bereich ausgewählt. Der Bereich `1..10` wählt z. B. somit die Aufgaben `3` und `7` aus, aber nicht `5flamingoes`.
>
> Führende Nullen haben für die Auswertung von Bereichsabhängigkeiten keine Bedeutung. Tasks mit den Zeichenfolgenbezeichnern `4`, `04` und `004` befinden sich alle *im* Bereich und werden daher alle als Task `4` behandelt, sodass der erste abgeschlossene Task die Abhängigkeit erfüllt.
>
> Damit der abhängige Task ausgeführt wird, muss jeder Task in dem Bereich die Abhängigkeit erfüllen. Dies kann durch erfolgreichen Abschluss geschehen oder durch Abschließen mit einem Fehler, der einer [Abhängigkeitsaktion](#dependency-actions) zugeordnet ist, die auf **Erfüllen** festgelegt ist.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Abhängigkeitsaktionen

Standardmäßig werden ein abhängiger Task oder eine Gruppe von Tasks erst ausgeführt, nachdem ein übergeordneter Task erfolgreich abgeschlossen wurde. In einigen Szenarios möchten Sie abhängige Tasks eventuell auch ausführen, wenn im übergeordneten Task ein Fehler auftritt. Sie können das Standardverhalten überschreiben, indem Sie eine *Abhängigkeitsaktion* angeben, die angibt, ob ein abhängiger Task ausgeführt werden kann.

Nehmen wir beispielsweise an, dass ein abhängiger Task auf Daten vom Abschluss des Upstreamtasks wartet. Wenn der Upstreamtask fehlschlägt, kann der abhängige Task möglicherweise mit älteren Daten ausgeführt werden. In diesem Fall kann eine Abhängigkeitsaktion angeben, dass der abhängige Task berechtigt ist, trotz des Fehlers im übergeordneten Task ausgeführt zu werden.

Eine Abhängigkeitsaktion basiert auf einer Beendigungsbedingung für den übergeordneten Task. Sie können eine Abhängigkeitsaktion für jede der folgenden Beendigungsbedingungen angeben:

- Wenn bei der Vorverarbeitung ein Fehler auftritt.
- Wenn beim Dateiupload ein Fehler auftritt. Wenn der Task mit einem Exitcode beendet wird, der über **ExitCodes** oder **ExitCodeRanges** angegeben wurde und dann ein Fehler beim Dateiupload auftritt, hat die durch den Exitcode angegebene Aktion Vorrang.
- Wenn der Task mit einem Exitcode beendet wird, der durch die **ExitCodes**-Eigenschaft definiert ist.
- Wenn der Task mit einem Exitcode aus einem Bereich beendet wird, der durch die **ExitCodeRanges**-Eigenschaft angegeben wird.
- Der Standardfall, wenn der Task einem nicht in **ExitCodes** oder **ExitCodeRanges** definierten Exitcode beendet wird oder wenn der Task mit einem Fehler bei der Vorverarbeitung beendet wird und die **PreProcessingError**-Eigenschaft nicht festgelegt ist oder wenn für den Task ein Fehler beim Dateiupload auftritt und die **FileUploadError**-Eigenschaft nicht festgelegt ist. 

Für .NET werden diese Bedingungen als Eigenschaften der [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions)-Klasse definiert.

Legen Sie zum Angeben einer Abhängigkeitsaktion für die [ExitOptions.DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction)-Eigenschaft der Beendigungsbedingung eine der folgenden Optionen fest:

- **Satisfy**: Gibt an, dass abhängige Tasks ausgeführt werden dürfen, wenn der übergeordnete Task mit einem angegebenen Fehler beendet wird.
- **Blockieren**: Gibt an, dass abhängige Tasks nicht zur Ausführung berechtigt sind.

Die Standardeinstellung für die **DependencyAction**-Eigenschaft ist **Satisfy** (Erfüllen) für den Exitcode 0 und **Block** (Blockieren) für alle anderen Beendigungsbedingungen.

Der folgende Codeausschnitt legt die **DependencyAction**-Eigenschaft für einen übergeordneten Task fest. Wenn der übergeordnete Task mit einem Fehler bei der Vorverarbeitung oder den angegebenen Fehlercodes beendet wird, wird der abhängige Task blockiert. Wenn der übergeordnete Task mit einem anderen Fehler ungleich 0 beendet wird, darf der abhängige Task ausgeführt werden.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Codebeispiel

Das [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies)-Beispielprojekt auf GitHub veranschaulicht Folgendes:

- Wie Sie Taskabhängigkeit für einen Auftrag aktivieren.
- Wie Sie Tasks erstellen, die von anderen Tasks abhängen.
- So führen Sie diese Tasks in einem Pool von Computeknoten aus

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Batch-Feature [Anwendungspakete](batch-application-packages.md), das eine einfache Möglichkeit zum Bereitstellen und Versionieren der Anwendungen bietet, die von Ihren Tasks auf Computeknoten ausgeführt werden.
- Erfahren Sie mehr über die [Überprüfung auf Auftrags- und Taskfehler](batch-job-task-error-checking.md).
