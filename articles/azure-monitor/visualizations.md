---
title: Visualisieren von Daten aus Azure Monitor | Microsoft-Dokumentation
description: Hier erhalten Sie eine Übersicht über die verfügbaren Methoden zum Visualisieren von in Azure Monitor gespeicherten Metrik- und Protokolldaten.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 257ca33952283ba3989ce11a2d933af3feceb8ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612349"
---
# <a name="visualize-data-from-azure-monitor"></a>Visualisieren von Daten aus Azure Monitor
Dieser Artikel biete eine Übersicht über die verfügbaren Methoden zum Visualisieren von in Azure Monitor gespeicherten Metrik- und Protokolldaten.

Visualisierungen wie Diagramme und Grafiken können Ihnen die Analyse Ihrer Überwachungsdaten erleichtern, sodass Sie Detailinformationen zu Problemen anzeigen und Muster identifizieren können. Je nach verwendetem Tool haben Sie möglicherweise auch die Option, Visualisierungen für andere Benutzer innerhalb und außerhalb Ihrer Organisation freizugeben.

## <a name="workbooks"></a>Arbeitsmappen
[Arbeitsmappen](./visualize/workbooks-overview.md) sind interaktive Dokumente, die umfassende Einblicke in Ihre Daten, Untersuchungen und die Zusammenarbeit innerhalb des Teams bereitstellen. Arbeitsmappen sind besonders nützlich für Leitfäden zur Problembehandlung und Incident-Postmortems.

![Diagramm: Screenshots von drei Seiten einer Arbeitsmappe – Analyse der Seitenaufrufe, Nutzung und die auf der Seite verbrachte Zeit.](media/visualizations/workbook.png)

Eine Arbeitsmappe bietet u. a. folgende Vorteile:

- Unterstützung sowohl für Metriken als auch für Protokolle.
- Unterstützung für Parameter, die interaktive Berichte ermöglichen, wobei durch die Auswahl eines Elements in einer Tabelle zugeordnete Diagramme und Visualisierungen dynamisch aktualisiert werden.
- Dokumentähnlicher Ablauf.
- Optionen für persönliche oder freigegebene Arbeitsmappen.
- Einfache Benutzeroberfläche für die Erstellung zur verbesserten Zusammenarbeit.
- Vorlagen, die einen öffentlichen, GitHub basierten Vorlagenkatalog unterstützen.


## <a name="azure-dashboards"></a>Azure-Dashboards
[Azure-Dashboards](../azure-portal/azure-portal-dashboards.md) sind die primäre Dashboardtechnologie für Azure. Sie bietet eine zentrale Ansicht Ihrer Azure-Infrastruktur und -Dienste, sodass Sie wichtige Aspekte und Probleme schnell identifizieren können.

![Screenshot: Beispiel eines Azure-Dashboards mit anpassbaren Informationen.](media/visualizations/dashboard.png)

Hier finden Sie eine Videoanleitung zum Erstellen von Dashboards:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

Ein Dashboard bietet u. a. die folgenden Vorteile:

- Enge Integration in Azure. Visualisierungen von mehreren Azure-Seiten (einschließlich [Metrik-Explorer](essentials/metrics-charts.md), [Log Analytics](logs/log-analytics-overview.md) und [Application Insights](app/app-insights-overview.md)) können an Dashboards angeheftet werden.
- Unterstützung sowohl für Metriken als auch für Protokolle.
- Es können Daten aus mehreren Quellen kombiniert werden, z. B. aus Ausgaben des [Metrik-Explorers](essentials/metrics-charts.md), [Protokollabfragen](logs/log-query-overview.md) und [Karten](app/app-map.md), und in [Application Insights](app/app-insights-overview.md) zur Verfügung gestellt werden.
- Optionen für persönliche oder freigegebene Dashboards durch Integration in die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md).
- Automatische Aktualisierung. Die Metrikaktualisierung hängt vom Zeitbereich ab, das Minimum beträgt fünf Minuten. Protokolle werden stündlich aktualisiert. Sie können die Daten bei Bedarf manuell aktualisieren, indem Sie in einer Visualisierung das Symbol **Aktualisieren** auswählen oder das gesamte Dashboard aktualisieren.
- Parametrisierte Metrikdashboards mit Zeitstempeln und benutzerdefinierten Parametern.
- Flexible Layoutoptionen.
- Vollbildmodus.


