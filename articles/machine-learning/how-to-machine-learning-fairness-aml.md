---
title: Bewerten der Fairness von ML-Modellen in Python (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von Fairlearn und dem Python-SDK für Azure Machine Learning die Fairness Ihrer Machine Learning-Modelle beurteilen und minimieren können.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, responsible-ml
ms.openlocfilehash: 635bde6c0d932ed0c77abb27d55770f52b59a1b9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562445"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Verwenden von Azure Machine Learning mit dem Open-Source-Paket Fairlearn zur Bewertung der Fairness von ML-Modellen (Vorschau)

In dieser Schrittanleitung erfahren Sie, wie Sie das Open-Source-Python-Paket [Fairlearn](https://fairlearn.github.io/) mit Azure Machine Learning verwenden, um die folgenden Aufgaben auszuführen:

* Bewerten Sie die Fairness Ihrer Modellvorhersagen. Weitere Informationen zur Fairness im Bereich maschinelles Lernen finden Sie im Artikel [Fairness bei Machine Learning-Modellen](concept-fairness-ml.md).
* Laden Sie Erkenntnisse aus der Fairnessbewertung in Azure Machine Learning Studio hoch, listen Sie sie dort auf, und laden Sie sie dort herunter.
* Sehen Sie sich ein Fairnessbewertungsdashboard in Azure Machine Learning Studio an, um mit den Erkenntnissen zur Fairness für Ihr(e) Modell(e) zu interagieren.

>[!NOTE]
> Eine Fairnessbewertung ist keine rein technische Angelegenheit. **Dieses Paket kann Ihnen dabei helfen, die Fairness eines Machine Learning-Modells zu bewerten, aber nur Sie können das Modell konfigurieren und Entscheidungen in Bezug auf dessen Leistung treffen.**  Zwar hilft es dabei, quantitative Metriken zur Bewertung der Fairness zu identifizieren, Entwickler von Machine Learning-Modellen müssen jedoch auch eine qualitative Analyse durchführen, um die Fairness ihrer Modelle zu bewerten.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning-Fairness-SDK 

Das Azure Machine Learning-Fairness-SDK (`azureml-contrib-fairness`) integriert das Open-Source-Python-Paket [Fairlearn](http://fairlearn.github.io) in Azure Machine Learning. Weitere Informationen zur Integration von Fairlearn in Azure Machine Learning finden Sie in diesen [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Weitere Informationen zu Fairlearn finden Sie in der [Anleitung mit Beispielen](https://fairlearn.org/main/auto_examples/) und den [Beispielnotebooks](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Verwenden Sie die folgenden Befehle, um die Pakete `azureml-contrib-fairness` und `fairlearn` zu installieren:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Spätere Versionen von Fairlearn sollten auch im folgenden Beispielcode funktionieren.



## <a name="upload-fairness-insights-for-a-single-model"></a>Hochladen von Erkenntnissen zur Fairness für ein einzelnes Modell

Das folgende Beispiel zeigt die Verwendung des Fairnesspakets. Wir laden die Erkenntnisse zur Modellfairness in Azure Machine Learning hoch und sehen das Fairnessbewertungsdashboard in Azure Machine Learning Studio.

1. Trainieren Sie ein Beispielmodell in Jupyter Notebook. 

    Als Dataset verwenden Sie das bekannte Zensusdataset mit Angaben zu Erwachsenen, das Sie über OpenML abrufen. Unser Ausgangspunkt ist, dass bei der Kreditvergabeentscheidung ein Problem mit der Bezeichnung auftritt, die angibt, ob eine Person einen früheren Kredit zurückgezahlt hat. Wir trainieren ein Modell, um vorherzusagen, ob bisher unbekannte Personen einen Kredit zurückzahlen werden. Ein solches Modell kann für Kreditvergabeentscheidungen verwendet werden.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from raiwidgets import FairnessDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in the fairness dashboard, and see the disparities which appear:
    from raiwidgets import FairnessDashboard
    FairnessDashboard(sensitive_features=A_test,
                      y_true=y_test,
                      y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Melden Sie sich bei Azure Machine Learning an, und registrieren Sie Ihr Modell.
   
    Das Fairnessdashboard kann mit registrierten oder nicht registrierten Modellen integriert werden. Registrieren Sie wie folgt Ihr Modell in Azure Machine Learning:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Berechnen Sie die Fairnessmetriken im Voraus.

    Erstellen Sie mit dem Paket `metrics` von Fairlearn ein Dashboardwörterbuch. Die `_create_group_metric_set`-Methode verfügt über ähnliche Argumente wie der Dashboard-Konstruktor, mit dem Unterschied, dass die sensiblen Attribute als Wörterbuch übergeben werden (um sicherzustellen, dass Namen verfügbar sind). Außerdem müssen Sie beim Aufruf dieser Methode den Vorhersagetyp angeben (in diesem Fall Binärklassifizierung).

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Laden Sie die im Voraus berechneten Fairnessmetriken hoch.
    
    Importieren Sie nun das Paket `azureml.contrib.fairness`, um den Upload auszuführen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Erstellen Sie ein Experiment, dann eine Ausführung, und laden Sie das Dashboard dort hoch:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Überprüfen Sie das Fairnessdashboard in Azure Machine Learning Studio.

    Wenn Sie die obigen Schritte ausführen (Hochladen generierter Erkenntnisse zur Fairness in Azure Machine Learning), können Sie das Fairnessdashboard in [Azure Machine Learning Studio](https://ml.azure.com) anzeigen. Dieses Dashboard ist das gleiche Visualisierungsdashboard, das auch in Fairlearn bereitgestellt wird, mit dem Sie die Unterschiede zwischen den Untergruppen Ihres sensiblen Attributs analysieren können (z. B. männlich vs. weiblich).
    Sie haben zwei Möglichkeiten, um auf das Visualisierungsdashboard in Azure Machine Learning-Studio zuzugreifen:

    * **Bereich „Experimente“ (Vorschau)**
    1. Klicken Sie im linken Bereich auf **Experimente**, damit Ihnen eine Liste mit Experimenten angezeigt wird, die Sie in Azure Machine Learning ausgeführt haben.
    1. Wählen Sie ein bestimmtes Experiment aus, um alle Ausführungen in diesem Experiment anzuzeigen.
    1. Klicken Sie auf eine Ausführung und dann auf die Registerkarte **Fairness**, um das Dashboard zur Erklärungsvisualisierung anzuzeigen.
    1. Wenn Sie auf der Registerkarte **Fairness** gelandet sind, klicken Sie im Menü auf der rechten Seite auf eine **Fairness-ID**.
    1. Konfigurieren Sie das Dashboard, indem Sie das sensible Attribut, die Leistungsmetrik und die gewünschte Fairnessmetrik auswählen, um auf der Seite zur Fairnessbewertung zu landen.
    1. Wechseln Sie von einem Diagrammtyp zum anderen, um sowohl Schäden bei der **Speicherbelegung** als auch bei der **Servicequalität** zu beobachten.



    [![Fairnessdashboard: Speicherbelegung](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![Fairnessdashboard: Servicequalität](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Bereich „Modelle“**
    1. Wenn Sie Ihr ursprüngliches Modell mit den obigen Schritten registriert haben, können Sie im linken Bereich auf **Modelle** klicken, um es anzuzeigen.
    1. Klicken Sie auf ein Modell und dann auf die Registerkarte **Fairness**, um das Dashboard zur Erklärungsvisualisierung anzuzeigen.

    Weitere Informationen zum Visualisierungsdashboard und den enthaltenen Funktionen finden Sie im [Fairlearn-Benutzerhandbuch](https://fairlearn.org/main/user_guide/assessment.html#fairlearn-dashboard).

## <a name="upload-fairness-insights-for-multiple-models"></a>Hochladen von Erkenntnissen zur Fairness für mehrere Modelle

Um mehrere Modelle zu vergleichen und zu sehen, wie sich deren Fairnessbewertungen unterscheiden, können Sie mehrere Modelle an das Visualisierungsdashboard übergeben und ihre jeweiligen Kompromisse zwischen Leistung und Fairness vergleichen.

1. Trainieren Sie Ihre Modelle:
    
    Erstellen Sie nun auf einem Support-Vector-Machine-Schätzer basierend einen zweiten Klassifizierer, und laden Sie mithilfe des Pakets `metrics` von Fairlearn ein Fairnessdashboard-Wörterbuch hoch. Wir gehen davon aus, dass das zuvor trainierte Modell weiterhin verfügbar ist.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Registrieren Sie Ihre Modelle.

    Registrieren Sie als Nächstes beide Modelle in Azure Machine Learning. Speichern Sie die Ergebnisse zur Vereinfachung in einem Wörterbuch, das die `id` des registrierten Modells (Zeichenfolge im Format `name:version`) dem Prädiktor selbst zuordnet:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Laden Sie das Fairness-Dashboard lokal.

    Bevor Sie die Erkenntnisse zur Fairness in Azure Machine Learning hochladen, können Sie die Vorhersagen in einem lokal aufgerufenen Fairness-Dashboard untersuchen. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from raiwidgets import FairnessDashboard

    FairnessDashboard(sensitive_features=A_test,
                      y_true=y_test.tolist(),
                      y_pred=ys_pred)
    ```

3. Berechnen Sie die Fairnessmetriken im Voraus.

    Erstellen Sie mit dem Paket `metrics` von Fairlearn ein Dashboardwörterbuch.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Laden Sie die im Voraus berechneten Fairnessmetriken hoch.
    
    Importieren Sie nun das Paket `azureml.contrib.fairness`, um den Upload auszuführen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Erstellen Sie ein Experiment, dann eine Ausführung, und laden Sie das Dashboard dort hoch:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Wie im vorherigen Abschnitt können Sie auf eine der oben beschriebenen Arten (über **Experimente** oder **Modelle**) in Azure Machine Learning Studio auf das Visualisierungsdashboard zugreifen und die beiden Modelle hinsichtlich der Fairness und Leistung vergleichen.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Hochladen von nicht entschärften und entschärften Erkenntnissen zur Fairness

Sie können die [Entschärfungsalgorithmen](https://fairlearn.org/main/user_guide/mitigation.html) von Fairlearn verwenden, die von diesen generierten entschärften Modelle mit dem ursprünglichen, nicht entschärften Modell vergleichen und durch die jeweiligen Kompromisse zwischen Leistung und Fairness der verglichenen Modelle navigieren.

Ein Beispiel für die Verwendung des [Rastersuche-Entschärfungsalgorithmus](https://fairlearn.org/main/user_guide/mitigation.html#grid-search) (der mehrere entschärfte Modelle mit unterschiedlichen Kompromissen zwischen Fairness und Leistung erstellt) finden Sie in diesem [Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Das Hochladen von Erkenntnissen zur Fairness für mehrere Modelle in einer einzigen Ausführung ermöglicht einen Vergleich der Modelle in Bezug auf Fairness und Leistung. Sie können auf jedes der im Modellvergleichsdiagramm angezeigten Modelle klicken, um die Erkenntnisse zur Fairness des jeweiligen Modells im Detail anzuzeigen.


[![Fairness-Dashboard für Modellvergleiche](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über Modellfairness.](concept-fairness-ml.md)

[Sehen Sie sich Azure Machine Learning-Beispielnotebooks zur Fairness an.](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
