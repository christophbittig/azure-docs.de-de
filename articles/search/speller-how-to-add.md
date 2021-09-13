---
title: Hinzufügen der Rechtschreibprüfung
titleSuffix: Azure Cognitive Search
description: Fügen Sie der Abfragepipeline eine Rechtschreibkorrektur hinzu, um Tippfehler in Abfragebegriffen zu korrigieren, bevor die Abfrage ausgeführt wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 3da9051a1f089d487be7021bf9341a95bae62b08
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614369"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Hinzufügen der Rechtschreibprüfung zu Abfragen in Azure Cognitive Search

> [!IMPORTANT]
> Die Rechtschreibkorrektur befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sie ist nur über das Azure-Portal und die Vorschau-REST-API verfügbar.

Sie können die Trefferquote (Recall) verbessern, indem Sie die Rechtschreibung einzelner Suchbegriffe der Abfrage korrigieren, bevor sie an die Suchmaschine übergeben werden. Der Parameter **speller** (Rechtschreibprüfung) wird für alle Abfragetypen unterstützt: [simple](query-simple-syntax.md) (einfach), [full](query-lucene-syntax.md) (vollständig) sowie die neue Option [semantic](semantic-how-to-query-request.md) (semantisch), die sich derzeit in der öffentlichen Vorschau befindet.

## <a name="prerequisites"></a>Voraussetzungen

