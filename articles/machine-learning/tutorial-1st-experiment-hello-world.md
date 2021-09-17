---
title: 'Tutorial: Erste Schritte mit einem Python-Skript'
titleSuffix: Azure Machine Learning
description: Erste Schritte mit Ihrem ersten Python-Skript in Azure Machine Learning. Dies ist Teil 1 einer dreiteiligen Reihe mit ersten Schritten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 04/27/2021
ms.custom: devx-track-python, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 472fcf4c7a1cc486db5aded40c87ffec2a9e796a
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634692"
---
# <a name="tutorial-get-started-with-a-python-script-in-azure-machine-learning-part-1-of-3"></a>Tutorial: Erste Schritte mit einem Python-Skript in Azure Machine Learning (Teil 1 von 3)

In diesem Tutorial führen Sie Ihr erstes Python-Skript in der Cloud mit Azure Machine Learning aus. Dieses Tutorial ist der *erste Teil einer dreiteiligen Reihe*.

In diesem Tutorial wird die Komplexität des Trainings eines Machine Learning-Modells vermieden. Sie führen das Python-Skript „Hallo Welt“ in der Cloud aus. Sie erfahren, wie ein Steuerskript verwendet wird, um eine Ausführung in Azure Machine Learning zu konfigurieren und zu erstellen.

In diesem Tutorial wird Folgendes vermittelt:

> [!div class="checklist"]
> * Lokales Ausführen eines Python-Skripts Python-Skript.
> * Erstellen eines Python-Steuerungsskripts zum Übermitteln von „Hello World!“ an Azure Machine Learning.
> * Verstehen der Azure Machine Learning Konzepte im Steuerungsskript.
> * Senden und Ausführen des Skripts „Hello World!“.
> * Anzeigen Ihrer Codeausgabe in der Cloud

## <a name="prerequisites"></a>Voraussetzungen

- Schließen Sie [Schnellstart: Einrichten Ihres Arbeitsbereichs für die ersten Schritte mit Azure Machine Learning](quickstart-create-resources.md) ab, um einen Arbeitsbereich, eine Computeinstanz und einen Computecluster für die Verwendung in dieser Tutorialreihe zu erstellen.

## <a name="create-and-run-a-python-script"></a>Erstellen und Ausführen eines Python-Skripts

In diesem Tutorial wird die Compute-Instanz als Entwicklungscomputer verwendet.  Erstellen Sie zunächst einige Ordner und das Skript:

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an und wählen Sie dann nach Aufforderung den Arbeitsbereich aus.
1. Wählen Sie links **Notebooks** aus.
1. Wählen Sie auf der Symbolleiste **Dateien** die Option **+** aus und wählen Sie dann **Neuen Ordner erstellen** aus.
  :::image type="content" source="media/tutorial-1st-experiment-hello-world/create-folder.png" alt-text="Screenshot: Erstellen eines neuen Ordnertools in der Symbolleiste.":::
1. Nennen Sie den Ordner **get-started**.
1. Verwenden Sie rechts vom Ordnernamen **...** , um unter **get-started** einen weiteren Ordner zu erstellen.
  :::image type="content" source="media/tutorial-1st-experiment-hello-world/create-sub-folder.png" alt-text="Screenshot: Menü „Unterordner erstellen“.":::
1. Nennen Sie den neuen Ordner **scr**.  Verwenden Sie den Link **Speicherort bearbeiten**, wenn der Speicherort der Datei nicht korrekt ist.
1. Verwenden Sie rechts neben dem **src**-Ordner **...** , um eine neue Datei im **src**-Ordner zu erstellen. 
1. Geben Sie ihrer Datei den Namen *hello.py*.  Ändern Sie den **Dateityp** in *Python (* .py)*.

Kopieren Sie diesen Code in Ihre Datei:

```python
# src/hello.py
print("Hello world!")
```

Ihre Ordnerverzeichnisstruktur sieht nun wie folgt aus: 

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="Ordnerstruktur mit „hello.py“ im Unterordner „src“.":::


