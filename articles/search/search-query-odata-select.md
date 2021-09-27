---
title: OData-Referenz zu „$select“
titleSuffix: Azure Cognitive Search
description: Syntax und Sprachreferenz für die explizite Auswahl von Feldern, die in den Suchergebnissen von Azure Cognitive Search-Abfragen zurückgegeben werden sollen.
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
ms.openlocfilehash: b399621b2b19368ccc2057e4bf8dffae6b1e24a6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576040"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData-Syntax von „$select“ in der kognitiven Azure-Suche

 Sie können den [OData-Parameter **$select**](query-odata-filter-orderby-syntax.md) verwenden, um die in die Suchergebnisse der kognitiven Azure-Suche einzubeziehenden Felder auszuwählen. In diesem Artikel wird die Syntax von **$select** detailliert beschrieben. Weitere allgemeine Informationen zur Verwendung von **$select** beim Darstellen von Suchergebnissen finden Sie unter [Arbeiten mit Suchergebnissen in der kognitiven Azure-Suche](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Der Parameter **$select** bestimmt, welche Felder für jedes Dokument im Abfrageresultset zurückgegeben werden. Die folgende [erweiterte Backus-Naur-Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) (EBNF) definiert die Grammatik des Parameters **$select**:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Ein interaktives Syntaxdiagramm ist ebenfalls verfügbar:

> [!div class="nextstepaction"]
> [OData-Syntaxdiagramm für Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Die vollständige EBNF finden Sie in der [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md).

Der Parameter **$select** ist in zwei Formen vorhanden:

1. Ein einzelnes Sternchen (`*`) gibt an, dass alle abrufbaren Felder zurückgegeben werden sollen.
1. Eine durch Trennzeichen getrennte Liste von Feldpfaden identifiziert, welche Felder zurückgegeben werden sollen.

Wenn Sie die zweite Form verwenden, können Sie nur abrufbare Felder in der Liste angeben.

Wenn Sie ein komplexes Feld auflisten, ohne die zugehörigen Unterfelder explizit anzugeben, werden alle abrufbaren Unterfelder in das Abfrageresultset einbezogen. Nehmen wir beispielsweise an, dass Ihr Index ein Feld `Address` mit den Unterfeldern `Street`, `City` und `Country` enthält, die alle abrufbar sind. Wenn Sie `Address` in **$select** angeben, sind alle drei Unterfelder in den Abfrageergebnissen enthalten.

## <a name="examples"></a>Beispiele

Beziehen Sie die Felder `HotelId`, `HotelName` und `Rating` (Felder der obersten Ebene) sowie das Unterfeld `City` von `Address` in die Ergebnisse ein:

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

Ein Beispielergebnis könnte wie folgt aussehen:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Beziehen Sie in die Ergebnisse das Feld `HotelName` (Feld der obersten Ebene) und alle Unterfelder von `Address` sowie die Unterfelder `Type` und `BaseRate` jedes Objekts der Sammlung `Rooms` ein:

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

Ein Beispielergebnis könnte wie folgt aussehen:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte  

- [Arbeiten mit Suchergebnissen in der kognitiven Azure-Suche](search-pagination-page-layout.md)
- [Übersicht über die OData-Ausdruckssprache für Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referenz zur OData-Ausdruckssyntax für Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Suchen von Dokumenten &#40;Azure Cognitive Search-REST-API&#41;](/rest/api/searchservice/Search-Documents)