Es gelten einige Einschränkungen, u. a. folgende:

- Begrenzte Kontrolle über Protokollvisualisierungen, keine Unterstützung für Datentabellen. Die Gesamtanzahl von Datenreihen ist auf 50 begrenzt. Weitere Datenreihen werden in einem _anderen_ Bucket gruppiert.
- Benutzerdefinierte Parameter für Protokolldiagramme werden nicht unterstützt.
- Begrenzter Zeitraum für Protokolldiagramme (letzte 30 Tage).
- Protokolldiagramme müssen an freigegebene Dashboards angeheftet werden.
- Keine Interaktivität mit Dashboarddaten.
- Eingeschränkte kontextbezogene Anzeige von Detailinformationen.

## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) ist eine offene Plattform, die sich besonders gut für operative Dashboards und die Visualisierung von Leistungs- und Verfügbarkeitsdaten eignet. Sie hilft beim Erkennen, Isolieren und Selektieren von operativen Incidents. Grafana bietet integrierte Unterstützung für Azure Monitor und enthält das [Azure Monitor-Plug-In](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/). Es unterstützt die Visualisierung von Daten aus drei Azure-Diensten:
- Azure Monitor-Metriken für numerische Zeitreihendaten aus Daten aus Azure-Ressourcen. 
- Azure Monitor-Protokolle für Protokoll- und Leistungsdaten aus Azure-Ressourcen, mit denen Sie Abfragen mithilfe der leistungsstarken Kusto Query Language (KQL) ausführen können.
- Azure Resource Graph, um Azure-Ressourcen abonnementübergreifend schnell abzufragen und zu identifizieren.

Grafana funktioniert mit einer Vielzahl von Datenquellen und ermöglicht es Ihnen, Dashboards und Panels zu erstellen, die Überwachungsdaten aus Ressourcen in Azure, lokalen Systemen oder anderen Clouds kombinieren. 

![Screenshot: Grafana-Visualisierungen.](media/visualizations/grafana.png)

> [!IMPORTANT]
> Internet Explorer 11 wird nur in Grafana-Versionen vor v6.0 vollständig unterstützt. Weitere Informationen finden Sie unter [Supported web browsers](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers) (Unterstützte Browser für Grafana).

Grafana bietet u. a. folgende Vorteile:

- Umfassende Visualisierungen.
- Ein umfangreiches Ökosystem aus Datenquellen, einschließlich Unterstützung für Nicht-Azure-Datenquellen.
- Dateninteraktivität einschließlich Zoomen auf Details.
- Anmerkungen, mit denen Ereignisdaten in Diagramme integriert werden können.
- Unterstützung für Parameter, einschließlich [Azure Monitor-Variablen](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/).

Es gelten einige Einschränkungen, u. a. folgende:

- Dashboards und Modelle können nicht über Azure Resource Manager verwaltet werden.
- Kosten für die Unterstützung zusätzlicher Grafana-Infrastruktur oder zusätzliche Kosten für Grafana Cloud.
## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) ist hilfreich bei der Erstellung von Dashboards und Berichte für das Business sowie von Berichten zur Analyse langfristiger KPI-Trends. Sie können [die Ergebnisse einer Protokollabfrage in ein Power BI-Dataset importieren](visualize/powerbi.md) und dann von dessen Features profitieren, z. B. dem Kombinieren von Daten aus verschiedenen Quellen oder dem Freigeben von Berichten im Web und auf mobilen Geräten.

![Screenshot: Power BI-Beispielbericht für IT-Vorgänge.](media/visualizations/power-bi.png)

Power BI bietet u. a. folgende Vorteile:

- Umfassende Visualisierungen.
- Umfassende Interaktivität einschließlich Zoomen auf Details und Kreuzfilterung.
- Einfache Freigabe in Ihrer gesamten Organisation.
- Integration in andere Daten aus mehreren Datenquellen.
- Bessere Leistung mit Ergebnissen, die in einem Cube zwischengespeichert werden.

Es gelten einige Einschränkungen, u. a. folgende:

- Unterstützung für Protokolle, nicht aber für Metriken.
- Keine Azure-Integration. Dashboards und Modelle können nicht über Azure Resource Manager verwaltet werden.
- Abfrageergebnisse müssen zur Konfiguration in ein Power BI-Modell importiert werden. 
- Eingeschränkter Umfang und eingeschränkte Aktualisierungsmöglichkeiten für Ergebnisse.
- Eingeschränkte Datenaktualisierung (achtmal pro Tag).


