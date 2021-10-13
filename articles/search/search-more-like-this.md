---
title: moreLikeThis-Abfragefunktion (Vorschauversion)
titleSuffix: Azure Cognitive Search
description: In diesem Artikel wird die moreLikeThis-Funktion (Vorschauversion) beschrieben, die in Vorschauversionen der REST-API der kognitiven Azure-Suche verfügbar ist.
author: bevloh
ms.author: beloh
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 1dae66ed27a0934d17503cc8262d39dfc02b46dc
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612064"
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

```http
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

In diesem Beispiel sucht die Anforderung nach Hotels, die mit `HotelId` 29 vergleichbar sind.
Sie können `MoreLikeThis` auch mithilfe von HTTP POST (anstelle von HTTP GET) aufrufen:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Anwenden von Filtern

`MoreLikeThis` kann mit anderen allgemeinen Abfrageparametern wie `$filter` kombiniert werden. Beispielsweise kann die Abfrage auf Hotels mit der Kategorie „Budget“ und einer Bewertung von mindestens 3,5 beschränkt werden:

```http
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Auswählen von Feldern und Einschränken der Ergebnisse

Mit dem Selektor `$top` kann die Anzahl von Ergebnissen eingeschränkt werden, die in einer Abfrage vom Typ `MoreLikeThis` zurückgegeben werden sollen. Außerdem können Felder mit `$select` ausgewählt werden. Hier werden die drei passendsten Hotels zusammen mit ihrer ID, ihrem Namen und ihrer Bewertung ausgewählt: 

```http
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Nächste Schritte

Sie können diese Funktion mit einem beliebigen Webtesttool ausprobieren.  Es empfiehlt sich die Verwendung von Postman.

> [!div class="nextstepaction"]
> [Erkunden von REST-APIs der kognitiven Azure-Suche mit Postman](search-get-started-rest.md)