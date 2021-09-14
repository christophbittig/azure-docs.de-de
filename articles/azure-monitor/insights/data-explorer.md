---
title: Azure Data Explorer Insights (ADX Insights) | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu Azure Data Explorer Insights (ADX Insights).
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 872c1e29b6c85f24c4e9841dca359a9429b92321
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458115"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer Insights

Azure Data Explorer Insights ermöglicht die umfassende Überwachung Ihrer Cluster mithilfe einer einheitlichen Ansicht der Leistung, Vorgänge, Nutzung und Fehler Ihres Clusters.
Dieser Artikel enthält Informationen zum Onboarding und zur Verwendung von Azure Data Explorer Insights.

## <a name="introduction-to-azure-data-explorer-insights"></a>Einführung in Azure Data Explorer Insights

Bevor Sie sich mit der Umgebung befassen, sollten Sie sich mit der Darstellung und Visualisierung von Informationen vertraut machen.
-    Eine **Übersichtsperspektive** zeigt eine Momentaufnahme der primären Metriken Ihres Clusters, sodass Sie die Leistung von Abfragen sowie von Erfassungs- und Exportvorgängen ganz einfach nachverfolgen können.
-   Anhand von **Detailinformationen** zu einem bestimmten Azure Data Explorer-Cluster können Sie detaillierte Analysen durchführen.
-    Die Ansichten sind **anpassbar**: Sie können zwischen verschiedenen Metriken wechseln, auf Ihre Grenzwerte abgestimmte Schwellenwerte festlegen und eigene benutzerdefinierte Arbeitsmappen speichern. Diagramme in der Arbeitsmappe können an Azure-Dashboards angeheftet werden.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Ansicht von Azure Monitor (Übersichtsperspektive)

In Azure Monitor können Sie die wichtigsten Leistungsmetriken für den Cluster anzeigen, beispielsweise Metriken für Abfragen sowie Erfassungs- und Exportvorgänge aus verschiedenen Clustern in Ihrem Abonnement, und Leistungsprobleme identifizieren.

Führen Sie die folgenden Schritte aus, um die Leistung Ihrer Cluster in all Ihren Abonnements anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im linken Bereich des Azure-Portals **Monitor** und im Abschnitt „Insights-Hub“ die Option **Azure Data Explorer-Cluster** aus.

