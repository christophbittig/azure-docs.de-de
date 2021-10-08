---
title: Konfigurieren von ein- und ausgehendem Netzwerkdatenverkehr
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie den erforderlichen ein- und ausgehenden Netzwerkdatenverkehr konfigurieren, wenn Sie einen sicheren Azure Machine Learning-Arbeitsbereich verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/14/2021
ms.custom: devx-track-python
ms.openlocfilehash: 2207ac32595d3780bf662d9a4124b7cbf74ce6a7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633969"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>Konfigurieren von ein- und ausgehendem Netzwerkdatenverkehr

In diesem Artikel erfahren Sie mehr über die Netzwerkkommunikationsanforderungen beim Sichern eines Azure Machine Learning-Arbeitsbereichs in einem virtuellen Netzwerk (VNet). Dies umfasst, wie Sie Azure Firewall konfigurieren, um den Zugriff auf Ihren Azure Machine Learning-Arbeitsbereich und das öffentliche Internet zu steuern. Weitere Informationen zum Schützen von Azure Machine Learning finden Sie unter [Unternehmenssicherheit für Azure Machine Learning](concept-enterprise-security.md).

> [!NOTE]
> Die Informationen in diesem Artikel gelten für den Azure Machine Learning-Arbeitsbereich, der mit einem privaten Endpunkt konfiguriert ist.

