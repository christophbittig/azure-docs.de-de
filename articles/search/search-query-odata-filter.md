---
title: Referenz zu OData-Filtern
titleSuffix: Azure Cognitive Search
description: OData-Sprachreferenz und vollständige Syntax, die zum Erstellen von Filterausdrücken in Azure Cognitive Search-Abfragen verwendet werden.
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
ms.openlocfilehash: 14c3cf748d6e9f5a6d80cf5fcaf8c3bb89daa525
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572564"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData-Syntax von „$filter“ in der kognitiven Azure-Suche

Bei der kognitiven Azure-Suche werden [OData-Filterausdrücke](query-odata-filter-orderby-syntax.md) verwendet, um neben Volltextsuchbegriffen zusätzliche Kriterien auf eine Suchabfrage anzuwenden. In diesem Artikel wird die Syntax von Filtern detailliert beschrieben. Weitere allgemeine Informationen darüber, was Filter sind und wie sie zur Realisierung bestimmter Abfrageszenarien verwendet werden, finden Sie unter [Filter in der kognitiven Azure-Suche](search-filters.md).

## <a name="syntax"></a>Syntax

In der OData-Sprache ist ein Filter ein boolescher Ausdruck, bei dem es sich wiederum um einen von mehreren Ausdruckstypen handeln kann, wie in der folgenden [erweiterten Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) (EBNF) gezeigt:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

Die Typen von booleschen Ausdrücken umfassen Folgendes:

- Sammlungsfilterausdrücke mit `any` oder `all`. Diese wenden Filterkriterien auf Sammlungsfelder an. Weitere Informationen finden Sie unter [OData-Sammlungsoperatoren in der kognitiven Azure-Suche](search-query-odata-collection-operators.md).
- Logische Ausdrücke, die andere boolesche Ausdrücke mithilfe der Operatoren `and`, `or` und `not` kombinieren. Weitere Informationen finden Sie unter [Logische OData-Operatoren in der kognitiven Azure-Suche](search-query-odata-logical-operators.md).
- Vergleichsausdrücke, die Felder oder Bereichsvariablen mithilfe der Operatoren `eq`, `ne`, `gt`, `lt`, `ge` und `le` mit konstanten Werten vergleichen. Weitere Informationen finden Sie unter [OData-Vergleichsoperatoren in der kognitiven Azure-Suche](search-query-odata-comparison-operators.md). Vergleichsausdrücke werden auch verwendet, um Entfernungen zwischen geografischen Koordinaten mithilfe der Funktion `geo.distance` zu vergleichen. Weitere Informationen finden Sie unter [OData-Geofunktionen in der kognitiven Azure-Suche](search-query-odata-geo-spatial-functions.md).
- Die booleschen Literale `true` und `false`. Diese Konstanten können manchmal beim programmgesteuerten Generieren von Filtern nützlich sein, aber ansonsten werden sie in der Praxis eher nicht verwendet.
- Aufrufe von booleschen Funktionen, einschließlich:
  - `geo.intersects`, die prüft, ob sich ein bestimmter Punkt innerhalb eines bestimmten Polygons befindet. Weitere Informationen finden Sie unter [OData-Geofunktionen in der kognitiven Azure-Suche](search-query-odata-geo-spatial-functions.md).
  - `search.in`, die ein Feld oder eine Bereichsvariable mit jedem Wert in einer Liste von Werten vergleicht. Weitere Informationen finden Sie unter [OData-Funktion `search.in` in der kognitiven Azure-Suche](search-query-odata-search-in-function.md).
  - `search.ismatch` und `search.ismatchscoring`, die Volltextsuchvorgänge in einem Filterkontext ausführen. Weitere Informationen finden Sie unter [Funktionen der OData-Volltextsuche in der kognitiven Azure-Suche](search-query-odata-full-text-search-functions.md).
