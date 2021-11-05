---
title: 'CI/CD-Workflow mit GitOps: Kubernetes mit Azure Arc-Unterstützung'
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
description: Dieser Artikel enthält eine konzeptionelle Übersicht über einen CI/CD-Workflow mit GitOps.
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, Container, CI, CD, Azure DevOps
ms.openlocfilehash: 837c0124739fa6659fc4c7652fcb9ca7be4fbf85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039656"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>CI/CD-Workflow mit GitOps: Kubernetes mit Azure Arc-Unterstützung

Moderne Kubernetes-Bereitstellungen umfassen mehrere Anwendungen, Cluster und Umgebungen. Mit GitOps können Sie diese komplexen Setups leichter verwalten und den gewünschten Zustand der Kubernetes-Umgebungen deklarativ mit Git nachverfolgen. Mithilfe gängiger Git-Tools zum Nachverfolgen des Clusterzustands können Sie die Verantwortlichkeit erhöhen, die Untersuchung von Fehlern vereinfachen sowie Automatisierungen für die Umgebungsverwaltung ermöglichen.

In dieser konzeptionellen Übersicht wird GitOps als Realität im gesamten Lebenszyklus der Anwendungsänderung mithilfe von Azure Arc, Azure Repos und Azure Pipelines erläutert. [Betrachten Sie ein Beispiel](#example-workflow) einer einzelnen Anwendungsänderung von GitOps gesteuerter Kubernetes-Umgebungen.

## <a name="architecture"></a>Aufbau

In diesem Beispiel wird eine Anwendung verwendet, die in mindestens einer Kubernetes-Umgebung bereitgestellt wird.

![GitOps-CI/CD-Architektur](./media/gitops-arch.png)

### <a name="application-repo"></a>Anwendungsrepository
Das Anwendungsrepository enthält den Anwendungscode, an dem Entwickler während der inneren Schleife arbeiten. Die Bereitstellungsvorlagen der Anwendung sind in generischer Form (z. B. Helm oder Kustomize) in diesem Repository vorhanden. Es werden keine umgebungsspezifischen Werte gespeichert. Durch Änderungen an diesem Repository wird eine PR- oder CI-Pipeline aufgerufen, die den Bereitstellungsprozess startet.
### <a name="container-registry"></a>Containerregistrierung
Die Containerregistrierung enthält alle Erst- und Drittanbieterimages, die in den Kubernetes-Umgebungen verwendet werden. Versehen Sie Anwendungsimages von Erstanbietern mit lesbaren Tags und dem Git-Commit für die Imageerstellung. Speichern Sie Images von Drittanbietern zwischen, um die Sicherheit, Geschwindigkeit und Resilienz zu verbessern. Legen Sie einen Plan für zeitnahe Tests und die Integration von Sicherheitsupdates fest. Weitere Informationen sowie ein Beispiel finden Sie unter [Verwenden und Verwalten von öffentlichen Inhalten mit Azure Container Registry Tasks](../../container-registry/tasks-consume-public-content.md).
### <a name="pr-pipeline"></a>PR-Pipeline
Das Gating von PRs für das Anwendungsrepository basiert auf einer erfolgreichen Ausführung der PR-Pipeline. Von dieser Pipeline werden die grundlegenden Quality Gates (wie Linten und Komponententests) für den Anwendungscode ausgeführt. Die Pipeline testet die Anwendung und lintet Dockerfiles und Helm-Vorlagen, die für die Bereitstellung in einer Kubernetes-Umgebung verwendet werden. Docker-Images sollten erstellt und getestet, aber nicht gepusht werden. Halten Sie die Pipelinedauer relativ kurz, um schnelle Iterationen zu ermöglichen.
### <a name="ci-pipeline"></a>CI-Pipeline
Die Anwendungs-CI-Pipeline führt alle PR-Pipelineschritte aus und erweitert das Testen und die Bereitstellungsüberprüfungen. Die Pipeline kann für jeden einzelnen Commit oder in regelmäßigen Abständen mit einer Gruppe von Commits ausgeführt werden. Führen Sie in dieser Phase Anwendungstests durch, die für eine PR-Pipeline zu lang sind. Pushen Sie Docker-Images, die Sie zur Vorbereitung der Bereitstellung erstellt haben, in die Container Registry-Instanz. Die ersetzte Vorlage kann mit Testwerten gelintet werden. Zur Dienstlaufzeit verwendete Images müssen an diesem Punkt gelintet, erstellt und getestet werden. Speziell im CI-Buildvorgang werden Artefakte für den CD-Schritt veröffentlicht, damit sie zur Vorbereitung der Bereitstellung genutzt werden können.
### <a name="flux"></a>Flux
Flux ist ein Dienst, der in jedem Cluster ausgeführt wird und für die Aufrechterhaltung des gewünschten Zustands zuständig ist. Der Dienst fragt regelmäßig das GitOps-Repository ab, um zu ermitteln, ob Clusteränderungen vorliegen, und wendet sie an.
### <a name="cd-pipeline"></a>CD-Pipeline
Die CD-Pipeline wird automatisch durch erfolgreiche CI-Buildvorgänge ausgelöst. Sie verwendet die zuvor veröffentlichten Vorlagen, ersetzt Umgebungswerte und erstellt einen PR für das GitOps-Repository, um eine Änderung am gewünschten Zustand mindestens eines Kubernetes-Clusters anzufordern. Clusteradministratoren überprüfen den PR für die Zustandsänderung und genehmigen die Zusammenführung im GitOps-Repository. Anschließend wartet die Pipeline, bis der PR abgeschlossen ist, was es Flux ermöglicht, die Zustandsänderung aufzugreifen.
### <a name="gitops-repo"></a>GitOps-Repository
Das GitOps-Repository stellt clusterübergreifend den aktuellen gewünschten Zustand aller Umgebungen dar. Änderungen an diesem Repository werden vom Flux-Dienst in jedem Cluster aufgegriffen und bereitgestellt. PRs mit Änderungen am gewünschten Zustand werden erstellt, überprüft und zusammengeführt. Diese PRs enthalten sowohl Änderungen an Bereitstellungsvorlagen als auch Änderungen an den resultierenden gerenderten Kubernetes-Manifesten. Gerenderte Manifeste auf niedriger Ebene ermöglichen eine sorgfältigere Überprüfung von Änderungen, die in der Regel auf Vorlagenebene nicht sichtbar sind.
### <a name="kubernetes-clusters"></a>Kubernetes-Cluster
Die verschiedenen von der Anwendung benötigten Umgebungen werden von mindestens einem Kubernetes-Cluster mit Azure Arc-Unterstützung bereitgestellt. Über unterschiedliche Namespaces kann von einem einzelnen Cluster beispielsweise sowohl die Entwicklungs- als auch die Qualitätssicherungsumgebung bereitgestellt werden. Die Verwendung eines zweiten Clusters ermöglicht eine einfachere Trennung der Umgebungen sowie eine präzisere Steuerung.
## <a name="example-workflow"></a>Beispielworkflow
Als Anwendungsentwickler führt Alice folgende Schritte aus:
* Sie schreibt Anwendungscode.
* Sie bestimmt, wie die Anwendung in einem Docker-Container ausgeführt werden soll.
* Sie definiert die Vorlagen für die Ausführung des Containers und der abhängigen Dienste in einem Kubernetes-Cluster.

Alice weiß zwar, dass die Anwendung in mehreren Umgebungen ausführbar sein muss, kennt aber nicht die spezifischen Einstellungen für jede Umgebung.

Angenommen, Alice möchte eine Anwendungsänderung vornehmen, die eine Änderung des Docker-Images zur Folge hat, das in der Vorlage für die Anwendungsbereitstellung verwendet wird.

1. Alice ändert die Bereitstellungsvorlage, pusht sie an einen Remotebranch im Anwendungsrepository und erstellt einen PR zur Überprüfung.
2. Alice bittet ihr Team, die Änderung zu überprüfen.
    * Von der PR-Pipeline wird eine Validierung durchgeführt.
    * Nach einer erfolgreichen Pipelineausführung gibt das Team seine Zustimmung, und die Änderung wird zusammengeführt.
3. Die CI-Pipeline überprüft die Änderung von Alice und wird erfolgreich abgeschlossen.
    * Die Änderung kann gefahrlos im Cluster bereitgestellt werden, und die Artefakte werden in der CI-Pipelineausführung gespeichert.
4. Die Änderung von Alice wird zusammengeführt und löst die CD-Pipeline aus.
    * Die CD-Pipeline greift die Artefakte auf, die in der CI-Pipelineausführung von Alice gespeichert wurden.
    * Die CD-Pipeline ersetzt die Vorlagen durch umgebungsspezifische Werte und staged alle Änderungen für den vorhandenen Clusterzustand im GitOps-Repository.
    * Die CD-Pipeline erstellt einen PR für das GitOps-Repository mit den gewünschten Clusterzustandsänderungen.
5. Das Team von Alice überprüft und genehmigt ihren PR.
    * Die Änderung wird in dem Zielbranch zusammengeführt, der der Umgebung entspricht.
6. Innerhalb weniger Minuten erkennt Flux eine Änderung im GitOps-Repository und pullt die Änderung von Alice.
    * Aufgrund der Änderung des Docker-Images muss der Anwendungspod aktualisiert werden.
    * Flux wendet die Änderung auf den Cluster an.
7. Alice testet den Anwendungsendpunkt, um sich zu vergewissern, dass die Bereitstellung erfolgreich abgeschlossen wurde.
   > [!NOTE]
   > Ist eine Bereitstellung in weiteren Umgebungen vorgesehen, wird die CD-Pipeline erneut durchlaufen, indem eine PR für die nächste Umgebung erstellt wird und die Schritte 4 bis 7 wiederholt werden. Für riskantere Bereitstellungen oder Umgebungen (beispielsweise für eine sicherheitsbezogene Änderung oder eine Produktionsumgebung) ist ggf. eine zusätzliche Genehmigung erforderlich.
8.  Wenn die Bereitstellung für alle Umgebungen erfolgreich war, wird die Pipeline abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über das Herstellen von Verbindungen zwischen Ihrem Cluster und einem Git-Repository als [Konfigurationsressource mit Kubernetes mit Azure Arc-Unterstützung](./conceptual-configurations.md).
