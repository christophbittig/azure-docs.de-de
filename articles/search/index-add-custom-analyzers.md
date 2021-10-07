---
title: Hinzufügen von benutzerdefinierten Analysetools zu Zeichenfolgenfeldern
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie Texttokenizer und Zeichenfilter, um bei der Indizierung und bei Abfragen Textanalysen für Zeichenfolgen auszuführen.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 9960908f38b0d963a030801dcbb378ced60f0f34
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784715"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Hinzufügen von benutzerdefinierten Analysetools zu Zeichenfolgenfeldern in einem Azure Cognitive Search-Index

Ein *benutzerdefiniertes Analysetool* ist eine benutzerdefinierte Kombination aus einem Tokenizer, einem oder mehreren Tokenfiltern und einem oder mehreren Zeichenfiltern, die im Suchindex definiert und dann in Felddefinitionen referenziert werden, die eine benutzerdefinierte Analyse erfordern. Der Tokenizer ist für das Aufteilen von Text in Token verantwortlich, und mit den Tokenfiltern werden die Token geändert, die vom Tokenizer ausgegeben werden. Mit Zeichenfiltern wird der Eingabetext vorbereitet, bevor er vom Tokenizer verarbeitet wird. Konzepte und Beispiele finden Sie unter [Analysetools für Textverarbeitung in der kognitiven Azure-Suche](search-analyzers.md).

Mit einem benutzerdefinierten Analysetool können Sie den Prozess der Konvertierung von Text in indizierbare und durchsuchbare Token steuern, indem Sie auswählen, welche Arten der Analyse oder Filterung aufgerufen werden sollen und in welcher Reihenfolge sie erfolgen. 

Erstellen Sie ein benutzerdefiniertes Analysetool, und weisen Sie es zu, wenn keines der Standardanalysetools (Standard Lucence), integrierten Analysetools oder Sprachanalysetools Ihren Anforderungen entspricht. Sie können auch ein benutzerdefiniertes Analysetool erstellen, wenn Sie ein integriertes Analysetool mit benutzerdefinierten Optionen verwenden möchten. Wenn Sie beispielsweise „maxTokenLength“ für „Standard“ ändern möchten, erstellen Sie ein benutzerdefiniertes Analysetool mit einem benutzerdefinierten Namen, um diese Option festzulegen.

Dies sind Szenarios, in denen benutzerdefinierte Analysetools hilfreich sein können:

- Verwenden von Zeichenfiltern, um HTML-Markup vor der Tokenisierung von Texteingaben zu entfernen oder bestimmte Zeichen oder Symbole zu ersetzen

- Phonetische Suche. Es wird ein phonetischer Filter hinzugefügt, um Suchen basierend auf der Aussprache eines Worts durchführen zu können, anstatt anhand der Schreibweise.  

- Deaktivieren der lexikalischen Analyse. Verwenden Sie die Schlüsselwortanalyse, um durchsuchbare Felder zu erstellen, die nicht analysiert werden.  

- Schnelle Präfix-/Suffixsuche. Fügen Sie den „Edge N-gram“-Tokenfilter hinzu, um Präfixe von Wörtern zu indizieren und so den schnellen Abgleich von Präfixen zu ermöglichen. Kombinieren Sie dies mit dem Reverse-Tokenfilter, um den Suffixabgleich durchzuführen.  

- Benutzerdefinierte Tokenisierung. Verwenden Sie beispielsweise den Whitespace-Tokenizer, um Sätze mit Leerzeichen als Trennzeichen in Token zu unterteilen.  

- ASCII-Folding. Fügen Sie den standardmäßigen ASCII-Folding-Filter hinzu, um diakritische Zeichen wie ö oder ê in Suchbegriffen zu normalisieren.  

> [!NOTE]  
> Benutzerdefinierte Analysetools werden nicht im Azure-Portal verfügbar gemacht. Die einzige Möglichkeit, ein benutzerdefiniertes Analysetool hinzuzufügen, ist die Verwendung von Code, mit dem ein Index definiert wird. 

## <a name="create-a-custom-analyzer"></a>Erstellen eines benutzerdefinierten Analysetools

Zum Erstellen eines benutzerdefinierten Analysetools geben Sie es zur Entwurfszeit im Abschnitt „analyzers“ eines Index an. Anschließend referenzieren Sie es unter Verwendung der analyzer-Eigenschaft oder dem Paar „indexAnalyzer“ und „searchAnalyzer“ in durchsuchbaren Edm.String-Feldern.

Eine Analysetooldefinition beinhaltet einen Namen, einen Typ, einen oder mehrere Zeichenfilter, maximal einen Tokenizer und einen oder mehrere Tokenfilter für die Verarbeitung nach der Tokenisierung. Zeichenfilter werden vor der Tokenisierung angewandt. Tokenfilter und Zeichenfilter werden von links nach rechts angewandt.

- Die Namen in einem benutzerdefinierten Analysetool müssen eindeutig sein und dürfen nicht mit den Namen von integrierten Analysetools, Tokenizern, Tokenfiltern oder Zeichenfiltern übereinstimmen. Er darf nur Buchstaben, Ziffern, Leerzeichen, Bindestriche und Unterstriche enthalten. Er muss mit alphanumerischen Zeichen beginnen und enden und darf maximal 128 Zeichen lang sein. 