- Feldpfade oder Bereichsvariablen vom Typ `Edm.Boolean`. Wenn beispielsweise Ihr Index ein boolesches Feld namens `IsEnabled` enthält und Sie alle Dokumente zurückgeben möchten, bei denen dieses Feld `true` ist, kann es sich bei Ihrem Filterausdruck einfach um den Namen `IsEnabled` handeln.
- Boolesche Ausdrücke in Klammern. Durch die Verwendung von Klammern können Sie die Reihenfolge von Vorgängen in einem Filter explizit bestimmen. Weitere Informationen zur Standardrangfolge der OData-Operatoren finden Sie im nächsten Abschnitt.

### <a name="operator-precedence-in-filters"></a>Operatorrangfolge in Filtern

Wenn Sie einen Filterausdruck schreiben, ohne die zugehörigen Unterausdrücke in Klammern zu setzen, wird der Ausdruck in der kognitiven Azure-Suche anhand einer Reihe von Regeln für die Operatorrangfolge ausgewertet. Diese Regeln basieren auf den zum Kombinieren von Unterausdrücken verwendeten Operatoren. In der folgenden Tabelle sind die Gruppen von Operatoren von der höchsten bis zur niedrigsten Rangfolge aufgelistet:

| Gruppieren | Operator(en) |
| --- | --- |
| Logische Operatoren | `not` |
| Vergleichsoperatoren | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logische Operatoren | `and` |
| Logische Operatoren | `or` |

Ein Operator, der in der obigen Tabelle eine höhere Rangfolge aufweist, wird „enger“ an seine Operanden „gebunden“ als andere Operatoren. Beispielsweise hat `and` Vorrang vor `or`, und Vergleichsoperatoren haben Vorrang vor diesen beiden. Daher sind die zwei folgenden Ausdrücke gleichwertig:

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

Der Operator `not` weist die höchste Rangfolge von allen auf und hat somit sogar Vorrang vor den Vergleichsoperatoren. Wenn Sie also versuchen, einen Filter wie den folgenden zu schreiben:

```odata-filter-expr
    not Rating gt 5
```

Wird die folgende Fehlermeldung angezeigt:

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

Dieser Fehler tritt auf, weil der Operator nur dem Feld `Rating` (vom Typ `Edm.Int32`) und nicht dem gesamten Vergleichsausdruck zugeordnet ist. Die Lösung besteht darin, den Operanden von `not` in Klammern zu setzen:

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Einschränkungen der Filtergröße

Für die Größe und Komplexität der Filterausdrücke, die Sie an die kognitive Azure-Suche senden können, gelten Grenzwerte. Die Grenzwerte basieren ungefähr auf der Anzahl von Klauseln in Ihrem Filterausdruck. Als gute Orientierungshilfe gilt, dass Sie bei Hunderten von Klauseln Gefahr laufen, den Grenzwert zu überschreiten. Wir empfehlen Ihnen, Ihre Anwendung so zu entwerfen, dass sie keine Filter unbegrenzter Größe generiert.

> [!TIP]
> Durch die Verwendung der [Funktion `search.in`](search-query-odata-search-in-function.md) anstelle von langen Disjunktionen von Gleichheitsvergleichen können Sie die Grenzwerte von Filterklauseln umgehen, da ein Funktionsaufruf als eine einzige Klausel zählt.

## <a name="examples"></a>Beispiele

Suche nach allen Hotels mit mindestens einem Zimmer mit einem Basistarif unter 200 $ mit einer Bewertung von mindestens 4:

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

Suche nach allen Hotels außer „Sea View Motel“, die seit 2010 renoviert wurden:

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

Suche nach allen Hotels, die seit 2010 renoviert wurden. Der DateTime-Literalwert enthält Zeitzoneninformationen für Pacific Normalzeit:  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

Suche nach allen Hotels, bei denen die Parkgebühren im Preis enthalten sind und in denen alle Zimmer Nichtraucherzimmer sind:

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- ODER -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

Suche nach allen Hotels, die der Kategorie „Luxury“ angehören oder bei denen die Parkgebühren im Preis enthalten sind, mit einer Bewertung von 5:  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

Suche nach allen Hotels mit dem Tag „wifi“ in mindestens einem Zimmer (wobei für jedes Zimmer Tags in einem Feld vom Typ `Collection(Edm.String)` gespeichert sind):  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

