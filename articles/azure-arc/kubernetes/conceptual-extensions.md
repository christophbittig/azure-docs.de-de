---
title: 'Clustererweiterungen: Kubernetes mit Azure Arc-Unterstützung'
services: azure-arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dieser Artikel bietet eine konzeptionelle Übersicht über die Funktionalität für Clustererweiterungen von Kubernetes mit Azure Arc-Unterstützung.
ms.openlocfilehash: 6ae9270822ae16608a2c470ed818bcb8e6c2aa38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279270"
---
# <a name="cluster-extensions"></a>Clustererweiterungen

Verwaltungsdienste wie Azure Monitor und Microsoft Defender für Kubernetes oder Dienste wie Azure App Services und Azure Data Services können über die Cluster Extensions-Fähigkeit auf Kubernetes-Clustern instanziiert werden. [Helm-Diagramme](https://helm.sh/) helfen Ihnen, Kubernetes-Anwendungen zu verwalten, indem sie die Bausteine bereitstellen, die zum Definieren, Installieren und Aktualisieren der komplexesten Kubernetes-Anwendungen erforderlich sind. Die Clustererweiterungsfunktion baut auf den Paketkomponenten von Helm auf, indem sie eine auf Azure Resource Manager basierende Möglichkeit für die Installation und Lebenszyklusverwaltung von Instanzen der Dienste bietet, die Sie in Ihrem Kubernetes-Cluster aktivieren möchten. Als Clusteroperator oder -administrator können Sie die Clustererweiterungsfunktion für Folgendes verwenden: 

- Installieren Sie verschiedene Erweiterungen für die gewünschte Funktionalität, und rufen Sie den Bestand aller Cluster und die Erweiterungen, die in diesen Clustern installiert sind, über Azure-Schnittstellen (z. B. Azure-Portal, Befehlszeilenschnittstelle, SDK usw.) ab. 
- Steuern Sie wie bei jeder anderen Azure-Ressource den Zugriff auf die Clustererweiterungsressource mithilfe der rollenbasierte Zugriffssteuerung von Azure (Azure Role Based Access Control, RBAC).
- Verwenden Sie Azure Policy, um die Bereitstellung von Clustererweiterungen im großen Stil in allen Clustern in Ihrer Umgebung zu automatisieren. 
- Abonnieren Sie Release Trains (z. B. preview, stable) für jede Erweiterung.
- Verwalten Sie Updates, indem Sie das automatische Upgrade für Erweiterungen einrichten oder eine bestimmte Version festlegen.
- Verwalten Sie den Lebenszyklus von Erweiterungen, einschließlich Updates von Erweiterungseigenschaften, oder löschen Sie eine oder mehrere Erweiterungsinstanzen.

Eine Erweiterung kann für einen Cluster oder einen Namespace gelten. Jeder Erweiterungstyp (z. B. Azure Monitor, Microsoft Defender für Cloud, Azure App Services) definiert den Bereich, in dem er auf dem Cluster arbeitet. 

## <a name="architecture-for-azure-arc-enabled-kubernetes-clusters"></a>Architektur für Kubernetes-Cluster mit Azure Arc-Unterstützung

[ ![Architektur von Clustererweiterungen](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

Die Clustererweiterungsinstanz wird als Azure Resource Manager-Erweiterungsressource (`Microsoft.KubernetesConfiguration/extensions`) auf der Kubernetes-Ressource mit Azure Arc-Unterstützung (von `Microsoft.Kubernetes/connectedClusters` dargestellt) in Azure Resource Manager erstellt. Mithilfe der Darstellung in Azure Resource Manager können Sie eine Richtlinie erstellen, die ermittelt, welche Kubernetes-Ressourcen mit Azure Arc-Unterstützung über eine spezifische Clustererweiterung verfügen und welche nicht. Nachdem Sie ermittelt haben, welchen Clustern Clustererweiterungen mit den gewünschten Eigenschaftswerten fehlen, können Sie diese nicht konformen Ressourcen mit Azure Policy korrigieren.

Der in Ihrem Cluster ausgeführte `config-agent` überwacht und verfolgt neu erstellte Erweiterungsressourcen oder Updates an vorhandenen Erweiterungsressourcen der Kubernetes-Ressource mit Azure Arc-Unterstützung. Die in Ihrem Cluster ausgeführte `extensions-manager`-Komponente pullt dann das Helm-Chart, das einer Clustererweiterung zugeordnet ist, aus Azure Container Registry oder Microsoft Container Registry und installiert es im Cluster. 

Die beiden im Cluster ausgeführten Komponenten `config-agent` und `extensions-manager` verarbeiten Updates für neue Versionen und andere Vorgänge wie Aktualisierungen von Eigenschaften der Erweiterungsinstanz und Löschungen. Diese Agents verwenden eine systemseitig zugewiesene verwaltete Identität, um sicher mit dem Back-End-Dienst in Azure zu kommunizieren. 

> [!NOTE]
> * `config-agent` überwacht die Erstellung neuer oder die Aktualisierung vorhandener Erweiterungsressourcen in der Kubernetes-Ressource mit Azure Arc-Unterstützung. Damit der gewünschte Zustand der Erweiterung in den Cluster gepullt werden kann, benötigen Agents also Konnektivität. Wenn Agents keine Verbindung mit Azure herstellen können, wird die Weitergabe des gewünschten Zustands an den Cluster verzögert.
> * Zu den Eigenschaften, die Sie für Clustererweiterungen festlegen können, gehören die ProtectedConfiguration-Einstellungen. Geschützte Konfigurationseinstellungen für eine Erweiterung werden bis zu 48 Stunden in den Kubernetes-Diensten mit Azure Arc-Unterstützung gespeichert. Wenn der Cluster während der 48 Stunden nach der Erstellung der Erweiterungsressource in Azure getrennt bleibt, geht die Erweiterung vom Status `Pending` in den Status `Failed` über. Sie sollten die Cluster so regelmäßig wie möglich online schalten.

## <a name="next-steps"></a>Nächste Schritte

* Nutzen Sie den Schnellstart zum [Verbinden eines Kubernetes-Clusters mit Azure Arc](./quickstart-connect-cluster.md).
* Stellen Sie [Clustererweiterungen](./extensions.md) in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung bereit.
