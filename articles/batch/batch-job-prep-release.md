---
title: Erstellen von Aufgaben zum Vorbereiten und Durchführen von Aufträgen auf Computeknoten
description: Verwenden Sie Vorbereitungs- und Freigabeaufgaben auf Auftragsebene, um Datenübertragungen auf Azure Batch-Computeknoten zu minimieren und Knoten nach Abschluss des Auftrags zu bereinigen.
ms.topic: how-to
ms.date: 09/10/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 66be694cc05655973afac088066e95c6d7033bb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659738"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Ausführen von Tasks zum Vorbereiten und Freigeben von Aufträgen auf Azure Batch-Computeknoten

 Ein Azure Batch-Auftrag erfordert häufig eine Einrichtung, bevor seine Tasks ausgeführt werden. Möglicherweise ist auch eine Wartung nach dem Auftrag erforderlich, nachdem seine Tasks abgeschlossen wurden. Sie müssen unter Umständen allgemeine Eingabedaten für die Tasks auf Ihre Serverknoten herunterladen oder Ausgabedaten der Tasks in Azure Storage hochladen, nachdem der Auftrag abgeschlossen wurde. Sie können Aufgaben vom Typ **Auftragsvorbereitung** und **Auftragsfreigabe** verwenden, um diese Vorgänge durchzuführen.

## <a name="what-are-job-preparation-and-release-tasks"></a>Was sind Aufgaben zur Auftragsvorbereitung und -freigabe?

Bevor die Aufgaben eines Auftrags ausgeführt werden, wird die Auftragsvorbereitungsaufgabe auf allen Computeknoten ausgeführt, für die die Ausführung von mindestens einer Aufgabe geplant ist. Nach Abschluss des Auftrags wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Wie bei normalen Batch-Aufgaben können Sie eine Befehlszeile angeben, die aufgerufen wird, wenn eine Aufgabe zur Auftragsvorbereitung oder -freigabe ausgeführt wird.

Aufgaben zur Auftragsvorbereitung und -freigabe verfügen über vertraute Batch-Aufgabenfunktionen, z.B. Herunterladen von Dateien ([Ressourcendateien](/dotnet/api/microsoft.azure.batch.jobpreparationtask.resourcefiles)), Ausführen mit höheren Rechten, benutzerdefinierte Umgebungsvariablen, maximale Ausführungsdauer, Anzahl von Wiederholungsversuchen und Dateiaufbewahrungsdauer.

In den folgenden Abschnitten erfahren Sie, wie Sie die Klassen [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) und [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) in der [Batch .NET](/dotnet/api/microsoft.azure.batch)-Bibliothek verwenden.

> [!TIP]
> Aufgaben zur Auftragsvorbereitung und -freigabe sind insbesondere in Umgebungen mit einem gemeinsam genutzten Pool hilfreich, in denen ein Pool mit Computeknoten zwischen Auftragsausführungen beibehalten und von vielen verschiedenen Aufträgen genutzt wird.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Verwendungsmöglichkeiten für Auftragsvorbereitungs- und Auftragsfreigabeaufgaben

Auftragsvorbereitungs- und Auftragsfreigabeaufgaben eignen sich gut für die folgenden Situationen:

- **Herunterladen gemeinsamer Taskdaten:** Batchaufträge erfordern oft einen gemeinsamen Satz von Daten als Eingabe für die Tasks des Auftrags. Bei täglich durchgeführten Berechnungen zur Risikoanalyse sind Marktdaten z.B. auftragsspezifisch, gelten aber trotzdem für alle Aufgaben im Auftrag. Diese Marktdaten, die oft mehrere GB groß sind, sollten auf jeden Computeknoten nur einmal heruntergeladen werden, sodass sie von jeder auf dem Knoten ausgeführten Aufgabe verwendet werden können. Verwenden Sie eine **Auftragsvorbereitungsaufgabe**, um diese Daten vor der Ausführung anderer Aufgaben im Auftrag auf jeden Knoten herunterzuladen.

- **Löschen von Auftrags- und Taskausgabe:** In einer Umgebung mit einem gemeinsam genutzten Pool, in der die Serverknoten eines Pools zwischen Aufträgen nicht außer Betrieb genommen werden, müssen Sie Auftragsdaten zwischen den Ausführungen unter Umständen löschen. Es kann sein, dass Sie auf den Knoten Speicherplatz sparen oder die Sicherheitsrichtlinien Ihrer Organisation einhalten müssen. Verwenden Sie einen **Auftragsfreigabetask**, um Daten zu löschen, die von einem Auftragsvorbereitungstask heruntergeladen oder während der Taskausführung generiert wurden.

