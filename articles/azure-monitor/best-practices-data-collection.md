---
title: Best Practices für Azure Monitor – Konfigurieren der Datensammlung
description: Anleitungen und Empfehlungen zum Konfigurieren der Datensammlung in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b5522cb26f7f8fe486b4b690938a23c7e75c746a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315956"
---
# <a name="azure-monitor-best-practices---configure-data-collection"></a>Best Practices für Azure Monitor – Konfigurieren der Datensammlung
Dieser Artikel ist Teil des Szenarios [Empfehlungen zum Konfigurieren von Azure Monitor](best-practices.md). Es werden die empfohlenen Schritte zum Konfigurieren der Datensammlung beschrieben, die zum Aktivieren von Azure Monitor-Features für Ihre Azure- und Hybridanwendungen und -ressourcen benötigt wird.

> [!IMPORTANT]
> Die Features von Azure Monitor und deren Konfiguration variieren abhängig von den geschäftlichen Anforderungen und den Kosten der aktivierten Features. Bei jedem der nachfolgenden Schritte wird angegeben, ob mögliche Kosten anfallen. Sie sollten diese Kosten abschätzen, bevor Sie fortfahren. Ausführliche Preisinformationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
Sie benötigen mindestens einen Log Analytics-Arbeitsbereich, um [Azure Monitor-Protokolle](logs/data-platform-logs.md) zu aktivieren. Dies ist für das Erfassen von Daten wie Protokollen von Azure-Ressourcen, das Sammeln von Daten vom Gastbetriebssystem virtueller Azure-Computer und die meisten Azure Monitor-Erkenntnisse erforderlich. Andere Dienste wie Microsoft Sentinel und Microsoft Defender für Cloud verwenden ebenfalls einen Log Analytics-Arbeitsbereich und können denselben wie Azure Monitor verwenden. Sie können für diese Überwachung mit einem einzelnen Arbeitsbereich beginnen. Sehen Sie sich aber auch den Artikel [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](logs/design-logs-deployment.md) an, in dem Sie erfahren, wann Sie mehrere Arbeitsbereiche verwenden sollten.

Es fallen keine Kosten für das Erstellen eines Log Analytics-Arbeitsbereichs an, allerdings wird möglicherweise eine Gebühr berechnet, wenn Sie das Erfassen von Daten darin konfigurieren. Ausführliche Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](logs/manage-cost-storage.md).  

Eine Anleitung zum Erstellen eines ersten Log Analytics-Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](logs/quick-create-workspace.md). Unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](logs/manage-access.md) erfahren Sie, wie Sie den Zugriff konfigurieren. Sie können skalierbare Methoden wie etwa Resource Manager-Vorlagen verwenden, um Arbeitsbereiche zu konfigurieren. Dies ist häufig jedoch nicht erforderlich, da die meisten Umgebungen nur eine minimale Anzahl erfordern.

## <a name="collect-data-from-azure-resources"></a>Erfassen von Daten von Azure-Ressourcen
Ein Teil der Überwachung von Azure-Ressourcen ist automatisch verfügbar, ohne dass eine Konfiguration erforderlich ist. Für das Sammeln zusätzlicher Überwachungsdaten müssen Sie jedoch weitere Konfigurationsschritte ausführen. In der folgenden Tabelle werden die erforderlichen Konfigurationsschritte zum Erfassen aller verfügbaren Daten von Ihren Azure-Ressourcen erläutert, einschließlich der Daten, die im jeweiligen Schritt an Azure Monitor-Metriken und Azure Monitor-Protokolle gesendet werden. Jeder Schritt wird in den nachstehenden Abschnitten ausführlicher beschrieben.

