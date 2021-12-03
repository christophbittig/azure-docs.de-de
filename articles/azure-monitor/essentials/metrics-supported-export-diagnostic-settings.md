---
title: Exportverhalten von Metriken mit NULL- und Nullwerten
description: In diesem Artikel werden NULL- vs. Nullwerte beim Exportieren von Metriken erläutert. Außerdem finden Sie einen Verweis auf eine Liste mit Metriken, die nicht exportiert werden können.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.openlocfilehash: 53c5aad7061afb0479e9678a188fe99779893fef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017880"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Über Diagnoseeinstellungen exportierbare Azure Monitor-Plattformmetriken

Azure Monitor stellt [Plattformmetriken](../essentials/data-platform-metrics.md) standardmäßig ohne Konfiguration bereit. Es gibt verschiedene Methoden für die Interaktion mit Plattformmetriken, z.B. die Diagrammdarstellung im Portal, den Zugriff über die REST-API oder die Abfrage über PowerShell oder CLI. Eine vollständige Liste der Plattformmetriken, die derzeit mit der konsolidierten Metrikpipeline von Azure Monitor zur Verfügung stehen, finden Sie unter [Unterstützte Metriken](./metrics-supported.md). Verwenden Sie die [API-Version 2018-01-01](/rest/api/monitor/metricdefinitions), um diese Metriken abzufragen und darauf zuzugreifen. Weitere Metriken stehen möglicherweise im Portal oder über Legacy-APIs zur Verfügung.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Über Diagnoseeinstellungen nicht exportierbare Metriken

