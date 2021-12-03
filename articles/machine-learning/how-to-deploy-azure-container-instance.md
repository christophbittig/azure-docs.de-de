---
title: Bereitstellen von Modellen in Azure Container Instances
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre Azure Machine Learning-Modelle mithilfe von Azure Container Instances als Webdienst bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: 148cce3452bdf27a2a8962aa5f7b58d924a8eb8f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553802"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Bereitstellen eines Modells in Azure Container Instances

Erfahren Sie, wie Sie ein Modell mit Azure Machine Learning als Webdienst in Azure Container Instances (ACI) bereitstellen. Verwenden Sie Azure Container Instances, wenn Folgendes zutrifft:

- Sie möchten keinen eigenen Kubernetes-Cluster verwalten.
- Sie benötigen nur ein Replikat Ihres Diensts (kann sich auf die Uptime auswirken).

Informationen zu den für ACI geltenden Kontingenten und zur Verfügbarkeit in den Regionen finden Sie im Artikel [Kontingente und Limits für Azure Container Instances](../container-instances/container-instances-quotas.md).

> [!IMPORTANT]
> Es wird dringend empfohlen, vor der Bereitstellung im Webdienst lokal zu debuggen. Weitere Informationen finden Sie unter [Lokales Debuggen](./how-to-troubleshoot-deployment-local.md).
>
> Weitere Informationen finden Sie auch unter Azure Machine Learning – [Bereitstellung auf lokalem Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

- Ein Machine Learning-Modell, das in Ihrem Arbeitsbereich registriert ist. Wenn Sie über kein registriertes Modell verfügen, finden Sie hier weitere Informationen: [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

- Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](how-to-setup-vs-code.md).

- Bei den in diesem Artikel verwendeten __Python__-Codeausschnitten wird davon ausgegangen, dass die folgenden Variablen festgelegt sind:

    * `ws`: Legen Sie diese Variable auf Ihren Arbeitsbereich fest.
    * `model`: Legen Sie diese Variable auf Ihr registriertes Modell fest.
    * `inference_config`: Legen Sie diese Variable auf die Rückschlusskonfiguration für das Modell fest.

    Weitere Informationen zum Festlegen dieser Variablen finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

- Bei den in diesem Artikel verwendeten __CLI__-Ausschnitten wird davon ausgegangen, dass Sie ein `inferenceconfig.json`-Dokument erstellt haben. Weitere Informationen zum Erstellen dieses Dokuments finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

## <a name="limitations"></a>Einschränkungen

* Wenn Sie Azure Container Instances in einem virtuellen Netzwerk verwenden, muss sich das virtuelle Netzwerk in derselben Ressourcengruppe wie der Azure Machine Learning-Arbeitsbereich befinden.
* Wenn Sie Azure Container Instances innerhalb des virtuellen Netzwerks verwenden, darf sich die Azure Container Registry (ACR) für Ihren Arbeitsbereich nicht ebenfalls im virtuellen Netzwerk befinden.

Weitere Informationen finden Sie unter [Schützen von Rückschlüssen mit virtuellen Netzwerken](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Bereitstellen für ACI

Um ein Modell für Azure Container Instances bereitzustellen, erstellen Sie eine __Bereitstellungskonfiguration__, in der die benötigten Computeressourcen beschrieben werden. Dies sind beispielsweise die Anzahl von Kernen und die Arbeitsspeichergröße. Außerdem benötigen Sie eine __Rückschlusskonfiguration__, in der die zum Hosten des Modells und des Webdiensts erforderliche Umgebung beschrieben wird. Weitere Informationen zum Erstellen der Rückschlusskonfiguration finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI eignet sich nur für kleine Modelle, die weniger als 1 GB groß sind. 
> * Wir empfehlen die Verwendung von AKS mit einem einzelnen Knoten, um größere Modelle zu entwickeln.
> * Die Anzahl der bereitzustellenden Modelle ist auf 1.000 Modelle pro Bereitstellung (pro Container) beschränkt. 

### <a name="using-the-sdk"></a>Verwenden des SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen, Methoden und Parametern finden Sie in den folgenden Referenzdokumenten:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Verwenden Sie für die Bereitstellung mit der CLI den folgenden Befehl. Ersetzen Sie `mymodel:1` durch den Namen und die Version des registrierten Modells. Ersetzen Sie `myservice` durch den Namen, den dieser Dienst erhalten soll:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Weitere Informationen finden Sie in der [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy)-Referenz. 

## <a name="using-vs-code"></a>Verwenden von VS Code

Informationen finden Sie im Artikel zum [Verwalten von Ressourcen in VS Code](how-to-manage-resources-vscode.md).

> [!IMPORTANT]
> Sie müssen vorab keinen ACI-Container zu Testzwecken erstellen. ACI-Container werden bei Bedarf erstellt.

> [!IMPORTANT]
> Wir fügen eine Arbeitsbereichs-ID mit Hash allen zugrunde liegenden ACI-Ressourcen hinzu, die erstellt werden. Alle ACI-Namen im selben Arbeitsbereich haben dasselbe Suffix. Der Azure Machine Learning Service-Name wäre immer noch derselbe vom Kunden vorgegebene „Dienstname“, und für alle Benutzer von Azure Machine Learning SDK-APIs ist keine Änderung erforderlich. Wir geben keine Garantien für die Namen der zugrunde liegenden Ressourcen, die erstellt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](./how-to-deploy-custom-container.md)
* [Problembehandlung von Bereitstellungen von Azure Machine Learning Service mit AKS und ACI](how-to-troubleshoot-deployment.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Consume a ML Model deployed as a web service (Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells)](how-to-consume-web-service.md).
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)