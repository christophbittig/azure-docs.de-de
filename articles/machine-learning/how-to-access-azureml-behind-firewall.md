---
title: Konfigurieren von ein- und ausgehendem Netzwerkdatenverkehr
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie den erforderlichen ein- und ausgehenden Netzwerkdatenverkehr konfigurieren, wenn Sie einen sicheren Azure Machine Learning-Arbeitsbereich verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/05/2021
ms.custom: devx-track-python, ignite-fall-2021
ms.openlocfilehash: a2b818aaea5bc737d1b68f9e88dd5c0611c297f1
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135911"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>Konfigurieren von ein- und ausgehendem Netzwerkdatenverkehr

In diesem Artikel erfahren Sie mehr über die Netzwerkkommunikationsanforderungen beim Sichern eines Azure Machine Learning-Arbeitsbereichs in einem virtuellen Netzwerk (VNet). Einschließlich der Konfiguration der Azure Firewall zur Kontrolle des Zugriffs auf Ihren Azure Machine Learning-Arbeitsbereich und das öffentliche Internet. Weitere Informationen zum Schützen von Azure Machine Learning finden Sie unter [Unternehmenssicherheit für Azure Machine Learning](concept-enterprise-security.md).

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

## <a name="well-known-ports"></a>Bekannte Häfen

Nachfolgend finden Sie bekannte Ports, die von den in diesem Artikel aufgeführten Diensten verwendet werden. Wenn in diesem Artikel ein Anschlussbereich verwendet wird, der in diesem Abschnitt nicht aufgeführt ist, ist er spezifisch für den Dienst und es gibt möglicherweise keine veröffentlichten Informationen darüber, wofür er verwendet wird:


| Port | BESCHREIBUNG |
| ----- | ----- | 
| 80 | Ungesicherter Webverkehr (HTTP) |
| 443 | Gesicherter Webverkehr (HTTPS) |
| 445 | SMB-Verkehr für den Zugriff auf Dateifreigaben in Azure File Storage |
| 8787 | Wird verwendet, wenn eine Verbindung zu RStudio auf einer Compute-Instanz hergestellt wird. |

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
    | AzureActiveDirectory | TCP | 80, 443 |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* In Azure China nicht erforderlich. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region ist nur für benutzerdefinierte Docker-Images erforderlich. Einschließlich kleiner Änderungen (z. B. zusätzliche Pakete) an den von Microsoft bereitgestellten Basis-Images.
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

> [!IMPORTANT]
> Azure Machine Learning verwendet Azure Storage Accounts in Ihrem Abonnement und in von Microsoft verwalteten Abonnements. Gegebenenfalls werden in diesem Abschnitt die folgenden Begriffe verwendet, um sie voneinander zu unterscheiden:
>
> * __Ihr Speicher__: Das Azure-Speicherkonto/die Azure-Speicherkonten in Ihrem Abonnement, das zum Speichern Ihrer Daten und Artefakte wie Modelle, Trainingsdaten, Trainingsprotokolle und Python-Skripte verwendet wird.
> * __Microsoft-Speicher__: Die Azure Machine Learning Compute-Instanz und die Compute-Cluster basieren auf Azure Batch und müssen auf Speicher zugreifen, der sich in einem Microsoft-Abonnement befindet. Dieser Speicher wird nur für die Verwaltung der Compute-Instanzen verwendet. Keine Ihrer Daten wird hier gespeichert.

**Allgemeine Azure-Hosts**

# <a name="azure-public"></a>[Azure (öffentlich)](#tab/public)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ---- | 
| Azure Active Directory | login.microsoftonline.com | TCP | 80, 443 |
| Azure-Portal | management.azure.com | TCP | 443 |
| Azure Resource Manager | management.azure.com | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ---- |
| Azure Active Directory | login.microsoftonline.us | TCP | 80, 443 |
| Azure-Portal | management.azure.us | TCP | 443 |
| Azure Resource Manager | management.usgovcloudapi.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.chinacloudapi.cn | TCP | 80, 443 |
| Azure-Portal | management.azure.cn | TCP | 443 |
| Azure Resource Manager | management.chinacloudapi.cn | TCP | 443 |

---

**Azure Machine Learning-Hosts**

> [!IMPORTANT]
> Ersetzen Sie `<storage>` in der folgenden Tabelle durch den Namen des Standardspeicherkontos für Ihren Azure Machine Learning-Arbeitsbereich.