### <a name="test-your-script"></a><a name="test"></a>Testen Sie Ihr Skript

Sie können Ihren Code lokal ausführen, in diesem Fall auf der Compute-Instanz. Das lokale Ausführen von Code hat den Vorteil, dass Sie Code interaktiv debuggen.  

Wenn Sie Ihre Compute-Instanz zuvor beendet haben, starten Sie sie jetzt mit dem **Computetool** rechts von der Compute-Dropdownliste. Warten Sie etwa eine Minute, bis der Status in *Wird ausgeführt* geändert wird.

:::image type="content" source="media/tutorial-1st-experiment-hello-world/start-compute.png" alt-text="Screenshot: Starten der Compute-Instanz, wenn sie zuvor beendet wurde":::

Wählen Sie **Skript speichern und im Terminal ausführen** aus, um das Skript auszuführen.

:::image type="content" source="media/tutorial-1st-experiment-hello-world/save-run-in-terminal.png" alt-text="Screenshot: Speichern und Ausführen eines Skripts im Terminaltool auf der Symbolleiste":::

Die Ausgabe des Skripts wird im Terminalfenster angezeigt, das geöffnet wird. Schließen Sie die Registerkarte, und wählen Sie **Beenden** aus, um die Sitzung zu schließen.

## <a name="create-a-control-script"></a><a name="control-script"></a> Erstellen eines Steuerungsskripts

Mit einem *Steuerskript* können Sie Ihr `hello.py` Skript auf verschiedenen Computeressourcen ausführen. Sie verwenden das Steuerungsskript, um zu steuern, wie und wo Ihr Machine Learning-Code ausgeführt wird.  

Wählen Sie am Ende des Ordners **get-started** die Option **...** aus, um eine neue Datei zu erstellen.  Erstellen Sie eine neue Python-Datei mit dem Namen *run-hello.py*, kopieren Sie den folgenden Code, und fügen Sie ihn in diese Datei ein:

```python
# get-started/run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

> [!TIP]
> Wenn Sie beim Erstellen ihres Computeclusters einen anderen Namen verwendet haben, stellen Sie sicher, dass Sie auch den Namen im Code `compute_target='cpu-cluster'` anpassen.

### <a name="understand-the-code"></a>Grundlegendes zum Code

Eine kurze Beschreibung zur Funktionsweise des Steuerungsskripts:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace) stellt eine Verbindung mit Ihrem Azure Machine Learning-Arbeitsbereich her, so dass Sie mit Ihren Azure Machine Learning-Ressourcen kommunizieren können.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment) bietet eine einfache Möglichkeit, mehrere Ausführungen unter einem einzelnen Namen zu ordnen. Später können Sie sehen, wie Experimente den Vergleich von Metriken zwischen Dutzenden von Ausführungen einfach machen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) umschließt Ihren `hello.py`-Code und übergibt ihn an Ihren Arbeitsbereich. Wie der Name schon nahelegt, können Sie diese Klasse verwenden, um die _Ausführung_ Ihres _Skripts_ in Azure Machine Learning zu _konfigurieren_. Außerdem wird angegeben, auf welchem Computeziel das Skript ausgeführt wird. In diesem Code ist das Ziel der Computecluster, den Sie im [Setup-Tutorial](./quickstart-create-resources.md) erstellt haben.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Übermittelt Ihr Skript. Diese Übermittlung wird als [Ausführung](/python/api/azureml-core/azureml.core.run%28class%29) bezeichnet. Eine Ausführung (Run) verkapselt eine einzelne Ausführung (Execution) Ihres Codes. Verwenden Sie eine Ausführung, um den Status Ihres Skripts zu überwachen, die Ausgabe zu erfassen, die Ergebnisse zu analysieren, die Metriken zu visualisieren und mehr.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Das `run`-Objekt stellt ein Handle für die Ausführung Ihres Codes bereit. Überwachen Sie deren Status in Azure Machine Learning Studio mit der URL, die vom Python-Skript ausgegeben wird.  
   :::column-end:::
:::row-end:::


## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Übermitteln und Ausführen Ihres Codes in der Cloud

1. Wählen Sie **Speichern und Ausführen des Skripts im Terminal** aus, um Ihr Steuerungsskript auszuführen, das seinerseits `hello.py` auf dem Computecluster ausführt, den Sie im [Setup-Tutorial](quickstart-create-resources.md) erstellt haben.

1. Im Terminal werden Sie möglicherweise aufgefordert, sich anzumelden, um sich zu authentifizieren.  Kopieren Sie den Code, und folgen Sie dem Link, um diesen Schritt abzuschließen.

1. Nachdem Sie authentifiziert wurden, wird im Terminal ein Link angezeigt. Wählen Sie den Link aus, um die Ausführung anzuzeigen.

    [!INCLUDE [amlinclude-info](../../includes/machine-learning-py38-ignore.md)]

## <a name="view-the-output"></a>Anzeigen der Ausgabe

1. Auf der Seite, die sich öffnet, sehen Sie den Status der Ausführung.
1. Wenn der Ausführungsstatus **Abgeschlossen** lautet, wählen Sie oben auf der Seite **Ausgabe und Protokolle** aus.
1. Wählen Sie **70_driver_log.txt** aus, um die Ausgabe der Ausführung anzuzeigen.

## <a name="monitor-your-code-in-the-cloud-in-the-studio"></a><a name="monitor"></a>Überwachen Ihres Codes in der Cloud in Studio

Die Ausgabe Ihres Skripts enthält einen Link zu Studio, der etwa wie folgt aussieht: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Klicken Sie auf den Link.  Zunächst sehen Sie den Status **In der Warteschlange** oder **Wird vorbereitet**.  Die erste Ausführung dauert fünf bis zehn Minuten. Dies hat folgende Ursachen:

* Ein Docker-Image wird in der Cloud erstellt.
* Die Größe des Computeclusters wird von 0 in 1 Knoten geändert.
* Das Docker-Image wird in den Computecluster heruntergeladen. 

Nachfolgende Ausführungen sind wesentlich schneller (ca. 15 Sekunden), da das Docker-Image in der Computeressource zwischengespeichert wird. Sie können dies testen, indem Sie den nachfolgenden Code nach Abschluss der ersten Ausführung erneut übermitteln.

Warten Sie ca. 10 Minuten.  Es wird eine Meldung angezeigt, dass die Ausführung abgeschlossen wurde. Verwenden Sie dann **Aktualisieren,** um die Statusänderung in *Abgeschlossen* zu ändern.  Navigieren Sie nach Abschluss des Auftrags zur Registerkarte **Ausgaben und Protokolle**. Dort sehen Sie eine Datei `70_driver_log.txt`, die wie folgt aussieht:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

In Zeile 8 sehen Sie die Ausgabe „Hallo Welt!“ .

Die Datei `70_driver_log.txt` enthält die Standardausgabe einer Ausführung. Diese Datei kann nützlich sein, wenn Sie Remoteausführungen in der Cloud debuggen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein einfaches Skript „Hello World!“ in Azure ausgeführt. Sie haben erfahren, wie Sie die Verbindung mit Ihrem Azure Machine Learning-Arbeitsbereich herstellen, ein Experiment erstellen und Ihren `hello.py`-Code in die Cloud übermitteln.

Im nächsten Tutorial bauen Sie auf dem Gelernten auf, indem Sie etwas Interessanteres als `print("Hello world!")`ausführen.

> [!div class="nextstepaction"]
> [Tutorial 1: Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Wenn Sie die Tutorialreihe hier beenden und nicht mit dem nächsten Schritt fortfahren möchten, vergessen Sie nicht, [Ihre Ressourcen zu bereinigen](tutorial-1st-experiment-bring-data.md#clean-up-resources).