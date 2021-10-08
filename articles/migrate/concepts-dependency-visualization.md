---
title: 'Abhängigkeitsanalyse in Azure Migrate: Ermittlung und Bewertung'
description: 'Hier wird beschrieben, wie Sie die Abhängigkeitsanalyse zur Bewertung mithilfe von Azure Migrate: Ermittlung und Bewertung verwenden.'
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 55e8efc7b49f14f6eb4cfe399866e53df29e3e36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767060"
---
# <a name="dependency-analysis"></a>Abhängigkeitsanalyse

In diesem Artikel wird die Abhängigkeitsanalyse in Azure Migrate: Ermittlung und Bewertung beschrieben.

Die Abhängigkeitsanalyse identifiziert Abhängigkeiten zwischen ermittelten lokalen Servern. Dies bietet folgende Vorteile:

- Sie können Server in Gruppen zusammenfassen, um sie genauer und mit größerer Sicherheit bewerten zu können.
- Sie können Server identifizieren, die gemeinsam migriert werden müssen. Dies ist besonders nützlich, wenn Sie nicht sicher sind, welche Server Teil einer App-Bereitstellung sind, die Sie zu Azure migrieren möchten.
- Sie können ermitteln, ob Server verwendet werden und welche Server außer Betrieb genommen werden können, anstatt sie zu migrieren.
- Die Analyse von Abhängigkeiten trägt dazu bei, dass nichts übersehen wird. Somit werden unerwartete Ausfälle nach der Migration vermieden.
- [Lesen Sie](common-questions-discovery-assessment.md#what-is-dependency-visualization) häufig gestellte Fragen zur Abhängigkeitsanalyse.

## <a name="analysis-types"></a>Analysetypen

Es gibt zwei Optionen für die Bereitstellung der Abhängigkeitsanalyse:

**Option** | **Details** | **Öffentliche Cloud** | **Azure Government**
----  |---- | ----
**Ohne Agent** | Fragt Daten von Servern auf VMware mit vSphere-APIs ab.<br/><br/> Sie müssen keine Agents auf Servern installieren.<br/><br/> Diese Option gilt derzeit nur für Server auf VMware. | Unterstützt. | Unterstützt.
**Agent-basierte Analyse** | Verwendet die [Dienstzuordnungslösung](../azure-monitor/vm/service-map.md) in Azure Monitor, um die Abhängigkeitsvisualisierung und Analyse zu ermöglichen.<br/><br/> Sie müssen auf jedem lokalen Server, den Sie analysieren möchten, Agents installieren. | Unterstützt | Wird nicht unterstützt.

## <a name="agentless-analysis"></a>Analyse ohne Agent

Bei der Abhängigkeitsanalyse ohne Agent werden TCP-Verbindungsdaten von den Servern erfasst, für die sie aktiviert wurde. Auf Servern sind keine Agents installiert. Verbindungen mit demselben Quellserver und -prozess sowie Zielserver, -prozess und -port werden logisch in einer Abhängigkeit gruppiert. Sie können erfasste Abhängigkeitsdaten in einer Kartenansicht visualisieren oder als CSV exportieren. Auf Servern, die Sie analysieren möchten, sind keine Agents installiert.

### <a name="dependency-data"></a>Abhängigkeitsdaten

Nach der Ermittlung der Abhängigkeitsdaten beginnt der Abruf:

- Die Azure Migrate-Appliance fragt alle fünf Minuten TCP-Verbindungsdaten von Servern ab, um Daten zu sammeln.
- Die Daten werden mithilfe von vSphere-APIs von Gast-Servern über vCenter Server gesammelt.
- Beim Abruf werden folgende Daten gesammelt:

    - Name von Prozessen mit aktiven Verbindungen
    - Name der Anwendung, die Prozesse mit aktiven Verbindungen ausführt
    - Zielport für die aktiven Verbindungen

- Die gesammelten Daten werden auf der Azure Migrate-Appliance verarbeitet, um Identitätsinformationen abzuleiten, und alle sechs Stunden an Azure Migrate gesendet.


## <a name="agent-based-analysis"></a>Agent-basierte Analyse

Bei der Agent-basierten Analyse wird die [Dienstzuordnung](../azure-monitor/vm/service-map.md)-Lösung in Azure Monitor von Azure Migrate: Ermittlung und Bewertung verwendet. Sie installieren den [Microsoft Monitoring Agent/Log Analytics-Agent](../azure-monitor/agents/agents-overview.md#log-analytics-agent) und den [Dependency-Agent](../azure-monitor/agents/agents-overview.md#dependency-agent) auf jedem Server, den Sie analysieren möchten.

### <a name="dependency-data"></a>Abhängigkeitsdaten

Die Agent-basierte Analyse stellt folgende Daten bereit:

- Name des Quellservers, Prozess, Anwendungsname.
- Name des Zielservers, Prozess, Anwendungsname und Port.
- Anzahl der Verbindungen, Wartezeiten und Datenübertragungsinformationen werden gesammelt und stehen für Log Analytics-Abfragen zur Verfügung.

## <a name="compare-agentless-and-agent-based"></a>Vergleich zwischen Visualisierung ohne Agent und Agent-basierter Visualisierung

Die Unterschiede zwischen der Visualisierung ohne Agent und der Agent-basierten Visualisierung sind in der Tabelle zusammengefasst.

**Anforderung** | **Ohne Agent** | **Agent-basiert**
--- | --- | ---
**Unterstützung** | Nur für Server in VMware verfügbar. [Lesen](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Sie den Artikel zu den unterstützten Betriebssystemen. | In der allgemeinen Verfügbarkeit (General Availability, GA).
**Agent** | Auf Servern, die Sie analysieren möchten, sind keine Agents erforderlich. | Auf jedem lokalen Server, den Sie analysieren möchten, sind Agents erforderlich.
**Log Analytics** | Nicht erforderlich. | Azure Migrate verwendet für die Abhängigkeitsanalyse die [Dienstzuordnungslösung](../azure-monitor/vm/service-map.md) in [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).<br/><br/> Sie ordnen einem Projekt einen Log Analytics-Arbeitsbereich zu. Der Arbeitsbereich muss sich in einer der Regionen „USA, Osten“, „Asien, Südosten“ oder „Europa, Westen“ befinden. Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Prozess** | Erfasst TCP-Verbindungsdaten. Nach der Ermittlung werden Daten in Abständen von fünf Minuten gesammelt. | Auf einem Server installierte Agents für die Dienstzuordnung sammeln Daten über TCP-Prozesse und ein-/ausgehende Verbindungen für die einzelnen Prozesse.
**Daten** | Name des Quellservers, Prozess, Anwendungsname.<br/><br/> Name des Zielservers, Prozess, Anwendungsname und Port. | Name des Quellservers, Prozess, Anwendungsname.<br/><br/> Name des Zielservers, Prozess, Anwendungsname und Port.<br/><br/> Anzahl der Verbindungen, Wartezeiten und Datenübertragungsinformationen werden gesammelt und stehen für Log Analytics-Abfragen zur Verfügung. 
**Visualisierung** | Das Abhängigkeitsdiagramm eines einzelnen Servers kann über eine Dauer von einer Stunde bis hin zu 30 Tagen angezeigt werden. | Abhängigkeitsdiagramm eines einzelnen Servers.<br/><br/> Abhängigkeitsdiagramm für eine Gruppe von Servern.<br/><br/>  Das Diagramm kann nur über eine Stunde angezeigt werden.<br/><br/> Hinzufügen und Entfernen von Servern in einer Gruppe aus der Diagrammansicht.
Datenexport | Die Daten der letzten 30 Tage können im CSV-Format heruntergeladen werden. | Daten können mit Log Analytics abgefragt werden.



## <a name="next-steps"></a>Nächste Schritte

- [Richten Sie](how-to-create-group-machine-dependencies.md) die Agent-basierte Visualisierung von Abhängigkeiten ein.
- [Probieren Sie](how-to-create-group-machine-dependencies-agentless.md) die Abhängigkeitsvisualisierung ohne Agent für Server auf VMware aus.
- Lesen Sie [häufige Fragen](common-questions-discovery-assessment.md#what-is-dependency-visualization) zur Abhängigkeitsvisualisierung.
