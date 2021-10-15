---
title: Hinzufügen der Rechtschreibprüfung
titleSuffix: Azure Cognitive Search
description: Fügen Sie der Abfragepipeline eine Rechtschreibkorrektur hinzu, um Tippfehler in Abfragebegriffen zu korrigieren, bevor die Abfrage ausgeführt wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: references_regions
ms.openlocfilehash: 98aec7fc2a3857ac50125e7e49c5f9ab105a49d7
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535933"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Hinzufügen der Rechtschreibprüfung zu Abfragen in Azure Cognitive Search

> [!IMPORTANT]
> Die Rechtschreibkorrektur befindet sich in der öffentlichen Vorschau und unterliegt den [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sie ist nur über das Azure-Portal und die Vorschau-REST-API verfügbar.

Sie können die Trefferquote (Recall) verbessern, indem Sie die Rechtschreibung einzelner Suchbegriffe der Abfrage korrigieren, bevor sie an die Suchmaschine übergeben werden. Der Parameter **speller** (Rechtschreibprüfung) wird für alle Abfragetypen unterstützt: [simple](query-simple-syntax.md) (einfach), [full](query-lucene-syntax.md) (vollständig) sowie die neue Option [semantic](semantic-how-to-query-request.md) (semantisch), die sich derzeit in der öffentlichen Vorschau befindet.

Die Rechtschreibprüfung wurde zusammen mit der [Vorschauversion der semantischen Suche](semantic-search-overview.md) veröffentlicht und nutzt den queryLanguage-Parameter gemeinsam, ist aber ansonsten ein unabhängiges Feature mit eigenen Voraussetzungen. Für die Nutzung dieses Features ist keine Anmeldung erforderlich, und es fallen keine zusätzlichen Gebühren an.

## <a name="prerequisites"></a>Voraussetzungen

Um die Rechtschreibprüfung verwenden zu können, ist Folgendes erforderlich:

+ Ein Suchdienst im Basic-Tarif oder höher in einer beliebigen Region.

+ Ein vorhandener Suchindex mit Inhalt in einer [unterstützten Sprache](#supported-languages)

+ [Eine Abfrageanforderung](/rest/api/searchservice/preview-api/search-documents), in der „speller=lexicon“ enthalten und „queryLanguage“ auf eine [unterstützte Sprache](#supported-languages) festgelegt ist. Die Rechtschreibprüfung funktioniert für Zeichenfolgen, die im Parameter „search“ übergeben werden. Für Filter wird sie nicht unterstützt.

Verwenden Sie einen Suchclient, der Vorschau-APIs für die Abfrageanforderung unterstützt. Für REST können Sie [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) oder Code verwenden, den Sie geändert haben, um REST-Aufrufe an die Vorschau-APIs zu senden. Sie können auch Betaversionen der Azure SDKs verwenden.

| Clientbibliothek | Versionen |
|----------|----------|
| REST-API | [2021-04-30-Preview](/rest/api/searchservice/index-preview) oder 2020-06-30-Preview |
| Azure SDK für .NET | [version 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) | 
| Azure SDK für Java |  [version 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar) |
| Azure SDK für JavaScript | [version 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2) |
| Azure SDK für Python | [version 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

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

Die Rechtschreibkorrektur erfolgt für einzelne Abfragebegriffe, die einer Textanalyse unterzogen werden, weshalb Sie den Parameter „speller“ bei einigen Lucene-Abfragen verwenden können, bei anderen jedoch nicht.

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

Gültige Werte für „queryLanguage“ finden Sie in der folgenden Tabelle, die aus der [Liste unterstützter Sprachen (REST-API-Referenz)](/rest/api/searchservice/preview-api/search-documents#queryLanguage) kopiert wurde.

| Sprache | queryLanguage |
|----------|---------------|
| Englisch [EN] | EN, EN-US (Standard) |
| Spanisch [ES] | ES, ES-ES (Standard)|
| Französisch [FR] | FR, FR-FR (Standard) |
| Deutsch [DE] | DE, DE-DE (Standard) |
| Niederländisch [NL] | NL, NL-BE, NL-NL (Standard) |

### <a name="querylanguage-considerations"></a>Überlegungen zu „queryLanguage“

Wie an anderer Stelle erwähnt, kann eine Abfrageanforderung nur einen queryLanguage-Parameter aufweisen, aber dieser Parameter wird von mehreren Features gemeinsam genutzt, von denen jedes eine andere Kohorte von Sprachen unterstützt. Wenn Sie nur die Rechtschreibprüfung verwenden, ist die Liste der unterstützten Sprachen in der obigen Tabelle vollständig. 

### <a name="language-analyzer-considerations"></a>Überlegungen zum Sprachanalysetool

Indizes, die nicht englischsprachige Inhalte enthalten, verwenden häufig [Sprachanalysetools](index-add-language-analyzers.md) für nicht englischsprachige Felder, um die linguistischen Regeln der nativen Sprache anzuwenden.

Wenn Sie jetzt eine Rechtschreibprüfung zu Inhalten hinzufügen, die auch einer Sprachanalyse unterzogen werden, erzielen Sie bessere Ergebnisse, wenn Sie bei jedem Schritt der Indizierung und Abfrageverarbeitung dieselbe Sprache verwenden. Wenn beispielsweise der Inhalt eines Felds mit dem Sprachanalysetool „fr.microsoft“ indiziert wurde, sollten Abfragen, Rechtschreibprüfung, semantische Beschriftungen und semantische Antworten alle ein französisches Lexikon oder eine Art französischer Sprachbibliothek verwenden.

So verwenden Sie Sprachbibliotheken in Cognitive Search:

+ Sprachanalysetools können während der Indizierung und Abfrageausführung aufgerufen werden und entsprechen entweder Apache Lucene (z. B. „de.lucene“) oder Microsoft („de.microsoft“).

+ Sprachlexikone, die während der Rechtschreibprüfung aufgerufen werden, werden mit einem der Sprachcodes in der obigen Tabelle angegeben.

In einer Abfrageanforderung gilt der zugewiesene queryLanguage-Wert gleichermaßen für Rechtschreibprüfung, [Antworten](semantic-answers.md) und Beschriftungen. 

> [!NOTE]
> Sprachkonsistenz über verschiedene Eigenschaftswerte hinweg ist nur dann ein Problem, wenn Sie Sprachanalysetools verwenden. Wenn Sie sprachunabhängige Analysetools verwenden (z. B. Schlüsselwort, einfach, Standard, Stop, Leerzeichen oder `standardasciifolding.lucene`), können Sie den queryLanguage-Wert frei festlegen.

Inhalt in einem Suchindex kann zwar aus mehreren Sprachen zusammengesetzt sein, die Abfrageeingabe erfolgt jedoch höchstwahrscheinlich nur in einer Sprache. Die Suchmaschine prüft nicht die Kompatibilität von „queryLanguage“, dem Sprachanalysetool und der Sprache, in der der Inhalt verfasst ist. Stellen Sie daher sicher, dass Sie Abfragen entsprechend einschränken, um falsche Ergebnisse zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

+ [Aufrufen von semantischer Rangfolge und Beschriftungen](semantic-how-to-query-request.md)
+ [Erstellen einer einfachen Abfrage](search-query-create.md)
+ [Verwenden der vollständigen Lucene-Abfragesyntax](query-Lucene-syntax.md)
+ [Verwenden einfacher Abfragesyntax](query-simple-syntax.md)
+ [Übersicht über die semantische Suche](semantic-search-overview.md)