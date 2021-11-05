---
title: Containersicherheit mit Microsoft Defender für Cloud und Microsoft Defender
description: Enthält Informationen zu den Features für die Containersicherheit von Microsoft Defender für Cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e3b839d572437e3fd9677ebabb111de68ef4d005
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010085"
---
# <a name="container-security-in-microsoft-defender-for-cloud"></a>Containersicherheit in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud ist die cloudnative Lösung zum Schützen Ihrer Container.

Mit Defender für Cloud können die folgenden Containerressourcentypen geschützt werden:

| Ressourcentyp | Schutzmaßnahmen von Defender für Cloud |
|:--------------------:|-----------|
| ![Kubernetes-Dienst](./media/container-security/icon-kubernetes-service-rec.png)<br>**Kubernetes-Cluster** | Kontinuierliche Bewertung Ihrer Cluster, um Einblick in Fehlkonfigurationen und Leitlinien zu erhalten, die Ihnen helfen, identifizierte Bedrohungen zu minimieren. Erfahren Sie mehr über die [Umgebungshärtung durch Sicherheitsempfehlungen](#environment-hardening).<br><br>Bedrohungsschutz für Cluster und Linux-Knoten. Warnungen zu verdächtigen Aktivitäten werden von [Microsoft Defender für Kubernetes](defender-for-kubernetes-introduction.md) bereitgestellt. Bei diesem Plan werden Ihre Kubernetes-Cluster unabhängig davon geschützt, ob sie in Azure Kubernetes Service (AKS), lokal oder bei anderen Cloudanbietern gehostet werden. . <br>Erfahren Sie mehr über den [Laufzeitschutz für Kubernetes-Knoten und -Cluster](#run-time-protection-for-kubernetes-nodes-and-clusters).|
| ![Containerhost](./media/container-security/icon-container-host-rec.png)<br>**Containerhosts**<br>(VMs, auf denen Docker ausgeführt wird) | Kontinuierliche Bewertung der Docker-Umgebungen, um Einblicke in Fehlkonfigurationen sowie Leitlinien bereitzustellen, die Ihnen helfen, Bedrohungen zu minimieren, die vom optionalen Tool [Microsoft Defender für Server](defender-for-servers-introduction.md) erkannt wurden.<br>Erfahren Sie mehr über die [Umgebungshärtung durch Sicherheitsempfehlungen](#environment-hardening).|
| ![Container Registry.](./media/container-security/icon-container-registry-rec.png)<br>**Azure Container Registry-Registrierungen (ACR)** | Tools zur Sicherheitsbewertung und -verwaltung für die Images auf Ihren Azure Resource Manager-basierten ACR-Instanzen mit dem optionalen Tool [Microsoft Defender für Containerregistrierungen](defender-for-container-registries-introduction.md).<br>Erfahren Sie mehr über das [Scannen von Containerimages auf Sicherheitsrisiken](#vulnerability-management---scanning-container-images). |
|||

In diesem Artikel wird beschrieben, wie Sie Defender für Cloud mit den optionalen erweiterten Schutzfunktionen für Containerregistrierungen, Server und Kubernetes verwenden, um die Sicherheit Ihrer Container und Ihrer Apps zu verbessern, zu überwachen und zu verwalten.

Sie erfahren, wie Defender für Cloud diese Kernaspekte der Containersicherheit unterstützt:

- [Verwaltung von Sicherheitsrisiken: Scannen von Containerimages](#vulnerability-management---scanning-container-images)
- [Umgebungshärtung](#environment-hardening)
- [Laufzeitschutz für Kubernetes-Knoten und -Cluster](#run-time-protection-for-kubernetes-nodes-and-clusters)

Im folgenden Screenshot sind die Seite „Ressourcenbestand“ und die verschiedenen Containerressourcentypen dargestellt, die mit Defender für Cloud geschützt werden.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Containerbezogene Ressourcen auf der Seite „Ressourcenbestand“ in Defender für Cloud" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Verwaltung von Sicherheitsrisiken: Scannen von Containerimages

Wenn Sie Images in Ihren Azure Resource Manager-basierten Azure-Containerregistrierungen überwachen möchten, aktivieren Sie [Microsoft Defender für Containerregistrierungen](defender-for-container-registries-introduction.md). Defender für Cloud überprüft alle Images, die in den letzten 30 Tagen gepullt, in Ihre Registrierung gepusht oder importiert wurden. Der integrierte Scanner wird von Qualys bereitgestellt. Hierbei handelt es sich um einen branchenführenden Anbieter von Tools zur Überprüfung auf Sicherheitsrisiken.

Wenn Probleme gefunden werden – von Qualys oder Defender für Cloud –, werden Sie im [Dashboard für den Workloadschutz](workload-protections-dashboard.md) entsprechend benachrichtigt. Für jedes Sicherheitsrisiko stellt Defender für Cloud umsetzbare Empfehlungen sowie eine Klassifizierung des Schweregrads und Anleitungen für die Behebung des Problems bereit. Ausführliche Informationen zu den Defender für Cloud-Empfehlungen für Container finden Sie in der [Referenzliste mit den Empfehlungen](recommendations-reference.md#recs-compute).

Defender für Cloud filtert und klassifiziert die Ergebnisse des Scanners. Wenn ein Image fehlerfrei ist, wird es von Defender für Cloud entsprechend gekennzeichnet. Defender für Cloud generiert Sicherheitsempfehlungen nur für Images, bei denen Probleme behoben werden müssen. Indem Sie nur benachrichtigt werden, wenn Probleme auftreten, sorgt Defender für Cloud für eine Reduzierung der potenziell unerwünschten Informationswarnungen.

## <a name="environment-hardening"></a>Umgebungshärtung

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuierliche Überwachung ihrer Docker-Konfiguration

Microsoft Defender für Cloud identifiziert nicht verwaltete Container, die auf IaaS-Linux-VMs oder anderen Linux-Computern mit ausgeführten Docker-Containern gehostet werden. Defender für Cloud führt eine ständige Bewertung der Konfigurationen dieser Container durch. Anschließend werden sie mit dem [Docker-Benchmark von Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/) verglichen.

Defender für Cloud umfasst den gesamten Regelsatz des Docker-Benchmarks des CSI und benachrichtigt Sie, falls Ihre Container eine der Kontrollen nicht bestehen. Falls Fehlkonfigurationen gefunden werden, werden von Defender für Cloud Sicherheitsempfehlungen generiert. Verwenden Sie die Seite **Empfehlungen** in Defender für Cloud, um Empfehlungen anzuzeigen und Probleme zu beheben. Die CIS-Benchmarkprüfungen können nicht auf von AKS verwalteten Instanzen oder von Databricks verwalteten virtuellen Computern ausgeführt werden.

Ausführliche Informationen zu den möglichen relevanten Defender für Cloud-Empfehlungen für dieses Feature finden Sie im Abschnitt [Compute](recommendations-reference.md#recs-compute) der Referenztabelle zu den Empfehlungen.

Wenn Sie die Sicherheitsprobleme auf einem virtuellen Computer untersuchen, stellt Defender für Cloud zusätzliche Informationen zu den Containern auf dem Computer bereit. Zu diesen Informationen gehören die Docker-Version und Anzahl der auf dem Host ausgeführten Images. 

Um nicht verwaltete Container zu überwachen, die auf IaaS-Linux-VMs gehostet werden, aktivieren Sie die optionale [Microsoft Defender für Server](defender-for-servers-introduction.md)-Instanz.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Fortlaufende Überwachung ihrer Kubernetes-Cluster
Defender für Cloud kann zusammen mit Azure Kubernetes Service (AKS), dem verwalteten Containerorchestrierungsdienst von Microsoft für die Entwicklung, Bereitstellung und Verwaltung von Containeranwendungen, verwendet werden.

AKS bietet Sicherheitssteuerungen und Einblicke in den Sicherheitsstatus ihrer Cluster. Defender für Cloud verwendet diese Features, um die Konfiguration Ihrer AKS-Cluster ständig zu überwachen und an Branchenstandards ausgerichtete Sicherheitsempfehlungen zu generieren.

Hier ist ein allgemeines Diagramm zur Interaktion zwischen Microsoft Defender für Cloud, Azure Kubernetes Service und Azure Policy angegeben:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Allgemeine Architektur der Interaktion zwischen Microsoft Defender für Cloud, Azure Kubernetes Service und Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Sie können sehen, dass Defender für Cloud die folgenden Elemente empfängt und analysiert:

- Überwachungsprotokolle vom API-Server
- Unformatierte Sicherheitsereignisse vom Log Analytics-Agent

    > [!NOTE]
    > Derzeit wird die Installation des Log Analytics-Agents in Azure Kubernetes Service-Clustern, die in VM-Skalierungsgruppen ausgeführt werden, nicht unterstützt.

- Clusterkonfigurationsinformationen vom AKS-Cluster
- Workloadkonfiguration von Azure Policy (über das **Azure Policy-Add-On für Kubernetes**)

Ausführliche Informationen zu den möglichen relevanten Defender für Cloud-Empfehlungen für dieses Feature finden Sie im Abschnitt [Compute](recommendations-reference.md#recs-compute) der Referenztabelle zu den Empfehlungen.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Bewährte Methoden zum Schutz von Workloads mithilfe der Kubernetes-Zugangssteuerung

Installieren Sie das **Azure Policy-Add-On für Kubernetes**, um verschiedene Empfehlungen zum Schutz der Workloads Ihrer Kubernetes-Container zu erhalten. Dieses Add-On kann auch automatisch bereitgestellt werden, wie unter [Aktivieren der automatischen Bereitstellung des Log Analytics-Agents und der Erweiterungen](enable-data-collection.md#auto-provision-mma) erläutert. Wenn die automatische Bereitstellung für das Add-On auf „Ein“ festgelegt ist, wird die Erweiterung standardmäßig in allen vorhandenen und zukünftigen Clustern aktiviert (sofern diese die Add-On-Installationsanforderungen erfüllen).

Wie auf [dieser Azure Policy-Seite für Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md) erläutert, erweitert das Add-On den Open-Source-Webhook für den Zugangscontroller [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) für den  [Open Policy-Agent](https://www.openpolicyagent.org/). Kubernetes-Zugangscontroller sind Plug-Ins, die die Art der Verwendung Ihrer Cluster erzwingen. Das Add-On registriert sich als Webhook bei der Kubernetes-Zugangssteuerung und ermöglicht das zentrale, konsistente Anwenden von Skalierungs- und Sicherheitsvorkehrungen in Ihren Clustern. 

Mit dem Add-On in Ihrem AKS-Cluster wird jede Anforderung an den Kubernetes-API-Server anhand der vordefinierten bewährten Methoden überwacht, bevor sie im Cluster persistent gespeichert wird. Anschließend können Sie das **Erzwingen** der bewährten Methoden konfigurieren und auf zukünftige Workloads anwenden. 

Beispielsweise können Sie vorschreiben, dass keine privilegierten Container erstellt werden sollen und dass zukünftige Anforderungen für diese Aktion blockiert werden sollen.

Weitere Informationen finden Sie unter [Schützen Ihrer Kubernetes-Workloads](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Laufzeitschutz für Kubernetes-Knoten und -Cluster

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Nächste Schritte

In dieser Übersicht haben Sie die wichtigsten Elemente der Containersicherheit in Microsoft Defender für Cloud kennengelernt. Verwandte Informationen finden Sie hier:

- [Einführung in Microsoft Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Einführung in Microsoft Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)
