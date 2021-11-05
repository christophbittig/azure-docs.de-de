---
title: OSS-Beobachtbarkeit für OSM
description: Konfigurieren von Beobachtbarkeit in Open-Source-Software für Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 5b8e056cd360a66c42324292d7e40e8fb25ce668
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066879"
---
# <a name="manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Manuelles Bereitstellen von Prometheus, Grafana und Jaeger zur Anzeige von Open Service Mesh (OSM)-Metriken für die Beobachtbarkeit

> [!WARNING]
> Die Installation von Prometheus, Grafana und Jaeger dient als allgemeine Anleitung, um zu zeigen, wie diese Tools zur Anzeige von OSM-Metrikdaten verwendet werden können. Die Installationsanleitung ist nicht für einen Produktionseinrichtung zu verwenden. Bitte lesen Sie in der Dokumentation des jeweiligen Tools nach, wie Sie die Installationen am besten an Ihre Bedürfnisse anpassen. Am bemerkenswertesten ist das Fehlen von persistentem Speicher, was bedeutet, dass alle Daten verloren gehen, sobald ein Prometheus-Grafana- und/oder Jaeger-Pod(s) beendet wird.

Open Service Mesh (OSM) generiert detaillierte Metriken in Bezug auf den gesamten Datenverkehr innerhalb des Mesh. Diese Metriken bieten Einblicke in das Verhalten von Anwendungen im Mesh und helfen Anwendern bei der Problembehandlung, Wartung und Analyse ihrer Anwendungen.

Ab heute sammelt OSM Metriken direkt von den Sidecar-Proxys (Envoy). OSM bietet umfangreiche Metriken für den ein- und ausgehenden Verkehr für alle Dienste im Mesh. Mit diesen Metriken kann der Benutzer-Informationen über das Gesamtvolumen des Datenverkehrs, Fehler innerhalb des Datenverkehrs und die Antwortzeit für Anfragen erhalten.

OSM verwendet Prometheus, um konsistente Verkehrsmetriken und Statistiken für alle im Mesh laufenden Anwendungen zu sammeln und zu speichern. Prometheus ist ein Open-Source-Überwachungs- und Alarmierungs-Toolkit, das häufig in (aber nicht nur) Kubernetes- und Service Mesh-Umgebungen eingesetzt wird.

Jede Anwendung, die Teil des Mesh ist, läuft in einem Pod, der einen Envoy-Sidecar enthält, der Metriken (Proxy-Metriken) im Prometheus-Format bereitstellt. Außerdem verfügt jeder Pod, der Teil des Netzes ist, über Prometheus-Annotationen, was es dem Prometheus-Server ermöglicht, die Anwendung dynamisch zu scrapen. Dieser Mechanismus ermöglicht automatisch das Scraping von Metriken, wenn ein neuer Namespace/Pod/Service zum Mesh hinzugefügt wird.

OSM-Metriken können mit Grafana, einer Open-Source-Visualisierungs- und Analysesoftware, angezeigt werden. Damit können Sie Ihre Metriken abfragen, visualisieren und untersuchen sowie Warnungen erstellen.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen und Bereitstellen einer Prometheus-Instanz
> - Aktualisieren die `Configmap` von Prometheus
> - Erstellen und Bereitstellen einer Grafana-Instanz
> - Konfigurieren Sie Grafana mit der Prometheus-Datenquelle
> - Aktivieren von Prometheus-Metriken für einen Benutzernamespace
> - Importieren des OSM-Dashboards für Grafana
> - Erstellen und Bereitstellen einer Jaeger-Instanz
> - Konfigurieren der Jaeger-Ablaufverfolgung für OSM

## <a name="before-you-begin"></a>Voraussetzungen

Die folgenden Ressourcen müssen installiert sein:

- Azure CLI, Version 2.20.0 oder höher
- OSM, Version 0.11.1 oder höher
- JSON-Prozessor „jq“ ab Version 1.6

## <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Bereitstellen und Konfigurieren einer Prometheus-Instanz für OSM

Wir werden Helm verwenden, um die Prometheus-Instanz bereitzustellen. Führen Sie die folgenden Befehle aus, um Prometheus über Helm zu installieren:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Wenn die Installation erfolgreich war, sollte unten eine ähnliche Ausgabe angezeigt werden. Notieren Sie sich den Port des Prometheus-Servers und den DNS-Namen des Clusters. Diese Informationen werden später verwendet, um Prometheus als Datenquelle für Grafana zu konfigurieren.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

### <a name="update-the-prometheus-configmap"></a>Aktualisieren der Prometheus-Configmap

