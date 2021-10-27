---
title: 'Best Practices für Azure Monitor: Analyse und Visualisierungen'
description: Hier finden Sie Anleitungen und Empfehlungen zum Anpassen von Visualisierungen in Azure Monitor über die standardmäßigen Analysefeatures hinaus.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 3f4b671237c842c156e06281926be02ee3cdeb4a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181644"
---
# <a name="azure-monitor-best-practices---analyze-and-visualize-data"></a>Best Practices für Azure Monitor: Analysieren und Visualisieren von Daten
Dieser Artikel ist Teil des Szenarios [Empfehlungen zum Konfigurieren von Azure Monitor](best-practices.md). Er beschreibt in Azure Monitor integrierte Features zum Analysieren gesammelter Daten sowie Optionen zum Erstellen benutzerdefinierter Visualisierungen, um die Anforderungen verschiedener Benutzer in Ihrer Organisation zu erfüllen. Visualisierungen wie Diagramme und Grafiken können Ihnen die Analyse Ihrer Überwachungsdaten erleichtern, sodass Sie Detailinformationen zu Problemen anzeigen und Muster identifizieren können.


## <a name="builtin-analysis-features"></a>Integrierte Analysefeatures
In den folgenden Abschnitten werden Azure Monitor-Features beschrieben, die eine Analyse der gesammelten Daten ohne Konfiguration ermöglichen.
### <a name="overview-page"></a>Seite „Übersicht“
Die meisten Azure-Dienste verfügen im Azure-Portal über eine **Übersichtsseite**, die einen Abschnitt **Überwachen** mit aktuellen Diagrammen für wichtige Metriken enthält. In diesem Abschnitt können Besitzer einzelner Dienste schnell die Leistung einer Ressource bewerten. Da diese Seite auf Plattformmetriken basiert, die automatisch erfasst werden, ist für dieses Feature keine Konfiguration erforderlich.

### <a name="metrics-explorer"></a>Metrik-Explorer
Mit dem Metrik-Explorer können Benutzer interaktiv mit Metrikdaten arbeiten und Metrikwarnungen erstellen. Die meisten Benutzer können den Metrik-Explorer mit minimalem Schulungsaufwand verwenden, müssen jedoch mit den Metriken vertraut sein, die sie analysieren möchten. Sobald die Datensammlung konfiguriert wurde, ist für dieses Feature ist keine weitere Konfiguration erforderlich. Plattformmetriken für Azure-Ressourcen sind automatisch verfügbar. Gastmetriken für virtuelle Computer sind verfügbar, wenn ein Azure Monitor-Agent auf diesen bereitgestellt wurde, und Anwendungsmetriken sind verfügbar, wenn Application Insights konfiguriert wurde.


### <a name="log-analytics"></a>Log Analytics
Mit Log Analytics können Benutzer Protokollabfragen erstellen, um interaktiv mit Protokolldaten zu arbeiten und Protokollabfragewarnungen zu erstellen. Es ist ein gewisser Schulungsaufwand erforderlich, bis Benutzer mit der Abfragesprache vertraut sind; für allgemeine Anforderungen können aber auch vordefinierte Abfragen verwendet werden. Sie können auch [Abfragepakete](logs/query-packs.md) mit Abfragen hinzufügen, die für Ihre Organisation eindeutig sind. Damit können Benutzer, die mit der Abfragesprache vertraut sind, Abfragen für andere Personen in der Organisation erstellen.


## <a name="workbooks"></a>Arbeitsmappen
[Arbeitsmappen](./visualize/workbooks-overview.md) sind die bevorzugte Visualisierungsplattform für Azure und bieten eine flexible Canvas für die Datenanalyse und die Erstellung von informativen visuellen Berichten. Mit Arbeitsmappen können Sie mehrere Datenquellen in Azure nutzen und sie zu einheitlichen interaktiven Oberflächen kombinieren. Sie sind besonders nützlich, um E2E-Überwachungsansichten für mehrere Azure-Ressourcen zu erstellen.

Application Insights verwendet vordefinierte Arbeitsmappen, um Benutzern wichtige Integritäts- und Leistungsinformationen für einen bestimmten Dienst zu präsentieren. Sie können im Azure Monitor-Menü auf der Registerkarte **Arbeitsmappen** auf einen Katalog mit zusätzlichen Arbeitsmappen zugreifen, und Sie können benutzerdefinierte Arbeitsmappen erstellen, um die Anforderungen Ihrer verschiedenen Benutzer zu erfüllen.

![Diagramm: Screenshots von drei Seiten einer Arbeitsmappe – Analyse der Seitenaufrufe, Nutzung und die auf der Seite verbrachte Zeit.](media/visualizations/workbook.png)

Hier finden Sie einige gängige Szenarien für Arbeitsmappen:

- Sie können einen interaktiven Bericht mit Parametern erstellen, bei dem durch Auswahl eines Elements in einer Tabelle die zugeordneten Diagramme und Visualisierungen dynamisch aktualisiert werden.
- Sie können einen Bericht mit anderen Benutzern in Ihrer Organisation gemeinsam verwenden.
- Sie können mit anderen Arbeitsmappenautoren in Ihrer Organisation zusammenarbeiten, indem Sie einen GitHub-basierten öffentlichen Vorlagenkatalog verwenden.