# <a name="azure-public"></a>[Azure (öffentlich)](#tab/public)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | TCP | 443 |
| API |\*.azureml.ms | TCP | 443 |
| Integriertes Notebook | \*.notebooks.azure.net | TCP | 443 |
| Integriertes Notebook | \<storage\>.file.core.windows.net | TCP | 443, 445 |
| Integriertes Notebook | \<storage\>.dfs.core.windows.net | TCP | 443 |
| Integriertes Notebook | \<storage\>.blob.core.windows.net | TCP | 443 |
| Integriertes Notebook | graph.microsoft.com | TCP | 443 |
| Integriertes Notebook | \*.aznbcontent.net | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.us | TCP | 443 |
| API | \*.ml.azure.us | TCP | 443 |
| Integriertes Notebook | \*.notebooks.usgovcloudapi.net | TCP | 443 |
| Integriertes Notebook | \<storage\>.file.core.usgovcloudapi.net | TCP | 443, 445 |
| Integriertes Notebook | \<storage\>.dfs.core.usgovcloudapi.net | TCP | 443 |
| Integriertes Notebook  | \<storage\>.blob.core.usgovcloudapi.net | TCP | 443 |
| Integriertes Notebook | graph.microsoft.us | TCP | 443 |
| Integriertes Notebook | \*.aznbcontent.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | studio.ml.azure.cn | TCP | 443 |
| API | \*.ml.azure.cn | TCP | 443 |
| Integriertes Notebook | \*.notebooks.chinacloudapi.cn | TCP | 443 |
| Integriertes Notebook | \<storage\>.file.core.chinacloudapi.cn | TCP | 443, 445 |
| Integriertes Notebook | \<storage\>.dfs.core.chinacloudapi.cn | TCP | 443 |
| Integriertes Notebook | \<storage\>.blob.core.chinacloudapi.cn | TCP | 443 |
| Integriertes Notebook | graph.chinacloudapi.cn | TCP | 443 |
| Integriertes Notebook | \*.aznbcontent.net | TCP | 443 |

---

**Azure Machine Learning-Compute-Instanz- und -Computeclusterhosts**

> [!TIP]
> * Der Host für __Azure Key Vault__ wird nur benötigt, wenn Ihr Arbeitsbereich mit aktiviertem [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) Flag erstellt wurde.
> * Die Ports 8787 und 18881 für __Recheninstanz__ werden nur benötigt, wenn Ihr Azure Machine-Arbeitsbereich einen privaten Endpunkt hat.
> * Ersetzen Sie `<storage>` in der folgenden Tabelle durch den Namen des Standardspeicherkontos für Ihren Azure Machine Learning-Arbeitsbereich.

# <a name="azure-public"></a>[Azure (öffentlich)](#tab/public)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Computecluster/-Instanz | graph.windows.net | TCP | 443 |
| Compute-Instanz | \*.instances.azureml.net | TCP | 443 |
| Compute-Instanz | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Microsoft Speicherzugriff | \*.blob.core.windows.net | TCP | 443 |
| Microsoft Speicherzugriff | \*.table.core.windows.net | TCP | 443 |
| Microsoft Speicherzugriff | \*.queue.core.windows.net | TCP | 443 |
| Ihr Speicherkonto | \<storage\>.file.core.windows.net | TCP | 443, 445 |
| Ihr Speicherkonto | \<storage\>.blob.core.windows.net | TCP | 443 |
| Azure-Schlüsseltresor | \*.vault.azure.net | TCP | 443 |

# <a name="azure-government"></a>[Azure Government](#tab/gov)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Computecluster/-Instanz | graph.windows.net | TCP | 443 |
| Compute-Instanz | \*.instances.azureml.us | TCP | 443 |
| Compute-Instanz | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Microsoft Speicherzugriff | \*.blob.core.usgovcloudapi.net | TCP | 443 |
| Microsoft Speicherzugriff | \*.table.core.usgovcloudapi.net | TCP | 443 |
| Microsoft Speicherzugriff | \*.queue.core.usgovcloudapi.net | TCP | 443 |
| Ihr Speicherkonto | \<storage\>.file.core.usgovcloudapi.net | TCP | 443, 445 |
| Ihr Speicherkonto | \<storage\>.blob.core.usgovcloudapi.net | TCP | 443 |
| Azure-Schlüsseltresor | \*.vault.usgovcloudapi.net | TCP | 443 |

