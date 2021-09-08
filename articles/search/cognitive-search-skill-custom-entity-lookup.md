---
title: Cognitive Search-Qualifikation „Benutzerdefinierte Entitätssuche“
titleSuffix: Azure Cognitive Search
description: Extrahieren Sie verschiedene benutzerdefinierte Entitäten aus Text in einer Azure Cognitive Search-Pipeline für die kognitive Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 977ac567f195e0ab8053d7b8bd98543801a3b6a4
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349851"
---
# <a name="custom-entity-lookup-cognitive-skill"></a>Cognitive Search-Qualifikation „Benutzerdefinierte Entitätssuche“

Die Qualifikation **Benutzerdefinierte Entitätssuche** sucht nach Text aus einer benutzerdefinierten Liste von Wörtern und Ausdrücken. Mithilfe dieser Liste werden alle Dokumente mit übereinstimmenden Entitäten mit einer Bezeichnung markiert. Die Qualifikation unterstützt auch einen gewissen Grad an Fuzzyübereinstimmung, der für die Suche nach ähnlichen, aber nicht exakten Übereinstimmungen verwendet werden kann.  

> [!NOTE]
> Diese Qualifikation ist nicht an eine Cognitive Services-API gebunden, erfordert jedoch einen Cognitive Services-Schlüssel, um mehr als 20 Transaktionen zu unterstützen. Diese Qualifikation wird [über Cognitive Search abgerechnet](https://azure.microsoft.com/pricing/details/search/#pricing).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Datengrenzwerte
+ Die maximal unterstützte Größe für den Eingabedatensatz beträgt 256 MB. Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an die Qualifikation „Benutzerdefinierte Entitätssuche“ senden, denken Sie daran, die [Qualifikation „Text teilen“](cognitive-search-skill-textsplit.md) zu verwenden.
+ Die maximal unterstützte Größe der Tabelle für Entitätsdefinitionen beträgt 10 MB, wenn sie mithilfe des Parameters *entitiesDefinitionUri* bereitgestellt wird. 
+ Wenn die Entitäten inline mit dem Parameter *inlineEntitiesDefinition* definiert werden, beträgt die maximal unterstützte Größe 10 KB.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Pfad zu einer JSON- oder CSV-Datei, die den gesamten Zieltext enthält, mit dem verglichen werden soll. Diese Entitätsdefinition wird am Anfang der Ausführung eines Indexers gelesen. Alle Aktualisierungen an dieser Datei während der Ausführung werden erst in nachfolgenden Ausführungen berücksichtigt. Auf diese Konfiguration muss über HTTPS zugegriffen werden können. Weitere Informationen zum erwarteten CSV- oder JSON-Schema finden Sie unter [Benutzerdefiniertes Entitätsdefinitionsformat](#custom-entity-definition-format) weiter unten.|
|`inlineEntitiesDefinition` | Inline-JSON-Entitätsdefinitionen. Dieser Parameter ersetzt den entitiesDefinitionUri-Parameter, falls vorhanden. Es können nicht mehr als 10 KB der Konfiguration inline bereitgestellt werden. Weitere Informationen zum erwarteten JSON-Schema finden Sie unter [Benutzerdefinierte Entitätsdefinition](#custom-entity-definition-format) weiter unten. |
|`defaultLanguageCode` |    (Optional) Sprachcode des Eingabetexts, der verwendet wird, um den Eingabetext mit Token zu versehen und abzugrenzen. Die folgenden Sprachen werden unterstützt: `da, de, en, es, fi, fr, it, ko, pt`. Die Standardsprache ist Englisch (`en`). Wenn Sie ein Sprachcode-Ländercode-Format übergeben, wird nur der Sprachcodeteil des Formats verwendet.  |
|`globalDefaultCaseSensitive` | (Optional) Hierbei handelt es sich um einen Standardwert zur Beachtung der Groß-/Kleinschreibung für den Skill. Wenn der Wert `defaultCaseSensitive` einer Entität nicht angegeben ist, wird dieser Wert als `defaultCaseSensitive`-Wert für die Entität verwendet. |
|`globalDefaultAccentSensitive` | (Optional) Hierbei handelt es sich um einen Standardwert für die Beachtung von Diakritika für den Skill. Wenn der Wert `defaultAccentSensitive` einer Entität nicht angegeben ist, wird dieser Wert als `defaultAccentSensitive`-Wert für die Entität verwendet. |
|`globalDefaultFuzzyEditDistance` | (Optional) Hierbei handelt es sich um einen Standardwert zur Fuzzybearbeitungsdistanz für den Skill. Wenn der Wert `defaultFuzzyEditDistance` einer Entität nicht angegeben ist, wird dieser Wert als `defaultFuzzyEditDistance`-Wert für die Entität verwendet. |

## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `text`          | Der zu analysierende Text          |
| `languageCode`    | Optional. Der Standardwert ist `"en"`.  |


## <a name="skill-outputs"></a>Skillausgaben


| Ausgabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `entities` | Ein Array von Objekten, die Informationen über die gefundenen Übereinstimmungen enthalten, sowie zugehörige Metadaten. Jede der identifizierten Entitäten kann die folgenden Felder enthalten:  <ul> <li> *name:* Identifizierte Entität der obersten Ebene. Die Entität stellt die „normalisierte“ Form dar. </li> <li> *id:*  Ein eindeutiger Bezeichner für die Entität, wie vom Benutzer im „Benutzerdefiniertes Entitätsdefinitionsformat“ definiert.</li> <li> *description*: Entitätsbeschreibung, wie vom Benutzer im „Benutzerdefiniertes Entitätsdefinitionsformat“ definiert. </li> <li> *type:* Entitätstyp, wie vom Benutzer im „Benutzerdefiniertes Entitätsdefinitionsformat“ definiert.</li> <li> *subtype:* Entitätsuntertyp, wie vom Benutzer im „Benutzerdefiniertes Entitätsdefinitionsformat“ definiert.</li>  <li> *matches*: Sammlung, die die einzelnen Übereinstimmungen für diese Entität im Quelltext beschreibt. Jede Übereinstimmung verfügt über die folgenden Elemente: </li> <ul> <li> *text*: Die unformatierte Textübereinstimmung aus dem Quelldokument. </li> <li> *offset*: Die Fundstelle der Übereinstimmung im Text. </li> <li> *length*:  Die Länge des übereinstimmenden Texts. </li> <li> *matchDistance*: Die Anzahl der Zeichen, in denen sich diese Übereinstimmung vom ursprünglichen Entitätsnamen oder -alias unterscheidet.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Benutzerdefiniertes Entitätsdefinitionsformat

Es gibt drei verschiedene Möglichkeiten, die Liste der benutzerdefinierten Entitäten für die Qualifikation „Benutzerdefinierte Entitätssuche“ bereitzustellen. Sie können die Liste in einer CSV-Datei, einer JSON-Datei oder als Inlinedefinition als Teil der Qualifikationsdefinition zur Verfügung stellen.  

Wenn es sich bei der Definitionsdatei um eine CSV- oder JSON-Datei handelt, muss der Pfad der Datei als Teil des *entitiesDefinitionUri*-Parameters bereitgestellt werden. In diesem Fall wird die Datei einmal am Anfang jeder Indexerausführung heruntergeladen. Die Datei muss verfügbar sein, solange der Indexer ausgeführt werden soll. Außerdem muss die Datei UTF-8-codiert sein.

Wenn die Definition inline bereitgestellt wird, sollte Sie inline als Inhalt des *inlineEntitiesDefinition*-Skillparameters bereitgestellt werden. 

### <a name="csv-format"></a>CSV-Format

Sie können die Definition der benutzerdefinierten Entitäten, nach denen gesucht werden soll, in einer CSV-Datei (Comma-Separated Value) bereitstellen, indem Sie den Pfad zur Datei angeben und ihn im Skillparameter *entitiesDefinitionUri* festlegen. Der Pfad sollte sich an einem https-Speicherort befinden. Die Definitionsdatei kann bis zu 10 MB groß sein.

Das CSV-Format ist einfach. Jede Zeile stellt eine einzelne Einheit dar, wie unten dargestellt:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

In diesem Fall gibt es drei Entitäten, die als gefundene Entitäten zurückgegeben werden können (Bill Gates, Satya Nadella, Microsoft), aber sie werden erkannt, wenn einer der Begriffe in der Zeile (Aliase) mit dem Text übereinstimmt. Wird in einem Dokument beispielsweise die Zeichenfolge „William H. Gates“ gefunden, wird eine Übereinstimmung für die Entität „Bill Gates“ zurückgegeben.

### <a name="json-format"></a>JSON-Format

Sie können auch die Definition der benutzerdefinierten Entitäten angeben, nach denen in einer JSON-Datei gesucht werden soll. Das JSON-Format bietet Ihnen etwas mehr Flexibilität, da es Ihnen erlaubt, Übereinstimmungsregeln pro Begriff zu definieren. Sie können z. B. die Fuzzyübereinstimmungsdistanz (Damerau-Levenshtein-Distanz) für jeden Begriff festlegen oder angeben, ob bei der Übereinstimmung Groß-/Kleinschreibung beachtet werden soll oder nicht. 

 Genau wie bei CSV-Dateien müssen Sie den Pfad zur JSON-Datei angeben und ihn im Skillparameter *entitiesDefinitionUri* festlegen. Der Pfad sollte sich an einem https-Speicherort befinden. Die Definitionsdatei kann bis zu 10 MB groß sein.

Die einfachste JSON-Definition einer benutzerdefinierten Entitätsliste kann eine Liste von Entitäten sein, die übereinstimmen müssen:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Ein komplexeres Beispiel einer JSON-Definition kann optional die ID, die Beschreibung, den Typ und den Untertyp jeder Entität sowie andere *Aliase* angeben. Auch wenn ein Aliasbegriff übereinstimmt, wird die Entität zurückgegeben:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

In den folgenden Tabellen werden die verschiedenen Konfigurationsparameter ausführlicher beschrieben, die Sie bei der Definition der Entitäten festlegen können, die übereinstimmen sollen:

|  Feldname  |        BESCHREIBUNG  |
|--------------|----------------------|
| `name` | Entitätsdeskriptor der obersten Ebene. Übereinstimmungen in der Ausgabe der Qualifikation werden nach diesem Namen gruppiert; er sollte die „normalisierte“ Form des gefundenen Texts darstellen.  |
| `description`  | (Optional) Dieses Feld kann als Pass-Through-Feld für benutzerdefinierte Metadaten über die übereinstimmenden Texte verwendet werden. Der Wert dieses Felds wird mit jeder Übereinstimmung zur Entität in der Ausgabe der Qualifikation angezeigt. |
| `type` | (Optional) Dieses Feld kann als Pass-Through-Feld für benutzerdefinierte Metadaten über die übereinstimmenden Texte verwendet werden. Der Wert dieses Felds wird mit jeder Übereinstimmung zur Entität in der Ausgabe der Qualifikation angezeigt. |
| `subtype` | (Optional) Dieses Feld kann als Pass-Through-Feld für benutzerdefinierte Metadaten über die übereinstimmenden Texte verwendet werden. Der Wert dieses Felds wird mit jeder Übereinstimmung zur Entität in der Ausgabe der Qualifikation angezeigt. |
| `id` | (Optional) Dieses Feld kann als Pass-Through-Feld für benutzerdefinierte Metadaten über die übereinstimmenden Texte verwendet werden. Der Wert dieses Felds wird mit jeder Übereinstimmung zur Entität in der Ausgabe der Qualifikation angezeigt. |
| `caseSensitive` | (Optional) Der Standardwert ist „false“. Boolescher Wert, der angibt, ob bei Vergleichen mit dem Entitätsnamen die Groß-/Kleinschreibung beachtet werden soll. Beispiel für Übereinstimmungen mit „Microsoft“ ohne Berücksichtigung der Groß-/Kleinschreibung: microsoft, microSoft, MICROSOFT |
| `accentSensitive` | (Optional) Der Standardwert ist „false“. Hierbei handelt es sich um einen booleschen Wert, der angibt, ob zwischen Buchstaben mit oder ohne Diakritika (z. B. „é“ und „e“) unterschieden werden soll. |
| `fuzzyEditDistance` | (Optional) Der Standardwert ist „0“. Der Maximalwert ist „5“. Legt die zulässige Anzahl von abweichenden Zeichen fest, die noch als eine Übereinstimmung mit dem Entitätsnamen betrachtet werden. Die kleinste mögliche Fuzzyübereinstimmung für eine bestimmte Übereinstimmung wird zurückgegeben.  Wenn die Bearbeitungsdistanz beispielsweise auf 3 festgelegt ist, stimmt „Windows 10“ noch überein mit „Windows“, „Windows 10“ und „Windows 7“. <br/> Ist die Groß-/Kleinschreibung auf „false“ festgelegt, werden Unterschiede in der Groß-/Kleinschreibung hinsichtlich der Fuzzyübereinstimmung nicht berücksichtigt; andernfalls schon. |
| `defaultCaseSensitive` | (Optional) Ändert den Standardwert für die Groß-/Kleinschreibung dieser Entität. Dieser Parameter kann dazu verwendet werden, den Standardwert für alle Aliase von caseSensitive-Werten zu ändern. |
| `defaultAccentSensitive` | (Optional) Dieser Parameter ändert den Standardwert für die Beachtung von Diakritika für diese Entität. Dieser Parameter kann dazu verwendet werden, den Standardwert für alle Aliase von accentSensitive-Werten zu ändern.|
| `defaultFuzzyEditDistance` | (Optional) Ändert den Standardwert für die Fuzzybearbeitungsdistanz dieser Entität. Es kann verwendet werden, um die Standardeinstellung für den fuzzyEditDistance-Wert aller Aliase zu ändern. |
| `aliases` | (Optional) Ein Array komplexer Objekte, die verwendet werden können, um alternative Schreibweisen oder Synonyme für den Stammnamen der Entität anzugeben. |

| Aliaseigenschaften | BESCHREIBUNG |
|------------------|-------------|
| `text`  | Die alternative Schreibweise oder Darstellung eines bestimmten Zielentitätsnamens.  |
| `caseSensitive` | (Optional) Funktioniert wie der oben beschriebene Parameter caseSensitive der Stammentität, gilt aber nur für diesen einen Alias. |
| `accentSensitive` | (Optional) Dieser Parameter weist dieselbe Funktion wie der oben genannte Stammentitätsparameter „accentSensitive“ auf, gilt jedoch nur für diesen einen Alias. |
| `fuzzyEditDistance` | (Optional) Funktioniert wie der oben beschriebene Parameter fuzzyEditDistance der Stammentität, gilt aber nur für diesen einen Alias. |

### <a name="inline-format"></a>Inlineformat

In einigen Fällen ist es möglicherweise einfacher, die Liste der benutzerdefinierten Entitäten, die abgeglichen werden sollen, direkt inline in der Qualifikationsdefinition bereitzustellen. In diesem Fall können Sie ein ähnliches JSON-Format wie das oben beschriebene verwenden, das jedoch inline in der Qualifikationsdefinition enthalten ist.
Nur Konfigurationen mit einer Größe von weniger als 10 KB (serialisierte Größe) unterstützen Inlinedefinitionen. 

## <a name="sample-definition"></a>Beispieldefinition

Eine Beispieldefinition einer Qualifikation mit einem Inlineformat wird unten gezeigt:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```

Wenn Sie sich alternativ dazu entschließen, einen Zeiger auf die Entitätsdefinitionsdatei bereitzustellen, sehen Sie im folgenden Beispiel eine Qualifikationsdefinition mit dem `entitiesDefinitionUri`-Format:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

## <a name="sample-input"></a>Beispieleingabe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>Beispielausgabe

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Fehler und Warnungen

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Warnung: Maximale Kapazität für Übereinstimmungen erreicht. Alle weiteren duplizierten Übereinstimmungen werden übersprungen.

Diese Warnung wird ausgegeben, wenn die Anzahl der erkannten Übereinstimmungen größer als die maximal zulässige Anzahl ist. In diesem Fall wird das Einschließen duplizierter Übereinstimmungen beendet. Wenn dies für Sie nicht akzeptabel ist, sollten Sie ein [Supportticket](https://ms.portal.azure.com/#create/Microsoft.Support) einreichen, damit wir Sie bei Ihrem individuellen Anwendungsfall unterstützen können.

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Qualifikation „Entitätserkennung“ (für die Suche nach bekannten Entitäten)](cognitive-search-skill-entity-recognition-v3.md)
