---
title: Datei einfügen
description: Datei einfügen
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 07/21/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: b0b46da23de802d9f26e53d7b3acc96f166e78d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443388"
---
Azure Machine Learning erfordert sowohl eingehenden als auch ausgehenden Zugriff auf das öffentliche Internet. Die folgenden Tabellen geben Aufschluss darüber, welcher Zugriff wofür erforderlich ist. Das __Protokoll__ für alle Elemente ist __TCP__. Ersetzen Sie bei Diensttags, die auf `.region` enden, `region` durch die Azure-Region, die Ihren Arbeitsbereich enthält. Zum Beispiel `Storage.westus`:

| Direction | Ports | Diensttag | Zweck |
| ----- |:-----:| ----- | ----- |
| Eingehend | 29876–29877 | BatchNodeManagement | Azure Machine Learning-Compute-Instanz- und -Computecluster |
| Eingehend | 44224 | AzureMachineLearning | Azure Machine Learning-Compute-Instanz. |
| Ausgehend | * | AzureActiveDirectory | Azure Active Directory-Authentifizierung |
| Ausgehend | 443 | AzureMachineLearning | Azure Machine Learning. |
| Ausgehend | 443 | AzureResourceManager | Azure Resource Manager |
| Ausgehend | 443 | Storage.region | Azure-Speicherkonto |
| Ausgehend | 443 | AzureFrontDoor.FrontEnd</br>* In Azure China nicht erforderlich. | Azure Front Door | 
| Ausgehend | 443 | ContainerRegistry.region | Azure Container Registry. Nur bei Verwendung benutzerdefinierter Docker-Images erforderlich. Einschließlich geringfügiger Änderungen (z. B. zusätzliche Pakete) an von Microsoft bereitgestellten Basisimages. |
| Ausgehend | 443 | MicrosoftContainerRegistry.region | Nur erforderlich, wenn Sie von Microsoft bereitgestellte Docker-Images verwenden und benutzerseitig verwaltete Abhängigkeiten aktivieren. |

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
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Wird zum Abrufen von VS Code-Serverbits verwendet, die über ein Setupskript in der Compute-Instanz installiert werden.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Wird zum Abrufen von Websocket-Serverbits verwendet, die in der Compute-Instanz installiert werden. Der Websocket-Server wird verwendet, um Anforderungen des Visual Studio Code-Clients (Desktopanwendung) an den Visual Studio Code-Server zu übertragen, der in der Compute-Instanz ausgeführt wird.|