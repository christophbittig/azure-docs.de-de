---
title: Azure Communication Services - Vorschau auf Log Analytics
titleSuffix: An Azure Communication Services concept document
description: Über die Verwendung von Log a Analytics für Anrufzusammenfassungs- und Anrufdiagnoseprotokolle
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 05ae40fc974228bf93641be3786294aab59c243a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138761"
---
# <a name="log-analytics-for-communications-services-preview"></a>Log-Analysen für die Vorschau der Kommunikationsdienste

## <a name="overview-and-access"></a>Übersicht und Zugriff

Bevor Sie die Vorteile von [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) für Ihre Kommunikationsdienstprotokolle nutzen können, müssen Sie zunächst die unter [Protokollierung in den Diagnoseeinstellungen aktivieren](enable-logging.md) beschriebenen Schritte durchführen. Sobald Sie Ihre Protokolle und einen [Log-Analytics-Arbeitsbereich](/azure/azure-monitor/logs/design-logs-deployment) aktiviert haben, haben Sie Zugang zu vielen hilfreichen [Standardabfragepaketen](/azure/azure-monitor/logs/query-packs#default-query-pack), die Ihnen helfen, die in Ihren Protokollen verfügbaren Daten schnell zu visualisieren und zu verstehen, die im Folgenden beschrieben werden. Über Log Analytics erhalten Sie auch Zugriff auf weitere Communications Services Insights über Azure Monitor Workbooks (siehe: [Communications Services Insights](insights.md)), die Möglichkeit, eigene Abfragen und Workbooks zu erstellen, [REST API-Zugang](https://dev.loganalytics.io/) zu jeder Abfrage.

### <a name="access"></a>Access
Sie können auf die Abfragen zugreifen, indem Sie auf Ihrer Ressourcenseite für Kommunikationsdienste beginnen und dann in der linken Navigation im Abschnitt Monitor auf "Logs" klicken:

:::image type="content" source="media\log-analytics\access-log-analytics.png" alt-text="Log Analytics Navigation":::

Von dort aus wird ein modaler Bildschirm angezeigt, der alle [Standardabfragepakete](/azure/azure-monitor/logs/query-packs#default-query-pack) enthält, die für Ihre Kommunikationsdienste verfügbar sind, mit einer Liste von Abfragepaketen zur Navigation auf der linken Seite.

:::image type="content" source="media\log-analytics\log-analytics-modal-resource.png" alt-text="log analytics abfragen modal" lightbox="media\log-analytics\log-analytics-modal-resource.png":::

Wenn Sie den modalen Bildschirm schließen, können Sie weiterhin zu den verschiedenen Abfragepaketen navigieren und direkt auf Daten in Form von Tabellen zugreifen, die auf dem Schema der Protokolle und Metriken basieren, die Sie in Ihren Diagnoseeinstellungen aktiviert haben. Hier können Sie mit [KQL (Kusto)](/azure/data-explorer/kusto/query/) Ihre eigenen Abfragen aus den Daten erstellen. Erfahren Sie mehr über die Verwendung, Bearbeitung und Erstellung von Abfragen, indem Sie mehr darüber lesen: [Log Analytics-Abfragen](/azure/azure-monitor/logs/queries)

:::image type="content" source="media\log-analytics\log-analytics-queries-resource.png" alt-text="Log Analytics-Anfragen in Ressource" lightbox="media\log-analytics\log-analytics-queries-resource.png":::

:::image type="content" source="media\log-analytics\log-analytics-tables-resource.png" alt-text="Log Analytics-Tabellen in Ressource" lightbox="media\log-analytics\log-analytics-tables-resource.png":::

## <a name="default-query-packs-for-call-summary-and-call-diagnostic-logs"></a>Standardabfragepakete für Anrufzusammenfassung und Anrufdiagnoseprotokolle
Nachfolgend finden Sie Beschreibungen der einzelnen Abfragen im [Standardabfragepaket](/azure/azure-monitor/logs/query-packs#default-query-pack), für die [Aufrufzusammenfassung und die Anrufdiagnoseprotokolle](call-logs-azure-monitor.md), einschließlich Codebeispielen und Beispielausgaben für jede verfügbare Abfrage:
### <a name="call-overview-queries"></a>Übersichtsabfragen aufrufen
#### <a name="number-of-participants-per-call"></a>Anzahl der Teilnehmer pro Anruf

```
// Count number of calls and participants,
// and print average participants per call
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId
| summarize num_participants=count(), num_calls=dcount(CorrelationId)
| extend avg_participants = todecimal(num_participants) / todecimal(num_calls)
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\call-overview-sample.png" alt-text="Aufruf der Übersichtsabfrage":::

#### <a name="number-of-participants-per-group-call"></a>Anzahl der Teilnehmer pro Gruppengespräch

```
// Count number of participants per group call
ACSCallSummary
| where CallType == 'Group'
| distinct CorrelationId, ParticipantId
| summarize num_participants=count() by CorrelationId
| summarize participant_counts=count() by num_participants
| order by num_participants asc 
| render columnchart with  (xcolumn = num_participants, title="Number of participants per group call")
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\participants-group-sample.png" alt-text="Teilnehmer pro Gruppenrufabfrage":::

#### <a name="ratio-of-call-types"></a>Verhältnis der Anrufarten

```
// Ratio of call types
ACSCallSummary
| summarize call_types=dcount(CorrelationId) by CallType
| render piechart title="Call Type Ratio"

```

Beispielausgabe:

:::image type="content" source="media\log-analytics\ratio-call-types-sample.png" alt-text="Anteil der Abrufart Abfrage":::

#### <a name="call-duration-distribution"></a>Verteilung der Anrufdauer

```
// Call duration histogram
ACSCallSummary
| distinct CorrelationId, CallDuration
|summarize duration_counts=count() by CallDuration
| order by CallDuration asc
| render columnchart with (xcolumn = CallDuration, title="Call duration histogram")
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\call-duration-sample.png" alt-text="Abfrage der Gesprächsdauer":::

#### <a name="call-duration-percentiles"></a>Perzentile der Anrufdauer

```
// Call duration percentiles
ACSCallSummary
| distinct CorrelationId, CallDuration
| summarize avg(CallDuration), percentiles(CallDuration, 50, 90, 99)
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\call-duration-percentile-example.png" alt-text="Anrufdauer Perzentilabfrage":::

### <a name="endpoint-information-queries"></a>Abfragen von Endpunktinformationen

#### <a name="number-of-endpoints-per-call"></a>Anzahl der Endpunkte pro Anruf

```
// Count number of calls and endpoints,
// and print average endpoints per call
ACSCallSummary
| distinct CorrelationId, EndpointId
| summarize num_endpoints=count(), num_calls=dcount(CorrelationId)
| extend avg_endpoints = todecimal(num_endpoints) / todecimal(num_calls)
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\endpoints-per-call.png" alt-text="Endpunkte pro Anruf Abfrage":::

#### <a name="ratio-of-sdk-versions"></a>Verhältnis der SDK-Versionen

```
// Ratio of SDK Versions
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, SdkVersion
| summarize sdk_counts=count() by SdkVersion
| order by SdkVersion asc
| render piechart title="SDK Version Ratio"
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\sdk-ratio-sample.png" alt-text="Kuchendiagramm zur Darstellung des Verhältnisses der SDK-Versionen."::: :::image type="content" source="media\log-analytics\sdk-ratio-table-sample.png" alt-text="Tabelle mit SDK-Versionen":::

#### <a name="ratio-of-os-versions-simplified-os-name"></a>Verhältnis der OS-Versionen (vereinfachter OS-Name)

```
// Ratio of OS Versions (simplified OS name)
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, OsVersion
| extend simple_os = case(  indexof(OsVersion, "Android") != -1, tostring(split(OsVersion, ";")[0]),
                            indexof(OsVersion, "Darwin") != -1, tostring(split(OsVersion, ":")[0]),
                            indexof(OsVersion, "Windows") != -1, tostring(split(OsVersion, ".")[0]),
                            OsVersion
                        )
| summarize os_counts=count() by simple_os
| order by simple_os asc
| render piechart title="OS Version Ratio"
```

Beispielausgabe:

:::image type="content" source="media\log-analytics\os-version-graph.png" alt-text="Kuchendiagramm mit den Verhältnissen der Betriebssysteme":::
:::image type="content" source="media\log-analytics\os-version-table.png" alt-text="Tabelle mit Betriebssystemversionen":::

### <a name="media-stream-queries"></a>Medienstrom-Abfragen
#### <a name="streams-per-call"></a>Ströme pro Anruf

```
// Count number of calls and streams,
// and print average streams per call
ACSCallDiagnostics
| summarize num_streams=count(), num_calls=dcount(CorrelationId)
| extend avg_streams = todecimal(num_streams) / todecimal(num_calls)
```
Beispielausgabe:

:::image type="content" source="media\log-analytics\streams-call-output.png" alt-text="Streams pro Anruf Abfrage":::

#### <a name="streams-per-call-histogram"></a>Histogramm der Datenströme pro Anruf

```
// Distribution of streams per call
ACSCallDiagnostics
| summarize streams_per_call=count() by CorrelationId
| summarize stream_counts=count() by streams_per_call
| order by streams_per_call asc
| render columnchart title="Streams per call histogram"
```

:::image type="content" source="media\log-analytics\streams-call-histogram.png" alt-text="Histogramm der Datenströme pro Anruf":::

#### <a name="ratio-of-media-types"></a>Verhältnis der Medientypen

```
// Ratio of media types by call
ACSCallDiagnostics
| summarize media_types=count() by MediaType
| render piechart title="Media Type Ratio"
```

:::image type="content" source="media\log-analytics\media-type-ratio.png" alt-text="Kuchendiagramm mit Verhältnissen der Medientypen":::

### <a name="quality-metrics-queries"></a>Abfragen von Qualitätsmetriken

#### <a name="average-telemetry-values"></a>Durchschnittliche Telemetriewerte

```
// Average telemetry values over all streams
ACSCallDiagnostics
| summarize Avg_JitterAvg=avg(JitterAvg),
            Avg_JitterMax=avg(JitterMax),
            Avg_RoundTripTimeAvg=avg(RoundTripTimeAvg),
            Avg_RoundTripTimeMax=avg(RoundTripTimeMax),
            Avg_PacketLossRateAvg=avg(PacketLossRateAvg),
            Avg_PacketLossRateMax=avg(PacketLossRateMax)
```

:::image type="content" source="media\log-analytics\avg-telemetry-values.png" alt-text="Durchschnittliche Telemetriewerte":::

#### <a name="jitteravg-histogram"></a>JitterAvg-Histogramm

```
// Jitter Average Histogram
ACSCallDiagnostics
| where isnotnull(JitterAvg)
| summarize JitterAvg_counts=count() by JitterAvg
| order by JitterAvg asc
| render columnchart with (xcolumn = JitterAvg, title="JitterAvg histogram")
```

:::image type="content" source="media\log-analytics\jitteravg-histogram.png" alt-text="Jitter-Durchschnittshistogramm":::

#### <a name="jittermax-histogram"></a>JitterMax-Histogramm

```
// Jitter Max Histogram
ACSCallDiagnostics
| where isnotnull(JitterMax)
|summarize JitterMax_counts=count() by JitterMax
| order by JitterMax asc
| render columnchart with (xcolumn = JitterMax, title="JitterMax histogram")
```

:::image type="content" source="media\log-analytics\jittermax-histogram.png" alt-text="jitter max histogram":::

#### <a name="packetlossrateavg-histogram"></a>Histogramm PacketLossRateAvg
```
// PacketLossRate Average Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateAvg)
|summarize PacketLossRateAvg_counts=count() by bin(PacketLossRateAvg, 0.01)
| order by PacketLossRateAvg asc
| render columnchart with (xcolumn = PacketLossRateAvg, title="PacketLossRateAvg histogram")
```

:::image type="content" source="media\log-analytics\packetloss-avg-histogram.png" alt-text="Histogramm der durchschnittlichen Paketverluste":::

#### <a name="packetlossratemax-histogram"></a>Histogramm PacketLossRateMax
```
// PacketLossRate Max Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateMax)
|summarize PacketLossRateMax_counts=count() by bin(PacketLossRateMax, 0.01)
| order by PacketLossRateMax asc
| render columnchart with (xcolumn = PacketLossRateMax, title="PacketLossRateMax histogram")
```

:::image type="content" source="media\log-analytics\packetloss-max-histogram.png" alt-text="Paketverlust-Max-Histogramm":::

#### <a name="roundtriptimeavg-histogram"></a>RoundTripTimeAvg-Histogramm
```
// RoundTripTime Average Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeAvg)
|summarize RoundTripTimeAvg_counts=count() by RoundTripTimeAvg
| order by RoundTripTimeAvg asc
| render columnchart with (xcolumn = RoundTripTimeAvg, title="RoundTripTimeAvg histogram")
```

:::image type="content" source="media\log-analytics\rtt-avg-histogram.png" alt-text="RTT-Durchschnittshistogramm":::

#### <a name="roundtriptimemax-histogram"></a>Histogramm RoundTripTimeMax
```
// RoundTripTime Max Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeMax)
|summarize RoundTripTimeMax_counts=count() by RoundTripTimeMax
| order by RoundTripTimeMax asc
| render columnchart with (xcolumn = RoundTripTimeMax, title="RoundTripTimeMax histogram")
```

:::image type="content" source="media\log-analytics\rtt-max-histogram.png" alt-text="RTT max histogram":::

#### <a name="poor-jitter-quality"></a>Schlechte Jitter-Qualität
```
// Get proportion of calls with poor quality jitter
// (defined as jitter being higher than 30ms)
ACSCallDiagnostics
| extend JitterQuality = iff(JitterAvg > 30, "Poor", "Good")
| summarize count() by JitterQuality
| render piechart title="Jitter Quality"
```

:::image type="content" source="media\log-analytics\jitter-quality.png" alt-text="Jitter-Qualität":::


#### <a name="packetlossrate-quality"></a>PacketLossRate Qualität
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\packet-loss-quality.png" alt-text="Paketverlustrate Qualität":::

#### <a name="roundtriptime-quality"></a>RoundTripTime-Qualität
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\rtt-quality.png" alt-text="RTT-Qualität":::

### <a name="parameterizable-queries"></a>Parametrisierbare Abfragen

#### <a name="daily-calls-in-the-last-week"></a>Tägliche Anrufe in der letzten Woche
```
// Histogram of daily calls over the last week
ACSCallSummary
| where CallStartTime > now() - 7d
| distinct CorrelationId, CallStartTime
| extend hour  = floor(CallStartTime, 1d)
| summarize event_count=count() by day
| sort by day asc
| render columnchart title="Number of calls in last week"
```

:::image type="content" source="media\log-analytics\calls-per-week.png" alt-text="Tägliche Anrufe letzte Woche":::

#### <a name="calls-per-hour-in-last-day"></a>Anrufe pro Stunde am letzten Tag
```
// Histogram of calls per hour in the last day
ACSCallSummary
| where CallStartTime > now() - 1d
| distinct CorrelationId, CallStartTime
| extend hour = floor(CallStartTime, 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render columnchart title="Number of calls per hour in last day"
```

:::image type="content" source="media\log-analytics\calls-per-hour.png" alt-text="Anrufe pro Stunde am letzten Tag":::