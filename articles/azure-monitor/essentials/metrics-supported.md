---
title: Von Azure Monitor unterstützte Metriken nach Ressourcentyp
description: Liste der Metriken, die mit Azure Monitor für jeden Ressourcentyp zur Verfügung stehen.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 10/05/2021
ms.author: robb
ms.openlocfilehash: 06d7fb0569572bc688f81154a4e149032cf996ed
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252880"
---
# <a name="supported-metrics-with-azure-monitor"></a>Unterstützte Metriken von Azure Monitor

> [!NOTE]
> Diese Liste wird größtenteils automatisch generiert. Jegliche Änderungen, die über GitHub an dieser Liste vorgenommen werden, können ohne Warnung überschrieben werden. Informationen dazu, wie die Liste dauerhaft aktualisiert werden kann, erhalten Sie beim Autor dieses Artikels.

Azure Monitor bietet verschiedene Methoden für die Interaktion mit Metriken, z. B. die Diagrammdarstellung im Azure-Portal, den Zugriff über die REST-API oder die Abfrage über PowerShell oder die Azure CLI. 

Dieser Artikel ist eine vollständige Liste aller Plattformmetriken (d. h. der automatisch erfassten), die derzeit mit der konsolidierten Metrikpipeline in Azure Monitor zur Verfügung stehen. Nach dem am Anfang dieses Artikels angegebenen Datum geänderte oder hinzugefügte Metriken werden möglicherweise in der Liste nicht aufgeführt. Verwenden Sie die [API-Version 2018-01-01](/rest/api/monitor/metricdefinitions), um diese Metriken programmgesteuert abzufragen und auf die Liste zuzugreifen. Andere Metriken, die nicht in dieser Liste enthalten sind, stehen möglicherweise im Portal oder über Legacy-APIs zur Verfügung.

Die Metriken sind nach Ressourcenanbieter und Ressourcentyp geordnet. Eine Liste der Dienste und der Ressourcenanbieter und Ressourcentypen, die zu diesen gehören, finden Sie unter [Ressourcenanbieter für Azure-Dienste](../../azure-resource-manager/management/azure-services-resource-providers.md).  

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportieren von Plattformmetriken an andere Speicherorte

Sie können die Plattformmetriken auf eine von zwei Arten aus der Azure Monitor-Pipeline an andere Speicherorte exportieren:

- Sie können die [REST-API für Metriken](/rest/api/monitor/metrics/list) verwenden.
- Verwenden Sie [Diagnoseeinstellungen](../essentials/diagnostic-settings.md), um Plattformmetriken an folgende Dienste weiterzuleiten: 
    - Azure Storage.
    - Azure Monitor-Protokolle (und somit Log Analytics)
    - Event Hubs (zur Übertragung an nicht von Microsoft stammende Systeme) 

Die Verwendung von Diagnoseeinstellungen ist die einfachste Möglichkeit, um Metriken weiterzuleiten. Es gibt jedoch einige Einschränkungen: 

- **Exportierbarkeit:** Alle Metriken können über die REST-API exportiert werden. Einige können jedoch aufgrund von Feinheiten im Azure Monitor-Back-End nicht über Diagnoseeinstellungen exportiert werden. Die Spalte „Über Diagnoseeinstellungen exportierbar?“ in den folgenden Tabellen enthält die Metriken, die auf diese Weise exportiert werden können.  

- **Mehrdimensionale Metriken:** Das Senden mehrdimensionaler Metriken an andere Speicherorte über Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert. 

  Beispiel: Die Metrik *Eingehende Nachrichten* eines Event Hubs kann auf einer warteschlangenspezifischen Ebene untersucht und in einem Diagramm dargestellt werden. Wenn die Metrik allerdings über die Diagnoseeinstellungen exportiert wird, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.

## <a name="guest-os-and-host-os-metrics"></a>Metriken für Gast- und Hostbetriebssysteme

Metriken für das Gastbetriebssystem (Gast-BS), das in Azure Virtual Machines, Service Fabric und Cloud Services ausgeführt wird, werden hier *nicht* aufgeführt. Die Metriken für das Gastbetriebssystem müssen stattdessen über einen oder mehrere Agents erfasst werden, die auf dem oder als Teil des Gastbetriebssystems ausgeführt werden. Zu den Metriken des Gastbetriebssystems gehören Leistungszähler, die den Gast-CPU-Prozentsatz oder die Speichernutzung verfolgen, die beide häufig für die automatische Skalierung oder Alarmierung verwendet werden. 

Für Hostbetriebssysteme *sind* Metriken verfügbar. Diese werden in den Tabellen aufgeführt. Die Hostbetriebssystem-Metriken beziehen sich auf die Hyper-V-Sitzung, die die Gastbetriebssystem-Sitzung hosten. 

> [!TIP]
> Eine bewährte Methode besteht darin, den Azure Monitor-Agent zu verwenden und so zu konfigurieren, dass er Leistungsmetriken für das Gastbetriebssystem an dieselbe Azure Monitor-Metrikdatenbank sendet, in der auch Plattformmetriken gespeichert werden. Der Agent leitet Gastbetriebssystemmetriken über die API für [benutzerdefinierte Metriken](../essentials/metrics-custom-overview.md) weiter. Sie können dann Gastbetriebssystemmetriken als Diagramm darstellen, mit Warnungen verknüpfen und anderweitig wie Plattformmetriken verwenden. 
>
> Alternativ oder zusätzlich dazu können Sie die Gastbetriebssystemmetriken mit demselben Agent an Azure Monitor-Protokolle senden. Dort können Sie diese Metriken in Kombination mit anderen Daten mithilfe von Log Analytics abfragen. 

Der Azure Monitor-Agent ersetzt die Azure-Diagnoseerweiterung und den Log Analytics-Agent, die zuvor für das Routing des Gastbetriebssystems verwendet wurden. Weitere wichtige Informationen finden Sie unter [Übersicht über Azure Monitor-Agents](../agents/agents-overview.md).

## <a name="table-formatting"></a>Tabellenformatierung

Mit dem aktuellen Update wird eine neue Spalte hinzugefügt, und die Metriken werden alphabetisch sortiert. Das bedeutet, dass je nach Größe Ihres Browserfensters am unteren Rand ggf. eine horizontale Scrollleiste bei den Tabellen vorhanden ist. Wenn Sie bestimmte Informationen nicht finden könnten, zeigen Sie mithilfe der Scrollleiste die gesamte Tabelle an.

## <a name="microsoftaadiamazureadmetrics"></a>microsoft.aadiam/azureADMetrics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ThrottledRequests|Nein|ThrottledRequests|Anzahl|Average|Metrik des Typs azureADMetrics|Keine Dimensionen|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Ja|Memory: Bereinigung – aktueller Preis|Anzahl|Average|Aktueller Preis des Arbeitsspeichers, $/Byte/Zeit, normalisiert auf 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Ja|Memory: Bereinigung – nicht verkleinerbarer Arbeitsspeicher|Byte|Average|Die Menge des Arbeitsspeichers in Byte, die nicht durch den Hintergrundbereinigungsprozess bereinigt wird.|ServerResourceType|
|CleanerMemoryShrinkable|Ja|Memory: Bereinigung – verkleinerbarer Arbeitsspeicher|Byte|Average|Die Menge des Arbeitsspeichers in Byte, die durch den Hintergrundbereinigungsprozess bereinigt wird.|ServerResourceType|
|CommandPoolBusyThreads|Ja|Threads: Ausgelastete Threads im Befehlspool|Anzahl|Average|Anzahl ausgelasteter Threads im Befehlsthreadpool.|ServerResourceType|
|CommandPoolIdleThreads|Ja|Threads: Leerlaufthreads im Befehlspool|Anzahl|Average|Anzahl von Leerlaufthreads im Befehlsthreadpool.|ServerResourceType|
|CommandPoolJobQueueLength|Ja|Warteschlangenlänge für Aufträge im Befehlspool|Anzahl|Average|Gibt die Anzahl von Aufträgen in der Warteschlange des Befehlsthreadpools an.|ServerResourceType|
|CurrentConnections|Ja|Verbindung: Aktuelle Verbindungen|Anzahl|Average|Aktuelle Anzahl hergestellter Clientverbindungen.|ServerResourceType|
|CurrentUserSessions|Ja|Aktuelle Benutzersitzungen|Anzahl|Average|Aktuelle Anzahl von eingerichteten Benutzersitzungen|ServerResourceType|
|LongParsingBusyThreads|Ja|Threads: Ausgelastete lange Analysethreads|Anzahl|Average|Anzahl ausgelasteter Threads im Pool für lange Analysethreads.|ServerResourceType|
|LongParsingIdleThreads|Ja|Threads: Lange Analysethreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads im Pool für lange Analysethreads.|ServerResourceType|
|LongParsingJobQueueLength|Ja|Threads: Warteschlangenlänge für lange Analyseaufträge|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Pools für lange Analysethreads.|ServerResourceType|
|mashup_engine_memory_metric|Ja|M-Engine – Arbeitsspeicher|Byte|Average|Arbeitsspeichernutzung durch Mashup-Engine-Prozesse|ServerResourceType|
|mashup_engine_private_bytes_metric|Ja|Private Bytes für M-Engine|Byte|Average|Nutzung privater Bytes durch Prozesse der Mashup-Engine.|ServerResourceType|
|mashup_engine_qpu_metric|Ja|M-Engine – QPU|Anzahl|Average|QPU-Nutzung durch Mashup-Engine-Prozesse|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Ja|Virtuelle Bytes für M-Engine|Byte|Average|Nutzung virtueller Bytes durch Prozesse der Mashup-Engine.|ServerResourceType|
|memory_metric|Ja|Arbeitsspeicher|Byte|Average|Arbeitsspeicher. Bereich: 0–25 GB für S1, 0–50 GB für S2 und 0–100 GB für S4|ServerResourceType|
|memory_thrashing_metric|Ja|Arbeitsspeicherüberlastung|Percent|Average|Durchschnittliche Arbeitsspeicherüberlastung.|ServerResourceType|
|MemoryLimitHard|Ja|Memory: Grenzwert für den festen Speicher|Byte|Average|Grenzwert für den festen Speicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitHigh|Ja|Memory: Obere Arbeitsspeichergrenze|Byte|Average|Oberer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitLow|Ja|Memory: Untere Arbeitsspeichergrenze|Byte|Average|Unterer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitVertiPaq|Ja|Memory: VertiPaq-Arbeitsspeichergrenze|Byte|Average|In-Memory-Grenzwert gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryUsage|Ja|Memory: Speicherauslastung|Byte|Average|Speicherauslastung des Serverprozesses, wie bei der Berechnung des Arbeitsspeicherpreises für die Bereinigung verwendet. Entspricht dem Indikator "Process\PrivateBytes" zuzüglich der Größe der im Speicher abgebildeten Daten. Von der xVelocity-Engine für Datenanalyse im Arbeitsspeicher (VertiPaq) abgebildeter oder belegter Arbeitsspeicher, der über die xVelocity-Arbeitsspeichergrenze hinausgeht, wird dabei ignoriert.|ServerResourceType|
|private_bytes_metric|Ja|Private Bytes|Byte|Average|Private Bytes.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Ja|Threads: Ausgelastete Threads für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von Threads, die E/A-Aufträge im Verarbeitungsthreadpool ausführen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Ja|Threads: Ausgelastete Nicht-E/A-Threads im Verarbeitungspool|Anzahl|Average|Anzahl von Threads, die Nicht-E/A-Aufträge im Verarbeitungsthreadpool ausführen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Ja|Threads: Leerlaufthreads für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Ja|Threads: Nicht-E/A-Leerlaufthreads im Verarbeitungspool|Anzahl|Average|Anzahl von Leerlaufthreads im Verarbeitungsthreadpool, die für Nicht-E/A-Aufträge vorgesehen sind.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Ja|Threads: Warteschlangenlänge für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools.|ServerResourceType|
|ProcessingPoolJobQueueLength|Ja|Warteschlangenlänge für Verarbeitungspoolaufträge|Anzahl|Average|Anzahl von Nicht-E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools|ServerResourceType|
|qpu_metric|Ja|QPU|Anzahl|Average|QPU. Bereich: 0–100 für S1, 0–200 für S2 und 0–400 für S4|ServerResourceType|
|QueryPoolBusyThreads|Ja|Ausgelastete Abfragepoolthreads|Anzahl|Average|Anzahl von ausgelasteten Threads im Abfragethreadpool|ServerResourceType|
|QueryPoolIdleThreads|Ja|Threads: Abfragepoolthreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|ServerResourceType|
|QueryPoolJobQueueLength|Ja|Threads: Auftragswarteschlangenlänge für Abfragepool|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools.|ServerResourceType|
|Kontingent|Ja|Memory: Kontingent|Byte|Average|Aktuelles Arbeitsspeicherkontingent in Byte. Das Arbeitsspeicherkontingent wird auch als Speicherzuweisung oder Speicherreservierung bezeichnet.|ServerResourceType|
|QuotaBlocked|Ja|Memory: Kontingent blockiert|Anzahl|Average|Aktuelle Anzahl von Kontingentanforderungen, die blockiert werden, bis andere Arbeitsspeicherkontingente freigegeben werden.|ServerResourceType|
|RowsConvertedPerSec|Ja|Verarbeitung: Konvertierte Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der Zeilen, die bei der Verarbeitung konvertiert werden.|ServerResourceType|
|RowsReadPerSec|Ja|Verarbeitung: Gelesene Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der aus allen relationalen Datenbanken gelesenen Zeilen.|ServerResourceType|
|RowsWrittenPerSec|Ja|Verarbeitung: Geschriebene Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der Zeilen, die bei der Verarbeitung geschrieben werden.|ServerResourceType|
|ShortParsingBusyThreads|Ja|Threads: Ausgelastete kurze Analysethreads|Anzahl|Average|Anzahl ausgelasteter Threads im Pool für kurze Analysethreads.|ServerResourceType|
|ShortParsingIdleThreads|Ja|Threads: Kurze Analysethreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads im Pool für kurze Analysethreads.|ServerResourceType|
|ShortParsingJobQueueLength|Ja|Threads: Warteschlangenlänge für kurze Analyseaufträge|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Pools für kurze Analysethreads.|ServerResourceType|
|SuccessfullConnectionsPerSec|Ja|Erfolgreiche Verbindungen pro Sekunde|Anzahl pro Sekunde|Average|Rate der erfolgreichen Verbindungsabschlüsse|ServerResourceType|
|TotalConnectionFailures|Ja|Verbindungsfehler gesamt|Anzahl|Average|Gesamtanzahl von fehlerhaften Verbindungsversuchen|ServerResourceType|
|TotalConnectionRequests|Ja|Total Connection Requests (Verbindungsanforderungen gesamt)|Anzahl|Average|Gesamtanzahl von Verbindungsanforderungen. Dies sind erhaltene Anforderungen.|ServerResourceType|
|VertiPaqNonpaged|Ja|Memory: Nicht ausgelagerte VertiPaq-Daten|Byte|Average|Bytes von Arbeitsspeicher, die im Arbeitssatz zur Verwendung durch die In-Memory-Engine gesperrt sind.|ServerResourceType|
|VertiPaqPaged|Ja|Memory: Ausgelagerte VertiPaq-Daten|Byte|Average|Bytes von ausgelagertem Arbeitsspeicher, die für In-Memory-Daten verwendet werden.|ServerResourceType|
|virtual_bytes_metric|Ja|Virtuelle Bytes|Byte|Average|Virtuelle Bytes.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BackendDuration|Ja|Dauer von Back-End-Anforderungen|Millisekunden|Average|Dauer von Back-End-Anforderungen in Millisekunden|Speicherort, Hostname|
|Capacity|Ja|Capacity|Percent|Average|Auslastungsmetrik für ApiManagement-Dienst. Hinweis: Für andere SKUs als Premium gibt die Aggregation „Max“ den Wert als 0 an.|Standort|
|ConnectionAttempts|Ja|WebSocket-Verbindungsversuche (Vorschau)|Anzahl|Gesamt|Anzahl der WebSocket-Verbindungsversuche basierend auf ausgewählter Quelle und ausgewähltem Ziel|Standort, Quelle, Ziel, Zustand|
|Duration|Ja|Gesamtdauer von Gatewayanforderungen|Millisekunden|Average|Gesamtdauer von Gatewayanforderungen in Millisekunden|Speicherort, Hostname|
|EventHubDroppedEvents|Ja|Gelöschte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl übersprungener Ereignisse aufgrund Erreichen der maximalen Warteschlangengröße|Standort|
|EventHubRejectedEvents|Ja|Abgelehnte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl abgelehnter EventHub-Ereignisse (falsche Konfiguration oder nicht autorisiert)|Standort|
|EventHubSuccessfulEvents|Ja|Erfolgreiche EventHub-Ereignisse|Anzahl|Gesamt|Anzahl erfolgreicher EventHub-Ereignisse|Standort|
|EventHubThrottledEvents|Ja|Gedrosselte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl gedrosselter EventHub-Ereignisse|Standort|
|EventHubTimedoutEvents|Ja|EventHub-Ereignisse mit Zeitüberschreitung|Anzahl|Gesamt|Anzahl von EventHub-Ereignissen mit Zeitüberschreitung|Standort|
|EventHubTotalBytesSent|Ja|Größe von EventHub-Ereignissen|Byte|Gesamt|Gesamtgröße von EventHub-Ereignissen in Byte|Standort|
|EventHubTotalEvents|Ja|Gesamtzahl von EventHub-Ereignissen|Anzahl|Gesamt|Anzahl von an EventHub gesendeten Ereignissen|Standort|
|EventHubTotalFailedEvents|Ja|Fehlerhafte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl fehlerhafter EventHub-Ereignisse|Standort|
|FailedRequests|Ja|Fehlerhafte Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von Fehlern bei Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|NetworkConnectivity|Ja|Netzwerkkonnektivitätsstatus von Ressourcen (Vorschau)|Anzahl|Average|Netzwerkkonnektivitätsstatus abhängiger Ressourcentypen des API Management-Diensts|Location, ResourceType|
|OtherRequests|Ja|Andere Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von anderen Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|Requests|Ja|Requests|Anzahl|Gesamt|Gatewayanforderungsmetriken mit mehreren Dimensionen|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Ja|Erfolgreiche Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von erfolgreichen Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|TotalRequests|Ja|Gatewayanforderungen insgesamt (veraltet)|Anzahl|Gesamt|Anzahl von Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|UnauthorizedRequests|Ja|Nicht autorisierte Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von nicht autorisierten Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|WebSocketMessages|Ja|WebSocket-Meldungen (Vorschau)|Anzahl|Gesamt|Anzahl der WebSocket-Meldungen basierend auf ausgewählter Quelle und ausgewähltem Ziel|Standort, Quelle, Ziel|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Ja|HttpIncomingRequestCount|Anzahl|Anzahl|Gesamtanzahl eingehender HTTP-Anforderungen|StatusCode, Authentication|
|HttpIncomingRequestDuration|Ja|HttpIncomingRequestDuration|Anzahl|Average|Latenz bei einer HTTP-Anforderung|StatusCode, Authentication|
|ThrottledHttpRequestCount|Ja|ThrottledHttpRequestCount|Anzahl|Anzahl|Gedrosselte HTTP-Anforderungen|Keine Dimensionen|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active-timer-count|Ja|active-timer-count|Anzahl|Average|Anzahl der derzeit aktiven Timer|Deployment, AppName, Pod|
|alloc-rate|Ja|alloc-rate|Byte|Average|Anzahl der im verwalteten Heap zugeordneten Bytes|Deployment, AppName, Pod|
|AppCpuUsage|Ja|App-CPU-Auslastung |Percent|Average|Die aktuelle CPU-Auslastung für die App|Deployment, AppName, Pod|
|assembly-count|Ja|assembly-count|Anzahl|Average|Anzahl der geladenen Assemblys|Deployment, AppName, Pod|
|cpu-usage|Ja|cpu-usage|Percent|Average|% Zeit, zu der der Prozess die CPU verwendet hat|Deployment, AppName, Pod|
|current-requests|Ja|current-requests|Anzahl|Average|Gesamtanzahl der Verarbeitungsanforderungen während der Lebensdauer des Prozesses|Deployment, AppName, Pod|
|exception-count|Ja|exception-count|Anzahl|Gesamt|Anzahl von Ausnahmen|Deployment, AppName, Pod|
|failed-requests|Ja|failed-requests|Anzahl|Average|Gesamtanzahl fehlgeschlagener Anforderungen während der Lebensdauer des Prozesses|Deployment, AppName, Pod|
|gc-heap-size|Ja|gc-heap-size|Anzahl|Average|Gesamte von der GC gemeldete Heapgröße (MB)|Deployment, AppName, Pod|
|gen-0-gc-count|Ja|gen-0-gc-count|Anzahl|Average|Anzahl von Gen: 0 GCS|Deployment, AppName, Pod|
|gen-0-size|Ja|gen-0-size|Byte|Average|Heapgröße: Gen 0|Deployment, AppName, Pod|
|gen-1-gc-count|Ja|gen-1-gc-count|Anzahl|Average|Anzahl von Gen: 1 GCs|Deployment, AppName, Pod|
|gen-1-size|Ja|gen-1-size|Byte|Average|Heapgröße: Gen 1|Deployment, AppName, Pod|
|gen-2-gc-count|Ja|gen-2-gc-count|Anzahl|Average|Anzahl von Gen: 2 GCs|Deployment, AppName, Pod|
|gen-2-size|Ja|gen-2-size|Byte|Average|Heapgröße: Gen 2|Deployment, AppName, Pod|
|IngressBytesReceived|Ja|Empfangene Bytes|Byte|Average|Anzahl der Bytes, die von Azure Spring Cloud von den Clients empfangen wurden|Hostname, HttpStatus|
|IngressBytesReceivedRate|Ja|Durchsatz eingehend (Bytes/Sek.)|Bytes pro Sekunde|Average|Anzahl der Bytes, die von Azure Spring Cloud von den Clients empfangen wurden|Hostname, HttpStatus|
|IngressBytesSent|Ja|Gesendete Bytes|Byte|Average|Anzahl der Bytes, die von Azure Spring Cloud an Clients gesendet wurden|Hostname, HttpStatus|
|IngressBytesSentRate|Ja|Durchsatz ausgehend (Bytes/Sek.)|Bytes pro Sekunde|Average|Anzahl der Bytes, die von Azure Spring Cloud pro Sekunde an Clients gesendet wurden|Hostname, HttpStatus|
|IngressFailedRequests|Ja|Anforderungsfehler|Anzahl|Average|Anzahl der fehlgeschlagenen Anforderungen von Azure Spring Cloud von den Clients|Hostname, HttpStatus|
|IngressRequests|Ja|Requests|Anzahl|Average|Anzahl der Anforderungen von Azure Spring Cloud von den Clients|Hostname, HttpStatus|
|IngressResponseStatus|Ja|Antwortstatus|Anzahl|Average|Der von Azure Spring Cloud zurückgegebene HTTP-Antwortstatus. die Antwortstatuscode-Verteilung kann weiter kategorisiert werden, um Antworten in 2xx-, 3xx-, 4xx- und 5xx-Kategorien anzuzeigen|Hostname, HttpStatus|
|IngressResponseTime|Ja|Antwortzeit|Sekunden|Average|Rückgabe der HTTP-Antwortzeit durch Azure Spring Cloud|Hostname, HttpStatus|
|jvm.gc.live.data.size|Ja|jvm.gc.live.data.size|Byte|Average|Größe des Arbeitsspeicherpools der alten Generation nach einer vollständigen Garbage Collection|Deployment, AppName, Pod|
|jvm.gc.max.data.size|Ja|jvm.gc.max.data.size|Byte|Average|Maximale Größe des Arbeitsspeicherpools der alten Generation|Deployment, AppName, Pod|
|jvm.gc.memory.allocated|Ja|jvm.gc.memory.allocated|Byte|Maximum|Inkrementiert zur Erhöhung der Größe des Speicherpools der neuen Generation nach einer Garbage Collection gegenüber dem Zeitpunkt vor der nächsten|Deployment, AppName, Pod|
|jvm.gc.memory.promoted|Ja|jvm.gc.memory.promoted|Byte|Maximum|Anzahl der positiven Erhöhungen der Größe des Speicherpools der alten Generation vor der Garbage Collection gegenüber nach der GC|Deployment, AppName, Pod|
|jvm.gc.pause.total.count|Ja|jvm.gc.pause.total.count|Anzahl|Gesamt|Anzahl der GC-Pausen|Deployment, AppName, Pod|
|jvm.gc.pause.total.time|Ja|jvm.gc.pause.total.time|Millisekunden|Gesamt|Gesamtzeit der GC-Pausen|Deployment, AppName, Pod|
|jvm.memory.committed|Ja|jvm.memory.committed|Byte|Average|JVM zugewiesener Arbeitsspeicher in Byte|Deployment, AppName, Pod|
|jvm.memory.max|Ja|jvm.memory.max|Byte|Maximum|Die maximale Speichermenge (in Byte), die für die Arbeitsspeicherverwaltung verwendet werden kann|Deployment, AppName, Pod|
|jvm.memory.used|Ja|jvm.memory.used|Byte|Average|Verwendeter App-Arbeitsspeicher in Byte|Deployment, AppName, Pod|
|loh-size|Ja|loh-size|Byte|Average|Heapgröße: LOH|Deployment, AppName, Pod|
|monitor-lock-contention-count|Ja|monitor-lock-contention-count|Anzahl|Average|Häufigkeit, mit der beim Versuch, die Überwachungssperre aufzuheben, Konflikte aufgetreten sind|Deployment, AppName, Pod|
|PodCpuUsage|Ja|App-CPU-Auslastung|Percent|Average|Die aktuelle CPU-Auslastung für die App|Deployment, AppName, Pod|
|PodMemoryUsage|Ja|App-Speicherauslastung|Percent|Average|Die aktuelle Speicherauslastung für die App|Deployment, AppName, Pod|
|process.cpu.usage|Ja|process.cpu.usage|Percent|Average|Die aktuelle CPU-Auslastung für den JVM-Prozess|Deployment, AppName, Pod|
|requests-per-second|Ja|requests-rate|Anzahl|Average|Anforderungsrate|Deployment, AppName, Pod|
|system.cpu.usage|Ja|system.cpu.usage|Percent|Average|Die aktuelle CPU-Auslastung für das gesamte System|Deployment, AppName, Pod|
|threadpool-completed-items-count|Ja|threadpool-completed-items-count|Anzahl|Average|Anzahl abgeschlossene Arbeitselemente des Threadpools|Deployment, AppName, Pod|
|threadpool-queue-length|Ja|threadpool-queue-length|Anzahl|Average|Warteschlangenlänge der Arbeitselemente im Threadpool|Deployment, AppName, Pod|
|threadpool-thread-count|Ja|threadpool-thread-count|Anzahl|Average|Anzahl der Threads im Threadpool|Deployment, AppName, Pod|
|time-in-gc|Ja|time-in-gc|Percent|Average|% Zeit in GC seit letzter GC|Deployment, AppName, Pod|
|tomcat.global.error|Ja|tomcat.global.error|Anzahl|Gesamt|Globale Tomcat-Fehler|Deployment, AppName, Pod|
|tomcat.global.received|Ja|tomcat.global.received|Byte|Gesamt|Insgesamt empfangene Bytes für Tomcat|Deployment, AppName, Pod|
|tomcat.global.request.avg.time|Ja|tomcat.global.request.avg.time|Millisekunden|Average|Durchschnittliche Zeit für Tomcat-Anforderungen|Deployment, AppName, Pod|
|tomcat.global.request.max|Ja|tomcat.global.request.max|Millisekunden|Maximum|Maximale Zeit für Tomcat-Anforderung|Deployment, AppName, Pod|
|tomcat.global.request.total.count|Ja|tomcat.global.request.total.count|Anzahl|Gesamt|Gesamtanzahl von Tomcat-Anforderungen|Deployment, AppName, Pod|
|tomcat.global.request.total.time|Ja|tomcat.global.request.total.time|Millisekunden|Gesamt|Tomcat Request Total Time (Gesamtzeit für Tomcat-Anforderungen)|Deployment, AppName, Pod|
|tomcat.global.sent|Ja|tomcat.global.sent|Byte|Gesamt|Insgesamt gesendete Bytes für Tomcat|Deployment, AppName, Pod|
|tomcat.sessions.active.current|Ja|tomcat.sessions.active.current|Anzahl|Gesamt|Tomcat Session Active Count (Maximale Anzahl aktiver Tomcat-Sitzungen)|Deployment, AppName, Pod|
|tomcat.sessions.active.max|Ja|tomcat.sessions.active.max|Anzahl|Gesamt|Maximale Anzahl aktiver Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.alive.max|Ja|tomcat.sessions.alive.max|Millisekunden|Maximum|Maximale aktive Zeit für Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.created|Ja|tomcat.sessions.created|Anzahl|Gesamt|Anzahl erstellter Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.expired|Ja|tomcat.sessions.expired|Anzahl|Gesamt|Anzahl abgelaufener Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.rejected|Ja|tomcat.sessions.rejected|Anzahl|Gesamt|Anzahl abgewiesener Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.threads.config.max|Ja|tomcat.threads.config.max|Anzahl|Gesamt|Tomcat Config Max Thread Count (Maximale Threadanzahl der Tomcat-Konfiguration)|Deployment, AppName, Pod|
|tomcat.threads.current|Ja|tomcat.threads.current|Anzahl|Gesamt|Tomcat Current Thread Count (Aktuelle Threadanzahl in Tomcat)|Deployment, AppName, Pod|
|total-requests|Ja|total-requests|Anzahl|Average|Gesamtanzahl der Anforderungen während der Lebensdauer des Prozesses|Deployment, AppName, Pod|
|working-set|Ja|working-set|Anzahl|Average|Vom Prozess verwendete Arbeitssatzmenge (MB)|Deployment, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|TotalJob|Ja|Gesamtzahl an Einzelvorgängen (Jobs)|Anzahl|Gesamt|Die Gesamtzahl an Einzelvorgängen (Jobs)|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Ja|Gesamtzahl von Updatebereitstellungsausführungen auf dem Computer|Anzahl|Gesamt|Gesamtzahl von Updatebereitstellungsausführungen für Software auf dem Computer in einer Updatebereitstellungsausführung für Software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Ja|Gesamtzahl von Updatebereitstellungsausführungen|Anzahl|Gesamt|Gesamtzahl von Updatebereitstellungsausführungen für Software|SoftwareUpdateConfigurationName, Status|


## <a name="microsoftavsprivateclouds"></a>microsoft.avs/privateClouds

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CapacityLatest|Ja|Gesamtkapazität des Datenträgers mit dem Datenspeicher|Byte|Average|Die Gesamtkapazität des Datenträgers im Datenspeicher|dsname|
|DiskUsedPercentage|Ja| Prozentsatz des verwendeten Datenträgers mit dem Datenspeicher|Percent|Average|Prozentsatz des verfügbaren Datenträgers im Datenspeicher|dsname|
|EffectiveCpuAverage|Ja|CPU in Prozent|Percent|Average|Prozentsatz der verwendeten CPU-Ressourcen im Cluster|clustername|
|EffectiveMemAverage|Ja|Durchschnittlicher effektiver Arbeitsspeicher|Byte|Average|Gesamtmenge des verfügbaren Computerspeichers im Cluster|clustername|
|OverheadAverage|Ja|Durchschnittlicher Overhead des Arbeitsspeichers|Byte|Average|Von der Virtualisierungsinfrastruktur genutzter physischer Arbeitsspeicher des Hosts|clustername|
|TotalMbAverage|Ja|Durchschnittlicher gesamter Arbeitsspeicher|Byte|Average|Gesamt Arbeitsspeicher im Cluster|clustername|
|UsageAverage|Ja|Durchschnittliche Arbeitsspeicherauslastung|Percent|Average|Speichernutzung als Prozentsatz des gesamten konfigurierten oder verfügbaren Arbeitsspeichers|clustername|
|UsedLatest|Ja|Verwendeter Datenträger mit dem Datenspeicher|Byte|Average|Die Gesamtmenge der im Datenspeicher verwendeten Datenträger|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CoreCount|Nein|Dedizierte Anzahl von Kernen|Anzahl|Gesamt|Gesamtzahl der dedizierten Kerne im Batch-Konto|Keine Dimensionen|
|CreatingNodeCount|Nein|Anzahl erstellter Knoten|Anzahl|Gesamt|Anzahl von Knoten, die gerade erstellt werden|Keine Dimensionen|
|IdleNodeCount|Nein|Anzahl von Knoten im Leerlauf|Anzahl|Gesamt|Anzahl von Knoten im Leerlauf|Keine Dimensionen|
|JobDeleteCompleteEvent|Ja|Ereignisse zum Abschluss des Löschvorgangs von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich gelöschten Aufträge|jobId|
|JobDeleteStartEvent|Ja|Ereignisse zum Starten des Löschvorgangs von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der zum Löschen angeforderten Aufträge|jobId|
|JobDisableCompleteEvent|Ja|Ereignisse zum Abschluss der Deaktivierung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich deaktivierten Aufträge|jobId|
|JobDisableStartEvent|Ja|Ereignisse zum Starten der Deaktivierung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der zum Deaktivieren angeforderten Aufträge|jobId|
|JobStartEvent|Ja|Ereignisse zum Starten von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich gestarteten Aufträge|jobId|
|JobTerminateCompleteEvent|Ja|Ereignisse zum Abschluss der Beendigung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich beendeten Aufträge|jobId|
|JobTerminateStartEvent|Ja|Ereignisse zum Starten der Beendigung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der zum Beenden angeforderten Aufträge|jobId|
|LeavingPoolNodeCount|Nein|Anzahl von Knoten, die den Pool verlassen|Anzahl|Gesamt|Anzahl von Knoten, die den Pool verlassen|Keine Dimensionen|
|LowPriorityCoreCount|Nein|Anzahl von LowPriority-Kernen|Anzahl|Gesamt|Gesamtzahl der Kerne mit niedriger Priorität im Batch-Konto|Keine Dimensionen|
|OfflineNodeCount|Nein|Anzahl von Offlineknoten|Anzahl|Gesamt|Anzahl offline geschalteter Knoten|Keine Dimensionen|
|PoolCreateEvent|Ja|Poolerstellungsereignisse|Anzahl|Gesamt|Gesamtanzahl erstellter Pools|poolId|
|PoolDeleteCompleteEvent|Ja|Ereignisse zum Abschluss des Löschvorgangs von Pools|Anzahl|Gesamt|Gesamtanzahl abgeschlossener Poollöschvorgänge|poolId|
|PoolDeleteStartEvent|Ja|Ereignisse zum Starten des Löschvorgangs von Pools|Anzahl|Gesamt|Gesamtanzahl gestarteter Poollöschvorgänge|poolId|
|PoolResizeCompleteEvent|Ja|Ereignisse zum Abschluss der Größenänderung von Pools|Anzahl|Gesamt|Gesamtanzahl abgeschlossener Größenänderungen von Pools|poolId|
|PoolResizeStartEvent|Ja|Ereignisse zum Start der Größenänderung von Pools|Anzahl|Gesamt|Gesamtanzahl gestarteter Größenänderungen von Pools|poolId|
|PreemptedNodeCount|Nein|Anzahl der vorzeitig entfernten Knoten|Anzahl|Gesamt|Die Anzahl der vorzeitig entfernten Knoten|Keine Dimensionen|
|RebootingNodeCount|Nein|Anzahl neu gestarteter Knoten|Anzahl|Gesamt|Anzahl von Knoten, die neu gestartet werden|Keine Dimensionen|
|ReimagingNodeCount|Nein|Anzahl von Knoten mit Reimaging|Anzahl|Gesamt|Anzahl von Knoten, für die ein Reimaging durchgeführt wird|Keine Dimensionen|
|RunningNodeCount|Nein|Anzahl ausgeführter Knoten|Anzahl|Gesamt|Anzahl ausgeführter Knoten|Keine Dimensionen|
|StartingNodeCount|Nein|Anzahl gestarteter Knoten|Anzahl|Gesamt|Anzahl von Knoten, die gerade gestartet werden|Keine Dimensionen|
|StartTaskFailedNodeCount|Nein|Anzahl von Knoten mit Starttaskfehlern|Anzahl|Gesamt|Anzahl von Knoten, bei denen der Starttask nicht durchgeführt werden konnte|Keine Dimensionen|
|TaskCompleteEvent|Ja|Taskabschlussereignisse|Anzahl|Gesamt|Gesamtanzahl abgeschlossener Tasks|poolId, jobId|
|TaskFailEvent|Ja|Taskfehlerereignisse|Anzahl|Gesamt|Gesamtanzahl von Tasks, die mit Fehlerstatus abgeschlossen wurden|poolId, jobId|
|TaskStartEvent|Ja|Taskstartereignisse|Anzahl|Gesamt|Gesamtanzahl gestarteter Tasks|poolId, jobId|
|TotalLowPriorityNodeCount|Nein|Anzahl der Knoten mit niedriger Priorität|Anzahl|Gesamt|Gesamtzahl der Knoten mit niedriger Priorität im Batchkonto|Keine Dimensionen|
|TotalNodeCount|Nein|Dedizierte Knotenanzahl|Anzahl|Gesamt|Gesamtzahl der dedizierten Knoten im Batch-Konto|Keine Dimensionen|
|UnusableNodeCount|Nein|Anzahl nicht verwendbarer Knoten|Anzahl|Gesamt|Anzahl nicht verwendbarer Knoten|Keine Dimensionen|
|WaitingForStartTaskNodeCount|Nein|Anzahl von Knoten, die auf den Starttask warten|Anzahl|Gesamt|Anzahl von Knoten, die auf den Abschluss des Starttasks warten|Keine Dimensionen|

## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Aktive Kerne|Ja|Aktive Kerne|Anzahl|Average|Gesamtanzahl der aktiven Kerne|Scenario, ClusterName|
|Aktive Knoten|Ja|Aktive Knoten|Anzahl|Average|Anzahl ausgeführter Knoten|Scenario, ClusterName|
|Kerne im Leerlauf|Ja|Kerne im Leerlauf|Anzahl|Average|Gesamtanzahl der Kerne im Leerlauf|Scenario, ClusterName|
|Knoten im Leerlauf|Ja|Knoten im Leerlauf|Anzahl|Average|Anzahl von Knoten im Leerlauf|Scenario, ClusterName|
|Auftrag abgeschlossen|Ja|Auftrag abgeschlossen|Anzahl|Gesamt|Anzahl der abgeschlossenen Aufträge|Scenario, ClusterName, ResultType|
|Auftrag übermittelt|Ja|Auftrag übermittelt|Anzahl|Gesamt|Anzahl der übermittelten Aufträge|Scenario, ClusterName|
|Ausscheidende Kerne|Ja|Ausscheidende Kerne|Anzahl|Average|Anzahl der ausscheidenden Kerne|Scenario, ClusterName|
|Ausscheidende Knoten|Ja|Ausscheidende Knoten|Anzahl|Average|Anzahl der ausscheidenden Knoten|Scenario, ClusterName|
|Vorzeitig entfernte Kerne|Ja|Vorzeitig entfernte Kerne|Anzahl|Average|Anzahl der vorzeitig entfernten Kerne|Scenario, ClusterName|
|Vorzeitig entfernte Knoten|Ja|Vorzeitig entfernte Knoten|Anzahl|Average|Die Anzahl der vorzeitig entfernten Knoten|Scenario, ClusterName|
|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Ja|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Anzahl|Average|Prozentualer Anteil der genutzten Kontingente|Scenario, ClusterName, VmFamilyName, VmPriority|
|Kerne insgesamt|Ja|Kerne insgesamt|Anzahl|Average|Gesamtanzahl von Kernen|Scenario, ClusterName|
|Knoten insgesamt|Ja|Knoten insgesamt|Anzahl|Average|Gesamtanzahl von Knoten|Scenario, ClusterName|
|Nicht verwendbare Kerne|Ja|Nicht verwendbare Kerne|Anzahl|Average|Anzahl der nicht verwendbaren Kerne|Scenario, ClusterName|
|Unusable Nodes (Nicht verwendbare Knoten)|Ja|Unusable Nodes (Nicht verwendbare Knoten)|Anzahl|Average|Anzahl nicht verwendbarer Knoten|Scenario, ClusterName|

## <a name="microsoftbingaccounts"></a>microsoft.bing/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BlockedCalls|Ja|Blockierte Aufrufe|Anzahl|Gesamt|Anzahl von Aufrufen, bei denen das Raten- oder Kontingentlimit überschritten wurde|ApiName, ServingRegion, StatusCode|
|ClientErrors|Ja|Clientfehler|Anzahl|Gesamt|Anzahl von Aufrufen mit Clientfehler (HTTP-Statuscode 4xx)|ApiName, ServingRegion, StatusCode|
|DataIn|Ja|Eingehende Daten|Byte|Gesamt|Eingehende Anforderung: Inhaltslänge in Bytes|ApiName, ServingRegion, StatusCode|
|DataOut|Ja|Datenausgabe|Byte|Gesamt|Ausgehende Antwort: Inhaltslänge in Bytes|ApiName, ServingRegion, StatusCode|
|Latency|Ja|Latency|Millisekunden|Average|Wartezeit in Millisekunden|ApiName, ServingRegion, StatusCode|
|ServerErrors|Ja|Serverfehler|Anzahl|Gesamt|Anzahl von Aufrufen mit Serverfehler (HTTP-Statuscode 5xx)|ApiName, ServingRegion, StatusCode|
|SuccessfulCalls|Ja|Erfolgreiche Aufrufe|Anzahl|Gesamt|Anzahl erfolgreicher Aufrufe (HTTP-Statuscode 2xx)|ApiName, ServingRegion, StatusCode|
|TotalCalls|Ja|Aufrufe gesamt|Anzahl|Gesamt|Gesamtanzahl von Aufrufen|ApiName, ServingRegion, StatusCode|
|TotalErrors|Ja|Fehler insgesamt|Anzahl|Gesamt|Anzahl von Aufrufen mit Fehler (HTTP-Statuscode 4xx oder 5xx)|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Ja|BroadcastProcessedCountDisplayName|Anzahl|Average|Die Anzahl der verarbeiteten Transaktionen|Node, channel, type, status|
|ChaincodeExecuteTimeouts|Ja|ChaincodeExecuteTimeoutsDisplayName|Anzahl|Average|Die Anzahl der Ausführungen der Chaincode (Initialisierung oder Aufruf) mit Timeout.|Knoten, Chaincode|
|ChaincodeLaunchFailures|Ja|ChaincodeLaunchFailuresDisplayName|Anzahl|Average|Die Anzahl fehlgeschlagener Starts von Chaincode.|Knoten, Chaincode|
|ChaincodeLaunchTimeouts|Ja|ChaincodeLaunchTimeoutsDisplayName|Anzahl|Average|Die Anzahl von Starts von Chaincode mit Timeout.|Knoten, Chaincode|
|ChaincodeShimRequestsCompleted|Ja|ChaincodeShimRequestsCompletedDisplayName|Anzahl|Average|Die Anzahl abgeschlossener Shim-Anforderungen von Chaincode.|Knoten, Typ, Kanal, Chaincode, Erfolg|
|ChaincodeShimRequestsReceived|Ja|ChaincodeShimRequestsReceivedDisplayName|Anzahl|Average|Die Anzahl empfangener Shim-Anforderungen von Chaincode.|Knoten, Typ, Kanal, Chaincode|
|ClusterCommEgressQueueCapacity|Ja|ClusterCommEgressQueueCapacityDisplayName|Anzahl|Average|Kapazität der ausgehenden Warteschlange.|Knoten, Host, MSG_TYPE, Kanal|
|ClusterCommEgressQueueLength|Ja|ClusterCommEgressQueueLengthDisplayName|Anzahl|Average|Länge der ausgehenden Warteschlange.|Knoten, Host, MSG_TYPE, Kanal|
|ClusterCommEgressQueueWorkers|Ja|ClusterCommEgressQueueWorkersDisplayName|Anzahl|Average|Anzahl der ausgehenden Warteschlangenworker.|Node, channel|
|ClusterCommEgressStreamCount|Ja|ClusterCommEgressStreamCountDisplayName|Anzahl|Average|Anzahl der Datenströme zu anderen Knoten.|Node, channel|
|ClusterCommEgressTlsConnectionCount|Ja|ClusterCommEgressTlsConnectionCountDisplayName|Anzahl|Average|Anzahl von TLS-Verbindungen mit anderen Knoten.|Knoten|
|ClusterCommIngressStreamCount|Ja|ClusterCommIngressStreamCountDisplayName|Anzahl|Average|Anzahl der Datenströme von anderen Knoten.|Knoten|
|ClusterCommMsgDroppedCount|Ja|ClusterCommMsgDroppedCountDisplayName|Anzahl|Average|Anzahl der gelöschten Nachrichten.|Knoten, Host, Kanal|
|ConnectionAccepted|Ja|Akzeptierte Verbindungen|Anzahl|Gesamt|Akzeptierte Verbindungen|Node|
|ConnectionActive|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Node|
|ConnectionHandled|Ja|Behandelte Verbindungen|Anzahl|Gesamt|Behandelte Verbindungen|Node|
|ConsensusEtcdraftActiveNodes|Ja|ConsensusEtcdraftActiveNodesDisplayName|Anzahl|Average|Anzahl aktiver Knoten in diesem Kanal.|Node, channel|
|ConsensusEtcdraftClusterSize|Ja|ConsensusEtcdraftClusterSizeDisplayName|Anzahl|Average|Anzahl der Knoten in diesem Kanal.|Node, channel|
|ConsensusEtcdraftCommittedBlockNumber|Ja|ConsensusEtcdraftCommittedBlockNumberDisplayName|Anzahl|Average|Die Blocknummer des letzten committeten Blocks|Node, channel|
|ConsensusEtcdraftConfigProposalsReceived|Ja|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Anzahl|Average|Die Gesamtanzahl der empfangenen Vorschläge für Transaktionen des Typs „Konfiguration“.|Node, channel|
|ConsensusEtcdraftIsLeader|Ja|ConsensusEtcdraftIsLeaderDisplayName|Anzahl|Average|Der Führungsstatus des aktuellen Knotens: Bei führender Position 1, andernfalls 0.|Node, channel|
|ConsensusEtcdraftLeaderChanges|Ja|ConsensusEtcdraftLeaderChangesDisplayName|Anzahl|Average|Die Anzahl der Führungswechsel seit Prozessbeginn.|Node, channel|
|ConsensusEtcdraftNormalProposalsReceived|Ja|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Anzahl|Average|Die Gesamtanzahl der empfangenen Vorschläge für Transaktionen des normalen Typs.|Node, channel|
|ConsensusEtcdraftProposalFailures|Ja|ConsensusEtcdraftProposalFailuresDisplayName|Anzahl|Average|Die Anzahl der Vorschlagsfehler.|Node, channel|
|ConsensusEtcdraftSnapshotBlockNumber|Ja|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Anzahl|Average|Die Blocknummer der letzten Momentaufnahme.|Node, channel|
|ConsensusKafkaBatchSize|Ja|ConsensusKafkaBatchSizeDisplayName|Anzahl|Average|Die durchschnittliche Batchgröße in Bytes, die an Themen gesendet werden.|Knoten, Thema|
|ConsensusKafkaCompressionRatio|Ja|ConsensusKafkaCompressionRatioDisplayName|Anzahl|Average|Das mittlere Komprimierungsverhältnis (als Prozentsatz) für Themen.|Knoten, Thema|
|ConsensusKafkaIncomingByteRate|Ja|ConsensusKafkaIncomingByteRateDisplayName|Anzahl|Average|Von Brokern pro Sekunde gelesene Bytes.|Knoten, broker_id|
|ConsensusKafkaLastOffsetPersisted|Ja|ConsensusKafkaLastOffsetPersistedDisplayName|Anzahl|Average|Der in den Blockmetadaten angegebene Offset des letzten Blocks mit Commit.|Node, channel|
|ConsensusKafkaOutgoingByteRate|Ja|ConsensusKafkaOutgoingByteRateDisplayName|Anzahl|Average|Bytes, die pro Sekunde in Broker geschrieben wurden.|Knoten, broker_id|
|ConsensusKafkaRecordSendRate|Ja|ConsensusKafkaRecordSendRateDisplayName|Anzahl|Average|Die Anzahl der Datensätze, die pro Sekunde an Themen gesendet werden.|Knoten, Thema|
|ConsensusKafkaRecordsPerRequest|Ja|ConsensusKafkaRecordsPerRequestDisplayName|Anzahl|Average|Die durchschnittliche Anzahl von Datensätzen, die pro Anforderung an Themen gesendet werden.|Knoten, Thema|
|ConsensusKafkaRequestLatency|Ja|ConsensusKafkaRequestLatencyDisplayName|Anzahl|Average|Die durchschnittliche Anforderungswartezeit in ms für Broker.|Knoten, broker_id|
|ConsensusKafkaRequestRate|Ja|ConsensusKafkaRequestRateDisplayName|Anzahl|Average|Anforderungen, die pro Sekunde an Broker gesendet werden.|Knoten, broker_id|
|ConsensusKafkaRequestSize|Ja|ConsensusKafkaRequestSizeDisplayName|Anzahl|Average|Die mittlere Größe der Anforderung in Bytes an Broker.|Knoten, broker_id|
|ConsensusKafkaResponseRate|Ja|ConsensusKafkaResponseRateDisplayName|Anzahl|Average|Anforderungen, die pro Sekunde an Broker gesendet werden.|Knoten, broker_id|
|ConsensusKafkaResponseSize|Ja|ConsensusKafkaResponseSizeDisplayName|Anzahl|Average|Die mittlere Antwortgröße in Bytes von Brokern.|Knoten, broker_id|
|CpuUsagePercentageInDouble|Ja|Prozentuale CPU-Auslastung|Percent|Maximum|Prozentuale CPU-Auslastung|Node|
|DeliverBlocksSent|Ja|DeliverBlocksSentDisplayName|Anzahl|Average|Die Anzahl der vom Zustelldienst gesendeten Blöcke.|Knoten, Kanal, gefiltert, data_type|
|DeliverRequestsCompleted|Ja|DeliverRequestsCompletedDisplayName|Anzahl|Average|Die Anzahl abgeschlossener Zustellanforderungen.|Knoten, Kanal, gefiltert, data_type, Erfolg|
|DeliverRequestsReceived|Ja|DeliverRequestsReceivedDisplayName|Anzahl|Average|Die Anzahl empfangener Zustellanforderungen.|Knoten, Kanal, gefiltert, data_type|
|DeliverStreamsClosed|Ja|DeliverStreamsClosedDisplayName|Anzahl|Average|Die Anzahl der GRPC-Streams, die für den Zustelldienst geschlossen wurden.|Knoten|
|DeliverStreamsOpened|Ja|DeliverStreamsOpenedDisplayName|Anzahl|Average|Die Anzahl der GRPC-Datenströme, die für den Zustelldienst geöffnet wurden.|Knoten|
|EndorserChaincodeInstantiationFailures|Ja|EndorserChaincodeInstantiationFailuresDisplayName|Anzahl|Average|Die Anzahl der fehlgeschlagenen Instanziierungen oder Aktualisierungen von Chaincodes.|Knoten, Kanal, Chaincode|
|EndorserDuplicateTransactionFailures|Ja|EndorserDuplicateTransactionFailuresDisplayName|Anzahl|Average|Die Anzahl fehlgeschlagener Vorschläge aufgrund einer doppelten Transaktions-ID.|Knoten, Kanal, Chaincode|
|EndorserEndorsementFailures|Ja|EndorserEndorsementFailuresDisplayName|Anzahl|Average|Anzahl der fehlerhaften Empfehlungen|Node, channel, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Ja|EndorserProposalAclFailuresDisplayName|Anzahl|Average|Die Anzahl der Vorschläge, die bei ACL-Prüfungen fehlgeschlagen sind.|Knoten, Kanal, Chaincode|
|EndorserProposalSimulationFailures|Ja|EndorserProposalSimulationFailuresDisplayName|Anzahl|Average|Die Anzahl fehlgeschlagener Vorschlagssimulationen.|Knoten, Kanal, Chaincode|
|EndorserProposalsReceived|Ja|EndorserProposalsReceivedDisplayName|Anzahl|Average|Die Anzahl empfangener Vorschläge.|Knoten|
|EndorserProposalValidationFailures|Ja|EndorserProposalValidationFailuresDisplayName|Anzahl|Average|Die Anzahl von Vorschlägen, die bei der Erstüberprüfung fehlgeschlagen sind.|Knoten|
|EndorserSuccessfulProposals|Ja|EndorserSuccessfulProposalsDisplayName|Anzahl|Average|Die Anzahl erfolgreicher Vorschläge.|Knoten|
|FabricVersion|Ja|FabricVersionDisplayName|Anzahl|Average|Die aktive Version von Fabric.|Knoten, Version|
|GossipCommMessagesReceived|Ja|GossipCommMessagesReceivedDisplayName|Anzahl|Average|Anzahl empfangener Nachrichten.|Knoten|
|GossipCommMessagesSent|Ja|GossipCommMessagesSentDisplayName|Anzahl|Average|Anzahl gesendeter Nachrichten.|Knoten|
|GossipCommOverflowCount|Ja|GossipCommOverflowCountDisplayName|Anzahl|Average|Anzahl der Pufferüberläufe in ausgehender Warteschlange.|Knoten|
|GossipLeaderElectionLeader|Ja|GossipLeaderElectionLeaderDisplayName|Anzahl|Average|Peer ist übergeordnet (1) oder untergeordnet (0).|Node, channel|
|GossipMembershipTotalPeersKnown|Ja|GossipMembershipTotalPeersKnownDisplayName|Anzahl|Average|Gesamtanzahl der bekannten Peers|Node, channel|
|GossipPayloadBufferSize|Ja|GossipPayloadBufferSizeDisplayName|Anzahl|Average|Größe des Nutzdatenpuffers.|Node, channel|
|GossipStateHeight|Ja|GossipStateHeightDisplayName|Anzahl|Average|Aktuelle Ledgerhöhe|Node, channel|
|GrpcCommConnClosed|Ja|GrpcCommConnClosedDisplayName|Anzahl|Average|Geschlossene gRPC-Verbindungen. „Geöffnet“ minus „Geschlossen“ ist die aktive Anzahl von Verbindungen.|Knoten|
|GrpcCommConnOpened|Ja|GrpcCommConnOpenedDisplayName|Anzahl|Average|Geöffnete gRPC-Verbindungen. „Geöffnet“ minus „Geschlossen“ ist die aktive Anzahl von Verbindungen.|Knoten|
|GrpcServerStreamMessagesReceived|Ja|GrpcServerStreamMessagesReceivedDisplayName|Anzahl|Average|Die Anzahl empfangener Datenstromnachrichten.|Knoten, Dienst, Methode|
|GrpcServerStreamMessagesSent|Ja|GrpcServerStreamMessagesSentDisplayName|Anzahl|Average|Die Anzahl gesendeter Datenstromnachrichten.|Knoten, Dienst, Methode|
|GrpcServerStreamRequestsCompleted|Ja|GrpcServerStreamRequestsCompletedDisplayName|Anzahl|Average|Die Anzahl abgeschlossener Datenstromanforderungen.|Knoten, Dienst, Methode, Code|
|GrpcServerUnaryRequestsReceived|Ja|GrpcServerUnaryRequestsReceivedDisplayName|Anzahl|Average|Die Anzahl empfangener unärer Anforderungen.|Knoten, Dienst, Methode|
|IOReadBytes|Ja|Gelesene E/A-Bytes|Byte|Gesamt|Gelesene E/A-Bytes|Node|
|IOWriteBytes|Ja|Geschriebene E/A-Bytes|Byte|Gesamt|Geschriebene E/A-Bytes|Node|
|LedgerBlockchainHeight|Ja|LedgerBlockchainHeightDisplayName|Anzahl|Average|Höhe der Kette in Blöcken.|Node, channel|
|LedgerTransactionCount|Ja|LedgerTransactionCountDisplayName|Anzahl|Average|Die Anzahl der verarbeiteten Transaktionen|Node, channel, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Ja|LoggingEntriesCheckedDisplayName|Anzahl|Average|Anzahl der Protokolleinträge, die mit dem aktiven Protokolliergrad überprüft wurden.|Knoten, Ebene|
|LoggingEntriesWritten|Ja|LoggingEntriesWrittenDisplayName|Anzahl|Average|Anzahl der geschriebenen Protokolleinträge.|Knoten, Ebene|
|MemoryLimit|Ja|Arbeitsspeicherlimit|Byte|Average|Arbeitsspeicherlimit|Node|
|MemoryUsage|Ja|Speicherauslastung|Byte|Average|Speicherauslastung|Node|
|MemoryUsagePercentageInDouble|Ja|Prozentuale Arbeitsspeicherauslastung|Percent|Average|Prozentuale Arbeitsspeicherauslastung|Node|
|PendingTransactions|Ja|Ausstehende Transaktionen|Anzahl|Average|Ausstehende Transaktionen|Node|
|ProcessedBlocks|Ja|Verarbeitete Blöcke|Anzahl|Gesamt|Verarbeitete Blöcke|Node|
|ProcessedTransactions|Ja|Verarbeitete Transaktionen|Anzahl|Gesamt|Verarbeitete Transaktionen|Node|
|QueuedTransactions|Ja|Transaktionen in Warteschlange|Anzahl|Average|Transaktionen in Warteschlange|Node|
|RequestHandled|Ja|Behandelte Anforderungen|Anzahl|Gesamt|Behandelte Anforderungen|Node|
|StorageUsage|Ja|Speicherauslastung|Byte|Average|Speicherauslastung|Node|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|RequestLatency|Ja|Anforderungswartezeit|Millisekunden|Gesamt|Zeit, die der Server für die Verarbeitung der Anforderung benötigt|Vorgang, Authentifizierung, Protokoll, DataCenter|
|RequestsTraffic|Ja|Datenverkehr durch Anforderungen|Percent|Anzahl|Anzahl gestellter Anforderungen|Vorgang, Authentifizierung, Protokoll, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|allcachehits|Ja|Cachetreffer (instanzbasiert)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allcachemisses|Ja|Cachefehler (instanzbasiert)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allcacheRead|Ja|Cachelesevorgänge (instanzbasiert)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allcacheWrite|Ja|Cacheschreibvorgänge (instanzbasiert)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allconnectedclients|Ja|Verbundene Clients (instanzbasiert)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allevictedkeys|Ja|Entfernte Schlüssel (instanzbasiert)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allexpiredkeys|Ja|Abgelaufene Schlüssel (instanzbasiert)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allgetcommands|Ja|Abrufe (instanzbasiert)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|alloperationsPerSecond|Ja|Vorgänge pro Sekunde (instanzbasiert)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allpercentprocessortime|Ja|CPU (instanzbasiert)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allserverLoad|Ja|Serverauslastung (instanzbasiert)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allsetcommands|Ja|Set-Vorgänge (instanzbasiert)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|alltotalcommandsprocessed|Ja|Vorgänge gesamt (instanzbasiert)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|alltotalkeys|Ja|Schlüssel insgesamt (instanzbasiert)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allusedmemory|Ja|Verwendeter Arbeitsspeicher (instanzbasiert)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allusedmemorypercentage|Ja|Prozentsatz der Arbeitsspeicherverwendung (instanzbasiert)|Percent|Maximum|Der Prozentsatz des Cachespeichers, der für Schlüssel-Wert-Paare verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|allusedmemoryRss|Ja|Verwendeter Arbeitsspeicher (RSS) (instanzbasiert)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, Port, Primär|
|cachehits|Ja|Cachetreffer|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|cachehits0|Ja|Cachetreffer (Shard 0)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits1|Ja|Cachetreffer (Shard 1)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits2|Ja|Cachetreffer (Shard 2)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits3|Ja|Cachetreffer (Shard 3)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits4|Ja|Cachetreffer (Shard 4)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits5|Ja|Cachetreffer (Shard 5)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits6|Ja|Cachetreffer (Shard 6)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits7|Ja|Cachetreffer (Shard 7)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits8|Ja|Cachetreffer (Shard 8)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachehits9|Ja|Cachetreffer (Shard 9)|Anzahl|Gesamt|Die Anzahl erfolgreicher Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheLatency|Ja|Cachewartezeit in Mikrosekunden (Vorschau)|Anzahl|Average|Die Wartezeit für den Cache in Mikrosekunden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|cachemisses|Ja|Cachefehler|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|cachemisses0|Ja|Cachefehler (Shard 0)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses1|Ja|Cachefehler (Shard 1)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses2|Ja|Cachefehler (Shard 2)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses3|Ja|Cachefehler (Shard 3)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses4|Ja|Cachefehler (Shard 4)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses5|Ja|Cachefehler (Shard 5)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses6|Ja|Cachefehler (Shard 6)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses7|Ja|Cachefehler (Shard 7)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses8|Ja|Cachefehler (Shard 8)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemisses9|Ja|Cachefehler (Shard 9)|Anzahl|Gesamt|Die Anzahl fehlerhafter Schlüsselsuchen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cachemissrate|Ja|Cachefehlerrate|Percent|Gesamt|Der Prozentsatz fehlerhafter Get-Anforderungen. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|cacheRead|Ja|Cache-Lesevorgänge|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|cacheRead0|Ja|Cachelesevorgänge (Shard 0)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead1|Ja|Cachelesevorgänge (Shard 1)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead2|Ja|Cachelesevorgänge (Shard 2)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead3|Ja|Cachelesevorgänge (Shard 3)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead4|Ja|Cachelesevorgänge (Shard 4)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead5|Ja|Cachelesevorgänge (Shard 5)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead6|Ja|Cachelesevorgänge (Shard 6)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead7|Ja|Cachelesevorgänge (Shard 7)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead8|Ja|Cachelesevorgänge (Shard 8)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheRead9|Ja|Cachelesevorgänge (Shard 9)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die aus dem Cache gelesen wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite|Ja|Cache-Schreibvorgänge|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|cacheWrite0|Ja|Cacheschreibvorgänge (Shard 0)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite1|Ja|Cacheschreibvorgänge (Shard 1)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite2|Ja|Cacheschreibvorgänge (Shard 2)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite3|Ja|Cacheschreibvorgänge (Shard 3)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite4|Ja|Cacheschreibvorgänge (Shard 4)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite5|Ja|Cacheschreibvorgänge (Shard 5)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite6|Ja|Cacheschreibvorgänge (Shard 6)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite7|Ja|Cacheschreibvorgänge (Shard 7)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite8|Ja|Cacheschreibvorgänge (Shard 8)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|cacheWrite9|Ja|Cacheschreibvorgänge (Shard 9)|Bytes pro Sekunde|Maximum|Die Menge an Daten in Megabyte pro Sekunde (MB/s), die in den Cache geschrieben wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients|Ja|Verbundene Clients|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|connectedclients0|Ja|Verbundene Clients (Shard 0)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients1|Ja|Verbundene Clients (Shard 1)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients2|Ja|Verbundene Clients (Shard 2)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients3|Ja|Verbundene Clients (Shard 3)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients4|Ja|Verbundene Clients (Shard 4)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients5|Ja|Verbundene Clients (Shard 5)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients6|Ja|Verbundene Clients (Shard 6)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients7|Ja|Verbundene Clients (Shard 7)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients8|Ja|Verbundene Clients (Shard 8)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|connectedclients9|Ja|Verbundene Clients (Shard 9)|Anzahl|Maximum|Die Anzahl von Clientverbindungen mit dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|errors|Ja|Errors|Anzahl|Maximum|Die Anzahl von Fehlern, die im Cache aufgetreten sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId, ErrorType|
|evictedkeys|Ja|Entfernte Schlüssel|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|evictedkeys0|Ja|Entfernte Schlüssel (Shard 0)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys1|Ja|Entfernte Schlüssel (Shard 1)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys2|Ja|Entfernte Schlüssel (Shard 2)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys3|Ja|Entfernte Schlüssel (Shard 3)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys4|Ja|Entfernte Schlüssel (Shard 4)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys5|Ja|Entfernte Schlüssel (Shard 5)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys6|Ja|Entfernte Schlüssel (Shard 6)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys7|Ja|Entfernte Schlüssel (Shard 7)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys8|Ja|Entfernte Schlüssel (Shard 8)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|evictedkeys9|Ja|Entfernte Schlüssel (Shard 9)|Anzahl|Gesamt|Die Anzahl von Elementen, die aus dem Cache entfernt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys|Ja|Abgelaufene Schlüssel|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|expiredkeys0|Ja|Abgelaufene Schlüssel (Shard 0)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys1|Ja|Abgelaufene Schlüssel (Shard 1)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys2|Ja|Abgelaufene Schlüssel (Shard 2)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys3|Ja|Abgelaufene Schlüssel (Shard 3)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys4|Ja|Abgelaufene Schlüssel (Shard 4)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys5|Ja|Abgelaufene Schlüssel (Shard 5)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys6|Ja|Abgelaufene Schlüssel (Shard 6)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys7|Ja|Abgelaufene Schlüssel (Shard 7)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys8|Ja|Abgelaufene Schlüssel (Shard 8)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|expiredkeys9|Ja|Abgelaufene Schlüssel (Shard 9)|Anzahl|Gesamt|Die Anzahl von Elementen, die im Cache abgelaufen sind. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands|Ja|get-Vorgänge|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|getcommands0|Ja|Get-Vorgänge (Shard 0)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands1|Ja|Get-Vorgänge (Shard 1)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands2|Ja|Get-Vorgänge (Shard 2)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands3|Ja|Get-Vorgänge (Shard 3)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands4|Ja|Get-Vorgänge (Shard 4)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands5|Ja|Get-Vorgänge (Shard 5)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands6|Ja|Get-Vorgänge (Shard 6)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands7|Ja|Get-Vorgänge (Shard 7)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands8|Ja|Get-Vorgänge (Shard 8)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|getcommands9|Ja|Get-Vorgänge (Shard 9)|Anzahl|Gesamt|Die Anzahl von Get-Vorgängen aus dem Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond|Ja|Vorgänge pro Sekunde|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|operationsPerSecond0|Ja|Vorgänge pro Sekunde (Shard 0)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond1|Ja|Vorgänge pro Sekunde (Shard 1)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond2|Ja|Vorgänge pro Sekunde (Shard 2)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond3|Ja|Vorgänge pro Sekunde (Shard 3)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond4|Ja|Vorgänge pro Sekunde (Shard 4)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond5|Ja|Vorgänge pro Sekunde (Shard 5)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond6|Ja|Vorgänge pro Sekunde (Shard 6)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond7|Ja|Vorgänge pro Sekunde (Shard 7)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond8|Ja|Vorgänge pro Sekunde (Shard 8)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|operationsPerSecond9|Ja|Vorgänge pro Sekunde (Shard 9)|Anzahl|Maximum|Die Anzahl von sofortigen Vorgängen pro Sekunde, die im Cache ausgeführt wurden. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime|Ja|CPU|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|percentProcessorTime0|Ja|CPU (Shard 0)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime1|Ja|CPU (Shard 1)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime2|Ja|CPU (Shard 2)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime3|Ja|CPU (Shard 3)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime4|Ja|CPU (Shard 4)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime5|Ja|CPU (Shard 5)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime6|Ja|CPU (Shard 6)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime7|Ja|CPU (Shard 7)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime8|Ja|CPU (Shard 8)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|percentProcessorTime9|Ja|CPU (Shard 9)|Percent|Maximum|Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad|Ja|Serverlast|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|serverLoad0|Ja|Serverauslastung (Shard 0)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad1|Ja|Serverauslastung (Shard 1)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad2|Ja|Serverauslastung (Shard 2)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad3|Ja|Serverauslastung (Shard 3)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad4|Ja|Serverauslastung (Shard 4)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad5|Ja|Serverauslastung (Shard 5)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad6|Ja|Serverauslastung (Shard 6)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad7|Ja|Serverauslastung (Shard 7)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad8|Ja|Serverauslastung (Shard 8)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|serverLoad9|Ja|Serverauslastung (Shard 9)|Percent|Maximum|Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands|Ja|Sets|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|setcommands0|Ja|Set-Vorgänge (Shard 0)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands1|Ja|Set-Vorgänge (Shard 1)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands2|Ja|Set-Vorgänge (Shard 2)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands3|Ja|Set-Vorgänge (Shard 3)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands4|Ja|Set-Vorgänge (Shard 4)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands5|Ja|Set-Vorgänge (Shard 5)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands6|Ja|Set-Vorgänge (Shard 6)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands7|Ja|Set-Vorgänge (Shard 7)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands8|Ja|Set-Vorgänge (Shard 8)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|setcommands9|Ja|Set-Vorgänge (Shard 9)|Anzahl|Gesamt|Die Anzahl von Set-Vorgängen an den Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed|Ja|Vorgänge gesamt|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|totalcommandsprocessed0|Ja|Vorgänge gesamt (Shard 0)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed1|Ja|Vorgänge gesamt (Shard 1)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed2|Ja|Vorgänge gesamt (Shard 2)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed3|Ja|Vorgänge gesamt (Shard 3)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed4|Ja|Vorgänge gesamt (Shard 4)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed5|Ja|Vorgänge gesamt (Shard 5)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed6|Ja|Vorgänge gesamt (Shard 6)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed7|Ja|Vorgänge gesamt (Shard 7)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed8|Ja|Vorgänge gesamt (Shard 8)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalcommandsprocessed9|Ja|Vorgänge gesamt (Shard 9)|Anzahl|Gesamt|Die Gesamtzahl der vom Cacheserver verarbeiteten Befehle. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys|Ja|Schlüssel insgesamt|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|totalkeys0|Ja|Schlüssel gesamt (Shard 0)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys1|Ja|Schlüssel gesamt (Shard 1)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys2|Ja|Schlüssel gesamt (Shard 2)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys3|Ja|Schlüssel gesamt (Shard 3)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys4|Ja|Schlüssel gesamt (Shard 4)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys5|Ja|Schlüssel gesamt (Shard 5)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys6|Ja|Schlüssel gesamt (Shard 6)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys7|Ja|Schlüssel gesamt (Shard 7)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys8|Ja|Schlüssel gesamt (Shard 8)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|totalkeys9|Ja|Schlüssel gesamt (Shard 9)|Anzahl|Maximum|Die Gesamtzahl der Elemente im Cache. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory|Ja|Verwendeter Arbeitsspeicher|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|usedmemory0|Ja|Verwendeter Arbeitsspeicher (Shard 0)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory1|Ja|Verwendeter Arbeitsspeicher (Shard 1)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory2|Ja|Verwendeter Arbeitsspeicher (Shard 2)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory3|Ja|Verwendeter Arbeitsspeicher (Shard 3)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory4|Ja|Verwendeter Arbeitsspeicher (Shard 4)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory5|Ja|Verwendeter Arbeitsspeicher (Shard 5)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory6|Ja|Verwendeter Arbeitsspeicher (Shard 6)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory7|Ja|Verwendeter Arbeitsspeicher (Shard 7)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory8|Ja|Verwendeter Arbeitsspeicher (Shard 8)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemory9|Ja|Verwendeter Arbeitsspeicher (Shard 9)|Byte|Maximum|Die Menge an Cachespeicher in MB, die für Schlüssel-Wert-Paare im Cache verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemorypercentage|Ja|Prozentsatz der Arbeitsspeicherverwendung|Percent|Maximum|Der Prozentsatz des Cachespeichers, der für Schlüssel-Wert-Paare verwendet wurde. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|usedmemoryRss|Ja|Verwendeter Arbeitsspeicher (RSS)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|ShardId|
|usedmemoryRss0|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 0)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss1|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 1)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss2|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 2)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss3|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 3)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss4|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 4)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss5|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 5)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss6|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 6)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss7|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 7)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss8|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 8)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|
|usedmemoryRss9|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 9)|Byte|Maximum|Die Menge an verwendetem Cachespeicher in MB, einschließlich Fragmentierung und Metadaten. Weitere Details finden Sie unter https://aka.ms/redis/metrics.|Keine Dimensionen|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|cachehits|Ja|Cachetreffer|Anzahl|Gesamt||Keine Dimensionen|
|cacheLatency|Ja|Cachewartezeit in Mikrosekunden (Vorschau)|Anzahl|Average||InstanceId|
|cachemisses|Ja|Cachefehler|Anzahl|Gesamt||InstanceId|
|cacheRead|Ja|Cache-Lesevorgänge|Bytes pro Sekunde|Maximum||InstanceId|
|cacheWrite|Ja|Cache-Schreibvorgänge|Bytes pro Sekunde|Maximum||InstanceId|
|connectedclients|Ja|Verbundene Clients|Anzahl|Maximum||InstanceId|
|errors|Ja|Errors|Anzahl|Maximum||InstanceId, ErrorType|
|evictedkeys|Ja|Entfernte Schlüssel|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys|Ja|Abgelaufene Schlüssel|Anzahl|Gesamt||Keine Dimensionen|
|getcommands|Ja|get-Vorgänge|Anzahl|Gesamt||Keine Dimensionen|
|operationsPerSecond|Ja|Vorgänge pro Sekunde|Anzahl|Maximum||Keine Dimensionen|
|percentProcessorTime|Ja|CPU|Percent|Maximum||InstanceId|
|serverLoad|Ja|Serverlast|Percent|Maximum||Keine Dimensionen|
|setcommands|Ja|Sets|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed|Ja|Vorgänge gesamt|Anzahl|Gesamt||Keine Dimensionen|
|totalkeys|Ja|Schlüssel insgesamt|Anzahl|Maximum||Keine Dimensionen|
|usedmemory|Ja|Verwendeter Arbeitsspeicher|Byte|Maximum||Keine Dimensionen|
|usedmemorypercentage|Ja|Prozentsatz der Arbeitsspeicherverwendung|Percent|Maximum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Ja|Anforderungsanzahl für die Web Application Firewall|Anzahl|Gesamt|Die Anzahl der von der Web Application Firewall verarbeiteten Clientanforderungen|PolicyName, RuleName, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ByteHitRatio|Ja|Bytetrefferquote|Percent|Average|Das Verhältnis der gesamten aus dem Cache bereitgestellten Bytes im Vergleich zu den gesamten Antwortbytes|Endpunkt|
|OriginHealthPercentage|Ja|Prozentsatz der Ursprungsintegrität|Percent|Average|Der Prozentsatz der erfolgreichen Integritätstests zwischen AFDX und Back-Ends.|Ursprung, OriginGroup|
|OriginLatency|Ja|Ursprüngliche Wartezeit|Millisekunden|Average|Die Zeitspanne zwischen dem Senden der Anforderung durch AFDX an das Back-End und dem Empfang des letzten Antwortbytes durch AFDX vom Back-End.|Ursprung, Endpunkt|
|OriginRequestCount|Ja|Anzahl der Ursprungsanforderungen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die von AFDX zum Ursprung gesendet wurden.|HttpStatus, HttpStatusGroup, Ursprung, Endpunkt|
|Percentage4XX|Ja|Prozentsatz von 4xx|Percent|Average|Der Prozentsatz aller Clientanforderungen, deren Antwortstatuscode 4XX ist|Endpunkt, ClientRegion, ClientCountry|
|Percentage5XX|Ja|Prozentsatz von 5xx|Percent|Average|Der Prozentsatz aller Clientanforderungen, deren Antwortstatuscode 5XX ist|Endpunkt, ClientRegion, ClientCountry|
|RequestCount|Ja|Anzahl der Anforderungen|Anzahl|Gesamt|Die Anzahl der vom HTTP/S-Proxy bereitgestellten Clientanforderungen|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpunkt|
|RequestSize|Ja|Anforderungsgröße|Byte|Gesamt|Die Anzahl der von Clients als Anforderungen an AFDX gesendeten Bytes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpunkt|
|ResponseSize|Ja|Antwortgröße|Byte|Gesamt|Die Anzahl der vom HTTP/S-Proxy als Antworten an Clients gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpunkt|
|TotalLatency|Ja|Gesamtlatenz|Millisekunden|Average|Die Zeitspanne zwischen dem Empfang der Clientanforderung durch den HTTP/S-Proxy und der Bestätigung des letzten Antwortbytes vom HTTP/S-Proxy durch den Client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpunkt|
|WebApplicationFirewallRequestCount|Ja|Anforderungsanzahl für die Web Application Firewall|Anzahl|Gesamt|Die Anzahl der von der Web Application Firewall verarbeiteten Clientanforderungen|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|Nein|Datenträgerlesevorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|RoleInstanceId|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|RoleInstanceId|
|Disk Write Bytes/Sec|Nein|Datenträgerschreibvorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|RoleInstanceId|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|RoleInstanceId|
|Netzwerk eingehend|Ja|Netzwerk eingehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|RoleInstanceId|
|Netzwerk ausgehend|Ja|Netzwerk ausgehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|RoleInstanceId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|Nein|Datenträgerlesevorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Disk Write Bytes/Sec|Nein|Datenträgerschreibvorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Netzwerk eingehend|Ja|Netzwerk eingehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Netzwerk ausgehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Ja|Verwendete Kapazität|Byte|Average|Vom Konto verwendete Kapazität|Keine Dimensionen|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|BlobCapacity|Nein|Blob-Kapazität|Byte|Average|Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte.|BlobType, Tier|
|BlobCount|Nein|Anzahl von Blobs|Anzahl|Average|Die Anzahl von Blobs im Blob-Dienst des Speicherkontos.|BlobType, Tier|
|ContainerCount|Ja|Anzahl von Blob-Containern|Anzahl|Average|Die Anzahl von Containern im Blob-Dienst des Speicherkontos.|Keine Dimensionen|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|IndexCapacity|Nein|Indexkapazität|Byte|Average|Die vom ADLS Gen2-Index (hierarchisch) verwendete Speichermenge in Byte.|Keine Dimensionen|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication, FileShare|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|Nein|Dateikapazität|Byte|Average|Die Größe des vom Dateidienst des Speicherkontos genutzten Speichers in Byte.|FileShare|
|FileCount|Nein|Dateianzahl|Anzahl|Average|Die Anzahl von Dateien im Dateidienst des Speicherkontos.|FileShare|
|FileShareCount|Nein|Anzahl von Dateifreigaben|Anzahl|Average|Die Anzahl von Dateifreigaben im Dateidienst des Speicherkontos.|Keine Dimensionen|
|FileShareQuota|Nein|Kontingentgröße für Dateifreigabe|Byte|Average|Die Obergrenze für die Speichermenge zur Verwendung durch den Azure Files-Dienst, angegeben in Byte.|FileShare|
|FileShareSnapshotCount|Nein|Anzahl von Momentaufnahmen in Dateifreigabe|Anzahl|Average|Die Anzahl der in der Freigabe im Dateidienst des Speicherkontos enthaltenen Momentaufnahmen.|FileShare|
|FileShareSnapshotSize|Nein|Größe der Momentaufnahmen in Dateifreigabe|Byte|Average|Die Speichermenge in Byte, die von den Momentaufnahmen im Dateidienst des Speicherkontos verwendet wird.|FileShare|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication, FileShare|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Ja|Warteschlangenkapazität|Byte|Average|Die Größe des vom Warteschlangendienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|QueueCount|Ja|Anzahl von Warteschlangen|Anzahl|Average|Die Anzahl von Warteschlangen im Warteschlangendienst des Speicherkontos.|Keine Dimensionen|
|QueueMessageCount|Ja|Anzahl von Warteschlangennachrichten|Anzahl|Average|Die ungefähre Anzahl von Warteschlangennachrichten im Warteschlangendienst des Speicherkontos.|Keine Dimensionen|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|TableCapacity|Ja|Tabellenkapazität|Byte|Average|Die Größe des vom Tabellendienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|TableCount|Ja|Anzahl von Tabellen|Anzahl|Average|Die Anzahl von Tabellen im Tabellendienst des Speicherkontos.|Keine Dimensionen|
|TableEntityCount|Ja|Anzahl von Tabellenentitäten|Anzahl|Average|Die Anzahl von Tabellenentitäten im Tabellendienst des Speicherkontos.|Keine Dimensionen|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcloudtesthostedpools"></a>Microsoft.Cloudtest/hostedpools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Zugeordnet|Ja|Zugeordnet|Anzahl|Average|Ressourcen, die zugeordnet werden|PoolId, SKU, Images, ProviderName|
|AllocationDurationMs|Ja|AllocationDurationMs|Millisekunden|Average|Durchschnittliche Zeit zum Zuordnen von Anforderungen (ms)|PoolId, Type, ResourceRequestType, Image|
|Anzahl|Ja|Anzahl|Anzahl|Anzahl|Anzahl von Anforderungen im letzten Speicherabbild|RequestType, Status, PoolId, Type, ErrorCode, FailureStage|
|NotReady|Ja|NotReady|Anzahl|Average|Ressourcen, die nicht zur Verwendung bereit sind|PoolId, SKU, Images, ProviderName|
|PendingReimage|Ja|PendingReimage|Anzahl|Average|Ressourcen, für die ein Reimaging aussteht|PoolId, SKU, Images, ProviderName|
|PendingReturn|Ja|PendingReturn|Anzahl|Average|Ressourcen, für die eine Rückgabe aussteht|PoolId, SKU, Images, ProviderName|
|Bereitgestellt|Ja|Bereitgestellt|Anzahl|Average|Ressourcen, die bereitgestellt werden|PoolId, SKU, Images, ProviderName|
|Bereit|Ja|Bereit|Anzahl|Average|Ressourcen, die zur Verwendung bereit sind|PoolId, SKU, Images, ProviderName|
|Wird gestartet|Ja|Wird gestartet|Anzahl|Average|Ressourcen, die gestartet werden|PoolId, SKU, Images, ProviderName|
|Gesamt|Ja|Gesamt|Anzahl|Average|Gesamtanzahl der Ressourcen|PoolId, SKU, Images, ProviderName|


## <a name="microsoftcloudtestpools"></a>Microsoft.Cloudtest/pools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Zugeordnet|Ja|Zugeordnet|Anzahl|Average|Ressourcen, die zugeordnet werden|PoolId, SKU, Images, ProviderName|
|AllocationDurationMs|Ja|AllocationDurationMs|Millisekunden|Average|Durchschnittliche Zeit zum Zuordnen von Anforderungen (ms)|PoolId, Type, ResourceRequestType, Image|
|Anzahl|Ja|Anzahl|Anzahl|Anzahl|Anzahl von Anforderungen im letzten Speicherabbild|RequestType, Status, PoolId, Type, ErrorCode, FailureStage|
|NotReady|Ja|NotReady|Anzahl|Average|Ressourcen, die nicht zur Verwendung bereit sind|PoolId, SKU, Images, ProviderName|
|PendingReimage|Ja|PendingReimage|Anzahl|Average|Ressourcen, für die ein Reimaging aussteht|PoolId, SKU, Images, ProviderName|
|PendingReturn|Ja|PendingReturn|Anzahl|Average|Ressourcen, für die eine Rückgabe aussteht|PoolId, SKU, Images, ProviderName|
|Bereitgestellt|Ja|Bereitgestellt|Anzahl|Average|Ressourcen, die bereitgestellt werden|PoolId, SKU, Images, ProviderName|
|Bereit|Ja|Bereit|Anzahl|Average|Ressourcen, die zur Verwendung bereit sind|PoolId, SKU, Images, ProviderName|
|Wird gestartet|Ja|Wird gestartet|Anzahl|Average|Ressourcen, die gestartet werden|PoolId, SKU, Images, ProviderName|
|Gesamt|Ja|Gesamt|Anzahl|Average|Gesamtanzahl der Ressourcen|PoolId, SKU, Images, ProviderName|


## <a name="microsoftclusterstornodes"></a>Microsoft.ClusterStor/nodes

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|TotalCapacityAvailable|Nein|TotalCapacityAvailable|Byte|Average|Die im Lustre-Dateisystem verfügbare Gesamtkapazität|filesystem_name, category, system|
|TotalCapacityUsed|Nein|TotalCapacityUsed|Byte|Average|Die im Lustre-Dateisystem verwendete Gesamtkapazität|filesystem_name, category, system|
|TotalRead|Nein|TotalRead|Bytes pro Sekunde|Average|Die Gesamtanzahl der Lesevorgänge pro Sekunde im Lustre-Dateisystem|filesystem_name, category, system|
|TotalWrite|Nein|TotalWrite|Bytes pro Sekunde|Average|Die Gesamtanzahl der Schreibvorgänge pro Sekunde im Lustre-Dateisystem|filesystem_name, category, system|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AudioSecondsTranscribed|Ja|Transkribierte Audiosekunden|Anzahl|Gesamt|Anzahl der transkribierten Sekunden|ApiName, FeatureName, UsageChannel, Region|
|AudioSecondsTranslated|Ja|Übersetzte Audiosekunden|Anzahl|Gesamt|Anzahl der übersetzten Sekunden|ApiName, FeatureName, UsageChannel, Region|
|BlockedCalls|Ja|Blockierte Aufrufe|Anzahl|Gesamt|Anzahl von Aufrufen, die das Raten- oder Kontingentlimit überschritten haben|ApiName, OperationName, Region|
|CharactersTrained|Ja|Trainierte Zeichen (veraltet)|Anzahl|Gesamt|Gesamtzahl trainierter Zeichen|ApiName, OperationName, Region|
|CharactersTranslated|Ja|Übersetzte Zeichen (veraltet)|Anzahl|Gesamt|Gesamtanzahl von Zeichen in einer eingehenden Textanforderung|ApiName, OperationName, Region|
|ClientErrors|Ja|Clientfehler|Anzahl|Gesamt|Anzahl von Aufrufen mit Fehler auf Clientseite (HTTP-Antwortcode 4xx)|ApiName, OperationName, Region|
|ComputerVisionTransactions|Ja|Transaktionen für maschinelles Sehen|Anzahl|Gesamt|Anzahl der Transaktionen für maschinelles Sehen|ApiName, FeatureName, UsageChannel, Region|
|CustomVisionTrainingTime|Ja|Trainingszeit für Custom Vision|Sekunden|Gesamt|Trainingszeit für Custom Vision|ApiName, FeatureName, UsageChannel, Region|
|CustomVisionTransactions|Ja|Transaktionen für Custom Vision|Anzahl|Gesamt|Anzahl der Vorhersagetransaktionen für Custom Vision|ApiName, FeatureName, UsageChannel, Region|
|DataIn|Ja|Eingehende Daten|Byte|Gesamt|Menge eingehender Daten in Byte|ApiName, OperationName, Region|
|DataOut|Ja|Datenausgabe|Byte|Gesamt|Menge ausgehender Daten in Byte|ApiName, OperationName, Region|
|DocumentCharactersTranslated|Ja|Übersetzte Dokumentzeichen|Anzahl|Gesamt|Anzahl der Zeichen in einer Dokumentübersetzungsanforderung|ApiName, FeatureName, UsageChannel, Region|
|DocumentCustomCharactersTranslated|Ja|Übersetzte benutzerdefinierte Dokumentzeichen|Anzahl|Gesamt|Anzahl der Zeichen in einer Anforderung zur benutzerdefinierten Dokumentübersetzung|ApiName, FeatureName, UsageChannel, Region|
|FaceImagesTrained|Ja|Trainierte Gesichtsbilder|Anzahl|Gesamt|Anzahl der trainierten Bilder; 1\.000 Bilder pro Transaktion trainiert|ApiName, FeatureName, UsageChannel, Region|
|FacesStored|Ja|Gespeicherte Gesichter|Anzahl|Gesamt|Anzahl der gespeicherten Gesichter, wird täglich anteilig berechnet; die Anzahl der gespeicherten Gesichter wird täglich gemeldet|ApiName, FeatureName, UsageChannel, Region|
|FaceTransactions|Ja|Gesichtserkennungstransaktionen|Anzahl|Gesamt|Anzahl der API-Aufrufe an den Gesichtserkennungsdienst|ApiName, FeatureName, UsageChannel, Region|
|ImagesStored|Ja|Gespeicherte Bilder|Anzahl|Gesamt|Anzahl der in Custom Vision gespeicherten Bilder|ApiName, FeatureName, UsageChannel, Region|
|Latency|Ja|Latency|Millisekunden|Average|Latenz in Millisekunden|ApiName, OperationName, Region|
|LearnedEvents|Ja|Erfasste Ereignisse|Anzahl|Gesamt|Anzahl erfasster Ereignisse.|IsMatchBaseline, Modus, RunId|
|LUISSpeechRequests|Ja|LUIS-Sprachanforderungen|Anzahl|Gesamt|Anzahl der LUIS-Anforderungen zum Verstehen der Sprach-Absichts-Umsetzung|ApiName, FeatureName, UsageChannel, Region|
|LUISTextRequests|Ja|LUIS-Textanforderungen|Anzahl|Gesamt|Anzahl der LUIS-Textanforderungen|ApiName, FeatureName, UsageChannel, Region|
|MatchedRewards|Ja|Übereinstimmende Belohnungen|Anzahl|Gesamt|Anzahl übereinstimmender Belohnungen.|Mode, RunId|
|NumberofSpeakerProfiles|Ja|Anzahl der Sprecherprofile|Anzahl|Gesamt|Anzahl der registrierten Sprecherprofile; wird anteilig stündlich berechnet|ApiName, FeatureName, UsageChannel, Region|
|ObservedRewards|Ja|Beobachtete Belohnungen|Anzahl|Gesamt|Anzahl beobachteter Belohnungen.|Mode, RunId|
|ProcessedCharacters|Ja|Verarbeitete Zeichen|Anzahl|Gesamt|Anzahl der vom Immersive Reader verarbeiteten Zeichen|ApiName, FeatureName, UsageChannel, Region|
|ProcessedHealthTextRecords|Ja|Verarbeitete Textdatensätze zur Integrität|Anzahl|Gesamt|Anzahl der verarbeiteten Textdatensätze zur Integrität|ApiName, FeatureName, UsageChannel, Region|
|ProcessedImages|Ja|Verarbeitete Bilder|Anzahl|Gesamt|Anzahl der verarbeiteten Bilder|ApiName, FeatureName, UsageChannel, Region|
|ProcessedPages|Ja|Verarbeitete Seiten|Anzahl|Gesamt|Anzahl der verarbeiteten Seiten|ApiName, FeatureName, UsageChannel, Region|
|ProcessedTextRecords|Ja|Verarbeitete Textdatensätze|Anzahl|Gesamt|Anzahl von Textdatensätzen.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors|Ja|Serverfehler|Anzahl|Gesamt|Anzahl von Aufrufen mit internem Dienstfehler (HTTP-Antwortcode 5xx)|ApiName, OperationName, Region|
|SpeakerRecognitionTransactions|Ja|Transaktionen zur Sprechererkennung|Anzahl|Gesamt|Anzahl von Transaktionen zur Sprechererkennung|ApiName, FeatureName, UsageChannel, Region|
|SpeechModelHostingHours|Ja|Hostingstunden des Sprachmodells|Anzahl|Gesamt|Anzahl der Hostingstunden des Sprachmodells|ApiName, FeatureName, UsageChannel, Region|
|SpeechSessionDuration|Ja|Dauer der Sprachsitzung (veraltet)|Sekunden|Gesamt|Gesamtdauer der Sprachsitzung in Sekunden|ApiName, OperationName, Region|
|SuccessfulCalls|Ja|Erfolgreiche Aufrufe|Anzahl|Gesamt|Anzahl erfolgreicher Aufrufe|ApiName, OperationName, Region|
|SynthesizedCharacters|Ja|Synthetisierte Zeichen|Anzahl|Gesamt|Anzahl von Zeichen.|ApiName, FeatureName, UsageChannel, Region|
|TextCharactersTranslated|Ja|Übersetzte Textzeichen|Anzahl|Gesamt|Anzahl der Zeichen in eingehenden Textübersetzungsanforderungen|ApiName, FeatureName, UsageChannel, Region|
|TextCustomCharactersTranslated|Ja|Übersetzte benutzerdefinierte Textzeichen|Anzahl|Gesamt|Anzahl der Zeichen in eingehenden benutzerdefinierten Textübersetzungsanforderungen|ApiName, FeatureName, UsageChannel, Region|
|TextTrainedCharacters|Ja|Trainierte Zeichen in Text|Anzahl|Gesamt|Anzahl der mithilfe der Textübersetzung trainierten Zeichen|ApiName, FeatureName, UsageChannel, Region|
|TotalCalls|Ja|Aufrufe gesamt|Anzahl|Gesamt|Gesamtanzahl von Aufrufen|ApiName, OperationName, Region|
|TotalErrors|Ja|Fehler insgesamt|Anzahl|Gesamt|Gesamtzahl von Aufrufen mit Fehlerantwort (HTTP-Antwortcode 4xx oder 5xx)|ApiName, OperationName, Region|
|TotalTokenCalls|Ja|Tokenaufrufe gesamt|Anzahl|Gesamt|Gesamtanzahl von Tokenaufrufen|ApiName, OperationName, Region|
|TotalTransactions|Ja|Transaktionen gesamt (veraltet)|Anzahl|Gesamt|Gesamtanzahl von Transaktionen|Keine Dimensionen|
|VoiceModelHostingHours|Ja|Hostingstunden des Stimmmodells|Anzahl|Gesamt|Anzahl der Stunden.|ApiName, FeatureName, UsageChannel, Region|
|VoiceModelTrainingMinutes|Ja|Trainingsminuten des Stimmmodells|Anzahl|Gesamt|Anzahl der Minuten.|ApiName, FeatureName, UsageChannel, Region|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|Nein|Anforderungen der Authentifizierungs-API|Anzahl|Anzahl|Anzahl aller Anforderungen an den Endpunkt „Communication Services-Authentifizierung“.|Vorgang, StatusCode, StatusCodeClass|
|APIRequestChat|Ja|Anforderungen der Chat-API|Anzahl|Anzahl|Anzahl aller Anforderungen an den Endpunkt „Communication Services-Chat“.|Vorgang, StatusCode, StatusCodeClass|
|APIRequestSMS|Ja|Anforderungen der SMS-API|Anzahl|Anzahl|Anzahl aller Anforderungen an den Endpunkt „Communication Services SMS“.|Vorgang, StatusCode, StatusCodeClass, ErrorCode|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarer Arbeitsspeicher in Byte|Ja|Verfügbarer Arbeitsspeicher in Byte (Vorschau)|Byte|Average|Menge an physischem Arbeitsspeicher in Bytes, der sofort für die Zuordnung zu einem Prozess oder für die Systemverwendung auf dem virtuellen Computer verfügbar ist|RoleInstanceId, RoleId|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|RoleInstanceId, RoleId|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|RoleInstanceId, RoleId|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|RoleInstanceId, RoleId|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|RoleInstanceId, RoleId|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|RoleInstanceId, RoleId|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|RoleInstanceId, RoleId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarer Arbeitsspeicher in Byte|Ja|Verfügbarer Arbeitsspeicher in Byte (Vorschau)|Byte|Average|Menge an physischem Arbeitsspeicher in Bytes, der sofort für die Zuordnung zu einem Prozess oder für die Systemverwendung auf dem virtuellen Computer verfügbar ist|RoleInstanceId, RoleId|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|RoleInstanceId, RoleId|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|RoleInstanceId, RoleId|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|RoleInstanceId, RoleId|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|RoleInstanceId, RoleId|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|RoleInstanceId, RoleId|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|RoleInstanceId, RoleId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Zusammengesetzter Datenträgerlesevorgang in Bytes/Sek.|Nein|Vom Datenträger gelesene Bytes/Sek. (Vorschauversion)|Byte|Average|Vom Datenträger gelesene Bytes/Sek. während des Überwachungszeitraums. Beachten Sie, dass sich diese Metrik in der Vorschau befindet und sich noch ändern kann, ehe sie allgemein verfügbar wird.|Keine Dimensionen|
|Zusammengesetzte Datenträgerlesevorgänge/Sek.|Nein|Lesevorgänge auf Datenträger/Sek. (Vorschau)|Byte|Average|Anzahl der auf einem Datenträger erfolgten Lese-E/As während des Überwachungszeitraums. Beachten Sie, dass sich diese Metrik in der Vorschau befindet und sich noch ändern kann, ehe sie allgemein verfügbar wird.|Keine Dimensionen|
|Zusammengesetzter Datenträgerschreibvorgang in Bytes/s|Nein|Auf Datenträger geschriebene Bytes/s|Byte|Average|Auf Datenträger geschriebene Bytes/Sek. während des Überwachungszeitraums. Beachten Sie, dass sich diese Metrik in der Vorschau befindet und sich noch ändern kann, ehe sie allgemein verfügbar wird.|Keine Dimensionen|
|Zusammengesetzte Datenträgerschreibvorgänge/s|Nein|Schreibvorgänge auf Datenträger/s|Byte|Average|Anzahl der auf einem Datenträger erfolgten Schreib-E/As während des Überwachungszeitraums. Beachten Sie, dass sich diese Metrik in der Vorschau befindet und sich noch ändern kann, ehe sie allgemein verfügbar wird.|Keine Dimensionen|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarer Arbeitsspeicher in Byte|Ja|Verfügbarer Arbeitsspeicher in Byte (Vorschau)|Byte|Average|Menge an physischem Arbeitsspeicher in Bytes, der sofort für die Zuordnung zu einem Prozess oder für die Systemverwendung auf dem virtuellen Computer verfügbar ist|Keine Dimensionen|
|Verbrauchte CPU-Guthaben|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtmenge von Guthaben, die vom virtuellen Computer verwendet werden. Nur auf burstfähigen VMs der B-Serie verfügbar.|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtmenge von Guthaben, die für den Burst verfügbar sind. Nur auf burstfähigen VMs der B-Serie verfügbar.|Keine Dimensionen|
|Beanspruchte Datenträgerbandbreite in Prozent|Ja|Beanspruchte Datenträgerbandbreite in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Datenträgerbandbreite|LUN|
|Beanspruchte Datenträger-IOPS in Prozent|Ja|Beanspruchte Datenträger-IOPS in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Datenträger-E/A-Vorgänge|LUN|
|Maximale Burstbandbreite für Datenträger|Ja|Maximale Burstbandbreite für Datenträger|Anzahl|Average|Maximaler Durchsatz (in Byte pro Sekunde), den der Datenträger mit Bursting erreichen kann|LUN|
|Maximale Burst-IOPS für Datenträger|Ja|Maximale Burst-IOPS für Datenträger|Anzahl|Average|Maximale IOPS, die der Datenträger mit Bursting erreichen kann|LUN|
|Warteschlangentiefe für Datenträger|Ja|Warteschlangentiefe für Datenträger|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN|
|Vom Datenträger gelesene Bytes/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN|
|Zielbandbreite für Datenträger|Ja|Zielbandbreite für Datenträger|Anzahl|Average|Baseline für den Durchsatz (in Byte pro Sekunde), den der Datenträger ohne Bursting erreichen kann|LUN|
|Ziel-IOPS für Datenträger|Ja|Ziel-IOPS für Datenträger|Anzahl|Average|Baseline für IOPS, die der Datenträger ohne Bursting erreichen kann|LUN|
|Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent|Ja|Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent|Percent|Average|Bisher verwendetes Datenträgerguthaben für Burstbandbreite in Prozent|LUN|
|Verwendetes Datenträgerguthaben für Burst-E/A in Prozent|Ja|Verwendetes Datenträgerguthaben für Burst-E/A in Prozent|Percent|Average|Bisher verwendetes Datenträgerguthaben für Burst-E/A in Prozent|LUN|
|Auf den Datenträger geschriebene Bytes/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Eingehende Datenflüsse|Ja|Eingehende Datenflüsse|Anzahl|Average|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für eingehende Datenflüsse|Ja|Maximale Erstellungsrate für eingehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Netzwerk eingehend|Ja|Abrechenbarer eingehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren empfangenen Bytes für alle Netzwerkschnittstellen der VMs (eingehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Abrechenbarer ausgehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren gesendeten Bytes für alle Netzwerkschnittstellen der VMs (ausgehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent|Ja|Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Betriebssystem-Datenträgerbandbreite|LUN|
|Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent|Ja|Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Betriebssystemdatenträger-E/A-Vorgänge|LUN|
|Maximale Burstbandbreite für Betriebssystemdatenträger|Ja|Maximale Burstbandbreite für Betriebssystemdatenträger|Anzahl|Average|Maximaler Durchsatz (in Byte pro Sekunde), den der Betriebssystemdatenträger mit Bursting erreicht werden kann|LUN|
|Maximale Burst-IOPS für Betriebssystemdatenträger|Ja|Maximale Burst-IOPS für Betriebssystemdatenträger|Anzahl|Average|Maximale IOPS-Anzahl, die der Betriebssystemdatenträger mit Bursting erreichen kann|LUN|
|Warteschlangentiefe für Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Zielbandbreite für Betriebssystemdatenträger|Ja|Zielbandbreite für Betriebssystemdatenträger|Anzahl|Average|Baseline für den Durchsatz (in Byte pro Sekunde), den der Betriebssystemdatenträger ohne Bursting erreichen kann|LUN|
|Ziel-IOPS für Betriebssystemdatenträger|Ja|Ziel-IOPS für Betriebssystemdatenträger|Anzahl|Average|Baseline für die IOPS-Anzahl, die der Betriebssystemdatenträger ohne Bursting erreichen kann|LUN|
|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-Bit/s in Prozent|Ja|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-Bit/s in Prozent|Percent|Average|Bisher verwendetes Betriebssystemdatenträger-Guthaben für Burstbandbreite in Prozent|LUN|
|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|Ja|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|Percent|Average|Bisher verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|LUN|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Ausgehende Datenflüsse|Ja|Ausgehende Datenflüsse|Anzahl|Average|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Ja|Maximale Erstellungsrate für ausgehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Datenträger|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Datenträger|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite|Ja|Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite|Percent|Average|Prozentsatz der von der VM beanspruchten zwischengespeicherten Datenträgerbandbreite|Keine Dimensionen|
|Verbrauchte von der VM zwischengespeicherte IOPS in Prozent|Ja|Verbrauchte von der VM zwischengespeicherte IOPS in Prozent|Percent|Average|Prozentsatz der von der VM beanspruchten zwischengespeicherten Datenträger-IOPS|Keine Dimensionen|
|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite|Ja|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite|Percent|Average|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Datenträgerbandbreite|Keine Dimensionen|
|Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent|Ja|Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent|Percent|Average|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Datenträger-IOPS|Keine Dimensionen|
|VmAvailabilityMetric|Ja|VM-Verfügbarkeitsmetrik (Vorschau)|Anzahl|Average|Maß für die Verfügbarkeit virtueller Computer im Zeitverlauf. Hinweis: Diese Metrik wird derzeit nur für eine kleine Gruppe von Kunden als Vorschauversion angezeigt, da wir die Verbesserung der Datenqualität und -konsistenz priorisieren. Im Rahmen der Verbesserung unseres Datenstandards werden wir dieses Feature phasenweise für die gesamte Flotte einführen.|Keine Dimensionen|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualmachineScaleSets

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarer Arbeitsspeicher in Byte|Ja|Verfügbarer Arbeitsspeicher in Byte (Vorschau)|Byte|Average|Menge an physischem Arbeitsspeicher in Bytes, der sofort für die Zuordnung zu einem Prozess oder für die Systemverwendung auf dem virtuellen Computer verfügbar ist|VMName|
|Verbrauchte CPU-Guthaben|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtmenge von Guthaben, die vom virtuellen Computer verwendet werden. Nur auf burstfähigen VMs der B-Serie verfügbar.|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtmenge von Guthaben, die für den Burst verfügbar sind. Nur auf burstfähigen VMs der B-Serie verfügbar.|Keine Dimensionen|
|Beanspruchte Datenträgerbandbreite in Prozent|Ja|Beanspruchte Datenträgerbandbreite in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Datenträgerbandbreite|LUN, VMName|
|Beanspruchte Datenträger-IOPS in Prozent|Ja|Beanspruchte Datenträger-IOPS in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Datenträger-E/A-Vorgänge|LUN, VMName|
|Maximale Burstbandbreite für Datenträger|Ja|Maximale Burstbandbreite für Datenträger|Anzahl|Average|Maximaler Durchsatz (in Byte pro Sekunde), den der Datenträger mit Bursting erreichen kann|LUN, VMName|
|Maximale Burst-IOPS für Datenträger|Ja|Maximale Burst-IOPS für Datenträger|Anzahl|Average|Maximale IOPS, die der Datenträger mit Bursting erreichen kann|LUN, VMName|
|Warteschlangentiefe für Datenträger|Ja|Warteschlangentiefe für Datenträger|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN, VMName|
|Vom Datenträger gelesene Bytes/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN, VMName|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN, VMName|
|Zielbandbreite für Datenträger|Ja|Zielbandbreite für Datenträger|Anzahl|Average|Baseline für den Durchsatz (in Byte pro Sekunde), den der Datenträger ohne Bursting erreichen kann|LUN, VMName|
|Ziel-IOPS für Datenträger|Ja|Ziel-IOPS für Datenträger|Anzahl|Average|Baseline für IOPS, die der Datenträger ohne Bursting erreichen kann|LUN, VMName|
|Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent|Ja|Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent|Percent|Average|Bisher verwendetes Datenträgerguthaben für Burstbandbreite in Prozent|LUN, VMName|
|Verwendetes Datenträgerguthaben für Burst-E/A in Prozent|Ja|Verwendetes Datenträgerguthaben für Burst-E/A in Prozent|Percent|Average|Bisher verwendetes Datenträgerguthaben für Burst-E/A in Prozent|LUN, VMName|
|Auf den Datenträger geschriebene Bytes/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN, VMName|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN, VMName|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|VMName|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|VMName|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|VMName|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|VMName|
|Eingehende Datenflüsse|Ja|Eingehende Datenflüsse|Anzahl|Average|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|VMName|
|Maximale Erstellungsrate für eingehende Datenflüsse|Ja|Maximale Erstellungsrate für eingehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|VMName|
|Netzwerk eingehend|Ja|Abrechenbarer eingehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren empfangenen Bytes für alle Netzwerkschnittstellen der VMs (eingehender Datenverkehr) (veraltet)|VMName|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|VMName|
|Netzwerk ausgehend|Ja|Abrechenbarer ausgehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren gesendeten Bytes für alle Netzwerkschnittstellen der VMs (ausgehender Datenverkehr) (veraltet)|VMName|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|VMName|
|Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent|Ja|Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Betriebssystem-Datenträgerbandbreite|LUN, VMName|
|Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent|Ja|Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Betriebssystemdatenträger-E/A-Vorgänge|LUN, VMName|
|Maximale Burstbandbreite für Betriebssystemdatenträger|Ja|Maximale Burstbandbreite für Betriebssystemdatenträger|Anzahl|Average|Maximaler Durchsatz (in Byte pro Sekunde), den der Betriebssystemdatenträger mit Bursting erreicht werden kann|LUN, VMName|
|Maximale Burst-IOPS für Betriebssystemdatenträger|Ja|Maximale Burst-IOPS für Betriebssystemdatenträger|Anzahl|Average|Maximale IOPS-Anzahl, die der Betriebssystemdatenträger mit Bursting erreichen kann|LUN, VMName|
|Warteschlangentiefe für Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|VMName|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|VMName|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|VMName|
|Zielbandbreite für Betriebssystemdatenträger|Ja|Zielbandbreite für Betriebssystemdatenträger|Anzahl|Average|Baseline für den Durchsatz (in Byte pro Sekunde), den der Betriebssystemdatenträger ohne Bursting erreichen kann|LUN, VMName|
|Ziel-IOPS für Betriebssystemdatenträger|Ja|Ziel-IOPS für Betriebssystemdatenträger|Anzahl|Average|Baseline für die IOPS-Anzahl, die der Betriebssystemdatenträger ohne Bursting erreichen kann|LUN, VMName|
|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-Bit/s in Prozent|Ja|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-Bit/s in Prozent|Percent|Average|Bisher verwendetes Betriebssystemdatenträger-Guthaben für Burstbandbreite in Prozent|LUN, VMName|
|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|Ja|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|Percent|Average|Bisher verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|LUN, VMName|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|VMName|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|VMName|
|Ausgehende Datenflüsse|Ja|Ausgehende Datenflüsse|Anzahl|Average|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|VMName|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Ja|Maximale Erstellungsrate für ausgehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|VMName|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|VMName|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Datenträger|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN, VMName|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Datenträger|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN, VMName|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|VMName|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|VMName|
|Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite|Ja|Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite|Percent|Average|Prozentsatz der von der VM beanspruchten zwischengespeicherten Datenträgerbandbreite|VMName|
|Verbrauchte von der VM zwischengespeicherte IOPS in Prozent|Ja|Verbrauchte von der VM zwischengespeicherte IOPS in Prozent|Percent|Average|Prozentsatz der von der VM beanspruchten zwischengespeicherten Datenträger-IOPS|VMName|
|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite|Ja|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite|Percent|Average|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Datenträgerbandbreite|VMName|
|Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent|Ja|Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent|Percent|Average|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Datenträger-IOPS|VMName|
|VmAvailabilityMetric|Ja|VM-Verfügbarkeitsmetrik (Vorschau)|Anzahl|Average|Maß für die Verfügbarkeit virtueller Computer im Zeitverlauf. Hinweis: Diese Metrik wird derzeit nur für eine kleine Gruppe von Kunden als Vorschauversion angezeigt, da wir die Verbesserung der Datenqualität und -konsistenz priorisieren. Im Rahmen der Verbesserung unseres Datenstandards werden wir dieses Feature phasenweise für die gesamte Flotte einführen.|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarer Arbeitsspeicher in Byte|Ja|Verfügbarer Arbeitsspeicher in Byte (Vorschau)|Byte|Average|Menge an physischem Arbeitsspeicher in Bytes, der sofort für die Zuordnung zu einem Prozess oder für die Systemverwendung auf dem virtuellen Computer verfügbar ist|Keine Dimensionen|
|Verbrauchte CPU-Guthaben|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtmenge von Guthaben, die vom virtuellen Computer verwendet werden. Nur auf burstfähigen VMs der B-Serie verfügbar.|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtmenge von Guthaben, die für den Burst verfügbar sind. Nur auf burstfähigen VMs der B-Serie verfügbar.|Keine Dimensionen|
|Beanspruchte Datenträgerbandbreite in Prozent|Ja|Beanspruchte Datenträgerbandbreite in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Datenträgerbandbreite|LUN|
|Beanspruchte Datenträger-IOPS in Prozent|Ja|Beanspruchte Datenträger-IOPS in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Datenträger-E/A-Vorgänge|LUN|
|Maximale Burstbandbreite für Datenträger|Ja|Maximale Burstbandbreite für Datenträger|Anzahl|Average|Maximaler Durchsatz (in Byte pro Sekunde), den der Datenträger mit Bursting erreichen kann|LUN|
|Maximale Burst-IOPS für Datenträger|Ja|Maximale Burst-IOPS für Datenträger|Anzahl|Average|Maximale IOPS, die der Datenträger mit Bursting erreichen kann|LUN|
|Warteschlangentiefe für Datenträger|Ja|Warteschlangentiefe für Datenträger|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN|
|Vom Datenträger gelesene Bytes/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN|
|Zielbandbreite für Datenträger|Ja|Zielbandbreite für Datenträger|Anzahl|Average|Baseline für den Durchsatz (in Byte pro Sekunde), den der Datenträger ohne Bursting erreichen kann|LUN|
|Ziel-IOPS für Datenträger|Ja|Ziel-IOPS für Datenträger|Anzahl|Average|Baseline für IOPS, die der Datenträger ohne Bursting erreichen kann|LUN|
|Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent|Ja|Verwendetes Datenträgerguthaben für Burst-Bit/s in Prozent|Percent|Average|Bisher verwendetes Datenträgerguthaben für Burstbandbreite in Prozent|LUN|
|Verwendetes Datenträgerguthaben für Burst-E/A in Prozent|Ja|Verwendetes Datenträgerguthaben für Burst-E/A in Prozent|Percent|Average|Bisher verwendetes Datenträgerguthaben für Burst-E/A in Prozent|LUN|
|Auf den Datenträger geschriebene Bytes/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Eingehende Datenflüsse|Ja|Eingehende Datenflüsse|Anzahl|Average|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für eingehende Datenflüsse|Ja|Maximale Erstellungsrate für eingehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Netzwerk eingehend|Ja|Abrechenbarer eingehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren empfangenen Bytes für alle Netzwerkschnittstellen der VMs (eingehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Abrechenbarer ausgehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren gesendeten Bytes für alle Netzwerkschnittstellen der VMs (ausgehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent|Ja|Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Betriebssystem-Datenträgerbandbreite|LUN|
|Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent|Ja|Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent|Percent|Average|Prozentsatz der pro Minute beanspruchten Betriebssystemdatenträger-E/A-Vorgänge|LUN|
|Maximale Burstbandbreite für Betriebssystemdatenträger|Ja|Maximale Burstbandbreite für Betriebssystemdatenträger|Anzahl|Average|Maximaler Durchsatz (in Byte pro Sekunde), den der Betriebssystemdatenträger mit Bursting erreicht werden kann|LUN|
|Maximale Burst-IOPS für Betriebssystemdatenträger|Ja|Maximale Burst-IOPS für Betriebssystemdatenträger|Anzahl|Average|Maximale IOPS-Anzahl, die der Betriebssystemdatenträger mit Bursting erreichen kann|LUN|
|Warteschlangentiefe für Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Zielbandbreite für Betriebssystemdatenträger|Ja|Zielbandbreite für Betriebssystemdatenträger|Anzahl|Average|Baseline für den Durchsatz (in Byte pro Sekunde), den der Betriebssystemdatenträger ohne Bursting erreichen kann|LUN|
|Ziel-IOPS für Betriebssystemdatenträger|Ja|Ziel-IOPS für Betriebssystemdatenträger|Anzahl|Average|Baseline für die IOPS-Anzahl, die der Betriebssystemdatenträger ohne Bursting erreichen kann|LUN|
|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-Bit/s in Prozent|Ja|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-Bit/s in Prozent|Percent|Average|Bisher verwendetes Betriebssystemdatenträger-Guthaben für Burstbandbreite in Prozent|LUN|
|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|Ja|Verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|Percent|Average|Bisher verwendetes Betriebssystemdatenträger-Guthaben für Burst-E/A in Prozent|LUN|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Bytes pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Ausgehende Datenflüsse|Ja|Ausgehende Datenflüsse|Anzahl|Average|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Ja|Maximale Erstellungsrate für ausgehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Datenträger|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Datenträger|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite|Ja|Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite|Percent|Average|Prozentsatz der von der VM beanspruchten zwischengespeicherten Datenträgerbandbreite|Keine Dimensionen|
|Verbrauchte von der VM zwischengespeicherte IOPS in Prozent|Ja|Verbrauchte von der VM zwischengespeicherte IOPS in Prozent|Percent|Average|Prozentsatz der von der VM beanspruchten zwischengespeicherten Datenträger-IOPS|Keine Dimensionen|
|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite|Ja|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite|Percent|Average|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Datenträgerbandbreite|Keine Dimensionen|
|Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent|Ja|Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent|Percent|Average|Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Datenträger-IOPS|Keine Dimensionen|


## <a name="microsoftconnectedvehicleplatformaccounts"></a>Microsoft.ConnectedVehicle/platformAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ClaimsProviderRequestLatency|Ja|Ausführungszeit der Anspruchsanforderung|Millisekunden|Average|Die durchschnittliche Ausführungszeit von Anforderungen an den Endpunkt des Anspruchsanbieters für Kunden in Millisekunden.|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|ClaimsProviderRequests|Ja|Anforderungen des Anspruchsanbieters|Anzahl|Gesamt|Anzahl der Anforderungen an den Anspruchsanbieter|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|ConnectionServiceRequestRuntime|Ja|Ausführungszeit der Anforderung an den Fahrzeugverbindungsdienst|Millisekunden|Average|Durchschnittliche Ausführungszeit der Fahrzeugverbindungsanforderung in Millisekunden|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|ConnectionServiceRequests|Ja|Anforderungen des Fahrzeugverbindungsdiensts|Anzahl|Gesamt|Gesamtzahl der Fahrzeugverbindungsanforderungen|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|DataPipelineMessageCount|Ja|Anzahl von Datenpipelinenachrichten|Anzahl|Gesamt|Die Gesamtanzahl von Nachrichten, die zur Speicherung an die MCVP-Datenpipeline gesendet werden.|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|ExtensionInvocationCount|Ja|Anzahl von Aufrufen der Erweiterung|Anzahl|Gesamt|Die Gesamtanzahl von Aufrufen einer Erweiterung.|VehicleId, DeviceName, ExtensionName, IsSuccessful, FailureCategory|
|ExtensionInvocationRuntime|Ja|Ausführungszeit des Erweiterungsaufrufs|Millisekunden|Average|Die durchschnittliche Ausführungszeit in einer Erweiterung in Millisekunden.|VehicleId, DeviceName, ExtensionName, IsSuccessful, FailureCategory|
|ProvisionerServiceRequestRuntime|Ja|Ausführungszeit der Fahrzeugbereitstellung|Millisekunden|Average|Die durchschnittliche Ausführungszeit von Fahrzeugbereitstellungsanforderungen in Millisekunden|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|ProvisionerServiceRequests|Ja|Anforderungen des Fahrzeugbereitstellungsdiensts|Anzahl|Gesamt|Gesamtzahl der Fahrzeugbereitstellungsanforderungen|VehicleId, DeviceName, IsSuccessful, FailureCategory|
|StateStoreReadRequestLatency|Ja|Ausführungszeit von Lesevorgängen im Zustandsspeicher|Millisekunden|Average|Durchschnittliche Ausführungszeit von Leseanforderungen im Zustandsspeicher in Millisekunden.|VehicleId, DeviceName, ExtensionName, IsSuccessful, FailureCategory|
|StateStoreReadRequests|Ja|Leseanforderungen des Zustandsspeichers|Anzahl|Gesamt|Anzahl der Leseanforderungen an den Zustandsspeicher|VehicleId, DeviceName, ExtensionName, IsSuccessful, FailureCategory|
|StateStoreWriteRequestLatency|Ja|Ausführungszeit von Schreibvorgängen im Zustandsspeicher|Millisekunden|Average|Durchschnittliche Ausführungszeit von Schreibanforderungen im Zustandsspeicher in Millisekunden.|VehicleId, DeviceName, ExtensionName, IsSuccessful, FailureCategory|
|StateStoreWriteRequests|Ja|Schreibanforderungen des Zustandsspeichers|Anzahl|Gesamt|Anzahl der Schreibanforderungen an den Zustandsspeicher|VehicleId, DeviceName, ExtensionName, IsSuccessful, FailureCategory|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CpuUsage|Ja|CPU-Auslastung|Anzahl|Average|CPU-Auslastung für alle Kerne in Millicores|containerName|
|MemoryUsage|Ja|Speicherauslastung|Byte|Average|Gesamtspeicherauslastung in Byte|containerName|
|NetworkBytesReceivedPerSecond|Ja|Empfangene Netzwerkbytes pro Sekunde|Byte|Average|Die pro Sekunde empfangenen Netzwerkbytes.|Keine Dimensionen|
|NetworkBytesTransmittedPerSecond|Ja|Übertragene Netzwerkbytes pro Sekunde|Byte|Average|Die pro Sekunde übertragenen Netzwerkbytes|Keine Dimensionen|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Ja|AgentPool – CPU-Zeit|Sekunden|Gesamt|CPU-Zeit von AgentPool in Sekunden|Keine Dimensionen|
|RunDuration|Ja|Ausführungsdauer|Millisekunden|Gesamt|Ausführungsdauer in Millisekunden|Keine Dimensionen|
|StorageUsed|Ja|Verwendeter Speicher|Byte|Average|Die von der Containerregistrierung beanspruchte Speichermenge. Bei einem Registrierungskonto ist dies die Summe der Kapazität, die von allen Repositorys innerhalb einer Registrierung verwendet wird. Es ist die Summe der Kapazität, die von freigegebenen Ebenen, Manifestdateien und Replikatkopien in den einzelnen Repositorys verwendet wird.|Geolocation|
|SuccessfulPullCount|Ja|Anzahl erfolgreicher Pullvorgänge|Anzahl|Gesamt|Anzahl erfolgreicher Image-Pullvorgänge|Keine Dimensionen|
|SuccessfulPushCount|Ja|Anzahl erfolgreicher Puchvorgänge|Anzahl|Gesamt|Anzahl erfolgreicher Image-Pushvorgänge|Keine Dimensionen|
|TotalPullCount|Ja|Gesamtzahl von Pullvorgängen|Anzahl|Gesamt|Anzahl von Image-Pullvorgängen insgesamt|Keine Dimensionen|
|TotalPushCount|Ja|Gesamtzahl von Pushvorgängen|Anzahl|Gesamt|Anzahl von Image-Pushvorgängen insgesamt|Keine Dimensionen|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|Nein|In-Flight-Anforderungen|Anzahl|Average|Maximale Anzahl der aktuell verwendeten In-Flight-Anforderungen auf dem Apiserver pro Anforderungstyp in der letzten Sekunde|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|Nein|Clusterintegrität|Anzahl|Average|Bestimmt, ob die Autoskalierung Aktionen für den Cluster durchführt oder nicht|Keine Dimensionen|
|cluster_autoscaler_scale_down_in_cooldown|Nein|Herunterskalieren im Abkühlen|Anzahl|Average|Bestimmt, ob sich das Herunterskalieren im Abkühlen befindet: Während dieses Zeitraums werden keine Knoten entfernt.|Keine Dimensionen|
|cluster_autoscaler_unneeded_nodes_count|Nein|Nicht benötigte Knoten|Anzahl|Average|Die Clusterautoskalierung markiert diese Knoten als Kandidaten zum Löschen, die auch schließlich gelöscht werden.|Keine Dimensionen|
|cluster_autoscaler_unschedulable_pods_count|Nein|Nicht planbare Pods|Anzahl|Average|Anzahl von Pods, die derzeit im Cluster nicht planbar sind.|Keine Dimensionen|
|kube_node_status_allocatable_cpu_cores|Nein|Gesamtzahl der verfügbaren CPU-Kerne in einem verwalteten Cluster|Anzahl|Average|Gesamtzahl der verfügbaren CPU-Kerne in einem verwalteten Cluster|Keine Dimensionen|
|kube_node_status_allocatable_memory_bytes|Nein|Gesamtzahl des verfügbaren Speicherplatzes in einem verwalteten Cluster|Byte|Average|Gesamtzahl des verfügbaren Speicherplatzes in einem verwalteten Cluster|Keine Dimensionen|
|kube_node_status_condition|Nein|Status für verschiedene Knotenbedingungen|Anzahl|Average|Status für verschiedene Knotenbedingungen|Bedingung, Status, Status2, Knoten|
|kube_pod_status_phase|Nein|Anzahl der Pods nach Phase|Anzahl|Average|Anzahl der Pods nach Phase|Phase, Namespace, Pod|
|kube_pod_status_ready|Nein|Anzahl der Pods mit dem Status „Bereit“|Anzahl|Average|Anzahl der Pods mit dem Status „Bereit“|Namespace, Pod, Bedingung|
|node_cpu_usage_millicores|Ja|CPU-Auslastung (Millicore)|MilliCores|Average|Aggregierte Messung der CPU-Auslastung im gesamten Cluster in Millicore|Knoten, Knotenpool|
|node_cpu_usage_percentage|Ja|Prozentuale CPU-Auslastung|Percent|Average|Aggregierte durchschnittliche CPU-Auslastung in Prozent für den gesamten Cluster|Knoten, Knotenpool|
|node_disk_usage_bytes|Ja|Datenträgerverwendung (Bytes)|Byte|Average|Verwendeter Datenträgerspeicherplatz in Bytes nach Gerät|Knoten, Knotenpool, Gerät|
|node_disk_usage_percentage|Ja|Datenträgerverwendung (Prozent)|Percent|Average|Verwendeter Datenträgerspeicherplatz in Prozent nach Gerät|Knoten, Knotenpool, Gerät|
|node_memory_rss_bytes|Ja|Arbeitsspeicher-RSS (Bytes)|Byte|Average|Verwendeter RSS-Arbeitsspeicher des Containers in Byte|Knoten, Knotenpool|
|node_memory_rss_percentage|Ja|Arbeitsspeicher-RSS (Prozent)|Percent|Average|Verwendeter RSS-Arbeitsspeicher des Containers in Prozent|Knoten, Knotenpool|
|node_memory_working_set_bytes|Ja|Arbeitssatz für Arbeitsspeicher (Bytes)|Byte|Average|Verwendeter Arbeitssatz-Arbeitsspeicher des Containers in Bytes|Knoten, Knotenpool|
|node_memory_working_set_percentage|Ja|Arbeitssatz für Arbeitsspeicher (Prozent)|Percent|Average|Verwendeter Arbeitssatz-Arbeitsspeicher des Containers in Prozent|Knoten, Knotenpool|
|node_network_in_bytes|Ja|Eingehender Netzwerkverkehr (Bytes)|Byte|Average|Empfangene Netzwerk-Bytes|Knoten, Knotenpool|
|node_network_out_bytes|Ja|Ausgehender Netzwerkverkehr (Bytes)|Byte|Average|Übertragene Netzwerk-Bytes|Knoten, Knotenpool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|FailedRequests|Ja|Anforderungsfehler|Anzahl|Gesamt|Ruft die verfügbaren Protokolle für benutzerdefinierte Ressourcenanbieter ab|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Ja|Erfolgreiche Anforderungen|Anzahl|Gesamt|Erfolgreiche Anforderungen durch den benutzerdefinierten Anbieter|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AvailableCapacity|Ja|Verfügbare Kapazität|Byte|Average|Die verfügbare Kapazität in Byte während des Berichtszeitraums.|Keine Dimensionen|
|BytesUploadedToCloud|Ja|Hochgeladene Cloudbytes (Gerät)|Byte|Average|Die Gesamtzahl von Bytes, die in Azure von einem Gerät während des Berichtszeitraum hochgeladen wird.|Keine Dimensionen|
|BytesUploadedToCloudPerShare|Ja|Hochgeladene Cloudbytes (Freigabe)|Byte|Average|Die Gesamtzahl von Bytes, die in Azure von einer Freigabe während des Berichtszeitraum hochgeladen wird.|Freigabe|
|CloudReadThroughput|Ja|Clouddownloaddurchsatz|Bytes pro Sekunde|Average|Der Clouddownloaddurchsatz in Azure während des Berichtszeitraums.|Keine Dimensionen|
|CloudReadThroughputPerShare|Ja|Clouddownloaddurchsatz (Freigabe)|Bytes pro Sekunde|Average|Der Downloaddurchsatz in Azure von einer Freigabe während des Berichtszeitraums.|Freigabe|
|CloudUploadThroughput|Ja|Clouduploaddurchsatz|Bytes pro Sekunde|Average|Der Clouduploaddurchsatz in Azure während des Berichtszeitraums.|Keine Dimensionen|
|CloudUploadThroughputPerShare|Ja|Clouduploaddurchsatz (Freigabe)|Bytes pro Sekunde|Average|Der Uploaddurchsatz in Azure von einer Freigabe während des Berichtszeitraums.|Freigabe|
|HyperVMemoryUtilization|Ja|Edgecomputing – Arbeitsspeichernutzung|Percent|Average|Größe des genutzten Arbeitsspeichers|InstanceName|
|HyperVVirtualProcessorUtilization|Ja|Edgecomputing – CPU in Prozent|Percent|Average|Prozentsatz der CPU-Auslastung|InstanceName|
|NICReadThroughput|Ja|Lesedurchsatz (Netzwerk)|Bytes pro Sekunde|Average|Der Lesedurchsatz der Netzwerkschnittstelle auf dem Gerät im Berichtszeitraum für alle Volumes im Gateway.|InstanceName|
|NICWriteThroughput|Ja|Schreibdurchsatz (Netzwerk)|Bytes pro Sekunde|Average|Der Schreibdurchsatz der Netzwerkschnittstelle auf dem Gerät im Berichtszeitraum für alle Volumes im Gateway.|InstanceName|
|TotalCapacity|Ja|Gesamtkapazität|Byte|Average|Die Gesamtkapazität des Geräts in Byte während des Berichtszeitraums.|Keine Dimensionen|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DataAssetCount|Ja|Erstellte Datenressourcen|Anzahl|Maximum|Anzahl der erstellten Datenressourcen|DataAssetName|
|PipelineCount|Ja|Erstellte Pipelines|Anzahl|Maximum|Anzahl der erstellten Pipelines|PipelineName|
|ProposalCount|Ja|Erstellte Vorschläge|Anzahl|Maximum|Anzahl der erstellten Vorschläge|ProposalName|
|ScriptCount|Ja|Erstellte Skripts|Anzahl|Maximum|Anzahl der erstellten Skripts|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|FailedRuns|Ja|Fehlerhafte Ausführungen|Anzahl|Gesamt||pipelineName, activityName|
|SuccessfulRuns|Ja|Erfolgreiche Ausführungen|Anzahl|Gesamt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Ja|Metriken zu abgebrochenen Aktivitätsausführungen|Anzahl|Gesamt||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Ja|Metriken zu fehlerhaften Aktivitätsausführungen|Anzahl|Gesamt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Ja|Metriken zu erfolgreichen Aktivitätsausführungen|Anzahl|Gesamt||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Ja|Gesamtgröße der Factory (Einheit: GB)|Anzahl|Maximum||Keine Dimensionen|
|IntegrationRuntimeAvailableMemory|Ja|Verfügbarer Speicher für Integration Runtime|Byte|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Ja|Anzahl verfügbarer Knoten für Integration Runtime|Anzahl|Average||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Ja|Dauer der Integration Runtime-Warteschlange|Sekunden|Average||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Ja|CPU-Auslastung der Integration Runtime|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Ja|Länge der Integration Runtime-Warteschlange|Anzahl|Average||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Ja|Maximal zulässige Factorygröße (Einheit: GB)|Anzahl|Maximum||Keine Dimensionen|
|MaxAllowedResourceCount|Ja|Anzahl maximal zulässiger Entitäten|Anzahl|Maximum||Keine Dimensionen|
|PipelineCancelledRuns|Ja|Metriken zu abgebrochenen Pipelineausführungen|Anzahl|Gesamt||FailureType, Name|
|PipelineElapsedTimeRuns|Ja|Metriken zur für Pipelineausführungen verstrichenen Zeit|Anzahl|Gesamt||RunId, Name|
|PipelineFailedRuns|Ja|Metriken zu fehlerhaften Pipelineausführungen|Anzahl|Gesamt||FailureType, Name|
|PipelineSucceededRuns|Ja|Metriken zu erfolgreichen Pipelineausführungen|Anzahl|Gesamt||FailureType, Name|
|ResourceCount|Ja|Gesamtzahl von Entitäten|Anzahl|Maximum||Keine Dimensionen|
|SSISIntegrationRuntimeStartCancel|Ja|Metriken zu abgebrochenen SSIS-Integration Runtime-Startvorgängen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Ja|Metriken zu fehlerhaften SSIS-Integration Runtime-Startvorgängen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Ja|Metriken zu erfolgreichen SSIS-Integration Runtime-Startvorgängen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Ja|Metriken zu unterbrochenen SSIS-Integration Runtime-Beendigungsvorgängen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Ja|Metriken zu erfolgreichen SSIS-Integration Runtime-Beendigungsvorgängen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Ja|Metriken zu abgebrochenen SSIS-Paketausführungen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Ja|Metriken zu fehlgeschlagenen SSIS-Paketausführungen|Anzahl|Gesamt||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Ja|Metriken zu erfolgreichen SSIS-Paketausführungen|Anzahl|Gesamt||IntegrationRuntimeName|
|TriggerCancelledRuns|Ja|Metriken zu abgebrochenen Triggerausführungen|Anzahl|Gesamt||Name, FailureType|
|TriggerFailedRuns|Ja|Metriken zu fehlerhaften Triggerausführungen|Anzahl|Gesamt||Name, FailureType|
|TriggerSucceededRuns|Ja|Metriken zu erfolgreichen Triggerausführungen|Anzahl|Gesamt||Name, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Ja|Abgebrochene AU-Zeit|Sekunden|Gesamt|Die Gesamt-AU-Zeit für abgebrochene Vorgänge|Keine Dimensionen|
|JobAUEndedFailure|Ja|Fehlgeschlagene AU-Zeit|Sekunden|Gesamt|Die Gesamt-AU-Zeit für fehlgeschlagene Vorgänge|Keine Dimensionen|
|JobAUEndedSuccess|Ja|Erfolgreiche AU-Zeit|Sekunden|Gesamt|Die Gesamt-AU-Zeit für erfolgreiche Vorgänge|Keine Dimensionen|
|JobEndedCancelled|Ja|Abgebrochene Vorgänge|Anzahl|Gesamt|Die Anzahl der abgebrochenen Vorgänge|Keine Dimensionen|
|JobEndedFailure|Ja|Fehlgeschlagene Vorgänge|Anzahl|Gesamt|Die Anzahl der fehlgeschlagenen Vorgänge|Keine Dimensionen|
|JobEndedSuccess|Ja|Erfolgreiche Vorgänge|Anzahl|Gesamt|Die Anzahl der erfolgreichen Vorgänge|Keine Dimensionen|
|JobStage|Ja|Aufträge in Phase|Anzahl|Gesamt|Anzahl von Aufträgen in den einzelnen Phasen.|Keine Dimensionen|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DataRead|Ja|Gelesene Daten|Byte|Gesamt|Gesamtmenge der im Konto gelesenen Daten|Keine Dimensionen|
|DataWritten|Ja|Geschriebene Daten|Byte|Gesamt|Gesamtmenge der in das Konto geschriebenen Daten|Keine Dimensionen|
|ReadRequests|Ja|Leseanforderungen|Anzahl|Gesamt|Anzahl der Datenleseanforderungen für das Konto|Keine Dimensionen|
|TotalStorage|Ja|Gesamtspeicher|Byte|Maximum|Gesamtmenge der im Konto gespeicherten Daten|Keine Dimensionen|
|WriteRequests|Ja|Schreibanforderungen|Anzahl|Gesamt|Anzahl der Datenschreibanforderungen für das Konto|Keine Dimensionen|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Ja|Fehler bei empfangenen Freigaben (Momentaufnahmen)|Anzahl|Anzahl|Anzahl von Fehlern bei empfangenen Freigaben (Momentaufnahmen) im Konto|Keine Dimensionen|
|FailedShareSynchronizations|Ja|Fehler bei gesendeten Freigaben (Momentaufnahmen)|Anzahl|Anzahl|Anzahl von Fehlern bei gesendeten Freigaben (Momentaufnahmen) im Konto|Keine Dimensionen|
|ShareCount|Ja|Gesendete Freigaben|Anzahl|Maximum|Anzahl von gesendeten Freigaben im Konto|ShareName|
|ShareSubscriptionCount|Ja|Empfangene Freigaben|Anzahl|Maximum|Anzahl von empfangenen Freigaben im Konto|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Ja|Erfolgreich empfangene Freigaben (Momentaufnahmen)|Anzahl|Anzahl|Anzahl von erfolgreich empfangenen Freigaben (Momentaufnahmen) im Konto|Keine Dimensionen|
|SucceededShareSynchronizations|Ja|Erfolgreich gesendete Freigaben (Momentaufnahmen)|Anzahl|Anzahl|Anzahl von erfolgreich gesendeten Freigaben (Momentaufnahmen) im Konto|Keine Dimensionen|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Average|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|seconds_behind_master|Ja|Replikationsverzögerung in Sekunden|Anzahl|Maximum|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|serverlog_storage_limit|Ja|Begrenzung des Serverprotokollspeichers|Byte|Maximum|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|serverlog_storage_percent|Ja|Serverprotokollspeicher in Prozent|Percent|Average|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Ja|Verwendeter Serverprotokollspeicher|Byte|Average|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|aborted_connections|Ja|Abgebrochene Verbindungen|Anzahl|Gesamt|Abgebrochene Verbindungen|Keine Dimensionen|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Maximum|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Maximum|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|cpu_credits_consumed|Ja|Verbrauchte CPU-Guthaben|Anzahl|Maximum|Verbrauchte CPU-Guthaben|Keine Dimensionen|
|cpu_credits_remaining|Ja|Verbleibende CPU-Guthaben|Anzahl|Maximum|Verbleibende CPU-Guthaben|Keine Dimensionen|
|cpu_percent|Ja|Host-CPU in Prozent|Percent|Maximum|Host-CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Maximum|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Hostarbeitsspeicher in Prozent|Percent|Maximum|Hostarbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Hostnetzwerk ausgehend|Byte|Gesamt|Aus Hostnetwork ausgehende Daten in Bytes|Keine Dimensionen|
|network_bytes_ingress|Ja|Hostnetzwerk eingehend|Byte|Gesamt|In Hostnetwork eingehende Daten in Bytes|Keine Dimensionen|
|Abfragen|Ja|Abfragen|Anzahl|Gesamt|Abfragen|Keine Dimensionen|
|replication_lag|Ja|Replikationsverzögerung in Sekunden|Sekunden|Maximum|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Maximum|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Maximum|Verwendeter Speicher|Keine Dimensionen|
|total_connections|Ja|Verbindungen insgesamt|Anzahl|Gesamt|Verbindungen insgesamt|Keine Dimensionen|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Average|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|seconds_behind_master|Ja|Replikationsverzögerung in Sekunden|Anzahl|Maximum|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|serverlog_storage_limit|Ja|Begrenzung des Serverprotokollspeichers|Byte|Maximum|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|serverlog_storage_percent|Ja|Serverprotokollspeicher in Prozent|Percent|Average|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Ja|Verwendeter Serverprotokollspeicher|Byte|Average|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|connections_succeeded|Ja|Erfolgreiche Verbindungen|Anzahl|Gesamt|Erfolgreiche Verbindungen|Keine Dimensionen|
|cpu_credits_consumed|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die vom Datenbankserver verwendet werden|Keine Dimensionen|
|cpu_credits_remaining|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die für den Burst verfügbar sind|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|disk_queue_depth|Ja|Warteschlangentiefe für Datenträger|Anzahl|Average|Anzahl offener E/A-Vorgänge für den Datenträger|Keine Dimensionen|
|iops|Ja|IOPS|Anzahl|Average|E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|maximum_used_transactionIDs|Ja|Maximale Anzahl von verwendeten Transaktions-IDs|Anzahl|Average|Maximale Anzahl von verwendeten Transaktions-IDs|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|read_iops|Ja|Lese-IOPS|Anzahl|Average|Anzahl der E/A-Lesevorgänge für den Datenträger pro Sekunde|Keine Dimensionen|
|read_throughput|Ja|Lesedurchsatz Bytes/Sekunde|Anzahl|Average|Vom Datenträger während des Überwachungszeitraums pro Sekunde gelesene Bytes|Keine Dimensionen|
|storage_free|Ja|Freier Speicher|Byte|Average|Freier Speicher|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|
|txlogs_storage_used|Ja|Verwendeter Transaktionsprotokollspeicher|Byte|Average|Verwendeter Transaktionsprotokollspeicher|Keine Dimensionen|
|write_iops|Ja|Schreib-IOPS|Anzahl|Average|Anzahl der E/A-Schreibvorgänge für den Datenträger pro Sekunde|Keine Dimensionen|
|write_throughput|Ja|Schreibdurchsatz Bytes/Sekunde|Anzahl|Average|Während des Überwachungszeitraums auf den Datenträger pro Sekunde geschriebene Bytes|Keine Dimensionen|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|ServerName|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|ServerName|
|iops|Ja|IOPS|Anzahl|Average|E/A-Vorgänge pro Sekunde|ServerName|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|ServerName|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|ServerName|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|ServerName|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|ServerName|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Average|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|pg_replica_log_delay_in_bytes|Ja|Maximale Verzögerung zwischen Replikaten|Byte|Maximum|Verzögerung in Byte beim Replikat mit der größten Verzögerung|Keine Dimensionen|
|pg_replica_log_delay_in_seconds|Ja|Replikatverzögerung|Sekunden|Maximum|Replikatverzögerung in Sekunden|Keine Dimensionen|
|serverlog_storage_limit|Ja|Begrenzung des Serverprotokollspeichers|Byte|Maximum|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|serverlog_storage_percent|Ja|Serverprotokollspeicher in Prozent|Percent|Average|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Ja|Verwendeter Serverprotokollspeicher|Byte|Average|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|iops|Ja|IOPS|Anzahl|Average|E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Ja|Angeforderte Nutzungsrate|Percent|Average|Angeforderte Nutzungsrate|Keine Dimensionen|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Ja|Abgebrochene C2D-Nachrichten|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgebrochen wurden|Keine Dimensionen|
|c2d.commands.egress.complete.success|Ja|Abgeschlossene C2D-Nachrichtenübermittlungen|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichtenübermittlungen, die vom Gerät erfolgreich abgeschlossen wurden|Keine Dimensionen|
|c2d.commands.egress.reject.success|Ja|Abgelehnte C2D-Nachrichten|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgelehnt wurden|Keine Dimensionen|
|c2d.methods.failure|Ja|Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe nicht erfolgreich waren.|Keine Dimensionen|
|c2d.methods.requestSize|Ja|Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)|Byte|Average|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen direkten Methodenaufrufe an.|Keine Dimensionen|
|c2d.methods.responseSize|Ja|Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)|Byte|Average|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Antworten für die direkte Methode an.|Keine Dimensionen|
|c2d.methods.success|Ja|Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe erfolgreich durchgeführt wurden.|Keine Dimensionen|
|c2d.twin.read.failure|Ja|Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.size|Ja|Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.success|Ja|Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.failure|Ja|Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.size|Ja|Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)|Byte|Average|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.success|Ja|Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|C2DMessagesExpired|Ja|Abgelaufene C2D-Nachrichten|Anzahl|Gesamt|Anzahl von abgelaufenen Cloud-zu-Gerät-Nachrichten|Keine Dimensionen|
|Konfigurationen|Ja|Konfigurationsmetriken|Anzahl|Gesamt|Metriken für Konfigurationsvorgänge|Keine Dimensionen|
|connectedDeviceCount|Ja|Verbundene Geräte|Anzahl|Average|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|d2c.endpoints.egress.builtIn.events|Ja|Routing: An Nachrichten/Ereignisse übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an den integrierter Endpunkt (Nachrichten/Ereignisse) übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.eventHubs|Ja|Routing: An Event Hub übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Event Hub-Endpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusQueues|Ja|Routing: An Service Bus-Warteschlange übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Warteschlangenendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusTopics|Ja|Routing: An Service Bus-Thema übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Themaendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage|Ja|Routing: An den Speicher übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage.blobs|Ja|Routing: An den Speicher übermittelte Blobs|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Blobs an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage.bytes|Ja|Routing: An den Speicher übermittelte Daten|Byte|Gesamt|Die Datenmenge (Bytes), die das IoT Hub-Routing an die Speicherendpunkte übermittelt.|Keine Dimensionen|
|d2c.endpoints.latency.builtIn.events|Ja|Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Telemetrienachricht beim integrierten Endpunkt (Nachrichten/Ereignisse).|Keine Dimensionen|
|d2c.endpoints.latency.eventHubs|Ja|Routing: Nachrichtenwartezeit für Event Hub|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Event Hub-Endpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusQueues|Ja|Routing: Nachrichtenwartezeit für Service Bus-Warteschlange|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Warteschlangenendpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusTopics|Ja|Routing: Nachrichtenwartezeit für Service Bus-Thema|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Themaendpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.storage|Ja|Routing: Nachrichtenwartezeit für Speicher|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Speicherendpunkt.|Keine Dimensionen|
|d2c.telemetry.egress.dropped|Ja|Routing: Verworfene Telemetrienachrichten |Anzahl|Gesamt|Die Anzahl der Nachrichten, die vom IoT Hub-Routing aufgrund von inaktiven Endpunkten gelöscht wurden. Dieser Wert zählt nicht die Nachrichten, die an die Fallbackroute übermittelt werden, da gelöschte Nachrichten dort nicht übermittelt werden.|Keine Dimensionen|
|d2c.telemetry.egress.fallback|Ja|Routing: An den Fallback übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten an den mit der Fallbackroute verbundenen Endpunkt übermittelt hat.|Keine Dimensionen|
|d2c.telemetry.egress.invalid|Ja|Routing: Nicht kompatible Telemetrienachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten aufgrund einer Inkompatibilität mit dem Endpunkt nicht übermitteln konnte. Dieser Wert umfasst keine Wiederholungen.|Keine Dimensionen|
|d2c.telemetry.egress.orphaned|Ja|Routing: Verwaiste Telemetrienachrichten |Anzahl|Gesamt|Die Häufigkeit, mit der Nachrichten durch das IoT Hub-Routing verwaist wurden, da sie mit keinen Routingregeln (einschließlich der Fallbackregel) übereinstimmten. |Keine Dimensionen|
|d2c.telemetry.egress.success|Ja|Routing: Übermittelte Telemetrienachrichten|Anzahl|Gesamt|Die Anzahl der erfolgreichen Nachrichtenübermittlungen an alle Endpunkte über das IoT Hub-Routing Wenn eine Nachricht an mehrere Endpunkte weitergeleitet wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins Wenn eine Nachricht mehrmals an denselben Endpunkt übermittelt wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins|Keine Dimensionen|
|d2c.telemetry.Ingress.allProtocol|Ja|Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die an Ihren IoT Hub gesendet werden sollten|Keine Dimensionen|
|d2c.telemetry.ingress.sendThrottle|Ja|Anzahl von Drosselungsfehlern|Anzahl|Gesamt|Anzahl von Drosselungsfehlern aufgrund von Drosselungen des Gerätedurchsatzes|Keine Dimensionen|
|d2c.telemetry.ingress.success|Ja|Telemetry messages sent (Gesendete Telemetrienachrichten)|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die erfolgreich an Ihren IoT Hub gesendet wurden|Keine Dimensionen|
|d2c.twin.read.failure|Ja|Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.size|Ja|Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.success|Ja|Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.failure|Ja|Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.size|Ja|Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)|Byte|Average|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.success|Ja|Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|dailyMessageQuotaUsed|Ja|Gesamtzahl verwendeter Nachrichten|Anzahl|Maximum|Gesamtzahl der heute verwendeten Nachrichten|Keine Dimensionen|
|deviceDataUsage|Ja|Gerätedatennutzung gesamt|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|deviceDataUsageV2|Ja|Gerätedatennutzung gesamt (Vorschau)|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|devices.connectedDevices.allProtocol|Ja|Verbundene Geräte (veraltet) |Anzahl|Gesamt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|devices.totalDevices|Ja|Geräte gesamt (veraltet)|Anzahl|Gesamt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|EventGridDeliveries|Ja|Event Grid-Übermittlungen|Anzahl|Gesamt|Die Anzahl von IoT Hub-Ereignissen, die in Event Grid veröffentlicht werden. Verwenden Sie die Dimension „Result“ für die Anzahl der erfolgreichen und fehlerhaften Anforderungen. Die „EventType“-Dimension zeigt den Typ des Ereignisses an(https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|Ja|Event Grid-Wartezeit|Millisekunden|Average|Die durchschnittliche Wartezeit (in Millisekunden) für den Zeitraum zwischen der Generierung des Iot Hub-Ereignisses und der Veröffentlichung des Ereignisses in Event Grid. Diese Zahl ist ein Durchschnittswert für alle Ereignistypen. Verwenden Sie die Dimension „EventType“, um die Wartezeit für einen bestimmten Ereignistyp anzuzeigen.|EventType|
|jobs.cancelJob.failure|Ja|Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.cancelJob.success|Ja|Successful job cancellations (Erfolgreiche Auftragsabbrüche)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.completed|Ja|Abgeschlossene Aufträge|Anzahl|Gesamt|Gibt die Anzahl von abgeschlossenen Aufträgen an.|Keine Dimensionen|
|jobs.createDirectMethodJob.failure|Ja|Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createDirectMethodJob.success|Ja|Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.failure|Ja|Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von allen nicht erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.success|Ja|Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.failed|Ja|Fehlerhafte Aufträge|Anzahl|Gesamt|Gibt die Anzahl aller fehlerhaften Aufträge an.|Keine Dimensionen|
|jobs.listJobs.failure|Ja|Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.listJobs.success|Ja|Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.failure|Ja|Failed job queries (Nicht erfolgreiche Auftragsabfragen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.success|Ja|Successful job queries (Erfolgreiche Auftragsabfragen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|RoutingDataSizeInBytesDelivered|Ja|Größe der Routingzustellungsnachrichten in Byte (Vorschau)|Byte|Gesamt|Die Gesamtgröße der Nachrichten in Byte, die von IoT Hub an einen Endpunkt übermittelt werden: Sie können die Dimensionen EndpointName und EndpointType verwenden, um die Größe der Nachrichten in Byte anzuzeigen, die verschiedenen Endpunkten zugestellt wurden. Der Metrikwert wird für jede zugestellte Nachricht erhöht. Das gilt auch, wenn die Nachricht an mehrere Endpunkte oder mehrfach an denselben Endpunkt übermittelt wird.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Ja|Routingübermittlungen (Vorschau)|Anzahl|Gesamt|Gibt an, wie oft IoT Hub versucht hat, Nachrichten per Routing an alle Endpunkte zu übermitteln. Verwenden Sie die Dimension „Result“, um die Anzahl von erfolgreichen Versuchen bzw. die Anzahl von Versuchen mit Fehlern abzurufen. Der Grund für einen Fehler (z. B. ungültig, verworfen oder verwaist) kann mit der Dimension „FailureReasonCategory“ abgerufen werden. Sie können auch die Dimensionen „EndpointName“ und „EndpointType“ verwenden, um Informationen dazu zu erhalten, wie viele Nachrichten an Ihre verschiedenen Endpunkte übermittelt wurden. Der Metrikwert wird für jeden Übermittlungsversuch um 1 erhöht. Dies gilt auch, wenn die Nachricht an mehrere Endpunkte oder mehrfach an denselben Endpunkt übermittelt wird.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Ja|Routingübermittlungslatenz (Vorschau)|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht bei IoT Hub und dem Eingang der Telemetrienachricht bei einem Endpunkt. Sie können auch die Dimensionen „EndpointName“ und „EndpointType“ verwenden, um Informationen zu den Latenzzeiten zu Ihren verschiedenen Endpunkten zu erhalten.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Ja|Angeforderte Nutzungsrate|Percent|Average|Angeforderte Nutzungsrate|Keine Dimensionen|
|totalDeviceCount|Ja|Total devices (Geräte gesamt)|Anzahl|Average|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|twinQueries.failure|Ja|Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries.resultSize|Ja|Twin queries result size (Ergebnisgröße von Zwillingsabfragen)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert der Ergebnisgröße aller erfolgreichen Zwillingsabfragen.|Keine Dimensionen|
|twinQueries.success|Ja|Successful twin queries (Erfolgreiche Zwillingsabfragen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Ja|Abgebrochene C2D-Nachrichten|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgebrochen wurden|Keine Dimensionen|
|c2d.commands.egress.complete.success|Ja|Abgeschlossene C2D-Nachrichtenübermittlungen|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichtenübermittlungen, die vom Gerät erfolgreich abgeschlossen wurden|Keine Dimensionen|
|c2d.commands.egress.reject.success|Ja|Abgelehnte C2D-Nachrichten|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgelehnt wurden|Keine Dimensionen|
|c2d.methods.failure|Ja|Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe nicht erfolgreich waren.|Keine Dimensionen|
|c2d.methods.requestSize|Ja|Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)|Byte|Average|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen direkten Methodenaufrufe an.|Keine Dimensionen|
|c2d.methods.responseSize|Ja|Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)|Byte|Average|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Antworten für die direkte Methode an.|Keine Dimensionen|
|c2d.methods.success|Ja|Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe erfolgreich durchgeführt wurden.|Keine Dimensionen|
|c2d.twin.read.failure|Ja|Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.size|Ja|Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.success|Ja|Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.failure|Ja|Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.size|Ja|Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)|Byte|Average|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.success|Ja|Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|C2DMessagesExpired|Ja|Abgelaufene C2D-Nachrichten|Anzahl|Gesamt|Anzahl von abgelaufenen Cloud-zu-Gerät-Nachrichten|Keine Dimensionen|
|Konfigurationen|Ja|Konfigurationsmetriken|Anzahl|Gesamt|Metriken für Konfigurationsvorgänge|Keine Dimensionen|
|connectedDeviceCount|Nein|Verbundene Geräte|Anzahl|Average|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|d2c.endpoints.egress.builtIn.events|Ja|Routing: An Nachrichten/Ereignisse übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an den integrierter Endpunkt (Nachrichten/Ereignisse) übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.eventHubs|Ja|Routing: An Event Hub übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Event Hub-Endpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusQueues|Ja|Routing: An Service Bus-Warteschlange übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Warteschlangenendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusTopics|Ja|Routing: An Service Bus-Thema übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Themaendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage|Ja|Routing: An den Speicher übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage.blobs|Ja|Routing: An den Speicher übermittelte Blobs|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Blobs an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage.bytes|Ja|Routing: An den Speicher übermittelte Daten|Byte|Gesamt|Die Datenmenge (Bytes), die das IoT Hub-Routing an die Speicherendpunkte übermittelt.|Keine Dimensionen|
|d2c.endpoints.latency.builtIn.events|Ja|Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Telemetrienachricht beim integrierten Endpunkt (Nachrichten/Ereignisse).|Keine Dimensionen|
|d2c.endpoints.latency.eventHubs|Ja|Routing: Nachrichtenwartezeit für Event Hub|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Event Hub-Endpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusQueues|Ja|Routing: Nachrichtenwartezeit für Service Bus-Warteschlange|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Warteschlangenendpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusTopics|Ja|Routing: Nachrichtenwartezeit für Service Bus-Thema|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Themaendpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.storage|Ja|Routing: Nachrichtenwartezeit für Speicher|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Speicherendpunkt.|Keine Dimensionen|
|d2c.telemetry.egress.dropped|Ja|Routing: Verworfene Telemetrienachrichten |Anzahl|Gesamt|Die Anzahl der Nachrichten, die vom IoT Hub-Routing aufgrund von inaktiven Endpunkten gelöscht wurden. Dieser Wert zählt nicht die Nachrichten, die an die Fallbackroute übermittelt werden, da gelöschte Nachrichten dort nicht übermittelt werden.|Keine Dimensionen|
|d2c.telemetry.egress.fallback|Ja|Routing: An den Fallback übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten an den mit der Fallbackroute verbundenen Endpunkt übermittelt hat.|Keine Dimensionen|
|d2c.telemetry.egress.invalid|Ja|Routing: Nicht kompatible Telemetrienachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten aufgrund einer Inkompatibilität mit dem Endpunkt nicht übermitteln konnte. Dieser Wert umfasst keine Wiederholungen.|Keine Dimensionen|
|d2c.telemetry.egress.orphaned|Ja|Routing: Verwaiste Telemetrienachrichten |Anzahl|Gesamt|Die Häufigkeit, mit der Nachrichten durch das IoT Hub-Routing verwaist wurden, da sie mit keinen Routingregeln (einschließlich der Fallbackregel) übereinstimmten. |Keine Dimensionen|
|d2c.telemetry.egress.success|Ja|Routing: Übermittelte Telemetrienachrichten|Anzahl|Gesamt|Die Anzahl der erfolgreichen Nachrichtenübermittlungen an alle Endpunkte über das IoT Hub-Routing Wenn eine Nachricht an mehrere Endpunkte weitergeleitet wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins Wenn eine Nachricht mehrmals an denselben Endpunkt übermittelt wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins|Keine Dimensionen|
|d2c.telemetry.Ingress.allProtocol|Ja|Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die an Ihren IoT Hub gesendet werden sollten|Keine Dimensionen|
|d2c.telemetry.ingress.sendThrottle|Ja|Anzahl von Drosselungsfehlern|Anzahl|Gesamt|Anzahl von Drosselungsfehlern aufgrund von Drosselungen des Gerätedurchsatzes|Keine Dimensionen|
|d2c.telemetry.ingress.success|Ja|Telemetry messages sent (Gesendete Telemetrienachrichten)|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die erfolgreich an Ihren IoT Hub gesendet wurden|Keine Dimensionen|
|d2c.twin.read.failure|Ja|Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.size|Ja|Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.success|Ja|Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.failure|Ja|Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.size|Ja|Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)|Byte|Average|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.success|Ja|Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|dailyMessageQuotaUsed|Ja|Gesamtzahl verwendeter Nachrichten|Anzahl|Maximum|Gesamtzahl der heute verwendeten Nachrichten|Keine Dimensionen|
|deviceDataUsage|Ja|Gerätedatennutzung gesamt|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|deviceDataUsageV2|Ja|Gerätedatennutzung gesamt (Vorschau)|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|devices.connectedDevices.allProtocol|Ja|Verbundene Geräte (veraltet) |Anzahl|Gesamt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|devices.totalDevices|Ja|Geräte gesamt (veraltet)|Anzahl|Gesamt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|EventGridDeliveries|Ja|Event Grid-Übermittlungen|Anzahl|Gesamt|Die Anzahl von IoT Hub-Ereignissen, die in Event Grid veröffentlicht werden. Verwenden Sie die Dimension „Result“ für die Anzahl der erfolgreichen und fehlerhaften Anforderungen. Die „EventType“-Dimension zeigt den Typ des Ereignisses an(https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|Ja|Event Grid-Wartezeit|Millisekunden|Average|Die durchschnittliche Wartezeit (in Millisekunden) für den Zeitraum zwischen der Generierung des Iot Hub-Ereignisses und der Veröffentlichung des Ereignisses in Event Grid. Diese Zahl ist ein Durchschnittswert für alle Ereignistypen. Verwenden Sie die Dimension „EventType“, um die Wartezeit für einen bestimmten Ereignistyp anzuzeigen.|EventType|
|jobs.cancelJob.failure|Ja|Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.cancelJob.success|Ja|Successful job cancellations (Erfolgreiche Auftragsabbrüche)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.completed|Ja|Abgeschlossene Aufträge|Anzahl|Gesamt|Gibt die Anzahl von abgeschlossenen Aufträgen an.|Keine Dimensionen|
|jobs.createDirectMethodJob.failure|Ja|Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createDirectMethodJob.success|Ja|Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.failure|Ja|Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von allen nicht erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.success|Ja|Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.failed|Ja|Fehlerhafte Aufträge|Anzahl|Gesamt|Gibt die Anzahl aller fehlerhaften Aufträge an.|Keine Dimensionen|
|jobs.listJobs.failure|Ja|Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.listJobs.success|Ja|Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.failure|Ja|Failed job queries (Nicht erfolgreiche Auftragsabfragen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.success|Ja|Successful job queries (Erfolgreiche Auftragsabfragen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|RoutingDataSizeInBytesDelivered|Ja|Größe der Routingzustellungsnachrichten in Byte (Vorschau)|Byte|Gesamt|Die Gesamtgröße der Nachrichten in Byte, die von IoT Hub an einen Endpunkt übermittelt werden: Sie können die Dimensionen EndpointName und EndpointType verwenden, um die Größe der Nachrichten in Byte anzuzeigen, die verschiedenen Endpunkten zugestellt wurden. Der Metrikwert wird für jede zugestellte Nachricht erhöht. Das gilt auch, wenn die Nachricht an mehrere Endpunkte oder mehrfach an denselben Endpunkt übermittelt wird.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Ja|Routingübermittlungen (Vorschau)|Anzahl|Gesamt|Gibt an, wie oft IoT Hub versucht hat, Nachrichten per Routing an alle Endpunkte zu übermitteln. Verwenden Sie die Dimension „Result“, um die Anzahl von erfolgreichen Versuchen bzw. die Anzahl von Versuchen mit Fehlern abzurufen. Der Grund für einen Fehler (z. B. ungültig, verworfen oder verwaist) kann mit der Dimension „FailureReasonCategory“ abgerufen werden. Sie können auch die Dimensionen „EndpointName“ und „EndpointType“ verwenden, um Informationen dazu zu erhalten, wie viele Nachrichten an Ihre verschiedenen Endpunkte übermittelt wurden. Der Metrikwert wird für jeden Übermittlungsversuch um 1 erhöht. Dies gilt auch, wenn die Nachricht an mehrere Endpunkte oder mehrfach an denselben Endpunkt übermittelt wird.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Ja|Routingübermittlungslatenz (Vorschau)|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht bei IoT Hub und dem Eingang der Telemetrienachricht bei einem Endpunkt. Sie können auch die Dimensionen „EndpointName“ und „EndpointType“ verwenden, um Informationen zu den Latenzzeiten zu Ihren verschiedenen Endpunkten zu erhalten.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|Nein|Total devices (Geräte gesamt)|Anzahl|Average|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|twinQueries.failure|Ja|Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries.resultSize|Ja|Twin queries result size (Ergebnisgröße von Zwillingsabfragen)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert der Ergebnisgröße aller erfolgreichen Zwillingsabfragen.|Keine Dimensionen|
|twinQueries.success|Ja|Successful twin queries (Erfolgreiche Zwillingsabfragen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AttestationAttempts|Ja|Nachweisversuche|Anzahl|Gesamt|Anzahl von versuchten Gerätenachweisen|ProvisioningServiceName, Status, Protokoll|
|DeviceAssignments|Ja|Zugewiesene Geräte|Anzahl|Gesamt|Anzahl von Geräten, die einem IoT-Hub zugewiesen sind|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Ja|Registrierungsversuche|Anzahl|Gesamt|Anzahl von versuchten Geräteregistrierungen|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ApiRequests|Ja|API-Anforderungen|Anzahl|Gesamt|Die Anzahl der API-Anforderungen, die für Lese-, Schreib-, Lösch- und Abfragevorgänge für Digital Twins durchgeführt wurden.|Vorgang, Authentifizierung, Protokoll, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Ja|API-Anforderungsfehlerrate|Percent|Average|Der Prozentsatz der API-Anforderungen, die der Dienst für Ihre Instanz erhält und die einen internen Fehler (500) als Antwortcode für Lese-, Schreib-, Lösch- und Abfragevorgänge von Digital Twins zurückgeben.|Vorgang, Authentifizierung, Protokoll|
|ApiRequestsLatency|Ja|API-Anforderungslatenz|Millisekunden|Average|Die Antwortzeit für API-Anforderungen, d. h. vom Eingang der Anforderung bei Azure Digital Twins und dem Zeitpunkt, zu dem der Dienst ein Erfolgs- bzw. Fehlerergebnis für Lese-, Schreib-, Lösch- und Abfragevorgänge von Digital Twins sendet.|Vorgang, Authentifizierung, Protokoll, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Ja|API-Abrechnungsvorgänge|Anzahl|Gesamt|Abrechnungsmetrik für die Anzahl aller API-Anforderungen, die für den Azure Digital Twins-Dienst durchgeführt wurden.|MeterId|
|BillingMessagesProcessed|Ja|Verarbeitete Abrechnungsnachrichten|Anzahl|Gesamt|Abrechnungsmetrik für die Anzahl von Nachrichten, die von Azure Digital Twins Zwillingen an externe Endpunkte gesendet werden.|MeterId|
|BillingQueryUnits|Ja|Abrechnungsabfrageeinheiten|Anzahl|Gesamt|Die Anzahl der Abfrageeinheiten (ein intern berechnetes Measure der Dienstressourcennutzung), die zum Ausführen von Abfragen genutzt werden.|MeterId|
|IngressEvents|Ja|Eingangsereignisse|Anzahl|Gesamt|Die Anzahl der in Azure Digital Twins eingehenden Telemetrieereignisse.|Ergebnis|
|IngressEventsFailureRate|Ja|Eingangsereignis-Fehlerrate|Percent|Average|Der Prozentsatz der eingehenden Telemetrieereignisse, bei denen der Dienst einen internen Fehlerantwortcode (500) zurückgibt.|Keine Dimensionen|
|IngressEventsLatency|Ja|Latenz von Eingangsereignissen|Millisekunden|Average|Die Zeit zwischen dem Eintreffen eines Ereignisses und dem Zeitpunkt, zu dem es von Azure Digital Twins ausgegeben werden kann, wobei der Dienst ein Erfolgs-/Fehlerergebnis sendet.|Ergebnis|
|ModelCount|Ja|Modellanzahl|Anzahl|Gesamt|Hierbei handelt es sich um die Gesamtanzahl von Modellen in einer Azure Digital Twins-Instanz. Verwenden Sie diese Metrik, um zu bestimmen, ob Sie sich dem Dienstlimit für die maximal pro Instanz zulässige Anzahl an Modellen nähern.|Keine Dimensionen|
|Routing|Ja|Weitergeleitete Nachrichten|Anzahl|Gesamt|Die Anzahl der Nachrichten, die an einen Azure-Endpunktdienst (z. B. Event Hub, Service Bus oder Event Grid) weitergeleitet werden.|EndpointType, Ergebnis|
|RoutingFailureRate|Ja|Routingfehlerrate|Percent|Average|Der Prozentsatz der Ereignisse, die zu einem Fehler führen, wenn sie von Azure Digital Twins zu einem Azure-Endpunktdienst wie Event Hub, Service Bus oder Event Grid weitergeleitet werden.|EndpointType|
|RoutingLatency|Ja|Routinglatenz|Millisekunden|Average|Die Zeit, die zwischen der Weiterleitung eines Ereignisses von Azure Digital Twins bis zu dem Zeitpunkt verstrichen ist, an dem es an den Azure-Endpunktdienst wie Event Hub, Service Bus oder Event Grid gesendet wird.|EndpointType, Ergebnis|
|TwinCount|Ja|Anzahl der Zwillinge|Anzahl|Gesamt|Hierbei handelt es sich um die Gesamtanzahl von Zwillingen in einer Azure Digital Twins-Instanz. Verwenden Sie diese Metrik, um zu bestimmen, ob Sie sich dem Dienstlimit für die maximal pro Instanz zulässige Anzahl an Zwillingen nähern.|Keine Dimensionen|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/DatabaseAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AddRegion|Ja|Region hinzugefügt|Anzahl|Anzahl|Region hinzugefügt|Region|
|AutoscaleMaxThroughput|Nein|Autoskalierung – Maximaler Durchsatz|Anzahl|Maximum|Maximaler Durchsatz für Autoskalierung|DatabaseName, CollectionName|
|AvailableStorage|Nein|Verfügbarer Speicher (veraltet)|Byte|Gesamt|Die Metrik „Verfügbarer Speicher“ wird Ende September 2023 aus Azure Monitor entfernt. Die Speichergröße für Cosmos DB-Sammlungen ist jetzt unbegrenzt. Die einzige Einschränkung besteht darin, dass die Speichergröße für jeden logischen Partitionsschlüssel 20 GB beträgt. Sie können PartitionKeyStatistics im Diagnoseprotokoll aktivieren, um den Speicherverbrauch der wichtigsten Partitionsschlüssel zu ermitteln. Weitere Informationen zum Speicherkontingent für Cosmos DB finden Sie in diesem Dokument [https://docs.microsoft.com/azure/cosmos-db/concepts-limits](../../cosmos-db/concepts-limits.md). Nach diesem Datum werden verbleibende Warnungsregeln, die noch für die veraltete Metrik definiert sind, automatisch deaktiviert.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Nein|Abschluss von Cassandra-Verbindungen|Anzahl|Gesamt|Anzahl von Cassandra-Verbindungen, die geschlossen wurden, gemeldet mit einer Granularität von einer Minute|APIType, Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Nein|Cassandra-Connector – Durchschnittliche Replikationslatenz|Millisekunden|Average|Durchschnittliche Replikationslatenz im Cassandra-Connector|Keine Dimensionen|
|CassandraConnectorReplicationHealthStatus|Nein|Cassandra-Connector – Integritätsstatus|Anzahl|Anzahl|Integritätsstatus des Cassandra-Connectors|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|Nein|Cassandra-Keyspace erstellt|Anzahl|Anzahl|Cassandra-Keyspace erstellt|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CassandraKeyspaceDelete|Nein|Cassandra-Keyspace gelöscht|Anzahl|Anzahl|Cassandra-Keyspace gelöscht|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Nein|Durchsatz eines Cassandra-Keyspace aktualisiert|Anzahl|Anzahl|Durchsatz eines Cassandra-Keyspace aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Nein|Cassandra-Keyspace aktualisiert|Anzahl|Anzahl|Cassandra-Keyspace aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CassandraRequestCharges|Nein|Gebühren für Cassandra-Anforderungen|Anzahl|Gesamt|Genutzte Anforderungseinheiten für gesendete Cassandra-Anforderungen|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Nein|Cassandra-Anforderungen|Anzahl|Anzahl|Anzahl ausgeführter Cassandra-Anforderungen|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Nein|Cassandra-Tabelle erstellt|Anzahl|Anzahl|Cassandra-Tabelle erstellt|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CassandraTableDelete|Nein|Cassandra-Tabelle gelöscht|Anzahl|Anzahl|Cassandra-Tabelle gelöscht|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Nein|Durchsatz der Cassandra-Tabelle aktualisiert|Anzahl|Anzahl|Durchsatz der Cassandra-Tabelle aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Nein|Cassandra-Tabelle aktualisiert|Anzahl|Anzahl|Cassandra-Tabelle aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CreateAccount|Ja|Konto erstellt|Anzahl|Anzahl|Konto erstellt|Keine Dimensionen|
|DataUsage|Nein|Datennutzung|Byte|Gesamt|Gemeldete Gesamtdatennutzung mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|DedicatedGatewayAverageCPUUsage|Nein|DedicatedGatewayAverageCPUUsage|Percent|Average|Durchschnittliche CPU-Auslastung aller dedizierten Gatewayinstanzen|Region, MetricType|
|DedicatedGatewayAverageMemoryUsage|Nein|DedicatedGatewayAverageMemoryUsage|Byte|Average|Durchschnittliche Speicherauslastung aller dedizierten Gatewayinstanzen, die sowohl für das Weiterleiten von Anforderungen als auch das Zwischenspeichern von Daten verwendet wird|Region|
|DedicatedGatewayMaximumCPUUsage|Nein|DedicatedGatewayMaximumCPUUsage|Percent|Average|Durchschnittliche maximale CPU-Auslastung aller dedizierten Gatewayinstanzen|Region, MetricType|
|DedicatedGatewayRequests|Ja|DedicatedGatewayRequests|Anzahl|Anzahl|Anforderungen an das dedizierte Gateway|DatabaseName, CollectionName, CacheExercised, OperationName, Region|
|DeleteAccount|Ja|Konto gelöscht|Anzahl|Anzahl|Konto gelöscht|Keine Dimensionen|
|DocumentCount|Nein|Dokumentanzahl|Anzahl|Gesamt|Gemeldete Gesamtdokumentanzahl mit 5-Minuten-, 1-Stunde- und 1-Tag-Granularität|CollectionName, DatabaseName, Region|
|DocumentQuota|Nein|Dokumentenkontingent|Byte|Gesamt|Gemeldetes Gesamtspeicherkontingent mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|GremlinDatabaseCreate|Nein|Gremlin-Datenbank erstellt|Anzahl|Anzahl|Gremlin-Datenbank erstellt|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|GremlinDatabaseDelete|Nein|Gremlin-Datenbank gelöscht|Anzahl|Anzahl|Gremlin-Datenbank gelöscht|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Nein|Durchsatz der Gremlin-Datenbank aktualisiert|Anzahl|Anzahl|Durchsatz der Gremlin-Datenbank aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Nein|Gremlin-Datenbank aktualisiert|Anzahl|Anzahl|Gremlin-Datenbank aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|GremlinGraphCreate|Nein|Gremlin-Diagramm erstellt|Anzahl|Anzahl|Gremlin-Diagramm erstellt|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|GremlinGraphDelete|Nein|Gremlin-Diagramm gelöscht|Anzahl|Anzahl|Gremlin-Diagramm gelöscht|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Nein|Durchsatz des Gremlin-Diagramms aktualisiert|Anzahl|Anzahl|Durchsatz des Gremlin-Diagramms aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Nein|Gremlin-Diagramm aktualisiert|Anzahl|Anzahl|Gremlin-Diagramm aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|IndexUsage|Nein|Indexnutzung|Byte|Gesamt|Gemeldete Gesamtindexnutzung mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|IntegratedCacheEvictedEntriesSize|Nein|IntegratedCacheEvictedEntriesSize|Byte|Average|Größe der Einträge, die aus dem integrierten Cache entfernt wurden|Region|
|IntegratedCacheItemExpirationCount|Nein|IntegratedCacheItemExpirationCount|Anzahl|Average|Anzahl der Elemente, die aufgrund von TTL-Ablauf aus dem integrierten Cache entfernt wurden|Region, CacheEntryType|
|IntegratedCacheItemHitRate|Nein|IntegratedCacheItemHitRate|Percent|Average|Anzahl der Punktlesevorgänge, die den integrierten Cache verwendet haben, dividiert durch die Anzahl der Punktlesevorgänge, die über das dedizierte Gateway mit letztlicher Konsistenz weitergeleitet wurden|Region, CacheEntryType|
|IntegratedCacheQueryExpirationCount|Nein|IntegratedCacheQueryExpirationCount|Anzahl|Average|Anzahl der Abfragen, die aufgrund von TTL-Ablauf aus dem integrierten Cache entfernt wurden|Region, CacheEntryType|
|IntegratedCacheQueryHitRate|Nein|IntegratedCacheQueryHitRate|Percent|Average|Anzahl der Abfragen, die den integrierten Cache verwendet haben, dividiert durch die Anzahl der Abfragen, die über das dedizierte Gateway mit letztlicher Konsistenz weitergeleitet wurden|Region, CacheEntryType|
|MaterializedViewCatchupGapInMinutes|Nein|Aufhollücke bei materialisierten Sichten in Minuten|Anzahl|Maximum|Maximaler Zeitunterschied in Minuten zwischen Daten im Quellcontainer und an eine materialisierte Sicht propagierte Daten|Region, TargetContainerName, BuildType|
|MaterializedViewsBuilderAverageCPUUsage|Nein|Durchschnittliche CPU-Auslastung durch Generator für materialisierte Sichten|Percent|Average|Durchschnittliche CPU-Auslastung über Instanzen des Generators für materialisierte Sichten hinweg, die zum Auffüllen von Daten in materialisierten Sichten verwendet werden|Region, MetricType|
|MaterializedViewsBuilderAverageMemoryUsage|Nein|Arbeitsspeicherauslastung durch Generator für materialisierte Sichten|Byte|Average|Durchschnittliche Arbeitsspeicherauslastung über Instanzen des Generators für materialisierte Sichten hinweg, die zum Auffüllen von Daten in materialisierten Sichten verwendet werden|Region|
|MaterializedViewsBuilderMaximumCPUUsage|Nein|Maximale CPU-Auslastung durch Generator für materialisierte Sichten|Percent|Average|Durchschnittliche maximale CPU-Auslastung über Instanzen des Generators für materialisierte Sichten hinweg, die zum Auffüllen von Daten in materialisierten Sichten verwendet werden|Region, MetricType|
|MetadataRequests|Nein|Anforderungen von Metadaten|Anzahl|Anzahl|Anzahl der Metadatenanforderungen. Cosmos DB unterhält eine Sammlung von Systemmetadaten für jedes Konto, wodurch Sie Sammlungen, Datenbanken usw. und deren Konfigurationen ohne anfallende Kosten auflisten können.|DatabaseName, CollectionName, Region, StatusCode, Role|
|MongoCollectionCreate|Nein|Mongo-Sammlung erstellt|Anzahl|Anzahl|Mongo-Sammlung erstellt|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoCollectionDelete|Nein|Mongo-Sammlung gelöscht|Anzahl|Anzahl|Mongo-Sammlung gelöscht|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Nein|Durchsatz der Mongo-Sammlung aktualisiert|Anzahl|Anzahl|Durchsatz der Mongo-Sammlung aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Nein|Mongo-Sammlung aktualisiert|Anzahl|Anzahl|Mongo-Sammlung aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoDatabaseDelete|Nein|Mongo-Datenbank gelöscht|Anzahl|Anzahl|Mongo-Datenbank gelöscht|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Nein|Durchsatz der Mongo-Datenbank aktualisiert|Anzahl|Anzahl|Durchsatz der Mongo-Datenbank aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseCreate|Nein|Mongo-Datenbank erstellt|Anzahl|Anzahl|Mongo-Datenbank erstellt|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoDBDatabaseUpdate|Nein|Mongo-Datenbank aktualisiert|Anzahl|Anzahl|Mongo-Datenbank aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoRequestCharge|Ja|Kosten der Mongo-Anforderung|Anzahl|Gesamt|Verbrauchte Mongo-Anforderungseinheiten|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|Ja|Mongo-Anforderungen|Anzahl|Anzahl|Anzahl der ausgegebenen Mongo-Anforderungen|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|NormalizedRUConsumption|Nein|Normalisierter RU-Verbrauch|Percent|Maximum|Maximaler RU-Verbrauch in Prozent pro Minute|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|Nein|Bereitgestellter Durchsatz|Anzahl|Maximum|Bereitgestellter Durchsatz|DatabaseName, CollectionName|
|RegionFailover|Ja|Failover der Region|Anzahl|Anzahl|Failover der Region|Keine Dimensionen|
|RemoveRegion|Ja|Region entfernt|Anzahl|Anzahl|Region entfernt|Region|
|ReplicationLatency|Ja|P99-Replikationswartezeit|Millisekunden|Average|P99-Replikationswartezeit für Quell- und Zielregionen für geofähiges Konto|SourceRegion, TargetRegion|
|ServerSideLatency|Nein|Serverseitige Latenz|Millisekunden|Average|Serverseitige Latenz|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Nein|Dienstverfügbarkeit|Percent|Average|Verfügbarkeit der Kontoanforderungen mit einer Granularität von einer Stunde, einem Tag oder einem Monat|Keine Dimensionen|
|SqlContainerCreate|Nein|SQL-Container erstellt|Anzahl|Anzahl|SQL-Container erstellt|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|SqlContainerDelete|Nein|SQL-Container gelöscht|Anzahl|Anzahl|SQL-Container gelöscht|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|Nein|Durchsatz des SQL-Containers aktualisiert|Anzahl|Anzahl|Durchsatz des SQL-Containers aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|Nein|SQL-Container aktualisiert|Anzahl|Anzahl|SQL-Container aktualisiert|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|SqlDatabaseCreate|Nein|SQL-Datenbank erstellt|Anzahl|Anzahl|SQL-Datenbank erstellt|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|SqlDatabaseDelete|Nein|SQL-Datenbank gelöscht|Anzahl|Anzahl|SQL-Datenbank gelöscht|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|Nein|Durchsatz der SQL-Datenbank aktualisiert|Anzahl|Anzahl|Durchsatz der SQL-Datenbank aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|Nein|SQL-Datenbank aktualisiert|Anzahl|Anzahl|SQL-Datenbank aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|TableTableCreate|Nein|AzureTable-Tabelle erstellt|Anzahl|Anzahl|AzureTable-Tabelle erstellt|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|TableTableDelete|Nein|AzureTable-Tabelle gelöscht|Anzahl|Anzahl|AzureTable-Tabelle gelöscht|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|Nein|Durchsatz der AzureTable-Tabelle aktualisiert|Anzahl|Anzahl|Durchsatz der AzureTable-Tabelle aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|Nein|AzureTable-Tabelle aktualisiert|Anzahl|Anzahl|AzureTable-Tabelle aktualisiert|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|TotalRequests|Ja|Anzahl von Anforderungen|Anzahl|Anzahl|Anzahl von gesendeten Anforderungen|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|Ja|Anforderungseinheiten gesamt|Anzahl|Gesamt|Verbrauchte Anforderungseinheiten|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|Ja|Kontoschlüssel aktualisiert|Anzahl|Anzahl|Kontoschlüssel aktualisiert|KeyType|
|UpdateAccountNetworkSettings|Ja|Netzwerkeinstellungen für Konto aktualisiert|Anzahl|Anzahl|Netzwerkeinstellungen für Konto aktualisiert|Keine Dimensionen|
|UpdateAccountReplicationSettings|Ja|Replikationseinstellungen für Konto aktualisiert|Anzahl|Anzahl|Replikationseinstellungen für Konto aktualisiert|Keine Dimensionen|
|UpdateDiagnosticsSettings|Nein|Diagnoseeinstellungen des Kontos aktualisiert|Anzahl|Anzahl|Diagnoseeinstellungen des Kontos aktualisiert|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Erweiterte Filterauswertungen|Anzahl|Gesamt|Gesamtanzahl erweiterter Filter, die für dieses Thema in Ereignisabonnements ausgewertet werden.|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|Topic, ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Thema|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|Keine Dimensionen|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|Keine Dimensionen|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|Keine Dimensionen|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|EventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|EventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Erweiterte Filterauswertungen|Anzahl|Gesamt|Gesamtanzahl erweiterter Filter, die für dieses Thema in Ereignisabonnements ausgewertet werden.|EventSubscriptionName|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|EventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|EventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Erweiterte Filterauswertungen|Anzahl|Gesamt|Gesamtanzahl erweiterter Filter, die für dieses Thema in Ereignisabonnements ausgewertet werden.|EventSubscriptionName|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|EventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|EventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Ja|Erweiterte Filterauswertungen|Anzahl|Gesamt|Gesamtanzahl erweiterter Filter, die für dieses Thema in Ereignisabonnements ausgewertet werden.|EventSubscriptionName|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|EventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|EventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Average|Aktive Verbindungen gesamt für Microsoft.EventHub.|Keine Dimensionen|
|AvailableMemory|Nein|Verfügbarer Arbeitsspeicher|Percent|Maximum|Verfügbarer Arbeitsspeicher für den Event Hub-Cluster als Prozentsatz des Gesamtarbeitsspeichers.|Role|
|CaptureBacklog|Nein|Backlog erfassen.|Anzahl|Gesamt|Erfassen des Backlogs für Microsoft.EventHub.|Keine Dimensionen|
|CapturedBytes|Nein|Erfasste Bytes.|Byte|Gesamt|Erfasste Bytes für Microsoft.EventHub.|Keine Dimensionen|
|CapturedMessages|Nein|Erfasste Nachrichten.|Anzahl|Gesamt|Erfasste Nachrichten für Microsoft.EventHub.|Keine Dimensionen|
|ConnectionsClosed|Nein|Geschlossene Verbindungen.|Anzahl|Average|Geschlossene Verbindungen für Microsoft.EventHub.|Keine Dimensionen|
|ConnectionsOpened|Nein|Geöffnete Verbindungen.|Anzahl|Average|Geöffnete Verbindungen für Microsoft.EventHub.|Keine Dimensionen|
|CPU|Nein|CPU|Percent|Maximum|CPU-Auslastung für den Event Hub-Cluster in Prozent|Role|
|IncomingBytes|Ja|Eingehende Bytes.|Byte|Gesamt|Eingehende Bytes für Microsoft.EventHub.|Keine Dimensionen|
|IncomingMessages|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Eingehende Nachrichten für Microsoft.EventHub.|Keine Dimensionen|
|IncomingRequests|Ja|Eingehende Anforderungen|Anzahl|Gesamt|Eingehende Anforderungen für Microsoft.EventHub.|Keine Dimensionen|
|OutgoingBytes|Ja|Ausgehende Bytes.|Byte|Gesamt|Ausgehende Bytes für Microsoft.EventHub.|Keine Dimensionen|
|OutgoingMessages|Ja|Ausgehende Nachrichten|Anzahl|Gesamt|Ausgehende Nachrichten für Microsoft.EventHub.|Keine Dimensionen|
|QuotaExceededErrors|Nein|Fehler aufgrund von Kontingentüberschreitung.|Anzahl|Gesamt|Fehler aufgrund von Kontingentüberschreitung für Microsoft.EventHub.|OperationResult|
|ServerErrors|Nein|Serverfehler.|Anzahl|Gesamt|Serverfehler für Microsoft.EventHub.|OperationResult|
|Size|Nein|Size|Byte|Average|Größe eines EventHub in Bytes.|Role|
|SuccessfulRequests|Nein|Erfolgreiche Anforderungen|Anzahl|Gesamt|Erfolgreiche Anforderungen für Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Nein|Gedrosselte Anforderungen.|Anzahl|Gesamt|Gedrosselte Anforderungen für Microsoft.EventHub.|OperationResult|
|UserErrors|Nein|Benutzerfehler.|Anzahl|Gesamt|Benutzerfehler für Microsoft.EventHub.|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Average|Aktive Verbindungen gesamt für Microsoft.EventHub.|Keine Dimensionen|
|CaptureBacklog|Nein|Backlog erfassen.|Anzahl|Gesamt|Erfassen des Backlogs für Microsoft.EventHub.|EntityName|
|CapturedBytes|Nein|Erfasste Bytes.|Byte|Gesamt|Erfasste Bytes für Microsoft.EventHub.|EntityName|
|CapturedMessages|Nein|Erfasste Nachrichten.|Anzahl|Gesamt|Erfasste Nachrichten für Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Nein|Geschlossene Verbindungen.|Anzahl|Average|Geschlossene Verbindungen für Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Nein|Geöffnete Verbindungen.|Anzahl|Average|Geöffnete Verbindungen für Microsoft.EventHub.|EntityName|
|EHABL|Ja|Archivierte Backlognachrichten (veraltet)|Anzahl|Gesamt|Event Hub-Archivnachrichten im Backlog für einen Namespace (veraltet)|Keine Dimensionen|
|EHAMBS|Ja|Durchsatz von Archivnachrichten (veraltet)|Byte|Gesamt|Event Hub-Durchsatz archivierter Nachrichten in einem Namespace (veraltet)|Keine Dimensionen|
|EHAMSGS|Ja|Archivnachrichten (veraltet)|Anzahl|Gesamt|Event Hub-Archivnachrichten in einem Namespace (veraltet)|Keine Dimensionen|
|EHINBYTES|Ja|Eingehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz eingehender Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHINMBS|Ja|Eingehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz eingehender Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für eingehende Bytes. (veraltet)|Keine Dimensionen|
|EHINMSGS|Ja|Eingehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von eingehenden Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHOUTBYTES|Ja|Ausgehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz ausgehender Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHOUTMBS|Ja|Ausgehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz ausgehender Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für ausgehende Bytes. (veraltet)|Keine Dimensionen|
|EHOUTMSGS|Ja|Ausgehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von ausgehenden Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|FAILREQ|Ja|Fehlerhafte Anforderungen (veraltet)|Anzahl|Gesamt|Gesamtzahl fehlerhafter Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|IncomingBytes|Ja|Eingehende Bytes.|Byte|Gesamt|Eingehende Bytes für Microsoft.EventHub.|EntityName|
|IncomingMessages|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Eingehende Nachrichten für Microsoft.EventHub.|EntityName|
|IncomingRequests|Ja|Eingehende Anforderungen|Anzahl|Gesamt|Eingehende Anforderungen für Microsoft.EventHub.|EntityName|
|INMSGS|Ja|Eingehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von eingehenden Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für eingehende Nachrichten. (veraltet)|Keine Dimensionen|
|INREQS|Ja|Eingehende Anforderungen (veraltet)|Anzahl|Gesamt|Gesamtzahl eingehender Sendeanforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|INTERR|Ja|Interne Serverfehler (veraltet)|Anzahl|Gesamt|Gesamtzahl interner Serverfehler für einen Namespace (veraltet)|Keine Dimensionen|
|MISCERR|Ja|Andere Fehler (veraltet)|Anzahl|Gesamt|Gesamtzahl fehlerhafter Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|OutgoingBytes|Ja|Ausgehende Bytes.|Byte|Gesamt|Ausgehende Bytes für Microsoft.EventHub.|EntityName|
|OutgoingMessages|Ja|Ausgehende Nachrichten|Anzahl|Gesamt|Ausgehende Nachrichten für Microsoft.EventHub.|EntityName|
|OUTMSGS|Ja|Ausgehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von ausgehenden Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für ausgehende Nachrichten. (veraltet)|Keine Dimensionen|
|QuotaExceededErrors|Nein|Fehler aufgrund von Kontingentüberschreitung.|Anzahl|Gesamt|Fehler aufgrund von Kontingentüberschreitung für Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Nein|Serverfehler.|Anzahl|Gesamt|Serverfehler für Microsoft.EventHub.|EntityName, OperationResult|
|Size|Nein|Size|Byte|Average|Größe eines EventHub in Bytes.|EntityName|
|SuccessfulRequests|Nein|Erfolgreiche Anforderungen|Anzahl|Gesamt|Erfolgreiche Anforderungen für Microsoft.EventHub.|EntityName, OperationResult|
|SUCCREQ|Ja|Erfolgreiche Anforderungen (veraltet)|Anzahl|Gesamt|Gesamtzahl erfolgreicher Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|SVRBSY|Ja|Fehler durch ausgelasteten Server (veraltet)|Anzahl|Gesamt|Gesamtzahl der Fehler durch ausgelasteten Server für einen Namespace (veraltet)|Keine Dimensionen|
|ThrottledRequests|Nein|Gedrosselte Anforderungen.|Anzahl|Gesamt|Gedrosselte Anforderungen für Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Nein|Benutzerfehler.|Anzahl|Gesamt|Benutzerfehler für Microsoft.EventHub.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Ja|Kategorisierte Gatewayanforderungen|Anzahl|Gesamt|Anzahl von Gatewayanforderungen nach Kategorien (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Ja|Gatewayanforderungen|Anzahl|Gesamt|Anzahl von Gatewayanforderungen|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Ja|RequestThroughput von REST-Proxyconsumer|Anzahl pro Sekunde|Gesamt|Anzahl von Consumeranforderungen an Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Ja|Nicht erfolgreiche Anforderungen von REST-Proxyconsumer|Anzahl pro Sekunde|Gesamt|Consumeranforderungenausnahmen|Computer, Thema|
|KafkaRestProxy.ConsumerRequestTime.p95|Ja|RequestLatency von REST-Proxyconsumer|Millisekunden|Average|Nachrichtenlatenz in einer Consumeranforderung über Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|Ja|Anforderungenbacklog von REST-Proxyconsumer|Millisekunden|Average|Consumer-REST-Proxy-Warteschlangenlänge|Computer, Thema|
|KafkaRestProxy.MessagesIn.m1_delta|Ja|MessageThroughput von REST-Proxyproducer|Anzahl pro Sekunde|Gesamt|Anzahl von Producernachrichten über Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.MessagesOut.m1_delta|Ja|MessageThroughput von REST-Proxyconsumer|Anzahl pro Sekunde|Gesamt|Anzahl von Consumernachrichten über Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.OpenConnections|Ja|ConcurrentConnections für REST-Proxy|Anzahl|Gesamt|Anzahl gleichzeitiger Verbindungen über Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.ProducerRequest.m1_delta|Ja|RequestThroughput von REST-Proxyproducer|Anzahl pro Sekunde|Gesamt|Anzahl von Produceranforderungen an Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Ja|Nicht erfolgreiche REST-Proxy-Produceranforderungen|Anzahl pro Sekunde|Gesamt|Produceranforderungenausnahmen|Computer, Thema|
|KafkaRestProxy.ProducerRequestTime.p95|Ja|RequestLatency von REST-Proxyproducer|Millisekunden|Average|Nachrichtenlatenz in einer Produceranforderung über Kafka-REST-Proxy|Computer, Thema|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Ja|Anforderungenbacklog von REST-Proxyproducer|Millisekunden|Average|Producer-REST-Proxy-Warteschlangenlänge|Computer, Thema|
|NumActiveWorkers|Ja|Anzahl aktiver Worker|Anzahl|Maximum|Anzahl aktiver Worker|MetricName|
|PendingCPU|Ja|Ausstehende CPU|Anzahl|Maximum|Ausstehende CPU-Anforderungen in YARN|Keine Dimensionen|
|PendingMemory|Ja|Ausstehender Arbeitsspeicher|Anzahl|Maximum|Ausstehende Arbeitsspeicheranforderungen in YARN|Keine Dimensionen|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeitsrate des Diensts.|Keine Dimensionen|
|CosmosDbCollectionSize|Ja|Größe von Cosmos DB-Sammlung|Byte|Gesamt|Die Größe der unterstützenden Cosmos DB-Sammlung in Bytes.|Keine Dimensionen|
|CosmosDbIndexSize|Ja|Größe des Cosmos DB-Index|Byte|Gesamt|Die Größe des Index der unterstützenden Cosmos DB-Sammlung in Bytes.|Keine Dimensionen|
|CosmosDbRequestCharge|Ja|RU-Verbrauch von Cosmos DB|Anzahl|Gesamt|Der RU-Verbrauch von Anforderungen an den Dienst der unterstützenden Cosmos DB-Instanz.|Vorgang, ResourceType|
|CosmosDbRequests|Ja|Anforderungen an den Dienst von Cosmos DB|Anzahl|SUM|Die Gesamtanzahl der Anforderungen an eine den Dienst unterstützende Cosmos DB-Instanz.|Vorgang, ResourceType|
|CosmosDbThrottleRate|Ja|Drosselungsrate der Cosmos DB-Instanz des Diensts|Anzahl|SUM|Die Gesamtanzahl von 429 Antworten auf Anforderungen an eine den Dienst unterstützende Cosmos DB-Instanz.|Vorgang, ResourceType|
|IoTConnectorDeviceEvent|Ja|Anzahl eingehender Nachrichten|Anzahl|SUM|Die Gesamtanzahl der vom Azure IoT-Connector für FHIR empfangenen Nachrichten vor einer Normalisierung.|Vorgang, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Ja|Durchschnittliche Wartezeit in der Normalisierungsphase|Millisekunden|Average|Die durchschnittliche Zeit zwischen dem Erfassungszeitpunkt eines Ereignisses und dem Zeitpunkt seiner Verarbeitung zur Normalisierung.|Vorgang, ConnectorName|
|IoTConnectorMeasurement|Ja|Anzahl der Messungen|Anzahl|SUM|Die Anzahl normalisierter Messwerte, die in der FHIR-Konvertierungsphase des Azure IoT-Connectors für FHIR empfangen wurden.|Vorgang, ConnectorName|
|IoTConnectorMeasurementGroup|Ja|Anzahl von Nachrichtengruppen|Anzahl|SUM|Die Gesamtanzahl eindeutiger Gruppierungen von Messungen nach Typ, Gerät, Patient und konfiguriertem Zeitraum, generiert in der FHIR-Konvertierungsphase.|Vorgang, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Ja|Durchschnittliche Wartezeit der Gruppierungsphase|Millisekunden|Average|Die Zeitspanne zwischen dem Empfang der Gerätedaten durch den IoT-Connector und der Verarbeitung der Daten in der FHIR-Konvertierungsphase.|Vorgang, ConnectorName|
|IoTConnectorNormalizedEvent|Ja|Anzahl normalisierter Nachrichten|Anzahl|SUM|Die Gesamtanzahl zugeordneter normalisierter Werte, die in der Normalisierungsphase des Azure IoT-Connectors für FHIR ausgegeben wurden.|Vorgang, ConnectorName|
|IoTConnectorTotalErrors|Ja|Gesamtfehlerzahl|Anzahl|SUM|Die Gesamtanzahl der vom Azure IoT-Connector für FHIR protokollierten Fehler|Name, Vorgang, ErrorType, ErrorSeverity, ConnectorName|
|TotalErrors|Ja|Fehler insgesamt|Anzahl|SUM|Die Gesamtanzahl der internen Serverfehler im Dienst.|Protokoll, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Ja|Gesamtlatenz|Millisekunden|Average|Die Antwortlatenz des Diensts.|Protocol|
|TotalRequests|Ja|Anzahl von Anforderungen|Anzahl|SUM|Die Gesamtanzahl der vom Dienst empfangenen Anforderungen.|Protocol|


## <a name="microsofthealthcareapisworkspacesiotconnectors"></a>Microsoft.HealthcareApis/workspaces/iotconnectors

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeviceEvent|Ja|Anzahl eingehender Nachrichten|Anzahl|SUM|Die Gesamtanzahl der vom Azure IoT-Connector für FHIR empfangenen Nachrichten vor einer Normalisierung.|Operation, ResourceName|
|DeviceEventProcessingLatencyMs|Ja|Durchschnittliche Wartezeit in der Normalisierungsphase|Millisekunden|Average|Die durchschnittliche Zeit zwischen dem Erfassungszeitpunkt eines Ereignisses und dem Zeitpunkt seiner Verarbeitung zur Normalisierung.|Operation, ResourceName|
|Messung|Ja|Anzahl der Messungen|Anzahl|SUM|Die Anzahl normalisierter Messwerte, die in der FHIR-Konvertierungsphase des Azure IoT-Connectors für FHIR empfangen wurden.|Operation, ResourceName|
|MeasurementGroup|Ja|Anzahl von Nachrichtengruppen|Anzahl|SUM|Die Gesamtanzahl eindeutiger Gruppierungen von Messungen nach Typ, Gerät, Patient und konfiguriertem Zeitraum, generiert in der FHIR-Konvertierungsphase.|Operation, ResourceName|
|MeasurementIngestionLatencyMs|Ja|Durchschnittliche Wartezeit der Gruppierungsphase|Millisekunden|Average|Die Zeitspanne zwischen dem Empfang der Gerätedaten durch den IoT-Connector und der Verarbeitung der Daten in der FHIR-Konvertierungsphase.|Operation, ResourceName|
|NormalizedEvent|Ja|Anzahl normalisierter Nachrichten|Anzahl|SUM|Die Gesamtanzahl zugeordneter normalisierter Werte, die in der Normalisierungsphase des Azure IoT-Connectors für FHIR ausgegeben wurden.|Operation, ResourceName|
|TotalErrors|Ja|Gesamtfehlerzahl|Anzahl|SUM|Die Gesamtanzahl der vom Azure IoT-Connector für FHIR protokollierten Fehler|Name, Operation, ErrorType, ErrorSeverity, ResourceName|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Ja|Durchschnittliche CPU-Auslastung|Percent|Average|Gesamter durchschnittlicher Prozentsatz der Auslastung der virtuellen CPU im Minutentakt. Die Gesamtanzahl der virtuellen CPUs basiert auf dem vom Benutzer konfigurierten Wert in der SKU-Definition. Weitere Filter können basierend auf dem in der SKU definierten RoleName angewendet werden.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Ja|Durchschnittliche CPU-Auslastung|Percent|Average|Gesamter durchschnittlicher Prozentsatz der Auslastung der virtuellen CPU im Minutentakt. Die Gesamtanzahl der virtuellen CPUs basiert auf dem vom Benutzer konfigurierten Wert in der SKU-Definition. Weitere Filter können basierend auf dem in der SKU definierten RoleName angewendet werden.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|MetricThreshold|Ja|Schwellenwert der Metrik|Anzahl|Average|Der bei der Ausführung der automatischen Skalierung konfigurierte Schwellenwert für die automatische Skalierung.|MetricTriggerRule|
|ObservedCapacity|Ja|Beobachtete Kapazität|Anzahl|Average|Die Kapazität, die beim Ausführen der automatischen Skalierung gemeldet wurde.|Keine Dimensionen|
|ObservedMetricValue|Ja|Beobachteter Metrikwert|Anzahl|Average|Der Wert, der beim Ausführen der automatischen Skalierung berechnet wurde.|MetricTriggerSource|
|ScaleActionsInitiated|Ja|Initiierte Skalierungsaktionen|Anzahl|Gesamt|Die Richtung des Skalierungsvorgangs.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Ja|Verfügbarkeit|Percent|Average|Prozentsatz erfolgreich abgeschlossener Verfügbarkeitstests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Nein|Verfügbarkeitstests|Anzahl|Anzahl|Anzahl von Verfügbarkeitstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Ja|Dauer von Verfügbarkeitstests|Millisekunden|Average|Dauer von Verfügbarkeitstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Ja|Netzwerkverbindungszeit zum Laden der Seite|Millisekunden|Average|Zeit zwischen Benutzeranforderung und Netzwerkverbindung. Umfasst DNS-Suche und Transportverbindung.|Keine Dimensionen|
|browserTimings/processingDuration|Ja|Clientverarbeitungszeit|Millisekunden|Average|Zeit zwischen dem Empfang des letzten Byte eines Dokuments und dem Laden des DOM. Asynchrone Anforderungen werden möglicherweise immer noch verarbeitet.|Keine Dimensionen|
|browserTimings/receiveDuration|Ja|Empfängt Antwortzeit|Millisekunden|Average|Zeit zwischen dem ersten und letzten Byte, oder bis zur Verbindungstrennung.|Keine Dimensionen|
|browserTimings/sendDuration|Ja|Anforderungszeit senden|Millisekunden|Average|Zeit zwischen der Netzwerkverbindung und dem Empfang des ersten Byte.|Keine Dimensionen|
|browserTimings/totalDuration|Ja|Browser-Seitenladezeit|Millisekunden|Average|Zeit ab der Benutzeranforderung, bis DOM, Stylesheets, Skripts und Bilder geladen werden.|Keine Dimensionen|
|dependencies/count|Nein|Abhängigkeitsaufrufe|Anzahl|Anzahl|Die Anzahl der von der Anwendung an externe Ressourcen ausgeführten Aufrufe.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Ja|Dauer der Abhängigkeit|Millisekunden|Average|Die Dauer der von der Anwendung an externe Ressourcen ausgeführten Aufrufe.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Nein|Fehler bei Abhängigkeitsaufrufen|Anzahl|Anzahl|Anzahl fehlerhafter Abhängigkeitsaufrufe von der Anwendung an externe Ressourcen.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|exceptions/browser|Nein|Browserausnahmen|Anzahl|Anzahl|Anzahl nicht erfasster Ausnahmen, die im Browser ausgelöst wurden.|cloud/roleName|
|exceptions/count|Ja|Ausnahmen|Anzahl|Anzahl|Die kombinierte Anzahl nicht abgefangener Ausnahmen.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|Nein|Serverausnahmen|Anzahl|Anzahl|Anzahl nicht erfasster Ausnahmen, die in der Serveranwendung ausgelöst wurden.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Ja|Seitenaufrufe|Anzahl|Anzahl|Anzahl der Seitenaufrufe|operation/synthetic, cloud/roleName|
|pageViews/duration|Ja|Ladezeit der Seitenansicht|Millisekunden|Average|Ladezeit der Seitenansicht|operation/synthetic, cloud/roleName|
|performanceCounters/exceptionsPerSecond|Ja|Ausnahmerate|Anzahl pro Sekunde|Average|Die Anzahl der behandelten Ausnahmen und Ausnahmefehler, die an Windows gemeldet werden, einschließlich .NET-Ausnahmen und nicht verwalteten Ausnahmen, die in .NET-Ausnahmen konvertiert werden.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Ja|Verfügbarer Arbeitsspeicher|Byte|Average|Physischer Speicher ist sofort für die Zuordnung zu einem Prozess oder für die Systemnutzung verfügbar.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Ja|Prozess-CPU|Percent|Average|Der Prozentsatz der verstrichenen Zeit für alle Prozessthreads, die den Prozessor zur Ausführung von Anweisungen verwendet haben. Dies kann zwischen 0 und 100 variieren. Diese Metrik gibt ausschließlich die Leistung des w3wp-Prozesses an.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Ja|E/A-Rate für Prozess|Bytes pro Sekunde|Average|Gesamtanzahl von pro Sekunde in Dateien, im Netzwerk und auf Geräten gelesenen und geschriebenen Bytes.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Ja|Prozessorzeit|Percent|Average|Der Prozentsatz der Zeit, die der Prozessor nicht im Leerlauf in Threads verbringt.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Ja|Private Bytes für Prozess|Byte|Average|Speicher, der exklusiv den überwachten Anwendungsprozessen zugewiesen ist.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Ja|Ausführungszeit der HTTP-Anforderung|Millisekunden|Average|Ausführungszeit der aktuellen Anforderung.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Ja|HTTP-Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Länge der Anwendungsanforderungswarteschleife.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Ja|HTTP-Anforderungsrate|Anzahl pro Sekunde|Average|Rate aller Anforderungen an die Anwendung pro Sekunde von ASP.NET.|cloud/roleInstance|
|requests/count|Nein|Serveranforderungen|Anzahl|Anzahl|Anzahl der abgeschlossenen HTTP-Anforderungen.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|Ja|Serverantwortzeit|Millisekunden|Average|Zeit zwischen dem Empfang einer HTTP-Anforderung und dem Abschluss des Sendevorgangs der Antwort.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Nein|Anforderungsfehler|Anzahl|Anzahl|Anzahl der als fehlerhaft markierten HTTP-Anforderungen. In den meisten Fällen sind dies Anforderungen mit dem Antwortcode >= 400 und ungleich 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Nein|Serveranforderungsrate|Anzahl pro Sekunde|Average|Rate der Serveranforderungen pro Sekunde|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Ja|Traces|Anzahl|Anzahl|Dokumentanzahl nachverfolgen|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|c2d.commands.failure|Ja|Fehlerhafte Befehlsaufrufe|Anzahl|Gesamt|Anzahl aller fehlerhaften Befehlsanforderungen, die von IoT Central initiiert wurden|Keine Dimensionen|
|c2d.commands.requestSize|Ja|Anforderungsgröße der Befehlsaufrufe|Byte|Gesamt|Anforderungsgröße aller von IoT Central initiierten Befehlsanforderungen|Keine Dimensionen|
|c2d.commands.responseSize|Ja|Antwortgröße der Befehlsaufrufe|Byte|Gesamt|Antwortgröße aller von IoT Central initiierten Befehlsantworten|Keine Dimensionen|
|c2d.commands.success|Ja|Erfolgreiche Befehlsaufrufe|Anzahl|Gesamt|Anzahl aller erfolgreichen Befehlsanforderungen, die von IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.read.failure|Ja|Fehlerhafte Lesevorgänge für Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftslesevorgänge, die über IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.read.success|Ja|Erfolgreiche Lesevorgänge für Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftslesevorgänge, die über IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.update.failure|Ja|Fehlerhafte Aktualisierungen von Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftsaktualisierungen, die über IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.update.success|Ja|Erfolgreiche Aktualisierungen von Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftsaktualisierungen, die über IoT Central initiiert wurden|Keine Dimensionen|
|connectedDeviceCount|Nein|Gesamtzahl der verbundenen Geräte|Anzahl|Average|Anzahl von Geräten, die mit IoT Central verbunden sind|Keine Dimensionen|
|d2c.property.read.failure|Ja|Fehlerhafte Lesevorgänge für Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftslesevorgänge, die von Geräten initiiert wurden|Keine Dimensionen|
|d2c.property.read.success|Ja|Erfolgreiche Lesevorgänge für Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftslesevorgänge, die von Geräten initiiert wurden|Keine Dimensionen|
|d2c.property.update.failure|Ja|Fehlerhafte Aktualisierungen von Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftsaktualisierungen, die über Geräte initiiert wurden|Keine Dimensionen|
|d2c.property.update.success|Ja|Erfolgreiche Aktualisierungen von Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftsaktualisierungen, die über Geräte initiiert wurden|Keine Dimensionen|
|d2c.telemetry.Ingress.allProtocol|Ja|Sendeversuche für Telemetrienachrichten gesamt|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät zur Cloud, für die Versuche zum Senden an die IoT Central-Anwendung unternommen wurden|Keine Dimensionen|
|d2c.telemetry.ingress.success|Ja|Gesendete Telemetrienachrichten gesamt|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät zur Cloud, die erfolgreich an die IoT Central-Anwendung gesendet wurden|Keine Dimensionen|
|dataExport.error|Ja|Datenexportfehler|Anzahl|Gesamt|Anzahl der beim Datenexport aufgetretenen Fehler|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Ja|Gefilterte Datenexportnachrichten|Anzahl|Gesamt|Anzahl der Nachrichten, die beim Datenexport Filter durchlaufen haben|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Ja|Empfangene Datenexportnachrichten|Anzahl|Gesamt|Anzahl der Nachrichten, die beim Datenexport eingehen, ehe Filterung und Anreicherung erfolgen|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|Ja|Geschriebene Datenexportnachrichten|Anzahl|Gesamt|Anzahl der in ein Ziel geschriebenen Nachrichten|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.statusChange|Ja|Änderung des Datenexportstatus|Anzahl|Gesamt|Anzahl von Statusänderungen|exportId, exportDisplayName, destinationId, destinationDisplayName, status|
|deviceDataUsage|Ja|Datennutzung durch Geräte gesamt|Byte|Gesamt|Anzahl übertragener Bytes an Geräte und von Geräten, die mit der IoT Central-Anwendung verbunden sind|Keine Dimensionen|
|provisionedDeviceCount|Nein|Bereitgestellte Geräte gesamt|Anzahl|Average|Anzahl von Geräten, die in der IoT Central-Anwendung bereitgestellt werden|Keine Dimensionen|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Nein|Allgemeine Dienstverfügbarkeit|Percent|Average|Verfügbarkeit von Dienstanforderungen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Ja|Dienst-API-Treffer, gesamt|Anzahl|Anzahl|Gesamtanzahl der Dienst-API-Treffer|ActivityType, ActivityName|
|ServiceApiLatency|Nein|Gesamtwartezeit für Dienst-API|Millisekunden|Average|Gesamtwartezeit für Dienst-API-Anforderungen|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Tresorverfügbarkeit insgesamt|Percent|Average|Verfügbarkeit von Tresoranforderungen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Nein|Tresorauslastung insgesamt|Percent|Average|Verwendete Tresorkapazität|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Ja|Dienst-API-Treffer, gesamt|Anzahl|Anzahl|Gesamtanzahl der Dienst-API-Treffer|ActivityType, ActivityName|
|ServiceApiLatency|Ja|Gesamtwartezeit für Dienst-API|Millisekunden|Average|Gesamtwartezeit für Dienst-API-Anforderungen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Ja|Dienst-API-Ergebnisse, gesamt|Anzahl|Anzahl|Gesamtanzahl der Dienst-API-Ergebnisse|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Ja|Gesamtanzahl der CPU-Kerne in einem verbundenen Cluster|Anzahl|Gesamt|Gesamtanzahl der CPU-Kerne in einem verbundenen Cluster|Keine Dimensionen|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BatchBlobCount|Ja|Batchblob – Anzahl|Anzahl|Average|Anzahl der Datenquellen in einem aggregierten Batch für die Erfassung|Datenbank|
|BatchDuration|Ja|Batchdauer|Sekunden|Average|Dauer der Aggregationsphase im Erfassungsflow|Datenbank|
|BatchesProcessed|Ja|Verarbeitete Batches|Anzahl|Gesamt|Anzahl der für die Erfassung aggregierten Batches Batchverarbeitungstyp: Gibt an, ob die in der Batchrichtlinie festgelegten Grenzwerte für Batchverarbeitungsdauer, Datengröße oder Dateianzahl erreicht wurden|Database, SealReason|
|BatchSize|Ja|Batchgröße|Byte|Average|Nicht komprimierte erwartete Datengröße in einem aggregierten Batch für die Erfassung|Datenbank|
|BlobsDropped|Ja|BlobsDropped|Anzahl|Gesamt|Die Anzahl der von einer Komponente dauerhaft abgelehnten Blobs.|Database, ComponentType, ComponentName|
|BlobsProcessed|Ja|Verarbeitete Blobs|Anzahl|Gesamt|Die Anzahl der von einer Komponente verarbeiteten Blobs.|Database, ComponentType, ComponentName|
|BlobsReceived|Ja|Empfangene Blobs|Anzahl|Gesamt|Die Anzahl der von einer Komponente aus dem Eingabestream empfangenen Blobs.|Database, ComponentType, ComponentName|
|CacheUtilization|Ja|Cacheauslastung|Percent|Average|Auslastungsgrad im Clusterbereich|Keine Dimensionen|
|CacheUtilizationFactor|Ja|Cacheauslastungsfaktor|Percent|Average|Prozentuale Differenz zwischen der aktuellen Anzahl von Instanzen und der optimalen Anzahl von Instanzen (pro Cacheauslastung)|Keine Dimensionen|
|ContinuousExportMaxLatenessMinutes|Ja|Fortlaufender Export – maximale Verzögerung|Anzahl|Maximum|Die Verzögerung (in Minuten), die von Aufträgen für den fortlaufenden Export im Cluster gemeldet wurde|Keine Dimensionen|
|ContinuousExportNumOfRecordsExported|Ja|Fortlaufender Export – Anzahl der exportierten Datensätze|Anzahl|Gesamt|Anzahl der exportierten Datensätze, ausgelöst für jedes Speicherartefakt, das während des Exportvorgangs geschrieben wird|ContinuousExportName, Database|
|ContinuousExportPendingCount|Ja|ContinuousExportPendingCount|Anzahl|Maximum|Die Anzahl ausstehender fortlaufender Exportaufträge, die zur Ausführung bereit sind.|Keine Dimensionen|
|ContinuousExportResult|Ja|Ergebnis des fortlaufenden Exports|Anzahl|Anzahl|Gibt an, ob der fortlaufende Export erfolgreich war|ContinuousExportName, Result, Database|
|CPU|Ja|CPU|Percent|Average|CPU-Auslastungsgrad|Keine Dimensionen|
|DiscoveryLatency|Ja|Wartezeit bei der Ermittlung|Sekunden|Average|Wird von Datenverbindungen gemeldet (sofern vorhanden). Zeit in Sekunden ab dem Zeitpunkt, an dem eine Nachricht in die Warteschlange eingereiht oder ein Ereignis erstellt wird, bis zur Ermittlung durch die Datenverbindung. Diese Zeit ist nicht in der Gesamtdauer der Erfassung von Azure Data Explorer enthalten.|ComponentType, ComponentName|
|EventsDropped|Ja|EventsDropped|Anzahl|Gesamt|Anzahl der Ereignisse, die durch die Datenverbindung permanent verworfen werden. Eine Metrik für das Erfassungsergebnis mit einem Fehlergrund wird gesendet.|ComponentType, ComponentName|
|EventsProcessed|Ja|Verarbeitete Ereignisse|Anzahl|Gesamt|Anzahl der vom Cluster verarbeiteten Ereignisse|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Ja|Verarbeitete Ereignisse (für Event Hub/IoT Hub)|Anzahl|Gesamt|Anzahl der vom Cluster verarbeiteten Ereignisse beim Erfassen aus Event/IoT Hub|EventStatus|
|EventsReceived|Ja|Empfangene Ereignisse|Anzahl|Gesamt|Anzahl der von der Datenverbindung empfangenen Ereignisse.|ComponentType, ComponentName|
|ExportUtilization|Ja|Exportauslastung|Percent|Maximum|Exportauslastung|Keine Dimensionen|
|IngestionLatencyInSeconds|Ja|Latenz bei der Erfassung|Sekunden|Average|Latenz der erfassten Daten ab dem Empfangszeitpunkt der Daten im Cluster bis zu dem Zeitpunkt, zu dem die Daten bereit zum Abfragen sind. Der Zeitraum der Erfassungslatenz richtet sich nach dem Erfassungsszenario.|Keine Dimensionen|
|IngestionResult|Ja|Ergebnis der Datenerfassung|Anzahl|Gesamt|Gesamtzahl von Quellen mit nicht erfolgreicher bzw. erfolgreicher Erfassung. Durch Aufteilen der Metrik nach Status erhalten Sie ausführliche Informationen zum Status der Erfassungsvorgänge.|IngestionResultDetails, FailureKind|
|IngestionUtilization|Ja|Datenerfassungsauslastung|Percent|Average|Verhältnis der verwendeten Datenerfassungsslots im Cluster|Keine Dimensionen|
|IngestionVolumeInMB|Ja|Datenerfassungsvolumen|Byte|Gesamt|Gesamtvolumen der im Cluster erfassten Daten|Datenbank|
|InstanceCount|Ja|Anzahl der Instanzen|Anzahl|Average|Gesamtzahl der Instanzen|Keine Dimensionen|
|KeepAlive|Ja|Keep-Alive|Anzahl|Average|Die Integritätsprüfung zeigt an, dass der Cluster auf Abfragen reagiert.|Keine Dimensionen|
|MaterializedViewAgeMinutes|Ja|Alter der materialisierten Sicht|Anzahl|Average|Das Alter der materialisierten Sicht in Minuten|Database, MaterializedViewName|
|MaterializedViewAgeSeconds|Ja|Alter der materialisierten Sicht|Sekunden|Average|Das Alter der materialisierten Sicht in Sekunden|Database, MaterializedViewName|
|MaterializedViewDataLoss|Ja|Datenverlust der materialisierten Sicht|Anzahl|Maximum|Gibt den potenziellen Datenverlust in der materialisierten Sicht an|Database, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Ja|Neu erstellte Erweiterungen der materialisierten Sicht|Anzahl|Average|Anzahl der neu erstellten Erweiterungen|Database, MaterializedViewName|
|MaterializedViewHealth|Ja|Integrität der materialisierten Sicht|Anzahl|Average|Die Integrität der materialisierten Sicht („1“ für fehlerfrei, „0“ für nicht fehlerfrei)|Database, MaterializedViewName|
|MaterializedViewRecordsInDelta|Ja|Datensätze der materialisierten Sicht in Delta|Anzahl|Average|Die Anzahl von Datensätzen im nicht materialisierten Teil der Sicht|Database, MaterializedViewName|
|MaterializedViewResult|Ja|Ergebnis der materialisierten Sicht|Anzahl|Average|Das Ergebnis des Materialisierungsprozesses|Database, MaterializedViewName, Result|
|QueryDuration|Ja|Abfragedauer|Millisekunden|Average|Abfragedauer in Sekunden|QueryStatus|
|QueryResult|Nein|Abfrageergebnis|Anzahl|Anzahl|Die Gesamtanzahl der Abfragen.|QueryStatus|
|QueueLength|Ja|Warteschlangenlänge|Anzahl|Average|Anzahl ausstehender Nachrichten in der Warteschlange einer Komponente.|ComponentType|
|QueueOldestMessage|Ja|Älteste Nachricht in Warteschlange|Anzahl|Average|Zeit in Sekunden ab dem Zeitpunkt, an dem die älteste Nachricht in der Warteschlange eingefügt wurde.|ComponentType|
|ReceivedDataSizeBytes|Ja|Größe der empfangenen Daten in Bytes|Byte|Average|Größe der von der Datenverbindung empfangenen Daten. Dies ist die Größe des Datenstroms oder der Rohdaten, falls vorhanden.|ComponentType, ComponentName|
|StageLatency|Ja|Phasenlatenz|Sekunden|Average|Kumulierte Zeit ab dem Zeitpunkt der Ermittlung einer Nachricht, bis sie von der Berichtskomponente zur Verarbeitung empfangen wird (die Ermittlungszeit wird festgelegt, wenn die Nachricht in die Erfassungswarteschlange eingereiht wird oder von der Datenverbindung ermittelt wird).|Database, ComponentType|
|SteamingIngestRequestRate|Ja|Anforderungsrate der Streamingerfassung|Anzahl|RateRequestsPerSecond|Anforderungsrate der Streamingerfassung (Anforderungen pro Sekunde)|Keine Dimensionen|
|StreamingIngestDataRate|Ja|Datenrate der Streamingerfassung|Anzahl|Average|Datenrate der Streamingerfassung (MB pro Sekunde)|Keine Dimensionen|
|StreamingIngestDuration|Ja|Dauer der Streamingerfassung|Millisekunden|Average|Dauer der Streamingerfassung in Millisekunden|Keine Dimensionen|
|StreamingIngestResults|Ja|Ergebnis der Streamingerfassung|Anzahl|Average|Ergebnis der Streamingerfassung|Ergebnis|
|TotalNumberOfConcurrentQueries|Ja|Gesamtanzahl gleichzeitiger Abfragen|Anzahl|Maximum|Gesamtanzahl gleichzeitiger Abfragen|Keine Dimensionen|
|TotalNumberOfExtents|Ja|Gesamtanzahl von Erweiterungen|Anzahl|Gesamt|Gesamtanzahl von Datenerweiterungen|Keine Dimensionen|
|TotalNumberOfThrottledCommands|Ja|Gesamtanzahl gedrosselter Befehle|Anzahl|Gesamt|Gesamtanzahl gedrosselter Befehle|CommandType|
|TotalNumberOfThrottledQueries|Ja|Gesamtanzahl gedrosselter Abfragen|Anzahl|Maximum|Gesamtanzahl gedrosselter Abfragen|Keine Dimensionen|
|WeakConsistencyLatency|Ja|Latenz der schwachen Konsistenz|Sekunden|Average|Die maximale Latenz zwischen der vorherigen Metadatensynchronisierung und der nächsten (im DB-/Knotenbereich)|Database, RoleInstance|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/IntegrationServiceEnvironments

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActionLatency|Ja|Aktionslatenz |Sekunden|Average|Latenz abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsCompleted|Ja|Abgeschlossene Aktionen |Anzahl|Gesamt|Anzahl abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsFailed|Ja|Aktionsfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowaktionen.|Keine Dimensionen|
|ActionsSkipped|Ja|Übersprungene Aktionen |Anzahl|Gesamt|Anzahl übersprungener Workflowaktionen.|Keine Dimensionen|
|ActionsStarted|Ja|Gestartete Aktionen |Anzahl|Gesamt|Anzahl gestarteter Workflowaktionen.|Keine Dimensionen|
|ActionsSucceeded|Ja|Erfolgreiche Aktionen |Anzahl|Gesamt|Anzahl erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionSuccessLatency|Ja|Latenz erfolgreicher Aktionen |Sekunden|Average|Latenz erfolgreicher Workflowaktionen.|Keine Dimensionen|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Ja|Connector-Arbeitsspeichernutzung für Integrationsdienstumgebung|Percent|Average|Connector-Arbeitsspeichernutzung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Ja|Connector-Prozessorauslastung für Integrationsdienstumgebung|Percent|Average|Connector-Prozessorauslastung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Ja|Workflow-Arbeitsspeichernutzung für Integrationsdienstumgebung|Percent|Average|Workflow-Arbeitsspeichernutzung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Ja|Workflow-Prozessorauslastung für Integrationsdienstumgebung|Percent|Average|Workflow-Prozessorauslastung für Integrationsdienstumgebung|Keine Dimensionen|
|RunLatency|Ja|Ausführungslatenz|Sekunden|Average|Latenz abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsCancelled|Ja|Abgebrochene Ausführungen|Anzahl|Gesamt|Anzahl abgebrochener Workflowausführungen.|Keine Dimensionen|
|RunsCompleted|Ja|Abgeschlossene Ausführungen|Anzahl|Gesamt|Anzahl abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsFailed|Ja|Ausführungsfehler|Anzahl|Gesamt|Anzahl fehlerhafter Workflowausführungen.|Keine Dimensionen|
|RunsStarted|Ja|Gestartete Ausführungen|Anzahl|Gesamt|Anzahl gestarteter Workflowausführungen.|Keine Dimensionen|
|RunsSucceeded|Ja|Erfolgreiche Ausführungen|Anzahl|Gesamt|Anzahl erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunSuccessLatency|Ja|Latenz erfolgreicher Ausführungen|Sekunden|Average|Latenz erfolgreicher Workflowausführungen.|Keine Dimensionen|
|TriggerFireLatency|Ja|Latenz beim Auslösen von Triggern |Sekunden|Average|Latenz ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerLatency|Ja|Triggerlatenz |Sekunden|Average|Latenz abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersCompleted|Ja|Abgeschlossene Trigger |Anzahl|Gesamt|Anzahl abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersFailed|Ja|Triggerfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowtrigger.|Keine Dimensionen|
|TriggersFired|Ja|Ausgelöste Trigger |Anzahl|Gesamt|Anzahl ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggersSkipped|Ja|Übersprungene Trigger|Anzahl|Gesamt|Anzahl übersprungener Workflowtrigger.|Keine Dimensionen|
|TriggersStarted|Ja|Gestartete Trigger |Anzahl|Gesamt|Anzahl gestarteter Workflowtrigger.|Keine Dimensionen|
|TriggersSucceeded|Ja|Erfolgreiche Trigger |Anzahl|Gesamt|Anzahl erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerSuccessLatency|Ja|Latenz erfolgreicher Trigger |Sekunden|Average|Latenz erfolgreicher Workflowtrigger.|Keine Dimensionen|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/Workflows

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActionLatency|Ja|Aktionslatenz |Sekunden|Average|Latenz abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsCompleted|Ja|Abgeschlossene Aktionen |Anzahl|Gesamt|Anzahl abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsFailed|Ja|Aktionsfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowaktionen.|Keine Dimensionen|
|ActionsSkipped|Ja|Übersprungene Aktionen |Anzahl|Gesamt|Anzahl übersprungener Workflowaktionen.|Keine Dimensionen|
|ActionsStarted|Ja|Gestartete Aktionen |Anzahl|Gesamt|Anzahl gestarteter Workflowaktionen.|Keine Dimensionen|
|ActionsSucceeded|Ja|Erfolgreiche Aktionen |Anzahl|Gesamt|Anzahl erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionSuccessLatency|Ja|Latenz erfolgreicher Aktionen |Sekunden|Average|Latenz erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionThrottledEvents|Ja|Ereignisse zu gedrosselten Aktionen|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen.|Keine Dimensionen|
|BillableActionExecutions|Ja|Ausführungen abrechenbarer Aktionen|Anzahl|Gesamt|Anzahl der Workflowaktionsausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|BillableTriggerExecutions|Ja|Ausführungen abrechenbarer Trigger|Anzahl|Gesamt|Anzahl der Workflowtriggerausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|BillingUsageNativeOperation|Ja|Nutzungsabrechnung für Ausführungen nativer Vorgänge|Anzahl|Gesamt|Anzahl von Ausführungen nativer Vorgänge, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStandardConnector|Ja|Nutzungsabrechnung für Ausführungen standardmäßiger Connectors|Anzahl|Gesamt|Anzahl von Ausführungen standardmäßiger Connectors, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStorageConsumption|Ja|Nutzungsabrechnung für Ausführungen mit Speicherverbrauch|Anzahl|Gesamt|Anzahl von Ausführungen mit Speicherverbrauch, die in Rechnung gestellt werden.|Keine Dimensionen|
|RunFailurePercentage|Ja|Prozentsatz der Ausführungsfehler|Percent|Gesamt|Prozentsatz fehlerhafter Workflowausführungen|Keine Dimensionen|
|RunLatency|Ja|Ausführungslatenz|Sekunden|Average|Latenz abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsCancelled|Ja|Abgebrochene Ausführungen|Anzahl|Gesamt|Anzahl abgebrochener Workflowausführungen.|Keine Dimensionen|
|RunsCompleted|Ja|Abgeschlossene Ausführungen|Anzahl|Gesamt|Anzahl abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsFailed|Ja|Ausführungsfehler|Anzahl|Gesamt|Anzahl fehlerhafter Workflowausführungen.|Keine Dimensionen|
|RunsStarted|Ja|Gestartete Ausführungen|Anzahl|Gesamt|Anzahl gestarteter Workflowausführungen.|Keine Dimensionen|
|RunsSucceeded|Ja|Erfolgreiche Ausführungen|Anzahl|Gesamt|Anzahl erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunStartThrottledEvents|Ja|Ereignisse zu gedrosselten Ausführungsstarts|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflow-Ausführungsstarts.|Keine Dimensionen|
|RunSuccessLatency|Ja|Latenz erfolgreicher Ausführungen|Sekunden|Average|Latenz erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunThrottledEvents|Ja|Ereignisse zu gedrosselten Ausführungen|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen oder Triggern.|Keine Dimensionen|
|TotalBillableExecutions|Ja|Gesamtzahl der abrechenbaren Ausführungen|Anzahl|Gesamt|Anzahl der Workflowausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|TriggerFireLatency|Ja|Latenz beim Auslösen von Triggern |Sekunden|Average|Latenz ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerLatency|Ja|Triggerlatenz |Sekunden|Average|Latenz abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersCompleted|Ja|Abgeschlossene Trigger |Anzahl|Gesamt|Anzahl abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersFailed|Ja|Triggerfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowtrigger.|Keine Dimensionen|
|TriggersFired|Ja|Ausgelöste Trigger |Anzahl|Gesamt|Anzahl ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggersSkipped|Ja|Übersprungene Trigger|Anzahl|Gesamt|Anzahl übersprungener Workflowtrigger.|Keine Dimensionen|
|TriggersStarted|Ja|Gestartete Trigger |Anzahl|Gesamt|Anzahl gestarteter Workflowtrigger.|Keine Dimensionen|
|TriggersSucceeded|Ja|Erfolgreiche Trigger |Anzahl|Gesamt|Anzahl erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerSuccessLatency|Ja|Latenz erfolgreicher Trigger |Sekunden|Average|Latenz erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerThrottledEvents|Ja|Ereignisse zu gedrosselten Triggern|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowtriggern.|Keine Dimensionen|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Aktive Kerne|Ja|Aktive Kerne|Anzahl|Average|Gesamtanzahl der aktiven Kerne|Scenario, ClusterName|
|Aktive Knoten|Ja|Aktive Knoten|Anzahl|Average|Anzahl der aktiven Knoten. Dabei handelt es sich um die Knoten, auf denen aktiv ein Auftrag ausgeführt wird.|Scenario, ClusterName|
|Ausführungen mit angefordertem Abbruch|Ja|Ausführungen mit angefordertem Abbruch|Anzahl|Gesamt|Anzahl von Ausführungen, bei denen ein Abbruch für diesen Arbeitsbereich angefordert wurde – die Anzahl wird aktualisiert, wenn eine Abbruchanforderung für eine Ausführung empfangen wurde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Abgebrochene Ausführungen|Ja|Abgebrochene Ausführungen|Anzahl|Gesamt|Anzahl abgebrochener Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung erfolgreich abgebrochen wurde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Abgeschlossene Ausführungen|Ja|Abgeschlossene Ausführungen|Anzahl|Gesamt|Anzahl erfolgreich abgeschlossener Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung abgeschlossen und die Ausgabe erfasst wurde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuCapacityMillicores|Ja|CpuCapacityMillicores|Anzahl|Average|Maximale Kapazität eines CPU-Knotens in Millicore. Die Kapazität wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|CpuMemoryCapacityMegabytes|Ja|CpuMemoryCapacityMegabytes|Anzahl|Average|Maximale Arbeitsspeicherauslastung eines CPU-Knotens in Megabyte. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|CpuMemoryUtilizationMegabytes|Ja|CpuMemoryUtilizationMegabytes|Anzahl|Average|Arbeitsspeicherauslastung eines CPU-Knotens in Megabyte. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|CpuMemoryUtilizationPercentage|Ja|CpuMemoryUtilizationPercentage|Anzahl|Average|Arbeitsspeicherauslastung eines CPU-Knotens in Prozent. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|CpuUtilization|Ja|CpuUtilization|Anzahl|Average|Prozentsatz der Auslastung auf einem CPU-Knoten. die Auslastung wird in Intervallen von einer Minute gemeldet.|Scenario, runId, NodeId, ClusterName|
|CpuUtilizationMillicores|Ja|CpuUtilizationMillicores|Anzahl|Average|Auslastung eines CPU-Knotens in Millicore. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|CpuUtilizationPercentage|Ja|CpuUtilizationPercentage|Anzahl|Average|Auslastung eines CPU-Knotens in Prozent. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|Errors|Ja|Errors|Anzahl|Gesamt|Anzahl von Ausführungsfehlern in diesem Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei der Ausführung ein Fehler auftritt.|Szenario|
|Fehlerhafte Ausführungen|Ja|Fehlerhafte Ausführungen|Anzahl|Gesamt|Anzahl fehlerhafter Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei einer Ausführung ein Fehler auftritt.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Ausführungen, die abgeschlossen werden|Ja|Ausführungen, die abgeschlossen werden|Anzahl|Gesamt|Anzahl von Ausführungen, die für diesen Arbeitsbereich abgeschlossen wurden – die Anzahl wird aktualisiert, wenn eine Ausführung abgeschlossen wurde, aber die Ausgabe noch erfasst wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuCapacityMilliGPUs|Ja|GpuCapacityMilliGPUs|Anzahl|Average|Maximale Kapazität eines GPU-Geräts in Milli-GPU. Die Kapazität wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, DeviceId, ComputeName|
|GpuEnergyJoules|Ja|GpuEnergyJoules|Anzahl|Gesamt|Intervallenergie in Joule auf einem GPU-Knoten. Die Energie wird in Intervallen von einer Minute gemeldet.|Scenario, runId, rootRunId, InstanceId, DeviceId, ComputeName|
|GpuMemoryCapacityMegabytes|Ja|GpuMemoryCapacityMegabytes|Anzahl|Average|Maximale Speicherkapazität eines GPU-Geräts in Megabyte. Die Kapazität wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, DeviceId, ComputeName|
|GpuMemoryUtilization|Ja|GpuMemoryUtilization|Anzahl|Average|Arbeitsspeicherauslastung auf einem GPU-Knoten in Prozent – die Auslastung wird in Intervallen von einer Minute gemeldet.|Scenario, runId, NodeId, DeviceId, ClusterName|
|GpuMemoryUtilizationMegabytes|Ja|GpuMemoryUtilizationMegabytes|Anzahl|Average|Arbeitsspeicherauslastung eines GPU-Geräts in Megabyte. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, DeviceId, ComputeName|
|GpuMemoryUtilizationPercentage|Ja|GpuMemoryUtilizationPercentage|Anzahl|Average|Arbeitsspeicherauslastung eines GPU-Geräts in Prozent. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, DeviceId, ComputeName|
|GpuUtilization|Ja|GpuUtilization|Anzahl|Average|Prozentsatz der Auslastung auf einem GPU-Knoten. die Auslastung wird in Intervallen von einer Minute gemeldet.|Scenario, runId, NodeId, DeviceId, ClusterName|
|GpuUtilizationMilliGPUs|Ja|GpuUtilizationMilliGPUs|Anzahl|Average|Auslastung eines GPU-Geräts in Milli-GPU. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, DeviceId, ComputeName|
|GpuUtilizationPercentage|Ja|GpuUtilizationPercentage|Anzahl|Average|Auslastung eines GPU-Geräts in Prozent. Die Auslastung wird in Intervallen von einer Minute aggregiert.|RunId, InstanceId, DeviceId, ComputeName|
|IBReceiveMegabytes|Ja|IBReceiveMegabytes|Anzahl|Average|Über InfiniBand empfangene Netzwerkdaten in Megabyte. Die Metriken werden in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|IBTransmitMegabytes|Ja|IBTransmitMegabytes|Anzahl|Average|Über InfiniBand gesendete Netzwerkdaten in Megabyte. Die Metriken werden in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|Kerne im Leerlauf|Ja|Kerne im Leerlauf|Anzahl|Average|Gesamtanzahl der Kerne im Leerlauf|Scenario, ClusterName|
|Knoten im Leerlauf|Ja|Knoten im Leerlauf|Anzahl|Average|Anzahl von Knoten im Leerlauf. Knoten im Leerlauf sind die Knoten, auf denen keine Aufträge ausgeführt werden, die aber einen neuen Auftrag bei Verfügbarkeit akzeptieren können.|Scenario, ClusterName|
|Ausscheidende Kerne|Ja|Ausscheidende Kerne|Anzahl|Average|Anzahl der ausscheidenden Kerne|Scenario, ClusterName|
|Ausscheidende Knoten|Ja|Ausscheidende Knoten|Anzahl|Average|Anzahl der ausscheidenden Knoten. Ausscheidende Knoten sind die Knoten, auf denen die Verarbeitung eines Auftrags gerade abgeschlossen wurde und die in den Leerlaufzustand wechseln.|Scenario, ClusterName|
|Model Deploy Failed (Fehler bei der Modellimplementierung)|Ja|Model Deploy Failed (Fehler bei der Modellimplementierung)|Anzahl|Gesamt|Anzahl der fehlerhaften Modellimplementierungen in diesem Arbeitsbereich|Scenario, StatusCode|
|Gestartete Modellimplementierungen|Ja|Gestartete Modellimplementierungen|Anzahl|Gesamt|Anzahl der gestarteten Modellimplementierungen in diesem Arbeitsbereich|Szenario|
|Erfolgreiche Modellimplementierungen|Ja|Erfolgreiche Modellimplementierungen|Anzahl|Gesamt|Anzahl der erfolgreichen Modellimplementierungen in diesem Arbeitsbereich|Szenario|
|Fehler bei der Modellregistrierung|Ja|Fehler bei der Modellregistrierung|Anzahl|Gesamt|Anzahl der fehlerhaften Modellregistrierungen in diesem Arbeitsbereich|Scenario, StatusCode|
|Erfolgreiche Modellregistrierungen|Ja|Erfolgreiche Modellregistrierungen|Anzahl|Gesamt|Anzahl der erfolgreichen Modellregistrierungen in diesem Arbeitsbereich|Szenario|
|NetworkInputMegabytes|Ja|NetworkInputMegabytes|Anzahl|Average|Empfangene Netzwerkdaten in Megabyte. Die Metriken werden in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|NetworkOutputMegabytes|Ja|NetworkOutputMegabytes|Anzahl|Average|Gesendete Netzwerkdaten in Megabyte. Die Metriken werden in Intervallen von einer Minute aggregiert.|RunId, InstanceId, ComputeName|
|Ausführungen ohne Rückmeldung|Ja|Ausführungen ohne Rückmeldung|Anzahl|Gesamt|Anzahl nicht reagierender Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung zum Status „Keine Rückmeldung“ übergeht.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Nicht gestartete Ausführungen|Ja|Nicht gestartete Ausführungen|Anzahl|Gesamt|Anzahl von Ausführungen mit Status „Nicht gestartet“ für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Anforderung zur Initiierung einer Ausführung eingeht, aber die Ausführungsinformationen nicht aufgefüllt wurden. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Vorzeitig entfernte Kerne|Ja|Vorzeitig entfernte Kerne|Anzahl|Average|Anzahl der vorzeitig entfernten Kerne|Scenario, ClusterName|
|Vorzeitig entfernte Knoten|Ja|Vorzeitig entfernte Knoten|Anzahl|Average|Anzahl der vorzeitig entfernten Knoten. Dabei handelt es sich um die Knoten mit niedriger Priorität, die aus dem Pool verfügbarer Knoten entfernt werden.|Scenario, ClusterName|
|Ausführungen in Vorbereitung|Ja|Ausführungen in Vorbereitung|Anzahl|Gesamt|Anzahl von Ausführungen, die für diesen Arbeitsbereich vorbereitet werden – die Anzahl wird aktualisiert, wenn eine Ausführung in den Status „Wird vorbereitet“ übergeht, während die Ausführungsumgebung vorbereitet wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Ausführungen in Bereitstellung|Ja|Ausführungen in Bereitstellung|Anzahl|Gesamt|Anzahl von Ausführungen, die sich für diesen Arbeitsbereich in Bereitstellung befinden – die Anzahl wird aktualisiert, wenn eine Ausführung darauf wartet, dass ein Computeziel erstellt oder bereitgestellt wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Ausführungen in Warteschlange|Ja|Ausführungen in Warteschlange|Anzahl|Gesamt|Anzahl von Ausführungen, die sich für diesen Arbeitsbereich in der Warteschlange befinden – die Anzahl wird aktualisiert, wenn eine Ausführung im Computeziel in die Warteschlange eingereiht wird. Dieser Fall kann eintreten, wenn darauf gewartet wird, dass erforderliche Computeknoten bereit sind.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Ja|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Anzahl|Average|Prozentualer Anteil der genutzten Kontingente|Scenario, ClusterName, VmFamilyName, VmPriority|
|Gestartete Ausführungen|Ja|Gestartete Ausführungen|Anzahl|Gesamt|Anzahl von Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung mit den erforderlichen Ressourcen gestartet wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Ausführungen, die gestartet werden|Ja|Ausführungen, die gestartet werden|Anzahl|Gesamt|Anzahl gestarteter Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, nachdem eine Anforderung zur Initiierung der Ausführung eingegangen ist und die Ausführungsinformationen (z. B. runId) aufgefüllt wurden.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kerne insgesamt|Ja|Kerne insgesamt|Anzahl|Average|Gesamtanzahl von Kernen|Scenario, ClusterName|
|Knoten insgesamt|Ja|Knoten insgesamt|Anzahl|Average|Gesamtanzahl von Knoten. Diese Summe umfasst Teile von aktiven Knoten, Leerlaufknoten, nicht verwendbaren Knoten, vorab erstellten Knoten, ausscheidenden Knoten.|Scenario, ClusterName|
|Nicht verwendbare Kerne|Ja|Nicht verwendbare Kerne|Anzahl|Average|Anzahl der nicht verwendbaren Kerne|Scenario, ClusterName|
|Unusable Nodes (Nicht verwendbare Knoten)|Ja|Unusable Nodes (Nicht verwendbare Knoten)|Anzahl|Average|Anzahl nicht verwendbarer Knoten. Nicht verwendbare Knoten sind aufgrund eines nicht auflösbaren Problems nicht funktionsfähig. Azure recycelt diese Knoten.|Scenario, ClusterName|
|Warnungen|Ja|Warnungen|Anzahl|Gesamt|Anzahl von Ausführungswarnungen in diesem Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei einer Ausführung eine Warnung auftritt.|Szenario|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Verfügbarkeit der APIs|ApiCategory, ApiName|
|CreatorUsage|Nein|Creator-Nutzung|Byte|Average|Nutzungsstatistiken zu Azure Maps Creator|Dienstname|
|Verwendung|Nein|Verwendung|Anzahl|Anzahl|Anzahl von API-Aufrufen|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AssetCount|Ja|Anzahl der Medienobjekte|Anzahl|Average|Anzahl der bereits im aktuellen Media Services-Konto erstellten Ressourcen|Keine Dimensionen|
|AssetQuota|Ja|Kontingent der Medienobjekte|Anzahl|Average|Anzahl der zulässigen Ressourcen für das aktuelle Media Services-Konto|Keine Dimensionen|
|AssetQuotaUsedPercentage|Ja|Prozentsatz des verwendeten Kontingents der Medienobjekte|Percent|Average|Prozentualer Anteil der verwendeten Ressourcen im aktuellen Media Services-Konto|Keine Dimensionen|
|ChannelsAndLiveEventsCount|Ja|Die Gesamtzahl der Liveereignisse|Anzahl|Average|Die Gesamtanzahl der Liveereignisse im aktuellen Media Services-Konto|Keine Dimensionen|
|ContentKeyPolicyCount|Ja|Anzahl der Richtlinien für Inhaltsschlüssel|Anzahl|Average|Anzahl der bereits im aktuellen Media Services-Konto erstellten Inhaltsschlüssel-Richtlinien|Keine Dimensionen|
|ContentKeyPolicyQuota|Ja|Kontingent der Richtlinien für Inhaltsschlüssel|Anzahl|Average|Anzahl der zulässigen Inhaltsschlüssel-Richtlinien für das aktuelle Media Services-Konto|Keine Dimensionen|
|ContentKeyPolicyQuotaUsedPercentage|Ja|Prozentsatz des Kontingents der Richtlinien für Inhaltsschlüssel|Percent|Average|Prozentualer Anteil der verwendeten Inhaltsschlüssel-Richtlinien im aktuellen Media Services-Konto|Keine Dimensionen|
|JobQuota|Ja|Auftragskontingent|Anzahl|Average|Das Auftragskontingent für das aktuelle Mediendienstkonto.|Keine Dimensionen|
|JobsScheduled|Ja|Geplante Aufträge|Anzahl|Average|Die Anzahl von Aufträgen im Zustand „Geplant“. Die Anzahl für diese Metrik spiegelt nur Aufträge wider, die über die v3-API übermittelt wurden. Aufträge, die über die v2-API (Legacy) übermittelt werden, werden nicht gezählt.|Keine Dimensionen|
|MaxChannelsAndLiveEventsCount|Ja|Maximales Liveereigniskontingent|Anzahl|Average|Die Maximalzahl der im aktuellen Media Services-Konto zulässigen Liveereignisse|Keine Dimensionen|
|MaxRunningChannelsAndLiveEventsCount|Ja|Maximal ausgeführtes Liveereigniskontingent|Anzahl|Average|Die Maximalzahl der im aktuellen Media Services-Konto zulässigen ausgeführten Liveereignisse|Keine Dimensionen|
|RunningChannelsAndLiveEventsCount|Ja|Anzahl aktiver Liveereignisse|Anzahl|Average|Die Gesamtanzahl der aktiven Liveereignisse im aktuellen Media Services-Konto|Keine Dimensionen|
|StreamingPolicyCount|Ja|Anzahl der Streamingrichtlinien|Anzahl|Average|Anzahl der im aktuellen Media Services-Konto bereits erstellten Streamingrichtlinien|Keine Dimensionen|
|StreamingPolicyQuota|Ja|Kontingent der Streamingrichtlinien|Anzahl|Average|Anzahl der zulässigen Streamingrichtlinien für das aktuelle Media Services-Konto|Keine Dimensionen|
|StreamingPolicyQuotaUsedPercentage|Ja|Prozentsatz des verwendeten Kontingents der Streamingrichtlinien|Percent|Average|Prozentualer Anteil der verwendeten Streamingrichtlinien im aktuellen Media Services-Konto|Keine Dimensionen|
|TransformQuota|Ja|Transformationskontingent|Anzahl|Average|Das Transformationskontingent für das aktuelle Mediendienstkonto.|Keine Dimensionen|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|IngestBitrate|Ja|Bitrate der Erfassung für Liveereignis|BitsPerSecond|Average|Die eingehende für ein Liveereignis erfasste Bitrate, in Bits pro Sekunde.|TrackName|
|IngestDriftValue|Ja|Wert der Abweichung bei Erfassung von Liveereignissen|Sekunden|Maximum|Abweichung zwischen dem Zeitstempel des erfassten Inhalts und der Systemuhr, gemessen in Sekunden pro Minute. Ein Wert ungleich null bedeutet, dass der erfasste Inhalt im Vergleich zur Systemuhrzeit langsamer eingeht.|TrackName|
|IngestLastTimestamp|Ja|Letzter Zeitstempel der Erfassung für Liveereignis|Millisekunden|Maximum|Der letzte für ein Liveereignis erfasste Zeitstempel.|TrackName|
|LiveOutputLastTimestamp|Ja|Zeitstempel der letzten Ausgabe|Millisekunden|Maximum|Zeitstempel des letzten in den Speicher hochgeladenen Fragments für die Ausgabe eines Liveereignisses.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CPU|Ja|CPU-Auslastung|Percent|Average|CPU-Auslastung für Premium-Streamingendpunkte. Diese Daten stehen für Standard-Streamingendpunkte nicht zur Verfügung.|Keine Dimensionen|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Menge der Ausgangsdaten in Byte.|OutputFormat|
|EgressBandwidth|Nein|Ausgangsbandbreite|BitsPerSecond|Average|Ausgangsbandbreite in Bits pro Sekunde.|Keine Dimensionen|
|Requests|Ja|Requests|Anzahl|Gesamt|Anforderungen an einen Streamingendpunkt|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Ja|End-to-End-Wartezeit bei Erfolg|Millisekunden|Average|Durchschnittliche Latenz für erfolgreiche Anforderungen in Millisekunden.|OutputFormat|


## <a name="microsoftmediavideoanalyzers"></a>Microsoft.Media/videoanalyzers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|IngressBytes|Ja|Eingehende Bytes|Byte|Gesamt|Die Anzahl von eingehenden Bytes im Pipelineknoten.|PipelineTopology, Pipeline, Knoten|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Ja|Aktive Renderingsitzungen|Anzahl|Average|Gesamtzahl aktiver Renderingsitzungen|SessionType, SDKVersion|
|AssetsConverted|Ja|Konvertierte Ressourcen|Anzahl|Gesamt|Gesamtzahl der konvertierten Ressourcen|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AnchorsCreated|Ja|Erstellte Anker|Anzahl|Gesamt|Anzahl der erstellten Anker|DeviceFamily, SDKVersion|
|AnchorsDeleted|Ja|Gelöschte Anker|Anzahl|Gesamt|Anzahl der gelöschten Anker|DeviceFamily, SDKVersion|
|AnchorsQueried|Ja|Abgefragte Anker|Anzahl|Gesamt|Anzahl abgefragter Raumanker|DeviceFamily, SDKVersion|
|AnchorsUpdated|Ja|Aktualisierte Anker|Anzahl|Gesamt|Anzahl der aktualisierten Anker|DeviceFamily, SDKVersion|
|PosesFound|Ja|Gefundene Posen|Anzahl|Gesamt|Anzahl zurückgegebener Posen.|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Ja|Tägliche Anker gesamt|Anzahl|Average|Gesamtanzahl von Ankern (täglich)|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Ja|Zugeordnete Poolgröße|Byte|Average|Bereitgestellte Poolgröße|Keine Dimensionen|
|VolumePoolAllocatedToVolumeThroughput|Ja|Dem Pool zugeteilter Durchsatz|Bytes pro Sekunde|Average|Summe des Durchsatzes aller zum Pool gehörenden Volumes|Keine Dimensionen|
|VolumePoolAllocatedUsed|Ja|Zu Volumegröße zugeordneter Pool|Byte|Average|Zugeordnete verwendete Größe des Pools|Keine Dimensionen|
|VolumePoolProvisionedThroughput|Ja|Für den Pool bereitgestellter Durchsatz|Bytes pro Sekunde|Average|Für diesen Pool bereitgestellter Durchsatz|Keine Dimensionen|
|VolumePoolTotalLogicalSize|Ja|Vom Pool genutzte Größe|Byte|Average|Summe der logischen Größe aller zum Pool gehörenden Volumes|Keine Dimensionen|
|VolumePoolTotalSnapshotSize|Ja|Gesamtgröße der Momentaufnahme für den Pool|Byte|Average|Summe der Momentaufnahmegrößen aller Volumes in diesem Pool|Keine Dimensionen|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageReadLatency|Ja|Durchschnittliche Wartezeit beim Lesevorgang|Millisekunden|Average|Durchschnittliche Wartezeit beim Lesen in Millisekunden pro Vorgang|Keine Dimensionen|
|AverageWriteLatency|Ja|Durchschnittliche Wartezeit beim Schreibvorgang|Millisekunden|Average|Durchschnittliche Wartezeit beim Schreiben in Millisekunden pro Vorgang|Keine Dimensionen|
|CbsVolumeBackupActive|Ja|Volumesicherung ausgesetzt?|Anzahl|Average|Ist die Sicherungsrichtlinie für das Volume ausgesetzt? 0, falls ja, 1, falls nein.|Keine Dimensionen|
|CbsVolumeLogicalBackupBytes|Ja|Bytes in Volumesicherung|Byte|Average|Summe der für dieses Volume gesicherten Bytes.|Keine Dimensionen|
|CbsVolumeOperationComplete|Ja|Vorgang der Volumesicherung abgeschlossen?|Anzahl|Average|Wurde der letzte Vorgang zur Sicherung oder Wiederherstellung des Volumes erfolgreich abgeschlossen? 1, falls ja, 0, falls nein.|Keine Dimensionen|
|CbsVolumeOperationTransferredBytes|Ja|Für Volumensicherung zuletzt übertragene Bytes|Byte|Average|Für den letzten Sicherungs-/Wiederherstellungsvorgang insgesamt übertragene Bytes.|Keine Dimensionen|
|CbsVolumeProtected|Ja|Volumesicherung aktiviert?|Anzahl|Average|Ist die Sicherung für das Volume aktiviert? 1, falls ja, 0, falls nein.|Keine Dimensionen|
|OtherThroughput|Ja|Sonstiger Durchsatz|Bytes pro Sekunde|Average|Sonstiger Durchsatz (ohne Lese- oder Schreibvorgang) in Bytes pro Sekunde|Keine Dimensionen|
|ReadIops|Ja|Lese-IOPS|Anzahl pro Sekunde|Average|E/A-Vorgänge lesen pro Sekunde|Keine Dimensionen|
|ReadThroughput|Ja|Lesedurchsatz|Bytes pro Sekunde|Average|Lesedurchsatz in Bytes pro Sekunde|Keine Dimensionen|
|TotalThroughput|Ja|Durchsatz gesamt|Bytes pro Sekunde|Average|Summe aller Durchsätze in Bytes pro Sekunde|Keine Dimensionen|
|VolumeAllocatedSize|Ja|Zugeordnete Größe des Volume|Byte|Average|Die bereitgestellte Größe eines Volumes|Keine Dimensionen|
|VolumeConsumedSizePercentage|Ja|Größe der verbrauchten Menge in Prozent|Percent|Average|Prozentsatz der Nutzung des Volumes, einschließlich Momentaufnahmen.|Keine Dimensionen|
|VolumeCoolTierDataReadSize|Ja|Größe der gelesenen Daten auf kalter Ebene des Volumes|Byte|Average|Mit GET eingelesene Daten pro Volume|Keine Dimensionen|
|VolumeCoolTierDataWriteSize|Ja|Größe der geschriebenen Daten auf kalter Ebene des Volumes|Byte|Average|Mit PUT ausgelagerte Daten pro Volume|Keine Dimensionen|
|VolumeCoolTierSize|Ja|Größe der kalten Ebene des Volumes|Byte|Average|Speicherbedarf des Volumes für kalte Ebene|Keine Dimensionen|
|VolumeLogicalSize|Ja|Vom Volume genutzte Größe|Byte|Average|Logische Größe des Volume (verwendete Bytes)|Keine Dimensionen|
|VolumeSnapshotSize|Ja|Größe der Volumenmomentaufnahme|Byte|Average|Größe aller Momentaufnahmen im Volumen|Keine Dimensionen|
|WriteIops|Ja|Schreib-IOPS|Anzahl pro Sekunde|Average|E/A-Vorgänge schreiben pro Sekunde|Keine Dimensionen|
|WriteThroughput|Ja|Schreibdurchsatz|Bytes pro Sekunde|Average|Schreibdurchsatz in Bytes pro Sekunde|Keine Dimensionen|
|XregionReplicationHealthy|Ja|Gibt an, ob der Volumereplikationsstatus „Fehlerfrei“ lautet|Anzahl|Average|Beziehungsbedingung: 1 oder 0|Keine Dimensionen|
|XregionReplicationLagTime|Ja|Verzögerungszeit bei der Volumereplikation|Sekunden|Average|Die Zeit in Sekunden, um die die Daten in der Spiegelung im Vergleich zur Quelle verzögert sind|Keine Dimensionen|
|XregionReplicationLastTransferDuration|Ja|Übertragungsdauer der letzten Volumereplikation|Sekunden|Average|Die Dauer der letzten Übertragung in Sekunden|Keine Dimensionen|
|XregionReplicationLastTransferSize|Ja|Übertragungsgröße der letzten Volumereplikation|Byte|Average|Bei der letzten Übertragung insgesamt übertragene Bytes|Keine Dimensionen|
|XregionReplicationRelationshipProgress|Ja|Fortschritt der Volumereplikation|Byte|Average|Insgesamt beim aktuellen Übertragungsvorgang übertragene Daten|Keine Dimensionen|
|XregionReplicationRelationshipTransferring|Ja|Gibt an, ob die Übertragung der Volumereplikation ausgeführt wird|Anzahl|Average|Gibt an, ob der Status der Volumereplikation „Wird übertragen“ lautet|Keine Dimensionen|
|XregionReplicationTotalTransferBytes|Ja|Insgesamt bei der Volumereplikation übertragene Bytes|Byte|Average|Kumulative für die Beziehung übertragene Bytes|Keine Dimensionen|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationgateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Nein|Application Gateway-Gesamtzeit|Millisekunden|Average|Durchschnittliche Zeit, bis eine Anforderung verarbeitet und die zugehörige Antwort gesendet wurde. Dies wird berechnet als durchschnittliches Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt, zu dem der Vorgang zum Senden der Antwort abgeschlossen ist. Beachten Sie, dass dies in der Regel die Application Gateway-Verarbeitungszeit, die Zeit für das Durchlaufen des Netzwerks durch die Anforderungs- und Antwortpakete und die Zeit bis zur Antwort des Back-End-Servers einschließt.|Listener|
|AvgRequestCountPerHealthyHost|Nein|Anforderungen pro Minute pro fehlerfreiem Host|Anzahl|Average|Durchschnittliche Anzahl von Anforderungen pro Minute pro fehlerfreiem Back-End-Host in einem Pool|BackendSettingsPool|
|BackendConnectTime|Nein|Back-End-Verbindungszeit|Millisekunden|Average|Zeitaufwand für das Herstellen einer Verbindung mit einem Back-End-Server|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Nein|Antwortzeit für erstes Byte des Back-Ends|Millisekunden|Average|Das Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des ersten Bytes des Antwortheaders und somit eine Abschätzung der Verarbeitungszeit des Back-End-Servers|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Nein|Antwortzeit für letztes Byte des Back-Ends|Millisekunden|Average|Das Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antworttexts|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Ja|Back-End-Antwortstatus|Anzahl|Gesamt|Anzahl der von den Back-End-Membern generierten HTTP-Antwortcodes. Dies schließt nicht die von Application Gateway generierten Antwortcodes ein.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BackendTlsNegotiationError|Ja|Back-End-TLS-Verbindungsfehler|Anzahl|Gesamt|TLS-Verbindungsfehler für Application Gateway-Back-End|BackendHttpSetting, BackendPool, ErrorType|
|BlockedCount|Ja|Regelverteilung für blockierte Web Application Firewall-Anforderungen|Anzahl|Gesamt|Regelverteilung für blockierte Web Application Firewall-Anforderungen|RuleGroup, RuleId|
|BlockedReqCount|Ja|Anzahl blockierter Web Application Firewall-Anforderungen|Anzahl|Gesamt|Anzahl blockierter Web Application Firewall-Anforderungen|Keine Dimensionen|
|BytesReceived|Ja|Empfangene Bytes|Byte|Gesamt|Gesamtanzahl der von Application Gateway von den Clients empfangenen Bytes|Listener|
|BytesSent|Ja|Gesendete Bytes|Byte|Gesamt|Gesamtanzahl der von Application Gateway an die Clients gesendeten Bytes|Listener|
|CapacityUnits|Nein|Aktuelle Kapazitätseinheiten|Anzahl|Average|Verbrauchte Kapazitätseinheiten|Keine Dimensionen|
|ClientRtt|Nein|Client-RTT|Millisekunden|Average|Durchschnittliche Roundtripzeit zwischen Clients und Application Gateway. Diese Metrik gibt an, wie lange es dauert, Verbindungen herzustellen und Bestätigungen zurückzugeben.|Listener|
|ComputeUnits|Nein|Aktuelle Compute-Einheiten|Anzahl|Average|Verbrauchte Compute-Einheiten|Keine Dimensionen|
|CpuUtilization|Nein|CPU-Auslastung|Percent|Average|Aktuelle CPU-Auslastung der Application Gateway-Instanz|Keine Dimensionen|
|CurrentConnections|Ja|Aktuelle Verbindungen|Anzahl|Gesamt|Anzahl von aktuellen Verbindungen mit Application Gateway|Keine Dimensionen|
|EstimatedBilledCapacityUnits|Nein|Geschätzte abgerechnete Kapazitätseinheiten|Anzahl|Average|Geschätzte Kapazitätseinheiten, die in Rechnung gestellt werden|Keine Dimensionen|
|FailedRequests|Ja|Anforderungsfehler|Anzahl|Gesamt|Anzahl von fehlerhaften Anforderungen über Application Gateway|BackendSettingsPool|
|FixedBillableCapacityUnits|Nein|Feste abrechenbare Kapazitätseinheiten|Anzahl|Average|Mindestkapazitätseinheiten, die in Rechnung gestellt werden|Keine Dimensionen|
|HealthyHostCount|Ja|Anzahl von fehlerfreien Hosts|Anzahl|Average|Anzahl von fehlerfreien Back-End-Hosts|BackendSettingsPool|
|MatchedCount|Ja|Gesamtregelverteilung in Web Application Firewall|Anzahl|Gesamt|Gesamtregelverteilung in Web Application Firewall für eingehenden Datenverkehr|RuleGroup, RuleId|
|NewConnectionsPerSecond|Nein|Neue Verbindungen pro Sekunde|Anzahl pro Sekunde|Average|Neue Verbindungen, die pro Sekunde mit Application Gateway hergestellt werden|Keine Dimensionen|
|RejectedConnections|Ja|Zurückgewiesene Verbindungen|Anzahl|Gesamt|Anzahl abgelehnter Verbindungen für Application Gateway-Front-End|Keine Dimensionen|
|ResponseStatus|Ja|Antwortstatus|Anzahl|Gesamt|Von Application Gateway zurückgegebener HTTP-Antwortstatus|HttpStatusGroup|
|Throughput|Nein|Throughput|Bytes pro Sekunde|Average|Anzahl von Bytes pro Sekunde, die das Application Gateway bereitgestellt hat|Keine Dimensionen|
|TlsProtocol|Ja|Client-TLS-Protokoll|Anzahl|Gesamt|Anzahl von TLS- und Nicht-TLS-Anforderungen, die von dem Client initiiert wurden, der eine Verbindung mit Application Gateway hergestellt hat. Um die Verteilung des TLS-Protokolls anzuzeigen, filtern Sie nach dem Dimensions-TLS-Protokoll.|Listener, TlsProtocol|
|TotalRequests|Ja|Anzahl von Anforderungen|Anzahl|Gesamt|Anzahl von erfolgreichen Anforderungen über Application Gateway|BackendSettingsPool|
|UnhealthyHostCount|Ja|Anzahl von fehlerhaften Hosts|Anzahl|Average|Anzahl von fehlerhaften Back-End-Hosts|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Ja|Trefferanzahl für Anwendungsregeln|Anzahl|Gesamt|Häufigkeit, mit der Anwendungsregeln aufgerufen wurden|Status, Grund, Protokoll|
|DataProcessed|Ja|Verarbeitete Datenmenge|Byte|Gesamt|Gesamtmenge der von dieser Firewall verarbeiteten Daten|Keine Dimensionen|
|FirewallHealth|Ja|Integritätszustand der Firewall|Percent|Average|Gibt die Gesamtintegrität dieser Firewall an|Status, Ursache|
|NetworkRuleHit|Ja|Trefferanzahl für Netzwerkregeln|Anzahl|Gesamt|Häufigkeit, mit der Netzwerkregeln aufgerufen wurden|Status, Grund, Protokoll|
|SNATPortUtilization|Ja|SNAT-Portnutzung|Percent|Average|Derzeit verwendete ausgehende SNAT-Ports in Prozent|Protocol|
|Throughput|Nein|Throughput|BitsPerSecond|Average|Von dieser Firewall verarbeiteter Durchsatz|Keine Dimensionen|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|pingmesh|Nein|Bastion-Kommunikationsstatus|Anzahl|Average|Kommunikationsstatus ist 1, wenn die gesamte Kommunikation gut ist, und 0 (null), wenn nicht.|Keine Dimensionen|
|Sitzungen|Nein|Sitzungsanzahl|Anzahl|Gesamt|Anzahl der Sitzungen für die Bastion. Anzeige insgesamt und pro Instanz.|host|
|total|Ja|Gesamter Speicher|Anzahl|Average|Statistiken zu gesamtem Arbeitsspeicher.|host|
|usage_user|Nein|CPU-Auslastung|Anzahl|Average|Statistik zur CPU-Auslastung.|CPU, Host|
|used|Ja|Speicherauslastung|Anzahl|Average|Statistiken zur Arbeitsspeicherauslastung.|host|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Ja|BitsInPerSecond|BitsPerSecond|Average|In Azure eingehende Bits pro Sekunde|Keine Dimensionen|
|BitsOutPerSecond|Ja|BitsOutPerSecond|BitsPerSecond|Average|Aus Azure ausgehende Bits pro Sekunde|Keine Dimensionen|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|QueryVolume|Nein|Abfragevolume|Anzahl|Gesamt|Anzahl von Abfragen für eine DNS-Zone|Keine Dimensionen|
|RecordSetCapacityUtilization|Nein|Kapazitätsauslastung von Datensatzgruppen|Percent|Maximum|Von einer DNS-Zone genutzte Kapazität einer Datensatzgruppe in Prozent|Keine Dimensionen|
|RecordSetCount|Nein|Anzahl von Datensatzgruppen|Anzahl|Maximum|Anzahl von Datensatzgruppen in einer DNS-Zone|Keine Dimensionen|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ArpAvailability|Ja|ARP-Verfügbarkeit|Percent|Average|ARP-Verfügbarkeit von MSEE für alle Peers.|PeeringType, Peer|
|BgpAvailability|Ja|BGP-Verfügbarkeit|Percent|Average|BGP-Verfügbarkeit von MSEE für alle Peers.|PeeringType, Peer|
|BitsInPerSecond|Ja|BitsInPerSecond|BitsPerSecond|Average|In Azure eingehende Bits pro Sekunde|PeeringType, DeviceRole|
|BitsOutPerSecond|Ja|BitsOutPerSecond|BitsPerSecond|Average|Aus Azure ausgehende Bits pro Sekunde|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|Nein|GlobalReachBitsInPerSecond|BitsPerSecond|Average|In Azure eingehende Bits pro Sekunde|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Nein|GlobalReachBitsOutPerSecond|BitsPerSecond|Average|Aus Azure ausgehende Bits pro Sekunde|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Ja|DroppedInBitsPerSecond|BitsPerSecond|Average|Verworfene Dateneingangsbits pro Sekunde|Keine Dimensionen|
|QosDropBitsOutPerSecond|Ja|DroppedOutBitsPerSecond|BitsPerSecond|Average|Verworfene Datenausgangsbits pro Sekunde|Keine Dimensionen|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Ja|BitsInPerSecond|BitsPerSecond|Average|In Azure eingehende Bits pro Sekunde|Keine Dimensionen|
|BitsOutPerSecond|Ja|BitsOutPerSecond|BitsPerSecond|Average|Aus Azure ausgehende Bits pro Sekunde|Keine Dimensionen|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Nein|BitsInPerSecond|BitsPerSecond|Average|In Azure eingehende Bits pro Sekunde|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Nein|BitsOutPerSecond|BitsPerSecond|Average|Aus Azure ausgehende Bits pro Sekunde|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Ja|Anzahl der für Peer angekündigten Routen (Vorschau)|Anzahl|Maximum|Anzahl der für Peer angekündigten Routen nach ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Ja|Anzahl der von Peer gelernten Routen (Vorschau)|Anzahl|Maximum|Anzahl der von Peer gelernten Routen nach ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Ja|CPU-Auslastung|Percent|Average|CPU-Auslastung des ExpressRoute-Gateways|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|Nein|Häufigkeit der Routenänderung (Vorschau)|Anzahl|Gesamt|Häufigkeit der Routenänderung in ExpressRoute-Gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|Nein|Anzahl der VMs im virtuellen Netzwerk (Vorschau)|Anzahl|Maximum|Anzahl der virtuellen Computer im virtuellen Netzwerk|Keine Dimensionen|
|ExpressRouteGatewayPacketsPerSecond|Nein|Pakete pro Sekunde|Anzahl pro Sekunde|Average|Paketanzahl von ExpressRoute-Gateways|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdminState|Ja|AdminState|Anzahl|Average|Administratorstatus des Ports|Link|
|LineProtocol|Ja|LineProtocol|Anzahl|Average|Zeilenprotokollstatus des Ports|Link|
|PortBitsInPerSecond|Nein|BitsInPerSecond|BitsPerSecond|Average|In Azure eingehende Bits pro Sekunde|Link|
|PortBitsOutPerSecond|Nein|BitsOutPerSecond|BitsPerSecond|Average|Aus Azure ausgehende Bits pro Sekunde|Link|
|RxLightLevel|Ja|RxLightLevel|Anzahl|Average|Empfangener Lichtpegel in dBm|Link, Lane|
|TxLightLevel|Ja|TxLightLevel|Anzahl|Average|Gesendeter Lichtpegel in dBm|Link, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Ja|Prozentsatz der Back-End-Integrität|Percent|Average|Der Prozentsatz der erfolgreichen Integritätstests vom HTTP/S-Proxy zu Back-Ends|Backend, BackendPool|
|BackendRequestCount|Ja|Back-End-Anforderungsanzahl|Anzahl|Gesamt|Die Anzahl der vom HTTP/S-Proxy an Back-Ends gesendeten Anforderungen|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Ja|Latenz der Back-End-Anforderung|Millisekunden|Average|Die Zeitspanne zwischen dem Senden der Anforderung durch den HTTP/S-Proxy an das Back-End und dem Empfangen des letzten Antwortbytes durch den HTTP/S-Proxy vom Back-End|Back-End|
|BillableResponseSize|Ja|Größe von abrechnungsfähigen Antworten|Byte|Gesamt|Die Anzahl der abrechnungsfähigen Bytes (mindestens 2 KB pro Anforderung), die als Antworten vom HTTP/S-Proxy an Clients gesendet wurden.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Ja|Anzahl der Anforderungen|Anzahl|Gesamt|Die Anzahl der vom HTTP/S-Proxy bereitgestellten Clientanforderungen|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Ja|Anforderungsgröße|Byte|Gesamt|Die Anzahl der von Clients als Anforderungen an den HTTP/S-Proxy gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Ja|Antwortgröße|Byte|Gesamt|Die Anzahl der vom HTTP/S-Proxy als Antworten an Clients gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Ja|Gesamtlatenz|Millisekunden|Average|Die Zeitspanne zwischen dem Empfang der Clientanforderung durch den HTTP/S-Proxy und der Bestätigung des letzten Antwortbytes vom HTTP/S-Proxy durch den Client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Ja|Anforderungsanzahl für die Web Application Firewall|Anzahl|Gesamt|Die Anzahl der von der Web Application Firewall verarbeiteten Clientanforderungen|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Nein|Zugeordnete SNAT-Ports|Anzahl|Average|Gesamtanzahl von SNAT-Ports, die im Zeitraum zugeordnet wurden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|ByteCount|Ja|Byteanzahl|Byte|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Ja|Integritätsteststatus|Anzahl|Average|Durchschnittlicher Load Balancer-Integritätsteststatus pro Zeitdauer|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Ja|Paketzahl|Anzahl|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Ja|Anzahl von SNAT-Verbindungen|Anzahl|Gesamt|Gesamtanzahl von neuen SNAT-Verbindungen, die innerhalb des Zeitraums erstellt wurden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Ja|SYN-Anzahl|Anzahl|Gesamt|Gesamtanzahl von SYN-Paketen, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|Nein|Verwendete SNAT-Ports|Anzahl|Average|Gesamtanzahl von SNAT-Ports, die im Zeitraum verwendet wurden|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|VipAvailability|Ja|Datenpfadverfügbarkeit|Anzahl|Average|Durchschnittliche Load Balancer-Datenpfadverfügbarkeit pro Zeitdauer|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ByteCount|Ja|Byte|Byte|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|Protokoll, Richtung|
|DatapathAvailability|Ja|Verfügbarkeit von Datapath (Vorschau)|Anzahl|Average|Verfügbarkeit von Datapath auf NAT-Gateway|Keine Dimensionen|
|PacketCount|Ja|Pakete|Anzahl|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|Protokoll, Richtung|
|PacketDropCount|Ja|Verworfene Pakete|Anzahl|Gesamt|Anzahl verworfener Pakete|Keine Dimensionen|
|SNATConnectionCount|Ja|Anzahl von SNAT-Verbindungen|Anzahl|Gesamt|Gleichzeitig aktive Verbindungen insgesamt|Protokoll, ConnectionState|
|TotalConnectionCount|Ja|Gesamtanzahl von SNAT-Verbindungen|Anzahl|Gesamt|Gesamtanzahl aktiver SNAT-Verbindungen|Protocol|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Ja|Empfangene Bytes|Byte|Gesamt|Anzahl der durch die Netzwerkschnittstelle empfangenen Bytes|Keine Dimensionen|
|BytesSentRate|Ja|Gesendete Bytes|Byte|Gesamt|Anzahl der durch die Netzwerkschnittstelle gesendeten Bytes|Keine Dimensionen|
|PacketsReceivedRate|Ja|Empfangene Pakete|Anzahl|Gesamt|Anzahl der durch die Netzwerkschnittstelle empfangenen Pakete|Keine Dimensionen|
|PacketsSentRate|Ja|Gesendete Pakete|Anzahl|Gesamt|Anzahl der durch die Netzwerkschnittstelle gesendeten Pakete|Keine Dimensionen|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Ja|Durchschn. Roundtripzeit (ms) (klassisch)|Millisekunden|Average|Durchschnittliche Netzwerk-Roundtripzeit (ms) für die Konnektivitätsüberwachung von Testdatenverkehr, der zwischen Quelle und Ziel gesendet wurde.|Keine Dimensionen|
|ChecksFailedPercent|Ja|Fehler bei Überprüfungen in Prozent|Percent|Average|Fehlerhafte Tests bei Konnektivitätsüberwachung in Prozent|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Ja|Fehlerhafte Tests in Prozent (klassisch)|Percent|Average|Fehlerhafte Tests bei Konnektivitätsüberwachung in Prozent|Keine Dimensionen|
|RoundTripTimeMs|Ja|Roundtripzeit (ms)|Millisekunden|Average|Roundtripzeit in Millisekunden für die Tests bei Konnektivitätsüberwachung|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Ja|Testergebnis|Anzahl|Average|Testergebnisse des Verbindungsmonitors|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>microsoft.network/p2svpngateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|P2SBandwidth|Ja|P2S-Gatewaybandbreite|Bytes pro Sekunde|Average|Point-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Instanz|
|P2SConnectionCount|Ja|Anzahl der P2S-Verbindungen|Anzahl|Gesamt|Anzahl der Point-to-Site-Verbindung eines Gateways|Protocol, Instance|
|UserVpnRouteCount|Nein|Anzahl der Benutzer-VPN-Routen|Anzahl|Gesamt|Anzahl der vom Gateway gelernten P2S-Benutzer-VPN-Routen|RouteType, Instance|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|QueryVolume|Ja|Abfragevolume|Anzahl|Gesamt|Anzahl von Abfragen für eine private DNS-Zone|Keine Dimensionen|
|RecordSetCapacityUtilization|Nein|Kapazitätsauslastung von Datensatzgruppen|Percent|Maximum|Von einer privaten DNS-Zone genutzte Kapazität einer Datensatzgruppe in Prozent|Keine Dimensionen|
|RecordSetCount|Ja|Anzahl von Datensatzgruppen|Anzahl|Maximum|Anzahl von Datensatzgruppen für eine private DNS-Zone|Keine Dimensionen|
|VirtualNetworkLinkCapacityUtilization|Nein|Kapazitätsauslastung von VNET-Verknüpfungen|Percent|Maximum|Von einer privaten DNS-Zone genutzte Kapazität einer VNET-Verknüpfung in Prozent|Keine Dimensionen|
|VirtualNetworkLinkCount|Ja|Anzahl von VNET-Verknüpfungen|Anzahl|Maximum|Anzahl von virtuellen Netzwerken, die mit einer privaten DNS-Zone verknüpft sind|Keine Dimensionen|
|VirtualNetworkWithRegistrationCapacityUtilization|Nein|Kapazitätsauslastung für VNET-Registrierungslink|Percent|Maximum|Von einer privaten DNS-Zone genutzte Kapazität einer VNET-Verknüpfung mit automatischer Registrierung in Prozent|Keine Dimensionen|
|VirtualNetworkWithRegistrationLinkCount|Ja|Anzahl von VNET-Registrierungslinks|Anzahl|Maximum|Anzahl von VNETs, die mit einer privaten DNS-Zone verknüpft sind und für die die automatische Registrierung aktiviert ist|Keine Dimensionen|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PEBytesIn|Ja|Bytes In|Anzahl|Gesamt|Gesamtanzahl ausgehender Bytes|Keine Dimensionen|
|PEBytesOut|Ja|Bytes Out|Anzahl|Gesamt|Gesamtanzahl ausgehender Bytes|Keine Dimensionen|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PLSBytesIn|Ja|Bytes In|Anzahl|Gesamt|Gesamtanzahl ausgehender Bytes|PrivateLinkServiceId|
|PLSBytesOut|Ja|Bytes Out|Anzahl|Gesamt|Gesamtanzahl ausgehender Bytes|PrivateLinkServiceId|
|PLSNatPortsUsage|Ja|Verwendung von NAT-Ports|Percent|Average|Verwendung von NAT-Ports|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ByteCount|Ja|Byteanzahl|Byte|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|Port, Richtung|
|BytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte Bytes|Keine Dimensionen|
|BytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Bytes|Keine Dimensionen|
|BytesInDDoS|Ja|Als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende Bytes|Keine Dimensionen|
|DDoSTriggerSYNPackets|Ja|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|DDoSTriggerTCPPackets|Ja|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|DDoSTriggerUDPPackets|Ja|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|IfUnderDDoSAttack|Ja|Unter DDoS-Angriff oder nicht|Anzahl|Maximum|Unter DDoS-Angriff oder nicht|Keine Dimensionen|
|PacketCount|Ja|Paketzahl|Anzahl|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|Port, Richtung|
|PacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte Pakete|Keine Dimensionen|
|PacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Pakete|Keine Dimensionen|
|PacketsInDDoS|Ja|Als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende Pakete|Keine Dimensionen|
|SynCount|Ja|SYN-Anzahl|Anzahl|Gesamt|Gesamtanzahl von SYN-Paketen, die im Zeitraum übertragen wurden|Port, Richtung|
|TCPBytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Bytes|Keine Dimensionen|
|TCPBytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Bytes|Keine Dimensionen|
|TCPBytesInDDoS|Ja|Als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende TCP-Bytes|Keine Dimensionen|
|TCPPacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Pakete|Keine Dimensionen|
|TCPPacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Pakete|Keine Dimensionen|
|TCPPacketsInDDoS|Ja|Als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende TCP-Pakete|Keine Dimensionen|
|UDPBytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Bytes|Keine Dimensionen|
|UDPBytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Bytes|Keine Dimensionen|
|UDPBytesInDDoS|Ja|Als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende UDP-Bytes|Keine Dimensionen|
|UDPPacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Pakete|Keine Dimensionen|
|UDPPacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Pakete|Keine Dimensionen|
|UDPPacketsInDDoS|Ja|Als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende UDP-Pakete|Keine Dimensionen|
|VipAvailability|Ja|Datenpfadverfügbarkeit|Anzahl|Average|Durchschnittliche Verfügbarkeit der IP-Adresse pro Zeitdauer|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Ja|Endpunktstatus nach Endpunkt|Anzahl|Maximum|1, wenn der Teststatus des Endpunkts „Aktiviert“ ist, andernfalls 0.|EndpointName|
|QpsByEndpoint|Ja|Abfragen nach zurückgegebenem Endpunkt|Anzahl|Gesamt|Häufigkeit, mit der ein Endpunkt des Traffic Managers innerhalb des angegebenen Zeitrahmens zurückgegeben wurde|EndpointName|


## <a name="microsoftnetworkvirtualhubs"></a>Microsoft.Network/virtualHubs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BgpPeerStatus|Nein|Status des BGP-Peers|Anzahl|Maximum|1 – Verbunden, 0 – Nicht verbunden|routeserviceinstance, bgppeerip, bgppeertype|
|CountOfRoutesAdvertisedToPeer|Nein|Anzahl der für Peer angekündigten Routen|Anzahl|Maximum|Gesamtzahl der für Peer angekündigten Routen|routeserviceinstance, bgppeerip, bgppeertype|
|CountOfRoutesLearnedFromPeer|Nein|Anzahl der von Peer gelernten Routen|Anzahl|Maximum|Gesamtzahl der von Peer gelernten Routen|routeserviceinstance, bgppeerip, bgppeertype|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>microsoft.network/virtualnetworkgateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageBandwidth|Ja|S2S-Gatewaybandbreite|Bytes pro Sekunde|Average|Site-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Instanz|
|BgpPeerStatus|Nein|Status des BGP-Peers|Anzahl|Average|Status des BGP-Peers|BgpPeerAddress, Instance|
|BgpRoutesAdvertised|Ja|Angekündigte BGP-Routen|Anzahl|Gesamt|Anzahl der über einen Tunnel angekündigten BGP-Routen|BgpPeerAddress, Instance|
|BgpRoutesLearned|Ja|Gelernte BGP-Routen|Anzahl|Gesamt|Anzahl der über einen Tunnel gelernten BGP-Routen|BgpPeerAddress, Instance|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Ja|Anzahl der für Peer angekündigten Routen (Vorschau)|Anzahl|Maximum|Anzahl der für Peer angekündigten Routen nach ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Ja|Anzahl der von Peer gelernten Routen (Vorschau)|Anzahl|Maximum|Anzahl der von Peer gelernten Routen nach ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Ja|CPU-Auslastung|Percent|Average|CPU-Auslastung des ExpressRoute-Gateways|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|Nein|Häufigkeit der Routenänderung (Vorschau)|Anzahl|Gesamt|Häufigkeit der Routenänderung in ExpressRoute-Gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|Nein|Anzahl der VMs im virtuellen Netzwerk (Vorschau)|Anzahl|Maximum|Anzahl der virtuellen Computer im virtuellen Netzwerk|roleInstance|
|ExpressRouteGatewayPacketsPerSecond|Nein|Pakete pro Sekunde|Anzahl pro Sekunde|Average|Paketanzahl von ExpressRoute-Gateways|roleInstance|
|MmsaCount|Ja|MMSA-Anzahl des Tunnels|Anzahl|Gesamt|MMSA-Anzahl|ConnectionName, RemoteIP, Instance|
|P2SBandwidth|Ja|P2S-Gatewaybandbreite|Bytes pro Sekunde|Average|Point-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Instanz|
|P2SConnectionCount|Ja|Anzahl der P2S-Verbindungen|Anzahl|Gesamt|Anzahl der Point-to-Site-Verbindung eines Gateways|Protocol, Instance|
|QmsaCount|Ja|QMSA-Anzahl des Tunnels|Anzahl|Gesamt|QMSA-Anzahl|ConnectionName, RemoteIP, Instance|
|TunnelAverageBandwidth|Ja|Bandbreite des Tunnels|Bytes pro Sekunde|Average|Durchschnittliche Bandbreite eines Tunnels in Bytes pro Sekunde|ConnectionName, RemoteIP, Instance|
|TunnelEgressBytes|Ja|Vom Tunnel ausgehende Bytes|Byte|Gesamt|Ausgehende Bytes eines Tunnels|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropCount|Ja|Anzahl ausgehender gelöschter Pakete eines Tunnels|Anzahl|Gesamt|Anzahl der ausgehenden gelöschten Pakete nach Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropTSMismatch|Ja|Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Anzahl|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten ausgehenden Pakete|ConnectionName, RemoteIP, Instance|
|TunnelEgressPackets|Ja|Vom Tunnel ausgehende Pakete|Anzahl|Gesamt|Ausgehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelIngressBytes|Ja|In Tunnel eingehende Bytes|Byte|Gesamt|Eingehende Bytes eines Tunnels|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropCount|Ja|Anzahl eingehender gelöschter Pakete eines Tunnels|Anzahl|Gesamt|Anzahl der eingehenden gelöschten Pakete nach Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropTSMismatch|Ja|Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Anzahl|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten eingehenden Pakete|ConnectionName, RemoteIP, Instance|
|TunnelIngressPackets|Ja|In Tunnel eingehende Pakete|Anzahl|Gesamt|Eingehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelNatAllocations|Nein|Tunnel-NAT-Zuordnungen|Anzahl|Gesamt|Anzahl der Zuordnungen für eine NAT-Regel in einem Tunnel|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedBytes|Nein|Tunnel-NAT-Bytes|Byte|Gesamt|Anzahl von Bytes, die aufgrund einer NAT-Regel in einem Tunnel einer NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedPackets|Nein|Tunnel-NAT-Pakete|Anzahl|Gesamt|Anzahl von Paketen, die aufgrund einer NAT-Regel in einem Tunnel einer NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatFlowCount|Nein|Tunnel-NAT-Flows|Anzahl|Gesamt|Anzahl der NAT-Flows in einem Tunnel nach Flowtyp und NAT-Regel|NatRule, FlowType, ConnectionName, RemoteIP, Instance|
|TunnelNatPacketDrop|Nein|Tunnel-NAT-Paketverluste|Anzahl|Gesamt|Anzahl der in einem Tunnel einer NAT unterzogenen Pakete, die verloren gegangen sind, nach Verlusttyp und NAT-Regel|NatRule, DropType, ConnectionName, RemoteIP, Instance|
|TunnelPeakPackets|Ja|PPS-Spitzenwert eines Tunnels|Anzahl|Maximum|Spitzenwert für Pakete pro Sekunde eines Tunnels|ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedBytes|Nein|Tunnel-Reverse-NAT-Bytes|Byte|Gesamt|Anzahl von Bytes, die aufgrund einer NAT-Regel in einem Tunnel einer Reverse-NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedPackets|Nein|Tunnel-Reverse-NAT-Pakete|Anzahl|Gesamt|Anzahl von Paketen in einem Tunnel, die aufgrund einer NAT-Regel einer Reverse-NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelTotalFlowCount|Ja|Gesamtanzahl von Flows für einen Tunnel|Anzahl|Gesamt|Gesamtanzahl von Flows für einen Tunnel|ConnectionName, RemoteIP, Instance|
|UserVpnRouteCount|Nein|Anzahl der Benutzer-VPN-Routen|Anzahl|Gesamt|Anzahl der vom Gateway gelernten P2S-Benutzer-VPN-Routen|RouteType, Instance|
|VnetAddressPrefixCount|Ja|Anzahl der VNET-Adresspräfixe|Anzahl|Gesamt|Anzahl der VNET-Adresspräfixe hinter dem Gateway|Instanz|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte Bytes|ProtectedIPAddress|
|BytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Bytes|ProtectedIPAddress|
|BytesInDDoS|Ja|Als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende Bytes|ProtectedIPAddress|
|DDoSTriggerSYNPackets|Ja|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|ProtectedIPAddress|
|DDoSTriggerTCPPackets|Ja|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|ProtectedIPAddress|
|DDoSTriggerUDPPackets|Ja|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|ProtectedIPAddress|
|IfUnderDDoSAttack|Ja|Unter DDoS-Angriff oder nicht|Anzahl|Maximum|Unter DDoS-Angriff oder nicht|ProtectedIPAddress|
|PacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte Pakete|ProtectedIPAddress|
|PacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Pakete|ProtectedIPAddress|
|PacketsInDDoS|Ja|Als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende Pakete|ProtectedIPAddress|
|PingMeshAverageRoundtripMs|Ja|Roundtripzeit für Pings an eine VM|Millisekunden|Average|Roundtripzeit für an eine Ziel-VM gesendete Pings|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Ja|Fehlerhafte Pings an einen virtuellen Computer|Percent|Average|Prozentualer Anteil der Anzahl fehlerhafter Pings an der Gesamtanzahl der gesendeten Pings einer Ziel-VM|SourceCustomerAddress, DestinationCustomerAddress|
|TCPBytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Bytes|ProtectedIPAddress|
|TCPBytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Bytes|ProtectedIPAddress|
|TCPBytesInDDoS|Ja|Als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende TCP-Bytes|ProtectedIPAddress|
|TCPPacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Pakete|ProtectedIPAddress|
|TCPPacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Pakete|ProtectedIPAddress|
|TCPPacketsInDDoS|Ja|Als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende TCP-Pakete|ProtectedIPAddress|
|UDPBytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Bytes|ProtectedIPAddress|
|UDPBytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Bytes|ProtectedIPAddress|
|UDPBytesInDDoS|Ja|Als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende UDP-Bytes|ProtectedIPAddress|
|UDPPacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Pakete|ProtectedIPAddress|
|UDPPacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Pakete|ProtectedIPAddress|
|UDPPacketsInDDoS|Ja|Als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende UDP-Pakete|ProtectedIPAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PeeringAvailability|Ja|BGP-Verfügbarkeit|Percent|Average|BGP-Verfügbarkeit zwischen VirtualRouter und Remotepeers|Peer|


## <a name="microsoftnetworkvpngateways"></a>microsoft.network/vpngateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageBandwidth|Ja|S2S-Gatewaybandbreite|Bytes pro Sekunde|Average|Site-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Instanz|
|BgpPeerStatus|Nein|Status des BGP-Peers|Anzahl|Average|Status des BGP-Peers|BgpPeerAddress, Instance|
|BgpRoutesAdvertised|Ja|Angekündigte BGP-Routen|Anzahl|Gesamt|Anzahl der über einen Tunnel angekündigten BGP-Routen|BgpPeerAddress, Instance|
|BgpRoutesLearned|Ja|Gelernte BGP-Routen|Anzahl|Gesamt|Anzahl der über einen Tunnel gelernten BGP-Routen|BgpPeerAddress, Instance|
|MmsaCount|Ja|MMSA-Anzahl des Tunnels|Anzahl|Gesamt|MMSA-Anzahl|ConnectionName, RemoteIP, Instance|
|QmsaCount|Ja|QMSA-Anzahl des Tunnels|Anzahl|Gesamt|QMSA-Anzahl|ConnectionName, RemoteIP, Instance|
|TunnelAverageBandwidth|Ja|Bandbreite des Tunnels|Bytes pro Sekunde|Average|Durchschnittliche Bandbreite eines Tunnels in Bytes pro Sekunde|ConnectionName, RemoteIP, Instance|
|TunnelEgressBytes|Ja|Vom Tunnel ausgehende Bytes|Byte|Gesamt|Ausgehende Bytes eines Tunnels|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropCount|Ja|Anzahl ausgehender gelöschter Pakete eines Tunnels|Anzahl|Gesamt|Anzahl der ausgehenden gelöschten Pakete nach Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropTSMismatch|Ja|Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Anzahl|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten ausgehenden Pakete|ConnectionName, RemoteIP, Instance|
|TunnelEgressPackets|Ja|Vom Tunnel ausgehende Pakete|Anzahl|Gesamt|Ausgehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelIngressBytes|Ja|In Tunnel eingehende Bytes|Byte|Gesamt|Eingehende Bytes eines Tunnels|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropCount|Ja|Anzahl eingehender gelöschter Pakete eines Tunnels|Anzahl|Gesamt|Anzahl der eingehenden gelöschten Pakete nach Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropTSMismatch|Ja|Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Anzahl|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten eingehenden Pakete|ConnectionName, RemoteIP, Instance|
|TunnelIngressPackets|Ja|In Tunnel eingehende Pakete|Anzahl|Gesamt|Eingehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP, Instance|
|TunnelNatAllocations|Nein|Tunnel-NAT-Zuordnungen|Anzahl|Gesamt|Anzahl der Zuordnungen für eine NAT-Regel in einem Tunnel|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedBytes|Nein|Tunnel-NAT-Bytes|Byte|Gesamt|Anzahl von Bytes, die aufgrund einer NAT-Regel in einem Tunnel einer NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedPackets|Nein|Tunnel-NAT-Pakete|Anzahl|Gesamt|Anzahl von Paketen, die aufgrund einer NAT-Regel in einem Tunnel einer NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatFlowCount|Nein|Tunnel-NAT-Flows|Anzahl|Gesamt|Anzahl der NAT-Flows in einem Tunnel nach Flowtyp und NAT-Regel|NatRule, FlowType, ConnectionName, RemoteIP, Instance|
|TunnelNatPacketDrop|Nein|Tunnel-NAT-Paketverluste|Anzahl|Gesamt|Anzahl der in einem Tunnel einer NAT unterzogenen Pakete, die verloren gegangen sind, nach Verlusttyp und NAT-Regel|NatRule, DropType, ConnectionName, RemoteIP, Instance|
|TunnelPeakPackets|Ja|PPS-Spitzenwert eines Tunnels|Anzahl|Maximum|Spitzenwert für Pakete pro Sekunde eines Tunnels|ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedBytes|Nein|Tunnel-Reverse-NAT-Bytes|Byte|Gesamt|Anzahl von Bytes, die aufgrund einer NAT-Regel in einem Tunnel einer Reverse-NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedPackets|Nein|Tunnel-Reverse-NAT-Pakete|Anzahl|Gesamt|Anzahl von Paketen in einem Tunnel, die aufgrund einer NAT-Regel einer Reverse-NAT unterzogen wurden|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelTotalFlowCount|Ja|Gesamtanzahl von Flows für einen Tunnel|Anzahl|Gesamt|Gesamtanzahl von Flows für einen Tunnel|ConnectionName, RemoteIP, Instance|
|VnetAddressPrefixCount|Ja|Anzahl der VNET-Adresspräfixe|Anzahl|Gesamt|Anzahl der VNET-Adresspräfixe hinter dem Gateway|Instanz|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|incoming|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Übermittlungs-API-Aufrufen an. |Keine Dimensionen|
|incoming.all.failedrequests|Ja|Alle eingehenden fehlerhaften Anforderungen|Anzahl|Gesamt|Gesamtzahl eingehender fehlerhafter Anforderungen für einen Notification Hub|Keine Dimensionen|
|incoming.all.requests|Ja|Alle eingehenden Anforderungen|Anzahl|Gesamt|Gesamtzahl eingehender Anforderungen für einen Notification Hub|Keine Dimensionen|
|incoming.scheduled|Ja|Geplante Pushbenachrichtigungen (gesendet)|Anzahl|Gesamt|Geplante Pushbenachrichtigungen (abgebrochen)|Keine Dimensionen|
|incoming.scheduled.cancel|Ja|Geplante Pushbenachrichtigungen (abgebrochen)|Anzahl|Gesamt|Geplante Pushbenachrichtigungen (abgebrochen)|Keine Dimensionen|
|installation.all|Ja|Installationsverwaltungsvorgänge|Anzahl|Gesamt|Installationsverwaltungsvorgänge|Keine Dimensionen|
|installation.delete|Ja|Installationsvorgänge löschen|Anzahl|Gesamt|Installationsvorgänge löschen|Keine Dimensionen|
|installation.get|Ja|Installationsvorgänge abrufen|Anzahl|Gesamt|Installationsvorgänge abrufen|Keine Dimensionen|
|installation.patch|Ja|Patchinstallationsvorgänge|Anzahl|Gesamt|Patchinstallationsvorgänge|Keine Dimensionen|
|installation.upsert|Ja|Installationsvorgänge erstellen oder aktualisieren|Anzahl|Gesamt|Installationsvorgänge erstellen oder aktualisieren|Keine Dimensionen|
|notificationhub.pushes|Ja|Alle ausgehenden Benachrichtigungen|Anzahl|Gesamt|Alle ausgehenden Benachrichtigungen des Benachrichtigungs-Hub|Keine Dimensionen|
|outgoing.allpns.badorexpiredchannel|Ja|Kanalfehler (unzulässig oder abgelaufen)|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der Kanal, das Token oder die Registrierungs-ID in der Registrierung abgelaufen oder ungültig war.|Keine Dimensionen|
|outgoing.allpns.channelerror|Ja|Kanalfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der Kanal ungültig, nicht der richtigen App zugeordnet, gedrosselt oder abgelaufen war.|Keine Dimensionen|
|outgoing.allpns.invalidpayload|Ja|Nutzlastfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil vom PNS ein Nutzlastfehler zurückgegeben wurde.|Keine Dimensionen|
|outgoing.allpns.pnserror|Ja|Fehler des externen Benachrichtigungssystems|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil ein Problem bei der Kommunikation mit dem PNS vorlag (ausschließlich Authentifizierungsprobleme).|Keine Dimensionen|
|outgoing.allpns.success|Ja|Erfolgreiche Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.apns.badchannel|Ja|APNS-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das Token ungültig ist (APNS-Statuscode: 8).|Keine Dimensionen|
|outgoing.apns.expiredchannel|Ja|APNS-Fehler durch abgelaufenen Kanal|Anzahl|Gesamt|Die Anzahl von Token, die aufgrund des APNS-Feedbackkanals ungültig gemacht wurden.|Keine Dimensionen|
|outgoing.apns.invalidcredentials|Ja|APNS-Autorisierungsfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.apns.invalidnotificationsize|Ja|APNS-Fehler durch ungültige Benachrichtigungsgröße|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast zu groß war (APNS-Statuscode: 7).|Keine Dimensionen|
|outgoing.apns.pnserror|Ja|APNS-Fehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit APNS aufgetreten sind.|Keine Dimensionen|
|outgoing.apns.success|Ja|Erfolgreiche APNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.gcm.authenticationerror|Ja|GCM-Authentifizierungsfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die angegebenen Anmeldeinformationen vom PNS nicht akzeptiert wurden, die Anmeldeinformationen blockiert wurden oder die SenderId in der App nicht richtig konfiguriert wurde (GCM-Ergebnis: MismatchedSenderId).|Keine Dimensionen|
|outgoing.gcm.badchannel|Ja|GCM-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung nicht erkannt wurde (GCM-Ergebnis: Ungültige Registrierung).|Keine Dimensionen|
|outgoing.gcm.expiredchannel|Ja|GCM-Fehler durch abgelaufenen Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung abgelaufen war (GCM-Ergebnis: NotRegistered).|Keine Dimensionen|
|outgoing.gcm.invalidcredentials|Ja|GCM-Autorisierungsfehler (ungültige Anmeldeinformationen)|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.gcm.invalidnotificationformat|Ja|Ungültiges GCM-Benachrichtigungsformat|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast nicht richtig formatiert war (GCM-Ergebnis: InvalidDataKey oder InvalidTtl).|Keine Dimensionen|
|outgoing.gcm.invalidnotificationsize|Ja|GCM-Fehler durch ungültige Benachrichtigungsgröße|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast zu groß war (GCM-Ergebnis: MessageTooBig).|Keine Dimensionen|
|outgoing.gcm.pnserror|Ja|GCM-Fehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit GCM aufgetreten sind.|Keine Dimensionen|
|outgoing.gcm.success|Ja|Erfolgreiche GCM-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.gcm.throttled|Ja|Gedrosselte GCM-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App von GCM gedrosselt wurde (GCM-Statuscode: 501 - 599 oder result:Unavailable).|Keine Dimensionen|
|outgoing.gcm.wrongchannel|Ja|GCM-Fehler durch falschen Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung nicht der aktuellen App zugeordnet war (GCM-Ergebnis: InvalidPackageName).|Keine Dimensionen|
|outgoing.mpns.authenticationerror|Ja|MPNS-Authentifizierungsfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.mpns.badchannel|Ja|MPNS-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der ChannelURI in der Registrierung nicht erkannt wurde (MPNS-Status: 404 – Nicht gefunden).|Keine Dimensionen|
|outgoing.mpns.channeldisconnected|Ja|MPNS-Kanal nicht verbunden|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Verbindung für den ChannelURI in der Registrierung getrennt wurde (MPNS-Status: 412 – Nicht gefunden).|Keine Dimensionen|
|outgoing.mpns.dropped|Ja|Verworfene MPNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die von MPNS verworfen wurden (MPNS-Antwortheader: X-NotificationStatus: QueueFull oder Suppressed).|Keine Dimensionen|
|outgoing.mpns.invalidcredentials|Ja|Ungültige MPNS-Anmeldeinformationen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.mpns.invalidnotificationformat|Ja|Ungültiges MPNS-Benachrichtigungsformat|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast der Benachrichtigung zu groß war.|Keine Dimensionen|
|outgoing.mpns.pnserror|Ja|MPNS-Fehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit MPNS aufgetreten sind.|Keine Dimensionen|
|outgoing.mpns.success|Ja|Erfolgreiche MPNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.mpns.throttled|Ja|Gedrosselte MPNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App vom MPNS gedrosselt wird (WNS-MPNS: 406 – Nicht annehmbar).|Keine Dimensionen|
|outgoing.wns.authenticationerror|Ja|WNS-Authentifizierungsfehler|Anzahl|Gesamt|Die Benachrichtigung wurde nicht übermittelt, weil Fehler bei der Kommunikation mit Windows Live aufgetreten sind, die Anmeldeinformationen ungültig waren oder das falsche Token verwendet wurde.|Keine Dimensionen|
|outgoing.wns.badchannel|Ja|WNS-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der ChannelURI in der Registrierung nicht erkannt wurde (WNS-Status: 404 – Nicht gefunden).|Keine Dimensionen|
|outgoing.wns.channeldisconnected|Ja|WNS-Kanal nicht verbunden|Anzahl|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde (WNS-Antwortheader: X-WNS-DeviceConnectionStatus: disconnected).|Keine Dimensionen|
|outgoing.wns.channelthrottled|Ja|WNS-Kanal gedrosselt|Anzahl|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde (WNS-Antwortheader: X-WNS-NotificationStatus:channelThrottled).|Keine Dimensionen|
|outgoing.wns.dropped|Ja|Verworfene WNS-Benachrichtigungen|Anzahl|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde („X-WNS-NotificationStatus: dropped“, aber nicht „X-WNS-DeviceConnectionStatus: disconnected“).|Keine Dimensionen|
|outgoing.wns.expiredchannel|Ja|WNS-Fehler durch abgelaufenen Kanal|Anzahl|Gesamt|Die Anzahl von nicht erfolgreichen Pushvorgängen, weil der ChannelURI abgelaufen ist (WNS-Status: 410 – Fehlend).|Keine Dimensionen|
|outgoing.wns.invalidcredentials|Ja|WNS-Autorisierungsfehler (ungültige Anmeldeinformationen)|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden. (Windows Live erkennt die Anmeldeinformationen nicht.)|Keine Dimensionen|
|outgoing.wns.invalidnotificationformat|Ja|Ungültiges WNS-Benachrichtigungsformat|Anzahl|Gesamt|Das Format der Benachrichtigung ist ungültig (WNS-Status: 400). Beachten Sie, dass von WNS nicht alle ungültigen Nutzlasten abgelehnt werden.|Keine Dimensionen|
|outgoing.wns.invalidnotificationsize|Ja|WNS-Fehler durch ungültige Benachrichtigungsgröße|Anzahl|Gesamt|Die Benachrichtigungsnutzlast ist zu groß (WNS-Status: 413).|Keine Dimensionen|
|outgoing.wns.invalidtoken|Ja|WNS-Autorisierungsfehler (ungültiges Token)|Anzahl|Gesamt|Das für WNS bereitgestellte Token ist nicht gültig (WNS-Status: 401 – Nicht autorisiert).|Keine Dimensionen|
|outgoing.wns.pnserror|Ja|WNS-Fehler|Anzahl|Gesamt|Die Benachrichtigung wurde aufgrund von Fehlern bei der Kommunikation mit WNS nicht übermittelt.|Keine Dimensionen|
|outgoing.wns.success|Ja|Erfolgreiche WNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.wns.throttled|Ja|Gedrosselte WNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App vom WNS gedrosselt wird (WNS-Status: 406 – Nicht annehmbar).|Keine Dimensionen|
|outgoing.wns.tokenproviderunreachable|Ja|WNS-Autorisierungsfehler (nicht erreichbar)|Anzahl|Gesamt|Windows Live ist nicht erreichbar.|Keine Dimensionen|
|outgoing.wns.wrongtoken|Ja|WNS-Autorisierungsfehler (falsches Token)|Anzahl|Gesamt|Das für WNS bereitgestellte Token ist gültig, gilt aber für eine andere Anwendung (WNS-Status: 403 – Verboten). Dieser Fall kann eintreten, wenn der ChannelURI in der Registrierung einer anderen App zugeordnet wird. Stellen Sie sicher, dass die Client-App der App zugeordnet ist, deren Anmeldeinformationen sich im Notification Hub befinden.|Keine Dimensionen|
|registration.all|Ja|Registrierungsvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsvorgängen an (Erstellungen, Aktualisierungen, Abfragen und Löschungen). |Keine Dimensionen|
|registration.create|Ja|Registrierungserstellungsvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungserstellungen an.|Keine Dimensionen|
|registration.delete|Ja|Registrierungslöschvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungslöschungen an.|Keine Dimensionen|
|registration.get|Ja|Registrierungslesevorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsabfragen an.|Keine Dimensionen|
|registration.update|Ja|Registrierungsaktualisierungsvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsaktualisierungen an.|Keine Dimensionen|
|scheduled.pending|Ja|Ausstehende Pushbenachrichtigungen|Anzahl|Gesamt|Ausstehende Pushbenachrichtigungen|Keine Dimensionen|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Average_% Available Memory|Ja|% verfügbarer Speicher|Anzahl|Average|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Ja|% verfügbarer Auslagerungsbereich|Anzahl|Average|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Ja|Zugesicherte verwendete Bytes (%)|Anzahl|Average|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Ja|% DPC-Zeit|Anzahl|Average|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|Ja|% freie Inodes|Anzahl|Average|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Ja|% freier Speicher|Anzahl|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Ja|% Leerlaufzeit|Anzahl|Average|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Ja|% Interruptzeit|Anzahl|Average|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Ja|% E/a-Wartezeit|Anzahl|Average|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Ja|% Nice-Zeit|Anzahl|Average|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Ja|% privilegierte Zeit|Anzahl|Average|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Ja|% Prozessorzeit|Anzahl|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Ja|% verwendete Inodes|Anzahl|Average|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Ja|% verwendeter Arbeitsspeicher|Anzahl|Average|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Ja|% verwendeter Speicher|Anzahl|Average|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Ja|% verwendeter Auslagerungsbereich|Anzahl|Average|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Ja|% Benutzerzeit|Anzahl|Average|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Ja|Verfügbare MB|Anzahl|Average|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Ja|Verfügbarer Speicher in MB|Anzahl|Average|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Ja|Verfügbarer Auslagerungsbereich in MB|Anzahl|Average|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/gelesen|Ja|Durchschn. Datenträger s/gelesen|Anzahl|Average|Average_Avg. Datenträger s/gelesen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/übertragen|Ja|Durchschn. Datenträger s/übertragen|Anzahl|Average|Average_Avg. Datenträger s/übertragen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/geschrieben|Ja|Durchschn. Datenträger s/geschrieben|Anzahl|Average|Average_Avg. Datenträger s/geschrieben|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Ja|Empfangene Byte/Sek.|Anzahl|Average|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Ja|Gesendete Byte/Sek.|Anzahl|Average|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Ja|Byte gesamt/Sek.|Anzahl|Average|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Ja|Aktuelle Warteschlangenlänge|Anzahl|Average|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Ja|Byte gelesen/s|Anzahl|Average|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Ja|Lesevorgänge/s|Anzahl|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Ja|Übertragungen/s|Anzahl|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Ja|Byte geschrieben/s|Anzahl|Average|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Ja|Schreibvorgänge/s|Anzahl|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Ja|Freie Megabytes|Anzahl|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Ja|Freier physischer Speicher|Anzahl|Average|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Ja|Freier Speicherplatz in Auslagerungsdateien|Anzahl|Average|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Ja|Freier virtueller Arbeitsspeicher|Anzahl|Average|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Ja|Logischer Datenträger Bytes/s|Anzahl|Average|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Ja|Seitenlesevorgänge/s|Anzahl|Average|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Ja|Schreibvorgänge/s|Anzahl|Average|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Ja|Seiten/s|Anzahl|Average|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Ja|PCT-privilegierte Zeit|Anzahl|Average|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Ja|PCT-Benutzerzeit|Anzahl|Average|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Ja|Physischer Datenträger Bytes/s|Anzahl|Average|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Ja|Prozesse|Anzahl|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Ja|Länge der Prozessorwarteschlange|Anzahl|Average|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Ja|Genutzter Speicherplatz in Auslagerungsdateien|Anzahl|Average|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Ja|Summe Bytes|Anzahl|Average|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Ja|Summe empfangene Bytes|Anzahl|Average|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Ja|Summe übertragene Bytes|Anzahl|Average|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Ja|Summe Konflikte|Anzahl|Average|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Ja|Summe empfangene Pakete|Anzahl|Average|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Ja|Summe übermittelte Pakete|Anzahl|Average|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Ja|Summe Rx-Fehler|Anzahl|Average|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Ja|Summe Tx-Fehler|Anzahl|Average|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Ja|Betriebszeit|Anzahl|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Ja|Verwendeter Auslagerungsbereich in MB|Anzahl|Average|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Ja|Verwendeter Arbeitsspeicher in KB|Anzahl|Average|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Ja|Verwendeter Speicher in MB|Anzahl|Average|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Ja|Benutzer|Anzahl|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Ja|Virtueller gemeinsamer Speicher|Anzahl|Average|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Ereignis|Ja|Ereignis|Anzahl|Average|Ereignis|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Heartbeat|Ja|Heartbeat|Anzahl|Gesamt|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aktualisieren|Ja|Aktualisieren|Anzahl|Average|Aktualisieren|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Ja|Rate des ausgehenden Datenverkehrs|BitsPerSecond|Average|Rate des ausgehenden Datenverkehrs in Bit pro Sekunde|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Ja|Rate des eingehenden Datenverkehrs|BitsPerSecond|Average|Rate des eingehenden Datenverkehrs in Bit pro Sekunde|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|Ja|Sitzungsverfügbarkeit|Anzahl|Average|Verfügbarkeit der Peeringsitzung|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PrefixLatency|Ja|Präfixlatenz|Millisekunden|Average|Medianwert der Präfixlatenz|PrefixName|
|RoundTripTime|Ja|Roundtripzeit|Millisekunden|Average|Durchschnittliche Roundtripzeit|ConnectionMonitorTestName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Ja|Memory: Bereinigung – aktueller Preis|Anzahl|Average|Aktueller Preis des Arbeitsspeichers, $/Byte/Zeit, normalisiert auf 1000.|Keine Dimensionen|
|CleanerMemoryNonshrinkable|Ja|Memory: Bereinigung – nicht verkleinerbarer Arbeitsspeicher|Byte|Average|Die Menge des Arbeitsspeichers in Byte, die nicht durch den Hintergrundbereinigungsprozess bereinigt wird.|Keine Dimensionen|
|CleanerMemoryShrinkable|Ja|Memory: Bereinigung – verkleinerbarer Arbeitsspeicher|Byte|Average|Die Menge des Arbeitsspeichers in Byte, die durch den Hintergrundbereinigungsprozess bereinigt wird.|Keine Dimensionen|
|CommandPoolBusyThreads|Ja|Threads: Ausgelastete Threads im Befehlspool|Anzahl|Average|Anzahl ausgelasteter Threads im Befehlsthreadpool.|Keine Dimensionen|
|CommandPoolIdleThreads|Ja|Threads: Leerlaufthreads im Befehlspool|Anzahl|Average|Anzahl von Leerlaufthreads im Befehlsthreadpool.|Keine Dimensionen|
|CommandPoolJobQueueLength|Ja|Warteschlangenlänge für Aufträge im Befehlspool|Anzahl|Average|Gibt die Anzahl von Aufträgen in der Warteschlange des Befehlsthreadpools an.|Keine Dimensionen|
|cpu_metric|Ja|CPU (Gen2)|Percent|Average|CPU-Auslastung. Wird nur für Power BI Embedded-Generation 2-Ressourcen unterstützt.|Keine Dimensionen|
|cpu_workload_metric|Ja|CPU pro Workload (Gen2)|Percent|Average|CPU-Auslastung pro Workload. Wird nur für Power BI Embedded-Generation 2-Ressourcen unterstützt.|Workload|
|CurrentConnections|Ja|Verbindung: Aktuelle Verbindungen|Anzahl|Average|Aktuelle Anzahl hergestellter Clientverbindungen.|Keine Dimensionen|
|CurrentUserSessions|Ja|Aktuelle Benutzersitzungen|Anzahl|Average|Aktuelle Anzahl von eingerichteten Benutzersitzungen|Keine Dimensionen|
|LongParsingBusyThreads|Ja|Threads: Ausgelastete lange Analysethreads|Anzahl|Average|Anzahl ausgelasteter Threads im Pool für lange Analysethreads.|Keine Dimensionen|
|LongParsingIdleThreads|Ja|Threads: Lange Analysethreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads im Pool für lange Analysethreads.|Keine Dimensionen|
|LongParsingJobQueueLength|Ja|Threads: Warteschlangenlänge für lange Analyseaufträge|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Pools für lange Analysethreads.|Keine Dimensionen|
|memory_metric|Ja|Arbeitsspeicher (Gen1)|Byte|Average|Arbeitsspeicher. Bereiche: 0-3 GB für A1, 0-5 GB für A2, 0-10 GB für A3, 0-25 GB für A4, 0-50 GB für A5 und 0-100 GB für A6. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Keine Dimensionen|
|memory_thrashing_metric|Ja|Arbeitsspeicherüberlastung (Datasets) (Gen1)|Percent|Average|Durchschnittliche Arbeitsspeicherüberlastung. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Keine Dimensionen|
|MemoryLimitHard|Ja|Memory: Grenzwert für den festen Speicher|Byte|Average|Grenzwert für den festen Speicher gemäß Konfigurationsdatei.|Keine Dimensionen|
|MemoryLimitHigh|Ja|Memory: Obere Arbeitsspeichergrenze|Byte|Average|Oberer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|Keine Dimensionen|
|MemoryLimitLow|Ja|Memory: Untere Arbeitsspeichergrenze|Byte|Average|Unterer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|Keine Dimensionen|
|MemoryLimitVertiPaq|Ja|Memory: VertiPaq-Arbeitsspeichergrenze|Byte|Average|In-Memory-Grenzwert gemäß Konfigurationsdatei.|Keine Dimensionen|
|MemoryUsage|Ja|Memory: Speicherauslastung|Byte|Average|Speicherauslastung des Serverprozesses, wie bei der Berechnung des Arbeitsspeicherpreises für die Bereinigung verwendet. Entspricht dem Indikator "Process\PrivateBytes" zuzüglich der Größe der im Speicher abgebildeten Daten. Von der xVelocity-Engine für Datenanalyse im Arbeitsspeicher (VertiPaq) abgebildeter oder belegter Arbeitsspeicher, der über die xVelocity-Arbeitsspeichergrenze hinausgeht, wird dabei ignoriert.|Keine Dimensionen|
|overload_metric|Ja|Überlastung (Gen2)|Anzahl|Average|Ressourcenüberlastung: 1, wenn die Ressource überlastet ist, andernfalls 0. Wird nur für Power BI Embedded-Generation 2-Ressourcen unterstützt.|Keine Dimensionen|
|ProcessingPoolBusyIOJobThreads|Ja|Threads: Ausgelastete Threads für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von Threads, die E/A-Aufträge im Verarbeitungsthreadpool ausführen.|Keine Dimensionen|
|ProcessingPoolBusyNonIOThreads|Ja|Threads: Ausgelastete Nicht-E/A-Threads im Verarbeitungspool|Anzahl|Average|Anzahl von Threads, die Nicht-E/A-Aufträge im Verarbeitungsthreadpool ausführen.|Keine Dimensionen|
|ProcessingPoolIdleIOJobThreads|Ja|Threads: Leerlaufthreads für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|Keine Dimensionen|
|ProcessingPoolIdleNonIOThreads|Ja|Threads: Nicht-E/A-Leerlaufthreads im Verarbeitungspool|Anzahl|Average|Anzahl von Leerlaufthreads im Verarbeitungsthreadpool, die für Nicht-E/A-Aufträge vorgesehen sind.|Keine Dimensionen|
|ProcessingPoolIOJobQueueLength|Ja|Threads: Warteschlangenlänge für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools.|Keine Dimensionen|
|ProcessingPoolJobQueueLength|Ja|Warteschlangenlänge für Verarbeitungspoolaufträge|Anzahl|Average|Anzahl von Nicht-E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools|Keine Dimensionen|
|qpu_high_utilization_metric|Ja|Hohe QPU-Auslastung (Gen1)|Anzahl|Gesamt|Hohe QPU-Auslastung in der letzten Minute, 1 für hohe Auslastung, andernfalls 0. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Keine Dimensionen|
|qpu_metric|Ja|QPU (Gen1)|Anzahl|Average|QPU. Der Bereich für A1 ist 0 bis 20, für A2 0 bis 40, für A3 0 bis 40, für A4 0 bis 80, für A5 0 bin 160, für A6 0 bis 320. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Keine Dimensionen|
|QueryDuration|Ja|Abfragedauer (Datasets) (Gen1)|Millisekunden|Average|Dauer der DAX-Abfrage im letzten Intervall. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Keine Dimensionen|
|QueryPoolBusyThreads|Ja|Ausgelastete Abfragepoolthreads|Anzahl|Average|Anzahl von ausgelasteten Threads im Abfragethreadpool|Keine Dimensionen|
|QueryPoolIdleThreads|Ja|Threads: Abfragepoolthreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|Keine Dimensionen|
|QueryPoolJobQueueLength|Ja|Warteschlangenlänge für Abfragepoolaufträge (Datasets) (Gen1)|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Keine Dimensionen|
|Kontingent|Ja|Memory: Kontingent|Byte|Average|Aktuelles Arbeitsspeicherkontingent in Byte. Das Arbeitsspeicherkontingent wird auch als Speicherzuweisung oder Speicherreservierung bezeichnet.|Keine Dimensionen|
|QuotaBlocked|Ja|Memory: Kontingent blockiert|Anzahl|Average|Aktuelle Anzahl von Kontingentanforderungen, die blockiert werden, bis andere Arbeitsspeicherkontingente freigegeben werden.|Keine Dimensionen|
|RowsConvertedPerSec|Ja|Verarbeitung: Konvertierte Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der Zeilen, die bei der Verarbeitung konvertiert werden.|Keine Dimensionen|
|RowsReadPerSec|Ja|Verarbeitung: Gelesene Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der aus allen relationalen Datenbanken gelesenen Zeilen.|Keine Dimensionen|
|RowsWrittenPerSec|Ja|Verarbeitung: Geschriebene Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der Zeilen, die bei der Verarbeitung geschrieben werden.|Keine Dimensionen|
|ShortParsingBusyThreads|Ja|Threads: Ausgelastete kurze Analysethreads|Anzahl|Average|Anzahl ausgelasteter Threads im Pool für kurze Analysethreads.|Keine Dimensionen|
|ShortParsingIdleThreads|Ja|Threads: Kurze Analysethreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads im Pool für kurze Analysethreads.|Keine Dimensionen|
|ShortParsingJobQueueLength|Ja|Threads: Warteschlangenlänge für kurze Analyseaufträge|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Pools für kurze Analysethreads.|Keine Dimensionen|
|SuccessfullConnectionsPerSec|Ja|Erfolgreiche Verbindungen pro Sekunde|Anzahl pro Sekunde|Average|Rate der erfolgreichen Verbindungsabschlüsse|Keine Dimensionen|
|TotalConnectionFailures|Ja|Verbindungsfehler gesamt|Anzahl|Average|Gesamtanzahl von fehlerhaften Verbindungsversuchen|Keine Dimensionen|
|TotalConnectionRequests|Ja|Total Connection Requests (Verbindungsanforderungen gesamt)|Anzahl|Average|Gesamtanzahl von Verbindungsanforderungen. Dies sind erhaltene Anforderungen.|Keine Dimensionen|
|VertiPaqNonpaged|Ja|Memory: Nicht ausgelagerte VertiPaq-Daten|Byte|Average|Bytes von Arbeitsspeicher, die im Arbeitssatz zur Verwendung durch die In-Memory-Engine gesperrt sind.|Keine Dimensionen|
|VertiPaqPaged|Ja|Memory: Ausgelagerte VertiPaq-Daten|Byte|Average|Bytes von ausgelagertem Arbeitsspeicher, die für In-Memory-Daten verwendet werden.|Keine Dimensionen|
|workload_memory_metric|Ja|Arbeitsspeicher pro Workload (Gen1)|Byte|Average|Arbeitsspeicher pro Workload. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Workload|
|workload_qpu_metric|Ja|QPU pro Workload (Gen1)|Anzahl|Average|QPU pro Workload. Der Bereich für A1 ist 0 bis 20, für A2 0 bis 40, für A3 0 bis 40, für A4 0 bis 80, für A5 0 bin 160, für A6 0 bis 320. Wird nur für Power BI Embedded-Generation 1-Ressourcen unterstützt.|Workload|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ScanCancelled|Ja|Überprüfung abgebrochen|Anzahl|Gesamt|Gibt die Anzahl von Überprüfungen an, die abgebrochen wurden.|Keine Dimensionen|
|ScanCompleted|Ja|Überprüfung abgeschlossen|Anzahl|Gesamt|Gibt die Anzahl von Überprüfungen an, die erfolgreich abgeschlossen wurden.|Keine Dimensionen|
|ScanFailed|Ja|Fehler bei der Überprüfung|Anzahl|Gesamt|Gibt die Anzahl von Überprüfungen an, bei denen ein Fehler aufgetreten ist.|Keine Dimensionen|
|ScanTimeTaken|Ja|Überprüfungsdauer|Sekunden|Gesamt|Gibt die Gesamtdauer der Überprüfung in Sekunden an.|Keine Dimensionen|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BackupHealthEvent|Ja|Ereignisse der Sicherungsintegrität (Vorschau)|Anzahl|Anzahl|Anzahl der Integritätsereignisse im Zusammenhang mit der Integrität des Sicherungsauftrags|dataSourceURL, backupInstanceUrl, dataSourceType, healthStatus, backupInstanceName|
|RestoreHealthEvent|Ja|Ereignisse der Wiederherstellungsintegrität (Vorschau)|Anzahl|Anzahl|Anzahl der Integritätsereignisse im Zusammenhang mit der Integrität des Wiederherstellungsauftrags|dataSourceURL, backupInstanceUrl, dataSourceType, healthStatus, backupInstanceName|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Gesamt|Gesamtzahl der ActiveConnections für Microsoft.Relay.|EntityName|
|ActiveListeners|Nein|ActiveListeners|Anzahl|Gesamt|Gesamtzahl der ActiveListeners für Microsoft.Relay.|EntityName|
|BytesTransferred|Ja|BytesTransferred|Byte|Gesamt|Gesamtzahl der BytesTransferred für Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|Nein|ListenerConnections-ClientError|Anzahl|Gesamt|ClientError bei ListenerConnections für Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|Nein|ListenerConnections-ServerError|Anzahl|Gesamt|ServerError bei ListenerConnections für Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-Success|Nein|ListenerConnections-Success|Anzahl|Gesamt|Erfolgreiche ListenerConnections für Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|Nein|ListenerConnections-TotalRequests|Anzahl|Gesamt|Gesamtzahl der ListenerConnections für Microsoft.Relay.|EntityName|
|ListenerDisconnects|Nein|ListenerDisconnects|Anzahl|Gesamt|Gesamtzahl der ListenerDisconnects für Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|Nein|SenderConnections-ClientError|Anzahl|Gesamt|ClientError bei SenderConnections für Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|Nein|SenderConnections-ServerError|Anzahl|Gesamt|ServerError bei SenderConnections für Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|Nein|SenderConnections-Success|Anzahl|Gesamt|Erfolgreiche SenderConnections für Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-TotalRequests|Nein|SenderConnections-TotalRequests|Anzahl|Gesamt|Gesamtzahl der SenderConnections-Anforderungen für Microsoft.Relay.|EntityName|
|SenderDisconnects|Nein|SenderDisconnects|Anzahl|Gesamt|Gesamtzahl der SenderDisconnects für Microsoft.Relay.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Latency|Nein|Latency|Sekunden|Average|Latenzdaten für alle Anforderungen an Azure Resource Manager|IsCustomerOriginated, Method, Namespace, RequestRegion, ResourceType, StatusCode, StatusCodeClass, Microsoft.SubscriptionId|
|Verkehr|Nein|Verkehr|Anzahl|Anzahl|Datenverkehrsdaten für alle Anforderungen an Azure Resource Manager|IsCustomerOriginated, Method, Namespace, RequestRegion, ResourceType, StatusCode, StatusCodeClass, Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DocumentsProcessedCount|Ja|Anzahl verarbeiteter Dokumente|Anzahl|Gesamt|Anzahl von verarbeiteten Dokumenten|DataSourceName, Failed, IndexerName, IndexName, SkillsetName|
|SearchLatency|Ja|Suchlatenz|Sekunden|Average|Durchschnittliche Suchlatenz für den Suchdienst|Keine Dimensionen|
|SearchQueriesPerSecond|Ja|Suchabfragen pro Sekunde|Anzahl pro Sekunde|Average|Suchabfragen pro Sekunde für den Suchdienst|Keine Dimensionen|
|SkillExecutionCount|Ja|Anzahl von Aufrufen der Skillausführung|Anzahl|Gesamt|Anzahl der Skillausführungen|DataSourceName, Failed, IndexerName, SkillName, SkillsetName, SkillType|
|ThrottledSearchQueriesPercentage|Ja|Gedrosselte Suchabfragen in Prozent|Percent|Average|Prozentsatz der Suchabfragen, die für den Suchdienst gedrosselt wurden|Keine Dimensionen|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Gesamt|Aktive Verbindungen gesamt für Microsoft.ServiceBus.|Keine Dimensionen|
|ActiveMessages|Nein|Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema|EntityName|
|ConnectionsClosed|Nein|Geschlossene Verbindungen.|Anzahl|Average|Geschlossene Verbindungen für Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|Nein|Geöffnete Verbindungen.|Anzahl|Average|Geöffnete Verbindungen für Microsoft.ServiceBus.|EntityName|
|CPUXNS|Nein|CPU (veraltet)|Percent|Maximum|CPU-Auslastungsmetrik für Service Bus-Premium-Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die CPU-Metrik (NamespaceCpuUsage).|Replikat|
|DeadletteredMessages|Nein|Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema|EntityName|
|IncomingMessages|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Eingehende Nachrichten für Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Ja|Eingehende Anforderungen|Anzahl|Gesamt|Eingehende Anforderungen für Microsoft.ServiceBus.|EntityName|
|Meldungen|Nein|Anzahl von Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von Nachrichten in einer Warteschlange/einem Thema|EntityName|
|NamespaceCpuUsage|Nein|CPU|Percent|Maximum|CPU-Auslastungsmetrik für Service Bus-Premium-Namespace|Replikat|
|NamespaceMemoryUsage|Nein|Speicherauslastung|Percent|Maximum|Speicherauslastungsmetrik für Service Bus-Premium-Namespace|Replikat|
|OutgoingMessages|Ja|Ausgehende Nachrichten|Anzahl|Gesamt|Ausgehende Nachrichten für Microsoft.ServiceBus.|EntityName|
|ScheduledMessages|Nein|Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema|EntityName|
|ServerErrors|Nein|Serverfehler.|Anzahl|Gesamt|Serverfehler für Microsoft.ServiceBus.|EntityName, OperationResult|
|Size|Nein|Size|Byte|Average|Größe einer Warteschlange/eines Themas in Bytes|EntityName|
|SuccessfulRequests|Nein|Erfolgreiche Anforderungen|Anzahl|Gesamt|Gesamtzahl der erfolgreichen Anforderungen für einen Namespace|EntityName, OperationResult|
|ThrottledRequests|Nein|Gedrosselte Anforderungen.|Anzahl|Gesamt|Gedrosselte Anforderungen für Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Nein|Benutzerfehler.|Anzahl|Gesamt|Benutzerfehler für Microsoft.ServiceBus.|EntityName, OperationResult|
|WSXNS|Nein|Speicherauslastung (Deprecated)|Percent|Maximum|Speicherauslastungsmetrik für Service Bus-Premium-Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Speicherauslastingsmetrik (NamespaceMemoryUsage).|Replikat|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ConnectionCount|Ja|Anzahl der Verbindungen|Anzahl|Maximum|Die Anzahl der Benutzerverbindungen.|Endpunkt|
|InboundTraffic|Ja|Eingehender Datenverkehr|Byte|Gesamt|Der eingehende Datenverkehr des Diensts|Keine Dimensionen|
|MessageCount|Ja|Nachrichtenanzahl|Anzahl|Gesamt|Die Gesamtmenge der Nachrichten.|Keine Dimensionen|
|OutboundTraffic|Ja|Ausgehender Datenverkehr|Byte|Gesamt|Der ausgehende Datenverkehr des Diensts|Keine Dimensionen|
|SystemErrors|Ja|Systemfehler|Percent|Maximum|Der Prozentsatz der Systemfehler|Keine Dimensionen|
|UserErrors|Ja|Benutzerfehler|Percent|Maximum|Der Prozentsatz der Benutzerfehler|Keine Dimensionen|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|InboundTraffic|Ja|Eingehender Datenverkehr|Byte|Gesamt|Der Datenverkehr, der von außerhalb des Diensts stammt. Dieser Wert wird aggregiert, indem alle Bytes des Datenverkehrs addiert werden.|Keine Dimensionen|
|OutboundTraffic|Ja|Ausgehender Datenverkehr|Byte|Gesamt|Der Datenverkehr, der von innerhalb des Diensts stammt. Dieser Wert wird aggregiert, indem alle Bytes des Datenverkehrs addiert werden.|Keine Dimensionen|
|TotalConnectionCount|Ja|Anzahl der Verbindungen|Anzahl|Maximum|Die Anzahl von Benutzerverbindungen, die mit dem Dienst hergestellt wurden. Dieser Wert wird aggregiert, indem alle Onlineverbindungen addiert werden.|Keine Dimensionen|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Ja|Durchschnittlicher CPU-Prozentsatz|Percent|Average|Durchschnittlicher CPU-Prozentsatz|Keine Dimensionen|
|io_bytes_read|Ja|Gelesene E/A-Bytes|Byte|Average|Gelesene E/A-Bytes|Keine Dimensionen|
|io_bytes_written|Ja|Geschriebene E/A-Bytes|Byte|Average|Geschriebene E/A-Bytes|Keine Dimensionen|
|io_requests|Ja|Anzahl von E/A-Anforderungen|Anzahl|Average|Anzahl von E/A-Anforderungen|Keine Dimensionen|
|reserved_storage_mb|Ja|Reservierter Speicherplatz|Anzahl|Average|Reservierter Speicherplatz|Keine Dimensionen|
|storage_space_used_mb|Ja|Belegter Speicherplatz|Anzahl|Average|Belegter Speicherplatz|Keine Dimensionen|
|virtual_core_count|Ja|Anzahl virtueller Kerne|Anzahl|Average|Anzahl virtueller Kerne|Keine Dimensionen|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_queries|Ja|Aktive Abfragen|Anzahl|Gesamt|Aktive Abfragen in allen Arbeitsauslastungsgruppen. Gilt nur für Data Warehouses.|Keine Dimensionen|
|allocated_data_storage|Ja|Zugeordneter Datenspeicherplatz|Byte|Average|Zugeordneter Datenspeicher. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|app_cpu_billed|Ja|Abgerechnete App-CPU|Anzahl|Gesamt|Abgerechnete App-CPU. Gilt für serverlose Datenbanken.|Keine Dimensionen|
|app_cpu_percent|Ja|App-CPU-Prozentsatz|Percent|Average|App-CPU-Prozentsatz. Gilt für serverlose Datenbanken.|Keine Dimensionen|
|app_memory_percent|Ja|App-Arbeitsspeicherprozentsatz|Percent|Average|App-Arbeitsspeicherprozentsatz. Gilt für serverlose Datenbanken.|Keine Dimensionen|
|base_blob_size_bytes|Ja|Basisspeichergröße für Blobs|Byte|Maximum|Basisspeichergröße für Blobs. Gilt für Hyperscale-Datenbanken.|Keine Dimensionen|
|blocked_by_firewall|Ja|Von der Firewall blockiert|Anzahl|Gesamt|Von der Firewall blockiert|Keine Dimensionen|
|cache_hit_percent|Ja|Prozentsatz der Cachetreffer|Percent|Maximum|Prozentsatz der Cachetreffer. Gilt nur für Data Warehouses.|Keine Dimensionen|
|cache_used_percent|Ja|Cacheverwendung in Prozent|Percent|Maximum|Cacheverwendung in Prozent. Gilt nur für Data Warehouses.|Keine Dimensionen|
|connection_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|connection_successful|Ja|Erfolgreiche Verbindungen|Anzahl|Gesamt|Erfolgreiche Verbindungen|Keine Dimensionen|
|cpu_limit|Ja|CPU-Grenzwert|Anzahl|Average|CPU-Grenzwert. Gilt für V-Kern-basierte Datenbanken.|Keine Dimensionen|
|cpu_percent|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Keine Dimensionen|
|cpu_used|Ja|Verwendete CPU|Anzahl|Average|Verwendete CPU. Gilt für V-Kern-basierte Datenbanken.|Keine Dimensionen|
|deadlock|Ja|Deadlocks|Anzahl|Gesamt|Deadlocks. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|delta_num_of_bytes_read|Ja|Remote-Datenlesevorgänge|Byte|Gesamt|Remotedatenlesevorgänge in Bytes|Keine Dimensionen|
|delta_num_of_bytes_total|Ja|Gesamtanzahl der gelesenen und geschriebenen Remotebytes|Byte|Gesamt|Gesamtanzahl der von Compute gelesenen und geschriebenen Remotebytes|Keine Dimensionen|
|delta_num_of_bytes_written|Ja|Remote-Protokollschreibvorgänge|Byte|Gesamt|Remoteprotokollschreibvorgänge in Bytes|Keine Dimensionen|
|diff_backup_size_bytes|Ja|Speichergröße für differenzielle Sicherungen|Byte|Maximum|Speichergröße für kumulative differenzielle Sicherungen Gilt für V-Kern-basierte Datenbanken. Gilt nicht für Hyperscale-Datenbanken.|Keine Dimensionen|
|dtu_consumption_percent|Ja|DTU-Prozentsatz|Percent|Average|DTU-Prozentsatz. Gilt für DTU-basierte Datenbanken.|Keine Dimensionen|
|dtu_limit|Ja|DTU-Grenzwert|Anzahl|Average|DTU-Grenzwert. Gilt für DTU-basierte Datenbanken.|Keine Dimensionen|
|dtu_used|Ja|DTU-Verbrauch|Anzahl|Average|DTU-Verbrauch. Gilt für DTU-basierte Datenbanken.|Keine Dimensionen|
|dwu_consumption_percent|Ja|DWU in Prozent|Percent|Maximum|DWU in Prozent. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dwu_limit|Ja|DWU-Grenzwert|Anzahl|Maximum|DWU-Grenzwert. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dwu_used|Ja|DWU-Verbrauch|Anzahl|Maximum|DWU-Verbrauch. Gilt nur für Data Warehouses.|Keine Dimensionen|
|full_backup_size_bytes|Ja|Speichergröße für vollständige Sicherungen|Byte|Maximum|Speichergröße für kumulative vollständige Sicherungen Gilt für V-Kern-basierte Datenbanken. Gilt nicht für Hyperscale-Datenbanken.|Keine Dimensionen|
|local_tempdb_usage_percent|Ja|Lokaler tempdb-Prozentsatz|Percent|Average|Lokaler tempdb-Prozentsatz. Gilt nur für Data Warehouses.|Keine Dimensionen|
|log_backup_size_bytes|Ja|Speichergröße für Protokollsicherungen|Byte|Maximum|Speichergröße für kumulative Protokollsicherungen Gilt für auf virtuellen Kernen basierende Datenbanken und Hyperscale-Datenbanken.|Keine Dimensionen|
|log_write_percent|Ja|E/A-Prozentsatz für Protokoll|Percent|Average|E/A-Prozentsatz für Protokoll. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|memory_usage_percent|Ja|Arbeitsspeicherprozentsatz|Percent|Maximum|Arbeitsspeicherprozentsatz. Gilt nur für Data Warehouses.|Keine Dimensionen|
|physical_data_read_percent|Ja|E/A-Prozentsatz für Daten|Percent|Average|E/A-Prozentsatz für Daten|Keine Dimensionen|
|queued_queries|Ja|Abfragen in Warteschlange|Anzahl|Gesamt|Abfragen in Warteschlange in allen Arbeitsauslastungsgruppen. Gilt nur für Data Warehouses.|Keine Dimensionen|
|sessions_percent|Ja|Sitzungen in Prozent|Percent|Average|Sitzungen in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|snapshot_backup_size_bytes|Ja|Speichergröße für Momentaufnahmesicherungen|Byte|Maximum|Speichergröße für kumulative Momentaufnahmesicherungen. Gilt für Hyperscale-Datenbanken.|Keine Dimensionen|
|sqlserver_process_core_percent|Ja|SQL Server-Prozess: Kern (in Prozent)|Percent|Maximum|CPU-Auslastung als Prozentsatz des SQL-DB-Prozesses. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|sqlserver_process_memory_percent|Ja|SQL Server-Prozess: Arbeitsspeicher (in Prozent)|Percent|Maximum|Speicherauslastung als Prozentsatz des SQL-DB-Prozesses. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|storage|Ja|Genutzter Datenspeicherplatz|Byte|Maximum|Genutzter Datenspeicherplatz. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|storage_percent|Ja|Genutzter Datenspeicherplatz in Prozent|Percent|Maximum|Genutzter Datenspeicherplatz in Prozent. Gilt nicht für Data Warehouses oder Hyperscale-Datenbanken.|Keine Dimensionen|
|tempdb_data_size|Ja|Größe der tempdb-Datendatei in Kilobytes|Anzahl|Maximum|Der in tempdb-Datendateien verwendete Speicherplatz in Kilobytes. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|tempdb_log_size|Ja|Größe der tempdb-Protokolldatei in Kilobytes|Anzahl|Maximum|Der in der tempdb-Transaktionsprotokolldatei verwendete Speicherplatz in Kilobytes. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|tempdb_log_used_percent|Ja|Nutzung des tempdb-Protokolls in Prozent|Percent|Maximum|Der in der tempdb-Transaktionsprotokolldatei verwendete Speicherplatz in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|wlg_active_queries|Ja|Aktive Abfragen von Arbeitsauslastungsgruppen|Anzahl|Gesamt|Aktive Abfragen in der Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Ja|Abfragetimeouts für Arbeitsauslastungsgruppen|Anzahl|Gesamt|Abfragen, für die ein Timeout aufgetreten ist, für die Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Ja|Zuordnung von Arbeitsauslastungsgruppen nach Systemprozentsatz|Percent|Maximum|Die prozentuale Zuordnung von Ressourcen in Relation zum gesamten System pro Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Ja|Arbeitsauslastungsgruppenzuordnung nach Ressourcenlimit (Prozent)|Percent|Maximum|Die prozentuale Zuordnung von Ressourcen in Relation zum angegebenen Ressourcenlimit pro Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Ja|Effektives Ressourcenlimit (Prozent)|Percent|Maximum|Eine feste Beschränkung des Prozentsatzes der Ressourcen, auf die die Arbeitsauslastungsgruppe zugreifen kann, unter Berücksichtigung des Wertes, der anderen Arbeitsauslastungsgruppen für die effektive Mindestanzahl von Ressourcen in Prozent zugeordnet ist. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Ja|Effektive Mindestanzahl von Ressourcen (Prozent)|Percent|Maximum|Der Mindestprozentsatz von Ressourcen, die unter Berücksichtigung des Servicelevelminimums für die Arbeitsauslastungsgruppe reserviert und isoliert sind. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Ja|In der Warteschlange befindliche Abfragen der Arbeitsauslastungsgruppe|Anzahl|Gesamt|Abfragen in Warteschlange in der Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|workers_percent|Ja|Worker in Prozent|Percent|Average|Worker in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|xtp_storage_percent|Ja|In-Memory-OLTP-Speicher in Prozent|Percent|Average|In-Memory-OLTP-Speicher in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|allocated_data_storage|Ja|Zugeordneter Datenspeicherplatz|Byte|Average|Zugeordneter Datenspeicherplatz|Keine Dimensionen|
|allocated_data_storage_percent|Ja|Zugeordneter Datenspeicherplatz in Prozent|Percent|Maximum|Zugeordneter Datenspeicherplatz in Prozent|Keine Dimensionen|
|cpu_limit|Ja|CPU-Grenzwert|Anzahl|Average|CPU-Grenzwert. Gilt für V-Kern-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|cpu_percent|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Keine Dimensionen|
|cpu_used|Ja|Verwendete CPU|Anzahl|Average|Verwendete CPU. Gilt für V-Kern-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|database_allocated_data_storage|Nein|Zugeordneter Datenspeicherplatz|Byte|Average|Zugeordneter Datenspeicherplatz|DatabaseResourceId|
|database_cpu_limit|Nein|CPU-Grenzwert|Anzahl|Average|CPU-Grenzwert|DatabaseResourceId|
|database_cpu_percent|Nein|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|DatabaseResourceId|
|database_cpu_used|Nein|Verwendete CPU|Anzahl|Average|Verwendete CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Nein|DTU-Prozentsatz|Percent|Average|DTU-Prozentsatz|DatabaseResourceId|
|database_eDTU_used|Nein|eDTU-Verbrauch|Anzahl|Average|eDTU-Verbrauch|DatabaseResourceId|
|database_log_write_percent|Nein|E/A-Prozentsatz für Protokoll|Percent|Average|E/A-Prozentsatz für Protokoll|DatabaseResourceId|
|database_physical_data_read_percent|Nein|E/A-Prozentsatz für Daten|Percent|Average|E/A-Prozentsatz für Daten|DatabaseResourceId|
|database_sessions_percent|Nein|Sitzungen in Prozent|Percent|Average|Sitzungen in Prozent|DatabaseResourceId|
|database_storage_used|Nein|Genutzter Datenspeicherplatz|Byte|Average|Genutzter Datenspeicherplatz|DatabaseResourceId|
|database_workers_percent|Nein|Worker in Prozent|Percent|Average|Worker in Prozent|DatabaseResourceId|
|dtu_consumption_percent|Ja|DTU-Prozentsatz|Percent|Average|DTU-Prozentsatz. Gilt für DTU-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|eDTU_limit|Ja|eDTU-Grenzwert|Anzahl|Average|eDTU-Grenzwert. Gilt für DTU-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|eDTU_used|Ja|eDTU-Verbrauch|Anzahl|Average|eDTU-Verbrauch. Gilt für DTU-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|log_write_percent|Ja|E/A-Prozentsatz für Protokoll|Percent|Average|E/A-Prozentsatz für Protokoll|Keine Dimensionen|
|physical_data_read_percent|Ja|E/A-Prozentsatz für Daten|Percent|Average|E/A-Prozentsatz für Daten|Keine Dimensionen|
|sessions_percent|Ja|Sitzungen in Prozent|Percent|Average|Sitzungen in Prozent|Keine Dimensionen|
|sqlserver_process_core_percent|Ja|SQL Server-Prozess: Kern (in Prozent)|Percent|Maximum|CPU-Auslastung als Prozentsatz des SQL-DB-Prozesses. Bezieht sich auf Pools für elastische Datenbanken.|Keine Dimensionen|
|sqlserver_process_memory_percent|Ja|SQL Server-Prozess: Arbeitsspeicher (in Prozent)|Percent|Maximum|Speicherauslastung als Prozentsatz des SQL-DB-Prozesses. Bezieht sich auf Pools für elastische Datenbanken.|Keine Dimensionen|
|storage_limit|Ja|Maximale Datengröße|Byte|Average|Maximale Datengröße|Keine Dimensionen|
|storage_percent|Ja|Genutzter Datenspeicherplatz in Prozent|Percent|Average|Genutzter Datenspeicherplatz in Prozent|Keine Dimensionen|
|storage_used|Ja|Genutzter Datenspeicherplatz|Byte|Average|Genutzter Datenspeicherplatz|Keine Dimensionen|
|tempdb_data_size|Ja|Größe der tempdb-Datendatei in Kilobytes|Anzahl|Maximum|Der in tempdb-Datendateien verwendete Speicherplatz in Kilobytes.|Keine Dimensionen|
|tempdb_log_size|Ja|Größe der tempdb-Protokolldatei in Kilobytes|Anzahl|Maximum|Der in der tempdb-Transaktionsprotokolldatei verwendete Speicherplatz in Kilobytes.|Keine Dimensionen|
|tempdb_log_used_percent|Ja|Nutzung des tempdb-Protokolls in Prozent|Percent|Maximum|Der in der tempdb-Transaktionsprotokolldatei verwendete Speicherplatz in Prozent|Keine Dimensionen|
|workers_percent|Ja|Worker in Prozent|Percent|Average|Worker in Prozent|Keine Dimensionen|
|xtp_storage_percent|Ja|In-Memory-OLTP-Speicher in Prozent|Percent|Average|In-Memory-OLTP-Speicher in Prozent|Keine Dimensionen|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Ja|Verwendete Kapazität|Byte|Average|Die vom Speicherkonto beanspruchte Speichermenge. Bei Standardspeicherkonten ist das die Summe der von Blob, Table, File und Queue beanspruchten Kapazität. Bei Premium-Speicherkonten und Blob Storage-Konten ist diese mit BlobCapacity oder FileCapacity identisch.|Keine Dimensionen|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|BlobCapacity|Nein|Blob-Kapazität|Byte|Average|Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte.|BlobType, Tier|
|BlobCount|Nein|Anzahl von Blobs|Anzahl|Average|Die Anzahl von im Speicherkonto gespeicherten Blobs.|BlobType, Tier|
|BlobProvisionedSize|Nein|Größe des bereitgestellten Blobs|Byte|Average|Die Speichermenge in Byte, die im Blob-Dienst des Speicherkontos bereitgestellt wird.|BlobType, Tier|
|ContainerCount|Ja|Anzahl von Blob-Containern|Anzahl|Average|Die Anzahl von Containern im Speicherkonto.|Keine Dimensionen|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|IndexCapacity|Nein|Indexkapazität|Byte|Average|Die Menge an Speicher, die vom hierarchischen Index in Azure Data Lake Storage Gen2 verwendet wird|Keine Dimensionen|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication, FileShare|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|Nein|Dateikapazität|Byte|Average|Der vom Speicherkonto beanspruchte File-Speicher.|FileShare|
|FileCount|Nein|Dateianzahl|Anzahl|Average|Die Anzahl von Dateien im Speicherkonto.|FileShare|
|FileShareCapacityQuota|Nein|Kontingent für Dateifreigabekapazität|Byte|Average|Die Obergrenze für die Speichermenge zur Verwendung durch den Azure Files-Dienst, angegeben in Byte.|FileShare|
|FileShareCount|Nein|Anzahl von Dateifreigaben|Anzahl|Average|Die Anzahl von Dateifreigaben im Speicherkonto.|Keine Dimensionen|
|FileShareProvisionedIOPS|Nein|Bereitgestellte IOPS für Dateifreigaben|Anzahl pro Sekunde|Average|Die Baseline der bereitgestellten IOPS für die Premium-Dateifreigabe im Premium-Dateispeicherkonto – diese Zahl wird anhand der bereitgestellten Größe (Kontingent) der Freigabekapazität berechnet.|FileShare|
|FileShareSnapshotCount|Nein|Anzahl von Momentaufnahmen in Dateifreigabe|Anzahl|Average|Die Anzahl der in der Freigabe im Dateidienst des Speicherkontos enthaltenen Momentaufnahmen.|FileShare|
|FileShareSnapshotSize|Nein|Größe der Momentaufnahmen in Dateifreigabe|Byte|Average|Die Speichermenge in Byte, die von den Momentaufnahmen im Dateidienst des Speicherkontos verwendet wird.|FileShare|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication, FileShare|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Ja|Warteschlangenkapazität|Byte|Average|Der vom Speicherkonto beanspruchte Queue-Speicher.|Keine Dimensionen|
|QueueCount|Ja|Anzahl von Warteschlangen|Anzahl|Average|Die Anzahl von Warteschlangen im Speicherkonto.|Keine Dimensionen|
|QueueMessageCount|Ja|Anzahl von Warteschlangennachrichten|Anzahl|Average|Die Anzahl nicht abgelaufener Warteschlangennachrichten im Speicherkonto.|Keine Dimensionen|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|TableCapacity|Ja|Tabellenkapazität|Byte|Average|Der vom Speicherkonto beanspruchte Table-Speicher.|Keine Dimensionen|
|TableCount|Ja|Anzahl von Tabellen|Anzahl|Average|Die Anzahl von Tabellen im Speicherkonto.|Keine Dimensionen|
|TableEntityCount|Ja|Anzahl von Tabellenentitäten|Anzahl|Average|Die Anzahl von Tabellenentitäten im Speicherkonto.|Keine Dimensionen|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ClientIOPS|Ja|Client-IOPS insgesamt|Anzahl|Average|Die Rate der vom Cache verarbeiteten Clientdateivorgänge.|Keine Dimensionen|
|ClientLatency|Ja|Durchschnittliche Clientlatenz|Millisekunden|Average|Durchschnittliche Latenz von Clientdateivorgängen im Cache.|Keine Dimensionen|
|ClientLockIOPS|Ja|IOPS für Clientsperren|Anzahl pro Sekunde|Average|Clientdatei-Sperrvorgänge pro Sekunde.|Keine Dimensionen|
|ClientMetadataReadIOPS|Ja|IOPS für Metadatenlesevorgänge durch Client|Anzahl pro Sekunde|Average|Die Rate der an den Cache gesendeten Clientdateivorgänge, ausgenommen Datenlesevorgänge, die den persistenten Zustand nicht ändern.|Keine Dimensionen|
|ClientMetadataWriteIOPS|Ja|IOPS für Metadatenschreibvorgänge durch Client|Anzahl pro Sekunde|Average|Die Rate der an den Cache gesendeten Clientdateivorgänge, ausgenommen Datenschreibvorgänge, die den persistenten Zustand ändern.|Keine Dimensionen|
|ClientReadIOPS|Ja|Lese-IOPS auf Client|Anzahl pro Sekunde|Average|Clientlesevorgänge pro Sekunde.|Keine Dimensionen|
|ClientReadThroughput|Ja|Durchschnittlicher Durchsatz von Cachelesevorgängen|Bytes pro Sekunde|Average|Übertragungsrate für Clientlesedaten.|Keine Dimensionen|
|ClientStatus|Ja|Clientstatus|Anzahl|Gesamt|Informationen zur Clientverbindung.|ClientSource, CacheAddress, ClientAddress, Protocol, ConnectionType|
|ClientWriteIOPS|Ja|Schreib-IOPS auf Client|Anzahl pro Sekunde|Average|Clientschreibvorgänge pro Sekunde.|Keine Dimensionen|
|ClientWriteThroughput|Ja|Durchschnittlicher Durchsatz von Cacheschreibvorgängen|Bytes pro Sekunde|Average|Übertragungsrate für Clientschreibdaten.|Keine Dimensionen|
|FileOps|Ja|Dateivorgänge|Anzahl pro Sekunde|Average|Anzahl der Dateivorgänge pro Sekunde.|SourceFile, Rank, FileType|
|FileReads|Ja|Dateilesevorgänge|Bytes pro Sekunde|Average|Anzahl der aus einer Datei gelesenen Bytes pro Sekunde.|SourceFile, Rank, FileType|
|FileUpdates|Ja|Dateiupdates|Anzahl pro Sekunde|Average|Anzahl von Verzeichnisupdates und Metadatenvorgängen pro Sekunde.|SourceFile, Rank, FileType|
|FileWrites|Ja|Dateischreibvorgänge|Bytes pro Sekunde|Average|Anzahl der in eine Datei geschriebenen Bytes pro Sekunde.|SourceFile, Rank, FileType|
|StorageTargetAsyncWriteThroughput|Ja|Asynchroner Schreibdurchsatz im Speicherziel|Bytes pro Sekunde|Average|Die Rate, mit der der Cache Daten asynchron in ein bestimmtes Speicherziel schreibt. Dabei handelt es sich um opportunistische Schreibvorgänge, durch die Clients nicht blockiert werden.|StorageTarget|
|StorageTargetBlocksRecycled|Ja|Wiederverwendete Blöcke für Speicherziel|Anzahl|Average|Gesamtzahl der wiederverwendeten (freigegebenen) 16k-Cacheblöcke pro Speicherziel.|StorageTarget|
|StorageTargetFillThroughput|Ja|Füllungsdurchsatz im Speicherziel|Bytes pro Sekunde|Average|Die Rate, mit der der Cache Daten aus dem Speicherziel liest, um einen Cachefehler zu verarbeiten.|StorageTarget|
|StorageTargetFreeReadSpace|Ja|Freier Lesespeicher für Speicherziel|Byte|Average|Verfügbarer Lesespeicher für das Zwischenspeichern von Dateien, die einem Speicherziel zugeordnet sind.|StorageTarget|
|StorageTargetFreeWriteSpace|Ja|Freier Schreibspeicher für Speicherziel|Byte|Average|Verfügbarer Schreibspeicher für geänderte Daten, die einem Speicherziel zugeordnet sind.|StorageTarget|
|StorageTargetHealth|Ja|Integrität des Speicherziels|Anzahl|Average|Boolesche Ergebnisse des Konnektivitätstests zwischen dem Cache und den Speicherzielen.|Keine Dimensionen|
|StorageTargetIOPS|Ja|Speicherziel-IOPS insgesamt|Anzahl|Average|Die Rate aller Dateivorgänge, die der Cache an ein bestimmtes Speicherziel sendet.|StorageTarget|
|StorageTargetLatency|Ja|Speicherziellatenz|Millisekunden|Average|Die durchschnittliche Roundtriplatenz aller Dateivorgänge, die der Cache an ein bestimmtes Speicherziel sendet.|StorageTarget|
|StorageTargetMetadataReadIOPS|Ja|Lese-IOPS für Metadaten im Speicherziel|Anzahl pro Sekunde|Average|Die Rate der Dateivorgänge mit Ausnahme des Lesevorgangs, die den persistenten Zustand nicht ändern und die der Cache an ein bestimmtes Speicherziel sendet.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Ja|Schreib-IOPS für Metadaten im Speicherziel|Anzahl pro Sekunde|Average|Die Rate der Dateivorgänge mit Ausnahme des Schreibvorgangs, die den persistenten Zustand ändern und die der Cache an ein bestimmtes Speicherziel sendet.|StorageTarget|
|StorageTargetReadAheadThroughput|Ja|Read-Ahead-Durchsatz im Speicherziel|Bytes pro Sekunde|Average|Die Rate, mit der der Cache Daten opportunistisch aus dem Speicherziel liest.|StorageTarget|
|StorageTargetReadIOPS|Ja|Lese-IOPS im Speicherziel|Anzahl pro Sekunde|Average|Die Rate der Dateilesevorgänge, die der Cache an ein bestimmtes Speicherziel sendet.|StorageTarget|
|StorageTargetRecycleRate|Ja|Wiederverwendungsrate für Speicherziel|Bytes pro Sekunde|Average|Cachespeicher-Wiederverwendungsrate, die einem Speicherziel in HPC Cache zugeordnet ist. Dies ist die Rate, mit der vorhandene Daten aus dem Cache entfernt werden, um Platz für neue Daten zu schaffen.|StorageTarget|
|StorageTargetSyncWriteThroughput|Ja|Synchroner Schreibdurchsatz im Speicherziel|Bytes pro Sekunde|Average|Die Rate, mit der der Cache Daten synchron in ein bestimmtes Speicherziel schreibt. Dabei handelt es sich um Schreibvorgänge, durch die Clients blockiert werden.|StorageTarget|
|StorageTargetTotalReadThroughput|Ja|Lesedurchsatz im Speicherziel insgesamt|Bytes pro Sekunde|Average|Die Gesamtrate, mit der der Cache Daten aus einem bestimmten Speicherziel liest.|StorageTarget|
|StorageTargetTotalWriteThroughput|Ja|Schreibdurchsatz im Speicherziel insgesamt|Bytes pro Sekunde|Average|Die Gesamtrate, mit der der Cache Daten in ein bestimmtes Speicherziel schreibt.|StorageTarget|
|StorageTargetUsedReadSpace|Ja|Verwendeter Lesespeicher für Speicherziel|Byte|Average|Lesespeicher, der von zwischengespeicherten Dateien verwendet wird, die einem Speicherziel zugeordnet sind.|StorageTarget|
|StorageTargetUsedWriteSpace|Ja|Verwendeter Schreibspeicher für Speicherziel|Byte|Average|Schreibspeicher, der von geänderte Daten verwendet wird, die einem Speicherziel zugeordnet sind.|StorageTarget|
|StorageTargetWriteIOPS|Ja|Schreib-IOPS im Speicherziel|Anzahl|Average|Die Rate der Dateischreibvorgänge, die der Cache an ein bestimmtes Speicherziel sendet.|StorageTarget|
|TotalBlocksRecycled|Ja|Gesamtanzahl wiederverwendeter Blöcke|Anzahl|Average|Gesamtzahl der wiederverwendeten (freigegebenen) 16k-Cacheblöcke für HPC Cache.|Keine Dimensionen|
|TotalFreeReadSpace|Ja|Freier Lesespeicher|Byte|Average|Verfügbarer Gesamtspeicher für das Zwischenspeichern von Lesedateien.|Keine Dimensionen|
|TotalFreeWriteSpace|Ja|Freier Schreibspeicher|Byte|Average|Verfügbarer Gesamtschreibspeicher zum Speichern geänderter Daten im Cache.|Keine Dimensionen|
|TotalRecycleRate|Ja|Wiederverwendungsrate|Bytes pro Sekunde|Average|Wiederverwendungsrate für den gesamten Cachespeicher in HPC Cache. Dies ist die Rate, mit der vorhandene Daten aus dem Cache entfernt werden, um Platz für neue Daten zu schaffen.|Keine Dimensionen|
|TotalUsedReadSpace|Ja|Verwendeter Lesespeicher|Byte|Average|Gesamter Lesespeicher, der von geänderten Daten für HPC Cache verwendet wird.|Keine Dimensionen|
|TotalUsedWriteSpace|Ja|Verwendeter Schreibspeicher|Byte|Average|Gesamter Schreibspeicher, der von geänderten Daten für HPC Cache verwendet wird.|Keine Dimensionen|
|Betriebszeit|Ja|Betriebszeit|Anzahl|Average|Boolesche Ergebnisse des Konnektivitätstests zwischen dem Cache und dem Überwachungssystem.|Keine Dimensionen|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Ja|Ergebnis der Synchronisierungssitzung|Anzahl|Average|Metrik, die jedes Mal dann einen Wert von 1 protokolliert, wenn der Serverendpunkt eine Synchronisierungssitzung mit dem Cloudendpunkt erfolgreich abgeschlossen hat.|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Ja|Bytes synchronisiert|Byte|Gesamt|Für Synchronisierungssitzungen übertragene Gesamtdateigröße|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncComputedCacheHitRate|Ja|Cachetrefferrate für Cloudtiering|Percent|Average|Prozentsatz der Bytes, die aus dem Cache bereitgestellt wurden|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncRecallComputedSuccessRate|Ja|Erfolgsquote beim Cloudtieringabruf|Percent|Average|Prozentsatz aller erfolgreichen Abrufe|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncRecalledNetworkBytesByApplication|Ja|Cloudtiering-Rückrufgröße nach Anwendung|Byte|Gesamt|Größe der zurückgerufenen Daten nach Anwendung|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Ja|Cloudtiering-Rückrufgröße|Byte|Gesamt|Größe der zurückgerufenen Daten|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncRecallThroughputBytesPerSecond|Ja|Cloudtiering-Rückrufdurchsatz|Bytes pro Sekunde|Average|Größe des Datenrückruf-Durchsatzes|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncServerHeartbeat|Ja|Onlinestatus des Servers|Anzahl|Maximum|Metrik, die jedes Mal den Wert 1 protokolliert, wenn der registrierte Server erfolgreich einen Heartbeat mit dem Cloudendpunkt erfasst|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Ja|Synchronisierte Dateien|Anzahl|Gesamt|Anzahl synchronisierter Dateien|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Ja|Dateien ohne Synchronisierung|Anzahl|Average|Anzahl von Dateien mit fehlerhafter Synchronisierung|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncTieringCacheSizeBytes|Ja|Größe des Servercaches|Byte|Average|Größe der auf dem Server zwischengespeicherten Daten|SyncGroupName, ServerName, ServerEndpointName|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Ja|Fehlerhafte Funktionsanforderungen|Anzahl|Gesamt|Fehlerhafte Funktionsanforderungen|LogicalName, PartitionId, ProcessorInstance|
|AMLCalloutInputEvents|Ja|Funktionsereignisse|Anzahl|Gesamt|Funktionsereignisse|LogicalName, PartitionId, ProcessorInstance|
|AMLCalloutRequests|Ja|Funktionsanforderungen|Anzahl|Gesamt|Funktionsanforderungen|LogicalName, PartitionId, ProcessorInstance|
|ConversionErrors|Ja|Konvertierungsfehler|Anzahl|Gesamt|Konvertierungsfehler|LogicalName, PartitionId, ProcessorInstance|
|DeserializationError|Ja|Eingabefehler bei Deserialisierung|Anzahl|Gesamt|Eingabefehler bei Deserialisierung|LogicalName, PartitionId, ProcessorInstance|
|DroppedOrAdjustedEvents|Ja|Ereignisse für falsche Reihenfolge|Anzahl|Gesamt|Ereignisse für falsche Reihenfolge|LogicalName, PartitionId, ProcessorInstance|
|EarlyInputEvents|Ja|Frühe Eingabeereignisse|Anzahl|Gesamt|Frühe Eingabeereignisse|LogicalName, PartitionId, ProcessorInstance|
|Errors|Ja|Laufzeitfehler|Anzahl|Gesamt|Laufzeitfehler|LogicalName, PartitionId, ProcessorInstance|
|InputEventBytes|Ja|Eingabeereignisbytes|Byte|Gesamt|Eingabeereignisbytes|LogicalName, PartitionId, ProcessorInstance|
|InputEvents|Ja|Eingabeereignisse|Anzahl|Gesamt|Eingabeereignisse|LogicalName, PartitionId, ProcessorInstance|
|InputEventsSourcesBacklogged|Ja|Eingabeereignisse im Rückstand|Anzahl|Maximum|Eingabeereignisse im Rückstand|LogicalName, PartitionId, ProcessorInstance|
|InputEventsSourcesPerSecond|Ja|Empfangene Eingabequellen|Anzahl|Gesamt|Empfangene Eingabequellen|LogicalName, PartitionId, ProcessorInstance|
|LateInputEvents|Ja|Ereignisse bei verspäteter Eingabe|Anzahl|Gesamt|Ereignisse bei verspäteter Eingabe|LogicalName, PartitionId, ProcessorInstance|
|OutputEvents|Ja|Ausgabeereignisse|Anzahl|Gesamt|Ausgabeereignisse|LogicalName, PartitionId, ProcessorInstance|
|OutputWatermarkDelaySeconds|Ja|Wasserzeichenverzögerung|Sekunden|Maximum|Wasserzeichenverzögerung|LogicalName, PartitionId, ProcessorInstance|
|ProcessCPUUsagePercentage|Ja|CPU-Auslastung in Prozent (Vorschau)|Percent|Maximum|CPU-Auslastung in Prozent (Vorschau)|LogicalName, PartitionId, ProcessorInstance|
|ResourceUtilization|Ja|Speichereinheitnutzung in %|Percent|Maximum|Speichereinheitnutzung in %|LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|Nein|Verarbeitete Daten (Bytes)|Byte|Gesamt|Gesamtmenge an Daten, die von Abfragen verarbeitet wurden|Keine Dimensionen|
|BuiltinSqlPoolLoginAttempts|Nein|Anmeldeversuche|Anzahl|Gesamt|Anzahl von Anmeldeversuchen, die erfolgreich waren oder bei denen ein Fehler aufgetreten ist|Ergebnis|
|BuiltinSqlPoolRequestsEnded|Nein|Beendete Anforderungen|Anzahl|Gesamt|Die Anzahl der erfolgreichen, fehlerhaften oder abgebrochenen Anforderungen|Ergebnis|
|IntegrationActivityRunsEnded|Nein|Beendete Aktivitätsausführungen|Anzahl|Gesamt|Die Anzahl der erfolgreichen, fehlerhaften oder abgebrochenen Integrationsaktivitäten|Result, FailureType, Activity, ActivityType, Pipeline|
|IntegrationPipelineRunsEnded|Nein|Beendete Pipelineausführungen|Anzahl|Gesamt|Die Anzahl der erfolgreichen, fehlerhaften oder abgebrochenen Integrationspipelineausführungen|Result, FailureType, Pipeline|
|IntegrationTriggerRunsEnded|Nein|Beendete Triggerausführungen|Anzahl|Gesamt|Die Anzahl der erfolgreichen, fehlerhaften oder abgebrochenen Integrationstrigger|Result, FailureType, Trigger|
|SQLStreamingBackloggedInputEventSources|Nein|Eingabeereignisse im Rückstand (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Eingabeereignisquellen im Rückstand.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingConversionErrors|Nein|Datenkonvertierungsfehler (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl der Ausgabeereignisse, die nicht in das erwartete Ausgabeschema konvertiert werden konnten. Die Fehlerrichtlinie kann auf „Drop“ geändert werden, um Ereignisse zu löschen, bei denen dieses Szenario auftritt.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingDeserializationError|Nein|Eingabedeserialisierungsfehler (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl der Eingabeereignisse, die nicht deserialisiert werden konnten.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingEarlyInputEvents|Nein|Frühe Eingabeereignisse (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Eingabeereignissen, bei denen die Anwendungszeit im Vergleich zur Eingangszeit als früh betrachtet wird (gemäß der Richtlinie für den frühen Eingang).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventBytes|Nein|Eingabeereignisbytes (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Vom Streamingauftrag empfangene Datenmenge (in Bytes). Kann verwendet werden, um sicherzustellen, dass Ereignisse an die Eingabequelle gesendet werden.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEvents|Nein|Eingabeereignisse (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Eingabeereignissen.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventsSourcesPerSecond|Nein|Empfangene Eingabequellen (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Eingabeereignisquellen pro Sekunde.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingLateInputEvents|Nein|Späte Eingabeereignisse (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Eingabeereignissen, bei denen die Anwendungszeit im Vergleich zur Eingangszeit als spät betrachtet wird (gemäß der Richtlinie für die Eingangsverzögerung).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutOfOrderEvents|Nein|Ereignisse für falsche Reihenfolge (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Event Hub-Ereignissen (serialisierte Nachrichten), die vom Event Hub-Eingabeadapter in falscher Reihenfolge empfangen und entweder gelöscht oder mit einem angepassten Zeitstempel versehen wurden (gemäß der Richtlinie für die Ereignissortierung).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputEvents|Nein|Ausgabeereignisse (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Anzahl von Ausgabeereignissen.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputWatermarkDelaySeconds|Nein|Wasserzeichenverzögerung (Vorschau)|Anzahl|Maximum|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Wasserzeichenverzögerung bei der Ausgabe (in Sekunden).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingResourceUtilization|Nein|Ressourcenverwendung in Prozent (Vorschau)|Percent|Maximum|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik.
 Als Prozentsatz angegebene Ressourcenverwendung. Eine hohe Auslastung deutet darauf hin, dass der Auftrag fast die Höchstzahl der bereitgestellten Ressourcen verwendet.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingRuntimeErrors|Nein|Laufzeitfehler (Vorschau)|Anzahl|Gesamt|In „USA, Osten“ und „Europa, Westen“ verfügbare Vorschaumetrik. Gesamtanzahl von Fehlern im Zusammenhang mit der Abfrageverarbeitung (mit Ausnahme von Fehlern, die beim Untersuchen von Ereignissen oder beim Ausgeben von Ergebnissen ermittelt werden).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|Nein|Zugeordnete virtuelle Kerne|Anzahl|Maximum|Die einem Apache Spark-Pool zugeordneten virtuellen Kerne|SubmitterId|
|BigDataPoolAllocatedMemory|Nein|Zugeordneter Arbeitsspeicher (GB)|Anzahl|Maximum|Der dem Apache Spark-Pool zugeordnete Arbeitsspeicher (GB)|SubmitterId|
|BigDataPoolApplicationsActive|Nein|Aktive Apache Spark-Anwendungen|Anzahl|Maximum|Die Gesamtanzahl der aktiven Apache Spark-Poolanwendungen|JobState|
|BigDataPoolApplicationsEnded|Nein|Beendete Apache Spark-Anwendungen|Anzahl|Gesamt|Die Anzahl der beendeten Apache Spark-Poolanwendungen|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveQueries|Nein|Aktive Abfragen|Anzahl|Gesamt|Die aktiven Abfragen. Bei Verwenden dieser Metrik ohne Filterung und Aufteilung werden alle aktiven Abfragen angezeigt, die im System ausgeführt werden|IsUserDefined|
|AdaptiveCacheHitPercent|Nein|Prozentsatz der Treffer für adaptiven Cache|Percent|Maximum|Misst, wie gut Arbeitsauslastungen den adaptiven Cache nutzen. Verwenden Sie diese Metrik mit der Metrik für den Prozentsatz der Cachetreffer, um zu ermitteln, ob eine Skalierung zur Bereitstellung zusätzlicher Kapazität oder eine erneute Ausführung von Arbeitsauslastungen zum Aktualisieren des Caches erforderlich ist|Keine Dimensionen|
|AdaptiveCacheUsedPercent|Nein|Verwendung des adaptiven Cache in Prozent|Percent|Maximum|Misst, wie gut Arbeitsauslastungen den adaptiven Cache nutzen. Verwenden Sie diese Metrik mit der Metrik zur Cacheverwendung in Prozent, um zu ermitteln, ob eine Skalierung zur Bereitstellung zusätzlicher Kapazität oder eine erneute Ausführung von Arbeitsauslastungen zum Aktualisieren des Caches erforderlich ist|Keine Dimensionen|
|Verbindungen|Ja|Verbindungen|Anzahl|Gesamt|Gesamtanzahl der Anmeldungen beim SQL-Pool|Ergebnis|
|ConnectionsBlockedByFirewall|Nein|Durch die Firewall blockierte Verbindungen|Anzahl|Gesamt|Anzahl von Verbindungen, die durch die Firewall blockiert wurden. Überprüfen Sie die Zugriffssteuerungsrichtlinien für Ihren SQL-Pool, und überwachen Sie diese Verbindungen bei einer hohen Anzahl|Keine Dimensionen|
|CPUPercent|Nein|CPU-Auslastung in Prozent|Percent|Maximum|CPU-Auslastung für alle Knoten im SQL-Pool|Keine Dimensionen|
|DWULimit|Nein|DWU-Grenzwert|Anzahl|Maximum|Servicelevelziel des SQL-Pools|Keine Dimensionen|
|DWUUsed|Nein|DWU-Verbrauch|Anzahl|Maximum|Eine allgemeine Darstellung der Nutzung für den SQL-Pool. Wird anhand von DWU-Limit × DWU-Prozentsatz berechnet|Keine Dimensionen|
|DWUUsedPercent|Nein|DWU-Verbrauch in Prozent|Percent|Maximum|Eine allgemeine Darstellung der Nutzung für den SQL-Pool. Wird anhand des Höchstwerts zwischen CPU-Prozentsatz und E/A-Prozentsatz für Daten berechnet|Keine Dimensionen|
|LocalTempDBUsedPercent|Nein|Lokale tempdb-Auslastung in Prozent|Percent|Maximum|Lokale tempdb-Auslastung für alle Computeknoten, Werte werden alle fünf Minuten ausgegeben|Keine Dimensionen|
|MemoryUsedPercent|Nein|Verwendeter Arbeitsspeicher in Prozent|Percent|Maximum|Arbeitsspeichernutzung aller Knoten im SQL-Pool|Keine Dimensionen|
|QueuedQueries|Nein|Abfragen in Warteschlange|Anzahl|Gesamt|Kumulierte Anzahl von Anforderungen, die in die Warteschlange gestellt wurden, nachdem das Parallelitätslimit erreicht wurde|IsUserDefined|
|WLGActiveQueries|Nein|Aktive Abfragen von Arbeitsauslastungsgruppen|Anzahl|Gesamt|Die aktiven Abfragen in der Arbeitsauslastungsgruppe. Bei Verwenden dieser Metrik ohne Filterung und Aufteilung werden alle aktiven Abfragen angezeigt, die im System ausgeführt werden|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Nein|Abfragetimeouts für Arbeitsauslastungsgruppen|Anzahl|Gesamt|Abfragen für die Arbeitsauslastungsgruppe, für die ein Timeout aufgetreten ist. Die von dieser Metrik gemeldeten Abfragetimeouts sind erst nach dem Start der Abfrage aufgetreten (Wartezeiten aufgrund von Sperren oder Ressourcenwartezeiten sind nicht enthalten).|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|Nein|Zuordnung von Arbeitsauslastungsgruppen nach maximalem Ressourcenprozentsatz|Percent|Maximum|Zeigt die prozentuale Zuordnung von Ressourcen in Relation zum effektiven Ressourcenlimit (Prozent) pro Arbeitsauslastungsgruppe an. Diese Metrik informiert über die effektive Auslastung der Arbeitsauslastungsgruppe|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Nein|Zuordnung von Arbeitsauslastungsgruppen nach Systemprozentsatz|Percent|Maximum|Die prozentuale Zuordnung von Ressourcen in Relation zum gesamten System|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Nein|Effektives Ressourcenlimit (Prozent)|Percent|Maximum|Das effektive Ressourcenlimit in Prozent für die Arbeitsauslastungsgruppe. Wenn andere Arbeitsauslastungsgruppen mit „min_percentage_resource“ > 0 vorhanden sind, wird „effective_cap_percentage_resource“ proportional gesenkt|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|Nein|Effektive Mindestanzahl von Ressourcen (Prozent)|Percent|Maximum|Die Einstellung für die effektive Mindestressourcenmenge in Prozent, die unter Berücksichtigung des Servicelevels und der Einstellungen der Arbeitsauslastungsgruppe zulässig ist. „effective min_percentage_resource“ kann bei niedrigeren Servicelevels erhöht werden|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Nein|In der Warteschlange befindliche Abfragen der Arbeitsauslastungsgruppe|Anzahl|Gesamt|Kumulierte Anzahl von Anforderungen, die in die Warteschlange gestellt wurden, nachdem das Parallelitätslimit erreicht wurde|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Ja|Eingang empfangene Bytes|Byte|Gesamt|Die Anzahl der aus sämtlichen Ereignisquellen gelesenen Bytes|Keine Dimensionen|
|IngressReceivedInvalidMessages|Ja|Eingang empfangene ungültige Nachrichten|Anzahl|Gesamt|Die Anzahl der aus allen Event Hub- oder IoT Hub-Ereignisquellen gelesenen ungültigen Nachrichten|Keine Dimensionen|
|IngressReceivedMessages|Ja|Eingang empfangene Nachrichten|Anzahl|Gesamt|Die Anzahl der aus allen Event Hub- oder IoT Hub-Ereignisquellen gelesenen Nachrichten|Keine Dimensionen|
|IngressReceivedMessagesCountLag|Ja|Ingress-Anzahlrückstand empfangener Nachrichten|Anzahl|Average|Unterschied zwischen der Sequenznummer der Nachricht, die in der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der am Eingang verarbeiteten Nachricht|Keine Dimensionen|
|IngressReceivedMessagesTimeLag|Ja|Ingress-Zeitabstand empfangener Nachrichten|Sekunden|Maximum|Der Unterschied zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung in Ingress|Keine Dimensionen|
|IngressStoredBytes|Ja|Eingang gespeicherte Bytes|Byte|Gesamt|Die Gesamtgröße der erfolgreich verarbeiteten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressStoredEvents|Ja|Gespeicherte Ingress-Ereignisse|Anzahl|Gesamt|Die Gesamtgröße der vereinfachten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|WarmStorageMaxProperties|Ja|Maximale Eigenschaften von Warm Storage|Anzahl|Maximum|Maximale Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU zugelassen werden, und maximale Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU zugelassen werden|Keine Dimensionen|
|WarmStorageUsedProperties|Ja|Verwendete Eigenschaften von Warm Storage |Anzahl|Maximum|Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU verwendet werden, und Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU verwendet werden|Keine Dimensionen|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Ja|Eingang empfangene Bytes|Byte|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen Bytes|Keine Dimensionen|
|IngressReceivedInvalidMessages|Ja|Eingang empfangene ungültige Nachrichten|Anzahl|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen ungültigen Nachrichten|Keine Dimensionen|
|IngressReceivedMessages|Ja|Eingang empfangene Nachrichten|Anzahl|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen Nachrichten|Keine Dimensionen|
|IngressReceivedMessagesCountLag|Ja|Ingress-Anzahlrückstand empfangener Nachrichten|Anzahl|Average|Unterschied zwischen der Sequenznummer der Nachricht, die in der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der am Eingang verarbeiteten Nachricht|Keine Dimensionen|
|IngressReceivedMessagesTimeLag|Ja|Ingress-Zeitabstand empfangener Nachrichten|Sekunden|Maximum|Der Unterschied zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung in Ingress|Keine Dimensionen|
|IngressStoredBytes|Ja|Eingang gespeicherte Bytes|Byte|Gesamt|Die Gesamtgröße der erfolgreich verarbeiteten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressStoredEvents|Ja|Gespeicherte Ingress-Ereignisse|Anzahl|Gesamt|Die Gesamtgröße der vereinfachten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|WarmStorageMaxProperties|Ja|Maximale Eigenschaften von Warm Storage|Anzahl|Maximum|Maximale Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU zugelassen werden, und maximale Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU zugelassen werden|Keine Dimensionen|
|WarmStorageUsedProperties|Ja|Verwendete Eigenschaften von Warm Storage |Anzahl|Maximum|Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU verwendet werden, und Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU verwendet werden|Keine Dimensionen|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Gesamter Datenträgerdurchsatz aufgrund von Lesevorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Die durchschnittliche Anzahl von E/A-Lesevorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Gesamter Datenträgerdurchsatz aufgrund von Schreibvorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Die durchschnittliche Anzahl von E/A-Schreibvorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|DiskReadBytesPerSecond|Ja|Disk Read Bytes/Sec|Bytes pro Sekunde|Average|Durchschnittlicher Datenträgerdurchsatz aufgrund von Lesevorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|DiskReadLatency|Ja|Wartezeit beim Datenträgerlesevorgang|Millisekunden|Average|Gesamtwartezeit beim Lesevorgang. Die Summe der Wartezeiten bei Lesevorgängen auf Gerät und Kernel.|Keine Dimensionen|
|DiskReadOperations|Ja|Datenträgerlesevorgänge|Anzahl|Gesamt|Die Anzahl von E/A-Lesevorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|DiskWriteBytesPerSecond|Ja|Disk Write Bytes/Sec|Bytes pro Sekunde|Average|Durchschnittlicher Datenträgerdurchsatz aufgrund von Schreibvorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|DiskWriteLatency|Ja|Wartezeit beim Datenträgerschreibvorgang|Millisekunden|Average|Gesamtwartezeit beim Schreibvorgang. Die Summe der Wartezeiten bei Schreibvorgängen auf Gerät und Kernel.|Keine Dimensionen|
|DiskWriteOperations|Ja|Datenträgerschreibvorgänge|Anzahl|Gesamt|Die Anzahl von E/A-Schreibvorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|MemoryActive|Ja|Aktiver Arbeitsspeicher|Byte|Average|Die Menge an Arbeitsspeicher, die vom virtuellen Computer im letzten kleinen Zeitfenster verwendet wurde. Dies ist die „wirkliche“ Anzahl des derzeit vom virtuellen Computer benötigten Arbeitsspeichers. Zusätzlicher, ungenutzter Arbeitsspeicher kann ausgelagert oder ausgeweitet werden, ohne dass dies Auswirkungen auf die Leistung für den Gast hat.|Keine Dimensionen|
|MemoryGranted|Ja|Zugewiesener Arbeitsspeicher|Byte|Average|Die Menge an Arbeitsspeicher, die dem virtuellen Computer vom Host zugewiesen wurde. Arbeitsspeicher wird dem Host erst bei der ersten Verwendung zugewiesen, und zugewiesener Speicher kann ausgelagert oder ausgeweitet werden, wenn der VMkernel den Speicher benötigt.|Keine Dimensionen|
|MemoryUsed|Ja|Verwendeter Arbeitsspeicher|Byte|Average|Die Menge an Computerarbeitsspeicher, die vom virtuellen Computer verwendet wird.|Keine Dimensionen|
|Netzwerk eingehend|Ja|Netzwerk eingehend|Byte|Gesamt|Gesamter Netzwerkdurchsatz für empfangenen Datenverkehr.|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Netzwerk ausgehend|Byte|Gesamt|Gesamter Netzwerkdurchsatz für gesendeten Datenverkehr.|Keine Dimensionen|
|NetworkInBytesPerSecond|Ja|Eingehender Netzwerkverkehr in Bytes/Sek.|Bytes pro Sekunde|Average|Durchschnittliche Netzwerkdurchsatz für empfangenen Datenverkehr.|Keine Dimensionen|
|NetworkOutBytesPerSecond|Ja|Ausgehender Netzwerkverkehr in Bytes/Sek.|Bytes pro Sekunde|Average|Durchschnittliche Netzwerkdurchsatz für gesendeten Datenverkehr.|Keine Dimensionen|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Die CPU-Auslastung. Dieser Wert wird mit 100 % gemeldet und stellt alle Prozessorkerne im System dar. Beispielsweise verwendet ein virtueller 2-Wege-Computer, der 50 % eines Systems mit vier Kernen nutzt, zwei dieser Kerne vollständig.|Keine Dimensionen|
|PercentageCpuReady|Ja|Prozentsatz der CPU-Bereitschaft|Millisekunden|Gesamt|Die Bereitschaftszeit ist die Zeit, über die im letzten Updateintervall auf die Verfügbarkeit von CPUs gewartet wurde.|Keine Dimensionen|


## <a name="microsoftwebconnections"></a>Microsoft.Web/connections

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Requests|Nein|Requests|Anzahl|Gesamt|API-Verbindungsanforderungen|HttpStatusCode, ClientIPAddress|


## <a name="microsoftwebhostingenvironments"></a>Microsoft.Web/hostingEnvironments

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveRequests|Ja|Aktive Anforderungen (veraltet)|Anzahl|Gesamt|ActiveRequests|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit (veraltet)|Sekunden|Average|AverageResponseTime|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|BytesReceived|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|BytesSent|Instanz|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|CpuPercentage|Instanz|
|DiskQueueLength|Ja|Warteschlangenlänge des Datenträgers|Anzahl|Average|DiskQueueLength|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|Http101|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|Http2xx|Instanz|
|Http3xx|Ja|HTTP 3xx|Anzahl|Gesamt|Http3xx|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|Http401|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|Http403|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|Http404|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|Http406|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|Http4xx|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|Http5xx|Instanz|
|HttpQueueLength|Ja|Länge der HTTP-Warteschlange|Anzahl|Average|HttpQueueLength|Instanz|
|HttpResponseTime|Ja|Antwortzeit|Sekunden|Average|HttpResponseTime|Instanz|
|LargeAppServicePlanInstances|Ja|Große Worker im App Service-Plan|Anzahl|Average|Große Worker im App Service-Plan|Keine Dimensionen|
|MediumAppServicePlanInstances|Ja|Mittlere Worker im App Service-Plan|Anzahl|Average|Mittlere Worker im App Service-Plan|Keine Dimensionen|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|MemoryPercentage|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Requests|Instanz|
|SmallAppServicePlanInstances|Ja|Kleine Worker im App Service-Plan|Anzahl|Average|Kleine Worker im App Service-Plan|Keine Dimensionen|
|TotalFrontEnds|Ja|Gesamtanzahl an Front-Ends|Anzahl|Average|Gesamtanzahl an Front-Ends|Keine Dimensionen|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveRequests|Ja|Aktive Anforderungen (veraltet)|Anzahl|Gesamt|Aktive Anforderungen|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit (veraltet)|Sekunden|Average|Die durchschnittliche Zeit in Sekunden, die das Front-End zum Verarbeiten von Anforderungen benötigte.|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Die für alle Front-Ends verwendete durchschnittliche Bandbreite für eingehenden Datenverkehr in MiB.|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Die für alle Front-Ends verwendete durchschnittliche Bandbreite für eingehenden Datenverkehr in MiB.|Instanz|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|Die durchschnittliche CPU-Auslastung aller Front-End-Instanzen.|Instanz|
|DiskQueueLength|Ja|Warteschlangenlänge des Datenträgers|Anzahl|Average|Die durchschnittliche Anzahl von Lese- und Schreibanforderungen, die im Speicher in die Warteschlange eingereiht wurden. Ein hoher Wert für die Länge der Datenträgerwarteschlange ist ein Anzeichen dafür, dass eine App aufgrund einer übermäßigen E/A-Aktivität des Datenträgers verlangsamt wird.|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zum HTTP-Statuscode 101 führen.|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 200, aber < 300 führen.|Instanz|
|Http3xx|Ja|HTTP 3xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 300, aber < 400 führen.|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 401-Statuscode führen.|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 403-Statuscode führen.|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 404-Statuscode führen.|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 406-Statuscode führen.|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 400, aber < 500 führen.|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 500, aber < 600 führen.|Instanz|
|HttpQueueLength|Ja|Länge der HTTP-Warteschlange|Anzahl|Average|Die durchschnittliche Anzahl von HTTP-Anforderungen, die sich in der Warteschlange befinden müssen, bevor sie erfüllt werden. Eine hohe oder zunehmende HTTP-Warteschlangenlänge deutet darauf hin, dass für einen Plan eine hohe Auslastung besteht.|Instanz|
|HttpResponseTime|Ja|Antwortzeit|Sekunden|Average|Die Zeit in Sekunden, die das Front-End zum Verarbeiten von Anforderungen benötigt.|Instanz|
|LargeAppServicePlanInstances|Ja|Große Worker im App Service-Plan|Anzahl|Average|Große Worker im App Service-Plan|Keine Dimensionen|
|MediumAppServicePlanInstances|Ja|Mittlere Worker im App Service-Plan|Anzahl|Average|Mittlere Worker im App Service-Plan|Keine Dimensionen|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|Der von allen Front-End-Instanzen verwendete durchschnittliche Arbeitsspeicher.|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Die Gesamtzahl von Anforderungen, unabhängig vom sich ergebenden HTTP-Statuscode.|Instanz|
|SmallAppServicePlanInstances|Ja|Kleine Worker im App Service-Plan|Anzahl|Average|Kleine Worker im App Service-Plan|Keine Dimensionen|
|TotalFrontEnds|Ja|Gesamtanzahl an Front-Ends|Anzahl|Average|Gesamtanzahl an Front-Ends|Keine Dimensionen|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|Die durchschnittliche CPU-Auslastung aller Workerpoolinstanzen.|Instanz|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|Der von allen Workerpoolinstanzen verwendete durchschnittliche Arbeitsspeicher.|Instanz|
|WorkersAvailable|Ja|Verfügbare Worker|Anzahl|Average|Verfügbare Worker|Keine Dimensionen|
|WorkersTotal|Ja|Gesamtanzahl an Workern|Anzahl|Average|Gesamtanzahl an Workern|Keine Dimensionen|
|WorkersUsed|Ja|Verwendete Worker|Anzahl|Average|Verwendete Worker|Keine Dimensionen|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Die durchschnittliche eingehende Bandbreite, die für alle Instanzen des Plans verwendet wird.|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Die durchschnittliche ausgehende Bandbreite, die für alle Instanzen des Plans verwendet wird.|Instanz|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|Die durchschnittliche CPU-Nutzung über alle Instanzen des Plans hinweg.|Instanz|
|DiskQueueLength|Ja|Warteschlangenlänge des Datenträgers|Anzahl|Average|Die durchschnittliche Anzahl von Lese- und Schreibanforderungen, die im Speicher in die Warteschlange eingereiht wurden. Ein hoher Wert für die Länge der Datenträgerwarteschlange ist ein Anzeichen dafür, dass eine App aufgrund einer übermäßigen E/A-Aktivität des Datenträgers verlangsamt wird.|Instanz|
|HttpQueueLength|Ja|Länge der HTTP-Warteschlange|Anzahl|Average|Die durchschnittliche Anzahl von HTTP-Anforderungen, die sich in der Warteschlange befinden müssen, bevor sie erfüllt werden. Eine hohe oder zunehmende HTTP-Warteschlangenlänge deutet darauf hin, dass für einen Plan eine hohe Auslastung besteht.|Instanz|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|Die durchschnittliche Arbeitsspeichernutzung über alle Instanzen des Plans hinweg.|Instanz|
|SocketInboundAll|Ja|Anzahl von Sockets für eingehende Anforderungen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets, die für eingehende HTTP-Anforderungen in allen Instanzen des Plans verwendet werden.|Instanz|
|SocketLoopback|Ja|Anzahl von Sockets für Loopbackverbindungen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets, die für Loopbackverbindungen in allen Instanzen des Plans verwendet werden.|Instanz|
|SocketOutboundAll|Ja|Anzahl von Sockets für ausgehende Anforderungen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets, die für ausgehende Verbindungen in allen Instanzen des Plans unabhängig von ihrem TCP-Status verwendet werden. Zu viele ausgehende Verbindungen können zu Konnektivitätsfehlern führen.|Instanz|
|SocketOutboundEstablished|Ja|Anzahl von Sockets mit Status ESTABLISHED für ausgehende Anforderungen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand ESTABLISHED, die für ausgehende Verbindungen in allen Instanzen des Plans verwendet werden.|Instanz|
|SocketOutboundTimeWait|Ja|Anzahl von Sockets mit Status TIME_WAIT für ausgehende Anforderungen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand TIME_WAIT, die für ausgehende Verbindungen in allen Instanzen des Plans verwendet werden. Eine hohe oder steigende Anzahl ausgehender Sockets im Zustand TIME_WAIT kann Konnektivitätsfehler verursachen.|Instanz|
|TcpCloseWait|Ja|Warten auf Schließen der TCP-Verbindung|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand CLOSE_WAIT für alle Instanzen des Plans.|Instanz|
|TcpClosing|Ja|TCP-Verbindung wird geschlossen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand CLOSING für alle Instanzen des Plans.|Instanz|
|TcpEstablished|Ja|TCP-Verbindung hergestellt|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand ESTABLISHED für alle Instanzen des Plans.|Instanz|
|TcpFinWait1|Ja|Warten 1 auf Beendigung der TCP-Verbindung|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand FIN_WAIT_1 für alle Instanzen des Plans.|Instanz|
|TcpFinWait2|Ja|Warten 2 auf Beendigung der TCP-Verbindung|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand FIN_WAIT_2 für alle Instanzen des Plans.|Instanz|
|TcpLastAck|Ja|Letzte TCP-Bestätigung|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand LAST_ACK für alle Instanzen des Plans.|Instanz|
|TcpSynReceived|Ja|TCP-Synchronisierung empfangen|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand SYN_RCVD für alle Instanzen des Plans.|Instanz|
|TcpSynSent|Ja|TCP-Synchronisierung gesendet|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand SYN_SENT für alle Instanzen des Plans.|Instanz|
|TcpTimeWait|Ja|TCP-Wartezeit|Anzahl|Average|Die durchschnittliche Anzahl von Sockets im Zustand TIME_WAIT für alle Instanzen des Plans.|Instanz|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AppConnections|Ja|Verbindungen|Anzahl|Average|Die Anzahl gebundener Sockets in der Sandbox („w3wp.exe“ und untergeordnete Prozesse). Ein gebundener Socket wird durch Aufrufen von bind()-/connect()-APIs erstellt und bleibt erhalten, bis er per „CloseHandle()“/“closesocket()“ geschlossen wird. Für WebApps und FunctionApps.|Instanz|
|AverageMemoryWorkingSet|Ja|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Average|Die durchschnittliche Menge an Arbeitsspeicher, die von der App verwendet wird, in Megabytes (MiB). Für WebApps und FunctionApps.|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit (veraltet)|Sekunden|Average|Die durchschnittliche Zeit in Sekunden, die die App zum Bereitstellen von Anforderungen benötigt. Für WebApps und FunctionApps.|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Die Menge an eingehender Bandbreite in MiB, die von der App verbraucht wird. Für WebApps und FunctionApps.|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Die Menge an ausgehender Bandbreite in MiB, die von der App verbraucht wird. Für WebApps und FunctionApps.|Instanz|
|CpuTime|Ja|CPU-Zeit|Sekunden|Gesamt|Die CPU-Menge in Sekunden, die von der App verbraucht wird. Weitere Informationen zu dieser Metrik finden Sie unter https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-Zeit und CPU-Prozentsatz). Nur für WebApps.|Instanz|
|CurrentAssemblies|Ja|Aktuelle Assemblys|Anzahl|Average|Die aktuelle Anzahl von Assemblys, die in allen Anwendungsdomänen in dieser Anwendung geladen wurden. Für WebApps und FunctionApps.|Instanz|
|FileSystemUsage|Ja|Dateisystemnutzung|Byte|Average|Prozentsatz des von der App genutzten Dateisystemkontingents. Für WebApps und FunctionApps.|Keine Dimensionen|
|FunctionExecutionCount|Ja|Ausführungsanzahl für Funktion|Anzahl|Gesamt|Ausführungsanzahl für Funktion. Nur für FunctionApps.|Instanz|
|FunctionExecutionUnits|Ja|Ausführungseinheiten für Funktion|Anzahl|Gesamt|Ausführungseinheiten für Funktion. Nur für FunctionApps.|Instanz|
|Gen0Collections|Ja|Garbage Collections der Generation 0|Anzahl|Gesamt|Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 0 ausgeführt wurde. In Garbage Collections höherer Generationen sind alle Garbage Collections niedrigerer Generationen enthalten. Für WebApps und FunctionApps.|Instanz|
|Gen1Collections|Ja|Garbage Collections der Generation 1|Anzahl|Gesamt|Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 1 ausgeführt wurde. In Garbage Collections höherer Generationen sind alle Garbage Collections niedrigerer Generationen enthalten. Für WebApps und FunctionApps.|Instanz|
|Gen2Collections|Ja|Garbage Collections der Generation 2|Anzahl|Gesamt|Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 2 ausgeführt wurde. Für WebApps und FunctionApps.|Instanz|
|Ziehpunkte|Ja|Anzahl von Handles|Anzahl|Average|Die Gesamtanzahl von Handles, die aktuell durch den App-Prozess geöffnet sind. Für WebApps und FunctionApps.|Instanz|
|HealthCheckStatus|Ja|Status der Integritätsüberprüfung|Anzahl|Average|Status der Integritätsüberprüfung. Für WebApps und FunctionApps.|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zum HTTP-Statuscode 101 führen. Für WebApps und FunctionApps.|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 200, aber < 300 führen. Für WebApps und FunctionApps.|Instanz|
|Http3xx|Ja|HTTP 3xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 300, aber < 400 führen. Für WebApps und FunctionApps.|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 401-Statuscode führen. Für WebApps und FunctionApps.|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 403-Statuscode führen. Für WebApps und FunctionApps.|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 404-Statuscode führen. Für WebApps und FunctionApps.|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 406-Statuscode führen. Für WebApps und FunctionApps.|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 400, aber < 500 führen. Für WebApps und FunctionApps.|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 500, aber < 600 führen. Für WebApps und FunctionApps.|Instanz|
|HttpResponseTime|Ja|Antwortzeit|Sekunden|Average|Die Zeit in Sekunden, die die App zum Bedienen von Anforderungen benötigt. Für WebApps und FunctionApps.|Instanz|
|IoOtherBytesPerSecond|Ja|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess Bytes an E/A-Vorgänge ausgibt, die keine Daten beinhalten (beispielsweise Steuerungsvorgänge). Für WebApps und FunctionApps.|Instanz|
|IoOtherOperationsPerSecond|Ja|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess E/A-Vorgänge ausgibt, bei denen es sich um keine Lese- oder Schreibvorgänge handelt. Für WebApps und FunctionApps.|Instanz|
|IoReadBytesPerSecond|Ja|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess Bytes aus E/A-Vorgängen liest. Für WebApps und FunctionApps.|Instanz|
|IoReadOperationsPerSecond|Ja|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess E/A-Lesevorgänge ausgibt. Für WebApps und FunctionApps.|Instanz|
|IoWriteBytesPerSecond|Ja|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess Bytes in E/A-Vorgänge schreibt. Für WebApps und FunctionApps.|Instanz|
|IoWriteOperationsPerSecond|Ja|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess E/A-Schreibvorgänge ausgibt. Für WebApps und FunctionApps.|Instanz|
|MemoryWorkingSet|Ja|Arbeitssatz für Arbeitsspeicher|Byte|Average|Die aktuelle Menge an Arbeitsspeicher in MiB, die von der App verwendet wird. Für WebApps und FunctionApps.|Instanz|
|PrivateBytes|Ja|Private Bytes|Byte|Average|„Private Bytes“ gibt die aktuell durch den App-Prozess zugeordnete Größe des Arbeitsspeichers (in Bytes) an, die nicht mit anderen Prozessen geteilt werden kann. Für WebApps und FunctionApps.|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Die Gesamtzahl von Anforderungen, unabhängig vom sich ergebenden HTTP-Statuscode. Für WebApps und FunctionApps.|Instanz|
|RequestsInApplicationQueue|Ja|Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Die Anzahl von Anforderungen in der Warteschlange für Anwendungsanforderungen. Für WebApps und FunctionApps.|Instanz|
|Threads|Ja|Threadanzahl|Anzahl|Average|Die Anzahl von Threads, die derzeit im App-Prozess aktiv sind. Für WebApps und FunctionApps.|Instanz|
|TotalAppDomains|Ja|App-Domänen insgesamt|Anzahl|Average|Die Anzahl von Anwendungsdomänen, die aktuell in dieser Anwendung geladen sind. Für WebApps und FunctionApps.|Instanz|
|TotalAppDomainsUnloaded|Ja|Entladene App-Domänen insgesamt|Anzahl|Average|Die Gesamtanzahl von Anwendungsdomänen, die seit dem Start der Anwendung entladen wurden. Für WebApps und FunctionApps.|Instanz|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AppConnections|Ja|Verbindungen|Anzahl|Average|Die Anzahl gebundener Sockets in der Sandbox („w3wp.exe“ und untergeordnete Prozesse). Ein gebundener Socket wird durch Aufrufen von bind()-/connect()-APIs erstellt und bleibt erhalten, bis er per „CloseHandle()“/“closesocket()“ geschlossen wird.|Instanz|
|AverageMemoryWorkingSet|Ja|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Average|Die durchschnittliche Menge an Arbeitsspeicher, die von der App verwendet wird, in Megabytes (MiB).|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit (veraltet)|Sekunden|Average|Die durchschnittliche Zeit in Sekunden, die die App zum Bereitstellen von Anforderungen benötigt.|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Die Menge an eingehender Bandbreite in MiB, die von der App verbraucht wird.|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Die Menge an ausgehender Bandbreite in MiB, die von der App verbraucht wird.|Instanz|
|CpuTime|Ja|CPU-Zeit|Sekunden|Gesamt|Die CPU-Menge in Sekunden, die von der App verbraucht wird. Weitere Informationen zu dieser Metrik finden Sie unter https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-Zeit und CPU-Prozentsatz).|Instanz|
|CurrentAssemblies|Ja|Aktuelle Assemblys|Anzahl|Average|Die aktuelle Anzahl von Assemblys, die in allen Anwendungsdomänen in dieser Anwendung geladen wurden.|Instanz|
|FileSystemUsage|Ja|Dateisystemnutzung|Byte|Average|Prozentsatz des von der App genutzten Dateisystemkontingents.|Keine Dimensionen|
|FunctionExecutionCount|Ja|Ausführungsanzahl für Funktion|Anzahl|Gesamt|Ausführungsanzahl für Funktion|Instanz|
|FunctionExecutionUnits|Ja|Ausführungseinheiten für Funktion|Anzahl|Gesamt|Ausführungseinheiten für Funktion|Instanz|
|Gen0Collections|Ja|Garbage Collections der Generation 0|Anzahl|Gesamt|Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 0 ausgeführt wurde. In Garbage Collections höherer Generationen sind alle Garbage Collections niedrigerer Generationen enthalten.|Instanz|
|Gen1Collections|Ja|Garbage Collections der Generation 1|Anzahl|Gesamt|Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 1 ausgeführt wurde. In Garbage Collections höherer Generationen sind alle Garbage Collections niedrigerer Generationen enthalten.|Instanz|
|Gen2Collections|Ja|Garbage Collections der Generation 2|Anzahl|Gesamt|Die Häufigkeit, mit der seit dem Start des App-Prozesses eine Garbage Collection für die Objekte der Generation 2 ausgeführt wurde.|Instanz|
|Ziehpunkte|Ja|Anzahl von Handles|Anzahl|Average|Die Gesamtanzahl von Handles, die aktuell durch den App-Prozess geöffnet sind.|Instanz|
|HealthCheckStatus|Ja|Status der Integritätsüberprüfung|Anzahl|Average|Status der Integritätsüberprüfung|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zum HTTP-Statuscode 101 führen.|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 200, aber < 300 führen.|Instanz|
|Http3xx|Ja|HTTP 3xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 300, aber < 400 führen.|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 401-Statuscode führen.|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 403-Statuscode führen.|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 404-Statuscode führen.|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP 406-Statuscode führen.|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 400, aber < 500 führen.|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|Die Anzahl von Anforderungen, die zu einem HTTP-Statuscode = 500, aber < 600 führen.|Instanz|
|HttpResponseTime|Ja|Antwortzeit|Sekunden|Average|Die Zeit in Sekunden, die die App zum Bedienen von Anforderungen benötigt.|Instanz|
|IoOtherBytesPerSecond|Ja|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess Bytes an E/A-Vorgänge ausgibt, die keine Daten beinhalten (beispielsweise Steuerungsvorgänge).|Instanz|
|IoOtherOperationsPerSecond|Ja|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess E/A-Vorgänge ausgibt, bei denen es sich um keine Lese- oder Schreibvorgänge handelt.|Instanz|
|IoReadBytesPerSecond|Ja|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess Bytes aus E/A-Vorgängen liest.|Instanz|
|IoReadOperationsPerSecond|Ja|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess E/A-Lesevorgänge ausgibt.|Instanz|
|IoWriteBytesPerSecond|Ja|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess Bytes in E/A-Vorgänge schreibt.|Instanz|
|IoWriteOperationsPerSecond|Ja|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|Die Rate, mit der der App-Prozess E/A-Schreibvorgänge ausgibt.|Instanz|
|MemoryWorkingSet|Ja|Arbeitssatz für Arbeitsspeicher|Byte|Average|Die aktuelle Menge an Arbeitsspeicher in MiB, die von der App verwendet wird.|Instanz|
|PrivateBytes|Ja|Private Bytes|Byte|Average|„Private Bytes“ gibt die aktuell durch den App-Prozess zugeordnete Größe des Arbeitsspeichers (in Bytes) an, die nicht mit anderen Prozessen geteilt werden kann.|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Die Gesamtzahl von Anforderungen, unabhängig vom sich ergebenden HTTP-Statuscode.|Instanz|
|RequestsInApplicationQueue|Ja|Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Die Anzahl von Anforderungen in der Warteschlange für Anwendungsanforderungen.|Instanz|
|Threads|Ja|Threadanzahl|Anzahl|Average|Die Anzahl von Threads, die derzeit im App-Prozess aktiv sind.|Instanz|
|TotalAppDomains|Ja|App-Domänen insgesamt|Anzahl|Average|Die Anzahl von Anwendungsdomänen, die aktuell in dieser Anwendung geladen sind.|Instanz|
|TotalAppDomainsUnloaded|Ja|Entladene App-Domänen insgesamt|Anzahl|Average|Die Gesamtanzahl von Anwendungsdomänen, die seit dem Start der Anwendung entladen wurden.|Instanz|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|BytesSent|Instanz|
|FunctionErrors|Ja|FunctionErrors|Anzahl|Gesamt|FunctionErrors|Instanz|
|FunctionHits|Ja|FunctionHits|Anzahl|Gesamt|FunctionHits|Instanz|
|SiteErrors|Ja|SiteErrors|Anzahl|Gesamt|SiteErrors|Instanz|
|SiteHits|Ja|SiteHits|Anzahl|Gesamt|SiteHits|Instanz|


## <a name="wandiscofusionmigrators"></a>Wandisco.Fusion/migrators

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Bytes pro Sekunde|Ja|Bytes pro Sekunde|Bytes pro Sekunde|Average|Für einen Migrator genutzte Durchsatzgeschwindigkeit in Bytes/Sekunde|Keine Dimensionen|
|DirectoriesCreatedCount|Ja|Anzahl der erstellten Verzeichnisse|Anzahl|Gesamt|Fortlaufende Ansicht, wie viele Verzeichnisse im Rahmen einer Migration erstellt wurden|Keine Dimensionen|
|FileMigrationCount|Ja|Anzahl der migrierten Dateien|Anzahl|Gesamt|Fortlaufende Gesamtanzahl der migrierten Dateien|Keine Dimensionen|
|InitialScanDataMigratedInBytes|Ja|Migrierte Bytes nach anfänglichem Scanvorgang|Byte|Gesamt|Zeigt die Gesamtanzahl der Bytes an, die als Ergebnis des anfänglichen Scanvorgangs des lokalen Dateisystems in einem neuen Migrator übertragen wurden. Daten, die der Migration nach dem anfänglichen Scanvorgang hinzugefügt werden, sind NICHT in dieser Metrik enthalten.|Keine Dimensionen|
|LiveDataMigratedInBytes|Ja|Migrierte Livedaten in Bytes|Anzahl|Gesamt|Fortlaufende Gesamtanzahl von Livedaten, die seit Beginn der Migration aufgrund von Clientaktivitäten geändert wurden|Keine Dimensionen|
|MigratorCPULoad|Ja|CPU-Auslastung durch Migrator|Percent|Average|CPU-Verbrauch durch den Migratorprozess|Keine Dimensionen|
|NumberOfExcludedPaths|Ja|Anzahl der ausgeschlossenen Pfade|Anzahl|Gesamt|Fortlaufende Anzahl der Pfade, die aufgrund von Ausschlussregeln von der Migration ausgeschlossen wurden|Keine Dimensionen|
|NumberOfFailedPaths|Ja|Anzahl der Pfade mit Fehlern|Anzahl|Gesamt|Anzahl der Pfade, die nicht migriert werden konnten|Keine Dimensionen|
|SystemCPULoad|Ja|CPU-Auslastung durch System|Percent|Average|CPU-Gesamtverbrauch|Keine Dimensionen|
|TotalMigratedDataInBytes|Ja|Gesamtmenge der migrierten Daten in Bytes|Byte|Gesamt|Ansicht der erfolgreich migrierten Bytes für einen bestimmten Migrator|Keine Dimensionen|
|TotalTransactions|Ja|Transaktionen gesamt|Anzahl|Gesamt|Eine fortlaufende Gesamtanzahl der Datentransaktionen, die dem Benutzer in Rechnung gestellt werden könnten|Keine Dimensionen|

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu Metriken in Azure Monitor](../data-platform.md)
- [Erstellen von Warnungen zu Metriken](../alerts/alerts-overview.md)
- [Exportieren von Metriken in Storage, Event Hub oder Log Analytics](../essentials/platform-logs-overview.md)