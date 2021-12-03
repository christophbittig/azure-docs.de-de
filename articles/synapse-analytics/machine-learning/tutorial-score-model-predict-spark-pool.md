---
title: 'Tutorial: Bewerten von Machine Learning-Modellen mit PREDICT in serverlosen Apache Spark-Pools'
description: Hier erfahren Sie, wie Sie die PREDICT-Funktion in serverlosen Apache Spark-Pools verwenden, um Bewertungen unter Verwendung von Machine Learning-Modellen vorherzusagen.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 45338fa4aed963d2172af25fec680e2017c98e88
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549886"
---
# <a name="tutorial-score-machine-learning-models-with-predict-in-serverless-apache-spark-pools"></a>Tutorial: Bewerten von Machine Learning-Modellen mit PREDICT in serverlosen Apache Spark-Pools

Hier erfahren Sie, wie Sie die PREDICT-Funktion in serverlosen Apache Spark-Pools in Azure Synapse Analytics verwenden, um Bewertungen vorherzusagen. Sie können ein trainiertes Modell verwenden, das in Azure Machine Learning (AML) oder in der Standardinstanz von Azure Data Lake Storage (ADLS) in Ihrem Synapse-Arbeitsbereich registriert ist.

Wenn Sie PREDICT in einem Synapse PySpark-Notebook verwenden, können Sie Machine Learning-Modelle mithilfe von SQL, benutzerdefinierten Funktionen (User Defined Functions, UDFs) oder Transformatoren bewerten. Mit PREDICT können Sie bereits vorhandene Machine Learning-Modelle, die außerhalb von Synapse trainiert und in Azure Data Lake Storage Gen2 oder Azure Machine Learning registriert wurden, verwenden, um historische Daten innerhalb der sicheren Grenzen von Azure Synapse Analytics zu bewerten. Die PREDICT-Funktion akzeptiert ein Modell und Daten als Eingaben. Dank dieses Features müssen für die Bewertung keine wertvollen Daten mehr an einen Speicherort außerhalb von Synapse verschoben werden. Dadurch sollen Modellconsumer mühelos Machine Learning-Modelle in Synapse ableiten sowie nahtlos mit Modellproducern zusammenarbeiten können, die das passende Framework für die jeweilige Aufgabe verwenden.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Vorhersagen von Bewertungen für Daten in einem serverlosen Apache Spark-Pool mithilfe von Machine Learning-Modellen, die außerhalb von Synapse trainiert und in Azure Machine Learning oder Azure Data Lake Storage Gen2 registriert werden

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Ein serverloser Apache Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../get-started-analyze-spark.md).
- Ein Azure Machine Learning-Arbeitsbereich ist erforderlich, wenn Sie das Modell in Azure Machine Learning trainieren oder registrieren möchten. Ausführliche Informationen finden Sie unter [Verwalten von Azure Machine Learning-Arbeitsbereichen im Portal oder mit dem Python SDK](../../machine-learning/how-to-manage-workspace.md).
- Wenn Ihr Modell in Azure Machine Learning registriert ist, benötigen Sie einen verknüpften Dienst. In Azure Synapse Analytics definiert ein verknüpfter Dienst Ihre Verbindungsinformationen für den Dienst. In diesem Tutorial wird ein verknüpfter Dienst für Azure Synapse Analytics und Azure Machine Learning hinzugefügt. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines neuen verknüpften Azure Machine Learning-Diensts in Synapse](quickstart-integrate-azure-machine-learning.md).
- Für die PREDICT-Funktion müssen Sie bereits über ein trainiertes Modell verfügen, das entweder in Azure Machine Learning registriert oder in Azure Data Lake Storage Gen2 hochgeladen wurde.

> [!NOTE]
> - Das PREDICT-Feature wird in Azure Synapse Analytics in serverlosen Apache Spark-Pools mit **Spark3** unterstützt. Für die Modellerstellung und das Training wird die Version **Python 3.8** empfohlen. 
> - PREDICT unterstützt die meisten Machine Learning-Modellpakete im Format **MLflow**: In dieser Vorschauversion werden **TensorFlow, ONNX, PyTorch, SkLearn und pyfunc** unterstützt.
> - PREDICT unterstützt **AML und ADLS** als Modellquelle. Das ADLS-Konto bezieht sich hier auf das **ADLS-Standardkonto des Synapse-Arbeitsbereichs**. 


## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.


## <a name="use-predict-for-mlflow-packaged-models"></a>Verwenden von PREDICT für mit MLFLOW gepackte Modelle

Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind, bevor Sie die folgenden Schritte für die Verwendung von PREDICT ausführen.

