---
title: Cloudregionsübergreifende Featureverfügbarkeit
titleSuffix: Azure Machine Learning
description: In diesem Artikel sind die Unterschiede bei der Featureverfügbarkeit zwischen der öffentlichen Cloud und Azure Government-, Azure Deutschland- und Azure China 21Vianet-Regionen aufgeführt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 10/21/2021
ms.custom: references_regions
ms.openlocfilehash: 828c093eba73ad1b0042d63ed52b34ba47032acd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558172"
---
# <a name="azure-machine-learning-feature-availability-across-clouds-regions"></a>Verfügbarkeit von Azure Machine Learning-Features über Cloudregionen hinweg

Hier erfahren Sie, welche Azure Machine Learning-Features in den Regionen „Azure Government“, „Azure Deutschland“ und „Azure China 21Vianet“ verfügbar sind. 

Die Liste mit den globalen Azure-Regionen enthält neben den öffentlichen Cloudregionen mehrere Regionen für bestimmte Märkte. Hierzu zählen beispielsweise die Regionen „Azure Government“ und „Azure China 21Vianet“. Azure Machine Learning wird sowohl in den öffentlichen Cloudregionen als auch in den folgenden Regionen bereitgestellt:

* Azure Government-Regionen **US-Arizona** und **US-Virginia**.
* Azure China 21Vianet-Region **China-East-2**.

Die Informationen im übrigen Teil dieses Dokuments enthalten Informationen dazu, welche Features von Azure Machine Learning in diesen Regionen verfügbar sind, sowie regionsspezifische Informationen zur Verwendung dieser Features.
## <a name="azure-government"></a>Azure Government 

