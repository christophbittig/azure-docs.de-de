---
title: Azure Kubernetes-Netzwerkrichtlinien | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über Kubernetes-Netzwerkrichtlinien, um den Kubernetes-Cluster zu schützen.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: f8a8e5785af1bd74fb958a315de379facdc87171
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575970"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Übersicht über Azure Kubernetes-Netzwerkrichtlinien

Netzwerkrichtlinien bieten Mikrosegmentierung für Pods, genau wie Netzwerksicherheitsgruppen (NSGs) Mikrosegmentierung für VMs bieten. Die Implementierung des Azure-Netzwerkrichtlinienmanagers (Network Policy Manager, NPM) unterstützt die Standardspezifikation der Kubernetes-Netzwerkrichtlinie. Sie können Bezeichnungen verwenden, um eine Gruppe von Pods auszuwählen und eine Liste von Ein- und Ausgangsregeln zu definieren, damit Sie den eingehenden und ausgehenden Datenverkehr für diese Pods filtern können. Weitere Informationen zu Kubernetes-Netzwerkrichtlinien finden Sie in der [Kubernetes-Dokumentation](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Übersicht über Kubernetes-Netzwerkrichtlinien](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Die Azure-NPM-Implementierung funktioniert in Verbindung mit Azure CNI, wodurch die VNet-Integration für Container ermöglicht wird. Der NPM wird derzeit nur unter Linux unterstützt. Die Implementierung erzwingt das Filtern von Datenverkehr durch das Konfigurieren, Zulassen und Ablehnen von IP-Regeln in Linux IPTables basierend auf den definierten Richtlinien. Diese Regeln werden mithilfe von Linux IPSets gruppiert.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planen der Sicherheit Ihres Kubernetes-Clusters
Wenn Sie die Sicherheit Ihres Clusters implementieren, verwenden Sie Netzwerksicherheitsgruppen, um den eingehenden und ausgehenden Datenverkehr des Clustersubnetzes zu filtern (Nord-Süd-Datenverkehr). Verwenden Sie Azure-NPM für den Datenverkehr zwischen Pods im Cluster (Ost-West-Datenverkehr).

## <a name="using-azure-npm"></a>Verwenden des Azure-NPM
Der Azure-NPM kann auf folgende Weise verwendet werden, um eine Mikrosegmentierung für Pods zu ermöglichen.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
Der NPM ist nativ in AKS verfügbar und kann während der Clustererstellung aktiviert werden. Weitere Informationen finden Sie unter [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AKS-Engine
Die AKS-Engine (Azure Container Service, Azure Kubernetes Service) ist ein Tool, das eine Azure Resource Manager-Vorlage zum Bereitstellen eines Kubernetes-Clusters in Azure generiert. Die Clusterkonfiguration wird in einer JSON-Datei angegeben, die beim Generieren der Vorlage an das Tool übergeben wird. Weitere Informationen zur gesamten Liste der unterstützten Clustereinstellungen und deren Beschreibungen finden Sie in der Clusterdefinition für die Azure Container Service-Engine (AKS).

Um Richtlinien für Cluster zu aktivieren, die mit der AKS-Engine bereitgestellt werden, geben Sie für den Wert der „networkPolicy“-Einstellung in der Clusterdefinitionsdatei „azure“ an.

#### <a name="example-configuration"></a>Beispielkonfiguration

Die folgende JSON-Beispielkonfiguration erstellt ein neues virtuelles Netzwerk und Subnetz und stellt darin einen Kubernetes-Cluster mit Azure CNI bereit. Wir empfehlen, dass Sie die JSON-Datei im Editor bearbeiten. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Eigenständiges Erstellen von Kubernetes-Clustern in Azure
 Installieren Sie bei eigenständig erstellten Clustern zunächst das CNI-Plug-In, und aktivieren Sie es auf jeder VM in einem Cluster. Ausführliche Anweisungen finden Sie unter [Bereitstellen des Plug-Ins für einen eigenen Kubernetes-Cluster](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Wenn der Cluster bereitgestellt wurde, führen Sie den folgenden `kubectl`-Befehl aus, um den Azure-NPM *daemon set* herunterzuladen und auf den Cluster anzuwenden.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Die Lösung ist ebenfalls Open Source, und der Code ist im [Azure-Containernetzwerkrepository](https://github.com/Azure/azure-container-networking/tree/master/npm) verfügbar.

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Überwachen und Visualisieren von Netzwerkkonfigurationen mit dem Azure-NPM
Der Azure-NPM enthält informative Prometheus-Metriken, mit denen Sie die Konfigurationen überwachen und besser verstehen können. Er bietet integrierte Visualisierungen im Azure-Portal oder in Grafana Labs. Sie können diese Metriken entweder mithilfe von Azure Monitor oder eines Prometheus-Servers sammeln.

### <a name="benefits-of-azure-npm-metrics"></a>Vorteile von Azure-NPM-Metriken
Benutzer konnten zuvor nur mit dem in einem Clusterknoten ausgeführten `iptables -L`-Befehl mehr über die Netzwerkkonfiguration erfahren. Dieser Befehl erzeugte eine ausführliche und schwer verständliche Ausgabe. NPM-Metriken bieten die folgenden Vorteile im Zusammenhang mit Netzwerkrichtlinien, IPTables-Regeln und IPSets.
- Einblicke in die Beziehung dieser drei Komponenten und eine Zeitdimension zum Debuggen einer Konfiguration
- Anzahl der Einträge in allen IPSets und den einzelnen IPSets
- Benötigte Zeit für das Anwenden einer Richtlinie mit der Granularität der IPTable-/IPSet-Ebene
 
### <a name="supported-metrics"></a>Unterstützte Metriken
Liste der unterstützten Metriken:

|Metrikname |BESCHREIBUNG  |Prometheus-Metriktyp  |Bezeichnungen  |
|---------|---------|---------|---------|
|`npm_num_policies`     |Anzahl der Netzwerkrichtlinien          |Maßstab         |-         |
|`npm_num_iptables_rules`     | Anzahl der IPTables-Regeln     | Maßstab        |-         |         
|`npm_num_ipsets`     |Anzahl von IPSets         |Maßstab            |-         |
|`npm_num_ipset_entries`     |Anzahl der IP-Adresseinträge in allen IPSets         |Maßstab         |-         |
|`npm_add_policy_exec_time`     |Laufzeit zum Hinzufügen einer Netzwerkrichtlinie         |Zusammenfassung         |Quan­til (0,5, 0,9 oder 0,99)         |
|`npm_add_iptables_rule_exec_time`     |Laufzeit zum Hinzufügen einer IPTables-Regel         |Zusammenfassung         |Quan­til (0,5, 0,9 oder 0,99)         |
|`npm_add_ipset_exec_time`     |Laufzeit zum Hinzufügen von IPSet         |Zusammenfassung         |Quan­til (0,5, 0,9 oder 0,99)         |
|`npm_ipset_counts` (erweitert)     |Anzahl von Einträgen in jedem einzelnen IPSet         |GaugeVec         |Name und Hash festlegen         |

Mit den verschiedenen Quantilebenen in den „exec_time“-Metriken können Sie zwischen allgemeinen und ungünstigen Szenarios unterscheiden.

Es gibt auch eine Metrik „exec_time_count“ und „exec_time_sum“ für die einzelnen Übersichtsmetriken „exec_time“.

Die Metriken können mit Azure Monitor für Container oder Prometheus ausgelesen werden.

### <a name="setup-for-azure-monitor"></a>Einrichten von Azure Monitor
Als Erstes müssen Sie Azure Monitor für Container für die Kubernetes-Cluster aktivieren. Die Schritte werden unter [Azure Monitor für Container – Übersicht](../azure-monitor/containers/container-insights-overview.md) beschrieben. Konfigurieren Sie nach der Aktivierung von Azure Monitor für Container [ConfigMap von Azure Monitor für Container](https://aka.ms/container-azm-ms-agentconfig), um die NPM-Integration und Sammlung von Prometheus-NPM-Metriken zu aktivieren. ConfigMap von Azure Monitor für Container verfügt über einen ```integrations```-Abschnitt mit Einstellungen zum Sammeln von NPM-Metriken. Diese Einstellungen sind in ConfigMap standardmäßig deaktiviert. Durch das Aktivieren der Grundeinstellung ```collect_basic_metrics = true``` werden grundlegende NPM-Metriken gesammelt. Wenn Sie die erweiterte Einstellung ```collect_advanced_metrics = true``` aktivieren, werden zusätzlich zu den grundlegenden Metriken erweiterte Metriken gesammelt. 

Nachdem Sie ConfigMap bearbeitet haben, speichern Sie die Datei lokal, und wenden Sie ConfigMap wie folgt auf den Cluster an.

`kubectl apply -f container-azm-ms-agentconfig.yaml`

 Nachfolgend finden Sie einen Codeausschnitt von [ConfigMap von Azure Monitor für Container](https://aka.ms/container-azm-ms-agentconfig), der die aktivierte NPM-Integration mit der erweiterten Sammlung von Metriken darstellt.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Erweiterte Metriken sind optional. Wenn Sie sie aktivieren, wird die grundlegende Sammlung von Metriken automatisch aktiviert. Erweiterte Metriken enthalten derzeit nur `npm_ipset_counts`.

Weitere Informationen finden Sie unter [Sammlungseinstellungen in ConfigMap von Azure Monitor für Container](../azure-monitor/containers/container-insights-agent-config.md).

### <a name="visualization-options-for-azure-monitor"></a>Visualisierungsoptionen für Azure Monitor
Nach der Aktivierung der Sammlung der NPM-Metriken können Sie die Metriken im Azure-Portal mithilfe von Container Insights oder in Grafana anzeigen.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Anzeigen in Azure-Portal unter Insights für den Cluster
Öffnen Sie das Azure-Portal. Navigieren Sie in Insights des Clusters zu „Arbeitsmappen“, und öffnen Sie die „NPM-Konfiguration“.

Neben dem Anzeigen der Arbeitsmappe (unten aufgeführte Abbildungen) können Sie die Prometheus-Metriken auch direkt in den Protokollen im Abschnitt „Insights“ abfragen. Diese Abfrage gibt z. B. alle gesammelten Metriken zurück.
| where TimeGenerated > ago(5h) | where Name contains "npm_"

Sie können die Metriken auch direkt in Log Analytics abfragen. Weitere Informationen finden Sie unter [Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/containers/container-insights-log-query.md). 

#### <a name="viewing-in-grafana-dashboard"></a>Anzeigen im Grafana-Dashboard
Richten Sie einen Grafana-Server ein und konfigurieren Sie wie unter [diesem Link](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource) beschrieben eine Log Analytics-Datenquelle. Importieren Sie dann das [Grafana-Dashboard mit einem Log Analytics-Back-End](https://grafana.com/grafana/dashboards/10956) in Grafana Labs.

Das Dashboard verfügt über visuelles Elemente ähnlich der Azure-Arbeitsmappe. Sie können Bereiche hinzufügen, um die NPM-Metriken aus der InsightsMetrics-Tabelle in einem Diagramm zu visualisieren.

### <a name="setup-for-prometheus-server"></a>Einrichten des Prometheus-Servers
Einige Benutzer sammeln Metriken mit einem Prometheus-Server, anstatt Azure Monitor für Container zu verwenden. Sie müssen lediglich zwei Aufträge in die ausgelesene Konfiguration hinzufügen, um NPM-Metriken zu sammeln.

Fügen Sie dieses Helm-Repository zum Cluster hinzu, um einen einfachen Prometheus-Server zu installieren.
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
Fügen Sie anschließend einen Server hinzu,
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
bei dem `prometheus-server-scrape-config.yaml` aus folgendem Code besteht:
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Sie können den Auftrag `azure-npm-node-metrics` auch durch den unten stehenden Inhalt ersetzen oder ihn in einen bereits vorhandenen Auftrag für Kubernetes-Pods einschließen:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Visualisierungsoptionen für Prometheus
Wenn Sie einen Prometheus-Server verwenden, wird nur das Grafana-Dashboard unterstützt. 

Wenn Sie dies noch nicht getan haben, richten Sie den Grafana-Server ein, und konfigurieren Sie eine Prometheus-Datenquelle. Importieren Sie dann das [Grafana-Dashboard mit einem Prometheus-Back-End](https://grafana.com/grafana/dashboards/13000) in Grafana Labs.

Die visuellen Elemente für dieses Dashboard sind identisch mit dem Dashboard mit einem Container Insights-/Log Analytics-Back-End.

### <a name="sample-dashboards"></a>Beispiele für Dashboards
Nachfolgend finden Sie einige Beispieldashboards für NPM-Metriken in Container Insights (CI) und Grafana.

#### <a name="ci-summary-counts"></a>Zusammenfassende Angaben in CI
![Zusammenfassende Angaben in der Azure-Arbeitsmappe](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Anzahl im Zeitverlauf in CI
[![Anzahl im Zeitverlauf in der Azure-Arbeitsmappe](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>IPSet-Einträge in CI
[![IPSet-Einträge in der Azure-Arbeitsmappe](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>Laufzeitquantile in CI
![Laufzeitquantile in der Azure-Arbeitsmappe](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Zusammenfassende Angaben auf dem Grafana-Dashboard
![Zusammenfassende Angaben auf dem Grafana-Dashboard](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Anzahl im Zeitverlauf auf dem Grafana-Dashboard
[![Anzahl im Zeitverlauf auf dem Grafana-Dashboard](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>IPSet-Einträge auf dem Grafana-Dashboard
[![IPSet-Einträge auf dem Grafana-Dashboard](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Laufzeitquantile auf dem Grafana-Dashboard
[![Laufzeitquantile auf dem Grafana-Dashboard](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Azure Kubernetes Service](../aks/intro-kubernetes.md)
-  Weitere Informationen zu [Containernetzwerken](container-networking-overview.md)
- [Bereitstellen des Plug-Ins](deploy-container-networking.md) für Kubernetes-Cluster oder Docker-Container