## <a name="azure-dashboards"></a>Azure-Dashboards
[Azure-Dashboards](../azure-portal/azure-portal-dashboards.md) bieten eine zentrale Ansicht Ihrer Azure-Infrastruktur und -Dienste. Eine Arbeitsmappe bietet zwar einen größeren Funktionsumfang, aber ein Dashboard kann Azure Monitor-Daten mit Daten aus anderen Azure-Diensten kombinieren.

![Screenshot: Beispiel eines Azure-Dashboards mit anpassbaren Informationen.](media/visualizations/dashboard.png)

Hier finden Sie eine Videoanleitung zum Erstellen von Dashboards:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

Hier finden Sie einige gängige Szenarien für Dashboards:

- Sie können ein Dashboard erstellen, das ein Metrikdiagramm und die Ergebnisse einer Protokollabfrage mit operativen Daten für verwandte Dienste kombiniert.
- Durch Integration in die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/overview.md) können Sie ein Dashboard gemeinsam mit anderen Dienstbesitzern nutzen.
  

Ausführliche Informationen zum Erstellen eines Dashboards, das Daten aus Azure Monitor-Protokollen enthält, finden Sie unter [Erstellen und Freigeben von Dashboards von Log Analytics-Daten](visualize/tutorial-logs-dashboards.md). Ausführliche Informationen zum Erstellen eines Dashboards, das Daten aus Application Insights enthält, finden Sie unter [Erstellen benutzerdefinierter KPI-Dashboards mithilfe von Azure Application Insights](app/tutorial-app-dashboards.md). 



## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) ist hilfreich bei der Erstellung von Dashboards und Berichte für das Business sowie von Berichten zur Analyse langfristiger KPI-Trends. Sie können [die Ergebnisse einer Protokollabfrage in ein Power BI-Dataset importieren](visualize/powerbi.md) und dann von dessen Features profitieren, z. B. dem Kombinieren von Daten aus verschiedenen Quellen oder dem Freigeben von Berichten im Web und auf mobilen Geräten.

![Screenshot: Power BI-Beispielbericht für IT-Vorgänge.](media/visualizations/power-bi.png)

Hier finden Sie einige gängige Szenarien für Power BI:

- Umfassende Visualisierungen.
- Umfassende Interaktivität einschließlich Zoomen auf Details und Kreuzfilterung.
- Einfache Freigabe in Ihrer gesamten Organisation.
- Integration in andere Daten aus mehreren Datenquellen.
- Bessere Leistung mit Ergebnissen, die in einem Cube zwischengespeichert werden.



## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) ist eine offene Plattform, die für operationale Dashboards ideal ist. Sie ist nützlich für die Erkennung, Isolierung und Selektierung von operativen Incidents, indem Visualisierungen von Azure- und Nicht-Azure-Datenquellen kombiniert werden, einschließlich der lokalen Umgebung, Tools von Drittanbietern und Datenspeichern in anderen Clouds. Grafana bietet beliebte Plug-Ins und Dashboardvorlagen für APM-Tools wie Dynatrace, New Relic und App Dynamics, mit denen Benutzer Azure-Plattformdaten zusammen mit anderen Metriken aus einer höheren Ebene des Stapels visualisieren können, die von anderen Tools erfasst werden. Darüber bietet Grafana Plug-Ins für AWS CloudWatch und GCP BigQuery zur Multi-Cloud-Überwachung in einer einzigen zentralen Konsole.




Sie können Ihrem Azure-Abonnement das [Azure Monitor-Datenquellen-Plug-In für Grafana](visualize/grafana-plugin.md) hinzufügen, damit es Ihre Azure-Metrikdaten visualisiert.

![Screenshot: Grafana-Visualisierungen.](media/visualizations/grafana.png)


Hier finden Sie einige gängige Szenarien für Grafana:

- Sie können Zeitreihen- und Ereignisdaten in einem einzelnen Visualisierungspanel kombinieren.
- Sie können ein dynamisches Dashboard basierend auf der Benutzerauswahl dynamischer Variablen erstellen.
- Sie können aus einer von der Community erstellten und unterstützten Vorlage ein Dashboard erstellen.
- Sie können ein anbieterunabhängiges BCDR-Szenario erstellen, das über einen beliebigen Cloudanbieter oder lokal ausgeführt wird.

## <a name="azure-monitor-partners"></a>Azure Monitor-Partner
Einige Azure Monitor-Partner bieten Visualisierungsfunktionen. Eine Liste der von Microsoft bewerteten Partner finden Sie unter [Azure Monitor – integrierte Partnerlösungen](./partners.md). Möglicherweise bietet ein Azure Monitor-Partner Ihnen vorgefertigte, sofort einsatzbereite Visualisierungen, mit denen Sie Zeit sparen. Allerdings können für solche Lösungen zusätzliche Kosten anfallen.


## <a name="custom-application"></a>Benutzerdefinierte Anwendung
Anschließend können Sie anhand von Metrik- und Protokolldaten in Azure Monitor, auf die über eine REST-API zugegriffen wird, selbst benutzerdefinierte Websites und Anwendungen erstellen. So profitieren Sie von vollständiger Flexibilität in Bezug auf Benutzeroberfläche, Visualisierung, Interaktivität und Features.


## <a name="next-steps"></a>Nächste Schritte
- Unter [Warnungen und automatisierte Aktionen](best-practices-alerts.md) erfahren Sie, wie Sie Warnungen und automatisierte Aktionen von Azure Monitor definieren. 