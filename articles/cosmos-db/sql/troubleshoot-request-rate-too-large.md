---
title: Troubleshooting für Ausnahmen aufgrund einer zu hohen Azure Cosmos DB-Anforderungsrate
description: Erfahren Sie mehr über das Diagnostizieren und Beheben von Problemen bei einer Ausnahme aufgrund einer zu hohen Anforderungsrate.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/25/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 44ecb59508b93347ba57fb40a88c274adfedd320
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434050"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-429-exceptions"></a>Diagnostizieren und Behandeln von Problemen im Zusammenhang mit der Azure Cosmos DB-Ausnahme „Zu hohe Anforderungsrate“ (429)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Dieser Artikel enthält bekannte Ursachen und Lösungen für verschiedene Fehler mit dem Statuscode 429 für die SQL-API. Wenn Sie die API für MongoDB verwenden, finden Sie im Artikel [Behandeln häufiger Probleme in der Azure Cosmos DB-API für MongoDB](../mongodb/error-codes-solutions.md) Informationen zum Debuggen des Statuscodes 16500.

Die Ausnahme „Anforderungsrate zu groß“ mit Fehlercode 429 weist darauf hin, dass die Rate Ihrer Anforderungen an Azure Cosmos DB begrenzt wird.

Wenn Sie bereitgestellten Durchsatz nutzen, legen Sie den Durchsatz fest, der in Anforderungseinheiten pro Sekunde (Request Units per Second, RU/s) gemessen wird, die für Ihre Workload erforderlich sind. Datenbankvorgänge des Diensts, z. B. Lese-, Schreib- und Abfragevorgänge, nehmen eine bestimmte Menge von Anforderungseinheiten in Anspruch. Weitere Informationen zu [Anforderungseinheiten](../request-units.md)

Wenn die Vorgänge in einer bestimmten Sekunde mehr als die bereitgestellten Anforderungseinheiten beanspruchen, gibt Azure Cosmos DB die Ausnahme 429 zurück. Die Anzahl der verfügbaren Anforderungseinheiten wird sekündlich zurückgesetzt.

Bevor Sie eine Aktion zum Ändern der RU/s vornehmen, müssen Sie zunächst die Grundursache der Ratenbegrenzung verstehen und dann das zugrunde liegende Problem beheben.  

