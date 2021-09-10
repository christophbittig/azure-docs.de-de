---
title: Log Analytics-Arbeitsbereich Insights
description: Eine Übersicht über die Log Analytics-Arbeitsbereich Insights – Erfassung, Nutzung, Integrität, Agents und mehr
services: azure-monitor
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 05/06/2021
ms.openlocfilehash: 0d5fb976bfeb496e6c69e7df401ed0a756985475
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2021
ms.locfileid: "113295882"
---
# <a name="log-analytics-workspace-insights-preview"></a>Die Log Analytics-Arbeitsbereich Insights (Vorschau)

Die Log Analytics-Arbeitsbereich Insights (Vorschau) bietet eine umfassende Überwachung Ihrer Arbeitsbereiche durch eine einheitliche Ansicht von der Nutzung, Leistung, Integrität, des Agents, der Abfragen und des Änderungsprotokolls Ihres Arbeitsbereichs. Dieser Artikel hilft Ihnen zu Verstehen wie die Log Analytics-Arbeitsbereich Insights (Vorschau) integriert und verwendet werden.

## <a name="overview-your-log-analytics-workspaces"></a>Eine Übersicht der Log Analytics-Arbeitsbereiche

Beim Zugriff auf die Log Analytics-Arbeitsbereich Insights über die Azure Monitor Insights wird die Perspektive „Im großen Stil“ angezeigt. Hier können Sie sehen, wie Ihre Arbeitsbereiche auf der ganzen Welt verteilt sind. Sie können ihre Aufbewahrungs-, Obergrenzen- und Lizenzdetails überprüfen (farblich codiert) und einen Arbeitsbereich auswählen, um seine Insights anzuzeigen.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-scale.png" alt-text="Screenshot: Die Log Analytics-Arbeitsbereich Insights-Liste der Arbeitsbereiche" lightbox="media/log-analytics-workspace-insights-overview/at-scale-expanded.png":::


Führen Sie die folgenden Schritte aus, um die Log Analytics-Arbeitsbereich Insights im großen Stil zu starten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Monitor** und im Abschnitt „Insights-Hub“ die Option **Log Analytics-Arbeitsbereich Insights (Vorschau)** aus.

## <a name="view-insights-for-a-log-analytics-workspace"></a>Anzeigen der Log Analytics-Arbeitsbereich Insights

Beim Starten der Insights im Kontext eines bestimmten Arbeitsbereichs werden umfassende Daten und Analysen der Arbeitsbereichsleistung, -nutzung, -integrität, -agents, -abfragen und des Änderungsprotokolls angezeigt.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-resource.png" alt-text="Screenshot: Eine Übersicht der Log Analytics-Arbeitsbereich Insights" lightbox="media/log-analytics-workspace-insights-overview/at-resource.png":::

Zugriff auf die Log Analytics-Arbeitsbereich Insights:

1. Öffnen der Log Analytics-Arbeitsbereich Insights über Azure Monitor (wie oben erläutert)

2. Auswählen eines Arbeitsbereichs für Detailinformationen

oder 

1. Wählen Sie im Azure-Portal die **Log Analytics-Arbeitsbereiche** aus

2. Auswählen eines Log Analytics-Arbeitsbereichs

3. Wählen Sie im Menü „Arbeitsbereich“ (unter Überwachung) die Option **Insights** aus

Die Daten sind in Registerkarten organisiert. Der oben angezeigte Zeitbereich (Standardwert: 24 Stunden) gilt für alle Registerkarten. Einige Diagramme und Tabellen verwenden einen anderen Zeitbereich, als in ihren Titeln angegeben ist.


### <a name="overview-tab"></a>Registerkarte „Übersicht“

Auf der Registerkarte **Übersicht** sehen Sie:

* Die Hauptstatistiken und Einstellungen
    - Das monatliche Erfassungsvolumen des Arbeitsbereichs
    - Wie viele Computer Heartbeats gesendet haben, d. h. Computer, die mit diesem Arbeitsbereich verbunden sind (in dem ausgewählten Zeitbereich)
    - Die Computer, die in der letzten Stunde (im ausgewählten Zeitbereich) keine Heartbeats gesendet haben
    - Den festgelegten Datenaufbewahrungszeitraum
    - Die festgelegte tägliche Obergrenze und die Menge der Daten, die am Tag zuvor bereits erfasst wurden

