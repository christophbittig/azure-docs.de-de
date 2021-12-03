---
title: Azure Cosmos DB Indizierungsmetriken
description: Erfahren Sie, wie Sie die Indizierungsmetriken in Azure Cosmos DB erhalten und interpretieren können
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: tisande
ms.openlocfilehash: f10208e83c9c7f23600285444d22ed5b5faf2488
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551974"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Indizierungsmetriken in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB bietet Indizierungsmetriken, um sowohl genutzte indizierte Pfade als auch empfohlene indizierte Pfade anzuzeigen. Sie können die Indizierungsmetriken verwenden, um die Abfrageleistung zu optimieren, insbesondere in Fällen, in denen Sie nicht sicher sind, wie Sie die [Indizierungsrichtlinie](../index-policy.md)) ändern können.

> [!NOTE]
> Die Indizierungsmetriken werden nur im .NET SDK (Version 3.21.0 oder höher) und Java SDK (Version 4.19.0 oder höher) unterstützt

## <a name="enable-indexing-metrics"></a>Aktivieren von Indizierungsmetriken

Sie können Indizierungsmetriken für eine Abfrage aktivieren, indem Sie die Eigenschaft `PopulateIndexMetrics` auf `true` setzen. Wenn nicht angegeben, wird `PopulateIndexMetrics` standardmäßig auf `false` gesetzt. Wir empfehlen die Aktivierung der Indexmetriken nur zur Fehlerbehebung bei der Abfrageleistung. Solange Ihre Abfragen und Indizierungsrichtlinien gleich bleiben, werden sich die Indexmetriken wahrscheinlich nicht ändern. Stattdessen empfehlen wir, teure Abfragen zu identifizieren, indem Sie die Abfrage-RU-Ladung und -Latenz mithilfe von Diagnoseprotokollen überwachen.

### <a name="net-sdk-example"></a>Beispiel für .NET SDK

```csharp
    string sqlQueryText = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    QueryDefinition query = new QueryDefinition(sqlQueryText);

    FeedIterator<Item> resultSetIterator = container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
          response = await resultSetIterator.ReadNextAsync();
          Console.WriteLine(response.IndexMetrics);
        }
```

### <a name="example-output"></a>Beispielausgabe

In dieser Beispielabfrage sehen wir die genutzten Pfade `/Item/?` und `/Price/?` die potenziellen zusammengesetzten Indizes `(/Item ASC, /Price ASC)`.

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /Item/?
    Index Impact Score: High
    ---
    Index Spec: /Price/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /Item ASC, /Price ASC
    Index Impact Score: High
    ---
