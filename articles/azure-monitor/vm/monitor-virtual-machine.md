---
title: Überwachen von VMs mit Azure Monitor
description: Hier erfahren Sie, wie Sie mithilfe von Azure Monitor die Integrität und Leistung von VMs und deren Workloads überwachen.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/02/2021
ms.openlocfilehash: c1506fe5fae652c198d53b5b354e818ede5c8a46
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315784"
---
# <a name="monitor-virtual-machines-with-azure-monitor"></a>Überwachen von VMs mit Azure Monitor
In diesem Szenario wird beschrieben, wie Sie mithilfe von Azure Monitor die Integrität und Leistung von VMs und deren Workloads überwachen. Es umfasst die Sammlung wichtiger Telemetriedaten für die Überwachung, die Analyse und Visualisierung der gesammelten Daten, um Trends zu identifizieren, sowie die Konfiguration von Warnungen, um proaktiv über kritische Probleme informiert zu werden.

Dieser Artikel beschreibt das Szenario und stellt allgemeine Konzepte für die Überwachung virtueller Computer mit Azure Monitor zur Verfügung. Wenn Sie direkt zu einem bestimmten Bereich wechseln möchten, sehen Sie sich einen der anderen in der folgenden Tabelle aufgelisteten Artikel an, die Teil dieses Szenarios sind.

| Artikel | BESCHREIBUNG |
|:---|:---|
| [Aktivieren der Überwachung](monitor-virtual-machine-configure.md) | Konfigurieren Sie Azure Monitor für die Überwachung virtueller Computer. Dies umfasst die Aktivierung von VM-Erkenntnissen und die Aktivierung der einzelnen virtuellen Computer für die Überwachung.  |
| [Analyse](monitor-virtual-machine-analyze.md) | Analysieren Sie Überwachungsdaten, die Azure Monitor von den virtuellen Computern sowie deren Gastbetriebssystemen und Anwendungen gesammelt hat, um Trends und kritische Informationen zu identifizieren. |
| [Warnungen](monitor-virtual-machine-alerts.md)   | Erstellen Sie Warnungen, um kritische Probleme in Ihren Überwachungsdaten proaktiv zu identifizieren. |
| [Überwachen der Sicherheit](monitor-virtual-machine-security.md) | Entdecken Sie die Azure-Dienste für die Überwachung der Sicherheit virtueller Computer. |
| [Überwachen von Workloads](monitor-virtual-machine-workloads.md) | Überwachen Sie Anwendungen und andere Workloads, die auf Ihren virtuellen Computern ausgeführt werden. |

> [!IMPORTANT]
> Dieses Szenario beinhaltet nur Features, die allgemein verfügbar sind. Features, die sich derzeit in der öffentlichen Vorschau befinden, z. B. die [Integrität der Gast-VM](vminsights-health-overview.md), haben das Potenzial, die hier gemachten Empfehlungen erheblich zu verändern. Das Szenario wird regelmäßig aktualisiert, sobald Vorschau-Features allgemein verfügbar werden.

## <a name="types-of-machines"></a>Computertypen
Dieses Szenario beinhaltet die Überwachung der folgenden Computertypen mit Azure Monitor. Viele der hier beschriebenen Prozesse sind bei allen Computertypen identisch. Überlegungen zu verschiedenen Computertypen werden an den entsprechenden Stellen eindeutig identifiziert. Zu den Computertypen gehören: 

- Virtuelle Azure-Computer
- Azure-VM-Skalierungsgruppen
- Hybridcomputer, bei denen es sich um virtuelle Computer handelt, die in anderen Clouds, durch einen verwalteten Dienstanbieter oder lokal ausgeführt werden. Dazu gehören auch physische Computer, die lokal ausgeführt werden.

## <a name="layers-of-monitoring"></a>Überwachungsebenen
Ein virtueller Computer verfügt grundsätzlich über vier Ebenen, die überwacht werden müssen. Jede Ebene verfügt über einen eigenen Satz von Telemetriedaten und Überwachungsanforderungen. 

