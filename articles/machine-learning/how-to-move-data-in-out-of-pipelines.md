---
title: Verschieben von Daten in ML-Pipelines
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning-Pipelines Daten erfassen und wie Sie Daten verwalten und zwischen Pipelineschritten verschieben.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: f3098bcc4d26d06af53bdb7ffa1bcc01e9fef7e3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558590"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Verschieben von Daten in ML-Pipelineschritte und zwischen ML-Pipelineschritten (Python)

Dieser Artikel enthält Code zum Importieren, Transformieren und Verschieben von Daten zwischen Schritten in einer Azure Machine Learning-Pipeline. Eine Übersicht über die Funktionsweise von Daten in Azure Machine Learning finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](how-to-access-data.md). Zu den Vorteilen und der Struktur von Azure Machine Learning Pipelines siehe [Was sind Azure Machine Learning Pipelines?](concept-ml-pipelines.md)

In diesem Artikel wird Folgendes gezeigt:

- Verwenden von `Dataset`-Objekten für bereits vorhandene Daten
- Zugreifen auf Daten in Ihren Schritten
- Aufteilen von `Dataset`-Daten in Teilmengen, z. B. Teilmengen für Training und Validierung
- Erstellen von `OutputFileDatasetConfig`-Objekten zum Übertragen von Daten in den nächsten Pipelineschritt
- Verwenden von `OutputFileDatasetConfig`-Objekten als Eingabe für Pipelineschritte
- Erstellen neuer `Dataset`-Objekte aus `OutputFileDatasetConfig`, die Sie beibehalten möchten

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro), oder greifen Sie auf [Azure Machine Learning-Studio](https://ml.azure.com/) zu.

- Ein Azure Machine Learning-Arbeitsbereich.
  
  Sie können wahlweise [einen Azure Machine Learning-Arbeitsbereich erstellen](how-to-manage-workspace.md) oder mithilfe des Python SDKs einen vorhandenen verwenden. Importieren Sie die Klasse `Workspace` und `Datastore`, und laden Sie Ihre Abonnementinformationen mit der Funktion `from_config()` aus der Datei `config.json`. Diese Funktion durchsucht standardmäßig das aktuelle Verzeichnis nach der JSON-Datei. Sie können jedoch auch einen Pfadparameter angeben, um mit `from_config(path="your/file/path")` auf die Datei zu verweisen.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Einige bereits vorhandene Daten. In diesem Artikel wird kurz die Verwendung eines [Azure-Blobcontainers](../storage/blobs/storage-blobs-overview.md) erläutert.

- Optional: Eine vorhandene Pipeline für maschinelles Lernen, wie die in [Erstellen und Ausführen von Machine Learning-Pipelines mit dem Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md) beschriebene Pipeline.

## <a name="use-dataset-objects-for-pre-existing-data"></a>Verwenden von `Dataset`-Objekten für bereits vorhandene Daten 

Die bevorzugte Methode zur Erfassung von Daten in einer Pipeline ist die Verwendung eines [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29)-Objekts. `Dataset`-Objekte stellen persistente Daten dar, die im gesamten Arbeitsbereich verfügbar sind.

Es gibt viele Möglichkeiten, `Dataset`-Objekte zu erstellen und zu registrieren. Tabellendatasets sind für durch Trennzeichen getrennte Daten verfügbar, die in einer oder mehreren Dateien enthalten sind. Dateidatasets sind für Binärdaten (z. B. Images) oder für zu analysierende Daten verfügbar. Die einfachsten programmgesteuerten Möglichkeiten zum Erstellen von `Dataset`-Objekte sind die Verwendung vorhandener Blobs im Arbeitsbereichsspeicher oder öffentlicher URLs:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

Weitere Optionen zum Erstellen von Datasets mit verschiedenen Optionen und aus unterschiedlichen Quellen, zum Registrieren und Überprüfen der Daten in der Azure Machine Learning-Benutzeroberfläche, zum Verständnis, wie die Datengröße mit der Computekapazität interagiert, und zu deren Versionsverwaltung finden Sie unter [Erstellen von Azure Machine Learning-Datasets](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Übergeben von Datasets an Ihr Skript

Verwenden Sie die `as_named_input()`-Methode des `Dataset`-Objekts, um den Pfad des Datasets an Ihr Skript zu übergeben. Sie können entweder das resultierende `DatasetConsumptionConfig`-Objekt als Argument an Ihr Skript übergeben oder durch Verwendung des `inputs`-Arguments an Ihr Pipelineskript das Dataset mit `Run.get_context().input_datasets[]` abrufen.

Nachdem Sie eine benannte Eingabe erstellt haben, können Sie ihren Zugriffsmodus auswählen: `as_mount()` oder `as_download()`. Wenn Ihr Skript alle Dateien in Ihrem Dataset verarbeitet und der Datenträger auf Ihrer Compute-Ressource ausreichend groß für das Dataset ist, ist der Zugriffsmodus „Herunterladen“ die bessere Wahl. Der Zugriffsmodus „Herunterladen“ vermeidet den Mehraufwand für das Streaming der Daten zur Laufzeit. Wenn Ihr Skript auf eine Teilmenge des Datasets zugreift oder es zu groß für Ihre Berechnung ist, verwenden Sie den Zugriffsmodus „Einbinden“. Weitere Informationen finden Sie unter [Einbinden im Vergleich zum Herunterladen](./how-to-train-with-datasets.md#mount-vs-download)

So übergeben Sie ein Dataset an den Pipelineschritt:

1. Verwenden Sie `TabularDataset.as_named_input()` oder `FileDataset.as_named_input()` (kein „s“ am Ende) zum Erstellen eines `DatasetConsumptionConfig`-Objekts.
1. Verwenden Sie `as_mount()` oder `as_download()`, um den Zugriffsmodus festzulegen.
1. Übergeben Sie die Datasets mithilfe des Arguments `arguments` oder `inputs` an ihre Pipelineschritte.

Der folgende Codeausschnitt zeigt das allgemeine Muster der Kombination dieser Schritte innerhalb des Konstruktors `PythonScriptStep`: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Sie müssen die Werte aller dieser Argumente (d. h. `"train_data"`, `"train.py"`, `cluster` und `iris_dataset`) durch eigene Daten ersetzen. Der obige Ausschnitt zeigt nur die Form des Aufrufs und ist nicht Teil eines Microsoft-Beispiels. 

Sie können auch Methoden wie `random_split()` und `take_sample()` verwenden, um mehrere Eingaben zu erstellen oder die Menge der an Ihren Pipelineschritt übergebenen Daten zu reduzieren:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Zugreifen auf Datasets in Ihrem Skript

Benannte Eingaben für Ihr Pipelineschrittskript sind als Wörterbuch innerhalb des `Run`-Objekts verfügbar. Rufen Sie das aktive `Run`-Objekt mit `Run.get_context()` und anschließend das Wörterbuch der benannten Eingaben mit `input_datasets` ab. Wenn Sie das `DatasetConsumptionConfig`-Objekt mit dem `arguments`-Argument statt mit dem `inputs`-Argument übergeben haben, greifen Sie auf die Daten mit `ArgParser`-Code zu. Beide Verfahren werden im folgenden Codeausschnitt veranschaulicht.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()],
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Der übergebene Wert ist der Pfad zu der/den Datasetdatei(en).

Es ist auch möglich, direkt auf ein registriertes `Dataset` zuzugreifen. Da registrierte Datasets persistent sind und in einem Arbeitsbereich freigegeben werden, können Sie sie direkt abrufen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Die vorherigen Ausschnitte zeigen nur die Form der Aufrufe und sind nicht Teil eines Microsoft-Beispiels. Sie müssen die verschiedenen Argumente durch Werte aus Ihrem eigenen Projekt ersetzen.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Verwenden von `OutputFileDatasetConfig` für Zwischendaten

Während `Dataset`-Objekte nur persistente Daten darstellen, können [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig)-Objekte zur temporären Datenausgabe von Pipelineschritten **und** für persistente Ausgabedaten verwendet werden. `OutputFileDatasetConfig` unterstützt das Schreiben von Daten in Blob-Speicher, Dateifreigaben, adlsgen1 oder adlsgen2. Sowohl der Einbindungsmodus als auch der Hochlademodus werden unterstützt. Im Einbindungsmodus werden Dateien, die in das eingebundene Verzeichnis geschrieben werden, beim Schließen der Datei dauerhaft gespeichert. Im Hochlademodus werden Dateien, die in das Ausgabeverzeichnis geschrieben werden, zum Auftragsende hochgeladen. Wenn beim Auftrag ein Fehler auftritt oder er abgebrochen wird, wird das Ausgabeverzeichnis nicht hochgeladen.

 Das Standardverhalten des `OutputFileDatasetConfig`-Objekts besteht darin, in den Standarddatenspeicher des Arbeitsbereichs zu schreiben. Übergeben Sie die `OutputFileDatasetConfig`-Objekte mit dem `arguments`-Parameter an Ihren `PythonScriptStep`.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

> [!NOTE]
> Beim gleichzeitigen Schreiben in eine `OutputFileDatasetConfig` tritt ein Fehler auf. Versuchen Sie nicht, eine einzelne `OutputFileDatasetConfig` parallel zu verwenden. Verwenden Sie keine einzelne, gemeinsam genutzte `OutputFileDatasetConfig` in einem Szenario mit Multiprocessing, etwa für ein [verteiltes Training](how-to-train-distributed-gpu.md). 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Verwenden von `OutputFileDatasetConfig` als Ausgaben eines Trainingsschritts

Innerhalb der `PythonScriptStep` Ihrer Pipeline können Sie die verfügbaren Ausgabepfade mit den Argumenten des Programms abrufen. Wenn dieser Schritt der erste ist und die Ausgabedaten initialisieren soll, müssen Sie das Verzeichnis unter dem angegebenen Pfad erstellen. Sie können dann die Dateien schreiben, die Sie in `OutputFileDatasetConfig` aufnehmen möchten.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Lesen von `OutputFileDatasetConfig` als Eingaben für nachfolgende Schritte

Nachdem der anfängliche Pipelineschritt einige Daten in den `OutputFileDatasetConfig`-Pfad geschrieben hat und sie als Ausgabe dieses anfänglichen Schritts dienen, können die Daten als Eingabe für einen späteren Schritt verwendet werden. 

Im folgenden Code wird Folgendes ausgeführt: 

* gibt `step1_output_data` an, dass die Ausgabe von PythonScriptStep, `step1`, im Uploadzugriffsmodus in den ADLS Gen 2-Datenspeicher `my_adlsgen2` geschrieben wird. Erfahren Sie mehr über das [Einrichten von Rollenberechtigungen](how-to-access-data.md#azure-data-lake-storage-generation-2), um Daten in ADLS Gen 2-Datenspeicher zurückzuschreiben. 

* Nachdem `step1` abgeschlossen und die Ausgabe in das durch `step1_output_data` angegebene Ziel geschrieben wurde, ist step2 bereit, `step1_output_data` als Eingabe zu verwenden. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Registrieren von `OutputFileDatasetConfig`-Objekten für die Wiederverwendung

Wenn Sie Ihre `OutputFileDatasetConfig`-Objekte über die Dauer Ihres Experiments hinaus verfügbar machen möchten, registrieren Sie sie in Ihrem Arbeitsbereich, um sie Experimente übergreifend freizugeben und wiederzuverwenden.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>Löschen des Inhalts von `OutputFileDatasetConfig`, wenn er nicht mehr benötigt wird

Zwischendaten, die mit `OutputFileDatasetConfig` geschrieben werden, werden von Azure nicht automatisch gelöscht. Um Speichergebühren für große Mengen nicht benötigter Daten zu vermeiden, empfiehlt es sich, einen der folgenden Schritte auszuführen:

* Programmgesteuertes Löschen von Zwischendaten am Ende einer Pipelineausführung, wenn sie nicht mehr benötigt werden
* Verwenden von Blobspeicher mit einer kurzfristigen Speicherrichtlinie für Zwischendaten (siehe [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal)) 
* Regelmäßiges Überprüfen und Löschen nicht mehr benötigter Daten

Weitere Informationen finden Sie unter [Planen und Verwalten von Kosten für Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure Machine Learning-Datasets](how-to-create-register-datasets.md)
* [Erstellen und Ausführen von Machine Learning-Pipelines mit dem Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)