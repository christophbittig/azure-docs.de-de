---
title: Modellbereitstellung mit hoher Leistung mit Triton (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihr Modell mit NVIDIA Triton Inference Server in Azure Machine Learning bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/17/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: d1f45c5501395ff486101f7270212c8fb333aede
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436966"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Bereitstellung mit hoher Leistung mit Triton Inference Server (Vorschau) 

Erfahren Sie, wie Sie mit [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) die Leistung des für Modellrückschlüsse verwendeten Webdiensts verbessern können.

Eine der Möglichkeiten, ein Modell für Rückschlüsse bereitzustellen, ist als Webdienst. Beispielsweise eine Bereitstellung für Azure Kubernetes Service oder Azure Container Instances. Standardmäßig verwendet Azure Machine Learning für die Bereitstellung von Webdiensten ein *universelles* Webframework mit einem einzelnen Thread.

Triton ist ein Framework, das für *Rückschlüsse* optimiert ist. Es ermöglicht eine bessere Auslastung der GPUs und kostengünstigere Rückschlüsse. Auf der Serverseite fasst es eingehende Anforderungen in Batches zusammen und übermittelt diese Batches für Rückschlüsse. Die Batchverarbeitung lastet die GPU-Ressourcen besser aus und ist ein wesentlicher Bestandteil der Leistung von Triton.

> [!IMPORTANT]
> Die Verwendung von Triton für die Bereitstellung von Azure Machine Learning befindet sich derzeit in der __Vorschau__. Vorschaufunktionen werden möglicherweise nicht vom Kundensupport behandelt. Weitere Informationen finden Sie in den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!TIP]
> Die Codeausschnitte in diesem Dokument dienen der Veranschaulichung und zeigen möglicherweise keine vollständige Lösung. Funktionierenden Beispielcode finden Sie in den [End-to-End-Beispielen von Triton in Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) ist eine Open Source-Software eines Drittanbieters, die in Azure Machine Learning integriert ist.

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.
* Vertrautheit mit der Vorgehensweise, [wie und wo ein Modell mit Azure Machine Learning bereitgestellt werden soll](how-to-deploy-and-where.md).
* Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/) **oder** die [Azure CLI](/cli/azure/) und die [Machine Learning-Erweiterung](reference-azure-machine-learning-cli.md).
* Eine funktionierende Installation von Docker für lokale Tests. Weitere Informationen zum Installieren und Überprüfen von Docker finden Sie unter [Orientation and setup](https://docs.docker.com/get-started/) (Ausrichtung und Setup) in der Docker-Dokumentation.

## <a name="architectural-overview"></a>Übersicht über die Architektur

Bevor Sie versuchen, Triton für Ihr eigenes Modell zu verwenden, ist es wichtig zu verstehen, wie es mit Azure Machine Learning funktioniert und wie es im Vergleich zu einer Standardbereitstellung aussieht.

**Standardbereitstellung ohne Triton**

* Es werden mehrere [Gunicorn](https://gunicorn.org/)-Worker gestartet, um eingehende Anforderungen parallel zu bearbeiten.
* Diese Worker übernehmen die Vorverarbeitung, den Aufruf des Modells und die Nachbearbeitung. 
* Clients verwenden den __Bewertungs-URI für Azure ML__. Beispiel: `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagramm zur normalen Bereitstellungsarchitektur ohne Triton":::

**Direktes Bereitstellen mit Triton**

* Anforderungen werden direkt an den Triton-Server gesendet.
* Triton verarbeitet Anforderungen in Batches, um die GPU-Auslastung zu maximieren.
* Vom Client wird der __Triton-URI__ verwendet, um Anforderungen zu senden. Beispiel: `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Bereitstellung von „Inferenceconfig“ nur mit Triton und ohne Python-Middleware":::


## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Bereitstellen von Triton ohne Python-Vorverarbeitung und -Nachbearbeitung

Führen Sie zunächst die folgenden Schritte aus, um zu überprüfen, ob der Triton Inference Server Ihr Modell verarbeiten kann.

### <a name="optional-define-a-model-config-file"></a>(Optional) Definieren einer Modellkonfigurationsdatei

Die Modellkonfigurationsdatei teilt Triton mit, wie viele Eingaben zu erwarten sind und in welchen Dimensionen diese Eingaben erfolgen sollen. Weitere Informationen zum Erstellen der Konfigurationsdatei finden Sie unter [Model configuration](https://aka.ms/nvidia-triton-docs) (Modellkonfiguration) in der NVIDIA-Dokumentation.

> [!TIP]
> Wir verwenden die Option `--strict-model-config=false` beim Start von Triton Inference Server, was bedeutet, dass Sie für ONNX- oder TensorFlow-Modelle keine `config.pbtxt`-Datei bereitstellen müssen.
> 
> Weitere Informationen zu dieser Option finden Sie unter [Generated model configuration](https://aka.ms/nvidia-triton-docs) (Generierte Modellkonfiguration) in der NVIDIA-Dokumentation.

### <a name="use-the-correct-directory-structure"></a>Verwenden der richtigen Verzeichnisstruktur

Wenn Sie ein Modell mit Azure Machine Learning registrieren, können Sie entweder einzelne Dateien oder eine Verzeichnisstruktur registrieren. Um Triton zu verwenden, muss die Modellregistrierung für eine Verzeichnisstruktur erfolgen, die ein Verzeichnis namens `triton` enthält. Die allgemeine Struktur dieses Verzeichnisses lautet wie folgt:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Diese Verzeichnisstruktur ist ein Triton-Modellrepository und ist für Ihre Modelle für die Arbeit mit Triton erforderlich. Weitere Informationen finden Sie unter [Triton Model Repositories](https://aka.ms/nvidia-triton-docs) (Triton-Modellrepositorys) in der NVIDIA-Dokumentation.

### <a name="register-your-triton-model"></a>Registrieren Ihres Triton-Modells

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Weitere Informationen zu `az ml model register` finden Sie in der [Referenzdokumentation](/cli/azure/ml/model).

Wenn Sie das Modell in Azure Machine Learning registrieren, muss der Wert für den Parameter `--model-path  -p` dem Namen des übergeordneten Ordners des Triton-Modellrepositorys entsprechen.
Im Beispiel oben ist der Wert von `--model-path` „models“.

Der Wert für den Parameter `--name  -n` (in diesem Beispiel `my_triton_models`) entspricht dem im Azure Machine Learning-Arbeitsbereich bekannten Modellnamen. 

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=register-model)]

Weitere Informationen finden Sie in der Dokumentation zur [Model-Klasse](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Wenn Sie über einen GPU-fähigen Azure Kubernetes Service-Cluster mit dem Namen „aks-gpu“ verfügen, der per Azure Machine Learning erstellt wurde, können Sie den folgenden Befehl zum Bereitstellen Ihres Modells verwenden.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=deploy-webservice)]

---

Lesen Sie die [weiteren Informationen zur Bereitstellung von Modellen](how-to-deploy-and-where.md).

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

### <a name="call-into-your-deployed-model"></a>Aufrufen Ihres bereitgestellten Modells

Ermitteln Sie zunächst Ihren Bewertungs-URI und die Bearertoken.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=get-keys)]