[![ Bereitstellen der Azure-Ressourcenüberwachung](media/best-practices-data-collection/best-practices-azure-resources.png)](media/best-practices-data-collection/best-practices-azure-resources.png#lightbox)

### <a name="collect-tenant-and-subscription-logs"></a>Erfassen von Mandanten- und Abonnementprotokollen
Die [Azure Active Directory-Protokolle für Ihren Mandanten](../active-directory/reports-monitoring/overview-reports.md) und das [Aktivitätsprotokoll](essentials/platform-logs-overview.md) für Ihr Abonnement werden automatisch gesammelt. Wenn Sie diese jedoch an einen Log Analytics-Arbeitsbereich senden, können Sie die Ereignisse zusammen mit anderen Protokolldaten mithilfe von Protokollabfragen in Log Analytics analysieren. Dies ermöglicht es Ihnen auch, Protokollabfragewarnungen zu erstellen. Dies ist die einzige Möglichkeit, Warnungen für Azure Active Directory-Protokolle zu erstellen und komplexere Logik als mit Aktivitätsprotokollwarnungen bereitzustellen.

Es fallen keine Kosten für das Senden des Aktivitätsprotokolls an einen Arbeitsbereich an, es gibt jedoch eine Gebühr für Datenerfassung und -aufbewahrung für Azure Active Directory-Protokolle. 

Weitere Informationen zum Erstellen einer Diagnoseeinstellung für Ihren Mandanten und Ihr Abonnement, um Protokolleinträge an Ihren Log Analytics-Arbeitsbereich zu senden, finden Sie unter [Integrieren von Azure AD-Protokollen mit Azure Monitor-Protokollen](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) und [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](essentials/diagnostic-settings.md). 




### <a name="collect-resource-logs-and-platform-metrics"></a>Erfassen von Ressourcenprotokollen und Plattformmetriken
Ressourcen in Azure generieren automatisch [Ressourcenprotokolle](essentials/platform-logs-overview.md), die Details zu den in der Ressource ausgeführten Vorgängen enthalten. Anders als bei Plattformmetriken müssen Sie das Erfassen von Ressourcenprotokollen jedoch konfigurieren. Erstellen Sie eine Diagnoseeinstellung, um die Protokolle an einen Log Analytics-Arbeitsbereich zu übermitteln und mit den anderen Daten zu verknüpfen, die mit Azure Monitor-Protokollen verwendet werden. Die gleiche Diagnoseeinstellung kann auch verwendet werden, um Plattformmetriken für die meisten Ressourcen an denselben Arbeitsbereich zu senden, sodass Sie Metrikdaten mithilfe von Protokollabfragen zusammen mit anderen gesammelten Daten analysieren können.

Es fallen Kosten für das Erfassen von Ressourcenprotokollen in Ihrem Log Analytics-Arbeitsbereich an. Wählen Sie daher nur Protokollkategorien mit relevanten Daten aus. Durch das Erfassen aller Kategorien entstehen Kosten für das Sammeln von Daten mit geringem Nutzen. Eine Beschreibung der Kategorien und Empfehlungen für die Erfassung finden Sie in der Überwachungsdokumentation für jeden Azure-Dienst. Ausführliche Informationen zur Kostenoptimierung Ihrer Protokollsammlung finden Sie auch unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](logs/manage-cost-storage.md).