| Funktion | Status der öffentlichen Cloud  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **[Automatisiertes maschinelles Lernen](concept-automated-ml.md)** | | | |
| Erstellen und Ausführen von Experimenten in Notebooks                                    | Allgemein verfügbar                   | YES                | YES         |
| Erstellen und Ausführen von Experimenten über die Studioweboberfläche                        | Öffentliche Vorschau       | YES                | YES         |
| Branchenführende Vorhersagefunktionen                                  | Allgemein verfügbar                   | YES                | YES         |
| Unterstützung für Deep Learning und andere erweiterte Lernmoduloptionen                      | Allgemein verfügbar                   | YES                | YES         |
| Unterstützung umfangreicher Daten (bis zu 100 GB)                                          | Öffentliche Vorschau       | YES                | YES         |
| Azure Databricks-Integration                                              | Allgemein verfügbar                   | Nein                 | Nein          |
| SQL-, Cosmos DB- und HDInsight-Integrationen                                   | Allgemein verfügbar                   | YES                | YES         |
| **[Machine Learning-Pipelines](concept-ml-pipelines.md)** |   |  | | 
| Erstellen, Ausführen und Veröffentlichen von Pipelines mit dem Azure ML SDK                   | Allgemein verfügbar                   | YES                | YES         |
| Erstellen von Pipelineendpunkten mit dem Azure ML SDK                           | Allgemein verfügbar                   | YES                | YES         |
| Erstellen, Bearbeiten und Löschen geplanter Ausführungen von Pipelines mit dem Azure ML SDK | Allgemein verfügbar                   | JA*               | JA*        |
| Anzeigen von Details zur Pipelineausführung in Studio                                        | Allgemein verfügbar                   | YES                | YES         |
| Erstellen, Ausführen, Visualisieren und Veröffentlichen von Pipelines in Azure ML Designer          | Allgemein verfügbar      | YES                | YES         |
| Azure Databricks-Integration mit ML-Pipeline                             | Allgemein verfügbar                   | Nein                 | Nein          |
| Erstellen von Pipelineendpunkten in Azure ML Designer                             | Allgemein verfügbar      | YES                | YES         |
| **[Integrierte Notebooks](how-to-run-jupyter-notebooks.md)** |   |  | | 
| Arbeitsbereichnotebook und Dateifreigabe                                        | Allgemein verfügbar                   | YES                | YES         |
| R- und Python-Unterstützung                                                       | Allgemein verfügbar                   | YES                | YES         |
| Unterstützung von Virtual Network                                                    | Öffentliche Vorschau       | Nein                 | Nein          |
| **[Compute-Instanz](concept-compute-instance.md)** |   |  | | 
| Verwaltete Compute-Instanzen für integrierte Notebooks                         | Allgemein verfügbar                   | YES                | YES         |
| Jupyter, JupyterLab-Integration                                            | Allgemein verfügbar                   | YES                | YES         |
| Unterstützung von virtuellen Netzwerken (VNETs)                                             | Öffentliche Vorschau       | YES                | YES         |
| **SDK-Unterstützung** |  |  | | 
| [Python SDK-Unterstützung](/python/api/overview/azure/ml/)                                                         | Allgemein verfügbar                   | YES                | YES         |
| **[Security](concept-enterprise-security.md)** |   | | | 
| VNET-Unterstützung (virtuelles Netzwerk) zu Trainingszwecken                                | Allgemein verfügbar                   | YES                | YES         |
| VNET-Unterstützung (virtuelles Netzwerk) für Rückschlüsse                               | Allgemein verfügbar                   | YES                | YES         |
| Bewerten der Endpunktauthentifizierung                                            | Öffentliche Vorschau       | YES                | YES         |
| Privater Endpunkt am Arbeitsplatz                                                 | Allgemein verfügbar  |  Allgemein verfügbar  |  Allgemein verfügbar |
| ACI hinter VNET                                                            | Öffentliche Vorschau       | Nein                 | Nein          |
| ACR hinter VNET                                                            | Allgemein verfügbar       | YES                 | YES          |
| Private IP-Adresse des AKS-Clusters                                                  | Öffentliche Vorschau       | Nein                 | Nein          |
| **Compute** |   | | |
| [Arbeitsbereichsübergreifende Kontingentverwaltung](how-to-manage-quotas.md)                                         | Allgemein verfügbar                   | YES                | YES         |
| **[Daten für maschinelles Lernen](concept-data.md)** |   | | |
| Erstellen, Anzeigen oder Bearbeiten von Datasets und Datenspeichern über das SDK                  | Allgemein verfügbar                   | YES                | YES         |
| Erstellen, Anzeigen oder Bearbeiten von Datasets und Datenspeichern über die Benutzeroberfläche                   | Allgemein verfügbar                   | YES                | YES         |
| Anzeigen, Bearbeiten oder Löschen von Datendriftüberwachungen über das SDK                   | Öffentliche Vorschau       | YES                | YES         |
| Anzeigen, Bearbeiten oder Löschen von Datendriftüberwachungen über die Benutzeroberfläche                    | Öffentliche Vorschau       | YES                | YES         |
| **Machine Learning-Lebenszyklus** |   | | |
| [Erstellung von Modellprofilen](how-to-deploy-profile-model.md)                                                            | Allgemein verfügbar                   | YES                | PARTIAL     |
| [Azure ML CLI 1.0](reference-azure-machine-learning-cli.md)     | Allgemein verfügbar                   | YES                | YES         |
| [FPGA-basierte hardwarebeschleunigte Modelle](how-to-deploy-fpga-web-service.md)                                     | Allgemein verfügbar                   | Nein                 | Nein          |
| [Visual Studio Code-Integration](how-to-setup-vs-code.md)                                             | Öffentliche Vorschau       | Nein                 | Nein          |
| [Event Grid-Integration](how-to-use-event-grid.md)                                                     | Öffentliche Vorschau       | Nein                 | Nein          |
| [Integrieren von Azure Stream Analytics in Azure Machine Learning](../stream-analytics/machine-learning-udf.md)               | Öffentliche Vorschau       | Nein                 | Nein          |
| **Beschriften von [Bildern](how-to-create-image-labeling-projects.md) und [Text](how-to-create-text-labeling-projects.md)** |   | | |
| Verwaltungsportal für Beschriftungsprojekte                                        | Allgemein verfügbar                   | YES                | YES         |
| Portal für Beschriftungsersteller                                                            | Allgemein verfügbar                   | YES                | YES         |
| Beschriften mithilfe privater Mitarbeiter                                          | Allgemein verfügbar                   | YES                | YES         |
| ML-gestützte Beschriftung (Bildklassifizierung und Objekterkennung)           | Öffentliche Vorschau       | YES                | YES         |
| **[Verantwortungsvolles maschinelles Lernen](concept-responsible-ml.md)** |   | | |
| Erklärbarkeit auf der Benutzeroberfläche                                                       | Öffentliche Vorschau       | Nein                 | Nein          |
| Differenzieller Datenschutz: SmartNoise-Toolkit                                    | OSS                  | Nein                 | Nein          |
| Benutzerdefinierte Tags in Azure Machine Learning zur Implementierung von Datenblättern              | Allgemein verfügbar                   | Nein                 | Nein          |
| Fairness bei der AzureML-Integration                                               | Öffentliche Vorschau       | Nein                 | Nein          |
| Interpretability SDK                                                      | Allgemein verfügbar                   | YES                | YES         |
| **Training** |   | | |
| [Protokollstreaming für Experimente](how-to-track-monitor-analyze-runs.md)                                              | Allgemein verfügbar                   | YES                | YES         |
| [Vertiefendes Lernen](how-to-use-reinforcement-learning.md)                                                     | Öffentliche Vorschau       | Nein                 | Nein          |
| [Benutzeroberfläche zum Experimentieren](how-to-track-monitor-analyze-runs.md)                                                         | Öffentliche Vorschau                   | YES                | YES         |
| [.NET-Integration ML.NET 1.0](/dotnet/machine-learning/tutorials/object-detection-model-builder)                                                | Allgemein verfügbar                   | YES                | YES         |
| **Rückschluss** |   | | |
| [Batchrückschluss](tutorial-pipeline-batch-scoring-classification.md)                                                          | Allgemein verfügbar                   | YES                | YES         |
| [Data Box Edge mit FPGA](how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)                                                    | Öffentliche Vorschau       | Nein                 | Nein          |
| **Andere** |   | | |
| [Open Datasets](../open-datasets/samples.md)                                                              | Öffentliche Vorschau       | YES                | YES         |
| [Benutzerdefinierte Cognitive Search-Instanz](how-to-deploy-model-cognitive-search.md)                                                    | Öffentliche Vorschau       | YES                | YES         |


