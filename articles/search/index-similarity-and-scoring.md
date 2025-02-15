---
title: Übersicht über Ähnlichkeit und Bewertung
titleSuffix: Azure Cognitive Search
description: In diesem Artikel werden die Konzepte „Ähnlichkeit“ und „Bewertung“ erläutert sowie Arten, auf die Entwickler Bewertungsergebnisse anpassen können.
author: puneet-hariharan-MSFT
ms.author: puhariharan
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cacf6097234a5a2c7ec5261623fe653e509b8d9f
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981428"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Ähnlichkeit und Bewertung in Azure Cognitive Search

In diesem Artikel werden die beiden Ähnlichkeitsalgorithmen für die Rangfolge beschrieben, die von Azure Cognitive Search verwendet werden, um zu bestimmen, welche passenden Dokumente für die Abfrage am relevantesten sind. Außerdem werden in diesem Artikel zwei verwandte Features eingeführt: *Bewertungsprofile* (Kriterien zur Anpassung einer Suchbewertung) und der Parameter *featuresMode* (entpackt eine Suchbewertung, um mehr Details anzuzeigen).

> [!NOTE]
> Ein dritter [Algorithmus zur Neuerstellung der semantischen Rangfolge](semantic-ranking.md) befindet sich gerade in der Public Preview-Phase. Wenn Sie weitere Informationen benötigen, lesen Sie zunächst [Übersicht über die semantische Suche](semantic-search-overview.md).

## <a name="similarity-ranking-algorithms"></a>Ähnlichkeitsalgorithmus für die Rangfolge

Azure Cognitive Search unterstützt zwei Ähnlichkeitsalgorithmen für die Rangfolge.

| Algorithmus | Score | Verfügbarkeit |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | Wurde von allen Suchdiensten bis zum 15. Juli 2020 verwendet. |
| BM25Similarity | @search.score | Wird von allen nach dem 15. Juli erstellten Suchdiensten verwendet. Ältere Dienste, die standardmäßig „klassisch“ verwenden, können [„BM25“ abonnieren](index-ranking-similarity.md). |

Sowohl der klassische als auch der BM25-Algorithmus bieten TF-IDF-ähnliche Abfragefunktionen, die die Vorkommenshäufigkeit (Term Frequency, TF) und die inverse Dokumenthäufigkeit (Inverse Document Frequency, IDF) als Variablen verwenden, um Relevanzbewertungen für jedes Dokument-Abfrage-Paar zu berechnen, die dann für die Rangfolge verwendet werden. Zwar ist „klassisch“ konzeptionell ähnlich, „BM25“ hat seine Wurzeln aber in der wahrscheinlichkeitstheoretischen Informationsabfrage, um das Ergebnis zu verbessern. BM25 bietet auch erweiterte Anpassungsoptionen, z. B. kann der Benutzer entscheiden, wie weit die Relevanzbewertung von der Häufigkeit übereinstimmender Begriffe abhängt.

Das folgende Videosegment bietet eine schnelle Übersicht über die in Azure Cognitive Search verwendeten allgemein verfügbaren Ähnlichkeitsalgorithmen. Sie können sich das vollständige Video ansehen, um weitere Hintergrundinformationen zu erhalten.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>Relevanzbewertung

Der Begriff „Bewertung“ bezieht sich auf die Berechnung einer Suchbewertung für jedes in Suchergebnissen für Volltext-Suchabfragen zurückgegebene Element. Die Bewertung ist ein Indikator für die Relevanz eines Elements im Kontext der aktuellen Abfrage. Je höher die Bewertung, desto relevanter das Element. In den Suchergebnissen ist den Elementen eine absteigende Reihenfolge zugeordnet, die auf den für die einzelnen Elemente berechneten Suchbewertungen basiert. Die Bewertung wird in der Antwort als „@search.score“ für jedes Dokument zurückgegeben.

