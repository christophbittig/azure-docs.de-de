---
title: Referenz zum Überwachen von Azure Event Hubs-Daten
description: Wichtige Referenzmaterialien für die Überwachung von Azure Event Hubs.
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 06/11/2021
ms.openlocfilehash: 4dc40cef074a223a2cc34e3a87c9acae840f8dbf
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992043"
---
# <a name="monitoring-azure-event-hubs-data-reference"></a>Referenz zum Überwachen von Azure Event Hubs-Daten
Ausführliche Informationen zum Erfassen und Analysieren von Überwachungsdaten für Azure Event Hubs finden Sie unter [Überwachen von Azure Event Hubs](monitor-event-hubs.md).

## <a name="metrics"></a>Metriken
In diesem Abschnitt werden alle automatisch erfassten Plattformmetriken aufgeführt, die für Azure Event Hubs erfasst werden. Der Ressourcenanbieter für diese Metriken ist **Microsoft.EventHub/clusters** oder **Microsoft.EventHub/clusters.**

### <a name="request-metrics"></a>Anforderungsmetriken
Zählt die Anzahl der Anforderungen von Daten und Verwaltungsvorgängen

| Metrikname |  Über Diagnoseeinstellungen exportierbar | Einheit | Aggregationstyp |  BESCHREIBUNG | Dimensionen | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| Eingehende Anforderungen| Ja | Anzahl | Gesamt | Die Anzahl von Anforderungen an den Event Hubs-Dienst in einem bestimmten Zeitraum. | Name der Entität| 
| Erfolgreiche Anforderungen| Nein | Anzahl | Gesamt | Die Anzahl erfolgreicher Anforderungen an den Event Hubs-Dienst in einem bestimmten Zeitraum. |  Name der Entität<br/><br/>Ergebnis des Vorgangs | 
| Gedrosselte Anforderungen| Nein | Anzahl | Gesamt |  Die Anzahl der Anforderungen, die aufgrund eines überschrittenen Verbrauchs gedrosselt wurden | Name der Entität<br/><br/>Ergebnis des Vorgangs |

Die beiden folgenden Arten von Fehlern werden als **Benutzerfehler** klassifiziert:

1. Clientseitige Fehler (in HTTP: Fehler vom Typ 400)
2. Fehler, die bei der Verarbeitung von Nachrichten auftreten.


### <a name="message-metrics"></a>Nachrichtenmetriken
| Metrikname |  Über Diagnoseeinstellungen exportierbar | Einheit | Aggregationstyp |  BESCHREIBUNG | Dimensionen | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|Eingehende Nachrichten|  Ja | Anzahl | Gesamt | Die Anzahl von Ereignissen oder Nachrichten, die in einem bestimmten Zeitraum an Event Hubs gesendet wurden | Name der Entität|
|Ausgehende Nachrichten| Ja | Anzahl | Gesamt | Die Anzahl von Ereignissen oder Nachrichten, die in einem bestimmten Zeitraum von Event Hubs empfangen wurden. | Name der Entität | 
| Erfasste Nachrichten| Nein | Anzahl| Gesamt | Die Anzahl der erfassten Nachrichten.  |  Name der Entität | 
|Eingehende Bytes | Ja |  Byte | Gesamt | Eingehende Bytes für einen Event Hub in einem bestimmten Zeitraum.  | Name der Entität| 
|Ausgehende Bytes | Ja |  Byte | Gesamt |Ausgehende Bytes für einen Event Hub in einem angegebenen Zeitraum.  | Name der Entität | 
| Size | Nein |  Byte | Average |  Größe eines Event Hub in Bytes.|Name der Entität |


> [!NOTE]
> Bei diesen Werten handelt es sich um Zeitpunktwerte. Eingehende Nachrichten, die unmittelbar nach diesem Zeitpunkt verarbeitet wurden, werden in diesen Metriken möglicherweise nicht berücksichtigt. 

