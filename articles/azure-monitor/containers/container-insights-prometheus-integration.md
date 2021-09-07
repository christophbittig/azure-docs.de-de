---
title: Konfigurieren der Prometheus-Integration in Container Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie den Container Insights-Agent so konfigurieren können, dass Metriken aus Prometheus mit Ihrem Kubernetes-Cluster abgerufen werden.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 441b468f71f0d134a503418b3fde64b758a033a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339613"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Konfigurieren des Abrufs von Prometheus-Metriken mit Container Insights

[Prometheus](https://prometheus.io/) ist eine beliebte Open-Source-Überwachungslösung für Metriken und Teil der [Cloud Native Compute Foundation](https://www.cncf.io/). Container Insights bietet eine nahtlos eingebundene Benutzeroberfläche für das Onboarding, über die Sie Prometheus-Metriken sammeln können. Um Prometheus verwenden zu können, müssen Sie normalerweise einen Prometheus-Server mit einem Speicher einrichten und verwalten. Durch die Integration in Azure Monitor ist kein Prometheus-Server erforderlich. Sie müssen lediglich den Prometheus-Metrikendpunkt über Ihre Exporter oder Pods (Anwendung) verfügbar machen, dann kann der Container-Agent für Container Insights die Metriken abrufen. 

![Architektur der Containerüberwachung für Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Die Mindestversion des Agents, die für das Abrufen von Prometheus-Metriken unterstützt wird, ist ciprod07092019 oder höher. Die Agent-Version, die für das Schreiben von Konfigurations- und Agent-Fehlern in die Tabelle `KubeMonAgentEvents` unterstützt wird, ist ciprod10112019. Die Agent-Version für Azure Red Hat OpenShift und Red Hat OpenShift v4 ist ciprod04162020 oder höher. 
>
>Weitere Informationen zu den Agent-Versionen und den Inhalten der jeweiligen Releases finden Sie in den [Versionshinweisen zum Agent](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). 
>Um die Agent-Version zu überprüfen, wählen Sie auf der Registerkarte **Knoten** einen Knoten aus, und notieren Sie im Eigenschaftenbereich den Wert der Eigenschaft **Agent-Imagetag**.

Das Scraping von Prometheus-Metriken wird bei Kubernetes-Clustern unterstützt, die in folgenden Diensten gehostet werden:

- Azure Kubernetes Service (AKS)
- Azure Stack oder lokal
- Azure Red Hat OpenShift Version 3.x
- Azure Red Hat OpenShift und Red Hat OpenShift Version 4.x

### <a name="prometheus-scraping-settings"></a>Prometheus-Abrufeinstellungen

Das aktive Abrufen von Metriken von Prometheus erfolgt aus einer von zwei Perspektiven:

* Clusterweit: HTTP-URL und ermittelte Ziele aus aufgelisteten Endpunkten eines Diensts. Beispielsweise k8s-Dienste wie kube-dns und kube-state-metrics sowie Podanmerkungen, die für eine Anwendung spezifisch sind. Die in diesem Kontext gesammelten Metriken werden im ConfigMap-Abschnitt *[Prometheus data_collection_settings.cluster]* definiert.
* Knotenweit: HTTP-URL und ermittelte Ziele aus aufgelisteten Endpunkten eines Diensts. Die in diesem Kontext gesammelten Metriken werden im ConfigMap-Abschnitt *[Prometheus_data_collection_settings.node]* definiert.

| Endpunkt | `Scope` | Beispiel |
|----------|-------|---------|
| Podanmerkung | Clusterweit | Anmerkungen: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes-Dienst | Clusterweit | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/Endpunkt | Pro Knoten und/oder clusterweit | `http://myurl:9101/metrics` |

Wenn eine URL angegeben wird, erfasst Container Insights nur den Endpunkt. Wenn „Kubernetes-Dienst“ angegeben wird, wird der Dienstname mit dem Cluster-DNS-Server aufgelöst, um die IP-Adresse abzurufen. Anschließend wird der aufgelöste Dienst abgerufen.

|`Scope` | Schlüssel | Datentyp | Wert | BESCHREIBUNG |
|------|-----|-----------|-------|-------------|
| Clusterweit | | | | Geben Sie eine der folgenden drei Methoden zum Abrufen von Endpunkten für Metriken an. |
| | `urls` | String | Durch Trennzeichen getrenntes Array | HTTP-Endpunkt (entweder IP-Adresse oder gültiger URL-Pfad angegeben). Beispiel: `urls=[$NODE_IP/metrics]`. („$NODE_IP“ ist ein spezifischer Container Insights-Parameter und kann anstelle der Knoten-IP-Adresse verwendet werden. Muss alles in Großbuchstaben sein.) |
| | `kubernetes_services` | String | Durch Trennzeichen getrenntes Array | Ein Array von Kubernetes Services zum Abrufen von Metriken aus „kube-state-metrics“. Hier müssen vollqualifizierte Domänennamen verwendet werden. Beispiel: `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace.svc.cluster.local:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | true oder false | Wenn die Option in den clusterweiten Einstellungen auf `true` festgelegt ist, ruft der Container Insights-Agent Kubernetes Pods aus dem gesamten Cluster für die folgenden Prometheus-Anmerkungen ab:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true oder false | Ermöglicht das Abrufen des Pods. `monitor_kubernetes_pods` muss auf `true` festgelegt werden. |
| | `prometheus.io/scheme` | String | HTTP oder HTTPS | Nimmt den Standardwert Abrufen über HTTP an. Legen Sie diesen Wert bei Bedarf auf `https` fest. | 
| | `prometheus.io/path` | String | Durch Trennzeichen getrenntes Array | Der HTTP-Ressourcenpfad, aus dem Metriken abgerufen werden sollen. Wenn der Metrikpfad nicht `/metrics` lautet, definieren Sie ihn mit dieser Anmerkung. |
| | `prometheus.io/port` | String | 9102 | Geben Sie einen Port an, von dem abgerufen werden soll. Wenn der Port nicht festgelegt ist, wird standardmäßig 9102 verwendet. |
| | `monitor_kubernetes_pods_namespaces` | String | Durch Trennzeichen getrenntes Array | Eine Positivliste der Namespaces zum Abrufen von Metriken von Kubernetes-Pods.<br> Zum Beispiel, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Knotenweit | `urls` | String | Durch Trennzeichen getrenntes Array | HTTP-Endpunkt (entweder IP-Adresse oder gültiger URL-Pfad angegeben). Beispiel: `urls=[$NODE_IP/metrics]`. („$NODE_IP“ ist ein spezifischer Container Insights-Parameter und kann anstelle der Knoten-IP-Adresse verwendet werden. Muss alles in Großbuchstaben sein.) |
| Knotenweit oder clusterweit | `interval` | String | 60s | Der Standardwert für das Sammlungsintervall ist eine Minute (60 Sekunden). Sie können die Sammlung entweder für *[prometheus_data_collection_settings.node]* und/oder für *[prometheus_data_collection_settings.cluster]* in Zeiteinheiten wie s, m, h ändern. |
| Knotenweit oder clusterweit | `fieldpass`<br> `fielddrop`| String | Durch Trennzeichen getrenntes Array | Sie können bestimmte Metriken angeben, die vom Endpunkt erfasst werden sollen oder nicht, indem Sie die Zulassungs- (`fieldpass`) und Sperrauflistung (`fielddrop`) festlegen. Sie müssen zunächst die Positivliste festlegen. |

ConfigMaps ist eine globale Liste, und es kann nur eine ConfigMap auf den Agent angewendet werden. Es kann keine andere ConfigMaps vorhanden sein, die die Sammlungen außer Kraft setzt.

## <a name="configure-and-deploy-configmaps"></a>Konfigurieren und Bereitstellen von ConfigMaps

Führen Sie die folgenden Schritte aus, um Ihre ConfigMap-Konfigurationsdatei für die folgenden Cluster zu konfigurieren:

* Azure Kubernetes Service (AKS)
* Azure Stack oder lokal
* Azure Red Hat OpenShift Version 4.x und Red Hat OpenShift Version 4.x

1. [Laden Sie die Vorlagendatei (YAML) für die ConfigMap herunter](https://aka.ms/container-azm-ms-agentconfig), und speichern Sie diese unter container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Dieser Schritt ist bei der Arbeit mit Azure Red Hat OpenShift nicht erforderlich, da die ConfigMap-Vorlage bereits im Cluster vorhanden ist.

2. Passen Sie die ConfigMap-Datei (YAML-Datei) für das Abrufen von Prometheus-Metriken wie gewünscht an.

    >[!NOTE]
    >Wenn Sie die ConfigMap-YAML-Datei für Azure Red Hat OpenShift bearbeiten, führen Sie zuerst den Befehl `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` aus, um die Datei in einem Text-Editor zu öffnen.

    >[!NOTE]
    >Die Anmerkung `openshift.io/reconcile-protect: "true"` muss in den Metadaten der ConfigMap *container-azm-ms-agentconfig* hinzugefügt werden, um einen Abgleich zu verhindern. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Zum Sammeln von Kubernetes-Diensten für den gesamten Cluster konfigurieren Sie die ConfigMap-Datei anhand des folgenden Beispiels.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Zum Konfigurieren des Abrufs von Prometheus-Metriken über eine bestimmte URL im gesamten Cluster konfigurieren Sie die ConfigMap-Datei anhand des folgenden Beispiels.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Zum Konfigurieren des Abrufs von Prometheus-Metriken aus dem DaemonSet eines Agents für jeden einzelnen Knoten im Cluster konfigurieren Sie Folgendes in der ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >„$NODE_IP“ ist ein spezifischer Container Insights-Parameter und kann anstelle der Knoten-IP-Adresse verwendet werden. Er darf nur Großbuchstaben enthalten. 

    - Führen Sie die folgenden Schritte aus, um das Abrufen von Prometheus-Metriken durch Angabe einer Podanmerkung zu konfigurieren:

       1. Geben Sie in der ConfigMap Folgendes an:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Geben Sie die folgende Konfiguration für Podanmerkungen an:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Wenn Sie die Überwachung auf bestimmte Namespaces für Pods beschränken möchten, die über Anmerkungen verfügen (z.B. nur Pods einschließen, die für Produktionsworkloads reserviert sind), legen Sie `monitor_kubernetes_pod` auf `true` in ConfigMap fest, und fügen Sie den Namespacefilter `monitor_kubernetes_pods_namespaces` zur Angabe der Namespaces hinzu, aus denen Metriken abgerufen werden sollen. Zum Beispiel, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Führen Sie den folgenden kubectl-Befehl aus: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Beispiel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

Es kann einige Minuten dauern, bis die Konfigurationsänderungen übernommen werden, und alle omsagent-Pods im Cluster werden neu gestartet. Der Neustart aller omsagent-Pods erfolgt gleitend, nicht alle werden gleichzeitig neu gestartet. Wenn die Neustarts abgeschlossen sind, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt: `configmap "container-azm-ms-agentconfig" created`.

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>Konfigurieren und Bereitstellen von ConfigMaps – Azure Red Hat OpenShift v3

Dieser Abschnitt enthält die Anforderungen und Schritte zum erfolgreichen Konfigurieren Ihrer ConfigMap-Konfigurationsdatei für den Azure Red Hat OpenShift v3.x-Cluster.

>[!NOTE]
>Für Azure Red Hat OpenShift v3.x wird eine ConfigMap-Vorlagendatei im Namespace *openshift-azure-logging* erstellt. Sie ist nicht für das aktive Scraping von Metriken oder die Datensammlung vom Agent konfiguriert.

### <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich vor dem Beginn, dass Sie Mitglied der Rolle „Customer Cluster Admin“ Ihres Azure Red Hat OpenShift-Clusters sind, um den Container-Agent und die Prometheus-Scrapingeinstellungen konfigurieren zu können. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Sie Mitglied der Gruppe *osa-customer-admins* sind:

``` bash
  oc get groups
```

Die Ausgabe ähnelt der folgenden:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Wenn Sie Mitglied der Gruppe *osa-customer-admins* sind, sollten Sie in der Lage sein, die ConfigMap `container-azm-ms-agentconfig` mit dem folgenden Befehl aufzulisten:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Die Ausgabe ähnelt der folgenden:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Aktivieren der Überwachung

Führen Sie die folgenden Schritte aus, um Ihre ConfigMap-Konfigurationsdatei für den Azure Red Hat OpenShift v3.x-Cluster zu konfigurieren.

1. Passen Sie die ConfigMap-Datei (YAML-Datei) für das Abrufen von Prometheus-Metriken wie gewünscht an. Die ConfigMap-Vorlage ist bereits auf dem Red Hat OpenShift v3-Cluster vorhanden. Führen Sie den Befehl `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` aus, um die Datei in einem Text-Editor zu öffnen.

    >[!NOTE]
    >Die Anmerkung `openshift.io/reconcile-protect: "true"` muss in den Metadaten der ConfigMap *container-azm-ms-agentconfig* hinzugefügt werden, um einen Abgleich zu verhindern. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Zum Sammeln von Kubernetes-Diensten für den gesamten Cluster konfigurieren Sie die ConfigMap-Datei anhand des folgenden Beispiels.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Zum Konfigurieren des Abrufs von Prometheus-Metriken über eine bestimmte URL im gesamten Cluster konfigurieren Sie die ConfigMap-Datei anhand des folgenden Beispiels.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Zum Konfigurieren des Abrufs von Prometheus-Metriken aus dem DaemonSet eines Agents für jeden einzelnen Knoten im Cluster konfigurieren Sie Folgendes in der ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >„$NODE_IP“ ist ein spezifischer Container Insights-Parameter und kann anstelle der Knoten-IP-Adresse verwendet werden. Er darf nur Großbuchstaben enthalten. 

    - Führen Sie die folgenden Schritte aus, um das Abrufen von Prometheus-Metriken durch Angabe einer Podanmerkung zu konfigurieren:

       1. Geben Sie in der ConfigMap Folgendes an:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Geben Sie die folgende Konfiguration für Podanmerkungen an:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Wenn Sie die Überwachung auf bestimmte Namespaces für Pods beschränken möchten, die über Anmerkungen verfügen (z.B. nur Pods einschließen, die für Produktionsworkloads reserviert sind), legen Sie `monitor_kubernetes_pod` auf `true` in ConfigMap fest, und fügen Sie den Namespacefilter `monitor_kubernetes_pods_namespaces` zur Angabe der Namespaces hinzu, aus denen Metriken abgerufen werden sollen. Zum Beispiel, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Speichern Sie Ihre Änderungen im Editor.

Es kann einige Minuten dauern, bis die Konfigurationsänderungen übernommen werden, und alle omsagent-Pods im Cluster werden neu gestartet. Der Neustart aller omsagent-Pods erfolgt gleitend, nicht alle werden gleichzeitig neu gestartet. Wenn die Neustarts abgeschlossen sind, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt: `configmap "container-azm-ms-agentconfig" created`.

Sie können die aktualisierte ConfigMap anzeigen, indem Sie den Befehl `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` ausführen. 

## <a name="applying-updated-configmap"></a>Anwenden der aktualisierten ConfigMap

Wenn Sie bereits eine ConfigMap in Ihrem Cluster bereitgestellt haben und sie mit einer neueren Konfiguration aktualisieren möchten, können Sie die zuvor verwendete ConfigMap-Datei bearbeiten und dann mit den gleichen Befehlen wie zuvor anwenden.

Für die folgenden Kubernetes-Umgebungen:

- Azure Kubernetes Service (AKS)
- Azure Stack oder lokal
- Azure Red Hat OpenShift und Red Hat OpenShift Version 4.x

führen Sie den Befehl `kubectl apply -f <configmap_yaml_file.yaml` aus. 

Führen Sie für einen Azure Red Hat OpenShift v3.x-Cluster den Befehl `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` aus, um die Datei in Ihrem Standard-Editor zu öffnen, damit Sie sie ändern und speichern können.

Es kann einige Minuten dauern, bis die Konfigurationsänderungen übernommen werden, und alle omsagent-Pods im Cluster werden neu gestartet. Der Neustart aller omsagent-Pods erfolgt gleitend, nicht alle werden gleichzeitig neu gestartet. Wenn die Neustarts abgeschlossen sind, wird eine Meldung angezeigt, die der folgenden ähnelt und das Ergebnis anzeigt: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Überprüfen der Konfiguration

Verwenden Sie den folgenden Befehl zum Überprüfen der Protokolle aus einem Agent-Pod, um sich zu vergewissern, dass die Konfiguration erfolgreich auf einen Cluster angewandt wurde: `kubectl logs omsagent-fdf58 -n=kube-system`. 

>[!NOTE]
>Dieser Befehl kann nicht für Azure Red Hat OpenShift v3.x-Cluster angewandt werden.
> 

Bei Konfigurationsfehlern aus den omsagent-Pods werden von der Ausgabe Fehler wie die folgenden angezeigt:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fehler im Zusammenhang mit der Anwendung von Konfigurationsänderungen können ebenfalls überprüft werden. Die folgenden Optionen stehen zur zusätzlichen Problembehandlung bei Konfigurationsänderungen und dem Abrufen von Prometheus-Metriken zur Verfügung:

- In einem Agent-Pod-Protokoll mithilfe desselben `kubectl logs`-Befehls 
    >[!NOTE]
    >Dieser Befehl kann nicht für Azure Red Hat OpenShift-Cluster angewandt werden.
    > 

- Aus Livedaten (Vorschauversion). Livedatenprotokolle (Vorschauversion) enthalten ähnliche Fehler wie die folgenden:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- In der Tabelle **KubeMonAgentEvents** in Ihrem Log Analytics Arbeitsbereich. Die Daten werden stündlich mit dem Schweregrad *Warnung* für Abruffehler und dem Schweregrad *Fehler* für Konfigurationsfehler gesendet. Wenn keine Fehler vorliegen, enthält der Eintrag in der Tabelle Daten mit dem Schweregrad *Info*, der Fehlerfreiheit angibt. Die **Tags**-Eigenschaft enthält weitere Informationen zum Pod und der Container-ID, für die der Fehler aufgetreten ist, sowie zum ersten Vorkommen, zum letzten Vorkommen und zur Anzahl in der letzten Stunde.

- Überprüfen Sie bei Azure Red Hat OpenShift v3.x und v4.x die omsagent-Protokolle, indem Sie die Tabelle **ContainerLog** durchsuchen, um zu überprüfen, ob die Protokollsammlung von „openshift-azure-logging“ aktiviert ist.

Fehler verhindern die Analyse der Datei durch omsagent, weshalb ein Neustart erfolgt und die Standardkonfiguration verwendet wird. Nachdem Sie für andere Cluster als Azure Red Hat OpenShift v3.x die Fehler in der ConfigMap korrigiert haben, speichern Sie die YAML-Datei, und wenden Sie die aktualisierte ConfigMap mit dem folgenden Befehl an: `kubectl apply -f <configmap_yaml_file.yaml`. 

Im Fall von Azure Red Hat OpenShift v3.x bearbeiten und speichern Sie die aktualisierte ConfigMap, indem Sie folgenden Befehl ausführen: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`.

## <a name="query-prometheus-metrics-data"></a>Abfragen von Prometheus-Metrikdaten

Weitere Informationen zum Anzeigen von Prometheus-Metriken, die von Azure Monitor abgerufen wurden, vom Agent gemeldeten Konfigurations-/Abruffehlern finden Sie unter [Abfragen von Prometheus-Metrikdaten](container-insights-log-query.md#query-prometheus-metrics-data) und [Abfragen von Konfigurations- oder Abruffehlern](container-insights-log-query.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Anzeigen von Prometheus-Metriken in Grafana

Container Insights unterstützt das Anzeigen von Metriken, die in Ihrem Log Analytics Arbeitsbereich in Grafana-Dashboards gespeichert sind. Wir haben eine Vorlage bereitgestellt, die Sie aus dem [Dashboard-Repository](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) von Grafana herunterladen können, um Ihnen den Einstieg zu erleichtern und Ihnen zu zeigen, wie Sie zusätzliche Daten aus Ihren überwachten Clustern zum Visualisieren in benutzerdefinierten Grafana-Dashboards abfragen können. 

## <a name="review-prometheus-data-usage"></a>Überprüfen der Prometheus-Datenverwendung

Um das Erfassungsvolumen jeder Metrikgröße in GB pro Tag zu ermitteln, um zu verstehen, ob es hoch ist, wird die folgende Abfrage bereitgestellt.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

Die Ausgabe zeigt ähnliche Ergebnisse wie die folgenden an:

![Screenshot der Protokollabfrageergebnisse zur Menge der erfassten Daten](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Um abzuschätzen, was jede Metrikgröße in GB für einen Monat bedeutet, um zu verstehen, ob das Volumen der im Arbeitsbereich empfangenen Erfassungsdaten hoch ist, wird die folgende Abfrage bereitgestellt.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Die Ausgabe zeigt ähnliche Ergebnisse wie die folgenden an:

![Protokollabfrageergebnisse zur Menge der erfassten Daten](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Weitere Informationen zum Überwachen der Datenverwendung und zum Analysieren von Kosten finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../logs/manage-cost-storage.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konfigurieren der Agent-Sammlungseinstellungen für stdout, stderr und Umgebungsvariablen aus Containerworkloads finden Sie [hier](container-insights-agent-config.md).