Es gibt verschiedene Fehlermeldungen, die verschiedenen Ausnahmen des Typs 429 entsprechen:
- [Die Anforderungsrate ist groß. Möglicherweise sind weitere Anforderungseinheiten erforderlich, sodass keine Änderungen erfolgt sind.](#request-rate-is-large)
- [Die Anforderung wurde aufgrund einer hohen Rate von Metadatenanforderungen nicht abgeschlossen.](#rate-limiting-on-metadata-requests)
- [Die Anforderung wurde aufgrund eines vorübergehenden Dienstfehlers nicht abgeschlossen.](#rate-limiting-due-to-transient-service-error)


## <a name="request-rate-is-large"></a>Anforderungsrate ist hoch
Dies ist das häufigste Szenario. Es tritt auf, wenn die Anforderungseinheiten, die von Datenvorgängen beansprucht werden, die bereitgestellte Anzahl von RU/s überschreiten. 

### <a name="step-1-check-the-metrics-to-determine-the-percentage-of-requests-with-429-error"></a>Schritt 1: Überprüfen der Metriken, um den Prozentsatz der Anforderungen mit Fehlercode 429 zu ermitteln
Fehlermeldungen mit dem Code 429 bedeuten nicht zwangsläufig, dass ein Problem mit Ihrer Datenbank oder Ihrem Container vorliegt.

#### <a name="how-to-investigate"></a>Vorgehen bei der Untersuchung

Bestimmen Sie, welcher Prozentsatz Ihrer Anforderungen an Ihre Datenbank oder Ihren Container im Vergleich zur Gesamtanzahl erfolgreicher Anforderungen zum Fehler 429 geführt hat. Navigieren Sie auf dem Blatt Ihres Azure Cosmos DB-Kontos zu **Erkenntnisse** > **Anforderungen** > **Anforderungen gesamt nach Statuscode**. Filtern Sie nach einer bestimmten Datenbank und einem bestimmten Container. 

Die SDKs des Azure Cosmos DB-Clients und Datenimporttools wie Azure Data Factory und BulkExecutor-Bibliothek wiederholen beim Fehlercode 429 Anforderungen automatisch. 9 Wiederholungsversuche sind die Regel. Daher enthalten Metriken möglicherweise den Fehler mit dem Code 429, die aber möglicherweise nicht einmal an Ihre Anwendung zurückgegeben werden. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/insights-429-requests.png" alt-text="Diagramm für „Anforderungen gesamt nach Statuscode“ mit Anforderungen mit den Nummern 429 und 2xx.":::


#### <a name="recommended-solution"></a>Empfohlene Lösung
Im Allgemeinen gilt für eine Workload in der Produktion: Wenn 1-5 % der Anforderungen den Fehlercode 429 aufweisen und die End-to-End-Latenz akzeptabel ist, ist dies ein gutes Zeichen dafür, dass die RU/s vollständig genutzt werden. Keine Aktion erforderlich. Fahren Sie andernfalls mit den nächsten Problembehandlungsschritten fort.

### <a name="step-2-determine-if-there-is-a-hot-partition"></a>Schritt 2: Ermitteln, ob eine heiße Partition vorhanden ist
Es kommt zu einer heißen Partition, wenn mindestens ein logischer Partitionsschlüssel aufgrund eines höheren Anforderungsvolumens einen unverhältnismäßig großen Teil der gesamten RU/s beansprucht. Dies kann durch einen Partitionsschlüsselentwurf verursacht werden, aufgrund dessen Anforderungen nicht gleichmäßig verteilt werden. Dies führt dazu, dass viele Anforderungen an eine kleine Teilmenge logischer (d. h. physischer) Partitionen weitergeleitet werden, die „heiß“ werden. Da sich alle Daten für eine logische Partition in einer physischen Partition befinden und die gesamten RU/s gleichmäßig auf die physischen Partitionen verteilt sind, kann eine heiße Partition zum Fehlercode 429 und einer ineffizienten Durchsatznutzung führen. 

Es folgen einige Beispiele für Partitionierungsstrategien, die zu heißen Partitionen führen:
- Sie verfügen über einen Container, der IoT-Gerätedaten für eine Workload mit hohem Schreibaufwand speichert, die nach `date` partitioniert ist. Alle Daten für ein einzelnes Datum befinden sich in derselben logischen und physischen Partition. Da alle täglich geschriebenen Daten das gleiche Datum haben, würde dies jeden Tag zu einer heißen Partition führen. 
    - Stattdessen erzielt für dieses Szenario ein Partitionsschlüssel wie `id` (entweder GUID oder Geräte-ID) oder ein [synthetischer Partitionsschlüssel](./synthetic-partition-keys.md), der `id` und `date` kombiniert, eine höhere Kardinalität der Werte und eine bessere Verteilung des Anforderungvolumens.
- Sie haben ein Szenario mit mehreren Mandanten und einem nach `tenantId` partitionierten Container. Wenn ein Mandant deutlich aktiver als die anderen ist, führt dies zu einer heißen Partition. Wenn z. B. der größte Mandant 100.000 Benutzer hat, die meisten Mandanten aber weniger als 10 Benutzer haben, tritt bei Partitionierung nach `tenantID` eine heiße Partition auf. 
    - Erwägen Sie für dieses Szenario einen dedizierten Container für den größten Mandanten, der nach einer detaillierteren Eigenschaft wie `UserId` partitioniert ist. 
    
#### <a name="how-to-identify-the-hot-partition"></a>Erkennen der heißen Partition

Um zu überprüfen, ob eine heiße Partition vorliegt, wechseln Sie zu **Erkenntnisse** > **Durchsatz** > **Normalisierter RU-Verbrauch (%) nach PartitionKeyRangeID**. Filtern Sie nach einer bestimmten Datenbank und einem bestimmten Container. 

Jede PartitionKeyRangeId ist einer physischen Partition zugeordnet. Wenn es eine PartitionKeyRangeId gibt, die einen deutlich höheren normalisierten RU-Verbrauch als andere aufwies (die z. B. konstant bei 100 % liegt, während andere bei höchstens 30 % liegen), kann dies ein Zeichen einer heißen Partition sein. Erfahren Sie mehr über die Metrik [Normalisierter RU-Verbrauch](../monitor-normalized-request-units.md).

:::image type="content" source="media/troubleshoot-request-rate-too-large/split-norm-utilization-by-pkrange-hot-partition.png" alt-text="Diagramm für „Normalisierter RU-Verbrauch nach PartitionKeyRangeId“ mit heißer Partition.":::

In den [Azure-Diagnoseprotokollen](../cosmosdb-monitor-resource-logs.md) können Sie herausfinden, welche logischen Partitionsschlüssel die meisten RU/s beanspruchen. Diese Beispielabfrage summiert für jeden logischen Partitionsschlüssel die insgesamt beanspruchten Anforderungseinheiten pro Sekunde. 

> [!IMPORTANT]
> Durch Aktivierung von Diagnoseprotokollen fällt eine separate Gebühr für den Log Analytics-Dienst an, die basierend auf der Menge der erfassten Daten abgerechnet wird. Es wird empfohlen, zum Debuggen Diagnoseprotokolle für einen begrenzten Zeitraum zu aktivieren und sie wieder zu deaktivieren, sobald sie nicht mehr erforderlich sind. Einzelheiten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/).

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24hour)
| where Category == "PartitionKeyRUConsumption"
| where collectionName_s == "CollectionName" 
| where isnotempty(partitionKey_s)
// Sum total request units consumed by logical partition key for each second
| summarize sum(todouble(requestCharge_s)) by partitionKey_s, operationType_s, bin(TimeGenerated, 1s)
| order by sum_requestCharge_s desc
```
Diese Beispielausgabe zeigt, dass in einer bestimmten Minute der logische Partitionsschlüssel mit dem Wert „Contoso“ etwa 12.000 RU/s beansprucht hat, während der logische Partitionsschlüssel mit dem Wert „Fabrikam“ weniger als 600 RU/s benötigte. Wenn dieses Muster während des Zeitraums, in dem die Ratenbegrenzung auftrat, gleichbleibend war, deutet dies auf eine heiße Partition hin. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/hot-logical-partition-key-results.png" alt-text="Logische Partitionsschlüssel, die die meisten Anforderungseinheiten pro Sekunde verbrauchen.":::

> [!TIP]
> Bei jeder Workload gibt es natürliche Schwankungen beim Anforderungsvolumen der logischen Partitionen. Ermitteln Sie, ob die heiße Partition durch eine grundsätzliche Schiefe aufgrund des gewählten Partitionsschlüssels (was möglicherweise das Ändern des Schlüssels erfordert) oder durch eine vorübergehende Spitze aufgrund natürlicher Schwankungen bei Workloadmustern verursacht wird.

#### <a name="recommended-solution"></a>Empfohlene Lösung
Lesen Sie die Anleitung [zur Auswahl eines geeigneten Partitionsschlüssels](../partitioning-overview.md#choose-partitionkey).

Wenn es einen hohen Prozentsatz an Anforderungen mit Ratenbegrenzung und keine heiße Partition gibt:
- Sie können mithilfe von Client-SDKs, Azure-Portal, PowerShell, CLI oder ARM-Vorlage die [RU/s für die Datenbank oder den Container erhöhen](../set-throughput.md). Halten Sie sich an die [bewährten Methoden für das Skalieren des bereitgestellten Durchsatzes (RU/s)](../scaling-provisioned-throughput-best-practices.md), um den richtigen Durchsatz zu ermitteln, der festgelegt werden sollte.

Wenn es einen hohen Prozentsatz an Anforderungen mit Ratenbegrenzung und eine zugrunde liegende heiße Partition gibt:
-  Langfristig sollten Sie aus Kosten- und Leistungsgründen das **Ändern des Partitionsschlüssels** erwägen. Weil der Partitionsschlüssel nicht direkt aktualisiert werden kann, müssen die Daten in einen neuen Container mit einem anderen Partitionsschlüssel migriert werden. Zu diesem Zweck unterstützt Azure Cosmos DB ein [Tool für die Migration von Livedaten](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).
- Kurzfristig können Sie die RU/s vorübergehend erhöhen, um der heißen Partition einen höheren Durchsatz zu ermöglichen. Dies wird nicht als langfristige Strategie empfohlen, da dies zu einer Überbereitstellung von RU/s und mehr Kosten führt. 

> [!TIP]
>  Wenn Sie den Durchsatz erhöhen, wird der Hochskalierungsvorgang entweder sofort abgeschlossen oder erfordert je nach Anzahl der RU/s, auf die Sie hochskalieren möchten, bis zu 5-6 Stunden. Wenn Sie die höchste Anzahl von RU/s bestimmen möchten, die Sie festlegen können, ohne den asynchronen Hochskalierungsvorgang auszulösen (für den Azure Cosmos DB weitere physische Partitionen bereitstellen muss), multiplizieren Sie die Anzahl der unterschiedlichen PartitionKeyRangeIds mit 10.0000 RU/s. Wenn Sie beispielsweise 30.000 RU/s bereitgestellt und fünf physische Partitionen (6.000 RU/s pro physischer Partition zugeordnet) haben, können Sie in einem sofortigen Hochskalierungsvorgang auf 50.000 RU/s (10.000 RU/s pro physischer Partition) erhöhen. Eine Erhöhung auf über 50.000 RU/s würde einen asynchronen Hochskalierungsvorgang erfordern.

### <a name="step-3-determine-what-requests-are-returning-429s"></a>Schritt 3: Bestimmen, welche Anforderungen den Fehlercode 429 zurückgeben

#### <a name="how-to-investigate-requests-with-429s"></a>Untersuchen von Anforderungen mit Fehlercode 429
Ermitteln Sie mithilfe von [Azure-Diagnoseprotokollen](../cosmosdb-monitor-resource-logs.md), welche Anforderungen den Fehlercode 429 zurückgeben und wie viele RUs sie verbraucht haben. Diese Beispielabfrage führt eine Aggregation auf Minutenebene durch. 

> [!IMPORTANT]
> Durch Aktivierung von Diagnoseprotokollen fällt eine separate Gebühr für den Log Analytics-Dienst an, die basierend auf der Menge der erfassten Daten abgerechnet wird. Es wird empfohlen, zum Debuggen Diagnoseprotokolle für einen begrenzten Zeitraum zu aktivieren und sie wieder zu deaktivieren, sobald sie nicht mehr erforderlich sind. Einzelheiten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/).

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24h)
| where Category == "DataPlaneRequests"
| summarize throttledOperations = dcountif(activityId_g, statusCode_s == 429), totalOperations = dcount(activityId_g), totalConsumedRUPerMinute = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, OperationName, requestResourceType_s, bin(TimeGenerated, 1min)
| extend averageRUPerOperation = 1.0 * totalConsumedRUPerMinute / totalOperations 
| extend fractionOf429s = 1.0 * throttledOperations / totalOperations
| order by fractionOf429s desc
```
Diese Beispielausgabe zeigt beispielsweise, dass jede Minute 30 % der Anforderungen zum Erstellen von Dokumenten einer Ratenbegrenzung unterlagen, wobei jede Anforderung durchschnittlich 17 RUs verbrauchte.
:::image type="content" source="media/troubleshoot-request-rate-too-large/throttled-requests-diagnostic-logs-results.png" alt-text="Anforderungen mit Fehlercode 429 in Diagnoseprotokollen.":::

#### <a name="recommended-solution"></a>Empfohlene Lösung
##### <a name="429s-on-create-replace-or-upsert-document-requests"></a>Fehlercode 429 bei Anforderungen zum Erstellen, Ersetzen oder Aktualisieren/Einfügen (Upsert) von Dokumenten
- Standardmäßig sind in der SQL-API alle Eigenschaften indiziert. Optimieren Sie die [Indizierungsrichtlinie](../index-policy.md) so, dass nur die erforderlichen Eigenschaften indiziert werden.
Dadurch wird die Anzahl der erforderlichen Anforderungseinheiten pro Dokumenterstellungsvorgang gesenkt, was die Wahrscheinlichkeit des Fehlercodes 429 reduziert oder Ihnen ermöglicht, bei gleicher Menge an bereitgestellten RU/s mehr Vorgänge pro Sekunde zu erreichen. 

##### <a name="429s-on-query-document-requests"></a>Fehlercode 429 bei Anforderungen zum Abfragen von Dokumenten
- Befolgen Sie die Anleitung zur [Problembehandlung bei Abfragen mit hoher RU-Gebühr](troubleshoot-query-performance.md#querys-ru-charge-is-too-high).

##### <a name="429s-on-execute-stored-procedures"></a>Fehlercode 429 bei Ausführen gespeicherter Prozeduren
- [Gespeicherte Prozeduren](stored-procedures-triggers-udfs.md) sind für Vorgänge vorgesehen, die Schreibtransaktionen über einen Partitionsschlüsselwert hinweg erfordern. Für eine große Anzahl von Lese- oder Abfragevorgängen werden gespeicherte Prozeduren nicht empfohlen. Für eine optimale Leistung sollten diese Lese- oder Abfragevorgänge auf Clientseite mithilfe der Cosmos-SDKs erfolgen. 

## <a name="rate-limiting-on-metadata-requests"></a>Ratenbegrenzung für Metadatenanforderungen

Eine Ratenbegrenzung für Metadaten kann erfolgen, wenn Sie eine große Anzahl von Metadatenvorgängen auf Datenbanken und/oder Container anwenden. Es folgen verschiedene Metadatenvorgänge:
- Erstellen, Lesen, Aktualisieren oder Löschen eines Containers oder einer Datenbank
- Auflisten von Datenbanken oder Containern in einem Cosmos-Konto
- Abfragen von Angeboten, um den aktuellen bereitgestellten Durchsatz einzusehen 

Es gibt einen vom System reservierten RU-Grenzwert für diese Vorgänge, sodass eine Erhöhung der für die Datenbank oder den Container bereitgestellten RU/s keine Auswirkungen hat und nicht empfohlen wird. Weitere Informationen finden Sie unter [Grenzwerte für Metadatenvorgänge](../concepts-limits.md#metadata-request-limits).

#### <a name="how-to-investigate"></a>Vorgehen bei der Untersuchung
Navigieren Sie zu **Erkenntnisse** > **System** > **Metadatenanforderungen nach Statuscode**. Filtern Sie nach Wunsch nach einer bestimmten Datenbank und einem bestimmten Container. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/metadata-throttling-insights.png" alt-text="Diagramm der Metadatenanforderungen nach Statuscode in „Erkenntnisse“.":::

#### <a name="recommended-solution"></a>Empfohlene Lösung
- Wenn für Ihre Anwendung Metadatenvorgänge erforderlich sind, erwägen Sie die Implementierung einer Backoffrichtlinie, um diese Anforderungen mit einer niedrigeren Rate zu senden. 

- Verwenden Sie statische Cosmos DB-Clientinstanzen. Wenn DocumentClient oder CosmosClient initialisiert wird, ruft das Cosmos DB-SDK Metadaten zum Konto ab, einschließlich Informationen zu Konsistenzebene, Datenbanken, Containern, Partitionen und Angeboten. Diese Initialisierung verbraucht unter Umständen eine hohe Anzahl von RUs und sollte daher nur selten ausgeführt werden. Verwenden Sie eine einzelne DocumentClient-Instanz, und nutzen Sie sie während der Lebensdauer Ihrer Anwendung.

- Speichern Sie die Namen von Datenbanken und Containern zwischen. Rufen Sie die Namen Ihrer Datenbanken und Container aus der Konfiguration ab, oder speichern Sie sie beim Starten zwischen. Aufrufe wie ReadDatabaseAsync/ReadDocumentCollectionAsync oder CreateDatabaseQuery/CreateDocumentCollectionQuery führen zu Metadatenaufrufen an den Dienst, die das vom System reservierte RU-Limit verbrauchen. Diese Vorgänge sollten selten erfolgen.

## <a name="rate-limiting-due-to-transient-service-error"></a>Ratenbegrenzung aufgrund eines vorübergehenden Dienstfehlers

Dieser Fehlercode 429 wird zurückgegeben, wenn bei der Anforderung ein vorübergehender Dienstfehler auftritt. Die Erhöhung der RU/s für Datenbank oder Container ist wirkungslos und wird nicht empfohlen.

#### <a name="recommended-solution"></a>Empfohlene Lösung
Wiederholen Sie die Anforderung. Wenn der Fehler mehrere Minuten bestehen bleibt, reichen Sie im [Azure-Portal](https://portal.azure.com/) ein Supportticket ein.

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen Sie den normalisierten RU/s-Verbrauch](../monitor-normalized-request-units.md) Ihrer Datenbank oder Ihres Containers.
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).