### <a name="capture-metrics"></a>Erfassen von Metriken
| Metrikname |  Über Diagnoseeinstellungen exportierbar | Einheit | Aggregationstyp |  BESCHREIBUNG | Dimensionen | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| Erfasste Nachrichten| Nein | Anzahl| Gesamt | Die Anzahl der erfassten Nachrichten.  | Name der Entität |
| Erfasste Bytes | Nein | Byte | Gesamt | Erfasste Bytes für einen Event Hub | Name der Entität | 
| Backlog erfassen | Nein | Anzahl| Gesamt | Erfassen des Backlogs für einen Event Hub | Name der Entität | 


### <a name="connection-metrics"></a>Verbindungsmetriken
| Metrikname |  Über Diagnoseeinstellungen exportierbar | Einheit | Aggregationstyp |  BESCHREIBUNG | Dimensionen | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Die aktiven Verbindungen.| Nein | Anzahl | Average | Die Anzahl der aktiven Verbindungen für einen Namespace sowie für eine Entität (Event Hub) im Namespace. Der Wert für diese Metrik ist ein Zeitpunktwert. Verbindungen, die unmittelbar nach diesem Zeitpunkt aktiv waren, werden in der Metrik möglicherweise nicht berücksichtigt.| Name der Entität | 
|Geöffnete Verbindungen | Nein | Anzahl | Average |  Die Anzahl der geöffneten Verbindungen | Name der Entität | 
|Geschlossene Verbindungen | Nein | Anzahl | Average|  Die Anzahl der geschlossenen Verbindungen | Name der Entität | 

### <a name="error-metrics"></a>Fehlermetriken
| Metrikname |  Über Diagnoseeinstellungen exportierbar | Einheit | Aggregationstyp |  BESCHREIBUNG | Dimensionen |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|Serverfehler| Nein | Anzahl | Gesamt | Die Anzahl der aufgrund eines Fehlers nicht verarbeiteten Anforderungen an den Event Hubs-Dienst in einem bestimmten Zeitraum. | Name der Entität<br/><br/>Ergebnis des Vorgangs |
|Benutzerfehler | Nein | Anzahl | Gesamt | Die Anzahl der aufgrund von Benutzerfehlern nicht verarbeiteten Anforderungen in einem bestimmten Zeitraum | Name der Entität<br/><br/>Ergebnis des Vorgangs|
|Fehler aufgrund von Kontingentüberschreitung | Nein |Anzahl | Gesamt | Die Anzahl der Fehler, die durch das Überschreiten von Kontingenten in einem bestimmten Zeitraum verursacht werden. | Name der Entität<br/><br/>Ergebnis des Vorgangs|

> [!NOTE]
> Von Logic Apps werden Empfänger mit epoch-Wert erstellt, und Empfänger können je nach Dienstauslastung zwischen Knoten verschoben werden. Während dieser Verschiebungen können Ausnahmen vom Typ `ReceiverDisconnection` auftreten. Sie werden aufseiten des Event Hubs-Diensts als Benutzerfehler gezählt. Von Logic Apps werden unter Umständen Fehler von Event Hubs-Clients erfasst, sodass Sie sie sich in Benutzerprotokollen ansehen können.

## <a name="metric-dimensions"></a>Metrikdimensionen

Azure Event Hubs unterstützt folgende Dimensionen für Metriken in Azure Monitor. Das Hinzufügen von Dimensionen zu Ihren Metriken ist optional. Wenn Sie keine Dimensionen hinzufügen, werden Metriken auf Namespaceebene angegeben. 

|Dimensionsname|BESCHREIBUNG|
| ------------------- | ----------------- |
|Entitätsname| Name des Event Hubs.|

## <a name="resource-logs"></a>Ressourcenprotokolle
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]



## <a name="azure-monitor-logs-tables"></a>Tabellen in Azure Monitor-Protokollen
Azure Event Hubs verwendet Kusto-Tabellen aus Azure Monitor-Protokollen. Sie können diese Tabellen mit Log Analytics abfragen. Eine Liste der Kusto-Tabellen, die der Dienst verwendet, finden Sie unter [Referenz zur Azure Monitor-Protokolltabelle](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs).


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Überwachen von Azure Event Hubs finden Sie unter [Überwachen von Azure Event Hubs](monitor-event-hubs.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
