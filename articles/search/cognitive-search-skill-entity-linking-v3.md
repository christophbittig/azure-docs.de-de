---
title: 'Kognitiver Skill: Entitätsverknüpfung'
titleSuffix: Azure Cognitive Search
description: Extrahieren Sie verschiedene verknüpfte Entitäten aus Text in einer Anreicherungspipeline in Azure Cognitive Search.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4b052e6ac5746d771e7725b39c13fd57b20facb8
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038462"
---
# <a name="entity-linking-cognitive-skill"></a>Kognitiver Skill: Entitätsverknüpfung

Der Skill **Entitätsverknüpfung** gibt eine Liste der erkannten Entitäten mit Links zu Artikeln in einer bekannten Wissensdatenbank (Wikipedia) zurück.

> [!NOTE]
> Dieser Skill ist an die Cognitive Services-[Textanalyse](../cognitive-services/text-analytics/overview.md) gebunden und erfordert eine [abrechenbare Ressource](cognitive-search-attach-cognitive-services.md) für Transaktionen, die 20 Dokumente pro Indexer und Tag überschreiten. Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet.
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.V3.EntityLinkingSkill

## <a name="data-limits"></a>Datengrenzwerte

Die maximale Größe eines Datensatzes beträgt 50.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten vor dem Senden an den Skill „Entitätsverknüpfung“ aufteilen müssen, denken Sie daran, den [Skill „Textaufteilung“](cognitive-search-skill-textsplit.md) zu verwenden.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parameternamen, die alle optional sind, muss die Groß-/Kleinschreibung beachtet werden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `defaultLanguageCode` |    Sprachcode des Eingabetexts. Wenn kein Standardsprachcode festgelegt ist, wird Englisch (en) als Standardsprachcode verwendet. <br/> Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md). |
| `minimumPrecision` | Ein Wert zwischen 0 und 1 ein. Wenn die Zuverlässigkeitsbewertung (in der `entities`-Ausgabe) unter diesem Wert liegt, wird die Entität nicht zurückgegeben. Die Standardeinstellung ist 0. |
| `modelVersion` | (Optional) Die Version des Modells, die beim Aufruf des Textanalysediensts verwendet werden soll. Wenn nichts angegeben ist, wird standardmäßig die neueste verfügbare Version verwendet. Es empfiehlt sich, diesen Wert nur anzugeben, wenn es unbedingt notwendig ist. Weitere Einzelheiten finden Sie unter [Versionsverwaltung der Modelle in der Textanalyse-API](../cognitive-services/text-analytics/concepts/model-versioning.md).|

## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `languageCode`    | Eine Zeichenfolge, die die Sprache der Datensätze angibt. Wenn dieser Parameter nicht angegeben ist, wird der Standardsprachcode zur Analyse der Datensätze verwendet. <br/>Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md). |
| `text`          | Der zu analysierende Text          |

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| `entities` | Ein Array mit komplexen Typen und den folgenden Feldern: <ul><li>name (der tatsächliche Entitätsname, wie er im Text angezeigt wird)</li> <li>id </li> <li>language (die Sprache des Texts, wie vom Skill ermittelt)</li> <li>url (die URL zu dieser Entität)</li> <li>bingId (die Bing-ID für dies verknüpfte Entität)</li> <li>dataSource (die der URL zugeordnete Datenquelle) </li> <li>matches (ein Array aus komplexen Typen, das `text`, `offset`, `length` und `confidenceScore` enthält)</li></ul>|

## <a name="sample-definition"></a>Beispieldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
    "context": "/document",
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
    "inputs": [
        {
            "name": "text", 
            "source": "/document/content"
        },
        {
            "name": "languageCode", 
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "entities", 
            "targetName": "entities" 
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
             "text": "Microsoft is liked by many.",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>Beispielausgabe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "entities": [
          {
            "name": "Microsoft", 
            "id": "Microsoft",
            "language": "en", 
            "url": "https://en.wikipedia.org/wiki/Microsoft", 
            "bingId": "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85", 
            "dataSource": "Wikipedia", 
            "matches": [
                {
                    "text": "Microsoft", 
                    "offset": 0, 
                    "length": 9, 
                    "confidenceScore": 0.13 
                }
            ]
          }
        ],
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