---
title: Protokollieren und Anzeigen von Metriken und Protokolldateien
titleSuffix: Azure Machine Learning
description: Aktivieren Sie die Protokollierung für Ihre ML-Trainingsausführungen, um Ausführungsmetriken in Echtzeit zu überwachen und Fehler und Warnungen zu diagnostizieren.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 81f05686954e35d68d93c598978e9676652fe71f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593475"
---
# <a name="log--view-metrics-and-log-files"></a>Protokollieren und Anzeigen von Metriken und Protokolldateien

Sie können sowohl mit dem standardmäßigen Python-Protokollierungspaket als auch mit den spezifischen Funktionen des Python-SDKs für Azure Machine Learning Echtzeitinformationen protokollieren. Sie können lokal protokollieren und Protokolle an Ihren Arbeitsbereich im Portal senden.

Protokolle helfen bei der Diagnose von Fehlern und Warnungen und beim Nachverfolgen von Leistungsmetriken wie Parametern und Modellleistung. In diesem Artikel erfahren Sie, wie Sie die Protokollierung in den folgenden Szenarien aktivieren:

> [!div class="checklist"]
> * Protokollieren von Ausführungsmetriken
> * Interaktive Trainingssitzungen
> * Übermitteln von Trainingsaufträgen mithilfe von ScriptRunConfig
> * Native `logging`-Einstellungen für Python
> * Protokollierung von zusätzlichen Quellen


> [!TIP]
> In diesem Artikel erfahren Sie, wie Sie den Modelltrainingsprozess überwachen. Wenn Sie sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

## <a name="data-types"></a>Datentypen

Sie können mehrere Datentypen protokollieren, einschließlich skalarer Werte, Listen, Tabellen, Images, Verzeichnisse und mehr. Weitere Informationen und Python-Codebeispiele für verschiedene Datentypen finden Sie auf der [Referenzseite für die Run-Klasse](/python/api/azureml-core/azureml.core.run%28class%29).

## <a name="logging-run-metrics"></a>Protokollieren von Ausführungsmetriken 

