---
title: Referenz zu den logischen OData-Operatoren
titleSuffix: Azure Cognitive Search
description: Syntax und Referenzdokumentation für die Verwendung der logischen OData-Operatoren „and“, „or“ und „not“ in Azure Cognitive Search-Abfragen.
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
ms.openlocfilehash: 0cd42f2a8ba7b9d543909604184e3618da31e710
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656548"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logische OData-Operatoren in der kognitiven Azure-Suche: `and`, `or`, `not`

[OData-Filterausdrücke](query-odata-filter-orderby-syntax.md) in der kognitiven Azure-Suche sind boolesche Ausdrücke, die in `true` oder `false` ausgewertet werden. Sie können einen komplexen Filter schreiben, indem Sie eine Reihe von [einfacheren Filtern](search-query-odata-comparison-operators.md) erstellen und diese dann mit den logischen Operatoren aus [boolescher Algebra](https://en.wikipedia.org/wiki/Boolean_algebra) zusammensetzen:

- `and`: Ein binärer Operator, der als `true` ausgewertet wird, wenn sein linker und rechter Unterausdruck als `true` ausgewertet werden.
- `or`: Ein binärer Operator, der als `true` ausgewertet wird, wenn sein linker oder rechter Unterausdruck als `true` ausgewertet wird.
- `not`: Ein unärer Operator, der als `true` ausgewertet wird, wenn sein Unterausdruck als `false` ausgewertet wird, und umgekehrt.

Diese Operatoren ermöglichen es Ihnen zusammen mit den [Sammlungsoperatoren`any` und `all`](search-query-odata-collection-operators.md), Filter zu erstellen, die sehr komplexe Suchkriterien ausdrücken können.

## <a name="syntax"></a>Syntax

Die folgende EBNF ([Erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik eines OData-Ausdrucks, der die logischen Operatoren verwendet.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

Es gibt zwei Formen von logischen Ausdrücken: binär (`and`/`or`) mit zwei Unterausdrücken und unär (`not`) mit nur einem Unterausdruck. Die Unterausdrücke können beliebige boolesche Ausdrücke sein:

- Felder oder Bereichsvariablen vom Typ `Edm.Boolean`
- Funktionen, die Werte vom Typ `Edm.Boolean` zurückgeben, z.B. `geo.intersects` oder `search.ismatch`
- [Vergleichsausdrücke](search-query-odata-comparison-operators.md), z.B. `rating gt 4`
- [Sammlungsausdrücke](search-query-odata-collection-operators.md), z.B. `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Die booleschen Literale `true` oder `false`.
- Andere logische Ausdrücke, die mit `and`, `or` oder `not` gebildet werden.

> [!IMPORTANT]
> Unter bestimmten Umständen können nicht alle Arten von Unterausdrücken mit `and`/`or` verwendet werden, insbesondere innerhalb von Lambdaausdrücken. Details dazu finden Sie unter [OData-Sammlungsoperatoren in der kognitiven Azure-Suche](search-query-odata-collection-operators.md#limitations).

### <a name="logical-operators-and-null"></a>Logische Operatoren und `null`

Die meisten booleschen Ausdrücke wie Funktionen und Vergleiche können keine `null`-Werte erzeugen, und die logischen Operatoren können nicht direkt auf das `null`-Literal angewendet werden (z.B. ist `x and null` unzulässig). Boolesche Felder können jedoch `null` sein, sodass Sie beachten müssen, wie sich die Operatoren `and`, `or` und `not` bei Vorhandensein von null verhalten. Dies wird in der folgenden Tabelle zusammengefasst, wobei `b` ein Feld vom Typ `Edm.Boolean` ist:

| Ausdruck | Ergebnis, wenn `b``null` ist |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Wenn ein boolesches Feld `b` von selbst in einem Filterausdruck vorkommt, verhält es sich, als wäre es `b eq true`. Wenn also `b``null` ist, wird der Ausdruck in `false` ausgewertet. Auf ähnliche Weise verhält sich `not b` wie `not (b eq true)` und wird daher in `true` ausgewertet. Auf diese Weise verhalten sich `null`-Felder wie `false`. Dies entspricht dem Verhalten bei der Kombination mit anderen Ausdrücken unter Verwendung von `and` und `or`, wie in der Tabelle oben gezeigt. Dennoch wird ein direkter Vergleich mit `false` (`b eq false`) immer noch in `false` ausgewertet. Mit anderen Worten: `null` ist nicht gleich `false`, auch wenn es sich in booleschen Ausdrücken so verhält.

## <a name="examples"></a>Beispiele

Abgleichen von Dokumenten, bei denen das `rating`-Feld zwischen 3 und 5 (einschließlich) liegt:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Abgleichen von Dokumenten, bei denen alle Elemente des `ratings`-Felds kleiner als 3 oder größer als 5 sind:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Abgleichen von Dokumenten, bei denen das `location`-Feld im angegebenen Polygon liegt und das Dokument nicht den Begriff „public“ (öffentlich) enthält.

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

Abgleichen von Dokumenten für Hotels in Vancouver (Kanada), die ein Luxuszimmer mit einem Basispreis kleiner als 160 bieten:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Nächste Schritte  

- [Filter in Azure Cognitive Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)