* Top-5-Tabellen: Diagramme, in denen die fünf am häufigsten erfassten Tabellen des letzten Monats analysiert werden.
    - Das Volumen der in jeder Tabelle erfassten Daten
    - Die tägliche Erfassung für jeden von ihnen, um die Spitzen oder Einbrüche visuell anzuzeigen
    - Erfassungsanomalien: Eine Liste der ermittelten Spitzen und Einbrüche bei der Erfassung in diesen Tabellen


### <a name="usage-tab"></a>Registerkarte „Verwendung“

#### <a name="usage-dashboard"></a>Dashboard „Nutzung“

Diese Registerkarte enthält Informationen zur Verwendung des Arbeitsbereichs. Auf der Unterregisterkarte des Dashboards werden die Erfassungsdaten nach Tabellen angezeigt. Standardmäßig werden die fünf am häufigsten erfassten Tabellen im ausgewählten Zeitbereich angezeigt (dieselben Tabellen werden auf der Seite Übersicht angezeigt). Sie können auswählen, welche Tabellen in der Dropdownliste „Arbeitsbereichstabellen“ angezeigt werden sollen. 

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage.png" alt-text="Screenshot: Die Registerkarte „Nutzung des Arbeitsbereichs“" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage.png":::

* Hauptraster: Hier werden die Tabellen angezeigt, gruppiert nach Lösungen und Informationen zu den einzelnen Tabellen.“
    - Die Menge der erfassten Daten (während des ausgewählten Zeitbereichs)
    - Der Prozentsatz, den diese Tabelle vom gesamten Erfassungsvolumen (während des ausgewählten Zeitraums) übernimmt. So können die Tabellen ermittelt werden, die sich am stärksten auf Ihre Erfassung auswirken. Im folgenden Screenshot sehen Sie, dass AzureDiagnostics und ContainerLog allein mehr als zwei Drittel (64 %) der in diesem Arbeitsbereich erfassten Daten ausmachen.
    - Wann wurden die Nutzungsstatistiken für die einzelnen Tabellen zuletzt aktualisiert? Normalerweise erwarten wir, dass die Nutzungsstatistiken stündlich aktualisiert werden. Da das Aktualisieren von Nutzungsstatistiken ein wiederkehrender dienst-interner Vorgang ist, wird eine Verzögerung bei der Aktualisierung dieser Daten nur notiert, damit Sie wissen, dass die Daten richtig interpretiert werden müssen. Es gibt keine Aktion, die Sie (als Benutzer) ergreifen sollten.
    - Abrechnungsfähig: Hier wird angezeigt, welche Tabellen kostenpflichtig und welche kostenlos sind.

* Die tabellenspezifischen Details

    Unten auf der Seite finden Sie die ausführlichen Informationen zu der im Hauptraster ausgewählten Tabelle.
    - Das Erfassungsvolumen: Die Anzahl der Daten, die von jeder Ressource in der Tabelle erfasst wurden und wie sie sich im Laufe der Zeit verteilen. Die Ressourcen, die mehr als 30 % des gesamt an diese Tabelle gesendeten Volumens erfassen, werden mit einem Warnzeichen gekennzeichnet, das Sie sich notieren können.
    - Die Erfassungslatenz: Die von der Erfassung in Anspruch genommene Zeit, analysiert für das 50., 90. oder 95. Perzentil von Anforderungen, die an diese Tabelle gesendet wurden. Das obere Diagramm in diesem Bereich stellt die gesamte Erfassungszeit der Anforderungen (für das ausgewählte Perzentil) von Ende zu Ende dar – vom Anfangszeitpunkt des Ereignisses bis zur Erfassung im Arbeitsbereich.
    Das Diagramm darunter zeigt separat die Latenz des Agents (die Zeit, die der Agent zum Senden des Protokolls an den Arbeitsbereich brauchte) und die der Pipeline (die Zeit, die der Dienst für die Verarbeitung der Daten und den Push-Vorgang in den Arbeitsbereich brauchte).
    :::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png" alt-text="Screenshot: Die Unterregisterkarte „Latenz bei der Erfassung der Arbeitsbereichsnutzung“" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png":::

#### <a name="additional-usage-queries"></a>Die zusätzlichen Nutzungsabfragen

