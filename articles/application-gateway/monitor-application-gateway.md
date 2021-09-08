---
title: Überwachen von Azure Application Gateway
description: Hier erfahren Sie, wie Sie Azure Application Gateway überwachen können.
author: vhorne
ms.author: victorh
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 7789590a2d13e6811e0ad8e7d2fa53cea9e0069a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295986"
---
<!-- VERSION 2.2
Template for the main monitoring article for Azure services. 
Keep the required sections and add/modify any content for any information specific to your service. 
This article should be in your TOC with the name *monitor-[Azure Application Gateway].md* and the TOC title "Monitor Azure Application Gateway". 
Put accompanying reference information into an article in the Reference section of your TOC with the name *monitor-[service-name]-reference.md* and the TOC title "Monitoring data". 
Keep the headings in this order. 
-->

# <a name="monitoring-azure-application-gateway"></a>Überwachen von Azure Application Gateway
<!-- REQUIRED. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Add to it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. 

In diesem Artikel werden die von Azure Application Gateway generierten Überwachungsdaten beschrieben. Azure Application Gateway verwendet [Azure Monitor](../azure-monitor/overview.md). Wenn Sie nicht mit den Features von Azure Monitor vertraut sind, die Bestandteil aller Azure-Dienste sind, für die Azure Monitor verwendet wird, lesen Sie den Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).


<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact robb@microsoft.com -->

## <a name="monitoring-overview-page-in-azure-portal"></a>Übersichtsseite zur Überwachung im Azure-Portal
<!-- OPTIONAL. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Edit it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Die Seite **Übersicht** im Azure-Portal für die jeweilige Application Gateway-Instanz enthält die folgenden Metriken:

- Summe der Anforderungen gesamt
- Summe fehlerhafter Anforderungen
- Summe der Antwortstatus nach HttpStatus
- Summe des Durchsatzes
- Summe von CurrentConnections
- Durchschnittliche Anzahl fehlerfreier Hosts nach BackendPool HttpSettings
- Durchschnittliche Anzahl fehlerhafter Hosts nach BackendPool HttpSettings

Dies ist nur eine Teilmenge der Metriken, die für Application Gateway verfügbar sind. Weitere Informationen finden Sie unter [Referenz zu den Überwachungsdaten von Azure Application Gateway](monitor-application-gateway-reference.md).


## <a name="azure-monitor-network-insights"></a>Azure Monitor Netzwerk-Insights

<!-- OPTIONAL SECTION.  Only include if your service has an "insight" associated with it. Examples of insights include
  - CosmosDB https://docs.microsoft.com/azure/azure-monitor/insights/cosmosdb-insights-overview
  - If you still aren't sure, contact azmondocs@microsoft.com.>
-->

Für einige Dienste in Azure enthält das Azure-Portal ein spezielles, vorgefertigtes Überwachungsdashboard, das als Ausgangspunkt für die Überwachung Ihres Diensts dient. Diese speziellen Dashboards werden als „insights“ bezeichnet.

<!-- Give a quick outline of what your "insight page" provides and refer to another article that gives details -->

Azure Monitor Network Insights bietet eine umfassende Übersicht der Integrität und Metriken für alle bereitgestellten Netzwerkressourcen (inklusive Application Gateway), ohne dass eine Konfiguration erforderlich ist. Weitere Informationen finden Sie unter [Azure Monitor-Netzwerkerkenntnisse](../azure-monitor/insights/network-insights-overview.md).

## <a name="monitoring-data"></a>Überwachungsdaten 

<!-- REQUIRED. Please keep headings in this order -->
Azure Application Gateway erfasst dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, die in [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) beschrieben werden. 

Ausführliche Informationen zu den Metriken und Protokollen, die von Azure Application Gateway erstellt werden, finden Sie in der [Referenz zu den Überwachungsdaten von Azure Application Gateway](monitor-application-gateway-reference.md).

<!-- If your service has additional non-Azure Monitor monitoring data then outline and refer to that here. Also include that information in the data reference as appropriate. -->

## <a name="collection-and-routing"></a>Sammlung und Routing

<!-- REQUIRED. Please keep headings in this order -->

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, sobald Sie eine Diagnoseeinstellung erstellt und an einen oder mehrere Standorte weitergeleitet haben.

