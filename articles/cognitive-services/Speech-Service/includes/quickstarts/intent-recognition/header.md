---
author: eric-urban
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: eur
ms.openlocfilehash: 55cb81958e91a6c17e548eb02161154abba8aa66
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507469"
---
In dieser Schnellstartanleitung verwenden Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) und den LUIS-Dienst (Language Understanding), um Absichten aus Audiodaten zu erkennen, die von einem Mikrofon aufgezeichnet wurden. Genauer gesagt, verwenden Sie das Speech SDK zum Erfassen der Sprachausgabe und eine vordefinierte Domäne von LUIS zur Absichtserkennung für die Gebäudeautomatisierung, beispielsweise zum Ein- und Ausschalten einer Lampe. 

Nach der Erfüllung einiger Voraussetzungen sind für die Erkennung von Sprache und Absichten über ein Mikrofon lediglich einige wenige Schritte erforderlich:

> [!div class="checklist"]
>
> * Erstellen eines Objekts vom Typ `SpeechConfig` auf der Grundlage Ihres Abonnementschlüssels und Ihrer Region
> * Erstellen eines `IntentRecognizer`-Objekts mit dem `SpeechConfig`-Objekt von weiter oben
> * Starten des Erkennungsprozesses für eine einzelne Äußerung unter Verwendung des Objekts `IntentRecognizer`
> * Überprüfen des zurückgegebenen Objekts `IntentRecognitionResult`

