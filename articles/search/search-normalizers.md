---
title: Textnormalisierung für Filter, Facetten und Sortierungen
titleSuffix: Azure Cognitive Search
description: Sie können Normalisierungsfunktionen für Textfelder in einem Index festlegen, um das strikte Verhalten zum Abgleichen von Schlüsselwörtern in Filtern, Facetten und Sortierungen anzupassen.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 9142f03f5f23e843018f7d9b702ef8da3d650758
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112578682"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Textnormalisierung für Filter, Facetten und Sortierungen ohne Beachtung der Groß-/Kleinschreibung

> [!IMPORTANT] 
> Dieses Feature befindet sich in der Public Preview-Phase und unterliegt [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Die [Vorschau-REST-API](/rest/api/searchservice/index-preview) unterstützt dieses Feature.

Beim Suchen und Abrufen von Dokumenten aus einem Azure Cognitive Search-Index muss die Abfrage mit den Inhalten des Dokuments abgeglichen werden. Der Inhalt kann analysiert werden, um Tokens für den Abgleich zu erzeugen, dasselbe gilt bei Verwendung des Parameters `search`. Alternativ kann der Inhalt unverändert für den strikten Schlüsselwortabgleich verwendet werden (siehe `$filter`, `facets` und `$orderby`). Dieser Alles-oder-Nichts-Ansatz deckt die meisten Szenarios ab, er ist jedoch in Fällen unzureichend, in denen einfache Vorverarbeitungen wie Groß-/Kleinschreibung, Entfernung von diakritischen Zeichen, Asciifolding und mehr erforderlich sind, ohne die gesamte Analysekette zu durchlaufen.

Betrachten Sie die folgenden Beispiele:

+ `$filter=City eq 'Las Vegas'` gibt nur Dokumente zurück, die die exakte Textzeichenfolge „Las Vegas“ enthalten, und schließt Dokumente aus, die die Zeichenfolgen „LAS VEGAS“ und „las vegas“ enthalten, was unzureichend ist, wenn der Anwendungsfall alle Dokumente unabhängig von der Groß-/Kleinschreibung erfordert.

+ `search=*&facet=City,count:5` gibt „Las Vegas“, „LAS VEGAS“ und „las vegas“ als separate Werte zurück, obwohl es sich um dieselbe Stadt handelt.

+ `search=usa&$orderby=City` gibt die Städte in lexikografischer Reihenfolge zurück: „Las Vegas“, „Seattle“, „las vegas“. Das ist auch der Fall, wenn die Absicht darin besteht, dieselben Städte unabhängig der Groß-/Kleinschreibung in einer Reihenfolge zu sortieren. 

## <a name="normalizers"></a>Normalisierungsfunktionen

Eine *Normalisierungsfunktion* ist eine Komponente der Suchmaschine, die für die Vorverarbeitung von Text für den Schlüsselwortabgleich zuständig ist. Normalisierungsfunktionen ähneln Analysetools mit der Ausnahme, dass sie die Abfrage nicht in Token umwandeln. Unter anderem können die folgenden Transformationen mithilfe von Normalisierungsfunktionen erzielt werden:

+ Konvertierung in Groß-/Kleinschreibung
+ Normalisierung von diakritischen Zeichen wie „ö“ oder „ê“ in entsprechende ASCII-Zeichen wie „o“ und „e“
+ Zuordnung von Zeichen wie `-` und Leerzeichen zu einem vom Benutzer festgelegten Zeichen

Normalisierungsfunktionen können für Textfelder im Index festgelegt werden. Diese werden sowohl bei der Indizierung als auch bei der Abfrageausführung angewendet.

## <a name="predefined-and-custom-normalizers"></a>Vordefinierte und benutzerdefinierte Normalisierungsfunktionen 

Azure Cognitive Search unterstützt vordefinierte Normalisierungsfunktionen für gängige Anwendungsfälle sowie die Möglichkeit, diese nach Bedarf anzupassen.

| Category | BESCHREIBUNG |
|----------|-------------|
| [Vordefinierte Normalisierungsfunktionen](#predefined-normalizers) | Diese sind vorgefertigt verfügbar und können ohne Konfiguration verwendet werden. |
|[Benutzerdefinierte Normalisierungsfunktionen](#add-custom-normalizers) | Diese sind für fortgeschrittene Szenarios vorgesehen. Sie erfordern eine benutzerdefinierte Konfiguration einer Kombination vorhandener Elemente, die aus Char- und Tokenfiltern bestehen.<sup>1</sup>|

<sup>(1)</sup> Benutzerdefinierte Normalisierungsfunktionen legen keine Tokenizer fest, da Normalisierungsfunktionen immer ein einzelnes Token erzeugen.

## <a name="how-to-specify-normalizers"></a>Festlegen von Normalisierungsfunktionen

Normalisierungsfunktionen können pro Feld für Textfelder (`Edm.String` und `Collection(Edm.String)`) festgelegt werden, die mindestens eine der Eigenschaften `filterable`, `sortable` oder `facetable` mit dem Wert TRUE aufweisen. Das Festlegen einer Normalisierungsfunktion ist optional, und der Standardwert ist `null`. Es wird empfohlen, vordefinierte Normalisierungsfunktionen in Betracht zu ziehen, bevor Sie eine benutzerdefinierte Normalisierungsfunktion konfigurieren, um die Benutzerfreundlichkeit zu verbessern. Testen Sie eine andere Normalisierungsfunktion, wenn die Ergebnisse nicht Ihren Erwartungen entsprechen.

Normalisierungsfunktionen können nur festgelegt werden, wenn ein neues Feld zum Index hinzugefügt wird. Es wird empfohlen, die Normalisierungsanforderungen im Voraus zu bewerten und Normalisierungsfunktionen in den frühen Phasen der Entwicklung zuzuweisen, in denen das Löschen und Neuerstellen von Indizes zur Routine gehört. Normalisierungsfunktionen können nicht für Felder festgelegt werden, die bereits erstellt wurden.

1. Beim Erstellen einer Felddefinition im [Index](/rest/api/searchservice/create-index) legen Sie die Eigenschaft **Normalizer** auf eine der folgenden Optionen fest: eine [vordefinierte Normalisierungsfunktion](#predefined-normalizers) wie `lowercase` oder eine benutzerdefinierte Normalisierungsfunktion (die im gleichen Indexschema definiert wird).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Benutzerdefinierte Normalisierungsfunktionen müssen zuerst im Abschnitt **[normalizers]** des Index definiert und dann wie im vorherigen Schritt gezeigt der Felddefinition zugewiesen werden. Weitere Informationen finden Sie unter [Erstellen des Index](/rest/api/searchservice/create-index) und unter [Hinzufügen benutzerdefinierter Normalisierungsfunktionen](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Zum Ändern der Normalisierungsfunktion eines vorhandenen Felds müssen Sie den Index vollständig neu erstellen (Sie können einzelne Felder nicht neu erstellen).

Eine gute Problemumgehung für Produktionsindizes, bei denen die Neuerstellung von Indizes kostspielig ist, besteht darin, ein neues Feld zu erstellen, die mit dem alten Feld identisch ist, aber die neue Normalisierungsfunktion umfasst, und dieses anstelle des alten Felds zu verwenden. Integrieren Sie das neue Feld mit [Update Index](/rest/api/searchservice/update-index) (Index aktualisieren), und füllen Sie es mit [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents). Später können Sie den Index als Bestandteil der geplanten Indexwartung bereinigen, um veraltete Felder zu entfernen.

## <a name="add-custom-normalizers"></a>Hinzufügen benutzerdefinierter Normalisierungsfunktionen

Benutzerdefinierte Normalisierungsfunktionen werden im Indexschema definiert und können mithilfe der Eigenschaft „field“ festgelegt werden. Die Definition von benutzerdefinierten Normalisierungsfunktionen umfasst einen Namen, einen Typ und jeweils mindestens einen Char- und Tokenfilter. Die Char- und Tokenfilter sind die Bausteine für eine benutzerdefinierte Normalisierungsfunktion und für die Verarbeitung des Texts verantwortlich. Diese Filter werden von links nach rechts angewendet.

 `token_filter_name_1` ist der Name des Tokenfilters, und `char_filter_name_1` und `char_filter_name_2` sind die Namen der Char-Filter (zulässige Werte finden Sie unter [Unterstützte Tokenfilter](#supported-token-filters) und in den unten aufgeführten Tabellen für Char-Filter).

Die Definition der Normalisierungsfunktion ist Teil des Größeren Index. Weitere Informationen zum Rest des Index finden Sie unter [Index-API erstellen](/rest/api/searchservice/create-index).

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Benutzerdefinierte Normalisierungsfunktionen können während der Erstellung des Index oder später hinzugefügt werden, indem Sie einen vorhandenen Index ändern. Für das Hinzufügen einer benutzerdefinierten Normalisierungsfunktion zu einem vorhandenen Index muss das Flag **allowIndexDowntime** im [Update-Index](/rest/api/searchservice/update-index) angegeben werden, was dazu führt, dass der Index einige Sekunden lang nicht verfügbar ist.

## <a name="normalizers-reference"></a>Referenz zu Normalisierungsfunktionen

### <a name="predefined-normalizers"></a>Vordefinierte Normalisierungsfunktionen
|**Name**|**Beschreibung und Optionen**|  
|-|-|  
|Standard| Der Text wird klein geschrieben und anschließend wird Asciifolding durchgeführt.|  
|Kleinbuchstaben| Zeichen werden in Kleinbuchstaben umgewandelt.|
|Großbuchstaben| Zeichen werden in Großbuchstaben umgewandelt.|
|asciifolding| Zeichen, die nicht im Unicodeblock Basis-Lateinisch enthalten sind, werden in ihre ASCII-Entsprechungen umgewandelt, sofern eine vorhanden ist. Beispielsweise wird „à“ in „a“ geändert.|
|elision| Die Elision wird vom Anfang der Token entfernt.|

### <a name="supported-char-filters"></a>Unterstützte Char-Filter
Weitere Informationen über Char-Filter finden Sie in der [Referenz zu Char-Filtern](index-add-custom-analyzers.md#CharFilter).
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Unterstützte Tokenfilter
In der folgenden Liste werden die Tokenfilter aufgeführt, die für Normalisierungsfunktionen unterstützt werden und eine Teilmenge der allgemeinen Tokenfilter darstellen, die an der lexikalischen Analyse beteiligt sind. Weitere Informationen zu den Filtern finden Sie in der [Referenz zu Tokenfiltern](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Beispiel für eine benutzerdefinierte Normalisierungsfunktion

Im folgenden Beispiel wird eine Definition einer benutzerdefinierten Normalisierungsfunktion mit entsprechenden Char- und Tokenfiltern veranschaulicht. Benutzerdefinierte Optionen für Char- und Tokenfilter werden separat in Form von benannten Konstrukten festgelegt und dann in der Definition der Normalisierungsfunktion referenziert (siehe unten).

* Eine benutzerdefinierte Normalisierungsfunktion namens „my_custom_normalizer“ wird im Abschnitt `normalizers` der Indexdefinition definiert.
* Die Normalisierungsfunktion besteht aus zwei Char- und drei Tokenfiltern: „elision“, „lowercase“ und einem benutzerdefinierten Asciifolding-Filter namens „my_asciifolding“.
* Der erste Char-Filter namens „map_dash“ ersetzt alle Bindestriche durch Unterstriche, und der zweite namens „remove_whitespace“ entfernt alle Leerzeichen.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Siehe auch
+ [Analysetools für linguistische Analyse und Textverarbeitung](search-analyzers.md)

+ [Search Documents (Azure Search Service REST API)](/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API)) 
