---
title: 'Schnellstart: LUIS-SDK-Clientbibliotheken (Language Understanding) und REST-API'
description: Hier erfahren Sie, wie Sie eine LUIS-App mithilfe der LUIS-SDK-Clientbibliotheken und der REST-API erstellen und abfragen.
ms.topic: quickstart
ms.date: 10/20/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, künstliche Intelligenz, KI, Verarbeitung natürlicher Sprache, NLP, LUIS, Azure LUIS, Verstehen natürlicher Sprache, KI Chatbot, Chatbot Maker, natürliche Sprache verstehen
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020, ignite-fall-2021
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: c98bbd885bb2646ff32c856aecc219cae326d01c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043868"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Schnellstart: LUIS-Clientbibliotheken (Language Understanding) und REST-API

> [!NOTE]
> Eine neuere Version der Language Understanding-Funktionen ist jetzt als Teil von Azure Cognitive Services für Sprache verfügbar. Weitere Informationen finden Sie in der [Dokumentation zu Azure Cognitive Services for Language](../language-service/index.yml). Informationen zu Language Understanding-Funktionen innerhalb des Sprachdiensts finden Sie unter [Conversational Language Understanding](../language-service/conversational-language-understanding/overview.md), [Benutzerdefinierte Erkennung benannter Entitäten](../language-service/custom-named-entity-recognition/overview.md) und [Benutzerdefinierte Klassifizierung](../language-service/custom-classification/overview.md).

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit den LUIS-SDK-Clientbibliotheken eine Azure LUIS-KI-App (Künstliche Intelligenz) mithilfe von C#, Python oder JavaScript erstellen und abfragen. Sie können auch cURL verwenden, um Anforderungen über die REST-API zu senden.

Language Understanding (LUIS) ermöglicht das Anwenden von NLP (Natural Language Processing, Verarbeitung natürlicher Sprache) auf Benutzertext mit natürlicher Sprache, um die allgemeine Bedeutung vorherzusagen sowie relevante, detaillierte Informationen zu extrahieren.

* Mit der **Authoring**-Clientbibliothek und -REST-API können Sie Ihre LUIS-App erstellen, bearbeiten, trainieren und veröffentlichen.
* Mit der **Prediction Runtime**-Clientbibliothek und -REST-API können Sie die veröffentlichte App abfragen.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die App über das [LUIS-Portal](https://www.luis.ai) und die Azure-Ressourcen über das [Azure-Portal](https://portal.azure.com/) löschen.

Löschen Sie nach Abschluss des Schnellstarts die Datei `ExampleUtterances.JSON` von Ihrem Dateisystem, wenn Sie die REST-API verwenden.

## <a name="troubleshooting"></a>Problembehandlung

* Authentifizieren bei der Clientbibliothek: Authentifizierungsfehler weisen in der Regel darauf hin, dass der falsche Schlüssel und Endpunkt verwendet wurden. In dieser Schnellstartanleitung werden der Einfachheit halber der Erstellungsschlüssel und der Endpunkt für die Vorhersagelaufzeit verwendet. Dies ist aber nur möglich, wenn Sie das monatliche Kontingent noch nicht aufgebraucht haben. Wenn Sie den Erstellungsschlüssel und den Endpunkt nicht verwenden können, müssen Sie beim Zugriff auf die Clientbibliothek des Prediction Runtime SDK den Schlüssel und den Endpunkt der Vorhersagelaufzeit verwenden.
* Erstellen von Entitäten: Wenn beim Erstellen der in diesem Tutorial verwendeten geschachtelten Machine Learning-Entität ein Fehler auftritt, stellen Sie sicher, dass Sie den Code kopiert und nicht zum Erstellen einer anderen Entität geändert haben.
* Erstellen von Beispieläußerungen: Wenn beim Erstellen der in diesem Tutorial verwendeten bezeichneten Beispieläußerung ein Fehler auftritt, stellen Sie sicher, dass Sie den Code kopiert und nicht zum Erstellen eines anderen bezeichneten Beispiels geändert haben.
* Trainieren: Wenn ein Trainingsfehler angezeigt wird, weist dies normalerweise auf eine leere App (keine Absichten mit Beispieläußerungen) oder eine App mit falsch formatierten Absichten oder Entitäten hin.
* Sonstige Fehler: Da der Code die Clientbibliotheken mit Text- und JSON-Objekten aufruft, vergewissern Sie sich, dass der Code nicht geändert wurde.

Andere Fehler: Wenn ein Fehler auftritt, der in der obigen Liste nicht behandelt wird, teilen Sie uns dies unten auf dieser Seite über den Feedbackbereich mit. Geben Sie die Programmiersprache und die Version der Clientbibliotheken an, die Sie installiert haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Iterative App Entwicklung für LUIS](./luis-concept-app-iteration.md)
