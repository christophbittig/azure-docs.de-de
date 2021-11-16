---
title: Modellerklärbarkeit beim automatisierten maschinellen Lernen (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Ihr automatisiertes Machine Learning-Modell die Featurerelevanz ermittelt und beim Verwenden des Azure Machine Learning SDK Vorhersagen trifft.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl, responsible-ml, mktng-kw-nov2021
ms.author: mithigpe
author: minthigpen
ms.date: 10/21/2021
ms.openlocfilehash: f739da0dde7f2ef9935466ebbd4e4d4498355263
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852615"
---
# <a name="interpretability-model-explainability-in-automated-ml-preview"></a>Interpretierbarkeit: Modellerklärbarkeit beim automatisierten maschinellen Lernen (Vorschau)


In diesem Artikel erfahren Sie, wie Sie in Azure Machine Learning Erklärungen für automatisierte Machine Learning-Modelle (automatisiertes ML) unter Verwendung des Python SDK erhalten können. Automatisiertes maschinelles Lernen hilft Ihnen dabei, die Featurerelevanz der generierten Modelle zu verstehen. 

Alle SDK-Versionen nach 1.0.85 sind standardmäßig auf `model_explainability=True` festgelegt. In SDK Version 1.0.85 und in früheren Versionen müssen die Benutzer `model_explainability=True` im `AutoMLConfig`-Objekt festlegen, um die Interpretierbarkeit des Modells zu nutzen. 


In diesem Artikel werden folgende Vorgehensweisen behandelt:

- Ausführen von Interpretierbarkeit während des Trainings für das beste oder ein beliebiges Modell.
- Aktivieren von Visualisierungen, um Muster in Daten und Erläuterungen zu erkennen.
- Implementieren von Interpretierbarkeit beim Ziehen von Rückschlüssen oder bei der Bewertung.

## <a name="prerequisites"></a>Voraussetzungen

- Interpretierbarkeitsfeatures. Führen Sie `pip install azureml-interpret` aus, um das erforderliche Paket abzurufen.
- Vorkenntnisse in Bezug auf die Erstellung von Experimenten für automatisiertes ML. Weitere Informationen zur Verwendung des Azure Machine Learning SDK finden Sie in diesem [Regressionsmodelltutorial](tutorial-auto-train-models.md) oder unter [Konfigurieren automatisierter ML-Experimente](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretierbarkeit während des Trainings nach dem besten Modell

Rufen Sie die Erklärung aus `best_run` ab, darin sind Erklärungen für unbearbeitete und entwickelte Features enthalten.

> [!NOTE]
> Interpretierbarkeit, Modellerklärung, ist nicht für das TCNForecaster-Modell verfügbar, das von Experimenten zur Vorhersage mit automatischem maschinellem Lernen empfohlen wird.

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Herunterladen der Relevanzen der entwickelten Features aus dem besten Lauf

Sie können mithilfe von `ExplanationClient` Erklärungen zu entwickelten Features aus dem Artefaktspeicher von `best_run` herunterladen. 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Herunterladen der Relevanzen der Rohfeatures aus dem besten Lauf

Sie können mithilfe von `ExplanationClient` Erklärungen zu den Rohfeatures aus dem Artefaktspeicher von `best_run` herunterladen.

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretierbarkeit während des Trainings nach einem beliebigen Modell 

Wenn Sie Modellerklärungen berechnen und visualisieren, sind Sie nicht auf eine vorhandene Modellerklärung für ein Modell zum automatisierten maschinellen Lernen beschränkt. Sie können auch eine Erläuterung für das Modell mit anderen Testdaten erhalten. Die Schritte in diesem Abschnitt zeigen, wie die Relevanz von entwickelten Features basierend auf Ihren Testdaten berechnet und visualisiert wird.

### <a name="retrieve-any-other-automl-model-from-training"></a>Abrufen eines anderen AutoML-Modells aus dem Training

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Einrichten der Modellerklärungen

Verwenden Sie `automl_setup_model_explanations`, um Erläuterungen zu den entwickelten und Rohfeatures abzurufen. `fitted_model` kann die folgenden Elemente generieren:

- Empfohlene Daten aus trainierten oder Testbeispielen
- Listen der entwickelten Featurenamen
- Auffindbare Klassen in Ihrer bezeichneten Spalte in Klassifizierungsszenarien

`automl_explainer_setup_obj` enthält alle Strukturen aus der oben aufgeführten Liste.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initialisieren von MimicExplainer für Featurerelevanz

Verwenden Sie die `MimicWrapper`-Klasse, um eine Erläuterung für automatisierte ML-Modelle zu generieren. Sie können die MimicWrapper-Klasse mit den folgenden Parametern initialisieren:

- Dem Erklärmoduleinrichtungsobjekt
- Ihrem Arbeitsbereich
- Ein Surrogatmodell zur Erklärung des automatisierten ML-Modells `fitted_model`

Die MimicWrapper-Klasse verwendet außerdem das `automl_run`-Objekt, in das die Erklärungen für entwickelte Features hochgeladen werden.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Verwenden von MimicExplainer zum Berechnen und Visualisieren der Relevanz entwickelter Features

Sie können die `explain()`-Methode in MimicWrapper mit den transformierten Testbeispielen aufrufen, um die Featurerelevanz für die generierten entwickelten Features abzurufen. Sie können sich auch bei [Azure Machine Learning](https://ml.azure.com/) anmelden, um die Erklärungen zur Dashboardvisualisierung der Featurerelevanzwerte der Featurizer für automatisiertes maschinelles Lernen generierten entwickelten Features anzuzeigen.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
Für Modelle, die mit automatisiertem ML trainiert werden, können Sie das beste Modell mithilfe der `get_output()` -Methode und der COMPUTE-Erläuterungen lokal erhalten.  Sie können die Erklärungsergebnisse mit `ExplanationDashboard` aus dem Paket `raiwidgets` visualisieren.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from raiwidgets import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from raiwidgets import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Verwenden von MimicExplainer zum Berechnen und Visualisieren der Relevanz von Rohfeatures

Sie können die `explain()`-Methode in MimicWrapper mit den transformierten Testbeispielen aufrufen, um die Featurerelevanz für die Rohfeatures abzurufen. Darüber hinaus können Sie im [Machine Learning Studio](https://ml.azure.com/) eine Dashboardvisualisierung der Relevanzwerte der Rohfeatures anzeigen.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpretierbarkeit beim Ziehen von Rückschlüssen

In diesem Abschnitt erfahren Sie, wie ein Modell für automatisiertes ML mit dem Erklärmodul operationalisiert wird, das im vorherigen Abschnitt zum Berechnen der Erklärungen verwendet wurde.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrieren des Modells und des Erklärmoduls für die Bewertung

Verwenden Sie `TreeScoringExplainer`, um das Erklärmodul für die Bewertung zu erstellen. Mit diesem werden beim Ziehen von Rückschlüssen die Relevanzwerte für die entwickelten Features berechnet. Sie initialisieren das Erklärmodul für die Bewertung mit dem zuvor berechneten `feature_map`-Element. 

Speichern Sie das Erklärmodul für die Bewertung, und registrieren das Modell und das Erklärmodul für die Bewertung dann mit dem Modellverwaltungsdienst. Führen Sie den folgenden Code aus:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Erstellen der conda-Abhängigkeiten zum Einrichten des Diensts

Erstellen Sie dann die erforderlichen Umgebungsabhängigkeiten im Container für das bereitgestellte Modell. Beachten Sie, dass azureml-defaults mit Version >= 1.0.45 als PIP-Abhängigkeit gelistet sein muss, da sie die Funktionalität enthält, die zum Hosten des Modells als Webdienst erforderlich ist.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Erstellen des Bewertungsskripts

Schreiben Sie ein Skript, das Ihr Modell lädt und Vorhersagen und Erklärungen auf Basis eines neuen Datenbatches erstellt.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Bereitstellen des Diensts

Stellen Sie den Dienst unter Verwendung der conda-Datei und der Bewertungsdatei aus den vorherigen Schritten bereit.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Rückschluss mit Testdaten

Führen Sie mit einigen Testdaten einen Rückschluss aus, um den vorhergesagten Wert aus dem Modell für automatisiertes maschinelles Lernen anzuzeigen. Dies wird derzeit nur über das Azure Machine Learning SDK unterstützt. Zeigen Sie die Featurerelevanzen an, die zu einem vorhergesagten Wert beitragen. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualisieren, um Muster in Daten und Erläuterungen zur Trainingszeit zu ermitteln

Sie können das Diagramm zur Featurerelevanz in Ihrem Arbeitsbereich in [Azure Machine Learning Studio](https://ml.azure.com) anzeigen. Wählen Sie nach Abschluss der Ausführung des automatisierten maschinellen Lernens **Modelldetails anzeigen** aus, um eine bestimmte Ausführung anzuzeigen. Klicken Sie auf die Registerkarte **Erklärungen**, um die Visualisierungen im Erklärungsdashboard anzuzeigen.

[![Architektur von Machine Learning Interpretability](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Weitere Informationen zur Visualisierung von Erläuterungsdashboards und bestimmten Plots finden Sie im [Leitfaden zur Interpretierbarkeit](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie Modellerklärungen und Featurerelevanz in anderen Bereichen als dem automatisierten maschinellen Lernen aktivieren können, finden Sie unter [Weitere Techniken für die Interpretierbarkeit von Modellen](how-to-machine-learning-interpretability.md).