1. **Importieren von Bibliotheken:** Importieren Sie die folgenden Bibliotheken, um PREDICT in einer Spark-Sitzung zu verwenden:

   ```python
      #Import libraries
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   ```

2. **Festlegen von Parametern mithilfe von Variablen:** Synapse-ADLS-Datenpfad und Modell-URI müssen mithilfe von Eingabevariablen festgelegt werden. Sie müssen auch die Runtime (in diesem Fall „mlflow“) sowie den Datentyp der zurückgegebenen Modellausgabe definieren. Beachten Sie, dass alle in PySpark unterstützten Datentypen auch über PREDICT unterstützt werden.

   > [!NOTE]
   > Aktualisieren Sie dieses Skript vor der Ausführung mit dem URI für die ADLS Gen2-Datendatei sowie mit dem Rückgabedatentyp der Modellausgabe und dem ADLS-/AML-URI für die Modelldatei.

   ```python
      #Set input data path
      DATA_FILE = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<file path>"
   
      #Set model URI
          #Set AML URI, if trained model is registered in AML
             AML_MODEL_URI = "<aml model uri>" #In URI ":x" signifies model version in AML. You can   choose which model version you want to run. If ":x" is not provided then by default   latest version will be picked.
   
          #Set ADLS URI, if trained model is uploaded in ADLS
             ADLS_MODEL_URI = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<model   mlflow folder path>"
   
      #Define model return type
      RETURN_TYPES = "<data_type>" # for ex: int, float etc. PySpark data types are supported
   
      #Define model runtime. This supports only mlflow
      RUNTIME = "mlflow"
   ```

3. **Möglichkeiten zum Authentifizieren des AML-Arbeitsbereichs:** Wenn das Modell im ADLS-Standardkonto des Synapse-Arbeitsbereichs gespeichert ist, ist keine weitere Einrichtung der Authentifizierung erforderlich. Wenn das Modell in Azure Machine Learning registriert ist, können Sie zwischen den beiden folgenden unterstützten Authentifizierungsmethoden wählen.

   > [!NOTE]
   > Aktualisieren Sie die Details des Mandanten, des Clients, des Abonnements, der Ressourcengruppe, des AML-Arbeitsbereichs und des verknüpften Diensts in diesem Skript, bevor Sie es ausführen.

   - **Über den Dienstprinzipal:** Sie können die Client-ID und das Geheimnis des Dienstprinzipals direkt für die Authentifizierung beim AML-Arbeitsbereich verwenden. Der Dienstprinzipal muss über Zugriff vom Typ „Mitwirkender“ für den AML-Arbeitsbereich verfügen.

      ```python
         #AML workspace authentication using service principal
         AZURE_TENANT_ID = "<tenant_id>"
         AZURE_CLIENT_ID = "<client_id>"
         AZURE_CLIENT_SECRET = "<client_secret>"
  
         AML_SUBSCRIPTION_ID = "<subscription_id>"
         AML_RESOURCE_GROUP = "<resource_group_name>"
         AML_WORKSPACE_NAME = "<aml_workspace_name>"
  
         svc_pr = ServicePrincipalAuthentication( 
             tenant_id=AZURE_TENANT_ID,
             service_principal_id=AZURE_CLIENT_ID,
             service_principal_password=AZURE_CLIENT_SECRET
         )
  
         ws = Workspace(
             workspace_name = AML_WORKSPACE_NAME,
             subscription_id = AML_SUBSCRIPTION_ID,
             resource_group = AML_RESOURCE_GROUP,
             auth=svc_pr
         )
      ```

   - **Über den verknüpften Dienst:** Sie können den verknüpften Dienst für die Authentifizierung beim AML-Arbeitsbereich verwenden. Der verknüpfte Dienst kann für die Authentifizierung den Dienstprinzipal oder die verwaltete Dienstidentität (Managed Service Identity, MSI) des Synapse-Arbeitsbereichs verwenden. Der Dienstprinzipal bzw. die verwaltete Dienstidentität (Managed Service Identity, MSI) muss über Zugriff vom Typ „Mitwirkender“ für den AML-Arbeitsbereich verfügen.

      ```python
         #AML workspace authentication using linked service
         from notebookutils.mssparkutils import azureML
         ws = azureML.getWorkspace("<linked_service_name>") #   "<linked_service_name>" is the linked service name, not AML workspace name. Also, linked   service supports MSI and service principal both
      ```

