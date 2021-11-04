---
title: Datenerfassung mit Azure Data Factory
titleSuffix: Azure Machine Learning
description: Erfahren Sie, welche Optionen zum Erstellen einer Datenerfassungspipeline mit Azure Data Factory zur Verfügung stehen und welche Vorteile sie jeweils bieten.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 3e608ffa1818ceef173336344547601ba172b609
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563072"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Datenerfassung mit Azure Data Factory

In diesem Artikel erfahren Sie, welche Optionen für das Erstellen einer Datenerfassungspipeline mit [Azure Data Factory](../data-factory/introduction.md) verfügbar sind. Diese Azure Data Factory-Pipeline wird für die Erfassung von Daten zur Verwendung mit [Azure Machine Learning](overview-what-is-azure-machine-learning.md) verwendet. Mit Data Factory können Sie auf einfache Weise Daten extrahieren, transformieren und laden (ETL). Nachdem die Daten transformiert und in den Speicher geladen wurden, können sie zum Trainieren Ihrer Machine Learning-Modelle in Azure Machine Learning verwendet werden.

Die einfache Datentransformation kann mit nativen Data Factory-Aktivitäten und -Verfahren, z. B. [Datenfluss](../data-factory/control-flow-execute-data-flow-activity.md), durchgeführt werden. Bei komplizierteren Szenarien können die Daten mit etwas benutzerdefiniertem Code verarbeitet werden. Beispiel: Python- oder R-Code.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Vergleichen von Pipelines für die Azure Data Factory-Datenerfassung 
Es gibt verschiedene gängige Verfahren, um die Daten während der Erfassung mit Data Factory zu transformieren. Jedes Verfahren hat Vor- und Nachteile. Anhand dieser Informationen können Sie die Eignung für einen bestimmten Anwendungsfall ermitteln:

| Verfahren | Vorteile | Nachteile |
| ----- | ----- | ----- |
| Data Factory und Azure Functions | <li> Geringe Wartezeit, serverloses Computing<li>Zustandsbehaftete Funktionen<li>Wiederverwendbare Funktionen | Nur geeignet für die Verarbeitung kurzzeitiger Prozesse |
| Data Factory und benutzerdefinierte Komponente | <li>Paralleles Computing in großem Umfang<li>Geeignet für komplizierte Algorithmen | <li>Erfordert das Umschließen von Code in einer ausführbaren Datei<li>Komplexität der Verarbeitung von Abhängigkeiten und E/A |
| Data Factory und Azure Databricks-Notebook |<li> Apache Spark<li>Native Python-Umgebung |<li>Kann teuer sein<li>Das Erstellen von Clustern nimmt zunächst Zeit in Anspruch und erhöht die Wartezeit

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory mit Azure Functions

Azure Functions ermöglicht das Ausführen kleiner Codeelemente (Funktionen), ohne sich Gedanken über die Anwendungsinfrastruktur machen zu müssen. Bei dieser Option werden die Daten mit benutzerdefiniertem Python-Code verarbeitet, der von einer Azure-Funktion umschlossen ist. 

Die Funktion wird mit der [Aktivität „Azure Function“ in Azure Data Factory](../data-factory/control-flow-azure-function-activity.md) aufgerufen. Dieser Ansatz ist eine gute Option für leichte Datentransformationen. 

![Die Abbildung zeigt eine Azure Data Factory-Pipeline mit Azure Functions und einem Vorgang zum Ausführen einer ML-Pipeline sowie eine Azure Machine Learning-Pipeline mit einem Vorgang zum Trainieren eines Modells. Außerdem wird die Interaktion dieser Pipelines mit Rohdaten und aufbereiteten Daten gezeigt.](media/how-to-data-ingest-adf/adf-function.png)



* Vorteile:
    * Die Daten werden auf einem serverlosen Compute mit einer relativ niedrigen Wartezeit verarbeitet
    * Die Data Factory-Pipeline kann eine [Durable Azure Function](../azure-functions/durable/durable-functions-overview.md) aufrufen, die einen komplexen Datentransformationsfluss implementieren kann 
    * Die Details der Datentransformation werden durch die Azure-Funktion abstrahiert, die wiederverwendet und von anderen Stellen aus aufgerufen werden kann
