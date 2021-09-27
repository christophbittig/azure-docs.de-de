---
title: Referenz zu OData-Vergleichsoperatoren
titleSuffix: Azure Cognitive Search
description: Syntax und Referenzdokumentation für die Verwendung der OData-Vergleichsoperatoren (eq, ne, gt, lt, ge, le) in Azure Cognitive Search-Abfragen.
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
ms.openlocfilehash: 804e974196b62b6dfed9d4bd64b8dfe0cc107faf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591518"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData-Vergleichsoperatoren in der kognitiven Azure-Suche: `eq`, `ne`, `gt`, `lt`, `ge` und `le`

Der grundlegendste Vorgang in einem [OData-Filterausdruck](query-odata-filter-orderby-syntax.md) in der kognitiven Azure-Suche ist der Vergleich eines Felds mit einem bestimmten Wert. Es sind zwei Arten von Vergleich möglich: Gleichheitsvergleich und Bereichsvergleich. Sie können die folgenden Operatoren verwenden, um ein Feld mit einem konstanten Wert zu vergleichen:

Gleichheitsoperatoren:

- `eq`: Testet, ob ein Feld **gleich** einem konstanten Wert ist.
- `ne`: Testet, ob ein Feld **ungleich** einem konstanten Wert ist.

Bereichsoperatoren:

- `gt`: Testet, ob ein Feld **größer als** ein konstanter Wert ist.
- `lt`: Testet, ob ein Feld **kleiner als** ein konstanter Wert ist.
- `ge`: Testet, ob ein Feld **größer oder gleich** einem konstanten Wert ist.
- `le`: Testet, ob ein Feld **kleiner oder gleich** einem konstanten Wert ist.

