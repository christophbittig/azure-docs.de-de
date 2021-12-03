---
title: Einrichten von AutoML mit Python
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie eine AutoML-Trainingsausführung mit dem Python SDK für Azure Machine Learning mithilfe von automatisiertem ML von Azure Machine Learning einrichten.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: devx-track-python,contperf-fy21q1, automl, contperf-fy21q4, FY21Q4-aml-seo-hack, contperf-fy22q1
ms.openlocfilehash: 57d529d74d5e320c8a41fdcf71ddd61d11e4379e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519380"
---
# <a name="set-up-automl-training-with-python"></a>Einrichten von AutoML-Training mit Python

Hier erfahren Sie, wie Sie eine AutoML-Trainingsausführung (automatisiertes maschinelles Lernen) mit dem [Python-SDK für Azure Machine Learning](/python/api/overview/azure/ml/intro) mithilfe von automatisiertem ML von Azure Machine Learning einrichten. Das automatisierte ML wählt einen Algorithmus und Hyperparameter für Sie aus und generiert ein Modell, das Sie anschließend bereitstellen können. Dieser Leitfaden enthält Details zu den verschiedenen Optionen, die Sie zum Konfigurieren von automatisierten ML-Experimenten verwenden können.

Ein vollständiges Beispiel finden Sie unter [Tutorial: Trainieren eines Regressionsmodells mit AutoML](tutorial-auto-train-models.md).

