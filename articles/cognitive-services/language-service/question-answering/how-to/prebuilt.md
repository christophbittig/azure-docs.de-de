---
title: Vorgefertigte API - Beantwortung von Fragen
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die vorgefertigte API zur Beantwortung von Fragen, um Fragen zu stellen und Antworten zu erhalten, ohne ein Projekt/eine Wissensdatenbank erstellen zu müssen.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 9fe4b80220ccb94e2b89bcdeaacf8cacac624ed0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159510"
---
# <a name="prebuilt-api"></a>Vordefinierte API

Die vorgefertigte API **zur Beantwortung von Fragen** bietet Ihnen die Möglichkeit, Fragen auf der Grundlage einer Textpassage zu beantworten, ohne dass Sie Projekte/Wissensdatenbanken erstellen, Frage-Antwort-Paare pflegen oder Kosten für eine nicht ausgelastete Infrastruktur aufbringen müssen. Diese Funktionalität wird als API zur Verfügung gestellt und kann verwendet werden, um die Anforderungen an die Beantwortung von Fragen zu erfüllen, ohne dass man sich mit den Einzelheiten der Beantwortung von Fragen vertraut machen muss.

Bei einer Abfrage des Benutzers und einem Block bzw. einer Passage mit Text gibt die API eine Antwort und (falls verfügbar) eine präzise Antwort zurück.

## <a name="example-api-usage"></a>Beispiel für die API-Verwendung

Angenommen, Sie haben einen oder mehrere Textblöcke, denen Sie Antworten auf eine bestimmte Frage entnehmen möchten. Normalerweise hätten Sie so viele Quellen erstellen müssen, wie es Textblöcke gibt. Mit der vorgefertigten API können Sie nun die Textblöcke abfragen, ohne Inhaltsquellen in einem Projekt/einer Wissensdatenbank definieren zu müssen.

Einige andere Szenarien, in denen diese API verwendet werden kann, sind:

* Sie entwickeln eine E-Book-Reader-App für Endbenutzer, die es ihnen ermöglicht, Text zu markieren, eine Frage einzugeben und Antworten über eine markierte Textpassage zu finden.
* Eine Browsererweiterung, die es den Nutzern ermöglicht, eine Frage über den Inhalt zu stellen, der gerade auf der Browserseite angezeigt wird.
* Ein Gesundheits-Bot, der Anfragen von Nutzern entgegennimmt und Antworten auf der Grundlage der medizinischen Inhalte gibt, die der Bot als besonders relevant für die Nutzeranfrage identifiziert.

Im Folgenden finden Sie ein Beispiel einer Anforderung:

## <a name="sample-request"></a>Beispiel für eine Anforderung

```
POST https://{Unique-to-your-endpoint}.api.cognitive.microsoft.com/language/:query-text
```

### <a name="sample-query-over-a-single-block-of-text"></a>Beispielabfrage über einen einzelnen Textblock

Anforderungstext

```json
{
  "parameters": {
    "Endpoint": "{Endpoint}",
    "Ocp-Apim-Subscription-Key": "{API key}",
    "Content-Type": "application/json",
    "api-version": "2021-10-01",
    "stringIndexType": "TextElements_v8",
    "textQueryOptions": {
      "question": "how long it takes to charge surface?",
      "records": [
        {
          "id": "1",
          "text": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it."
        },
        {
          "id": "2",
          "text": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."
        }
      ],
      "language": "en"
    }
  }
}
```

## <a name="sample-response"></a>Beispiel für eine Antwort

Im obigen Anforderungstext fragen wir einen einzelnen Textblock ab. Eine Beispielantwort der obigen Abfrage ist unten dargestellt:

```json
{
"responses": {
    "200": {
      "headers": {},
      "body": {
        "answers": [
          {
            "answer": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.93,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 28,
              "length": 45
            },
            "offset": 0,
            "length": 224
          },
          {
            "answer": "It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.92,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 8,
              "length": 25
            },
            "offset": 20,
            "length": 224
          },
          {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.05,
            "id": "1",
            "answerSpan": null,
            "offset": 110,
            "length": 244
          }
        ]
      }
    }
  }
```

Wir sehen, dass mehrere Antworten als Teil der API-Antwort empfangen werden. Jede Antwort hat eine bestimmte Zuverlässigkeitsbewertung, die ihnen hilft, die Gesamtrelevanz der Antwort zu verstehen. Die Antwortspanne gibt an, ob auch eine mögliche kurze Antwort erkannt wurde. Die Benutzer können anhand dieses Vertrauenswertes bestimmen, welche Antworten sie auf die Anfrage geben sollen.

## <a name="prebuilt-api-limits"></a>Vorgefertigte API-Grenzwerte

Wenn Sie größere Dokumente verwenden müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. In diesem Zusammenhang ist ein Dokument eine definierte einzelne Zeichenfolge von Text.

Diese Zahlen stellen die Grenzen **für jeden einzelnen API-Aufruf dar**:

* Anzahl der Dokumente: 5.
* Maximale Größe eines einzelnen Dokuments: 5.120 Zeichen.
* Maximal drei Antworten pro Dokument

## <a name="prebuilt-api-reference"></a>Referenz zur vordefinierten API

Lesen Sie die [vollständige Dokumentation der vorgefertigten API-Beispiele](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/Language/stable/2021-10-01/examples/questionanswering/SuccessfulQueryText.json), um die für den Aufruf der API erforderlichen Eingabe- und Ausgabeparameter zu verstehen.