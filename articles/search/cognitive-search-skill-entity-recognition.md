---
title: Die kognitive Qualifikation „Entitätserkennung“
titleSuffix: Azure Cognitive Search
description: Extrahieren verschiedener Entitätstypen aus Text in einer Anreicherungspipeline in der kognitiven Azure-Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: 0ff55bf38e88bf62b483cf70df89a17b9c5afb8d
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210381"
---
# <a name="entity-recognition-cognitive-skill"></a>Die kognitive Qualifikation „Entitätserkennung“

Mit der Qualifikation **Entitätserkennung** (EntityRecognitionSkill) können Sie Entitäten aus verschiedenen Arten von Text extrahieren. Bei dieser Qualifikation werden die Machine Learning-Modelle verwendet, die in Cognitive Services über die [Textanalyse](../cognitive-services/text-analytics/overview.md) bereitgestellt werden.

> [!IMPORTANT]
> Der Skill „Entitätserkennung“ wurde eingestellt und durch [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) ersetzt. Führen Sie unter Berücksichtigung der Empfehlungen unter [Veraltete Qualifikationen für die kognitive Suche](cognitive-search-skill-deprecated.md) eine Migration zu einer unterstützten Qualifikation durch.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumententschlüsselungsphase in Azure Cognitive Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion sind in der [Preisübersicht für Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/) angegeben.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes beträgt 50.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an die Schlüsselbegriffserkennung senden, denken Sie daran, den [Skill „Text teilen“](cognitive-search-skill-textsplit.md) zu verwenden.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern, die alle optional sind, wird die Groß-/Kleinschreibung beachtet.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `categories`    | Array von zu extrahierenden Kategorien.  Mögliche Kategorietypen: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"` und `"Email"`. Wenn keine Kategorie angegeben ist, werden alle Typen zurückgegeben.|
| `defaultLanguageCode` |    Sprachcode des Eingabetexts. Die folgenden Sprachen werden unterstützt: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`. Nicht alle Entitätskategorien werden für alle Sprachen unterstützt (siehe Anmerkung unten).|
| `minimumPrecision` | Ein Wert zwischen 0 und 1 ein. Wenn die Zuverlässigkeitsbewertung (in der `namedEntities`-Ausgabe) unter diesem Wert liegt, wird die Entität nicht zurückgegeben. Die Standardeinstellung ist 0. |
| `includeTypelessEntities` | Legen Sie diese Option auf `true` fest, wenn Sie bekannte Entitäten erkennen möchten, die nicht in die aktuellen Kategorien passen. Erkannte Entitäten werden im komplexen Ausgabefeld `entities` zurückgegeben. „Windows 10“ ist z.B. eine bekannte Entität (ein Produkt), aber da „Produkte“ keine unterstützte Kategorie ist, wäre diese Entität im Entitäten-Ausgabefeld enthalten. Die Standardeinstellung ist `false`. |


## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `languageCode`    | Optional. Der Standardwert ist `"en"`.  |
| `text`          | Der zu analysierende Text          |

## <a name="skill-outputs"></a>Skillausgaben

> [!NOTE]
> Nicht alle Entitätskategorien werden für alle Sprachen unterstützt. Die Entitätskategorietypen `"Person"`, `"Location"` und `"Organization"` werden für die vollständige Liste der oben genannten Sprachen unterstützt. Nur _de_, _en_, _es_, _fr_ und _zh-hans_ unterstützen die Extraktion der Typen `"Quantity"`, `"Datetime"`, `"URL"` und `"Email"`. Weitere Informationen finden Sie unter [Sprach- und Regionsunterstützung für die Textanalyse-API](../cognitive-services/text-analytics/language-support.md).  

| Ausgabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `persons`       | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge den Namen einer Person darstellt. |
| `locations`  | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge einen Ort darstellt. |
| `organizations`  | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge eine Organisation darstellt. |
| `quantities`  | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge eine Menge darstellt. |
| `dateTimes`  | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge einen DateTime-Wert darstellt (wie im Text gezeigt). |
| `urls` | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge eine URL darstellt. |
| `emails` | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge eine E-Mail-Adresse darstellt. |
| `namedEntities` | Ein Array mit komplexen Typen und den folgenden Feldern: <ul><li>category</li> <li>value (der tatsächliche Entitätsname)</li><li>offset (die Fundstelle im Text)</li><li>Zuverlässigkeit (ein höherer Wert bedeutet, dass es sich mit höherer Wahrscheinlichkeit um eine echte Entität handelt)</li></ul> |
| `entities` | Ein Array mit komplexen Typen, die umfangreiche Informationen zu den aus dem Text extrahierten Entitäten enthalten, und den folgenden Feldern: <ul><li> name (der tatsächliche Entitätsname; stellt eine „normalisierte“ Form dar)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (Link zur Wikipedia-Seite für die Entität)</li><li>bingId</li><li>type (Kategorie der erkannten Entität)</li><li>subType (nur für bestimmte Kategorien verfügbar; ermöglicht eine präzisere Ansicht des Entitätstyps)</li><li> matches (eine komplexe Sammlung mit:)<ul><li>text (unformatierter Text für die Entität)</li><li>offset (Fundstelle)</li><li>length (Länge des unformatierten Texts für die Entität)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Beispieldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Beispieleingabe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Beispielausgabe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Beachten Sie, dass die für Entitäten in der Ausgabe dieses Skills zurückgegebenen Offsets direkt von der [Textanalyse-API](../cognitive-services/text-analytics/overview.md) zurückgegeben werden. Dies bedeutet, dass Sie, wenn Sie sie zum Indizieren in der ursprünglichen Zeichenfolge verwenden, die [StringInfo](/dotnet/api/system.globalization.stringinfo)-Klasse in .NET verwenden müssen, um den richtigen Inhalt zu extrahieren.  [Weitere Informationen finden Sie hier.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Warnungsfälle
Wird der Sprachcode für das Dokument nicht unterstützt, wird eine Warnung zurückgegeben, und es werden keine Entitäten extrahiert.

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Skill „Entitätserkennung“ (V3)](cognitive-search-skill-entity-recognition-v3.md)