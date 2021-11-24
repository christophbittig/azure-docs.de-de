---
title: Was sind Endpunkte (Vorschau)?
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie Azure Machine Learning-Endpunkte (Vorschau) verwenden, um Machine Learning-Bereitstellungen zu vereinfachen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2, ignite-fall-2021
ms.date: 10/21/2021
ms.openlocfilehash: 7129286a0a91e27e80ebe4a6f3fc99e2b0e6c9b8
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135937"
---
# <a name="what-are-azure-machine-learning-endpoints-preview"></a>Was sind Azure Machine Learning-Endpunkte (Vorschau)? 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Verwenden Sie Azure Machine Learning-Endpunkte (Vorschau), um Modellbereitstellungen für die Bereitstellung von Echtzeit- und Batchrückschlüssen zu optimieren. Endpunkte verfügen über eine einheitliche Schnittstelle zum übergreifenden Aufrufen und Verwalten von Modellbereitstellungen für alle Computetypen.

In diesem Artikel lernen Sie Folgendes:
> [!div class="checklist"]
> * Endpunkte
> * Bereitstellungen
> * Verwaltete Onlineendpunkte
> * Kubernetes-Onlineendpunkte
> * Batchrückschluss-Endpunkte

## <a name="what-are-endpoints-and-deployments-preview"></a>Was sind Endpunkte und Bereitstellungen (Vorschau)?

Nachdem Sie ein Modell für maschinelles Lernen trainiert haben, müssen Sie das Modell bereitstellen, damit andere es zum Rückschließen verwenden können. In Azure Machine Learning können Sie hierfür **Endpunkte** (Vorschau) und **Bereitstellungen** (Vorschau) verwenden.

Ein **Endpunkt** ist ein HTTPS-Endpunkt, der von Clients aufgerufen werden kann, um die Rückschlussausgabe (Bewertung) eines trainierten Modells zu empfangen. Sie bietet: 
- Authentifizierung mit "Schlüssel & Token"-basierter Authentifizierung 
- SSL-Terminierung 
- Stabilen Bewertungs-URI (endpoint-name.region.inference.ml.azure.com)


Eine **Einrichtung** ist ein Satz von Ressourcen, die für das Hosting des Modells erforderlich sind, das die eigentliche Inferenz durchführt. 

Ein einzelner Endpunkt kann mehrere Bereitstellungen enthalten. Endpunkte und Bereitstellungen sind unabhängige Azure Resource Manager-Ressourcen, die im Azure-Portal angezeigt werden.

