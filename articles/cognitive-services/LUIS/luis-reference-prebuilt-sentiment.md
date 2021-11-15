---
title: 'Stimmungsanalyse: LUIS'
titleSuffix: Azure Cognitive Services
description: Wenn die Standpunktanalyse konfiguriert wurde, enthält die JSON-Antwort von LUIS eine Standpunktanalyse.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/28/2021
ms.openlocfilehash: 2c80e23335de38929f8ca0fc8cf0b04f835d9e10
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446636"
---
# <a name="sentiment-analysis"></a>Stimmungsanalyse
Wenn die Standpunktanalyse konfiguriert wurde, enthält die JSON-Antwort von LUIS eine Standpunktanalyse. Weitere Informationen zur Stimmungsanalyse finden Sie in der Dokumentation zum [Sprachdienst](../language-service/index.yml).

LUIS verwendet V2 der API. 

Die Standpunktanalyse wird beim Veröffentlichen Ihrer Anwendung konfiguriert. Weitere Informationen finden im Artikel zum [Veröffentlichen einer App](./luis-how-to-publish-app.md).

## <a name="resolution-for-sentiment"></a>Lösung für Stimmung

Stimmungsdaten stellen eine Bewertung zwischen 1 und 0 dar, die eine eher positive (näher an 1) oder negative (näher an 0) Stimmung in den Daten angibt.

#### <a name="english-language"></a>[Englische Sprache](#tab/english)

Wenn die Kultur `en-us` ist, lautet die Antwort:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Weitere Sprachen](#tab/other-languages)

Für alle anderen Kulturen lautet die Antwort:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).
