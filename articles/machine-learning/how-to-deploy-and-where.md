---
title: Bereitstellen von Machine Learning-Modellen
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie und wo Sie Machine Learning-Modelle bereitstellen können. Die Bereitstellung ist für Azure Container Instances, Azure Kubernetes Service und FPGA möglich.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: ssambare
author: shivanissambare
ms.date: 11/12/2021
ms.topic: how-to
ms.custom: devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2, contperf-fy21q4, mktng-kw-nov2021
adobe-target: true
ms.openlocfilehash: c24aa6fd09539c1bd9536ba0cecb3815edb79e2f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488181"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Bereitstellen von Machine Learning-Modellen für Azure 

Es wird beschrieben, wie Sie Ihr Machine Learning- oder Deep Learning-Modell als Webdienst in der Azure-Cloud bereitstellen.

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

## <a name="workflow-for-deploying-a-model"></a>Workflow zum Bereitstellen eines Modells

Der Workflow ist unabhängig vom Bereitstellungsort Ihres Modells sehr ähnlich:

1. Registrieren des Modells.
1. Vorbereiten eines Einstiegsskripts.
1. Vorbereiten einer Rückschlusskonfiguration.
1. Lokales Bereitstellen des Modells, um sicherzustellen, dass alles funktioniert.
1. Auswählen eines Computeziels
1. Stellen Sie das Modell in der Cloud bereit.
1. Testen des resultierenden Webdiensts

Weitere Informationen zu den Konzepten, die für den Workflow zur Bereitstellung von Machine Learning-Modellen gelten, finden Sie unter [Verwalten, Bereitstellen und Überwachen von Modellen mit Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

[!INCLUDE [cli10-only](../../includes/machine-learning-cli-version-1-only.md)]

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Ein Modell. In den Beispielen in diesem Artikel wird ein vortrainiertes Modell verwendet.
- Ein Computer, auf dem Docker ausgeführt werden kann, z. B. eine [Compute-Instanz.](how-to-create-manage-compute-instance.md)

# <a name="python"></a>[Python](#tab/python)

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Ein Modell. In den Beispielen in diesem Artikel wird ein vortrainiertes Modell verwendet.
- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro).
- Ein Computer, auf dem Docker ausgeführt werden kann, z. B. eine [Compute-Instanz](how-to-create-manage-compute-instance.md).
---

## <a name="connect-to-your-workspace"></a>Herstellen einer Verbindung mit Ihrem Arbeitsbereich

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Verwenden Sie die folgenden Befehle, um die Arbeitsbereiche anzuzeigen, auf die Sie Zugriff haben:

```azurecli-interactive
az login
az account set -s <subscription>
az ml workspace list --resource-group=<resource-group>
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace(subscription_id="<subscription_id>",
               resource_group="<resource_group>",
               workspace_name="<workspace_name>")
```