Das Konzept der Endpunkte und Bereitstellungen wird von Azure Machine Learning genutzt, um unterschiedliche Arten von Endpunkten zu implementieren: [Onlineendpunkte](#what-are-online-endpoints-preview) und [Batchendpunkte](#what-are-batch-endpoints-preview).

### <a name="multiple-developer-interfaces"></a>Mehrere Entwicklerschnittstellen

Erstellen und Verwalten von Batch- und Onlineendpunkten mit verschiedenen Entwicklertools:
- Die Azure CLI
- Azure-Resource Manager/REST-API
- Azure Machine Learning Studio-Webportal
- Azure-Portal (IT/Administrator)
- Unterstützung für CI/CD-MLOps-Pipelines über die Azure CLI- und REST-/ARM-Schnittstelle

## <a name="what-are-online-endpoints-preview"></a>Was sind Onlineendpunkte (Vorschau)?

**Onlineendpunkte** (Vorschau) sind Endpunkte, die für Onlinerückschlüsse (Echtzeit) verwendet werden. Gegenüber **Batchendpunkten** enthalten **Onlineendpunkte** **Bereitstellungen**, die bereit für den Empfang der Daten von Clients sind und in Echtzeit Rückantworten senden können.

Das folgende Diagramm zeigt einen Online-Endpunkt, der zwei Bereitstellungen hat, "blau" und "grün". Die blaue Bereitstellung verwendet VMs mit einer CPU-SKU und führt v1 eines Modells aus. Die grüne Bereitstellung verwendet VMs mit einer GPU-SKU und verwendet v2 des Modells. Der Endpunkt ist so konfiguriert, dass er 90 % des eingehenden Datenverkehrs an die blaue Bereitstellung weiterleitet, während die verbleibenden 10 % auf die grüne Bereitstellung entfallen.

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="Diagramm: Endpunkt, bei dem Datenverkehr auf zwei Bereitstellungen aufgeteilt wird":::

### <a name="online-endpoints-requirements"></a>Anforderungen an Onlineendpunkte

Um einen Online-Endpunkt zu erstellen, müssen Sie die folgenden Elemente angeben:
- Modelldateien (oder Angeben eines registrierten Modells in Ihrem Arbeitsbereich) 
- Bewertungsskript - für die Bewertung/Inferencing benötigter Code
- Umgebung: Docker-Image mit Conda-Abhängigkeiten oder ein Dockerfile 
- Einstellungen für Compute-Instanzen und Skalierungen 

Hier wird beschrieben, wie Sie Onlineendpunkte über die [CLI](how-to-deploy-managed-online-endpoints.md) und das [Studio-Webportal](how-to-use-managed-online-endpoint-studio.md) bereitstellen.

### <a name="test-and-deploy-locally-for-faster-debugging"></a>Lokales Testen und Bereitstellen zur Beschleunigung des Debuggens

Führen Sie die lokale Bereitstellung durch, um Ihre Endpunkte ohne Bereitstellung in der Cloud zu testen. Von Azure Machine Learning wird ein lokales Docker-Image erstellt, mit dem das Azure ML-Image imitiert wird. Azure Machine Learning führt die Erstellungs- und Bereitstellungsvorgänge für Sie lokal durch und speichert das Image zwischen, um schnelle Iterationen zu ermöglichen.

### <a name="native-bluegreen-deployment"></a>Native Blau-Grün-Bereitstellung 

Beachten Sie den bereits erwähnten Hinweis, dass ein einzelner Endpunkt mehrere Bereitstellungen enthalten kann. Der Online-Endpunkt kann einen Lastausgleich vornehmen, um einen beliebigen Prozentsatz des Datenverkehrs an jede Einrichtung zu verteilen.

Die Verkehrsaufteilung kann verwendet werden, um sichere Blue/Green-Rollouts durchzuführen, indem Anfragen zwischen verschiedenen Instanzen ausgeglichen werden.

> [!TIP]
> Eine Anforderung kann den konfigurierten Lastausgleich umgehen, indem sie einen HTTP-Header von `azureml-model-deployment` enthält. Setzen Sie den Wert der Kopfzeile auf den Namen der Einrichtung, an die die Anforderung weitergeleitet werden soll.

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="Screenshot: Schieberegler zum Festlegen der Datenverkehrszuordnung für mehrere Bereitstellungen":::

Informieren Sie sich, wie Sie einen [sicheren Rollout für Onlineendpunkte](how-to-safely-rollout-managed-endpoints.md) durchführen.

### <a name="application-insights-integration"></a>Application Insights-Integration

Alle Onlineendpunkte sind mit Application Insights integriert, um SLAs zu überwachen und Probleme zu diagnostizieren. 

[Verwaltete Onlineendpunkte](#managed-online-endpoints-vs-kubernetes-online-endpoints-preview) verfügen aber auch über eine standardmäßige Integration mit Azure-Protokollen und -Metriken.

### <a name="security"></a>Sicherheit

- Authentifizierung: Schlüssel und Azure ML-Token
- Verwaltete Identität: Benutzer- und systemseitig zugewiesen (nur verwalteter Onlineendpunkt)
- Standardmäßiges SSL für Aufruf von Endpunkten

### <a name="autoscaling"></a>Automatische Skalierung

Autoscale führt automatisch die richtige Menge an Ressourcen aus, um die Last Ihrer Anwendung zu bewältigen. Verwaltete Endpunkte unterstützen Autoscaling durch die Integration mit der Funktion [Azure Monitor Autoscale](/azure/azure-monitor/autoscale/autoscale-overview.md). Sie können metrikbasierte Skalierung (z. B. CPU-Auslastung >70 %), zeitplanbasierte Skalierung (z. B. Skalierungsregeln für Hauptgeschäftszeiten) oder eine Kombination davon konfigurieren.

:::image type="content" source="media/concept-endpoints/concept-autoscale.png" alt-text="Screenshot, der zeigt, dass Autoscale flexibel zwischen min und max Instanzen, abhängig von den Regeln":::

### <a name="visual-studio-code-debugging"></a>Visual Studio Code-Debugging

Visual Studio Code ermöglicht es Ihnen, Endpunkte interaktiv zu debuggen.

:::image type="content" source="media/concept-endpoints/visual-studio-code-full.png" alt-text="Screenshot des Endpunkt-Debugging in VSCode." lightbox="media/concept-endpoints/visual-studio-code-full.png" :::

## <a name="managed-online-endpoints-vs-kubernetes-online-endpoints-preview"></a>Verwaltete Onlineendpunkte und Kubernetes-Onlineendpunkte (Vorschau)

Es gibt zwei Arten von Onlineendpunkten: **Verwaltete Onlineendpunkte** (Vorschau) und **Kubernetes-Onlineendpunkte** (Vorschau). In der folgenden Tabelle sind einige der wichtigsten Unterschiede aufgeführt.

|  | Verwaltete Onlineendpunkte | Kubernetes-Onlineendpunkte |
|-|-|-|
| **Empfohlene Benutzer** | Benutzer, die eine Bereitstellung eines verwalteten Modells und eine verbesserte MLOps-Benutzeroberfläche benötigen | Benutzer, die Kubernetes bevorzugen und Infrastrukturanforderungen selbst verwalten können |
| **Infrastrukturverwaltung** | Verwaltete Bereitstellung von Computeressourcen, Skalierung, Updates für Hostbetriebssystem-Images und Sicherheitshärtung | Benutzerverantwortung |
| **Computetyp** | Verwaltet (AmlCompute) | Kubernetes-Cluster (Kubernetes) |
| **Standardmäßige Überwachung** | [Azure-Überwachung](how-to-monitor-online-endpoints.md) <br> (Umfasst wichtige Metriken, z. B. Latenz und Durchsatz.) | Nicht unterstützt |
| **Standardmäßige Protokollierung** | [Azure-Protokolle und Log Analytics auf Endpunktebene](how-to-deploy-managed-online-endpoints.md#optional-integrate-with-log-analytics) | Unterstützt |
| **Application Insights** | Unterstützt | Unterstützt |
| **Verwaltete Identität** | [Unterstützt](how-to-access-resources-from-endpoints-managed-identities.md) | Unterstützt |
| **Virtuelles Netzwerk (VNET)** | Noch nicht unterstützt (wir arbeiten daran) | Unterstützt |
| **Anzeigen von Kosten** | [Endpunkt- und Bereitstellungsebene](how-to-view-online-endpoints-costs.md) | Clusterebene |

### <a name="managed-online-endpoints"></a>Verwaltete Onlineendpunkte

Verwaltete Onlineendpunkte können zur Optimierung Ihres Bereitstellungsprozesses beitragen. Verwaltete Onlineendpunkte haben gegenüber Kubernetes-Onlineendpunkten die folgenden Vorteile:

- Verwaltete Infrastruktur
    - Automatische Bereitstellung der Computeressourcen und Hosting des Modells (Sie müssen nur VM-Typ und Skalierungseinstellungen angeben) 
    - Automatische Updates und Patches für das zugrunde liegende Host-Betriebssystem-Image
    - Automatische Wiederherstellung von Knoten bei einem Systemausfall

- Überwachung und Protokolle
    - Überwachen Sie die Modellverfügbarkeit, Leistung und SLA über die [native Integration mit Azure Monitor](how-to-monitor-online-endpoints.md).
    - Debuggen Sie Bereitstellungen, indem Sie die Protokolle und die native Integration mit Azure Log Analytics verwenden.

    :::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="Screenshot: Azure Monitor-Graph zur Endpunktlatenz":::

- Verwaltete Identität
    -  Verwenden Sie [verwaltete Identitäten zum Zugreifen auf geschützte Ressourcen über das Bewertungsskript](tutorial-deploy-managed-endpoints-using-system-managed-identity.md).

- Anzeigen von Kosten 
    - Mit verwalteten Onlineendpunkten können Sie die [Kosten auf Endpunkt- und Bereitstellungsebene überwachen](how-to-view-online-endpoints-costs.md).
    
    :::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="Screenshot: Kostendiagramm für einen Endpunkt und eine Bereitstellung":::

Ein Tutorial mit einer Schritt-für-Schritt-Anleitung finden Sie unter [Bereitstellen verwalteter Onlineendpunkte](how-to-deploy-managed-online-endpoints.md).

## <a name="what-are-batch-endpoints-preview"></a>Was sind Batchendpunkte (Vorschau)?

**Batch-Endpunkte** (Vorschau) sind Endpunkte, die für die Batch-Inferenzierung großer Datenmengen über einen bestimmten Zeitraum hinweg verwendet werden.  Mit **Batchendpunkten** werden Zeiger auf Daten empfangen und Aufträge asynchron ausgeführt, um die Daten in Computeclustern parallel zu verarbeiten. Auf Batchendpunkten werden Ausgaben zur weiteren Analyse in einem Datenspeicher gespeichert.

:::image type="content" source="media/concept-endpoints/batch-endpoint.png" alt-text="Das Diagramm zeigt, dass ein einzelner Batch-Endpunkt Anfragen an mehrere Bereitstellungen weiterleiten kann, von denen eine die Standardbereitstellung ist.":::

### <a name="batch-deployment-requirements"></a>Anforderungen für die Batch-Bereitstellung

Um eine Batch-Bereitstellung zu erstellen, müssen Sie die folgenden Elemente angeben:

- Modelldateien (oder geben Sie ein in Ihrem Arbeitsbereich registriertes Modell an)
- Compute
- Bewertungsskript - Code, der für die Bewertung/Inferenzierung benötigt wird
- Umgebung: Docker-Image mit Conda-Abhängigkeiten

Wenn Sie [MLFlow-Modelle](how-to-use-mlflow.md) einsetzen, brauchen Sie kein Scoring-Skript und keine Ausführungsumgebung bereitzustellen, da beides automatisch generiert wird.

Lernen Sie, wie man [Batch-Endpunkte mit der Azure CLI](how-to-use-batch-endpoint.md) und dem [Studio-Webportal](how-to-use-batch-endpoints-studio.md) einrichtet und verwendet

### <a name="managed-cost-with-autoscaling-compute"></a>Verwaltete Kosten mit automatischer Skalierung von Computeressourcen

Beim Aufrufen eines Batchendpunkts wird ein asynchroner Batchrückschlussauftrag ausgelöst. Computeressourcen werden automatisch bereitgestellt, wenn der Auftrag gestartet wird, und nach Abschluss des Auftrags wird die Bereitstellung dann automatisch wieder aufgehoben. Sie zahlen also nur für Computeressourcen, wenn Sie diese auch nutzen.

Sie können [die Einstellungen der Rechenressourcen](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings) (z. B. Anzahl der Instanzen) und erweiterte Einstellungen (z. B. Mini-Batch-Größe, Fehlerschwelle usw.) für jeden einzelnen Batch-Inferenzauftrag ändern, um die Ausführung zu beschleunigen und die Kosten zu senken.

### <a name="flexible-data-sources-and-storage"></a>Flexible Datenquellen und flexibler Speicher

Sie können beim Aufrufen eines Batchendpunkts die folgenden Optionen für Eingabedaten verwenden:

- Registrierte Azure Machine Learning-Datasets: Weitere Informationen finden Sie unter [Erstellen von Azure Machine Learning-Datasets](how-to-train-with-datasets.md).
- Clouddaten: Entweder ein öffentlicher Daten-URI oder ein Datenpfad im Datenspeicher. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Daten in Azure Machine Learning Studio](how-to-connect-data-ui.md).
- Lokal gespeicherte Daten

Geben Sie den Speicherausgabeort für einen beliebigen Datenspeicher und Pfad an. Die Ausgabe für Batchendpunkte wird standardmäßig im Standardblobspeicher des Arbeitsbereichs gespeichert. Die Daten sind hierbei nach Auftragsname (systemseitig generierte GUID) sortiert.

### <a name="security"></a>Sicherheit

- Authentifizierung: Azure Active Directory-Token
- Standardmäßiges SSL für Aufruf von Endpunkten

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen verwalteter Onlineendpunkte mit der Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Bereitstellen von Batchendpunkten mit der Azure CLI](how-to-use-batch-endpoint.md)
- [Verwenden von verwalteten Onlineendpunkten mit Studio](how-to-use-managed-online-endpoint-studio.md)
- [Bereitstellen von Modellen per REST (Vorschau)](how-to-deploy-with-rest.md)
- [Überwachen verwalteter Onlineendpunkte](how-to-monitor-online-endpoints.md)
- [Anzeigen der Kosten für Onlineendpunkte](how-to-view-online-endpoints-costs.md)
- [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
