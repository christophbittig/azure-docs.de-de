---
title: Datei einfügen
description: include file
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f691f760c9abfed773db8602f878dfc7ba0c08ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520692"
---
### <a name="general-query-limits"></a>Allgemeine Abfragegrenzwerte

| Begrenzung | BESCHREIBUNG |
|:---|:---|
| Abfragesprache | Azure Monitor verwendet dieselbe [Kusto-Abfragesprache](/azure/kusto/query/) wie Azure Data Explorer. Weitere Informationen finden Sie unter [Azure Monitor – Unterschiede in der Protokollabfragesprache](/azure/data-explorer/kusto/query/) für KQL-Sprachelemente, die in Azure Monitor nicht unterstützt werden. |
| Azure-Regionen | Protokollabfragen können zu einem übermäßigen Aufwand führen, wenn sich die Daten über Log Analytics-Arbeitsbereiche in mehreren Azure-Regionen erstrecken. Weitere Informationen finden Sie unter [Abfragegrenzwerte](../articles/azure-monitor/logs/scope.md#query-scope-limits). |
| Ressourcenübergreifende Abfragen | Die maximale Anzahl von Application Insights-Ressourcen und Log Analytics-Arbeitsbereichen in einer einzelnen Abfrage ist auf 100 beschränkt.<br>Ressourcenübergreifende Abfrage wird im View Designer nicht unterstützt.<br>Eine ressourcenübergreifende Abfrage in Protokollwarnungen wird in der neuen scheduledQueryRules-API unterstützt.<br>Ausführliche Informationen finden Sie unter [Ressourcenübergreifende Abfragelimits](../articles/azure-monitor/logs/cross-workspace-query.md#cross-resource-query-limits). |

### <a name="user-query-throttling"></a>Drosselung von Benutzerabfragen
In Azure Monitor gelten mehrere Drosselungsgrenzwerte zum Schutz vor einer übermäßige Anzahl von Abfragen. Dieses Verhalten kann die System-Back-End-Ressourcen überlasten und die Reaktionsfähigkeit des Diensts gefährden. Die folgenden Grenzwerte sind darauf ausgelegt, Kunden vor Unterbrechungen zu schützen und eine konsistente Dienstebene zu gewährleisten. Die Drosselung und die Grenzwerte für Benutzer betreffen nur Szenarien mit übermäßigem Verbrauch und sind bei normaler Verwendung in der Regel nicht relevant.


| "Measure" | Grenzwert pro Benutzer | BESCHREIBUNG |
|:---|:---|:---|
| Gleichzeitige Abfragen | 5 | Ein Benutzer kann bis zu 5 Abfragen gleichzeitig ausführen. Jede zusätzliche Abfrage wird einer Warteschlange hinzugefügt. Sobald eine der laufenden Abfragen abgeschlossen ist, wird die erste wartende Abfrage aus der Warteschlange abgerufen und ausgeführt. Hinweis: Dieser Grenzwert gilt nicht für Warnungsabfragen.
| Zeit in der Parallelitätswarteschlange | 3 Minuten | Wenn sich eine Abfrage länger als drei Minuten in der Warteschlange befindet, ohne zu starten, wird sie mit einer HTTP-Fehlerantwort mit dem Code 429 beendet. |
| Gesamtanzahl von Abfragen in der Parallelitätswarteschlange | 200 | Sobald die Anzahl von Abfragen in der Warteschlange 200 erreicht hat, wird die nächste Abfrage mit dem HTTP-Fehlercode 429 abgelehnt. Diese Zahl gilt zusätzlich zu den 5 Abfragen, die gleichzeitig ausgeführt werden können. |
| Abfragerate | 200 Abfragen pro 30 Sekunden | Dies ist die Gesamtrate, mit der Abfragen von einem einzelnen Benutzer an alle Arbeitsbereiche übermittelt werden können. Dieser Grenzwert gilt für programmgesteuerte Abfragen und für Abfragen, die von Visualisierungskomponenten wie Azure-Dashboards und der Zusammenfassungsseite für den Log Analytics-Arbeitsbereich initiiert wurden. |

- Optimieren Sie Ihre Abfragen wie unter [Optimieren von Protokollabfragen in Azure Monitor](../articles/azure-monitor/logs/query-optimization.md) beschrieben.
- Dashboards und Arbeitsmappen können mehrere Abfragen in einer einzelnen Ansicht enthalten, die bei jedem Laden oder Aktualisieren einen Burst von Abfragen generieren. Zerlegen Sie sie ggf. in mehrere Ansichten, die bedarfsgesteuert geladen werden. 
- Erwägen Sie, in Power BI anstelle von unformatierten Protokollen nur aggregierte Ergebnisse zu extrahieren.