### <a name="azure-government-scenarios"></a>Azure Government-Szenarien

| Szenario                                                    | US-Virginia | US-Arizona| Einschränkungen  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Allgemeines Sicherheits-Setup** |   | | |
| Deaktivieren/Steuern des Internetzugriffs (eingehend und ausgehend) und bestimmtes VNET | PARTIAL| PARTIAL   |  | 
| Platzierung für alle zugeordneten Ressourcen/Dienste  | YES | YES |  |
| Verschlüsselung von ruhenden Daten und während der Übertragung                                                 | YES | YES |  |
| Root- und SSH-Zugriff auf Computeressourcen                                          | YES | YES |  |
| Gewährleisten der Sicherheit bereitgestellter Systeme (Instanzen, Endpunkte usw.) – einschließlich Endpunktschutz, Patches und Protokollierung |  PARTIAL|  PARTIAL |ACI hinter VNet derzeit nicht verfügbar |                                  
| Steuern (Deaktivieren/Einschränken/Beschränken) der Nutzung der ACI-/AKS-Integration                    | PARTIAL| PARTIAL |ACI hinter VNet derzeit nicht verfügbar|
| Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC): Erstellung benutzerdefinierter Rollen                           | YES | YES |  |
| Steuern des Zugriffs auf die vom ML-Dienst verwendeten ACR-Images (von Azure bereitgestellt/verwaltet versus benutzerdefiniert)  |PARTIAL|  PARTIAL |  |
| **Allgemeine Verwendung des Machine Learning-Diensts** |  | | |
| Möglichkeit zur Verwendung einer Entwicklungsumgebung, um ein Modell zu erstellen, zu trainieren, als Endpunkt zu hosten und über eine Web-App zu nutzen     | YES | YES |  |
| Möglichkeit zum Pullen von Daten aus ADLS (Data Lake Storage)                                 |YES | YES |  |
| Möglichkeit zum Pullen von Daten aus Azure Blob Storage                                       |YES | YES |  |



