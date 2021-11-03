---
title: Azure Monitor-Protokolle
description: Lernen Sie die Grundlagen der Azure Monitor-Protokolle kennen, die für die erweiterte Analyse von Überwachungsdaten verwendet werden.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 0814a25c96ae1d3d2d624746db0c753c782d8dad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078216"
---
# <a name="azure-monitor-logs-overview"></a>Übersicht über Azure Monitor-Protokolle
Azure Monitor-Protokolle sind ein Feature von Azure Monitor, mit dem Protokoll- und Leistungsdaten von [überwachten Ressourcen](../monitor-reference.md) gesammelt und organisiert werden. Daten aus mehreren Quellen können in einen einzelnen Arbeitsbereich konsolidiert werden. Diese Quellen können folgende sein:

- [Plattformprotokolle](../essentials/platform-logs-overview.md) von Azure-Diensten.
- Protokoll- und Leistungsdaten von [Agents für virtuelle Computer](../agents/agents-overview.md).
- Nutzungs- und Leistungsdaten von [Anwendungen](../app/app-insights-overview.md). 

Diese Daten können Sie mithilfe einer ausgereiften Abfragesprache analysieren, die eine schnelle Analyse von Millionen von Datensätzen ermöglicht. 

Sie können eine einfache Abfrage ausführen, die nur eine bestimmte Gruppe von Datensätzen abruft; Sie können auch eine anspruchsvolle Datenanalyse durchführen, um kritische Muster in Ihren Überwachungsdaten zu erkennen. Arbeiten Sie mithilfe von Log Analytics interaktiv mit Protokollabfragen und deren Ergebnissen, verwenden Sie diese in Warnungsregeln, um bei Problemen proaktiv benachrichtigt zu werden, oder visualisieren Sie die Ergebnisse in einer Arbeitsmappe oder einem Dashboard.

> [!NOTE]
> Azure Monitor-Protokolle sind die eine Hälfte der Datenplattform, die Azure Monitor unterstützt. Die andere Hälfte sind [Azure Monitor-Metriken](../essentials/data-platform-metrics.md), die numerische Daten in einer Zeitreihendatenbank speichern. Numerische Daten sind weniger ressourcenintensiv als Daten in Azure Monitor Protokollen. Azure Monitor-Metriken können Szenarien in Quasi-Echtzeit unterstützen und eignen sich daher gut für Warnungen und die schnelle Erkennung von Problemen. 
>
> Azure Monitor-Metriken können nur numerische Daten in einer bestimmten Struktur speichern, Azure Monitor-Protokolle dagegen können eine Vielzahl von Datentypen speichern, die über eigene Strukturen verfügen. In Azure Monitor-Protokolldaten können Sie auch komplexe Analysen mithilfe von Protokollabfragen durchführen, die zur Analyse von Azure Monitor-Metrikdaten nicht verwendet werden können.

## <a name="what-can-you-do-with-azure-monitor-logs"></a>Welche Möglichkeiten bieten Azure Monitor-Protokolle?
Die folgende Tabelle beschreibt einige der Möglichkeiten zur Nutzung von Azure Monitor-Protokollen:

|  | BESCHREIBUNG |
|:---|:---|
| **Analysieren** | Verwenden von [Log Analytics](./log-analytics-tutorial.md) im Azure-Portal zum Schreiben von [Protokollabfragen](./log-query-overview.md) und interaktives Analysieren von Protokolldaten mithilfe einer leistungsstarken Analyse-Engine. |
| **Warnung** | Konfigurieren einer [Protokollwarnungsregel](../alerts/alerts-log.md), die eine Benachrichtigung sendet oder eine [automatisierte Aktion](../alerts/action-groups.md) ausführt, wenn die Ergebnisse der Abfrage mit einem bestimmten Ergebnis übereinstimmen. |
| **Visualisieren** | Anheften der als Tabellen oder Diagramme gerenderten Abfrageergebnisse an ein [Azure-Dashboard](../../azure-portal/azure-portal-dashboards.md).<br>Erstellen einer [Arbeitsmappe](../visualize/workbooks-overview.md), um mehrere Sätze von Daten in einem interaktiven Bericht zu kombinieren. <br>Exportieren der Ergebnisse einer Abfrage nach [Power BI](./log-powerbi.md), um verschiedene Visualisierungen zu verwenden und sie mit Benutzern außerhalb von Azure zu teilen.<br>Exportieren der Ergebnisse einer Abfrage nach [Grafana](../visualize/grafana-plugin.md), um dessen Dashboards zu nutzen und die Daten mit anderen Quellen zu kombinieren.|
| **Gewinnen von Erkenntnissen** | Unterstützen von [Erkenntnissen](../monitor-reference.md#insights-and-curated-visualizations), die eine angepasste Überwachungsoberfläche für bestimmte Anwendungen und Dienste bereitstellen.  |
| **Abrufen** | Zugreifen auf Ergebnisse von Protokollabfragen über eine Befehlszeile mit der [Azure CLI](/cli/azure/monitor/log-analytics).<br>Zugreifen auf Ergebnisse von Protokollabfragen über eine Befehlszeile mit [PowerShell-Cmdlets](/powershell/module/az.operationalinsights).<br>Zugreifen auf Ergebnisse von Protokollabfragen über eine benutzerdefinierte Anwendung mit der [REST-API](https://dev.loganalytics.io/). |
| **Export** | Konfigurieren des [automatisierten Exports von Protokolldaten](./logs-data-export.md) in ein Azure-Speicherkonto oder in Azure Event Hubs.<br>Erstellen eines Workflows zum Abrufen von Protokolldaten und Kopieren der Daten an einen externen Speicherort mithilfe von [Azure Logic Apps](./logicapp-flow-connector.md). |

![Diagramm: Übersicht über Azure Monitor-Protokolle.](media/data-platform-logs/logs-overview.png)

## <a name="data-collection"></a>Datensammlung
Nachdem Sie einen Log Analytics-Arbeitsbereich erstellt haben, müssen Sie Quellen zum Senden von Daten konfigurieren. Es werden keine Daten automatisch erfasst. 

Diese Konfiguration ist je nach Datenquelle unterschiedlich. Zum Beispiel:

- [Erstellen Sie Diagnoseeinstellungen](../essentials/diagnostic-settings.md), um Ressourcenprotokolle von Azure-Ressourcen an den Arbeitsbereich zu senden. 
- [Aktivieren Sie VM Insights](../vm/vminsights-enable-overview.md), um Daten von virtuellen Computern zu sammeln. 
- [Konfigurieren Sie Datenquellen im Arbeitsbereich](../agents/data-sources.md), um weitere Ereignisse und Leistungsdaten zu sammeln.

Unter [Was wird von Azure Monitor überwacht?](../monitor-reference.md) finden Sie eine vollständige Liste der Datenquellen, die Sie für das Senden von Daten an Azure Monitor-Protokolle konfigurieren können.

## <a name="log-analytics-and-workspaces"></a>Log Analytics und Arbeitsbereiche
Log Analytics ist ein Tool im Azure-Portal. Verwenden Sie es, um Protokollabfragen zu bearbeiten und auszuführen und deren Ergebnisse interaktiv zu analysieren. Anschließend können Sie diese Abfragen zur Unterstützung anderer Features in Azure Monitor verwenden, wie z. B. Protokollabfragewarnungen und Arbeitsmappen. Sie können über die Option **Protokolle** im Azure Monitor-Menü oder über die meisten anderen Dienste im Azure-Portal auf Log Analytics zugreifen.

Eine Beschreibung von Log Analytics finden Sie unter [Übersicht über Log Analytics in Azure Monitor](./log-analytics-overview.md). Verwenden Sie das [Log Analytics-Tutorial](./log-analytics-tutorial.md), um die Verwendung von Log Analytics-Features zum Erstellen einer einfachen Protokollabfrage und Analysieren der Ergebnisse schrittweise kennenzulernen.

Azure Monitor-Protokolle speichern die gesammelten Daten in mindestens einem [Log Analytics-Arbeitsbereich](./design-logs-deployment.md). Ein Arbeitsbereich definiert Folgendes:

- Den geografischen Standort der Daten.
- Zugriffsrechte, die definieren, welche Benutzer auf Daten zugreifen können.
- Konfigurationseinstellungen wie Tarif und Datenaufbewahrung.  

Sie müssen mindestens einen Arbeitsbereich erstellen, um Azure Monitor-Protokolle zu verwenden. Ein einziger Arbeitsbereich kann für alle Überwachungsdaten ausreichend sein; Sie können aber auch je nach Anforderungen mehrere Arbeitsbereiche erstellen. Sie können beispielsweise über einen Arbeitsbereich für die Produktionsdaten und einen anderen für Testzwecke verfügen. 

Informationen zum Erstellen eines neuen Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](./quick-create-workspace.md). Überlegungen zum Erstellen mehrerer Arbeitsbereiche finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](design-logs-deployment.md).