Standardmäßig werden in der Antwort die ersten 50 Elemente zurückgegeben, Sie können jedoch den **$top**-Parameter verwenden, um eine kleinere oder größere Anzahl von Elementen (bis zu 1000 in einer einzelnen Antwort) zurückzugeben, sowie den Parameter **$skip**, um zu den nächsten Ergebnissen zu gelangen.

Eine Suchbewertung wird auf Basis der statistischen Eigenschaften der Daten und der Abfrage berechnet. Azure Cognitive Search findet Dokumente, die Suchbegriffe enthalten (einige oder alle, in Abhängigkeit von [searchMode](/rest/api/searchservice/search-documents#query-parameters)), wobei Dokumente bevorzugt werden, in denen der Suchbegriff häufig vorkommt. Die Suchbewertung fällt sogar noch höher aus, wenn der Begriff nur selten im Datenindex, jedoch innerhalb des Dokuments häufig vorkommt. Die Grundlage für diesen Ansatz zur Berechnung der Relevanz wird als *TF-IDF* (Term Frequency – Inverse Document Frequency, Vorkommenshäufigkeit – Inverse Dokumenthäufigkeit) bezeichnet.

Suchbewertungswerte können in einem Resultset wiederholt vorkommen. Wenn mehrere Treffer die gleiche Suchbewertung aufweisen, ist die Sortierung von Elementen mit gleicher Bewertung nicht definiert und somit auch nicht stabil. Wenn Sie die Abfrage noch mal ausführen, sehen Sie möglicherweise, dass sich die Position von Elementen ändert. Dies ist insbesondere dann der Fall, wenn Sie den kostenlosen Dienst oder einen abrechenbaren Dienst mit vielen Replikaten verwenden. Wenn zwei Elemente mit identischer Bewertung vorliegen, kann nicht garantiert werden, welches Element zuerst angezeigt wird.

Wenn Sie eine Reihenfolge für Elemente mit der gleichen Bewertung festlegen möchten, können Sie eine **$orderby**-Klausel hinzufügen, um erst nach Bewertung und dann nach einem anderen sortierbaren Feld zu sortieren (z. B. `$orderby=search.score() desc,Rating desc`). Weitere Informationen finden Sie unter [$orderby](search-query-odata-orderby.md).

> [!NOTE]
> `@search.score = 1.00` bedeutet, dass es sich um ein nicht bewertetes oder unsortiertes Resultset handelt. Die Bewertung wird für alle Ergebnisse gleich durchgeführt. Nicht bewertete Ergebnisse treten auf, wenn das Abfrageformular eine Fuzzysuche, Platzhalterabfrage oder Abfrage mit regulären Ausdrücken ausführt oder es sich um einen **$filter**-Ausdruck handelt.

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Bewertungsstatistiken und persistente Sitzungen

Aus Gründen der Skalierbarkeit verteilt Azure Cognitive Search jeden Index horizontal mithilfe eines Shardingprozesses. Dies bedeutet, dass [Teile eines Index physisch voneinander getrennt](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards) sind.

Standardmäßig wird die Bewertung eines Dokuments basierend auf statistischen Eigenschaften der Daten *innerhalb eines Shards* berechnet. Diese Vorgehensweise stellt in der Regel bei einem großen Datenkorpus kein Problem dar und bietet eine bessere Leistung als die Berechnung der Bewertung auf Grundlage von Informationen in allen Shards. Allerdings kann die Verwendung dieser Leistungsoptimierung dazu führen, dass zwei sehr ähnliche (oder sogar identische) Dokumente unterschiedliche Relevanzbewertungen erhalten, wenn sie sich in verschiedenen Shards befinden.

Wenn Sie die Bewertung lieber auf Grundlage der statistischen Eigenschaften in allen Shards berechnen möchten, können Sie dies tun, indem Sie *scoringStatistics=global* als [Abfrageparameter](/rest/api/searchservice/search-documents) hinzufügen (oder *"scoringStatistics": "global"* als Textkörperparameter der [Abfrageanforderung](/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Durch die Verwendung von „scoringStatistics“ wird sichergestellt, dass alle Shards in demselben Replikat die gleichen Ergebnisse liefern. Dennoch können sich unterschiedliche Replikate geringfügig voneinander unterscheiden, da sie immer mit den neuesten Änderungen am Index aktualisiert werden. In einigen Szenarien möchten Sie möglicherweise, dass die Benutzer während einer Abfragesitzung konsistentere Ergebnisse erhalten. In solchen Szenarien können Sie eine `sessionId` in den Abfragen bereitstellen. Die `sessionId` ist eine eindeutige Zeichenfolge, die Sie erstellen, um auf eine eindeutige Benutzersitzung zu verweisen.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Solange dieselbe `sessionId` verwendet wird, wird ein bestmöglicher Versuch unternommen, das gleiche Replikat als Ziel zu verwenden und die Konsistenz der Ergebnisse zu erhöhen, die den Benutzern angezeigt werden. 

> [!NOTE]
> Wenn Sie wiederholt die gleichen `sessionId`-Werte verwenden, können der Lastenausgleich der Anforderungen über Replikate hinweg und die Leistung des Suchdiensts beeinträchtigt werden. Der als „sessionId“ verwendete Wert darf nicht mit dem Zeichen „_“ beginnen.

## <a name="scoring-profiles"></a>Bewertungsprofile

Sie können die Sortierung der verschiedenen Felder anpassen, indem Sie ein *Bewertungsprofil* definieren. Bewertungsprofile bieten Ihnen mehr Kontrolle über die Rangfolge der Elemente in Suchergebnissen. Sie können beispielsweise Elemente auf Basis ihres Umsatzpotentials optimieren, neuere Elemente hochstufen oder auch Elemente fördern, die sich bereits zu lange im Lager befinden. 

Ein Bewertungsprofil ist Teil der Indexdefinition und besteht aus gewichteten Feldern, Funktionen und Parametern. Weitere Informationen zum Definieren von Bewertungsprofilen finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Index für Azure Cognitive Search](index-add-scoring-profiles.md).

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode-Parameter (Vorschau)

Anforderungen zur [Dokumentsuche](/rest/api/searchservice/preview-api/search-documents) weisen einen neuen [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode)-Parameter auf, der weitere Details über die Relevanz auf Feldebene bereitstellen kann. Während `@searchScore` für das gesamte Dokument berechnet wird (wie relevant ist dieses Dokument im Kontext dieser Abfrage) können Sie mithilfe von featuresMode Informationen zu einzelnen Feldern erhalten, wie in einer `@search.features`-Struktur ausgedrückt. Die Struktur enthält alle in der Abfrage verwendeten Felder (entweder spezifische Felder mithilfe von **searchFields** in einer Abfrage oder alle in einem Index als **searchable** gekennzeichneten Felder). Für jedes Feld erhalten Sie die folgenden Werte:

+ Anzahl der im Feld gefundenen eindeutigen Token
+ Ähnlichkeitsbewertung oder eine Kennzahl für die Ähnlichkeit des Feldinhalts im Verhältnis zum Abfrageausdruck
+ Ausdruckshäufigkeit oder Anzahl der Vorkommen des Ausdrucks im Feld

Für eine Abfrage, die auf die Felder „Beschreibung“ und „Titel“ abzielt, könnte eine Antwort, die `@search.features` beinhaltet, etwa so aussehen:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

Sie können diese Datenpunkte in [benutzerdefinierten Bewertungslösungen](https://github.com/Azure-Samples/search-ranking-tutorial) verbrauchen oder die Informationen zum Debuggen von Problemen bei der Suchrelevanz verwenden.

## <a name="see-also"></a>Weitere Informationen

+ [Bewertungsprofile](index-add-scoring-profiles.md)
+ [REST-API-Referenz](/rest/api/searchservice/)
+ [Dokumente durchsuchen (Azure Cognitive Search-REST-API)](/rest/api/searchservice/search-documents)
+ [.NET SDK für die kognitive Azure-Suche](/dotnet/api/overview/azure/search)