---
title: Abfragen von Protokollen aus Container Insights
description: Container Insights erfasst Metriken und Protokolldaten. In diesem Artikel werden die Datensätze und einige Beispielabfragen beschrieben.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: af48255c1895142107e693595c256d2c76fdee65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179219"
---
# <a name="how-to-query-logs-from-container-insights"></a>Abfragen von Protokollen aus Container Insights

Container Insights erfasst Leistungsmetriken, Inventurdaten und Informationen zum Integritätszustand von Containerhosts und Containern. Die Daten werden alle drei Minuten gesammelt und an den Log Analytics-Arbeitsbereich in Azure Monitor weitergeleitet. Dort werden sie für [Protokoll-Abfragen](../logs/log-query-overview.md) mithilfe von [Log Analytics](../logs/log-analytics-overview.md) in Azure Monitor zur Verfügung stehen. Diese Daten können in verschiedenen Szenarios von Nutzen sein, z.B. bei der Migrationsplanung, Kapazitätsanalyse, Ermittlung und Ad-hoc-Behebung von Leistungsproblemen. Mit Azure Monitor-Protokollen können Sie nach Trends suchen, Engpässe diagnostizieren, Prognosen erstellen oder Daten korrelieren, die Ihnen die Einschätzung ermöglichen, ob die aktuelle Clusterkonfiguration optimal funktioniert.

Informationen zur Verwendung dieser Abfragen finden Sie unter [Verwendung von Abfragen in Azure Monitor Log Analytics](../logs/queries.md). Unter [Log Analytics-Tutorial](../logs/log-analytics-tutorial.md) finden Sie ein vollständiges Tutorial zur Verwendung von Log Analytics zum Ausführen von Abfragen und Arbeiten mit deren Ergebnissen.

## <a name="open-log-analytics"></a>Öffnen von Log Analytics
Es gibt mehrere Optionen zum Starten von Log Analytics, die jeweils mit einem anderen [Bereich](../logs/scope.md) beginnen. Um auf alle Daten im Arbeitsbereich zuzugreifen, wählen Sie im Menü **Monitor** die Option **Protokolle** aus. Um die Daten auf einen einzelnen Kubernetes-Cluster zu beschränken, wählen Sie **Protokolle** aus dem Menü dieses Clusters aus. 

:::image type="content" source="media/container-insights-log-query/start-log-analytics.png" alt-text="Starten von Log Analytics" lightbox="media/container-insights-log-query/start-log-analytics.png":::

## <a name="existing-log-queries"></a>Vorhandene Protokollabfragen
Sie müssen nicht unbedingt verstehen, wie Sie eine Protokollabfrage schreiben, um Log Analytics zu verwenden. Es gibt mehrere vordefinierte Abfragen, die Sie auswählen und entweder ohne Änderung ausführen oder als Start für eine benutzerdefinierte Abfrage verwenden können. Klicken Sie oben im Bildschirm von „Log Analytics“ auf **Abfragen** und zeigen Sie die Abfragen mit dem **Ressourcentyp** von **Kubernetes Services** an. 

:::image type="content" source="media/container-insights-log-query/log-analytics-queries.png" alt-text="Log Analytics-Abfragen für Kubernetes" lightbox="media/container-insights-log-query/log-analytics-queries.png":::

## <a name="container-tables"></a>Containertabellen
Eine Liste der Tabellen und deren ausführliche Beschreibungen, die von Containererkenntnissen verwendet werden, finden Sie in der [Azure Monitor Tabellenreferenz](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services). Alle diese Tabellen sind für die Protokollabfragen verfügbar.


## <a name="example-log-queries"></a>Beispiele für Protokollabfragen
Es ist oft hilfreich, die Abfrageerstellung ausgehend von einem oder zwei Beispielen zu beginnen und diese dann den Anforderungen entsprechend anzupassen. Sie können mit den folgenden Beispielabfragen experimentieren, um komplexere Abfragen zu erstellen:

### <a name="list-all-of-a-containers-lifecycle-information"></a>Liste mit Lebenszyklusinformationen aller Container

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Kubernetes-Ereignisse

``` kusto
KubeEvents
| where not(isempty(Namespace))
| sort by TimeGenerated desc
| render table
```

