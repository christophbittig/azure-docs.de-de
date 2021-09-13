---
title: Der kognitive Skill „Stimmung“ (V3)
titleSuffix: Azure Cognitive Search
description: Dient zum Bereitstellen von Stimmungsbezeichnungen für Text in einer KI-Anreicherungspipeline in Azure Cognitive Search.
manager: jennmar
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4baadea0d78db64d97d1ae94317d7d506a141f41
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349895"
---
# <a name="sentiment-cognitive-skill-v3"></a>Der kognitive Skill „Stimmung“ (V3)

Der V3-Skill **Stimmung** bewertet unstrukturierten Text und gibt für jeden Datensatz Stimmungsbezeichnungen (z. B. „negativ“, „neutral“ und „positiv“) basierend auf der höchsten Zuverlässigkeitsbewertung an, die vom Dienst auf Satz- und Dokumentebene gefunden wird. Bei diesem Skill werden die Machine Learning-Modelle verwendet, die in Cognitive Services von Version 3 der [Textanalyse](../cognitive-services/text-analytics/overview.md) bereitgestellt werden. Damit werden außerdem die [Opinion Mining-Funktionen der Textanalyse-API](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining) verfügbar gemacht. Diese liefert detailliertere Informationen zu den Meinungen in Bezug auf die Attribute von Produkten oder Dienstleistungen im Text.

> [!NOTE]
> Dieser Skill ist an Cognitive Services gebunden und erfordert [eine abrechenbare Ressource](cognitive-search-attach-cognitive-services.md) für Transaktionen, die 20 Dokumente pro Indexer und Tag überschreiten. Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.SentimentSkill

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes beträgt 5.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an den Skill für Stimmung senden, verwenden Sie den [Skill „Text teilen“](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername | BESCHREIBUNG |
|----------------|----------------------|
| `defaultLanguageCode` | (Optional) Der Sprachcode, der für Dokumente gilt, die nicht explizit eine Sprache angeben. <br/> Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md). |
| `modelVersion`   | (Optional) Die Version des Modells, die beim Aufruf des Textanalysediensts verwendet werden soll. Wenn nichts angegeben ist, wird standardmäßig die neueste Version verwendet. Es empfiehlt sich, diesen Wert nur anzugeben, wenn es unbedingt notwendig ist. Weitere Einzelheiten finden Sie unter [Versionsverwaltung der Modelle in der Textanalyse-API](../cognitive-services/text-analytics/concepts/model-versioning.md). |
| `includeOpinionMining` | Bei Festlegung dieser Option auf `true` wird [die Opinion Mining-Funktion von TextAnalytics](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining) aktiviert, mit der aspektbasierte Stimmungsanalyse in die Ausgabeergebnisse aufgenommen werden kann. Wird standardmäßig auf `false` festgelegt. |

## <a name="skill-inputs"></a>Skilleingaben 

| Eingabename | BESCHREIBUNG |
|--------------------|-------------|
| `text` | Der zu analysierende Text|
| `languageCode`    |  (Optional) Eine Zeichenfolge, die die Sprache der Datensätze angibt. Wenn dieser Parameter nicht angegeben wird, ist der Standardwert „en“. <br/>Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md).|

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename | BESCHREIBUNG |
|--------------------|-------------|
| `sentiment` | Ein Zeichenfolgenwert, der die Stimmungsbezeichnung des gesamten analysierten Texts darstellt (entweder positiv, neutral oder negativ) |
| `confidenceScores` | Ein komplexer Typ mit drei double-Werten, einer für die positive Bewertung, einer für die neutrale Bewertung und einer für die negative Bewertung. Die Werte reichen von 0 bis 1,00. Dabei stellt 1,00 die höchstmögliche Konfidenz in einer bestimmten Bezeichnungszuweisung dar. |
| `sentences` | Eine Auflistung komplexer Typen, die die Stimmung des Texts Satz für Satz aufschlüsseln. Hier werden auch Opinion Mining-Ergebnisse in Form von Zielen und Bewertungen zurückgegeben, wenn `includeOpinionMining` auf `true` festgelegt ist.|


##  <a name="sample-definition"></a>Beispieldefinition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
    "includeOpinionMining": true,
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languageCode"
        }
    ],
    "outputs": [
        {
            "name": "sentiment",
            "targetName": "sentiment"
        },
        {
            "name": "confidenceScores",
            "targetName": "confidenceScores"
        },
        {
            "name": "sentences",
            "targetName": "sentences"
        }
    ]
}
```

##  <a name="sample-input"></a>Beispieleingabe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Beispielausgabe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "sentiment": "negative",
                "confidenceScores": {
                    "positive": 0.0,
                    "neutral": 0.0,
                    "negative": 1.0
                },
                "sentences": [
                    {
                        "text": "I had a terrible time at the hotel.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset": 0,
                        "length": 35,
                        "targets": [],
                        "assessments": [],
                    },
                    {
                        "text": "The staff was rude and the food was awful.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset":36,
                        "length": 42,
                        "targets": [
                            {
                                "text": "staff",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 40,
                                "length": 5,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/0",
                                    }
                                ]
                            },
                            {
                                "text": "food",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 63,
                                "length": 4,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/1",
                                    }
                                ]
                            }
                        ],
                        "assessments": [
                            {
                                "text": "rude",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 50,
                                "length": 4,
                                "isNegated": false
                            },
                            {
                                "text": "awful",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 72,
                                "length": 5,
                                "isNegated": false
                            }
                        ],
                    }
                ]
            }
        }
    ]
}
```

## <a name="warning-cases"></a>Warnungsfälle
Wenn der Text leer ist, wird eine Warnung generiert, und es werden keine Stimmungswerte zurückgegeben.
Wird eine Sprache nicht unterstützt, wird eine Warnung generiert, und es werden keine Stimmungswerte zurückgegeben.

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)