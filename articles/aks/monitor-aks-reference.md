---
title: Referenz zur Überwachung von AKS-Daten
description: Wichtiges Referenzmaterial für die Überwachung von AKS
ms.service: container-service
ms.custom: subject-monitoring
ms.date: 07/29/2021
ms.topic: reference
ms.openlocfilehash: 9e8a12f8d3075bade1619ce766c74dc8cac23481
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816054"
---
# <a name="monitoring-aks-data-reference"></a>Referenz zur Überwachung von AKS-Daten

Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für AKS finden Sie unter [Überwachen von AKS](monitor-aks.md).

## <a name="metrics"></a>Metriken

In der folgenden Tabelle werden die für AKS gesammelten Plattformmetriken aufgeführt.  Unter den einzelnen Links finden Sie jeweils eine ausführliche Liste der Metriken für jeden Typ.

|Metriktyp | Ressourcenanbieter/Typnamespace<br/> und Link zu einzelnen Metriken |
|-------|-----|
| Verwaltete Cluster | [Microsoft.ContainerService/managedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters)
| Verbundene Cluster | [microsoft.kubernetes/connectedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftkubernetesconnectedclusters)
| Virtuelle Computer| [Microsoft.Compute/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
| VM-Skalierungsgruppen | [Microsoft.Compute/virtualMachineScaleSets](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
| VM-Skalierungsgruppen/VMs | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

Weitere Informationen finden Sie in der Liste [aller in Azure Monitor unterstützten Plattformmetriken](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Metrikdimensionen

In der folgenden Tabelle sind [Dimensionen](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics) für AKS-Metriken aufgeführt. 

<!-- listed here /azure/azure-monitor/essentials/metrics-supported#microsoftcontainerservicemanagedclusters-->

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| requestKind | Wird von Metriken wie *Inflightanforderungen* verwendet, um nach Anforderungstyp zu teilen |
| condition | Wird von Metriken wie *Status für verschiedene Knotenbedingungen* und *Anzahl der Pods mit dem Status „Bereit“* verwendet, um nach Bedingungstyp zu teilen |
| status | Wird von Metriken wie *Status für verschiedene Knotenbedingungen* verwendet, um nach Bedingungsstatus zu teilen |
| status2 | Wird von Metriken wie *Status für verschiedene Knotenbedingungen* verwendet, um nach Bedingungsstatus zu teilen  |
| Knoten | Wird von Metriken wie *CPU-Auslastung (Millicore)* verwendet, um nach Knotennamen zu teilen |
| phase | Wird von Metriken wie *Anzahl der Pods nach Phase* verwendet, um nach Podphase zu teilen |
| Namespace | Wird von Metriken wie *Anzahl der Pods nach Phase* verwendet, um nach Namespace des Pods zu teilen |
| Pod | Wird von Metriken wie *Anzahl der Pods nach Phase* verwendet, um nach Podname zu teilen |
| nodepool | Wird von Metriken wie *Datenträgerverwendung (Bytes)* verwendet, um nach Name des Knotenpools zu teilen |
| device | Wird von Metriken wie *Datenträgerverwendung (Bytes)* verwendet, um nach Name des Geräts zu teilen |

## <a name="resource-logs"></a>Ressourcenprotokolle

In der folgenden Tabelle sind die Kategorien der Ressourcenprotokolle aufgeführt, die Sie für AKS sammeln können. Dabei handelt es sich um die Protokolle für AKS-Steuerungsebenenkomponenten. Informationen zum Erstellen einer Diagnoseeinstellung zum Sammeln dieser Protokolle und Empfehlungen für die Aktivierung finden Sie unter [Konfigurieren der Überwachung](monitor-aks.md#configure-monitoring). Abfragebeispiele finden Sie unter [Abfragen von Protokollen aus Container Insights](../azure-monitor/containers/container-insights-log-query.md#resource-logs).

Eine Referenz finden Sie in der Liste [aller Typen von Ressourcenprotokollkategorien, die in Azure Monitor unterstützt werden](../azure-monitor/essentials/resource-logs-schema.md). 

| Category                | BESCHREIBUNG |
|:---|:---|
| cluster-autoscaler       | Hiermit können Sie nachvollziehen, warum der möglicherweise unerwartet AKS-Cluster hoch- oder herunterskaliert wird. Diese Informationen sind auch nützlich, um Zeitintervalle zu korrelieren, in denen möglicherweise etwas Relevantes im Cluster passiert ist. |
| guard                   | Diese Kategorie ist für verwaltete Azure Active Directory- und Azure RBAC-Überwachungen vorgesehen. Für verwaltete Azure AD-Instanzen schließt dies eingehende Token und ausgehende Benutzerdaten ein. Für Azure RBAC schließt dies eingehende und ausgehende Zugriffsüberprüfungen ein. |
| kube-apiserver          | Protokolle vom API-Server |
| kube-audit              | Überwachungsprotokolldaten für alle Überwachungsereignisse, einschließlich get, list, create, update, delete, patch und post |
| kube-audit-admin        | Unterkategorie von kube-audit Hiermit wird die Anzahl der Protokolle erheblich verringert, indem die get- und list-Überwachungsereignisse aus dem Protokoll ausgeschlossen werden. |
| kube-controller-manager | Hiermit erhalten Sie einen tieferen Einblick in Probleme, die zwischen Kubernetes und der Azure-Steuerungsebene auftreten können. Ein typisches Beispiel ist ein AKS-Cluster mit fehlenden Berechtigungen für die Interaktion mit Azure. |
| kube-scheduler          | Protokolle aus dem Scheduler |
| AllMetrics              | Diese Kategorie enthält alle Plattformmetriken. Diese Werte werden an den Log Analytics-Arbeitsbereich gesendet, wo sie mithilfe von Protokollabfragen mit anderen Daten ausgewertet werden können. |

## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen

Dieser Abschnitt bezieht sich auf alle Tabellen in Azure Monitor Logs, die für AKS relevant sind und durch Log Analytics abgefragt werden können. 



|Ressourcentyp | Hinweise |
|-------|-----|
| [Kubernetes-Dienste](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) | Unter den einzelnen Links finden Sie eine ausführliche Liste der von AKS verwendeten Tabellen und eine Erläuterung für deren Struktur. |


Eine Referenz zu allen Azure Monitor-Protokollen und Log Analytics-Tabellen finden Sie in der [Referenz zu Tabellen in Azure Monitor-Protokollen](/azure/azure-monitor/reference/tables/tables-resourcetype).


## <a name="activity-log"></a>Aktivitätsprotokoll

In der folgenden Tabelle finden Sie einige Beispielvorgänge in Zusammenhang mit AKS, die im [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) erstellt werden können. Verwenden Sie das Aktivitätsprotokoll, um Informationen zu erfassen und zu verfolgen, z. B. wann ein Cluster erstellt wird oder seine Konfiguration geändert wurde. Sie können diese Informationen entweder im Portal anzeigen oder eine Aktivitätsprotokollwarnung erstellen, um proaktiv benachrichtigt zu werden, wenn ein Ereignis auftritt.

| Vorgang | BESCHREIBUNG |
|:---|:---|
| Microsoft.ContainerService/managedClusters/write | Verwalteten Cluster erstellen oder aktualisieren |
| Microsoft.ContainerService/managedClusters/delete | Verwalteten Cluster löschen |
| Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | clusterMonitoringUser-Anmeldeinformationen auflisten |
| Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | clusterAdmin-Anmeldeinformationen auflisten |
| Microsoft.ContainerService/managedClusters/agentpools/write | Agentpool erstellen oder aktualisieren |

Eine vollständige Liste der möglichen Protokolleinträge finden Sie unter [Vorgänge für Azure-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).

Weitere Informationen zum Schema von Aktivitätsprotokolleinträgen finden Sie unter [Aktivitätsprotokollschema](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von AKS finden Sie unter [Überwachen von AKS](monitor-aks.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).