Weitere Informationen zum Erstellen einer Diagnoseeinstellung für eine Azure-Ressource finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen und -metriken in Azure](essentials/diagnostic-settings.md#create-in-azure-portal). 

Da für jede Azure-Ressource eine Diagnoseeinstellung erstellt werden muss, verwenden Sie Azure Policy, um beim Erstellen der einzelnen Ressourcen automatisch eine Diagnoseeinstellung zu erstellen. Jeder Azure-Ressourcentyp verfügt über einen eindeutigen Satz von Kategorien, die in der Diagnoseeinstellung aufgelistet werden müssen. Daher ist für jeden Ressourcentyp eine separate Richtliniendefinition erforderlich. Einige Ressourcentypen verfügen über integrierte Richtliniendefinitionen, die Sie ohne Änderungen zuweisen können. Bei anderen Ressourcentypen müssen Sie eine benutzerdefinierte Definition erstellen.

Unter [Bedarfsorientierte Erstellung mithilfe von Azure Policy](essentials/diagnostic-settings.md#create-at-scale-using-azure-policy) finden Sie ein Verfahren zum Erstellen von Richtliniendefinitionen für einen bestimmten Azure-Dienst sowie Details zum bedarfsorientierten Erstellen von Diagnoseeinstellungen.

### <a name="enable-insights"></a>Aktivieren von Erkenntnissen
Erkenntnisse bieten eine angepasste Überwachungsfunktion für einen bestimmten Azure-Dienst. Sie nutzen dieselben Daten, die bereits erfasst werden, wie z. B. Plattformmetriken und Ressourcenprotokolle, stellen jedoch benutzerdefinierte Arbeitsmappen zur Verfügung, die Sie bei der Identifizierung und Analyse der wichtigsten Daten unterstützen. Die meisten Erkenntnisse stehen im Azure-Portal zur Verfügung, ohne dass eine Konfiguration erforderlich ist, abgesehen von der Erfassung von Ressourcenprotokollen für diesen Dienst. In der Dokumentation zur Überwachung für jeden Azure-Dienst erfahren Sie, ob Erkenntnisse verfügbar sind und ob eine Konfiguration erforderlich ist.

Es fallen keine Kosten für Erkenntnisse an, Ihnen werden jedoch möglicherweise die gesammelten Daten in Rechnung gestellt.

Unter [Was wird von Azure Monitor überwacht?](monitor-reference.md) finden Sie eine Liste der in Azure Monitor verfügbaren Erkenntnisse und Lösungen. Informationen zur jeweiligen Konfiguration und den Preisen finden Sie in der zugehörigen Dokumentation.

> [!IMPORTANT]
> Die folgenden Erkenntnisse sind deutlich komplexer als andere und beinhalten zusätzliche Anleitungen für ihre Konfiguration.
> 
> - [VM Insights](#monitor-virtual-machines)
> - [Container Insights](#monitor-containers)
> - [Überwachen von Anwendungen](#monitor-applications)


## <a name="monitor-virtual-machines"></a>Überwachen virtueller Maschinen
VMs generieren ähnliche Daten wie andere Azure-Ressourcen, aber sie benötigen einen Agent, um Daten vom Gastbetriebssystem zu sammeln. Aufgrund der unterschiedlichen Workloads, die auf VMs ausgeführt werden, gelten auch besondere Überwachungsanforderungen. Ein dediziertes Szenario zur VM-Überwachung mit Azure Monitor finden Sie unter [Überwachen von virtuellen Azure-Computern mit Azure Monitor](vm/monitor-vm-azure.md).

## <a name="monitor-containers"></a>Überwachen von Containern
VMs generieren ähnliche Daten wie andere Azure-Ressourcen, erfordern jedoch eine Containerversion des Log Analytics-Agents, um die erforderlichen Daten zu sammeln. Container Insights hilft Ihnen bei der Vorbereitung Ihrer Containerumgebung für die Überwachung und arbeitet mit Drittanbietertools zusammen, um eine umfassende Überwachung von AKS und den unterstützten Workflows zu ermöglichen. Unter [Überwachen von Azure Kubernetes Service (AKS) mit Azure Monitor](../aks/monitor-aks.md?toc=/azure/azure-monitor/toc.json) finden Sie ein dediziertes Szenario zum Überwachen von AKS mit Azure Monitor.

## <a name="monitor-applications"></a>Überwachen von Anwendungen
Azure Monitor überwacht Ihre benutzerdefinierten Anwendungen mithilfe von [Application Insights](app/app-insights-overview.md), das Sie für jede zu überwachende Anwendung konfigurieren müssen. Der Konfigurationsvorgang variiert abhängig vom Typ der zu überwachenden Anwendung und der Art der gewünschten Überwachung. Die von Application Insights gesammelten Daten werden je nach Feature in Azure Monitor-Metriken, Azure Monitor-Protokollen oder Azure Blob Storage gespeichert. Leistungsdaten werden ohne zusätzliche Konfiguration in Azure Monitor-Metriken und Azure Monitor-Protokollen gespeichert.

### <a name="create-an-application-resource"></a>Erstellen einer Anwendungsressource
Application Insights ist das Azure Monitor-Feature zur Überwachung Ihrer cloudnativen und hybriden Anwendungen.

Sie müssen in Application Insights für jede Anwendung, die Sie überwachen möchten, eine Ressource erstellen. Die von Application Insights gesammelten Protokolldaten für eine arbeitsbereichsbasierte Anwendung werden in Azure Monitor-Protokollen gespeichert. Protokolldaten für klassische Anwendungen werden getrennt vom Log Analytics-Arbeitsbereich gespeichert, wie es unter [Datenstruktur](logs/data-platform-logs.md#data-structure) beschrieben ist.

 Wenn Sie die Anwendung erstellen, müssen Sie auswählen, ob Sie eine klassische oder eine arbeitsbereichsbasierte Anwendung verwenden möchten. Weitere Informationen zum Erstellen einer klassischen Anwendung finden Sie unter [Erstellen einer Application Insights-Ressource](app/create-new-resource.md). Weitere Informationen zum Erstellen einer arbeitsbereichsbasierten Anwendung finden Sie unter [Arbeitsbereichsbasierte Application Insights-Ressourcen (Vorschau)](app/create-workspace-resource.md).


 Eine grundlegende Entwurfsentscheidung ist die Verwendung einer separaten oder einzelnen Anwendungsressource für mehrere Anwendungen. Separate Ressourcen können Kosten sparen und verhindern, dass Daten aus verschiedenen Anwendungen vermischt werden, wohingegen eine einzige Ressource die Überwachung vereinfachen kann, indem sie alle relevanten Telemetriedaten zusammenfasst. Detaillierte Kriterien für diese Entscheidung finden Sie unter [Wie viele Application Insights-Ressourcen soll ich bereitstellen?](app/separate-resources.md).



### <a name="configure-codeless-or-code-based-monitoring"></a>Konfigurieren der Überwachung mit oder ohne Code
Um die Überwachung für eine Anwendung zu aktivieren, müssen Sie entscheiden, ob Sie die codebasierte Überwachung oder die Überwachung ohne Code verwenden möchten. Der Konfigurationsvorgang variiert abhängig von dieser Entscheidung und vom Typ der Anwendung, die Sie überwachen möchten.

Die **Überwachung ohne Code** ist am einfachsten zu implementieren und kann nach der Codeentwicklung konfiguriert werden. Es sind keine Anpassungen an Ihrem Code erforderlich. Weitere Informationen zum Aktivieren der Überwachung in Abhängigkeit von Ihrer Anwendung finden Sie unter den folgenden Ressourcen.

- [Anwendungen, die in Azure-Web-Apps gehostet werden](app/azure-web-apps.md)
- [Java-Anwendungen](app/java-in-process-agent.md)
- [Auf IIS gehostete ASP.NET-Anwendungen auf virtuellem Azure-Computer oder in Azure-VM-Skalierungsgruppe](app/azure-vm-vmss-apps.md)
- [In IIS lokal gehostete ASP.NET-Anwendungen](app/status-monitor-v2-overview.md)


Die **codebasierte Überwachung** bietet mehr Anpassungsmöglichkeiten und sammelt zusätzliche Telemetriedaten. Sie erfordert jedoch das Hinzufügen einer Abhängigkeit von NuGet-Paketen des Application Insights SDK zu Ihrem Code. Weitere Informationen zum Aktivieren der Überwachung in Abhängigkeit von Ihrer Anwendung finden Sie unter den folgenden Ressourcen.

- [ASP.NET-Anwendungen](app/asp-net.md)
- [ASP.NET Core-Anwendungen](app/asp-net-core.md)
- [.NET-Konsolenanwendungen](app/console.md)
- [Java](app/java-in-process-agent.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Andere Plattformen](app/platforms.md)

### <a name="configure-availability-testing"></a>Konfigurieren von Verfügbarkeitstests
Verfügbarkeitstests in Application Insights sind wiederkehrende Tests, mit denen die Verfügbarkeit und Reaktionsfähigkeit Ihrer Anwendung in regelmäßigen Abständen von Standorten auf der ganzen Welt überwacht wird. Sie können einen einfachen Ping-Test kostenlos erstellen, oder Sie erstellen eine Sequenz von Webanforderungen, um Benutzertransaktionen zu simulieren, dies verursacht jedoch Kosten. 

Unter [Überwachen der Verfügbarkeit von Websites](app/monitor-web-app-availability.md) finden Sie eine Zusammenfassung der verschiedenen Arten von Tests sowie Details zu deren Erstellung.

### <a name="configure-profiler"></a>Konfigurieren von Profiler
Profiler in Application Insights stellt Leistungsüberwachungen für .NET-Anwendungen bereit. Der Dienst unterstützt Sie beim Identifizieren des langsamsten Codepfads, der beim Verarbeiten einer bestimmten Webanforderung am längsten dauert. Der Vorgang zum Konfigurieren von Profiler variiert je nach Anwendungstyp. 

Einzelheiten zum Konfigurieren von Profiler finden Sie unter [Profilerstellung für Produktionsanwendungen in Azure mit Application Insights](app/profiler-overview.md).

### <a name="configure-snapshot-debugger"></a>Konfigurieren des Momentaufnahmedebuggers
Der Momentaufnahmedebugger in Application Insights überwacht Telemetriedaten von Ausnahmen Ihrer .NET-Anwendung und sammelt Momentaufnahmen zu den am häufigsten ausgelösten Ausnahmen, damit Sie über die erforderlichen Informationen zur Diagnose von Problemen in der Produktion verfügen. Das Konfigurieren des Momentaufnahmedebuggers variiert abhängig vom Anwendungstyp. 

Ausführliche Informationen zum Konfigurieren des Momentaufnahmedebuggers finden Sie unter [Debugmomentaufnahmen von Ausnahmen in .NET-Apps](app/snapshot-debugger.md).

## <a name="next-steps"></a>Nächste Schritte

- Wenn die Datenerfassung für all Ihre Azure-Ressourcen konfiguriert ist, finden Sie unter [Analysieren und Visualisieren von Daten](best-practices-analysis.md) die Optionen zur Analyse dieser Daten. 
