---
title: Installieren und Verwenden der Azure Machine Learning-CLI
description: Hier erfahren Sie, wie Sie die Azure CLI-Erweiterung für ML verwenden, um Ressourcen wie Arbeitsbereich, Datenspeicher, Datasets, Pipelines, Modelle und Bereitstellungen zu erstellen und zu verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 41f12d9421a49cb926a666469beda97cd8f53eac
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559730"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installieren und Verwenden der CLI-Erweiterung für Azure Machine Learning

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

Der Azure Machine Learning-CLI ist eine Erweiterung der [Azure CLI](/cli/azure/), eine plattformübergreifende Befehlszeilenschnittstelle für die Azure-Plattform. Diese Erweiterung unterstützt Befehle für die Arbeit mit Azure Machine Learning. Sie ermöglicht Ihnen die Automatisierung Ihrer Machine Learning-Aktivitäten. Die folgende Liste enthält einige Beispielaktionen, die Sie mit der CLI-Erweiterung ausführen können:

+ Ausführen von Experimenten zum Erstellen von Machine Learning-Modellen

+ Registrieren von Machine Learning-Modellen zur Verwendung durch Kunden

+ Paketieren, Bereitstellen und Nachverfolgen des Lebenszyklus Ihrer Machine Learning-Modelle

Die CLI ist kein Ersatz für das Azure Machine Learning SDK. Sie stellt ein ergänzendes Tool dar, das für die Verarbeitung hochgradig parametrisierter Aufgaben optimiert ist.

## <a name="prerequisites"></a>Voraussetzungen

* Für die Verwendung der CLI benötigen Sie ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Um die CLI-Befehle in diesem Dokument aus Ihrer **lokalen Umgebung** zu verwenden, benötigen Sie die [Azure CLI](/cli/azure/install-azure-cli).

    Wenn Sie die [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) verwenden, befindet sich die CLI in der Cloud, und der Zugriff erfolgt über den Browser.

## <a name="full-reference-docs"></a>Vollständige Referenzdokumentation

Hier finden Sie die [vollständige Referenzdokumentation für die Erweiterung „azure-cli-ml“ der Azure CLI](/cli/azure/ml(v1)/).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Herstellen einer Verbindung zwischen der CLI und Ihrem Azure-Abonnement

> [!IMPORTANT]
> Wenn Sie Azure Cloud Shell verwenden, können Sie diesen Abschnitt überspringen. Die Cloud Shell authentifiziert Sie automatisch mit dem Konto, mit dem Sie sich bei Ihrem Azure-Abonnement anmelden.

Ihnen stehen mehrere Möglichkeiten zur Verfügung, sich über die CLI bei Ihrem Azure-Abonnement zu authentifizieren. Die grundlegendste ist die interaktive Authentifizierung mithilfe eines Browsers. Öffnen Sie zur interaktiven Authentifizierung eine Befehlszeile oder ein Terminal, und verwenden Sie den folgenden Befehl:

```azurecli-interactive
az login
```

Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Anmeldeseite. Andernfalls müssen Sie einen Browser öffnen und die Anweisungen in der Befehlszeile befolgen. Die Anweisungen umfassen das Navigieren zu [https://aka.ms/devicelogin](https://aka.ms/devicelogin) und Eingeben eines Autorisierungscodes.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Andere Methoden zur Authentifizierung finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-the-extension"></a>Installieren der Erweiterung

So installieren Sie die CLI-Erweiterung (v1):
```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="update-the-extension"></a>Aktualisieren der Erweiterung

Um die Machine Learning-CLI-Erweiterung zu aktualisieren, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>Entfernen der Erweiterung

Verwenden Sie zum Entfernen der CLI-Erweiterung den folgenden Befehl:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Ressourcenverwaltung

Die folgenden Befehle veranschaulichen, wie Sie mit der CLI Ressourcen verwalten, die von Azure Machine Learning verwendet werden.

+ Erstellen Sie eine Ressourcengruppe, wenn noch keine vorhanden ist:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Erstellen Sie einen Azure Machine Learning-Arbeitsbereich:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Weitere Informationen finden Sie unter [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create).

+ Fügen Sie eine Arbeitsbereichskonfiguration einem Ordner hinzu, um CLI-Kontextfähigkeit zu aktivieren.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Dieser Befehl erstellt ein `.azureml`-Unterverzeichnis, das RUNCONFIG-Beispieldateien und Conda-Umgebungsdateien enthält. Es enthält auch eine `config.json`-Datei, die für die Kommunikation mit dem Azure Machine Learning-Arbeitsbereich verwendet wird.

    Weitere Informationen finden Sie unter [az ml folder attach](/cli/azure/ml(v1)/folder#az_ml_folder_attach).

+ Fügen Sie einen Azure-Blobcontainer als Datenspeicher an.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Weitere Informationen finden Sie unter [az ml datastore attach-blob](/cli/azure/ml/datastore#az_ml_datastore_attach-blob).

+ Laden Sie Dateien in einen Datenspeicher hoch.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Weitere Informationen finden Sie unter [az ml datastore upload](/cli/azure/ml/datastore#az_ml_datastore_upload).

+ Fügen Sie einen AKS-Cluster als Computeziel an.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Weitere Informationen finden Sie unter [az ml computetarget attach aks](/cli/azure/ml(v1)/computetarget/attach#az_ml_computetarget_attach-aks).

### <a name="compute-clusters"></a>Computecluster

+ Erstellen Sie einen neuen verwalteten Computecluster.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Erstellen eines neuen verwalteten Computeclusters mit einer verwalteten Identität

  + Benutzerseitig zugewiesene verwaltete Identität

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Systemseitig zugewiesene verwaltete Identität

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Hinzufügen einer verwalteten Identität zu einem vorhandenen Cluster:

    + Benutzerseitig zugewiesene verwaltete Identität
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Systemseitig zugewiesene verwaltete Identität

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Weitere Informationen finden Sie unter [az ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Compute-Instanz
Verwalten von Computeinstanzen.  In allen untenstehenden Beispielen lautet der Name der Computeinstanz **cpu**.

+ Erstellen Sie eine neue computeinstance-Klasse.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget create computeinstance](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_computeinstance).

+ Stoppen Sie eine computeinstance-Klasse.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget computeinstance stop](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

+ Starten Sie eine computeinstance-Klasse.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget computeinstance start](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

+ Starten Sie eine computeinstance-Klasse neu.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget computeinstance restart](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

+ Löschen Sie eine computeinstance-Klasse.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget delete computeinstance](/cli/azure/ml(v1)/computetarget#az_ml_computetarget_delete).


## <a name="run-experiments"></a><a id="experiments"></a>Ausführen von Experimenten

* Starten Sie eine Ausführung Ihres Experiments. Wenn Sie diesen Befehl verwenden, geben Sie den Namen der Runconfig-Datei (den Text vor „\*.runconfig“, wenn Sie auf Ihr Dateisystem schauen) für den -c-Parameter an.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Der Befehl `az ml folder attach` erstellt ein `.azureml`-Unterverzeichnis, das zwei RUNCONFIG-Beispieldateien enthält. 
    >
    > Wenn Sie ein Python-Skript haben, das programmgesteuert ein Laufzeitkonfigurationsobjekt erstellt, können Sie es mit [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) als RUNCPNFIG-Datei speichern.
    >
    > Das vollständige RUNCONFIG-Schema finden Sie in dieser [JSON-Datei](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Das Schema ist durch den `description`-Schlüssel der einzelnen Objekte selbstdokumentierend. Zusätzlich gibt es Enumerationen für mögliche Werte und einen Vorlagencodeausschnitt am Ende.

    Weitere Informationen finden Sie unter [az ml run submit-script](/cli/azure/ml(v1)/run#az_ml_run_submit_script).

* Zeigen Sie eine Liste der Experimente an:

    ```azurecli-interactive
    az ml experiment list
    ```

    Weitere Informationen finden Sie unter [az ml experiment list](/cli/azure/ml(v1)/experiment#az_ml_experiment_list).

### <a name="hyperdrive-run"></a>HyperDrive-Ausführung

Sie können HyperDrive mit der Azure CLI verwenden, um Parameteroptimierungsausführungen auszuführen. Erstellen Sie zunächst eine HyperDrive-Konfigurationsdatei im folgenden Format. Weitere Informationen zu den Hyperparameter-Optimierungsparametern finden Sie im Artikel [Optimieren von Hyperparametern für Ihr Modell](how-to-tune-hyperparameters.md).

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Fügen Sie diese Datei den Laufzeitkonfigurationsdateien hinzu. Übermitteln Sie dann eine HyperDrive-Ausführung:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Beachten Sie den Abschnitt *arguments* in der Laufzeitkonfiguration and *parameter space* in der HyperDrive-Konfiguration. Diese Abschnitte enthalten die Befehlszeilenargumente, die an das Trainingsskript übergeben werden sollen. Der Wert in der Laufzeitkonfiguration bleibt für jede Iteration gleich, während der Bereich in der HyperDrive-Konfiguration iteriert wird. Geben Sie nicht das gleiche Argument in beiden Dateien an.

## <a name="dataset-management"></a>Datasetverwaltung

Die folgenden Befehle veranschaulichen die Arbeit mit Datasets in Azure Machine Learning:

+ Registrieren eines Datasets:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Informationen zum Format der JSON-Datei, mit der das Dataset definiert wird, finden Sie unter `az ml dataset register --show-template`.

    Weitere Informationen finden Sie unter [az ml dataset register](/cli/azure/ml(v1)/dataset#az_ml_dataset_register).

+ Auflisten aller Datasets in einem Arbeitsbereich:

    ```azurecli-interactive
    az ml dataset list
    ```

    Weitere Informationen finden Sie unter [az ml dataset list](/cli/azure/ml(v1)/dataset#az_ml_dataset_list).

+ Abrufen der Details zu einem Dataset:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Weitere Informationen finden Sie unter [az ml dataset show](/cli/azure/ml(v1)/dataset#az_ml_dataset_show).

+ Aufheben der Registrierung eines Datasets:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Weitere Informationen finden Sie unter [az ml dataset unregister](/cli/azure/ml(v1)/dataset#az_ml_dataset_archive).

## <a name="environment-management"></a>Umgebungsverwaltung

Anhand der folgenden Befehle wird veranschaulicht, wie Sie Azure Machine Learning-[Umgebungen](how-to-configure-environment.md) für Ihren Arbeitsbereich erstellen, registrieren und auflisten:

+ Erstellen Sie die Gerüstdateien für eine Umgebung:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Weitere Informationen finden Sie unter [az ml environment scaffold](/cli/azure/ml/environment#az_ml_environment_scaffold).

+ Registrieren Sie eine Umgebung:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Weitere Informationen finden Sie unter [az ml environment register](/cli/azure/ml/environment#az_ml_environment_register).

+ Listen Sie die registrierten Umgebungen auf:

    ```azurecli-interactive
    az ml environment list
    ```

    Weitere Informationen finden Sie unter [az ml environment list](/cli/azure/ml/environment#az_ml_environment_list).

+ Laden Sie eine registrierte Umgebung herunter:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Weitere Informationen finden Sie unter [az ml environment download](/cli/azure/ml/environment#az_ml_environment_download).

### <a name="environment-configuration-schema"></a>Umgebungskonfigurationsschema

Wenn Sie den Befehl `az ml environment scaffold` verwendet haben, wird eine `azureml_environment.json`-Vorlagendatei generiert, die geändert und zum Erstellen von benutzerdefinierten Umgebungskonfigurationen mit der Befehlszeilenschnittstelle verwendet werden kann. Das Objekt der obersten Ebene wird lose der [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29)-Klasse im Python-SDK zugeordnet. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

In der folgenden Tabelle ist jedes Feld der obersten Ebene in der JSON-Datei, sein Typ und eine Beschreibung aufgeführt. Wenn ein Objekttyp mit einer Klasse aus dem Python-SDK verknüpft ist, gibt es eine lose 1:1-Übereinstimmung zwischen den einzelnen JSON-Feldern und dem öffentlichen Variablennamen in der Python-Klasse. In einigen Fällen wird das Feld möglicherweise eher einem Konstruktorargument als einer Klassenvariablen zugeordnet. Das Feld `environmentVariables` wird z. B. der Variablen `environment_variables` in der [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29)-Klasse zugeordnet.

| JSON-Feld | type | BESCHREIBUNG |
|---|---|---|
| `name` | `string` | Der Name der Umgebung. Beginnen Sie den Namen nicht mit **Microsoft** oder **AzureML**. |
| `version` | `string` | Die Version der Umgebung. |
| `environmentVariables` | `{string: string}` | Eine Hashzuordnung mit Namen und Werten von Umgebungsvariablen. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection), das die Python-Umgebung und den Interpreter definiert, die auf der Zielcomputeressource verwendet werden sollen. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Definiert Einstellungen zum Anpassen des Docker-Images, das nach den Spezifikationen der Umgebung erstellt wird. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | Der Abschnitt konfiguriert die Spark-Einstellungen. Er wird nur verwendet, wenn das Framework auf PySpark festgelegt ist. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Konfiguriert die Abhängigkeiten der Databricks-Bibliothek. |
| `inferencingStackVersion` | `string` | Gibt die dem Image hinzugefügte Version des Rückschlussstapels an. Um das Hinzufügen eines Rückschlussstapels zu vermeiden, belassen Sie dieses Feld `null`. Gültiger Wert: „latest“ (Aktuellste). |

## <a name="ml-pipeline-management"></a>Verwaltung von ML-Pipelines

Die folgenden Befehle veranschaulichen, wie mit Machine Learning-Pipelines gearbeitet wird.

+ Erstellen Sie eine Machine Learning-Pipeline:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Weitere Informationen finden Sie unter [az ml pipeline create](/cli/azure/ml(v1)/pipeline#az_ml_pipeline_create).

    Weitere Informationen zur Pipeline-YAML-Datei finden Sie unter [Definieren von Machine Learning-Pipelines in YAML](reference-pipeline-yaml.md).

+ Führen Sie eine Pipeline aus:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Weitere Informationen finden Sie unter [az ml run submit-pipeline](/cli/azure/ml(v1)/run#az_ml_run_submit_pipeline).

    Weitere Informationen zur Pipeline-YAML-Datei finden Sie unter [Definieren von Machine Learning-Pipelines in YAML](reference-pipeline-yaml.md).

+ Planen Sie eine Pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexperiment -i mypipelineid -y myschedule.yml
    ```

    Weitere Informationen finden Sie unter [az ml pipeline create-schedule](/cli/azure/ml(v1)/pipeline#az_ml_pipeline_create-schedule).

    Weitere Informationen zur YAML-Datei für einen Pipelinezeitplan finden Sie unter [Definieren von Machine Learning-Pipelines in YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registrierung von Modellen, Profilerstellung und Bereitstellung

Die folgenden Befehle veranschaulichen, wie ein trainiertes Modell registriert und dann als Produktionsdienst bereitgestellt wird:

+ Hiermit registrieren Sie ein Modell bei Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Weitere Informationen finden Sie unter [az ml model register](/cli/azure/ml/model#az_ml_model_register).

+ **OPTIONAL** Erstellen Sie ein Profil für Ihr Modell, um optimale CPU- und Arbeitsspeicherwerte für die Bereitstellung zu erzielen.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Weitere Informationen finden Sie unter [az ml model profile](/cli/azure/ml/model#az_ml_model_profile).

+ Bereitstellen Ihrer Modelldatei für AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Weitere Informationen zum Rückschlusskonfigurationsdatei-Schema finden Sie unter [Inference configuration schema (Rückschlusskonfigurationsschema)](#inferenceconfig).
    
    Weitere Informationen zum Bereitstellungskonfigurationsdatei-Schema finden Sie unter [Deployment configuration schema (Bereitstellungskonfigurationsschema)](#deploymentconfig).

    Weitere Informationen finden Sie unter [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Rückschlusskonfigurationsschema

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Bereitstellungskonfigurationsschema

### <a name="local-deployment-configuration-schema"></a>Konfigurationsschema zur lokalen Bereitstellung

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Konfigurationsschema zur Bereitstellung einer Azure-Containerinstanz 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Konfigurationsschema zur Bereitstellung von Azure Kubernetes Service

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Befehlsreferenz für die Machine Learning-CLI-Erweiterung](/cli/azure/ml)

* [Trainieren und Bereitstellen von Machine Learning-Modellen mit Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
