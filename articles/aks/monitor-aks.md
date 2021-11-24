---
title: Überwachen von Azure Kubernetes Service (AKS) mit Azure Monitor
description: Hier erfahren Sie, wie Sie mithilfe von Azure Monitor die Integrität und Leistung von AKS-Clustern und deren Workloads überwachen.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2021
ms.openlocfilehash: 28bf92ffeb97c65aadae3f61cf471efba943b5b7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491960"
---
# <a name="monitoring-azure-kubernetes-service-aks-with-azure-monitor"></a>Überwachen von Azure Kubernetes Service (AKS) mit Azure Monitor
In diesem Szenario erfahren Sie, wie Sie mithilfe von Azure Monitor die Integrität und Leistung von Azure Kubernetes Service (AKS) überwachen. Es umfasst die Sammlung wichtiger Telemetriedaten für die Überwachung, die Analyse und Visualisierung der gesammelten Daten, um Trends zu identifizieren, sowie die Konfiguration von Warnungen, um proaktiv über kritische Probleme informiert zu werden.

Im [Leitfaden zur Cloudüberwachung](/azure/cloud-adoption-framework/manage/monitor/) werden die [wichtigsten Überwachungsziele](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements) definiert, auf die Sie sich bei Ihren Azure-Ressourcen konzentrieren sollten. In diesem Szenario steht die Integritäts- und Statusüberwachung mithilfe von Azure Monitor im Mittelpunkt.

## <a name="scope-of-the-scenario"></a>Umfang des Szenarios
Dieses Szenario richtet sich an Kunden, die Azure Monitor zur Überwachung von AKS verwenden. Folgendes wird nicht behandelt, aber möglicherweise bei späteren Szenarioaktualisierungen hinzugefügt:

- Überwachen von Kubernetes-Clustern außerhalb von Azure (mit Ausnahme von Verweisen auf vorhandene Inhalte für Kubernetes mit Azure Arc-Unterstützung). 
- Überwachen von AKS mit anderen Tools als Azure Monitor (außer zur Schließung von Lücken in Azure Monitor und Containererkenntnissen)

> [!NOTE]
> Azure Monitor wurde entwickelt, um die Verfügbarkeit und Leistung von Cloudressourcen zu überwachen. Die in Azure Monitor gespeicherten operativen Daten können zwar bei der Untersuchung von Sicherheitsincidents hilfreich sein, für die Überwachung der Sicherheit stehen jedoch andere Dienste in Azure zur Verfügung. Die Sicherheitsüberwachung für AKS erfolgt mit [Microsoft Sentinel](../sentinel/overview.md) und [Microsoft Defender für Cloud](../defender-for-cloud/defender-for-cloud-introduction.md). Eine Beschreibung der Sicherheitsüberwachungstools in Azure sowie Informationen zu ihrer Beziehung zu Azure Monitor finden Sie unter [Überwachen von VMs mit Azure Monitor: Sicherheitsüberwachung](../azure-monitor/vm/monitor-virtual-machine-security.md).
>
> Informationen zur Verwendung der Sicherheitsdienste für die Überwachung von AKS finden Sie unter [Microsoft Defender für Kubernetes – Vorteile und Features](../defender-for-cloud/defender-for-kubernetes-introduction.md) und [Herstellen einer Verbindung von AKS-Diagnoseprotokollen (Azure Kubernetes Service) mit Microsoft Sentinel](../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks).
## <a name="container-insights"></a>Container Insights
Von AKS werden genau wie von anderen Azure-Ressourcen [Plattformmetriken und Ressourcenprotokolle](monitor-aks-reference.md) generiert, die Sie zum Überwachen der grundlegenden Integrität und Leistung verwenden können. Aktivieren Sie [Containererkenntnisse](../azure-monitor/containers/container-insights-overview.md), um diese Überwachung zu erweitern. Containererkenntnisse sind ein Feature in Azure Monitor, das neben anderen Clusterkonfigurationen auch die Integrität und Leistung verwalteter, in AKS gehosteter Kubernetes-Cluster überwacht. Containererkenntnisse bieten interaktive Ansichten und Arbeitsmappen für die Analyse gesammelter Daten in verschiedensten Überwachungsszenarien. 

