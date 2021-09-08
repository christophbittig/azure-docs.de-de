---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 3620bcff2724607e4cbc7537479d45817461f19c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543240"
---
In dieser Schnellstartanleitung verwenden Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) und den LUIS-Dienst (Language Understanding), um Absichten aus Audiodaten zu erkennen, die von einem Mikrofon aufgezeichnet wurden. Genauer gesagt, verwenden Sie das Speech SDK zum Erfassen der Sprachausgabe und eine vordefinierte Domäne von LUIS zur Absichtserkennung für die Gebäudeautomatisierung, beispielsweise zum Ein- und Ausschalten einer Lampe. 

Nach der Erfüllung einiger Voraussetzungen sind für die Erkennung von Sprache und Absichten über ein Mikrofon lediglich einige wenige Schritte erforderlich:

> [!div class="checklist"]
>
> * Erstellen eines Objekts vom Typ `SpeechConfig` auf der Grundlage Ihres Abonnementschlüssels und Ihrer Region
> * Erstellen eines `IntentRecognizer`-Objekts mit dem `SpeechConfig`-Objekt von weiter oben
> * Starten des Erkennungsprozesses für eine einzelne Äußerung unter Verwendung des Objekts `IntentRecognizer`
> * Überprüfen des zurückgegebenen Objekts `IntentRecognitionResult`

