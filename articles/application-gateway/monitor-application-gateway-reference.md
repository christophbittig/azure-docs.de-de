---
title: Referenz zu den Überwachungsdaten von Azure Application Gateway
description: Wichtige Referenzmaterialien für die Überwachung von Application Gateway
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: application-gateway
ms.custom: subject-monitoring
ms.date: 06/10/2021
ms.openlocfilehash: 5ece5b220a59f562774fde7b093236ed5946f019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836236"
---
<!-- VERSION 2.2
Template for monitoring data reference article for Azure services. This article is support for the main "Monitoring [servicename]" article for the service. -->

# <a name="monitoring-azure-application-gateway-data-reference"></a>Referenz zu den Überwachungsdaten von Azure Application Gateway

Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für Azure Application Gateway finden Sie unter [Überwachen von Azure Application Gateway](monitor-application-gateway.md).

## <a name="metrics"></a>Metriken

<!-- REQUIRED if you support Metrics. If you don't, keep the section but call that out. Some services are only onboarded to logs.
<!-- Please keep headings in this order -->

<!--  OPTION 2 -  Link to the metrics as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the metrics-supported link. For highly customized example, see [CosmosDB](../cosmos-db/monitor-cosmos-db-reference.md#metrics). They even regroup the metrics into usage type vs. resource provider and type.
-->

<!-- Example format. Mimic the setup of metrics supported, but add extra information -->

### <a name="application-gateway-v2-metrics"></a>Application Gateway v2-Metriken

Ressourcenanbieter und -typ: [Microsoft.Compute/applicationGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkapplicationgateways)

#### <a name="timing-metrics"></a>Zeitmetriken
Application Gateway bietet mehrere integrierte Zeitsteuerungsmetriken, die sich auf die Anforderung und die Antwort beziehen und jeweils in Millisekunden gemessen werden.

> [!NOTE]
>
> Wenn Application Gateway mehr als einen Listener enthält, filtern Sie immer nach der Dimension *Listener*, und vergleichen Sie dabei verschiedene Latenzmetriken, um aussagekräftigere Rückschlüsse zu erhalten.


| Metrik | Einheit | BESCHREIBUNG|
|:-------|:-----|:------------|
|**Verbindungszeit für das Back-End**|Millisekunden|Zeitaufwand für das Herstellen einer Verbindung mit der Back-End-Anwendung.<br><br>Dies beinhaltet die Netzwerklatenz sowie die Zeit, die der TCP-Stapel des Back-End-Servers zur Herstellung neuer Verbindungen benötigt. Im Fall von TLS umfasst dies auch die mit dem Handshake verbrachte Zeit.|
|**Antwortzeit für erstes Byte des Back-Ends**|Millisekunden|Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antwortheaders.<br><br>Dies entspricht ungefähr der Summe aus der Back-End-Verbindungszeit, der von der Anforderung zum Erreichen des Back-Ends von Application Gateway benötigten Zeit, der Zeit, die die Back-End-Anwendung für die Antwort benötigt (Zeit für die Generierung der Inhalte durch den Server, ggf. Abrufen von Datenbankabfragen), und der Zeit, bis das erste Byte der Antwort von Application Gateway vom Back-End erreicht.|
|**Antwortzeit für letztes Byte des Back-Ends**|Millisekunden|Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antworttexts.<br><br>Dies entspricht etwa der Summe der Antwortzeit des ersten Bytes des Back-Ends und der Datenübertragungszeit. Diese Zahl kann je nach Größe der angeforderten Objekte und der Latenz des Servernetzwerks stark variieren.|
|**Application Gateway-Gesamtzeit**|Millisekunden|Durchschnittliche Zeit, bis eine Anforderung empfangen, verarbeitet und die zugehörige Antwort gesendet wurde.<br><br>Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem das letzte Byte der Antwort an den Client gesendet wurde. Dies schließt die Verarbeitungszeit durch Application Gateway, die Antwortzeit bis zum letzten Byte des Back-Ends, die Zeit, die Application Gateway zum Senden der Antwort benötigt, sowie die Client-RTT ein.|
|**Client-RTT**|Millisekunden|Durchschnittliche Roundtripzeit zwischen Clients und Application Gateway.|

Mithilfe dieser Metriken können Sie ermitteln, ob die beobachtete Verlangsamung auf das Clientnetzwerk, die Leistung von Application Gateway, das Back-End-Netzwerk und die TCP-Stapelauslastung des Back-End-Servers, die Leistung der Back-End-Anwendung oder eine große Datei zurückzuführen ist.

Wenn z. B. eine Spitze im Trend der *Back-End-Antwortzeit für das erste Byte* auftritt, aber der Trend der *Back-End-Verbindungszeit* stabil ist, können Sie daraus ableiten, dass es Latenzen zwischen Application Gateway und dem Back-End gibt und dass die Zeit zum Herstellen der Verbindung gleichbleibend ist. Damit wissen Sie, dass die Spitze durch einen Anstieg der Antwortzeit der Back-End-Anwendung verursacht wird. Wenn andererseits die Spitze bei der *Back-End-Antwortzeit für das erste Byte* mit einer entsprechenden Spitze bei der *Back-End-Verbindungszeit* zusammenhängt, können Sie daraus schließen, dass entweder das Netzwerk zwischen Application Gateway und dem Back-End-Server oder der TCP-Stapel des Back-End-Servers ausgelastet ist. 

Wenn Sie eine Spitze bei der *Back-End-Antwortzeit für das erste Byte* feststellen, aber die *Back-End-Antwortzeit für das erste Byte* gleichbleibend ist, kann die Spitze darauf zurückgeführt werden, dass eine größere Datei angefordert wird.

Entsprechend kann eine Spitze bei der *Application Gateway-Gesamtzeit* bei gleichbleibender *Back-End-Antwortzeit für das letzte Byte* ein Zeichen entweder für einen Leistungsengpass bei Application Gateway oder einen Engpass im Netzwerk zwischen Client und Application Gateway sein. Wenn außerdem die *Client-RTT* eine entsprechende Spitze aufweist, deutet dies darauf hin, dass das Problem auf das Netzwerk zwischen Client und Application Gateway zurückzuführen ist.

#### <a name="application-gateway-metrics"></a>Application Gateway-Metriken

| Metrik | Einheit | BESCHREIBUNG|
|:-------|:-----|:------------|
|**Empfangene Bytes**|Byte|Anzahl der von der Application Gateway-Instanz von den Clients empfangenen Bytes.|
|**Gesendete Bytes**|Byte|Anzahl der von der Application Gateway-Instanz an die Clients gesendeten Bytes.|
|**Client-TLS-Protokoll**|count|Anzahl von TLS- und Nicht-TLS-Anforderungen, die von dem Client initiiert wurden, der eine Verbindung mit Application Gateway hergestellt hat. Um die Verteilung des TLS-Protokolls anzuzeigen, filtern Sie nach der TLS-Protokolldimension.|
|**Aktuelle Kapazitätseinheiten**|count|Anzahl von Kapazitätseinheiten, die für den Lastenausgleich des Datenverkehrs genutzt werden. Die Kapazitätseinheit setzt sich aus drei Größen zusammen: Compute-Einheit, permanente Verbindungen und Durchsatz. Jede Kapazitätseinheit setzt sich maximal zusammen aus: einer Compute-Einheit oder 2.500 permanenten Verbindungen oder 2,22 MBit/s Durchsatz.|
|**Aktuelle Compute-Einheiten**|count|Menge der verbrauchten Prozessorkapazität. Faktoren, die sich auf die Compute-Einheit auswirken, sind TLS-Verbindungen/Sekunde, URL-Rewrite-Berechnungen und Verarbeitung von WAF-Regeln.|
|**Aktuelle Verbindungen**|count|Die Gesamtanzahl der gleichzeitig aktiven Verbindungen von Clients mit der Application Gateway-Instanz.|
|**Geschätzte abgerechnete Kapazitätseinheiten**|count|Bei der V2-SKU wird ein verbrauchsbasiertes Preismodell verwendet. Kapazitätseinheiten geben die verbrauchsbasierten Kosten an, die zusätzlich zu den Fixkosten berechnet werden. *Geschätzte abgerechnete Kapazitätseinheiten* geben die Anzahl von Kapazitätseinheiten an, für die die Abrechnung geschätzt wird. Hierzu wird der größere Wert zwischen *Aktuelle Kapazitätseinheiten* (Kapazitätseinheiten, die für den Lastenausgleich des Datenverkehrs erforderlich sind) und *Feste abrechenbare Kapazitätseinheiten* (Mindestanzahl von Kapazitätseinheiten, die bereitgestellt bleiben) berechnet.|
|**Anforderungsfehler**|count|Anzahl von Anforderungen, die von Application Gateway mit 5xx-Serverfehlercodes verarbeitet wurden. Dies umfasst die 5xx-Codes, die von Application Gateway generiert werden, sowie die 5xx-Codes, die vom Back-End generiert werden. Die Anzahl der Anforderungen kann weiter gefiltert werden, um die Anzahl für die einzelnen/spezifischen Kombinationen aus Back-End-Pools und HTTP-Einstellungen anzuzeigen.|
|**Feste abrechenbare Kapazitätseinheiten**|count|Die Mindestanzahl von Kapazitätseinheiten, die gemäß der Einstellung *Mindesteinheiten für Skalierung* (in der Application Gateway-Konfiguration) bereitgestellt bleiben. Eine Instanz entspricht hierbei zehn Kapazitätseinheiten.|
|**Neue Verbindungen pro Sekunde**|count|Die durchschnittliche Anzahl neuer TCP-Verbindungen pro Sekunde, die von Clients mit Application Gateway und von Application Gateway mit den Back-End-Mitgliedern hergestellt werden.|
|**Antwortstatus**|status code|Von Application Gateway zurückgegebener HTTP-Antwortstatus. Die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen.|
|**Durchsatz**|Byte/Sek.|Anzahl von Bytes pro Sekunde, die die Application Gateway-Instanz bereitgestellt hat.|
|**Anforderungen insgesamt**|count|Anzahl von erfolgreichen Anforderungen über Application Gateway. Die Anzahl der Anforderungen kann weiter gefiltert werden, um die Anzahl für die einzelnen/spezifischen Kombinationen aus Back-End-Pools und HTTP-Einstellungen anzuzeigen.|

#### <a name="backend-metrics"></a>Back-End-Metriken

| Metrik | Einheit | BESCHREIBUNG|
|:-------|:-----|:------------|
|**Back-End-Antwortstatus**|count|Anzahl der von den Back-Ends zurückgegebenen HTTP-Antwortstatuscodes. Dies schließt nicht die von Application Gateway generierten Antwortcodes ein. Die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen.|
|**Anzahl von fehlerfreien Hosts**|count|Die Anzahl der Back-Ends, die im Integritätstest als fehlerfrei ermittelt wurden. Sie können auf Back-End-Pool-Basis filtern, um die Anzahl fehlerfreier Hosts in einem bestimmten Back-End-Pool anzuzeigen.|
|**Anzahl von fehlerhaften Hosts**|count|Die Anzahl der Back-Ends, die im Integritätstest als fehlerhaft ermittelt wurden. Sie können auf Back-End-Pool-Basis filtern, um die Anzahl fehlerhafter Hosts in einem bestimmten Back-End-Pool anzuzeigen.|
|**Anforderungen pro Minute pro fehlerfreiem Host**|count|Die durchschnittliche Anzahl von Anforderungen, die von jedem fehlerfreien Mitglied in einem Back-End-Pool pro Minute empfangen werden. Der Back-End-Pool muss über die Dimension *BackendPool HttpSettings* angegeben werden.|


### <a name="application-gateway-v1-metrics"></a>Application Gateway v1-Metriken

#### <a name="application-gateway-metrics"></a>Application Gateway-Metriken

| Metrik | Einheit | BESCHREIBUNG|
|:-------|:-----|:------------|
|**CPU-Auslastung**|Prozent|Zeigt die Auslastung der CPUs an, die dem Application Gateway zugeordnet sind. Unter normalen Bedingungen sollte die CPU-Auslastung regelmäßig nicht 90 % überschreiten, da dies zu Wartezeiten bei den Websites führen kann, die hinter dem Application Gateway gehostet werden, sowie die Clienterfahrung merklich stören kann. Sie können die CPU-Auslastung indirekt steuern oder verbessern, indem Sie die Konfiguration des Application Gateway durch Erhöhen der Anzahl der Instanzen ändern oder durch Verschieben zu einer größeren SKU-Größe – oder durch beides.|
|**Aktuelle Verbindungen**|count|Anzahl der mit der Application Gateway-Instanz hergestellten aktuellen Verbindungen.|
|**Anforderungsfehler**|count|Die Anzahl von Anforderungen, für die aufgrund von Verbindungsproblemen ein Fehler aufgetreten ist. In dieser Anzahl sind auch Anforderungen enthalten, für die aufgrund einer Überschreitung der HTTP-Einstellung *Anforderungstimeout* ein Fehler aufgetreten ist, sowie Anforderungen, die aufgrund von Verbindungsproblemen zwischen der Application Gateway-Instanz und dem Back-End nicht erfolgreich waren. Diese Anzahl umfasst keine Fehler, die aufgetreten sind, weil kein fehlerfreies Back-End verfügbar war. Back-End-Antworten vom Typ „4xx“ und „5xx“ werden im Rahmen dieser Metrik ebenfalls nicht berücksichtigt.|
|**Antwortstatus**|status code|Von Application Gateway zurückgegebener HTTP-Antwortstatus. Die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen.|
|**Durchsatz**|Byte/Sek.|Anzahl von Bytes pro Sekunde, die die Application Gateway-Instanz bereitgestellt hat.|
|**Anforderungen insgesamt**|count|Anzahl von erfolgreichen Anforderungen über Application Gateway. Die Anzahl der Anforderungen kann weiter gefiltert werden, um die Anzahl für die einzelnen/spezifischen Kombinationen aus Back-End-Pools und HTTP-Einstellungen anzuzeigen.|
|**Anzahl der von Web Application Firewall blockierten Anforderungen**|count|Anzahl der von WAF blockierten Anforderungen.|
|**Verteilung der von Web Application Firewall blockierten Anforderungen**|count|Anzahl der von WAF blockierten Anforderungen, die gefiltert wurden, um die Anzahl pro jeweiliger/bestimmter WAF-Regelgruppe oder WAF-Regel-ID-Kombination zu zeigen.|
|**Gesamtregelverteilung in Web Application Firewall**|count|Anzahl der empfangenen Anforderungen pro jeweiliger spezifischer WAF-Regelgruppe oder WAF-Regel-ID-Kombination.|


<!-- Keep this text as-is -->
Weitere Informationen finden Sie in der Liste [aller in Azure Monitor unterstützten Plattformmetriken](../azure-monitor/essentials/metrics-supported.md).



## <a name="metric-dimensions"></a>Metrikdimensionen

<!-- REQUIRED. Please  keep headings in this order -->
<!-- If you have metrics with dimensions, outline it here. If you have no dimensions, say so.  Questions email azmondocs@microsoft.com -->

Weitere Informationen zu Metrikdimensionen finden Sie unter [Mehrdimensionale Metriken](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).


<!-- See https://docs.microsoft.com/azure/storage/common/monitor-storage-reference#metrics-dimensions for an example. Part is copied below. -->

Azure Application Gateway unterstützt Dimensionen für einige Metriken in Azure Monitor. Die jeweilige Metrik enthält eine Beschreibung, in der die speziell für diese Metrik verfügbaren Dimensionen erläutert werden.


## <a name="resource-logs"></a>Ressourcenprotokolle
<!-- REQUIRED. Please  keep headings in this order -->

In diesem Abschnitt werden die Typen der Ressourcenprotokolle aufgeführt, die für Azure Application Gateway erfasst werden können. 

<!-- List all the resource log types you can have and what they are for -->  

Eine Referenz finden Sie in der Liste [aller Typen von Ressourcenprotokollkategorien, die in Azure Monitor unterstützt werden](../azure-monitor/essentials/resource-logs-schema.md).

> [!NOTE]
> Das Leistungsprotokoll ist nur für die v1-SKU verfügbar. Verwenden Sie für die v2-SKU [Metriken](#metrics) für die Leistungsdaten.

Weitere Informationen finden Sie unter [Back-End-Integrität und Diagnoseprotokolle für Application Gateway](application-gateway-diagnostics.md#access-log).

<!--  OPTION 2 -  Link to the resource logs as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the resource-log-categories link. You can group these sections however you want provided you include the proper links back to resource-log-categories article. 
-->

<!-- Example format. Add extra information -->

### <a name="application-gateway"></a>Application Gateway

Ressourcenanbieter und -typ: [Microsoft.Network/applicationGateways](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkapplicationgateways)

| Kategorie | Anzeigename | Informationen|
|:---------|:-------------|------------------|
| **Activitylog**   | Aktivitätsprotokoll | Aktivitätsprotokolleinträge werden standardmäßig erfasst. Mit dem Feature [Azure-Aktivitätsprotokolle](../azure-monitor/essentials/activity-log.md) (ehemals Betriebs- und Überwachungsprotokolle) können Sie sämtliche an Ihr Azure-Abonnement übermittelten Vorgänge sowie deren Status anzeigen. |
|**ApplicationGatewayAccessLog**|Zugriffsprotokoll| Mithilfe dieses Protokolls können Sie Application Gateway-Zugriffsmuster anzeigen und wichtige Informationen analysieren. Dazu gehören die IP-Adresse des Aufrufers, die angeforderte URL, die Antwortlatenz, der Rückgabecode sowie die ein- und ausgehenden Bytes. Ein Zugriffsprotokoll wird alle 60 Sekunden erstellt. Dieses Protokoll enthält einen Datensatz pro Instanz von Application Gateway. Die Application Gateway-Instanz wird anhand der InstanceId-Eigenschaft identifiziert.|
| **ApplicationGatewayPerformanceLog**|Leistungsprotokoll|Mithilfe dieses Protokolls können Sie die Leistung von Application Gateway-Instanzen anzeigen. In diesem Protokoll werden Leistungsinformationen für jede Instanz erfasst, z.B. insgesamt bereitgestellte Anforderungen, Durchsatz in Byte, Anzahl von Anforderungen mit Fehlern und die Anzahl von fehlerfreien und fehlerhaften Back-End-Instanzen. Ein Leistungsprotokoll wird alle 60 Sekunden erstellt. Das Leistungsprotokoll ist nur für die v1-SKU verfügbar. Verwenden Sie für die v2-SKU [Metriken](#metrics) für die Leistungsdaten.|
|**ApplicationGatewayFirewallLog**|Firewallprotokoll|Mithilfe dieses Protokolls können Sie die Anforderungen anzeigen, die entweder über den Erkennungs- oder über den Schutzmodus eines Anwendungsgateways protokolliert werden, das mit der Web Application Firewall konfiguriert wurde. Firewallprotokolle werden alle 60 Sekunden erfasst.|


## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen
<!-- REQUIRED. Please keep heading in this order -->

Dieser Abschnitt bezieht sich auf alle Kusto-Tabellen in Azure Monitor-Protokollen, die für Azure Application Gateway relevant und für die Abfrage durch Log Analytics verfügbar sind. 


<!-- OPTION 1 - Minimum -  Link to relevant bookmarks in https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-resourcetype where your service tables are listed. These files are auto generated from the REST API.   If this article is missing tables that you and the PM know are available, both of you contact azmondocs@microsoft.com.  
-->

<!-- Example format. There should be AT LEAST one Resource Provider/Resource Type here. -->

|Ressourcentyp | Hinweise |
|-------|-----|
| [Application Gateway](/azure/azure-monitor/reference/tables/tables-resourcetype#application-gateways) |Enthält AzureActivity, AzureDiagnostics und AzureMetrics |


Eine Referenz zu allen Azure Monitor-Protokollen und Log Analytics-Tabellen finden Sie in der [Referenz zu Tabellen in Azure Monitor-Protokollen](/azure/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Diagnosetabellen
<!-- REQUIRED. Please keep heading in this order -->
<!-- If your service uses the AzureDiagnostics table in Azure Monitor Logs / Log Analytics, list what fields you use and what they are for. Azure Diagnostics is over 500 columns wide with all services using the fields that are consistent across Azure Monitor and then adding extra ones just for themselves.  If it uses service specific diagnostic table, refers to that table. If it uses both, put both types of information in. Most services in the future will have their own specific table. If you have questions, contact azmondocs@microsoft.com -->

Azure Application Gateway speichert Ressourcenprotokollinformationen in der [Azure-Diagnose](/azure/azure-monitor/reference/tables/azurediagnostics)-Tabelle. Die folgenden Spalten sind relevant.

**Azure-Diagnose**

| Eigenschaft | BESCHREIBUNG |
|:--- |:---|
requestUri_s | Der URI der Clientanforderung.|
`Message` | Informationsmeldungen wie „Angriff mit Einschleusung von SQL-Befehlen“|
userAgent_s | Benutzer-Agent-Details der Clientanforderung|
ruleName_s | Zum Bedienen dieser Anforderung verwendete Anforderungsroutingregel|
httpMethod_s | HTTP-Methode der Clientanforderung|
instanceId_s | Die Appgw-Instanz, an die die Clientanforderung zur Auswertung geroutet wird|
httpVersion_s | HTTP-Version der Clientanforderung|
clientIP_s | IP-Adresse, von der die Anforderung gestellt wird|
host_s | Hostheader der Clientanforderung|
requestQuery_s | Abfragezeichenfolge als Teil der Clientanforderung|
sslEnabled_s | Ob SSL für die Clientanforderung aktiviert ist|


## <a name="see-also"></a>Weitere Informationen

<!-- replace below with the proper link to your main monitoring service article -->
- Unter [Überwachen von Azure Application Gateway](monitor-application-gateway.md) finden Sie eine Beschreibung der Überwachung von Azure Application Gateway.
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).