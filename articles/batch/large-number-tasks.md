---
title: Übermitteln einer großen Anzahl von Aufgaben an einen Batch-Auftrag
description: Lernen Sie, effizient eine sehr große Anzahl von Aufgaben in einem einzigen Azure Batch-Auftrag zu übermitteln.
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: c3b9612dd485c531c3a25bfe80758cf73c17123d
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965241"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Übermitteln einer großen Anzahl von Aufgaben an einen Batch-Auftrag

Wenn Sie umfangreiche Azure Batch-Workloads ausführen, müssen Sie unter Umständen Zehntausende, Hunderttausende oder sogar noch mehr Aufgaben an einen einzigen Auftrag übermitteln.

Dieser Artikel zeigt Ihnen, wie Sie große Mengen von Aufgaben mit wesentlich verbessertem Durchsatz an einen einzigen Batch-Auftrag übermitteln. Nachdem die Aufgaben übermittelt wurden, werden sie in die Batch-Warteschlange zur Verarbeitung in dem Pool eingereiht, den Sie für den Auftrag angegeben haben.

## <a name="use-task-collections"></a>Verwenden von Aufgabensammlungen

Wenn Sie eine große Menge an Aufgaben hinzufügen, verwenden Sie die geeigneten von den Batch APIs bereitgestellten Methoden oder Überladungen, um Aufgaben als *-Sammlung* statt einzeln hinzuzufügen. Im Allgemeinen erstellen Sie eine Aufgabensammlung, indem Sie Aufgaben definieren, während Sie eine Reihe von Eingabedateien oder -parametern für Ihren Auftrag durchlaufen.

Die maximale Größe der Aufgabensammlung, die Sie in einem einzigen Aufruf hinzufügen können, hängt von der von Ihnen verwendeten Batch-API ab.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>APIs, die Sammlungen von bis zu 100 Aufgaben ermöglichen

Diese Batch-APIs begrenzen die Sammlung auf 100 Aufgaben. Der Grenzwert kann je nach Größe der Aufgaben auch kleiner sein – z. B. wenn die Aufgaben viele Ressourcendateien oder Umgebungsvariablen umfassen.

- [REST-API](/rest/api/batchservice/task/addcollection)
- [Python-API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js-API](/javascript/api/@azure/batch/task)

Wenn Sie diese APIs verwenden, müssen Sie die Logik zum Aufteilen der Anzahl von Aufgaben bereitstellen, um den Sammlungsgrenzwert einzuhalten. Sie müssen ebenfalls die Logik zum Behandeln von Fehlern und Wiederholungen bereitstellen, falls beim Hinzufügen von Aufgaben Fehler auftreten. Wenn eine Aufgabensammlung zu groß zum Hinzufügen ist, generiert die Anforderung einen Fehler und sollte mit einer geringeren Anzahl von Aufgaben wiederholt werden.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>APIs, die Sammlungen einer größeren Anzahl von Aufgaben ermöglichen