- Der Typ muss „#Microsoft.Azure.Search.CustomAnalyzer“ lauten.

- „charFilters“ kann einen oder mehrere Filter aus [Zeichenfiltern](#CharFilter) enthalten, die vor der Tokenisierung in der angegebenen Reihenfolge verarbeitet werden. Einige Zeichenfilter umfassen Optionen, die im Abschnitt „charFilter“ festgelegt werden können. Zeichenfilter sind optional.

- „tokenizer“ enthält genau einen [Tokenizer](#tokenizers). Ein Wert ist erforderlich. Wenn Sie mehr als einen Tokenizer benötigen, können Sie mehrere benutzerdefinierte Analysetools erstellen und diese feldweise in Ihrem Indexschema zuordnen.

- „tokenFilters“ kann einen oder mehrere Filter aus [Tokenfiltern](#TokenFilters) enthalten, die nach der Tokenisierung in der angegebenen Reihenfolge verarbeitet werden. Fügen Sie für Tokenfilter, die Optionen umfassen, den Abschnitt „tokenFilter“ hinzu, um die Konfiguration anzugeben. Tokenfilter sind optional.

Analysetools dürfen keine Token mit mehr als 300 Zeichen generieren. Andernfalls treten bei der Indizierung Fehler auf. Um lange Token zu kürzen oder auszuschließen, verwenden Sie **TruncateTokenFilter** bzw. **LengthTokenFilter**. Siehe [**Tokenfilter**](#TokenFilters) als Referenz.

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Innerhalb einer Indexdefinition können Sie diesen Abschnitt an beliebiger Stelle im Hauptteil einer Anforderung zum Erstellen eines Index platzieren, aber normalerweise wird er am Ende platziert:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Die Definition der Analysetools ist ein Teil des größeren Index. Definitionen für Zeichenfilter, Tokenizer und Tokenfilter werden dem Index nur hinzugefügt, wenn Sie benutzerdefinierte Optionen festlegen. Um einen vorhandenen Filter oder Tokenizer unverändert zu verwenden, geben Sie ihn in der Analysetooldefinition namentlich an. Weitere Informationen finden Sie unter [Create Index (REST-API)](/rest/api/searchservice/create-index). Weitere Beispiele finden Sie unter [Hinzufügen von Analysetools in Azure Cognitive Search](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Testen von benutzerdefinierten Analysetools

Mit dem [Test Analyzer (REST)](/rest/api/searchservice/test-analyzer) können Sie prüfen, wie ein Analysetool Text in Token aufteilt.

**Anforderung**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Antwort**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Aktualisieren von benutzerdefinierten Analysetools

Nach der Definition können Analysetools, Tokenizer, Tokenfilter oder Zeichenfilter nicht mehr geändert werden. Neue können nur dann einem vorhandenen Index hinzugefügt werden, wenn das `allowIndexDowntime` -Flag in der Anforderung zur Indexaktualisierung auf „true“ gesetzt ist:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Mit diesem Vorgang wird Ihr Index für mindestens ein paar Sekunden offline geschaltet, sodass Indizierungs- und Abfrageanforderungen nicht gelingen. Leistung und Schreibverfügbarkeit des Indexes können nach der Indexaktualisierung mehrere Minuten lang eingeschränkt sein, bei sehr großen Indizes auch länger. Aber diese Auswirkungen sind nur vorübergehend und lösen sich von selbst auf.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Integrierte Analysetools

Wenn Sie ein integriertes Analysetool mit benutzerdefinierten Optionen verwenden möchten, lassen sich diese Optionen durch Erstellen eines benutzerdefinierten Analysetools angeben. Wenn Sie dagegen ein benutzerdefiniertes Analysetool unverändert verwenden möchten, müssen Sie lediglich in der Felddefinition [den entsprechenden Namen angeben](search-analyzers.md#how-to-specify-analyzers).

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Beschreibung und Optionen**|  
|-----------------|-------------------------------|---------------------------|  
|[keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (Der Typ gilt nur, wenn Optionen verfügbar sind.) |Behandelt den gesamten Inhalt eines Felds als ein einzelnes Token. Dies ist nützlich für Daten wie Postleitzahlen, IDs und einige Produktnamen.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Trennt Text flexibel über ein reguläres Ausdrucksmuster in Begriffe. </br></br>**Optionen** </br></br>lowercase (Typ: Boolscher Wert) – bestimmt, ob Begriffe klein geschrieben werden. Der Standardwert ist „true“. </br></br>[pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (Typ: Zeichenfolge) – ein reguläres Ausdrucksmuster zum Abgleich von Tokentrennlinien. Der Standardwert ist `\W+`, was zum Abgleich von Nicht-Wortzeichen dient. </br></br>[flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Typ: Zeichenfolge) – Flags für einen regulären Ausdruck. Der Standardwert ist eine leere Zeichenfolge. Zulässige Werte: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES </br></br>stopwords (Typ: Zeichenfolgenarray) – eine Liste an Stoppwörtern. Der Standardwert ist eine leere Liste.|  
|[simple](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.) |Teilt Text an Nicht-Buchstaben und konvertiert ihn in Kleinbuchstaben. |  
|[standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Wird auch als standard.lucene bezeichnet.)|StandardAnalyzer|Standardmäßiges Lucene-Analysetool, bestehend aus dem Standardtokenizer, Kleinbuchstabenfilter und Stoppfilter. </br></br>**Optionen** </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge. Der Standardwert ist 255. Token, die die maximale Länge überschreiten, werden geteilt. Ein Token darf maximal 300 Zeichen lang sein. </br></br>stopwords (Typ: Zeichenfolgenarray) – eine Liste an Stoppwörtern. Der Standardwert ist eine leere Liste.|  
|standardasciifolding.Lucene|(Der Typ gilt nur, wenn Optionen verfügbar sind.) |Standardanalysetool mit ASCII-Folding-Filter. |  
|[stop](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Teilt Text an Nicht-Buchstaben, wendet die Kleinbuchstaben- und Stoppwort-Tokenfilter an. </br></br>**Optionen** </br></br>stopwords (Typ: Zeichenfolgenarray) – eine Liste an Stoppwörtern. Der Standard ist eine vordefinierte Liste für Englisch. |  
|[whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.) |Ein Analysetool, das den Whitespace-Tokenizer verwendet. Token mit mehr als 255 Zeichen werden geteilt.|  

 <sup>1</sup> Analysetooltypen wird in Code immer „#Microsoft.Azure.Search“ vorangestellt, sodass „PatternAnalyzer“ als „#Microsoft.Azure.Search.PatternAnalyzer“ angegeben wird. Wir haben das Präfix aus Gründen der Übersichtlichkeit entfernt, aber das Präfix ist in Ihrem Code erforderlich.

Der „analyzer_type“ wird nur für Analysetools angegeben, die angepasst werden können. Wenn es keine Optionen gibt, wie es beim Schlüsselwortanalysetool der Fall ist, gibt es keinen zugehörigen #Microsoft.Azure.Search-Typ.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Zeichenfilter

Zeichenfilter ermöglichen die Verarbeitung, bevor eine Zeichenfolge den Tokenizer erreicht.

Cognitive Search unterstützt die Zeichenfilter in der folgenden Liste. Weitere Informationen zu den einzelnen Filtern finden Sie in der Lucene-API-Referenz.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Beschreibung und Optionen**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Ein Zeichenfilter, der versucht, HTML-Konstrukte zu entfernen.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Ein Zeichenfilter, der anhand der Zuordnungsoptionen definierten Zuordnungen anwendet. Der Abgleich ist umfangreich (der längste Musterabgleich an einem bestimmten Punkt wird verwendet). Eine Ersetzung kann eine leere Zeichenfolge sein.  </br></br>**Optionen**  </br></br> mappings (Type: Zeichenfolgenarray) – eine Liste an Zuordnungen im folgenden Format: "a=>b" (alle Vorkommen des Zeichens „a“ werden durch das Zeichen „b“ ersetzt). Erforderlich.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Ein Zeichenfilter, der Zeichen in der Eingabezeichenfolge ersetzt. Er verwendet einen regulären Ausdruck, um zu erhaltende Zeichenfolgen zu identifizieren, und ein Ersatzmuster, um zu ersetzende Zeichen zu identifizieren. Beispiel: input text = "aa  bb aa bb", pattern="(aa)\\\s+(bb)" replacement="$1#$2", result = "aa#bb aa#bb".  </br></br>**Optionen**  </br></br>pattern (Typ: Zeichenfolge) – erforderlich.  </br></br>replacement (Typ: Zeichenfolge) – erforderlich.|  

 <sup>1</sup> Zeichenfiltertypen wird in Code immer „#Microsoft.Azure.Search“ vorangestellt, sodass „MappingCharFilter“ als „#Microsoft.Azure.Search.MappingCharFilter“ angegeben wird. Wir haben das Präfix entfernt, um die Breite der Tabelle zu verringern, denken Sie aber daran, das Präfix in Ihren Code einzubinden. Beachten Sie, dass „char_filter_type“ nur für Filter angegeben wird, die angepasst werden können. Wenn es keine Optionen gibt, wie es bei html_strip der Fall ist, gibt es keinen zugehörigen #Microsoft.Azure.Search-Typ.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizer

Ein Tokenizer teilt fortlaufenden Text in eine Sequenz von Token auf (z. B. Zerlegen eines Satzes in Wörter oder eines Worts in Stammformen). 

Cognitive Search unterstützt die Tokenizer in der folgenden Liste. Weitere Informationen zu den einzelnen Filtern finden Sie in der Lucene-API-Referenz.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Beschreibung und Optionen**|  
|------------------|-------------------------------|---------------------------|  
|[Klassisch](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Grammatikbasierter Tokenizer, der sich für die Verarbeitung der meisten europäischsprachigen Dokumente eignet.  </br></br>**Optionen**  </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge. Standardwert: 255, Maximum: 300. Token, die die maximale Länge überschreiten, werden geteilt.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenisiert die Eingabe von einem Edge in N-Gramme einer festgelegten Größe.  </br></br> **Optionen**  </br></br>minGram (type: int) – Standard: 1, Maximum: 300.  </br></br>maxGram (type: int) – Standard: 2, Maximum: 300. Der Wert muss größer als „minGram“.  </br></br>tokenChars (Typ: Zeichenfolgenarray) – Zeichenklassen, die in den Token beibehalten werden. Zulässige Werte: </br>„letter“, „digit“, „whitespace“, „punctuation“, „symbol“. Der Standardwert ist ein leeres Array – behält alle Zeichen bei. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Gibt die gesamte Eingabe als ein einzelnes Token aus.  </br></br>**Optionen**  </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge. Standardwert: 256, Maximum: 300. Token, die die maximale Länge überschreiten, werden geteilt.|  
|[letter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Teilt Text in nicht-Buchstaben. Token mit mehr als 255 Zeichen werden geteilt.|  
|[lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Teilt Text an Nicht-Buchstaben und konvertiert ihn in Kleinbuchstaben. Token mit mehr als 255 Zeichen werden geteilt.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Teilt Text mit sprachspezifische Regeln auf.  </br></br>**Optionen**  </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge, Standard: 255, Maximum: 300. Token, die die maximale Länge überschreiten, werden geteilt. Token mit mehr als 300 Zeichen werden zunächst in Token mit 300 Zeichen aufgeteilt und dann wird jeder dieser Token basierend auf "maxTokenLength" unterteilt.  </br></br>isSearchTokenizer (Typ: Boolscher Wert) – wird auf „true“ gesetzt, wenn er als Such-Tokenizer verwendet wird, wird auf „false“ gesetzt, wenn er als Indexierungs-Tokenizer verwendet wird. </br></br>language (Type: Zeichenfolge) – zu verwendende Sprache, Standard „english“. Zulässige Werte sind: </br>"bangla", "bulgarian", "catalan", "chineseSimplified",  "chineseTraditional", "croatian", "czech", "danish", "dutch", "english",  "french", "german", "greek", "gujarati", "hindi", "icelandic", "indonesian", "italian", "japanese", "kannada", "korean", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovenian", "spanish", "swedish", "tamil", "telugu", "thai", "ukrainian", "urdu", "vietnamese" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Teilt Text nach sprachspezifischen Regeln auf und reduziert Wörter auf ihre Grundformen. </br></br>**Optionen** </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge, Standard: 255, Maximum: 300. Token, die die maximale Länge überschreiten, werden geteilt. Token mit mehr als 300 Zeichen werden zunächst in Token mit 300 Zeichen aufgeteilt und dann wird jeder dieser Token basierend auf "maxTokenLength" unterteilt. </br></br> isSearchTokenizer (Typ: Boolscher Wert) – wird auf „true“ gesetzt, wenn er als Such-Tokenizer verwendet wird, wird auf „false“ gesetzt, wenn er als Indexierungs-Tokenizer verwendet wird. </br></br>language (Type: Zeichenfolge) – zu verwendende Sprache, Standard „english“. Zulässige Werte sind: </br>"arabic", "bangla", "bulgarian", "catalan", "croatian", "czech", "danish", "dutch", "english", "estonian", "finnish", "french", "german", "greek", "gujarati", "hebrew", "hindi", "hungarian", "icelandic", "indonesian", "italian", "kannada", "latvian", "lithuanian", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovak", "slovenian", "spanish", "swedish", "tamil", "telugu", "turkish", "ukrainian", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenisiert die Eingabe in N-Gramme einer festgelegten Größe. </br></br>**Optionen** </br></br>minGram (type: int) – Standard: 1, Maximum: 300. </br></br>maxGram (type: int) – Standard: 2, Maximum: 300. Der Wert muss größer als „minGram“. </br></br>tokenChars (Typ: Zeichenfolgenarray) – Zeichenklassen, die in den Token beibehalten werden. Zulässige Werte: „letter“, „digit“, „whitespace“, „punctuation“, „symbol“. Der Standardwert ist ein leeres Array – behält alle Zeichen bei. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer für pfadähnliche Hierarchien. **Optionen** </br></br>delimiter (type: Zeichenfolge) – Standard: '/. </br></br>replacement (Typ: Zeichenfolge) – wenn festgelegt, wird das Trennzeichen ersetzt. Standardmäßig ist dies identisch mit dem Wert für das Trennzeichen. </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge. Standardwert: 300, Maximum: 300. Pfade, die länger sind als „MaxTokenLength“ werden ignoriert. </br></br>reverse (Type: Boolscher Wert) – wenn auf „true“ festgelegt, werden Token in umgekehrter Reihenfolge generiert. Standardwert: false. </br></br>skip (Typ: Boolscher Wert) – Erste Token, die übersprungen werden. Die Standardeinstellung ist 0.|  
|[pattern](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Dieser Tokenizer verwendet den RegEx-Musterabgleich, um verschiedene Token zu erstellen. </br></br>**Optionen** </br></br> [pattern](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (Typ: Zeichenfolge): Ein reguläres Ausdrucksmuster zum Abgleich von Tokentrennlinien. Der Standardwert ist `\W+`, was zum Abgleich von Nicht-Wortzeichen dient. </br></br>[flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Typ: Zeichenfolge) – Flags für einen regulären Ausdruck. Der Standardwert ist eine leere Zeichenfolge. Zulässige Werte: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES </br></br>group (Typ: Int) – die Gruppe, die in Token extrahiert werden soll. Der Standardwert ist „-1 (split)“.|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Teilt Text gemäß den [Regeln für Unicode-Textsegmentierung](https://unicode.org/reports/tr29/) auf. </br></br>**Optionen** </br></br>maxTokenLength (Typ: int) – die maximale Tokenlänge. Standardwert: 255, Maximum: 300. Token, die die maximale Länge überschreiten, werden geteilt.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenisiert URLs und E-Mails als ein Token. </br></br>**Optionen** </br></br> maxTokenLength (Typ: int) – die maximale Tokenlänge. Standardwert: 255, Maximum: 300. Token, die die maximale Länge überschreiten, werden geteilt.|  
|[whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.) |Teilt Text an den Leerzeichen auf. Token mit mehr als 255 Zeichen werden geteilt.|  

 <sup>1</sup> Tokenizertypen wird in Code immer „#Microsoft.Azure.Search“ vorangestellt, sodass „ClassicTokenizer“ als „#Microsoft.Azure.Search.ClassicTokenizer“ angegeben wird. Wir haben das Präfix entfernt, um die Breite der Tabelle zu verringern, denken Sie aber daran, das Präfix in Ihren Code einzubinden. Beachten Sie, dass „tokenizer_type“ nur für Tokenizer angegeben wird, die angepasst werden können. Wenn es keine Optionen gibt, wie es beim Buchstabentokenizer der Fall ist, gibt es keinen zugehörigen #Microsoft.Azure.Search-Typ.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Tokenfilter

Ein Tokenfilter wird verwendet, um die von einem Tokenizer generierten Token herauszufiltern oder zu ändern. Sie können beispielsweise einen Filter für Kleinbuchstaben angeben, mit dem alle Zeichen in Kleinbuchstaben konvertiert werden. Sie können in einem benutzerdefinierten Analysemodul mehrere Tokenfilter verwenden. Tokenfilter werden in der Reihenfolge ausgeführt, in der sie aufgeführt sind.

In der folgenden Tabelle bieten die Tokenfilter, die mit Apache Lucene implementiert wurden, Links zur Lucene-API-Dokumentation.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Beschreibung und Optionen**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Ein Tokenfilter, der den Normalisierer für arabische Sprachen anwendet, um die Orthographie zu normalisieren.|  
|[apostrophe](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Entfernt alle Zeichen nach einem Apostroph (einschließlich des Apostrophs). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Konvertiert alphabetische, numerische und symbolische Unicode-Zeichen, die nicht in den ersten 127 ASCII-Zeichen (dem Unicode-Block „Basic Latin“) enthalten sind, in ihre ASCII-Äquivalente, falls vorhanden.<br /><br /> **Optionen**<br /><br /> preserveOriginal (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, wir der ursprüngliche Token beibehalten. Die Standardeinstellung ist „false“.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Bildet Bigramme von CJK-Benennungen, die aus StandardTokenizer generiert werden.<br /><br /> **Optionen**<br /><br /> ignoreScripts (Typ: Zeichenfolgenarray) – zu ignorierende Skripte. Zulässige Werte sind: „han“, „hiragana“, „katakana“, „hangul“. Der Standardwert ist eine leere Liste.<br /><br /> outputUnigrams (Typ: Boolscher Wert) – auf „true“ setzen, wenn Sie immer sowohl Unigramme als auch Bigramme ausgeben möchten. Die Standardeinstellung ist „false“.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Normalisiert CJK-Breitenabweichungen. Faltet ASCII-Varianten mit voller Breite in die äquivalente Basislatein-Varianten und Katakana-Varianten mit halber Breite in die äquivalente Kana-Versionen. |  
|[Klassisch](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Entfernt die Possessive und Punkte aus Akronymen. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Konstruiert Bigramme für häufig vorkommende Begriffe während der Indexierung. Einzelne Begriffe werden ebenfalls indexiert und mit Bigrammen überlagert.<br /><br /> **Optionen**<br /><br /> commonWords (Typ: Zeichenfolgenarray) – der Satz an allgemeinen Wörtern. Der Standardwert ist eine leere Liste. Erforderlich.<br /><br /> ignoreCase (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, wird beim Abgleich die Groß-/Kleinschreibung berücksichtigt. Die Standardeinstellung ist „false“.<br /><br /> queryMode (Typ: Boolscher Wert) – Generiert Bigramme und entfernt dann allgemeine Wörter und einzelne Begriffe, gefolgt von einem allgemeinen Wort. Die Standardeinstellung ist „false“.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Zerlegt zusammengesetzte Wörter, die in vielen germanischen Sprachen vorkommen.<br /><br /> **Optionen**<br /><br /> wordList (Typ: Zeichenfolgenarray) – die Liste der Wörter, die abgeglichen wird. Der Standardwert ist eine leere Liste. Erforderlich.<br /><br /> minWordSize (Typ: int) – es werden nur Wörter verarbeitet, die länger als dieser Wert sind. Der Standardwert ist 5.<br /><br /> minSubwordSize (Typ: int) – es werden nur Wörter ausgegeben, die länger als dieser Wert sind. Der Standardwert ist 2.<br /><br /> maxSubwordSize (Typ: int) – es werden nur Wörter ausgegeben, die kürzer als dieser Wert sind. Der Standardwert ist 15.<br /><br /> onlyLongestMatch (Typ: Boolscher Wert) – es wird nur das Teilwort mit der längsten Übereinstimmung ausgegeben. Die Standardeinstellung ist „false“.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generiert N-Gramme einer festgelegten Größe beginnend am Anfang oder Ende eines Ausgabetokens.<br /><br /> **Optionen**<br /><br /> minGram (type: int) – Standard: 1, Maximum: 300.<br /><br /> maxGram (type: int) – Standard: 2, Maximum 300. Der Wert muss größer als „minGram“.<br /><br /> side (Typ: Zeichenfolge) – gibt an, von welcher Seite der Eingabe aus das N-Gramm generiert werden soll. Zulässige Werte: „front“, „back“ |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Entfernt Elisionen. Beispielsweise wird „l'avion“ (das Flugzeug) in „avion“ (Flugzeug) konvertiert.<br /><br /> **Optionen**<br /><br /> articles (Typ: Zeichenfolgenarray) – ein Satz an zu entfernenden Artikeln. Der Standardwert ist eine leere Liste. Wenn es keine Liste der Artikel gibt, werden standardmäßig alle französischen Artikel entfernt.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Normalisiert deutsche Zeichen gemäß der Heuristik des [German2 Snowball-Algorithmus](https://snowballstem.org/algorithms/german2/stemmer.html).|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Normalisiert Text in Hindi, um einige Unterschiede in der Schreibweise zu beseitigen. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalisiert die Unicode-Darstellung von Text in indischen Sprachen.
|[keep](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Ein Tokenfilter, der nur Token mit Text in der angegebenen Wortliste enthält.<br /><br /> **Optionen**<br /><br /> keepWords (Typ: Zeichenfolgenarray) – eine Liste von beizubehaltenden Wörtern. Der Standardwert ist eine leere Liste. Erforderlich.<br /><br /> keepWordsCase (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, werden zunächst alle Wörter klein geschrieben. Die Standardeinstellung ist „false“.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Begriffe werden als Schlüsselwörter gekennzeichnet.<br /><br /> **Optionen**<br /><br /> keywords (Typ: Zeichenfolgenarray) – eine Liste von Wörtern, die als Schlüsselwörter gekennzeichnet werden. Der Standardwert ist eine leere Liste. Erforderlich.<br /><br /> ignoreCase (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, werden zunächst alle Wörter klein geschrieben. Die Standardeinstellung ist „false“.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Gibt jedes eingehende Token zweimal als Schlüsselwort und einmal als Nicht-Schlüsselwort aus. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Ein Hochleistungs-Kstem-Filter für Englisch. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Entfernt die Wörter, die zu lang oder zu kurz sind.<br /><br /> **Optionen**<br /><br /> min (Typ: int) – die Mindestanzahl. Standardwert: 0, Maximum: 300.<br /><br /> max (type: int) – die Maximalanzahl. Standardwert: 300, Maximum: 300.|  
|[limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Beschränkt die Anzahl der Token während der Indizierung.<br /><br /> **Optionen**<br /><br /> MaxTokenCount (Typ: Int) – maximale Anzahl der zu erzeugenden Token. Der Standardwert ist 1.<br /><br /> consumeAllTokens (Typ: Boolscher Wert) – legt fest, ob alle Token aus der Ausgabe verarbeitet werden, auch wenn der Wert für „maxTokenCount“ erreicht ist. Die Standardeinstellung ist „false“.|  
|[lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Normalisiert den Tokentext in Kleinbuchstaben. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generiert N-Gramme einer festgelegten Größe.<br /><br /> **Optionen**<br /><br /> minGram (type: int) – Standard: 1, Maximum: 300.<br /><br /> maxGram (type: int) – Standard: 2, Maximum 300. Der Wert muss größer als „minGram“.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Verwendet Java-RegExe zur Ausgabe mehrerer Token – eines für jede Erfassungsgruppe in einem oder mehreren Mustern.<br /><br /> **Optionen**<br /><br /> patterns (Typ: Zeichenfolgenarray) – eine Liste von Mustern, die mit jedem Token abgeglichen werden. Erforderlich.<br /><br /> preserveOriginal (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, wird der ursprüngliche Token zurückgegeben, auch wenn eines der Muster übereinstimmt, Standard: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Ein Tokenfilter, der jedem Token im Stream ein Muster zuweist und Übereinstimmungen durch die angegebene Ersetzungszeichenkette ersetzt.<br /><br /> **Optionen**<br /><br /> pattern (Typ: Zeichenfolge) – erforderlich.<br /><br /> replacement (Typ: Zeichenfolge) – erforderlich.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.) |Wendet die Normalisierung für Persisch an. |  
|[phonetic](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Erstellt Token für phonetische Übereinstimmungen.<br /><br /> **Optionen**<br /><br /> encoder (Typ: Zeichenfolge) – zu verwendender phonetischer Encoder. Zulässige Werte sind: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Standard: "metaphone". Der Standard lautet "metaphone".<br /><br /> Weitere Informationen finden Sie unter [Encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html).<br /><br /> replace (Typ: Boolscher Wert) – wird auf „true“ gesetzt, wenn das kodierte Token das ursprüngliche Token ersetzen soll, „false“, wenn sie als Synonyme hinzugefügt werden sollen. Der Standardwert ist „true“.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Transformiert den Tokenstream gemäß [Porter-Stemmer-Algorithmus](https://tartarus.org/~martin/PorterStemmer/). |  
|[reverse](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Kehrt die Tokenzeichenfolge um. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Normalisiert die Verwendung der austauschbar skandinavische Zeichen. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Faltet skandinavische Zeichen åÅäæÄÆ->a und öÖøØ->o. Es unterscheidet auch die Verwendung von doppelten Vokalen aa, ae, ae, ao, ao, oe und oo, wobei nur der erste übrig bleibt. |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Kombiniert Token zu einem einzelnen Token.<br /><br /> **Optionen**<br /><br /> maxShingleSize (Typ: int) – Standard ist 2.<br /><br /> minShingleSize (Typ: int) – Standard ist 2.<br /><br /> outputUnigrams (Typ: Boolscher Wert) – wenn „true“ enthält der Ausgabestream die Eingabetoken (Unigramme) und die Schindeln. Der Standardwert ist „true“.<br /><br /> outputUnigramsIfNoShingles (Typ: Boolscher Wert) – wenn „true“ wird das Verhalten von „outputUnigrams==false“ für die Fälle überschrieben, bei denen keine Schindeln verfügbar sind. Die Standardeinstellung ist „false“.<br /><br /> tokenSeparator (Typ: Zeichenfolge) – Die Zeichenfolge, die beim Verbinden benachbarter Token zum Bilden einer Schindel verwendet werden soll. Der Standardwert ist „ “.<br /><br /> FilterToken (Typ: Zeichenfolge) – die einzufügende Zeichenfolge für jede Position, an der sich kein Token befindet. Der Standardwert ist „_“.|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball-Tokenfilter.<br /><br /> **Optionen**<br /><br /> language (Typ: Zeichenfolge) – Zulässige Werte sind: "armenian", "basque", "catalan", "danish", "dutch", "english", "finnish", "french", "german", "german2", "hungarian", "italian", "kp", "lovins", "norwegian", "porter", "portuguese", "romanian", "russian", "spanish", "swedish", "turkish"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalisiert die Unicode-Darstellung von Text in Sorani.<br /><br /> **Optionen**<br /><br /> Keine.|  
|stemmer|StemmerTokenFilter|Sprachspezifischer für die Wortstammerkennung.<br /><br /> **Optionen**<br /><br /> language (Typ: Zeichenfolge) – Zulässige Werte sind: <br /> -   ["arabic"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["armenian"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazilian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />–   "bulgarian"<br />-   ["catalan"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["danish"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["english"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finnish"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />–   "lightFinnish"<br />-   ["french"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />–   "galician"<br />–   "minimalGalician"<br />-   ["german"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />–   "minimalGerman"<br />-   ["greek"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />–   "hindi"<br />-   ["hungarian"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonesian"](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   ["irish"](https://snowballstem.org/algorithms/irish/stemmer.html)<br />-   ["italian"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["latvian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norwegian"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portuguese"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romanian"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russian"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["spanish"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["swedish"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />–   "lightSwedish"<br />-   ["turkish"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Alle aus einem Wörterbuch stammenden Begriffe werden als Schlüsselwörter markiert, wodurch verhindert wird, dass sie die Kette hinuntergereicht werden. Muss vor dem Filter für die Wortstammerkennung platziert werden.<br /><br /> **Optionen**<br /><br /> rules (Typ: Zeichenfolgenarray) – Wortstammerkennungsregeln im folgenden Format: "word => stem" zum Beispiel "ran => run". Der Standardwert ist eine leere Liste.  Erforderlich.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Entfernt Stoppwörter aus einem Tokenstream. Standardmäßig verwendet der Filter eine vordefinierte Stoppwortliste für Englisch.<br /><br /> **Optionen**<br /><br /> stopwords (Typ: Zeichenfolgenarray) – eine Liste an Stoppwörtern. Kann nicht angegeben werden, wenn eine „stopwordsList“ angegeben wird.<br /><br /> stopwordsList (Typ: Zeichenfolge) – eine vordefinierte Liste an Stoppwörtern. Kann nicht angegeben werden, wenn ein Stoppwort angegeben ist. Zulässige Werte sind:"arabic", "armenian", "basque", "brazilian", "bulgarian", "catalan", "czech", "danish", "dutch", "english", "finnish", "french", "galician", "german", "greek", "hindi", "hungarian", "indonesian", "irish", "italian", "latvian", "norwegian", "persian", "portuguese", "romanian", "russian", "sorani", "spanish", "swedish", "thai", "turkish", default: "english". Kann nicht angegeben werden, wenn ein Stoppwort angegeben ist. <br /><br /> ignoreCase (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, werden zunächst alle Wörter klein geschrieben. Die Standardeinstellung ist „false“.<br /><br /> removeTrailing (Typ: Boolscher Wert) – wenn auf „true“ gesetzt, wird der letzte Suchbegriff ignoriert, wenn dieser ein Stoppwort ist. Der Standardwert ist „true“.
|[synonym](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Gleicht Synonyme mit einzelnen oder mehreren Wörtern in einem Tokenstream ab.<br /><br /> **Optionen**<br /><br /> synonyms (Typ: Zeichenfolgenarray) – erforderlich. Liste von Synonymen in einem der beiden folgenden Formate:<br /><br /> -incredible, unbelievable, fabulous => amazing – alle Begriffe auf der linken Seite von => werden durch alle Begriffe auf der rechten Seite ersetzt.<br /><br /> -incredible, unbelievable, fabulous, amazing – eine durch Kommas getrennte Liste von äquivalenten Wörtern. Legen Sie die expand-Option fest, um zu ändern, wie diese Liste interpretiert wird.<br /><br /> ignoreCase (Type: Boolscher Wert) – Groß-/Kleinschreibung faltet die Eingabe für den Abgleich. Die Standardeinstellung ist „false“.<br /><br /> expand (Typ: Boolscher Wert) – wenn „true“ werden alle Wörter in der Liste der Synonyme (wenn Notation => nicht verwendet wird) einander zugeordnet. <br />Die folgende Liste: „incredible, unbelievable, fabulous, amazing“ entspricht: „incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing“<br /><br />Wenn „false“: die folgende Liste: „incredible, unbelievable, fabulous, amazing“ entspricht: „ incredible, unbelievable, fabulous, amazing => incredible“|  
|[trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Entfernt führende und nachfolgende Leerzeichen aus Token. |  
|[truncate](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Schneidet die Begriffe auf eine bestimmte Länge zu.<br /><br /> **Optionen**<br /><br /> length (Typ: int) – Standard: 300, Maximum: 300. Erforderlich.|  
|[unique](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtert Token mit dem gleichen Text wie das vorherige Token heraus.<br /><br /> **Optionen**<br /><br /> onlyOnSamePosition (Typ: Boolscher Wert) – wenn festgelegt, werden nur Duplikate an der selben Position entfernt. Der Standardwert ist „true“.|  
|[uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(Der Typ gilt nur, wenn Optionen verfügbar sind.)  |Normalisiert Tokentext in Großbuchstaben. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Unterteilt Wörter in Teilwörter und führt optionale Transformationen in Teilwortgruppen durch.<br /><br /> **Optionen**<br /><br /> generateWordParts (Typ: Boolscher Wert) – dadurch werden Teile von Wörtern generiert, z.B. wird aus „AzureSearch“ „Azure“ „Search“. Der Standardwert ist „true“.<br /><br /> generateNumberParts (Typ: Boolscher Wert) – führt dazu, dass Nummernteilwörter generiert werden. Der Standardwert ist „true“.<br /><br /> catenateWords (Typ: Boolscher Wert) – verursacht maximale Durchläufe von Wortteilen, die verkettete werden, z.B. wird aus „Azure-Search“ „AzureSearch“. Die Standardeinstellung ist „false“.<br /><br /> catenateNumbers (Typ: Boolscher Wert) – verursacht maximale Durchläufe von Nummernteilen, die verkettete werden, z.B. wird aus „1-2“ „12“. Die Standardeinstellung ist „false“.<br /><br /> catenateAll (Typ: Boolscher Wert) –verursacht, dass alle Teilwörterteile verkettete werden, z.B. wird aus „Azure-Search-1“ „AzureSearch1“. Die Standardeinstellung ist „false“.<br /><br /> splitOnCaseChange (Typ: Boolscher Wert) – wenn „true“, werden Wörter bei einem Wechsel der Schreibung aufgeteilt, z.B. wird aus „AzureSearch“ „Azure“ „Search“. Der Standardwert ist „true“.<br /><br /> preserveOriginal – führt dazu, dass die ursprünglichen Wörter beibehalten und zur Teilwortliste hinzugefügt werden. Die Standardeinstellung ist „false“.<br /><br /> splitOnNumerics (Typ: Boolscher Wert) – wenn „true“ erfolgt die Teilung bei Zahlen, z.B. wird aus „Azure1Search“ „Azure“ „1“ „Search“. Der Standardwert ist „true“.<br /><br /> stemEnglishPossessive (Typ: Boolscher Wert) – führt dazu, dass das nachgestellte „s“ für jedes Teilwort entfernt werden. Der Standardwert ist „true“.<br /><br /> protectedWords (Type: Zeichenfolgenarray) – Token, die vor dem Trennen geschützt werden sollen. Der Standardwert ist eine leere Liste.|  

 <sup>1</sup> Tokenfiltertypen wird in Code immer „#Microsoft.Azure.Search“ vorangestellt, sodass „ArabicNormalizationTokenFilter“ als „#Microsoft.Azure.Search.ArabicNormalizationTokenFilter“ angegeben wird.  Wir haben das Präfix entfernt, um die Breite der Tabelle zu verringern, denken Sie aber daran, das Präfix in Ihren Code einzubinden.  

## <a name="see-also"></a>Weitere Informationen

- [REST-APIs für die kognitive Azure-Suche](/rest/api/searchservice/)
- [Analysetools in Azure Cognitive Search (Beispiele)](search-analyzers.md#examples)
- [Erstellen eines Index (REST)](/rest/api/searchservice/create-index)
