---
title: 'Analyse von Wissensdatenbanken: Benutzerdefinierte Fragen und Antworten'
titleSuffix: Azure Cognitive Services
description: „Benutzerdefinierte Fragen und Antworten“ speichert die Telemetriedaten und Chatprotokolle mithilfe der Azure-Diagnoseprotokollierung.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: fba1481ffe7aa45514e53813ccf7b14ea01bd6bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095648"
---
# <a name="get-analytics-for-your-knowledge-base"></a>Abrufen von Analysen zu Ihrer Wissensdatenbank

„Benutzerdefinierte Fragen und Antworten“ speichert die Telemetriedaten und Chatprotokolle mithilfe der Azure-Diagnoseprotokollierung. Führen Sie die folgenden Schritte aus, um Beispielabfragen auszuführen, um Analysen zur Verwendung ihres Projekts vom Typ „Benutzerdefinierte Fragen und Antworten“ zu erhalten.

1. [Aktivieren Sie die Diagnoseprotokollierung](../../../diagnostic-logging.md) für Ihre Sprachressource mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“.

2. Wählen Sie im vorherigen Schritt zusätzlich zu **Audit, RequestResponse und AllMetrics** noch **Trace** für die Protokollierung aus.

    ![Aktivieren der Ablaufverfolgungsprotokollierung in „Benutzerdefinierte Fragen und Antworten“](../media/analytics/qnamaker-v2-enable-trace-logging.png)

## <a name="kusto-queries"></a>Kusto-Abfragen

### <a name="chat-log"></a>Chatprotokoll

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>Datenverkehrszähler pro Wissensdatenbank und Benutzer in einem bestimmten Zeitraum

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>Wartezeit der GenerateAnswer-API

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>Durchschnittliche Wartezeit aller Vorgänge

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>Unbeantwortete Fragen

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Auswählen der Kapazität](../../../qnamaker/how-to/improve-knowledge-base.md)
