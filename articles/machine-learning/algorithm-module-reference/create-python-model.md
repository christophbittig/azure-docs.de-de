---
title: 'Create Python Model: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe des Moduls „Create Python Model“ in Azure Machine Learning ein benutzerdefiniertes Modellierungs- oder Datenverarbeitungsmodul erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/18/2021
ms.openlocfilehash: 08cf192a650c592d7e5624c556ee8dfc766292fa
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831620"
---
# <a name="create-python-model-module"></a>Modul „Create Python Model“

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Erfahren Sie, wie Sie das „Create Python Model“-Modul verwenden, um anhand eines Python-Skripts ein untrainiertes Modell zu erstellen. Sie können das Modell auf der Grundlage jedes Lernmodells erstellen, das in einem Python-Paket in der Azure Machine Learning-Designer-Umgebung enthalten ist. 

Nachdem Sie das Modell erstellt haben, können Sie es mithilfe von [Train Model](train-model.md) wie alle anderen Learner in Azure Machine Learning mit einem Dataset trainieren. Das trainierte Modell kann an [Score Model](score-model.md) übergeben werden, um Vorhersagen zu treffen. Anschließend können Sie das trainierte Modell speichern und den Bewertungsworkflow als Webdienst veröffentlichen.

> [!WARNING]
> Derzeit ist es nicht möglich, dieses Modul mit dem Modul **Tune Model Hyperparameters** (Modellhyperparameter abstimmen) zu verbinden oder das bewertete Ergebnis eines Python-Modells an [Evaluate Model](evaluate-model.md) (Modell auswerten) zu übergeben. Wenn Sie die Hyperparameter abstimmen oder ein Modell auswerten müssen, können Sie mit dem Modul [Execute Python Script](execute-python-script.md) (Python-Skript ausführen) ein benutzerdefiniertes Python-Skript schreiben.


## <a name="configure-the-module"></a>Konfigurieren des Moduls

Die Verwendung dieses Moduls setzt gute oder fortgeschrittene Kenntnisse von Python voraus. Das Modul unterstützt die Verwendung jedes Learners, der in den bereits in Azure Machine Learning installierten Python-Paketen enthalten ist. Die Liste der vorinstallierten Python-Pakete finden Sie unter [Execute Python Script](execute-python-script.md).

> [!NOTE]
> Gehen Sie beim Schreiben Ihres Skripts sehr vorsichtig vor, und stellen Sie sicher, dass keine Syntaxfehler vorliegen, z. B. das Verwenden eines nicht deklarierten Objekts oder eines nicht importierten Moduls.

> [!NOTE]
> Widmen Sie außerdem der Liste der vorinstallierten Module in [Ausführen des Python-Skripts](execute-python-script.md) besondere Aufmerksamkeit. Importieren Sie nur vorinstallierte Module. Installieren Sie in diesem Skript keine zusätzlichen Pakete, wie z. B. „pip install xgboost“, da andernfalls Fehler beim Lesen von Modellen in Downstreammodulen ausgelöst werden.
  
In diesem Artikel wird anhand einer einfachen Pipeline erläutert, wie Sie **Create Python Model** verwenden. Hier folgt ein Diagramm der Pipeline:

![Diagramm zu „Create Python Model“](./media/module/create-python-model.png)

1. Wählen Sie **Create Python Model** aus, und bearbeiten Sie das Skript zum Implementieren Ihres Modellierungs- oder Datenverwaltungsprozesses. Sie können das Modell auf Grundlage jedes Learners erstellen, der in einem Python-Paket in der Azure Machine Learning-Umgebung enthalten ist.

> [!NOTE]
> Achten Sie besonders auf die Kommentare im Beispielcode des Skripts, und stellen Sie sicher, dass Ihr Skript strikt die Anforderungen einhält, einschließlich des Klassennamens, der Methoden sowie der Methodensignatur. Verstöße verursachen Ausnahmen. 
> Beim **Erstellen eines Python-Modells** wird nur das Erstellen eines sklearn-basierten Modells unterstützt, das mit **Modell trainieren** trainiert werden soll.

   Der folgende Beispielcode der Naive Bayes-Klassifizierung mit zwei Klassen verwendet das beliebte *sklearn*-Paket:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Verbinden Sie das soeben erstellte **Create Python Model**-Modul mit **Train Model** und **Score Model**.

3. Wenn Sie das Modell auswerten müssen, fügen Sie ein [Execute Python Script](execute-python-script.md)-Modul hinzu, und bearbeiten Sie das Python-Skript.

   Das folgende Skript ist ein Beispiel für einen Auswertungscode:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 