---
title: Zuverlässigkeitsbewertung – Fragen und Antworten
titleSuffix: Azure Cognitive Services
description: Wenn eine Benutzerabfrage mit einer Wissensdatenbank abgeglichen wird, gibt „Fragen und Antworten“ relevante Antworten zusammen mit einer Zuverlässigkeitsbewertung zurück.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 605f4ed4e4d146b6a03979496f171417330732ba
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095655"
---
# <a name="confidence-score"></a>Zuverlässigkeitsbewertung

Wenn eine Benutzerabfrage mit einem Projekt (auch als Wissensdatenbank bezeichnet) abgeglichen wird, gibt „Fragen und Antworten“ relevante Antworten zusammen mit einer Zuverlässigkeitsbewertung zurück. Diese Bewertung zeigt die Zuverlässigkeit dafür an, dass die Antwort die richtige Übereinstimmung für die jeweilige Benutzerabfrage ist.

Die Zuverlässigkeitsbewertung ist eine Zahl zwischen 0 und 100. Eine Bewertung von 100 ist wahrscheinlich eine genaue Übereinstimmung, während eine Bewertung von 0 bedeutet, dass keine passende Antwort gefunden wurde. Je höher die Bewertung, desto größer die Zuverlässigkeit der Antwort. Für eine bestimmte Abfrage können mehrere Antworten zurückgegeben werden. In diesem Fall werden die Antworten in der Reihenfolge abnehmender Zuverlässigkeitsbewertungen zurückgegeben.

Die folgende Tabelle zeigt typische Zuverlässigkeit, die mit einer bestimmten Bewertung verknüpft ist.

|Bewertungswert|Bewertungsbedeutung|Beispielabfrage|
|--|--|--|
|90–100|Eine fast genaue Übereinstimmung von Benutzerabfrage und Wissensdatenbankfrage.|
|> 70|Hohe Zuverlässigkeit – in der Regel eine gute Antwort, die die Abfrage des Benutzers vollständig beantwortet.|
|50–70|Mittlere Zuverlässigkeit – in der Regel eine recht gute Antwort, die die Hauptabsicht der Benutzerabfrage beantworten sollte.|
|30–50|Niedrige Zuverlässigkeit – in der Regel eine relevante Antwort, die die Absicht des Benutzers teilweise beantwortet.|
|< 30|Sehr niedrige Zuverlässigkeit – beantwortet in der Regel nicht die Abfrage des Benutzers, enthält aber übereinstimmende Wörter oder Ausdrücke. |
|0|Keine Übereinstimmung, weshalb die Antwort nicht zurückgegeben wird.|

## <a name="choose-a-score-threshold"></a>Auswählen eines Bewertungsschwellenwerts

Die obige Tabelle zeigt den Bereich der Bewertungen an, die bei der Abfrage mit „Fragen und Antworten“ auftreten können. Da aber jedes Projekt anders ist, verschiedene Typen von Wörtern enthält und mit unterschiedlichen Absichten und Zielen angelegt wird, empfehlen wir Ihnen, die Schwellenwerte zu testen und den auszuwählen, der für Sie am besten funktioniert. Standardmäßig ist der Schwellenwert auf `0` festgelegt, sodass alle möglichen Antworten zurückgegeben werden. Der empfohlene Schwellenwert, der mit den meisten Projekten funktionieren sollte, ist **50**.

Bei der Auswahl Ihres Schwellenwerts sollten Sie die Ausgewogenheit zwischen **Genauigkeit** und **Abdeckung** berücksichtigen und Ihren Schwellenwert auf Grundlage Ihrer Anforderungen anpassen.

- Wenn **Genauigkeit** (oder Exaktheit) für Ihr Szenario wichtiger ist, dann erhöhen Sie den Schwellenwert. Auf diese Weise ist jede von Ihnen zurückgegebene Antwort ein wesentlich ZUVERLÄSSIGERER Fall, und es ist wesentlich wahrscheinlicher, dass es die vom Benutzer gesuchte Antwort ist. In diesem Fall bleiben aber eventuell mehr Fragen unbeantwortet. 