Andere Batch-APIs unterstützen viel größere Aufgabensammlungen – die Anzahl wird nur durch die Verfügbarkeit des Arbeitsspeichers auf dem übermittelnden Client beschränkt. Diese APIs verarbeiten transparent die Unterteilung der Aufgabensammlung in „Blöcke“ für die APIs auf niedrigerer Ebene sowie die Wiederholungen, wenn beim Hinzufügen der Aufgaben ein Fehler auftritt.

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Azure Batch-CLI-Erweiterung](batch-cli-templates.md) mit Batch-CLI-Vorlagen
- [Python SDK-Erweiterung](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Erhöhen des Durchsatzes für die Aufgabenübermittlung

Das Hinzufügen einer großen Sammlung von Aufgaben zu einem Auftrag kann einige Zeit in Anspruch nehmen. Beispielsweise kann das Hinzufügen von 20.000 Tasks über die .NET-API bis zu einer Minute dauern. Je nach Batch-API und Workload können Sie den Aufgabendurchsatz verbessern, indem Sie einen oder mehrere der folgenden Werte ändern.

### <a name="task-size"></a>Aufgabengröße

Das Hinzufügen großer Aufgaben dauert länger als das Hinzufügen kleinerer Aufgaben. Um die Aufgaben in einer Sammlung zu verkleinern, können Sie die Befehlszeile für die Aufgabe vereinfachen, die Anzahl von Umgebungsvariablen reduzieren oder Anforderungen für die Aufgabenausführung effizienter verarbeiten.

Anstatt beispielsweise eine große Anzahl von Ressourcendateien zu verwenden, installieren Sie Aufgabenabhängigkeiten mit einer [Startaufgabe](jobs-and-tasks.md#start-task) im Pool, oder verwenden Sie ein [Anwendungspaket](batch-application-packages.md) oder einen [Docker-Container](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Anzahl paralleler Vorgänge

Je nach Batch-API können Sie den Durchsatz verbessern, indem Sie die Maximalzahl von Vorgängen erhöhen, die vom Batch-Client gleichzeitig durchgeführt werden. Konfigurieren Sie diese Einstellung mithilfe der Eigenschaft [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) in der .NET API oder mit dem `threads`-Parameter in Methoden wie z.B. [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) in der Batch Python SDK-Erweiterung. (Diese Eigenschaft ist im nativen Batch Python SDK nicht verfügbar.)

Standardmäßig ist diese Eigenschaft auf 1 festgelegt, Sie können diesen Wert jedoch erhöhen, um den Durchsatz von Vorgängen zu verbessern. Der Nachteil des erhöhten Durchsatzes ist der höhere Verbrauch an Netzwerkbandbreite und CPU-Leistung. Der Aufgabendurchsatz erhöht sich um das bis zu Hundertfache des `MaxDegreeOfParallelism`- oder des `threads`-Werts. In der Praxis sollten Sie die Anzahl gleichzeitiger Vorgänge auf einen Wert unter 100 festlegen.

 Die Azure Batch-CLI-Erweiterung mit Batch-Vorlagen erhöht die Anzahl gleichzeitiger Vorgänge automatisch basierend auf der Anzahl verfügbarer Kerne, aber diese Eigenschaft kann in der CLI nicht konfiguriert werden.

### <a name="http-connection-limits"></a>HTTP-Verbindungsbeschränkungen

Viele gleichzeitige HTTP-Verbindungen können die Leistung des Batch-Clients beim Hinzufügen einer großen Menge an Aufgaben drosseln. Einige APIs beschränken die Anzahl der HTTP-Verbindungen. Beim Entwicklung mit der .NET-API beispielsweise ist die Eigenschaft [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) standardmäßig auf 2 festgelegt. Es wird empfohlen, den Wert auf eine Zahl festzulegen, die etwa gleich oder größer als die Anzahl von parallelen Vorgängen ist.

## <a name="example-batch-net"></a>Beispiel: Batch .NET

Die folgenden C#-Codeausschnitte zeigen die Einstellungen, die konfiguriert werden müssen, wenn Sie eine große Anzahl von Aufgaben mithilfe der Batch-.NET-API hinzufügen.

Um den Aufgabendurchsatz zu steigern, erhöhen Sie den Wert der [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism)-Eigenschaft von [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient). Beispiel:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Fügen Sie dem Auftrag eine Aufgabensammlung hinzu, indem Sie die geeignete Überladung der [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)- oder der [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
)-Methode verwenden. Beispiel:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Beispiel: Batch-CLI-Erweiterung

Verwenden Sie die Azure Batch-CLI-Erweiterungen mit [Batch-CLI-Vorlagen](batch-cli-templates.md), um eine JSON-Aufgabenvorlagendatei zu erstellen, die eine [Aufgabenfactory](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md) enthält. Die Aufgabenfactory konfiguriert eine Sammlung verwandter Aufgaben für einen Auftrag aus einer einzelnen Aufgabendefinition.  

Im Folgenden sehen Sie ein Beispiel für eine Aufgabenvorlage für einen eindimensionalen parameterbasierten Sweepauftrag mit einer großen Anzahl von Aufgaben (in diesem Fall 250.000). Die Aufgabenbefehlszeile ist ein einfacher `echo`-Befehl.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Informationen zum Ausführen des Auftrags mit der Vorlage finden Sie unter [Verwenden von Azure Batch-CLI-Vorlagen und Dateiübertragung](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Beispiel: Batch Python SDK-Erweiterung

Um die Azure Batch Python SDK-Erweiterung zu verwenden, installieren Sie zunächst das Python SDK und die Erweiterung:

```
pip install azure-batch
pip install azure-batch-extensions
```

Nachdem Sie das Paket mit `import azext.batch as batch` importiert haben, richten Sie ein `BatchExtensionsClient`-Objekt ein, das die SDK-Erweiterung verwendet:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Erstellen Sie eine Sammlung von Aufgaben, die einem Auftrag hinzugefügt werden sollen. Beispiel:

```python
tasks = list()
# Populate the list with your tasks
...
```

Fügen Sie die Aufgabensammlung mithilfe von [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) hinzu. Legen Sie den `threads`-Parameter fest, um die Anzahl von gleichzeitigen Vorgängen zu erhöhen:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Die Batch Python SDK-Erweiterung unterstützt auch das Hinzufügen von Aufgabenparametern zu einem Auftrag mithilfe einer JSON-Spezifikation für eine Aufgabenfactory. Konfigurieren Sie beispielsweise Auftragsparameter für einen parameterbasierten Sweepauftrag, ähnlich dem im vorherigen Beispiel für die Batch-CLI-Vorlage:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Fügen Sie die Auftragsparameter zum Auftrag hinzu. Legen Sie den `threads`-Parameter fest, um die Anzahl von gleichzeitigen Vorgängen zu erhöhen:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Verwendung der Azure Batch-CLI-Erweiterung mit [Batch-CLI-Vorlagen](batch-cli-templates.md).
- Erfahren Sie mehr über die [Batch Python SDK-Erweiterung](https://pypi.org/project/azure-batch-extensions/).
- Erfahren Sie mehr über [Best Practices für Azure Batch](best-practices.md).
