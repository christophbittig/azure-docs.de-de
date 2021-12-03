---
title: Erstellen einer Semantikabfrage
titleSuffix: Azure Cognitive Search
description: Legen Sie eine Abfrage vom Typ „semantic“ (semantisch) fest, um die Deep Learning-Modelle mit der Abfrageverarbeitung zu verknüpfen und so die Absicht und den Kontext als Teil des Suchrangs und der Relevanz abzuleiten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: e89f449d62a86d8a935647d0a60af25415f65b85
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058333"
---
# <a name="create-a-query-that-invokes-semantic-ranking-and-returns-semantic-captions"></a>Erstellen einer Abfrage zum Aufrufen der semantischen Rangfolge und zum Zurückgeben semantischer Beschriftungen

> [!IMPORTANT]
> Die semantische Suche befindet sich in der öffentlichen Vorschau und unterliegt [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sie ist über das Azure-Portal, die Vorschau-REST-API und Beta-SDKs verfügbar. Diese Features sind abrechenbar. Weitere Informationen finden Sie unter [Verfügbarkeit und Preise](semantic-search-overview.md#availability-and-pricing).

Die semantische Suche ist ein Premium-Feature in Azure Cognitive Search, das einen semantischen Rangfolgealgorithmus für ein Resultset aufruft und semantische Beschriftungen (und optional [semantische Antworten](semantic-answers.md)) zurückgibt. Die relevantesten Begriffe und Ausdrücke werden dabei hervorgehoben. Beschriftungen und Antworten werden in Abfrageanforderungen zurückgegeben, die mit dem Abfragetyp „Semantik“ formuliert werden.

Sowohl Beschriftungen als auch Antworten werden wörtlich aus dem Text im Suchdokument extrahiert. Das semantische Subsystem bestimmt, welcher Inhalt die Merkmale einer Beschriftung oder Antwort hat, aber es werden keine neuen Sätze oder Ausdrücke verfasst. Aus diesem Grund funktionieren Inhalte, die Erläuterungen oder Definitionen enthalten, am besten für die semantische Suche.

## <a name="prerequisites"></a>Voraussetzungen

+ Ein Cognitive Search-Dienst in einem Standard-Tarif (S1, S2, S3) in einer der folgenden Regionen: „USA, Norden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Osten 2“, „Europa, Norden“, „Europa, Westen“. Wenn Sie in einer dieser Regionen über einen Dienst mit Tarif S1 oder höher verfügen, können Sie sich für die Vorschau registrieren, ohne einen neuen Dienst erstellen zu müssen.

+ [Registrieren Sie sich hier für die Vorschau.](https://aka.ms/SemanticSearchPreviewSignup) Die erwartete Verarbeitungszeit beträgt etwa zwei Werktage.

+ Ein vorhandener Suchindex mit Inhalt in einer [unterstützten Sprache](/rest/api/searchservice/preview-api/search-documents#queryLanguage) Die semantische Suche funktioniert am besten für informatorische oder beschreibende Inhalte.

+ Ein Suchclient zum Senden von Abfragen

  Der Suchclient muss in der Abfrageanforderung Vorschau-REST-APIs unterstützen. Sie können [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) oder Code verwenden, um REST-Aufrufe an die Vorschau-APIs zu senden. Sie können auch den [Suchexplorer](search-explorer.md) im Azure-Portal verwenden, um eine Semantikabfrage zu übermitteln. Sie können auch [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) verwenden.

+ Eine [Abfrageanforderung](/rest/api/searchservice/preview-api/search-documents) muss den Parameter `queryType=semantic` und weitere in diesem Artikel beschriebene Parameter enthalten.

## <a name="whats-a-semantic-query-type"></a>Was ist ein Semantikabfragetyp?

In Azure Cognitive Search handelt es sich bei einer Abfrage um eine parametrisierte Anforderung, mit der die Abfrageverarbeitung und die Form der Antwort bestimmt werden. Eine *Semantikabfrage* [fügt Parameter hinzu](#query-using-rest), die das Modell zur semantischen Neuanordnung aufrufen. Mit diesem können der Kontext sowie die Bedeutung übereinstimmender Ergebnisse bewertet, relevantere Übereinstimmungen in der Rangfolge nach oben verschoben und semantische Antworten und Beschriftungen zurückgegeben werden.

Die folgende Anforderung ist charakteristisch für eine minimale Semantikabfrage (ohne Antwort).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Wie bei allen Abfragen in Azure Cognitive Search zielt die Anforderung auf die Dokumentensammlung eines einzelnen Indexes ab. Außerdem wird bei einer Semantikabfrage dieselbe Sequenz von Parsen, Analysieren, Überprüfen und Bewerten durchlaufen wie bei einer nicht semantischen Abfrage. 

Der Unterschied liegt in der Relevanz und Bewertung. Wie in diesem Vorschaurelease definiert, ist eine Semantikabfrage eine Abfrage, deren *Ergebnisse* mithilfe eines semantischen Sprachmodells erneut bewertet werden. Dadurch wird eine Möglichkeit bereitgestellt, die Übereinstimmungen, die von dem Semantikrangfolge-Modul als am relevantesten eingestuft werden, als erstes anzuzeigen.

Nur die 50 besten Treffer aus den anfänglichen Ergebnissen können in eine semantische Rangfolge gebracht werden, und alle Ergebnisse enthalten Beschriftungen in der Antwort. Optional können Sie bei der Anforderung einen **`answer`** -Parameter angeben, um eine mögliche Antwort zu extrahieren. Weitere Informationen finden Sie unter [Semantische Antworten](semantic-answers.md).

## <a name="query-in-azure-portal"></a>Abfragen im Azure-Portal

Der [Suchexplorer](search-explorer.md) wurde aktualisiert und enthält nun Optionen für semantische Abfragen. Diese Optionen werden im Portal angezeigt, nachdem Sie die folgenden Schritte ausgeführt haben:

1. Öffnen Sie das Portal mit der Syntax `https://portal.azure.com/?feature.semanticSearch=true` in einem Suchdienst, für den die Vorschau aktiviert ist.

1. Klicken Sie oben auf der Übersichtsseite auf **Suchexplorer**.

1. Wählen Sie einen Index mit Inhalt in einer [unterstützten Sprache](/rest/api/searchservice/preview-api/search-documents#queryLanguage) aus.

1. Legen Sie im Suchexplorer Abfrageoptionen fest, die Semantikabfragen, searchFields und Rechtschreibkorrekturen ermöglichen. Sie können auch die erforderlichen Abfrageparameter in die Abfragezeichenfolge einfügen.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Abfrageoptionen im Suchexplorer" border="true":::

## <a name="query-using-rest"></a>Abfragen über REST

Verwenden Sie die [Suchdokumente (REST-Vorschau)](/rest/api/searchservice/preview-api/search-documents), um die Anforderung programmgesteuert zu formulieren. Eine Antwort enthält automatisch Beschriftungen und Hervorhebungen. Wenn Sie eine Rechtschreibkorrektur oder Antworten in der Antwort wünschen, fügen Sie der Anforderung **`speller`** oder **`answers`** hinzu.

Im folgenden Beispiel wird [hotels-sample-index](search-get-started-portal.md) dazu verwendet, eine Semantikabfrageanforderung mit Rechtschreibprüfung, semantischen Antworten und Beschriftungen zu erstellen:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

In der folgenden Tabelle werden die in einer Semantikabfrage verwendeten Parameter zusammengefasst. Eine Liste aller Parameter finden Sie unter [Dokumente durchsuchen (Vorschau-REST-API)](/rest/api/searchservice/preview-api/search-documents).

| Parameter | type | BESCHREIBUNG |
|-----------|-------|-------------|
| queryType | String | Gültige Werte sind „simple“, „full“ und „semantic“. Der Wert „semantic“ wird für Semantikabfragen benötigt. |
| queryLanguage | String | Für Semantikabfragen erforderlich. Das von Ihnen angegebene Lexikon gilt gleichermaßen für die semantische Rangfolge, Beschriftungen, Antworten und die Rechtschreibprüfung. Weitere Informationen finden Sie unter [Unterstützte Sprachen (REST-API-Referenz)](/rest/api/searchservice/preview-api/search-documents#queryLanguage). |
| searchFields | String | Hierbei handelt es sich um eine durch Trennzeichen getrennte Liste mit durchsuchbaren Feldern. Gibt die Felder an, für die eine semantische Rangfolge auftritt, von der Beschriftungen und Antworten extrahiert werden. </br></br>Im Gegensatz zu einfachen und vollständigen Abfragetypen bestimmt die Reihenfolge, in der Felder aufgeführt sind, welches Feld Vorrang hat. Weitere Anweisungen zur Verwendung finden Sie unter [Schritt 2: Festlegen von searchFields](#searchfields). |
| speller | String | Dies ist ein optionaler Parameter und nicht spezifisch für semantische Abfragen. Er korrigiert falsch geschriebene Begriffe, bevor diese die Suchmaschine erreichen. Weitere Informationen finden Sie unter [Hinzufügen von Rechtschreibkorrekturen zu Abfragen](speller-how-to-add.md). |
| answers |String | Dies sind optionale Parameter, um anzugeben, ob semantische Antworten in das Ergebnis einbezogen werden sollen. Derzeit wird nur „extractive“ implementiert. Antworten können so konfiguriert werden, dass maximal zehn Werte zurückgegeben werden. Der Standardwert ist 1. Dieses Beispiel zeigt drei Antworten: `extractive\|count-3`. Weitere Informationen finden Sie unter [Rückgabe von semantischen Antworten](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formulieren der Anforderung

In diesem Abschnitt wird die Abfrageformulierung Schritt für Schritt erläutert.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Schritt 1: Festlegen von queryType und queryLanguage

Fügen Sie der REST-API folgende Parameter hinzu. Es müssen beide Parameter angegeben werden.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

Der queryLanguage-Parameter muss einer [unterstützten Sprache](/rest/api/searchservice/preview-api/search-documents#queryLanguage) entsprechen und mit den [Sprachanalysetools](index-add-language-analyzers.md) übereinstimmen, die den Felddefinitionen im Indexschema zugewiesen sind. Beispiel: Wenn Sie französische Zeichenfolgen mithilfe eines französischen Sprachanalysetools (z. B. „fr.microsoft“ oder „fr.lucene“) indiziert haben, dann muss auch der queryLanguage-Parameter eine Variante der französischen Sprache sein.

Wenn Sie in einer Abfrageanforderung auch die [Rechtschreibkorrektur](speller-how-to-add.md) verwenden, gilt die festgelegte „queryLanguage“ gleichermaßen für Rechtschreibprüfung, Antworten und Beschriftungen. Es gibt keine Außerkraftsetzung einzelner Teile. Die Rechtschreibprüfung unterstützt [weniger Sprachen](speller-how-to-add.md#supported-languages). Wenn Sie dieses Feature verwenden, müssen Sie den queryLanguage-Parameter also auf eine aus dieser Liste festlegen.

Inhalt in einem Suchindex kann zwar aus mehreren Sprachen zusammengesetzt sein, die Abfrageeingabe erfolgt jedoch höchstwahrscheinlich nur in einer Sprache. Die Suchmaschine prüft nicht die Kompatibilität von „queryLanguage“, dem Sprachanalysetool und der Sprache, in der der Inhalt verfasst ist. Stellen Sie daher sicher, dass Sie Abfragen entsprechend einschränken, um falsche Ergebnisse zu vermeiden.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Schritt 2: Festlegen von searchFields

Fügen Sie der Anforderung searchFields hinzu. Dieser Schritt ist optional, wird jedoch dringend empfohlen.

```json
"searchFields": "HotelName,Category,Description",
```

Der searchFields-Parameter wird verwendet, um Passagen zu identifizieren, die für die „semantische Ähnlichkeit“ zur Abfrage evaluiert werden. Für die Vorschau empfiehlt es sich nicht, searchFields leer zu lassen, da das Modell einen Hinweis benötigt, welche Felder für die Verarbeitung am wichtigsten sind.

Im Gegensatz zu anderen Parametern sind searchFields nicht neu. Möglicherweise verwenden Sie searchFields bereits in vorhandenem Code für einfache oder vollständige Lucene-Abfragen. Wenn ja, überprüfen Sie erneut, wie der Parameter verwendet wird, damit Sie beim Wechseln zu einem Semantikabfragetyp eine Überprüfung nach Feldreihenfolge durchführen können.

##### <a name="allowed-data-types"></a>Zulässige Datentypen

Wählen Sie beim Festlegen von searchFields nur Felder der folgenden [unterstützten Datentypen](/rest/api/searchservice/supported-data-types) aus. Wenn Sie ungültige Felder einbeziehen, wird zwar kein Fehler ausgegeben, aber die betreffenden Felder werden nicht in der semantischen Rangfolge verwendet.

| Datentyp | Beispiel aus hotels-sample-index |
|-----------|----------------------------------|
| Edm.String | HotelName, Category, Description |
| Edm.ComplexType | Address.StreetNumber, Address.City, Address.StateProvince, Address.PostalCode |
| Collection(Edm.String) | Tags (eine durch Trennzeichen getrennte Liste von Zeichenfolgen) |

##### <a name="order-of-fields-in-searchfields"></a>Reihenfolge der Felder in searchFields

Die Feldreihenfolge ist entscheidend, da vom Semantikbewerter die Menge an Inhalten, die von ihm verarbeitet werden können, eingeschränkt wird, während die Antwortzeit dennoch angemessen bleibt. Inhalte aus Feldern am Anfang der Liste werden wahrscheinlicher einbezogen. Inhalte vom Ende können abgeschnitten werden, wenn die maximale Grenze erreicht wird. Weitere Informationen finden Sie unter [Vorabverarbeitung während der semantischen Bewertung](semantic-ranking.md#pre-processing).

+ Wenn Sie nur ein Feld angeben, verwenden Sie ein beschreibendes Feld, in dem die Antwort auf Semantikabfragen gefunden werden kann, z. B. der Hauptinhalt eines Dokuments. 

+ Für zwei oder mehr Felder in SearchFields:

  + Das erste Feld sollte immer präzise sein (z. B. ein Titel oder ein Name) und idealerweise eine Zeichenfolge von weniger als 25 Wörtern enthalten.

  + Falls der Index ein URL-Feld besitzt, das für Benutzer lesbar ist (wie `www.domain.com/name-of-the-document-and-other-details`) und nicht computerbezogen ist (wie `www.domain.com/?id=23463&param=eis`), platzieren Sie es in der Liste an zweiter Stelle. Wenn kein präzises Titelfeld vorhanden ist, platzieren Sie es an erster Stelle.

  + Lassen Sie den obigen Feldern andere beschreibende Felder folgen, in denen die Antworten auf Semantikabfragen gefunden werden können, z. B. der Hauptinhalt eines Dokuments.

#### <a name="step-3-remove-or-bracket-query-features-that-bypass-relevance-scoring"></a>Schritt 3: Entfernen oder Ausklammern von Abfragefeatures, die die Relevanzbewertung umgehen

Einige Abfragefunktionen in Cognitive Search unterliegen keiner Relevanzbewertung, und einige umgehen die Volltextsuchmaschine völlig. Wenn Ihre Abfragelogik die folgenden Features umfasst, erhalten Sie keine Relevanzwerte oder semantische Rangfolgen für Ihre Ergebnisse:

+ Filter, Fuzzysuchabfragen und reguläre Ausdrücke durchlaufen nicht mit Token versehenen Text und suchen nach wörtlichen Übereinstimmungen im Inhalt. Suchbewertungen für alle oben genannten Abfrageformen erhalten einheitlich den Wert 1,0 und bieten keine sinnvollen Eingaben für eine semantische Rangfolge.

+ Auch eine Sortierung (orderby-Klausel) nach bestimmten Feldern überschreibt Suchbewertungen und semantische Rangfolge. Da die semantische Rangfolge zum Sortieren der Ergebnisse verwendet wird, führt eine explizite Sortierungslogik zur Rückgabe eines HTTP-400-Fehlers.

#### <a name="step-4-add-answers"></a>Schritt 4: Hinzufügen von Antworten

Fügen Sie optional semantische Antworten („answers“) hinzu, wenn Sie zusätzliche Verarbeitungsschritte einschließen möchten, die eine semantische Antwort bereitstellen. Weitere Informationen zu diesem Parameter finden Sie unter [Angeben von semantischen Antworten](semantic-answers.md).

```json
"answers": "extractive|count-3",
```

Antworten (und Beschriftungen) werden aus den in searchFields aufgeführten Feldern extrahiert. Aus diesem Grund empfiehlt es sich, inhaltsreiche Felder in searchFields einzubeziehen, damit Sie die besten Antworten in einer Antwort erhalten können. Antworten sind nicht bei jeder Anforderung garantiert. Die Abfrage muss wie eine Frage aussehen, und der Inhalt muss Text enthalten, der wie eine Antwort aussieht.

#### <a name="step-5-add-other-parameters"></a>Schritt 5: Hinzufügen weiterer Parameter

Legen Sie alle anderen Parameter fest, die in der Anforderung enthalten sein sollen. Parameter wie [speller](speller-how-to-add.md), [select](search-query-odata-select.md) und „count“ verbessern die Qualität der Anforderung und die Lesbarkeit der Antwort.

```json
"speller": "lexicon",
"select": "HotelId,HotelName,Description,Category",
"count": true,
"highlightPreTag": "<mark>",
"highlightPostTag": "</mark>",
```

Hervorhebungsstile werden auf Beschriftungen in der Antwort angewendet. Sie können den Standardstil verwenden oder optional den auf Beschriftungen angewendeten Hervorhebungsstil anpassen. Beschriftungen stellen eine Hervorhebung der Formatierung für Schlüsselpassagen im Dokument dar, die die Antwort zusammenfassen. Der Standardwert lautet `<em>`. Wenn Sie den Formatierungstyp angeben möchten (beispielsweise gelber Hintergrund), können Sie „highlightPreTag“ und „highlightPostTag“ festlegen.

## <a name="query-using-azure-sdks"></a>Abfragen mithilfe von Azure-SDKs

Betaversionen der Azure-SDKs bieten Unterstützung für die semantische Suche. Da es sich bei diesen SDKs um Betaversionen handelt, gibt es weder eine Dokumentation noch Beispiele, aber Sie können den Abschnitt zur REST-API oben zurate ziehen, um zu erfahren, wie die APIs funktionieren sollten.

| Azure SDK | Paket |
|-----------|---------|
| .NET | [Azure.Search.Documents package 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)  |
| Java | [com.azure:azure-search-documents 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar)  |
| JavaScript | [azure/search-documents 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2)|
| Python | [azure-search-documents 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="evaluate-the-response"></a>Auswertung der Antwort

Eine Antwort umfasst genau wie bei anderen Abfragen entweder alle als abrufbar markierten Felder oder nur Felder, die im Auswahlparameter aufgeführt sind. Sie enthält die ursprüngliche Relevanzbewertung und kann auch eine Anzahl oder Batchergebnisse enthalten, je nachdem, wie Sie die Anforderung formuliert haben.

In einer semantischen Abfrage weist die Antwort zusätzliche Elemente auf: eine neue Relevanzbewertung mit semantischer Reihenfolge, Beschriftungen in Nur-Text und mit Hervorhebungen und optional eine Antwort.

In einer Client-App können Sie die Suchseite so strukturieren, dass sie eine Beschriftung als Beschreibung der Übereinstimmung und nicht den gesamten Inhalt eines bestimmten Felds enthält. Dies ist hilfreich, wenn einzelne Felder für die Seite mit den Suchergebnissen zu umfangreich sind.

Die Antwort auf die oben stehende Beispielabfrage gibt den folgenden Treffer als beste Übereinstimmung zurück. Beschriftungen werden automatisch zurückgegeben, mit Klartext und hervorgehobenen Versionen. Antworten werden aus dem Beispiel ausgelassen, da keine für diese Abfrage und den Korpus bestimmt werden konnte.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Nächste Schritte

Beachten Sie, dass semantische Rangfolge und semantische Antworten über ein anfängliches Resultset erstellt werden. Jede Logik, die die Qualität der anfänglichen Ergebnisse verbessert, wird an die semantische Suche weitergeleitet. Als nächsten Schritt überprüfen Sie die Features, die zu den anfänglichen Ergebnissen beitragen, einschließlich der Analysetools, die die Tokenisierung von Zeichenketten beeinflussen, der Bewertungsprofile, mit denen die Ergebnisse optimiert werden können, und des Standardrelevanzalgorithmus.

+ [Analysetools für Textverarbeitung](search-analyzers.md)
+ [Ähnlichkeitsalgorithmus für die Rangfolge](index-similarity-and-scoring.md)
+ [Bewertungsprofile](index-add-scoring-profiles.md)
+ [Übersicht über die semantische Suche](semantic-search-overview.md)
+ [Semantische Priorisierung in Azure Cognitive Search](semantic-ranking.md)