# <a name="azure-china-21vianet"></a>[Azure China 21Vianet](#tab/china)

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Computecluster/-Instanz | graph.chinacloudapi.cn | TCP | 443 |
| Compute-Instanz |  \*.instances.azureml.cn | TCP | 443 |
| Compute-Instanz | \*.instances.azureml.ms | TCP | 443, 8787, 18881 |
| Microsoft Speicherzugriff | \*blob.core.chinacloudapi.cn | TCP | 443 |
| Microsoft Speicherzugriff | \*.table.core.chinacloudapi.cn | TCP | 443 |
| Microsoft Speicherzugriff | \*.queue.core.chinacloudapi.cn | TCP | 443 |
| Ihr Speicherkonto | \<storage\>.file.core.chinacloudapi.cn | TCP | 443, 445 |
| Ihr Speicherkonto | \<storage\>.blob.core.chinacloudapi.cn | TCP | 443 |
| Azure-Schlüsseltresor | \*.vault.azure.cn | TCP | 443 |

---

**Docker-Images, die von Azure Machine Learning verwaltet werden**

| **Erforderlich für** | **Hosts** | **Protokoll** | **Ports** |
| ----- | ----- | ----- | ----- |
| Microsoft Container Registry | mcr.microsoft.com | TCP | 443 |
| Vordefinierte Azure Machine Learning-Images | viennaglobal.azurecr.io | TCP | 443 |

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

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes mit Azure Arc-Unterstützung <a id="arc-kubernetes"></a>

Kubernetes-Cluster mit Azure Arc-Unterstützung sind von Azure Arc-Verbindungen abhängig. Vergewissern Sie sich, dass Sie die [Azure Arc-Netzwerkanforderungen](/azure/azure-arc/kubernetes/quickstart-connect-cluster?tabs=azure-cli#meet-network-requirements) erfüllen.

Mithilfe der in diesem Abschnitt beschriebenen Hosts werden die Azure Machine Learning-Erweiterung in Kubernetes-Clustern bereitgestellt sowie Trainings- und Rückschlussworkloads an die Cluster übermittelt.

**Bereitstellen der Azure Machine Learning-Erweiterung**

Aktivieren Sie bei der Bereitstellung der Azure Machine Learning-Erweiterung im Cluster den Zugriff auf ausgehenden Datenverkehr zu den folgenden Endpunkten.

| Zielendpunkt| Port | Zweck |
|--|--|--|
|  *.data.mcr.microsoft.com| https:443 | Erforderlich für den auf Azure Content Delivery Network (CDN) basierenden MCR-Speicher. |
| quay.io, *.quay.io | https:443 | Quay.io-Registrierung, erforderlich zum Pullen von Containerimages für Komponenten der AML-Erweiterung. |
| gcr.io| https:443 | Google Cloud-Repository, erforderlich zum Pullen von Containerimages für Komponenten der AML-Erweiterung. |
| storage.googleapis.com | https:443 | Google Cloud Storage, zum Hosten von GCR-Images. |
| registry-1.docker.io, production.cloudflare.docker.com  | https:443 | Docker-Hub-Registry, erforderlich zum Abrufen von Container-Images für AML-Erweiterungskomponenten |
| auth.docker.io| https:443 | Authentifizierung für das Docker-Repository, erforderlich für den Zugriff auf die Docker Hub-Registrierung. |
| *.kusto.windows.net, *.table.core.windows.net, *.queue.core.windows.net | https:443 | Erforderlich zum Hochladen und Analysieren von Systemprotokollen in Kusto. |

**Nur Trainingsworkloads**

Aktivieren Sie den Zugriff auf ausgehenden Datenverkehr zu den folgenden Endpunkten, um Trainingsworkloads an den Cluster zu übermitteln.

| Zielendpunkt| Port | Zweck |
|--|--|--|
| pypi.org | https:443 | Python-Paketindex zum Installieren von PIP-Paketen, die zum Initialisieren der Auftragsumgebung verwendet werden. |
| archive.ubuntu.com, security.ubuntu.com, ppa.launchpad.net | http:80 | Über diese Adresse können die Init-Container die erforderlichen Sicherheitspatches und Updates herunterladen. |

**Trainings- und Rückschlussworkloads**

Aktivieren Sie zusätzlich zu den Endpunkten für Trainingsworkloads den Zugriff auf ausgehenden Datenverkehr zu den folgenden Endpunkten, um Trainings- und Rückschlussworkloads zu übermitteln.

| Zielendpunkt| Port | Zweck |
|--|--|--|
| *.azurecr.io | https:443 | Azure Container Registry, erforderlich zum Pullen von Containerimages zum Hosten von Trainings- oder Rückschlussaufträgen.|
| *.blob.core.windows.net | https:443 | Azure Blob Storage, erforderlich zum Abrufen von Machine Learning-Projektskripts, Containerimages und Auftragsprotokollen/-metriken. |
| *.workspace.\<region\>.api.azureml.ms , \<region\>.experiments.azureml.net, \<region\>.api.azureml.ms | https:443 | Azure machine learning service api, erforderlich für die Kommunikation mit AML |

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
