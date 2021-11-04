---
title: SKUs und Preise für Cognitive Services
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2021
ms.author: pafarley
ms.openlocfilehash: f20f73c37224abe1333d2577db8a54ef07eeed34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521222"
---
Weitere Informationen finden Sie unten in der Liste der SKUs und Preise. 

#### <a name="multi-service"></a>Mehrere Dienste

| Dienst     | Variante    |
|-------------|------------|
| Mehrere Dienste. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Bildanalyse

| Dienst    | Variante    |
|------------|---------|
| Maschinelles Sehen            | `ComputerVision`          |
| Custom Vision – Vorhersage | `CustomVision.Prediction` |
| Custom Vision – Training   | `CustomVision.Training`   |
| Gesicht                       | `Face`                    |
| Formularerkennung            | `FormRecognizer`          |

#### <a name="speech"></a>Spracheingabe

| Dienst            | Variante                 |
|--------------------|----------------------|
| Spracherkennungsdienste    | `SpeechServices`     |
| Spracherkennung | `SpeakerRecognition` |

#### <a name="language"></a>Sprache

| Dienst            | Variante                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Sprachdienst   | `TextAnalytics`     |
| Textübersetzung   | `TextTranslation`   |

#### <a name="decision"></a>Entscheidung

| Dienst           | Variante               |
|-------------------|--------------------|
| Anomalieerkennung  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalisierung      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Tarife und Abrechnung

Die Tarife (und der Betrag, der Ihnen in Rechnung gestellt wird) basieren auf der Anzahl der unter ihren Authentifizierungsinformationen gesendeten Transaktionen. Für jeden Tarif wird Folgendes angegeben:
* Maximale Anzahl zulässiger Transaktionen pro Sekunde (TPS).
* Dienstfeatures, die innerhalb des Tarifs aktiviert sind.
* Kosten für eine vordefinierte Anzahl von Transaktionen. Wird diese Anzahl überschritten, fällt eine zusätzliche Gebühr gemäß den [Preisdetails](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) für Ihren Dienst an.

> [!NOTE]
> Für viele der Cognitive Services-Dienste ist ein kostenloser Tarif verfügbar, mit dem Sie den Dienst ausprobieren können. Um den kostenlosen Tarif zu nutzen, verwenden Sie `F0` als SKU für Ihre Ressource.