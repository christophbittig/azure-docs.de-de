---
title: Referenz für die OData-Funktion „search.score“
titleSuffix: Azure Cognitive Search
description: Syntax und Referenzdokumentation für die Verwendung der search.score-Funktion in Azure Cognitive Search-Abfragen.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 27cd6b6a9fb827d2ce2e4f3489cf51db797d0a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595577"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData-Funktion `search.score` in der kognitiven Azure-Suche

Wenn Sie eine Abfrage ohne den [Parameter **$orderby**](search-query-odata-orderby.md) an die kognitive Azure-Suche senden, werden die zurückgegebenen Ergebnisse in absteigender Reihenfolge nach Relevanz sortiert. Selbst wenn Sie **$orderby** verwenden, wird die Relevanzbewertung standardmäßig verwendet, um Verbindungen aufzuheben. Manchmal ist es jedoch sinnvoll, die Relevanzbewertung als erstes Sortierkriterium und einige andere Kriterien als Entscheidungshilfe zu verwenden. Die `search.score`-Funktion ermöglicht dies.

## <a name="syntax"></a>Syntax

Die Syntax für `search.score` in **$orderby** ist `search.score()`. Die Funktion `search.score` akzeptiert keine Parameter. Sie kann mit dem Spezifizierer `asc` oder `desc` für die Sortierreihenfolge verwendet werden, genau wie jede andere Klausel im Parameter **$orderby**. Sie kann an einer beliebigen Stelle in der Liste der Sortierkriterien platziert werden.

## <a name="example"></a>Beispiel

Sortieren von Hotels in absteigender Reihenfolge nach `search.score` und `rating` und anschließend in aufsteigender Reihenfolge nach der Entfernung von den angegebenen Koordinaten, sodass von zwei Hotels mit identischen Bewertungen das nächstgelegene zuerst aufgeführt wird:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Nächste Schritte  

- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;REST-API für die kognitive Azure-Suche&#41;](/rest/api/searchservice/Search-Documents)