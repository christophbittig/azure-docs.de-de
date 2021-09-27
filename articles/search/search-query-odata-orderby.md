---
title: OData-Referenz zu „$orderby“
titleSuffix: Azure Cognitive Search
description: Syntax und Sprachreferenzdokumentation für die Verwendung von „order-by“ in Azure Cognitive Search-Abfragen.
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
ms.openlocfilehash: c98ef5871fa4f3d45633965671e6f915b518409e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607024"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData-Syntax von „$orderby“ in Azure Cognitive Search

 Mit dem [OData-Parameter **$orderby**](query-odata-filter-orderby-syntax.md) können Sie in Azure Cognitive Search eine benutzerdefinierte Sortierreihenfolge auf Suchergebnisse anwenden. In diesem Artikel wird die Syntax von **$orderby** detailliert beschrieben. Weitere allgemeine Informationen zur Verwendung von **$orderby** bei der Darstellung von Suchergebnissen finden Sie unter [Arbeiten mit Suchergebnissen in Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Der Parameter **$orderby** akzeptiert eine durch Trennzeichen getrennte Liste von bis zu 32 **ORDER BY-Klauseln**. Die Syntax einer ORDER BY-Klausel wird in der folgenden [erweiterten Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) (EBNF) veranschaulicht:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

Jede Klausel enthält Sortierkriterien, optional gefolgt von einer Sortierreihenfolge (`asc` = aufsteigend oder `desc` = absteigend). Wenn Sie keine Richtung angeben, wird die Standardeinstellung „asc“ (aufsteigend) verwendet. Wenn im Feld NULL-Werte vorhanden sind, werden NULL-Werte zuerst angezeigt, wenn die Sortierung `asc` lautet. Wenn die Sortierung `desc` lautet, werden sie an letzter Stelle angezeigt.

Bei den Sortierkriterien kann es sich entweder um den Pfad eines Felds vom Typ `sortable` oder um einen Aufruf der Funktion [`geo.distance`](search-query-odata-geo-spatial-functions.md) oder [`search.score`](search-query-odata-search-score-function.md) handeln.

Wenn für mehrere Dokumente die gleichen Sortierkriterien gelten und die Funktion `search.score` nicht verwendet wird (z. B. wenn Sie nach einem numerischen Feld vom Typ `Rating` sortieren und drei Dokumente eine Bewertung von 4 aufweisen), werden Verknüpfungen auf Grundlage des Dokumentergebnisses in absteigender Reihenfolge getrennt. Wenn Dokumentergebnisse identisch sind (z. B. wenn in der Anforderung keine Volltextsuchabfrage angegeben ist), dann ist die relative Sortierung der verknüpften Dokumente unbestimmt.

Sie können mehrere Sortierkriterien angeben. Die Reihenfolge der Ausdrücke bestimmt die endgültige Sortierreihenfolge. Die Syntax zum absteigenden Sortieren nach Suchergebnis, gefolgt von der Bewertung lautet beispielsweise `$orderby=search.score() desc,Rating desc`.

Die Syntax für `geo.distance` in **$orderby** entspricht der Syntax in **$filter**. Bei der Verwendung von `geo.distance` in **$orderby** muss das Feld, für das der Ausdruck gilt, vom Typ `Edm.GeographyPoint` und zudem `sortable` sein.

Die Syntax für `search.score` in **$orderby** ist `search.score()`. Die Funktion `search.score` akzeptiert keine Parameter.

## <a name="examples"></a>Beispiele

Sortieren von Hotels in aufsteigender Reihenfolge nach Basistarif:

```odata-filter-expr
    $orderby=BaseRate asc
```

Sortieren von Hotels in absteigender Reihenfolge nach Bewertung und anschließend in aufsteigender Reihenfolge nach Basistarif (bedenken Sie, dass die Standardreihenfolge aufsteigend ist):

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

Sortieren von Hotels in absteigender Reihenfolge nach Bewertung und anschließend in aufsteigender Reihenfolge nach der Entfernung von den angegebenen Koordinaten:

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

Sortieren von Hotels in absteigender Reihenfolge nach Suchergebnis und Bewertung und anschließend in aufsteigender Reihenfolge nach der Entfernung von den angegebenen Koordinaten. Bei zwei Hotels mit identischen Relevanzbewertungen und Bewertungen wird das nächstgelegene zuerst aufgeführt:

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Nächste Schritte  

- [Arbeiten mit Suchergebnissen in der kognitiven Azure-Suche](search-pagination-page-layout.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)