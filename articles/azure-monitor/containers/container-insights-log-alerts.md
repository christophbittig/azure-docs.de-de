---
title: Warnungsprotokollierung in Container Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Protokollwarnungen für die Arbeitsspeicherauslastung und die CPU-Auslastung in Container Insights erstellen.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 82d6629ba903b656db9932b3c6bd6f5a2b92ea6a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339614"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Erstellen von Protokollwarnungen in Container Insights

Container Insights überwacht die Leistung von Containerworkloads, die für verwaltete oder selbstverwaltete Kubernetes-Cluster bereitgestellt werden. Um Warnungen zu wichtigen Aspekten zu erhalten, wird in diesem Artikel beschrieben, wie Sie protokollbasierte Warnungen für die folgenden Situationen mit AKS-Clustern erstellen:

- Wenn die CPU- oder Arbeitsspeicherauslastung für Clusterknoten einen Schwellenwert überschreitet
- Wenn die CPU- oder Arbeitsspeicherauslastung für einen Container innerhalb eines Controllers einen Schwellenwert relativ zu einem für die entsprechende Ressource festgelegten Grenzwert überschreitet
- Knotenanzahl mit Status *NotReady*
- Podphasenanzahl mit Status *Failed*, *Pending*, *Unknown*, *Running* oder *Succeeded*
- Wenn der freie Speicherplatz für Clusterknoten einen Schwellenwert überschreitet

Verwenden Sie zum Auslösen einer Warnung bei hoher CPU- oder Arbeitsspeicherauslastung oder wenig freiem Speicherplatz für Clusterknoten die bereitgestellten Abfragen, um eine Metrikwarnung oder eine Warnung aufgrund von metrischen Messungen zu erstellen. Zwar weisen Metrikwarnungen eine geringere Latenz als Protokollwarnungen auf, doch ermöglichen Protokollwarnungen erweiterte Abfragen und größere Komplexität. Protokollwarnungsabfragen vergleichen mithilfe des *now*-Operators einen „datetime“-Wert mit dem vorhandenen Wert und gehen um eine Stunde zurück. (Container Insights speichert alle Daten im UTC-Format (koordinierte Weltzeit).)

