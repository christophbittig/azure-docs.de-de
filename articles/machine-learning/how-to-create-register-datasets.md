---
title: Erstellen von Azure Machine Learning-Datasets
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Azure Machine Learning-Datasets erstellen, um auf Ihre Daten für Machine Learning-Experimentausführungen zuzugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: contperf-fy21q1, data4ml
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.openlocfilehash: 2916b7c8042a96e35ad138ae5ae20c389c62e751
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866637"
---
# <a name="create-azure-machine-learning-datasets"></a>Erstellen von Azure Machine Learning-Datasets

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Datasets erstellen, um auf Daten für Ihre lokalen oder remotebasierten Experimente mit dem Python-SDK für Azure Machine Learning zuzugreifen. Informationen dazu, welche Rolle Datasets im Workflow für den Datenzugriff in Azure Machine Learning spielen, finden Sie im Artikel [Datenzugriff in Azure Machine Learning](concept-data.md#data-workflow).

Durch Erstellen eines Datasets erstellen Sie einen Verweis auf den Speicherort der Datenquelle sowie eine Kopie der zugehörigen Metadaten. Da die Daten an ihrem bisherigen Speicherort verbleiben, entstehen Ihnen keine zusätzlichen Speicherkosten und Sie riskieren nicht die Integrität Ihrer Datenquellen. Auch Datasets werden verzögert ausgewertet, was zum Erhöhen der Workflowleistung beiträgt. Sie können Datasets aus Datenspeichern, öffentlichen URLs und [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) erstellen.

Für eine Erfahrung mit sehr wenig Code können Sie [Azure Machine Learning-Datasets mit Azure Machine Learning Studio erstellen](how-to-connect-data-ui.md#create-datasets).

Azure Machine Learning-Datasets ermöglichen Folgendes:

* Aufbewahren einer einzelnen Datenkopie in Ihrem Speicher, auf die durch Datasets verwiesen wird

* Nahtloses Zugreifen auf Daten während des Modelltrainings, ohne sich Gedanken über Verbindungszeichenfolgen oder Datenpfade machen zu müssen [Erfahren Sie mehr über das Trainieren mit Datasets](how-to-train-with-datasets.md).

* Freigeben von Daten und Zusammenarbeiten mit anderen Benutzern

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Datasets zu erstellen und zu nutzen:

* Ein Azure-Abonnement. Wenn Sie keines haben, erstellen Sie ein kostenloses Konto, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

* Eine [Installation des Azure Machine Learning-SDK für Python](/python/api/overview/azure/ml/install), in dem das Paket „azureml-datasets“ enthalten ist.

    * Erstellen Sie eine [Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md), bei der es sich um eine vollständig konfigurierte und verwaltete Entwicklungsumgebung handelt, die integrierte Notebooks und das bereits installierte SDK beinhaltet.

    **OR**

    * Arbeiten Sie an Ihrem eigenen Jupyter-Notebook, und [installieren Sie das SDK selbst](/python/api/overview/azure/ml/install).

> [!NOTE]
> Einige Datasetklassen sind vom Paket [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) abhängig, das nur mit 64-Bit Python kompatibel ist. Wenn Sie unter __Linux__ entwickeln, basieren diese Klassen auf .NET Core 2.1 und werden nur von bestimmten Distributionen unterstützt. Weitere Informationen zu den unterstützten Distributionen finden Sie in der .NET Core 2.1-Spalte im Artikel [Installieren von .NET unter Linux](/dotnet/core/install/linux).

> [!IMPORTANT]
> Das Paket funktioniert zwar möglicherweise in älteren Versionen von Linux-Distributionen, sie sollten jedoch keine Distribution verwenden, die nicht vom grundlegenden Support unterstützt wird. Distributionen, die nicht vom grundlegenden Support unterstützt werden, können Sicherheitsrisiken aufweisen, da sie nicht die neuesten Updates erhalten. Sie sollten die neueste unterstützte Version Ihrer Distribution verwenden, die mit  kompatibel ist.

## <a name="compute-size-guidance"></a>Leitfaden für die Computegröße

Wenn Sie ein Dataset erstellen, überprüfen Sie Ihre Computeverarbeitungsleistung und die Größe der Daten im Arbeitsspeicher. Die Größe der Daten im Speicher ist nicht mit der Größe der Daten in einem Datenrahmen identisch. Beispielsweise können Daten in CSV-Dateien sich in einem Datenrahmen um das10-fache ausdehnen, sodass aus einer 1 GB großen CSV-Datei 10 GB in einem Datenrahmen werden können. 

Wenn Ihre Daten komprimiert sind, können sie sich weiter ausdehnen. 20 GB Daten mit relativ geringer Dichte, die im komprimierten Parquet-Format gespeichert sind, können sich auf etwa 800 GB im Arbeitsspeicher ausdehnen. Da in Parquet-Dateien Daten in einem Spaltenformat gespeichert werden, müssen Sie, wenn Sie nur die Hälfte der Spalten benötigen, nur etwa 400 GB in den Arbeitsspeicher laden.

[Erfahren Sie mehr über die Optimierung der Datenverarbeitung in Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Datasettypen

Es gibt zwei Typen von Datasets, je nachdem, wie sie von den Benutzern im Training verwendet werden: FileDatasets und TabularDatasets. Beide Typen können in Trainingsworkflows von Azure Machine Learning mit Schätzern, AutoML, HyperDrive und Pipelines verwendet werden. 

### <a name="filedataset"></a>FileDataset

Ein [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) verweist auf eine einzelne Datei oder auf mehrere Dateien in Ihren Datenspeichern oder öffentlichen URLs. Wenn Ihre Daten bereits bereinigt und für Trainingsexperimente bereit sind, können Sie die Dateien als FileDataset-Objekt für Ihre Computeinstanz [herunterladen oder einbinden](how-to-train-with-datasets.md#mount-vs-download). 

Wir empfehlen FileDatasets für Ihre Machine Learning-Workflows, da die Quelldateien in jedem beliebigen Format vorliegen können, was ein breiteres Spektrum von Machine Learning-Szenarien ermöglicht, einschließlich Deep Learning.

Erstellen eines FileDataset mit dem [Python SDK](#create-a-filedataset) oder [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).
### <a name="tabulardataset"></a>TabularDataset

Ein [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) stellt Daten in einem tabellarischen Format dar, indem die bereitgestellte Datei oder Liste von Dateien analysiert wird. Dadurch haben Sie die Möglichkeit, die Daten in einem Pandas- oder Spark-Datenrahmen zu materialisieren, sodass Sie mit vertrauten Datenaufbereitungs- und Trainingsbibliotheken arbeiten können, ohne das Notebook verlassen zu müssen. Ein Objekt vom Typ `TabularDataset` kann auf der Grundlage von CSV-, TSV-, [Parquet](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)- und [JSONL-Dateien](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--) sowie auf der Grundlage von [SQL-Abfrageergebnissen](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) erstellt werden.

Mit TabularDataset-Objekten können Sie einen Zeitstempel auf der Grundlage einer Spalte in den Daten oder auf der Grundlage des Orts angeben, an dem die Pfadmusterdaten gespeichert sind, um ein Zeitreihenmerkmal zu aktivieren. Diese Angabe ermöglicht einfaches und effizientes Filtern nach Zeit. Ein Beispiel finden Sie unter [Demo einer tabellarischen, zeitreihenbezogenen API mit NOAA-Wetterdaten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Erstellen Sie ein TabularDataset mit dem [Python SDK](#create-a-tabulardataset) oder [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> Über Azure Machine Learning Studio generierte Workflows vom Typ [Automatisiertes maschinelles Lernen](concept-automated-ml.md) unterstützen derzeit nur „TabularDatasets“. 

## <a name="access-datasets-in-a-virtual-network"></a>Zugreifen auf Datasets in einem virtuellen Netzwerk

Wenn sich Ihr Arbeitsbereich in einem virtuellen Netzwerk befindet, müssen Sie das Dataset so konfigurieren, dass die Überprüfung übersprungen wird. Weitere Informationen zur Verwendung von Datenspeichern und Datasets in einem virtuellen Netzwerk finden Sie unter [Sichern eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken](how-to-secure-workspace-vnet.md#datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Erstellen von Datasets auf der Grundlage von Datenspeichern

Damit Azure Machine Learning auf die Daten zugreifen kann, müssen Datasets auf der Grundlage von Pfaden in [Azure Machine Learning-Datenspeichern](how-to-access-data.md) oder auf der Grundlage von Web-URLs erstellt werden. 

> [!TIP] 
> Sie können Datasets direkt auf der Grundlage von Speicher-URLs mit identitätsbasiertem Datenzugriff erstellen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Speicher mit identitätsbasiertem Datenzugriff](how-to-identity-based-data-access.md).

 
So erstellen Sie Datasets auf der Grundlage eines Datenspeichers mit dem Python SDK:

1. Vergewissern Sie sich, dass Sie für den zugrunde liegenden Speicherdienst Ihres registrierten Azure Machine Learning-Datenspeichers über Zugriff vom Typ `contributor` oder `owner` verfügen. [Überprüfen Sie Ihre Speicherkontoberechtigungen im Azure-Portal.](../role-based-access-control/check-access.md)

1. Erstellen Sie das Dataset, indem Sie auf Pfade im Datenspeicher verweisen. Ein Dataset kann aus mehreren Pfaden in mehreren Datenspeichern erstellt werden. Es gibt keine festen Grenzwerte für die Anzahl der Dateien oder die Datengröße, mit denen Sie ein Dataset erstellen können. 

> [!NOTE]
> Für jeden Datenpfad werden einige Anforderungen an den Speicherdienst gesendet, um zu prüfen, ob er auf eine Datei oder einen Ordner verweist. Dieser Aufwand kann zu einer Beeinträchtigung der Leistung oder zu einem Fehler führen. Ein Dataset, das auf einen Ordner mit 1000 Dateien verweist, bezieht sich auf einen Datenpfad. Es wird empfohlen, für eine optimale Leistung ein Dataset zu erstellen, das auf weniger als 100 Pfade in Datenspeichern verweist.

### <a name="create-a-filedataset"></a>Erstellen eines FileDataset-Elements

Verwenden Sie die Methode [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) für die Klasse `FileDatasetFactory`, um Dateien in einem beliebigen Format zu laden und ein nicht registriertes FileDataset-Objekt zu erstellen. 

Wenn sich Ihr Speicher hinter einem virtuellen Netzwerk oder einer Firewall befindet, legen Sie den Parameter `validate=False` in Ihrer `from_files()`-Methode fest. Dadurch wird der erste Überprüfungsschritt umgangen und sichergestellt, dass Sie Ihr Dataset aus diesen sicheren Dateien erstellen können. Informieren Sie sich über die [Verwendung von Datenspeichern und Datasets in einem virtuellen Netzwerk](how-to-secure-workspace-vnet.md#datastores-and-datasets).

```Python
from azureml.core import Workspace, Datastore, Dataset

# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

Wenn Sie alle Dateien aus einem lokalen Verzeichnis hochladen möchten, erstellen Sie ein FileDataset in einer einzelnen Methode mit [upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Mit dieser Methode werden Daten in den zugrunde liegenden Speicher hochgeladen, was zu Speicherkosten führt. 

```Python
from azureml.core import Workspace, Datastore, Dataset
from azureml.data.datapath import DataPath

ws = Workspace.from_config()
datastore = Datastore.get(ws, '<name of your datastore>')
ds = Dataset.File.upload_directory(src_dir='<path to you data>',
           target=DataPath(datastore,  '<path on the datastore>'),
           show_progress=True)

```

[Registrieren Sie Ihr Dataset](#register-datasets), um Datasets in Ihrem Arbeitsbereich experimentübergreifend wiederverwenden und freigeben zu können. 

### <a name="create-a-tabulardataset"></a>Erstellen eines TabularDataset-Elements

Verwenden Sie die Methode [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) für die Klasse `TabularDatasetFactory`, um Dateien im CSV- oder TSV-Format zu lesen und ein nicht registriertes TabularDataset-Objekt zu erstellen. Zum Einlesen von Dateien im PARQUET-Format verwenden Sie die [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)-Methode. Wenn Sie Daten aus mehreren Dateien lesen, werden die Ergebnisse in einer Tabellendarstellung aggregiert. 

Informationen zu den unterstützten Dateiformaten sowie zur Syntax und den Entwurfsmustern finden Sie in der [Referenzdokumentation von TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory), z. B. zur [Unterstützung mehrerer Zeilen](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--). 

Wenn sich Ihr Speicher hinter einem virtuellen Netzwerk oder einer Firewall befindet, legen Sie den Parameter `validate=False` in Ihrer `from_delimited_files()`-Methode fest. Dadurch wird der erste Überprüfungsschritt umgangen und sichergestellt, dass Sie Ihr Dataset aus diesen sicheren Dateien erstellen können. Erfahren Sie mehr über die Verwendung von [Datenspeichern und Datasets in einem virtuellen Netzwerk](how-to-secure-workspace-vnet.md#datastores-and-datasets).

Mit dem folgenden Code werden der vorhandene Arbeitsbereich und der gewünschte Datenspeicher anhand des Namens abgerufen. Anschließend werden die Speicherorte von Datenspeicher und Datei dem `path`-Parameter übergeben, um ein neues TabularDataset `weather_ds` zu erstellen.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Festlegen des Datenschemas

Beim Erstellen eines TabularDataset-Objekts werden Spaltendatentypen standardmäßig automatisch abgeleitet. Wenn die abgeleiteten Typen nicht Ihren Erwartungen entsprechen, können Sie Ihr Datasetschema aktualisieren, indem Sie die Spaltentypen mit dem folgenden Code angeben. Der Parameter `infer_column_type` ist nur für Datasets anwendbar, die aus durch Trennzeichen getrennten Dateien erstellt wurden. [Weitere Informationen zu unterstützten Datentypen](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|Name|Geschlecht|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||E
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38,0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||E

[Registrieren Sie Ihr Dataset](#register-datasets), um Datasets in Ihrem Arbeitsbereich experimentübergreifend wiederverwenden und freigeben zu können.

## <a name="wrangle-data"></a>Data Wrangling
Nachdem Sie Ihr Dataset erstellt und [registriert](#register-datasets) haben, können Sie es für das Data Wrangling und zum [Durchsuchen](#explore-data) vor dem Modelltraining in Ihr Notebook laden. 

Wenn Sie kein Data Wrangling und keine Durchsuchungen durchführen müssen, erfahren Sie unter [Trainieren mit Datasets](how-to-train-with-datasets.md), wie Sie Datasets in Ihren Trainingsskripts zum Übermitteln von ML-Experimenten nutzen.

### <a name="filter-datasets-preview"></a>Filtern von Datasets (Vorschau)

Die Filterfunktionen sind von dem Datasettyp abhängig, über den Sie verfügen. 
> [!IMPORTANT]
> Das Filtern von Datasets mit der Vorschaumethode [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) ist eine [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktion, die sich jederzeit ändern kann. 
> 
**Für TabularDatasets** können Sie Spalten mithilfe der Methoden [keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) und [drop_columns()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) beibehalten oder entfernen.

Verwenden Sie die Methode [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (Vorschau), um Zeilen nach einem spezifischen Spaltenwert in einem tabellarischen Dataset (TabularDataset) herauszufiltern. 

Die folgenden Beispiele geben ein nicht registriertes Dataset auf Grundlage der festgelegten Ausdrücke zurück.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

In **FileDatasets** entspricht jede Zeile einem Pfad einer Datei, weshalb das Filtern nach Spaltenwerten nicht hilfreich ist. Allerdings können Sie die [filter()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-)-Methode zum Herausfiltern von Zeilen nach Metadaten wie CreationTime (Erstellungszeit), Größe und mehr verwenden.

Die folgenden Beispiele geben ein nicht registriertes Dataset auf Grundlage der festgelegten Ausdrücke zurück.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Bezeichnete Datasets**, die mit [Bildbezeichnungsprojekten](how-to-create-image-labeling-projects.md) erstellt wurden, sind ein Sonderfall. Diese Datasets sind vom Typ „TabularDataset“ und bestehen aus Bilddateien. Für diese Datasettypen können Sie die [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-)-Methode zum Filtern von Bildern nach Metadaten und nach Spaltenwerten wie `label` und `image_details` verwenden.

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data"></a>Partitionieren von Daten

Sie können ein Dataset partitionieren, indem Sie beim Erstellen eines Datasets vom Typ „TabularDataset“ oder „FileDataset“ den Parameter `partitions_format` mit einschließen. 

Wenn Sie ein Dataset partitionieren, werden die Partitionsinformationen der einzelnen Dateipfade jeweils basierend auf dem angegebenen Format in Spalten extrahiert. Das Format muss an der Position des ersten Partitionsschlüssels beginnen und bis zum Ende des Dateipfads reichen. 

Ein Beispiel: Angenommen, der Pfad lautet `../Accounts/2019/01/01/data.jsonl`, und die Partition soll nach Abteilungsname und Datum erfolgen. In diesem Fall werden durch `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` eine Zeichenfolgenspalte namens „Department“ (Abteilung) mit dem Wert „Accounts“ (Konten) und eine Datetime-Spalte namens „PartitionDate“ (Partitionsdatum) mit dem Wert `2019-01-01` erstellt.

Wenn Ihre Daten bereits über vorhandene Partitionen verfügen und Sie dieses Format beibehalten möchten, schließen Sie den Parameter `partitioned_format` in die Methode [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) ein, um ein Dataset vom Typ „FileDataset“ zu erstellen. 

Wenn Sie ein Dataset vom Typ „TabularDataset“ erstellen und bereits vorhandene Partitionen beibehalten möchten, schließen Sie den Parameter `partitioned_format` in die Methode [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) oder [from_delimited_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) ein.

Im folgenden Beispiel werden folgende Aktionen ausgeführt:
* Erstellen eines Datasets vom Typ „FileDataset“ auf der Grundlage partitionierter Dateien
* Abrufen des Partitionsschlüssels
* Erstellen eines neuen indizierten Datasets vom Typ „FileDataset“ mithilfe von Folgendem:
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

Mit der Methode [partitions_by()](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-) kann auch eine neue Partitionsstruktur für Datasets vom Typ „TabularDatasets“ erstellt werden.

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

## <a name="explore-data"></a>Durchsuchen von Daten

Wenn Sie mit dem Data Wrangling fertig sind, können Sie Ihr Dataset [registrieren](#register-datasets) und anschließend zur Datenerkundung vor dem Modelltraining in Ihr Notebook laden.

FileDatasets können Sie wahlweise **einbinden** oder **herunterladen** und die Python-Bibliotheken auf sie anwenden, die Sie normalerweise zur Datenuntersuchung verwenden. [Weitere Informationen zum Vergleich von Einbinden und Herunterladen](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Verwenden Sie für TabularDatasets die [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)-Methode, um Ihre Daten in einem Datenrahmen anzuzeigen. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|Name|Geschlecht|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||E
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38,0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||E

## <a name="create-a-dataset-from-pandas-dataframe"></a>Erstellen eines Datasets aus Pandas-Datenrahmen

Um ein TabularDataset aus einem speicherinternen Pandas-Datenrahmen zu erstellen, verwenden Sie die [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#register-pandas-dataframe-dataframe--target--name--description-none--tags-none--show-progress-true-)-Methode. Diese Methode registriert das TabularDataset im Arbeitsbereich und lädt die Daten in den zugrunde liegenden Speicher hoch, wodurch Speicherkosten entstehen. 

```python
from azureml.core import Workspace, Datastore, Dataset
import pandas as pd

pandas_df = pd.read_csv('<path to your csv file>')
ws = Workspace.from_config()
datastore = Datastore.get(ws, '<name of your datastore>')
dataset = Dataset.Tabular.register_pandas_dataframe(pandas_df, datastore, "dataset_from_pandas_df", show_progress=True)

```
> [!TIP]
> Erstellen und registrieren Sie ein TabularDataset aus einem speicherinternen Spark-Datenrahmen oder einem Dask-Datenrahmen mit den öffentlichen Vorschaumethoden, [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory##register-spark-dataframe-dataframe--target--name--description-none--tags-none--show-progress-true-) und [`register_dask_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#register-dask-dataframe-dataframe--target--name--description-none--tags-none--show-progress-true-). Diese Methoden sind [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktionen und können jederzeit geändert werden. 
> 
>  Mit ihnen werden Daten in den zugrunde liegenden Speicher hochgeladen, was zu Speicherkosten führt. 

## <a name="register-datasets"></a>Registrieren von Datasets

Registrieren Sie Ihre Datasets bei einem Arbeitsbereich, um den Erstellungsprozess abzuschließen. Verwenden Sie die Methode [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-), um Datasets bei Ihrem Arbeitsbereich zu registrieren, damit sie für andere freigegeben und für Experimente in Ihrem Arbeitsbereich wiederverwendet werden können:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Erstellen von Datasets mithilfe des Azure Resource Managers

Unter [https://github.com/Azure/azure-quickstart-templates/tree/master//quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices) werden viele Vorlagen zur Verfügung gestellt, die zum Erstellen von Datasets verwendet werden können.

Informationen zur Verwendung dieser Vorlagen finden Sie unter [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning](how-to-create-workspace-template.md).
 

## <a name="train-with-datasets"></a>Trainieren mit Datasets

Verwenden Sie Ihre Datasets in Ihren Machine Learning-Experimenten zum Trainieren von ML-Modellen. [Erfahren Sie mehr über das Trainieren mit Datasets](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Versionsverwaltung von Datasets

Sie können ein neues Dataset unter demselben Namen registrieren, indem Sie eine neue Version erstellen. Eine Datasetversion ermöglicht es, den Zustand der Daten zu markieren, sodass Sie eine bestimmte Version des Datasets für Experimente oder zukünftige Reproduktion verwenden können. Weitere Informationen zu Datasetversionen finden Sie [hier](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie mit Datasets trainiert wird](how-to-train-with-datasets.md).
* Verwenden Sie automatisiertes Machine Learning, um [mit TabularDatasets zu trainieren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Weitere Beispiele zum Trainieren von Datasets finden Sie in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