![Screenshot: Übersicht mit mehreren Diagrammen](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Registerkarte „Übersicht“

Auf der Registerkarte **Übersicht** für das ausgewählte Abonnement zeigt die Tabelle interaktive Metriken für die Azure Data Explorer-Cluster, die im Abonnement gruppiert sind. Ergebnisse können auf der Grundlage der Optionen gefiltert werden, die Sie in den folgenden Dropdownlisten auswählen:

* Abonnements: Es werden nur Abonnements aufgelistet, in denen Azure Data Explorer-Cluster vorhanden sind.

* Azure Data Explorer-Cluster: Standardmäßig sind bis zu fünf Cluster vorab ausgewählt. Wenn Sie in der Bereichsauswahl mehrere oder alle Cluster auswählen, werden bis zu 200 Cluster zurückgegeben.

* Zeitbereich: Standardmäßig werden die Informationen der letzten 24 Stunden basierend auf der entsprechenden Auswahl angezeigt.

Die Zählerkachel unter der Dropdownliste gibt Aufschluss über die Gesamtanzahl von Azure Data Explorer-Clustern in den ausgewählten Abonnements sowie über die Anzahl ausgewählter Cluster. Es gibt bedingte Farbcodierungen für die Spalten: Keepalive, Auslastung der Erfassung und Cacheauslastung. Orange hinterlegte Zellen weisen Werte auf, die für den Cluster nicht nachhaltig sind. 

Informationen zum besseren Verständnis der einzelnen Metriken finden Sie in der Dokumentation zu [Azure Data Explorer-Metriken](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Registerkarte „Abfrageleistung“

Diese Registerkarte zeigt die Abfragedauer, die Gesamtanzahl von gleichzeitigen Abfrage und die Gesamtanzahl von gedrosselten Abfragen.

![Screenshot der Registerkarte „Abfrageleistung“](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Registerkarte „Erfassungsleistung“

Diese Registerkarte zeigt die Latenz bei Datenerfassungen, erfolgreiche Erfassungsergebnisse, fehlerhafte Erfassungsergebnisse, den Erfassungsumfang sowie für Event Hubs und IoT Hubs verarbeitete Ereignisse.

[![Screenshot der Registerkarte „Erfassungsleistung“](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Registerkarte „Leistung der Streamingerfassung“

Diese Registerkarte bietet Informationen zur durchschnittlichen Datenrate zur durchschnittlichen Dauer und zur Anforderungsrate.

### <a name="export-performance-tab"></a>Registerkarte „Exportleistung“

Diese Registerkarte bietet Informationen zu exportierten Datensätzen, Verzögerungen, ausstehenden Datensätzen sowie den Prozentsatz der Auslastung für fortlaufende Exportvorgänge.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Ansicht aus einer Azure Data Explorer-Clusterressource (Detailinformationen)

So greifen Sie direkt von einem Azure Data Explorer-Cluster aus auf Azure Data Explorer Insights zu:

1. Wählen Sie im Azure-Portal **Azure Data Explorer-Cluster** aus.

2. Wählen Sie einen Azure Data Explorer-Cluster aus der Liste aus. Wählen Sie im Abschnitt „Überwachung“ die Option **Insights** aus.

Sie können auch auf diese Ansichten zugreifen, indem Sie in der Insights-Ansicht von Azure Monitor den Ressourcennamen eines Azure Data Explorer-Clusters auswählen.

Azure Data Explorer Insights kombiniert Protokolle und Metriken, um eine globale Überwachungslösung bereitzustellen. Um protokollbasierte Visualisierungen einzuschließen, müssen Benutzer die [Diagnoseprotokollierung ihres Azure Data Explorer-Clusters aktivieren und die Protokolle an einem Log Analytics-Arbeitsbereich senden](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). Folgende Diagnoseprotokolle sollten aktiviert werden: **Command**, **Query**, **TableDetails** und **TableUsageStatistics**.

![Screenshot der blauen Schaltfläche mit dem Text „Protokolle für die Überwachung konfigurieren“](./media/data-explorer/enable-logs.png)


 Die Registerkarte **Übersicht** zeigt Folgendes:

- Metrikkacheln mit Verfügbarkeit und Gesamtstatus des Clusters, sodass Sie schnell auf Informationen zur Integrität zugreifen können.

- Eine Zusammenfassung aktiver [Advisor-Empfehlungen](/azure/data-explorer/azure-advisor) und des Status der [Ressourcenintegrität](/azure/data-explorer/monitor-with-resource-health).

- Diagramme der wichtigsten CPU- und Arbeitsspeicherconsumer und die Anzahl eindeutiger Benutzer im Lauf der Zeit.


[![Screenshot der Ansicht aus einer Azure Data Explorer-Clusterressource](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

Die Registerkarte **Schlüsselmetrik** zeigt eine einheitliche Ansicht einiger Metriken des Clusters, gruppiert nach folgenden Kategorien: allgemeine Metriken, abfragebezogenen Metriken, erfassungsbezogene Metriken sowie Metriken in Bezug auf die Streamingerfassung.

[![Screenshot: Fehleransicht](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

Auf der Registerkarte **Verbrauch** können Benutzer Details zur Leistung der Befehle und Abfragen des Clusters anzeigen. Auf dieser Seite haben Sie folgende Möglichkeiten:
 
 - Sie können ermitteln, welche Arbeitsauslastungsgruppen, Benutzer und Anwendungen die meisten Abfragen senden oder die meisten CPU- und Arbeitsspeicherressourcen verbrauchen. So können Sie herausfinden, welche Arbeitsauslastungen die ressourcenintensivsten Abfragen zur Verarbeitung durch den Cluster senden.
 - Sie können die wichtigsten Arbeitsauslastungsgruppen, Benutzer und Anwendungen in Bezug auf fehlerhafte Abfragen identifizieren.
 - Sie können aktuelle Änderungen an der Anzahl von Abfragen im Vergleich zum bisherigen Tagesdurchschnitt (der letzten 16 Tage) identifizieren (nach Arbeitsauslastungsgruppe, Benutzer oder Anwendung).
 - Sie können Trends und Spitzenwerte bei der Anzahl von Abfragen sowie beim Arbeitsspeicher- und CPU-Verbrauch nach Arbeitsauslastungsgruppe, Benutzer, Anwendung und Befehlstyp identifizieren.

[![Screenshot der Vorgangsansicht mit Ringdiagrammen der wichtigsten Anwendungen nach Anzahl von Befehlen und Abfragen, der wichtigsten Prinzipale nach Anzahl von Befehlen und Abfragen und der wichtigsten Befehle nach Befehlstyp](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Screenshot der Vorgangsansicht mit Liniendiagrammen der Abfrageanzahl nach Anwendung, des Arbeitsspeicherverbrauchs insgesamt nach Anwendung und des CPU-Verbrauchs insgesamt nach Anwendung](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

Die Registerkarte **Tabellen** zeigt die neuesten und bisherigen Eigenschaften von Tabellen im Cluster. Sie können feststellen, welche Tabellen den meisten Platz beanspruchen und den Wachstumsverlauf anhand von Tabellengröße, heißen Daten und der Anzahl von Zeilen im Lauf der Zeit nachverfolgen.

Auf der Registerkarte **Cache** können Benutzer die Muster im Rückblickfenster ihrer tatsächlichen Abfragen analysieren und mit der konfigurierten Cacherichtlinie vergleichen (für jede Tabelle). Sie können sowohl Tabellen identifizieren, die von sehr vielen Abfragen verwendet werden, als auch Tabellen, die gar nicht abgefragt werden. Dann können Sie die Cacherichtlinie entsprechend anpassen. Sie können in Azure Advisor bestimmte Empfehlungen zu Cacherichtlinien für bestimmte Tabellen abrufen (derzeit sind Cacheempfehlungen nur auf dem [Hauptdashboard von Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations) verfügbar). Die Empfehlungen basieren auf dem Rückblickfenster der tatsächlichen Abfragen in den letzten 30 Tagen und einer nicht optimierten Cacherichtlinie für mindestens 95 % der Abfragen. Die Empfehlungen von Azure Advisor zur Cachereduzierung sind für Cluster verfügbar, die „datengebunden“ sind (das bedeutet, dass der Cluster eine geringe CPU- und Erfassungsauslastung aufweist, aufgrund einer hohen Datenkapazität jedoch nicht ab- oder herunterskaliert werden konnte).

[![Screenshot von Cachedetails](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

Auf der Registerkarte **Clustergrenzen** werden die Clustergrenzen basierend auf Ihrer Nutzung angezeigt. Auf dieser Registerkarte können Sie die CPU-, Erfassungs- und Cacheauslastung untersuchen. Die zugehörigen Metriken werden als „Niedrig“, „Mittel“ oder „Hoch“ bewertet. Diese Metriken und Bewertungen sind wichtig bei der Wahl der optimalen SKU und Instanzenanzahl für Ihren Cluster und werden in den SKU-/Größenempfehlung von Azure Advisor berücksichtigt. Auf dieser Registerkarte können Sie eine Metrikkachel auswählen und ausführliche Informationen zum zugehörigen Trend und zur Bewertung anzeigen. Außerdem können Sie die SKU-/Größenempfehlung von Azure Advisor für Ihren Cluster anzeigen. In der folgenden Abbildung sehen Sie beispielsweise, dass alle Metriken als „Niedrig“ bewertet werden. Dadurch erhält der Cluster eine Kostenempfehlung, die es ermöglicht, den Cluster ab- bzw. herunterzuskalieren, um Kosten zu sparen.

> [!div class="mx-imgBorder"]
> [![Screenshot: Clustergrenzen](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Anheften an Azure-Dashboard

Sie können einen beliebigen Metrikabschnitt (aus der Übersichtsperspektive) an ein Azure-Dashboard anheften, indem Sie rechts oben im Abschnitt das Stecknadelsymbol auswählen.

![Screenshot: Ausgewähltes Stecknadelsymbol](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Anpassen von Azure Data Explorer Insights

In diesem Abschnitt werden gängige Szenarien für die Bearbeitung der Arbeitsmappe erläutert, um sie zur Unterstützung Ihrer Datenanalyseanforderungen anzupassen:
* Festlegen des Gültigkeitsbereichs der Arbeitsmappe, sodass immer ein bestimmtes Abonnement oder mindestens ein bestimmter Azure Data Explorer-Cluster ausgewählt ist
* Ändern von Metriken im Raster
* Ändern von Schwellenwerten oder farbliche Darstellung/Codierung

Aktivieren Sie zum Vornehmen von Anpassungen zunächst den Bearbeitungsmodus, indem Sie auf der oberen Symbolleiste die Schaltfläche **Anpassen** auswählen.

![Screenshot: Schaltfläche „Anpassen“](./media/data-explorer/customize.png)

Anpassungen werden in einer benutzerdefinierten Arbeitsmappe gespeichert, um zu verhindern, dass die Standardkonfiguration in unserer veröffentlichten Arbeitsmappe überschrieben wird. Arbeitsmappen werden in einer Ressourcengruppe gespeichert, und zwar entweder in Ihrem privaten Bereich „Meine Berichte“ oder im Bereich „Freigegebene Berichte“, der für alle Benutzer mit Zugriff auf die Ressourcengruppe zugänglich ist. Nachdem Sie die benutzerdefinierte Arbeitsmappe gespeichert haben, müssen Sie zum Arbeitsmappenkatalog wechseln, um die Mappe zu starten.

![Screenshot: Arbeitsmappenkatalog](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Problembehandlung

Allgemeine Anleitungen zur Problembehandlung finden Sie im Artikel zur [Problembehandlung für arbeitsmappenbasierte Erkenntnisse](troubleshoot-workbooks.md).

Dieser Abschnitt unterstützt Sie bei der Diagnose und Behandlung einiger bekannter Probleme, die bei der Verwendung von Azure Data Explorer Insights auftreten können. In der Liste unten finden Sie die für Ihre spezifische Fragestellung relevanten Informationen.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Warum werden in der Abonnementauswahl nicht alle meine Abonnements angezeigt?

Es werden nur Abonnements angezeigt, die Azure Data Explorer-Cluster enthalten (aus dem gewählten Abonnementfilter). Die Auswahl erfolgt im Header des Azure-Portals unter „Verzeichnis + Abonnement“.

![Screenshot: Abonnementfilter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Warum werden mir in den Abschnitten „Verbrauch“, „Tabellen“ oder „Cache“ keine Daten für meinen Azure Data Explorer-Cluster angezeigt?

Zum Anzeigen Ihrer protokollbasierten Daten müssen Sie [Diagnoseprotokolle aktivieren](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs), und zwar für jeden Azure Data Explorer-Cluster, den Sie überwachen möchten. Dies kann in den Diagnoseeinstellungen für den jeweiligen Cluster konfiguriert werden. Sie müssen Ihre Daten an einen Log Analytics-Arbeitsbereich senden. Folgende Diagnoseprotokolle sollten aktiviert werden: Command, Query, TableDetails und TableUsageStatistics.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Ich habe bereits Protokolle für meinen Azure Data Explorer-Cluster aktiviert. Warum kann ich meine Daten unter „Befehle und Abfragen“ immer noch nicht sehen?

Diagnoseprotokolle werden derzeit nicht rückwirkend angezeigt. Daten werden also erst angezeigt, nachdem Aktionen für Ihre Azure Data Explorer-Cluster ausgeführt wurden. Dies kann eine Weile dauern (einige Stunden, möglicherweise sogar einen Tag), je nachdem, wie aktiv Ihr Azure Data Explorer-Cluster ist.


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md) über die Szenarien, die Arbeitsmappen unterstützen sollen, wie Sie neue Berichte erstellen und vorhandene Berichte anpassen können, und vieles mehr.
