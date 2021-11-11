---
title: Architektur eines Kubernetes-Agents mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dieser Artikel enthält eine Übersicht über die Architekturen Azure Arc-fähiger Kubernetes-Agents.
keywords: Kubernetes, Arc, Azure, Container
ms.openlocfilehash: 6bb1b035e1c64e82f89804928854ca019ad22201
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450835"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architektur eines Kubernetes-Agents mit Azure Arc-Unterstützung

[Kubernetes](https://kubernetes.io/) kann eigenständig containerisierte Workloads konsistent in Hybridumgebungen und Umgebungen mit mehreren Clouds bereitstellen. Kubernetes mit Azure Arc-Unterstützung arbeitet jedoch als zentralisierte, konsistente Steuerungsebene, die Richtlinien, Governance und Sicherheit in heterogenen Umgebungen verwaltet. Dieser Artikel enthält:

* Eine Übersicht der Architekturen für das Verbinden eines Clusters mit Azure Arc.
* Das Konnektivitätsmuster, das Agents einhalten.
* Eine Beschreibung der zwischen der Clusterumgebung und Azure ausgetauschten Daten.

## <a name="deploy-agents-to-your-cluster"></a>Bereitstellen von Agents in Ihrem Cluster

Die meisten lokalen Rechenzentren erzwingen strikte Netzwerkregeln, die eingehende Kommunikation in der Netzwerkgrenzenfirewall verhindern. Kubernetes mit Azure Arc-Unterstützung arbeitet mit diesen Einschränkungen, indem keine eingehenden Ports in der Firewall erforderlich sind und nur selektive Ausgangsendpunkte für die ausgehende Kommunikation aktiviert werden. Kubernetes mit Azure Arc-Unterstützung initiiert diese ausgehende Verbindung. 

![Übersicht über die Architektur](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Verbinden eines Clusters mit Azure Arc

1. Erstellen Sie einen Kubernetes-Cluster auf der von Ihnen gewünschten Infrastruktur (VMware vSphere, Amazon Web Services, Google Cloud Platform usw.). 

    > [!NOTE]
    > Da Kubernetes mit Azure Arc-Unterstützung derzeit nur das Anfügen vorhandener Kubernetes-Cluster an Azure Arc unterstützt, müssen Kunden den Lebenszyklus des Kubernetes-Clusters selbst erstellen und verwalten.  

1. Starten Sie die Azure Arc-Registrierung für Ihren Cluster mithilfe der Azure CLI.
    * Azure CLI verwendet Helm, um das Agent-Helm-Chart im Cluster bereitzustellen.
    * Die Clusterknoten initiieren eine ausgehende Kommunikation mit der [Microsoft Container Registry](https://github.com/microsoft/containerregistry) und rufen die Images ab, die zum Erstellen der folgenden Agents im `azure-arc`-Namespace erforderlich sind:

        | Agent | BESCHREIBUNG |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Kubernetes mit Azure Arc-Unterstützung unterstützt derzeit nur [systemseitig zugewiesene Identitäten](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` initiiert die erste ausgehende Kommunikation. Diese erste Kommunikation ruft das von anderen Agents für die Kommunikation mit Azure verwendete MSI-Zertifikat (Managed Service Identity, Verwaltete Dienstidentität) ab. |
        | `deployment.apps/config-agent` | Überwacht den verbundenen Cluster auf Ressourcen zur Konfiguration der Quellcodeverwaltung, die auf den Cluster angewendet werden. Aktualisiert den Compliancezustand. |
        | `deployment.apps/controller-manager` | Ist ein Operator für Operatoren und koordiniert Interaktionen zwischen Azure Arc-Komponenten. |    
        | `deployment.apps/metrics-agent` | Sammelt Metriken anderer Arc-Agents, um auf optimale Leistung zu überprüfen. |
        | `deployment.apps/cluster-metadata-operator` | Sammelt Clustermetadaten, einschließlich Clusterversion, Knotenanzahl und Version des Azure Arc-Agents. |
        | `deployment.apps/resource-sync-agent` | Synchronisiert die oben erwähnten Clustermetadaten mit Azure. |
        | `deployment.apps/flux-logs-agent` | Sammelt Protokolle von den bei der Konfiguration der Quellcodeverwaltung bereitgestellten Flux-Operatoren. |
        | `deployment.apps/extension-manager` | Installiert Erweiterungs-Helm-Diagramme und verwaltet deren Lebenszyklus |  
        | `deployment.apps/clusterconnect-agent` | Der Reverseproxy-Agent, der ermöglicht, dass das Cluster Connect-Feature Zugriff auf den `apiserver` des Clusters bereitstellt. Dies ist eine optionale Komponente, die nur dann bereitgestellt wird, wenn das Feature `cluster-connect` im Cluster aktiviert ist.   |
        | `deployment.apps/guard` | Authentifizierungs- und Autorisierungs-Webhookserver, der für das AAD RBAC-Feature verwendet wird. Dies ist eine optionale Komponente, die nur dann bereitgestellt wird, wenn das Feature `azure-rbac` im Cluster aktiviert ist.   |

1. Sobald alle Kubernetes-Agent-Pods mit Azure Arc-Unterstützung sich im Zustand `Running` befinden, stellen Sie sicher, dass Ihr Cluster mit Azure Arc verbunden ist. Folgendes sollte angezeigt werden:
    * Eine Kubernetes-Ressource mit Azure Arc-Unterstützung in [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure verfolgt diese Ressource als Projektion des kundenseitig verwalteten Kubernetes-Clusters nach, nicht den eigentlichen Kubernetes-Cluster selbst.
    * Clustermetadaten (wie Kubernetes-Version, Agent-Version und Anzahl der Knoten) werden in der Kubernetes-Ressource mit Azure Arc-Unterstützung als Metadaten angezeigt.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Datenaustausch zwischen Clusterumgebung und Azure

| Datentyp | Szenario | Kommunikationsmodus |
| --------- | -------- | ------------------ |
| Kubernetes-Clusterversion | Clustermetadaten | Agent-Pushvorgänge zu Azure |
| Anzahl der Knoten im Cluster | Clustermetadaten | Agent-Pushvorgänge an Azure |
| Agent-Version | Clustermetadaten | Agent-Pushvorgänge an Azure |
| Kubernetes-Verteilungstyp | Clustermetadaten | Azure CLI-Pushvorgänge an Azure |
| Infrastrukturtyp (AWS/GCP/vSphere/...) | Clustermetadaten | Azure CLI-Pushvorgänge an Azure |
| vCPU-Anzahl der Knoten im Cluster | Abrechnung | Azure CLI-Pushvorgänge an Azure |
| Agent-Takt | Resource Health | Agent-Pushvorgänge an Azure |
| Ressourcenverbrauch (Arbeitsspeicher/CPU) nach Agents | Diagnose und Unterstützbarkeit | Agent-Pushvorgänge an Azure |
| Protokolle aller Agent-Container | Diagnose und Unterstützbarkeit | Agent-Pushvorgänge an Azure |
| Upgrade-Verfügbarkeit für Agent | Agent-Upgrade | Agent-Pullvorgänge von Azure |
| Gewünschter Zustand der Konfiguration: Git-Repository-URL, Flux-Operatorparameter, privater Schlüssel, Inhalt bekannter Hosts, HTTPS-Benutzername, Token oder Kennwort | Konfiguration | Agent-Pullvorgänge von Azure |
| Status der Flux-Operatorinstallation | Konfiguration | Agent-Pushvorgänge an Azure |
| Azure Policy-Zuweisungen, die Gatekeeper-Erzwingung innerhalb des Clusters benötigen. | Azure Policy | Agent-Pullvorgänge von Azure |
| Überwachungs- und Compliancestatus von Richtlinienerzwingungen im Cluster | Azure Policy | Agent-Pushvorgänge an Azure |
| Metriken und Protokolle von Kundenworkloads | Azure Monitor | Agent-Pushvorgänge an Log Analytics-Arbeitsbereichsressource im Mandanten und Abonnement des Kunden. |

## <a name="connectivity-status"></a>Konnektivitätsstatus

| Status | BESCHREIBUNG |
| ------ | ----------- |
| Verbindung | Kubernetes-Ressource mit Azure Arc-Unterstützung wird in Azure Resource Manager erstellt, aber der Dienst hat noch nicht den Agent-Takt empfangen. |
| Verbunden | Kubernetes-Dienst mit Azure Arc-Unterstützung hat irgendwann während der vorangegangenen 15 Minuten einen Agent-Takt empfangen. |
| Offline | Kubernetes-Ressource mit Azure Arc-Unterstützung war zuvor verbunden, aber der Dienst hat 15 Minuten lang keinen Agent-Takt mehr empfangen. |
| Abgelaufen | Das Ablauffenster des MSI-Zertifikats nach seiner Ausgabe beträgt 90 Tage. Nach Ablauf dieses Zertifikats wird die Ressource als `Expired` betrachtet, und alle Features wie Konfiguration, Überwachung und Richtlinie stellen in diesem Cluster die Funktion ein. Weitere Informationen zum Behandeln abgelaufener Kubernetes-Ressourcen mit Azure Arc-Unterstützung finden Sie [im Artikel zu häufig gestellten Fragen](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Grundlegendes zu Konnektivitätsmodi

| Konnektivitätsmodus | BESCHREIBUNG |
| ----------------- | ----------- |
| Vollständig verbunden | Agents können mit geringer Verzögerung bei der Weitergabe von GitOps-Konfigurationen, beim Erzwingen von Azure Policy- und Gatekeeper-Richtlinien und Sammeln von Workloadmetriken und -protokollen in Azure Monitor konsistent mit Azure kommunizieren. |
| Halb verbunden | Das von `clusteridentityoperator` abgerufene MSI-Zertifikat ist maximal für 90 Tage gültig, bevor es abläuft. Beim Ablauf stellt die Kubernetes-Ressource mit Azure Arc-Unterstützung den Betrieb ein. Um alle Azure Arc-Features auf dem Cluster zu reaktivieren, löschen Sie die Kubernetes-Ressource mit Azure Arc-Unterstützung und die Agents, und erstellen Sie sie neu. Stellen Sie während der 90 Tage mindestens einmal alle 30 Tage eine Verbindung mit dem Cluster her. |
| Getrennt | Kubernetes-Cluster in nicht verbundenen Umgebungen ohne Zugriff auf Azure werden zurzeit von Kubernetes mit Azure Arc-Unterstützung nicht unterstützt. Wenn diese Funktion für Sie von Interesse ist, reichen Sie eine Idee im [UserVoice-Forum von Azure Arc](https://feedback.azure.com/d365community/forum/5c778dec-0625-ec11-b6e6-000d3a4f0858) ein, oder stimmen Sie dort für eine Idee ab.

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie den Schnellstart zum [Verbinden eines Kubernetes-Clusters mit Azure Arc](./quickstart-connect-cluster.md) durch.
* Erfahren Sie mehr über das Herstellen von Verbindungen zwischen Ihrem Cluster und einem Git-Repository als [Konfigurationsressource mit Kubernetes mit Azure Arc-Unterstützung](./conceptual-configurations.md).
