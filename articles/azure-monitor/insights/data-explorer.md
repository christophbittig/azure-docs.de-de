---
title: Azure Data Explorer Insights (ADX Insights) | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Verwendung von Azure Data Explorer Insights.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: b522e510968b97d80917bc6fa4123d33e4480cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662208"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer Insights

Azure Data Explorer Insights ermöglicht die umfassende Überwachung Ihrer Cluster mithilfe einer einheitlichen Ansicht der Leistung, Vorgänge, Nutzung und Fehler Ihres Clusters.

Es bietet:

-    **Maßstabgerechte Perspektive**. Eine Momentaufnahme der primären Metriken Ihrer Cluster hilft Ihnen, die Leistung von Abfragen, Erfassungs- und Exportvorgängen zu verfolgen.
-   **Detailinformationen zur Analyse**. Sie können Drilldown in einen bestimmten Azure Data Explorer-Cluster ausführen, um eine detaillierte Analyse durchzuführen.
-    **Anpassung**. Sie können ändern, welche Metriken Sie sehen möchten, Schwellenwerte ändern oder festlegen, die mit Ihren Grenzwerten übereinstimmen, und Ihre eigenen benutzerdefinierten Arbeitsmappen speichern. Diagramme in einer Arbeitsmappe können an Azure Dashboards angeheftet werden.

Dieser Artikel enthält Informationen zum Onboarding und zur Verwendung von Azure Data Explorer Insights.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Ansicht von Azure Monitor (maßstabgerechte Perspektive)

Über Azure Monitor können Sie die wichtigsten Leistungsmetriken für den Cluster anzeigen. Diese Metriken umfassen Informationen zu Abfragen, Erfassungs- und Exportvorgängen aus mehreren Clustern in Ihrem Abonnement. Sie können Ihnen helfen, Leistungsprobleme zu identifizieren.

Um die Leistung Ihrer Cluster für alle Ihre Abonnements anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im linken Bereich **Monitor** aus. Wählen Sie im Abschnitt **Insights Hub** die Option **Azure Data Explorer Clusters** aus.

