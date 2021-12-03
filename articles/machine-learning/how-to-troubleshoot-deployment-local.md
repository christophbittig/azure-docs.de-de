---
title: Behandeln von Problemen bei der lokalen Modellimplementierung
titleSuffix: Azure Machine Learning
description: Versuchen Sie es bei der Problembehandlung im Zusammenhang mit Modellimplementierungsfehlern zunächst mit einer lokalen Modellimplementierung.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: luquinta
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 6db00dfa109bad8acf0f9a56b9dd2559e5999d3e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560718"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Behandeln von Problemen mit einer lokalen Modellimplementierung

Versuchen Sie es bei der Problembehandlung im Zusammenhang mit der Bereitstellung in Azure Container Instances (ACI) oder Azure Kubernetes Service (AKS) zunächst mit einer lokalen Modellimplementierung.  Die Verwendung eines lokalen Webdiensts erleichtert die Erkennung und Behebung allgemeiner Fehler im Zusammenhang mit der Docker-Webdienstbereitstellung für Azure Machine Learning.

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.
* Option A (**empfohlen**): lokales Debuggen in der Azure Machine Learning Compute-Instanz
   * Ein Azure Machine Learning-Arbeitsbereich mit einer ausgeführten [Compute-Instanz](how-to-deploy-local-container-notebook-vm.md)
* Option B: lokales Debuggen auf Ihrer Computeressource
   * Das [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
   * Die [Azure CLI](/cli/azure/install-azure-cli)
   * Die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).
   * Eine funktionierende Installation von Docker auf Ihrem lokalen System. 
   * Verwenden Sie den Befehl `docker run hello-world` über ein Terminal oder eine Befehlszeile, um Ihre Docker-Installation zu überprüfen. Informationen zur Installation von Docker oder zur Problembehandlung bei Docker-Fehlern finden Sie in der [Docker-Dokumentation](https://docs.docker.com/).
* Option C: Aktivieren des lokalen Debuggens mit einem HTTP-Rückschlussserver für Azure Machine Learning.
    * Der HTTP-Rückschlussserver von Azure Machine Learning [(Vorschau)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ist ein Python-Paket, mit dem Sie Ihr Einstiegsskript (`score.py`) in einer lokalen Entwicklungsumgebung problemlos überprüfen können. Wenn ein Problem mit dem Bewertungsskript vorliegt, gibt der Server einen Fehler zurück. Außerdem wird die Position zurückgegeben, an der der Fehler aufgetreten ist.
    * Der Server kann auch verwendet werden, wenn Validierungsgates in einer Pipeline für Continuous Integration und Continuous Deployment erstellt werden. Starten Sie z. B. den Server mit dem Kandidatenskript, und führen Sie die Testsammlung für den lokalen Endpunkt aus.

## <a name="azure-machine-learning-inference-http-server"></a>HTTP-Rückschlussserver für Azure Machine Learning

Mit dem lokalen Rückschlussserver können Sie Ihr Eingabeskript (`score.py`) schnell debuggen. Falls das zugrunde liegende Bewertungsskript einen Fehler enthält, kann der Server das Modell nicht initialisieren oder bereitstellen. Stattdessen wird eine Ausnahme ausgelöst und der Ort angegeben, an dem die Probleme aufgetreten sind. [Weitere Informationen zum HTTP-Rückschlussserver von Azure Machine Learning](how-to-inference-server-http.md)

1. Installieren Sie das `azureml-inference-server-http`-Paket aus dem [pypi](https://pypi.org/)-Feed:

    ```bash
    python -m pip install azureml-inference-server-http
    ```

2. Starten Sie den Server, und legen Sie `score.py` als Einstiegsskript fest:

    ```bash
    azmlinfsrv --entry_script score.py
    ```

3. Senden Sie eine Bewertungsanforderung mithilfe von `curl` an den Server:

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

## <a name="debug-locally"></a>Lokales Debuggen

Im Repository [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) finden Sie ein beispielhaftes [lokales Bereitstellungsnotebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb), das Sie erkunden können.

> [!WARNING]
> Bereitstellungen lokaler Webdienste werden nicht für Produktionsszenarien unterstützt.

Ändern Sie zum lokalen Bereitstellen Ihren Code so, dass `LocalWebservice.deploy_configuration()` zum Erstellen einer Bereitstellungskonfiguration verwendet wird. Verwenden Sie dann `Model.deploy()`, um den Dienst bereitzustellen. Im folgenden Beispiel wird ein (in der Modellvariablen enthaltenes) Modell als lokaler Webdienst bereitgestellt:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Wenn Sie Ihre eigene YAML-Datei für die Conda-Spezifikation definieren, listen Sie „azureml-defaults“ mit einer Version größer oder gleich 1.0.45 als Pip-Abhängigkeit auf. Dieses Paket ist erforderlich, um das Modell als Webdienst zu hosten.

An diesem Punkt können Sie mit dem Dienst wie gewohnt arbeiten. Der folgende Code zeigt, wie Daten an den Dienst gesendet werden:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Weitere Informationen zur Anpassung Ihrer Python-Umgebung finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualisieren des Diensts

Beim lokalen Testen müssen Sie möglicherweise die `score.py`-Datei aktualisieren, um die Protokollierung hinzuzufügen oder ggf. zu versuchen, alle Probleme zu behandeln, die Sie ermittelt haben. Zum erneuten Laden von Änderungen an der `score.py`-Datei verwenden Sie `reload()`. Der folgende Code lädt z.B. das Skript für den Dienst neu und sendet dann Daten an ihn. Die Daten werden mithilfe der aktualisierten `score.py`-Datei bewertet:

> [!IMPORTANT]
> Die Methode `reload` ist nur für lokale Bereitstellungen verfügbar. Informationen zum Aktualisieren einer Bereitstellung auf ein anderes Computeziel finden Sie unter [Aktualisieren von Webdiensten](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Das Skript wird aus dem Speicherort erneut geladen, der durch das vom Dienst verwendete `InferenceConfig`-Objekt angegeben wird.

Um das Modell, Conda-Abhängigkeiten oder eine Bereitstellungskonfiguration zu ändern, verwenden Sie [update()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-). Das folgende Beispiel aktualisiert das vom Dienst verwendete Modell:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Löschen des Diensts

Verwenden Sie zum Löschen des Diensts [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Untersuchen des Docker-Protokolls

Das Dienstobjekt erlaubt die Ausgabe von detaillierten Protokollnachrichten der Docker-Engine. Sie können das Protokoll für ACI, AKS und lokale Bereitstellungen anzeigen. Die Ausgabe der Protokolle wird im folgenden Beispiel veranschaulicht.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Wenn die Zeile `Booting worker with pid: <pid>` in den Protokollen mehrmals angezeigt wird, ist nicht ausreichend Arbeitsspeicher vorhanden, um den Worker zu starten.
Sie können den Fehler beheben, indem Sie den Wert von `memory_gb` in `deployment_config` ändern.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bereitstellung finden Sie hier:

* [Problembehandlung bei Remotebereitstellungen](how-to-troubleshoot-deployment.md)
* [HTTP-Rückschlussserver für Azure Machine Learning](how-to-inference-server-http.md)
* [Bereitstellung: wie und wo?](how-to-deploy-and-where.md)
* [Tutorial: Trainieren und Bereitstellen von Modellen](tutorial-train-models-with-aml.md)
* [Lokales Ausführen und Debuggen von Experimenten](./how-to-debug-visual-studio-code.md)