Die Unterregisterkarte „Zusätzliche Abfragen“ macht Abfragen verfügbar, die über alle Arbeitsbereichstabellen hinweg ausgeführt werden (anstatt sich auf die Nutzungsmetadaten zu verlassen, die stündlich aktualisiert werden). Da ihre Abfragen viel umfangreicher und weniger effizient sind, werden sie nicht automatisch ausgeführt. Sie können jedoch interessante Informationen darüber anzeigen, welche Ressourcen die meisten Protokolle an den Arbeitsbereich senden, und sich möglicherweise auf die Abrechnung auswirken.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png" alt-text="Screenshot: Die Registerkarte „Zusätzliche Abfragen“ für die Arbeitsbereichsnutzung" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png":::

Eine solche Abfrage ist „Welche Azure-Ressourcen senden die meisten Protokolle an diesen Arbeitsbereich“ (Anzeige der Top 50).
In unserem Demoarbeitsbereich sehen Sie deutlich, dass 3 Kuberbetes-Cluster weitaus mehr Daten senden als alle anderen Ressourcen zusammen. Ein bestimmter von ihnen belastet den Arbeitsbereich am meisten.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png" alt-text="Screenshot: Die Registerkarte „Zusätzliche Abfragen“ für die Arbeitsbereichsnutzung mit den Ergebnissen einer zusätzlichen Abfrage" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png":::


### <a name="health-tab"></a>Die Registerkarte „Integrität“