Prometheus ist standardmäßig so festgelegt, dass es die OSM-Komponenten auslesen kann. Die Standardinstallation von Prometheus enthält zwei Kubernetes-`configmaps`. Sie können die Liste der Prometheus-`configmaps` mit dem folgenden Befehl anzeigen.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Wir müssen die prometheus.yml-Konfiguration, die sich in der `configmap` **stable-prometheus-server** befindet, durch die folgende OSM-Konfiguration ersetzen. Es gibt verschiedene Techniken zur Dateibearbeitung, um diese Aufgabe zu erfüllen. Ein einfacher und sicherer Weg ist es, die `configmap` zu exportieren, eine Kopie davon zur Sicherung zu erstellen und diese dann mit einem Editor wie Visual Studio Code zu bearbeiten.

> [!NOTE]
> Wenn Sie Visual Studio Code nicht installiert haben, können Sie es [hier](https://code.visualstudio.com/Download)herunterladen und installieren.

Lassen Sie uns zunächst die Configmap von **stable-prometheus-server** exportieren und dann eine Kopie zur Sicherung erstellen.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Öffnen Sie als nächstes die Datei mit Visual Studio Code, um Sie zu bearbeiten.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Nachdem Sie die `configmap` im Visual Studio Code-Editor geöffnet haben, ersetzen Sie die Datei „Prometheus.yml“ durch die unten stehende OSM-Konfiguration, und speichern Sie die Datei.

> [!WARNING]
> Es ist äußerst wichtig, dass Sie darauf achten, dass Sie die Einziehungsstruktur der yaml-Datei beibehalten. Änderungen an der Struktur der yaml-Datei können dazu führen, dass die configmap nicht mehr angewendet werden kann.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Wenden Sie die aktualisierte `configmap`-YAML-Datei mit dem folgenden Befehl an.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Möglicherweise erhalten Sie eine Meldung über eine fehlende benötigte Kubernetes-Anmerkung. Dies kann vorerst ignoriert werden.

### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Überprüfen Sie, ob Prometheus für das Scrapen des OSM-Netzes und der API-Endpunkte konfiguriert ist

Um zu überprüfen, ob Prometheus richtig konfiguriert ist, um das OSM-Mesh und die API-Endpunkte zu scrapen, werden wir zum Prometheus-Pod portieren und die Zielkonfiguration anzeigen. Führen Sie die folgenden Befehle aus:

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Öffnen Sie einen Browser bis zu `http://localhost:9090/targets`

Wenn Sie nach unten scrollen, sollten Sie alle SMI-Metrik-Endpunkte mit dem Status **AUF** sowie andere OSM-Metriken sehen, die wie unten abgebildet definiert sind.

![OSM Prometheus Zielmetriken Benutzeroberfläche Bild](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

## <a name="deploy-and-configure-a-grafana-instance-for-osm"></a>Bereitstellen und Konfigurieren einer Grafana-Instanz für OSM

Wir verwenden Helm zum Bereitstellen der Grafana-Instanz. Führen Sie die folgenden Befehle aus, um Prometheus über Helm zu installieren:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Als nächstes holen wir uns das Standard-Grafana-Passwort, um uns auf der Grafana-Seite anzumelden.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Aktualisieren des Grafana-Kennworts.

Als Nächstes rufen wir den Grafana-Pod ab, um das Grafana-Dashboard für die Anmeldung zu portieren.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l "app.kubernetes.io/name=grafana" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Öffnen Sie einen Browser bis zu `http://localhost:3000`

Geben Sie auf dem unten abgebildeten Anmeldebildschirm **Admin** als Benutzername ein, und verwenden Sie das zuvor erfasste Grafana-Kennwort.

![OSM Grafana Login-Seite UI-Bild](./media/aks-osm-addon/osm-grafana-ui-login.png)

### <a name="configure-the-grafana-prometheus-data-source"></a>Konfigurieren Sie die Grafana Prometheus-Datenquelle

Nachdem Sie sich erfolgreich in Grafana eingeloggt haben, ist der nächste Schritt, Prometheus als Datenquellen für Grafana hinzuzufügen. Navigieren Sie hierzu im Menü auf der linken Seite zum Konfigurationssymbol, und wählen Sie wie unten dargestellt die Option Datenquellen aus.

![OSM Grafana Datenquellen Seite UI Bild](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Klicken Sie auf die Schaltfläche **Datenquelle hinzufügen**, und wählen Sie unter Zeitreihen-Datenbanken

![Die OSM Grafana-Datenquellen Auswahlseite Benutzeroberfläche Bild](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Geben Sie auf der Seite **Konfigurieren Sie Ihre Prometheus-Datenquelle unten** den Kubernetes Cluster-voll qualifizierten Namen für den Prometheus-Dienst für die HTTP-URL-Einstellung ein. Der Standardport sollte `stable-prometheus-server.default.svc.cluster.local`sein. Sobald Sie den Endpunkt des Prometheus-Dienstes eingegeben haben, scrollen Sie zum Ende der Seite und wählen Sie **Speichern & Testen**. Sie sollten ein grünes Kontrollkästchen erhalten, das anzeigt, dass die Datenquelle funktioniert.

### <a name="enable-prometheus-metrics-for-a-user-namespace"></a>Aktivieren von Prometheus-Metriken für einen Benutzernamespace
Führen Sie den folgenden Befehl aus, um Prometheus zum Auslesen von Metriken aus einem Anwendungsnamespace zu konfigurieren.
```azurecli-interactive
osm metrics enable --namespace <app-namespace>
```

### <a name="importing-osm-dashboards"></a>Importieren von OSM-Dashboards

OSM-Dashboards sind über Folgendes verfügbar:

- [Unser Repository](https://github.com/openservicemesh/osm/tree/release-v0.11/charts/osm/grafana/dashboards)kann als JSON-BLOB über das Webverwaltungs-Portal importiert werden.
- oder [Online unter Grafana.com](https://grafana.com/grafana/dashboards/14145)

Um ein Dashboard zu importieren, suchen `+` Sie im Menü auf der linken Seite nach dem Zeichen, und wählen Sie aus `import` .
Sie können das Dashboard direkt anhand seiner ID importieren auf `Grafana.com`. `OSM Mesh Details`Das Dashboard verwendet beispielsweise ID `14145`, Sie können die ID direkt auf dem Formular verwenden und Folgendes auswählen `import` :

![OSM Grafana Dashboard Import Seite Benutzeroberfläche Bild](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Sobald Sie importieren auswählen, werden Sie automatisch in Ihr importiertes Dashboard gebracht.

![OSM Grafana Dashboard Mesh Details Seite Benutzeroberfläche Bild](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

## <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Bereitstellen und Konfigurieren eines Jaeger-Operators auf Kubernetes für OSM

[Jaeger](https://www.jaegertracing.io/) ist ein Open Source-Ablaufverfolgungs-System, das für die Überwachung und Problembehandlung verteilter Systeme verwendet wird. Es kann mit OSM als neue Instanz bereitgestellt werden oder Sie können Ihre eigene Instanz mitbringen. Mit den folgenden Anweisungen wird eine neue Instanz von Jaeger im Namespace auf dem`jaeger` AKS-Cluster bereitgestellt.

### <a name="deploy-jaeger-to-the-aks-cluster"></a>Bereitstellen von Jaeger auf dem AKS-Cluster

Erstellen Sie zunächst einen jaeger-Namespace:

```azurecli-interactive
kubectl create namespace jaeger
```

Wenden Sie das folgende Manifest an, um Jaeger zu installieren:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

### <a name="add-rbac-for-jaeger-sa"></a>Hinzufügen von RBAC für Jaeger SA

Wenden Sie die folgende RBAC an, um dem Jaeger-Dienstkonto die angegebene Clusterrolle zu gewähren:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    app: jaeger
  name: jaeger
  namespace: jaeger
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  labels:
    app: jaeger
  name: jaeger
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: jaeger
  labels:
    app: jaeger
subjects:
  - kind: ServiceAccount
    name: jaeger
    namespace: jaeger
roleRef:
  kind: ClusterRole
  name: jaeger
  apiGroup: rbac.authorization.k8s.io
```

### <a name="enable-tracing-for-the-osm-add-on"></a>Aktivieren der Ablaufverfolgung für das OSM-Add-on

Als nächstes müssen wir die Ablaufverfolgung für das OSM-Add-on aktivieren.

Führen Sie den folgenden Befehl aus, um die Ablaufverfolgung für das OSM-Add-on zu aktivieren:

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true, "address": "jaeger.jaeger.svc.cluster.local"}}}}' --type=merge
```

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Anzeigen der Jaeger-Benutzeroberfläche mit Portweiterleitung

Die Benutzeroberfläche von Jaeger läuft auf Port 16686. Um die Web-Benutzeroberfläche anzuzeigen, können Sie kubectl Port-Forward verwenden:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

Im Browser sollten Sie ein Service-Dropdown sehen, mit dem Sie aus den verschiedenen Anwendungen auswählen können, die von der Buchladen-Demo bereitgestellt werden. Wählen Sie einen Dienst aus, um alle Spans davon anzuzeigen. Wenn Sie z. B. `bookbuyer` mit einem Rückblick von einer Stunde auswählen, können Sie die Interaktionen mit bookstore-v1 und bookstore-v2 nach Zeit sortiert anzeigen.

![OSM Jaeger Verfolgungsseite UI-Bild](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Wählen Sie ein beliebiges Element aus, um es im Detail zu betrachten. Wählen Sie mehrere Elemente aus, um Spuren zu vergleichen. Zum Beispiel können Sie die Interaktionen des `bookbuyer` mit Buchhandlung und Buchhandlung-v2 zu einem bestimmten Zeitpunkt vergleichen.

Sie können auch die Registerkarte Systemarchitektur auswählen, um eine Grafik der Interaktion/Kommunikation der verschiedenen Anwendungen anzuzeigen. Dies bietet einen Überblick darüber, wie der Datenverkehr zwischen den Anwendungen fließt.

![OSM Jaeger Systemarchitektur Benutzeroberfläche Bild](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)
