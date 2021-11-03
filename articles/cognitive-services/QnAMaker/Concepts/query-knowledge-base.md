---
title: 'Abfragen der Wissensdatenbank: QnA Maker'
description: Eine Wissensdatenbank muss veröffentlicht werden. Nach der Veröffentlichung wird die Wissensdatenbank mithilfe der generateAnswer-API am Vorhersageendpunkt der Runtime abgefragt.
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bc1ef09e05b118c2f0a8b5b69cec38f2c38f7fb8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086790"
---
# <a name="query-the-knowledge-base-for-answers"></a>Abfragen der Wissensdatenbank nach Antworten

Eine Wissensdatenbank muss veröffentlicht werden. Nach der Veröffentlichung wird die Wissensdatenbank mithilfe der generateAnswer-API am Vorhersageendpunkt der Runtime abgefragt. Die Abfrage enthält den Fragetext und andere Einstellungen, mit denen QnA Maker die bestmögliche Entsprechung für eine Antwort auswählen kann.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Verarbeitung einer Benutzerabfrage mit QnA Maker, um die beste Antwort auszuwählen

Die trainierte und [veröffentlichte](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker-Wissensdatenbank empfängt eine Benutzerabfrage von einem Bot oder einer anderen Clientanwendung über die [GenerateAnswer-API](../how-to/metadata-generateanswer-usage.md). Im folgenden Diagramm ist der Prozess nach dem Empfang der Benutzerabfrage dargestellt.

![Einstufungsmodellprozess für eine Benutzerabfrage](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Einstufungsprozess

Der Prozess wird in der folgenden Tabelle erläutert:

|Schritt|Zweck|
|--|--|
|1|Die Clientanwendung sendet die Benutzerabfrage an die [GenerateAnswer-API](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker führt die Vorverarbeitung der Benutzerabfrage mit Spracherkennung, Rechtschreibprüfung und Worttrennung durch.|
|3|Diese Vorverarbeitung hat das Ziel, die Benutzerabfrage so anzupassen, dass die besten Suchergebnisse erzielt werden.|
|4|Die geänderte Abfrage wird an den Azure Cognitive Search-Index gesendet, der die mit `top` festgelegte Anzahl von Ergebnissen empfängt. Falls diese Ergebnisse die richtige Antwort nicht enthalten, sollten Sie den Wert von `top` leicht erhöhen. Im Allgemeinen eignet sich der Wert „10“ für `top` bei 90 % aller Abfragen. Azure Search filtert [Stoppwörter](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) in diesem Schritt.|
|5|QnA Maker verwendet eine auf Syntax und Semantik basierende Featurebereitstellung, um die Ähnlichkeit zwischen der Benutzerabfrage und den abgerufenen QnA-Ergebnissen zu ermitteln.|
|6|Das maschinell erlernte Bewertermodell verwendet die verschiedenen Features aus Schritt 5, um die Zuverlässigkeitsbewertungen und die neue Rangfolge zu ermitteln.|
|7|Die neuen Ergebnisse werden in der Rangfolge an die Clientanwendung zurückgegeben.|
|||

Zu den verwendeten Funktionen gehören u. a. Semantik auf Wortebene, Wichtigkeit auf Begriffsebene in einem Korpus und Deep Learning-Semantikmodelle, um die Ähnlichkeit und Relevanz zwischen zwei Textzeichenfolgen zu ermitteln.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-Anforderung und -Antwort mit Endpunkt

Beim Veröffentlichen Ihrer Wissensdatenbank erstellt der Dienst einen REST-basierten HTTP-Endpunkt, den Sie in Ihre Anwendung integrieren können, etwa in Form eines Chatbots.

### <a name="the-user-query-request-to-generate-an-answer"></a>Benutzerabfrageanforderung zum Generieren einer Antwort

Eine Benutzerabfrage ist die Frage, die der Benutzer an die Wissensdatenbank richtet, z. B. `How do I add a collaborator to my app?`. Die Abfrage wird häufig in einem natürlichen Sprachformat oder in Form von Schlüsselwörtern formuliert, die die Frage darstellen, z. B. `help with collaborators`. Die Abfrage wird von einer HTTP-Anforderung in der Clientanwendung an Ihre Wissensdatenbank gesendet.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Sie steuern die Antwort, indem Sie Eigenschaften wie [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) und [strictFilters](../how-to/query-knowledge-base-with-metadata.md) festlegen.

Verwenden Sie [Unterhaltungskontext](../how-to/query-knowledge-base-with-metadata.md) mit [Mehrfachdurchlauffunktion](../how-to/multiturn-conversation.md), um die Konversation aufrechtzuerhalten und die Fragen und Antworten so zu verfeinern, dass die richtige und abschließende Antwort gefunden wird.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Reaktion auf einen Aufruf zum Generieren einer Antwort

Die HTTP-Antwort ist die Antwort, die aus der Wissensdatenbank basierend auf der besten Übereinstimmung für eine bestimmte Benutzerabfrage abgerufen wird. Darin enthalten ist die eigentliche Antwort und das Vorhersageergebnis. Wenn Sie mit der Eigenschaft `top` mehr als eine Top-Antwort angefordert haben, erhalten Sie mehrere Top-Antworten jeweils mit einer Bewertung.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zuverlässigkeitsbewertung](./confidence-score.md)