<!-- Include any additional information on collecting logs.  The number of things that diagnostics settings control is expanding -->

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für Azure Application Gateway sind in [Ressourcenprotokolle](monitor-application-gateway-reference.md#resource-logs) aufgeführt.

<!-- OPTIONAL: Add specific examples of configuration for this service. For example, CLI and PowerShell commands for creating diagnostic setting. Ideally, customers should set up a policy to automatically turn on collection for services. Azure monitor has Resource Manager template examples you can point to. See https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings.  Contact azmondocs@microsoft.com if you have questions.   -->

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

<!-- REQUIRED. Please keep headings in this order 
If you don't support metrics, say so. Some services may be only onboarded to logs -->

Sie können Metriken für Azure Application Gateway mit Metriken aus anderen Azure-Diensten mithilfe des Metrik-Explorers analysieren, indem Sie im Menü **Azure Monitor** die Option **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

<!-- Point to the list of metrics available in your monitor-service-reference article. -->
Eine Liste der für Azure Application Gateway erfassten Plattformmetriken finden Sie unter [Metriken](monitor-application-gateway-reference.md#metrics).  


Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/essentials/metrics-supported.md) anzeigen.

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about how to use metrics explorer specifically for your service. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

## <a name="analyzing-logs"></a>Analysieren von Protokollen

<!-- REQUIRED. Please keep headings in this order
If you don't support resource logs, say so. Some services may be only onboarded to metrics and the activity log. -->

Daten in Azure Monitor-Protokollen werden in Tabellen gespeichert, wobei jede Tabelle ihren eigenen Satz an eindeutigen Eigenschaften hat.  

Alle Ressourcenprotokolle in Azure Monitor enthalten dieselben Felder, gefolgt von dienstspezifischen Feldern. Das allgemeine Schema wird in [Allgemeines und dienstspezifisches Schema für Azure-Ressourcenprotokolle](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema) skizziert. 

Das [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) ist ein Plattformprotokoll in Azure, das einen Einblick in Ereignisse auf Abonnementebene ermöglicht. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.  

Eine Liste der für Azure Application Gateway erfassten Arten von Ressourcenprotokollen finden Sie unter [Ressourcenprotokolle](monitor-application-gateway-reference.md#resource-logs).

Eine Liste der Tabellen, die von Azure Monitor-Protokollen verwendet und von Log Analytics abgefragt werden können, finden Sie unter [Tabellen in Azure Monitor-Protokollen](monitor-application-gateway-reference.md#azure-monitor-logs-tables).  

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about log usage or what logs are most important. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

<!-- REQUIRED if you support logs. Please keep headings in this order -->
<!-- Add sample Log Analytics Kusto queries for your service. -->

> [!IMPORTANT]
> Wenn Sie im Application Gateway-Menü die Option **Protokolle** auswählen, wird Log Analytics geöffnet, und der Abfragebereich ist auf die aktuelle Application Gateway-Instanz festgelegt. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Application Gateway-Instanzen oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/).

<!-- REQUIRED: Include queries that are helpful for figuring out the health and state of your service. Ideally, use some of these queries in the alerts section. It's possible that some of your queries may be in the Log Analytics UI (sample or example queries). Check if so.  -->

Sie können die folgenden Abfragen verwenden, um Ihre Application Gateway-Ressource zu überwachen. 

<!-- Put in a code section here. -->  
```Kusto
// Requests per hour 
// Count of the incoming requests on the Application Gateway. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart 
```

```kusto
// Failed requests per hour 
// Count of requests to which Application Gateway responded with an error. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart
```

```kusto
// Top 10 Client IPs 
// Count of requests per client IP. 
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by clientIP_s
| top 10 by AggregatedValue
```

```kusto
// Errors by user agent 
// Number of errors by user agent. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by userAgent_s, _ResourceId
| sort by AggregatedValue desc
```

## <a name="alerts"></a>Alerts

<!-- SUGGESTED: Include useful alerts on metrics, logs, log conditions or activity log. Ask your PMs if you don't know. 
This information is the BIGGEST request we get in Azure Monitor so do not avoid it long term. People don't know what to monitor for best results. Be prescriptive  
-->

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/alerts/alerts-metric-overview.md), [Protokolle](../azure-monitor/alerts/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben jeweils ihre Vor- und Nachteile.

<!-- only include next line if applications run on your service and work with App Insights. --> 

Wenn Sie eine Anwendung erstellen oder ausführen, die Application Gateway verwendet, bietet [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) möglicherweise zusätzliche Arten von Warnungen.
<!-- end -->

In den folgenden Tabellen sind allgemeine und empfohlene Warnungsregeln für Application Gateway aufgeführt.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->

**Application Gateway v1**

| Warnungstyp | Bedingung | BESCHREIBUNG  |
|:---|:---|:---|
|Metrik|CPU-Auslastung überschreitet 80 %|Unter normalen Bedingungen sollte die CPU-Auslastung regelmäßig nicht 90 % überschreiten, da dies zu Wartezeiten bei den Websites führen kann, die hinter dem Application Gateway gehostet werden, sowie die Clienterfahrung merklich stören kann.|
|Metrik|Anzahl von fehlerhaften Hosts überschreitet den Schwellenwert|Zeigt die Anzahl von Back-End-Servern an, die die Application Gateway-Instanz nicht erfolgreich testen kann. Dadurch werden Probleme abgefangen, bei denen Application Gateway-Instanzen keine Verbindung mit dem Back-End herstellen können. Eine Warnung sollte ausgegeben werden, wenn diese Anzahl mehr als 20 % der Back-End-Kapazität überschreitet.|
|Metrik|Antwortstatus (4xx, 5xx) überschreitet den Schwellenwert|Wenn der Application Gateway-Antwortstatus 4xx oder 5xx lautet. Aufgrund vorübergehender Probleme kann gelegentlich die Antwort 4xx oder 5xx angezeigt werden. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.|
|Metrik|Fehlerhafte Anforderungen überschreiten Schwellenwert|Wenn die Metrik für fehlerhafte Anforderungen einen Schwellenwert überschreitet. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.|


**Application Gateway v2**

| Warnungstyp | Bedingung | BESCHREIBUNG  |
|:---|:---|:---|
|Metrik|Auslastung der Compute-Einheiten überschreitet 75 % der durchschnittlichen Nutzung|Die Compute-Einheit ist das Maß für die Computeauslastung Ihrer Application Gateway-Instanz. Überprüfen Sie Ihre durchschnittliche Nutzung der Compute-Einheiten im letzten Monat, und legen Sie eine Warnung fest für den Fall, dass 75 % davon überschritten werden.|
|Metrik|Kapazitätseinheitenauslastung überschreitet 75 % der Spitzenauslastung|Kapazitätseinheiten stellen die Gesamtauslastung des Gateways in Bezug auf Durchsatz, Compute und Verbindungsanzahl dar. Überprüfen Sie Ihre maximale Kapazitätseinheitennutzung im letzten Monat, und legen Sie eine Warnung fest für den Fall, dass 75 % davon überschritten werden.|
|Metrik|Anzahl von fehlerhaften Hosts überschreitet den Schwellenwert|Zeigt die Anzahl von Back-End-Servern an, die die Application Gateway-Instanz nicht erfolgreich testen kann. Dadurch werden Probleme abgefangen, bei denen Application Gateway-Instanzen keine Verbindung mit dem Back-End herstellen können. Eine Warnung sollte ausgegeben werden, wenn diese Anzahl mehr als 20 % der Back-End-Kapazität überschreitet.|
|Metrik|Antwortstatus (4xx, 5xx) überschreitet den Schwellenwert|Wenn der Application Gateway-Antwortstatus 4xx oder 5xx lautet. Aufgrund vorübergehender Probleme kann gelegentlich die Antwort 4xx oder 5xx angezeigt werden. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.|
|Metrik|Fehlerhafte Anforderungen überschreiten Schwellenwert|Wenn die Metrik für fehlerhafte Anforderungen einen Schwellenwert überschreitet. Sie sollten das Gateway in der Produktion beobachten, um den statischen Schwellenwert zu ermitteln oder den dynamischen Schwellenwert für die Warnung zu verwenden.|
|Metrik|Antwortzeit des Back-Ends für das letzte Byte überschreitet den Schwellenwert|Gibt das Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antworttexts an. Eine Warnung sollte ausgegeben werden, wenn die Wartezeit für die Back-End-Antwort länger ist als üblich.|
|Metrik|Application Gateway-Gesamtzeit überschreitet den Schwellenwert|Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem das letzte Byte der Antwort an den Client gesendet wurde. Eine Warnung sollte ausgegeben werden, wenn die Wartezeit für die Back-End-Antwort den üblichen Wert überschreitet.|

## <a name="next-steps"></a>Nächste Schritte

<!-- Add additional links. You can change the wording of these and add more if useful.   -->

- Eine Referenz zu den Metriken, Protokollen und anderen wichtigen Werten, die von Application Gateway erstellt werden, finden Sie in der [Referenz zu den Überwachungsdaten von Azure Application Gateway](monitor-application-gateway-reference.md).

- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).