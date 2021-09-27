---
title: Referenz zu OData-Sammlungsoperatoren
titleSuffix: Azure Cognitive Search
description: Beim Erstellen von Filterausdrücken in Azure Cognitive Search-Abfragen verwenden Sie die Operatoren „any“ und „all“ in Lambda-Ausdrücken, wenn der Filter in einer Sammlung oder einem komplexen Sammlungsfeld vorhanden ist.
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
ms.openlocfilehash: 7fc46da1ee5d22eb8774dc7c7533d123c6415e88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673500"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData-Sammlungsoperatoren in Azure Cognitive Search: `any` und `all`

Beim Schreiben eines [OData-Filterausdrucks](query-odata-filter-orderby-syntax.md), der mit Azure Cognitive Search verwendet werden soll, ist es oft hilfreich, nach Sammlungsfeldern zu filtern. Dies können dies mit den Operatoren `any` und `all` erreichen.

## <a name="syntax"></a>Syntax

Die folgende EBNF ([Erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiert die Grammatik eines OData-Ausdrucks, der `any` oder `all` verwendet.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

Es gibt drei Formen von Ausdrücken, die Sammlungen filtern.

- Die ersten beiden iterieren über ein Sammlungsfeld und wenden ein Prädikat in Form eines Lambdaausdrucks auf jedes Element der Sammlung an.
  - Ein Ausdruck, der `all` verwendet, gibt `true` zurück, wenn das Prädikat für jedes Element der Sammlung TRUE ist.
  - Ein Ausdruck, der `any` verwendet, gibt `true` zurück, wenn das Prädikat für mindestens ein Element der Sammlung TRUE ist.
- Die dritte Form von Sammlungsfilter verwendet `any` ohne einen Lambdaausdruck, um zu testen, ob ein Sammlungsfeld leer ist. Wenn die Sammlung über Elemente verfügt, wird `true` zurückgegeben. Wenn die Sammlung leer ist, wird `false` zurückgegeben.

Ein **Lambdaausdruck** in einem Sammlungsfilter ist wie der Textkörper einer Schleife in einer Programmiersprache. Er definiert eine Variable, die als **Bereichsvariable** bezeichnet wird und das aktuelle Element der Sammlung während der Iteration enthält. Er definiert auch einen weiteren booleschen Ausdruck, der die Filterkriterien darstellt, die auf die Bereichsvariable für jedes Element der Sammlung angewendet werden sollen.

## <a name="examples"></a>Beispiele

Abgleichen von Dokumenten, deren `tags`-Feld die genaue Zeichenfolge „wifi“ enthält:

```text
tags/any(t: t eq 'wifi')
```

Abgleichen von Dokumenten, in denen jedes Element des `ratings`-Felds zwischen 3 und 5 (einschließlich) liegt:

```text
ratings/all(r: r ge 3 and r le 5)
```

Abgleichen von Dokumenten, in denen jede der Geokoordinaten im `locations`-Feld im angegebenen Polygon liegt:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Abgleichen von Dokumenten, bei denen das `rooms`-Feld leer ist:

```text
not rooms/any()
```

Abgleichen von Dokumenten, in denen das `rooms/amenities`-Feld für alle Zimmer „tv“ enthält und `rooms/baseRate` kleiner als 100 ist:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Einschränkungen

Nicht jedes Merkmal von Filterausdrücken ist innerhalb des Textkörpers eines Lambdaausdrucks verfügbar. Die unterschiedlichen Einschränkungen hängen vom Datentyp des Sammlungsfelds ab, nach dem Sie filtern möchten. Die Einschränkungen werden in der folgenden Tabelle zusammengefasst.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Weitere Informationen zu diesen Einschränkungen sowie Beispiele finden Sie unter [Problembehandlung von Sammlungsfiltern in Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Ausführlichere Informationen dazu, warum diese Einschränkungen vorhanden sind, finden Sie unter [Grundlegendes zu Sammlungsfiltern in Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nächste Schritte  

- [Filter in Azure Cognitive Search](search-filters.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)