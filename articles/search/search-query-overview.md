---
title: Abfragetypen
titleSuffix: Azure Cognitive Search
description: Lernen Sie die Abfragetypen kennen, die in Cognitive Search unterstützt werden, wie z. B. Freitext, Filter, AutoVervollständigen und Vorschläge, geografische Suche, Systemabfragen und Dokumentsuche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b9ca2a4ba836adacb81a82ad59a11c21ffd6c3fb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216876"
---
# <a name="querying-in-azure-cognitive-search"></a>Abfragen in Azure Cognitive Search

Azure Cognitive Search bietet eine umfangreiche Abfragesprache zur Unterstützung einer ganzen Reihe von Szenarien – von der Freitextsuche bis hin zu hochgradig spezifizierten Abfragemustern. Dieser Artikel beschreibt Abfrageanforderungen sowie die Abfragetypen, die Sie erstellen können.

Ein Abfrage in Cognitive Search ist eine vollständige Spezifikation eines **`search`** -Roundtripvorgangs mit Parametern, die sowohl Informationen für die Abfrageausführung enthalten als auch die zurückgegebene Antwort formen. Parameter und Parser bestimmen den Typ der Abfrageanforderung. Das folgende Abfragebeispiel ist eine Freitextabfrage mit einem booleschen Operator. Verwendet wird die [REST-API zum Durchsuchen von Dokumenten](/rest/api/searchservice/search-documents), abgefragt wird die Dokumentsammlung [hotels-sample-index](search-get-started-portal.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Bei der Abfrageausführung werden u. a. folgende Parameter verwendet:

+ **`queryType`** legt den Parser fest. Es kann es sich dabei um den [einfachen Standardabfrageparser](search-query-simple-examples.md) (optimal für die Volltextsuche) handeln oder um den [vollständigen Lucene-Abfrageparser](search-query-lucene-examples.md), der für erweiterte Abfragekonstrukte wie reguläre Ausdrücke, NEAR-Suche, Fuzzy- und Platzhaltersuche usw. verwendet wird.

+ **`searchMode`** gibt an, ob Übereinstimmungen auf den Kriterien „all“ oder „any“ im Ausdruck basieren. Der Standardwert ist „any“.

+ **`search`** gibt die Übereinstimmungskriterien an – in der Regel ganze Ausdrücke oder Begriffe, mit oder ohne Operatoren. Jedes im Indexschema als *durchsuchbar* (searchable) attributierte Feld ist ein Kandidat für diesen Parameter.

+ **`searchFields`** schränkt die Abfrageausführung auf bestimmte durchsuchbare Felder ein. Während der Entwicklung ist es hilfreich, dieselbe Feldliste für „select“ und „search“ zu verwenden. Andernfalls könnte eine Entsprechung auf Feldwerten basieren, die in den Ergebnissen nicht angezeigt werden, sodass ungewiss ist, warum das Dokument zurückgegeben wurde.

Zum Formen der Antwort werden folgende Parameter verwendet:

+ **`select`** gibt an, welche Felder in der Antwort zurückgegeben werden sollen. Nur die im Index als *abrufbar* (retrievable) markierten Felder können in einer SELECT-Anweisung verwendet werden.

+ **`top`** gibt die angegebene Anzahl der am besten übereinstimmenden Dokumente zurück. In diesem Beispiel werden nur 10 Treffer zurückgegeben. Sie können „Zurück zum Seitenanfang“ und „Überspringen“ verwenden (nicht dargestellt), um die Ergebnisse seitenweise auszugeben.

+ **`count`** gibt Aufschluss darüber, wie viele Dokumente im gesamten Index insgesamt übereinstimmen. Diese Zahl kann größer als die Zahl der zurückgegebenen Dokumente sein. 

+ **`orderby`** wird verwendet, wenn Sie die Ergebnisse nach einem Wert sortieren möchten, etwa nach einer Bewertung oder einem Standort. Andernfalls ist das Standardverhalten, das Relevanzergebnis zur Bewertung von Ergebnissen zu verwenden. Ein Feld muss per Attribut als *sortierbar* gekennzeichnet sein, damit es sich für diesen Parameter eignet.

Die obige Liste ist repräsentativ, aber nicht vollständig. Eine vollständige Liste der Parameter in einer Abfrageanforderung finden Sie unter [Dokumente durchsuchen (REST-API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Abfragetypen

Abgesehen von einigen nennenswerten Ausnahmen durchläuft eine Abfrageanforderung invertierte Indizes, die für schnelle Scans strukturiert sind. Hierbei kann in jedem Feld einer beliebigen Anzahl von Suchdokumenten eine Übereinstimmung gefunden werden. In Cognitive Search werden für die Suche nach Übereinstimmungen primär Volltextsuchvorgänge oder Filter verwendet, Sie können jedoch auch andere bekannte Suchfunktionen wie AutoVervollständigen oder die Suche nach geografischen Standorten implementieren. Der Rest dieses Artikels bietet eine Übersicht über Abfragen in Cognitive Search sowie Links zu weiteren Informationen und Beispielen.

## <a name="full-text-search"></a>Volltextsuche

Wenn Ihre Such-App ein Suchfeld enthält, das Begriffseingaben erfasst, ist wahrscheinlich die Volltextsuche der Abfragevorgang, der diese Suchfunktion unterstützt. Die Volltextsuche akzeptiert Begriffe oder Ausdrücke, die in einem **`search`** -Parameter an alle *durchsuchbaren* Felder in Ihrem Index übergeben werden. Optionale boolesche Operatoren in der Abfragezeichenfolge können Einschluss- oder Ausschlusskriterien angeben. Sowohl der einfache als auch der vollständige Parser unterstützen die Volltextsuche.

In Cognitive Search basiert die Volltextsuche auf der Abfrage-Engine Apache Lucene. Abfragezeichenfolgen in der Volltextsuche werden einer lexikalischen Analyse unterzogen, um Scans effizienter zu gestalten. Bei der Analyse werden alle Begriffe in Kleinbuchstaben umgewandelt, Stoppwörter wie „der“, „die“, „das“ entfernt und Begriffe auf die einfache Stammform reduziert. Gemäß Vorgabe wird die Standardversion von Lucene verwendet.

Wenn übereinstimmende Begriffe gefunden werden, setzt die Abfrage-Engine unter Verwendung des Dokumentschlüssels oder der Dokument-ID ein Suchdokument mit der Übereinstimmung ein, um Feldwerte zusammenzustellen. Anschließend ordnet die Engine die Dokumente in der Reihenfolge ihrer Relevanz an und gibt (standardmäßig) die wichtigsten 50 Dokumente (oder bei Angabe von **`top`** die gewünschte Anzahl) zurück.

Wenn Sie eine Volltextsuche implementieren, sollten Sie wissen, wie die Inhalte mit Token versehen werden, damit Sie Abfrageanomalien debuggen können. Abfragen für Zeichenfolgen mit Bindestrichen oder Sonderzeichen erfordern möglicherweise ein anderes Analysetool als die Lucene-Standardversion, um sicherzustellen, dass der Index die richtigen Token enthält. Sie können die Standardeinstellung außer Kraft setzen und stattdessen [Sprachanalysetools](index-add-language-analyzers.md#language-analyzer-list) oder [spezielle Analysetools](index-add-custom-analyzers.md#built-in-analyzers) verwenden, die die lexikalische Analyse ändern. Ein Beispiel hierfür ist ein [Schlüsselwort](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html), durch das der gesamte Inhalt eines Felds als ein einzelnes Token behandelt wird. Dies ist nützlich für Daten wie Postleitzahlen, IDs und einige Produktnamen. Weitere Informationen finden Sie unter [Suche nach Teilausdrücken und Mustern mit Sonderzeichen (Bindestriche, Platzhalter, reguläre Ausdrücke, Muster)](search-query-partial-matching.md).

Wenn Sie mit einem vermehrten Einsatz boolescher Operatoren rechnen – was in Indizes mit großen Textblöcken wie Inhaltsfeldern oder langen Beschreibungen recht wahrscheinlich ist –, stellen Sie sicher, dass Sie Abfragen mit dem Parameter **`searchMode=Any|All`** testen, um die Auswirkung dieser Einstellung auf eine boolesche Suche zu ermitteln.

## <a name="autocomplete-and-suggested-queries"></a>AutoVervollständigen und vorgeschlagene Abfragen

[AutoVervollständigen und Vorschläge](search-add-autocomplete-suggestions.md) sind Alternativen zu **`search`** , die in Funktionen zur Suche während der Eingabe mehrere aufeinander folgende Abfrageanforderungen basierend auf eingegebenen Teilzeichenfolgen (nach jedem Zeichen) auslösen. Sie können die Parameter **`autocomplete`** und **`suggestions`** gemeinsam oder separat verwenden, wie in [diesem Tutorial](tutorial-csharp-type-ahead-and-suggestions.md) beschrieben. Die Verwendung mit **`search`** ist allerdings nicht möglich. Sowohl automatisch vervollständigte Begriffe als auch vorgeschlagene Abfragen werden aus dem Indexinhalt abgeleitet. Die Engine gibt niemals eine Zeichenfolge oder einen Vorschlag zurück, die bzw. der im Index nicht vorhanden ist. Weitere Informationen finden Sie unter [AutoVervollständigen (REST-API)](/rest/api/searchservice/autocomplete) und [Vorschläge (REST-API)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtern der Suche

Filter werden in Apps mit Cognitive Search sehr häufig verwendet. Auf Anwendungsseiten werden Filter oft als Facetten in Linknavigationsstrukturen dargestellt, sodass Benutzer die Filterung steuern können. Filter werden auch intern verwendet, um Slices der indizierten Inhalte verfügbar zu machen. Beispielsweise können Sie eine Suchseite mithilfe eines auf eine Produktkategorie festgelegten Filters initialisieren oder einen Sprachfilter verwenden, wenn ein Index Felder sowohl in englischer als auch in französischer Sprache enthält.

Möglicherweise benötigen Sie auch Filter, um ein spezielles Abfrageformular aufzurufen, wie in der folgenden Tabelle beschrieben. Sie können einen Filter mit einer nicht spezifizierten Suche ( **`search=*`** ) oder mit einer Abfragezeichenfolge verwenden, die Begriffe, Ausdrücke, Operatoren und Muster umfasst.

| Filterszenario | Beschreibung |
|-----------------|-------------|
| Bereichsfilter | In der kognitiven Azure-Suche werden Bereichsabfragen anhand des Filterparameters erstellt. Weitere Informationen und Beispiele finden Sie unter [Beispiel: Bereichsfilter](search-query-simple-examples.md#example-5-range-filters). |
| Suche nach einem geografischen Standort | Wenn ein durchsuchbares Feld den [Typ „Edm.GeographyPoint“](/rest/api/searchservice/supported-data-types) aufweist, können Sie einen Filterausdruck für „Standorte in meiner Nähe suchen“ oder kartenbasierte Suchsteuerelemente erstellen. Felder, die eine geografische Suche unterstützen, enthalten Koordinaten. Weitere Informationen und ein Beispiel finden Sie unter [Beispiel: Geografische Suche](search-query-simple-examples.md#example-6-geo-search). |
| Facettennavigation | In der Struktur [Facettennavigation](search-faceted-navigation.md) können Benutzer Facetten auswählen und die Ergebnisse bei jedem Klick eingrenzen. Jede Facette wird durch einen Filter unterstützt, der Dokumente ausschließt, die nicht mehr den Kriterien entsprechen. |

> [!NOTE]
> In einem Filterausdruck verwendeter Text wird während der Abfrageverarbeitung nicht analysiert. Hierbei wird davon ausgegangen, dass es sich bei der Texteingabe um ein wortgetreues Zeichenmuster mit Beachtung der Groß- und Kleinschreibung handelt, das entweder erfolgreich oder nicht erfolgreich mit der Abfrage abgeglichen wird. Filterausdrücke werden mithilfe der [OData-Syntax](query-odata-filter-orderby-syntax.md) erstellt und in einem **`filter`** -Parameter an alle *filterbaren* Felder in Ihrem Index übergeben. Weitere Informationen finden Sie unter [Filter in Azure Cognitive Search](search-filters.md).

## <a name="document-look-up"></a>Dokumentsuche

Im Gegensatz zu den bisher beschriebenen Abfrageformen wird hier ein einzelnes [Suchdokument anhand der ID](/rest/api/searchservice/lookup-document) abgerufen, ohne entsprechende Indexsuche oder -überprüfung. Es wird nur dieses eine Dokument angefordert und zurückgegeben. Wenn ein Benutzer in Suchergebnissen ein Element auswählt, besteht die typische Antwort darin, dass das Dokument abgerufen und die Detailseite mit Feldern aufgefüllt wird. Die Dokumentsuche ist der Vorgang, der diesen Fall unterstützt.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Erweiterte Suche: Fuzzy, Platzhalter, Näherung, regulärer Ausdruck

Ein erweitertes Abfrageformular benötigt die vollständige Version des Lucene-Parsers sowie Operatoren, die ein bestimmtes Abfrageverhalten auslösen.

| Abfragetyp | Verwendung | Beispiele und weitere Informationen |
|------------|--------|------------------------------|
| [Feldbezogene Suche](query-lucene-syntax.md#bkmk_fields) | **`search`** -Parameter, **`queryType=full`**  | Erstellen Sie einen zusammengesetzten Abfrageausdruck für ein einzelnes Feld. <br/>[Beispiel für die feldbezogene Suche](search-query-lucene-examples.md#example-1-fielded-search) |
| [Fuzzysuche](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** -Parameter, **`queryType=full`** | Sucht nach Begriffen mit ähnlichem Aufbau oder ähnlicher Rechtschreibung. <br/>[Beispiel für die Fuzzysuche](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [NEAR-Suche](query-lucene-syntax.md#bkmk_proximity) | **`search`** -Parameter, **`queryType=full`** | Sucht nach Begriffen, die in einem Dokument nahe beieinander vorkommen. <br/>[Beispiel für die NEAR-Suche](search-query-lucene-examples.md#example-3-proximity-search) |
| [Term Boosting](query-lucene-syntax.md#bkmk_termboost) | **`search`** -Parameter, **`queryType=full`** | Weist einem Dokument, das den verstärkten Begriff enthält, im Vergleich zu anderen Dokumenten, bei denen dies nicht der Fall ist, einen höheren Rang zu. <br/>[Beispiele für die Begriffsverstärkung (Term Boosting)](search-query-lucene-examples.md#example-4-term-boosting) |
| [Suche mit regulären Ausdrücken](query-lucene-syntax.md#bkmk_regex) | **`search`** -Parameter, **`queryType=full`** | Sucht basierend auf dem Inhalt eines regulären Ausdrucks nach Übereinstimmungen. <br/>[Beispiel für einen regulären Ausdruck](search-query-lucene-examples.md#example-5-regex) |
|  [Platzhalter- oder Präfixsuche](query-lucene-syntax.md#bkmk_wildcard) | **`search`** -Parameter mit **_`~`_* oder **`?`** , **`queryType=full`**| Sucht basierend auf einem Präfix und dem Tildezeichen (`~`) oder einem einzelnen Zeichen (`?`) nach Übereinstimmungen. <br/>[Beispiel für die Platzhaltersuche](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Nächste Schritte

Genauere Informationen zur Implementierung der Abfrage finden Sie in den Beispielen für die jeweilige Syntax. Wenn Sie noch nicht mit der Volltextsuche gearbeitet haben, sollten Sie sich auch genauer ansehen, wie die Abfrage-Engine funktioniert.

+ [Aufrufen der einfachen Abfrageanalyse](search-query-simple-examples.md)
+ [Beispiele für die Lucene-Abfragesyntax zum Erstellen von Abfragen in Azure Search](search-query-lucene-examples.md)
+ [Funktionsweise der Volltextsuche in Azure Cognitive Search](search-lucene-query-architecture.md)