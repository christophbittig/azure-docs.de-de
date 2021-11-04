---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: aceaeb74b89a8c0eaa30728a2c456b367d64cf94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478774"
---
### <a name="schema-mapping-examples"></a>Beispiele für die Schemazuordnung

**Beispiel für die Tabellenschemazuordnung**

Wenn Ihre Daten drei Spalten (`Timespan`, `Metric` und `Value`) enthalten und die Eigenschaften, die Sie einschließen, `x-opt-enqueued-time` und `x-opt-offset` lauten, erstellen oder ändern Sie das Tabellenschema mit dem folgenden Befehl:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Beispiel für eine CSV-Zuordnung**

Führen Sie die folgenden Befehle aus, um am Anfang des Datensatzes Daten hinzuzufügen. Beachten Sie die Ordinalwerte.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Beispiel für eine JSON-Zuordnung**

Daten werden mithilfe der Systemeigenschaftenzuordnung hinzugefügt. Führen Sie diese Befehle aus:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
