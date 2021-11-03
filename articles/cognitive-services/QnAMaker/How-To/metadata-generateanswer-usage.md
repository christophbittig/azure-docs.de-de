---
title: 'Verwenden von Metadaten mit der GenerateAnswer-API: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker ermöglicht Ihnen das Hinzufügen von Metadaten in Form von Schlüssel-Wert-Paaren zu Ihren Frage-Antwort-Paaren. Sie können Ergebnisse nach Benutzerabfragen filtern und zusätzliche Informationen speichern, die in Folgekonversationen verwendet werden können.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: ba2b39a854dd7d70690aea943f7365bc50bc8804
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038782"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Abrufen einer Antwort mit der GenerateAnswer-API

Um die vorhergesagte Antwort auf die Frage eines Benutzers zu erhalten, verwenden Sie die GenerateAnswer-API. Wenn Sie eine Wissensdatenbank veröffentlichen, werden Informationen zur Verwendung dieser API auf der Seite **Veröffentlichen** angezeigt. Sie können die API auch so konfigurieren, dass sie Antworten anhand von Metadatentags filtert, und Sie können die Wissensdatenbank vom Endpunkt aus mit dem Testabfrage-Zeichenfolgenparameter testen.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Abrufen von Antwortvorhersagen mit der GenerateAnswer-API

Sie verwenden die [GenerateAnswer-API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) in Ihrem Bot oder Ihrer Anwendung, um Ihre Wissensdatenbank mit einer Benutzerfrage abzufragen und die beste Übereinstimmung aus den Frage-Antwort-Paaren zu erhalten.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Veröffentlichen am GenerateAnswer-Endpunkt

Nachdem Sie Ihre Wissensdatenbank im [QnA Maker-Portal](https://www.qnamaker.ai) oder mithilfe der [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) veröffentlicht haben, können Sie die Details von Ihrem GenerateAnswer-Endpunkt abrufen.

So rufen Sie Endpunktdetails ab
1. Melden Sie sich bei [https://www.qnamaker.ai](https://www.qnamaker.ai) an.
1. Klicken Sie in **Meine Wissensdatenbanken** für Ihre Wissensdatenbank auf **Code anzeigen**.
    ![Screenshot von „Meine Wissensdatenbanken“](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Rufen Sie Ihre GenerateAnswer-Endpunktdetails ab.

    ![Screenshot der Endpunktdetails](../media/qnamaker-how-to-metadata-usage/view-code.png)

Sie können Ihre Endpunktdetails auch auf der Registerkarte **Einstellungen** Ihrer Knowledge Base abrufen.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer-Anforderungskonfiguration

Sie rufen GenerateAnswer über eine HTTP POST-Anforderung auf. Beispielcode, der zeigt, wie GenerateAnswer aufgerufen wird, finden Sie unter [Schnellstarts](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Die POST-Anforderung verwendet:

* Erforderliche [URI-Parameter](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Erforderliche Headereigenschaft `Authorization` für die Sicherheit
* Erforderliche [Texteigenschaften](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Die GenerateAnswer-URL weist das folgende Format auf:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Denken Sie daran, die HTTP-Header-Eigenschaft von `Authorization` auf folgenden Wert festzulegen: die Zeichenfolge `EndpointKey` mit einem nachgestellten Leerzeichen, gefolgt von dem Endpunktschlüssel, der sich auf der Seite **Einstellungen** befindet.

Ein JSON-Beispieltext sieht folgendermaßen aus:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Weitere Informationen zu „rankerType“ finden Sie [hier](../concepts/best-practices.md#choosing-ranker-type).

Der vorherige JSON-Code forderte nur Antworten an, die 30 % oder mehr über dem Schwellenwert liegen.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer-Antworteigenschaften

Die [Antwort](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) ist ein JSON-Objekt, das alle Informationen enthält, die Sie benötigen, um die Antwort sowie die nächste Reaktion in der Unterhaltung, falls verfügbar, anzuzeigen.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Der vorherige JSON-Code gab eine Antwort mit dem Punktestand 38,5 % zurück.

## <a name="match-questions-only-by-text"></a>Nur Fragen nach Textübereinstimmungen durchsuchen

Standardmäßig durchsucht QnA Maker Fragen und Antworten. Wenn Sie nur Fragen durchsuchen möchten, um eine Antwort zu generieren, verwenden Sie `RankerType=QuestionOnly` im POST-Text der GenerateAnswer-Anforderung.

Sie können mit `isTest=false` die veröffentlichte Wissensdatenbank oder mit `isTest=true` die Testwissensdatenbank durchsuchen.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>Verwenden von QnA Maker mit einem Bot in C#

Das Bot-Framework bietet mit der [getAnswer-API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__) Zugriff auf die Eigenschaften von QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Der vorherige JSON-Code forderte nur Antworten an, die 30 % oder mehr über dem Schwellenwert liegen.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Verwenden von QnA Maker mit einem Bot in Node.js

Das Bot-Framework bietet mit der [getAnswer-API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-) Zugriff auf die Eigenschaften von QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Der vorherige JSON-Code forderte nur Antworten an, die 30 % oder mehr über dem Schwellenwert liegen.

## <a name="get-precise-answers-with-generateanswer-api"></a>Abrufen präziser Antworten mit der GenerateAnswer-API

Ein Feature für präzise Antworten ist nur in der verwalteten QnA Maker-Version enthalten.

## <a name="common-http-errors"></a>Häufige HTTP-Fehler

|Code|Erklärung|
|:--|--|
|2xx|Erfolg|
|400|Die Parameter der Anforderung sind fehlerhaft, da die erforderlichen Parameter nicht vorhanden, falsch formatiert oder zu groß sind|
|400|Der Text der Anforderung ist fehlerhaft, da der JSON-Code nicht vorhanden, falsch formatiert oder zu groß ist|
|401|Ungültiger Schlüssel|
|403|Verboten – Sie verfügen nicht über die richtigen Berechtigungen|
|404|KB nicht vorhanden|
|410|Diese API ist veraltet und nicht mehr verfügbar.|

## <a name="next-steps"></a>Nächste Schritte

Die Seite **Veröffentlichen** enthält auch Informationen zum [Generieren einer Antwort](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) mit Postman oder cURL.

> [!div class="nextstepaction"]
> [Abrufen von Analysen zu Ihrer Wissensdatenbank](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Zuverlässigkeitsbewertung](../Concepts/confidence-score.md)
