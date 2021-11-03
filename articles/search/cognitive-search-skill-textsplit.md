---
title: Der Skill „Text teilen“
titleSuffix: Azure Cognitive Search
description: Teilen Sie Text in mehrere Blöcke oder Seiten anhand der Länge in einer KI-Anreicherungspipeline in der kognitiven Azure-Suche auf.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: e2531bcb010b83f795cc7f77b68848c86c4c470a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010667"
---
# <a name="text-split-cognitive-skill"></a>Der Skill „Text teilen“

Der Skill **Text teilen** unterteilt den Text in Blöcke. Sie können festlegen, ob Sie den Text in Sätze oder in Seiten einer bestimmten Länge aufteilen möchten. Dieser Skill ist besonders nützlich, wenn Sie eine maximale Textlänge in nachfolgenden Skills einhalten müssen. 

> [!NOTE]
> Dieser Skill ist nicht an Cognitive Services gebunden. Er ist nicht gebührenpflichtig und weist keine Cognitive Services-Schlüsselanforderungen auf.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `textSplitMode`    | Entweder `pages` oder `sentences` | 
| `maximumPageLength` | Gilt nur, wenn `textSplitMode` auf `pages` festgelegt ist. Dies bezieht sich auf die von `String.Length` gemessene maximale Seitenlänge in Zeichen. Der Mindestwert ist 300, der Höchstwert ist 100.000; der Standardwert ist 10.000.  Der Algorithmus versucht immer, den Text an den Satzgrenzen zu trennen, sodass die Größe der einzelnen Teile etwas geringer sein kann als `maximumPageLength`. | 
| `defaultLanguageCode` | (Optional) Einer der folgenden Sprachcodes: `am, bs, cs, da, de, en, es, et, fr, he, hi, hr, hu, fi, id, is, it, ja, ko, lv, no, nl, pl, pt-PT, pt-BR, ru, sk, sl, sr, sv, tr, ur, zh-Hans`. Die Standardsprache ist Englisch (en). Zu beachtende Aspekte:<ul><li>Die Angabe eines Sprachcodes ist sinnvoll, um bei Sprachen ohne Leerzeichen wie Chinesisch, Japanisch oder Koreanisch zu vermeiden, dass ein Wort in der Mitte getrennt wird.</li><li>Wenn Sie die Sprache nicht kennen (d. h., Sie müssen den Text für die Eingabe in [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) teilen), sollte der Standardwert „English (en)“ ausreichen. </li></ul>  |


## <a name="skill-inputs"></a>Skilleingaben

| Parametername       | BESCHREIBUNG      |
|----------------------|------------------|
| `text`    | Der Text, der in Teilzeichenfolgen aufgeteilt werden soll. |
| `languageCode`    | (Optional) Der Sprachcode für das Dokument. Wenn Sie die Sprache nicht kennen (d. h., Sie müssen den Text für die Eingabe in [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) teilen), können Sie diese Eingabe sicher entfernen. Wenn die Sprache nicht in der Liste der unterstützten Sprachen für den Parameter `defaultLanguageCode` oben enthalten ist, wird eine Warnung ausgegeben, und der Text wird nicht aufgeteilt.  |

## <a name="skill-outputs"></a>Skillausgaben 

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| `textItems`   | Ein Array von Teilzeichenfolgen, die extrahiert wurden. |


##  <a name="sample-definition"></a>Beispieldefinition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
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
            "name": "textItems",
            "targetName": "mypages"
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
                "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia...",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Auftretende Fehler
Wird eine Sprache nicht unterstützt, wird eine Warnung generiert.

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
