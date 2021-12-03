---
title: Open-Source-Machine Learning-Integration
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Open-Source-Python-Frameworks für maschinelles Lernen verwenden, um End-to-End-Lösungen für maschinelles Lernen in Azure Machine Learning zu trainieren, bereitzustellen und zu verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.custom: mktng-kw-nov2021
ms.date: 11/04/2021
ms.openlocfilehash: 9809169a8da6965c8d1fdbb741ddd4817f8d2af7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850183"
---
# <a name="use-open-source-machine-learning-libraries-and-platforms-with-azure-machine-learning"></a>Verwenden von Open-Source-Bibliotheken und -Plattformen für maschinelles Lernen mit Azure Machine Learning

In diesem Artikel erfahren Sie mehr über Open-Source-Python-Bibliotheken und -Plattformen für maschinelles Lernen, die Sie mit Azure Machine Learning verwenden können. Führen Sie das Training, die Bereitstellung und Verwaltung für den End-to-End-Prozess des maschinellen Lernens mithilfe der von Ihnen bevorzugten Open-Source-Projekte durch.  Verwenden Sie Entwicklungstools wie Jupyter Notebooks und Visual Studio Code, um Ihre vorhandenen Modelle und Skripts in Azure Machine Learning zu nutzen.  

## <a name="train-open-source-machine-learning-models"></a>Trainieren von Open-Source-Machine Learning-Modellen

Der Trainingsprozess des maschinellen Lernens umfasst die Anwendung von Algorithmen auf Ihre Daten, um eine Aufgabe zu erfüllen oder ein Problem zu lösen. Je nach Problem können Sie verschiedene Algorithmen wählen, die am besten zu der Aufgabe und Ihren Daten passen. Weitere Informationen zu den Möglichkeiten des maschinellen Lernens finden Sie im Artikel [Deep Learning im Vergleich zu maschinellem Lernen](./concept-deep-learning-vs-machine-learning.md) und im [Spickzettel für Machine Learning-Algorithmen](algorithm-cheat-sheet.md).

### <a name="classical-machine-learning-scikit-learn"></a>Klassisches maschinelles Lernen: scikit-learn