### <a name="other-azure-government-limitations"></a>Weitere Einschränkungen von Azure Government

* Für Azure Machine Learning-Compute-Instanzen können in Azure Government keine Token mit einer Gültigkeitsdauer von mehr 24 Stunden aktualisiert werden.
* Von der Modellprofilerstellung werden in der Region „US-Arizona“ keine vier CPUs unterstützt.   
* Beispielnotebooks funktionieren in Azure Government möglicherweise nicht, wenn sie Zugriff auf öffentliche Daten benötigen.
* IP-Adressen: Der CLI-Befehl, der in den Anweisungen für den [erforderlichen öffentlichen Internetzugriff](how-to-secure-training-vnet.md#required-public-internet-access) verwendet wurde, gibt keine IP-Adressbereiche zurück. Verwenden Sie stattdessen die [Azure-IP-Adressbereiche und -Diensttags für Azure Government](https://www.microsoft.com/download/details.aspx?id=57063).
* Für geplante Pipelines steht auch ein blobbasierter Triggermechanismus zur Verfügung. Dieser Mechanismus wird für CMK-Arbeitsbereiche nicht unterstützt. Wenn Sie einen blobbasierten Trigger für CMK-Arbeitsbereiche aktivieren möchten, sind zusätzliche Setupschritte erforderlich. Weitere Informationen finden Sie unter [Auslösen der Ausführung einer Machine Learning-Pipeline aus einer Logik-App](how-to-trigger-published-pipeline.md).
* Firewalls: Fügen Sie Ihrer Firewalleinstellung bei Verwendung einer Azure Government-Region die folgenden Hosts hinzu:

    * Arizona: `usgovarizona.api.ml.azure.us`
    * Virginia: `usgovvirginia.api.ml.azure.us`
    * Beide: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China (21Vianet) 

| Funktion                                       | Status der öffentlichen Cloud | CH-East-2 | CH-North-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Automatisiertes maschinelles Lernen** |    | | |
| Erstellen und Ausführen von Experimenten in Notebooks                                    | Allgemein verfügbar               | YES       | –        |
| Erstellen und Ausführen von Experimenten über die Studioweboberfläche                        | Öffentliche Vorschau   | YES       | –        |
| Branchenführende Vorhersagefunktionen                                  | Allgemein verfügbar               | YES       | –        |
| Unterstützung für Deep Learning und andere erweiterte Lernmoduloptionen                      | Allgemein verfügbar               | YES       | –        |
| Unterstützung umfangreicher Daten (bis zu 100 GB)                                          | Öffentliche Vorschau   | YES       | –        |
| Azure Databricks-Integration                                              | Allgemein verfügbar               | Nein        | –        |
| SQL-, Cosmos DB- und HDInsight-Integrationen                                   | Allgemein verfügbar               | YES       | –        |
| **Machine Learning-Pipelines** |    | | |
| Erstellen, Ausführen und Veröffentlichen von Pipelines mit dem Azure ML SDK                   | Allgemein verfügbar               | YES       | –        |
| Erstellen von Pipelineendpunkten mit dem Azure ML SDK                           | Allgemein verfügbar               | YES       | –        |
| Erstellen, Bearbeiten und Löschen geplanter Ausführungen von Pipelines mit dem Azure ML SDK | Allgemein verfügbar               | YES       | –        |
| Anzeigen von Details zur Pipelineausführung in Studio                                        | Allgemein verfügbar               | YES       | –        |
| Erstellen, Ausführen, Visualisieren und Veröffentlichen von Pipelines in Azure ML Designer          | Allgemein verfügbar  | YES       | –        |
| Azure Databricks-Integration mit ML-Pipeline                             | Allgemein verfügbar               | Nein        | –        |
| Erstellen von Pipelineendpunkten in Azure ML Designer                             | Allgemein verfügbar   | YES       | –        |
| **Integrierte Notebooks** |   | | |
| Arbeitsbereichnotebook und Dateifreigabe                                        | Allgemein verfügbar               | YES       | –        |
| R- und Python-Unterstützung                                                       | Allgemein verfügbar               | YES       | –        |
| Unterstützung von Virtual Network                                                    | Öffentliche Vorschau   | Nein        | –        |
| **Compute-Instanz** |    | | |
| Verwaltete Compute-Instanzen für integrierte Notebooks                         | Allgemein verfügbar               | YES        | –        |
| Jupyter, JupyterLab-Integration                                            | Allgemein verfügbar               | YES       | –        |
| Unterstützung von virtuellen Netzwerken (VNETs)                                             | Öffentliche Vorschau   | YES       | –        |
| **SDK-Unterstützung** |    | | |
| Python SDK-Unterstützung                                                         | Allgemein verfügbar               | YES       | –        |
| **Security** |   | | |
| VNET-Unterstützung (virtuelles Netzwerk) zu Trainingszwecken                                | Allgemein verfügbar               | YES       | –        |
| VNET-Unterstützung (virtuelles Netzwerk) für Rückschlüsse                               | Allgemein verfügbar               | YES       | –        |
| Bewerten der Endpunktauthentifizierung                                            | Öffentliche Vorschau   | YES       | –        |
| Privater Endpunkt am Arbeitsplatz                                                 | Allgemein verfügbar               | Nein        | –        |
| ACI hinter VNET                                                            | Öffentliche Vorschau   | Nein        | –        |
| ACR hinter VNET                                                            | Allgemein verfügbar   | YES       | –        |
| Private IP-Adresse des AKS-Clusters                                                  | Öffentliche Vorschau   | Nein        | –        |
| **Compute** |   | | |
| Arbeitsbereichsübergreifende Kontingentverwaltung                                         | Allgemein verfügbar               | YES       | –        |
| **Daten für maschinelles Lernen** | | | |
| Erstellen, Anzeigen oder Bearbeiten von Datasets und Datenspeichern über das SDK                  | Allgemein verfügbar               | YES       | –        |
| Erstellen, Anzeigen oder Bearbeiten von Datasets und Datenspeichern über die Benutzeroberfläche                   | Allgemein verfügbar               | YES       | –        |
| Anzeigen, Bearbeiten oder Löschen von Datendriftüberwachungen über das SDK                   | Öffentliche Vorschau   | YES       | –        |
| Anzeigen, Bearbeiten oder Löschen von Datendriftüberwachungen über die Benutzeroberfläche                    | Öffentliche Vorschau   | YES       | –        |
| **Machine Learning-Lebenszyklus** |    | | |
| Erstellung von Modellprofilen                                                            | Allgemein verfügbar               | PARTIAL   | –        |
| Die Azure DevOps-Erweiterung für Machine Learning und die Azure ML CLI         | Allgemein verfügbar               | YES       | –        |
| FPGA-basierte hardwarebeschleunigte Modelle                                     | Allgemein verfügbar               | Nein        | –        |
| Visual Studio Code-Integration                                             | Öffentliche Vorschau   | Nein        | –        |
| Event Grid-Integration                                                     | Öffentliche Vorschau   | YES       | –        |
| Integrieren von Azure Stream Analytics mit Azure Machine Learning               | Öffentliche Vorschau   | Nein        | –        |
| **Bezeichnung** |    | | |
| Verwaltungsportal für Beschriftungsprojekte                                        | Allgemein verfügbar               | YES       | –        |
| Portal für Beschriftungsersteller                                                            | Allgemein verfügbar               | YES       | –        |
| Beschriften mithilfe privater Mitarbeiter                                          | Allgemein verfügbar               | YES       | –        |
| ML-gestützte Beschriftung (Bildklassifizierung und Objekterkennung)           | Öffentliche Vorschau   | YES       | –        |
| **Verantwortungsvolles maschinelles Lernen** |    | | |
| Erklärbarkeit auf der Benutzeroberfläche                                                       | Öffentliche Vorschau   | Nein        | –        |
| Differenzieller Datenschutz: SmartNoise-Toolkit                                    | OSS              | Nein        | –        |
| Benutzerdefinierte Tags in Azure Machine Learning zur Implementierung von Datenblättern              | Allgemein verfügbar               | Nein        | –        |
| Fairness bei der AzureML-Integration                                               | Öffentliche Vorschau   | Nein        | –        |
| Interpretability SDK                                                      | Allgemein verfügbar               | YES       | –        |
| **Training** |    | | |
| Protokollstreaming für Experimente                                              | Allgemein verfügbar               | YES       | –        |
| Vertiefendes Lernen                                                     | Öffentliche Vorschau   | Nein        | –        |
| Benutzeroberfläche zum Experimentieren                                                         | Allgemein verfügbar               | YES       | –        |
| .NET-Integration ML.NET 1.0                                                | Allgemein verfügbar               | YES       | –        |
| **Rückschluss** |   | | |
| Batchrückschluss                                                          | Allgemein verfügbar               | YES       | –        |
| Data Box Edge mit FPGA                                                    | Öffentliche Vorschau   | Nein        | –        |
| **Andere** |    | | |
| Open Datasets                                                              | Öffentliche Vorschau   | YES       | –        |
| Benutzerdefinierte Cognitive Search-Instanz                                                    | Öffentliche Vorschau   | YES       | –        |



### <a name="other-azure-china-limitations"></a>Weitere Einschränkungen für Azure China

* Azure China verfügt über eingeschränkte VM-SKUs. Das gilt insbesondere für die GPU-SKUs. Es steht nur die NCv3-Familie (V100) zur Verfügung.
* Rest-API-Endpunkte unterscheiden sich von globalen Azure-Umgebungen. Verwenden Sie die folgende Tabelle, um den REST-API-Endpunkt für Azure China-Regionen zu finden:

    | REST-Endpunkt                 | Globale Azure-Umgebung                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Verwaltungsebene | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Datenebene       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Beispielnotebooks funktionieren möglicherweise nicht, wenn sie Zugriff auf öffentliche Daten benötigen.
* IP-Adressbereiche: Der CLI-Befehl, der in den Anweisungen für den [erforderlichen öffentlichen Internetzugriff](how-to-secure-training-vnet.md#required-public-internet-access) verwendet wurde, gibt keine IP-Adressbereiche zurück. Verwenden Sie stattdessen die [Azure-IP-Adressbereiche und -Diensttags für Azure China](https://www.microsoft.com//download/details.aspx?id=57062).
* Die Vorschauversion von Azure Machine Learning-Compute-Instanzen wird in Arbeitsbereichen, in denen private Endpunkte aktiviert sind, vorerst nicht unterstützt. Compute-Instanzen werden jedoch in der nächsten Bereitstellung für die Diensterweiterung auf alle AML-Regionen unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Regionen, in denen Azure Machine Learning verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).