Wenn Sie lieber ohne Code arbeiten, finden Sie hier Informationen zum [Einrichten von AutoML-Training in Azure Machine Learning Studio ohne Code](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel ist Folgendes erforderlich: 
* Ein Azure Machine Learning-Arbeitsbereich. Informationen zum Erstellen des Arbeitsbereichs finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Installation des Azure Machine Learning Python SDK,
    zum Installieren des SDK können Sie wie folgt vorgehen: 
    * Erstellen Sie eine Compute-Instanz, wodurch das SDK automatisch installiert und für ML-Workflows vorkonfiguriert wird. Weitere Informationen hierzu finden Sie unter [Erstellen und Verwalten einer Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md). 

    * [Installieren Sie das Paket `automl` selbst](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/README.md#setup-using-a-local-conda-environment). Es enthält die [Standardinstallation](/python/api/overview/azure/ml/install#default-install) des SDK.

    [!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]
    
    > [!WARNING]
    > Python 3.8 ist nicht kompatibel mit `automl`. 

## <a name="select-your-experiment-type"></a>Auswählen der Experimentart

Legen Sie vor Experimentbeginn fest, welche Art von Problem des maschinellen Lernens Sie lösen möchten. Das automatisierte maschinelle Lernen unterstützt die Aufgabentypen `classification`, `regression` und `forecasting`. Weitere Informationen zu [Aufgabentypen](concept-automated-ml.md#when-to-use-automl-classification-regression-forecasting--computer-vision)

Der folgende Code nutzt den Parameter `task` im Konstruktor `AutoMLConfig`, um den Experimenttyp `classification` anzugeben.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Datenquelle und Datenformat

Das automatisierte Machine Learning unterstützt Daten, die sich auf dem lokalen Desktop oder in der Cloud (z. B. in Azure Blob Storage) befinden. Die Daten können in einen **Pandas DataFrame** oder ein **Azure Machine Learning-TabularDataset** eingelesen werden. [Erfahren Sie mehr über Datasets](how-to-create-register-datasets.md).

Anforderungen für das Trainieren von Daten beim maschinellen Lernen:
- Die Daten müssen in Tabellenform vorliegen.
- Der Wert, der vorhergesagt werden soll (Zielspalte), muss in den Daten vorhanden sein.

**Für Remoteexperimente** müssen Trainingsdaten über die Remotecomputeressource zugänglich sein. Automatisiertes ML akzeptiert nur [TabularDatasets von Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset), wenn Sie auf einer Remotecomputeressource arbeiten. 

Azure Machine Learning-Datasets stellen Funktionen für folgende Zwecke bereit:

* Einfacher Datentransfer von statischen Dateien oder URL-Quellen in Ihren Arbeitsbereich
* Bereitstellen Ihrer Daten für Trainingsskripts bei Ausführung auf Cloudcomputeressourcen (Ein Beispiel für die Verwendung der `Dataset`-Klasse zum Einbinden von Daten in Ihr Remotecomputeziel finden Sie unter [Trainieren mit Datasets](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).)

Mit dem folgenden Code wird ein TabularDataset-Element aus einer Web-URL erstellt. Codebeispiele zum Erstellen von Datasets aus anderen Quellen wie lokalen Dateien und Datenspeichern finden Sie unter [Erstellen eines TabularDataset-Elements](how-to-create-register-datasets.md#create-a-tabulardataset).

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
```

**Für lokale Computeexperimente** werden DataFrames-Objekte von Pandas für schnellere Verarbeitungszeiten empfohlen.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Trainieren, Überprüfen und Testen von Daten

Sie können separate **Trainingsdaten und Datasets für die Überprüfung** direkt im `AutoMLConfig`-Konstruktor angeben. Erfahren Sie mehr über das [Konfigurieren von Training, Validierung, Kreuzvalidierung und Testdaten](how-to-configure-cross-validation-data-splits.md) für Ihre AutoML-Experimente. 

Wenn Sie keinen `validation_data`- oder `n_cross_validation`-Parameter explizit angeben, wendet das automatisierte maschinelle Lernen Standardverfahren an, um zu bestimmen, wie die Überprüfung durchgeführt wird. Diese Bestimmung hängt von der Anzahl der Zeilen im Dataset ab, das Ihrem `training_data`-Parameter zugewiesen wird. 

|Größe der Trainings&nbsp;daten&nbsp;| Validierungsverfahren |
|---|-----|
|**Größer&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Es wird eine Aufteilung in Trainings- und Validierungsdaten vorgenommen. Standardmäßig werden 10 % des ursprünglichen Trainingsdatasets als Validierungsset verwendet. Dieses Validierungsset wird seinerseits für die Metrikberechnung verwendet.
|**Kleiner&nbsp;als&nbsp;20.000&nbsp;Zeilen**| Der Kreuzvalidierungsansatz wird angewendet. Die Standardanzahl der Faltungen (Folds) hängt von der Zeilenanzahl ab. <br> **Wenn das Dataset weniger als 1.000 Zeilen aufweist**, werden 10 Faltungen verwendet. <br> **Wenn die Anzahl der Zeilen zwischen 1.000 und 20.000 liegt**, werden drei Faltungen verwendet.


> [!TIP] 
> Sie können **Testdaten (Vorschau)** hochladen, um Modelle zu bewerten, die durch automatisiertes maschinelles Lernen für Sie generiert wurden. Diese Features sind [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktionen und können jederzeit geändert werden.
> In diesem Artikel werden folgende Themen erläutert: 
> * [Übergeben von Testdaten an Ihr AutoMLConfig-Objekt](how-to-configure-cross-validation-data-splits.md#provide-test-data-preview) 
> * [Testen der Modelle, die durch automatisiertes maschinelles Lernen für Ihr Experiment generiert wurden](#test-models-preview)
>  
> Wenn Sie eine Vorgehensweise ohne Code bevorzugen, fahren Sie mit [Schritt 11 unter „Einrichten des automatisierten maschinellen Lernens mit der Studio-Benutzeroberfläche“](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment) fort.


### <a name="large-data"></a>Große Datenmengen 

Automatisiertes ML unterstützt eine begrenzte Anzahl von Algorithmen für das Training mit großen Datenmengen, die erfolgreich Modelle für große Daten auf kleinen virtuellen Computern erstellen können. Heuristiken für automatisiertes ML hängen von Eigenschaften wie der Datengröße, der Arbeitsspeichergröße des virtuellen Computers, dem Timeout für Experimente und den Featurisierungseinstellungen ab, um festzustellen, ob diese Algorithmen für große Datenmengen angewendet werden sollten. [Erfahren Sie mehr darüber, welche Modelle beim automatisierten maschinellen Lernen unterstützt werden](#supported-models). 

* Für die Regression sind dies [Online Gradient Descent Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) und [Fast Linear Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest) (Schneller linearer Regressor).

* Für die Klassifizierung sind dies [Averaged Perceptron Classifier](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest) und [Linear SVM Classifier](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest). Dabei verfügt der „Linear SVM Classifier“ sowohl über Versionen für große Datenmengen als auch kleine Datenmengen.

Wenn Sie diese Heuristiken außer Kraft setzen möchten, wenden Sie die folgenden Einstellungen an: 

Aufgabe | Einstellung | Notizen
|---|---|---
Blockieren&nbsp;von Datenstreamingalgorithmen | `blocked_models` in Ihrem `AutoMLConfig`-Objekt und Auflisten der Modelle, die Sie nicht verwenden möchten. | Führt entweder zu einem Ausführungsfehler oder zu einer langen Laufzeit.
Verwenden von Datenstreamingalgorithmen&nbsp;&nbsp;&nbsp;| `allowed_models` in Ihrem `AutoMLConfig`-Objekt und Auflisten der Modelle, die Sie verwenden möchten.| 
Verwenden von Datenstreamingalgorithmen&nbsp;&nbsp;&nbsp; <br> [(Experimente mit der Studio-Benutzeroberfläche)](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)|Blockieren Sie alle Modelle außer den Big Data-Algorithmen, die Sie verwenden möchten. |

## <a name="compute-to-run-experiment"></a>Computeziel zum Ausführen des Experiments

Legen Sie als Nächstes die Instanz fest, auf der das Modell trainiert werden soll. Ein Trainingsexperiment mit automatisiertem ML kann auf den folgenden Computeoptionen ausgeführt werden. Informieren Sie sich über die [Vor- und Nachteile der lokalen und Remotecomputeoptionen](concept-automated-ml.md#local-remote). 

* Ihr **lokaler** Computer (z. B. lokaler Desktop oder Laptop): Diese Option wird in der Regel für kleine Datasets und während der Untersuchungsphase verwendet. Ein Beispiel mit einem lokalen Computeziel finden Sie in [diesem Notebook](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb). 
 
* Ein **Remotecomputer** in der Cloud: [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) ist ein verwalteter Dienst, mit dem Machine Learning-Modelle in Clustern virtueller Azure-Computer trainiert werden können. Compute-Instanzen werden auch als Computeziel unterstützt.

    Ein Remotebeispiel mit Azure Machine Learning Managed Compute finden Sie in [diesem Notebook](https://github.com/Azure/azureml-examples/blob/main/python-sdk/tutorials/automl-with-azureml/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). 

* Ein **Azure Databricks-Cluster** im Azure-Abonnement. Weitere Informationen finden Sie unter [Einrichten eines Azure Databricks-Clusters für automatisiertes maschinelles Lernen](how-to-configure-databricks-automl-environment.md). Beispielnotebooks mit Azure Databricks finden Sie auf der [GitHub-Website](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl).

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurieren der Experimenteinstellungen

Es gibt mehrere Optionen zum Konfigurieren von Experimenten mit automatisiertem ML. Diese Parameter werden beim Instanziieren eines `AutoMLConfig`-Objekts festgelegt. Unter [AutoMLConfig-Klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) finden Sie eine vollständige Liste der Parameter.

Das folgende Beispiel gilt für eine Klassifizierungsaufgabe. Im Experiment wird die AUC gewichtet als [primäre Metrik](#primary-metric) verwendet. Für das Experiment sind ein Timeoutwert von 30 Minuten und zwei Faltungen für die Kreuzvalidierung festgelegt.

```python
    automl_classifier=AutoMLConfig(task='classification',
                                   primary_metric='AUC_weighted',
                                   experiment_timeout_minutes=30,
                                   blocked_models=['XGBoostClassifier'],
                                   training_data=train_data,
                                   label_column_name=label,
                                   n_cross_validations=2)
```
Sie können auch Vorhersageaufgaben konfigurieren, was eine zusätzliche Einrichtung erfordert. Weitere Informationen finden Sie im Artikel [Einrichten von AutoML zum Trainieren eines Zeitreihenvorhersagemodells mit Python](how-to-auto-train-forecast.md). 

```python
    time_series_settings = {
                            'time_column_name': time_column_name,
                            'time_series_id_column_names': time_series_id_column_names,
                            'forecast_horizon': n_test_periods
                           }
    
    automl_config = AutoMLConfig(
                                 task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings
                                )
```
    
### <a name="supported-models"></a>Unterstützte Modelle

Beim automatisierten maschinellen Lernen werden während des Automatisierungs- und Optimierungsprozesses verschiedene Modelle und Algorithmen getestet. Als Benutzer müssen Sie den Algorithmus nicht angeben. 

Die drei verschiedenen Werte des Parameters `task` bestimmen die Liste der anzuwendenden Algorithmen oder Modelle. Verwenden Sie die Parameter `allowed_models` oder `blocked_models`, um Iterationen mit den verfügbaren Modellen, die eingeschlossen oder ausgeschlossen werden sollen, weiter zu ändern. 

In der folgenden Tabelle werden die unterstützten Modelle anhand des Tasktyps zusammengefasst. 

> [!NOTE]
> Wenn Sie vorhaben, Ihre mit automatisiertem ML erstellten Modelle in ein [ONNX-Modell](concept-onnx.md) zu exportieren, beachten Sie, dass nur mit einem Sternchen (*) angegebene Algorithmen in das ONNX-Format konvertiert werden können. Erfahren Sie mehr über das [Konvertieren von Modellen zu ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Beachten Sie auch, dass ONNX zurzeit nur Klassifizierungs- und Regressionsaufgaben unterstützt. 

Klassifizierung | Regression | Zeitreihe und Vorhersage
|-- |-- |--
[Logistische Regression](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#logisticregression----logisticregression-)* | [Elastisches Netz](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#elasticnet----elasticnet-)* | [AutoARIMA](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting#autoarima----autoarima-)
[Light GBM](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#lightgbmclassifier----lightgbm-)* | [Light GBM](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#lightgbmregressor----lightgbm-)* | [Prophet](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels.forecasting#prophet----prophet-)
[Gradient Boosting](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#gradientboosting----gradientboosting-)* | [Gradient Boosting](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#gradientboostingregressor----gradientboosting-)* | [Elastisches Netz](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Entscheidungsstruktur](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#decisiontree----decisiontree-)* |[Entscheidungsstruktur](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#decisiontreeregressor----decisiontree-)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[K nächste Nachbarn](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#knearestneighborsclassifier----knn-)* |[K nächste Nachbarn](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#knearestneighborsregressor----knn-)* | [Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Lineare SVC](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#linearsupportvectormachine----linearsvm-)* |[LARS-Lasso](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#lassolars----lassolars-)* | [Entscheidungsstruktur](https://scikit-learn.org/stable/modules/tree.html#regression)
[Support Vector Classification (SVC)](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#supportvectormachine----svm-)* |[Stochastisches Gradientenabstiegsverfahren (SGD)](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#sgdregressor----sgd-)* | [Arimax](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels.forecasting#arimax----arimax-)
[Zufällige Gesamtstruktur](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#randomforest----randomforest-)* | [Random Forest](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#randomforestregressor----randomforest-) | [LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* | [Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* | [Stochastisches Gradientenabstiegsverfahren (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[XGBoost](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#xgboostclassifier----xgboostclassifier-)* |[XGBoost](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#xgboostregressor----xgboostregressor-)* | [Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Averaged Perceptron Classifier](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#averagedperceptronclassifier----averagedperceptronclassifier-)| [Online Gradient Descent Regressor](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Schneller linearer Regressor](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression#fastlinearregressor----fastlinearregressor-)| [ForecastTCN](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels.forecasting#tcnforecaster----tcnforecaster-)
[Stochastisches Gradientenabstiegsverfahren (SGD)](/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification#sgdclassifier----sgd-)* || Naiv
[Linearer SVM-Klassifizierer](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)* || SeasonalNaive
||| Average
||| SeasonalAverage
||| [ExponentialSmoothing](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels.forecasting#exponentialsmoothing----exponentialsmoothing-)

### <a name="primary-metric"></a>Primary metric (Primäre Metrik)

Der Parameter `primary_metric` bestimmt die Metrik, die während des Modelltrainings für die Optimierung verwendet werden soll. Die verfügbaren Metriken, die Sie auswählen können, richten sich nach der Art der ausgewählten Aufgabe.

Bei der Auswahl einer primären Metrik für optimiertes automatisiertes ML muss eine Vielzahl von Faktoren berücksichtigt werden. Der wichtigste Aspekt sollte sein, welche Metrik Ihre Geschäftsanforderungen am besten widerspiegelt. Anschließend sollten Sie überprüfen, ob die Metrik für Ihr Datasetprofil (Datengröße, Datenbereich, Klassenverteilung usw.) geeignet ist. In den folgenden Abschnitten werden die empfohlenen primären Metriken basierend auf dem Aufgabentyp und Geschäftsszenario zusammengefasst. 

Informationen zu den speziellen Definitionen dieser Metriken finden Sie unter [Grundlagen von Ergebnissen des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md).

#### <a name="metrics-for-classification-scenarios"></a>Metriken für Klassifizierungsszenarien 

Schwellenwertabhängige Metriken wie `accuracy`, `recall_score_weighted`, `norm_macro_recall` und `precision_score_weighted` sind möglicherweise nicht optimal für kleine Datensätze, die eine sehr große Klassenschiefe (Klassenungleichgewicht) aufweisen, oder wenn der erwartete Metrikwert sehr nahe bei 0,0 oder 1,0 liegt. In diesen Fällen eignet sich `AUC_weighted` möglicherweise besser als primäre Metrik. Nach Abschluss des automatisierten ML können Sie das stärkste Modell basierend auf der Metrik auswählen, die sich am besten für Ihre Geschäftsanforderungen eignet.

| Metrik | Beispiel eines Anwendungsfalls |
| ------ | ------- |
| `accuracy` | Bildklassifizierung, Stimmungsanalyse, Vorhersage der Kundenabwanderung |
| `AUC_weighted` | Betrugserkennung, Bildklassifizierung, Anomalie-/Spamerkennung |
| `average_precision_score_weighted` | Stimmungsanalyse |
| `norm_macro_recall` | Vorhersage der Kundenabwanderung |
| `precision_score_weighted` |  |

#### <a name="metrics-for-regression-scenarios"></a>Metriken für Regressionsszenarien

`r2_score`, `normalized_mean_absolute_error` und `normalized_root_mean_squared_error` versuchen alle, die Vorhersagefehler zu minimieren. `r2_score` und `normalized_root_mean_squared_error` minimieren beide die durchschnittlichen quadratischen Fehler, während `normalized_mean_absolute_error` den durchschnittlichen absoluten Wert der Fehler minimiert. Der absolute Wert behandelt Fehler aller Größenordnungen gleich, und quadrierte Fehler haben einen viel größeren Nachteil für Fehler mit größeren absoluten Werten. Je nachdem, ob größere Fehler stärker bestraft werden sollen oder nicht, kann man wählen, ob man den quadratischen Fehler oder den absoluten Fehler optimiert.

Der Hauptunterschied zwischen `r2_score` und `normalized_root_mean_squared_error` ist die Art und Weise, wie sie normalisiert werden, und ihre Bedeutung. `normalized_root_mean_squared_error` ist der mittlere quadratische Fehler, normiert auf den Bereich und kann als durchschnittliche Fehlergröße für die Vorhersage interpretiert werden. `r2_score` ist der mittlere quadratische Fehler, normalisiert durch eine Schätzung der Varianz der Daten. Es ist der Anteil der Variation, der durch das Modell erfasst werden kann. 

> [! Hinweis] `r2_score` und `normalized_root_mean_squared_error` verhalten sich auch als primäre Metriken ähnlich. Wenn ein fester Validierungssatz verwendet wird, optimieren diese beiden Metriken dasselbe Ziel, nämlich den mittleren quadratischen Fehler, und werden von demselben Modell optimiert. Wenn nur ein Trainingsset zur Verfügung steht und Kreuzvalidierung angewendet wird, würden sie sich leicht unterscheiden, da der Normalisierer für `normalized_root_mean_squared_error` als Bereich des Trainingssets festgelegt ist, aber der Normalisierer für `r2_score` würde für jede Falte variieren, da er die Varianz für jede Falte darstellt.

Wenn der Rang und nicht der genaue Wert von Interesse ist, kann `spearman_correlation` die bessere Wahl sein, da er die Rangkorrelation zwischen realen Werten und Vorhersagen misst.

Derzeit gibt es jedoch keine primäre Regressionsmetrik, die sich mit relativen Unterschieden befasst. Alle `r2_score`, `normalized_mean_absolute_error` und `normalized_root_mean_squared_error` behandeln einen Vorhersagefehler von $20k für einen Arbeitnehmer mit einem Gehalt von $30k genauso wie für einen Arbeitnehmer, der $20M verdient, wenn diese beiden Datenpunkte zum selben Datensatz für die Regression oder zur selben Zeitreihe gehören, die durch den Zeitreihenbezeichner angegeben wird. Tatsächlich ist ein Vorhersagefehler bzw. eine Abweichung von 20.000 USD bei einem Jahresgehalt von 20 Millionen USD jedoch sehr nah am eigentlichen Wert (eine geringe relative Differenz von 0,1 %), wohingegen ein Vorhersagefehler bzw. eine Abweichung von 20.000 USD bei einem Gehalt von 30.000 USD eine erhebliche Abweichung darstellt (eine große relative Differenz von 67 %). Um das Problem der relativen Unterschiede zu lösen, kann man ein Modell mit den verfügbaren primären Metriken trainieren und dann das Modell mit den besten `mean_absolute_percentage_error` oder `root_mean_squared_log_error` auswählen.

| Metrik | Beispiel eines Anwendungsfalls |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Preisvorhersage (Haus/Produkt/Trinkgeld), Vorhersage eines Bewertungsergebnisses |
| `r2_score` | Verspätungen bei Fluggesellschaften, Gehaltsschätzung, erforderlicher Zeitaufwand bis zur Problemlösung |
| `normalized_mean_absolute_error` |  |

#### <a name="metrics-for-time-series-forecasting-scenarios"></a>Metriken für Szenarien zur Zeitreihenvorhersage

Die Empfehlungen ähneln den Empfehlungen für Regressionsszenarien. 

| Metrik | Beispiel eines Anwendungsfalls |
| ------ | ------- |
| `normalized_root_mean_squared_error` | Preisvorhersage, Bestandoptimierung, Bedarfsvorhersage | 
| `r2_score` | Preisvorhersage, Bestandoptimierung, Bedarfsvorhersage |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Merkmalerstellung für Daten

In jedem Experiment mit automatisiertem ML werden Ihre Daten automatisch skaliert und normalisiert, um *bestimmte* Algorithmen zu unterstützen, die auf Features mit unterschiedlichen Skalierungen sensibel reagieren. Diese Skalierung und Normalisierung wird als Featurisierung bezeichnet. Weitere Informationen und Codebeispiele finden Sie unter [Featurisierung in AutoML](how-to-configure-auto-features.md#). 

Beim Konfigurieren Ihrer Experimente im Objekt `AutoMLConfig` können Sie die Einstellung `featurization` aktivieren/deaktivieren. In der folgenden Tabelle werden die akzeptierten Einstellungen für die Featurisierung im [AutoMLConfig-Objekt](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) aufgeführt. 

|Konfiguration der Featurebereitstellung | BESCHREIBUNG |
| ------------- | ------------- |
|`"featurization": 'auto'`| Gibt an, dass im Rahmen der Vorverarbeitung die folgenden [Schritte für Datenschutzmaßnahmen und Featurebereitstellung](how-to-configure-auto-features.md#featurization) automatisch durchgeführt werden. **Standardeinstellung**.|
|`"featurization": 'off'`| Gibt an, dass der Featurisierungsschritt nicht automatisch durchgeführt werden soll|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Gibt an, dass ein angepasster Featurebereitstellungsschritt verwendet werden soll. [Erfahren Sie, wie Sie die Featurebereitstellung anpassen](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Die Schritte zur Featurebereitstellung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Schritte zur Featurebereitstellung automatisch auf Ihre Eingabedaten angewendet.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Ensemble-Konfiguration

Ensemble-Modelle sind standardmäßig aktiviert und treten als abschließende Ausführungsiterationen in einer AutoML-Ausführung auf. Derzeit werden **VotingEnsemble** und **StackEnsemble** unterstützt. 

Mit VotingEnsemble wird Soft-Voting implementiert, wobei gewichtete Durchschnittswerte verwendet werden. Bei der StackEnsemble-Implementierung wird eine Implementierung auf zwei Ebenen verwendet, bei der die erste Ebene dieselben Modelle wie VotingEnsemble besitzt, und das Modell der zweiten Ebene verwendet wird, um die optimale Kombination der Modelle aus der ersten Ebene zu ermitteln. 

Wenn Sie ONNX-Modelle verwenden **oder** die Modellerklärung aktiviert haben, wird StackEnsemble deaktiviert und nur VotingEnsemble wird verwendet.

Das Ensemble-Training kann mithilfe der booleschen Parameter `enable_voting_ensemble` und `enable_stack_ensemble`deaktiviert werden.

```python
automl_classifier = AutoMLConfig(
                                 task='classification',
                                 primary_metric='AUC_weighted',
                                 experiment_timeout_minutes=30,
                                 training_data=data_train,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 enable_voting_ensemble=False,
                                 enable_stack_ensemble=False
                                )
```

Es gibt mehrere Standardargumente, die als `kwargs` in einem `AutoMLConfig`-Objekt bereitgestellt werden können, um das Ensemble-Standardverhalten zu verändern.

> [!IMPORTANT]
>  Die folgenden Parameter sind keine expliziten Parameter der AutoMLConfig-Klasse. 

* `ensemble_download_models_timeout_sec`: Während der **VotingEnsemble**- und **StackEnsemble**-Modellgenerierung werden mehrere angepasste Modelle aus den vorherigen Ausführungen untergeordneter Elemente heruntergeladen. Wenn der folgende Fehler angezeigt wird, müssen Sie möglicherweise mehr Zeit für das Herunterladen der Modelle einplanen: `AutoMLEnsembleException: Could not find any models for running ensembling`. Der Standardwert beträgt 300 Sekunden für das parallele Herunterladen dieser Modelle, und es gibt kein maximales Timeoutlimit. Konfigurieren Sie diesen Parameter mit einem höheren Wert als 300 Sekunden, wenn mehr Zeit erforderlich ist. 

  > [!NOTE]
  >  Wenn das Zeitlimit erreicht ist und Modelle heruntergeladen wurden, wird das Ensembling mit so vielen Modellen fortgesetzt, wie es heruntergeladen hat. Es ist nicht erforderlich, dass alle Modelle heruntergeladen werden müssen, um innerhalb dieses Zeitlimits fertig zu werden.

Die folgenden Parameter gelten nur für **StackEnsemble**-Modelle: 

* `stack_meta_learner_type`: Der meta-learner ist ein mit der Ausgabe der einzelnen heterogenen Modelle trainiertes Modell. Standard-meta-learner sind `LogisticRegression` für Klassifizierungsaufgaben (oder `LogisticRegressionCV`, wenn die Kreuzvalidierung aktiviert ist) und `ElasticNet` für Regressions-/Vorhersage-Aufgaben (oder `ElasticNetCV`, wenn die Kreuzvalidierung aktiviert ist). Dieser Parameter kann ein der folgenden Zeichenfolgen sein: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` oder `LinearRegression`.

* `stack_meta_learner_train_percentage`: gibt den Teil des Trainingssatzes an (bei Auswahl des Trainings- und Validierungstyps des Trainings), der für das Training des meta-learners reserviert werden soll. Der Standardwert ist `0.2`. 

* `stack_meta_learner_kwargs`: optionale Parameter, die an den Initialisierer des meta-learners übergeben werden sollen. Diese Parameter und Parametertypen spiegeln die Parameter und Parametertypen des entsprechenden Modellkonstruktors wider und werden an den Modellkonstruktor weitergeleitet.

Der folgende Code zeigt ein Beispiel für das Angeben des benutzerdefinierten Ensemble-Verhaltens in einem `AutoMLConfig`-Objekt.

```python
ensemble_settings = {
                     "ensemble_download_models_timeout_sec": 600
                     "stack_meta_learner_type": "LogisticRegressionCV",
                     "stack_meta_learner_train_percentage": 0.3,
                     "stack_meta_learner_kwargs": {
                                                    "refit": True,
                                                    "fit_intercept": False,
                                                    "class_weight": "balanced",
                                                    "multi_class": "auto",
                                                    "n_jobs": -1
                                                  }
                    }

automl_classifier = AutoMLConfig(
                                 task='classification',
                                 primary_metric='AUC_weighted',
                                 experiment_timeout_minutes=30,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 **ensemble_settings
                                )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Exit Criteria (Beendigungskriterien)

Es gibt einige Optionen, die Sie in Ihrer AutoMLConfig definieren können, um Ihr Experiment zu beenden.

|Kriterien| description
|----|----
No&nbsp;criteria (Keine Kriterien) | Wenn Sie keine Beendigungsparameter definieren, wird das Experiment fortgesetzt, bis kein weiterer Fortschritt bei Ihrer primären Metrik erzielt wird.
After&nbsp;a&nbsp;length&nbsp;of&nbsp;time (Nach einem Zeitraum)| Verwenden Sie `experiment_timeout_minutes` in Ihren Einstellungen, um zu definieren, wie lange Ihr Experiment in Minuten weiterhin ausgeführt werden soll. <br><br> Es liegt ein Mindestwert von 15 Minuten vor (oder 60 Minuten, wenn die Zeile nach Spaltengröße 10 Millionen überschreitet), um Fehler aufgrund von Zeitüberschreitungen beim Experiment zu vermeiden.
A&nbsp;score&nbsp;has&nbsp;been&nbsp;reached (Eine Bewertung wurde erzielt)| Wenn Sie `experiment_exit_score` verwenden, wird das Experiment abgeschlossen, nachdem der angegebene primäre metrischer Bewertungswert erreicht wurde.

## <a name="run-experiment"></a>Ausführen des Experiments

> [!WARNING]
> Wenn Sie ein Experiment mit denselben Konfigurationseinstellungen und derselben primären Metrik mehrmals durchführen, werden Sie wahrscheinlich bei jedem Experiment eine Abweichung in der abschließenden metrischen Bewertung und in den generierten Modellen sehen. Die Algorithmen, die beim automatisierten maschinellen Lernen eingesetzt werden, weisen eine inhärente Zufälligkeit auf, die zu geringfügigen Abweichungen bei den im Experiment ausgegebenen Modellen und der abschließenden metrischen Bewertung eines empfohlenen Modells führen kann, z. B. bei der Genauigkeit. Sie werden wahrscheinlich auch Ergebnisse mit demselben Modellnamen, aber unterschiedlichen verwendeten Hyperparametern erhalten. 

Für automatisierte ML-Experimente erstellen Sie ein `Experiment`-Objekt, wobei es sich um ein benanntes Objekt in einem `Workspace` handelt, das zur Ausführung von Experimenten verwendet wird.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Übermitteln Sie das auszuführende Experiment, um ein Modell zu generieren. Übergeben Sie die Methode `AutoMLConfig` an die `submit`-Methode, um das Modell zu generieren.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Abhängigkeiten werden zunächst auf einem neuen Computer installiert.  Es dauert bis zu 10 Minuten, bevor die Ausgabe angezeigt wird.
>Wenn Sie `show_output` auf `True` festlegen, wird die Ausgabe auf der Konsole angezeigt.

### <a name="multiple-child-runs-on-clusters"></a>Mehrere untergeordnete Ausführungen auf Clustern

Untergeordnete Ausführungen von automatisierten ML-Experimenten können auf einem Cluster durchgeführt werden, auf dem bereits ein anderes Experiment ausgeführt wird. Das Timing hängt jedoch davon ab, über wie viele Knoten der Cluster verfügt und ob diese Knoten zur Ausführung eines anderen Experiments zur Verfügung stehen.

Jeder Knoten im Cluster fungiert als einzelner virtueller Computer (VM), der eine einzelne Trainingsausführung durchführen kann. Für automatisiertes ML bedeutet dies eine untergeordnete Ausführung. Wenn alle Knoten ausgelastet sind, wird das neue Experiment in die Warteschlange gestellt. Wenn es jedoch freie Knoten gibt, führt das neue Experiment untergeordnete Ausführungen von automatisiertem ML parallel auf den verfügbaren Knoten/VMs aus.

Um die Verwaltung von untergeordneten Ausführungen und deren Zeitpunkt der Durchführung zu erleichtern, empfehlen wir Ihnen, einen dedizierten Cluster pro Experiment zu erstellen und die Anzahl von `max_concurrent_iterations` Ihres Experiments an die Anzahl der Knoten im Cluster anzupassen. Auf diese Weise verwenden Sie alle Clusterknoten gleichzeitig mit der von Ihnen gewünschten Anzahl von gleichzeitigen untergeordneten Ausführungen/Iterationen.

Konfigurieren Sie `max_concurrent_iterations` in Ihrem `AutoMLConfig`-Objekt. Wenn es nicht konfiguriert ist, ist standardmäßig nur eine gleichzeitige untergeordnete Ausführung/Iteration pro Experiment zulässig.
Im Fall einer Compute-Instanz kann `max_concurrent_iterations` auf die gleiche Anzahl von Kernen wie auf dem virtuellen Computer der Compute-Instanz festgelegt werden.

## <a name="explore-models-and-metrics"></a>Untersuchen von Modellen und Metriken

Automatisiertes ML bietet Ihnen Möglichkeiten zur Überwachung und Auswertung Ihrer Trainingsergebnisse. 

* Sie können Ihre Trainingsergebnisse in einem Widget oder in der Inlineansicht anzeigen, wenn Sie ein Notebook verwenden. Weitere Informationen finden Sie unter [Überwachen automatisierter Machine Learning-Ausführungen](#monitor).

* Definitionen und Beispiele zu den Leistungsdiagrammen und -Metriken, die für jede Ausführung angegeben werden, finden Sie unter [Auswerten der Ergebnisse von Experimenten des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md). 

* Eine Zusammenfassung der Featurisierung und Informationen zu den Features, die zu einem bestimmten Modell hinzugefügt wurden, finden Sie unter [Transparenz der Featurisierung](how-to-configure-auto-features.md#featurization-transparency). 

Sie können die Hyperparameter, die Skalierungs- und Normalisierungstechniken und den Algorithmus, der auf einen bestimmten automatisierten ML-Lauf angewendet wurde, mit der folgenden benutzerdefinierten Codelösung anzeigen. 

Im folgenden wird die benutzerdefinierte-Methode definiert, welche `print_model()` die Hyperparameter für jeden Schritt der automatisierten ML-Trainings Pipeline ausdruckt.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

Für eine lokale Ausführung oder Remoteausführung, die über dasselbe Experimentnotebook eingereicht und trainiert wurde, können Sie das beste Modell anhand der `get_output()`-Methode übergeben. 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

Die folgende Ausgabe zeigt an, dass:
 
* Die StandardScalerWrapper-Technik zum Skalieren und Normalisieren der Daten vor dem Training verwendet wurde.

* Der XGBoostClassifier-Algorithmus wurde als beste Ausführung identifiziert und zeigt auch die Hyperparameterwerte an. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

Für eine vorhandene Ausführung aus einem anderen Experiment in Ihrem Arbeitsbereich erhalten Sie die spezifische Ausführungs-ID, die Sie untersuchen möchten, und an die `print_model()`Methode übergeben wollen. 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Überwachen automatisierter Machine Learning-Ausführungen

Damit Sie bei Ausführungen mit automatisiertem ML auf die Diagramme aus einer vorherigen Ausführung zugreifen können, ersetzen Sie `<<experiment_name>>` durch den Namen des entsprechenden Experiments:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-Widget für automatisiertes Machine Learning](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="test-models-preview"></a>Testmodelle (Vorschau)

>[!IMPORTANT]
> Das Testen Ihrer Modelle mit einem Testdataset zum Bewerten der durch automatisiertes maschinelles Lernen generierten Modelle ist eine Previewfunktion. Diese Funktion ist eine [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktion, die jederzeit geändert werden kann.

Das Übergeben der Parameter `test_data` oder `test_size` an `AutoMLConfig` löst automatisch einen Remotetestlauf aus, der die bereitgestellten Testdaten verwendet, um das beste Modell zu bewerten, das durch automatisiertes maschinelles Lernen nach Abschluss des Experiments empfohlen wird. Dieser Remotetestlauf erfolgt am Ende des Experiments, nachdem das beste Modell ermittelt wurde. Erfahren Sie, wie Sie [Testdaten an Ihre `AutoMLConfig` übergeben](how-to-configure-cross-validation-data-splits.md#provide-test-data-preview). 

### <a name="get-test-run-results"></a>Abrufen der Ergebnisse des Testlaufs 

Sie können die Vorhersagen und Metriken aus dem Remotetestlauf in [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#view-remote-test-run-results-preview) oder mit dem folgenden Code abrufen. 

```python
best_run, fitted_model = remote_run.get_output()
test_run = next(best_run.get_children(type='automl.model_test'))
test_run.wait_for_completion(show_output=False, wait_post_processing=True)

# Get test metrics
test_run_metrics = test_run.get_metrics()
for name, value in test_run_metrics.items():
    print(f"{name}: {value}")

# Get test predictions as a Dataset
test_run_details = test_run.get_details()
dataset_id = test_run_details['outputDatasets'][0]['identifier']['savedId']
test_run_predictions = Dataset.get_by_id(workspace, dataset_id)
predictions_df = test_run_predictions.to_pandas_dataframe()

# Alternatively, the test predictions can be retrieved via the run outputs.
test_run.download_file("predictions/predictions.csv")
predictions_df = pd.read_csv("predictions.csv")

```

### <a name="test-existing-automated-ml-model"></a>Testen eines vorhandenen Modells für automatisiertes maschinelles Lernen

Führen Sie zum Testen anderer vorhandener Modelle für automatisiertes maschinelles Lernen (beste oder untergeordnete Ausführung) [`ModelProxy()`](/python/api/azureml-train-automl-client/azureml.train.automl.model_proxy.modelproxy) aus, um ein Modell zu testen, nachdem die Hauptausführung des automatisierten maschinellen Lernens abgeschlossen wurde. `ModelProxy()` gibt bereits die Vorhersagen und Metriken zurück und erfordert keine weitere Verarbeitung zum Abrufen der Ausgaben.

> [!NOTE]
> ModelProxy ist eine [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Vorschauklasse und kann jederzeit geändert werden.

Der folgende Code veranschaulicht das Testen eines Modells aus einer beliebigen Ausführung mithilfe der [ModelProxy.test()](/python/api/azureml-train-automl-client/azureml.train.automl.model_proxy.modelproxy#test-test-data--azureml-data-abstract-dataset-abstractdataset--include-predictions-only--bool---false-----typing-tuple-azureml-data-abstract-dataset-abstractdataset--typing-dict-str--typing-any--)-Methode. Sie können in der test()-Methode mithilfe des Parameters `include_predictions_only` angeben, dass nur die Vorhersagen des Testlaufs angezeigt werden sollen. 

```python
from azureml.train.automl.model_proxy import ModelProxy

model_proxy = ModelProxy(child_run=my_run, compute_target=cpu_cluster)
predictions, metrics = model_proxy.test(test_data, include_predictions_only= True
)
```

## <a name="register-and-deploy-models"></a>Registrieren und Bereitstellen von Modellen

Nachdem Sie ein Modell getestet und bestätigt haben, dass Sie es in der Produktion verwendet möchten, können Sie es für die spätere Verwendung registrieren und 

Zum Registrieren eines Modells aus einer Ausführung für das automatisierte maschinelle Lernen verwenden Sie die [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)-Methode. 

```Python

best_run = run.get_best_child()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Details zum Erstellen einer Bereitstellungskonfiguration und Bereitstellen eines registrierten Modells in einem Webdienst finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration).

> [!TIP]
> Für registrierte Modelle steht in [Azure Machine Learning Studio](https://ml.azure.com) die Bereitstellung mit nur einem Klick zur Verfügung. Weitere Informationen finden Sie unter [Bereitstellen registrierter Modelle aus Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretierbarkeit von Modellen

Die Interpretierbarkeit von Modellen ermöglicht es Ihnen, zu verstehen, warum Ihre Modelle Vorhersagen erstellt haben, und die zugrunde liegenden Featurewichtigkeitswerte zu verstehen. Das SDK enthält verschiedene Pakete zum Aktivieren von Modellinterpretierbarkeitsfeatures, sowohl für die Trainings- als auch die Rückschlusszeit, für lokale und bereitgestellte Modelle.

Weitere Informationen zum Aktivieren von Interpretierbarkeitsfeatures insbesondere in Experimenten mit automatisiertem ML finden Sie unter [Interpretierbarkeit: Modellerklärungen beim automatisierten maschinellen Lernen (Vorschau)](how-to-machine-learning-interpretability-automl.md).

Allgemeine Informationen dazu, wie Modellerklärungen und Featurewichtigkeit in anderen Bereichen des SDK außerhalb des automatisierten maschinellen Lernens aktiviert werden können, finden Sie im Artikel [Modellinterpretierbarkeit in Azure Machine Learning (Vorschauversion)](how-to-machine-learning-interpretability.md).

> [!NOTE]
> Das ForecastTCN-Modell wird aktuell vom Erklärungsclient nicht unterstützt. Dieses Modell gibt kein Erklärungsdashboard zurück, wenn es als bestes Modell zurückgegeben wird, und es unterstützt keine Erklärungsläufe auf Anforderung.

## <a name="next-steps"></a>Nächste Schritte

+ Lernen Sie, [wie und wo Sie Modelle bereitstellen](how-to-deploy-and-where.md) können.

+ Weitere Informationen finden Sie unter [Trainieren eines Regressionsmodells mit automatisiertem Machine Learning](tutorial-auto-train-models.md).

+ [Behandeln Sie Probleme bei automatisierten ML-Experimenten.](how-to-troubleshoot-auto-ml.md) 