| Ebene | BESCHREIBUNG |
|:---|:---|
| VM-Host | Der Host für virtuelle Computer in Azure. Azure Monitor hat keinen Zugriff auf den Host in anderen Clouds, sondern muss sich auf die Informationen verlassen, die vom Gastbetriebssystem gesammelt werden. Der Host kann für das Nachverfolgen von bestimmten Aktivitäten, wie z. B. Konfigurationsänderungen, nützlich sein. Er wird in der Regel jedoch nicht für bedeutende Warnungen verwendet. |
| Gastbetriebssystem | Das Betriebssystem, das auf dem virtuellen Computer ausgeführt wird. Dabei handelt es sich um eine Windows- oder Linux-Version. Das Gastbetriebssystem verfügt über eine beträchtliche Menge an Überwachungsdaten, z. B. Leistungsdaten und Ereignisse. VM Insights in Azure Monitor verfügt über vielfältige Logikoptionen zur Überwachung der Integrität und Leistung des Gastbetriebssystems. |
| Arbeitsauslastungen | Workloads, die in dem Gastbetriebssystem ausgeführt werden und Ihre Geschäftsanwendungen unterstützen. Azure Monitor bietet eine vordefinierte Überwachung für bestimmte Workloads. In der Regel müssen Sie die Datensammlung und Warnungen für andere Workloads konfigurieren, indem Sie die von diesen Workloads generierten Überwachungsdaten verwenden. |
| Application | Die Geschäftsanwendung, die von Ihren virtuellen Computern abhängt, kann mithilfe von [Application Insights](../app/app-insights-overview.md) überwacht werden. 

:::image type="content" source="media/monitor-virtual-machines/monitoring-layers.png" alt-text="Diagramm der Überwachungsebenen" lightbox="media/monitor-virtual-machines/monitoring-layers.png":::

## <a name="vm-insights"></a>VM Insights
Dieses Szenario konzentriert sich auf [VM Insights](../vm/vminsights-overview.md), das primäre Feature in Azure Monitor zur Überwachung von virtuellen Computern. VM Insights bietet die folgenden Funktionen:

- Vereinfachtes Onboarding von Agents zum Aktivieren der Überwachung eines VM-Gastbetriebssystems und von Workloads. 
- Vordefinierte Leistungsdiagramme und -arbeitsmappen, mit denen Sie Kernleistungsmetriken aus dem VM-Gastbetriebssystem analysieren und Trends erkennen können.
- Abhängigkeitsdiagramm, dass Prozesse anzeigt, die auf den einzelnen virtuellen Computern ausgeführt werden, und die mit anderen Computern und externen Quellen verbundenen Komponenten.

## <a name="agents"></a>Agents
Jedes Überwachungstool (z. B. Azure Monitor) erfordert, dass ein Agent auf dem Computer installiert ist, mit dem Daten von dem Gastbetriebssystem gesammelt werden können. Azure Monitor verfügt derzeit über mehrere Agents, die unterschiedliche Daten sammeln, Daten an unterschiedliche Speicherorte senden und verschiedene Features unterstützen. Über VM Insights wird die Bereitstellung und Konfiguration der Agents verwaltet, die von den meisten Kunden genutzt werden. In der folgenden Tabelle werden verschiedene Agents für unterschiedliche Szenarien beschrieben. Eine ausführliche Beschreibung der einzelnen Agents finden Sie unter [Übersicht über die Azure Monitor-Agents](../agents/agents-overview.md).

> [!NOTE]
> Der Azure Monitor-Agent wird den Log Analytics-Agent, die Diagnoseerweiterung und den Telegraf-Agent vollständig ersetzen, sobald er die erforderliche Funktionalität erreicht. Diese anderen Agents sind weiterhin für Features wie VM Insights, Microsoft Defender für Cloud und Microsoft Sentinel erforderlich.

- [Azure Monitor-Agent](../agents/agents-overview.md#azure-monitor-agent): Unterstützt virtuelle Computer in Azure, anderen Cloudumgebungen und lokal. Sendet Daten an Azure Monitor Metrics und Azure Monitor Logs. Wenn der Agent VM Insights, Microsoft Defender für Cloud und Microsoft Sentinel vollständig unterstützt, kann er den Protokollanalyse-Agent und die Diagnoseerweiterung vollständig ersetzen.
- [Log Analytics-Agent](../agents/agents-overview.md#log-analytics-agent): Unterstützt virtuelle Computer in Azure, anderen Cloudumgebungen und lokal. Sendet Daten an Azure Monitor Logs. Unterstützt VM Insights-Lösungen und Überwachungslösungen Derselbe Agent wird für den System Center Operations Manager verwendet.
- [Dependency-Agent](../agents/agents-overview.md#dependency-agent): Sammelt Daten über Prozesse, die auf dem virtuellen Computer ausgeführt werden, und über ihre Abhängigkeiten. Basiert auf dem Log Analytics-Agent zum Übertragen von Daten in Azure und unterstützt VM Insights, Dienstzuordnung und Wire Data 2.0-Lösungen.
- [Azure-Diagnose-Erweiterung](../agents/agents-overview.md#azure-diagnostics-extension): Nur für Azure Monitor-VMs verfügbar. Kann Daten an Azure Event Hubs und Azure Storage senden.

## <a name="next-steps"></a>Nächste Schritte

[Analysieren der für VMs gesammelten Überwachungsdaten](monitor-virtual-machine-analyze.md)