- **Aufbewahren von Protokollen:** Eventuell möchten Sie eine Kopie der von den Tasks generierten Protokolle oder die von fehlerhaften Anwendungen generierten Absturzabbilddateien aufbewahren. Verwenden Sie in solchen Fällen einen **Auftragsfreigabetask**, um diese Daten zu komprimieren und in ein [Azure Storage-Konto](accounts.md#azure-storage-accounts) hochzuladen.

## <a name="job-preparation-task"></a>Auftragsvorbereitungsaufgabe

Vor der Ausführung der Tasks eines Auftrags führt Batch den Auftragsvorbereitungstask auf jedem Serverknoten aus, der zum Ausführen eines Tasks eingeplant ist. Standardmäßig wartet Batch, bis die Auftragsvorbereitungsaufgabe abgeschlossen ist, bevor die geplanten Aufgaben auf dem Knoten ausgeführt werden. Sie können den Dienst allerdings auch dahingehend konfigurieren, dass er nicht wartet. Wenn der Knoten neu gestartet wird, wird die Auftragsvorbereitungsaufgabe erneut ausgeführt. Sie können dieses Verhalten auch deaktivieren. Wenn Sie einen Auftrag mit einer Auftragsvorbereitungsaufgabe haben und eine Auftrags-Manager-Aufgabe konfiguriert ist, wird die Auftragsvorbereitungsaufgabe vor der Auftrags-Manager-Aufgabe ausgeführt, genauso wie bei allen anderen Tasks. Die Auftragsvorbereitungsaufgabe wird immer zuerst ausgeführt.

Die Auftragsvorbereitungsaufgabe wird nur auf Knoten ausgeführt, die zum Ausführen einer Aufgabe eingeplant sind. Das verhindert die unnötige Ausführung von Auftragsvorbereitungstasks auf Knoten, denen keine Tasks zugewiesen sind. Dies kann eintreten, wenn die Anzahl der Aufgaben für einen Auftrag geringer ist, als die Anzahl der Knoten in einem Pool. Dies gilt auch, wenn die [gleichzeitige Aufgabenausführung](batch-parallel-node-tasks.md) aktiviert ist. Einige Knoten bleiben unbeschäftigt, wenn die Gesamtzahl von Aufgaben geringer als die Gesamtzahl der möglichen zeitgleich ausgeführten Aufgaben ist.

> [!NOTE]
> [JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) unterscheidet sich vom [CloudPool.StartTask](/dotnet/api/microsoft.azure.batch.cloudpool.starttask) insofern, dass „JobPreparationTask“ beim Start jedes Auftrags ausgeführt wird, während „StartTask“ nur ausgeführt wird, wenn ein Computeknoten einem Pool erstmals hinzugefügt oder neu gestartet wird.

## <a name="job-release-task"></a>Auftragsfreigabeaufgabe

Nachdem ein Auftrag als abgeschlossen markiert wurde, wird der Auftragsfreigabetask auf jedem Knoten im Pool ausgeführt, der mindestens einen Task ausgeführt hat. Um einen Auftrag als abgeschlossen zu markieren, geben Sie eine Terminate-Anforderung aus. Diese Anforderung legt anschließend den Status des Auftrags auf *Wird beendet* fest, beendet alle aktiven bzw. ausgeführten Tasks im Zusammenhang mit dem Auftrag und führt den Auftragsfreigabetask aus. Danach wird der Status des Auftrags in *Abgeschlossen* geändert.

> [!NOTE]
> Beim Löschen eines Auftrags wird der Auftragsfreigabetask ebenfalls ausgeführt. Wurde ein Auftrag jedoch bereits zuvor beendet, wird die Freigabeaufgabe kein zweites Mal ausgeführt, wenn der Auftrag später gelöscht wird.

Auftragsfreigabeaufgaben können maximal 15 Minuten lang ausgeführt werden, bevor Sie vom Batch-Dienst beendet werden. Weitere Informationen finden Sie in der [Referenzdokumentation zur REST-API](/rest/api/batchservice/job/add#jobreleasetask).

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Aufgaben zur Auftragsvorbereitung und -freigabe mit Batch .NET

Zum Verwenden einer Auftragsvorbereitungsaufgabe weisen Sie der Eigenschaft [CloudJob.JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) des Auftrags ein [JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)-Objekt zu. Auf ähnliche Weise initialisieren Sie einen [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) und weisen ihn dem [CloudJob.JobReleaseTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) Ihres Auftrags zu, um den Freigabetask des Auftrags zu verwenden.

In diesem Codeausschnitt ist `myBatchClient` eine Instanz von [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient), und `myPool` ist ein vorhandener Pool im Batch-Konto.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Wie oben erwähnt, wird die Freigabeaufgabe ausgeführt, wenn ein Auftrag beendet oder gelöscht wird. Beenden Sie einen Auftrag mit [JobOperations.TerminateJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.terminatejobasync). Löschen Sie einen Auftrag mit [JobOperations.DeleteJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.deletejobasync). Normalerweise beenden oder löschen Sie einen Auftrag, wenn dessen Aufgaben abgeschlossen sind oder wenn eine Zeitüberschreitung erreicht wird, die Sie festgelegt haben.

```csharp
// Terminate the job to mark it as completed; this will initiate the
// job release task on any node that executed job tasks. Note that the
// job release task is also executed when a job is deleted, so you don't
// have to call Terminate if you delete jobs after task completion.

await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Codebeispiel auf GitHub

Sehen Sie sich das Beispielprojekt [JobPrepRelease](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease) auf GitHub an, um Aufgaben zur Auftragsvorbereitung und -freigabe in Aktion zu erleben. Diese Konsolenanwendung führt folgende Schritte aus:

1. Sie erstellt einen Pool mit zwei Knoten.
1. Sie erstellt einen Auftrag mit Auftragsvorbereitungs-, Auftragsfreigabe- und Standardaufgaben.
1. Sie führt die Auftragsvorbereitungsaufgabe aus, die zunächst die Knoten-ID in eine Textdatei im „freigegebenen“ Verzeichnis des Knotens schreibt.
1. Sie führt auf jedem Knoten eine Aufgabe aus, welche die Aufgaben-ID in dieselbe Textdatei schreibt.
1. Nach dem Abschluss aller Aufgaben (oder Erreichen des Timeouts) gibt sie den Inhalt der Textdatei jedes Knotens an die Konsole aus.
1. Sie führt die Auftragsfreigabeaufgabe aus, um die Datei aus dem Knoten zu löschen, wenn der Auftrag abgeschlossen ist.
1. Sie gibt die Exitcodes der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben für jeden Knoten aus, auf dem diese ausgeführt wurden.
1. Sie hält die Ausführung an, um auf die Löschbestätigung für den Auftrag und/oder den Pool zu warten.

Die Ausgabe der Beispielanwendung sieht in etwa wie folgt aus:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Die tatsächliche Ausgabe kann sich aufgrund der variablen Erstellungs- und Startzeit von Knoten in einem neuen Pool unterscheiden. (Einige Knoten sind schneller für Aufgaben bereit als andere.) Aufgrund der schnellen Ausführung der Aufgaben werden unter Umständen alle Aufgaben des Auftrags von einem einzelnen Knoten des Pools ausgeführt. In diesem Fall werden Sie feststellen, dass die Aufgaben zur Auftragsvorbereitung und -freigabe für den Knoten, die keine Aufgaben ausgeführt hat, nicht vorhanden sind.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Überprüfen von Aufgaben zur Auftragsvorbereitung und -freigabe im Azure-Portal

Sie können das [Azure-Portal](https://portal.azure.com) verwenden, um die Eigenschaften des Auftrags und seiner Tasks anzuzeigen. Nachdem Sie die Beispielanwendung ausgeführt haben, können Sie auch die durch die Tasks des Auftrags geänderte, freigegebene Textdatei herunterladen.

Der folgende Screenshot zeigt das Blatt **Vorbereitungstasks** im Azure-Portal. Navigieren Sie nach Abschluss der Aufgaben (aber noch vor dem Löschen des Auftrags und Pools) zu den Eigenschaften für *JobPrepReleaseSampleJob*, und klicken Sie auf **Vorbereitungstasks** oder **Freigabetasks**, um die entsprechenden Eigenschaften anzuzeigen.

:::image type="content" source="media/batch-job-prep-release/portal-jobprep-01.png" alt-text="Screenshot der Eigenschaften des Auftragsvorbereitungstasks im Azure-Portal":::

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Überprüfung auf Auftrags- und Taskfehler](batch-job-task-error-checking.md).
- Erfahren Sie, wie Sie [Anwendungspakete](batch-application-packages.md) verwenden, um Batch-Serverknoten auf die Taskausführung vorzubereiten.
- Erkunden Sie verschiedene Möglichkeiten zum [Kopieren von Daten und Anwendungen auf Batch-Serverknoten](batch-applications-to-pool-nodes.md).
- Erfahren Sie, wie Sie Protokolle und andere Auftrags- und Taskausgabedaten mithilfe der Bibliothek [Azure Batch File Conventions](batch-task-output.md#use-the-batch-file-conventions-library-for-net) (Azure Batch-Dateikonventionen) speichern.
