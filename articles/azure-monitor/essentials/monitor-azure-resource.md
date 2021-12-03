---
title: Überwachen von Azure-Ressourcen mit Azure Monitor | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Überwachungsdaten von Ressourcen in Azure mithilfe von Azure Monitor sammeln und analysieren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/15/2021
ms.openlocfilehash: f93cd688ef22d7765edbd50e742262e9febec14c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297843"
---
# <a name="tutorial-monitor-azure-resources-with-azure-monitor"></a>Tutorial: Überwachen von Azure-Ressourcen mit Azure Monitor
Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. Die Überwachung wird mithilfe von Azure Monitor gewährleistet, einem umfassenden Stapelüberwachungsdienst in Azure, der einen vollständigen Satz an Funktionen zum Überwachen Ihrer Azure-Ressourcen sowie von Ressourcen in anderen Clouds und lokalen Umgebungen bereitstellt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Was Azure Monitor ist und wie es für andere Azure-Dienste in das Portal integriert wird
> * Die Datentypen, die von Azure Monitor für Azure-Ressourcen gesammelt werden
> * Azure Monitor Tools zum Sammeln und Analysieren von Daten

> [!NOTE]
> In diesem Tutorial werden Konzepte von Azure Monitor beschrieben und verschiedene Menüelemente erläutert. Beginnen Sie mit dem [Tutorial: Analysieren von Metriken für eine Azure-Ressource](../essentials/tutorial-metrics.md), um direkt mit dem Gebrauch der Funktionen von Azure Monitor zu beginnen.


## <a name="monitoring-data"></a>Überwachungsdaten

### <a name="azure-monitor-data-collection"></a>Azure Monitor Datensammlung
Sobald Sie eine Azure-Ressource erstellen, wird Azure Monitor aktiviert und beginnt mit dem Sammeln von Metriken und Aktivitätsprotokollen. Bei einigen Konfigurationen können Sie zusätzliche Überwachungsdaten sammeln und weitere Funktionen aktivieren. Die Azure Monitor-Datenplattform besteht aus Metriken und Protokollen. Jede sammelt verschiedene Arten von Daten und ermöglicht verschiedene Funktionen bei Azure Monitor.

- [Azure Monitor-Metriken](../essentials/data-platform-metrics.md) stellen eine Funktion von Azure Monitor dar, das numerische Daten von überwachten Ressourcen in eine Zeitreihendatenbank speichert. Die Metrikdatenbank wird automatisch für jedes Azure-Abonnement erstellt. Verwenden Sie den [Metrik-Explorer](../essentials/tutorial-metrics.md), um Daten aus Azure Monitor-Protokollen zu analysieren.
- Die [Azure Monitor-Protokolle](../logs/data-platform-logs.md) sammeln Protokolle und Leistungsdaten, wobei sie mithilfe von Protokollabfragen auf unterschiedliche Weise abgerufen und analysiert werden können. Sie müssen einen Protokoll-Analyse-Arbeitsbereich (Log Analytics Workspace) erstellen, um Protokolldaten zu sammeln. Verwenden Sie die [Protokoll-Analyse](../logs/log-analytics-tutorial.md) zum Analysieren von Daten aus Azure Monitor-Protokollen.

### <a name="monitoring-data-from-azure-resources"></a>Daten aus den Azure-Ressourcen überwachen
Während Ressourcen aus verschiedenen Azure-Diensten unterschiedliche Überwachungsanforderungen haben, generieren sie Überwachungsdaten in denselben Formaten, sodass Sie dieselben Azure Monitor-Tools verwenden können, um alle Azure-Ressourcen zu analysieren.

Die Azure-Ressourcen generieren die folgenden Überwachungsdaten:

- [Aktivitätsprotokoll](./platform-logs-overview.md) - Ereignisse auf Abonnementebene, die Vorgänge für jede Azure-Ressource nachverfolgen, z. B. das Erstellen einer neuen Ressource oder das Starten eines virtuellen Computers. Aktivitätsprotokollereignisse werden automatisch generiert und für die Anzeige im Azure-Portal gesammelt. Sie können eine Diagnoseeinstellung machen, um das Aktivitätsprotokoll an die Azure Monitor-Protokolle zu senden.
- [Plattformmetriken](../essentials/data-platform-metrics.md) sind numerische Werte, die automatisch in regelmäßigen Abständen erfasst werden und einen Aspekt einer Ressource zu einem bestimmten Zeitpunkt beschreiben. Plattformmetriken werden automatisch generiert und in den Metriken von Azure Monitor gesammelt.
- [Ressourcenprotokolle](./platform-logs-overview.md) - bieten einen Einblick in Vorgänge, die von einer Azure-Ressource ausgeführt wurden, z. B. das Abrufen eines Geheimnisses aus einem Key Vault (Schlüsseltresor) oder die Ausgabe einer Anforderung an eine Datenbank. Ressourcenprotokolle werden automatisch generiert, aber Sie müssen eine Diagnoseeinstellung vornehmen, um sie an die Protokolle von Azure Monitor zu senden.
- [Gastmetriken und -protokolle für virtuelle Computer]() - Leistungs- und Protokolldaten vom Gastbetriebssystem virtueller Azure-Computer. Sie müssen einen Agent auf dem virtuellen Computer installieren, um diese Daten zu sammeln und an die Metriken und Protokolle von Azure Monitor.


## <a name="menu-options"></a>Menüoptionen
Sie können zwar auf die Funktionen von Azure Monitor im **Monitor**-Menü im Azure-Portal zugreifen, aber sie können auf die Funktionen von Azure Monitor auch direkt über das Menü für die verschiedenen Azure-Dienste zugreifen. Während verschiedene Azure-Dienste möglicherweise etwas unterschiedlich sind, haben sie einen gemeinsamen Satz von Überwachungsoptionen im Azure-Portal. Dies umfasst die **Übersicht** und das **Aktivitätsprotokoll** sowie mehrere Optionen im Menü-Abschnitt **Überwachung**. 

