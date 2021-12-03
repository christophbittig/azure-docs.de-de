---
title: 'Überwachen von virtuellen Computern mit Azure Monitor: Analysieren von Überwachungsdaten'
description: Erfahren Sie mehr über die verschiedenen Features von Azure Monitor, die Sie verwenden können, um die Integrität und Leistung von virtuellen Computern zu analysieren.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 9eb84e0b685a41cd0ead7aae1feb38c475465f83
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325670"
---
# <a name="monitor-virtual-machines-with-azure-monitor-analyze-monitoring-data"></a>Überwachen von virtuellen Computern mit Azure Monitor: Analysieren von Überwachungsdaten
Dieser Artikel ist Teil des Szenarios [Überwachen von virtuellen Computern und den jeweiligen Workloads in Azure Monitor](monitor-virtual-machine.md). In dem Artikel wird beschrieben, wie Überwachungsdaten für virtuelle Computer analysiert werden, nachdem Sie ihre Konfiguration abgeschlossen haben.

Nachdem Sie VM Insights auf virtuellen Computern aktiviert haben, stehen Daten zur Analyse zur Verfügung. In diesem Artikel werden die verschiedenen Features von Azure Monitor beschrieben, die Sie verwenden können, um die Integrität und Leistung von virtuellen Computern zu analysieren. Einige dieser Features bieten je nachdem, ob Sie einen einzelnen oder mehrere Computer analysieren, ein anderes Nutzungsszenario. Jedes Nutzungsszenario wird hier mit jedem eindeutigen Verhalten der einzelnen Features beschrieben, je nachdem, welches Nutzungsszenario besteht.

> [!NOTE]
> Dieser Artikel enthält Anleitungen zum Analysieren von Daten, die von Azure Monitor und VM Insights gesammelt werden. Daten, die Sie zum Überwachen von Workloads konfigurieren, die auf virtuellen Computern ausgeführt werden, finden Sie unter [Überwachen von Workloads](monitor-virtual-machine-workloads.md).

## <a name="single-machine-experience"></a>Nutzung mit einem einzelnen Computer
Die Analyseoberfläche für einzelne Computer ist über das Menü im Azure-Portal im Abschnitt **Überwachung** für jede Azure-VM und jeden Azure Arc-fähigen Server zugänglich. Mit diesen Optionen beschränken Sie entweder die Daten, die Sie anzeigen, auf den betreffenden Computer, oder Sie legen zumindest einen anfänglichen Filter dafür fest. Auf diese Weise können Sie sich auf einen bestimmten Computer konzentrieren, seine aktuelle Leistung und seinen Trend im Zeitverlauf anzeigen und dabei helfen, mögliche Probleme zu identifizieren.

:::image type="content" source="media/monitor-virtual-machines/vm-menu.png" alt-text="Screenshot: Analysieren eines virtuellen Computers im Azure-Portal" lightbox="media/monitor-virtual-machines/vm-menu.png":::