- Wenn die **Abdeckung** (oder Abruf) wichtiger ist, und Sie so viele Fragen wie möglich beantworten möchten, auch wenn es nur einen partiellen Bezug zur Frage des Benutzers gibt, SENKEN Sie den Schwellenwert. Das bedeutet, dass möglicherweise mehr Fälle auftreten, bei denen die Antwort nicht die eigentliche Abfrage des Benutzers beantwortet, aber zumindest eine andere, immer noch relevante Antwort gibt.

## <a name="set-threshold"></a>Festlegen des Schwellenwerts

Sie legen den Schwellenwert als eine Eigenschaft im [JSON-Code der REST-API](../quickstart/sdk.md) fest. Das bedeutet, dass Sie ihn für jeden Aufruf der REST-API festlegen müssen.

## <a name="improve-confidence-scores"></a>Verbessern von Zuverlässigkeitsbewertungen

Um die Zuverlässigkeitsbewertung einer bestimmten Antwort auf eine Benutzerabfrage zu verbessern, können Sie der Wissensdatenbank die Benutzerabfrage als alternative Frage für diese Antwort hinzufügen. Sie können auch [Synonyme](../tutorials/adding-synonyms.md) verwenden, bei denen die Groß- und Kleinschreibung nicht berücksichtigt wird, um Ihrem Projekt Synonyme für Schlüsselwörter hinzuzufügen.

## <a name="similar-confidence-scores"></a>Ähnliche Zuverlässigkeitsbewertungen

Wenn mehrere Antworten eine ähnliche Zuverlässigkeitsbewertung aufweisen, ist es wahrscheinlich, dass die Abfrage zu allgemein war und daher mit gleicher Wahrscheinlichkeit mit mehreren Antworten übereinstimmt. Versuchen Sie, Ihre Fragen und Antworten (Questions and Answers, QnAs) besser zu strukturieren, damit jede QnA-Entität eine eindeutige Absicht ausdrückt.

## <a name="confidence-score-differences-between-test-and-production"></a>Unterschiede bei der Zuverlässigkeitsbewertung zwischen Test und Produktion

Die Zuverlässigkeitsbewertung einer Antwort kann zwischen der Testversion und der bereitgestellten Version des Projekts geringfügig variieren, auch wenn der Inhalt identisch ist. Das liegt daran, dass sich der Inhalt des Tests und des bereitgestellten Projekts in verschiedenen Azure Cognitive Search-Indizes befinden.

Der Testindex enthält alle Frage-Antwort-Paare Ihres Projekts. Wenn Sie den Testindex abfragen, gilt die Abfrage für den gesamten Index, und die Ergebnisse sind auf die Partition für dieses spezifische Projekt beschränkt. Wenn die Ergebnisse der Testabfrage negative Auswirkungen auf die Fähigkeit zum Überprüfen des Projekts haben, können Sie wie folgt vorgehen:
* Organisieren Sie Ihr Projekt mithilfe einer der folgenden Möglichkeiten:
    * Eine Ressource beschränkt auf ein Projekt: Beschränken Sie Ihre einzelne Sprachressource (und den resultierenden Azure Cognitive Search-Testindex) auf ein Projekt bzw. eine Wissensdatenbank.
    * Zwei Ressourcen – Eine für Tests, eine für die Produktion: Nutzen Sie zwei Sprachressourcen, eine für Tests (mit eigenen Test- und Produktionsindizes) und eine für die Produktion (ebenfalls mit eigenen Test- und Produktionsindizes).
* Verwenden Sie immer dieselben Parameter, wenn Sie sowohl Ihre Test- als auch Produktionsprojekte abfragen.

Wenn Sie ein Projekt bereitstellen, wird der Frage-Antwort-Inhalt Ihres Projekts vom Testindex in einen Produktionsindex in Azure Search verschoben.

Wenn Sie über ein Projekt in verschiedenen Regionen verfügen, verwendet jede Region einen eigenen Azure Cognitive Search-Index. Da verschiedene Indizes verwendet werden, werden die Ergebnisse nicht genau gleich sein.

## <a name="no-match-found"></a>Keine Übereinstimmung gefunden

Wenn das Rangfolgemodul keine gute Übereinstimmung findet, wird die Zuverlässigkeitsbewertung „0,0“ oder „Keine“ sowie die Standardantwort zurückgegeben. Sie können die [Standardantwort](../how-to/change-default-answer.md) ändern.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [bewährten Methoden](./best-practices.md)