Sie können die Bereichsoperatoren in Kombination mit den [logischen Operatoren](search-query-odata-logical-operators.md) verwenden, um zu testen, ob ein Feld innerhalb eines bestimmten Wertebereichs liegt. [Beispiele](#examples) dazu finden Sie in den Abschnitten weiter unten in diesem Artikel.

> [!NOTE]
> Wenn Sie dies vorziehen, können Sie den konstanten Wert auf der linken Seite des Operators und den Feldnamen auf der rechten Seite platzieren. Bei Bereichsoperatoren wird die Bedeutung des Vergleichs umgekehrt. Wenn sich der konstante Wert beispielsweise auf der linken Seite befindet, würde `gt` überprüfen, ob der konstante Wert größer als das Feld ist. Sie können die Vergleichsoperatoren auch verwenden, um das Ergebnis einer Funktion (beispielsweise `geo.distance`) mit einem Wert zu vergleichen. Für boolesche Funktionen wie `search.ismatch` ist der Vergleich des Ergebnisses mit `true` oder `false` optional.

## <a name="syntax"></a>Syntax

Die folgende EBNF ([Erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik eines OData-Ausdrucks, der die Vergleichsoperatoren verwendet.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

Es gibt zwei Formen von Vergleichsausdrücken. Der einzige Unterschied zwischen ihnen besteht darin, ob die Konstante auf der linken oder rechten Seite des Operators platziert wird. Der Ausdruck auf der anderen Seite des Operators muss eine **Variable** oder ein Funktionsaufruf sein. Eine Variable kann entweder ein Feldname oder eine Bereichsvariable im Fall eines [Lambdaausdrucks](search-query-odata-collection-operators.md) sein.

## <a name="data-types-for-comparisons"></a>Datentypen für Vergleiche

Die Datentypen auf beiden Seiten eines Vergleichsoperators müssen kompatibel sein. Wenn die linke Seite beispielsweise ein Feld vom Typ `Edm.DateTimeOffset` ist, dann muss die rechte Seite eine Datum/Uhrzeit-Konstante sein. Numerische Datentypen sind flexibler. Sie können Variablen und Funktionen eines beliebigen numerischen Typs mit Konstanten eines beliebigen anderen numerischen Typs vergleichen. Dabei gelten wenige Einschränkungen, die in der folgenden Tabelle beschrieben werden.

| Variable oder Funktionstyp | Typ des konstanten Werts | Einschränkungen |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Vergleich unterliegt [besonderen Regeln für `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstante wird in `Edm.Double` konvertiert, was zu einem Genauigkeitsverlust bei großen Werten führt |
| `Edm.Double` | `Edm.Int32` | – |
| `Edm.Int64` | `Edm.Double` | Vergleiche mit `NaN`, `-INF` oder `INF` sind unzulässig |
| `Edm.Int64` | `Edm.Int64` | – |
| `Edm.Int64` | `Edm.Int32` | Konstante wird vor dem Vergleich in `Edm.Int64` konvertiert |
| `Edm.Int32` | `Edm.Double` | Vergleiche mit `NaN`, `-INF` oder `INF` sind unzulässig |
| `Edm.Int32` | `Edm.Int64` | – |
| `Edm.Int32` | `Edm.Int32` | – |

Für Vergleiche, die unzulässig sind (z. B. Vergleich eines Felds vom Typ `Edm.Int64` mit `NaN`), gibt die REST-API von Azure Cognitive Search die Fehlermeldung „HTTP 400: Ungültige Anforderung“ zurück.

> [!IMPORTANT]
> Auch wenn numerische Typvergleiche flexibel sind, wird dringend empfohlen, Vergleiche in Filtern zu schreiben, sodass der konstante Wert vom gleichen Datentyp ist wie die Variable oder Funktion, mit der er verglichen wird. Dies ist besonders wichtig beim Mischen von Gleitkomma- und Integerwerten, bei denen implizite Konvertierungen möglich sind, die zu einem Genauigkeitsverlust führen.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Sonderfälle für `null` und `NaN`

Bei der Verwendung von Vergleichsoperatoren muss berücksichtigt werden, dass alle Nicht-Sammlungsfelder in der kognitiven Azure-Suche potenziell `null` sein können. Die folgende Tabelle zeigt alle möglichen Ergebnisse für einen Vergleichsausdruck, bei dem jede Seite `null` sein kann:

| Operator | Ergebnis, wenn nur das Feld oder die Variable `null` ist | Ergebnis, wenn nur die Konstante `null` ist | Ergebnis, wenn sowohl das Feld oder die Variable als auch die Konstante `null` sind |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Ungültige Anforderung | HTTP 400: Ungültige Anforderung |
| `lt` | `false` | HTTP 400: Ungültige Anforderung | HTTP 400: Ungültige Anforderung |
| `ge` | `false` | HTTP 400: Ungültige Anforderung | HTTP 400: Ungültige Anforderung |
| `le` | `false` | HTTP 400: Ungültige Anforderung | HTTP 400: Ungültige Anforderung |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Zusammenfassend lässt sich sagen, dass `null` nur mit sich selbst gleich ist und nicht kleiner oder größer als jeder andere Wert ist.

Wenn Ihr Index Felder vom Typ `Edm.Double` aufweist und Sie `NaN`-Werte in diese Felder hochladen, müssen Sie dies beim Schreiben von Filtern berücksichtigen. Bei der kognitiven Azure-Suche wird der IEEE 754-Standard für die Verarbeitung von `NaN`-Werten implementiert. Vergleiche mit solchen Werten führen zu nicht eindeutigen Ergebnissen, wie in der folgenden Tabelle gezeigt.

| Operator | Ergebnis, wenn mindestens einer der Operanden `NaN` ist |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Zusammenfassend lässt sich sagen, dass `NaN` mit keinem Wert gleich ist, auch nicht mit sich selbst.

### <a name="comparing-geo-spatial-data"></a>Vergleichen von räumlichen Daten

Sie können ein Feld vom Typ `Edm.GeographyPoint` nicht direkt mit einem konstanten Wert vergleichen, aber Sie können die `geo.distance`-Funktion verwenden. Diese Funktion gibt einen Wert vom Typ `Edm.Double` zurück, sodass Sie ihn mit einer numerischen Konstante vergleichen können, die basierend auf dem Abstand von konstanten räumlichen Koordinaten gefiltert werden soll. Weitere Informationen finden Sie in den folgenden [Beispielen](#examples).

### <a name="comparing-string-data"></a>Vergleichen von Zeichenfolgendaten

Zeichenfolgen können in Filtern mit den Operatoren `eq` und `ne` verglichen werden, um genaue Übereinstimmungen zu ermitteln. Bei diesen Vergleichen wird die Groß-/Kleinschreibung beachtet.

## <a name="examples"></a>Beispiele

Abgleichen von Dokumenten, bei denen das `Rating`-Feld zwischen 3 und 5 (einschließlich) liegt:

```text
Rating ge 3 and Rating le 5
```

Abgleichen von Dokumenten, bei denen das `Location`-Feld weniger als 2 Kilometer vom angegebenen Breitengrad und Längengrad entfernt ist:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

Abgleichen von Dokumenten, bei denen das Feld `LastRenovationDate` größer oder gleich dem 1. Januar 2015, 24 Uhr UTC ist:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

Abgleichen von Dokumenten, bei denen das `Details/Sku`-Feld nicht `null` ist:

```text
Details/Sku ne null
```

Abgleichen von Dokumenten für Hotels, in denen mindestens ein Raum den Typ „Deluxe Room“ (Luxuszimmer) aufweist, wobei die Zeichenfolge des `Rooms/Type`-Felds genau mit dem Filter übereinstimmt:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>Nächste Schritte  

- [Filter in Azure Cognitive Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)