---

Stellen Sie anschließend wie folgt sicher, dass Ihr Dienst ausgeführt wird: 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Die Ausgabe dieses Befehls sieht etwa wie folgt aus. Beachten Sie das `200 OK`. Dieser Status bedeutet, dass der Webserver aktiv ist.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=query-service)]

---


Nachdem Sie eine Integritätsprüfung durchgeführt haben, können Sie einen Client erstellen, um Daten für Rückschlüsse an Triton zu senden. Weitere Informationen zum Erstellen eines Clients finden Sie in den [Clientbeispielen](https://aka.ms/nvidia-client-examples) in der NVIDIA-Dokumentation. Es gibt auch [Python-Beispiele im Triton GitHub](https://aka.ms/nvidia-triton-docs).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie planen, den Azure Machine Learning-Arbeitsbereich weiterhin zu nutzen, aber den bereitgestellten Dienst entfernen möchten, verwenden Sie eine der folgenden Optionen:


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=delete-service)]

---

## <a name="how-to-use-azure-machine-learning-triton-inference-server-container-image"></a>Verwenden des Azure Machine Learning-Containerimages für Triton Inference Server

Erfahren Sie, wie Sie das Azure Machine Learning-Containerimage für Triton Inference Server mit der neuen [Befehlszeilenschnittstelle (V2)](/cli/azure/ml?view=azure-cli-latest&preserve-view=true) verwenden. In den folgenden Beispielen wird das Konzept von [Onlineendpunkten und -bereitstellungen](concept-endpoints.md#what-are-online-endpoints-preview) angewandt. 

1. [Bereitstellen eines einzelnen Triton-Modells](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-managed-online-endpoint.sh)
1. [Bereitstellen mehrerer Triton-Modelle](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-multiple-models-online-endpoint.sh)
1. [Bereitstellen eines Triton-Ensemblemodells](https://github.com/Azure/azureml-examples/blob/main/cli/deploy-triton-ensemble-managed-online-endpoint.sh)
1. Auschecken der [Triton-Beispiele](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/triton)

## <a name="troubleshoot"></a>Problembehandlung

* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md): Erfahren Sie, wie Sie häufige Fehler behandeln und beheben oder umgehen können, die beim Bereitstellen eines Modells auftreten können.

* Wenn in den Bereitstellungsprotokollen angezeigt wird, dass **TritonServer nicht starten konnte**, lesen Sie die [Open-Source-Dokumentation von Nvidia](https://github.com/triton-inference-server/server).

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen der End-to-End-Beispiele von Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Testen der [Triton-Clientbeispiele](https://aka.ms/nvidia-client-examples)
* Lesen der [Triton Inference Server-Dokumentation](https://aka.ms/nvidia-triton-docs)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