## <a name="log-queries"></a>Protokollabfragen
Daten werden mithilfe einer Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Hierbei handelt es sich um eine schreibgeschützte Anforderung zum Verarbeiten von Daten und Zurückgeben von Ergebnissen. Protokollabfragen werden in der [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/) geschrieben. KQL ist die gleiche Abfragesprache, die auch von Azure Data Explorer verwendet wird. 

Sie können Protokollabfragen in Log Analytics schreiben, um die Ergebnisse interaktiv zu analysieren, sie in Warnungsregeln verwenden, um proaktiv über Probleme informiert zu werden, oder die Ergebnisse in Arbeitsmappen oder Dashboards einschließen. Erkenntnisse umfassen vordefinierte Abfragen zur Unterstützung der Sichten und Arbeitsmappen.

Eine Liste der Einsatzmöglichkeiten von Protokollabfragen sowie Hinweise zu Tutorials und weiterer Dokumentation für den Einstieg finden Sie unter [Protokollabfragen in Azure Monitor](./log-query-overview.md).

![Screenshot: Abfragen in Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="data-structure"></a>Datenstruktur
Protokollabfragen rufen Daten aus einem Log Analytics-Arbeitsbereich ab. Jeder Arbeitsbereich enthält mehrere Tabellen, die in separaten Spalten mit mehreren Datenzeilen angeordnet sind. Jede Tabelle wird durch einen einzigartigen Satz an Spalten definiert. Von der Datenquelle bereitgestellte Datenzeilen verwenden diese Spalten gemeinsam. 

[![Diagramm: Struktur der Azure Monitor-Protokolle](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)

Protokolldaten aus Application Insights werden ebenfalls in Azure Monitor-Protokollen gespeichert, allerdings erfolgt die Speicherung je nach Konfiguration Ihrer Anwendung auf unterschiedliche Weise: 

- Bei einer auf einem Arbeitsbereich basierenden Anwendung werden die Daten in einem Log Analytics-Arbeitsbereich in einem Standardsatz Tabellen gespeichert. Zu den Datentypen gehören Anwendungsanforderungen, Ausnahmen und Seitenaufrufe. Mehrere Anwendungen können denselben Arbeitsbereich verwenden. 

- Bei einer klassischen Anwendung werden die Daten nicht in einem Log Analytics-Arbeitsbereich gespeichert. Es wird die gleiche Abfragesprache verwendet, und Sie können Abfragen mit dem gleichen Log Analytics-Tool im Azure-Portal erstellen und ausführen. Datenelemente für klassische Anwendungen werden getrennt voneinander gespeichert. Die allgemeine Struktur entspricht der für arbeitsbereichsbasierte Anwendungen, die Tabellen- und Spaltennamen unterscheiden sich jedoch. 

Einen ausführlichen Vergleich des Schemas für arbeitsbereichsbasierte und klassische Anwendungen finden Sie unter [Änderungen bei arbeitsbereichsbasierten Ressourcen](../app/apm-tables.md).

> [!NOTE]
> Die klassische Application Insights-Oberfläche bietet Abwärtskompatibilität für Ihre Ressourcenabfragen, Arbeitsmappen und protokollbasierten Warnungen. Um die [neue arbeitsbereichsbasierte Tabellenstruktur und das neue Tabellenschema](../app/apm-tables.md) anzuzeigen und abzufragen, müssen Sie zuerst zu Ihrem Log Analytics-Arbeitsbereich navigieren. Während der Vorschau erhalten Sie durch Auswahl der Option **Protokolle** in den Application Insights-Bereichen Zugriff auf die klassische Application Insights-Benutzeroberfläche für Abfragen. Weitere Informationen finden Sie unter [Abfragebereich](./scope.md).

[![Diagramm: Azure Monitor-Protokollstruktur für Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="relationship-to-azure-data-explorer"></a>Beziehung zu Azure Data Explorer
Azure Monitor-Protokolle basieren auf Azure Data Explorer. Ein Log Analytics-Arbeitsbereich entspricht in etwa einer Datenbank in Azure Data Explorer. Tabellen sind gleich strukturiert, und beide verwenden KQL. 

Die Verwendung von Log Analytics zum Arbeiten mit Azure Monitor-Abfragen im Azure-Portal ähnelt der Verwendung der Azure Data Explorer-Webbenutzeroberfläche. Sie können sogar [Daten aus einem Log Analytics-Arbeitsbereich in eine Azure Data Explorer-Abfrage einschließen](/azure/data-explorer/query-monitor-data). 

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Protokollabfragen](./log-query-overview.md) zum Abrufen und Analysieren von Daten aus einem Log Analytics-Arbeitsbereich.
- Informieren Sie sich über [Metriken in Azure Monitor](../essentials/data-platform-metrics.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](../agents/data-sources.md).