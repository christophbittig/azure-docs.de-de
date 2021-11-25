---
title: 'Überwachen von VMs mit Azure Monitor: Konfigurieren der Überwachung'
description: Erfahren Sie, wie Sie virtuelle Computer (VMs) für die Überwachung in Azure Monitor konfigurieren. Überwachen Sie VMs und zugehörige Workloads in einem Azure Monitor-Szenario.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 6eb624fd132823afe25a91414c2a316f9dc7bab3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310336"
---
# <a name="monitor-virtual-machines-with-azure-monitor-configure-monitoring"></a>Überwachen von VMs mit Azure Monitor: Konfigurieren der Überwachung
Dieser Artikel ist Teil des Szenarios [Überwachen von VMs und zugehöriger Workloads in Azure Monitor](monitor-virtual-machine.md). Es wird beschrieben, wie Sie die Überwachung Ihrer Azure- und Hybrid-VMs in Azure Monitor konfigurieren.

In diesem Artikel werden die gängigsten Azure Monitor-Features zum Überwachen des VM-Hosts und des zugehörigen Gastbetriebssystems erläutert. Abhängig von Ihrer speziellen Umgebung und Ihren geschäftlichen Anforderungen möchten Sie möglicherweise nicht alle Features implementieren, die durch diese Konfiguration unterstützt werden. In jedem Abschnitt wird beschrieben, welche Features durch diese Konfiguration aktiviert werden und ob dies zu zusätzlichen Kosten führen kann. Anhand dieser Informationen können Sie entscheiden, welche der einzelnen Konfigurationsschritte Sie ausführen sollten. Ausführliche Preisinformationen finden Sie in der [Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Eine allgemeine Beschreibung der einzelnen Features, die durch diese Konfiguration aktiviert werden, finden Sie in der [Übersicht für das Szenario](monitor-virtual-machine.md). Dieser Artikel enthält außerdem Links zu Inhalten, die eine ausführliche Beschreibung der einzelnen Features enthalten, um Sie bei der Bewertung Ihrer Anforderungen zu unterstützen.

> [!NOTE]
> Die durch die Konfiguration aktivierten Features unterstützen die Überwachung von Workloads, die auf Ihrer VM ausgeführt werden. Allerdings ist abhängig von Ihren speziellen Workloads in der Regel eine zusätzliche Konfiguration erforderlich. Weitere Informationen zu dieser zusätzlichen Konfiguration finden Sie unter [Workloadüberwachung](monitor-virtual-machine-workloads.md).

## <a name="configuration-overview"></a>Konfigurationsübersicht
In der folgenden Tabelle sind die Schritte aufgeführt, die für diese Konfiguration ausgeführt werden müssen. Die jeweiligen Schritte sind mit einem Abschnitt verlinkt, der ausführliche Informationen zu diesem Konfigurationsschritt bereitstellt.

| Schritt | BESCHREIBUNG |
|:---|:---|
| [Keine Konfiguration](#no-configuration) | Aktivitätsprotokolle und Plattformmetriken für die Azure-VM-Hosts werden automatisch und ohne Konfiguration erfasst. |
| [Erstellen und Vorbereiten eines Log Analytics-Arbeitsbereichs](#create-and-prepare-a-log-analytics-workspace) | Erstellen Sie einen Log Analytics-Arbeitsbereich, und konfigurieren Sie ihn für VM Insights. Abhängig von Ihren speziellen Anforderungen können Sie mehrere Arbeitsbereiche konfigurieren. |
| [Senden eines Azure-Protokolls an einen Log Analytics-Arbeitsbereich](#send-an-activity-log-to-a-log-analytics-workspace) | Senden Sie das Aktivitätsprotokoll an den Arbeitsbereich, um es mit anderen Protokolldaten zu analysieren. |
| [Vorbereiten von Hybridcomputern](#prepare-hybrid-machines) | Hybridcomputer benötigen entweder die über Azure Arc installierten Server-Agents, damit sie wie Azure-VMs verwaltet werden können, oder die zugehörigen Agents müssen manuell installiert werden. |
| [Aktivieren von VM Insights für Computer](#enable-vm-insights-on-machines) | Integrieren Sie Computer in VM Insights. Hierdurch werden die erforderlichen Agents bereitgestellt, und es wird mit der Erfassung von Daten aus dem Gastbetriebssystem begonnen. |
| [Senden von Gastleistungsdaten an Metriken](#send-guest-performance-data-to-metrics) |Installieren Sie den Azure Monitor-Agent, um Leistungsdaten an Azure Monitor-Metriken zu senden. |

## <a name="no-configuration"></a>Keine Konfiguration
Azure Monitor stellt kostenfrei und ohne Konfiguration eine grundlegende Überwachung für Ihre Azure-VMs bereit. Plattformmetriken für Azure-VMs umfassen wichtige Metriken, darunter beispielsweise zur CPU-, Netzwerk- und Datenträgerauslastung. Diese können auf der [Übersichtsseite](monitor-virtual-machine-analyze.md#single-machine-experience) der VM im Azure-Portal angezeigt werden. Das Aktivitätsprotokoll wird ebenfalls automatisch erfasst und enthält die jüngsten Aktivitäten des Computers, z. B. Konfigurationsänderungen und Informationen zu Beendigung und Start des Computers.

## <a name="create-and-prepare-a-log-analytics-workspace"></a>Erstellen und Vorbereiten eines Log Analytics-Arbeitsbereichs
Sie benötigen mindestens einen Log Analytics-Arbeitsbereich, um VM Insights zu unterstützen und Telemetriedaten vom Log Analytics-Agent zu sammeln. Für den Arbeitsbereich entstehen keine Kosten, aber beim Sammeln von Daten fallen Gebühren für Erfassung und Aufbewahrung an. Weitere Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../logs/manage-cost-storage.md).

Viele Umgebungen verwenden einen einzelnen Arbeitsbereich für sämtliche VMs und weitere überwachte Azure-Ressourcen. Sie können sogar einen Arbeitsbereich gemeinsam nutzen, der von [Microsoft Defender für Cloud und Microsoft Sentinel](monitor-virtual-machine-security.md) verwendet wird, obwohl sich viele Kunden dafür entscheiden, ihre Verfügbarkeits- und Leistungstelemetrie von Sicherheitsdaten zu trennen. Beim Einstieg in Azure Monitor sollten Sie mit einem einzigen Arbeitsbereich beginnen und die Erstellung weiterer Arbeitsbereiche in Betracht ziehen, wenn sich Ihre Anforderungen weiterentwickeln.

Ausführliche Informationen zur Logik, die Sie beim Entwerfen einer Arbeitsbereichskonfiguration berücksichtigen sollten, finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../logs/design-logs-deployment.md).

### <a name="multihoming-agents"></a>Multihoming-Agents
Multihoming bezieht sich auf eine VM, die eine Verbindung mit mehreren Arbeitsbereichen herstellt. In der Regel gibt es kaum einen Grund, nur für Azure Monitor Multihoming-Agents zu nutzen. Wenn ein Agent Daten an mehrere Arbeitsbereiche sendet, werden höchstwahrscheinlich doppelte Daten in jedem Arbeitsbereich generiert, was die Gesamtkosten erhöht. Sie können Daten aus mehreren Arbeitsbereichen kombinieren, indem Sie [arbeitsbereichsübergreifende Abfragen](../logs/cross-workspace-query.md) und [Arbeitsmappen verwenden](../visualizations/../visualize/workbooks-overview.md).

Ein Grund, warum Sie Multihoming in Betracht ziehen sollten, ist, wenn Sie eine Umgebung mit Microsoft Defender für Cloud oder Microsoft Sentinel haben, die in einem Arbeitsbereich gespeichert ist, der von Azure Monitor getrennt ist. Ein Computer, der von jedem Dienst überwacht wird, muss Daten an jeden Arbeitsbereich senden. Der Windows-Agent unterstützt dieses Szenario, da er Daten an bis zu vier Arbeitsbereiche senden kann. Der Linux-Agent kann derzeit nur Daten an einen einzelnen Arbeitsbereich senden. Wenn Sie Azure Monitor und Microsoft Defender für Cloud oder Microsoft Sentinel einen gemeinsamen Satz von Linux-Rechnern überwachen lassen möchten, müssen die Dienste denselben Arbeitsbereich nutzen.

Ein weiterer Grund für die Nutzung von Multihoming-Agents liegt vor, wenn Sie ein [Hybridüberwachungsmodell](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) verwenden. In diesem Modell verwenden Sie Azure Monitor und Operations Manager gemeinsam, um dieselben Computer zu überwachen. Der Log Analytics-Agent und der Microsoft Management Agent für Operations Manager sind derselbe Agent. Gelegentlich wird auf sie mit unterschiedlichen Namen verwiesen.

### <a name="workspace-permissions"></a>Arbeitsbereichberechtigungen
Der Zugriffsmodus des Arbeitsbereichs definiert, welche Benutzer auf verschiedene Sätze von Daten zugreifen können. Ausführliche Informationen zum Definieren des Zugriffsmodus und zum Konfigurieren von Berechtigungen finden Sie unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](../logs/manage-access.md). Wenn Sie gerade erst mit der Nutzung von Azure Monitor beginnen, sollten Sie bei der Erstellung Ihres Arbeitsbereichs die Standardeinstellungen übernehmen und die zugehörigen Berechtigungen später konfigurieren.

### <a name="prepare-the-workspace-for-vm-insights"></a>Vorbereiten des Arbeitsbereichs für VM Insights
Bereiten Sie jeden Arbeitsbereich für VM Insights vor, bevor Sie die VM-Überwachung aktivieren. In diesem Schritt werden die erforderlichen Lösungen installiert, die die Datensammlung über den Log Analytics-Agent unterstützen. Sie nehmen diese Konfiguration nur einmal für jeden Arbeitsbereich vor. Ausführliche Informationen zu dieser Konfiguration mithilfe des Azure-Portals sowie anderen Methoden finden Sie unter [Übersicht zum Aktivieren von VM Insights](vminsights-enable-overview.md).

## <a name="send-an-activity-log-to-a-log-analytics-workspace"></a>Senden eines Aktivitätsprotokolls an einen Log Analytics-Arbeitsbereich
Sie können die Plattformmetriken und das Aktivitätsprotokoll anzeigen, die für jeden VM-Host im Azure-Portal gesammelt wurden. Senden Sie diese Daten an denselben Log Analytics-Arbeitsbereich wie VM Insights, um sie mit den anderen Überwachungsdaten zu analysieren, die für die VM gesammelt wurden. Möglicherweise haben Sie diese Aufgabe bereits ausgeführt, als Sie die Überwachung für andere Azure-Ressourcen konfiguriert haben, da ein einziges Aktivitätsprotokoll für alle Ressourcen in einem Azure-Abonnement verwendet wird.

Es entstehen keine Kosten für die Erfassung oder Aufbewahrung von Aktivitätsprotokolldaten. Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung zum Senden des Aktivitätsprotokolls an Ihren Log Analytics-Arbeitsbereich finden Sie unter [Erstellen von Diagnoseeinstellungen](../essentials/diagnostic-settings.md).

### <a name="network-requirements"></a>Netzwerkanforderungen
Der Log Analytics-Agent für Linux und Windows kommuniziert nach außen über TCP-Port 443 mit dem Azure Monitor-Dienst. Der Dependency-Agent verwendet den Log Analytics-Agent für die gesamte Kommunikation, sodass keine weiteren Ports benötigt werden. Ausführliche Informationen zum Konfigurieren Ihrer Firewall und Ihres Proxys finden Sie unter [Netzwerkanforderungen](../agents/log-analytics-agent.md#network-requirements).

:::image type="content" source="media/monitor-virtual-machines/network-diagram.png" alt-text="Diagramm: Darstellung des Netzwerks" lightbox="media/monitor-virtual-machines/network-diagram.png":::

### <a name="gateway"></a>Gateway
Mit dem Log Analytics-Gateway können Sie die Kommunikation von Ihren lokalen Computern über ein einzelnes Gateway leiten. Sie können die Azure Arc-fähigen Server-Agents jedoch nicht mit dem Log Analytics-Gateway verwenden. Wenn Ihre Sicherheitsrichtlinie ein Gateway vorschreibt, müssen Sie die Agents für Ihre lokalen Computer manuell installieren. Ausführliche Informationen zum Konfigurieren und Verwenden des Log Analytics-Gateways finden Sie unter [Log Analytics-Gateway](../agents/gateway.md).

### <a name="azure-private-link"></a>Azure Private Link
Mit Azure Private Link können Sie einen privaten Endpunkt für Ihren Log Analytics-Arbeitsbereich erstellen. Nach dessen Konfiguration müssen alle Verbindungen mit dem Arbeitsbereich über diesen privaten Endpunkt hergestellt werden. Private Link funktioniert mithilfe von DNS-Überschreibungen, sodass die einzelnen Agents nicht konfiguriert werden müssen. Weitere Informationen zu Private Link finden Sie unter [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Monitor](../logs/private-link-security.md).

## <a name="prepare-hybrid-machines"></a>Vorbereiten von Hybridcomputern
Ein Hybridcomputer ist jeder Computer, der nicht in Azure ausgeführt wird. Es handelt sich um eine VM, die in einer anderen Cloud ausgeführt oder gehostet wird, oder um einen virtuellen oder physischen Computer, der lokal in Ihrem Rechenzentrum ausgeführt wird. Verwenden Sie [Azure Arc-fähige Server](../../azure-arc/servers/overview.md) für Hybridcomputer, damit Sie sie ähnlich wie Ihre Azure-VMs verwalten können. Sie können VM Insights in Azure Monitor verwenden, um die Überwachung für Azure Arc-fähige Server auf die gleiche Weise zu aktivieren wie für Azure-VMs. Eine vollständige Anleitung zum Vorbereiten Ihrer Hybridcomputer für Azure finden Sie unter [Planen und Bereitstellen von Servern mit Azure Arc-Unterstützung](../../azure-arc/servers/plan-at-scale-deployment.md). Diese Aufgabe umfasst das Aktivieren einzelner Computer und die Verwendung [Azure Policy](../../governance/policy/overview.md), um Ihre gesamte Hybridumgebung bedarfsgerecht zu unterstützen.

Es entstehen keine zusätzlichen Kosten für Azure Arc-fähige Server, aber es können Kosten für verschiedene Optionen anfallen, die Sie aktivieren. Weitere Informationen finden Sie in der [Azure Arc-Preisübersicht](https://azure.microsoft.com/pricing/details/azure-arc/). Es entstehen Kosten für die im Arbeitsbereich gesammelten Daten, nachdem die Hybridcomputer für VM Insights aktiviert wurden.

### <a name="machines-that-cant-use-azure-arc-enabled-servers"></a>Computer, die keine Azure Arc-fähigen Server verwenden können
Wenn Sie über Hybridcomputer verfügen, die den folgenden Kriterien entsprechen, können diese keine Azure Arc-fähigen Server verwenden:

- Das Betriebssystem des Computers wird von den Server-Agents, die von Azure Arc aktiviert werden, nicht unterstützt. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](../../azure-arc/servers/agent-overview.md#prerequisites).
- Ihre Sicherheitsrichtlinie lässt nicht zu, dass Computer eine direkte Verbindung mit Azure herstellen. Der Log Analytics-Agent kann das [Log Analytics-Gateway](../agents/gateway.md) unabhängig davon verwenden, ob Azure Arc-fähige Server installiert sind oder nicht. Die von Azure Arc aktivierten Server-Agents müssen eine direkte Verbindung mit Azure herstellen.

Sie können diese Computer trotzdem mit Azure Monitor überwachen, aber Sie müssen die zugehörigen Agents manuell installieren. Informationen zum manuellen Installieren des Log Analytics-Agents und des Dependency-Agents auf diesen Hybridcomputern finden Sie unter [Aktivieren von VM Insights für eine Hybrid-VM](vminsights-enable-hybrid.md).

> [!NOTE]
> Der private Endpunkt für Azure Arc-fähige Server befindet sich derzeit in der öffentlichen Vorschau. Mit dem Endpunkt können Ihre Hybridcomputer eine sichere Verbindung mit Azure herstellen, indem sie eine private IP-Adresse aus Ihrem virtuellen Netzwerk verwenden.

## <a name="enable-vm-insights-on-machines"></a>Aktivieren von VM Insights für Computer
Nachdem Sie VM Insights für einen Computer aktiviert haben, werden der Log Analytics-Agent und der Dependency-Agent installiert und eine Verbindung mit einem Arbeitsbereich hergestellt. Anschließend wird mit dem Sammeln von Leistungsdaten begonnen. Sie können Leistungsansichten und Arbeitsmappen verwenden, um Trends für eine Vielzahl von Gastbetriebssystemmetriken zu analysieren. Durch das Aktivieren der Zuordnungsfunktion von VM Insights können Sie ausgeführte Prozesse und Abhängigkeiten zwischen Rechnern analysieren, und Sie können die Daten sammeln, die Sie für die Erstellung einer Vielzahl von Warnungsregeln benötigen.

Sie können VM Insights für einzelne Computer aktivieren, indem Sie die gleichen Methoden für Azure-VMs und Azure Arc-fähige Server verwenden. Zu diesen Methoden gehören das Onboarding einzelner Computer mit den Azure-Portal oder mithilfe von Azure Resource Manager-Vorlagen oder das bedarfsorientierte Aktivieren von Computern mithilfe von Azure Policy. Es entstehen keine direkten Kosten für VM Insights, aber es fallen Gebühren für die Erfassung und Aufbewahrung von Daten an, die im Log Analytics-Arbeitsbereich gesammelt werden.

Informationen zu den verschiedenen Optionen zum Aktivieren von VM Insights für Ihre Computer finden Sie unter [Übersicht zum Aktivieren von VM Insights](vminsights-enable-overview.md). Informationen zum Erstellen einer Richtlinie, die VM Insights automatisch für alle neuen Computer während ihrer Erstellung aktiviert, finden Sie unter [Aktivieren von VM Insights mithilfe von Azure Policy](vminsights-enable-policy.md).


## <a name="send-guest-performance-data-to-metrics"></a>Senden von Gastleistungsdaten an Metriken
Der [Azure Monitor Agent](../agents/azure-monitor-agent-overview.md) ersetzt den Log Analytics Agent, wenn er Azure Monitor, Microsoft Defender für Cloud und Microsoft Sentinel vollständig unterstützt. Bis dahin kann er mit dem Log Analytics-Agent installiert werden, um Leistungsdaten zum Gastbetriebssystem von Computern an Azure Monitor-Metriken zu senden. Mit dieser Konfiguration können Sie die Daten anhand des Metrik-Explorers auswerten und Metrikwarnungen verwenden.

Der Azure Monitor-Agent erfordert mindestens eine Datensammlungsregel, die definiert, welche Daten gesammelt und wohin diese Daten gesendet werden sollen. Eine einzelne Datensammlungsregel kann von allen Computern in derselben Ressourcengruppe verwendet werden.

Erstellen Sie eine einzelne Datensammlungsregel für jede Ressourcengruppe mit zu überwachenden Computern, indem Sie die folgende Datenquelle verwenden: 

- **Datenquellentyp**: Leistungsindikatoren
- **Ziel**: Azure Monitor-Metriken

Achten Sie darauf, keine Daten an Protokolle zu senden. Dies würde zu doppelten Daten führen, wenn die Daten bereits vom Log Analytics-Agent erfasst werden.

Sie können einen Azure Monitor-Agent für einzelne Computer installieren, indem Sie die gleichen Methoden für Azure-VMs und Azure Arc-fähige Server verwenden. Zu diesen Methoden gehören das Onboarding einzelner Computer mit den Azure-Portal oder mithilfe von Resource Manager-Vorlagen oder das bedarfsorientierte Aktivieren von Computern mithilfe von Azure Policy. Installieren Sie den Agent für Hybridcomputer, die Azure Arc-fähige Server nicht verwenden können, manuell.

Informationen zum Erstellen einer Datensammlungsregel und zum Bereitstellen des Azure Monitor-Agents für einen oder mehrere Agents mithilfe des Azure-Portals finden Sie unter [Erstellen einer Regel und einer Zuordnung im Azure-Portal](../agents/data-collection-rule-azure-monitor-agent.md). Weitere Installationsmethoden werden unter [Installieren des Azure Monitor-Agents](../agents/azure-monitor-agent-install.md) beschrieben. Informationen zum Erstellen einer Richtlinie, die den Agent und die Datensammlungsregel automatisch auf allen neuen Computern bereitstellt, während sie erstellt werden, finden Sie unter [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](../best-practices.md).

## <a name="next-steps"></a>Nächste Schritte

* [Analysieren der für VMs gesammelten Überwachungsdaten](monitor-virtual-machine-analyze.md)
* [Erstellen von Warnungen anhand von gesammelten Daten](monitor-virtual-machine-alerts.md)
* [Überwachen von auf VMs ausgeführten Workloads](monitor-virtual-machine-workloads.md)