[Prometheus](https://prometheus.io/) und [Grafana](https://www.prometheus.io/docs/visualization/grafana/) sind gängige, von der CNCF unterstützte Open-Source-Tools für die Kubernetes-Überwachung. Da AKS viele Metriken im Prometheus-Format verfügbar macht, ist Prometheus ein beliebtes Überwachungstool. Das Feature [Containererkenntnisse](../azure-monitor/containers/container-insights-overview.md) bietet eine native AKS-Integration, sammelt wichtige Metriken und Protokolle, gibt Warnungen zu identifizierten Problemen aus und stellt Visualisierungen mit Arbeitsmappen bereit. Darüber hinaus werden durch dieses Feature bestimmte Prometheus-Metriken erfasst, und viele native Azure Monitor-Erkenntnisse basieren auf Prometheus-Metriken. Containererkenntnisse ergänzen und vervollständigen die E2E-Überwachung von AKS – einschließlich Protokollsammlung, die Prometheus als eigenständiges Tool nicht bietet. Viele Kunden kombinieren die Prometheus-Integration und Azure Monitor für die E2E-Überwachung.

Weitere Informationen zur Verwendung von Containererkenntnissen finden Sie unter [Übersicht über Container Insights](../azure-monitor/containers/container-insights-overview.md). Unter [Überwachen von AKS-Ebenen mit Containererkenntnissen](#monitor-layers-of-aks-with-container-insights) werden verschiedene Features von Containererkenntnissen sowie die von ihnen unterstützten Überwachungsszenarien erläutert.

:::image type="content" source="media/monitor-aks/container-insights.png" alt-text="Container Insights" lightbox="media/monitor-aks/container-insights.png":::


## <a name="configure-monitoring"></a>Konfigurieren der Überwachung
In den folgenden Abschnitten werden die Schritte beschrieben, die zum Konfigurieren der vollständigen Überwachung Ihres AKS-Clusters mithilfe von Azure Monitor ausgeführt werden müssen.
### <a name="create-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
Sie benötigen mindestens einen Log Analytics-Arbeitsbereich, um Container Insights zu unterstützen und weitere Telemetriedaten zu Ihrem AKS-Cluster zu sammeln und zu analysieren. Für den Arbeitsbereich entstehen keine Kosten, aber beim Sammeln von Daten fallen Gebühren für die Erfassung und Aufbewahrung an. Ausführliche Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/logs/manage-cost-storage.md).

Falls Sie Azure Monitor noch nicht verwendet haben, sollten Sie mit einem einzelnen Arbeitsbereich beginnen und die Erstellung weiterer Arbeitsbereiche in Betracht ziehen, wenn dies aufgrund Ihrer Anforderungen erforderlich ist. In vielen Umgebungen wird ein einzelner Arbeitsbereich für alle überwachten Azure-Ressourcen verwendet. Sie können sogar einen Arbeitsbereich gemeinsam nutzen, der von [Microsoft Defender für Cloud und Microsoft Sentinel](../azure-monitor/vm/monitor-virtual-machine-security.md) verwendet wird, obwohl sich viele Kunden dafür entscheiden, ihre Verfügbarkeits- und Leistungstelemetrie von Sicherheitsdaten zu trennen. 

Ausführliche Informationen zur Logik, die Sie beim Entwerfen einer Arbeitsbereichskonfiguration berücksichtigen sollten, finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../azure-monitor/logs/design-logs-deployment.md).

### <a name="enable-container-insights"></a>Aktivieren von Container Insights
Wenn Sie Containererkenntnisse für Ihren AKS-Cluster aktivieren, wird eine Containerversion des [Log Analytics-Agents](../agents/../azure-monitor/agents/log-analytics-agent.md) bereitgestellt, die Daten an Azure Monitor sendet. Für die Aktivierung gibt es mehrere Methoden – je nachdem, ob Sie mit einem neuen oder mit einem bereits vorhandenen AKS-Cluster arbeiten. Informationen zu Voraussetzungen und Konfigurationsoptionen finden Sie unter [Aktivieren von Container Insights](../azure-monitor/containers/container-insights-onboard.md).


### <a name="configure-collection-from-prometheus"></a>Konfigurieren der Sammlung aus Prometheus
Mit Containererkenntnissen können Sie bestimmte Prometheus-Metriken ganz ohne Prometheus-Server in Ihrem Log Analytics-Arbeitsbereich sammeln. Diese Daten können dann mithilfe von Azure Monitor zusammen mit anderen gesammelten Daten der Containererkenntnisse analysiert werden. Ausführliche Informationen zu dieser Konfiguration finden Sie unter [Konfigurieren des Abrufs von Prometheus-Metriken mit Container Insights](../azure-monitor/containers/container-insights-prometheus-integration.md).


