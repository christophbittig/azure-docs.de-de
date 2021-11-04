---
title: MLflow und Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie mehr über MLflow mit Azure Machine Learning, um Metriken und Artefakte von ML-Modellen zu protokollieren, und stellen Sie Ihre ML-Modelle als Webdienst bereit.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4900e66afa994b97a479e377e300c13ff29ae8e4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555113"
---
# <a name="mlflow-and-azure-machine-learning"></a>MLflow und Azure Machine Learning

[MLflow](https://www.mlflow.org) ist eine Open-Source-Bibliothek zum Verwalten des Lebenszyklus Ihrer Machine Learning-Experimente.  Der Nachverfolgungs-URI und die Protokollierungs-API von MLflow werden zusammen als [MLflow-Nachverfolgung](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) bezeichnet und sind eine Komponente von MLflow, die Ihre Trainingsausführungsmetriken und Modellartefakte unabhängig von der Umgebung Ihres Experiments protokolliert und nachverfolgt: lokal auf Ihrem Computer, auf einem Remotecomputeziel, auf einem virtuellen Computer oder in einem Azure Databricks-Cluster. 

Mit der MLflow-Nachverfolgung und Azure Machine Learning können Sie die Ausführungsmetriken eines Experiments nachverfolgen und Modellartefakte in Ihrem Azure Machine Learning-Arbeitsbereich speichern. Das Experiment kann lokal auf Ihrem Computer, auf einem Remotecomputeziel, einem virtuellen Computer oder in einem [Azure Databricks-Cluster](how-to-use-mlflow-azure-databricks.md) ausgeführt werden. 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Vergleich von MLflow mit Azure Machine Learning-Clients

 In der folgenden Tabelle sind die unterschiedlichen Clients, für die Azure Machine Learning genutzt werden kann, und die entsprechenden Funktionen zusammengefasst.

 MLflow-Tracking verfügt über Funktionen für Metrikprotokollierung und Artefaktspeicherung, die sonst nur über das [Azure Machine Learning-SDK für Python](/python/api/overview/azure/ml/intro) zur Verfügung stehen.

| Funktion | MLflow-Tracking und -Bereitstellung | Python-SDK für Azure Machine Learning |  Azure Machine Learning-CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Verwalten des Arbeitsbereichs |   | ✓ | ✓ | ✓ |
| Verwenden von Datenspeichern  |   | ✓ | ✓ | |
| Protokollieren von Metriken      | ✓ | ✓ |   | |
| Hochladen von Artefakten | ✓ | ✓ |   | |
| Anzeigen von Metriken     | ✓ | ✓ | ✓ | ✓ |
| Verwalten von Compute   |   | ✓ | ✓ | ✓ |
| Bereitstellen von Modellen    | ✓ | ✓ | ✓ | ✓ |
|Überwachen der Modellleistung||✓|  |   |
| Erkennen von Datenabweichungen |   | ✓ |   | ✓ |


## <a name="track-experiments"></a>Nachverfolgen von Experimenten

Die MLflow-Nachverfolgung ermöglicht das Verbinden von Azure Machine Learning als Back-End Ihres MLflow-Experiments. Dadurch können Sie die folgenden Aufgaben ausführen:

+ Sie können die Metriken und Artefakte von Experimenten im [Azure Machine Learning-Arbeitsbereich](./concept-azure-machine-learning-architecture.md#workspace) nachverfolgen und protokollieren. Falls Sie MLflow-Tracking bereits für Ihre Experimente verwenden, ist der Arbeitsbereich ein zentraler, sicherer und skalierbarer Ort zum Speichern von Trainingsmetriken und -modellen. 

+ Sie können Modelle in MLflow und der Azure Machine Learning-Modellregistrierung nachverfolgen und verwalten.

+ [Nachverfolgen von Azure Databricks-Testausführungen](how-to-use-mlflow-azure-databricks.md)

Weitere Informationen finden Sie unter [Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning](how-to-use-mlflow.md). 

## <a name="train-mlflow-projects"></a>Trainieren von MLflow-Projekten

Sie können den Nachverfolgungs-URI und die Protokollierungs-API von MLflow (zusammen als „MLflow-Nachverfolgung“ bezeichnet) nutzen, um Trainingsausführungen mit [MLflow-Projekten](https://www.mlflow.org/docs/latest/projects.html) und Azure Machine Learning-Back-End-Unterstützung (Vorschau) zu übermitteln. Sie können Aufträge lokal über die Azure Machine Learning-Nachverfolgung übermitteln oder Ihre Ausführungen zur Cloud migrieren, z. B. über [Azure Machine Learning Compute](./how-to-create-attach-compute-cluster.md).

Weitere Informationen finden Sie unter [Trainieren von ML-Modellen mit MLflow-Projekten und Azure Machine Learning (Vorschau)](how-to-train-mlflow-projects.md).


## <a name="deploy-mlflow-experiments"></a>Bereitstellen von MLflow-Experimenten

Sie können [Ihr MLflow-Modell als Azure-Webdienst bereitstellen](how-to-deploy-mlflow-models.md), um die Azure Machine Learning-Funktionen für die Modellverwaltung und Datendrifterkennung nutzen und auf Ihre Produktionsmodelle anwenden zu können.

## <a name="next-steps"></a>Nächste Schritte
* [Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning](how-to-use-mlflow.md) 
* [Trainieren von ML-Modellen mit MLflow-Projekten und Azure Machine Learning (Vorschau)](how-to-train-mlflow-projects.md)
* [Nachverfolgen von Azure Databricks-Ausführungen mit MLflow](how-to-use-mlflow-azure-databricks.md)
* [Bereitstellen von Modellen mit MLflow](how-to-deploy-mlflow-models.md).