Weitere Informationen finden Sie in der Spalte „Exportierbar?“ unter [Unterstützte Metriken von Azure Monitor](./metrics-supported.md#exporting-platform-metrics-to-other-locations).

Für das Exportieren von Metriken über Diagnoseeinstellungen gibt es Beschränkungen. Alle Metriken können jedoch mithilfe der REST-API exportiert werden.

## <a name="exported-zero-vs-null-values"></a>Exportierte Null- vs. NULL-Werte

Metriken unterscheiden sich in ihrem Verhalten, je nachdem, ob Null- oder NULL-Werte vorhanden sind.  Manche Metriken melden 0, wenn keine Daten enthalten sind, z. B. Metriken zu HTTP-Fehlern. Andere Metriken speichern NULL-Werte, wenn keine Daten enthalten sind, da dies darauf hinweisen kann, dass die Ressource offline ist. Bei der Diagrammerstellung für diese Metriken mit NULL-Werten sehen Sie den Unterschied. Diese Werte werden als [gestrichelte Linien](metrics-troubleshoot.md#chart-shows-dashed-line) angezeigt. 

Wenn Plattformmetriken über Diagnoseeinstellungen exportiert werden können, stimmen sie mit dem Verhalten der Metrik überein. Das heißt, es werden NULL-Werte exportiert, wenn die Ressource keine Daten sendet.  Null-Werte werden nur exportiert, wenn sie von der zugrunde liegenden Ressource tatsächlich ausgegeben wurden. 

Wenn Sie eine Ressourcengruppe oder eine bestimmte Ressource löschen, werden Metrikdaten aus den betroffenen Ressourcen nicht mehr an die Exportziele der Diagnoseeinstellungen gesendet. Das heißt, sie werden nicht mehr in Event Hubs, Azure Storage-Konten und Log Analytics-Arbeitsbereichen angezeigt.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Metriken mit bisherigem Null-Export für NULL-Werte

**Bis zum 1. Juni 2020** gaben die untenstehenden Metriken Null-Werte aus, wenn keine Daten vorhanden waren. Diese Null-Werte konnten dann in nachgelagerte Systeme wie Log Analytics und Azure Storage exportiert werden.  Dieses Verhalten hat zu Verwirrung zwischen echten Null-Werten, die von der Ressource ausgegeben werden, und interpretierten Null-Werten (NULL-Werte) geführt. Deshalb wurde dieses Verhalten geändert, damit es dem der zugrunde liegenden Metrik entspricht. Dies wurde auch im vorherigen Abschnitt bereits erwähnt. 

Diese Änderung wurde in allen öffentlichen und privaten Clouds vorgenommen.

Die Änderung wirkte sich nicht auf das Verhalten der folgenden Elemente und Funktionen aus: 
   - Plattformressourcenprotokolle, die über Diagnoseeinstellungen exportiert werden
   - Metrikdiagramme in Metrik-Explorer
   - Warnungen zu Plattformmetriken
 
Unten sehen Sie eine Liste der Metriken, deren Verhalten sich geändert hat. 

| ResourceType                    | Metrik               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Nicht autorisierte Gatewayanforderungen (veraltet)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Gatewayanforderungen insgesamt (veraltet)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Erfolgreiche Gatewayanforderungen (veraltet)  | 
| Microsoft.ApiManagement/service | Requests |  Requests  | 
| Microsoft.ApiManagement/service | OtherRequests |  Andere Gatewayanforderungen (veraltet)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Fehlerhafte Gatewayanforderungen (veraltet)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Fehlerhafte EventHub-Ereignisse  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Gesamtzahl von EventHub-Ereignissen  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Größe von EventHub-Ereignissen  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  EventHub-Ereignisse mit Zeitüberschreitung  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Gedrosselte EventHub-Ereignisse  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Erfolgreiche EventHub-Ereignisse  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Abgelehnte EventHub-Ereignisse  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Gelöschte EventHub-Ereignisse  | 
| Microsoft.ApiManagement/service | Duration |  Gesamtdauer von Gatewayanforderungen  | 
| Microsoft.ApiManagement/service | BackendDuration |  Dauer von Back-End-Anforderungen  | 
| Microsoft.DBforMariaDB/servers | storage_used |  Verwendeter Speicher  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  Speicher in Prozent  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  Speicherbegrenzung  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  Verwendeter Serverprotokollspeicher  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  Serverprotokollspeicher in Prozent  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  Begrenzung des Serverprotokollspeichers  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  Replikationsverzögerung in Sekunden  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  Netzwerk eingehend  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  Netzwerk ausgehend  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  Arbeitsspeicher in Prozent  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  E/A in Prozent  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  CPU in Prozent  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  Verbindungsfehler  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  Verwendeter Sicherungsspeicher  | 
| Microsoft.DBforMariaDB/servers | active_connections |  Die aktiven Verbindungen.  | 
| Microsoft.DBforMySQL/servers | storage_used |  Verwendeter Speicher  | 
| Microsoft.DBforMySQL/servers | storage_percent |  Speicher in Prozent  | 
| Microsoft.DBforMySQL/servers | storage_limit |  Speicherbegrenzung  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  Verwendeter Serverprotokollspeicher  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  Serverprotokollspeicher in Prozent  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  Begrenzung des Serverprotokollspeichers  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  Replikationsverzögerung in Sekunden  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  Netzwerk eingehend  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  Netzwerk ausgehend  | 
| Microsoft.DBforMySQL/servers | memory_percent |  Arbeitsspeicher in Prozent  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  E/A in Prozent  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  CPU in Prozent  | 
| Microsoft.DBforMySQL/servers | connections_failed |  Verbindungsfehler  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  Verwendeter Sicherungsspeicher  | 
| Microsoft.DBforMySQL/servers | active_connections |  Die aktiven Verbindungen.  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  Platzhalter für Digital Twins-Knotentelemetrie  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  Successful twin queries (Erfolgreiche Zwillingsabfragen)  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  Twin queries result size (Ergebnisgröße von Zwillingsabfragen)  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  Successful job queries (Erfolgreiche Auftragsabfragen)  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  Failed job queries (Nicht erfolgreiche Auftragsabfragen)  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)  | 
| Microsoft.Devices/IotHubs | jobs.failed |  Fehlerhafte Aufträge  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)  | 
| Microsoft.Devices/IotHubs | jobs.completed |  Abgeschlossene Aufträge  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  Successful job cancellations (Erfolgreiche Auftragsabbrüche)  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Event Grid-Wartezeit (Vorschau)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Event Grid-Übermittlungen (Vorschau)  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  Geräte gesamt (veraltet)  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  Verbundene Geräte (veraltet)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  Gerätedatennutzung gesamt (Vorschau)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  Gerätedatennutzung gesamt  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  Gesamtzahl verwendeter Nachrichten  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  Telemetry messages sent (Gesendete Telemetrienachrichten)  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  Anzahl von Drosselungsfehlern  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.Ingress.allProtocol |  Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  Routing: Übermittelte Telemetrienachrichten  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  Routing: Verwaiste Telemetrienachrichten   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  Routing: Nicht kompatible Telemetrienachrichten  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  Routing: An den Fallback übermittelte Nachrichten  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  Routing: Verworfene Telemetrienachrichten   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  Routing: Nachrichtenwartezeit für Speicher  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  Routing: Nachrichtenwartezeit für Service Bus-Thema  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  Routing: Nachrichtenwartezeit für Service Bus-Warteschlange  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  Routing: Nachrichtenwartezeit für Event Hub  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events |  Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  Routing: An den Speicher übermittelte Daten  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  Routing: An den Speicher übermittelte Blobs  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  Routing: An den Speicher übermittelte Nachrichten  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  Routing: An Service Bus-Thema übermittelte Nachrichten  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  Routing: An Service Bus-Warteschlange übermittelte Nachrichten  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  Routing: An Event Hub übermittelte Nachrichten  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  Routing: An Nachrichten/Ereignisse übermittelte Nachrichten  | 
| Microsoft.Devices/IotHubs | Konfigurationen |  Konfigurationsmetriken  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  Abgelaufene C2D-Nachrichten (Vorschau)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  Abgelehnte C2D-Nachrichten  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  Abgeschlossene C2D-Nachrichtenübermittlungen  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  Abgebrochene C2D-Nachrichten  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  Registrierungsversuche  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  Zugewiesene Geräte  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  Nachweisversuche  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Anforderungseinheiten gesamt  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Anzahl von Anforderungen  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo-Anforderungen  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Kosten der Mongo-Anforderung  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  Latenz erfolgreicher Veröffentlichungen  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  Veröffentlichte Ereignisse  | 
| Microsoft.EventGrid/domains | PublishFailCount |  Ereignisse mit Veröffentlichungsfehler  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  Übereinstimmende Ereignisse  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  Gelöschte Ereignisse  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  Übermittelte Ereignisse  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  Unzustellbare Ereignisse  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  Übereinstimmende Ereignisse  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  Gelöschte Ereignisse  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  Übermittelte Ereignisse  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  Unzustellbare Ereignisse  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  Ereignisse ohne Übereinstimmung  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Latenz erfolgreicher Veröffentlichungen  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  Veröffentlichte Ereignisse  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  Ereignisse mit Veröffentlichungsfehler  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  Ereignisse ohne Übereinstimmung  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  Latenz erfolgreicher Veröffentlichungen  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  Veröffentlichte Ereignisse  | 
| Microsoft.EventGrid/topics | PublishFailCount |  Ereignisse mit Veröffentlichungsfehler  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  Ausgehende Nachrichten  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  Ausgehende Bytes.  | 
| Microsoft.EventHub/clusters | IncomingRequests |  Eingehende Anforderungen  | 
| Microsoft.EventHub/clusters | IncomingMessages |  Eingehende Nachrichten  | 
| Microsoft.EventHub/clusters | IncomingBytes |  Eingehende Bytes.  | 
| Microsoft.EventHub/namespaces | SVRBSY |  Fehler durch ausgelasteten Server (veraltet)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  Erfolgreiche Anforderungen (veraltet)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  Ausgehende Nachrichten (veraltet)  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  Ausgehende Nachrichten  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  Ausgehende Bytes.  | 
| Microsoft.EventHub/namespaces | MISCERR |  Andere Fehler (veraltet)  | 
| Microsoft.EventHub/namespaces | INTERR |  Interne Serverfehler (veraltet)  | 
| Microsoft.EventHub/namespaces | INREQS |  Eingehende Anforderungen (veraltet)  | 
| Microsoft.EventHub/namespaces | INMSGS |  Eingehende Nachrichten (veraltet)  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  Eingehende Anforderungen  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  Eingehende Nachrichten  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  Eingehende Bytes.  | 
| Microsoft.EventHub/namespaces | FAILREQ |  Fehlerhafte Anforderungen (veraltet)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  Ausgehende Nachrichten (veraltet)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  Ausgehende Bytes (veraltet)  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  Ausgehende Bytes (veraltet)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  Eingehende Nachrichten (veraltet)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  Eingehende Bytes (veraltet)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  Eingehende Bytes (veraltet)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  Archivnachrichten (veraltet)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  Durchsatz von Archivnachrichten (veraltet)  | 
| Microsoft.EventHub/namespaces | EHABL |  Archivierte Backlognachrichten (veraltet)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  Anzahl aktiver Worker  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  Gatewayanforderungen  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  Kategorisierte Gatewayanforderungen  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  Initiierte Skalierungsaktionen  | 
| Microsoft.Insights/Components | traces/count |  Traces  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  HTTP-Anforderungsrate  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  HTTP-Anforderungen in der Anwendungswarteschlange  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  Ausnahmerate  | 
| Microsoft.Insights/Components | pageViews/count |  Seitenaufrufe  | 
| Microsoft.Insights/Components | exceptions/count |  Ausnahmen  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  Ergebnis der Streamingerfassung  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  Dauer der Streamingerfassung  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  Datenrate der Streamingerfassung  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  Anforderungsrate der Streamingerfassung  | 
| Microsoft.Kusto/Clusters | QueryDuration |  Abfragedauer  | 
| Microsoft.Kusto/Clusters | KeepAlive |  Keep-Alive  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  Datenerfassungsvolumen (in MB)  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  Datenerfassungsauslastung  | 
| Microsoft.Kusto/Clusters | IngestionResult |  Ergebnis der Datenerfassung  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  Erfassungslatenz (in Sekunden)  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  Exportauslastung  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  Verarbeitete Ereignisse (für Event/IoT Hub)  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  Ergebnis des fortlaufenden Exports  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  ContinuousExportPendingCount  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  Fortlaufender Export – Anzahl der exportierten Datensätze  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  Maximale Wartezeit in Minuten für fortlaufenden Export  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  Cacheauslastung  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Ereignisse zu gedrosselten Triggern  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  Erfolgreiche Trigger   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  Gestartete Trigger   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  Übersprungene Trigger  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  Ausgelöste Trigger   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  Triggerfehler   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  Abgeschlossene Trigger   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  Ereignisse zu gedrosselten Ausführungen  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Ereignisse zu gedrosselten Ausführungsstarts  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  Erfolgreiche Ausführungen  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  Gestartete Ausführungen  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  Ausführungsfehler  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  Abgeschlossene Ausführungen  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  Abgebrochene Ausführungen  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  Prozentsatz der Ausführungsfehler  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  Ereignisse zu gedrosselten Aktionen  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  Erfolgreiche Aktionen   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  Gestartete Aktionen   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  Übersprungene Aktionen   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  Aktionsfehler   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  Abgeschlossene Aktionen   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  Ereignisse zu gedrosselten Triggern  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  Erfolgreiche Trigger   | 
| Microsoft.Logic/workflows | TriggersStarted |  Gestartete Trigger   | 
| Microsoft.Logic/workflows | TriggersSkipped |  Übersprungene Trigger  | 
| Microsoft.Logic/workflows | TriggersFired |  Ausgelöste Trigger   | 
| Microsoft.Logic/workflows | TriggersFailed |  Triggerfehler   | 
| Microsoft.Logic/workflows | TriggersCompleted |  Abgeschlossene Trigger   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  Gesamtzahl der abrechenbaren Ausführungen  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  Ereignisse zu gedrosselten Ausführungen  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  Ereignisse zu gedrosselten Ausführungsstarts  | 
| Microsoft.Logic/workflows | RunsSucceeded |  Erfolgreiche Ausführungen  | 
| Microsoft.Logic/workflows | RunsStarted |  Gestartete Ausführungen  | 
| Microsoft.Logic/workflows | RunsFailed |  Ausführungsfehler  | 
| Microsoft.Logic/workflows | RunsCompleted |  Abgeschlossene Ausführungen  | 
| Microsoft.Logic/workflows | RunsCancelled |  Abgebrochene Ausführungen  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  Prozentsatz der Ausführungsfehler  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Nutzungsabrechnung für Ausführungen mit Speicherverbrauch  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Nutzungsabrechnung für Ausführungen mit Speicherverbrauch  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Nutzungsabrechnung für Ausführungen standardmäßiger Connectors  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Nutzungsabrechnung für Ausführungen standardmäßiger Connectors  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Nutzungsabrechnung für Ausführungen nativer Vorgänge  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Nutzungsabrechnung für Ausführungen nativer Vorgänge  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  Ausführungen abrechenbarer Trigger  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  Ausführungen abrechenbarer Aktionen  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  Ereignisse zu gedrosselten Aktionen  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  Erfolgreiche Aktionen   | 
| Microsoft.Logic/workflows | ActionsStarted |  Gestartete Aktionen   | 
| Microsoft.Logic/workflows | ActionsSkipped |  Übersprungene Aktionen   | 
| Microsoft.Logic/workflows | ActionsFailed |  Aktionsfehler   | 
| Microsoft.Logic/workflows | ActionsCompleted |  Abgeschlossene Aktionen   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Anforderungsanzahl für die Web Application Firewall  | 
| Microsoft.Network/frontdoors | TotalLatency |  Gesamtlatenz  | 
| Microsoft.Network/frontdoors | ResponseSize |  Antwortgröße  | 
| Microsoft.Network/frontdoors | RequestSize |  Anforderungsgröße  | 
| Microsoft.Network/frontdoors | RequestCount |  Anzahl der Anforderungen  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  Größe von abrechnungsfähigen Antworten  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  Latenz der Back-End-Anforderung  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  Back-End-Anforderungsanzahl  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  Prozentsatz der Back-End-Integrität  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  Abfragen nach zurückgegebenem Endpunkt  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  Ausstehende Pushbenachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  Registrierungsaktualisierungsvorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  Registrierungslesevorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  Registrierungslöschvorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  Registrierungserstellungsvorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  Registrierungsvorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  WNS-Autorisierungsfehler (falsches Token)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  WNS-Autorisierungsfehler (nicht erreichbar)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  Gedrosselte WNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  Erfolgreiche WNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  WNS-Fehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  WNS-Autorisierungsfehler (ungültiges Token)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  WNS-Fehler durch ungültige Benachrichtigungsgröße  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  Ungültiges WNS-Benachrichtigungsformat  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  WNS-Autorisierungsfehler (ungültige Anmeldeinformationen)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  WNS-Fehler durch abgelaufenen Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  Verworfene WNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  WNS-Kanal gedrosselt  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  WNS-Kanal nicht verbunden  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  WNS-Fehler durch fehlerhaften Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  WNS-Authentifizierungsfehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  Gedrosselte MPNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  Erfolgreiche MPNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  MPNS-Fehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  Ungültiges MPNS-Benachrichtigungsformat  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  Ungültige MPNS-Anmeldeinformationen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  Verworfene MPNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  MPNS-Kanal nicht verbunden  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  MPNS-Fehler durch fehlerhaften Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  MPNS-Authentifizierungsfehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  GCM-Fehler durch falschen Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  Gedrosselte GCM-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  Erfolgreiche GCM-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  GCM-Fehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  GCM-Fehler durch ungültige Benachrichtigungsgröße  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  Ungültiges GCM-Benachrichtigungsformat  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  GCM-Autorisierungsfehler (ungültige Anmeldeinformationen)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  GCM-Fehler durch abgelaufenen Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  GCM-Fehler durch fehlerhaften Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  GCM-Authentifizierungsfehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  Erfolgreiche APNS-Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  APNS-Fehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  APNS-Fehler durch ungültige Benachrichtigungsgröße  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  APNS-Autorisierungsfehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  APNS-Fehler durch abgelaufenen Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  APNS-Fehler durch fehlerhaften Kanal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  Erfolgreiche Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  Fehler des externen Benachrichtigungssystems  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  Nutzlastfehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  Kanalfehler  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  Alle ausgehenden Benachrichtigungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  Installationsvorgänge erstellen oder aktualisieren  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  Patchinstallationsvorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  Installationsvorgänge abrufen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  Installationsvorgänge löschen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  Installationsverwaltungsvorgänge  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  Geplante Pushbenachrichtigungen (abgebrochen)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  Geplante Pushbenachrichtigungen (gesendet)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  Alle eingehenden Anforderungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  Alle eingehenden fehlerhaften Anforderungen  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  Eingehende Nachrichten  | 
| Microsoft.OperationalInsights/workspaces | Heartbeat |  Heartbeat  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  Ausgehende Nachrichten  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  Eingehende Anforderungen  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  Eingehende Nachrichten  | 
| Microsoft.SignalRService/SignalR | UserErrors |  Benutzerfehler  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  Systemfehler  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  Ausgehender Datenverkehr  | 
| Microsoft.SignalRService/SignalR | MessageCount |  Nachrichtenanzahl  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  Eingehender Datenverkehr  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  Anzahl der Verbindungen  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  Durchschnittlicher CPU-Prozentsatz  | 
| Microsoft.Sql/servers | dtu_used |  DTU-Verbrauch  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  DTU-Prozentsatz  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  In-Memory-OLTP-Speicher in Prozent  | 
| Microsoft.Sql/servers/databases | workers_percent |  Worker in Prozent  | 
| Microsoft.Sql/servers/databases | sessions_percent |  Sitzungen in Prozent  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  E/A-Prozentsatz für Daten  | 
| Microsoft.Sql/servers/databases | log_write_percent |  E/A-Prozentsatz für Protokoll  | 
| Microsoft.Sql/servers/databases | dwu_used |  DWU-Verbrauch  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  DWU in Prozent  | 
| Microsoft.Sql/servers/databases | dtu_used |  DTU-Verbrauch  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  DTU-Prozentsatz  | 
| Microsoft.Sql/servers/databases | deadlock |  Deadlocks  | 
| Microsoft.Sql/servers/databases | cpu_used |  Verwendete CPU  | 
| Microsoft.Sql/servers/databases | cpu_percent |  CPU-Prozentsatz  | 
| Microsoft.Sql/servers/databases | connection_successful |  Erfolgreiche Verbindungen  | 
| Microsoft.Sql/servers/databases | connection_failed |  Verbindungsfehler  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  Prozentsatz der Cachetreffer  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  Von der Firewall blockiert  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  In-Memory-OLTP-Speicher in Prozent  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  Worker in Prozent  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  Sitzungen in Prozent  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  E/A-Prozentsatz für Daten  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  E/A-Prozentsatz für Protokoll  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  eDTU-Verbrauch  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  DTU-Prozentsatz  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  CPU-Prozentsatz  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Gesamtanzahl an Front-Ends  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Kleine Worker im App Service-Plan  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Requests |  Requests  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Arbeitsspeicherprozentsatz  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Mittlere Worker im App Service-Plan  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Große Worker im App Service-Plan  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Länge der HTTP-Warteschlange  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  HTTP-Serverfehler  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  HTTP 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  HTTP 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  HTTP 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  HTTP 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  HTTP 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  HTTP 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  HTTP 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  HTTP 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Warteschlangenlänge des Datenträgers  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU-Prozentsatz  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  Datenausgabe  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  Eingehende Daten  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Durchschnittliche Antwortzeit  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  Aktive Anforderungen  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  Verwendete Worker  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  Gesamtanzahl an Workern  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  Verfügbare Worker  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  Arbeitsspeicherprozentsatz  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  CPU-Prozentsatz  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  TCP-Wartezeit  | 
| Microsoft.Web/serverfarms | TcpSynSent |  TCP-Synchronisierung gesendet  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  TCP-Synchronisierung empfangen  | 
| Microsoft.Web/serverfarms | TcpLastAck |  Letzte TCP-Bestätigung  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  Warten 2 auf Beendigung der TCP-Verbindung  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  Warten 1 auf Beendigung der TCP-Verbindung  | 
| Microsoft.Web/serverfarms | TcpEstablished |  TCP-Verbindung hergestellt  | 
| Microsoft.Web/serverfarms | TcpClosing |  TCP-Verbindung wird geschlossen  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  Warten auf Schließen der TCP-Verbindung  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  Arbeitsspeicherprozentsatz  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Länge der HTTP-Warteschlange  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  Warteschlangenlänge des Datenträgers  | 
| Microsoft.Web/serverfarms | CpuPercentage |  CPU-Prozentsatz  | 
| Microsoft.Web/serverfarms | BytesSent |  Datenausgabe  | 
| Microsoft.Web/serverfarms | BytesReceived |  Eingehende Daten  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  Entladene App-Domänen insgesamt  | 
| Microsoft.Web/sites | TotalAppDomains |  App-Domänen insgesamt  | 
| Microsoft.Web/sites | Threads |  Threadanzahl  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  Anforderungen in der Anwendungswarteschlange  | 
| Microsoft.Web/sites | Requests |  Requests  | 
| Microsoft.Web/sites | PrivateBytes |  Private Bytes  | 
| Microsoft.Web/sites | MemoryWorkingSet |  Arbeitssatz für Arbeitsspeicher  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  E/A: Schreibvorgänge pro Sekunde  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  E/A: Geschriebene Bytes pro Sekunde  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  E/A: Lesevorgänge pro Sekunde  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  E/A: Gelesene Bytes pro Sekunde  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  E/A: Andere Vorgänge pro Sekunde  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  E/A: Andere Bytes pro Sekunde  | 
| Microsoft.Web/sites | HttpResponseTime |  Antwortzeit  | 
| Microsoft.Web/sites | Http5xx |  HTTP-Serverfehler  | 
| Microsoft.Web/sites | Http4xx |  HTTP 4xx  | 
| Microsoft.Web/sites | Http406 |  HTTP 406  | 
| Microsoft.Web/sites | Http404 |  HTTP 404  | 
| Microsoft.Web/sites | Http403 |  HTTP 403  | 
| Microsoft.Web/sites | Http401 |  HTTP 401  | 
| Microsoft.Web/sites | Http3xx |  HTTP 3xx  | 
| Microsoft.Web/sites | Http2xx |  HTTP 2xx  | 
| Microsoft.Web/sites | Http101 |  HTTP 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  Status der Integritätsüberprüfung  | 
| Microsoft.Web/sites | Ziehpunkte |  Anzahl von Handles  | 
| Microsoft.Web/sites | Gen2Collections |  Garbage Collections der Generation 2  | 
| Microsoft.Web/sites | Gen1Collections |  Garbage Collections der Generation 1  | 
| Microsoft.Web/sites | Gen0Collections |  Garbage Collections der Generation 0  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  Ausführungseinheiten für Funktion  | 
| Microsoft.Web/sites | FunctionExecutionCount |  Ausführungsanzahl für Funktion  | 
| Microsoft.Web/sites | CurrentAssemblies |  Aktuelle Assemblys  | 
| Microsoft.Web/sites | CpuTime |  CPU-Zeit  | 
| Microsoft.Web/sites | BytesSent |  Datenausgabe  | 
| Microsoft.Web/sites | BytesReceived |  Eingehende Daten  | 
| Microsoft.Web/sites | AverageResponseTime |  Durchschnittliche Antwortzeit  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  Durchschnittlicher Arbeitssatz für Arbeitsspeicher  | 
| Microsoft.Web/sites | AppConnections |  Verbindungen  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  Entladene App-Domänen insgesamt  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  App-Domänen insgesamt  | 
| Microsoft.Web/sites/slots | Threads |  Threadanzahl  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  Anforderungen in der Anwendungswarteschlange  | 
| Microsoft.Web/sites/slots | Requests |  Requests  | 
| Microsoft.Web/sites/slots | PrivateBytes |  Private Bytes  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  Arbeitssatz für Arbeitsspeicher  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  E/A: Schreibvorgänge pro Sekunde  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  E/A: Geschriebene Bytes pro Sekunde  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  E/A: Lesevorgänge pro Sekunde  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  E/A: Gelesene Bytes pro Sekunde  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  E/A: Andere Vorgänge pro Sekunde  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  E/A: Andere Bytes pro Sekunde  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  Antwortzeit  | 
| Microsoft.Web/sites/slots | Http5xx |  HTTP-Serverfehler  | 
| Microsoft.Web/sites/slots | Http4xx |  HTTP 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  HTTP 406  | 
| Microsoft.Web/sites/slots | Http404 |  HTTP 404  | 
| Microsoft.Web/sites/slots | Http403 |  HTTP 403  | 
| Microsoft.Web/sites/slots | Http401 |  HTTP 401  | 
| Microsoft.Web/sites/slots | Http3xx |  HTTP 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  HTTP 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  HTTP 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  Status der Integritätsüberprüfung  | 
| Microsoft.Web/sites/slots | Ziehpunkte |  Anzahl von Handles  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Garbage Collections der Generation 2  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Garbage Collections der Generation 1  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Garbage Collections der Generation 0  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  Ausführungseinheiten für Funktion  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  Ausführungsanzahl für Funktion  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  Aktuelle Assemblys  | 
| Microsoft.Web/sites/slots | CpuTime |  CPU-Zeit  | 
| Microsoft.Web/sites/slots | BytesSent |  Datenausgabe  | 
| Microsoft.Web/sites/slots | BytesReceived |  Eingehende Daten  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  Durchschnittliche Antwortzeit  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  Durchschnittlicher Arbeitssatz für Arbeitsspeicher  | 
| Microsoft.Web/sites/slots | AppConnections |  Verbindungen  | 
| Microsoft.Sql/servers/databases | cpu_percent | CPU-Prozentsatz | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | E/A-Prozentsatz für Daten | 
| Microsoft.Sql/servers/databases | log_write_percent | E/A-Prozentsatz für Protokoll | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU-Prozentsatz | 
| Microsoft.Sql/servers/databases | connection_successful | Erfolgreiche Verbindungen | 
| Microsoft.Sql/servers/databases | connection_failed | Verbindungsfehler | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | Von der Firewall blockiert | 
| Microsoft.Sql/servers/databases | deadlock | Deadlocks | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | In-Memory-OLTP-Speicher in Prozent | 
| Microsoft.Sql/servers/databases | workers_percent | Worker in Prozent | 
| Microsoft.Sql/servers/databases | sessions_percent | Sitzungen in Prozent | 
| Microsoft.Sql/servers/databases | dtu_used | DTU-Verbrauch | 
| Microsoft.Sql/servers/databases | cpu_used | Verwendete CPU | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU in Prozent | 
| Microsoft.Sql/servers/databases | dwu_used | DWU-Verbrauch | 
| Microsoft.Sql/servers/databases | cache_hit_percent | Prozentsatz der Cachetreffer | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | Zuordnung von Arbeitsauslastungsgruppen nach Systemprozentsatz | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | Zuordnung von Arbeitsauslastungsgruppen nach maximalem Ressourcenprozentsatz | 
| Microsoft.Sql/servers/databases | wlg_active_queries | Aktive Abfragen von Arbeitsauslastungsgruppen | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | In der Warteschlange befindliche Abfragen der Arbeitsauslastungsgruppe | 
| Microsoft.Sql/servers/databases | active_queries | Aktive Abfragen | 
| Microsoft.Sql/servers/databases | queued_queries | Abfragen in Warteschlange | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | Abfragetimeouts für Arbeitsauslastungsgruppen | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | Workload group queued query timeouts (Abfragetimeouts für Arbeitsauslastungsgruppen in der Warteschlange) | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | Effektive Mindestanzahl von Ressourcen (Prozent) | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | Effektives Ressourcenlimit (Prozent) | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | CPU-Prozentsatz | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | E/A-Prozentsatz für Daten | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | E/A-Prozentsatz für Protokoll | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU-Prozentsatz | 
| Microsoft.Sql/servers/elasticPools | workers_percent | Worker in Prozent | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | Sitzungen in Prozent | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU-Verbrauch | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | In-Memory-OLTP-Speicher in Prozent | 
| Microsoft.Sql/servers | dtu_consumption_percent | DTU-Prozentsatz | 
| Microsoft.Sql/servers | dtu_used | DTU-Verbrauch | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | Durchschnittlicher CPU-Prozentsatz |