Weitere Informationen zur Verwendung des SDKs, um eine Verbindung mit einem Arbeitsbereich herzustellen, finden Sie in der Dokumentation zum [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro#workspace).


---

## <a name="register-the-model"></a><a id="registermodel"></a> Registrieren des Modells

Eine typische Situation für einen bereitgestellten Machine Learning-Dienst ist, dass Sie die folgenden Komponenten benötigen:
    
+ Ressourcen, die das spezifische Modell darstellen, das bereitgestellt werden soll (z. B. eine PyTorch-Modelldatei)
+ Code, den Sie im Dienst ausführen möchten, der das Modell für eine bestimmte Eingabe ausführt

Mit Azure Machine Learning erlaubt es Ihnen, die Bereitstellung in zwei separate Komponenten zu unterteilen, damit Sie denselben Code beibehalten, aber lediglich das Modell aktualisieren können. Wir definieren den Mechanismus, mit dem Sie ein Modell _getrennt_ von Ihrem Code hochladen, als „Registrieren des Modells“.

Wenn Sie ein Modell registrieren, laden wir das Modell in die Cloud hoch (im Standardspeicherkonto Ihres Arbeitsbereichs) und binden es dann auf derselben Compute-Instanz ein, auf der Ihr Webdienst ausgeführt wird.

Die folgenden Beispiele veranschaulichen das Registrieren eines Modells.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Mit den folgenden Befehle laden Sie ein Modell herunter und registrieren es dann bei Ihrem Azure Machine Learning-Arbeitsbereich:

```azurecli-interactive
wget https://aka.ms/bidaf-9-model -O model.onnx --show-progress
az ml model register -n bidaf_onnx \
    -p ./model.onnx \
    -g <resource-group> \
    -w <workspace-name>
```

Legen Sie `-p` auf den Pfad eines Ordners oder einer Datei fest, den bzw. die Sie registrieren möchten.

Weitere Informationen zu `az ml model register` finden Sie in der [Referenzdokumentation](/cli/azure/ml(v1)/model).

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrieren eines Modells aus einer Azure ML-Trainingsausführung

Wenn Sie ein Modell registrieren müssen, das zuvor über einen Azure Machine Learning-Trainingsauftrag erstellt wurde, können Sie das Experiment, die Ausführung und den Pfad zum Modell angeben:

```azurecli-interactive
az ml model register -n bidaf_onnx --asset-path outputs/model.onnx --experiment-name myexperiment --run-id myrunid --tag area=qna
```

Der `--asset-path`-Parameter verweist auf den Cloudspeicherort des Modells. In diesem Beispiel wird der Pfad einer einzelnen Datei verwendet. Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `--asset-path` auf den Pfad eines Ordners fest, der die Dateien enthält.

Weitere Informationen zu `az ml model register` finden Sie in der [Referenzdokumentation](/cli/azure/ml(v1)/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-a-local-file"></a>Registrieren eines Modells aus einer lokalen Datei

Sie können ein Modell registrieren, indem Sie den lokalen Pfad des Modells bereitstellen. Sie können den Pfad eines Ordners oder einer einzelnen Datei auf Ihrem lokalen Computer angeben.
<!-- pyhton nb call -->
[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=register-model-from-local-file-code)]


Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `model_path` auf den Pfad eines Ordners fest, der die Dateien enthält.

Weitere Informationen finden Sie in der Dokumentation zur [Model-Klasse](/python/api/azureml-core/azureml.core.model.model).


### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrieren eines Modells aus einer Azure ML-Trainingsausführung

  Wenn Sie das SDK verwenden, um ein Modell zu trainieren, können Sie entweder ein [Run](/python/api/azureml-core/azureml.core.run.run)-Objekt oder ein [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun)-Objekt empfangen. Dies hängt davon ab, wie Sie das Modell trainiert haben. Jedes Objekt kann verwendet werden, um ein Modell zu registrieren, das von einer Experimentausführung erstellt wurde.

  + Registrieren Sie ein Modell über ein `azureml.core.Run`-Objekt:
 
    ```python
    model = run.register_model(model_name='bidaf_onnx',
                               tags={'area': 'qna'},
                               model_path='outputs/model.onnx')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Der `model_path`-Parameter verweist auf den Cloudspeicherort des Modells. In diesem Beispiel wird der Pfad einer einzelnen Datei verwendet. Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `model_path` auf den Pfad eines Ordners fest, der die Dateien enthält. Weitere Informationen finden Sie in der Dokumentation zum [Run.register-Modell](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-).

  + Registrieren Sie ein Modell über ein `azureml.train.automl.run.AutoMLRun`-Objekt:

    ```python
    description = 'My AutoML Model'
    model = run.register_model(description = description,
                                tags={'area': 'qna'})

    print(run.model_id)
    ```

    In diesem Beispiel sind die Parameter `metric` und `iteration` nicht angegeben. Daher wird die Iteration mit der besten primären Metrik registriert. Der von der Ausführung zurückgegebene `model_id`-Wert wird anstelle eines Modellnamens verwendet.

    Weitere Informationen finden Sie in der Dokumentation zu [AutoMLRun.register-Modell](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-).

    Um ein registriertes Modell von einem `AutoMLRun` bereitzustellen, können Sie die [Schaltfläche zum Bereitstellen mit nur einem Klick in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) verwenden. 

---

## <a name="define-a-dummy-entry-script"></a>Definieren eines Dummyeinstiegsskripts

Das Eingangsskript empfängt an einen bereitgestellten Webdienst übermittelte Daten und übergibt sie an das Modell. Anschließend wird die Antwort des Modells an den Client zurückgegeben. *Das Skript ist auf Ihr Modell zugeschnitten*. Im Einstiegsskript muss die Struktur der vom Modell erwarteten und zurückgegebenen Daten bekannt sein.

Sie müssen die folgenden beiden Schritte in Ihrem Eingabeskript ausführen:

1. Laden des Modells (mit einer Funktion namens `init()`)
1. Ausführen des Modells für Eingabedaten (mit einer Funktion namens `run()`)

Verwenden Sie für die erste Bereitstellung ein Dummyeinstiegsskript, das die empfangenen Daten ausgibt.

:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/echo_score.py":::

Speichern Sie diese Datei als `echo_score.py` in einem Verzeichnis mit dem Namen `source_dir`. Dieses Platzhalterskript gibt die Daten zurück, die Sie an das Skript senden, sodass es das Modell nicht verwendet. Es ist jedoch nützlich, um zu testen, ob das Bewertungsskript ausgeführt wird.

## <a name="define-an-inference-configuration"></a>Definieren einer Rückschlusskonfiguration

Eine Rückschlusskonfiguration beschreibt den Docker-Container und die Dateien, die beim Initialisieren Ihres Webdiensts verwendet werden sollen. Alle Dateien in Ihrem Quellverzeichnis, einschließlich Unterverzeichnissen, werden gezippt und in die Cloud hochgeladen, wenn Sie Ihren Webdienst bereitstellen.

Die unten gezeigte Rückschlusskonfiguration gibt an, dass die Machine Learning-Bereitstellung die Datei `echo_score.py` im `./source_dir` Verzeichnis verwendet, um eingehende Anforderungen zu verarbeiten, und dass das Docker-Image mit den in der`project_environment`-Umgebung angegebenen Python-Paketen verwendet wird.

Sie können alle [zusammengestellten Azure Machine Learning-Rückschlussumgebungen](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference) als Docker-Basisimage verwenden, wenn Sie Ihre Projektumgebung erstellen. Wir installieren darauf die erforderlichen Abhängigkeiten und speichern das resultierende Docker-Image in dem Repository, das Ihrem Arbeitsbereich zugeordnet ist.

> [!NOTE]
> Beim Hochladen des [Rückschlussquellverzeichnisses](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py#constructor&preserve-view=true) von Azure Machine Learning wird **.gitignore** oder **.amlignore** nicht beachtet.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Eine Konfiguration für minimalen Rückschluss kann wie folgt geschrieben werden:

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/dummyinferenceconfig.json":::

Speichern Sie die Datei mit dem Namen `dummyinferenceconfig.json`.


[In diesem Artikel](./reference-azure-machine-learning-cli.md#inference-configuration-schema) finden Sie eine ausführliche Erörterung von Rückschlusskonfigurationen. 

# <a name="python"></a>[Python](#tab/python)

Im folgenden Beispiel wird veranschaulicht, wie Sie mithilfe des oben definierten Dummybewertungsskripts eine minimale Umgebung ohne PIP-Abhängigkeiten erstellen.

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=inference-configuration-code)]

Weitere Informationen zu Umgebungen finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md).

Weitere Informationen zur Rückschlusskonfiguration finden Sie in der Dokumentation der [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)-Klasse.

---


## <a name="define-a-deployment-configuration"></a>Definieren einer Bereitstellungskonfiguration

Eine Bereitstellungskonfiguration gibt die Menge an Arbeitsspeicher und die Anzahl von Kernen an, die Ihr Webdienst für die Ausführung benötigt. Außerdem enthält sie Konfigurationsdetails für den zugrunde liegenden Webdienst. Mit einer Bereitstellungskonfiguration können Sie beispielsweise angeben, dass Ihr Dienst 2 GB Arbeitsspeicher, 2 CPU-Kerne sowie 1 GPU-Kern benötigt und dass Sie die automatische Skalierung aktivieren möchten.

Die für eine Bereitstellungskonfiguration verfügbaren Optionen unterscheiden sich je nach dem von Ihnen ausgewählten Computeziel. In einer lokalen Bereitstellung können Sie nur angeben, an welchem Port Ihr Webdienst bedient wird.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Weitere Informationen finden Sie unter [Bereitstellungsschema](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Im folgenden Python-Code wird veranschaulicht, wie Sie eine lokale Bereitstellungskonfiguration erstellen: 

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deployment-configuration-code)]

---

## <a name="deploy-your-machine-learning-model"></a>Bereitstellen Ihres Machine Learning-Modells

Sie können Ihr Modell jetzt bereitstellen. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Ersetzen Sie `bidaf_onnx:1` mit den Namen Ihres Modells und dessen Versionsnummer.

```azurecli-interactive
az ml model deploy -n myservice \
    -m bidaf_onnx:1 \
    --overwrite \
    --ic dummyinferenceconfig.json \
    --dc deploymentconfig.json \
    -g <resource-group> \
    -w <workspace-name>
```

# <a name="python"></a>[Python](#tab/python)


[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-print-logs)]

Weitere Informationen finden Sie in der Dokumentation zu[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) und [Web-Service](/python/api/azureml-core/azureml.core.webservice.webservice).

---

## <a name="call-into-your-model"></a>Aufrufen Ihres Modells

Lassen Sie uns überprüfen, ob Ihr Echomodell erfolgreich bereitgestellt wurde. Sie sollten in der Lage sein, eine einfache Liveanforderung sowie eine Bewertungsanforderung zu senden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli-interactive
curl -v http://localhost:32267
curl -v -X POST -H "content-type:application/json" \
    -d '{"query": "What color is the fox", "context": "The quick brown fox jumped over the lazy dog."}' \
    http://localhost:32267/score
```

# <a name="python"></a>[Python](#tab/python)
<!-- python nb call -->
[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-into-model-code)]

