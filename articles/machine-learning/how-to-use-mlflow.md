---
title: MLflow-Tracking für ML-Experimente
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie die MLflow-Nachverfolgung mit Azure Machine Learning einrichten, um Metriken und Artefakte aus ML-Modellen zu protokollieren.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: b407c248d20e60effbc7c64b56f857590671ef70
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556215"
---
# <a name="track-ml-models-with-mlflow-and-azure-machine-learning"></a>Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie den Nachverfolgungs-URI und die Protokollierungs-API von MLflow (zusammen als [MLflow-Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) bezeichnet) zum Verbinden von Azure Machine Learning als Back-End Ihrer MLflow-Experimente nutzen. 

Unterstützte Funktionen umfassen u. a.: 

+ Sie können die Metriken und Artefakte von Experimenten im [Azure Machine Learning-Arbeitsbereich](./concept-azure-machine-learning-architecture.md#workspace) nachverfolgen und protokollieren. Falls Sie MLflow-Tracking bereits für Ihre Experimente verwenden, ist der Arbeitsbereich ein zentraler, sicherer und skalierbarer Ort zum Speichern von Trainingsmetriken und -modellen.

+ [Übermitteln von Trainingsaufträgen mit MLflow-Projekten und Azure Machine Learning-Back-End-Unterstützung.](how-to-train-mlflow-projects.md) Sie können Aufträge lokal über die Azure Machine Learning-Nachverfolgung übermitteln oder Ihre Ausführungen zur Cloud migrieren, z. B. über [Azure Machine Learning Compute](how-to-create-attach-compute-cluster.md).

+ Sie können Modelle in MLflow und der Azure Machine Learning-Modellregistrierung nachverfolgen und verwalten.

[MLflow](https://www.mlflow.org) ist eine Open-Source-Bibliothek zum Verwalten des Lebenszyklus Ihrer Machine Learning-Experimente. MLFlow-Nachverfolgung ist eine Komponente von MLflow, die Ihre Trainingsausführungsmetriken und Modellartefakte unabhängig von der Umgebung Ihres Experiments protokolliert und nachverfolgt: lokal auf Ihrem Computer, auf einem Remotecomputeziel, auf einem virtuellen Computer oder in einem [Azure Databricks-Cluster](how-to-use-mlflow-azure-databricks.md). 

Weitere Informationen zur Integration von MLflow- und Azure Machine Learning-Funktionen finden Sie unter [MLflow und Azure Machine Learning](concept-mlflow.md).

Im folgenden Diagramm ist dargestellt, wie Sie mit MLflow-Tracking die Ausführungsmetriken eines Experiments nachverfolgen und Modellartefakte in Ihrem Azure Machine Learning-Arbeitsbereich speichern.

![MLflow mit Azure Machine Learning: Diagramm](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Die Informationen in diesem Dokument sind hauptsächlich für Datenanalysten und Entwickler gedacht, die den Modelltrainingsprozess überwachen möchten. Wenn Sie Administrator sind und sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

> [!NOTE] 
> Sie können den [MLflow Skinny-Client](https://github.com/mlflow/mlflow/blob/master/README_SKINNY.rst) verwenden. Dabei handelt es sich um ein einfaches MLflow-Paket ohne SQL-Speicher, Server, Benutzeroberfläche oder Data Science-Abhängigkeiten. Dies wird für Benutzer empfohlen, die in erster Linie die Nachverfolgungs- und Protokollierungsfunktionen benötigen, ohne die gesamte Suite von MLflow-Features einschließlich Bereitstellungen zu importieren. 

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie das `azureml-mlflow`-Paket. 
    * Mit diesem Paket wird automatisch `azureml-core` des [The Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) bereitgestellt, um MLflow den Zugriff auf Ihren Arbeitsbereich zu ermöglichen.
* [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich.](how-to-manage-workspace.md)
    * Überprüfen Sie, welche [Zugriffsberechtigungen Sie benötigen, um Ihre MLflow-Vorgänge mit Ihrem Arbeitsbereich](how-to-assign-roles.md#mlflow-operations) auszuführen.

## <a name="track-local-runs"></a>Nachverfolgen von lokalen Ausführungen

Mit MLflow-Tracking mit Azure Machine Learning können Sie die protokollierten Metriken und Artefakte aus Ihren lokalen Ausführungen in Ihrem Azure Machine Learning-Arbeitsbereich speichern.

Importieren Sie die Klassen `mlflow` und [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29), um auf den Tracking-URI von MLflow zuzugreifen und Ihren Arbeitsbereich zu konfigurieren.

Im folgenden Code wird mit der `get_mlflow_tracking_uri()`-Methode dem Arbeitsbereich `ws` eine eindeutige Tracking-URI-Adresse zugewiesen, und mit `set_tracking_uri()` wird für den Tracking-URI von MLflow auf diese Adresse verwiesen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Der Tracking-URI ist maximal eine Stunde lang gültig. Wenn Sie Ihr Skript nach einer Leerlaufzeit neu starten, sollten Sie die get_mlflow_tracking_uri-API verwenden, um einen neuen URI abzurufen.

Legen Sie den Namen des MLflow-Experiments mit `set_experiment()` fest, und starten Sie Ihre Trainingsausführung mit `start_run()`. Verwenden Sie anschließend `log_metric()`, um die API für die MLflow-Protokollierung zu aktivieren, und beginnen Sie mit dem Protokollieren Ihrer Metriken für die Trainingsausführungen.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Nachverfolgen von Remoteausführungen

Bei Remoteausführungen können Sie für das Trainieren Ihrer Modelle leistungsfähigere Computeumgebungen, z. B. GPU-fähige virtuelle Computer, oder Machine Learning Compute-Cluster verwenden. Informationen zu den verschiedenen Computeoptionen finden Sie unter [Übermitteln einer Trainingsausführung an ein Computeziel](how-to-set-up-training-targets.md).

Mit MLflow-Tracking mit Azure Machine Learning können Sie die protokollierten Metriken und Artefakte aus Ihren Remoteausführungen in Ihrem Azure Machine Learning-Arbeitsbereich speichern. Bei jeder Ausführung mit MLflow-Nachverfolgungscode werden automatisch Metriken im Arbeitsbereich protokolliert. 

Die folgende Conda-Beispielumgebung enthält `mlflow` und `azureml-mlflow` als PIP-Pakete. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - mlflow
    - numpy
```

Konfigurieren Sie in Ihrem Skript die Umgebung für Computevorgänge und Trainingsausführungen mit der [`Environment`](/python/api/azureml-core/azureml.core.environment.environment)-Klasse. Erstellen Sie anschließend [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) mit Ihrer Remotecomputeumgebung als Computeziel.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Verwenden Sie bei dieser Konfiguration für Computevorgänge und Trainingsausführungen die `Experiment.submit()`-Methode, um eine Ausführung zu übermitteln. Mit dieser Methode wird der MLflow Tracking-URI automatisch festgelegt und die Protokollierung von MLflow an Ihren Arbeitsbereich geleitet.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Anzeigen von Metriken und Artefakten in Ihrem Arbeitsbereich

Die Metriken und Artefakte aus dem MLflow-Protokoll werden in Ihrem Arbeitsbereich gespeichert. Sie können sie jederzeit anzeigen. Navigieren Sie dazu zu Ihrem Arbeitsbereich, und suchen Sie über den Namen nach dem Experiment in Ihrem Arbeitsbereich in [Azure Machine Learning-Studio](https://ml.azure.com).  Führen Sie alternativ den folgenden Befehl aus: 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Verwalten von Modellen 

Registrieren und verfolgen Sie Ihre Modelle mit der [Azure Machine Learning-Modellregistrierung](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere), die die MLflow-Modellregistrierung unterstützt. Azure Machine Learning-Modelle werden am MLflow-Modellschema ausgerichtet, sodass Sie diese Modelle problemlos exportieren und in verschiedene Workflows importieren können. Die Metadaten zu MLflow, z. B. die Ausführungs-ID, werden auch mit dem registrierten Modell gekennzeichnet, um sie besser nachverfolgen zu können. Benutzer können Trainingsausführungen senden, registrieren und bereitstellen, die aus MLflow-Ausführungen erstellt wurden. 

Wenn Sie Ihr Produktionsbereitstellungsmodell in einem Schritt bereitstellen und registrieren möchten, finden Sie weitere Informationen unter [Bereitstellen und Registrieren von MLflow-Modellen](how-to-deploy-mlflow-models.md).

Führen Sie die folgenden Schritte aus, um ein Modell aus einer Ausführung zu registrieren und anzuzeigen:

1. Wenn die Ausführung beendet ist, rufen Sie die `register_model()`-Methode auf.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Zeigen Sie das registrierte Modell in Ihrem Arbeitsbereich in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) an.

    Im folgenden Beispiel sind die Metadaten für die MLflow-Nachverfolgung für das registrierte Modell `my-model` gekennzeichnet. 

    ![register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Wählen Sie die Registerkarte **Artefakte** aus, um alle Modelldateien anzuzeigen, die mit dem MLflow-Modellschema („conda.yaml“, MLmodel, „model.pkl“) übereinstimmen.

    ![Modellschema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Wählen Sie „MLmodel“ aus, um die von der Ausführung generierte MLmodel-Datei anzuzeigen.

    ![MLmodel-Schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beachten Sie Folgendes, falls Sie nicht planen, die protokollierten Metriken und Artefakte in Ihrem Arbeitsbereich zu verwenden: Das Löschen einzelner Einträge ist derzeit nicht möglich. Löschen Sie stattdessen die Ressourcengruppe, die das Speicherkonto und den Arbeitsbereich enthält, damit hierfür keine Gebühren anfallen:

1. Wählen Sie ganz links im Azure-Portal **Ressourcengruppen** aus.

   ![Löschen im Azure-Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie anschließend die Option **Löschen**.

## <a name="example-notebooks"></a>Beispielnotebooks

Die [Notebooks „MLflow mit Azure ML“](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstrieren und erklären die in diesem Artikel vorgestellten Konzepte.

> [!NOTE]
> Ein Communityrepository mit Beispielen, die MLflow verwenden, finden Sie unter https://github.com/Azure/azureml-examples.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Modellen mit MLflow](how-to-deploy-mlflow-models.md).
* Überwachen Ihrer Produktionsmodelle auf [Datenabweichungen](./how-to-enable-data-collection.md)
* [Nachverfolgen von Azure Databricks-Ausführungen mit MLflow](how-to-use-mlflow-azure-databricks.md)
* [Verwalten Ihrer Modelle](concept-model-management-and-deployment.md)