### <a name="container-cpu"></a>Container-CPU

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Containerspeicher

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Anforderungen pro Minute mit benutzerdefinierten Metriken

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc
| project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```
### <a name="pods-by-name-and-namespace"></a>Pods nach Name und Namespace

```kusto
let startTimestamp = ago(1h);
KubePodInventory
| where TimeGenerated > startTimestamp
| project ContainerID, PodName=Name, Namespace
| where PodName contains "name" and Namespace startswith "namespace"
| distinct ContainerID, PodName
| join
(
    ContainerLog
    | where TimeGenerated > startTimestamp
)
on ContainerID
// at this point before the next pipe, columns from both tables are available to be "projected". Due to both
// tables having a "Name" column, we assign an alias as PodName to one column which we actually want
| project TimeGenerated, PodName, LogEntry, LogEntrySource
| summarize by TimeGenerated, LogEntry
| order by TimeGenerated desc
```

### <a name="pod-scale-out-hpa"></a>Horizontale Pod-Skalierung (HPA)
Gibt die Anzahl der hochskalierten Replikate in jeder Bereitstellung zurück. Berechnet den Prozentsatz für die Horizontale Skalierung mit der maximalen Anzahl von Replikaten, die in der HPA konfiguriert sind.


```kusto
let _minthreshold = 70; // minimum threshold goes here if you want to setup as an alert
let _maxthreshold = 90; // maximum threshold goes here if you want to setup as an alert
let startDateTime = ago(60m);
KubePodInventory
| where TimeGenerated >= startDateTime 
| where Namespace !in('default', 'kube-system') // List of non system namespace filter goes here.
| extend labels = todynamic(PodLabel)
| extend deployment_hpa = reverse(substring(reverse(ControllerName), indexof(reverse(ControllerName), "-") + 1))
| distinct tostring(deployment_hpa)
| join kind=inner (InsightsMetrics 
    | where TimeGenerated > startDateTime 
    | where Name == 'kube_hpa_status_current_replicas'
    | extend pTags = todynamic(Tags) //parse the tags for values
    | extend ns = todynamic(pTags.k8sNamespace) //parse namespace value from tags
    | extend deployment_hpa = todynamic(pTags.targetName) //parse HPA target name from tags
    | extend max_reps = todynamic(pTags.spec_max_replicas) // Parse maximum replica settings from HPA deployment
    | extend desired_reps = todynamic(pTags.status_desired_replicas) // Parse desired replica settings from HPA deployment
    | summarize arg_max(TimeGenerated, *) by tostring(ns), tostring(deployment_hpa), Cluster=toupper(tostring(split(_ResourceId, '/')[8])), toint(desired_reps), toint(max_reps), scale_out_percentage=(desired_reps * 100 / max_reps)
    //| where scale_out_percentage > _minthreshold and scale_out_percentage <= _maxthreshold
    )
    on deployment_hpa
```

### <a name="nodepool-scale-outs"></a>Hochskalierungen für Knotenpools 
Gibt die Anzahl der aktiven Knoten in jedem Knotenpool zurück. Berechnet die Anzahl der verfügbaren aktiven Knoten und die maximale Knotenkonfiguration in den Einstellungen für die automatische Skalierung, um den Prozentsatz für die horizontale Skalierung zu bestimmen. Informationen für das Verwenden einer Warnregel für die **Anzahl von Ergebnissen** finden Sie in den kommentierten Zeilen der Abfrage.

```kusto
let nodepoolMaxnodeCount = 10; // the maximum number of nodes in your auto scale setting goes here.
let _minthreshold = 20;
let _maxthreshold = 90;
let startDateTime = 60m;
KubeNodeInventory
| where TimeGenerated >= ago(startDateTime)
| extend nodepoolType = todynamic(Labels) //Parse the labels to get the list of node pool types
| extend nodepoolName = todynamic(nodepoolType[0].agentpool) // parse the label to get the nodepool name or set the specific nodepool name (like nodepoolName = 'agentpool)'
| summarize nodeCount = count(Computer) by ClusterName, tostring(nodepoolName), TimeGenerated
//(Uncomment the below two lines to set this as an log search alert)
//| extend scaledpercent = iff(((nodeCount * 100 / nodepoolMaxnodeCount) >= _minthreshold and (nodeCount * 100 / nodepoolMaxnodeCount) < _maxthreshold), "warn", "normal")
//| where scaledpercent == 'warn'
| summarize arg_max(TimeGenerated, *) by nodeCount, ClusterName, tostring(nodepoolName)
| project ClusterName, 
    TotalNodeCount= strcat("Total Node Count: ", nodeCount),
    ScaledOutPercentage = (nodeCount * 100 / nodepoolMaxnodeCount),  
    TimeGenerated, 
    nodepoolName
