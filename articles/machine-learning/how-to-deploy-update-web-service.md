---
title: " Aktualisieren von Webdiensten"
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie einen Webdienst aktualisieren, der bereits in Azure Machine Learning bereitgestellt wurde. Sie können Einstellungen wie Modell, Umgebung und Einstiegsskript aktualisieren.
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.custom: deploy
ms.openlocfilehash: ae53790d2fc119f1242f44326e8ca40b8483f9eb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557260"
---
# <a name="update-a-deployed-web-service"></a>Aktualisieren eines bereitgestellten Webdiensts

In diesem Artikel erfahren Sie, wie Sie einen Webdienst aktualisieren, der mit Azure Machine Learning bereitgestellt wurde.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass Sie bereits einen Webdienst mit Azure Machine Learning bereitgestellt haben. [Befolgen Sie diese Schritte](how-to-deploy-and-where.md), wenn Sie erfahren müssen, wie ein Webdienst bereitgestellt wird.

## <a name="update-web-service"></a>Aktualisieren des Webdiensts

Verwenden Sie die `update`-Methode, um einen Webdienst zu aktualisieren. Sie können den Webdienst aktualisieren, um ein neues Modell, ein neues Eingabeskript oder neue Abhängigkeiten zu verwenden, die in einer Rückschlusskonfiguration angegeben werden können. Weitere Informationen finden Sie in der Dokumentation zu [Webservice.update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-).

Weitere Informationen finden Sie unter [AKS-Dienstaktualisierungsmethode.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Weitere Informationen finden Sie unter [ACI-Dienstaktualisierungsmethode.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Wenn Sie eine neue Version eines Modells erstellen, müssen Sie jeden Dienst, der die neue Version verwenden soll, manuell aktualisieren.
>
> Sie können nicht das SDK verwenden, um einen Webdienst zu aktualisieren, der über den Azure Machine Learning-Designer veröffentlicht wurde.

> [!IMPORTANT]
> Azure Kubernetes Service verwendet den [Blobfuse FlexVolume-Treiber](https://github.com/Azure/kubernetes-volume-drivers/blob/master/flexvolume/blobfuse/README.md) für die Versionen <=1.16 und den [Blob CSI-Treiber](https://github.com/kubernetes-sigs/blob-csi-driver/blob/master/README.md) für die Versionen >=1.17. 
>
> Daher ist es wichtig, den Webdienst nach dem Clusterupgrade erneut bereitzustellen oder zu aktualisieren, um die Bereitstellung für die richtige Blobfuse-Methode für die Clusterversion durchzuführen.

> [!NOTE]
> Wenn ein Vorgang bereits ausgeführt wird, antwortet jeder neue Vorgang für denselben Webdienst mit einem 409-Konfliktfehler. Wenn beispielsweise ein Webdienstvorgang erstellt oder aktualisiert wird und Sie einen neuen Löschvorgang auslösen, wird ein Fehler ausgelöst.

**Verwenden des SDK**

Im folgenden Code wird gezeigt, wie Sie mithilfe des SDK das Modell, die Umgebung und das Eingabeskript für einen Webdienst aktualisieren:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**Verwenden der CLI**

Sie können einen Webdienst auch über die ML CLI aktualisieren. Im folgenden Beispiel wird veranschaulicht, wie Sie ein neues Modell registrieren und anschließend einen Webdienst für die Verwendung des neuen Modells aktualisieren:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In diesem Beispiel wird ein JSON-Dokument verwendet, um die Modellinformationen vom Registrierungsbefehl an den Aktualisierungsbefehl zu übergeben.
>
> Um den Dienst so zu aktualisieren, dass für ihn ein neues Eingabeskript oder eine neue Umgebung verwendet wird, erstellen Sie eine [Rückschlusskonfigurationsdatei](./reference-azure-machine-learning-cli.md#inference-configuration-schema), und geben Sie diese mit dem `ic`-Parameter an.

Weitere Informationen finden Sie in der Dokumentation zu [az ml service update](/cli/azure/ml(v1)/service?view=azure-cli-latest#az_ml_v1__service_update&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Erstellen von Clientanwendungen zum Nutzen von Webdiensten](how-to-consume-web-service.md)
* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](./how-to-deploy-custom-container.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
* [Erstellen von Ereigniswarnungen und Triggern für Modellbereitstellungen](how-to-use-event-grid.md)
