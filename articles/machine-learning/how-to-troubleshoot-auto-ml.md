---
title: Behandeln von Problemen bei Experimenten mit automatisiertem ML
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie Probleme bei Ihren Experimenten für das automatisierte maschinelle Lernen behandeln und beheben.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python, automl, references_regions
ms.openlocfilehash: 4916f65ef70e6df04a1dfacb690450da20e35386
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561970"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Behandeln von Problemen bei Experimenten mit automatisiertem ML in Python

In diesem Leitfaden wird beschrieben, wie Sie bekannte Probleme bei Ihren Experimenten mit automatisiertem maschinellen Lernen mit dem [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) identifizieren und beheben.

## <a name="version-dependencies"></a>Versionsabhängigkeiten

**`AutoML`-Abhängigkeiten von neueren Paketversionen führen dazu, dass die Kompatibilität nicht mehr gegeben ist**. In den SDK-Versionen nach 1.13.0 werden Modelle in älteren SDKs nicht mehr geladen, weil eine Inkompatibilität zwischen den älteren Versionen in den vorherigen `AutoML`-Paketen und den neueren heutigen Versionen besteht.

Es können Fehler der folgenden Art auftreten:

* Fehler „Modul nicht gefunden“, z. B.:

  `No module named 'sklearn.decomposition._truncated_svd`

* Importfehler, z. B.:

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Attributfehler, z. B.:

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

Die Lösungen richten sich nach Ihrer `AutoML`-SDK-Trainingsversion:

* Wenn Ihre `AutoML` SDK-Trainingsversion höher als 1.13.0 ist, benötigen Sie `pandas == 0.25.1` und `scikit-learn==0.22.1`.

    * Wenn die Version nicht übereinstimmt, führen Sie ein Upgrade von scikit-learn oder Pandas auf die richtige Version durch, indem Sie Folgendes verwenden:

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Wenn Ihre `AutoML` SDK-Trainingsversion kleiner als oder gleich 1.12.0 ist, benötigen Sie `pandas == 0.23.4` und `sckit-learn==0.20.3`.
  * Wenn die Version nicht übereinstimmt, führen Sie ein Downgrade von scikit-learn oder Pandas auf die richtige Version durch, indem Sie Folgendes verwenden:
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Einrichten

Für `AutoML`-Paketänderungen seit Version 1.0.76 muss die vorherige Version deinstalliert werden, bevor das Update auf die neue Version durchgeführt wird.

* **`ImportError: cannot import name AutoMLConfig`**

    Falls dieser Fehler nach dem Upgrade von einer älteren SDK-Version als v1.0.76 auf v1.0.76 oder höher auftritt, können Sie ihn beheben, indem Sie Folgendes ausführen: `pip uninstall azureml-train automl` und dann `pip install azureml-train-automl`. Das Skript „automl_setup.cmd“ erledigt dies automatisch.

