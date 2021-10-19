---
title: Azure Monitor für bestehende Operations Manager-Kunden
description: Leitfaden für bestehende Benutzer von Operations Manager, um im Rahmen des Übergangs zur Cloud die Überwachung bestimmter Workloads auf Azure Monitor zu übertragen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: ccc46073ef81977f5cd467c84dc96946980cc8c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714469"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor für bestehende Operations Manager-Kunden
Dieser Artikel enthält Anleitungen für Kunden, die derzeit [System Center Operations Manager](/system-center/scom/welcome) verwenden und bei der Migration von Geschäftsanwendungen und anderen Ressourcen zu Azure einen Übergang zu [Azure Monitor](overview.md) planen. Es wird davon ausgegangen, dass das letztendliche Ziel ein vollständiger Übergang in die Cloud ist, bei dem so viele Operations Manager-Funktionen wie möglich durch Azure Monitor ersetzt werden, ohne dass die betrieblichen Anforderungen Ihres Geschäfts- und IT-Bereichs beeinträchtigt werden. 

Die in diesem Artikel genannten Empfehlungen ändern sich, sobald Funktionen von Azure Monitor und Operations Manager hinzugefügt werden. Die grundlegende Strategie bleibt jedoch unverändert.

> [!IMPORTANT]
> Die Implementierung einiger der hier beschriebenen Azure Monitor-Funktionen ist mit Kosten verbunden. Deshalb sollten Sie deren Wert vor der Bereitstellung in der gesamten Umgebung abschätzen.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie [Operations Manager](/system-center/scom) bereits verwenden und zumindest über grundlegende Kenntnisse von [Azure Monitor](overview.md)verfügen. Einen umfassenden Vergleich zwischen den beiden Plattformen finden Sie unter [Leitfaden zur Cloudüberwachung: Übersicht über Überwachungsplattformen](/azure/cloud-adoption-framework/manage/monitor/platform-overview). In diesem Artikel werden bestimmte Funktionsunterschiede zwischen den beiden Plattformen erläutert, damit Sie einige der hier genannten Empfehlungen besser verstehen können. 


## <a name="general-strategy"></a>Allgemeine Strategie
Es gibt keine Migrationstools zum Konvertieren von Ressourcen von Operations Manager zu Azure Monitor, da sich die Plattformen grundlegend unterscheiden. Bei der Migration handelt es sich stattdessen um eine [Azure Monitor-Standardimplementierung](deploy.md), wobei Sie Operations Manager weiterhin verwenden. Während Sie Azure Monitor entsprechend Ihren Anforderungen für verschiedene Anwendungen und Komponenten anpassen und immer mehr Funktionen hinzukommen, können Sie damit beginnen, die Nutzung verschiedener Management Packs und Agents in Operations Manager einzustellen.

Die in diesem Artikel empfohlene allgemeine Strategie entspricht derjenigen im [Leitfaden zur Cloudüberwachung](/azure/cloud-adoption-framework/manage/monitor/), die ein [Überwachen der Hybrid Cloud](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) empfiehlt und Ihnen somit einen schrittweisen Übergang zur Cloud ermöglicht. Obwohl sich einige Funktionen überlappen können, bietet Ihnen diese Strategie die Möglichkeit, die vorhandenen Geschäftsprozesse beizubehalten, während Sie sich mit der neuen Plattform vertraut machen. Stellen Sie die Verwendung von Operations Manager-Funktionen lediglich dann ein, wenn Sie sie durch Azure Monitor ersetzen können. Die Verwendung mehrerer Überwachungstools erhöht zwar die Komplexität, bietet Ihnen jedoch die Möglichkeit, die Vorteile von Azure Monitor zur Überwachung von Cloudworkloads der nächsten Generation zu nutzen und gleichzeitig die Überwachung von Serversoftware und Infrastrukturkomponenten, die möglicherweise lokal oder in anderen Clouds vorhanden sind, durch Operations Manager beizubehalten. 


## <a name="components-to-monitor"></a>Zu überwachende Komponenten
Es ist hilfreich, die verschiedenen Typen von Workloads, die Sie überwachen müssen, in Kategorien einzuteilen, um jeweils eine eigene Überwachungsstrategie festzulegen. [Leitfaden zur Cloudüberwachung: Formulieren einer Überwachungsstrategie](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) enthält eine detaillierte Aufschlüsselung der verschiedenen Ebenen in Ihrer Umgebung, die überwacht werden müssen, wenn Sie von älteren Unternehmensanwendungen zu modernen Anwendungen in der Cloud übergehen.