- **Übersicht**: Wählen Sie die Registerkarte **Überwachung** aus, um [Plattformmetriken](../essentials/data-platform-metrics.md) für den Host des virtuellen Computers anzuzeigen. Sie erhalten einen schnellen Überblick über den Trend in verschiedenen Zeiträumen für wichtige Metriken wie CPU, Netzwerk und Datenträger. Da es sich hierbei jedoch um Hostmetriken handelt, sind Leistungsindikatoren des Gastbetriebssystems, z. B. Arbeitsspeicher, nicht enthalten. Wählen Sie ein Diagramm aus, um mit diesen Daten im [Metrik-Explorer](../essentials/metrics-getting-started.md) zu arbeiten, in dem Sie verschiedene Aggregationen ausführen und weitere Leistungsindikatoren für die Analyse hinzufügen können.
- **Aktivitätsprotokoll**: Zeigen Sie [Aktivitätsprotokolleinträge](../essentials/activity-log.md#view-the-activity-log) an, die für den aktuellen virtuellen Computer gefiltert werden. Verwenden Sie dieses Protokoll, um die zuletzt durchgeführte Aktivität des Computers, z. B. Konfigurationsänderungen, und die Zeitpunkte anzuzeigen, an denen der Computer angehalten und gestartet wurde. 
- **Insights**: Öffnen Sie [VM Insights](../vm/vminsights-overview.md) mit der Zuordnung für den aktuell ausgewählten virtuellen Computer. Die Zuordnung zeigt die auf dem Computer ausgeführten Prozesse, Abhängigkeiten von anderen Computern sowie externe Prozesse. Weitere Informationen zur Verwendung der Kartenansicht für einen einzelnen Computer finden Sie unter [Verwenden des Zuordnungsfeatures von VM Insights zum Verstehen von Anwendungskomponenten](vminsights-maps.md#view-a-map-from-a-vm).

    Wählen Sie die Registerkarte **Leistung** aus, um Trends kritischer Leistungsindikatoren über verschiedene Zeiträume hinweg anzuzeigen. Wenn Sie VM Insights über das VM-Menü öffnen, verfügen Sie auch über eine Tabelle mit detaillierten Metriken für jeden Datenträger. Weitere Informationen zur Verwendung der Kartenansicht für einen einzelnen Computer finden Sie unter [Diagrammleistung mit VM Insights](vminsights-performance.md#view-performance-directly-from-an-azure-vm). 

- **Warnungen**: Zeigen Sie [Warnungen](../alerts/alerts-overview.md) für den aktuellen virtuellen Computer an. Bei diesen Warnungen wird nur der Computer als Zielressource verwendet, sodass ihm möglicherweise weitere Warnungen zugeordnet sind. Möglicherweise müssen Sie die Option **Warnungen** im Azure Monitor-Menü verwenden, um Warnungen für alle Ressourcen anzeigen zu können. Weitere Informationen finden Sie unter [Überwachen von virtuellen Computern mit Azure Monitor – Warnungen](monitor-virtual-machine-alerts.md).
- **Metriken**: Öffnen Sie den Metrik-Explorer, und legen Sie dabei den Bereich auf den aktuellen Computer fest. Diese Option ist identisch mit der Auswahl eines der Leistungsdiagramme auf der Seite **Übersicht**, außer dass die Metrik noch nicht hinzugefügt wurde.
- **Diagnoseeinstellungen**: Aktivieren und konfigurieren Sie die [Diagnoseerweiterung](../agents/diagnostics-extension-overview.md) für den aktuellen virtuellen Computer. Diese Option ist anders als die Option **Diagnoseeinstellungen** für andere Azure-Ressourcen. Aktivieren Sie die Diagnoseerweiterung nur, wenn Sie Daten an Azure Event Hubs oder Azure Storage senden müssen.
- **Advisor-Empfehlungen**: Zeigen Sie Empfehlungen von [Azure Advisor](../../advisor/index.yml) für den aktuellen virtuellen Computer an.
- **Protokolle**: Öffnen Sie [Log Analytics](../logs/log-analytics-overview.md), und legen Sie dabei den [Bereich](../logs/scope.md) auf den aktuellen virtuellen Computer fest. Sie können aus einer Vielzahl von vorhandenen Abfragen auswählen, um einen Drilldown in Protokoll- und Leistungsdaten nur für diesen Computer auszuführen. 
- **Verbindungsmonitor**: Öffnen Sie [Network Watcher – Verbindungsmonitor](../../network-watcher/connection-monitor-overview.md), um die Verbindungen zwischen dem aktuellen virtuellen Computer und anderen virtuellen Computern zu überwachen. 
- **Arbeitsmappen**: Öffnen Sie den Arbeitsmappenkatalog mit den VM Insights-Arbeitsmappen für einzelne Computer. Eine Liste der VM Insights-Arbeitsmappen, die für einzelne Computer entwickelt wurden, finden Sie unter [VM Insights-Arbeitsmappen](vminsights-workbooks.md#vm-insights-workbooks).

## <a name="multiple-machine-experience"></a>Nutzung für mehrere Computer
Die Analyseoberfläche für mehrere Computer ist im Azure-Portal über das Menü **Überwachen** für jede Azure-VM und jeden Azure Arc-fähigen Server zugänglich. Diese Optionen bieten Zugriff auf alle Daten, sodass Sie die virtuellen Computer auswählen können, die Sie vergleichen möchten.

:::image type="content" source="media/monitor-virtual-machines/monitor-menu.png" alt-text="Screenshot: Analysieren von mehreren virtuellen Computern im Azure-Portal" lightbox="media/monitor-virtual-machines/monitor-menu.png":::

- **Aktivitätsprotokoll**: Zeigen Sie [Aktivitätsprotokolleinträge](../essentials/activity-log.md#view-the-activity-log) an, die für alle Ressourcen gefiltert werden. Erstellen Sie einen Filter für einen **Ressourcentyp** von virtuellen Computern oder VM-Skalierungsgruppen, um Ereignisse für alle Computer anzeigen zu können.
- **Warnungen**: Zeigen Sie [Warnungen](../alerts/alerts-overview.md) für alle Ressourcen an, einschließlich Warnungen im Zusammenhang mit virtuellen Computern, die jedoch dem Arbeitsbereich zugeordnet sind. Erstellen Sie einen Filter für einen **Ressourcentyp** von virtuellen Computern oder VM-Skalierungsgruppen, um Warnungen für alle Computer anzeigen zu können. 
- **Metriken**: Öffnen Sie den [Metrik-Explorer](../essentials/metrics-getting-started.md), und wählen Sie dabei keinen Bereich aus. Dieses Feature ist besonders nützlich, wenn Sie Trends auf mehreren Computern vergleichen möchten. Wählen Sie ein Abonnement oder eine Ressourcengruppe aus, um schnell eine Gruppe von Computern hinzuzufügen, die zusammen analysiert werden.
- **Protokolle**: Öffnen Sie [Log Analytics](../logs/log-analytics-overview.md), und legen Sie dabei den [Bereich](../logs/scope.md) auf den Arbeitsbereich fest. Sie können aus einer Vielzahl von vorhandenen Abfragen auswählen, um einen Drilldown in Protokoll- und Leistungsdaten für alle Computer auszuführen. Sie können auch eine benutzerdefinierte Abfrage erstellen, um zusätzliche Analysen durchzuführen.
- **Arbeitsmappen**: Öffnen Sie den Arbeitsmappenkatalog mit den VM Insights-Arbeitsmappen für mehrere Computer. Eine Liste der VM Insights-Arbeitsmappen, die für mehrere Computer entwickelt wurden, finden Sie unter [VM Insights-Arbeitsmappen](vminsights-workbooks.md#vm-insights-workbooks). 
- **Virtual Machines**: Öffnen Sie [VM Insights](../vm/vminsights-overview.md) mit geöffneter Registerkarte **Erste Schritte**. Durch diese Aktion werden alle Computer in Ihrem Azure-Abonnement angezeigt, und es wird angegeben, welche davon überwacht werden. Verwenden Sie diese Ansicht, um einzelne Computer einzubinden, die noch nicht überwacht werden.

    Wählen Sie die Registerkarte **Leistung** aus, um Trends kritischer Leistungsindikatoren für mehrere Computer über verschiedene Zeiträume hinweg zu vergleichen. Wählen Sie alle Computer in einem Abonnement oder einer Ressourcengruppe aus, die in die Ansicht aufgenommen werden sollen. Weitere Informationen zur Verwendung der Kartenansicht für einen einzelnen Computer finden Sie unter [Diagrammleistung mit VM Insights](vminsights-performance.md#view-performance-directly-from-an-azure-vm).

    Wählen Sie die Registerkarte **Zuordnung** aus, um ausgeführte Prozesse auf Computern, Abhängigkeiten zwischen Computern sowie externe Prozesse anzuzeigen. Wählen Sie alle Computer in einem Abonnement oder einer Ressourcengruppe aus, oder überprüfen Sie die Daten für einen einzelnen Computer. Weitere Informationen zur Verwendung der Kartenansicht für mehrere Computer finden Sie unter [Verwenden des Zuordnungsfeatures von VM Insights zum Verstehen von Anwendungskomponenten](vminsights-maps.md#view-a-map-from-azure-monitor). 
 
## <a name="compare-metrics-and-logs"></a>Vergleichen von Metriken und Protokollen
Für viele Features von Azure Monitor müssen Sie die verschiedenen Datentypen, die verwendet werden, und deren Speicherung nicht verstehen. Sie können VM Insights beispielsweise verwenden, ohne zu wissen, welche Daten zum Auffüllen der Leistungsansicht, der Kartenansicht und der Arbeitsmappen verwendet werden. Sie konzentrieren sich einfach auf die Logik, die Sie analysieren. Wenn Sie tiefer gehen, müssen Sie den Unterschied zwischen [Metriken](../essentials/data-platform-metrics.md) und [Protokollen](../logs/data-platform-logs.md) verstehen. Verschiedene Features von Azure Monitor verwenden unterschiedliche Arten von Daten. Die Art der Warnung, die Sie für ein bestimmtes Szenario verwenden, hängt davon ab, ob diese Daten an einem bestimmten Speicherort verfügbar sind.

Dieses Maß an Detailliertheit kann verwirrend sein, wenn Sie noch nicht mit Azure Monitor vertraut sind. Die folgenden Informationen helfen Ihnen, die Unterschiede zwischen den Datentypen zu verstehen:

- Alle nicht numerischen Daten, z. B. Ereignisse, werden in Protokollen gespeichert. Metriken können nur numerische Daten enthalten, für die in regelmäßigen Abständen Stichproben entnommen werden.
- Numerische Daten können sowohl in Metriken als auch in Protokollen gespeichert werden, sodass sie auf unterschiedliche Weise analysiert werden können und verschiedene Arten von Warnungen unterstützen.
- Leistungsdaten vom Gastbetriebssystem werden mithilfe des Log Analytics-Agents von VM Insights an Protokolle gesendet.
- Leistungsdaten vom Gastbetriebssystem werden vom Azure Monitor-Agent an Metriken gesendet.

> [!NOTE]
> Vom Azure Monitor-Agent werden Daten sowohl an Metriken als auch an Protokolle gesendet. In diesem Szenario wird der Agent nur für Metriken verwendet, da der Log Analytics-Agent Daten an Protokolle sendet, wie es derzeit für VM Insights erforderlich ist. Wenn VM Insights den Azure Monitor-Agent verwendet, wird dieses Szenario so aktualisiert, dass der Log Analytics-Agent entfernt wird.

## <a name="analyze-data-with-vm-insights"></a>Analysieren von Daten mit VM Insights
VM Insights umfasst mehrere Leistungsdiagramme, mit denen Sie schnell einen Betriebsstatus von überwachten Computern, deren Leistung im Trend des Zeitverlaufs sowie die Abhängigkeiten zwischen Computern und Prozessen abrufen können. Darüber hinaus erhalten Sie eine konsolidierte Ansicht der verschiedenen Aspekte eines überwachten Computers, z. B. seine Eigenschaften und Ereignisse, die im Log Analytics-Arbeitsbereich gesammelt werden.

Auf der Registerkarte **Erste Schritte** werden alle Computer in Ihrem Azure-Abonnement angezeigt, und es wird angegeben, welche davon überwacht werden. Verwenden Sie diese Ansicht, um schnell zu ermitteln, welche Computer nicht überwacht werden, und um einzelne Computer einzubeziehen, die noch nicht überwacht werden.

:::image type="content" source="media/monitor-virtual-machines/vminsights-get-started.png" alt-text="Screenshot: „Erste Schritte“ in VM Insights" lightbox="media/monitor-virtual-machines/vminsights-get-started.png":::

Die **Leistungsansicht** enthält mehrere Diagramme mit mehreren Key Performance Indicators (KPIs), mit deren Hilfe Sie die Leistung der Computer ermitteln können. Die Diagramme zeigen die Ressourcenverwendung im Zeitverlauf. Sie können die Diagramme verwenden, um Engpässe und Anomalien zu erkennen oder zu einer Perspektive zu wechseln, in der die einzelnen Computer aufgelistet sind, sodass Sie die Ressourcenverwendung nach der ausgewählten Metrik anzeigen können. Weitere Informationen zur Verwendung der Leistungsansicht finden Sie unter [Diagrammleistung mit VM Insights](vminsights-performance.md).

:::image type="content" source="media/monitor-virtual-machines/vminsights-performance.png" alt-text="Screenshot: „Leistung“ in VM Insights" lightbox="media/monitor-virtual-machines/vminsights-performance.png":::

Verwenden Sie die **Kartenansicht**, um die auf Computern ausgeführten Prozesse und ihre Abhängigkeiten von anderen Computern sowie externe Prozesse anzuzeigen. Sie können das Zeitfenster für die Ansicht ändern, um zu bestimmen, ob sich diese Abhängigkeiten im Zeitverlauf geändert haben. Weitere Informationen zur Verwendung der Kartenansicht finden Sie unter [Verwenden des Zuordnungsfeatures von VM Insights zum Verstehen von Anwendungskomponenten](vminsights-maps.md).

:::image type="content" source="media/monitor-virtual-machines/vminsights-map.png" alt-text="Screenshot: „Zuordnung“ in VM Insights" lightbox="media/monitor-virtual-machines/vminsights-map.png":::

## <a name="analyze-metric-data-with-metrics-explorer"></a>Analysieren von Metrikdaten mit dem Metrik-Explorer
Mit dem Metrik-Explorer können Sie Diagramme erstellen, Trends visuell korrelieren und Spitzen und Täler in Metriken untersuchen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../essentials/metrics-getting-started.md). 

Von virtuellen Computern werden drei Namespaces verwendet.

| Namespace | BESCHREIBUNG | Anforderung |
|:---|:---|:---|
| Host für virtuelle Computer | Hostmetriken werden automatisch für alle virtuellen Azure-Computer gesammelt. Eine ausführliche Liste der Metriken finden Sie unter [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines). | Werden automatisch gesammelt, ohne dass eine Konfiguration erforderlich ist. |
| Gast (klassisch) | Eingeschränkte Gruppe von Leistungsdaten aus Gastbetriebssystem und Anwendung. Im Metrik-Explorer, aber nicht in anderen Azure Monitor-Features wie Metrikwarnungen verfügbar.  | [Diagnoseerweiterung](../agents/diagnostics-extension-overview.md) ist installiert. Daten werden aus Azure Storage gelesen.  |
| VM-Gast | Leistungsdaten aus Gastbetriebssystem und Anwendung sind für alle Azure Monitor-Features über Metriken verfügbar. | [Azure Monitor-Agent](../agents/azure-monitor-agent-overview.md) ist mit einer [Datensammlungsregel](../agents/data-collection-rule-overview.md) installiert. |

## <a name="analyze-log-data-with-log-analytics"></a>Analysieren von Protokolldaten mit Log Analytics
Mit Log Analytics können Sie eine benutzerdefinierte Analyse von Protokolldaten durchführen. Verwenden Sie Log Analytics, wenn Sie ausführlichere Informationen zu den Daten erhalten möchten, die zum Erstellen der Ansichten in VM Insights verwendet werden. Möglicherweise möchten Sie verschiedene Logiken und Aggregationen dieser Daten analysieren, von Azure Security Center und Azure Sentinel gesammelte Sicherheitsdaten mit Ihren Integritäts- und Verfügbarkeitsdaten korrelieren oder mit Daten arbeiten, die für Ihre [Workloads](monitor-virtual-machine-workloads.md) gesammelt wurden.

Sie müssen nicht unbedingt verstehen, wie Sie eine Protokollabfrage schreiben müssen, um Log Analytics verwenden zu können. Es gibt mehrere vordefinierte Abfragen, die Sie auswählen und entweder ohne Änderung ausführen oder als Start für eine benutzerdefinierte Abfrage verwenden können. Wählen Sie oben im Bildschirm von „Log Analytics“ die Option **Abfragen** aus, und zeigen Sie die Abfragen mit dem **Ressourcentyp** von **Virtuelle Computer** oder **VM-Skalierungsgruppen** an. Informationen zur Verwendung dieser Abfragen finden Sie unter [Verwenden von Abfragen in Azure Monitor Log Analytics](../logs/queries.md). Ein Tutorial zur Verwendung von Log Analytics zum Ausführen von Abfragen und zum Arbeiten mit ihren Ergebnissen finden Sie im [Log Analytics-Tutorial](../logs/log-analytics-tutorial.md).

:::image type="content" source="media/monitor-virtual-machines/vm-queries.png" alt-text="Screenshot: Abfragen virtueller Computer" lightbox="media/monitor-virtual-machines/vm-queries.png":::

Wenn Sie Log Analytics über VM Insights starten, indem Sie den Eigenschaftenbereich in der **Leistungs**- oder **Kartenansicht** verwenden, werden die Tabellen aufgelistet, die Daten für den ausgewählten Computer enthalten. Wählen Sie eine Tabelle aus, um Log Analytics mit einer einfachen Abfrage zu öffnen, die alle Datensätze in dieser Tabelle für den ausgewählten Computer zurückgibt. Arbeiten Sie mit diesen Ergebnissen, oder ändern Sie die Abfrage, damit Sie eine komplexere Analyse durchführen können. Der [Bereich](../log/../logs/scope.md), der auf den Arbeitsbereich festgelegt ist, bedeutet, dass Sie über Zugriffsdaten für alle Computer verfügen, die diesen Arbeitsbereich verwenden. 

:::image type="content" source="media/monitor-virtual-machines/table-query.png" alt-text="Screenshot: Tabellenabfrage" lightbox="media/monitor-virtual-machines/table-query.png":::

## <a name="visualize-data-with-workbooks"></a>Visualisieren von Daten mit Arbeitsmappen
[Arbeitsmappen](../visualize/workbooks-overview.MD) bieten interaktive Berichte im Azure-Portal und kombinieren verschiedene Arten von Daten in einer einzigen Ansicht. Arbeitsmappen kombinieren Text,  [Protokollabfragen](/azure/data-explorer/kusto/query/), Metriken und Parameter zu umfassenden interaktiven Berichten. Arbeitsmappen können von anderen Teammitgliedern bearbeitet werden, die Zugriff auf dieselben Azure-Ressourcen haben.

Arbeitsmappen sind beispielsweise für folgende Szenarien hilfreich:

* Untersuchen der Nutzung Ihres virtuellen Computers, wenn Sie noch nicht wissen, welche Metriken für Sie von Interesse sind: CPU-Auslastung, Speicherplatz, Arbeitsspeicher und Netzwerkabhängigkeiten. Im Gegensatz zu anderen Tools für die Analyse der Nutzung können Sie mit Arbeitsmappen mehrere Arten von Visualisierungen und Analysen kombinieren, sodass Sie bei der Untersuchung deutlich weniger stark eingeschränkt sind.
* Präsentieren von Leistungsindikatoren und anderen Protokollereignissen, um Ihrem Team die Leistung eines kürzlich bereitgestellten virtuellen Computers zu veranschaulichen
* Weitergeben der Ergebnisse eines Größenänderungsexperiments für Ihren virtuellen Computer an andere Mitglieder Ihres Teams. Sie können die Ziele für das Experiment mit Text erläutern. Anschließend können Sie die einzelnen Nutzungsmetriken und Analyseabfragen für die Auswertung des Experiments sowie eindeutige Anmerkungen dazu präsentieren, ob eine Metrik oberhalb oder unterhalb des Zielwerts lag.
* Melden der Auswirkungen eines Ausfalls auf die Nutzung Ihres virtuellen Computers durch eine Kombination aus Daten, einer Erläuterung in Textform und einer Auflistung der nächsten Schritte, mit denen sich Ausfälle in Zukunft vermeiden lassen.

VM Insights umfasst die folgenden Arbeitsmappen. Sie können diese Arbeitsmappen verwenden oder sie als Ausgangspunkt nutzen, um benutzerdefinierte Arbeitsmappen für Ihre speziellen Anforderungen zu erstellen.

### <a name="single-virtual-machine"></a>Einzelner virtueller Computer

| Arbeitsmappe | BESCHREIBUNG |
|----------|-------------|
| Leistung | Eine anpassbare Version der Leistungsansicht, die alle von Ihnen aktivierten Log Analytics-Leistungsindikatoren nutzt. | 
| Verbindungen | Eine detaillierte Darstellung der ein- und ausgehenden Verbindungen des virtuellen Computers. | 

### <a name="multiple-virtual-machines"></a>Mehrere Virtuelle Computer

| Arbeitsmappe | BESCHREIBUNG |
|----------|-------------|
| Leistung | Eine anpassbare Version der Top-N-Liste und der Diagrammansicht in einer einzelnen Arbeitsmappe, die alle von Ihnen aktivierten Log Analytics-Leistungsindikatoren nutzt.|
| Leistungsindikatoren | Eine Top-N-Diagrammansicht für ein breites Spektrum von Leistungsindikatoren. |
| Verbindungen | Eine detaillierte Darstellung der ein- und ausgehenden Verbindungen Ihrer überwachten Computer. |
| Aktive Ports | Eine Liste der Prozesse, die an die Ports der überwachten Computer gebunden sind, und ihrer Aktivität innerhalb des ausgewählten Zeitrahmens. |
| Offene Ports | Die Anzahl geöffneter Ports Ihrer überwachten Computer sowie Details zu diesen geöffneten Ports. |
| Verbindungsfehler | Die Anzahl von Verbindungsfehlern für Ihre überwachten Computer, der Fehlertrend sowie die Angabe, ob sich der Prozentsatz an Fehlern im Laufe der Zeit erhöht. |
| Security and Audit | Eine Analyse Ihres TCP/IP-Datenverkehrs mit Informationen zu Verbindungen im Allgemeinen, zu schädlichen Verbindungen sowie dazu, wo sich die IP-Endpunkte global befinden. Wenn Sie alle Features aktivieren möchten, müssen Sie die Sicherheitserkennung aktivieren. |
| TCP-Datenverkehr | Ein Bericht mit einer Rangfolge Ihrer überwachten Computer und ihrem gesendeten, empfangenen und gesamten Netzwerkdatenverkehr in einem Raster und als Trendlinie. |
| Vergleich von Datenverkehr | Diese Arbeitsmappe ermöglicht den Vergleich von Trends beim Netzwerkdatenverkehr eines einzelnen Computers oder einer Gruppe von Computern. |
| Log Analytics-Agent | Analysiert die Integrität der Agents, einschließlich der Anzahl der fehlerhaften Agents, bei denen eine Verbindung mit einem Arbeitsbereich besteht, und der Auswirkungen des Agents auf die Leistung des Computers. Diese Arbeitsmappe ist nicht wie die anderen Arbeitsmappen über VM Insights verfügbar. Wechseln Sie im Azure Monitor-Menü zu **Arbeitsmappen**, und wählen Sie **Öffentliche Vorlagen** aus. |

Anweisungen zum Erstellen eigener benutzerdefinierter Arbeitsmappen finden Sie unter [Erstellen interaktiver Berichte – VM Insights mit Arbeitsmappen](vminsights-workbooks.md).

:::image type="content" source="media/monitor-virtual-machines/workbook-example.png" alt-text="Screenshot: Arbeitsmappen virtueller Computer" lightbox="media/monitor-virtual-machines/workbook-example.png":::

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Warnungen anhand von gesammelten Daten](monitor-virtual-machine-alerts.md)
* [Überwachen von auf VMs ausgeführten Workloads](monitor-virtual-machine-workloads.md)
