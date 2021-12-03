---
title: 'Vordefinierte KeyPhrase-Entität: LUIS'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erhalten Sie Informationen zur vorgefertigten keyphrase-Entität in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/28/2021
ms.openlocfilehash: 0fd252c5be2f4c0a44eea998ed6ea5a83d4f2c7c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454635"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Vordefinierte KeyPhrase-Entität für eine LUIS-App
Die keyPhrase-Entität extrahiert eine Vielzahl von Schlüsselbegriffen aus einer Äußerung. Sie müssen der App keine Beispieläußerungen hinzufügen, die „keyPhrase“ enthalten. Die keyPhrase-Entität wird für [viele Kulturen](luis-language-support.md#languages-supported) im Rahmen der Features des [Sprachdienstes](../language-service/overview.md) unterstützt.

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Auflösung der vorgefertigten keyPhrase-Entität

Die folgenden Entitätsobjekte werden für die Abfrage zurückgegeben:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[V3-Antwort](#tab/V3)

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Ausführliche V3-Antwort](#tab/V3-verbose)
Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-Antwort](#tab/V2)

Im folgenden Beispiel wird die Auflösung der Entität **builtin.keyPhrase** veranschaulicht.

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).

Erfahren Sie mehr zu den [Prozentsatz](luis-reference-prebuilt-percentage.md)-, [Anzahl](luis-reference-prebuilt-number.md)- und [Altersentitäten](luis-reference-prebuilt-age.md).