```

## <a name="utilized-indexed-paths"></a>Verwendete indizierte Pfade

Die genutzten Einzelindizes bzw. die genutzten zusammengesetzten Indizes zeigen die von der Abfrage verwendeten eingeschlossenen Pfade und zusammengesetzten Indizes. Abfragen können mehrere indizierte Pfade sowie eine Mischung aus einbezogenen Pfaden und zusammengesetzten Indizes verwenden. Wenn ein indizierter Pfad nicht als genutzt aufgeführt ist, hat das Entfernen des indizierten Pfads keine Auswirkungen auf die Leistung der Abfrage.

Betrachten Sie die Liste der verwendeten indizierten Pfade als Beweis dafür, dass eine Abfrage diese Pfade verwendet hat. Wenn Sie nicht sicher sind, ob ein neuer indizierter Pfad die Abfrageleistung verbessert, sollten Sie versuchen, die neuen indizierten Pfade hinzuzufügen und prüfen, ob die Abfrage sie verwendet.

## <a name="potential-indexed-paths"></a>Potenzielle indizierte Pfade

Die potenziellen Einzelindizes bzw. die verwendeten zusammengesetzten Indizes zeigen die eingeschlossenen Pfade und zusammengesetzten Indizes an, die, wenn sie hinzugefügt werden, von der Abfrage verwendet werden könnten. Wenn Sie potenzielle indizierte Pfade sehen, sollten Sie in Erwägung ziehen, diese zu Ihrer Indizierungsrichtlinie hinzuzufügen und beobachten, ob sie die Abfrageleistung verbessern.

Betrachten Sie die Liste der potenziellen Indizierungspfade als Empfehlungen und nicht als schlüssigen Beweis dafür, dass eine Abfrage einen bestimmten Indizierungspfad verwenden wird. Die potenziellen Indizierungspfade sind keine erschöpfende Liste der Indizierungspfade, die eine Abfrage verwenden könnte. Außerdem ist es möglich, dass einige potentielle indizierte Pfade keinen Einfluss auf die Abfrageleistung haben. [ Fügen Sie die empfohlenen indizierten Pfade](how-to-manage-indexing-policy.md) hinzu und bestätigen Sie, dass sie die Abfrageleistung verbessern.

> [!NOTE]
> Haben Sie ein Feedback zu den Indizierungsmetriken? Teilen Sie uns Ihre Meinung mit! Sie können Feedback direkt an das Azure Cosmos DB-Entwicklungsteam senden: cosmosdbindexing@microsoft.com

## <a name="index-impact-score"></a>Indexauswirkungs-Score

Der Index Impact Score ist die Wahrscheinlichkeit, dass ein indizierter Pfad, basierend auf der Abfrageform, einen signifikanten Einfluss auf die Abfrageleistung hat. Mit anderen Worten, der Index-Impact-Score ist die Wahrscheinlichkeit, dass ohne diesen spezifischen indizierten Pfad die EVU-Last der Abfrage wesentlich höher gewesen wäre. 

Es gibt zwei mögliche Index-Impact-Scores: **hoch** und **niedrig**. Wenn Sie mehrere potenzielle indizierte Pfade haben, empfehlen wir, sich auf indizierte Pfade mit einem **hohen** Impact Score zu konzentrieren.

Das einzige Kriterium, das für die Indexauswirkungsbewertung herangezogen wird, ist die Form der Abfrage. In der unten stehenden Abfrage würde dem indizierten Pfad `/name/?` beispielsweise ein **hoher** Index Impact Score zugewiesen werden:

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

Die tatsächliche Auswirkung hängt von der Art der Daten ab. Wenn nur wenige Elemente mit dem Filter `/name` übereinstimmen, wird der indizierte Pfad die RU-Last der Abfrage erheblich verbessern. Wenn jedoch die meisten Elemente ohnehin mit dem `/name`-Filter übereinstimmen, verbessert der indizierte Pfad die Abfrageleistung möglicherweise nicht. In jedem dieser Fälle würde dem indizierten Pfad `/name/?` ein **hoher** Indexauswirkungs-Score zugewiesen, da der indizierte Pfad auf der Grundlage der Abfrageform eine hohe Wahrscheinlichkeit hat, die Abfrageleistung zu verbessern.

## <a name="additional-examples"></a>Weitere Beispiele

### <a name="example-query"></a>Beispielabfrage

```sql
SELECT c.id 
FROM c 
WHERE c.name = 'Tim' AND c.age > 15 AND c.town = 'Redmond' AND c.timestamp > 2349230183
```

### <a name="index-metrics"></a>Indexmetriken

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /name/?
    Index Impact Score: High
    ---
    Index Spec: /age/?
    Index Impact Score: High
    ---
    Index Spec: /town/?
    Index Impact Score: High
    ---
    Index Spec: /timestamp/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /name ASC, /town ASC, /age ASC
    Index Impact Score: High
    ---
    Index Spec: /name ASC, /town ASC, /timestamp ASC
    Index Impact Score: High
    ---
```
Diese Indexmetriken zeigen, dass die Abfrage die indizierten Pfade `/name/?`, `/age/?`, `/town/?` und `/timestamp/?` verwendet hat. Die Indexmetriken zeigen auch, dass es eine hohe Wahrscheinlichkeit gibt, dass das Hinzufügen der zusammengesetzten Indizes `(/name ASC, /town ASC, /age ASC)` und `(/name ASC, /town ASC, /timestamp ASC)` die Leistung weiter verbessern wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

- [Übersicht über die Indizierung](../index-overview.md)
- [Gewusst wie: Verwalten der Indizierungsrichtlinie](how-to-manage-indexing-policy.md)