![Screenshot der Auswahlmöglichkeiten für die Anzeige der Leistung von Azure Data Explorer-Clustern.](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Registerkarte „Übersicht“

Auf der Registerkarte **Übersicht** für das ausgewählte Abonnement zeigt die Tabelle interaktive Metriken für die Azure Data Explorer-Cluster, die im Abonnement gruppiert sind. Sie können die Ergebnisse anhand der Optionen filtern, die Sie in den folgenden Dropdown-Listen auswählen:

* **Subscriptions**: Es werden nur Abonnements aufgelistet, die Azure Data Explorer-Cluster haben.

* **Azure Data Explorer-Cluster**: Standardmäßig sind bis zu fünf Cluster vorab ausgewählt. Wenn Sie in der Bereichsauswahl mehrere oder alle Cluster auswählen, werden bis zu 200 Cluster zurückgegeben.

* **Time Range**: Standardmäßig zeigt die Tabelle die Informationen der letzten 24 Stunden auf der Grundlage der getroffenen korrespondierenden Auswahlen an.

Die Zählerkachel unter der Dropdown-Liste gibt die Gesamtzahl der Azure Data Explorer-Cluster in den ausgewählten Abonnements an und zeigt, wie viele ausgewählt sind. Es gibt bedingte Farbcodierungen für die Spalten: **Keep-Alive,** **CPU,** **Erfassungsauslastung** und **Cacheauslastung.** Orange hinterlegte Zellen weisen Werte auf, die für den Cluster nicht nachhaltig sind. 

Informationen zum besseren Verständnis der einzelnen Metriken finden Sie in der Dokumentation zu [Azure Data Explorer-Metriken](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Registerkarte „Abfrageleistung“

Die Registerkarte **Abfrageleistung** zeigt die Abfragedauer, die Gesamtzahl der gleichzeitigen Abfragen und die Gesamtzahl der gedrosselten Abfragen an.

![Screenshot der Registerkarte "Abfrageleistung".](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Registerkarte „Erfassungsleistung“

Die Registerkarte **Ingestion Performance** zeigt die Latenz bei der Erfassung, erfolgreiche Erfassungsergebnisse, fehlgeschlagene Erfassungsergebnisse, Erfassungsvolumen und verarbeitete Ereignisse für Event-Hubs und IoT-Hubs.

[![Screenshot der Registerkarte „Erfassungsleistung“.](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Registerkarte „Leistung der Streamingerfassung“

Die Registerkarte **Leistung der Streamingerfassung** enthält Informationen über die durchschnittliche Datenrate, die durchschnittliche Dauer und die Rate der Anforderungen.

### <a name="export-performance-tab"></a>Registerkarte „Exportleistung“

Die Registerkarte **Exportleistung** enthält Informationen zu exportierten Datensätzen, Verspätungen, der Anzahl ausstehender Daten und dem Nutzungsprozentsatz für fortlaufende Exportvorgänge.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Ansicht von einer Azure Data Explorer Cluster-Ressource (Drilldown-Analyse)

Um auf Azure Data Explorer Erkenntnisse direkt von einem Azure Data Explorer Cluster zuzugreifen:

1. Wählen Sie im Azure-Portal **Azure Data Explorer-Cluster** aus.

2. Wählen Sie aus der Liste einen Azure Data Explorer-Cluster aus. Wählen Sie im Abschnitt Überwachung **Insights** aus.

Sie können auch auf diese Ansichten zugreifen, indem Sie den Ressourcennamen eines Azure Data Explorer-Clusters in der Azure Monitor Ansicht Insights auswählen.

> [!NOTE]
> Azure Data Explorer Insights kombiniert Protokolle und Metriken, um eine globale Überwachungslösung bereitzustellen. Für die Einbeziehung von protokollbasierten Visualisierungen müssen Benutzer [ die Diagnoseprotokollierung ihres Azure Data Explorer-Clusters aktivieren und sie an einen Log Analytics-Arbeitsbereich ](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) senden. Die Diagnoseprotokolle, die aktiviert werden sollten, sind **Befehl**,  **Abfrage**: **Succeededlngestion0**, **Failedlngestion**, **lngesfionBatching**, **TableDetails** und **TableUsageStatistics**. (Das Aktivieren von **Succeeded!ngestion**-Protokollen kann kostspielig sein. Aktivieren Sie sie nur, wenn Sie erfolgreiche Erfassungen überwachen müssen.)

![Screenshot der Schaltfläche zum Konfigurieren von Protokollen für die Überwachung.](./media/data-explorer/enable-logs.png)

### <a name="overview-tab"></a>Registerkarte „Übersicht“

Die Registerkarte **Übersicht** zeigt Folgendes:

- Metrikkacheln, die die Verfügbarkeit und den Gesamtstatus des Clusters für eine schnelle Integritätsbewertung hervorheben.

- Eine Zusammenfassung der aktiven [Azure Advisor-Empfehlungen](/azure/data-explorer/azure-advisor) und des Status der [Ressourcen-Integrität](/azure/data-explorer/monitor-with-resource-health).

- Diagramme, die die größten CPU- und Speicherverbraucher sowie die Anzahl der einzelnen Benutzer im zeitlichen Verlauf anzeigen.

[![Screenshot der Ansicht von einer Azure Data Explorer-Cluster-Ressource.](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

### <a name="key-metrics-tab"></a>Registerkarte Schlüsselmetriken

Auf der **Registerkarte Schlüsselmetriken** wird eine einheitliche Ansicht einiger Metriken des Clusters angezeigt. Sie werden in allgemeine Metriken, abfragebezogene Metriken, erfassungsbezogene Metriken und Metriken im Zusammenhang mit der Streaming-Erfassung gruppiert.

[![Screenshot von Diagramme auf der Registerkarte Schlüsselmetriken.](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

### <a name="usage-tab"></a>Registerkarte „Verwendung“

Auf der Registerkarte **Verbrauch** können Benutzer Details zur Leistung der Befehle und Abfragen des Clusters anzeigen. Auf dieser Registerkarte können Sie folgende Aktionen ausführen:
 
- Sehen Sie, welche Workloadgruppen, Benutzer und Anwendungen die meisten Abfragen senden oder die meiste CPU und den meisten Speicher verbrauchen. Sie können dann verstehen, welche Workloads die umfangreichsten Abfragen stellen, die der Cluster zu verarbeiten hat.
- Identifizieren Sie die wichtigsten Workloadgruppen, Benutzer und Anwendungen anhand von fehlgeschlagenen Abfragen.
- Identifizieren Sie die jüngsten Änderungen in der Anzahl der Abfragen im Vergleich zum historischen Tagesdurchschnitt (über die letzten 16 Tage), aufgeschlüsselt nach Workloadgruppen, Benutzern und Anwendungen.
- Identifizieren Sie Trends und Spitzen bei der Anzahl der Abfragen, dem Speicher- und CPU-Verbrauch nach Workloadgruppe, Benutzer, Anwendung und Befehlstyp.

[![Screenshot der Betriebsansicht mit Ringdiagrammen in Bezug zu Befehlen und Abfragen.](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Screenshot der Betriebsansicht mit Liniendiagrammen in Bezug zu Abfragen und Speicher.](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

### <a name="tables-tab"></a>Registerkarte Tabellen

Die Registerkarte **Tabellen** zeigt die aktuellen und historischen Eigenschaften der Tabellen im Cluster. Sie können sehen, welche Tabellen den meisten Platz verbrauchen. Sie können auch den Wachstumsverlauf nach Tabellengröße, heißen Daten und der Anzahl der Zeilen im zeitlichen Verlauf verfolgen.

### <a name="cache-tab"></a>Registerkarte Cache

Die Registerkarte **Cache** ermöglicht es den Benutzern, die Muster der tatsächlichen Abfragen im Rückblickfenster zu analysieren und sie mit der konfigurierten Cache-Richtlinie (für jede Tabelle) zu vergleichen. Sie können sowohl Tabellen identifizieren, die von sehr vielen Abfragen verwendet werden, als auch Tabellen, die gar nicht abgefragt werden. Dann können Sie die Cacherichtlinie entsprechend anpassen. 

Möglicherweise erhalten Sie Cacherichtlinienempfehlungen für bestimmte Tabellen in Azure Advisor. Derzeit sind Cacheempfehlungen nur über das [Hauptdashboard Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations) verfügbar. Sie basieren auf dem tatsächlichen Abfrage-Rückblickfenster der letzten 30 Tage und einer nicht optimierten Cacherichtlinie für mindestens 95 Prozent der Abfragen. 

Empfehlungen zur Cacheverringerung in Azure Advisor sind für Cluster verfügbar, die „durch Daten gebunden“ sind. Dies bedeutet, dass der Cluster über eine niedrige CPU- und Erfassungsauslastung verfügt, aber aufgrund einer hohen Datenkapazität kann der Cluster nicht zentral herunterskaliert oder herunterskaliert werden.

[![Screenshot von Cachedetails.](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

### <a name="cluster-boundaries-tab"></a>Registerkarte Clustergrenzen

Die Registerkarte **Clustergrenzen** zeigt die Clustergrenzen basierend auf Ihrer Nutzung an. Auf dieser Registerkarte können Sie die CPU-, Erfassungs- und Cacheauslastung überprüfen. Die Metriken werden als **Niedrig**, **Mittel** oder **Hoch** bewertet. Diese Metriken und Bewertungen sind wichtig, wenn Sie über die optimale SKU und Anzahl der Instanzen für Ihren Cluster entscheiden. Sie werden in den Azure Advisor SKU/Größenempfehlungen berücksichtigt. 

Auf dieser Registerkarte können Sie eine metrische Kachel auswählen und weiter in die Tiefe gehen, um ihren Trend zu verstehen und zu erfahren, wie ihre Bewertung ermittelt wird. Außerdem können Sie die SKU-/Größenempfehlung von Azure Advisor für Ihren Cluster anzeigen. In der folgenden Abbildung sehen Sie zum Beispiel, dass alle Metriken als **Low** bewertet werden. Der Cluster erhält eine Kostenempfehlung, die es möglich macht, ihn zu vergrößern/verkleinern und Kosten zu sparen.

> [!div class="mx-imgBorder"]
> [![Screenshot: Clustergrenzen](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-an-azure-dashboard"></a>Anheften an ein Azure Dashboard

Sie können jeden der Metrikabschnitte (der "maßstabgerechten"-Perspektive) an ein Azure-Dashboard anheften, indem Sie das Pushpin-Symbol oben rechts im Abschnitt auswählen.

![Screenshot des ausgewählten Pushpin-Symbols.](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Anpassen von Azure Data Explorer Insights

Sie können die Arbeitsmappe bearbeiten, um sie an Ihre Datenanalyseanforderungen anzupassen:
* Legen Sie die Arbeitsmappe so fest, dass immer ein bestimmtes Abonnement oder Azure Data Explorer-Cluster ausgewählt wird.
* Ändern von Metriken im Raster.
* Ändern von Schwellenwerten oder farbliches Rendering/Codierung.

Sie können mit den Anpassungen beginnen, indem Sie auf der oberen Symbolleiste die Schaltfläche **Anpassen** auswählen.

![Screenshot: Schaltfläche Anpassen.](./media/data-explorer/customize.png)

Anpassungen werden in einer benutzerdefinierten Arbeitsmappe gespeichert, um zu verhindern, dass die Standardkonfiguration in einer veröffentlichten Arbeitsmappe überschrieben wird. Arbeitsmappen werden in einer Ressourcengruppe gespeichert, und zwar entweder im für Sie privaten Abschnitt **Meine Berichte** oder im Abschnitt **Freigegebene Berichte**, der für alle Benutzer mit Zugriff auf die Ressourcengruppe zugänglich ist. Nachdem Sie die benutzerdefinierte Arbeitsmappe gespeichert haben, können Sie sie in dem Arbeitsmappenkatalog öffnen.

![Screenshot des Arbeitsmappenkatalogs.](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Problembehandlung

Eine allgemeine Anleitung zur Problembehandlung finden Sie im Artikel [ Problembehandlung bei arbeitsmappenbasierten Erkenntnissen](troubleshoot-workbooks.md).

Die folgenden Abschnitte helfen Ihnen bei der Diagnose und Problembehandlung einiger der häufigsten Probleme, die bei der Verwendung von Azure Data Explorer Insights auftreten können.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Warum werden in der Abonnementauswahl nicht alle meine Abonnements angezeigt?

Azure Data Explorer Insights zeigt nur Abonnements an, die Azure Data Explorer-Cluster enthalten, die aus dem ausgewählten Abonnementfilter ausgewählt wurden. Sie wählen einen Abonnementfilter unter **Verzeichnis + Abonnement** im Azure-Portal aus.

![Screenshot der Auswahl eines Abonnementfilters.](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-dont-i-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-section"></a>Warum sehe ich keine Daten für meinen Azure Data Explorer-Cluster unter dem Abschnitt Nutzung, Tabellen oder Cache?

Um Ihre protokollbasierten Daten anzuzeigen, müssen Sie die [ Diagnoseprotokolle akitivieren](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) für jeden Azure Data Explorer-Cluster, den Sie überwachen möchten. Sie können dies unter den Diagnoseeinstellungen für jeden Cluster tun. Sie müssen Ihre Daten an einen Log Analytics-Arbeitsbereich senden. Die Diagnoseprotokolle, die aktiviert werden sollten, sind **Befehl**,  **Abfrage**, **TableDetails** und **TableUsageStatistics**.

### <a name="ive-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Ich habe bereits Protokolle für meinen Azure Data Explorer-Cluster aktiviert. Warum kann ich meine Daten immer noch nicht unter Befehle und Abfragen sehen?

Derzeit funktionieren Diagnoseprotokolle nicht rückwirkend. Die Daten werden erst angezeigt, nachdem Aktionen in Azure Data Explorer durchgeführt wurden. Das kann einige Zeit dauern, von Stunden bis zu einem Tag, je nachdem, wie aktiv Ihr Azure Data Explorer-Cluster ist.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie die Szenarien, für die Arbeitsmappen entwickelt wurden, wie Sie neue Berichte erstellen und vorhandene Berichte anpassen können und vieles mehr, indem Sie [Erstellen von interaktiven Berichten mit Azure Monitor Arbeitsmappen](../visualize/workbooks-overview.md)lesen.