---

## <a name="define-an-entry-script"></a>Definieren eines Einstiegsskripts

Jetzt ist es an der Zeit, Ihr Modell tatsächlich zu laden. Ändern Sie zunächst Ihr Einstiegsskript:


:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/score.py":::

Speichern Sie diese Datei als `score.py` in `source_dir`.

Beachten Sie die Verwendung der Umgebungsvariablen `AZUREML_MODEL_DIR`, um Ihr registriertes Modell aufzufinden. Sie haben nun einige pip-Pakete hinzugefügt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/inferenceconfig.json":::

Speichern Sie diese Datei als `inferenceconfig.json`. 

# <a name="python"></a>[Python](#tab/python)

```python
env = Environment(name='myenv')
python_packages = ['nltk', 'numpy', 'onnxruntime']
for package in python_packages:
    env.python.conda_dependencies.add_pip_package(package)

inference_config = InferenceConfig(environment=env, source_directory='./source_dir', entry_script='./score.py')
```

Weitere Informationen finden Sie in der Dokumentation zu [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) und [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---

## <a name="deploy-again-and-call-your-service"></a>Erneutes Bereitstellen und Aufrufen Ihres Diensts

Stellen Sie Ihren Dienst erneut bereit:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Ersetzen Sie `bidaf_onnx:1` mit den Namen Ihres Modells und dessen Versionsnummer.

```azurecli-interactive
az ml model deploy -n myservice \
    -m bidaf_onnx:1 \
    --overwrite \
    --ic inferenceconfig.json \
    --dc deploymentconfig.json \
    -g <resource-group> \
    -w <workspace-name>
```

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-model-print-logs)]