Verwenden Sie die folgenden Methoden in den Protokollierungs-APIs, um die Visualisierung Ihrer Metriken anzupassen. Beachten Sie die [Diensteinschränkungen](./resource-limits-quotas-capacity.md#metrics) für diese protokollierten Metriken. 

|Protokollierter Wert|Beispielcode| Formatieren im Portal|
|----|----|----|
|Protokollieren eines Arrays mit numerischen Werten| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Liniendiagramm mit nur einer Variablen|
|Protokollieren eines einzelnen numerischen Werts mit mehrfacher Verwendung desselben Metriknamens (z.B. in einer for-Schleife)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Liniendiagramm mit nur einer Variablen|
|Wiederholtes Protokollieren einer Zeile mit zwei numerischen Spalten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Liniendiagramm mit zwei Variablen|
|Protokollieren einer Tabelle mit zwei numerischen Spalten|`run.log_table(name='Sine Wave', value=sines)`|Liniendiagramm mit zwei Variablen|
|Protokollieren eines Bilds|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Verwenden Sie diese Methode, um eine Bilddatei oder einen Matplotlib-Plot für die Ausführung zu protokollieren. Diese Bilder sind in der Ausführungsaufzeichnung sicht- und vergleichbar.|

## <a name="logging-with-mlflow"></a>Protokollieren mit MLflow

Es wird empfohlen, Ihre Modelle, Metriken und Artefakte mit MLflow zu protokollieren, da es sich um eine Open Source-Lösung handelt, die eine Portierung vom lokalen Modus zur Cloud unterstützt. Die folgende Tabelle und die Codebeispiele zeigen, wie Sie MLflow verwenden, um Metriken und Artefakte aus Ihren Trainingsläufen zu protokollieren. 
[Erfahren Sie mehr über die Protokollierungsmethoden und Entwurfsmuster von MLflow](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.log_artifact).

Stellen Sie sicher, dass Sie die pip-Pakete `mlflow` und `azureml-mlflow` in Ihrem Arbeitsbereich installieren. 

```conda
pip install mlflow
pip install azureml-mlflow
```

Legen Sie den MLflow-Nachverfolgungs-URI so fest, dass er auf das Azure Machine Learning-Back-End verweist, um sicherzustellen, dass Ihre Metriken und Artefakte in Ihrem Arbeitsbereich protokolliert werden. 

```python
from azureml.core import Workspace
import mlflow
from mlflow.tracking import MlflowClient

ws = Workspace.from_config()
mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())

mlflow.create_experiment("mlflow-experiment")
mlflow.set_experiment("mlflow-experiment")
mlflow_run = mlflow.start_run()
```

|Protokollierter Wert|Beispielcode| Notizen|
|----|----|----|
|Protokollierung eines numerischen Werts (int oder float) | `mlflow.log_metric('my_metric', 1)`| |
|Protokollierung eines booleschen Werts | `mlflow.log_metric('my_metric', 0)`| 0 = True, 1 = False|
|Protokollierung einer Zeichenfolge | `mlflow.log_text('foo', 'my_string')`| Protokollierung als Artefakt|
|Protokollierung von numpy-Metriken oder PIL-Imageobjekten|`mlflow.log_image(img, 'figure.png')`||
|Protokollierung von matlotlib-Plot oder Imagedatei|` mlflow.log_figure(fig, "figure.png")`||

## <a name="view-run-metrics-via-the-sdk"></a>Anzeigen von Ausführungsmetriken über das SDK
Mit `run.get_metrics()` können Sie die Metriken eines trainierten Modells anzeigen. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping metric names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

Sie können mithilfe von MLflow anhand der Daten- und Infoeigenschaften des Ausführungsobjekts auch auf Ausführungsinformationen zugreifen. Weitere Informationen finden Sie in der Dokumentation zum [MLflow.entities.Run-Objekt](https://mlflow.org/docs/latest/python_api/mlflow.entities.html#mlflow.entities.Run). 

Nach Abschluss der Ausführung können Sie das Objekt mithilfe von MlFlowClient() abrufen.

```python
from mlflow.tracking import MlflowClient

# Use MlFlow to retrieve the run that was just completed
client = MlflowClient()
finished_mlflow_run = MlflowClient().get_run(mlflow_run.info.run_id)
```

Sie können die Metriken, Parameter und Tags für die Ausführung im Datenfeld des Ausführungsobjekts anzeigen.

```python
metrics = finished_mlflow_run.data.metrics
tags = finished_mlflow_run.data.tags
params = finished_mlflow_run.data.params
```

>[!NOTE]
> Das Metrikwörterbuch unter `mlflow.entities.Run.data.metrics` gibt nur den zuletzt protokollierten Wert für einen angegebenen Metriknamen zurück. Wenn Sie z. B. nacheinander den Wert 1, dann 2, dann 3 und dann 4 für eine Metrik mit dem Namen `sample_metric` protokollieren, enthält das Metrikwörterbuch für `sample_metric` nur den Wert 4 vorhanden.
> 
> Um alle für einen bestimmten Metriknamen protokollierten Metriken abzurufen, können Sie [`MlFlowClient.get_metric_history()`](https://www.mlflow.org/docs/latest/python_api/mlflow.tracking.html#mlflow.tracking.MlflowClient.get_metric_history) verwenden.

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-the-studio-ui"></a>Anzeigen von Ausführungsmetriken in der Studio-Benutzeroberfläche

Sie können abgeschlossene Ausführungsaufzeichnungen, einschließlich protokollierter Metriken, in [Azure Machine Learning Studio](https://ml.azure.com) durchsuchen.

Navigieren Sie zur Registerkarte **Experimente**. Um alle Ihre Ausführungen in Ihrem Arbeitsbereich experimentübergreifend anzuzeigen, wählen Sie die Registerkarte **Alle Ausführungen** aus. Sie können einen Drilldown für die Ausführungen für bestimmte Experimente ausführen, indem Sie den Experimentfilter auf der oberen Menüleiste anwenden.

Wählen Sie für die Ansicht einzelner Experimente die Registerkarte **Alle Experimente** aus. Auf dem Dashboard für die Experimentausführung können Sie nachverfolgte Metriken und Protokolle für jede Ausführung sehen. 

Sie können auch die Tabelle der Ausführungsliste bearbeiten, um mehrere Ausführungen auszuwählen und den letzten, den minimalen oder den maximalen protokollierten Wert für Ihre Ausführungen anzuzeigen. Passen Sie Ihre Diagramme an, um die protokollierten Metrikwerte und Aggregate über mehrere Ausführungen zu vergleichen. Sie können mehrere Metriken auf der y-Achse Ihres Diagramms zeichnen und Ihre x-Achse anpassen, um Ihre protokollierten Metriken zu zeichnen. 


### <a name="view-and-download-log-files-for-a-run"></a>Anzeigen und Herunterladen von Protokolldateien für eine Ausführung 

Protokolldateien sind eine wichtige Ressource zum Debuggen von Azure Machine Learning-Workloads. Nachdem Sie einen Trainingsauftrag übermittelt haben, zeigen Sie Detailinformationen zu einer bestimmten Ausführung an, um die zugehörigen Protokolle und Ausgaben anzuzeigen:  

1. Navigieren Sie zur Registerkarte **Experimente**.
1. Wählen Sie die runID für eine bestimmte Ausführung aus.
1. Wählen Sie oben auf der Seite **Ausgaben und Protokolle** aus.
2. Wählen Sie **Alle herunterladen** aus, um alle Ihre Protokolle in einen ZIP-Ordner herunterzuladen.
3. Sie können auch einzelne Protokolldateien herunterladen, indem Sie zuerst die Protokolldatei und dann **Herunterladen** auswählen.

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Screenshot des Abschnitts „Ausgaben und Protokolle“ einer Ausführung.":::

In den folgenden Tabellen werden die Inhalte der Protokolldateien in den Ordnern aufgelistet, die in diesem Abschnitt angezeigt werden.

> [!NOTE]
> Es ist nicht notwendigerweise jede Datei bei jeder Ausführung vorhanden. Beispielsweise ist die Datei „20_image_build_log*.txt“ nur vorhanden, wenn ein neues Image erstellt wird (z. B. wenn Sie die Umgebung ändern).

#### <a name="azureml-logs-folder"></a>`azureml-logs` "

|Datei  |BESCHREIBUNG  |
|---------|---------|
|20_image_build_log.txt     | Das Buildprotokoll für das Docker-Image der Trainingsumgebung, optional, einmal pro Ausführung. Nur vorhanden, wenn Sie Ihre Umgebung aktualisieren. Andernfalls wird das zwischengespeicherte Image von AML wiederverwendet. Bei erfolgreicher Ausführung enthält sie Registrierungsdetails für das entsprechende Image.         |
|55_azureml-execution-<Knoten-ID>.txt     | stdout-/stderr-Protokoll des Hosttools, einmal pro Knoten. Pullt das Image auf das Computeziel. Beachten Sie, dass dieses Protokoll nur vorhanden ist, nachdem Sie Computeressourcen geschützt haben.         |
|65_job_prep-<Knoten-ID>.txt     |   stdout-/stderr-Protokoll des Skripts zur Auftragsvorbereitung, einmal pro Knoten. Laden Sie Ihren Code auf das Computeziel und in Ihre Datenspeicher herunter (falls angefordert).       |
|70_driver_log(_x).txt      |  stdout-/stderr-Protokoll des AML-Steuerskripts und des Kundentrainingsskripts, einmal pro Prozess. **Standardausgabe Ihres Skripts. In dieser Datei werden die Protokolle Ihres Codes (z. B. print-Anweisungen) angezeigt.** In den meisten Fällen überwachen Sie hier die Protokolle.       |
|70_mpi_log.txt     |   Protokoll des MPI-Frameworks, optional, einmal pro Ausführung. Nur bei MPI-Ausführungen.   |
|75_job_post-<Knoten-ID>.txt     |  stdout-/stderr-Protokoll des Skripts zur Auftragsfreigabe, einmal pro Knoten. Senden Sie Protokolle, und geben Sie die Computeressourcen wieder in Azure frei.        |
|process_info.json      |   Zeigt an, welcher Prozess auf welchem Knoten ausgeführt wird.  |
|process_status.json      | Zeigt den Prozessstatus an, z. B., ob ein Prozess nicht gestartet wurde, ausgeführt wird oder abgeschlossen wurde.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` "

|Datei  |BESCHREIBUNG  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   Systemprotokoll für die Auftragsvorbereitung        |
|job_release_azureml.log     | Systemprotokoll für die Auftragsfreigabe        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` "

Wenn Sidecar aktiviert ist, werden die Skripts für die Auftragsvorbereitung und -freigabe im Sidecar-Container ausgeführt.  Es gibt für jeden Knoten einen Ordner. 

|Datei  |BESCHREIBUNG  |
|---------|---------|
|start_cms.txt     |  Protokoll des Prozesses, der beim Start des Sidecar-Containers gestartet wird       |
|prep_cmd.txt      |   Protokoll für ContextManagers, wird beim Ausführen von `job_prep.py` gestartet (teilweise wird der Inhalt an `azureml-logs/65-job_prep` gestreamt)       |
|release_cmd.txt     |  Protokoll für ComtextManagers, wird beim Ausführen von `job_release.py` beendet        |

#### <a name="other-folders"></a>Andere Ordner

Für Aufträge zum Trainieren auf mehreren Computeclustern sind für jede Knoten-IP-Adresse Protokolle vorhanden. Die Struktur ist bei den einzelnen Knoten identisch mit der bei Aufträgen mit einzelnen Knoten. Es gibt einen weiteren Protokolleordner für die Protokolle zur allgemeinen Ausführung und für stderr und stdout.

Azure Machine Learning protokolliert während des Trainings auch Informationen aus verschiedenen Quellen, wie z. B. aus AutoML oder dem Docker-Container, in dem der Trainingsauftrag ausgeführt wird. Viele dieser Protokolle sind nicht dokumentiert. Wenn Sie Probleme haben und sich an den Microsoft-Support wenden, können diese Protokolle möglicherweise bei der Problembehandlung verwendet werden.


## <a name="interactive-logging-session"></a>Interaktive Protokollierungssitzung

Interaktive Protokollierungssitzungen werden in der Regel in Notebook-Umgebungen verwendet. Die Methode [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) startet eine interaktive Protokollierungssitzung. Alle Metriken, die während der Sitzung protokolliert werden, werden der Ausführungsaufzeichnung im Experiment hinzugefügt. Die Methode [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) beendet die Sitzungen und markiert die Ausführung als abgeschlossen.

## <a name="scriptrun-logs"></a>ScriptRun-Protokolle

In diesem Abschnitt erfahren Sie, wie Sie Protokollierungscode innerhalb von Ausführungen hinzufügen, die beim Konfigurieren mit ScriptRunConfig erstellt werden. Sie können die Klasse [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) verwenden, um Skripts und Umgebungen für wiederholbare Ausführungen zu kapseln. Sie können diese Option auch verwenden, um ein visuelles Jupyter Notebooks-Widget zur Überwachung anzuzeigen.

Dieses Beispiel führt einen Parameter-Sweep über Alphawerte durch und erfasst die Ergebnisse mit der Methode [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Erstellen Sie ein Trainingsskript, das die Protokollierungslogik (`train.py`) enthält.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Übermitteln Sie das ```train.py```-Skript, um in einer vom Benutzer verwalteten Umgebung ausgeführt zu werden. Der gesamte Skriptordner wird zum Training übermittelt.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Der Parameter `show_output` aktiviert die ausführliche Protokollierung, mit der Sie Details aus dem Trainingsprozess sowie Informationen zu allen entfernten Ressourcen oder Computezielen anzeigen können. Verwenden Sie den folgenden Code, um die ausführliche Protokollierung zu aktivieren, wenn Sie das Experiment übermitteln.

```python
run = exp.submit(src, show_output=True)
```

Sie können denselben Parameter auch in der `wait_for_completion`-Funktion der resultierende Ausführung verwenden.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Native Python-Protokollierung

Einige Protokolle im SDK können einen Fehler enthalten, der Sie anweist, den Protokolliergrad auf DEBUG festzulegen. Um den Protokolliergrad festzulegen, fügen Sie Ihrem Skript den folgenden Code hinzu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>Weitere Protokollierungsquellen

Azure Machine Learning kann während des Trainings auch Informationen aus anderen Quellen protokollieren, etwa aus automatisierten Machine Learning-Ausführungen oder aus Docker-Containern, in denen der Auftrag ausgeführt wird. Diese Protokolle sind nicht dokumentiert, aber wenn Probleme auftreten und Sie sich an den Microsoft-Support wenden, können diese Protokolle möglicherweise bei der Problembehandlung verwendet werden.

Informationen zur Protokollierung von Metriken im Azure Machine Learning-Designer finden Sie unter [Protokollieren von Metriken im Designer](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Beispielnotebooks

Die folgenden Notebooks verdeutlichen die Konzepte in diesem Artikel:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie diese Artikel, um mehr über die Verwendung von Azure Machine Learning zu erfahren:

* Ein Beispiel für die Registrierung des besten Modells und dessen Bereitstellung finden Sie im Tutorial [Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-train-models-with-aml.md).
