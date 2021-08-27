---
author: laujan
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: lajanuar
ms.openlocfilehash: 6d00bf01bcbcc344e4f066f3dab7d140af042a94
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071765"
---
In dieser Schnellstartanleitung verwenden Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) und den LUIS-Dienst (Language Understanding), um Absichten aus Audiodaten zu erkennen, die von einem Mikrofon aufgezeichnet wurden. Genauer gesagt, verwenden Sie das Speech SDK zum Erfassen der Sprachausgabe und eine vordefinierte Domäne von LUIS zur Absichtserkennung für die Gebäudeautomatisierung, beispielsweise zum Ein- und Ausschalten einer Lampe. 

Nach der Erfüllung einiger Voraussetzungen sind für die Erkennung von Sprache und Absichten über ein Mikrofon lediglich einige wenige Schritte erforderlich:

> [!div class="checklist"]
>
> * Erstellen eines Objekts vom Typ `SpeechConfig` auf der Grundlage Ihres Abonnementschlüssels und Ihrer Region
> * Erstellen eines `IntentRecognizer`-Objekts mit dem `SpeechConfig`-Objekt von weiter oben
> * Starten des Erkennungsprozesses für eine einzelne Äußerung unter Verwendung des Objekts `IntentRecognizer`
> * Überprüfen des zurückgegebenen Objekts `IntentRecognitionResult`

