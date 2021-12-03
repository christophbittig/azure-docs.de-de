---
title: 'Übersicht über die Absichtserkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Mit der Absichtserkennung können Sie vordefinierte Zielsetzungen von Benutzern erkennen. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Absichtserkennungsdiensts.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
keywords: Absichtserkennung
ms.openlocfilehash: 221f5f0f022cba85df72979ed47b4033b2c3fdd1
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988481"
---
# <a name="what-is-intent-recognition"></a>Was ist die Absichtserkennung?

In dieser Übersicht werden der Nutzen und die Funktionen der Absichtserkennung vorgestellt. Das Cognitive Services Speech SDK bietet zwei Möglichkeiten zum Erkennen von Absichten, die im Folgenden beschrieben werden. Eine Absicht ist eine Aufgabe, die der Benutzer ausführen möchte: einen Flug buchen, sich über das Wetter informieren oder einen Anruf tätigen. Mithilfe der Absichtserkennung können Ihre Anwendungen, Tools und Geräte basierend auf den in der Absichtserkennung oder in LUIS definierten Optionen bestimmen, was der Benutzer initiieren oder tun möchte.

## <a name="pattern-matching"></a>Musterabgleich
Das SDK stellt einen eingebetteten Musterabgleich (Matcher) bereit, mit dem Sie Absichten sehr genau erkennen können. Dies ist hilfreich, wenn Sie eine schnelle Offlinelösung benötigen. Der Abgleich funktioniert besonders gut, wenn der Benutzer in irgendeiner Form geschult wird oder erwartet werden kann, dass er bestimmte Ausdrücke zum Auslösen von Absichten verwendet. Beispiele für solche Ausdrücke sind „Gehe in Etage sieben“, „Schalte die Lampe ein“ usw. Für den Anfang wird dieser Ansatz empfohlen. Wenn der Musterabgleich Ihre Anforderungen nicht mehr erfüllt, können Sie stattdessen LUIS oder eine Kombination aus LUIS und Musterabgleich verwenden. 

## <a name="luis-language-understanding-intent-service"></a>LUIS (Language Understanding Intent Service)
Der Microsoft LUIS-Dienst ist als vollständiger KI-Dienst für die Absichtserkennung verfügbar. Er funktioniert gut, wenn Ihre Domäne möglicher Absichten groß ist und Sie nicht sicher sind, was der Benutzer sagen wird. Er unterstützt viele komplexe Szenarien, Absichten und Entitäten.

### <a name="luis-key-required"></a>LUIS-Schlüssel erforderlich

* LUIS ist in Speech Service integriert, um Absichten aus Sprache zu erkennen. Ein Speech Service-Abonnement ist nicht erforderlich, nur LUIS.
* Die Sprechabsichtserkennung ist in das Speech SDK integriert. Sie können einen LUIS-Schlüssel mit dem Speech-Dienst verwenden.
* Die Absichtserkennung durch das Speech SDK wird [in einigen der von LUIS unterstützten Regionen angeboten](./regions.md#intent-recognition).

## <a name="get-started"></a>Erste Schritte
Informationen zu den ersten Schritten mit dem Musterabgleich finden Sie in dieser [Schrittanleitung](how-to-use-simple-language-pattern-matching.md).

Informationen zu den ersten Schritten mit der LUIS-Absichtserkennung finden Sie in diesem [Schnellstart](get-started-intent-recognition.md).

## <a name="sample-code"></a>Beispielcode

Beispielcode für Absichtserkennung:

* [Schnellstart: Verwenden der vordefinierten Home Automation-App](../luis/luis-get-started-create-app.md)
* [Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#](./how-to-recognize-intents-from-speech-csharp.md)
* [Absichtserkennung und andere Speech-Dienste mit Unity in C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Erkennen von Absichten mit dem Speech SDK für Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Absichtserkennung und andere Speech-Dienste mit dem Speech SDK für C++ unter Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Absichtserkennung und andere Speech-Dienste mit dem Speech SDK für Java unter Windows oder Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Absichtserkennung und andere Speech-Dienste mit dem Speech SDK für JavaScript in einem Webbrowser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Referenz

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>Nächste Schritte

* Arbeiten Sie die [Schnellstartanleitung](get-started-intent-recognition.md) für die Absichtserkennung durch.
* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
* [Abrufen des Speech SDK](speech-sdk.md)