* **Fehler bei „automl_setup“**

  * Unter Windows führen Sie „automl_setup“ von einer Anaconda-Eingabeaufforderung aus. [Installieren Sie Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Stellen Sie sicher, dass die 64-Bit-Version 4.4.10 oder höher von Conda installiert ist. Sie können dies mit dem Befehl `conda info` überprüfen. `platform` sollte für Windows `win-64` und für Mac `osx-64` lauten. Verwenden Sie den Befehl `conda -V`, um die Version zu überprüfen. Wenn eine frühere Version installiert ist, können Sie diese mit dem folgenden Befehl aktualisieren: `conda update conda`. Sie können auch eine Überprüfung auf eine 32-Bit-Version durchführen. 

  * Stellen Sie sicher, dass Conda installiert ist. 

  * Linux: `gcc: error trying to exec 'cc1plus'`

    1. Wenn der Fehler `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` auftritt, sollten Sie die GCC-Buildtools für Ihre Linux-Distribution installieren. Verwenden Sie unter Ubuntu beispielsweise den folgenden Befehl: `sudo apt-get install build-essential`.

    1. Übergeben Sie einen neuen Namen als ersten Parameter an „automl_setup“, um eine neue Conda-Umgebung zu erstellen. Vorhandene Conda-Umgebungen können Sie mit `conda env list` anzeigen und mit `conda env remove -n <environmentname>` entfernen.

* **Fehler bei „automl_setup_linux.sh“** : Gehen Sie folgendermaßen vor, wenn bei „automl_setup_linus.sh“ unter Ubuntu Linux folgender Fehler auftritt: `unable to execute 'gcc': No such file or directory`

  1. Stellen Sie sicher, dass die ausgehenden Ports 53 und 80 aktiviert sind. Auf einem virtuellen Azure-Computer können Sie zu diesem Zweck zum Azure-Portal wechseln, die VM auswählen und auf **Netzwerk** klicken.
  1. Führen Sie den folgenden Befehl aus: `sudo apt-get update`
  1. Führen Sie den folgenden Befehl aus: `sudo apt-get install build-essential --fix-missing`
  1. Führen Sie `automl_setup_linux.sh` erneut aus.

* **Fehler bei „configuration.ipynb“** :

  * Stellen Sie bei einer lokalen Conda-Instanz zunächst sicher, dass `automl_setup` erfolgreich ausgeführt wurde.
  * Stellen Sie sicher, dass die „subscription_id“ richtig ist. Suchen Sie die „subscription_id“ im Azure-Portal, indem Sie „Alle Dienste“ und dann „Abonnements“ auswählen. Die Zeichen „<“ und „>“ dürfen im subscription_id-Wert nicht enthalten sein. Dies ist ein Beispiel für ein gültiges Format: `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"`.
  * Stellen Sie sicher, dass Sie Zugriff als „Besitzer“ oder „Mitwirkender“ auf das Abonnement haben.
  * Überprüfen Sie, ob die verwendete Region unterstützt wird: `eastus2`, `eastus`, `westcentralus`, `southeastasia`, `westeurope`, `australiaeast`, `westus2`, `southcentralus`.
  * Stellen Sie über das Azure-Portal sicher, dass Zugriff auf die Region besteht.
  
* **Fehler bei „workspace.from_config“** :

  Gehen Sie wie folgt vor, falls beim Aufruf `ws = Workspace.from_config()` ein Fehler auftritt:

  1. Stellen Sie sicher, dass das Notebook „configuration.ipynb“ erfolgreich ausgeführt wurde.
  1. Wenn das Notebook in einem Ordner ausgeführt werden soll, der sich nicht in dem Ordner befindet, in dem `configuration.ipynb` ausgeführt wurde, kopieren Sie den Ordner „aml_config“ und die darin enthaltene Datei „config.json“ in den neuen Ordner. „Workspace.from_config“ liest die Datei „config.json“ für den Notebookordner oder den übergeordneten Ordner.
  1. Wenn ein neues Abonnement, eine neue Ressourcengruppe, ein neuer Arbeitsbereich oder eine neue Region verwendet wird, müssen Sie das Notebook `configuration.ipynb` erneut ausführen. Eine direkte Änderung von „config.json“ funktioniert nur, wenn der Arbeitsbereich bereits in der angegebenen Ressourcengruppe im angegebenen Abonnement vorhanden ist.
  1. Wenn Sie die Region ändern möchten, ändern Sie den Arbeitsbereich, die Ressourcengruppe oder das Abonnement. `Workspace.create` erstellt keinen Arbeitsbereich und aktualisiert auch keinen vorhandenen Arbeitsbereich, auch dann nicht, wenn eine andere Region angegeben wird.

## <a name="tensorflow"></a>TensorFlow

Ab der SDK-Version 1.5.0 installiert das automatisierte maschinelle Lernen TensorFlow-Modelle nicht mehr standardmäßig. Installieren Sie `tensorflow==1.12.0` über `CondaDependencies`, wenn Sie TensorFlow installieren und für Ihre Experimente mit automatisiertem ML nutzen möchten.

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>NumPy-Fehler

* **`import numpy` führt unter Windows zu einem Fehler:** In einigen Windows-Umgebungen tritt beim Laden von NumPy mit der neuesten Python-Version 3.6.8 ein Fehler auf. Versuchen Sie in diesem Fall die Python-Version 3.6.7.

* **`import numpy` führt zu einem Fehler:** Überprüfen Sie die TensorFlow-Version in der Conda-Umgebung für automatisiertes ML. Es werden Versionen unter 1.13 unterstützt. Deinstallieren Sie TensorFlow aus der Umgebung, wenn die Version größer oder gleich 1.13 ist.

Sie können die Version von TensorFlow wie folgt überprüfen und deinstallieren:

  1. Starten Sie eine Befehlsshell, und aktivieren Sie die Conda-Umgebung, in der die Pakete für das automatisierte maschinelle Lernen installiert sind.
  1. Geben Sie `pip freeze` ein, und suchen Sie nach `tensorflow`. Die aufgelistete Version sollte kleiner als 1.13 sein.
  1. Wenn die aufgelistete Version keine unterstützte Version ist, geben Sie in der Befehlsshell `pip uninstall tensorflow` ein, und bestätigen Sie den Befehl mit „y“.

## `jwt.exceptions.DecodeError`

Die genaue Fehlermeldung lautet: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`.

Bei SDK-Versionen bis einschließlich 1.17.0 führt die Installation möglicherweise zu einer nicht unterstützten Version von PyJWT. Vergewissern Sie sich, dass die PyJWT-Version in der Conda-Umgebung für automatisiertes ML eine unterstützte Version ist. Dies sind PyJWT-Version unter 2.0.0.

Sie können die PyJWT-Version wie folgt überprüfen:

1. Starten Sie eine Befehlsshell, und aktivieren Sie die Conda-Umgebung, in der die Pakete für automatisiertes ML installiert sind.

1. Geben Sie `pip freeze` ein, und suchen Sie nach `PyJWT`. Die aufgelistete Version sollte kleiner als 2.0.0 sein.

Wenn die aufgeführte Version nicht unterstützt wird:

1. Erwägen Sie, ein Upgrade auf die neueste Version des AutoML SDK durchzuführen: `pip install -U azureml-sdk[automl]`.

1. Wenn dies nicht praktikabel ist, deinstallieren Sie PyJWT aus der Umgebung, und installieren Sie die richtige Version wie folgt:

    1. Geben Sie in der Befehlsshell `pip uninstall PyJWT` ein, und bestätigen Sie den Vorgang mit `y`.
    1. Führen Sie die Installation mithilfe von `pip install 'PyJWT<2.0.0'` aus.
  

## <a name="data-access"></a>Datenzugriff
 
Für Ausführungen von automatisiertem ML müssen Sie sicherstellen, dass der Dateidatenspeicher, der eine Verbindung mit Ihrem AzureFile-Speicher herstellt, über die entsprechenden Anmeldeinformationen für die Authentifizierung verfügt. Andernfalls wird die folgende Meldung angezeigt. Erfahren Sie, wie Sie [Ihre Anmeldeinformationen für die Datenzugriffsauthentifizierung aktualisieren](how-to-train-with-datasets.md#azurefile-storage).

Fehlermeldung: `Could not create a connection to the AzureFileService due to missing credentials. Either an Account Key or SAS token needs to be linked the default workspace blob store.`

## <a name="databricks"></a>Databricks
Weitere Informationen finden Sie unter [Konfigurieren eines Experiments mit automatisiertem ML über Databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).


## <a name="forecasting-r2-score-is-always-zero"></a>Prognose-R2-Score ist immer 0 (null)

 Dieses Problem tritt auf, wenn die bereitgestellten Trainingsdaten Zeitreihen enthalten, die für die letzten `n_cv_splits` + `forecasting_horizon` Datenpunkte den gleichen Wert aufweisen.

Wenn dieses Muster in Ihrer Zeitreihe erwartet wird, können Sie die primäre Metrik in die **normalisierte mittlere quadratische Gesamtabweichung** ändern.

## <a name="failed-deployment"></a>Fehler bei der Bereitstellung

 Bei SDK-Versionen bis einschließlich 1.18.0 kann bei dem für die Bereitstellung erstellten Basisimage der folgende Fehler auftreten: `ImportError: cannot import name cached_property from werkzeug`.

  Mit den folgenden Schritten können Sie das Problem umgehen:

  1. Herunterladen des Modellpakets
  1. Entzippen Sie das Paket.
  1. Bereitstellen mithilfe der entzippten Ressourcen

## <a name="azure-functions-application"></a>Azure Functions-Anwendung
  
  Azure Functions-Anwendungen werden beim automatisierten ML derzeit nicht unterstützt. 

## <a name="sample-notebook-failures"></a>Fehler für Beispielnotebook

 Wenn bei einem Beispielnotebook ein Fehler aufgrund einer fehlenden Eigenschaft, Methode oder Bibliothek auftritt, gehen Sie folgendermaßen vor:

* Stellen Sie sicher, dass im Jupyter Notebook der richtige Kernel ausgewählt wurde. Der Kernel wird oben rechts auf der Seite des Notebooks angezeigt. Der Standardwert lautet *azure_automl*. Der Kernel wird als Teil des Notebooks gespeichert. Wenn Sie zu einer neuen Conda-Umgebung wechseln, müssen Sie den neuen Kernel im Notebook auswählen.

  * Bei Azure Notebooks sollte Python 3.6 verwendet werden.
  * Bei lokalen Conda-Umgebungen sollte es sich um den Namen der Conda-Umgebung handeln, die Sie in „automl_setup“ angegeben haben.

* Stellen Sie sicher, dass das Notebook auf die von Ihnen verwendete SDK-Version ausgelegt ist.
  * Überprüfen Sie die SDK-Version, indem Sie `azureml.core.VERSION` in einer Jupyter Notebook-Zelle ausführen.
  * Mit den folgenden Schritten können Sie die vorherige Version der Beispielnotebooks von GitHub herunterladen:
    1. Wählen Sie die Schaltfläche `Branch` aus.
    1. Navigieren Sie zur Registerkarte `Tags`.
    1. Wählen Sie die Version aus.

## <a name="next-steps"></a>Nächste Schritte

+ Erfahren Sie mehr über das [Trainieren eines Regressionsmodells mit automatisiertem maschinellem Lernen](tutorial-auto-train-models.md) und das [Trainieren mit automatisiertem maschinellem Lernen auf einer Remoteressource](concept-automated-ml.md#local-remote).

+ Lernen Sie, [wie und wo Sie Modelle bereitstellen](how-to-deploy-and-where.md) können.
