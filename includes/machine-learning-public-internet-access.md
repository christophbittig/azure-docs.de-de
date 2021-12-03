---
title: Includedatei
description: Includedatei
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 11/05/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: e066c97e12f4b8e34f66235f53857c583e4350a9
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135901"
---
Azure Machine Learning erfordert sowohl eingehenden als auch ausgehenden Zugriff auf das öffentliche Internet. Die folgenden Tabellen geben Aufschluss darüber, welcher Zugriff wofür erforderlich ist. Das __Protokoll__ für alle Elemente ist __TCP__. Ersetzen Sie bei Diensttags, die auf `.region` enden, `region` durch die Azure-Region, die Ihren Arbeitsbereich enthält. Zum Beispiel `Storage.westus`:

| Direction | Ports | Diensttag | Zweck |
| ----- |:-----:| ----- | ----- |
| Eingehend | 29876–29877 | BatchNodeManagement | Erstellen, Aktualisieren und Löschen einer Azure Machine Learning Compute-Instanz und des Computeclusters. |
| Eingehend | 44224 | AzureMachineLearning | Erstellen, Aktualisieren und Löschen einer Azure Machine Learning Compute-Instanz. |
| Ausgehend | 80, 443 | AzureActiveDirectory | Verwenden von Azure AD für die Authentifizierung. |
| Ausgehend | 443 | AzureMachineLearning | Verwenden von Azure Machine Learning Service. |
| Ausgehend | 443 | AzureResourceManager | Erstellen von Azure-Ressourcen mit Azure Machine Learning. |
| Ausgehend | 443 | Storage.region | Auf Daten zugreifen, die im Azure Storage-Konto für den Azure Batch-Dienst sind. |
| Ausgehend | 443 | AzureFrontDoor.FrontEnd</br>* In Azure China nicht erforderlich. | Globaler Einstiegspunkt für [Azure Machine Learning Studio](https://ml.azure.com). | 
| Ausgehend | 443 | ContainerRegistry.region | Auf Docker-Images zugreifen, die von Microsoft bereitgestellt werden. |
| Ausgehend | 443 | MicrosoftContainerRegistry.region | Auf Docker-Images zugreifen, die von Microsoft bereitgestellt werden. Einrichten des Azure Machine Learning Routers für Azure Kubernetes Service. |
| Ausgehend | 443 | Keyvault.region | Greifen Sie auf den Schlüsseltresor für den Azure Batch-Dienst zu. Dieser Parameter ist nur erforderlich, wenn Ihr Arbeitsbereich mit aktiviertem [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)-Flag erstellt wurde. |

> [!TIP]
> Falls Sie anstelle von Diensttags die IP-Adressen benötigen, verwenden Sie eine der folgenden Optionen:
> * Laden Sie unter [Azure-IP-Bereiche und -Diensttags: Öffentliche Cloud](https://www.microsoft.com/download/details.aspx?id=56519) eine Liste herunter.
> * Verwenden Sie den Azure CLI-Befehl [az network list-service-tags](/cli/azure/network#az_network_list_service_tags).
> * Verwenden Sie den Azure PowerShell-Befehl [get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag).
> 
> Die IP-Adressen können sich von Zeit zu Zeit ändern.

Unter Umständen muss auch __ausgehender__ Datenverkehr für Visual Studio Code und Microsoft-fremde Websites zugelassen werden, um die Installation von Paketen zu ermöglichen, die für Ihr Machine Learning-Projekt erforderlich sind. Die folgende Tabelle enthält gängige Repositorys für maschinelles Lernen:

| Hostname | Zweck |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | Wird verwendet, um Standardpakete zu installieren |
| **\*.anaconda.org** | Wird verwendet, um Repositorydaten abzurufen |
| **pypi.org** | Wird zum Auflisten von Abhängigkeiten vom Standardindex verwendet, sofern vorhanden, und der Index wird nicht durch Benutzereinstellungen überschrieben. Wenn der Index überschrieben wird, müssen Sie auch **\*.pythonhosted.org** zulassen. |
| **cloud.r-project.org** | Wird beim Installieren von CRAN-Paketen für die R-Entwicklung verwendet. |
| **\*pytorch.org** | Wird von einigen Beispielen verwendet, die auf PyTorch basieren. |
| **\*.tensorflow.org** | Wird von einigen Beispielen verwendet, die auf Tensorflow basieren. |
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Dient zum Abrufen von VS Code-Server-Bits, die über ein Setup-Skript auf der Compute-Instanz installiert werden.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Dient zum Abrufen von Websocket-Server-Bits, die auf der Compute-Instanz installiert sind. Der Websocket-Server wird verwendet, um Anforderungen des Visual Studio Code-Clients (Desktopanwendung) an den Visual Studio Code-Server zu übertragen, der in der Compute-Instanz ausgeführt wird.|

Wenn Sie Azure Kubernetes Service (AKS) mit Azure Machine Learning verwenden, lassen Sie den folgenden Datenverkehr an das AKS-VNET zu:

* Allgemeine Anforderungen für eingehenden/ausgehenden Datenverkehr für AKS, wie im Artikel [Einschränken des ausgehenden Datenverkehrs in Azure Kubernetes Service](../articles/aks/limit-egress-traffic.md) beschrieben.
* __Ausgehend__ an mcr.microsoft.com.
* Wenn Sie ein Modell in einem AKS-Cluster bereitstellen, beziehen Sie sich auf den Leitfaden im Artikel [Bereitstellen von ML-Modellen in Azure Kubernetes Service](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md#connectivity).