> [!IMPORTANT]
> Für die meisten Warnungsregeln fallen Kosten an, die von der Art der Regel, der Anzahl der einbezogenen Dimensionen und der Häufigkeit der Ausführung abhängen. Lesen Sie **Warnungsregeln** in [Azure Monitor-Preise](https://azure.microsoft.com/pricing/details/monitor/), bevor Sie Warnungsregeln erstellen.

Wenn Sie mit Azure Monitor-Warnungen nicht vertraut sind, lesen Sie zunächst den [Überblick über Warnungen in Microsoft Azure](../alerts/alerts-overview.md). Weitere Informationen zu Warnungen, bei denen Protokollabfragen verwendet werden, finden Sie unter [Protokollwarnungen in Azure Monitor](../alerts/alerts-unified-log.md). Weitere Informationen zu Metrikwarnungen finden Sie unter [Metrikwarnungen in Azure Monitor](../alerts/alerts-metric-overview.md).

## <a name="log-query-measurements"></a>Protokollabfragemessungen
Protokollabfragewarnungen können zwei verschiedene Messungen des Ergebnisses einer Protokollabfrage durchführen, die jeweils unterschiedliche Szenarien für die Überwachung von VMs unterstützen.

Bei [Warnungen des Typs „Metrische Maßeinheit“](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) wird eine separate Warnung für jeden Datensatz in den Abfrageergebnissen erzeugt, dessen numerischer Wert einen in der Warnungsregel definierten Schwellenwert überschreitet. Diese Warnungen sind ideal für numerische Daten wie zur CPU.

Bei [Warnungen des Typs „Anzahl der Ergebnisse“](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) wird eine einzelne Warnung erzeugt, wenn eine Abfrage mindestens eine angegebene Anzahl von Datensätzen zurückgibt. Diese Warnungen sind ideal für nicht numerische Daten oder zum Analysieren von Leistungstrends auf mehreren Computern. Sie können diese Strategie auch wählen, wenn Sie die Anzahl der Warnungen minimieren möchten oder eine Warnung nur dann erstellen möchten, wenn mehrere Komponenten dieselbe Bedingung aufweisen.

> [!NOTE]
> Ressourcenorientierte Protokollwarnungsregeln, die sich derzeit in der öffentlichen Vorschauversion befindet, vereinfachen Protokollabfragewarnungen und ersetzen die Funktionalität, die derzeit von Abfragen metrischer Maßeinheiten bereitgestellt wird. Sie können den AKS-Cluster als Ziel für die Regel verwenden, um ihn besser als betroffene Ressource zu identifizieren. Sobald die ressourcenorientierten Protokollabfragewarnungen allgemein verfügbar sind, wird die Anleitung in diesem Szenario aktualisiert.

## <a name="create-a-log-query-alert-rule"></a>Erstellen einer Warnungsregel für eine Protokollabfrage
Der [Vergleich der Messungen für Protokollabfragewarnungen](../vm/monitor-virtual-machine-alerts.md#comparison-of-log-query-alert-measures) bietet eine exemplarische Vorgehensweise für die Regeln für Protokollabfragewarnungen für jede Art von Messung, einschließlich eines Vergleichs der Protokollabfragen, die die jeweilige Messung unterstützen. Sie können dieselben Prozesse verwenden, um Warnungsregeln für AKS-Cluster zu erstellen, indem Sie ähnliche Abfragen wie in diesem Artikel verwenden.

## <a name="resource-utilization"></a>Ressourcennutzung 

**Durchschnittliche CPU-Auslastung als durchschnittliche CPU-Auslastung von Memberknoten pro Minute (metrische Maßeinheit)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

**Durchschnittliche Arbeitsspeicherauslastung als durchschnittliche Arbeitsspeicherauslastung der Mitgliedsknoten pro Minute (metrische Maßeinheit)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```


>[!IMPORTANT]
>Die folgenden Abfragen verwenden die Platzhalterwerte \<your-cluster-name> und \<your-controller-name> zur Darstellung Ihres Clusters und Controllers. Ersetzen Sie sie durch spezifische Werte für Ihre Umgebung, wenn Sie Warnungen einrichten.

**Durchschnittliche CPU-Auslastung aller Container in einem Controller als durchschnittliche CPU-Auslastung der einzelnen Containerinstanzen in einem Controller pro Minute (metrische Maßeinheit)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

**Durchschnittliche Arbeitsspeicherauslastung aller Container in einem Controller als durchschnittliche Arbeitsspeicherauslastung der einzelnen Containerinstanzen in einem Controller pro Minute (metrische Maßeinheit)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="resource-availability"></a>Ressourcenverfügbarkeit 

**Knoten und Anzahlen mit dem Status „Ready“ und „NotReady“ (metrische Maßeinheit)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Die folgende Abfrage gibt die Podphasenanzahl, basierend auf allen Phasen, zurück: *Failed*, *Pending*, *Unknown*, *Running* oder *Succeeded*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Ändern Sie zum Ausgeben von Warnungen bei bestimmten Podphasen (z.B. *Pending*, *Failed* oder *Unknown*) die letzte Zeile der Abfrage. Verwenden Sie beispielsweise zum Ausgeben einer Warnung für *FailedCount*: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Die folgende Abfrage gibt Clusterknotendatenträger mit einer Speicherplatzbelegung von über 90 Prozent zurück. Führen Sie zum Abrufen der Cluster-ID zunächst die folgende Abfrage aus, und kopieren Sie den Wert aus der Eigenschaft `ClusterId`:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```



**Neustarts einzelner Container (Anzahl der Ergebnisse)**<br>
Warnungen, wenn die Anzahl der Neustarts einzelner Systemcontainer einen Schwellenwert für die letzten 10 Minuten überschreitet.

 
```kusto
let _threshold = 10m; 
let _alertThreshold = 2;
let Timenow = (datetime(now) - _threshold); 
let starttime = ago(5m); 
KubePodInventory
| where TimeGenerated >= starttime
| where Namespace in ('default', 'kube-system') // the namespace filter goes here
| where ContainerRestartCount > _alertThreshold
| extend Tags = todynamic(ContainerLastStatus)
| extend startedAt = todynamic(Tags.startedAt)
| where startedAt >= Timenow
| summarize arg_max(TimeGenerated, *) by Name
```

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Beispiele zu Protokollabfragen](container-insights-log-query.md) an, die auch vordefinierte Abfragen enthalten. Mit diesen Materialien können Sie Auswertungen von bzw. Anpassungen für Warnungen, Visualisierungen und Analysen von Clustern vornehmen.

- Weitere Informationen zu Azure Monitor und darüber, wie Sie andere Aspekte Ihres Kubernetes-Clusters überwachen können, finden Sie unter [Überwachen der Leistung von Kubernetes-Clustern](container-insights-analyze.md) und [Überwachen der Integrität von Kubernetes-Clustern](./container-insights-overview.md).