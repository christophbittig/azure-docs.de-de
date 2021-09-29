---
title: Der kognitive Skill „Stimmung“
titleSuffix: Azure Cognitive Search
description: Extrahieren eines Positiv-Negativ-Stimmungswerts aus Text in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: b750e99d30c0b540a381ce9e9b32b228b4dceaf8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210946"
---
# <a name="sentiment-cognitive-skill"></a>Der kognitive Skill „Stimmung“

Der Skill **Stimmung** bewertet unstrukturierten Text anhand eines Positiv-Negativ-Kontinuums und gibt für jeden Datensatz einen numerischen Wert zwischen 0 und 1 zurück. Dabei weisen Werte nahe 1 auf eine positive Stimmung und Werte nahe 0 auf eine negative Stimmung hin. Bei dieser Qualifikation werden die Machine Learning-Modelle verwendet, die in Cognitive Services über die [Textanalyse](../cognitive-services/text-analytics/overview.md) bereitgestellt werden.

> [!IMPORTANT]
> Der Skill „Stimmung“ wurde eingestellt und durch [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) ersetzt. Führen Sie unter Berücksichtigung der Empfehlungen unter [Veraltete Qualifikationen für die kognitive Suche](cognitive-search-skill-deprecated.md) eine Migration zu einer unterstützten Qualifikation durch.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumententschlüsselungsphase in Azure Cognitive Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion sind in der [Preisübersicht für Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/) angegeben.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes beträgt 5.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an das Stimmungsanalysetool senden, verwenden Sie den [Skill „Text teilen“](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername | BESCHREIBUNG |
|----------------|----------------------|
| `defaultLanguageCode` | (Optional) Der Sprachcode, der für Dokumente gilt, die nicht explizit eine Sprache angeben. <br/> Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md). |

## <a name="skill-inputs"></a>Skilleingaben 

| Eingabename | BESCHREIBUNG |
|--------------------|-------------|
| `text` | Der zu analysierende Text|
| `languageCode`    |  (Optional) Eine Zeichenfolge, die die Sprache der Datensätze angibt. Wenn dieser Parameter nicht angegeben wird, ist der Standardwert „en“. <br/>Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/language-support.md).|

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename | BESCHREIBUNG |
|--------------------|-------------|
| `score` | Ein Wert zwischen 0 und 1, der die Stimmung des analysierten Textes repräsentiert. Werte nahe 0 stehen für eine negative, nahe 0,5 für eine neutrale und nahe 1 für eine positive Stimmung.|


##  <a name="sample-definition"></a>Beispieldefinition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
                "score": 0.01
            }
        }
    ]
}
```

## <a name="warning-cases"></a>Warnungsfälle
Wenn der Text leer ist, wird eine Warnung generiert, und es wird keine Stimmungswert zurückgegeben.
Wird eine Sprache nicht unterstützt, wird eine Warnung generiert und kein Stimmungswert zurückgegeben.

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Skill „Stimmung“ (V3)](cognitive-search-skill-sentiment-v3.md)