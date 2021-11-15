---
title: Zugreifen auf Kubernetes-Ressourcen aus dem Azure-Portal
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Erfahren Sie, wie Sie mit Kubernetes-Ressourcen interagieren, um einen Kubernetes-Cluster mit Azure Arc-Unterstützung über das Azure-Portal zu verwalten.
ms.openlocfilehash: 5af26c853df67b213b1951224ae9603d15a9dd25
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000604"
---
# <a name="access-kubernetes-resources-from-azure-portal"></a>Zugreifen auf Kubernetes-Ressourcen aus dem Azure-Portal

Das Azure-Portal umfasst eine Kubernetes-Ressourcenansicht für den einfachen Zugriff auf die Kubernetes-Ressourcen in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung. Durch das Anzeigen von Kubernetes-Ressourcen aus dem Azure-Portal wird das Wechseln des Kontexts zwischen dem Azure-Portal und dem `kubectl`-Befehlszeilentool verringert, wodurch die Erfahrung des Anzeigens und Bearbeitens Ihrer Kubernetes-Ressourcen optimiert wird. Die Ressourcenanzeige enthält derzeit mehrere Ressourcentypen, z. B. Bereitstellungen, Pods und Replikatgruppen.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein vorhandener Kubernetes-Cluster, der als Kubernetes-Ressource mit Azure Arc-Unterstützung mit Azure [verbunden](quickstart-connect-cluster.md) ist.

- [Die Cluster Connect-Funktion muss auf dem Kubernetes-Cluster mit Azure Arc-Unterstützung aktiviert sein](cluster-connect.md#enable-cluster-connect-feature).

- [Dienstkontotoken](cluster-connect.md#service-account-token-authentication-option) für die Authentifizierung beim Cluster.

## <a name="view-kubernetes-resources"></a>Anzeigen von Kubernetes-Ressourcen

Um die Kubernetes-Ressourcen anzuzeigen, navigieren Sie im Azure-Portal zu Ihrem AKS-Cluster. Der Navigationsbereich auf der linken Seite wird verwendet, um auf Ihre Ressourcen zuzugreifen. Die Ressource umfasst Folgendes:

- **Namespaces** zeigt die Namespaces Ihres Clusters an. Der Filter am Anfang der Namespaceliste bietet eine schnelle Möglichkeit zum Filtern und Anzeigen Ihrer Namespaceressourcen.
- **Workloads** zeigt Informationen zu Bereitstellungen, Pods, Replikatgruppen, StatefulSets, Daemongruppen, Aufträgen und cron-Aufträgen an, die auf Ihrem Cluster bereitgestellt wurden.
- **Dienste und Eingänge** zeigt alle Dienst- und Eingangsressourcen Ihres Clusters an.
- **Storage** zeigt Ihre Azure-Speicher Klassen und persistenten Volumeinformationen an.
- Unter **Konfiguration** werden die ConfigMaps und Geheimnisse Ihres Clusters angezeigt.

[ ![Im Azure-Portal angezeigte Kubernetes-Workloadinformationen](media/kubernetes-resource-view/workloads.png) ](media/kubernetes-resource-view/workloads.png#lightbox)

## <a name="edit-yaml"></a>Bearbeiten von YAML

Die Kubernetes-Ressourcenansicht enthält auch einen YAML-Editor. Ein integrierter YAML-Editor bedeutet, dass Sie Kubernetes-Objekte aus dem Portal heraus aktualisieren und Änderungen sofort anwenden können.

Nachdem Sie die YAML-Datei bearbeitet haben, werden Änderungen angewendet, indem Sie **Überprüfen und speichern** auswählen, die Änderungen bestätigen und dann erneut speichern.

[ ![Im Azure-Portal angezeigter YAML-Editor für Kubernetes-Objekte](media/kubernetes-resource-view/yaml-editor.png) ](media/kubernetes-resource-view/yaml-editor.png#lightbox)

>[!WARNING]
> Die Durchführung direkter Produktionsänderungen über die Benutzeroberfläche oder CLI wird nicht empfohlen, und Sie sollten die Verwendung von [Konfigurationen (GitOps)](tutorial-use-gitops-connected-cluster.md) für Produktionsumgebungen in Betracht ziehen. Die Kubernetes-Verwaltungsfunktionen und der YAML-Editor im Azure-Portal sind für das Erlernen und Erstellen von Flights neuer Bereitstellungen in einer Entwicklungs- und Testumgebung konzipiert.

## <a name="next-steps"></a>Nächste Schritte

Azure Monitor für Container bietet mehr detaillierte Informationen zu Knoten und Containern des Clusters im Vergleich zur logischen Ansicht der Kubernetes-Ressourcen, die in der in diesem Artikel beschriebenen Ansicht für Kubernetes-Ressourcen verfügbar sind. Erfahren Sie, wie Sie [Azure Monitor für Container in Ihrem Cluster bereitstellen](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json).