---
title: Erfassen von Protokolldaten
titleSuffix: Azure Cognitive Search
description: Erfassen und analysieren Sie Protokolldaten, indem Sie eine Diagnoseeinstellung aktivieren, und verwenden Sie dann die Kusto Query Language, um die Ergebnisse zu untersuchen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5643f9a8bb1caec8d264ba5dfba9913d5d13b2ec
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414843"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Sammeln und Analysieren von Protokolldaten für Azure Cognitive Search

Diagnose- oder Betriebsprotokolle bieten Einblicke in die detaillierten Vorgänge von Azure Cognitive Search und sind für die Überwachung des Status und der Prozesse des Diensts nützlich. Intern speichert Microsoft Systeminformationen für einen kurzen Zeitraum (etwa 30 Tage) im Back-End, was für den Fall, dass Sie ein Supportticket erstellen, für die Untersuchung und Analyse ausreicht. Wenn Sie jedoch die Besitzrechte an den Betriebsdaten wünschen, sollten Sie eine Diagnoseeinstellung konfigurieren, um anzugeben, wo die Protokollinformationen gesammelt werden.

Die Diagnoseprotokollierung wird durch Back-End-Integration in [Azure Monitor](../azure-monitor/index.yml) aktiviert. Beim Einrichten der Diagnoseprotokollierung werden Sie zur Angabe einer Speicheroption für das Beibehalten des Protokolls aufgefordert. Die folgende Tabelle enthält eine Auflistung Ihrer Optionen.

| Resource | Syntaxelemente |
|----------|----------|
| [Senden an den Log Analytics-Arbeitsbereich](../azure-monitor/essentials/tutorial-resource-logs.md) | Ereignisse und Metriken werden an einen Log Analytics-Arbeitsbereich gesendet, der im Portal abgefragt werden kann, um ausführliche Informationen zurückzugeben. Eine Einführung finden Sie unter [Erste Schritte mit Azure Monitor-Protokollen](../azure-monitor/logs/log-analytics-tutorial.md). |
| [Archivieren mit Blobspeicher](../storage/blobs/storage-blobs-overview.md) | Ereignisse und Metriken werden in einem Blobcontainer archiviert und in JSON-Dateien gespeichert. Protokolle können sehr präzise sein (nach Stunde/Minute), was für die Untersuchung eines bestimmten Vorfalls nützlich ist, aber nicht für eine Untersuchung mit offenem Ende. Mit einem JSON-Editor können Sie eine unformatierte Protokolldatei oder Power BI anzeigen, um Protokolldaten zu aggregieren und zu visualisieren.|
| [Streamen an Event Hub](../event-hubs/index.yml) | Ereignisse und Metriken werden an einen Azure Event Hubs-Dienst gestreamt. Wählen Sie diese Lösung als alternativen Datensammlungsdienst für sehr große Ereignisprotokolle aus. |

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie Ressourcen vorab, damit Sie beim Konfigurieren der Diagnoseprotokollierung mindestens eine auswählen können.

+ [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/logs/quick-create-workspace.md)

+ [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md)