Suche nach allen Hotels mit beliebigen Zimmern:  

```odata-filter-expr
    $filter=Rooms/any()
```

Suche nach allen Hotels, die keine Zimmer haben:

```odata-filter-expr
    $filter=not Rooms/any()
```

Suche nach allen Hotels in einem Umkreis von 10 Kilometern von einem angegebenen Referenzpunkt (wobei `Location` ein Feld vom Typ `Edm.GeographyPoint` ist):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

Suche nach allen Hotels in einem bestimmten Anzeigebereich, der als Polygon definiert ist (wobei `Location` ein Feld vom Typ „Edm.GeographyPoint“ ist). Das Polygon muss geschlossen sein, d. h. der erste und der letzte Punkt müssen identisch sein. Darüber hinaus [müssen die Punkte gegen den Uhrzeigersinn aufgelistet sein](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

Suche nach allen Hotels, bei denen das Feld „Description“ gleich „null“ ist. Das Feld ist „null“, wenn es entweder nicht festgelegt oder explizit auf „null“ festgelegt wurde:  

```odata-filter-expr
    $filter=Description eq null
```

Suche nach allen Hotels, deren Name „Sea View motel“ oder „Budget hotel“ entspricht. Diese Ausdrücke enthalten Leerzeichen, und ein Leerzeichen ist ein Standardtrennzeichen. Sie können ein alternatives Trennzeichen in einfachen Anführungszeichen als dritten Zeichenfolgenparameter angeben:  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Suche nach allen Hotels, deren Name „Sea View motel“ oder „Budget hotel“ entspricht (getrennt durch „|“):  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Suche nach allen Hotels, bei denen alle Zimmer über das Tag „wifi“ oder „tub“ verfügen:

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

Suchen Sie eine Übereinstimmung mit Ausdrücken innerhalb einer Sammlung, z.B. „beheizte Handtuchhalter“ oder „Haartrockner inklusive“ in Tags.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Suche nach Dokumenten mit dem Wort „waterfront“. Diese Filterabfrage ist identisch mit einer [Suchanforderung](/rest/api/searchservice/search-documents) mit `search=waterfront`.

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

Suche nach Dokumenten mit dem Wort „hostel“ und einer Bewertung größer oder gleich 4 oder Dokumenten mit dem Wort „motel“ und einer Bewertung von 5. Diese Anforderung kann nicht ohne die Funktion `search.ismatchscoring` ausgedrückt werden, weil sie Volltextsuchvorgänge mit Filtervorgängen mithilfe von `or` kombiniert.

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Suche nach Dokumenten ohne das Wort „luxury“:

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

Suche nach Dokumenten mit dem Ausdruck „ocean view“ oder einer Bewertung von 5. Die Abfrage `search.ismatchscoring` wird nur für die Felder `HotelName` und `Description` ausgeführt. Dokumente, die nur der zweiten Klausel der Disjunktion entsprechen, werden ebenfalls zurückgegeben (Hotels mit einem `Rating`-Wert von 5). Diese Dokumente werden mit einer Bewertung von Null zurückgegeben, um zu verdeutlichen, dass sie mit keinem der bewerteten Teile des Ausdrucks übereinstimmen.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

Suche nach Hotels, bei denen die Begriffe „hotel“ und „airport“ in der Beschreibung nicht mehr als fünf Wörter voneinander entfernt sind und in denen alle Zimmer Nichtraucherzimmer sind. Diese Abfrage verwendet die [vollständige Lucene-Abfragesprache](query-lucene-syntax.md).

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

Suchen Sie im Feld „Beschreibung“ nach Dokumenten mit einem Wort, das mit den Buchstaben „lux“ beginnt. Diese Abfrage verwendet die [Präfixsuche](query-simple-syntax.md#prefix-queries) in Kombination mit `search.ismatch`.

```odata-filter-expr
    $filter=search.ismatch('lux*', 'Description')
```

## <a name="next-steps"></a>Nächste Schritte  

- [Filter in Azure Cognitive Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)