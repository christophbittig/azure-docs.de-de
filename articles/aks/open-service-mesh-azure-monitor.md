---
title: Verwenden von Azure Monitor und Application Insights
description: Verwenden von Azure Monitor und Application Insights mit Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 4e1df1275e225b42bf01bd2a4092cea07f9c4c12
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057812"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Open Service Mesh (OSM)-Überwachung und-Observability mithilfe von Azure Monitor und Application Insights

Sowohl Azure Monitor als auch Azure Application Insights unterstützen Sie dabei, die Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste zu maximieren. Diese Dienste bieten eine umfassende Lösung für das Sammeln, Analysieren und Behandeln von Telemetriedaten aus Ihren Cloud- und lokalen Umgebungen.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Das OSM-AKS-Add-On verfügt über tiefe Integrationen in diese beiden Azure-Dienste und bietet eine nahtlose Azure-Erfahrung für die Anzeige von kritischen KPIs und die Reaktion auf diese kritische KPIs, die von OSM-Metriken bereitgestellt werden. 

## <a name="enable-azure-monitor"></a>Aktivieren von Azure Monitor

Nachdem das OSM-Add-On für AKS im AKS-Cluster aktiviert wurde, muss Azure Monitor über das Azure-Portal im Cluster aktiviert werden. Klicken Sie auf den AKS-Cluster, navigieren Sie zur Registerkarte „Erkenntnisse“ unter „Überwachung“, und wählen Sie „Aktivieren“ aus. 

Nachdem Azure Monitor aktiviert wurde, sollten die folgenden Protokolle im kube-system-Namespace angezeigt werden: 

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>Aktivieren von Metriken in von OSM überwachten Namespaces

Damit Metriken aus einem bestimmten Namespace, der vom Netz überwacht wird, erfasst werden können, muss der folgende Befehl ausgeführt werden:

```sh
osm metrics enable --osm-namespace <namespace>
```

Wenn Sie beispielsweise die [Buchladen-Demo](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/) ausführen, führen Sie den Befehl `osm metrics enable` für die folgenden Namespaces aus:

```sh
osm metrics enable --osm-namespace bookbuyer
osm metrics enable --osm-namespace bookstore
osm metrics enable --osm-namespace bookthief
osm metrics enable --osm-namespace bookwarehouse
```
## <a name="apply-configmaps"></a>Anwenden von ConfigMaps

Erstellen Sie die folgende ConfigMap in `kube-system`, um Azure Monitor mitzuteilen, welche Namespaces überwacht werden sollen. Für die Demo „Buchkäufer/Buchladen“ sieht die ConfigMap beispielsweise wie folgt aus: 

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system

```

Als Nächstes müssen Sie eine zweite ConfigMap erstellen, um [monitor_kubernetes_pods](https://github.com/microsoft/Docker-Provider/blob/24b709f9e3c3b18779102b491fc98b87a99d1335/kubernetes/container-azm-ms-agentconfig.yaml#L72) auf TRUE festzulegen.

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: container-azm-ms-agentconfig
  namespace: kube-system
data:
  schema-version: v1
  config-version: ver1
  prometheus-data-collection-settings: |-
    [prometheus_data_collection_settings.cluster]
        interval = "30s"
        monitor_kubernetes_pods = true
```

## <a name="view-metrics-in-the-azure-portal"></a>Anzeigen von Metriken im Azure-Portal

Wählen Sie im Azure-Portal den Kubernetes-Cluster und dann unter „Überwachung“ die Registerkarte „Protokolle“ aus. Sie sollten nun in der Lage sein, die Tabelle `InsightsMetrics` abzufragen, um Metriken in den aktivierten Namespaces anzuzeigen. Wenn Sie beispielsweise die Envoy-Metriken für `bookbuyer` anzeigen möchten, verwenden Sie die folgende Abfrage:

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>Zusätzliche Informationen

Weitere Informationen zum Aktivieren und Konfigurieren von Azure Monitor und Azure Application Insights für das OSM-Add-On für AKS finden Sie auf der Seite [Azure Monitor für OSM](https://aka.ms/azmon/osmpreview).