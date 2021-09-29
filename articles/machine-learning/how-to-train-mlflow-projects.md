---
title: Training mit MLflow-Projekten
titleSuffix: Azure Machine Learning
description: Einrichten von MLflow mit Azure Machine Learning zum Protokollieren von Metriken und Artefakten aus ML-Modellen
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/16/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 11358869e727948627e76c0f56c049b95c0abbb9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784779"
---
# <a name="train-ml-models-with-mlflow-projects-and-azure-machine-learning"></a>Trainieren von ML-Modellen mit MLflow-Projekten und Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie den Nachverfolgungs-URI und die Protokollierungs-API von MLflow (zusammen als [MLflow-Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) bezeichnet) nutzen, um Trainingsausführungen mit [MLflow-Projekten](https://www.mlflow.org/docs/latest/projects.html) und Azure Machine Learning-Back-End-Unterstützung zu übermitteln. Sie können Aufträge lokal über die Azure Machine Learning-Nachverfolgung übermitteln oder Ihre Ausführungen zur Cloud migrieren, z. B. über [Azure Machine Learning Compute](./how-to-create-attach-compute-cluster.md).

[MLflow-Projekte](https://mlflow.org/docs/latest/projects.html) ermöglichen es Ihnen, Ihren Code zu organisieren und zu beschreiben, damit Sie von anderen Datenanalysten (oder automatisierten Tools) ausgeführt werden können. MLflow-Projekte mit Azure Machine Learning ermöglichen es, Trainingsausführungen in Ihrem Arbeitsbereich nachzuverfolgen und zu verwalten.

[MLflow](https://www.mlflow.org) ist eine Open-Source-Bibliothek zum Verwalten des Lebenszyklus Ihrer Machine Learning-Experimente. MLFlow-Nachverfolgung ist eine Komponente von MLflow, die Ihre Trainingsausführungsmetriken und Modellartefakte unabhängig von der Umgebung Ihres Experiments protokolliert und nachverfolgt: lokal auf Ihrem Computer, auf einem Remotecomputeziel, auf einem virtuellen Computer oder in einem [Azure Databricks-Cluster](how-to-use-mlflow-azure-databricks.md).

[Erfahren Sie mehr über die MLflow- und Azure Machine Learning-Integration.](how-to-use-mlflow.md)

> [!TIP]
> Die Informationen in diesem Dokument sind hauptsächlich für Datenanalysten und Entwickler gedacht, die den Modelltrainingsprozess überwachen möchten. Wenn Sie Administrator sind und sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie das `azureml-mlflow`-Paket.
    * Mit diesem Paket wird automatisch `azureml-core` des [The Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) bereitgestellt, um MLflow den Zugriff auf Ihren Arbeitsbereich zu ermöglichen.
* [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich.](how-to-manage-workspace.md)
    * Überprüfen Sie, welche [Zugriffsberechtigungen Sie benötigen, um Ihre MLflow-Vorgänge mit Ihrem Arbeitsbereich](how-to-assign-roles.md#mlflow-operations) auszuführen.

## <a name="train-mlflow-projects-on-local-compute"></a>Trainieren von MLflow-Projekten in einer lokalen Computeressource

Dieses Beispiel zeigt, wie Sie MLflow-Projekte mit Azure Machine Learning-Nachverfolgung lokal übermitteln.

Installieren Sie das Paket `azureml-mlflow`, um MLflow-Nachverfolgung mit Azure Machine Learning lokal für Ihre Experimente zu nutzen. Ihre Experimente können über ein Jupyter Notebook oder einen Code-Editor ausgeführt werden.

```shell
pip install azureml-mlflow
```

Importieren Sie die Klassen `mlflow` und [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29), um auf den Tracking-URI von MLflow zuzugreifen und Ihren Arbeitsbereich zu konfigurieren.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Legen Sie den Namen des MLflow-Experiments mit `set_experiment()` fest, und starten Sie Ihre Trainingsausführung mit `start_run()`. Verwenden Sie anschließend `log_metric()`, um die API für die MLflow-Protokollierung zu aktivieren, und beginnen Sie mit dem Protokollieren Ihrer Metriken für die Trainingsausführungen.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Erstellen Sie das Back-End-Konfigurationsobjekt, in dem die erforderlichen Informationen für die Integration gespeichert werden sollen, z. B. das Computeziel und den Typ der zu verwendenden verwalteten Umgebung.

```python
backend_config = {"USE_CONDA": False}
```

Fügen Sie das `azureml-mlflow`-Paket als PIP-Abhängigkeit Ihrer Umgebungskonfigurationsdatei hinzu, um Metriken und wichtige Artefakte in Ihrem Arbeitsbereich zu verfolgen. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

Senden Sie die lokale Ausführung, und stellen Sie sicher, dass Sie den Parameter `backend = "azureml" ` festlegen. Mit dieser Einstellung können Sie Testläufe lokal senden und die zusätzliche Unterstützung für die automatische Ausgabenachverfolgung, Protokolldateien, Momentaufnahmen und im Arbeitsbereich ausgegebene Fehler nutzen.

Zeigen Sie Ihre Ausführungen und Metriken in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) an.

```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="train-mlflow-projects-with-remote-compute"></a>Trainieren von MLflow-Projekten mit einer Remotecomputeressource

Dieses Beispiel zeigt, wie Sie MLflow-Projekte mit Azure Machine Learning-Nachverfolgung in einer Remotecoputeressource übermitteln.

Installieren Sie das Paket `azureml-mlflow`, um MLflow-Nachverfolgung mit Azure Machine Learning lokal für Ihre Experimente zu nutzen. Ihre Experimente können über ein Jupyter Notebook oder einen Code-Editor ausgeführt werden.

```shell
pip install azureml-mlflow
```

Importieren Sie die Klassen `mlflow` und [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29), um auf den Tracking-URI von MLflow zuzugreifen und Ihren Arbeitsbereich zu konfigurieren.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Legen Sie den Namen des MLflow-Experiments mit `set_experiment()` fest, und starten Sie Ihre Trainingsausführung mit `start_run()`. Verwenden Sie anschließend `log_metric()`, um die API für die MLflow-Protokollierung zu aktivieren, und beginnen Sie mit dem Protokollieren Ihrer Metriken für die Trainingsausführungen.

```Python
experiment_name = 'train-mlflow-project-amlcompute'
mlflow.set_experiment(experiment_name)
```

Erstellen Sie das Back-End-Konfigurationsobjekt, in dem die erforderlichen Informationen für die Integration gespeichert werden sollen, z. B. das Computeziel und den Typ der zu verwendenden verwalteten Umgebung.

Die Integration akzeptiert „COMPUTE“ und „USE_CONDA“ als Parameter. Dabei wird „COMPUTE“ auf den Namen Ihres Remotecomputeclusters und „USE_CONDA“ festgelegt, wodurch eine neue Umgebung für das Projekt auf der Grundlage der Umgebungskonfigurationsdatei erstellt wird. Ist „COMPUTE“ im Objekt vorhanden, wird das Projekt automatisch an die Remotecomputeressource übermittelt und „USE_CONDA“ ignoriert. MLflow akzeptiert ein Wörterbuchobjekt oder eine JSON-Datei.

```python
# dictionary
backend_config = {"COMPUTE": "cpu-cluster", "USE_CONDA": False}
```

Fügen Sie das `azureml-mlflow`-Paket als PIP-Abhängigkeit Ihrer Umgebungskonfigurationsdatei hinzu, um Metriken und wichtige Artefakte in Ihrem Arbeitsbereich zu verfolgen. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

Übermitteln Sie MLflow-Projektausführung, und stellen Sie sicher, dass der Parameter `backend = "azureml" ` festgelegt ist. Mit dieser Einstellung können Sie Ihre Ausführung an die Remotecomputeressource übermitteln und die zusätzliche Unterstützung für die automatische Ausgabenachverfolgung, Protokolldateien, Momentaufnahmen und im Arbeitsbereich ausgegebene Fehler nutzen.

Zeigen Sie Ihre Ausführungen und Metriken in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) an.

```python
remote_mlflow_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    backend_config = backend_config, 
                                    )

```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beachten Sie Folgendes, falls Sie nicht planen, die protokollierten Metriken und Artefakte in Ihrem Arbeitsbereich zu verwenden: Das Löschen einzelner Einträge ist derzeit nicht möglich. Löschen Sie stattdessen die Ressourcengruppe, die das Speicherkonto und den Arbeitsbereich enthält, damit hierfür keine Gebühren anfallen:

1. Wählen Sie ganz links im Azure-Portal **Ressourcengruppen** aus.

   ![Löschen im Azure-Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie anschließend die Option **Löschen**.

## <a name="example-notebooks"></a>Beispielnotebooks

Die [Notebooks „MLflow mit Azure ML“](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstrieren und erklären die in diesem Artikel vorgestellten Konzepte.

  * [Trainieren eines MLflow-Projekts in einer lokalen Computeressource](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-local/train-projects-local.ipynb)
  * [Trainieren eines MLflow-Projekts in einer Remotecomputeressource](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-remote/train-projects-remote.ipynb)

> [!NOTE]
> Ein Communityrepository mit Beispielen, die MLflow verwenden, finden Sie unter https://github.com/Azure/azureml-examples.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Modellen mit MLflow](how-to-deploy-mlflow-models.md).
* Überwachen Ihrer Produktionsmodelle auf [Datenabweichungen](./how-to-enable-data-collection.md)
* [Nachverfolgen von Azure Databricks-Ausführungen mit MLflow](how-to-use-mlflow-azure-databricks.md)
* [Verwalten Ihrer Modelle](concept-model-management-and-deployment.md)