Vor dem Übergang in die Cloud haben Sie Operations Manager zur Überwachung aller Ebenen verwendet. Wenn Sie die Umstellung mit Infrastructure-as-a-Service (IaaS) beginnen, verwenden Sie weiterhin Operations Manager für Ihre virtuellen Computer, beginnen aber mit der Nutzung von Azure Monitor für Ihre Cloudressourcen. Beim weiteren Übergang zu modernen Anwendungen mithilfe von Platform-as-a-Service (PaaS) können Sie sich stärker auf Azure Monitor konzentrieren und mit der Einstellung von Operations Manager-Funktionalität beginnen.


![Cloudmodelle](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Diese Ebenen können zur Vereinfachung in die folgenden Kategorien eingeteilt werden, die im weiteren Verlauf dieses Artikels näher beschrieben werden. Zwar lässt sich möglicherweise nicht jede Überwachungsworkload in Ihrer Umgebung exakt in eine dieser Kategorien einordnen, doch sollte jede einer bestimmten Kategorie so nahe kommen, dass die allgemeinen Empfehlungen angewendet werden können.

**Geschäftsanwendungen:** Anwendungen, die spezifische Funktionen für Ihr Unternehmen bieten. Diese können intern oder extern sein und werden häufig intern mithilfe von benutzerdefiniertem Code entwickelt. Ihre älteren Anwendungen werden in der Regel auf virtuellen oder physischen Computern unter Windows oder Linux gehostet, während Ihre neueren Anwendungen auf Anwendungsdiensten in Azure wie Azure-Web-Apps und Azure Functions basieren.

**Azure-Dienste:** Ressourcen in Azure, die Ihre in die Cloud migrierten Geschäftsanwendungen unterstützen. Dies umfasst Dienste wie Azure Storage, Azure SQL und Azure IoT. Dazu gehören auch virtuelle Azure-Computer, da sie wie andere Azure-Dienste überwacht werden, aber die Anwendungen und Software, die auf dem Gastbetriebssystem dieser virtuellen Computer ausgeführt werden, erfordern mehr Überwachung als der Host.

**Serversoftware:** Software, die auf virtuellen und physischen Computern ausgeführt wird, die Ihre Geschäftsanwendungen unterstützen, oder gepackte Anwendungen, die allgemeine Funktionalität für Ihr Unternehmen bereitstellen. Beispiele hierfür sind Internet Information Server (IIS), SQL Server, Exchange und SharePoint. Dazu gehört auch das Windows- oder Linux-Betriebssystem auf Ihren virtuellen und physischen Computern.

**Lokale Infrastruktur:** Spezifische Komponenten für Ihre lokale Umgebung, die überwacht werden müssen. Dies umfasst Ressourcen wie physische Server, Speicher und Netzwerkkomponenten. Hierbei handelt es sich um die Komponenten, die beim Übergang in die Cloud virtualisiert werden.

## <a name="sample-walkthrough"></a>Exemplarische Vorgehensweise
Es folgt eine exemplarische Vorgehensweise für die Migration von Operations Manager zu Azure Monitor. Diese soll nicht die vollständige Komplexität einer tatsächlichen Migration darstellen, gibt aber zumindest die grundlegenden Schritte und die Reihenfolge an. In den folgenden Abschnitten werden diese Schritte ausführlicher beschrieben.

Vor dem Verschieben von Komponenten in Azure basiert Ihre Umgebung auf virtuellen und physischen Computern, die lokal vorhanden sind oder sich bei einem Anbieter verwalteter Dienste befinden. Operations Manager wird zur Überwachung von Geschäftsanwendungen, Serversoftware und anderer Infrastrukturkomponenten in Ihrer Umgebung wie physische Server und Netzwerke verwendet. Sie nutzen Standard-Management Packs für Serversoftware, z. B. IIS, SQL Server und Software verschiedener Anbieter, und Sie optimieren diese Management Packs für Ihre spezifischen Anforderungen. Sie erstellen benutzerdefinierte Management Packs für Ihre Geschäftsanwendungen und anderen Komponenten, die mit den vorhandenen Management Packs nicht überwacht werden können, und konfigurieren Operations Manager zur Unterstützung Ihrer Geschäftsprozesse.

Die Migration zu Azure beginnt mit IaaS, wobei virtuelle Computer, die Geschäftsanwendungen unterstützen, in Azure verschoben werden. Die Überwachungsanforderungen für diese Anwendungen und die Serversoftware, von der sie abhängen, ändern sich nicht, und Sie verwenden weiterhin Operations Manager auf diesen Servern mit den vorhandenen Management Packs. 

Azure Monitor wird für Ihre Azure-Dienste aktiviert, sobald Sie ein Azure-Abonnement erstellen. Es werden automatisch Plattformmetriken und das Aktivitätsprotokoll erfasst, und Sie konfigurieren die zu erfassenden Ressourcenprotokolle, damit Sie alle verfügbaren Telemetriedaten mithilfe von Protokollabfragen interaktiv analysieren können. Sie können VM Insights für Ihre VMs (virtuelle Computer) aktivieren, um Überwachungsdaten aus Ihrer gesamten Umgebung gemeinsam zu analysieren und Beziehungen zwischen Computern und Prozessen zu ermitteln. Sie weiten die Verwendung von Azure Monitor auf Ihre lokalen physischen und virtuellen Computer aus, indem Sie Azure Arc-fähige Server darauf aktivieren. 

Sie aktivieren Application Insights für jede Ihrer Geschäftsanwendungen. Hiermit werden die verschiedenen Komponenten jeder Anwendung identifiziert, es wird mit der Erfassung von Nutzungs- und Leistungsdaten begonnen, und im Code auftretende Fehler werden ermittelt. Sie erstellen Verfügbarkeitstests, um Ihre externen Anwendungen proaktiv zu testen und Sie bei Leistungs- oder Verfügbarkeitsproblemen zu benachrichtigen. Obwohl Application Insights leistungsstarke Funktionen bietet, die Ihnen in Operations Manager nicht bereitstehen, sind Sie weiterhin auf benutzerdefinierte Management Packs angewiesen, die Sie für Ihre Geschäftsanwendungen entwickelt haben, da sie Überwachungsszenarien enthalten, die von Azure Monitor noch nicht abgedeckt werden. 

Da Ihnen Azure Monitor nun schon etwas vertrauter ist, beginnen Sie mit dem Erstellen von Warnungsregeln, die einige Management Pack-Funktionen ersetzen können, und setzen die Entwicklung Ihrer Geschäftsprozesse für die Verwendung der neuen Überwachungsplattform fort. Dadurch können Sie nach und nach Computer und Management Packs aus der Operations Manager-Verwaltungsgruppe entfernen. Sie verwenden weiterhin Management Packs für wichtige Serversoftware und die lokale Infrastruktur, achten jedoch weiterhin auf neue Funktionen in Azure Monitor, die es Ihnen ermöglichen, zusätzliche Funktionen außer Betrieb zu nehmen.

## <a name="monitor-azure-services"></a>Überwachen von Azure-Diensten
Azure-Dienste benötigen Azure Monitor, um Telemetriedaten zu erfassen, und dies wird in dem Moment aktiviert, in dem Sie ein Azure-Abonnement erstellen. Das [Aktivitätsprotokoll](essentials/activity-log.md) wird automatisch für das Abonnement erfasst, und [Plattformmetriken](essentials/data-platform-metrics.md) werden automatisch von allen von Ihnen erstellten Azure-Ressourcen gesammelt. Sie können sofort mit der Verwendung des [Metrik-Explorers](essentials/metrics-getting-started.md) beginnen. Dieser ähnelt den Leistungsansichten in der Operations-Konsole, bietet aber interaktive Analysen und [erweiterte Aggregationen](essentials/metrics-charts.md) von Daten. [Erstellen Sie eine Metrikwarnung](alerts/alerts-metric.md), um benachrichtigt zu werden, sobald ein Wert einen Schwellenwert überschreitet, oder [fügen Sie einem Azure-Dashboard ein Diagramm hinzu](essentials/metrics-charts.md#pinning-to-dashboards), um die Sichtbarkeit zu verbessern.

[![Metrik-Explorer](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Erstellen Sie eine Diagnoseeinstellung](essentials/diagnostic-settings.md) für jede Azure-Ressource, um Metriken und [Ressourcenprotokolle](essentials/resource-logs.md), die Details zum internen Betrieb der einzelnen Ressourcen bieten, an einen Log Analytics-Arbeitsbereich zu senden. Dadurch erhalten Sie alle verfügbaren Telemetriedaten für Ihre Ressourcen und können [Log Analytics](logs/log-analytics-overview.md) verwenden, um Protokoll- und Leistungsdaten mithilfe einer Sprache für erweiterte Abfragen, die in Operations Manager in vergleichbarer Form nicht vorhanden ist, interaktiv zu analysieren. Sie können auch [Protokollabfragewarnungen](alerts/alerts-log-query.md) erstellen, die komplexe Logik verwenden können, um Warnungsbedingungen zu ermitteln und Daten für mehrere Ressourcen zu korrelieren.

[![Protokollanalyse](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

[Erkenntnisse](monitor-reference.md) in Azure Monitor ähneln Management Packs, da sie eine eindeutige Überwachung für einen bestimmten Azure-Dienst bieten. Erkenntnisse sind derzeit für mehrere Dienste verfügbar, darunter Netzwerk, Speicher und Container, und es werden ständig weitere Dienste hinzugefügt.

[![Beispiel für Erkenntnisse](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Erkenntnisse basieren auf [Arbeitsmappen](visualize/workbooks-overview.md) in Azure Monitor, in denen Metriken und Protokollabfragen zu umfassenden interaktiven Berichten kombiniert werden. Erstellen Sie eigene Arbeitsmappen, um Daten aus mehreren Diensten zu kombinieren. Dies ähnelt dem Erstellen benutzerdefinierter Ansichten und Berichte in der Operations-Konsole.

### <a name="azure-management-pack"></a>Azure-Management Pack
Mit dem [Azure-Management Pack](https://www.microsoft.com/download/details.aspx?id=50013) ist Operations Manager in der Lage, Azure-Ressourcen zu ermitteln und deren Integrität basierend auf einer bestimmten Reihe von Überwachungsszenarien zu überwachen. Dieses Management Pack erfordert zwar eine zusätzliche Konfiguration für jede Ressource in Azure, es kann jedoch hilfreich sein, für eine gewisse Sichtbarkeit Ihrer Azure-Ressourcen in der Operations-Konsole zu sorgen, bis Sie Ihre Geschäftsprozesse schwerpunktmäßig für Azure Monitor weiterentwickeln.

[![Azure-Management Pack](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Sie können das Azure-Management Pack verwenden, wenn Sie die Sichtbarkeit bestimmter Azure-Ressourcen in der Operations-Konsole wünschen und einige grundlegende Warnungen in Ihre vorhandenen Prozesse integrieren möchten. Es werden tatsächlich von Azure Monitor erfasste Daten verwendet. Für eine langfristige vollständige Überwachung Ihrer Azure-Ressourcen sollten Sie jedoch Azure Monitor verwenden. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Überwachen von Serversoftware und lokaler Infrastruktur
Wenn Sie Computer in die Cloud verschieben, ändern sich die Überwachungsanforderungen für die zugehörige Software nicht. Sie müssen zwar die physischen Komponenten nicht mehr überwachen, da sie virtualisiert sind, doch müssen unabhängig von der Umgebung das Gastbetriebssystem und seine Workloads dieselben Anforderungen erfüllen.

[VM Insights](vm/vminsights-overview.md) ist das primäre Feature in Azure Monitor zur Überwachung von virtuellen Computern und deren Gastbetriebssystem und Workloads. Ähnlich wie Operations Manager verwendet VM Insights einen Agent, um Daten aus dem Gastbetriebssystem virtueller Computer zu erfassen. Dabei handelt es sich um die gleichen Leistungs- und Ereignisdaten, die normalerweise von Management Packs für die Analyse und Warnungen verwendet werden. Es sind jedoch noch keine Regeln vorhanden, um Probleme der Geschäftsanwendungen und Serversoftware auf diesen Computern zu erkennen und zu melden. Sie müssen eigene Warnungsregeln erstellen, um proaktiv über erkannte Probleme benachrichtigt zu werden.

[![Leistung von VM Insights](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor misst auch nicht die Integrität verschiedener Anwendungen und Dienste, die auf einem virtuellen Computer ausgeführt werden. Metrikwarnungen können automatisch aufgelöst werden, wenn ein Wert unter einen Schwellenwert fällt, doch können in Azure Monitor derzeit keine Integritätskriterien für Anwendungen und Dienste definiert werden, die auf dem Computer ausgeführt werden, und es steht auch kein Integritätsrollup zum Gruppieren der Integrität zugehöriger Komponenten bereit.

> [!NOTE]
> Das neue [Feature „Gastintegrität“ von VM Insights](vm/vminsights-health-overview.md) befindet sich nun in der öffentlichen Vorschau und gibt Warnungen basierend auf dem Integritätszustand einer Gruppe von Leistungsmetriken aus. Dies ist jedoch zunächst auf einen bestimmten Satz von Leistungsindikatoren beschränkt, die sich auf das Gastbetriebssystem beziehen und nicht auf Anwendungen oder andere Workloads, die auf dem virtuellen Computer ausgeführt werden.
> 
> [![Gastintegrität von VM Insights](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

Zur Überwachung der Software auf Ihren Computern in einer Hybridumgebung wird in der Regel eine Kombination aus VM Insights und Operations Manager verwendet, je nach Anforderungen der einzelnen Computer und Ihrem Reifegrad bei der Entwicklung von Betriebsprozessen rund um Azure Monitor. Der Microsoft-Verwaltungs-Agent (als Log Analytics-Agent in Azure Monitor bezeichnet) wird von beiden Plattformen verwendet, sodass ein einzelner Computer gleichzeitig von beiden überwacht werden kann.

> [!NOTE]
> Zukünftig wird VM Insights zum [Azure Monitor-Agent](agents/azure-monitor-agent-overview.md) übergehen, der sich derzeit in der öffentlichen Vorschau befindet. Dieser wird mit Microsoft Monitoring Agent kompatibel sein, sodass derselbe virtuelle Computer weiterhin von beiden Plattformen überwacht werden kann.

Verwenden Sie Operations Manager weiterhin für Funktionen, die von Azure Monitor noch nicht bereitgestellt werden können. Dazu gehören Management Packs für wichtige Serversoftware wie IIS, SQL Server oder Exchange. Möglicherweise haben Sie auch benutzerdefinierte Management Packs für die lokale Infrastruktur entwickelt, die mit Azure Monitor nicht erreichbar sind. Verwenden Sie Operations Manager auch weiterhin, wenn er eng in Ihre betrieblichen Prozesse integriert ist, bis Sie zur Modernisierung Ihrer Dienstvorgänge übergehen können, bei der diese durch Azure Monitor und andere Azure-Dienste ergänzt oder ersetzt werden können. 

Verwenden Sie Azure Monitor für VMs, um die aktuelle Überwachung zu verbessern, auch wenn Operations Manager dadurch nicht sofort ersetzt wird. Es folgen einige Beispiele für Azure Monitor-spezifische Funktionen:

- Ermitteln und Überwachen von Beziehungen zwischen virtuellen Computern und ihren externen Abhängigkeiten
- Anzeigen aggregierter Leistungsdaten für mehrere virtuelle Computer in interaktiven Diagrammen und Arbeitsmappen
- Verwenden von [Protokollabfragen](logs/log-query-overview.md), um Telemetriedaten von Ihren virtuellen Computern mit Daten von Ihren anderen Azure-Ressourcen interaktiv zu analysieren
- Erstellen von [Protokollwarnungsregeln](alerts/alerts-log-query.md) basierend auf komplexer Logik für mehrere virtuelle Computer

[![VM Insights-Zuordnung](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Mithilfe von [Azure Arc-fähigen Servern](../azure-arc/servers/overview.md) kann VM Insights zusätzlich zu virtuellen Azure-Computern auch Computer lokal und in anderen Clouds überwachen. Mit Azure Arc-fähigen Servern können Sie Windows- und Linux-Computer, die außerhalb von Azure in Ihrem Unternehmensnetzwerk oder von einem anderen Cloudanbieter gehostet werden, auf konsistente Weise verwalten wie native virtuelle Azure-Computer.



## <a name="monitor-business-applications"></a>Überwachen von Geschäftsanwendungen
Sie benötigen in der Regel benutzerdefinierte Management Packs, um Ihre Geschäftsanwendungen mit Operations Manager zu überwachen, wobei auf den einzelnen virtuellen Computern installierte Agents genutzt werden. Application Insights in Azure Monitor überwacht webbasierte Anwendungen unabhängig davon, ob Sie in Azure, in anderen Clouds oder lokal vorhanden sind, sodass dies für alle Ihre Anwendungen ungeachtet deren Migration zu Azure verwendet werden kann.

Wenn Ihre Überwachung einer Geschäftsanwendung auf die Funktionen beschränkt ist, die von der [Vorlage für die .NET-App-Leistung]() in Operations Manager bereitgestellt werden, können Sie wahrscheinlich ohne Funktionsverlust zu Application Insights migrieren. Tatsächlich umfasst Application Insights eine beträchtliche Anzahl zusätzlicher Funktionen, zu denen folgende gehören:

- Automatisches Ermitteln und Überwachen von Anwendungskomponenten
- Erfassen detaillierter Nutzungs- und Leistungsdaten für Anwendungen, z. B. Antwortzeit, Fehlerraten und Anforderungsraten
- Erfassen von Browserdaten, z. B. Seitenaufrufe und Ladeleistung
- Erkennen von Ausnahmen und detaillierte Anzeige der Stapelüberwachung und zugehöriger Anforderungen
- Ausführen erweiterter Analysen mithilfe von Funktionen wie der [verteilten Ablaufverfolgung](app/distributed-tracing.md) und [intelligenten Erkennung](app/proactive-diagnostics.md)
- Verwenden des [Metrik-Explorers](essentials/metrics-getting-started.md) zur interaktiven Analyse von Leistungsdaten
- Verwenden von [Protokollabfragen](logs/log-query-overview.md), um erfasste Telemetriedaten zusammen mit den für Azure-Dienste und VM Insights erfassten Daten interaktiv zu analysieren

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Es gibt jedoch bestimmte Szenarien, in denen Sie möglicherweise Operations Manager zusätzlich zu Application Insights weiterhin verwenden müssen, bis Sie die erforderliche Funktionalität erreichen können. In den folgenden Beispielszenarien muss möglicherweise weiterhin Operations Manager verwendet werden:

-  Für [Verfügbarkeitstests](app/monitor-web-app-availability.md), mit denen Sie die Verfügbarkeit und Reaktionsfähigkeit Ihrer Anwendungen überwachen und melden können, sind eingehende Anforderungen von den IP-Adressen der Webtest-Agents erforderlich. Wenn Ihre Richtlinie einen solchen Zugriff nicht zulässt, müssen Sie möglicherweise weiterhin [Verfügbarkeitsmonitore für Webanwendungen](/system-center/scom/web-application-availability-monitoring-template) in Operations Manager verwenden.
- In Operations Manager können Sie ein beliebiges Abrufintervall für Verfügbarkeitstests festlegen, wobei viele Kunden alle 60 bis 120 Sekunden eine Überprüfung vornehmen. Application Insights weist ein minimales Abrufintervall von 5 Minuten auf, das für einige Kunden möglicherweise zu lang ist.
- Ein erheblicher Teil der Überwachung in Operations Manager erfolgt durch Erfassen von Ereignissen, die von Anwendungen und durch Ausführen von Skripts auf dem lokalen Agent generiert werden. Dies sind keine Standardoptionen in Application Insights, sodass eine benutzerdefinierte Anpassung erforderlich sein kann, um Ihre geschäftlichen Anforderungen zu erfüllen. Dazu können benutzerdefinierte Warnungsregeln gehören, die in einem Log Analytics-Arbeitsbereich gespeicherte Ereignisdaten und Skripts verwenden, die auf einem virtuellen Gastcomputer mithilfe von [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) gestartet werden.
- Je nach Sprache, in der Ihre Anwendung geschrieben ist, können Sie bei der [Instrumentierung, die Sie mit Application Insights verwenden können](app/platforms.md), eingeschränkt sein.

Folgen Sie der grundlegenden Strategie in den anderen Abschnitten dieses Leitfadens, und verwenden Sie weiterhin Operations Manager für Ihre Geschäftsanwendungen, doch nutzen Sie zusätzliche Funktionen von Application Insights. Wenn Sie wichtige Funktionen durch Azure Monitor ersetzen können, können Sie die Nutzung Ihrer benutzerdefinierten Management Packs nach und nach einstellen.


## <a name="next-steps"></a>Nächste Schritte

- Im [Leitfaden zur Cloudüberwachung](/azure/cloud-adoption-framework/manage/monitor/) finden Sie einen ausführlichen Vergleich von Azure Monitor und System Center Operations Manager sowie weitere Informationen zum Entwerfen und Implementieren einer hybriden Überwachungsumgebung.
- Informieren Sie sich über das [Überwachen von Azure-Ressourcen in Azure Monitor](essentials/monitor-azure-resource.md).
- Informieren Sie sich über das [Überwachen virtueller Azure-Computer in Azure Monitor](vm/monitor-vm-azure.md).
- Erfahren Sie mehr über [VM Insights](vm/vminsights-overview.md).
- Erfahren Sie mehr über [Application Insights](app/app-insights-overview.md).