:::image type="content" source="media/monitor-azure-resource/menu-01.png" lightbox="media/monitor-azure-resource/menu-01.png" alt-text="Monitor-Menü 1":::

:::image type="content" source="media/monitor-azure-resource/menu-02.png" lightbox="media/monitor-azure-resource/menu-02.png" alt-text="Monitor-Menü 2":::


## <a name="overview-page"></a>Seite „Übersicht“
Die Seite **Übersicht** enthält Details zur Ressource und häufig zu ihrem aktuellen Status. Ein virtueller Computer zeigt beispielsweise seinen aktuellen Ausführungsstatus an. Viele Azure-Dienste verfügen über eine Registerkarte **Überwachung**, die Diagramme für eine Reihe von Schlüsselmetriken enthält. Dies ist eine schnelle Möglichkeit, den Betrieb der Ressource anzuzeigen, und Sie können auf eines der Diagramme klicken, um sie im Metrik-Explorer für eine ausführlichere Analyse zu öffnen. 

Unter dem Tutorial [Tutorial: Metriken für eine Azure-Ressource analysieren](../essentials/tutorial-metrics.md) erfahren Sie, wie Sie Metrik-Explorer verwenden.

![Seite „Übersicht“](media/monitor-azure-resource/overview-page.png)
### <a name="activity-log"></a>Aktivitätsprotokoll 
Mit dem Menüelement **Aktivitätsprotokoll** können Sie Einträge im [Aktivitätsprotokoll](../essentials/activity-log.md) für die aktuelle Ressource anzeigen lassen. 

:::image type="content" source="media/monitor-azure-resource/activity-log.png" lightbox="media/monitor-azure-resource/activity-log.png" alt-text="Aktivitätsprotokoll":::

## <a name="alerts"></a>Alerts
Auf der Seite **Warnungen** werden alle aktuellen Warnungen angezeigt, die für die Ressource ausgelöst wurden. Warnungen benachrichtigen Sie proaktiv, wenn wichtige Anforderungen in Ihren Überwachungsdaten gefunden werden, und sie können Daten aus Metriken oder Protokollen verwenden.

Anleitungen zum Erstellen von Warnungsregeln und Anzeigen von Warnungen finden Sie unter [Tutorial: Erstellen einer Metrikwarnung für eine Azure-Ressource](../alerts/tutorial-metric-alert.md) oder unter [Tutorial: Erstellen einer Protokollabfragewarnung für eine Azure-Ressource](../alerts/tutorial-log-alert.md).

:::image type="content" source="media/monitor-azure-resource/alerts-view.png" lightbox="media/monitor-azure-resource/alerts-view.png" alt-text="Anzeige der Warnung":::

## <a name="metrics"></a>Metriken
Im Menüpunkt **Metriken** wird der [Metrik-Explorer](./metrics-getting-started.md) geöffnet, was es Ihnen ermöglicht, mit einzelnen Metriken zu arbeiten oder mehrere Metriken zu kombinieren, um Korrelationen und Trends zu erkennen. Dies ist derselbe Metrik-Explorer, der geöffnet wird, wenn Sie auf eines der Diagramme auf der Seite **Übersicht** klicken.

Unter dem Tutorial [Tutorial: Metriken für eine Azure-Ressource analysieren](../essentials/tutorial-metrics.md) erfahren Sie, wie Sie Metrik-Explorer verwenden.

:::image type="content" source="media/monitor-azure-resource/metrics.png" lightbox="media/monitor-azure-resource/metrics.png" alt-text="Metrik-Explorer":::


## <a name="diagnostic-settings"></a>Diagnoseeinstellungen
Auf der Seite **Diagnoseeinstellungen** können Sie eine [Diagnoseeinstellung](../essentials/diagnostic-settings.md) vornehmen, um die Ressourcenprotokolle für Ihre Ressource zu sammeln. Sie können sie an mehrere Standorte senden, aber meistens werden sie an einen Protokoll-Anlalyse-Arbeitsbereich gesendet, damit Sie sie mit der Protokollanalyse (Log Analytics) analysieren können.

Eine Anleitung zum Erstellen einer Diagnoseeinstellung finden Sie unter [Tutorial: Sammeln und Analysieren von Ressourcenprotokollen aus einer Azure-Ressource](../essentials/tutorial-resource-logs.md).

:::image type="content" source="media/monitor-azure-resource/diagnostic-settings.png" lightbox="media/monitor-azure-resource/diagnostic-settings.png" alt-text="Diagnoseeinstellungen":::



## <a name="insights"></a>Einblicke 
Das Menüelement **Einblicke** bietet Einblicke in die Ressource, wenn der Azure-Dienst über dies verfügt. [Erkenntnisse](../monitor-reference.md) bieten eine angepasste Überwachungsumgebung, die auf der Azure Monitor-Datenplattform und den Standardfeatures basiert. 


Unter [Einblicke und Kern-Lösungen](../monitor-reference.md#insights-and-curated-visualizations) finden Sie eine Liste der verfügbaren Einsichten sowie Links zu deren Dokumentation.

:::image type="content" source="media/monitor-azure-resource/insights.png" lightbox="media/monitor-azure-resource/insights.png" alt-text="Screenshot Einblicke":::

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun über grundlegende Kenntnisse von Azure Monitor verfügen, beginnen Sie mit der Analyse einiger Metriken für eine Azure-Ressource.

> [!div class="nextstepaction"]
> [Analysieren von Metriken für eine Azure-Ressource](../essentials/tutorial-metrics.md)