4. **Aktivieren von PREDICT in einer Spark-Sitzung:** Legen Sie die Spark-Konfiguration `spark.synapse.ml.predict.enabled` auf `true` fest, um die Bibliothek zu aktivieren.

   ```python
      #Enable SynapseML predict
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

5. **Binden eines Modells in einer Spark-Sitzung:** Binden Sie ein Modell mit erforderlichen Eingaben, damit in der Spark-Sitzung auf das Modell verwiesen werden kann. Definieren Sie auch einen Alias, um den gleichen Alias im PREDICT-Aufruf verwenden zu können.

   > [!NOTE]
   > Aktualisieren Sie den Modellalias und den Modell-URI in diesem Skript, bevor Sie es ausführen.

   ```python
      #Bind model within Spark session
       model = pcontext.bind_model(
        return_types=RETURN_TYPES, 
        runtime=RUNTIME, 
        model_alias="<random_alias_name>", #This alias will be used in PREDICT call to refer  this   model
        model_uri=ADLS_MODEL_URI, #In case of AML, it will be AML_MODEL_URI
        aml_workspace=ws #This is only for AML. In case of ADLS, this parameter can be removed
        ).register()
   ```

6. **Lesen von Daten aus ADLS:** Lesen Sie Daten aus ADLS. Erstellen Sie einen Spark-Datenrahmen und eine auf dem Datenrahmen basierende Ansicht.

   > [!NOTE]
   > Aktualisieren Sie den Ansichtsnamen in diesem Skript, bevor Sie es ausführen.

   ```python
      #Read data from ADLS
      df = spark.read \
       .format("csv") \
       .option("header", "true") \
       .csv(DATA_FILE,
           inferSchema=True)
      df.createOrReplaceTempView('<view_name>')
   ```

7. **Generieren einer Bewertung mithilfe von PREDICT:** Sie können PREDICT auf drei Arten aufrufen: über die Spark SQL-API, mithilfe einer benutzerdefinierten Funktion (User-Defined Function, UDF) und mithilfe der Transformator-API. Im Anschluss folgen entsprechende Beispiele.

   > [!NOTE]
   > Aktualisieren Sie den Modellaliasnamen, den Ansichtsnamen und den Namen der kommagetrennten Modelleingabespalte in diesem Skript, bevor Sie es ausführen. Kommagetrennte Modelleingabespalten entsprechen den Spalten, die beim Trainieren des Modells verwendet werden.

   ```python
      #Call PREDICT using Spark SQL API
   
      predictions = spark.sql(
                     """
                         SELECT PREDICT('<random_alias_name>',
                                   <comma_separated_model_input_column_name>) AS predict 
                         FROM <view_name>
                     """
                 ).show()
   ```

   ```python
      #Call PREDICT using user defined function (UDF)
   
      df = df[<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
   
      df.withColumn("PREDICT",model.udf(lit("<random_alias_name>"),*df.columns)).show()
   ```

   ```python
      #Call PREDICT using Transformer API
      
      columns = [<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
      
      tranformer = model.create_transformer().setInputCols(columns).setOutputCol("PREDICT")
   
      tranformer.transform(df).show()
   ```

## <a name="sklearn-example-using-predict"></a>Sklearn-Beispiel mit PREDICT

1. Importieren Sie Bibliotheken, und lesen Sie das Trainingsdataset aus ADLS.

   ```python
      # Import libraries and read training dataset from ADLS
      
      import fsspec
      import pandas
      from fsspec.core import split_protocol
      
      adls_account_name = 'xyz' #Provide exact ADLS account name
      adls_account_key = 'xyz' #Provide exact ADLS account key
      
      fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>',      account_name=adls_account_name, account_key=adls_account_key)
      
      with fsspec_handle.open() as f:
          train_df = pandas.read_csv(f)
   ```

1. Trainieren Sie das Modell, und generieren Sie MLflow-Artefakte.

   ```python
      # Train model and generate mlflow artifacts
   
      import os
      import shutil
      import mlflow
      import json
      from mlflow.utils import model_utils
      import numpy as np
      import pandas as pd
      from sklearn.linear_model import LinearRegression
      
      
      class LinearRegressionModel():
        _ARGS_FILENAME = 'args.json'
        FEATURES_KEY = 'features'
        TARGETS_KEY = 'targets'
        TARGETS_PRED_KEY = 'targets_pred'
      
        def __init__(self, fit_intercept, nb_input_features=9, nb_output_features=1):
          self.fit_intercept = fit_intercept
          self.nb_input_features = nb_input_features
          self.nb_output_features = nb_output_features
      
        def get_args(self):
          args = {
              'nb_input_features': self.nb_input_features,
              'nb_output_features': self.nb_output_features,
              'fit_intercept': self.fit_intercept
          }
          return args
      
        def create_model(self):
          self.model = LinearRegression(fit_intercept=self.fit_intercept)
      
        def train(self, dataset):
      
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
      
          targets = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.TARGETS_KEY])], axis=0)
      
      
          self.model.fit(features, targets)
      
        def predict(self, dataset):
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
          targets_pred = self.model.predict(features)
          return targets_pred
      
        def save(self, path):
          if os.path.exists(path):
            shutil.rmtree(path)
      
          # save the sklearn model with mlflow
          mlflow.sklearn.save_model(self.model, path)
      
          # save args
          self._save_args(path)
      
        def _save_args(self, path):
          args_filename = os.path.join(path, LinearRegressionModel._ARGS_FILENAME)
          with open(args_filename, 'w') as f:
            args = self.get_args()
            json.dump(args, f)
      
      
      def train(train_df, output_model_path):
        print(f"Start to train LinearRegressionModel.")
      
        # Initialize input dataset
        dataset = train_df.to_numpy()
        datasets = {}
        datasets['targets'] = dataset[:, -1]
        datasets['features'] = dataset[:, :9]
      
        # Initialize model class obj
        model_class = LinearRegressionModel(fit_intercept=10)
        with mlflow.start_run(nested=True) as run:
          model_class.create_model()
          model_class.train(datasets)
          model_class.save(output_model_path)
          print(model_class.predict(datasets))
      
      
      train(train_df, './artifacts/output')
   ```

1. Speichern Sie MLflow-Artefakte in ADLS, oder registrieren Sie sie in AML.

   ```python
      # Store model MLFLOW artifacts in ADLS
      
      STORAGE_PATH = 'abfs[s]://<container>/<path-to-store-folder>'
      
      protocol, _ = split_protocol(STORAGE_PATH)
      print (protocol)
   
      storage_options = {
          'account_name': adls_account_name,
          'account_key': adls_account_key
      }
      fs = fsspec.filesystem(protocol, **storage_options)
      fs.put(
          './artifacts/output',
          STORAGE_PATH, 
          recursive=True, overwrite=True)
   ```

   ```python
      # Register model MLFLOW artifacts in AML
      
      from azureml.core import Workspace, Model
      from azureml.core.authentication import ServicePrincipalAuthentication
      
      AZURE_TENANT_ID = "xyz"
      AZURE_CLIENT_ID = "xyz"
      AZURE_CLIENT_SECRET = "xyz"
      
      AML_SUBSCRIPTION_ID = "xyz"
      AML_RESOURCE_GROUP = "xyz"
      AML_WORKSPACE_NAME = "xyz"
      
      svc_pr = ServicePrincipalAuthentication( 
          tenant_id=AZURE_TENANT_ID,
          service_principal_id=AZURE_CLIENT_ID,
          service_principal_password=AZURE_CLIENT_SECRET
      )
      
      ws = Workspace(
          workspace_name = AML_WORKSPACE_NAME,
          subscription_id = AML_SUBSCRIPTION_ID,
          resource_group = AML_RESOURCE_GROUP,
          auth=svc_pr
      )
      
      model = Model.register(
          model_path="./artifacts/output",
          model_name="xyz",
          workspace=ws,
      )
   ```

1. Legen Sie erforderliche Parameter mithilfe von Variablen fest.

   ```python
      # If using ADLS uploaded model
   
      import pandas as pd
      from pyspark.sql import SparkSession
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      ADLS_MODEL_URI_SKLEARN = "abfss://xyz@xyz.dfs.core.windows.net/mlflow/sklearn/     e2e_linear_regression/"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

   ```python
      # If using AML registered model
   
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      AML_MODEL_URI_SKLEARN = "aml://xyz"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

1. Aktivieren Sie die SynapseML-PREDICT-Funktion in der Spark-Sitzung.

   ```python
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

1. Binden Sie das Modell in der Spark-Sitzung.

   ```python
      # If using ADLS uploaded model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=ADLS_MODEL_URI_SKLEARN,
       ).register()
   ```

   ```python
      # If using AML registered model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=AML_MODEL_URI_SKLEARN,
       aml_workspace=ws
       ).register()
   ```

1. Laden Sie Testdaten aus ADLS.

   ```python
      # Load data from ADLS
   
      df = spark.read \
          .format("csv") \
          .option("header", "true") \
          .csv(DATA_FILE,
              inferSchema=True)
      df = df.select(df.columns[:9])
      df.createOrReplaceTempView('data')
      df.show(10)
   ```

1. Rufen Sie PREDICT auf, um die Bewertung zu generieren.

   ```python
      # Call PREDICT
      
      predictions = spark.sql(
                        """
                            SELECT PREDICT('sklearn_linear_regression', *) AS predict FROM data
                        """
                    ).show()
   ```


## <a name="next-steps"></a>Nächste Schritte

- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)