Auf dieser Registerkarte werden der Integritätsstatus des Arbeitsbereichs und der Zeitpunkt der letzten Meldung sowie operative [Fehler und Warnungen](../logs/monitor-workspace.md) (aus der _LogOperation-Tabelle abgerufen) angezeigt. Weitere Informationen zu den aufgeführten Problemen sowie Schritte zur Entschärfung finden Sie [hier](../logs/monitor-workspace.md#categories).

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-health.png" alt-text="Screenshot: Die Registerkarte „Integrität des Arbeitsbereichs“" lightbox="media/log-analytics-workspace-insights-overview/workspace-health.png":::

### <a name="agents-tab"></a>Registerkarte "Agents"

Diese Registerkarte enthält Informationen zu den Agents, die Protokolle an diesen Arbeitsbereich senden.
:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-agents.png" alt-text="Screenshot: Die Registerkarte „Arbeitsbereich-Agents“" lightbox="media/log-analytics-workspace-insights-overview/workspace-agents.png":::

* Vorgangsfehler und Warnungen: Das sind die Fehler und Warnungen, die sich speziell auf die Agents beziehen. Sie sind nach dem Fehler-/Warnungstitel gruppiert, damit Sie einen genaueren Überblick über verschiedene Probleme erhalten, die auftreten können. Sie können aber erweitert werden, um die genauen Zeiten und Ressourcen anzuzeigen, auf die sie verweisen. Beachten Sie auch, dass Sie auf „Abfrage in Protokollen ausführen“ klicken können, um die _LogOperation-Tabelle über die Protokollierung abzufragen, die Rohdaten anzuzeigen und sie ggf. weiter zu analysieren.
* Die Arbeitsbereich-Agents: Das sind die Agents, die während des ausgewählten Zeitraums Protokolle an den Arbeitsbereich gesendet haben. Hier sehen Sie den Typ und den Integritätszustand der Agents. Als fehlerfrei markierte Agents funktionieren nicht unbedingt gut. Die Markierung deutete nur darauf hin, dass sie in der letzten Stunde einen Heartbeat gesendet haben. Ein ausführlicherer Integritätsstatus wird im folgenden Raster beschrieben.
* Die Agents-Aktivität: In diesem Raster werden die Informationen zu allen Agents, unabhängig davon ob fehlerfrei oder fehlerhaft, angezeigt. Auch hier hat „Fehlerfrei“ nur daraufhin gedeutet, dass der Agent in der letzten Stunde einen Heartbeat gesendet hat. Um den Zustand besser zu verstehen, überprüfen Sie den im Raster angezeigten Trend. Er zeigt, wie viele Heartbeats dieser Agent im Laufe der Zeit gesendet hat. Der tatsächliche Integritätsstatus kann nur hergeleitet werden, wenn Sie wissen, wie die überwachte Ressource funktioniert. Wenn ein Computer beispielsweise absichtlich zu bestimmten Zeiten heruntergefahren wird, können Sie erwarten, dass die Agent-Heartbeats zeitweilig in einem Abgleichmuster angezeigt werden.


### <a name="query-audit-tab"></a>Die Registerkarte „Abfrageüberwachung“

Die Abfrageüberwachung erstellt Protokolle zur Ausführung von Abfragen im Arbeitsbereich. Wenn diese Daten aktiviert sind, sind sie für das Verstehen und die Verbesserung der Leistung, Effizienz und Auslastung von Abfragen von großem Vorteil. Weitere Informationen zum Aktivieren der Abfrageüberwachung in Ihrem Arbeitsbereich finden Sie unter den [Abfrageüberwachungen in den Azure Monitor Protokollen](../logs/query-audit.md).

#### <a name="performance"></a>Leistung 
Diese Registerkarte zeigt:
* Die Abfragedauer: Das 95. Perzentil und das 50. Perzentil (Median) der Dauer in Millisekunden im Laufe der Zeit.
* Die Anzahl der zurückgegebenen Zeilen: Das 95. Perzentil und das 50. Perzentil (Median) der Zeilenanzahl im Laufe der Zeit.
* Das Volumen der verarbeiteten Daten: Das 95. Perzentil und das 50. Perzentil und die Gesamtmenge der verarbeiteten Daten in allen Anforderungen im Laufe der Zeit.
* Die Antwortcodes: Die Verteilung von Antwortcodes an alle Abfragen im ausgewählten Zeitbereich.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png" alt-text="Screenshot: Die Registerkarte „Abfrageüberwachung“, Unterregisterkarte „Leistung“" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png":::

#### <a name="slow-and-inefficient-queries"></a>Die langsamen und ineffizienten Abfragen 
Auf dieser Registerkarte werden zwei Raster angezeigt, mit deren Hilfe Sie die langsamen und ineffizienten Abfragen identifizieren können, die Sie möglicherweise überdenken möchten. Diese Abfragen sollten nicht in den Dashboards oder Warnungen verwendet werden, da sie zu einer unnötigen chronischen Belastung in Ihrem Arbeitsbereich führen.
* Die meisten ressourcenintensiven Abfragen: Die 10 Abfragen mit der höchsten CPU-Belastung zusammen mit dem verarbeiteten Datenvolumen (KB), dem Zeitbereich und dem Text jeder Abfrage.
* Die langsamsten Abfragen: die 10 langsamsten Abfragen, zusammen mit dem Zeitbereich und dem Text jeder Abfrage.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png" alt-text="Screenshot der Registerkarte „Abfrageüberwachung“, Unterregisterkarte „Langsame Abfragen“" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png":::

#### <a name="query-users"></a>Die Abfragebenutzer 
Auf dieser Registerkarte wird die Benutzeraktivität für diesen Arbeitsbereich angezeigt:
* Die Abfragen nach den Benutzern gruppiert: Wie viele Abfragen jeder Benutzer im ausgewählten Zeitbereich ausgeführt hat
* Gedrosselte Benutzer: Die Benutzer, die Abfragen durchgeführt haben, die gedrosselt wurden (aufgrund von zu vielen Abfragen im Arbeitsbereich)

### <a name="change-log-tab"></a>Die Registerkarte „Änderungsprotokoll“

Auf dieser Registerkarte werden die Konfigurationsänderungen angezeigt, die in den letzten 90 Tagen im Arbeitsbereich vorgenommen wurden (unabhängig vom ausgewählten Zeitbereich) und wer sie vorgenommen hat.
Es soll Ihnen helfen zu überwachen, wer wichtige Arbeitsbereichseinstellungen ändert, z. B. die Datendeckelung oder die Arbeitsbereichslizenz.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-change-log.png" alt-text="Screenshot: Die Registerkarte „Änderungsprotokoll“ des Arbeitsbereichs" lightbox="media/log-analytics-workspace-insights-overview/workspace-change-log.png":::


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Erstellen interaktiver Berichte mit Azure Monitor-Arbeitsmappen](../visualize/workbooks-overview.md) über die Szenarien, die Arbeitsmappen unterstützen sollen, wie Sie neue Berichte erstellen und vorhandene Berichte anpassen können, und vieles mehr.