Für Trainingsaufgaben, die Algorithmen des klassischen maschinellen Lernens einbeziehen, z. B. Klassifizierung, Clustering und Regression, könnten Sie etwas wie scikit-learn verwenden. Wie Sie ein Klassifizierungsmodell für Blumen trainieren können, erfahren Sie im Artikel [Trainieren mit scikit-learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Neuronale Netzwerke: PyTorch, TensorFlow, Keras

Open-Source-Algorithmen für maschinelles Lernen, bekannt als neuronale Netze, eine Teilmenge des maschinellen Lernens, sind hilfreich für das Trainieren von Deep Learning-Modellen in Azure Machine Learning.

Open-Source-Deep Learning-Frameworks und -Schrittanleitungen umfassen Folgendes:

 *  [PyTorch](https://github.com/pytorch/pytorch): [Trainieren eines Deep Learning-Bildklassifizierungsmodells mithilfe von Lerntransfer](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [Erkennen von handschriftlichen Ziffern mithilfe von TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [Erstellen eines neuronalen Netzwerks zur Analyse von Bildern mithilfe von Keras](how-to-train-keras.md)

### <a name="transfer-learning"></a>Transferlernen

Das Trainieren eines Deep Learning-Modells von Grund auf erfordert oft viel Zeit sowie große Mengen an Daten und Computeressourcen. Sie können das Training abkürzen, indem Sie den Lerntransfer verwenden. Lerntransfer ist ein Verfahren, bei dem das bei der Lösung eines Problems gewonnene Wissen auf ein anderes, aber verwandtes Problem angewandt wird. Dies bedeutet, dass Sie ein vorhandenes Modell verwenden und es umfunktionieren können. Weitere Informationen zum Lerntransfer finden Sie im Artikel [Deep Learning im Vergleich zu maschinellem Lernen](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning).

### <a name="reinforcement-learning-ray-rllib"></a>Vertiefendes Lernen: Ray RLLib

Das vertiefende Lernen ist ein Verfahren der künstlichen Intelligenz, bei dem Modelle unter Verwendung von Aktionen, Zuständen und Relevanzen trainiert werden: Die Agents für vertiefendes Lernen lernen, eine Reihe von vordefinierten Aktionen auszuführen, die die angegebenen Relevanzen basierend auf dem aktuellen Zustand ihrer Umgebung maximieren. 

Das [Ray RLLib](https://github.com/ray-project/ray)-Projekt verfügt über eine Reihe von Features, die eine hohe Skalierbarkeit während des Trainingsprozesses ermöglichen. Der iterative Prozess ist sowohl zeit- als auch ressourcenintensiv, da die Agents für vertiefendes Lernen versuchen, den optimalen Weg zum Erreichen einer Aufgabe zu lernen.  Ray RLLib unterstützt auch nativ Deep-Learning-Frameworks wie TensorFlow und PyTorch.  

Wie Sie Ray RLLib mit Azure Machine Learning verwenden, erfahren Sie im Abschnitt [Trainieren eines Modells für vertiefendes Lernen](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Überwachen der Modellleistung: TensorBoard

Das Training eines einzelnen oder mehrerer Modelle erfordert die Visualisierung und Überprüfung der gewünschten Metriken, um sicherzustellen, dass das Modell die erwartete Leistung erbringt. Sie können [TensorBoard in Azure Machine Learning verwenden, um Experimentmetriken nachzuverfolgen und zu visualisieren](./how-to-monitor-tensorboard.md)

## <a name="responsible-ml-privacy-and-fairness"></a>Verantwortungsvolles maschinelles Lernen: Datenschutz und Fairness

### <a name="preserve-data-privacy-with-differential-privacy"></a>Beibehalten des Datenschutzes mithilfe des differenziellen Datenschutzes

Zum Trainieren eines Machine Learning-Modells benötigen Sie Daten. Manchmal sind diese Daten vertraulich, und es ist wichtig, sicherzustellen, dass die Daten sicher und privat sind. Differenzieller Datenschutz ist ein Verfahren zur Beibehaltung der Vertraulichkeit von Informationen in einem Dataset. Weitere Informationen finden Sie im Artikel [Beibehalten des Datenschutzes](concept-differential-privacy.md). 

Open-Source-Lösungen für differenzielle Datenschutztoolkits wie [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) helfen Ihnen den [Datenschutz von Daten](how-to-differential-privacy.md) in Azure Machine Learning-Lösungen beizubehalten.

### <a name="frameworks-for-interpretable-and-fair-models"></a>Frameworks für interpretierbare und faire Modelle

Machine Learning-Systeme werden in verschiedenen Bereichen der Gesellschaft verwendet, z. B. im Bank-, Bildungs- und Gesundheitswesen. Daher ist es wichtig, dass diese Systeme für die Vorhersagen und Empfehlungen verantwortlich sind, die sie vornehmen, um unbeabsichtigte Konsequenzen zu vermeiden.

Open-Source-Frameworks wie [InterpretML](https://github.com/interpretml/interpret/) und Fairlearn (https://github.com/fairlearn/fairlearn) ) arbeiten mit Azure Machine Learning, um transparentere und angemessene Machine Learning-Modelle zu erstellen.

Weitere Informationen zum Erstellen von fairen und interpretierbaren Modellen finden Sie in den folgenden Artikeln:

- [Modellinterpretierbarkeit in Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpretieren und Erläutern von Machine Learning-Modellen](how-to-machine-learning-interpretability-aml.md)
- [Erläutern von Modellen für automatisiertes maschinelles Lernen](how-to-machine-learning-interpretability-automl.md)
- [Erhöhen der Fairness von Machine Learning-Modellen](concept-fairness-ml.md)
- [Verwenden von Azure Machine Learning für die Ressourcenmodellfairness](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Modellimplementierung

Sobald die Modelle trainiert und für die Produktionsumgebung bereit sind, müssen Sie entscheiden, wie sie bereitgestellt werden sollen. Azure Machine Learning bietet verschiedene Bereitstellungsziele. Weitere Informationen finden Sie im Artikel [Bereitstellen: wo und wie](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Standardisierung von Modellformaten mit ONNX

Nach dem Training werden die Inhalte des Modells, z. B. gelernte Parameter, serialisiert und in einer Datei gespeichert. Jedes Framework verfügt über ein eigenes Serialisierungsformat. Wenn Sie mit verschiedenen Frameworks und Tools arbeiten, bedeutet dies, dass Sie Modelle gemäß den Anforderungen des Frameworks bereitstellen müssen. Um diesen Prozess zu standardisieren, können Sie das Open Neural Network Exchange-Format (ONNX) verwenden. ONNX ist ein Open-Source-Format für Modelle für künstliche Intelligenz. ONNX unterstützt die Interoperabilität zwischen Frameworks. Das bedeutet, dass Sie ein Modell in einem der vielen beliebten Frameworks für maschinelles Lernen wie PyTorch trainieren, es in das ONNX-Format konvertieren und das ONNX-Modell in einem anderen Framework wie ML.NET verwenden können.

Weitere Informationen zu ONNX und wie Sie ONNX-Modelle verwenden können, finden Sie in den folgenden Artikeln:

- [Erstellen und Beschleunigen von ML-Modellen mit ONNX](concept-onnx.md)
- [Verwenden von ONNX-Modellen in .NET-Anwendungen](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Verpacken und Bereitstellen von Modellen als Container

Containertechnologien wie Docker sind eine Möglichkeit, Modelle als Webdienste bereitzustellen. Container bieten eine plattform- und ressourcenunabhängige Möglichkeit, reproduzierbare Softwareumgebungen zu erstellen und zu orchestrieren. Mit diesen Kerntechnologien können Sie [vorkonfigurierte Umgebungen](./how-to-use-environments.md), [vorkonfigurierte Containerimages](./how-to-deploy-custom-container.md) oder benutzerdefinierte Umgebungen verwenden, um Ihre Machine Learning-Modelle bereitzustellen, z. B. [Kubernetes-Cluster](./how-to-deploy-azure-kubernetes-service.md?tabs=python). Bei GPU-intensiven Workflows können Sie Tools wie NVIDIA Triton Inference Server verwenden, um Vorhersagen [mithilfe von GPUs](how-to-deploy-with-triton.md?tabs=python) zu treffen.

### <a name="secure-deployments-with-homomorphic-encryption"></a>Sichere Bereitstellungen mit homomorpher Verschlüsselung

Die Sicherung von Bereitstellungen ist ein wichtiger Teil des Bereitstellungsprozesses. Zur [Bereitstellung von verschlüsselten Rückschlussdiensten](how-to-homomorphic-encryption-seal.md) verwenden Sie die Open-Source-Python-Bibliothek `encrypted-inference`. Das `encrypted inferencing`-Paket stellt Bindungen bereit, die auf [Microsoft SEAL](https://github.com/Microsoft/SEAL) basieren, einer homomorphen Verschlüsselungsbibliothek.

## <a name="machine-learning-operations-mlops"></a>Machine Learning Operations (MLOps)

Machine Learning Operations (MLOps), gemeinhin als DevOps für maschinelles Lernen bezeichnet, ermöglicht es Ihnen, transparentere, resilientere und reproduzierbarere Workflows für maschinelles Lernen zu erstellen. Lesen Sie den Artikel [Informationen zu MLOps](./concept-model-management-and-deployment.md), um mehr über MLOps zu erfahren. 

Mithilfe von DevOps-Methoden wie Continuous Integration (CI) und Continuous Deployment (CD) können Sie den gesamten Lebenszyklus des maschinellen Lernens automatisieren und Governancedaten dazu erfassen. Sie können Ihre [CI/CD-Pipeline für maschinelles Lernen in GitHub-Aktionen](./how-to-github-actions-machine-learning.md) definieren, um Trainings- und Bereitstellungsaufgaben für Azure Machine Learning auszuführen. 

Die Erfassung von Softwareabhängigkeiten, Metriken, Metadaten, Daten und Versionsverwaltung für Modelle sind ein wichtiger Teil des MLOps-Prozesses, um transparente, reproduzierbare und überwachbare Pipelines zu erstellen. Für diese Aufgabe können Sie [MLFlow in Azure Machine Learning verwenden](how-to-use-mlflow.md), ebenso wie beim [Training von Machine Learning-Modellen in Azure Databricks](./how-to-use-mlflow-azure-databricks.md). Sie können auch [MLflow-Modelle als Azure-Webdienst bereitstellen](how-to-deploy-mlflow-models.md).