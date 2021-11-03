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
ms.openlocfilehash: 02c927b55812e4b309e53679cf3548d889bdc12f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079413"
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

Nach dem Trainieren eines Machine Learning-Modells müssen Sie das Modell so bereitstellen, dass es von anderen Benutzern zum Durchführen von Rückschlussvorgängen genutzt werden kann. In Azure Machine Learning können Sie hierfür **Endpunkte** (Vorschau) und **Bereitstellungen** (Vorschau) verwenden.

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="Diagramm: Endpunkt, bei dem Datenverkehr auf zwei Bereitstellungen aufgeteilt wird":::

Ein **Endpunkt** ist ein HTTPS-Endpunkt, der von Clients aufgerufen werden kann, um die Rückschlussausgabe (Bewertung) eines trainierten Modells zu empfangen. Sie bietet: 
- Authentifizierung mit "Schlüssel & Token"-basierter Authentifizierung 
- SSL-Terminierung 
- Datenverkehrszuordnung zwischen Bereitstellungen 
- Stabilen Bewertungs-URI (endpoint-name.region.inference.ml.azure.com)


Eine **Bereitstellung** ist eine Gruppe von Computeressourcen, von denen das Modell gehostet wird, das die eigentlichen Rückschlussvorgänge durchführt. Sie enthält folgende Elemente: 
- Modelldetails (Code, Modell, Umgebung) 
- Einstellungen für Computeressourcen und Skalierung 
- Erweiterte Einstellungen (z. B. Anforderungs- und Testeinstellungen)

Ein einzelner Endpunkt kann mehrere Bereitstellungen enthalten. Endpunkte und Bereitstellungen sind unabhängige ARM-Ressourcen, die im Azure-Portal angezeigt werden.

