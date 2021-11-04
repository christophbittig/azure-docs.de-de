---
title: Problembehandlung von OData-Sammlungsfiltern
titleSuffix: Azure Cognitive Search
description: Erfahren Sie mehr über die Ansätze zum Beheben von OData-Sammlungsfilterfehlern in Azure Cognitive Search-Abfragen.
author: bevloh
ms.author: beloh
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 0b80869f3f2cf7754a7dbca8882fa22a38539f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048062"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Problembehandlung von OData-Sammlungsfiltern in der kognitiven Azure-Suche

Um in der kognitiven Azure-Suche nach Sammlungsfeldern zu [filtern](query-odata-filter-orderby-syntax.md), können Sie die [Operatoren `any` und `all`](search-query-odata-collection-operators.md) zusammen mit **Lambdaausdrücken** verwenden. Ein Lambdaausdruck ist ein Unterfilter, der auf jedes Element einer Sammlung angewendet wird.

In einem Lambdaausdruck sind nicht alle Funktionen von Filterausdrücken verfügbar. Die jeweils verfügbaren Funktionen hängen vom Datentyp des Sammlungsfelds ab, nach dem Sie filtern möchten. Wenn Sie versuchen, eine Funktion in einem Lambdaausdruck zu verwenden, die in diesem Kontext nicht unterstützt wird, kann dies zu einem Fehler führen. Wenn solche Fehler beim Versuch auftreten, einen komplexen Filter über Sammlungsfelder zu schreiben, kann Ihnen dieser Artikel dabei helfen, das Problem zu beheben.

## <a name="common-collection-filter-errors"></a>Häufige Sammlungsfilterfehler

In der folgenden Tabelle werden Fehler aufgeführt, die auftreten können, wenn Sie versuchen, einen Sammlungsfilter auszuführen. Diese Fehler treten auf, wenn Sie eine in einem Lambdaausdruck nicht unterstützte Funktion von Filterausdrücken verwenden. Jede Fehlermeldung enthält eine Anleitung dazu, wie Sie Ihren Filter neu schreiben können, um den Fehler zu vermeiden. Die Tabelle enthält auch einen Link zum relevanten Abschnitt dieses Artikels, der weitere Informationen zur Vermeidung des Fehlers bietet.