* Nachteile:
    * Azure Functions muss vor der Verwendung mit ADF erstellt werden
    * Azure Functions ist nur für die kurzzeitige Datenverarbeitung geeignet

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory mit Aktivität vom Typ „Benutzerdefinierte Komponente“

Bei dieser Option werden die Daten mit benutzerdefiniertem Python-Code verarbeitet, der von einer ausführbaren Datei umschlossen ist. Er wird mit einer [Azure Data Factory-Aktivität vom Typ „Benutzerdefinierte Komponente“](../data-factory/transform-data-using-dotnet-custom-activity.md) aufgerufen. Dieser Ansatz eignet sich besser für große Datenmengen als das vorherige Verfahren.

![Die Abbildung zeigt eine Azure Data Factory-Pipeline mit benutzerdefinierter Komponente und Vorgang zum Ausführen einer ML-Pipeline sowie eine Azure Machine Learning-Pipeline mit Vorgang zum Trainieren eines Modells. Außerdem wird die Interaktion dieser Pipelines mit Rohdaten und vorbereiteten Daten gezeigt.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Vorteile:
    * Die Daten werden im [Azure Batch](../batch/batch-technical-overview.md)-Pool verarbeitet, der paralleles und leistungsstarkes Computing in großem Umfang ermöglicht
    * Kann zum Ausführen komplexer Algorithmen und zur Verarbeitung großer Datenmengen verwendet werden
* Nachteile:
    * Der Azure Batch-Pool muss vor der Verwendung mit Data Factory erstellt werden
    * Übermäßige Entwicklung hinsichtlich der Umschließung von Python-Code in einer ausführbaren Datei. Komplexität der Verarbeitung von Abhängigkeiten und Eingabe-/Ausgabeparametern

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory mit Azure Databricks-Python-Notebook

[Azure Databricks](https://azure.microsoft.com/services/databricks/) ist eine Apache Spark-basierte Analyseplattform in der Microsoft-Cloud.

Bei diesem Verfahren wird die Datentransformation von einem [Python-Notebook](../data-factory/transform-data-using-databricks-notebook.md) ausgeführt, das in einem Azure Databricks-Cluster ausgeführt wird. Dies ist wahrscheinlich der häufigste Ansatz, bei dem die volle Leistung eines Azure Databricks-Diensts genutzt wird. Er ist für die verteilte Datenverarbeitung im großen Stil konzipiert.

![Die Abbildung zeigt eine Azure Data Factory-Pipeline mit Azure Databricks Python und Vorgang zum Ausführen einer ML-Pipeline sowie eine Azure Machine Learning-Pipeline mit Vorgang zum Trainieren eines Modells. Außerdem wird die Interaktion dieser Pipelines mit Rohdaten und vorbereiteten Daten gezeigt.](media/how-to-data-ingest-adf/adf-databricks.png)

* Vorteile:
    * Die Daten werden über den leistungsfähigsten Azure-Dienst für die Datenverarbeitung transformiert, der von der Apache Spark-Umgebung unterstützt wird
    * Native Unterstützung von Python zusammen mit Data Science-Frameworks und Bibliotheken wie TensorFlow, PyTorch und scikit-learn
    * Es ist nicht erforderlich, den Python-Code in Funktionen oder ausführbare Module zu verpacken. Der Code funktioniert so, wie er ist.
* Nachteile:
    * Die Azure Databricks-Infrastruktur muss vor der Verwendung mit Data Factory erstellt werden
    * Kann je nach Azure Databricks-Konfiguration teuer sein
    * Das Hochfahren von Computeclustern aus dem „kalten“ Modus dauert einige Zeit, was eine hohe Wartezeit für die Lösung bedeutet 
    

## <a name="consume-data-in-azure-machine-learning"></a>Nutzen von Daten in Azure Machine Learning 

Für die Data Factory-Pipeline werden die aufbereiteten Daten in Ihrem Cloudspeicher (z. B. Azure Blob oder Azure Data Lake) gespeichert. <br>
Nutzen Sie Ihre aufbereiteten Daten in Azure Machine Learning wie folgt: 

* Aufrufen einer Azure Machine Learning-Pipeline aus Ihrer Data Factory-Pipeline<br>**OR**
* Erstellen eines [Azure Machine Learning-Datenspeichers](how-to-access-data.md#create-and-register-datastores) und [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) zur späteren Verwendung

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Aufrufen einer Azure Machine Learning-Pipeline aus Data Factory

Diese Methode wird für [Workflows mit Machine Learning-Vorgängen (MLOps)](concept-model-management-and-deployment.md#what-is-mlops) empfohlen. Falls Sie keine Azure Machine Learning-Pipeline einrichten möchten, helfen Ihnen die Informationen unter [Direktes Lesen von Daten aus dem Speicher](#read-data-directly-from-storage) weiter.

Bei jeder Ausführung der Data Factory-Pipeline passiert Folgendes: 

1. Die Daten werden an einem anderen Ort im Speicher gespeichert. 
1. Um den Speicherort an Azure Machine Learning zu übergeben, ruft die Data Factory-Pipeline eine [Azure Machine Learning-Pipeline](concept-ml-pipelines.md) auf. Beim Aufruf der ML-Pipeline werden der Datenspeicherort und die Ausführungs-ID als Parameter gesendet. 
1. Die ML-Pipeline kann dann einen Azure Machine Learning-Datenspeicher und ein Dataset mit dem Speicherort der Daten erstellen. Weitere Informationen finden Sie unter [Ausführen von Azure Machine Learning-Pipelines in Azure Data Factory-Pipelines](../data-factory/transform-data-machine-learning-service.md).

![Die Abbildung zeigt eine Azure Data Factory-Pipeline und eine Azure Machine Learning-Pipeline sowie die Interaktion dieser Pipelines mit Rohdaten und vorbereiteten Daten. Die Data Factory-Pipeline sendet Daten an die Datenbank für vorbereitete Daten, die die Daten wiederum an einen Datenspeicher übergibt. Der Datenspeicher füllt Datasets im Machine Learning-Arbeitsbereich.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Datasets [unterstützen die Versionsverwaltung](./how-to-version-track-datasets.md), sodass die ML-Pipeline eine neue Version des Datasets registrieren kann, die auf die neuesten Daten aus der ADF-Pipeline verweist.

Sobald die Daten über einen Datenspeicher oder ein Dataset zugänglich sind, können Sie sie zum Trainieren eines Machine Learning-Modells verwenden. Der Trainingsprozess könnte Teil derselben ML-Pipeline sein, die von ADF aufgerufen wird. Oder es könnte ein separater Prozess sein, z. B. ein Experiment in einem Jupyter Notebook.

Da Datasets die Versionsverwaltung unterstützen und jede Ausführung der Pipeline eine neue Version erzeugt, ist es leicht zu erkennen, welche Version der Daten zum Trainieren eines Modells verwendet wurde.

### <a name="read-data-directly-from-storage"></a>Direktes Lesen von Daten aus dem Speicher

Falls Sie keine ML-Pipeline erstellen möchten, können Sie auf die Daten direkt über das Speicherkonto zugreifen, unter dem Ihre aufbereiteten Daten in einem Azure Machine Learning-Datenspeicher und einem Dataset gespeichert sind. 

Der folgende Python-Code veranschaulicht, wie Sie einen Datenspeicher erstellen, der über eine Verbindung mit Azure Data Lake Gen2-Speicher verfügt. [Lesen Sie die Informationen zu Datenspeichern und zur Ermittlung der Berechtigungen für Dienstprinzipale](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Erstellen Sie als Nächstes ein Dataset zum Verweisen auf die Dateien, die Sie in Ihrer Aufgabe für maschinelles Lernen verwenden möchten. 

Mit dem folgenden Code wird ein TabularDataset-Element aus der CSV-Datei `prepared-data.csv` erstellt. Erfahren Sie mehr zu [Datasettypen und akzeptierten Dateiformaten](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Verwenden Sie ab jetzt `prepared_dataset`, um auf Ihre aufbereiteten Daten zu verweisen, z. B. in Ihren Trainingsskripts. Informieren Sie sich über das [Trainieren von Modellen mit Datasets in Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen eines Databricks-Notebooks in Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Zugreifen auf Daten in Azure Storage-Diensten](./how-to-access-data.md#create-and-register-datastores)
* [Trainieren von Modellen mit Datasets in Azure Machine Learning](./how-to-train-with-datasets.md)
* [DevOps für eine Datenerfassungspipeline](./how-to-cicd-data-ingestion.md)