Weitere Informationen finden Sie in der Dokumentation zu[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) und [Web-Service](/python/api/azureml-core/azureml.core.webservice.webservice).

---
Stellen Sie anschließend sicher, dass Sie eine Post-Anforderung an den Dienst senden können:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli-interactive
curl -v -X POST -H "content-type:application/json" \
    -d '{"query": "What color is the fox", "context": "The quick brown fox jumped over the lazy dog."}' \
    http://localhost:32267/score
```

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=send-post-request-code)]

---

## <a name="choose-a-compute-target"></a>Auswählen eines Computeziels

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

## <a name="deploy-to-cloud"></a>Bereitstellen in der Cloud

Nachdem Sie bestätigt haben, dass Ihr Dienst lokal funktioniert, und ein Remotecomputeziel ausgewählt haben, sind Sie zum Bereitstellen in der Cloud bereit. 

Ändern Sie ihre Bereitstellungskonfiguration so, dass sie dem ausgewählten Computeziel entspricht, in diesem Fall Azure Container Instances:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Die für eine Bereitstellungskonfiguration verfügbaren Optionen unterscheiden sich je nach dem von Ihnen ausgewählten Computeziel.

:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/re-deploymentconfig.json":::

Speichern Sie diese Datei als `re-deploymentconfig.json`.

Weitere Informationen finden Sie [dieser Referenz](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=deploy-model-on-cloud-code)]

---

Stellen Sie Ihren Dienst erneut bereit:


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Ersetzen Sie `bidaf_onnx:1` mit den Namen Ihres Modells und dessen Versionsnummer.

```azurecli-interactive
az ml model deploy -n myservice \
    -m bidaf_onnx:1 \
    --overwrite \
    --ic inferenceconfig.json \
    --dc re-deploymentconfig.json \
    -g <resource-group> \
    -w <workspace-name>
```

Verwenden Sie zum Anzeigen der Dienstprotokolle den folgenden Befehl:

```azurecli-interactive
az ml service get-logs -n myservice \
    -g <resource-group> \
    -w <workspace-name>