```

### <a name="system-containers-replicaset-availability"></a>Verfügbarkeit von Systemcontainern (ReplicaSet)
Gibt die Systemcontainer (ReplicaSets) zurück und meldet den nicht verfügbaren Prozentsatz. Informationen für das Verwenden einer Warnregel für die **Anzahl von Ergebnissen** finden Sie in den kommentierten Zeilen der Abfrage.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'ReplicaSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

### <a name="system-containers-daemonsets-availability"></a>Verfügbarkeit von Systemcontainern (DaemonSets)
Gibt die Systemcontainer (DaemonSets) zurück und meldet den nicht verfügbaren Prozentsatz. Informationen für das Verwenden einer Warnregel für die **Anzahl von Ergebnissen** finden Sie in den kommentierten Zeilen der Abfrage.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'DaemonSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

## <a name="resource-logs"></a>Ressourcenprotokolle
Ressourcenprotokolle für AKS werden in der Tabelle [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) gespeichert. Mit der Spalte **Kategorie** können Sie verschiedene Protokolle unterscheiden. Eine Beschreibung der einzelnen Kategorien finden Sie in den [AKS-Referenzressourcenprotokollen](../../aks/monitor-aks-reference.md). Die folgenden Beispiele erfordern eine Diagnoseerweiterung, um die Ressourcenprotokolle für einen AKS-Cluster an einen Log Analytics-Arbeitsbereich zu senden. Weitere Informationen finden Sie unter [Konfigurieren einer Überwachung](../../aks/monitor-aks.md#configure-monitoring).

### <a name="api-server-logs"></a>API-Serverprotokolle

```kusto
AzureDiagnostics 
| where Category == "kube-apiserver"
```

### <a name="count-logs-for-each-category"></a>Zählen von den Protokollen für jede Kategorie

```kusto
AzureDiagnostics
| where ResourceType == "MANAGEDCLUSTERS"
| summarize count() by Category
```

## <a name="query-prometheus-metrics-data"></a>Abfragen von Prometheus-Metrikdaten

Das folgende Beispiel zeigt eine Prometheus-Metrikabfrage mit Datenträgerlesevorgänge pro Sekunde und Datenträger pro Knoten.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Zum Anzeigen von Prometheus-Metriken, die von Azure Monitor nach Namespace gefiltert abgerufen wurden, geben Sie „prometheus“ an. Hier sehen Sie eine Beispielabfrage zum Anzeigen von Prometheus-Metriken aus dem `default`-Kubernetes-Namespace:

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus-Daten können außerdem direkt anhand des Namens abgefragt werden.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Abfragen von Konfigurations- oder Abruffehlern

Zum Untersuchen von Konfigurations- oder Abruffehlern werden mit der folgenden Beispielabfrage Informationsereignisse aus der Tabelle `KubeMonAgentEvents` zurückgegeben.

```
KubeMonAgentEvents | where Level != "Info" 
```

Die Ausgabe enthält Ergebnisse, die dem folgenden Beispiel ähneln:

![Protokollabfrageergebnisse von Informationsereignissen vom Agent](./media/container-insights-log-query/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Nächste Schritte

Container Insights umfasst keine vordefinierten Warnungen. Informationen zum Erstellen empfohlener Warnungen für hohe CPU- und Arbeitsspeicherauslastung zur Unterstützung Ihrer DevOps oder Betriebsprozesse und -verfahren finden Sie unter [Erstellen von Leistungswarnungen mit Container Insights](./container-insights-log-alerts.md).