+ [Erstellen eines Event Hubs](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Aktivieren der Datensammlung

Diagnoseeinstellungen geben an, wie protokollierte Ereignisse und Metriken erfasst werden.

1. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

   ![Diagnoseeinstellungen](./media/search-monitor-usage/diagnostic-settings.png "Diagnoseeinstellungen")

1. Wählen Sie **+ Diagnoseeinstellung hinzufügen**  aus.

1. Aktivieren Sie **Log Analytics**, wählen Sie den Arbeitsbereich und dann **OperationLogs** und **AllMetrics** aus.

   ![Konfigurieren der Datensammlung](./media/search-monitor-usage/configure-storage.png "Konfigurieren der Datensammlung")

1. Speichern Sie die Einstellung.

1. Nach dem Aktivieren der Protokollierung beginnt Ihr Suchdienst mit dem Generieren von Protokollen und Metriken. Es kann eine Weile dauern, bis protokollierte Ereignisse und Metriken verfügbar werden.

Log Analytics: Es dauert mehrere Minuten, bis die Daten verfügbar sind. Anschließend können Sie Kusto-Abfragen ausführen, um Daten zurückzugeben. Weitere Informationen finden Sie unter [Überwachen von Abfrageanforderungen](search-monitor-logs.md).

Blobspeicher: Die Container werden nach einer Stunde im Blobspeicher angezeigt. Es gibt einen Blob pro Stunde pro Container. Container werden nur erstellt, wenn eine Aktivität zum Protokollieren oder Messen vorhanden ist. Beim Kopieren der Daten in ein Speicherkonto werden sie im JSON-Format formatiert und in zwei Containern platziert:

+ „insights-logs-operationlogs“: für Suchdatenverkehrsprotokolle
+ „insights-metrics-pt1m“: für Metriken

## <a name="query-log-information"></a>Abfragen von Protokollinformationen

Zwei Tabellen enthalten Protokolle und Metriken für Azure Cognitive Search: **AzureDiagnostics** und **AzureMetrics**.

1. Wählen Sie unter **Überwachung** die Option **Protokolle** aus.

1. Geben Sie im Abfragefenster **AzureMetrics** ein, überprüfen Sie den Bereich (Ihren Suchdienst) und den Zeitbereich, und klicken Sie dann auf **Ausführen**, um sich mit den in dieser Tabelle gesammelten Daten vertraut zu machen.

   Scrollen Sie in der Tabelle, um Metriken und Werte anzuzeigen. Beachten Sie die Datensatzanzahl oben. Wenn Ihr Dienst eine Weile lang Metriken gesammelt hat, können Sie das Zeitintervall anpassen, um ein verwaltbares Dataset zu erhalten.

   ![AzureMetrics-Tabelle](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics-Tabelle")

1. Geben Sie die folgende Abfrage ein, um ein tabellarisches Resultset zurückzugeben.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Wiederholen Sie die vorherigen Schritte, beginnend mit **AzureDiagnostics**, um alle Spalten zu Informationszwecken zurückzugeben, gefolgt von einer selektiveren Abfrage, mit der interessantere Informationen extrahiert werden.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics-Tabelle](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics-Tabelle")

## <a name="kusto-query-examples"></a>Kusto-Abfragebeispiele

Wenn Sie die Diagnoseprotokollierung aktiviert haben, können Sie **AzureDiagnostics** abfragen, um eine Liste der für Ihren Dienst ausgeführten Vorgänge einschließlich der Ausführungszeit zu erhalten. Sie können Aktivitäten auch korrelieren, um Änderungen an der Leistung zu untersuchen.

#### <a name="example-list-operations"></a>Beispiel: Auflisten von Vorgängen 

So geben Sie eine Liste der Vorgänge und die Anzahl dieser Vorgänge zurück

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Beispiel: Korrelieren von Vorgängen

So korrelieren Sie die Abfrageanforderung mit Indizierungsvorgängen und rendern die Datenpunkte in einem Zeitdiagramm, um die Vorgänge abzugleichen

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(durationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Protokollierte Vorgänge

Von Azure Monitor werden auch Ereignisse im Zusammenhang mit Indizierung und Abfragen protokolliert. In der Tabelle **AzureDiagnostics** in Log Analytics werden operative Daten im Zusammenhang mit Abfragen und Indizierung gesammelt.

| Vorgangsname | BESCHREIBUNG |
|---------------|-------------|
| ServiceStats | Bei diesem Vorgang handelt es sich um einen Routineaufruf zum [Abrufen von Dienststatistiken](/rest/api/searchservice/get-service-statistics), der entweder direkt oder implizit erfolgt, um eine Übersicht im Portal aufzufüllen, wenn diese geladen oder aktualisiert wird. |
| Query.Search |  Abfrageanforderungen für einen Index. Weitere Informationen zu protokollierten Abfragen finden Sie unter [Überwachen von Abfragen](search-monitor-queries.md).|
| Indexing.Index  | Dieser Vorgang dient dem [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](/rest/api/searchservice/addupdate-or-delete-documents). |
| indexes.Prototype | Dies ist ein Index, der vom Datenimport-Assistenten erstellt wird. |
| Indexers.Create | Erstellt explizit oder implizit einen Indexer mithilfe des Datenimport-Assistenten. |
| Indexers.Get | Gibt jeweils den Namen eines Indexers zurück, wenn der Indexer ausgeführt wird. |
| Indexers.Status | Gibt jeweils den Status eines Indexers zurück, wenn der Indexer ausgeführt wird. |
| DataSources.Get | Gibt den Namen der Datenquelle zurück, wenn ein Indexer ausgeführt wird.|
| Indexes.Get | Gibt den Namen eines Indexes zurück, wenn ein Indexer ausgeführt wird. |

## <a name="log-schema"></a>Protokollschema

Wenn Sie benutzerdefinierte Berichte erstellen, entsprechen die Datenstrukturen, die Azure Cognitive Search-Protokolldaten enthalten, dem folgenden Schema. Blobspeicher: Jedes Blob hat ein Stammobjekt mit dem Namen **records**, das ein Array von Protokollobjekten enthält. Jedes Blob enthält Einträge zu allen Vorgängen, die während einer bestimmten Stunde erfolgt sind.

Die folgende Tabelle ist eine Teilliste häufiger Felder für die Ressourcenprotokollierung.

| Name | type | Beispiel | Notizen |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559Z" |Zeitstempel des Vorgangs |
| resourceId |Zeichenfolge |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Ihre Ressourcen-ID |
| operationName |Zeichenfolge |„Query.Search“ |Name des Vorgangs |
| operationVersion |Zeichenfolge |"2020-06-30" |Die verwendete API-Version |
| category |Zeichenfolge |„OperationLogs“ |Konstante |
| resultType |Zeichenfolge |„Success“ |Mögliche Werte: Erfolgreich oder Fehler |
| resultSignature |INT |200 |HTTP-Ergebniscode |
| durationMS |INT |50 |Dauer des Vorgangs in Millisekunden. |
| properties |Objekt (object) |Siehe hierzu die folgende Tabelle. |Objekt, das vorgangsspezifische Daten enthält |

### <a name="properties-schema"></a>Eigenschaftsschema

Die folgenden Eigenschaften gelten speziell für Azure Cognitive Search.

| Name | type | Beispiel | Notizen |
| --- | --- | --- | --- |
| Description_s |Zeichenfolge |„GET-/indexes('content')/docs“ |Endpunkt des Vorgangs |
| Documents_d |INT |42 |Anzahl von verarbeiteten Dokumenten |
| IndexName_s |Zeichenfolge |„test-index“ |Name des Indexes, der dem Vorgang zugeordnet ist |
| Query_s |Zeichenfolge |"?search=AzureSearch&$count=true&api-version=2020-06-30" |Die Abfrageparameter |

## <a name="metrics-schema"></a>Metrikenschema

Metriken werden für Abfrageanforderungen erfasst und in Intervallen von einer Minute gemessen. Jede Metrik macht Mindest-, Höchst- und Durchschnittswerte pro Minute verfügbar. Weitere Informationen finden Sie unter [Überwachen von Abfrageanforderungen](search-monitor-queries.md).

| Name | type | Beispiel | Notizen |
| --- | --- | --- | --- |
| resourceId |Zeichenfolge |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Ihre Ressourcen-ID |
| metricName |Zeichenfolge |„Latency“ |Der Name der Metrik |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Der Zeitstempel des Vorgangs |
| average |INT |64 |Der Durchschnittswert der unformatierten Stichproben im Metrikzeitintervall, Einheiten in Sekunden oder Prozentsatz, abhängig von der Metrik. |
| minimum |INT |37 |Der Mindestwert der unformatierten Beispiele im Metrikzeitintervall, Einheiten in Sekunden. |
| maximum |INT |78 |Der Höchstwert der unformatierten Beispiele im Metrikzeitintervall, Einheiten in Sekunden.  |
| total |INT |258 |Der Gesamtwert der unformatierten Beispiele im Metrikzeitintervall, Einheiten in Sekunden.  |
| count |INT |4 |Anzahl der Metriken, die innerhalb des Ein-Minuten-Intervalls von einem Knoten an das Protokoll ausgegeben werden.  |
| timegrain |Zeichenfolge |„PT1M“ |Das Aggregationsintervall der Metrik in ISO 8601. |

Es ist üblich, dass Abfragen in Millisekunden ausgeführt werden, sodass nur Abfragen, die als Sekunden gemessen werden, in Metriken wie QPS angezeigt werden.

Bei der Metrik **Suchabfragen pro Sekunde** ist der Mindestwert der niedrigste Wert für Suchabfragen pro Sekunde, der während dieser Minute registriert wurde. Dasselbe gilt für den Höchstwert. Der Durchschnittswert ist das Aggregat der gesamten Minute. Innerhalb einer Minute kann beispielsweise folgendes Muster vorliegen: Für eine Sekunde tritt eine sehr hohe Last auf (dies ist der Höchstwert für „SearchQueriesPerSecond“), gefolgt von 58 Sekunden mit mittlerer Last sowie einer Sekunde mit nur einer Abfrage, was der Mindestwert ist.

Für **Gedrosselte Suchabfragen in Prozent** entsprechen der Mindest-, Höchst-, Durchschnitts- und Gesamtwert demselben Wert, nämlich dem Prozentsatz von Suchabfragen, die gedrosselt wurden, basierend auf der Gesamtanzahl von Suchabfragen während einer Minute.

## <a name="view-raw-log-files"></a>Anzeigen unformatierter Protokolldateien

Der Blobspeicher wird für die Archivierung von Protokolldateien verwendet. Sie können die Protokolldatei mit einem beliebigen JSON-Editor anzeigen. Wenn Sie über keinen verfügen, empfiehlt sich die Verwendung von [Visual Studio Code](https://code.visualstudio.com/download).

1. Öffnen Sie Ihr Speicherkonto im Azure-Portal. 

2. Klicken Sie im linken Navigationsbereich auf **Blobs**. **insights-logs-operationlogs** und **insights-metrics-pt1m** sollten angezeigt werden. Diese Container werden in der kognitiven Azure-Suche erstellt, wenn die Protokolldaten in Blob Storage exportiert werden.

3. Klicken Sie in der Ordnerhierarchie nach unten bis zur JSON-Datei.  Verwenden Sie das Kontextmenü, um die Datei herunterzuladen.

Nach dem Herunterladen der Datei können Sie sie in einem JSON-Editor öffnen und die Inhalte anzeigen.

## <a name="next-steps"></a>Nächste Schritte

Fall noch nicht geschehen, überprüfen Sie die Grundlagen der Überwachung von Suchdiensten, um mehr über die gesamte Palette an Überwachungsfunktionen zu erfahren.

> [!div class="nextstepaction"]
> [Überwachen von Vorgängen und Aktivitäten von Azure Cognitive Search](search-monitor-usage.md)