## <a name="azure-monitor-partners"></a>Azure Monitor-Partner
Einige Azure Monitor-Partner bieten Visualisierungsfunktionen. Eine Liste der von Microsoft bewerteten Partner finden Sie unter [Azure Monitor – integrierte Partnerlösungen](./partners.md). 

Ein Azure Monitor-Partner kann sofort einsatzbereite Visualisierungen bereitstellen, um Ihnen Zeit zu sparen. 

Es gelten einige Einschränkungen, u. a. folgende:

- Es können zusätzliche Kosten anfallen.
- Das Untersuchen und Bewerten von Partnerangeboten nimmt etwas Zeit in Anspruch.

## <a name="your-own-custom-application"></a>Ihre eigene benutzerdefinierte Anwendung
Sie können über eine API mit einem beliebigen REST-Client auf Metrik- und Protokolldaten in Azure Monitor zugreifen. Anschließend können Sie eigene benutzerdefinierte Websites und Anwendungen erstellen.

Die Erstellung einer benutzerdefinierten Anwendung bietet u. a. folgende Vorteile:

- Vollständige Flexibilität bei Benutzeroberfläche, Visualisierung, Interaktivität und Funktionen.
- Die Möglichkeit, Metrik- und Protokolldaten mit anderen Datenquellen zu kombinieren.

Ein wesentlicher Nachteil ist allerdings der erforderliche Entwicklungsaufwand.

## <a name="azure-monitor-views"></a>Azure Monitor-Ansichten

> [!IMPORTANT]
> Ansichten werden zurzeit ausgesondert. Eine Anleitung zum Konvertieren von Ansichten in Arbeitsmappen finden Sie im [Handbuch für den Übergang](visualize/view-designer-conversion-overview.md).

Mit [Ansichten in Azure Monitor](visualize/view-designer.md) können Sie mithilfe von Protokolldaten benutzerdefinierte Visualisierungen erstellen. [Überwachungslösungen](insights/solutions.md) verwenden Ansichten, um die gesammelten Daten darzustellen.


![Screenshot: Kachel einer Containerüberwachungslösung und detaillierte Azure Monitor-Ansicht, die bei Auswahl der Kachel geöffnet wird.](media/visualizations/view.png)

Ansichten bieten u. a. folgende Vorteile:

- Umfangreiche Visualisierungen für die Protokolldaten.
- Die Möglichkeit zum Exportieren und Importieren von Ansichten, um sie in andere Ressourcengruppen und Abonnements zu übertragen.
- Integration in das Azure Monitor-Verwaltungsmodell mit Arbeitsbereichen und Überwachungslösungen.
- [Filtern](visualize/view-designer-filters.md) nach benutzerdefinierten Parametern.
- Interaktivität mit Unterstützung für eine Detailanzeige auf mehreren Ebenen (eine Ansicht, die einen Drilldown in eine andere Ansicht ausführt).

Es gelten einige Einschränkungen, u. a. folgende:

- Unterstützung für Protokolle, nicht aber für Metriken.
- Keine persönlichen Ansichten. Ansichten sind für alle Benutzer mit Zugriff auf den Arbeitsbereich verfügbar.
- Keine automatische Aktualisierung.
- Begrenzte Layoutoptionen.
- Keine Unterstützung von Abfragen über mehrere Arbeitsbereiche oder Application Insights-Anwendungen hinweg.
- Begrenzte Größe für Abfrageantworten (8 MB) und begrenzte Ausführungszeit von Abfragen (110 Sekunden).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [von Azure Monitor gesammelte Daten](data-platform.md).
- Weitere Informationen zum [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md).
- Erfahren Sie mehr über den [Metrik-Explorer](essentials/metrics-getting-started.md).
- Erfahren Sie mehr über [Arbeitsmappen](./visualize/workbooks-overview.md).
- Erfahren Sie mehr über das [Importieren von Protokolldaten in Power BI](./visualize/powerbi.md).
- Erfahren Sie mehr über das [Azure Monitor-Datenquellen-Plug-In für Grafana](./visualize/grafana-plugin.md).
- Erfahren Sie mehr über [Ansichten in Azure Monitor](visualize/view-designer.md).
