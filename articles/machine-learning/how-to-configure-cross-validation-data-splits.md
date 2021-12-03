---
title: Datenaufteilung und Kreuzvalidierung beim automatisierten maschinellen Lernen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Trainings-, Validierungs-, Kreuzvalidierungs- und Testdaten für automatisierte Experimente zum maschinellen Lernen konfigurieren können.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 11/15/2021
ms.openlocfilehash: 69f5913b03db51561cde17117ef97ec3c9e50868
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520615"
---
# <a name="configure-training-validation-cross-validation-and-test-data-in-automated-machine-learning"></a>Konfigurieren von Trainings-, Validierungs-, Kreuzvalidierungs- und Testdaten in automatisiertem maschinellem Lernen

In diesem Artikel lernen Sie die verschiedenen Optionen zum Konfigurieren der Aufteilung von Trainings- und Validierungsdaten sowie die Einstellungen für die Kreuzvalidierung für Ihre Experimente mit automatisiertem maschinellem Lernen (automatisiertem ML) kennen.

Wenn Sie in Azure Machine Learning automatisiertes maschinelles Lernen zum Erstellen mehrerer ML-Modelle verwenden, muss jede untergeordnete Ausführung das zugehörige Modell durch Berechnen der Qualitätsmetriken für das Modell validieren, etwa „Genauigkeit“ oder „AUC gewichtet“. Diese Metriken werden durch Vergleichen der mit jedem Modell gemachten Vorhersagen mit realen Bezeichnungen aus Beobachtungen in der Vergangenheit in den Validierungsdaten berechnet. [Erfahren Sie mehr darüber, wie Metriken basierend auf dem Validierungstyp berechnet werden.](#metric-calculation-for-cross-validation-in-machine-learning) 

Experimente mit automatisiertem maschinellem Lernen führen die Modellvalidierung automatisch durch. In den folgenden Abschnitten wird beschrieben, wie Sie die Validierungseinstellungen mit dem [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) weiter anpassen können. 

Anweisungen zum Arbeiten mit wenig Code oder ohne Code finden Sie unter [Erstellen automatisierter Experimente mit maschinellem Lernen in Azure Machine Learning-Studio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel ist Folgendes erforderlich:

* Ein Azure Machine Learning-Arbeitsbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Grundkenntnisse in der Einrichtung eines automatisierten Experiments mit maschinellem Lernen mit dem Azure Machine Learning SDK. Machen Sie sich anhand des [Tutorials](tutorial-auto-train-models.md) oder der [Anleitung](how-to-configure-auto-train.md) mit den grundlegenden Entwurfsmustern für automatisierte ML-Experimente vertraut.

* Ein grundlegendes Verständnis der Aufteilung von Trainings- und Validierungsdaten sowie der Kreuzvalidierung als Konzepte des maschinellen Lernens. Eine allgemeine Erklärung finden Sie unter

    * [About training, validation and test data in machine learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7) (Informationen zu Trainings-, Validierungs- und Testdaten beim maschinellen Lernen)

    * [Understand Cross Validation in machine learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) (Grundlegendes zur Kreuzvalidierung beim maschinellen Lernen) 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Standarddatenaufteilung und Kreuzvalidierung beim maschinellen Lernen

Verwenden Sie das [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)-Objekt zum Definieren Ihrer Experiment- und Trainingseinstellungen. Beachten Sie im folgenden Codeausschnitt, dass nur die erforderlichen Parameter definiert sind, d. h., die Parameter für `n_cross_validations` oder `validation_data` sind **nicht** enthalten.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Wenn Sie weder einen `validation_data`- noch einen `n_cross_validations`-Parameter explizit angeben, wendet die automatisierte ML Standardtechniken an, die von der Anzahl der im Einzeldatensatz `training_data` angegebenen Zeilen abhängen.

|Größe der Trainings&nbsp;daten&nbsp;| Validierungsverfahren |
|---|-----|
|**Größer&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Es wird eine Aufteilung in Trainings- und Validierungsdaten vorgenommen. Standardmäßig werden 10 % des ursprünglichen Trainingsdatasets als Validierungsset verwendet. Dieses Validierungsset wird seinerseits für die Metrikberechnung verwendet.
|**Kleiner&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Der Kreuzvalidierungsansatz wird angewendet. Die Standardanzahl der Faltungen (Folds) hängt von der Zeilenanzahl ab. <br> **Wenn das Dataset weniger als 1.000 Zeilen aufweist**, werden 10 Faltungen verwendet. <br> **Wenn die Anzahl der Zeilen zwischen 1.000 und 20.000 liegt**, werden drei Faltungen verwendet.


## <a name="provide-validation-data"></a>Bereitstellen von Validierungsdaten

In diesem Fall können Sie entweder von einer einzelnen Datendatei ausgehen, die Sie in Trainings- und Validierungsdatasets aufteilen, oder Sie können für den Validierungssatz eine separate Datendatei bereitstellen. In beiden Fällen weist der Parameter `validation_data` in Ihrem `AutoMLConfig`-Objekt die Daten zu, die als Validierungsset verwendet werden. Dieser Parameter akzeptiert nur Datasets in der Form eines [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) oder eines Pandas-Dataframes.   

> [!NOTE]
> Für den Parameter `validation_data` müssen die Parameter `training_data` und `label_column_name` ebenfalls festgelegt werden. Sie können nur einen Validierungsparameter festlegen, d. h., Sie können nur oder und `validation_data``n_cross_validations` nicht beide angeben.

Im folgenden Codebeispiel ist explizit definiert, welcher Teil der bereitgestellten Daten in `dataset` zum Training und welcher zur Validierung verwendet wird.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Angeben der Größe des Validierungssets

In diesem Fall wird nur ein einzelnes Dataset für das Experiment bereitgestellt. Das heißt, der `validation_data`-Parameter wird **nicht** angegeben, und das bereitgestellte Dataset wird dem Parameter `training_data` zugewiesen.  

In Ihrem `AutoMLConfig`-Objekt können Sie den Parameter `validation_size` so festlegen, dass er einen Teil der Trainingsdaten für die Validierung reserviert. Das bedeutet, dass der Validierungssatz durch das automatisierte maschinelle Lernen von den ursprünglich bereitgestellten Trainingsdaten (`training_data`) getrennt wird. Dieser Wert sollte zwischen 0,0 und 1.0 ausschließlich betragen (beispielsweise bedeutet 0,2, dass 20 % der Daten als Validierungsdaten zurückgehalten werden).

> [!NOTE]
> Der Parameter `validation_size` wird in Vorhersageszenarien nicht unterstützt. 

Betrachten Sie das folgende Codebeispiel:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K-fache Kreuzvalidierung

Um eine k-fache Kreuzvalidierung durchzuführen, schließen Sie den Parameter `n_cross_validations` ein, und legen Sie ihn auf einen Wert fest. Dieser Parameter legt fest, wie viele Kreuzvalidierungen durchgeführt werden, ausgehend von der gleichen Anzahl der Faltungen.

> [!NOTE]
> Der Parameter `n_cross_validations` wird in Klassifizierungsszenarien mit Deep Neural Networks nicht unterstützt.
 
Im folgenden Code werden fünf Faltungen für die Kreuzvalidierung definiert. Es ergeben sich also fünf verschiedene Trainings, von denen jedes 4/5 der Daten und jede Validierung 1/5 der Daten mit einer pro Durchgang verschiedenen Faltung der zurückgehaltenen Daten verwendet.

Als Ergebnis werden Metriken mit dem Durchschnitt der fünf Validierungsmetriken berechnet.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Monte Carlo-Kreuzvalidierung

Für eine Monte Carlo-Kreuzvalidierung müssen die beiden Parameter `validation_size` und `n_cross_validations` in das Objekt `AutoMLConfig` eingeschlossen werden. 

Bei der Monte Carlo-Kreuzvalidierung stellt das automatisierte maschinelle Lernen den durch den Parameter `validation_size` angegebenen Teil der Trainingsdaten für die Validierung zurück und weist die restlichen Daten zu Trainingszwecken zu. Dieser Prozess wird dann basierend auf dem im Parameter `n_cross_validations` angegebenen Wert wiederholt. Dabei werden jedes Mal nach dem Zufallsprinzip neue Trainings- und Validierungsaufteilungen generiert.

> [!NOTE]
> Die Monte Carlo-Kreuzvalidierung wird in Vorhersageszenarien nicht unterstützt.

Der folgende Code definiert eine siebenfache Kreuzvalidierung, und 20 Prozent der Trainingsdaten sollen für die Validierung verwendet werden. Es ergeben sich also sieben verschiedene Trainings. Dabei werden jeweils 80 Prozent der Daten für das Training verwendet und 20 Prozent zur Validierung mit jeweils anderen zurückgehaltenen Daten.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Angeben benutzerdefinierter Faltungen für die Kreuzvalidierung

Sie können außerdem eigene Datenfaltungen für die Kreuzvalidierung (Cross-Validation, CV) angeben. Dies wird als fortgeschrittenes Szenario angesehen, da Sie angeben, welche Spalten aufgeteilt und zur Validierung verwendet werden sollen.  Nehmen Sie benutzerdefinierte aufgeteilte CV-Spalten in Ihre Trainingsdaten auf, und geben Sie die Spalten an, indem Sie die Spaltennamen in den `cv_split_column_names`-Parameter einsetzen. Jede Spalte stellt eine Kreuzvalidierungsaufteilung dar und enthält die Integerwerte 1 oder 0. Dabei gibt 1 an, dass die Zeile zum Training verwendet werden soll, und 0, dass die Zeile zur Validierung verwendet werden soll.

Der folgende Codeausschnitt enthält Marketingdaten einer Bank mit zwei CV-Aufteilungsspalten ‚cv1‘ und ‚cv2‘.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Um `cv_split_column_names` mit `training_data` und `label_column_name` zu verwenden, führen Sie ein Upgrade Ihres Azure Machine Learning Python SDK auf Version 1.6.0 oder höher durch. Informieren Sie sich für frühere SDK-Versionen über die Verwendung von `cv_splits_indices`, beachten Sie aber, dass dies nur in Verbindung mit der `X`- und `y`-Eingabe von Datasets verwendet wird. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Metrikberechnung für die Kreuzvalidierung beim maschinellen Lernen

Wenn die k-fache Kreuzvalidierung oder die Monte Carlo-Kreuzvalidierung verwendet wird, werden Metriken für jeden Validierungsteil berechnet und anschließend aggregiert. Der Aggregationsvorgang ist bei skalaren Metriken ein Mittelwert und bei Diagrammen eine Summe. Die bei der Kreuzvalidierung berechneten Metriken basieren auf allen Aufteilungen und somit auf allen Stichproben aus dem Trainingssatz. [Weitere Informationen zu Metriken beim automatisierten maschinellen Lernen.](how-to-understand-automated-ml.md)

Wenn ein benutzerdefinierter Validierungssatz oder ein automatisch ausgewählter Validierungssatz verwendet wird, werden die Metriken zur Modellauswertung nur mit diesem Validierungssatz berechnet und nicht mit den Trainingsdaten.

## <a name="provide-test-data-preview"></a>Testdaten bereitstellen (Vorschau)

Sie können auch Testdaten bereitstellen, um das empfohlene Modell auszuwerten, das automatisierte ML nach Abschluss des Experiments für Sie generiert. Wenn Sie Testdaten bereitstellen, werden diese getrennt von Trainings- und Validierungsdaten betrachtet, um die Ergebnisse des Testlaufs des empfohlenen Modells nicht zu verfälschen. [Erfahren Sie mehr über Trainings-, Validierungs- und Testdaten in automatisierten ML.](concept-automated-ml.md#training-validation-and-test-data) 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Testdatasets müssen in Form eines [Azure Machine Learning TabularDataset](how-to-create-register-datasets.md#tabulardataset)vorliegen. Sie können ein Testdataset mit den `test_data` Parametern und `test_size` in Ihrem `AutoMLConfig` -Objekt angeben.  Diese Parameter schließen sich gegenseitig aus und können nicht gleichzeitig angegeben werden. 

Geben Sie mit dem `test_data` Parameter ein vorhandenes Dataset an, das an Ihr Objekt übergeben werden `AutoMLConfig` soll. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             ...
                             # Provide an existing test dataset
                             test_data=test_dataset,
                             ...
                             forecasting_parameters=forecasting_parameters)
```

Verwenden Sie beim Erstellen den Parameter , um eine Train/Test-Aufteilung zu verwenden, anstatt Testdaten direkt `test_size``AutoMLConfig` bereitzustellen. Dieser Parameter muss ein Gleitkommawert zwischen ausschließlich 0,0 und 1,0 sein und gibt den Prozentsatz des Trainingsdatensatzes an, der für den Testdatensatz verwendet werden soll.

```python
automl_config = AutoMLConfig(task = 'regression',
                             ...
                             # Specify train/test split
                             training_data=training_data,
                             test_size=0.2)
```

> [!Note]
> Für Regressionsaufgaben wird eine Zufallsstichprobe verwendet.<br>
> Für Klassifizierungsaufgaben werden geschichtete Stichproben verwendet, aber auch Zufallsstichproben, wenn eine geschichtete Stichprobe nicht durchführbar ist. <br>
> Die Vorhersage unterstützt derzeit nicht die Angabe eines Testdatensatzes mit einer Aufteilung in Training und Test.

Wenn sie die `test_data` Parameter oder `test_size` an `AutoMLConfig` übergeben, wird nach Abschluss des Experiments automatisch ein Remotetestlauf ausgelöst. In diesem Testlauf werden die bereitgestellten Testdaten verwendet, um das beste Modell auszuwerten, das von automatisierten ML empfohlen wird. Erfahren Sie mehr über [das Abrufen der Vorhersagen aus dem Testlauf.](how-to-configure-auto-train.md#test-models-preview)

## <a name="next-steps"></a>Nächste Schritte

* [Vermeiden von unausgeglichenen Daten und Überanpassung](concept-manage-ml-pitfalls.md).
* [Tutorial: Vorhersagen von Preisen für Taxifahrten mit automatisiertem maschinellen Lernen – -Abschnitt zur Datenaufteilung](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Anleitung: [Automatisches Trainieren eines Modells für die Zeitreihenprognose](how-to-auto-train-forecast.md).