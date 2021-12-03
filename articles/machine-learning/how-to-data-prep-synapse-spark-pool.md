---
title: Data Wrangling mit Apache Spark-Pools (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Apache Spark-Pools für Data Wrangling mit Azure Synapse Analytics und Azure Machine Learning anfügen und starten.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 711521554e30b2905184150fa631ce92390534dd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564383"
---
# <a name="data-wrangling-with-apache-spark-pools-preview"></a>Data Wrangling mit Apache Spark-Pools (Vorschau) 

In diesem Artikel erfahren Sie, wie Sie Data Wrangling-Aufgaben interaktiv innerhalb einer dedizierten Synapse-Sitzung, die von [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) unterstützt wird, in einem Jupyter-Notebook mithilfe des [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) ausführen. 

Wenn Sie die Verwendung von Azure Machine Learning-Pipelines bevorzugen, finden Sie weitere Informationen unter [Verwenden von Apache Spark (unterstützt von Azure Synapse Analytics) in Ihrer Machine Learning-Pipeline (Vorschau)](how-to-use-synapsesparkstep.md).

Eine Anleitung zur Verwendung von Azure Synapse Analytics mit einem Synapse-Arbeitsbereich finden Sie in der Reihe [Erste Schritte mit Azure Synapse Analytics](../synapse-analytics/get-started.md).

>[!IMPORTANT]
> Die Integration von Azure Machine Learning und Azure Synapse Analytics befindet sich in der Vorschauphase. Die in diesem Artikel vorgestellten Funktionen verwenden das Paket `azureml-synapse`, das [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktionen enthält, die sich jederzeit ändern können.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration"></a>Integration von Azure Machine Learning und Azure Synapse Analytics

Die Integration von Azure Synapse Analytics in Azure Machine Learning (Vorschau) ermöglicht Ihnen das Anfügen eines von Azure Synapse unterstützten Apache Spark-Pools für die interaktive Datenuntersuchung und -aufbereitung. Dank dieser Integration können Sie eine dedizierte Computeressource für das Data Wrangling im großen Stil innerhalb des gleichen Python-Notebooks nutzen, das Sie auch zum Trainieren Ihrer Machine Learning-Modelle verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Das [Azure Machine Learning Python SDK muss installiert sein](/python/api/overview/azure/ml/install). 

* [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md?tabs=python).

* [Erstellen eines Azure Synapse Analytics-Arbeitsbereichs im Azure-Portal](../synapse-analytics/quickstart-create-workspace.md)

* [Erstellen Sie einen Apache Spark-Pool über das Azure-Portal, Webtools oder Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

* [Konfigurieren Sie Ihre Entwicklungsumgebung](how-to-configure-environment.md) für die Installation des Azure Machine Learning SDK, oder verwenden Sie eine [Azure Machine Learning-Computeinstanz](concept-compute-instance.md#create) mit bereits installiertem SDK. 

* Installieren Sie das Paket `azureml-synapse` (Vorschau) mit dem folgenden Code:

  ```python
  pip install azureml-synapse
  ```

* Verknüpfen Sie den Azure Machine Learning-Arbeitsbereich und den Azure Synapse Analytics-Arbeitsbereich mit dem [Azure Machine Learning Python SDK](how-to-link-synapse-ml-workspaces.md#link-sdk) oder über [Azure Machine Learning Studio](how-to-link-synapse-ml-workspaces.md#link-studio).

* [Fügen Sie einen Synapse Spark-Pool](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute) als Computeziel an.

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Starten des Synapse Spark-Pools für Data Wrangling-Aufgaben

Geben Sie für die Datenaufbereitung mit dem Apache Spark-Pool zunächst den Namen der angefügten Spark Synapse- Computeressource an. Diesen Namen finden Sie über Azure Machine Learning Studio auf der Registerkarte **Angefügte Computeressourcen**. 

![Abrufen des Namens einer angefügten Computeressource](media/how-to-data-prep-synapse-spark-pool/attached-compute.png)

> [!IMPORTANT]
> Wenn Sie den Apache Spark-Pool weiterhin verwenden möchten, müssen Sie angeben, welche Computeressource in den Data Wrangling-Aufgaben verwendet werden soll. Verwenden Sie `%synapse` für einzelne Codezeilen und `%%synapse` für mehrere Zeilen. [Erfahren Sie mehr über den Magic-Befehl %synapse](/python/api/azureml-synapse/azureml.synapse.magics.remotesynapsemagics(class)).

```python
%synapse start -c SynapseSparkPoolAlias
```

Nach dem Start der Sitzung können Sie die Metadaten der Sitzung überprüfen.

```python
%synapse meta
```

Sie können eine [Azure Machine Learning-Umgebung](concept-environments.md) angeben, die während der Apache Spark-Sitzung verwendet werden soll. Nur in der Umgebung angegebene Conda-Abhängigkeiten werden wirksam. Ein Docker-Image wird nicht unterstützt.

>[!WARNING]
>  In Conda-Umgebungsabhängigkeiten angegebene Python-Abhängigkeiten werden in Apache Spark-Pools nicht unterstützt. Derzeit werden nur feste Python-Versionen unterstützt. Überprüfen Sie Ihre Python-Version, indem Sie `sys.version_info` in Ihr Skript einschließen.

Der folgende Code erstellt die-Umgebung `myenv`, in der vor Beginn der Sitzung Version 1.20.0 von `azureml-core` und Version 1.17.0 von `numpy` installiert werden. Anschließend können Sie diese Umgebung in die `start`-Anweisung der Apache Spark-Sitzung einschließen.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Geben Sie für die Datenaufbereitung mit dem Apache Spark-Pool und der benutzerdefinierten Umgebung zunächst den Namen des Apache Spark-Pools und die Umgebung an, die während der Apache Spark-Sitzung verwendet werden soll. Darüber hinaus können Sie Ihre Abonnement-ID, die Ressourcengruppe für den Machine Learning-Arbeitsbereich und den Namen des Machine Learning-Arbeitsbereichs angeben.

>[!IMPORTANT]
> Stellen Sie sicher, dass im verknüpften Synapse-Arbeitsbereich [Pakete auf Sitzungsebene zulassen](../synapse-analytics/spark/apache-spark-manage-python-packages.md#session-scoped-packages) aktiviert ist.
>
>![Aktivieren von Paketen auf Sitzungsebene](media/how-to-data-prep-synapse-spark-pool/enable-session-level-package.png)

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```

## <a name="load-data-from-storage"></a>Laden von Daten aus dem Speicher

Lesen Sie nach dem Starten der Apache Spark-Sitzung die Daten ein, die Sie aufbereiten möchten. Das Laden von Daten wird für Azure Blob Storage and Azure Data Lake Storage Gen1 und Gen2 unterstützt.

Es gibt zwei Möglichkeiten, Daten aus diesen Speicherdiensten zu laden: 

* Direktes Laden von Daten aus dem Speicher mithilfe des HDFS-Pfads (Hadoop Distributed Files System)

* Einlesen von Daten aus einem vorhandenen [Azure Machine Learning-Dataset](how-to-create-register-datasets.md)

Für den Zugriff auf diese Speicherdienste benötigen Sie Berechtigungen vom Typ **Storage-Blobdatenleser**. Wenn Sie Daten in diese Speicherdienste zurückschreiben möchten, benötigen Sie Berechtigungen vom Typ **Mitwirkender an Storage-Blobdaten**. Weitere Informationen zu Speicherberechtigungen und -rollen finden Sie [hier](../storage/blobs/assign-azure-role-data-access.md).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Laden von Daten mithilfe des HDFS-Pfads (Hadoop Distributed Files System)

Zum Laden und Einlesen von Daten aus Speicher mit dem entsprechenden HDFS-Pfad müssen Sie Ihre Anmeldeinformationen für die Datenzugriffsauthentifizierung bereithalten. Diese Anmeldeinformationen unterscheiden sich je nach Speichertyp.  

Der folgende Code veranschaulicht das Einlesen von Daten aus **Azure Blob Storage** in einen Spark-Datenrahmen mit Ihrem SAS-Token (Shared Access Signature) oder Zugriffsschlüssel: 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Der folgende Code veranschaulicht das Einlesen von Daten aus **Azure Data Lake Storage Generation 1 (ADLS Gen 1)** mit den Anmeldeinformationen Ihres Dienstprinzipals: 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Der folgende Code veranschaulicht das Einlesen von Daten aus **Azure Data Lake Storage Generation 2 (ADLS Gen 2)** mit den Anmeldeinformationen Ihres Dienstprinzipals: 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Einlesen von Daten aus registrierten Datasets

Sie können auch ein vorhandenes registriertes Dataset in Ihrem Arbeitsbereich abrufen und die Datenaufbereitung durchführen, indem Sie es in einen Spark-Datenrahmen konvertieren.

Im folgenden Beispiel wird der Arbeitsbereich authentifiziert sowie das registrierte Tabellendataset (TabularDataset) `blob_dset` abgerufen, das auf Dateien im Blobspeicher verweist, und in einen Spark-Datenrahmen konvertiert. Beim Konvertieren Ihrer Datasets in einen Spark-Datenrahmen können Sie die `pyspark`-Datenuntersuchung und -Vorbereitungsbibliotheken nutzen.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Ausführen von Data Wrangling-Aufgaben

Nach dem Abrufen und Untersuchen Ihrer Daten können Sie Data Wrangling-Aufgaben ausführen.

Der folgende Code erweitert das HDFS-Beispiel im vorherigen Abschnitt, filtert die Daten im Spark-Datenrahmen `df` basierend auf der Spalte **Survivor** und gruppiert diese Liste nach **Age**.

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Speichern von Daten im Speicher und Beenden der Spark-Sitzung

Nachdem Sie die Datenuntersuchung und -aufbereitung abgeschlossen haben, speichern Sie Ihre aufbereiteten Daten zur späteren Verwendung in Ihrem Speicherkonto in Azure.

Im folgenden Beispiel werden die aufbereiteten Daten in Azure Blob Storage zurückgeschrieben und die ursprüngliche Datei `Titanic.csv` im Verzeichnis `training_data` überschrieben. Für das Zurückschreiben in den Speicher benötigen Sie Berechtigungen vom Typ **Mitwirkender an Storage-Blobdaten**. Weitere Informationen zu Speicherberechtigungen und -rollen finden Sie [hier](../storage/blobs/assign-azure-role-data-access.md).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Wenn die Datenaufbereitung abgeschlossen ist und die aufbereiteten Daten im Speicher gespeichert wurden, beenden Sie die Verwendung Ihres Apache Spark-Pools mit dem folgenden Befehl:

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Erstellen eines Datasets zur Darstellung aufbereiteter Daten

Wenn Sie die aufbereiteten Daten für das Modelltraining nutzen möchten, stellen Sie mit einem [Azure Machine Learning-Datenspeicher](how-to-access-data.md) eine Verbindung mit dem Speicher her, und geben Sie an, welche Dateien mit einem [Azure Machine Learning-Dataset](how-to-create-register-datasets.md) verwendet werden sollen.

Für das folgende Codebeispiel gilt Folgendes:

* Es wird davon ausgegangen, dass Sie bereits einen Datenspeicher erstellt haben, der eine Verbindung mit dem Speicherdienst herstellt, in dem Sie die aufbereiteten Daten gespeichert haben.  
* Der vorhandene Datenspeicher `mydatastore` wird mit der Methode „get()“ aus dem Arbeitsbereich `ws` abgerufen.
* Das [FileDataset](how-to-create-register-datasets.md#filedataset)-Element `train_ds` wird erstellt, das auf die aufbereiteten Datendateien im Verzeichnis `training_data` in `mydatastore` verweist.  
* Die Variable `input1` wird erstellt, die später zum Verfügbarmachen des Datasets `train_ds` für ein Computeziel für Ihre Trainingsaufgaben verwendet werden kann.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Verwenden eines `ScriptRunConfig` zum Übermitteln einer Experimentausführung an einen Synapse Spark-Pool

Wenn Sie bereit sind, Ihre Data Wrangling-Aufgaben zu automatisieren und produktionsbereit zu machen, können Sie eine Experimentausführung an einen [angefügten Synapse Spark-Pool](how-to-link-synapse-ml-workspaces.md#attach-a-pool-with-the-python-sdk) mit dem Objekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) übermitteln.  

Wenn Sie über eine Azure Machine Learning-Pipeline verfügen, können Sie analog [„SynapseSparkStep“ zur Angabe Ihres Synapse Spark-Pool als Computeziel](how-to-use-synapsesparkstep.md) für den Datenaufbereitungsschritt in Ihrer Pipeline verwenden.

Wie Sie Ihre Daten für den Synapse Spark-Pool verfügbar machen, hängt von Ihrem Datasettyp ab. 

* Für ein FileDataset können Sie die [`as_hdfs()`](/python/api/azureml-core/azureml.data.filedataset#as-hdfs--)-Methode verwenden. Wenn die Ausführung übermittelt wird, wird das Dataset dem Synapse Spark-Pool als verteiltes Hadoop-Dateisystem (Hadoop Distributed File System, HFDS) zur Verfügung gestellt. 
* Für ein [TabularDataset](how-to-create-register-datasets.md#tabulardataset) können Sie die [`as_named_input()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#as-named-input-name-)-Methode verwenden. 

Für den folgenden Code gilt: 

* Erstellt die Variable `input2` aus dem FileDataset `train_ds`, das im vorherigen Codebeispiel erstellt wurde.
* Erstellt die Variable `output` mit der HDFSOutputDatasetConfiguration-Klasse. Nach Abschluss der Ausführung können wir mit dieser Klasse die Ausgabe der Ausführung als Dataset `test` im Datenspeicher `mydatastore` speichern. Im Azure Machine Learning-Arbeitsbereich wird das Dataset `test` unter dem Namen `registered_dataset` registriert. 
* Konfiguriert Einstellungen, die die Ausführung für eine gute Leistung im Synapse Spark-Pool verwenden sollte. 
* Definiert die ScriptRunConfig-Parameter, um 
  * `dataprep.py` für die Ausführung zu verwenden. 
  * anzugeben, welche Daten als Eingabe verwendet werden sollen, und wie diese für den Synapse Spark-Pool verfügbar gemacht werden sollen.
  * anzugeben, wo die Ausgabedaten `output` gespeichert werden sollen.  

```Python
from azureml.core import Dataset, HDFSOutputDatasetConfig
from azureml.core.environment import CondaDependencies
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

input2 = train_ds.as_hdfs()
output = HDFSOutputDatasetConfig(destination=(datastore, "test").register_on_complete(name="registered_dataset")

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

conda_dep = CondaDependencies()
conda_dep.add_pip_package("azureml-core==1.20.0")

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Nachdem Ihr `ScriptRunConfig`-Objekt eingerichtet wurde, können Sie die Ausführung übermitteln.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```

Weitere Details, z. B. das in diesem Beispiel verwendete `dataprep.py`-Skript, finden Sie im [Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb).

Nachdem Ihre Daten aufbereitet wurden, können Sie sie als Eingabe für Ihre Trainingsaufträge verwenden. Im oben genannten Codebeispiel geben Sie als Eingabedaten für Trainingsaufträge das `registered_dataset` an. 

## <a name="example-notebooks"></a>Beispielnotebooks

In diesem Beispielnotebook finden Sie weitere Konzepte und Demonstrationen der Azure Synapse Analytics- und Azure Machine Learning-Integrationsfunktionen.
* [Führen Sie eine interaktive Spark-Sitzung aus einem Notebook in Ihrem Azure Machine Learning-Arbeitsbereich aus](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb).
* [Übermitteln Sie eine Azure Machine Learning-Experimentausführung mit einem Synapse Spark-Pool als Computeziel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb).

## <a name="next-steps"></a>Nächste Schritte

* [Trainieren eines Modells](how-to-set-up-training-targets.md).
* [Trainieren Sie mit Azure Machine Learning-Datasets](how-to-train-with-datasets.md).