### <a name="collect-resource-logs"></a>Sammeln von Ressourcenprotokollen
Die Protokolle für Komponenten der AKS-Steuerungsebene werden in Azure als [Ressourcenprotokolle](../azure-monitor/essentials/resource-logs.md) implementiert. Da diese Protokolle derzeit nicht von Containererkenntnissen verwendet werden, müssen Sie eigene Protokollabfragen erstellen, um sie anzuzeigen und zu analysieren. Ausführliche Informationen zur Struktur dieser Protokolle sowie zum Schreiben entsprechender Abfragen finden Sie im Artikel „Abfragen von Protokollen aus Container Insights“ unter [Ressourcenprotokolle](../azure-monitor/containers/container-insights-log-query.md#resource-logs).

Sie müssen eine Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen erstellen. Erstellen Sie mehrere Diagnoseeinstellungen, um verschiedene Gruppen von Protokollen an unterschiedliche Speicherorte zu senden. Informationen zum Erstellen von Diagnoseeinstellungen für Ihren AKS-Cluster finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../azure-monitor/essentials/diagnostic-settings.md). 

Für das Senden von Ressourcenprotokollen an einen Arbeitsbereich fallen Kosten an. Daher sollten Sie nur die Protokollkategorien erfassen, die Sie verwenden möchten. Senden Sie Protokolle an ein Azure-Speicherkonto, um Kosten zu sparen, wenn die Informationen aufbewahrt, aber nicht umgehend für die Analyse verfügbar gemacht werden müssen.  Unter [Ressourcenprotokolle](monitor-aks-reference.md#resource-logs) finden Sie eine Beschreibung der für AKS verfügbaren Kategorien. Ausführliche Informationen zu den Kosten für die Erfassung und Aufbewahrung von Protokolldaten finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/logs/manage-cost-storage.md). Erfassen Sie zunächst eine minimale Anzahl von Kategorien, und ändern Sie die Diagnoseeinstellung, um weitere Kategorien zu erfassen, wenn sich Ihre Anforderungen erhöhen und Sie besser mit den damit verbundenen Kosten vertraut sind.

Falls Sie nicht genau wissen, welche Ressourcenprotokolle Sie anfangs aktivieren sollen, können Sie sich an den Empfehlungen in der folgenden Tabelle orientieren, die auf den gängigsten Kundenanforderungen basieren. Aktivieren Sie die anderen Kategorien, wenn Sie später feststellen, dass Sie diese Informationen benötigen.

| Category | Aktivieren? | Destination |
|:---|:---|:---|
| cluster-autoscaler      | Aktivieren, wenn die Autoskalierung aktiviert ist | Log Analytics-Arbeitsbereich |
| guard                   | Aktivieren, wenn Azure Active Directory aktiviert ist | Log Analytics-Arbeitsbereich |
| kube-apiserver          | Aktivieren | Log Analytics-Arbeitsbereich |
| kube-audit              | Aktivieren | Azure-Speicher. Dadurch werden die Kosten minimiert und die Überwachungsprotokolle trotzdem aufbewahrt, falls sie von einem Prüfer benötigt werden. |
| kube-audit-admin        | Aktivieren | Log Analytics-Arbeitsbereich |
| kube-controller-manager | Aktivieren | Log Analytics-Arbeitsbereich |
| kube-scheduler          | Disable | |
| AllMetrics              | Aktivieren | Log Analytics-Arbeitsbereich |





## <a name="access-azure-monitor-features"></a>Zugreifen auf Azure Monitor-Features

Auf Azure Monitor-Features für alle AKS-Cluster können Sie in Ihrem Abonnement über das Azure-Portalmenü **Überwachung** zugreifen. Für einen einzelnen AKS-Cluster können Sie den Abschnitt **Überwachung** des Menüs für Kubernetes-Dienste verwenden. Der folgende Screenshot zeigt das Menü **Überwachung** des Clusters:

:::image type="content" source="media/monitor-aks/monitoring-menu.png" alt-text="AKS-Menü „Überwachung“" lightbox="media/monitor-aks/monitoring-menu.png":::

| Menüoption | BESCHREIBUNG |
|:---|:---|
| Einblicke | Öffnet Containererkenntnisse für den aktuellen Cluster. Wählen Sie im Menü **Überwachung** die Option **Container** aus, um Containererkenntnisse für alle Cluster zu öffnen.  |
| Alerts | Zeigt Warnungen für den aktuellen Cluster an. |
| Metriken | Öffnet den Metrik-Explorer und legt den Bereich auf den aktuellen Cluster fest. |
| Diagnoseeinstellungen | Dient zum Erstellen von Diagnoseeinstellungen für den Cluster, um Ressourcenprotokolle zu sammeln. |
| Advisor-Empfehlungen | Empfehlungen von Azure Advisor für den aktuellen Cluster. |
| Protokolle | Öffnet Log Analytics und legt den Bereich auf den aktuellen Cluster fest, sodass Sie Protokolldaten analysieren und auf vordefinierte Abfragen zugreifen können. |
| Arbeitsmappen | Öffnet den Arbeitsmappenkatalog für den Kubernetes-Dienst. |




## <a name="monitor-layers-of-aks-with-container-insights"></a>Überwachen von AKS-Ebenen mit Containererkenntnissen
Aufgrund der großen Unterschiede bei Kubernetes-Implementierungen hat jeder Kunde individuelle Anforderungen an die AKS-Überwachung. Der von Ihnen verfolgte Ansatz sollte auf Faktoren wie Skalierung, Topologie, Organisationsrollen und Mandanten mit mehreren Clustern basieren. In diesem Abschnitt wird eine gängige Strategie vorgestellt – ein Bottom-Up-Ansatz, der bei der Infrastruktur beginnt und bis zu Anwendungen reicht. Jede Ebene hat individuelle Überwachungsanforderungen. Diese Ebenen werden im folgenden Diagramm veranschaulicht und in den folgenden Abschnitten ausführlicher erläutert.

:::image type="content" source="media/monitor-aks/layers.png" alt-text="AKS-Ebenen"  border="false":::

### <a name="level-1---cluster-level-components"></a>Ebene 1: Komponenten auf Clusterebene
Die Clusterebene umfasst die folgenden Komponenten:

| Komponente | Überwachungsanforderungen |
|:---|:---|
| Node |  Machen Sie sich vor der Bereitstellung von Workloads mit dem Bereitschaftsstatus und der Leistung von CPU, Arbeitsspeicher und Datenträger für jeden Knoten vertraut, und überwachen Sie deren Nutzungstrends proaktiv. |


Verwenden Sie vorhandene Ansichten und Berichte in Container Insights, um Komponenten auf Clusterebene zu überwachen. In der Ansicht **Cluster** können Sie sich schnell einen Überblick über die Leistung der Knoten in Ihrem Cluster sowie über deren CPU- und Arbeitsspeicherauslastung verschaffen. In der Ansicht **Knoten** können Sie sich neben der Integrität der einzelnen Knoten auch die Integrität und Leistung der jeweils ausgeführten Pods ansehen. Ausführliche Informationen zur Verwendung dieser Ansicht sowie zur Analyse der Knotenintegrität und -leistung finden Sie unter [Überwachen der Leistung Ihres Kubernetes-Clusters mit Container Insights](../azure-monitor/containers/container-insights-analyze.md).

:::image type="content" source="media/monitor-aks/container-insights-cluster-view.png" alt-text="Containererkenntnisse: Clusteransicht" lightbox="media/monitor-aks/container-insights-cluster-view.png":::

Verwenden Sie Arbeitsmappen vom Typ **Knoten** in Containererkenntnissen, um neben Datenträgerkapazität und E/A auch der GPU-Nutzung zu analysieren. Eine Beschreibung dieser Arbeitsmappen finden Sie unter [Arbeitsmappen für Knoten](../azure-monitor/containers/container-insights-reports.md#node-workbooks).

:::image type="content" source="media/monitor-aks/container-insights-node-workbooks.png" alt-text="Containererkenntnisse: Arbeitsmappen für Knoten" lightbox="media/monitor-aks/container-insights-node-workbooks.png":::


In Problembehandlungsszenarien müssen Sie zur Wartung oder sofortigen Protokollsammlung unter Umständen direkt auf die AKS-Knoten zugreifen. Aus Sicherheitsgründen werden die AKS-Knoten nicht im Internet verfügbar gemacht. Sie können jedoch `kubectl debug` verwenden, um eine SSH-Verbindung mit den AKS-Knoten herzustellen. Ausführliche Informationen zu diesem Prozess finden Sie unter [Herstellen einer SSH-Verbindung mit Azure Kubernetes Service-Clusterknoten (AKS) zur Wartung oder Problembehandlung](ssh.md).



### <a name="level-2---managed-aks-components"></a>Ebene 2: Verwaltete AKS-Komponenten
Die Ebene für verwaltete AKS-Komponenten umfasst Folgendes:

| Komponente | Überwachung |
|:---|:---|
| API-Server | Überwachen Sie den Status des API-Servers, und identifizieren Sie jegliche Zunahme der Anforderungslast sowie Engpässe, wenn der Dienst nicht verfügbar ist. |
| Kubelet | Die Überwachung von Kubelet hilft bei der Behandlung von Problemen mit der Podverwaltung, mit nicht startenden Pods, mit nicht bereiten Knoten oder mit beendeten Pods.  |

Azure Monitor und Containererkenntnisse bieten noch keine vollständige Überwachung für den API-Server. Sie können sich zwar im Metrik-Explorer den Leistungsindikator **In-Flight-Anforderungen** ansehen, es empfiehlt sich jedoch, Metriken in Prometheus heranzuziehen, um sich ein umfassendes Bild von der Leistung des API-Servers zu machen. Dies schließt Werte wie Anforderungslatenz und Verarbeitungszeit der Arbeitswarteschlange ein. Ein Grafana-Dashboard mit Ansichten der kritischen Metriken für den API-Server finden Sie unter [Grafana Labs](https://grafana.com/grafana/dashboards/12006). Verwenden Sie dieses Dashboard auf Ihrem vorhandenen Grafana-Server, oder richten Sie einen neuen Grafana-Server in Azure ein, wie unter [Überwachen Ihrer Azure-Dienste in Grafana](../azure-monitor/visualize/grafana-plugin.md) beschrieben.

:::image type="content" source="media/monitor-aks/grafana-api-server.png" alt-text="Grafana-API-Server" lightbox="media/monitor-aks/grafana-api-server.png":::

Verwenden Sie die Arbeitsmappe **Kubelet**, um die Integrität und Leistung der einzelnen Kubelets anzuzeigen. Ausführliche Informationen zu dieser Arbeitsmappe finden Sie unter [Arbeitsmappen für Ressourcenüberwachung](../azure-monitor/containers/container-insights-reports.md#resource-monitoring-workbooks). In Problembehandlungsszenarien können Sie mithilfe des unter [Abrufen von Kubelet-Protokollen aus Azure Kubernetes Service-Clusterknoten (AKS)](kubelet-logs.md) beschriebenen Prozesses auf Kubelet-Protokolle zugreifen.

:::image type="content" source="media/monitor-aks/container-insights-kubelet-workbook.png" alt-text="Containererkenntnisse: Kubelet-Arbeitsmappe" lightbox="media/monitor-aks/container-insights-kubelet-workbook.png":::

### <a name="resource-logs"></a>Ressourcenprotokolle
Verwenden Sie [Protokollabfragen mit Ressourcenprotokollen](../azure-monitor/containers/container-insights-log-query.md#resource-logs), um Protokolle auf Steuerungsebene zu analysieren, die von AKS-Komponenten generiert wurden. 

### <a name="level-3---kubernetes-objects-and-workloads"></a>Ebene 3: Kubernetes-Objekte und -Workloads
Die Ebene für Kubernetes-Objekte und -Workloads umfasst die folgenden Komponenten:

| Komponente | Überwachungsanforderungen |
|:---|:---|
| Bereitstellungen | Überwachen Sie den tatsächlichen/gewünschten Zustand der Bereitstellung sowie den Status und die Ressourcenverwendung der darin ausgeführten Pods.  | 
| Pods | Überwachen Sie den Status und die Ressourcenverwendung (einschließlich CPU und Arbeitsspeicher) der in Ihrem AKS-Cluster ausgeführten Pods. |
| Container | Überwachen Sie die Ressourcenverwendung (einschließlich CPU und Arbeitsspeicher) der in Ihrem AKS-Cluster ausgeführten Container. |


Verwenden Sie vorhandene Ansichten und Berichte in Containererkenntnissen, um Container und Pods zu überwachen. Verwenden Sie die Ansichten **Knoten** und **Controller**, um die Integrität und Leistung der darin ausgeführten Pods anzuzeigen, und führen Sie einen Drilldown zur Integrität und Leistung der zugehörigen Container aus. Zeigen Sie die Integrität und Leistung für Container direkt in der Ansicht **Container** an. Ausführliche Informationen zur Verwendung dieser Ansicht sowie zur Analyse der Containerintegrität und -leistung finden Sie unter [Überwachen der Leistung Ihres Kubernetes-Clusters mit Container Insights](../azure-monitor/containers/container-insights-analyze.md).

:::image type="content" source="media/monitor-aks/container-insights-containers-view.png" alt-text="Containererkenntnisse: Containeransicht" lightbox="media/monitor-aks/container-insights-containers-view.png":::

Verwenden Sie die Arbeitsmappe **Bereitstellung** in Containererkenntnissen, um die für Bereitstellungen gesammelten Metriken anzuzeigen. Ausführliche Informationen finden Sie unter [Bereitstellungs- und HPA-Metriken mit Container Insights](../azure-monitor/containers/container-insights-deployment-hpa-metrics.md).

> [!NOTE]
> Die Ansicht „Bereitstellungen“ in Containererkenntnissen befindet sich derzeit in der Public Preview-Phase.

:::image type="content" source="media/monitor-aks/container-insights-deployments-workbook.png" alt-text="Containererkenntnisse: Arbeitsmappe für Bereitstellungen" lightbox="media/monitor-aks/container-insights-deployments-workbook.png":::

#### <a name="live-data"></a>Livedaten
In Problembehandlungsszenarien bieten Containererkenntnisse Zugriff auf Liveprotokolle für AKS-Container (stdout/stderror) sowie auf entsprechende Ereignisse und Podmetriken. Ausführliche Informationen zur Verwendung dieses Features finden Sie unter [Anzeigen von Kubernetes-Protokollen, -Ereignissen und -Podmetriken in Echtzeit](../azure-monitor/containers/container-insights-livedata-overview.md).

:::image type="content" source="media/monitor-aks/container-insights-live-data.png" alt-text="Containererkenntnisse: Livedaten" lightbox="media/monitor-aks/container-insights-live-data.png":::

### <a name="level-4--applications"></a>Ebene 4: Anwendungen
Die Anwendungsebene umfasst die im AKS-Cluster ausgeführten Anwendungsworkloads.

| Komponente | Überwachungsanforderungen |
|:---|:---|
| Anwendungen | Überwachen Sie Bereitstellungen von Microserviceanwendungen, um Anwendungsfehler und Wartezeitprobleme zu identifizieren. Dies schließt Informationen wie Anforderungsraten, Antwortzeiten und Ausnahmen ein. |

Application Insights ermöglicht die vollständige Überwachung von Anwendungen, die in AKS und anderen Umgebungen ausgeführt werden. Wenn Sie über eine Java-Anwendung verfügen, können Sie die Überwachung ohne Instrumentierung Ihres Codes bereitstellen. Eine entsprechende Anleitung finden Sie unter [Anwendungsüberwachung ohne Instrumentierung für Kubernetes: Azure Monitor Application Insights](../azure-monitor/app/kubernetes-codeless.md). Für eine umfassende Überwachung müssen Sie jedoch die codebasierte Überwachung konfigurieren. Die Vorgehensweise ist dabei abhängig von Ihrer Anwendung:

- [ASP.NET-Anwendungen](../azure-monitor/app/asp-net.md)
- [ASP.NET Core-Anwendungen](../azure-monitor/app/asp-net-core.md)
- [.NET-Konsolenanwendungen](../azure-monitor/app/console.md)
- [Java](../azure-monitor/app/java-in-process-agent.md)
- [Node.js](../azure-monitor/app/nodejs.md)
- [Python](../azure-monitor/app/opencensus-python.md)
- [Andere Plattformen](../azure-monitor/app/platforms.md)

Weitere Informationen finden Sie unter [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md). 

### <a name="level-5--external-components"></a>Ebene 5: Externe Komponenten
Die externen Komponenten von AKS umfassen Folgendes:

| Komponente | Überwachungsanforderungen |
|:---|:---|
| Dienstnetz, ein-/ausgehender Datenverkehr | Komponentenbasierte Metriken |
| Datenbank- und Arbeitswarteschlangen | Komponentenbasierte Metriken |

Überwachen Sie externe Komponenten wie das Dienstnetz mit ein-/ausgehendem Datenverkehr mithilfe von Prometheus und Grafana oder anderen proprietären Tools. Überwachen Sie Datenbanken und andere Azure-Ressourcen mithilfe anderer Features von Azure Monitor.

## <a name="analyze-metric-data-with-metrics-explorer"></a>Analysieren von Metrikdaten mit dem Metrik-Explorer
Verwenden Sie den Metrik-Explorer, wenn Sie eine benutzerdefinierte Analyse der für Ihre Container gesammelten Metrikdaten durchführen möchten. Mit dem Metrik-Explorer können Sie Diagramme erstellen, Trends visuell korrelieren und Spitzen und Rückgänge in Metriken untersuchen. Erstellen Sie eine Metrikwarnung, um proaktiv benachrichtigt zu werden, wenn ein Metrikwert einen Schwellenwert über- oder unterschreitet, und heften Sie Diagramme an Dashboards an, sodass sie von verschiedenen Mitgliedern Ihrer Organisation verwendet werden können.

Ausführliche Informationen zur Verwendung dieses Features finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md). Eine Liste mit den für AKS gesammelten Plattformmetriken finden Sie in der Referenz zur Überwachung von AKS-Daten unter [Metriken](monitor-aks-reference.md#metrics). Wenn Containererkenntnisse für einen Cluster aktiviert sind, stehen [zusätzliche Metrikwerte](../azure-monitor/containers/container-insights-update-metrics.md) zur Verfügung.

:::image type="content" source="media/monitor-aks/metrics-explorer.png" alt-text="Metrik-Explorer" lightbox="media/monitor-aks/metrics-explorer.png":::



## <a name="analyze-log-data-with-log-analytics"></a>Analysieren von Protokolldaten mit Log Analytics
Verwenden Sie Log Analytics, wenn Sie Ressourcenprotokolle analysieren oder sich eingehender mit den Daten beschäftigen möchten, die zum Erstellen der Ansichten in den Containererkenntnissen verwendet werden. Mit Log Analytics können Sie benutzerdefinierte Analysen für Ihre Protokolldaten durchführen. 

Unter [Abfragen von Protokollen aus Container Insights](../azure-monitor/containers/container-insights-log-query.md) erfahren Sie, wie Sie Protokollabfragen zum Analysieren von Daten verwenden, die von Containererkenntnissen gesammelt werden. Unter [Verwenden von Abfragen in Azure Monitor Log Analytics](../azure-monitor/logs/queries.md) finden Sie Informationen zur Verwendung dieser Abfragen, und im [Log Analytics-Tutorial](../azure-monitor/logs/log-analytics-tutorial.md) erfahren Sie anhand eines umfassenden Tutorials, wie Sie Log Analytics verwenden, um Abfragen auszuführen und deren Ergebnisse zu verwerten.

Eine Liste der für AKS gesammelten Tabellen, die Sie im Metrik-Explorer analysieren können, finden Sie in der Referenz zur Überwachung von AKS-Daten unter [Tabellen in Azure Monitor-Protokollen](monitor-aks-reference.md#azure-monitor-logs-tables).

:::image type="content" source="media/monitor-aks/log-analytics-queries.png" alt-text="Log Analytics-Abfragen für Kubernetes" lightbox="media/monitor-aks/log-analytics-queries.png":::

Neben Daten der Containererkenntnisse können Sie Protokollabfragen verwenden, um Ressourcenprotokolle aus AKS zu analysieren. Eine Liste mit den verfügbaren Protokollkategorien finden Sie in der Referenz zur Überwachung von AKS-Daten unter [Ressourcenprotokolle](monitor-aks-reference.md#resource-logs). Damit diese Daten gesammelt werden, müssen Sie wie unter [Konfigurieren der Überwachung](#configure-monitoring) beschrieben eine Diagnoseeinstellung erstellen, um die einzelnen Kategorien zu erfassen. 




## <a name="alerts"></a>Alerts
[Warnungen in Azure Monitor](../azure-monitor/alerts/alerts-overview.md) informieren Sie proaktiv über interessante Daten und Muster in Ihren Überwachungsdaten. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Es gibt keine vorkonfigurierten Warnungsregeln für AKS-Cluster. Sie können jedoch basierend auf den von Containererkenntnissen gesammelten Daten eigene Warnungsregeln erstellen.

> [!IMPORTANT]
> Für die meisten Warnungsregeln fallen Kosten an, die von der Art der Regel, der Anzahl der einbezogenen Dimensionen und der Häufigkeit der Ausführung abhängen. Machen Sie sich unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/) mit dem Abschnitt **Warnungsregeln** vertraut, bevor Sie Warnungsregeln erstellen.


### <a name="choosing-the-alert-type"></a>Auswählen des Warnungstyps
Die gängigsten Arten von Warnungsregeln in Azure Monitor sind [Metrikwarnungen](../azure-monitor/alerts/alerts-metric.md) und [Protokollabfragewarnungen](../azure-monitor/alerts/alerts-log-query.md). Welche Art von Warnungsregel für ein bestimmtes Szenario erstellt wird, hängt davon ab, wo sich die Daten befinden, für die Sie Warnungen erstellen. Es kann vorkommen, dass Daten für ein bestimmtes Warnungsszenario sowohl in Metriken als auch in Protokollen verfügbar sind und Sie sich für einen Regeltyp entscheiden müssen. 

In den meisten Fällen empfiehlt es sich, anstelle von Protokollwarnungen Metrikwarnungen zu verwenden, da diese reaktionsschneller und zustandsbehaftet sind. Eine Metrikwarnung kann für beliebige Werte erstellt werden, die Sie im Metrik-Explorer analysieren können. Wenn die Logik für Ihre Warnungsregel Daten in Protokollen erfordert oder eine komplexere Logik benötigt wird, können Sie eine Warnungsregel für Protokollabfragen verwenden.

Falls Sie also beispielsweise eine Warnung erhalten möchten, wenn die CPU-Auslastung einer Anwendungsworkload übermäßig hoch ist, können Sie eine Metrikwarnung unter Verwendung der CPU-Metrik erstellen. Falls Sie eine Warnung erhalten möchten, wenn eine bestimmte Nachricht in einem Protokoll der Steuerungsebene gefunden wird, benötigen Sie eine Protokollwarnung.
### <a name="metric-alert-rules"></a>Metrikwarnungsregeln
Metrikwarnungsregeln verwenden die gleichen Metrikwerte wie der Metrik-Explorer. Es ist sogar möglich, direkt im Metrik-Explorer eine Warnungsregel mit den Daten zu erstellen, die Sie gerade analysieren. Für Metrikwarnungsregeln können alle Werte verwendet werden, die in der Referenz zur Überwachung von AKS-Daten unter [Metriken](monitor-aks-reference.md#metrics) angegeben sind.

Containererkenntnisse enthalten Public Preview-Feature, durch das eine empfohlene Gruppe von Metrikwarnungsregeln für Ihren AKS-Cluster erstellt wird. Dieses Feature erstellt neue Metrikwerte (ebenfalls in der Vorschauphase), die von den Warnungsregeln verwendet werden, die Sie auch im Metrik-Explorer verwenden können. Ausführliche Informationen zu diesem Feature und zum Erstellen von Metrikwarnungen für AKS finden Sie unter [Empfohlene Metrikwarnungen von Container Insights (Vorschau)](../azure-monitor/containers/container-insights-metric-alerts.md).


### <a name="log-alerts-rules"></a>Protokollwarnungsregeln
Verwenden Sie Protokollwarnungsregeln, um eine Warnung aus den Ergebnissen einer Protokollabfrage zu generieren. Hierbei kann es sich um Daten handeln, die durch Containererkenntnisse oder aus AKS-Ressourcenprotokollen gesammelt wurden. Unter [Erstellen von Protokollwarnungen in Container Insights](../azure-monitor/containers/container-insights-log-alerts.md) finden Sie ausführliche Informationen zu Protokollwarnungsregeln für AKS sowie eine Reihe von Beispielabfragen für Warnungsregeln. Ausführliche Informationen zu Protokollabfragen, die für Warnungsregeln geändert werden können, finden Sie unter [Abfragen von Protokollen aus Container Insights](../azure-monitor/containers/container-insights-log-query.md).

### <a name="virtual-machine-alerts"></a>VM-Warnungen
Von AKS wird eine VM-Skalierungsgruppe genutzt, die für die Ausführung von AKS-Workloads fehlerfrei sein muss. Sie können Warnungen für kritische Metriken wie CPU, Arbeitsspeicher und Speicherplatz für den virtuellen Computer erstellen. Eine entsprechende Anleitung finden Sie unter [Überwachen von VMs mit Azure Monitor: Warnungen](../azure-monitor/vm/monitor-virtual-machine-alerts.md).

### <a name="prometheus-alerts"></a>Prometheus-Warnungen
In Fällen, in denen Azure Monitor entweder nicht über die für eine Warnungsbedingung erforderlichen Daten verfügt oder in denen eine Warnung möglicherweise nicht reaktionsfähig genug ist, sollten Warnungen in Prometheus konfiguriert werden. Ein Beispiel hierfür sind Warnungen für den API-Server. Von Azure Monitor werden keine wichtigen Informationen für den API-Server gesammelt – auch nicht zu dessen Verfügbarkeit oder zu Engpässen. Sie können eine Protokollabfragewarnung mit den Daten aus der Ressourcenprotokollkategorie „kube-apiserver“ erstellen. Es kann jedoch einige Minuten dauern, bis Sie eine Warnung erhalten, was für Ihre Anforderungen möglicherweise nicht ausreicht. 


## <a name="next-steps"></a>Nächste Schritte

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von AKS erstellt werden, finden Sie in der [Referenz zur Überwachung von AKS-Daten](monitor-aks-reference.md).
