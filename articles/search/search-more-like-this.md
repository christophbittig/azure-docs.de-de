---
title: moreLikeThis-Abfragefunktion (Vorschauversion)
titleSuffix: Azure Cognitive Search
description: In diesem Artikel wird die moreLikeThis-Funktion (Vorschauversion) beschrieben, die in Vorschauversionen der REST-API der kognitiven Azure-Suche verfügbar ist.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 595884bf9d73e6e7af6663c051a2735908670eb4
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112578665"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (Vorschauversion) in der kognitiven Azure-Suche

> [!IMPORTANT] 
> Dieses Feature befindet sich in der Public Preview-Phase und unterliegt [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die [Vorschau-REST-API](/rest/api/searchservice/index-preview) unterstützt dieses Feature.

`moreLikeThis=[key]` ist ein Abfrageparameter in der [API zum Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents), über den Dokumente gesucht werden, die dem mit dem Dokumentschlüssel angegebenen Dokument ähneln. Beim Ausführen einer Suchanforderung mit `moreLikeThis` wird eine Abfrage mit Suchbegriffen generiert, die aus dem angegebenen Dokument extrahiert werden und das Dokument am besten beschreiben. Anhand der generierten Abfrage wird dann die Suchanforderung erstellt. Standardmäßig werden die Inhalte aller durchsuchbaren Felder berücksichtigt, mit Ausnahme aller eingeschränkten Felder, die Sie mit dem Parameter `searchFields` angegeben haben. Der Parameter `moreLikeThis` kann nicht mit dem Suchparameter `search=[string]` verwendet werden.

Standardmäßig werden die Inhalte aller durchsuchbaren Felder auf oberster Ebene berücksichtigt. Wenn Sie stattdessen bestimmte Felder angeben möchten, können Sie den Parameter `searchFields` verwenden. 

Sie können `MoreLikeThis` nicht für durchsuchbare untergeordnete Felder eines [komplexen Typs](search-howto-complex-data-types.md) verwenden.

## <a name="examples"></a>Beispiele

In allen folgenden Beispielen wird das Beispiel „hotels“ aus [Schnellstart: Erstellen eines Suchindex im Azure-Portal](search-get-started-portal.md) verwendet.

### <a name="simple-query"></a>Einfache Abfrage

Mit der folgenden Abfrage werden Dokumente gesucht, deren Beschreibungsfelder dem Feld des Quelldokuments entsprechend der Angabe durch den Parameter `moreLikeThis` am ähnlichsten sind:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

In diesem Beispiel sucht die Anforderung nach Hotels, die mit `HotelId` 29 vergleichbar sind.
Sie können `MoreLikeThis` auch mithilfe von HTTP POST (anstelle von HTTP GET) aufrufen:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Anwenden von Filtern

`MoreLikeThis` kann mit anderen allgemeinen Abfrageparametern wie `$filter` kombiniert werden. Beispielsweise kann die Abfrage auf Hotels mit der Kategorie „Budget“ und einer Bewertung von mindestens 3,5 beschränkt werden:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Auswählen von Feldern und Einschränken der Ergebnisse

Mit dem Selektor `$top` kann die Anzahl von Ergebnissen eingeschränkt werden, die in einer Abfrage vom Typ `MoreLikeThis` zurückgegeben werden sollen. Außerdem können Felder mit `$select` ausgewählt werden. Hier werden die drei passendsten Hotels zusammen mit ihrer ID, ihrem Namen und ihrer Bewertung ausgewählt: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Nächste Schritte

Sie können diese Funktion mit einem beliebigen Webtesttool ausprobieren.  Es empfiehlt sich die Verwendung von Postman.

> [!div class="nextstepaction"]
> [Untersuchen der Azure Cognitive Search-REST-APIs](search-get-started-rest.md)