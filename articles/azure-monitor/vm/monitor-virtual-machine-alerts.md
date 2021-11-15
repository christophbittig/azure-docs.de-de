---
title: 'Überwachen von VMs mit Azure Monitor: Warnungen'
description: Erfahren Sie, wie Sie mithilfe von Azure Monitor Warnungen für VMs und zugehörige Gastworkloads erstellen.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: b06d7c573514e0fe0471e13df3476bf5b13f20e3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252685"
---
# <a name="monitor-virtual-machines-with-azure-monitor-alerts"></a>Überwachen von VMs mit Azure Monitor: Warnungen

Dieser Artikel ist Teil des Szenarios [Überwachen von VMs und der zugehörigen Workloads in Azure Monitor](monitor-virtual-machine.md). Er enthält Anleitungen zum Erstellen von Warnungsregeln für Ihre VMs und die zugehörigen Gastbetriebssysteme. [Warnungen in Azure Monitor](../alerts/alerts-overview.md) informieren Sie proaktiv über interessante Daten und Muster in Ihren Überwachungsdaten. Es gibt keine vorkonfigurierten Warnungsregeln für VMs. Sie können jedoch basierend auf den mit VM Insights gesammelten Daten eigene Warnungsregeln erstellen. 

> [!NOTE]
> Diese unterscheiden sich von den Warnungen, die über das [Feature „Gastintegrität“ von Azure Monitor für VMs](vminsights-health-overview.md) erstellt werden, das sich derzeit in der öffentlichen Vorschauphase befindet. Wenn dieses Feature allgemein verfügbar wird, werden die Informationen für Warnungen konsolidiert.

> [!IMPORTANT]
> Für die meisten Warnungsregeln fallen Kosten an, deren Höhe von der Art der Regel, der Anzahl einbezogener Dimensionen und der Häufigkeit der Ausführung abhängt. Informieren Sie sich vor der Erstellung von Warnungsregeln unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/) über die Gebühren für **Warnungsregeln**.

## <a name="choose-the-alert-type"></a>Auswählen des Warnungstyps
Die gängigsten Arten von Warnungsregeln in Azure Monitor sind [Metrikwarnungen](../alerts/alerts-metric.md) und [Protokollabfragewarnungen](../alerts/alerts-log-query.md). Der Typ der für ein bestimmtes Szenario erstellten Warnungsregel hängt vom Standort der Daten ab, für die Sie Warnungen erstellen. Es kann vorkommen, dass Daten für ein bestimmtes Warnungsszenario sowohl in Form von Metriken als auch in Protokollen verfügbar sind und Sie bestimmen müssen, welcher Regeltyp verwendet werden soll. Vielleicht sind Sie auch flexibel in Bezug auf die Art und Weise der Sammlung bestimmter Daten und legen die Methode für die Datenerfassung basierend auf dem gewünschten Typ der Warnungsregel fest.

In den meisten Fällen empfiehlt es sich, anstelle von Protokollwarnungen Metrikwarnungen zu verwenden, da diese reaktionsschneller und zustandsbehaftet sind. Zur Verwendung von Metrikwarnungen müssen die Daten, für die Sie Warnungen auslösen möchten, als Metriken verfügbar sein. VM Insights sendet derzeit alle Daten an Protokolle. Deshalb müssen Sie den Azure Monitor-Agent installieren, um Metrikwarnungen mit Daten aus dem Gastbetriebssystem zu nutzen. Verwenden Sie Protokollabfragewarnungen mit metrischen Daten, wenn diese nicht in Metriken verfügbar sind oder Sie eine Logik benötigen, die über die relativ einfache Logik für eine Metrikwarnungsregel hinausgeht.

### <a name="metric-alert-rules"></a>Metrikwarnungsregeln
[Metrikwarnungsregeln](../alerts/alerts-metric.md) sind nützlich für die Warnungserstellung, wenn eine bestimmte Metrik einen Schwellenwert überschreitet. Ein Beispiel hierfür wäre eine hohe CPU-Auslastung eines Computers. Das Ziel einer Metrikwarnungsregel kann ein bestimmter Computer, eine Ressourcengruppe oder ein Abonnement sein. In diesem Fall können Sie eine einzelne Regel erstellen, die für eine Gruppe von Computern gilt.

Metrikregeln für VMs können die folgenden Daten verwenden:

