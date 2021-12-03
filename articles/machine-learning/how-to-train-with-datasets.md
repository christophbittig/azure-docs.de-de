---
title: Trainieren mit Machine Learning-Datasets
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihre Daten für das Modelltraining mit Azure Machine Learning-Datasets lokal oder remote verfügbar machen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 761b78c3485adf2827e1733011d6513ed77e2ba5
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557298"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Trainieren von Modellen mit Azure Machine Learning-Datasets 

In diesem Artikel erfahren Sie, wie Sie [Azure Machine Learning-Datasets](/python/api/azureml-core/azureml.core.dataset%28class%29) zum Trainieren von Machine Learning-Modellen einsetzen.  Sie können Datasets in Ihrem Lokal- oder Remotecomputeziel verwenden, ohne sich Gedanken über Verbindungszeichenfolgen oder Datenpfade machen zu müssten. 

Azure Machine Learning-Datasets bieten eine nahtlose Integration in Azure Machine Learning-Trainingsfunktionen wie [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) und [Azure Machine Learning-Pipelines](./how-to-create-machine-learning-pipelines.md).

Wenn Sie noch nicht bereit sind, Ihre Daten für das Modelltraining verfügbar zu machen, aber Ihre Daten zur Untersuchung in Ihr Notebook laden möchten, lesen Sie unter [Untersuchen der Daten in Ihrem Dataset](how-to-create-register-datasets.md#explore-data) nach. 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Datasets zu erstellen und für das Training zu nutzen:

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

* Eine [Installation des Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install) (ab Version 1.13.0), in dem das Paket `azureml-datasets` enthalten ist.

> [!Note]
> Einige Datasetklassen sind vom Paket [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) abhängig. Für Linux-Benutzer werden diese Klassen nur unter den folgenden Distributionen unterstützt:  Red Hat Enterprise Linux, Ubuntu, Fedora und CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Nutzen von Datasets in Trainingsskripts für maschinelles Lernen

Wenn Sie strukturierte Daten besitzen, die noch nicht als Dataset registriert wurden, können Sie eine TabularDataset-Klasse erstellen und diese direkt im Trainingsskript für das Lokal- oder Remoteexperiment verwenden.

In diesem Beispiel erstellen Sie ein nicht registriertes [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)-Element und geben es als Skriptargument im [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig)-Objekt für das Training an. Wenn Sie diese TabularDataset-Klasse für andere Experimente in Ihrem Arbeitsbereich wiederverwenden möchten, finden Sie weitere Informationen unter [Registrieren von Datasets](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Erstellen eines TabularDataset-Elements

Der folgende Code erstellt ein nicht registriertes TabularDataset-Element aus einer Web-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-Objekte bieten die Möglichkeit, die TabularDataset-Daten in einen Pandas- oder Spark-Datenrahmen zu laden, damit Sie mit vertrauten Datenaufbereitungs- und Trainingsbibliotheken arbeiten können, ohne das Notebook zu verlassen.

### <a name="access-dataset-in-training-script"></a>Zugreifen auf das Dataset im Trainingsskript

Mit dem folgenden Code wird das Skriptargument `--input-data` konfiguriert, das Sie beim Konfigurieren der Trainingsausführung angeben (siehe nächster Abschnitt). Wenn das Tabellendataset als Argumentwert übergeben wird, wird dieser von Azure ML in die ID des Datasets aufgelöst. Sie können diese ID dann für den Zugriff auf das Dataset im Trainingsskript verwenden (ohne den Namen oder die ID des Datasets in Ihrem Skript hartcodieren zu müssen). Anschließend wird die [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)-Methode verwendet, um das Dataset in einen Pandas-Datenrahmen zu laden und die Datenuntersuchung und -vorbereitung vor dem Training zu vertiefen.

> [!Note]
> Wenn die ursprüngliche Datenquelle einen nicht numerischen Wert, leere Zeichenfolgen oder leere Werte enthält, werden diese Werte bei Verwendung von `to_pandas_dataframe()` durch *NULL*-Werte ersetzt.

Wenn Sie die vorbereiteten Daten aus einem In-Memory-Pandas-Datenrahmen in ein neues Dataset laden müssen, schreiben Sie die Daten in eine lokale Datei, z. B. eine Parquet-Datei, und erstellen das neue Dataset aus dieser Datei. Erfahren Sie mehr über das [Erstellen von Datasets](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Konfigurieren der Trainingsausführung

Zum Konfigurieren und Übermitteln der Trainingsausführung wird ein Objekt vom Typ [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) verwendet.

Dieser Code erstellt ein ScriptRunConfig-Objekt (`src`), das Folgendes festlegt:

* Ein Skriptverzeichnis für Ihre Skripts. Alle Dateien in dieses Verzeichnis werden zur Ausführung in die Clusterknoten hochgeladen.
* Das Trainingsskript *train_titanic.py*
* Das Eingabedataset für das Training (`titanic_ds`) als Skriptargument. Azure ML löst dies in die entsprechende ID des Datasets auf, wenn es an Ihr Skript übergeben wird.
* Das Computeziel für die Ausführung
* Die Umgebung für die Ausführung

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Einbinden von Dateien in Remotecomputezielen

Wenn Sie unstrukturierte Daten besitzen, sollten Sie eine [FileDataset](/python/api/azureml-core/azureml.data.filedataset)-Klasse erstellen und Ihre Datendateien einbinden oder herunterladen, damit diese vom Remotecomputeziel für das Training genutzt werden können. Lesen Sie nach, wann Sie Daten [einbinden oder herunterladen](#mount-vs-download) sollten, wenn Sie Remotetrainingsexperimente durchführen. 

Im folgenden Beispiel werden folgende Aktionen ausgeführt: 

* Erstellen eines FileDataset-Eingabeelements (`mnist_ds`) für Ihre Trainingsdaten
* Angeben, wohin Trainingsergebnisse geschrieben werden sollen und Höherstufen dieser Ergebnisse als FileDataset
* Einbinden des Eingabedatasets am Computeziel

> [!Note]
> Wenn Sie ein benutzerdefiniertes Docker-Basisimage verwenden, müssen Sie fuse über `apt-get install -y fuse` als Abhängigkeit für die Durchführung der Datasetinstallation installieren. Erfahren Sie, wie Sie ein [benutzerdefiniertes Buildimage erstellen](./how-to-deploy-custom-container.md).

Ein Notebookbeispiel finden Sie unter [Konfigurieren einer Trainingsausführung mit Dateneingabe und -ausgabe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

### <a name="create-a-filedataset"></a>Erstellen eines FileDataset-Elements

Im folgenden Beispiel wird ein nicht registriertes FileDataset-Element (`mnist_data`) aus Web-URLs erstellt. Dieses FileDataset-Element stellt die Eingabedaten für Ihre Trainingsausführung dar.

Erfahren Sie mehr über das [Erstellen von Datasets](how-to-create-register-datasets.md) aus anderen Quellen.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>Speicherort zum Schreiben der Trainingsausgabe

Sie können mit einem [OutputFileDatasetConfig-Objekt](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) angeben, wohin Ihre Trainingsergebnisse geschrieben werden. 

OutputFileDatasetConfig-Objekte ermöglichen Ihnen Folgendes: 

* Einbinden oder Hochladen der Ausgabe einer Ausführung in von Ihnen angegebenen Cloudspeicher
* Speichern der Ausgabe als FileDataset in den folgenden unterstützten Speichertypen:
    * Azure-Blob
    * Azure-Dateifreigabe
    * Azure Data Lake Storage Gen1 und Gen2
* Nachverfolgen der Datenherkunft zwischen Trainingsausführungen

Der folgende Code gibt an, dass Trainingsergebnisse als FileDataset im Ordner `outputdataset` im Standardblobdatenspeicher `def_blob_store` gespeichert werden soll: 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Konfigurieren der Trainingsausführung

Es wird empfohlen, das Dataset bei der Einbindung als Argument über den Parameter `arguments` des Konstruktors `ScriptRunConfig` zu übergeben. Auf diese Weise erhalten Sie den Datenpfad (Bereitstellungspunkt) im Trainingsskript über Argumente. So können Sie dasselbe Trainingsskript für lokales Debugging und Remotetraining auf jeder Cloudplattform verwenden.

Im folgenden Beispiel wird ein ScriptRunConfig-Element erstellt, das das FileDataset-Element über `arguments` übergibt. Nach dem Übermitteln der Ausführung werden Datendateien, auf die mit dem Dataset `mnist_ds` verwiesen wird, am Computeziel eingebunden, und die Trainingsergebnisse werden im angegebenen Ordner `outputdataset` im Standarddatenspeicher gespeichert.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Einfaches Trainingsskript

Das folgende Skript wird über das ScriptRunConfig-Element übermittelt. Es liest das Dataset `mnist_ds `als Eingabe und schreibt die Datei in den Ordner `outputdataset` im Standardblobdatenspeicher `def_blob_store`.

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Vergleich: Einbinden oder Herunterladen

Das Einbinden oder Herunterladen von Dateien beliebiger Formate aus Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL-Datenbank und Azure Database for PostgreSQL wird für Datasets beliebiger Formate unterstützt. 

Wenn Sie ein Dataset **einbinden**, fügen Sie die Datei, auf die das Dataset verweist, an ein Verzeichnis (Bereitstellungspunkt) an und stellen sie auf dem Computeziel zur Verfügung. Einbinden wird für Linux-basierte Computeressourcen unterstützt, einschließlich Azure Machine Learning Compute, virtuelle Computer und HDInsight. Wenn der Umfang Ihrer Daten die Größe des Computedatenträgers übersteigt, ist das Herunterladen nicht möglich. Für dieses Szenario empfehlen wir das Einbinden, da nur die von Ihrem Skript verwendeten Datendateien zum Zeitpunkt der Verarbeitung geladen werden.

Beim **Herunterladen** eines Datasets werden alle Dateien, auf die das Dataset verweist, auf das Computeziel heruntergeladen. Herunterladen wird für alle Computetypen unterstützt. Wenn Ihr Skript alle Dateien verarbeitet, auf die das Dataset verweist, und Ihr Computedatenträger über Platz für das vollständige Dataset verfügt, wird Herunterladen empfohlen, um den Mehraufwand das Streamen von Daten von Speicherdiensten zu vermeiden. Informationen zu Downloads mit mehreren Knoten finden Sie unter [Problembehandlung](#troubleshooting). 

> [!NOTE]
> Der Name des Downloadpfads darf für das Windows-Betriebssystem höchstens 255 alphanumerische Zeichen lang sein. Für das Linux-Betriebssystem darf der Name des Downloadpfads höchstens 4.096 alphanumerische Zeichen lang sein. Außerdem sollte der Dateiname (das letzte Segment des Downloadpfads) `/path/to/file/{filename}` für das Linux-Betriebssystem höchstens 255 alphanumerische Zeichen lang sein.

Der folgende Code bindet `dataset` in das temporäre Verzeichnis unter `mounted_path` ein.

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Abrufen von Datasets in Machine Learning-Skripts

Auf registrierte Datasets kann sowohl lokal als auch remote in Computeclustern wie Azure Machine Learning Compute zugegriffen werden. Um experimentübergreifend auf Ihr registriertes Dataset zuzugreifen, verwenden Sie den folgenden Code, um auf Ihren Arbeitsbereich zuzugreifen und das Dataset abzurufen, das in Ihrer zuvor übermittelten Ausführung verwendet wurde. Die Methode [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) für die Klasse `Dataset` gibt standardmäßig die neueste Version des im Arbeitsbereich registrierten Datasets zurück.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Zugreifen auf den Quellcode während des Trainings

Azure Blob Storage verfügt über eine höhere Durchsatzgeschwindigkeit als die Azure-Dateifreigabe und wird auf eine hohe Anzahl parallel gestarteter Aufträge skaliert. Aus diesem Grund empfiehlt es sich, Ausführungen so zu konfigurieren, dass für die Übertragung von Quellcodedateien Blobspeicher verwendet wird.

Im folgenden Codebeispiel wird in der Laufzeitkonfiguration angegeben, welcher Blobdatenspeicher für Quellcodeübertragungen verwendet werden soll.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook-Beispiele

+ Weitere Datasetbeispiele und -konzepte finden Sie in den [Dataset-Notebooks](https://aka.ms/dataset-tutorial).
+ Informationen zum Parametrisieren von Datasets in ML-Pipelines finden Sie [hier](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Problembehandlung

**Dataset initialization failed:  Waiting for mount point to be ready has timed out** (Fehler bei der Initialisierung des Datasets: Timeout beim Warten auf Verfügbarkeit des Bereitstellungpunkts): 
  * Wenn Sie keine Ausgangsregeln für [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) haben und `azureml-sdk>=1.12.0` verwenden, aktualisieren Sie `azureml-dataset-runtime` und die zugehörigen Abhängigkeiten auf die neueste Version für die jeweilige Nebenversion. Wenn Sie das SDK in einer Ausführung verwenden, erstellen Sie die Umgebung neu, damit sie über den neuesten Patch mit dem Fix verfügt. 
  * Wenn Sie `azureml-sdk<1.12.0` verwenden, führen Sie ein Upgrade auf die neueste Version aus.
  * Wenn Sie über Ausgangsregeln für Netzwerksicherheitsgruppen verfügen, stellen Sie sicher, dass eine Ausgangsregel vorhanden ist, die sämtlichen Datenverkehr für das Diensttag `AzureResourceMonitor` zulässt.

**Fehler bei der Datasetinitialisierung: StreamAccessException durch ThrottlingException verursacht**

Bei Dateidownloads mit mehreren Knoten versuchen möglicherweise alle Knoten, alle Dateien im Dateidataset aus dem Azure Storage-Dienst herunterzuladen. Dies führt zu einem Drosselungsfehler. Legen Sie zum Vermeiden der Drosselung zunächst die Umgebungsvariable `AZUREML_DOWNLOAD_CONCURRENCY` auf einen Wert fest, der achtmal so groß wie die Anzahl von CPU-Kernen geteilt durch die Anzahl von Knoten ist. Beim Einrichten eines Werts für diese Umgebungsvariable müssen Sie unter Umständen etwas experimentieren, daher ist die oben genannte Anleitung ein guter Ausgangspunkt.

Im folgenden Beispiel wird von 32 Kernen und 4 Knoten ausgegangen.

```python
from azureml.core.environment import Environment 
myenv = Environment(name="myenv")
myenv.environment_variables = {"AZUREML_DOWNLOAD_CONCURRENCY":64}
```

### <a name="azurefile-storage"></a>AzureFile-Speicher

**Projektdateien können nicht in das Arbeitsverzeichnis in AzureFile hochgeladen werden, da der Speicher überlastet ist**:

* Falls Sie eine Dateifreigabe für andere Workloads (beispielsweise die Datenübertragung) verwenden, empfiehlt es sich, Blobs zu verwenden, damit die Dateifreigabe für die Übermittlung von Ausführungen frei ist.

* Eine andere Option ist, die Workload auf zwei verschiedene Arbeitsbereiche aufzuteilen.

**ConfigException: Aufgrund fehlender Anmeldeinformationen konnte keine Verbindung mit AzureFileService hergestellt werden. Entweder muss ein Kontoschlüssel oder ein SAS-Token mit dem Standardblobspeicher des Arbeitsbereichs verknüpft werden.**

Führen Sie die folgenden Schritte aus, um sicherzustellen, dass Ihre Anmeldeinformationen für den Speicherzugriff mit dem Arbeitsbereich und dem zugehörigen Dateidatenspeicher verknüpft sind:

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu Ihrem Arbeitsbereich.
1. Wählen Sie auf der Seite **Übersicht** des Arbeitsbereichs den Speicherlink aus.
1. Wählen Sie auf der Speicherseite im Menü links die Option **Zugriffsschlüssel** aus. 
1. Kopieren Sie den Schlüssel.
1. Navigieren Sie zum [Azure Machine Learning-Studio](https://ml.azure.com) für Ihren Arbeitsbereich.
1. Wählen Sie im Studio den Dateidatenspeicher aus, für den Sie Authentifizierungsanmeldeinformationen angeben möchten. 
1. Wählen Sie **Authentifizierung aktualisieren** aus.
1. Fügen Sie den Schlüssel aus den vorherigen Schritten ein. 
1. Wählen Sie **Speichern** aus. 

### <a name="passing-data-as-input"></a>Übergeben von Daten als Eingabe

**TypeError: FileNotFound: Datei oder Verzeichnis nicht vorhanden**: Dieser Fehler tritt auf, wenn sich die Datei nicht an dem von Ihnen angegebenen Dateipfad befindet. Sie müssen sicherstellen, dass Ihre Verweise auf die Datei konsistent mit dem Einbindungsort des Datasets auf Ihrem Computeziel ist. Um einen deterministischen Zustand sicherzustellen, empfiehlt es sich, für die Einbindung eines Datasets an einem Computeziel den abstrakten Pfad zu verwenden. Im folgenden Code wird das Dataset beispielsweise unter dem Stammverzeichnis des Dateisystems des Computeziels eingebunden: `/tmp`. 
    
```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Wenn Sie den vorangestellten Schrägstrich („/“) nicht einschließen, müssen Sie das Arbeitsverzeichnis auf dem Computeziel als Präfix hinzufügen, z. B. `/mnt/batch/.../tmp/dataset`, um anzugeben, wo das Dataset eingebunden werden soll.


## <a name="next-steps"></a>Nächste Schritte

* [Automatisches Trainieren von Machine Learning-Modellen](how-to-configure-auto-train.md#data-source-and-format) mit TabularDatasets

* [Trainieren von Bildklassifizierungsmodellen](https://aka.ms/filedataset-samplenotebook) mit FileDatasets

* [Training mit Datasets mithilfe von Pipelines](./how-to-create-machine-learning-pipelines.md)