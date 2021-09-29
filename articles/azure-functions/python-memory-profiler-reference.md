---
title: Arbeitsspeicher-Profilerstellung für Python-Apps in Azure Functions
description: Erfahren Sie, wie Sie ein Profil der Arbeitsspeichernutzung für Python-Apps erstellen und den Arbeitsspeicherengpass ermitteln.
ms.topic: how-to
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f894f369fa1efdb80160761e3c58cad937708b08
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737428"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Erstellen eines Profils der Arbeitsspeichernutzung für Python-Apps in Azure Functions

Bei der Entwicklung oder nach der Bereitstellung Ihres lokalen Python-Funktions-App-Projekts in Azure sollten Sie analysieren, ob Ihre Funktionen potenzielle Arbeitsspeicherengpässe aufweisen. Solche Engpässe können die Leistung Ihrer Funktionen beeinträchtigen und zu Fehlern führen. Die folgende Anleitung zeigt Ihnen, wie Sie das Python-Paket [memory-profiler](https://pypi.org/project/memory-profiler) verwenden, das den Arbeitsspeicherverbrauch Ihrer Funktionen während der Ausführung Zeile für Zeile analysiert.

> [!NOTE]
> Die Arbeitsspeicher-Profilerstellung ist nur für die Analyse des Arbeitsspeicherbedarfs in der Entwicklungsumgebung vorgesehen. Wenden Sie den Arbeitsspeicherprofiler nicht auf Produktionsfunktions-Apps an.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Entwicklung einer Python-Funktions-App beginnen, müssen Sie die folgenden Anforderungen erfüllen:

* [Python 3.6.x oder höher](https://www.python.org/downloads/release/python-374/). Die vollständige Liste der unterstützten Python-Versionen in Azure Functions finden Sie im [Python-Entwicklerhandbuch](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

* [Visual Studio Code](https://code.visualstudio.com/) auf einer der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms) installiert.

* Ein aktives Azure-Abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Der Prozess der Arbeitsspeicher-Profilerstellung

1. Fügen Sie in Ihrer „requirements.txt“ `memory-profiler` hinzu, um sicherzustellen, dass das Paket mit Ihrer Bereitstellung gebündelt wird. Wenn Sie auf dem lokalen Computer entwickeln, können Sie [eine virtuelle Python-Umgebung aktivieren](create-first-function-cli-python.md#create-venv) und eine Paketauflösung mit `pip install -r requirements.txt` durchführen.

2. Fügen Sie in Ihrem Funktionsskript (in der Regel \_\_init\_\_.py) die folgenden Zeilen oberhalb der `main()`-Funktion hinzu. Dadurch wird sichergestellt, dass die Stammprotokollierung die Namen der untergeordneten Protokollierung meldet, damit die Protokolle der Arbeitsspeicher-Profilerstellung durch das Präfix `memory_profiler_logs` unterschieden werden können.

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Beispiel

Hier ist ein Beispiel für die Durchführung der Arbeitsspeicher-Profilerstellung auf einem asynchronen und einem synchronen HTTP-Trigger mit den Namen „HttpTriggerAsync“ und „HttpTriggerSync“. Wir erstellen eine Python-Funktions-App, die einfach GET-Anforderungen an die Startseite von Microsoft sendet.

### <a name="create-a-python-function-app"></a>Erstellen einer Python-Funktions-App

Eine Python-Funktions-App sollte die von Azure Functions vorgegebene [Ordnerstruktur](functions-reference-python.md#folder-structure) berücksichtigen. Sie sollten Azure Functions Core Tools als Gerüst des Projekts verwenden, indem Sie die folgenden Befehle ausführen:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Aktualisieren des Dateiinhalts

Die Datei „requirements.txt“ definiert die Pakete, die in unserem Projekt verwendet werden. Neben dem Azure Functions SDK und dem Arbeitsspeicherprofiler werden `aiohttp` für asynchrone HTTP-Anforderungen und `requests` für synchrone HTTP-Aufrufe eingeführt.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Außerdem müssen wir den asynchronen HTTP-Trigger `HttpTriggerAsync/__init__.py` neu schreiben und den Arbeitsspeicherprofiler, das Format der Stammprotokollierung und die Bindung des Protokollierungsstreamings konfigurieren.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Informationen zum synchronen HTTP-Trigger finden Sie im folgenden `HttpTriggerSync/__init__.py`-Codeabschnitt:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profilerstellung für Python-Funktions-Apps in lokaler Entwicklungsumgebung

Nachdem Sie alle oben genannten Änderungen vorgenommen haben, sind einige weitere Schritte erforderlich, um eine virtuelle Python-Umgebung für die Azure Functions-Runtime zu initialisieren.

1. Öffnen Sie nach Wunsch eine Windows PowerShell-Instanz oder eine beliebige Linux-Shell.
2. Erstellen Sie eine virtuelle Python-Umgebung mit `py -m venv .venv` unter Windows oder `python3 -m venv .venv` unter Linux.
3. Aktivieren Sie die virtuelle Python-Umgebung mit `.venv\Scripts\Activate.ps1` in der Windows PowerShell-Instanz oder `source .venv/bin/activate` in der Linux-Shell.
4. Wiederherstellen der Python-Abhängigkeiten mit `pip install requirements.txt`
5. Starten Sie die Azure Functions-Runtime lokal mit `func host start` Azure Functions Core Tools.
6. Senden Sie eine GET-Anforderung an `https://localhost:7071/api/HttpTriggerAsync` oder `https://localhost:7071/api/HttpTriggerSync`.
7. Es sollte ein Arbeitsspeicher-Profilerstellungs-Bericht angezeigt werden, der dem folgenden Abschnitt in Azure Functions Core Tools ähnelt.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Python-Entwicklung für Azure Functions finden Sie in den folgenden Ressourcen:

* [Python-Entwicklerhandbuch für Azure Functions](functions-reference-python.md)
* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