- Hostmetriken für Azure-VMs, die automatisch erfasst werden. 
- Metriken, die vom Azure Monitor-Agent zum Gastbetriebssystem gesammelt werden. 

> [!NOTE]
> Wenn VM Insights den Azure Monitor-Agent unterstützt (derzeit in der öffentlichen Vorschauphase), werden Leistungsdaten vom Gastbetriebssystem an Metriken gesendet, damit Sie Metrikwarnungen verwenden können.

### <a name="log-alerts"></a>Protokollwarnungen
[Protokollwarnungen](../alerts/alerts-metric.md) können das Ergebnis einer Protokollabfrage auf zwei verschiedene Arten messen, die jeweils unterschiedliche Szenarien für die Überwachung von VMs unterstützen:

- [Metrische Maßeinheit](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value): Hierbei wird eine separate Warnung für jeden Datensatz in den Abfrageergebnissen erzeugt, dessen numerischer Wert einen in der Warnungsregel definierten Schwellenwert überschreitet. Diese Art der Messung eignet sich ideal für nicht numerische Daten, wie z. B. Windows- und Syslog-Ereignisse, die vom Log Analytics-Agent gesammelt werden, oder zum Analysieren von Leistungstrends auf mehreren Computern.
- [Anzahl der Ergebnisse](../alerts/alerts-unified-log.md#count-of-the-results-table-rows): Hierbei wird eine einzelne Warnung erzeugt, wenn eine Abfrage mindestens eine angegebene Anzahl von Datensätzen zurückgibt. Diese Art der Messung eignet sich ideal für nicht numerische Daten, wie z. B. Windows- und Syslog-Ereignisse, die vom [Log Analytics-Agent](../agents/log-analytics-agent.md) gesammelt werden, oder zum Analysieren von Leistungstrends auf mehreren Computern. Sie können diese Strategie auch wählen, wenn Sie die Anzahl der Warnungen minimieren möchten oder eine Warnung nur dann erstellen möchten, wenn mehrere Computer dieselbe Bedingung aufweisen.

### <a name="target-resource-and-impacted-resource"></a>Zielressource und betroffene Ressource

> [!NOTE]
> Ressourcenorientierte Protokollwarnungsregeln (derzeit in der öffentlichen Vorschauversion) vereinfachen Protokollabfragewarnungen für VMs und ersetzen die Funktionalität, die derzeit von Abfragen vom Typ „Metrische Maßeinheit“ bereitgestellt wird. Sie können den Computer als Ziel für die Regel verwenden, wodurch er besser als die betroffene Ressource identifiziert wird. Sie können auch eine einzelne Warnungsregel auf alle Computer in einer bestimmten Ressourcengruppe oder einem bestimmten Abonnement anwenden. Sobald die ressourcenorientierten Protokollabfragewarnungen allgemein verfügbar sind, wird die Anleitung in diesem Szenario aktualisiert.
> 
Jede Warnung in Azure Monitor umfasst die Eigenschaft **Betroffene Ressource**, die durch das Ziel der Regel definiert wird. Bei Metrikwarnungsregeln ist die betroffene Ressource der Computer, sodass sie diese problemlos in der Standardwarnungsansicht identifizieren können. Protokollabfragewarnungen werden der Arbeitsbereichsressource und nicht dem Computer zugeordnet – selbst wenn Sie eine Warnung vom Typ „Metrische Maßeinheit“ verwenden, die eine Warnung für jeden Computer erstellt. Sie müssen die Details der Warnung anzeigen, um den betroffenen Computer anzuzeigen.

Der Computername wird in der Eigenschaft **Betroffene Ressource** gespeichert, die Sie in den Details der Warnung anzeigen können. Sie wird auch als Dimension in E-Mails angezeigt, die von der Warnung gesendet werden.

:::image type="content" source="media/monitor-virtual-machines/alert-metric-measurement.png" alt-text="Screenshot: Warnung mit betroffener Ressource" lightbox="media/monitor-virtual-machines/alert-metric-measurement.png":::

Möglicherweise möchten Sie eine Ansicht anzeigen, in der die Warnungen mit dem betroffenen Computer aufgeführt sind. Sie erhalten eine solche Ansicht durch die Verwendung einer benutzerdefinierten Arbeitsmappe, die eine benutzerdefinierte [Resource Graph](../../governance/resource-graph/overview.md)-Instanz verwendet. Verwenden Sie die folgende Abfrage, um Warnungen anzuzeigen, und verwenden Sie in der Arbeitsmappe die Datenquelle **Azure Resource Graph**.

```kusto
alertsmanagementresources
| extend dimension = properties.context.context.condition.allOf
| mv-expand dimension
| extend dimension = dimension.dimensions
| mv-expand dimension
| extend Computer = dimension.value
| extend AlertStatus = properties.essentials.alertState
| summarize count() by Alert=name, tostring(AlertStatus), tostring(Computer)
| project Alert, AlertStatus, Computer
```
## <a name="common-alert-rules"></a>Gängige Warnungsregeln
Im folgenden Abschnitt werden gängige Warnungsregeln für VMs in Azure Monitor aufgelistet. Für jede Regel werden Details zu Metrikwarnungen und Protokollwarnungen des Typs „Metrische Maßeinheit“ bereitgestellt. Eine Orientierungshilfe zur Auswahl des Warnungstyps finden Sie unter [Auswählen des Warnungstyps](#choose-the-alert-type).

Wenn Sie mit dem Verfahren zum Erstellen von Warnungsregeln in Azure Monitor nicht vertraut sind, finden Sie in den folgenden Artikeln entsprechende Anleitungen:

- [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../alerts/alerts-metric.md)
- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../alerts/alerts-log.md)

### <a name="machine-unavailable"></a>Computer nicht verfügbar
Die grundlegendste Anforderung besteht darin, eine Warnung zu senden, wenn ein Computer nicht verfügbar ist. Der Computer wurde möglicherweise beendet, das Gastbetriebssystem zeigt keine Reaktion, oder der Agent antwortet nicht. Es gibt verschiedene Möglichkeiten, diese Warnungen zu konfigurieren, üblicherweise wird jedoch der vom Log Analytics-Agent gesendete Heartbeat verwendet. 

#### <a name="log-query-alert-rules"></a>Warnungsregeln für Protokollabfragen
Protokollabfragewarnungen verwenden die [Tabelle „Heartbeat“](/azure/azure-monitor/reference/tables/heartbeat), die für jede Minute einen Heartbeatdatensatz von jedem Computer enthalten sollte. 

**Separate Warnungen**

Verwenden Sie eine Regel vom Typ „Metrische Maßeinheit“ mit der folgenden Abfrage.

```kusto
Heartbeat
| summarize TimeGenerated=max(TimeGenerated) by Computer
| extend Duration = datetime_diff('minute',now(),TimeGenerated)
| summarize AggregatedValue = min(Duration) by Computer, bin(TimeGenerated,5m)
```

**Einzelne Warnung**

Verwenden Sie eine Warnung vom Typ „Anzahl der Ergebnisse“ mit der folgenden Abfrage.

```kusto
Heartbeat
| summarize LastHeartbeat=max(TimeGenerated) by Computer 
| where LastHeartbeat < ago(5m)
```

#### <a name="metric-alert-rules"></a>Metrikwarnungsregeln
Jeder Log Analytics-Arbeitsbereich umfasst eine Metrik namens *Heartbeat*. Jede VM, die mit diesem Arbeitsbereich verbunden ist, sendet jede Minute einen Wert für die Heartbeatmetrik. Da es sich bei dem Computer um eine Dimension der Metrik handelt, können Sie eine Warnung senden, wenn ein beliebiger Computer keinen Heartbeat sendet. Legen Sie den **Aggregationstyp** auf **Anzahl** und den **Schwellenwert** auf die entsprechende **Granularität der Auswertung** fest.

### <a name="cpu-alerts"></a>CPU-Warnungen
#### <a name="metric-alert-rules"></a>Metrikwarnungsregeln

| Ziel | Metrik |
|:---|:---|
| Host | CPU-Prozentsatz |
| Windows-Gast | \Processor Information(_Total)\% Processor Time |
| Linux-Gast | cpu/usage_active |

#### <a name="log-alert-rules"></a>Protokollwarnungsregeln

**CPU-Auslastung** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**CPU-Auslastung für alle Computeressourcen in einem Abonnement**

```kusto
 InsightsMetrics
 | where Origin == "vm.azm.ms"
 | where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/") 
 | where Namespace == "Processor" and Name == "UtilizationPercentage" | summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

**CPU-Auslastung für alle Computeressourcen in einer Ressourcengruppe** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/" or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/"
| where Namespace == "Processor" and Name == "UtilizationPercentage" | summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId 
```

### <a name="memory-alerts"></a>Arbeitsspeicherwarnungen

#### <a name="metric-alert-rules"></a>Metrikwarnungsregeln

| Ziel | Metrik |
|:---|:---|
| Windows-Gast | \Memory\% Committed Bytes in Use<br>\Memory\Verfügbare Bytes |
| Linux-Gast | mem/available<br>mem/available_percent |

#### <a name="log-alert-rules"></a>Protokollwarnungsregeln

**Verfügbarer Arbeitsspeicher in MB** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**Verfügbarer Arbeitsspeicher in Prozent** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"]) | extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer  
``` 

### <a name="disk-alerts"></a>Datenträgerwarnungen

#### <a name="metric-alert-rules"></a>Metrikwarnungsregeln

| Ziel | Metrik |
|:---|:---|
| Windows-Gast | \Logical Disk\(_Total)\% Free Space<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux-Gast | disk/free<br>disk/free_percent |

#### <a name="log-query-alert-rules"></a>Warnungsregeln für Protokollabfragen

**Nutzung von logischem Datenträger – alle Datenträger auf jedem Computer** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**Nutzung von logischem Datenträger – einzelne Datenträger** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk 
```

**IOPS des logischen Datenträgers**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk 
```
**Datenrate des logischen Datenträgers**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk 
```

## <a name="network-alerts"></a>Netzwerkwarnungen

#### <a name="metric-alert-rules"></a>Metrikwarnungsregeln

| Ziel | Metrik |
|:---|:---|
| Windows-Gast | \Network Interface\Bytes Sent/sec<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux-Gast | disk/free<br>disk/free_percent |

### <a name="log-query-alert-rules"></a>Warnungsregeln für Protokollabfragen

**An Netzwerkschnittstellen empfangene Bytes – alle Schnittstellen**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId  
```

**An Netzwerkschnittstellen empfangene Bytes – einzelne Schnittstellen**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

**An Netzwerkschnittstellen gesendete Bytes – alle Schnittstellen**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**An Netzwerkschnittstellen gesendete Bytes – einzelne Schnittstellen**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

## <a name="comparison-of-log-query-alert-measures"></a>Vergleich der Messungstypen von Protokollabfragewarnungen
Um das Verhalten der beiden Messungstypen für Protokollwarnungen zu vergleichen, wird im Folgenden die Erstellung einer Warnung erläutert, wenn die CPU-Auslastung der VM 80 Prozent überschreitet. Die benötigten Daten sind in der [Tabelle „InsightsMetrics“](/azure/azure-monitor/reference/tables/insightsmetrics) enthalten. Die folgende Abfrage gibt die Datensätze zurück, die für die Warnung ausgewertet werden müssen. Jeder Warnungsregeltyp verwendet eine Variante dieser Abfrage.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
```

### <a name="metric-measurement"></a>Metrische Maßeinheit
Bei einer Messung des Typs **Metrische Maßeinheit** wird eine separate Warnung für jeden Datensatz in einer Abfrage erzeugt, dessen Wert einen in der Warnungsregel definierten Schwellenwert überschreitet. Diese Warnungsregeln eignen sich ideal für VM-Leistungsdaten, weil für jeden Computer individuelle Warnungen erstellt werden. Die Protokollabfrage für diese Art der Messung muss einen Wert für jeden Computer zurückgeben. Der Schwellenwert in der Warnungsregel bestimmt, ob für den Wert eine Warnung ausgelöst werden soll.

> [!NOTE]
> Ressourcenorientierte Protokollwarnungsregeln (derzeit in der öffentlichen Vorschauversion) vereinfachen Protokollabfragewarnungen für VMs und ersetzen die Funktionalität, die derzeit von Abfragen vom Typ „Metrische Maßeinheit“ bereitgestellt wird. Sie können den Computer als Ziel für die Regel verwenden, wodurch er besser als die betroffene Ressource identifiziert wird. Sie können auch eine einzelne Warnungsregel auf alle Computer in einer bestimmten Ressourcengruppe oder Beschreibung anwenden. Sobald die ressourcenorientierten Protokollabfragewarnungen allgemein verfügbar sind, wird die Anleitung in diesem Szenario aktualisiert.

#### <a name="query"></a>Abfrage
Die Abfrage für Regeln des Typs „Metrische Maßeinheit“ muss für jeden Computer einen Datensatz mit einer numerischen Eigenschaft namens **AggregatedValue** enthalten. Dieser Wert wird mit dem Schwellenwert in der Warnungsregel verglichen. Die Abfrage muss diesen Wert nicht mit einem Schwellenwert vergleichen, da der Schwellenwert in der Warnungsregel definiert ist.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

#### <a name="alert-rule"></a>Warnungsregel
Wählen Sie im Azure Monitor-Menü die Option **Protokolle** aus, um Log Analytics zu öffnen. Stellen Sie sicher, dass der richtige Arbeitsbereich für Ihren Bereich ausgewählt ist. Falls nicht, klicken Sie oben links auf **Bereich auswählen**, und wählen Sie den richtigen Arbeitsbereich aus. Fügen Sie die Abfrage mit der gewünschten Logik ein, und klicken Sie auf **Ausführen**, um sicherzustellen, dass die richtigen Ergebnisse zurückgegeben werden.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-query-results.png" alt-text="Screenshot: Ergebnisse der Warnungsabfrage vom Typ „Metrische Maßeinheit“" lightbox="media/monitor-virtual-machines/log-alert-metric-query-results.png":::

Wählen Sie **Neue Warnungsregel** aus, um eine Regel mit der aktuellen Abfrage zu erstellen. Die Regel verwendet Ihren Arbeitsbereich als **Ressource**.

Wählen Sie **Bedingung** aus, um die Konfiguration anzuzeigen. Die Abfrage ist bereits mit einer grafischen Darstellung des Werts vorausgefüllt, der von der Abfrage für jeden Computer zurückgegeben wird. Wählen Sie den in der Dropdownliste **Pivotiert für** den Computer aus.

Scrollen Sie nach unten zu **Warnungslogik**, und wählen Sie für die Eigenschaft **Basierend auf** die Option **Metrische Maßeinheit** aus. Da Sie eine Warnung auslösen möchten, wenn die Auslastung 80 Prozent überschreitet, legen Sie **Aggregatwert** auf **Größer als** und **Schwellenwert** auf **80** fest.

Scrollen Sie nach unten zu **Warnungslogik**, und wählen Sie für die Eigenschaft **Basierend auf** die Option **Metrische Maßeinheit** aus. Geben Sie einen **Schwellenwert** an, der mit dem von der Abfrage zurückgegebenen Wert verglichen werden soll. In diesem Beispiel lautet dieser Wert **80**. Geben Sie im Feld **Warnung auslösen basierend auf** an, wie oft der Schwellenwert überschritten werden muss, bevor eine Warnung erzeugt wird. Möglicherweise ist es für Sie nicht wichtig, wenn der Prozessor einen Schwellenwert einmalig überschreitet und dann in den Normalzustand zurückkehrt. Es ist jedoch von Bedeutung, wenn der Schwellenwert bei mehreren aufeinanderfolgenden Messungen überschritten wird. Legen Sie für dieses Beispiel **Aufeinanderfolgende Sicherheitsverletzungen** auf **3** fest.

Scrollen Sie nach unten zu **Auswertung basierend auf**. **Zeitraum** gibt die Zeitspanne für die Abfrage an. Legen Sie einen Wert von **15** Minuten fest. Dies bedeutet, dass die Abfrage nur Daten verwendet, die in den letzten 15 Minuten gesammelt wurden. **Häufigkeit** gibt an, wie oft die Abfrage ausgeführt wird. Durch einen niedrigeren Wert wird die Warnungsregel reaktionsfähiger, verursacht aber auch höhere Kosten. Geben Sie den Wert **15** an, um die Abfrage alle 15 Minuten auszuführen.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-rule.png" alt-text="Screenshot: Warnungsabfrageregel vom Typ „Metrische Maßeinheit“" lightbox="media/monitor-virtual-machines/log-alert-metric-rule.png":::

### <a name="number-of-results-rule"></a>Anzahl der Ergebnisse
Die Regel vom Typ **Anzahl der Ergebnisse** erzeugt eine einzelne Warnung, wenn eine Abfrage mindestens eine angegebene Anzahl von Datensätzen zurückgibt. Die Protokollabfrage für diesen Warnungsregeltyp identifiziert in der Regel die Warnungsbedingung, während der Schwellenwert für die Warnungsregel bestimmt, ob eine ausreichende Anzahl von Datensätzen zurückgegeben wird.

#### <a name="query"></a>Abfrage
In diesem Beispiel ist der Schwellenwert für die CPU-Auslastung in der Abfrage enthalten. Die Anzahl der von der Abfrage zurückgegebenen Datensätze entspricht der Anzahl von Computern, die diesen Schwellenwert überschreiten. Der Schwellenwert für die Warnungsregel ist die Mindestanzahl von Computern, die zum Auslösen der Warnung erforderlich sind. Wenn Sie eine Warnung erhalten möchten, wenn ein einzelner Computer sich in einem Fehlerzustand befindet, lautet der Schwellenwert für die Warnungsregel 0.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AverageUtilization = avg(Val) by Computer
| where AverageUtilization > 80
```

#### <a name="alert-rule"></a>Warnungsregel
Wählen Sie im Azure Monitor-Menü die Option **Protokolle** aus, um Log Analytics zu öffnen. Stellen Sie sicher, dass der richtige Arbeitsbereich für Ihren Bereich ausgewählt ist. Falls nicht, klicken Sie oben links auf **Bereich auswählen**, und wählen Sie den richtigen Arbeitsbereich aus. Fügen Sie die Abfrage mit der gewünschten Logik ein, und klicken Sie auf **Ausführen**, um sicherzustellen, dass die richtigen Ergebnisse zurückgegeben werden. Sie verfügen derzeit wahrscheinlich über keinen Computer, der den Schwellenwert überschreitet. Legen Sie deshalb vorübergehend einen niedrigeren Schwellenwert fest, um die Ergebnisse zu überprüfen. Legen Sie dann den geeigneten Schwellenwert fest, bevor Sie die Warnungsregel erstellen.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-query-results.png" alt-text="Screenshot: Ergebnisse der Warnungsabfrage vom Typ „Anzahl der Ergebnisse“" lightbox="media/monitor-virtual-machines/log-alert-number-query-results.png":::

Wählen Sie **Neue Warnungsregel** aus, um eine Regel mit der aktuellen Abfrage zu erstellen. Die Regel verwendet Ihren Arbeitsbereich als **Ressource**.

Wählen Sie **Bedingung** aus, um die Konfiguration anzuzeigen. Die Abfrage ist bereits mit einer grafischen Darstellung der Anzahl von Datensätzen vorausgefüllt, die von der Abfrage in den vergangenen Minuten zurückgegeben wurden. 

Scrollen Sie nach unten zu **Warnungslogik**, und wählen Sie für die Eigenschaft **Basierend auf** die Option **Anzahl der Ergebnisse** aus. In diesem Beispiel möchten Sie eine Warnung erhalten, wenn Datensätze zurückgegeben werden. Dies bedeutet, dass für mindestens eine VM eine Prozessorauslastung von über 80 Prozent vorliegt. Wählen Sie für den **Operator** die Option **Größer als** und als **Schwellenwert** den Wert **0** aus.

Scrollen Sie nach unten zu **Auswertung basierend auf**. **Zeitraum** gibt die Zeitspanne für die Abfrage an. Legen Sie einen Wert von **15** Minuten fest. Dies bedeutet, dass die Abfrage nur Daten verwendet, die in den letzten 15 Minuten gesammelt wurden. **Häufigkeit** gibt an, wie oft die Abfrage ausgeführt wird. Durch einen niedrigeren Wert wird die Warnungsregel reaktionsfähiger, verursacht aber auch höhere Kosten. Geben Sie den Wert **15** an, um die Abfrage alle 15 Minuten auszuführen.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-rule.png" alt-text="Screenshot: Warnungsabfrageregel vom Typ „Anzahl der Ergebnisse“" lightbox="media/monitor-virtual-machines/log-alert-number-rule.png":::

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von auf VMs ausgeführten Workloads](monitor-virtual-machine-workloads.md)
* [Analysieren der für VMs gesammelten Überwachungsdaten](monitor-virtual-machine-analyze.md)