```

# <a name="python"></a>[Python](#tab/python)


[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=re-deploy-service-print-logs)]

Weitere Informationen finden Sie in der Dokumentation zu[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) und [Web-Service](/python/api/azureml-core/azureml.core.webservice.webservice).

---


## <a name="call-your-remote-webservice"></a>Aufrufen Ihres Remotewebdiensts

Wenn Sie eine Remotebereitstellung durchführen, haben Sie möglicherweise die Schlüsselauthentifizierung aktiviert. Das folgende Beispiel zeigt, wie Sie Ihren Dienstschlüssel mit Python abrufen, um eine Rückschlussanforderung zu senden.

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-web-service-code)]

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=call-remote-webservice-print-logs)]



Weitere Beispielclients in anderen Sprachen finden Sie im Artikel [Clientanwendungen zum Verwenden von Webdiensten](how-to-consume-web-service.md).

### <a name="understanding-service-state"></a>Grundlegendes zum Dienstzustand

Während der Modellimplementierung kann es vorkommen, dass sich der Dienstzustand während der vollständigen Bereitstellung ändert.

In der folgenden Tabelle werden die verschiedenen Dienstzustände beschrieben:

| Webservice-Zustand | BESCHREIBUNG | Endgültiger Zustand?
| ----- | ----- | ----- |
| Im Übergang | Der Dienst wird gerade bereitgestellt. | Nein |
| Fehlerhaft | Der Dienst wurde bereitgestellt, ist aber zurzeit nicht erreichbar.  | Nein |
| Nicht planbar | Der Dienst kann derzeit aufgrund fehlender Ressourcen nicht bereitgestellt werden. | Nein |
| Fehler | Der Dienst konnte aufgrund eines Fehlers oder Absturzes nicht bereitgestellt werden. | Ja |
| Healthy | Der Dienst ist fehlerfrei und der Endpunkt ist verfügbar. | Ja |

> [!TIP]
> Bei der Bereitstellung werden Docker-Images für Computeziele aus Azure Container Registry (ACR) erstellt und geladen. Standardmäßig erstellt Azure Machine Learning eine ACR-Instanz mit der Dienstebene *Basic*. Wenn Sie die ACR-Instanz für Ihren Arbeitsbereich auf einen der Tarife „Standard“ oder „Premium“ umstellen, kann dies die Zeit zum Erstellen und Bereitstellen von Images auf Ihren Computezielen verringern. Weitere Informationen finden Sie unter [Azure Container Registry-Tarife](../container-registry/container-registry-skus.md).

> [!NOTE]
> Wenn Sie ein Modell in Azure Kubernetes Service (AKS) bereitstellen, empfehlen wir Ihnen, [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) für diesen Cluster zu aktivieren. Dadurch können Sie die Gesamtintegrität des Clusters sowie die Ressourcennutzung besser nachvollziehen. Folgende Ressourcen sind unter Umständen ebenfalls hilfreich:
>
> * [Überprüfung auf Resource Health-Ereignisse, die sich auf Ihren AKS-Cluster auswirken (Vorschau)](../aks/aks-resource-health.md)
> * [Übersicht über die Azure Kubernetes Service-Diagnose (Vorschau)](../aks/concepts-diagnostics.md)
>
> Wenn Sie versuchen, ein Modell in einem fehlerhaften oder überladenen Cluster bereitzustellen, ist davon auszugehen, dass Probleme auftreten. Sollten Sie Hilfe bei der Behandlung von AKS-Clusterproblemen benötigen, wenden Sie sich an den Support.

## <a name="delete-resources"></a>Löschen von Ressourcen

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)


[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/2.deploy-local-cli.ipynb?name=delete-resource-code)]

```azurecli-interactive
az ml service delete -n myservice
az ml service delete -n myaciservice
az ml model delete --model-id=<MODEL_ID>
```

Um einen bereitgestellten Webdienst zu löschen, verwenden Sie `az ml service delete <name of webservice>`.

Um ein registriertes Modell aus Ihrem Arbeitsbereich zu löschen, verwenden Sie `az ml model delete <model id>`.

Erfahren Sie mehr über das [Löschen eines Webdiensts](/cli/azure/ml(v1)/computetarget/create#az_ml_service_delete) und das [Löschen eines Modells](/cli/azure/ml/model#az_ml_model_delete).

# <a name="python"></a>[Python](#tab/python)

[!Notebook-python[] (~/azureml-examples-main/python-sdk/tutorials/deploy-local/1.deploy-local.ipynb?name=delete-resource-code)]

Verwenden Sie zum Löschen eines bereitgestellten Webdiensts `service.delete()`.
Verwenden Sie zum Löschen eines registrierten Modells `model.delete()`.

Weitere Informationen finden Sie in der Dokumentation zu [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) und [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--).

---

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Bereitstellung mit nur einem Klick für Ausführungen zum automatisierten maschinellen Lernen in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Erstellen von Ereigniswarnungen und Triggern für Modellbereitstellungen](how-to-use-event-grid.md)