Das Konzept der Endpunkte und Bereitstellungen wird von Azure Machine Learning genutzt, um unterschiedliche Arten von Endpunkten zu implementieren: [**Onlineendpunkte**](#what-are-online-endpoints-preview) und [**Batchendpunkte**](#what-are-batch-endpoints-preview).

### <a name="multiple-developer-interfaces"></a>Mehrere Entwicklerschnittstellen

Erstellen und Verwalten von Batch- und Onlineendpunkten mit verschiedenen Entwicklertools:
- Azure CLI
- ARM/REST-API
- Azure Machine Learning Studio-Webportal
- Azure-Portal (IT/Administrator)
- Unterstützung für CI/CD-MLOps-Pipelines über die Azure CLI- und REST-/ARM-Schnittstelle

## <a name="what-are-online-endpoints-preview"></a>Was sind Onlineendpunkte (Vorschau)?

**Onlineendpunkte** (Vorschau) sind Endpunkte, die für Onlinerückschlüsse (Echtzeit) verwendet werden. Gegenüber **Batchendpunkten** enthalten **Onlineendpunkte** **Bereitstellungen**, die bereit für den Empfang der Daten von Clients sind und in Echtzeit Rückantworten senden können.

### <a name="online-endpoints-requirements"></a>Anforderungen an Onlineendpunkte

Sie müssen Folgendes angeben, um einen Onlineendpunkt zu erstellen:
- Modelldateien (oder Angeben eines registrierten Modells in Ihrem Arbeitsbereich) 
- Bewertungsskript: Code, der zum Durchführen von Bewertungs- bzw. Rückschlussvorgängen benötigt wird
- Umgebung: Docker-Image mit Conda-Abhängigkeiten oder ein Dockerfile 
- Einstellungen für Compute-Instanzen und Skalierungen 

Hier wird beschrieben, wie Sie Onlineendpunkte über die [CLI](how-to-deploy-managed-online-endpoints.md) und das [Studio-Webportal](how-to-use-managed-online-endpoint-studio.md) bereitstellen.

### <a name="test-and-deploy-locally-for-faster-debugging"></a>Lokales Testen und Bereitstellen zur Beschleunigung des Debuggens

Führen Sie die lokale Bereitstellung durch, um Ihre Endpunkte ohne Bereitstellung in der Cloud zu testen. Von Azure Machine Learning wird ein lokales Docker-Image erstellt, mit dem das Azure ML-Image imitiert wird. Azure Machine Learning führt die Erstellungs- und Bereitstellungsvorgänge für Sie lokal durch und speichert das Image zwischen, um schnelle Iterationen zu ermöglichen.

### <a name="native-bluegreen-deployment"></a>Native Blau-Grün-Bereitstellung 

Beachten Sie den bereits erwähnten Hinweis, dass ein einzelner Endpunkt mehrere Bereitstellungen enthalten kann. Vom Onlineendpunkt kann ein Lastenausgleich durchgeführt werden, um den einzelnen Bereitstellungen jeweils einen bestimmten Prozentsatz an Datenverkehr zuzuordnen.

Die Datenverkehrszuordnung kann genutzt werden, um einen sicheren Rollout für Blau-Grün-Bereitstellungen zu ermöglichen, indem Anforderungen auf verschiedene Instanzen verteilt werden.

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="Screenshot: Schieberegler zum Festlegen der Datenverkehrszuordnung für mehrere Bereitstellungen":::

Informieren Sie sich, wie Sie einen [sicheren Rollout für Onlineendpunkte](how-to-safely-rollout-managed-endpoints.md) durchführen.

### <a name="application-insights-integration"></a>Application Insights-Integration

Alle Onlineendpunkte sind mit Application Insights integriert, um SLAs zu überwachen und Probleme zu diagnostizieren. 

[Verwaltete Onlineendpunkte](#managed-online-endpoints-vs-kubernetes-online-endpoints-preview) verfügen aber auch über eine standardmäßige Integration mit Azure-Protokollen und -Metriken.

### <a name="security"></a>Sicherheit

- Authentifizierung: Schlüssel und Azure ML-Token
- Verwaltete Identität: Benutzer- und systemseitig zugewiesen (nur verwalteter Onlineendpunkt)
- Standardmäßiges SSL für Aufruf von Endpunkten


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
| **Verwaltete Identität** | [Unterstützt](tutorial-deploy-managed-endpoints-using-system-managed-identity.md) | Unterstützt |
| **Virtuelles Netzwerk (VNET)** | Nicht unterstützt (öffentliche Vorschauversion) | Unterstützt |
| **Anzeigen von Kosten** | [Endpunkt- und Bereitstellungsebene](how-to-view-online-endpoints-costs.md) | Clusterebene |

### <a name="managed-online-endpoints"></a>Verwaltete Onlineendpunkte

Verwaltete Onlineendpunkte können zur Optimierung Ihres Bereitstellungsprozesses beitragen. Verwaltete Onlineendpunkte haben gegenüber Kubernetes-Onlineendpunkten die folgenden Vorteile:

- Verwaltete Infrastruktur
    - Automatische Bereitstellung der Computeressourcen und Hosting des Modells (Sie müssen nur VM-Typ und Skalierungseinstellungen angeben) 
    - Automatisches Durchführen von Updates und Bereitstellen von Patches für das zugrunde liegende Hostbetriebssystem-Image
    - Automatische Knotenwiederherstellung bei einem Systemausfall

:::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="Screenshot: Azure Monitor-Graph zur Endpunktlatenz":::

- Überwachung und Protokolle
    - Überwachen Sie die Modellverfügbarkeit, Leistung und SLA über die [native Integration mit Azure Monitor](how-to-monitor-online-endpoints.md).
    - Debuggen Sie Bereitstellungen, indem Sie die Protokolle und die native Integration mit Azure Log Analytics verwenden.

- Verwaltete Identität
    -  Verwenden Sie [verwaltete Identitäten zum Zugreifen auf geschützte Ressourcen über das Bewertungsskript](tutorial-deploy-managed-endpoints-using-system-managed-identity.md).

:::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="Screenshot: Kostendiagramm für einen Endpunkt und eine Bereitstellung":::

- Anzeigen von Kosten 
    - Mit verwalteten Onlineendpunkten können Sie die [Kosten auf Endpunkt- und Bereitstellungsebene überwachen](how-to-view-online-endpoints-costs.md).

Ein Tutorial mit einer Schritt-für-Schritt-Anleitung finden Sie unter [Bereitstellen verwalteter Onlineendpunkte](how-to-deploy-managed-online-endpoints.md).

## <a name="what-are-batch-endpoints-preview"></a>Was sind Batchendpunkte (Vorschau)?

**Batchendpunkte** (Vorschau) werden verwendet, um Batchrückschlussvorgänge für große Datenmengen innerhalb eines bestimmten Zeitraums durchzuführen.  Mit **Batchendpunkten** werden Zeiger auf Daten empfangen und Aufträge asynchron ausgeführt, um die Daten in Computeclustern parallel zu verarbeiten. Auf Batchendpunkten werden Ausgaben zur weiteren Analyse in einem Datenspeicher gespeichert.

Erfahren Sie mehr zum [Bereitstellen und Verwenden von Batchendpunkten mit der Azure CLI](how-to-use-batch-endpoint.md).

### <a name="no-code-mlflow-model-deployments"></a>MLflow-Modellbereitstellungen ohne Code

Verwenden Sie die Benutzeroberfläche für die Erstellung von Batchendpunkten ohne Code für [MLflow-Modelle](how-to-use-mlflow.md), um die automatische Erstellung von Bewertungsskripts und Ausführungsumgebungen zu ermöglichen.  

Für Batchendpunkte, für die MLflow-Modelle verwendet werden, müssen Sie Folgendes angeben:
- Modelldateien (oder Angeben eines registrierten Modells in Ihrem Arbeitsbereich)
- Computeziel

Wenn Sie **kein** MLflow-Modell bereitstellen, müssen Sie zusätzliche Anforderungen angeben:
- Bewertungsskript: Code, der zum Durchführen von Bewertungs- bzw. Rückschlussvorgängen benötigt wird
- Umgebung: Docker-Image mit Conda-Abhängigkeiten


### <a name="managed-cost-with-autoscaling-compute"></a>Verwaltete Kosten mit automatischer Skalierung von Computeressourcen

Beim Aufrufen eines Batchendpunkts wird ein asynchroner Batchrückschlussauftrag ausgelöst. Computeressourcen werden automatisch bereitgestellt, wenn der Auftrag gestartet wird, und nach Abschluss des Auftrags wird die Bereitstellung dann automatisch wieder aufgehoben. Sie zahlen also nur für Computeressourcen, wenn Sie diese auch nutzen.

Sie können die [Einstellungen für Computeressourcen außer Kraft setzen](how-to-use-batch-endpoint.md#overwrite-settings) (z. B. die Instanzanzahl). Dies gilt auch für die erweiterten Einstellungen (z. B. Minibatchgröße, Fehlerschwellenwert usw.) der einzelnen Batchrückschlussaufträge, um die Ausführung zu beschleunigen und die Kosten zu reduzieren.

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