| Fehlermeldung | Situation | Weitere Informationen finden Sie unter |
| --- | --- | --- |
| Die Funktion „ismatch“ enthält keine Parameter, die an die Bereichsvariable „s“ gebunden sind. In Lambdaausdrücken („any“ oder „all“) werden nur gebundene Feldverweise unterstützt. Ändern Sie den Filter so, dass sich die Funktion „ismatch“ außerhalb des Lambdaausdrucks befindet, und versuchen Sie es erneut. | Verwenden von `search.ismatch` oder `search.ismatchscoring` in einem Lambdaausdruck | [Regeln für das Filtern komplexer Sammlungen](#bkmk_complex) |
| Ungültiger Lambdaausdruck. Es wurde ein Test auf Gleichheit oder Ungleichheit gefunden, bei dem das Gegenteil in einem Lambdaausdruck erwartet wurde, der ein Feld vom Typ „Collection(Edm.String)“ durchläuft. Verwenden Sie für „any“ Ausdrücke der Form „x eq y“ oder „search.in(...)“. Verwenden Sie für „all“ Ausdrücke der Form „x ne y“, „not (x eq y)“ oder „not search.in(...)“. | Filtern nach einem Feld vom Typ `Collection(Edm.String)` | [Regeln für das Filtern von Zeichenfolgensammlungen](#bkmk_strings) |
| Ungültiger Lambdaausdruck. Es wurde eine nicht unterstützte Form eines komplexen booleschen Ausdrucks gefunden. Verwenden Sie für „any“ Ausdrücke, die „ORs von ANDs“ sind, auch bekannt als disjunktive Normalform. Zum Beispiel: `(a and b) or (c and d)`, wobei a, b, c und d Vergleichs- oder Gleichheitsunterausdrücke sind. Verwenden Sie für „all“ Ausdrücke, die „ANDs von ORs“ sind, auch bekannt als konjunktive Normalform. Zum Beispiel: `(a or b) and (c or d)`, wobei a, b, c und d Vergleichs- oder Ungleichheitsunterausdrücke sind. Beispiele für Vergleichsausdrücke: „x gt 5“, „x le 2“. Beispiel für einen Gleichheitsausdruck: „x eq 5“. Beispiel für einen Ungleichheitsausdruck: „x ne 5“. | Filtern nach Feldern vom Typ `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)` oder `Collection(Edm.Int64)` | [Regeln für das Filtern vergleichbarer Sammlungen](#bkmk_comparables) |
| Ungültiger Lambdaausdruck. Es wurde eine nicht unterstützte Verwendung von „geo.distance()“ oder „geo.intersects()“ in einem Lambdaausdruck gefunden, der ein Feld vom Typ „Collection(Edm.GeographyPoint)“ durchläuft. Stellen Sie für „any“ sicher, dass Sie „geo.distance()“ mit dem Operator „lt“ oder „le“ vergleichen und die Verwendung von „geo.intersects()“ nicht negiert wird. Stellen Sie für „all“ sicher, dass Sie „geo.distance()“ mit dem Operator „gt“ oder „ge“ vergleichen und die Verwendung von „geo.intersects()“ negiert wird. | Filtern nach einem Feld vom Typ `Collection(Edm.GeographyPoint)` | [Regeln für das Filtern von GeographyPoint-Sammlungen](#bkmk_geopoints) |
| Ungültiger Lambdaausdruck. Komplexe boolesche Ausdrücke werden in Lambdaausdrücken, die Felder vom Typ „Collection(Edm.GeographyPoint)“ durchlaufen, nicht unterstützt. Verknüpfen Sie bei Verwendung von „any“ Unterausdrücke mit „or“. „Und“ wird nicht unterstützt. Verknüpfen Sie bei Verwendung von „all“ Unterausdrücke mit „and“. „Or“ wird nicht unterstützt. | Filtern nach Feldern vom Typ `Collection(Edm.String)` oder `Collection(Edm.GeographyPoint)` | [Regeln für das Filtern von Zeichenfolgensammlungen](#bkmk_strings) <br/><br/> [Regeln für das Filtern von GeographyPoint-Sammlungen](#bkmk_geopoints) |
| Ungültiger Lambdaausdruck. Es wurde ein Vergleichsoperator gefunden („lt“, „le“, „gt“ oder „ge“). Es sind nur Gleichheitsoperatoren in Lambdaausdrücken zulässig, die Felder vom Typ „Collection(Edm.String)“ durchlaufen. Verwenden Sie für „any“ Ausdrücke der Form „x eq y“. Verwenden Sie für „all“ Ausdrücke der Form „x ne y“ oder „not (x eq y)“. | Filtern nach einem Feld vom Typ `Collection(Edm.String)` | [Regeln für das Filtern von Zeichenfolgensammlungen](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Schreiben gültiger Sammlungsfilter

Die Regeln für das Schreiben gültiger Sammlungsfilter sind für jeden Datentyp unterschiedlich. In den folgenden Abschnitten werden die Regeln beschrieben, indem anhand von Beispielen aufgezeigt wird, welche Filterfunktionen unterstützt werden und welche nicht:

- [Regeln für das Filtern von Zeichenfolgensammlungen](#bkmk_strings)
- [Regeln für das Filtern boolescher Sammlungen](#bkmk_bools)
- [Regeln für das Filtern von GeographyPoint-Sammlungen](#bkmk_geopoints)
- [Regeln für das Filtern vergleichbarer Sammlungen](#bkmk_comparables)
- [Regeln für das Filtern komplexer Sammlungen](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regeln für das Filtern von Zeichenfolgensammlungen

In Lambdaausdrücken für Zeichenfolgensammlungen können nur die Vergleichsoperatoren `eq` und `ne` verwendet werden.

> [!NOTE]
> Die Operatoren `lt`/`le`/`gt`/`ge` für Zeichenfolgen werden von der kognitiven Azure-Suche nicht unterstützt, weder innerhalb noch außerhalb eines Lambdaausdrucks.

Der Textkörper eines `any` kann nur auf Gleichheit überprüfen, während der Textkörper eines `all` nur auf Ungleichheit überprüfen kann.

Es ist auch möglich, mehrere Ausdrücke über `or` im Textkörper eines `any` und über `and` im Textkörper eines `all` zu kombinieren. Da die Funktion `search.in` dem Kombinieren von Gleichheitsüberprüfungen mit `or` entspricht, ist sie auch im Textkörper eines `any` zulässig. Entsprechend ist `not search.in` im Textkörper eines `all` zulässig.

Beispielsweise sind die folgenden Ausdrücke zulässig:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Dagegen sind die folgenden Ausdrücke unzulässig:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regeln für das Filtern boolescher Sammlungen

Der Typ `Edm.Boolean` unterstützt nur die Operatoren `eq` und `ne`. Daher ist es nicht sinnvoll, die Kombination von Klauseln zuzulassen, die die gleiche Bereichsvariable mit `and`/`or` überprüfen, da dies immer zu Tautologien oder Widersprüchen führen würde.

Nachfolgend finden Sie einige Beispiele für zulässige Filter für boolesche Sammlungen:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Im Gegensatz zu Zeichenfolgensammlungen gelten für boolesche Sammlungen keine Einschränkungen, welcher Operator in welchem Typ von Lambdaausdruck verwendet werden kann. `eq` und `ne` können im Textkörper von `any` oder `all` verwendet werden.

Die folgenden Ausdrücke sind beispielsweise für boolesche Sammlungen unzulässig:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regeln für das Filtern von GeographyPoint-Sammlungen

In einer Sammlung enthaltene Werte vom Typ `Edm.GeographyPoint` können nicht direkt miteinander verglichen werden. Sie müssen stattdessen als Parameter für die Funktionen `geo.distance` und `geo.intersects` verwendet werden. Die Funktion `geo.distance` muss wiederum mit einem Abstandswert mit einem Vergleichsoperator (`lt`, `le`, `gt` oder `ge`) verglichen werden. Diese Regeln gelten auch für Nicht-Sammlungsfelder vom Typ „Edm.GeographyPoint“.

Wie für Zeichenfolgensammlungen gelten auch für `Edm.GeographyPoint`-Sammlungen einige Regeln für die Verwendung und Kombination von Geofunktionen in den verschiedenen Typen von Lambdaausdrücken:

- Welche Vergleichsoperatoren Sie mit der Funktion `geo.distance` verwenden können, hängt vom Typ des Lambdaausdrucks ab. Für `any` können Sie nur `lt` oder `le` verwenden. Für `all` können Sie nur `gt` oder `ge` verwenden. Sie können Ausdrücke in Verbindung mit `geo.distance` negieren, aber Sie müssen den Vergleichsoperator ändern (`geo.distance(...) lt x` wird zu `not (geo.distance(...) ge x)` und `geo.distance(...) le x` zu `not (geo.distance(...) gt x)`).
- Im Textkörper eines `all` muss die Funktion `geo.intersects` negiert werden. Im Textkörper eines `any` darf die Funktion `geo.intersects` dagegen nicht negiert werden.
- Im Textkörper eines `any` können Geoausdrücke mit `or` kombiniert werden. Im Textkörper eines `all` können diese Ausdrücke mit `and` kombiniert werden.

Die oben genannten Einschränkungen bestehen aus ähnlichen Gründen wie die für Zeichenfolgensammlungen geltenden Einschränkungen in Bezug auf Gleichheit/Ungleichheit. Unter [Grundlegendes zu OData-Sammlungsfiltern in der kognitiven Azure-Suche](search-query-understand-collection-filters.md) werden diese Gründe genauer erläutert.

Nachfolgend finden Sie einige Beispiele für zulässige Filter für `Edm.GeographyPoint`-Sammlungen:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Die folgenden Ausdrücke sind beispielsweise für `Edm.GeographyPoint`-Sammlungen unzulässig:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regeln für das Filtern vergleichbarer Sammlungen

Dieser Abschnitt gilt für alle folgenden Datentypen:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typen wie `Edm.Int32` und `Edm.DateTimeOffset` unterstützen alle sechs Vergleichsoperatoren: `eq`, `ne`, `lt`, `le`, `gt` und `ge`. Lambdaausdrücke über Sammlungen dieser Typen können einfache Ausdrücke mit einem dieser Operatoren enthalten. Dies gilt für `any` und `all`. Beispielsweise sind die folgenden Filter zulässig:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Es gibt jedoch Einschränkungen in Bezug darauf, wie solche Vergleichsausdrücke zu komplexeren Ausdrücken in einem Lambdaausdruck kombiniert werden können:

- Regeln für `any`:
  - Einfache Ungleichheitsausdrücke können nicht sinnvoll mit anderen Ausdrücken kombiniert werden. Beispielsweise ist der folgende Ausdruck zulässig:
    - `ratings/any(r: r ne 5)`

    Der folgende Ausdruck ist jedoch nicht zulässig:
    - `ratings/any(r: r ne 5 and r gt 2)`

    Und der folgende Ausdruck ist zwar zulässig, aber nicht sinnvoll, weil sich die Bedingungen überschneiden:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Einfache Vergleichsausdrücke in Verbindung mit `eq`, `lt`, `le`, `gt` oder `ge` können mit `and`/`or` kombiniert werden. Beispiel:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Mit `and` (Konjunktionen) kombinierte Vergleichsausdrücke können mit `or` weiter kombiniert werden. Diese Form wird in boolescher Logik als „[disjunktive Normalform](https://en.wikipedia.org/wiki/Disjunctive_normal_form)“ (DNF) bezeichnet. Beispiel:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regeln für `all`:
  - Einfache Gleichheitsausdrücke können nicht sinnvoll mit anderen Ausdrücken kombiniert werden. Beispielsweise ist der folgende Ausdruck zulässig:
    - `ratings/all(r: r eq 5)`

    Der folgende Ausdruck ist jedoch nicht zulässig:
    - `ratings/all(r: r eq 5 or r le 2)`

    Und der folgende Ausdruck ist zwar zulässig, aber nicht sinnvoll, weil sich die Bedingungen überschneiden:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Einfache Vergleichsausdrücke in Verbindung mit `ne`, `lt`, `le`, `gt` oder `ge` können mit `and`/`or` kombiniert werden. Beispiel:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Mit `or` (Disjunktionen) kombinierte Vergleichsausdrücke können mit `and` weiter kombiniert werden. Diese Form wird in boolescher Logik als „[konjunktive Normalform](https://en.wikipedia.org/wiki/Conjunctive_normal_form)“ (KNF) bezeichnet. Zum Beispiel:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regeln für das Filtern komplexer Sammlungen

Lambdaausdrücke über komplexe Sammlungen unterstützen eine viel flexiblere Syntax als Lambdaausdrücke über Sammlungen einfacher Typen. Sie können jedes Filterkonstrukt innerhalb eines Lambdaausdrucks verwenden, das Sie außerhalb eines solchen verwenden können, mit nur zwei Ausnahmen.

Erstens: Die Funktionen `search.ismatch` und `search.ismatchscoring` werden in Lambdaausdrücken nicht unterstützt. Weitere Informationen finden Sie unter [Grundlegendes zu OData-Sammlungsfiltern in der kognitiven Azure-Suche](search-query-understand-collection-filters.md).

Zweitens: Das Verweisen auf Felder, die NICHT an die Bereichsvariable *gebunden* sind (so genannte *freie Variablen*), ist unzulässig. Betrachten Sie beispielsweise die folgenden zwei äquivalenten OData-Filterausdrücke:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Der erste Ausdruck ist zulässig, während die zweite Form abgelehnt wird, weil `details/margin` nicht an die Bereichsvariable `s` gebunden ist.

Diese Regel gilt auch für Ausdrücke mit Variablen, die in einem äußeren Bereich gebunden sind. Diese Variablen sind frei in Bezug auf den Bereich, in dem sie angezeigt werden. So ist im folgenden Beispiel der erste Ausdruck zulässig, während der zweite äquivalente Ausdruck nicht zulässig ist, weil `s/name` in Bezug auf den Bereich der Bereichsvariable `a` frei ist:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Diese Einschränkung sollte in der Praxis kein Problem darstellen, weil immer die Möglichkeit besteht, Filter so zu konstruieren, dass Lambdaausdrücke nur gebundene Variablen enthalten.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Kurzübersicht für Sammlungsfilterregeln

In der folgenden Tabelle werden die Regeln für das Konstruieren gültiger Filter für jeden Sammlungsdatentyp zusammengefasst.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Beispiele für das Konstruieren gültiger Filter für jeden Anwendungsfall finden Sie unter [Schreiben gültiger Sammlungsfilter](#bkmk_examples).

Wenn Sie häufig Filter schreiben und das Verstehen der Regeln der ersten Prinzipien Ihnen mehr helfen würde, als sie nur auswendig zu lernen, lesen Sie [Grundlegendes zu OData-Sammlungsfiltern in der kognitiven Azure-Suche](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nächste Schritte  

- [Grundlegendes zu OData-Sammlungsfiltern in der kognitiven Azure-Suche](search-query-understand-collection-filters.md)
- [Filter in Azure Cognitive Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)