Die Rechtschreibprüfung wurde zusammen mit der [Vorschauversion der semantischen Suche](semantic-search-overview.md) veröffentlicht. Es ist zwar eine [Registrierung](https://aka.ms/SemanticSearchPreviewSignup) erforderlich, für die Nutzung fallen aber keine Gebühren an, und es gelten keine Tarifeinschränkungen. Die Rechtschreibprüfung ist in den [gleichen Regionen](semantic-search-overview.md#availability-and-pricing) verfügbar wie die semantische Suche.

Nachdem die Registrierung verarbeitet wurde, benötigen Sie Folgendes:

+ Ein vorhandener Suchindex mit Inhalt in einer [unterstützten Sprache](#supported-languages) Derzeit funktioniert die Rechtschreibkorrektur nicht mit [Synonymen](search-synonyms.md). Vermeiden Sie deren Verwendung in Indizes mit Synonymzuordnungen in beliebigen Felddefinitionen.

+ Ein Suchclient zum Senden von Abfragen

  Der Suchclient muss in der Abfrageanforderung Vorschau-REST-APIs unterstützen. Sie können [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) oder Code verwenden, den Sie geändert haben, um REST-Aufrufe an die Vorschau-APIs zu senden.

+ [Für eine Abfrageanforderung](/rest/api/searchservice/preview-api/search-documents), die die Rechtschreibkorrektur aufruft, müssen „api-version=2020-06-30-Preview“, „speller=lexicon“ und „queryLanguage“ auf eine [unterstützte Sprache](#supported-languages) festgelegt sein.

## <a name="spell-correction-with-simple-search"></a>Rechtschreibkorrektur mit einfacher Suche

Im folgenden Beispiel wird der integrierte Index „hotels-sample“ verwendet, um die Rechtschreibkorrektur bei einer einfachen Freitextabfrage zu veranschaulichen. Ohne Rechtschreibkorrektur gibt die Abfrage keine Ergebnisse zurück. Bei der Korrektur gibt die Abfrage ein Ergebnis für die familienfreundliche Ferienunterkunft von Johnson zurück.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Rechtschreibkorrektur mit Lucene-Abfragen vom Typ „full“ (vollständig)

Die Rechtschreibkorrektur erfolgt für einzelne Abfragebegriffe, die einer Analyse unterzogen werden, weshalb Sie den Parameter „speller“ bei einigen Lucene-Abfragen verwenden können, bei anderen jedoch nicht.

+ Nicht kompatible Abfrageformulare, die die Textanalyse umgehen, sind: Platzhalter, Regex, Fuzzy
+ Kompatible Abfrageformulare umfassen Folgendes: feldbezogene Suche, NEAR-Suche, Term Boosting

In diesem Beispiel wird die feldbezogene Suche über das Feld „Kategorie“ mit vollständiger Lucene-Syntax und einen falsch geschriebenen Abfragebegriff verwendet. Durch die Einbeziehung der Rechtschreibprüfung wird der Tippfehler in „Suiite“ korrigiert, und die Abfrage ist erfolgreich.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Rechtschreibkorrektur mit semantischer Suche

Für diese Abfrage, bei der jeder Begriff mit Ausnahme von einem Tippfehler enthält, werden Rechtschreibkorrekturen vorgenommen, um relevante Ergebnisse zurückzugeben. Weitere Informationen finden Sie unter [Erstellen einer Semantikabfrage](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="supported-languages"></a>Unterstützte Sprachen

Gültige Werte für die Rechtschreibprüfung für queryLanguage finden Sie in der folgenden Tabelle. Diese Liste ist eine Teilmenge der [unterstützten Sprachen (REST-API-Referenz)](/rest/api/searchservice/preview-api/search-documents#queryLanguage). Wenn Sie semantische Beschriftungen und Antworten ohne Rechtschreibprüfung verwenden, können Sie aus der größeren Liste von Sprachen und Varianten auswählen.

| Sprache | queryLanguage |
|----------|---------------|
| Englisch [EN] | EN, EN-US (Standard) |
| Spanisch [ES] | ES, ES-ES (Standard)|
| Französisch [FR] | FR, FR-FR (Standard) |
| Deutsch [DE] | DE, DE-DE (Standard) |

## <a name="language-considerations"></a>Sprachbezogene Überlegungen

Der für eine Semantikabfrage erforderliche queryLanguage-Parameter muss mit den [Sprachanalysetools](index-add-language-analyzers.md) übereinstimmen, die den Felddefinitionen im Index zugewiesen sind. Wenn beispielsweise der Inhalt eines Felds mit dem Sprachanalyseprogramm „fr.microsoft“ indiziert wurde, sollten Abfragen, Rechtschreibprüfung, semantische Untertitel und semantische Antworten alle eine französische Sprachbibliothek in irgendeiner Form verwenden.

So verwenden Sie Sprachbibliotheken in Cognitive Search:

+ Sprachanalysetools können während der Indizierung und Abfrageausführung aufgerufen werden, und können entweder vollständig Lucene (z. B. „de.lucene“) oder Microsoft („de.microsoft“) sein.

+ Sprachlexikone, die während der Rechtschreibprüfung aufgerufen werden, werden mit einem der Sprachcodes in der obigen Tabelle angegeben.

In einer Abfrageanforderung gilt der festgelegte queryLanguage-Wert gleichermaßen für Rechtschreibprüfung, [Antworten](semantic-answers.md) und Beschriftungen. Es gibt keine Außerkraftsetzung für einzelne Teile einer semantischen Antwort. 

> [!NOTE]
> Sprachkonsistenz über verschiedene Eigenschaftswerte hinweg ist nur dann ein Problem, wenn Sie Sprachanalysetools verwenden. Wenn Sie sprachunabhängige Analysetools verwenden (z. B. Schlüsselwort, einfach, Standard, Stop, Leerzeichen oder `standardasciifolding.lucene`), können Sie den queryLanguage-Wert frei festlegen.

Inhalt in einem Suchindex kann zwar aus mehreren Sprachen zusammengesetzt sein, die Abfrageeingabe erfolgt jedoch höchstwahrscheinlich nur in einer Sprache. Die Suchmaschine prüft nicht die Kompatibilität von „queryLanguage“, dem Sprachanalysetool und der Sprache, in der der Inhalt verfasst ist. Stellen Sie daher sicher, dass Sie Abfragen entsprechend einschränken, um falsche Ergebnisse zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

+ [Aufrufen von semantischer Rangfolge und Beschriftungen](semantic-how-to-query-request.md)
+ [Erstellen einer einfachen Abfrage](search-query-create.md)
+ [Verwenden der vollständigen Lucene-Abfragesyntax](query-Lucene-syntax.md)
+ [Verwenden einfacher Abfragesyntax](query-simple-syntax.md)
+ [Übersicht über die semantische Suche](semantic-search-overview.md)