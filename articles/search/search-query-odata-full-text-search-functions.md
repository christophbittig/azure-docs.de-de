---
title: Funktionsreferenz zur OData-Volltextsuche
titleSuffix: Azure Cognitive Search
description: Funktionen search.ismatch und search.ismatchscoring der OData-Volltextsuche in Abfragen der kognitiven Azure-Suche.
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
ms.openlocfilehash: c58c45ba0cff48b5760c9c3c164cb95b0f4e6336
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559226"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funktionen der OData-Volltextsuche in der kognitiven Azure-Suche: `search.ismatch` und `search.ismatchscoring`

Die kognitive Azure-Suche unterstützt die Volltextsuche im Kontext von [OData-Filterausdrücken](query-odata-filter-orderby-syntax.md) über die Funktionen `search.ismatch` und `search.ismatchscoring`. Diese Funktionen ermöglichen es Ihnen, die Volltextsuche mit strenger boolescher Filterung auf eine Art und Weise zu kombinieren, die nur mit dem Parameter `search` auf oberster Ebene der [Search-API](/rest/api/searchservice/search-documents) nicht möglich ist.

> [!NOTE]
> Die Funktionen `search.ismatch` und `search.ismatchscoring` werden nur in Filtern in der [Search-API](/rest/api/searchservice/search-documents) unterstützt. Sie werden nicht in der [Vorschlags](/rest/api/searchservice/suggestions)- oder [AutoVervollständigen](/rest/api/searchservice/autocomplete)-API unterstützt.

## <a name="syntax"></a>Syntax

Die folgende EBNF ([Erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik der Funktionen `search.ismatch` und `search.ismatchscoring`:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

### <a name="searchismatch"></a>search.ismatch

Die Funktion `search.ismatch` wertet eine Volltextsuchabfrage als Teil eines Filterausdrucks aus. Die mit der Suchabfrage übereinstimmenden Dokumente werden im Resultset zurückgegeben. Die folgenden Überladungen dieser Funktion sind verfügbar:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Die Parameter werden in der folgenden Tabelle definiert:

| Parametername | Typ | BESCHREIBUNG |
| --- | --- | --- |
| `search` | `Edm.String` | Die Suchabfrage (in der [einfachen](query-simple-syntax.md) oder [vollständigen](query-lucene-syntax.md) Lucene-Abfragesyntax). |
| `searchFields` | `Edm.String` | Durch Trennzeichen getrennte Liste von durchsuchbaren Feldern, in denen gesucht werden soll. Standardmäßig werden alle durchsuchbaren Felder im Index durchsucht. Wenn Sie die [feldbezogene Suche](query-lucene-syntax.md#bkmk_fields) im Parameter `search` verwenden, überschreiben die Feldspezifizierer in der Lucene-Abfrage alle in diesem Parameter angegebenen Felder. |
| `queryType` | `Edm.String` | `'simple'` oder `'full'`, der Standardwert ist `'simple'`. Gibt an, welche Abfragesprache im `search`-Parameter verwendet wurde. |
| `searchMode` | `Edm.String` | `'any'` oder `'all'`, der Standardwert ist `'any'`. Gibt an, ob für beliebige oder alle der Suchbegriffe im Parameter `search` Übereinstimmungen gefunden werden müssen, damit das Dokument in den Ergebnissen zurückgegeben wird. Bei Verwendung der [booleschen Lucene-Operatoren](query-lucene-syntax.md#bkmk_boolean) im Parameter `search` besitzen sie Vorrang vor diesem Parameter. |

Alle der oben aufgeführten Parameter sind gleichwertig mit den entsprechenden [Suchanforderungsparametern in der Search-API](/rest/api/searchservice/search-documents).

Die Funktion `search.ismatch` gibt einen Wert vom Typ `Edm.Boolean` zurück. Dies ermöglicht es Ihnen, ihn mit anderen Filterunterausdrücken unter Verwendung der booleschen [logischen Operatoren](search-query-odata-logical-operators.md) zu kombinieren.

> [!NOTE]
> Die kognitive Azure-Suche unterstützt keine Verwendung von `search.ismatch` oder `search.ismatchscoring` innerhalb von Lambdaausdrücken. Dies bedeutet, dass es nicht möglich ist, Filter über Sammlungen von Objekten zu schreiben, die Volltextsuchtreffer mit strengen Filtertreffern für dasselbe Objekt korrelieren können. Weitere Informationen zu dieser Einschränkung sowie Beispiele finden Sie unter [Problembehandlung von Sammlungsfiltern in der kognitiven Azure-Suche](search-query-troubleshoot-collection-filters.md). Ausführlichere Informationen dazu, warum diese Einschränkung vorhanden ist, finden Sie unter [Grundlegendes zu Sammlungsfiltern in der kognitiven Azure-Suche](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

Die Funktion `search.ismatchscoring` gibt ebenso wie die Funktion `search.ismatch``true` für Dokumente zurück, die mit der als Parameter übergebenen Volltextsuchabfrage übereinstimmen. Der Unterschied zwischen den beiden Funktionen besteht darin, dass die Relevanzbewertung der Dokumente, die mit der Abfrage `search.ismatchscoring` übereinstimmen, in die Gesamtbewertung des Dokuments einfließt, während sich die Dokumentbewertung im Fall von `search.ismatch` nicht ändert. Die folgenden Überladungen dieser Funktion sind verfügbar (mit den gleichen Parametern wie bei `search.ismatch`):

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Die Funktionen `search.ismatch` und `search.ismatchscoring` können beide im selben Filterausdruck verwendet werden.

## <a name="examples"></a>Beispiele

Suche nach Dokumenten mit dem Wort „waterfront“. Diese Filterabfrage ist identisch mit einer [Suchanforderung](/rest/api/searchservice/search-documents) mit `search=waterfront`.

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

Suche nach Dokumenten mit dem Wort „hostel“ und einer Bewertung größer oder gleich 4 oder Dokumenten mit dem Wort „motel“ und einer Bewertung von 5. Beachten Sie, dass diese Anforderung nicht ohne die Funktion `search.ismatchscoring` ausgedrückt werden kann.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

Suche nach Dokumenten ohne das Wort „luxury“:

```odata-filter-expr
    not search.ismatch('luxury')
```

Suche nach Dokumenten mit dem Ausdruck „ocean view“ oder einer Bewertung von 5. Die Abfrage `search.ismatchscoring` wird nur für die Felder `HotelName` und `Rooms/Description` ausgeführt.

Dokumente, die nur der zweiten Klausel der Disjunktion entsprechen, werden ebenfalls zurückgegeben (Hotels mit einem `Rating`-Wert von 5). Um deutlich zu machen, dass diese Dokumente mit keinem der bewerteten Teile des Ausdrucks übereinstimmen, werden sie mit einem Ergebnis von Null zurückgegeben.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

Suche nach Dokumenten, die die Begriffe „hotel“ und „airport“ in einem Abstand von fünf Wörtern voneinander in der Beschreibung des Hotels sowie ein Rauchverbot für mindestens einige Räume enthalten. Diese Abfrage verwendet die [vollständige Lucene-Abfragesprache](query-lucene-syntax.md).

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

Suchen Sie im Feld „Beschreibung“ nach Dokumenten mit einem Wort, das mit den Buchstaben „lux“ beginnt. Diese Abfrage verwendet die [Präfixsuche](query-simple-syntax.md#prefix-queries) in Kombination mit `search.ismatch`.

```odata-filter-expr
    search.ismatch('lux*', 'Description')
```

## <a name="next-steps"></a>Nächste Schritte  

- [Filter in Azure Cognitive Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)