> [!TIP]
> Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:
>
> * [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
> * [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
> * [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
> * [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
> * [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
> * [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)

## <a name="required-public-internet-access"></a>Erforderlicher öffentlicher Internetzugriff

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Azure Firewall

> [!IMPORTANT]
> Azure Firewall bietet Sicherheit für _Azure Virtual Network-Ressourcen_. Einige Azure-Dienste, z. B. Azure Storage-Konten, verfügen über eigene Firewalleinstellungen, die für den _öffentlichen Endpunkt dieser speziellen Dienstinstanz gelten_. Die Informationen in diesem Dokument beziehen sich auf Azure Firewall.
> 
> Informationen zu den Firewalleinstellungen der Dienstinstanz finden Sie unter [Verwenden von Studio in einem virtuellen Netzwerk](how-to-enable-studio-virtual-network.md#firewall-settings).

* Verwenden Sie für den __eingehenden__ Datenverkehr zum Azure Machine Learning-Computecluster und zur Compute-Instanz [benutzerdefinierte Routen (UDRs)](../virtual-network/virtual-networks-udr-overview.md), um die Firewall zu umgehen.

* Für __ausgehenden__ Datenverkehr erstellen Sie __Netzwerkregeln__ und __Anwendungsregeln__. 

Diese Regelsammlungen werden unter [Wie lauten einige der Azure Firewall-Konzepte?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) ausführlicher beschrieben.

### <a name="inbound-configuration"></a>Konfiguration für eingehenden Datenverkehr

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>Konfiguration für ausgehenden Datenverkehr

1. Fügen Sie __Netzwerkregeln__ hinzu, um Datenverkehr __zu__ und __von__ den folgenden Diensttags zuzulassen:

    | Diensttag | Protocol | Port |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* In Azure China nicht erforderlich. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region ist nur für benutzerdefinierte Docker-Images erforderlich. Dazu gehören geringfügige Änderungen (z. B. zusätzliche Pakete) an von Microsoft bereitgestellten Basisimages.
    > * MicrosoftContainerRegistry.region wird nur benötigt, wenn Sie planen, die von Microsoft bereitgestellten _Standard-Docker-Images_ zu verwenden und vom _Benutzer verwaltete Abhängigkeiten zu aktivieren_.
    > * „Keyvault.region“ ist nur erforderlich, wenn Ihr Arbeitsbereich mit aktiviertem [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)-Flag erstellt wurde.
    > * Ersetzen Sie bei Einträgen, die `region` enthalten, durch die verwendete Azure-Region. Beispiel: `ContainerRegistry.westus`.

1. Fügen Sie __Anwendungsregeln__ für die folgenden Hosts hinzu:

    > [!NOTE]
    > Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten Python-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

    | **Hostname** | **Zweck** |
    | ---- | ---- |
    | **graph.windows.net** | Verwendet von einer Compute-Instanz oder einem Computecluster von Azure Machine Learning |
    | **anaconda.com**</br>**\*.anaconda.com** | Wird verwendet, um Standardpakete zu installieren |
    | **\*.anaconda.org** | Wird verwendet, um Repositorydaten abzurufen |
    | **pypi.org** | Wird zum Auflisten von Abhängigkeiten vom Standardindex verwendet, sofern vorhanden, und der Index wird nicht durch Benutzereinstellungen überschrieben. Wenn der Index überschrieben wird, müssen Sie auch **\*.pythonhosted.org** zulassen. |
    | **cloud.r-project.org** | Wird beim Installieren von CRAN-Paketen für die R-Entwicklung verwendet. |
    | **\*pytorch.org** | Wird von einigen Beispielen verwendet, die auf PyTorch basieren. |
    | **\*.tensorflow.org** | Wird von einigen Beispielen verwendet, die auf Tensorflow basieren. |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Wird zum Abrufen von VS Code-Serverbits verwendet, die über ein Setupskript in der Compute-Instanz installiert werden.|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Wird zum Abrufen von Websocket-Serverbits verwendet, die in der Compute-Instanz installiert werden. Der Websocket-Server wird verwendet, um Anforderungen des Visual Studio Code-Clients (Desktopanwendung) an den Visual Studio Code-Server zu übertragen, der in der Compute-Instanz ausgeführt wird.|
    | **dc.applicationinsights.azure.com** | Wird zum Sammeln von Metriken und Diagnoseinformationen für die Zusammenarbeit mit dem Microsoft-Support verwendet. |
    | **dc.applicationinsights.microsoft.com** | Wird zum Sammeln von Metriken und Diagnoseinformationen für die Zusammenarbeit mit dem Microsoft-Support verwendet. |
    | **dc.services.visualstudio.com** | Wird zum Sammeln von Metriken und Diagnoseinformationen für die Zusammenarbeit mit dem Microsoft-Support verwendet. | 
    

    Wählen Sie __http, https__ für __Protokoll:Port__ aus.

    Weitere Informationen zum Konfigurieren von Anwendungsregeln finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Um den ausgehenden Datenverkehr für Modelle einzuschränken, die in Azure Kubernetes Service (AKS) bereitgestellt werden, lesen Sie die Artikel [Einschränken des ausgehenden Datenverkehrs in Azure Kubernetes Service](../aks/limit-egress-traffic.md) und [Bereitstellen von ML-Modellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Service

Wenn Sie Azure Kubernetes Service mit Azure Machine Learning verwenden, muss der folgende Datenverkehr zugelassen werden:

* Allgemeine Anforderungen für eingehenden/ausgehenden Datenverkehr für AKS, wie im Artikel [Einschränken des ausgehenden Datenverkehrs in Azure Kubernetes Service](../aks/limit-egress-traffic.md) beschrieben.
* __Ausgehend__ an mcr.microsoft.com.
* Wenn Sie ein Modell in einem AKS-Cluster bereitstellen, beziehen Sie sich auf den Leitfaden im Artikel [Bereitstellen von ML-Modellen in Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

## <a name="other-firewalls"></a>Andere Firewalls

Die Anleitungen in diesem Abschnitt sind generisch, da jede Firewall über eine eigene Terminologie und bestimmte Konfigurationen verfügt. Wenn Sie Fragen haben, lesen Sie die Dokumentation für die von Ihnen verwendete Firewall.

Wenn sie nicht ordnungsgemäß konfiguriert ist, kann die Firewall Probleme bei der Verwendung Ihres Arbeitsbereichs verursachen. Es gibt verschiedene Hostnamen, die vom Azure Machine Learning-Arbeitsbereich verwendet werden. In den folgenden Abschnitten sind Hosts aufgeführt, die für Azure Machine Learning erforderlich sind.

### <a name="microsoft-hosts"></a>Microsoft-Hosts

Die Hosts in den folgenden Tabellen befinden sich im Besitz von Microsoft und stellen Dienste bereit, die für die ordnungsgemäße Funktionsweise Ihres Arbeitsbereichs erforderlich sind. In den Tabellen werden Hosts für die Regionen „Azure (öffentlich)“, „Azure Government“ und „Azure China 21ViaNet“ aufgelistet.

**Allgemeine Azure-Hosts**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure-Portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning-Hosts**

> [!IMPORTANT]
> Ersetzen Sie `<storage>` in der folgenden Tabelle durch den Namen des Standardspeicherkontos für Ihren Azure Machine Learning-Arbeitsbereich.

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Integriertes Notebook | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Integriertes Notebook | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| Integriertes Notebook | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| Integriertes Notebook | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| Integriertes Notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Integriertes Notebook | \*.aznbcontent.net |  | |

**Azure Machine Learning-Compute-Instanz- und -Computeclusterhosts**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Computecluster/-Instanz | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Compute-Instanz | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Compute-Instanz | \*.instances.azureml.ms |  |  |
| Azure Storage-Konto | \*.blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure-Schlüsseltresor | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> Ihre Firewall muss die Kommunikation mit \*.instances.azureml.ms über die __TCP__-Ports __18881, 443 und 8787__ zulassen.

> [!TIP]
> Der FQDN für Azure Key Vault ist nur erforderlich, wenn Ihr Arbeitsbereich mit aktiviertem [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)-Flag erstellt wurde.

**Docker-Images, die von Azure Machine Learning verwaltet werden**

| **Erforderlich für** | **Azure (öffentlich)** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Vordefinierte Azure Machine Learning-Images | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * __Azure Container Registry__ ist für jedes benutzerdefinierte Docker-Image erforderlich. Dazu gehören geringfügige Änderungen (z. B. zusätzliche Pakete) an von Microsoft bereitgestellten Basisimages.
> * __Microsoft Container Registry__ wird nur benötigt, wenn Sie planen, die von Microsoft bereitgestellten _Standard-Docker-Images_ zu verwenden und vom _Benutzer verwaltete Abhängigkeiten zu aktivieren_.
> * Wenn Sie die Verbundidentität verwenden möchten, befolgen Sie die Anleitungen im Artikel [Bewährte Methoden zum Sichern von Active Directory-Verbunddiensten](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs).

Verwenden Sie außerdem die Informationen im Abschnitt über die [Eingangskonfiguration](#inbound-configuration), um IP-Adressen für `BatchNodeManagement` und `AzureMachineLearning` hinzuzufügen.

Informationen zum Einschränken des Zugriffs auf Modelle, die in AKS bereitgestellt werden, finden Sie unter [Steuern des ausgehenden Datenverkehrs in Azure Kubernetes Service](../aks/limit-egress-traffic.md).

**Unterstützen der Diagnose**

Damit der Microsoft-Support Probleme mit Ihrem Arbeitsbereich diagnostizieren kann, müssen Sie ausgehenden Datenverkehr an die folgenden Hosts zulassen:

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

Eine Liste der IP-Adressen für diese Hosts finden Sie unter [Von Azure Monitor verwendete IP-Adressen](../azure-monitor/app/ip-addresses.md).

### <a name="python-hosts"></a>Python-Hosts

Die Hosts in diesem Abschnitt werden zum Installieren von Python-Paketen verwendet und sind während der Entwicklung, des Trainings und der Bereitstellung erforderlich. 

> [!NOTE]
> Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten Python-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

| **Hostname** | **Zweck** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Wird verwendet, um Standardpakete zu installieren |
| **\*.anaconda.org** | Wird verwendet, um Repositorydaten abzurufen |
| **pypi.org** | Wird zum Auflisten von Abhängigkeiten vom Standardindex verwendet, sofern vorhanden, und der Index wird nicht durch Benutzereinstellungen überschrieben. Wenn der Index überschrieben wird, müssen Sie auch **\*.pythonhosted.org** zulassen. |
| **\*pytorch.org** | Wird von einigen Beispielen verwendet, die auf PyTorch basieren. |
| **\*.tensorflow.org** | Wird von einigen Beispielen verwendet, die auf Tensorflow basieren. |

### <a name="r-hosts"></a>R-Hosts

Die Hosts in diesem Abschnitt werden zum Installieren von R-Paketen verwendet und sind während der Entwicklung, des Trainings und der Bereitstellung erforderlich.

> [!NOTE]
> Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten R-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

| **Hostname** | **Zweck** |
| ---- | ---- |
| **cloud.r-project.org** | Beim Installieren von CRAN-Paketen verwendet |

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Service

Wenn Sie Azure Kubernetes Service mit Azure Machine Learning verwenden, muss der folgende Datenverkehr zugelassen werden:

* Allgemeine Anforderungen für eingehenden/ausgehenden Datenverkehr für AKS, wie im Artikel [Einschränken des ausgehenden Datenverkehrs in Azure Kubernetes Service](../aks/limit-egress-traffic.md) beschrieben.
* __Ausgehend__ an mcr.microsoft.com.
* Wenn Sie ein Modell in einem AKS-Cluster bereitstellen, beziehen Sie sich auf den Leitfaden im Artikel [Bereitstellen von ML-Modellen in Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="visual-studio-code-hosts"></a>Visual Studio Code-Hosts

Die Hosts in diesem Abschnitt werden verwendet, um Visual Studio Code-Pakete zu installieren, um eine Remoteverbindung zwischen Visual Studio Code und Compute-Instanzen in Ihrem Azure Machine Learning-Arbeitsbereich einzurichten.

> [!NOTE]
> Diese Liste ist nicht vollständig. Sie enthält nur die Hosts, die für die am häufigsten verwendeten Visual Studio Code-Ressourcen im Internet erforderlich sind. Wenn Sie z. B. Zugriff auf ein GitHub-Repository oder einen anderen Host benötigen, müssen Sie die erforderlichen Hosts für dieses Szenario ermitteln und hinzufügen.

| **Hostname** | **Zweck** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Wird zum Abrufen von VS Code-Serverbits verwendet, die über ein Setupskript in der Compute-Instanz installiert werden.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |Wird zum Abrufen von Websocket-Serverbits verwendet, die in der Compute-Instanz installiert werden. Der Websocket-Server wird verwendet, um Anforderungen des Visual Studio Code-Clients (Desktopanwendung) an den Visual Studio Code-Server zu übertragen, der in der Compute-Instanz ausgeführt wird. |

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

* [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)

Weitere Informationen zum Konfigurieren von Azure Firewall finden Sie unter [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](../firewall/tutorial-firewall-deploy-portal.md).
