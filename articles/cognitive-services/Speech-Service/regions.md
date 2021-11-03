---
title: Regionen für den Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Eine Liste der verfügbaren Regionen und Endpunkte für den Speech-Dienst, einschließlich Spracherkennung, Sprachsynthese und Sprachübersetzung.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2021
ms.author: panosper
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 5ae6618731a6b8d0561a58a1997dbee9584e3551
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031117"
---
# <a name="speech-service-supported-regions"></a>Vom Speech-Dienst unterstützte Regionen

Mithilfe des Speech-Diensts kann Ihre Anwendung Sprache in Text konvertieren, Sprachübersetzungen ausführen und Text in Sprache konvertieren. Der Dienst ist in mehreren Regionen mit eindeutigen Endpunkten für das Speech SDK und REST-APIs verfügbar.

Das Speech-Portal, in dem Sie individuelle Konfigurationen für alle Regionen vornehmen können, finden Sie unter [speech.microsoft.com](https://speech.microsoft.com).

Beim Erwägen einer Region sollten Sie die folgenden Punkte bedenken:

* Wenn Ihre Anwendung ein [Speech SDK](speech-sdk.md) verwendet, geben Sie beim Erstellen einer Sprachkonfiguration den Regionsbezeichner (z. B. `westus`) an. Achten Sie darauf, dass die Region mit der Region Ihres Abonnements übereinstimmt.
* Wenn Ihre Anwendung eine der [REST-APIs](./overview.md#reference-docs) des Sprachdiensts verwendet, ist die Region Teil des Endpunkt-URIs, den Sie bei der Anforderung verwenden.
* Schlüssel, die für eine Region erstellt wurden, sind nur in dieser Region gültig. Der Versuch, sie mit anderen Regionen zu verwenden, führt zu Authentifizierungsfehlern.

> [!NOTE]
> Bei Speech-Diensten werden keine Kundendaten außerhalb der Region, in der der Kunde die Dienstinstanz bereitstellt, gespeichert oder verarbeitet.

## <a name="speech-sdk"></a>Sprach-SDK

Im [Speech SDK](speech-sdk.md) wird die Region als Parameter angegeben (zum Beispiel als Parameter für `SpeechConfig.FromSubscription` im Speech SDK für C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spracherkennung, Sprachsynthese und Übersetzung

Der Speech-Dienst ist in den folgenden Regionen für **Spracherkennung**, **Sprachsynthese** und **Übersetzung** verfügbar:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Wenn Sie ein benutzerdefiniertes Modell mit Audiodaten trainieren möchten, verwenden Sie eine der [Regionen mit dedizierter Hardware](custom-speech-overview.md#set-up-your-azure-account), um das Training zu beschleunigen. Sie können die [REST-API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) verwenden, um das vollständig trainierte Modell später in eine andere Region zu kopieren.

### <a name="intent-recognition"></a>Absichtserkennung

Verfügbare Regionen für **Absichtserkennung** über das Speech SDK sind die folgenden:

| Globale Region | Region           | Regionsbezeichner |
| ------------- | ---------------- | -------------------- |
| Asia          | Asien, Osten        | `eastasia`           |
| Asia          | Asien, Südosten   | `southeastasia`      |
| Australien     | Australien (Osten)   | `australiaeast`      |
| Europa        | Nordeuropa     | `northeurope`        |
| Europa        | Europa, Westen      | `westeurope`         |
| Nordamerika | East US          | `eastus`             |
| Nordamerika | USA (Ost) 2        | `eastus2`            |
| Nordamerika | USA Süd Mitte | `southcentralus`     |
| Nordamerika | USA, Westen-Mitte  | `westcentralus`      |
| Nordamerika | USA (Westen)          | `westus`             |
| Nordamerika | USA, Westen 2        | `westus2`            |
| Südamerika | Brasilien Süd     | `brazilsouth`        |

Dies ist eine Teilmenge der Veröffentlichungsregionen, die vom [Language Understanding Intelligent Service (LUIS)](../luis/luis-reference-regions.md) unterstützt werden.

### <a name="voice-assistants"></a>Sprachassistenten

Das [Speech SDK](speech-sdk.md) unterstützt Funktionen des **Sprach-Assistenten** mithilfe von [Direct Line Speech](./direct-line-speech.md) in diesen Regionen:

| Globale Region | Region           | Regionsbezeichner    |
| ------------- | ---------------- | -------------------- |
| Nordamerika | USA (Westen)          | `westus`             |
| Nordamerika | USA, Westen 2        | `westus2`            |
| Nordamerika | East US          | `eastus`             |
| Nordamerika | USA (Ost) 2        | `eastus2`            |
| Nordamerika | USA, Westen-Mitte  | `westcentralus`      |
| Nordamerika | USA Süd Mitte | `southcentralus`     |
| Europa        | Europa, Westen      | `westeurope`         |
| Europa        | Nordeuropa     | `northeurope`        |
| Asia          | Asien, Osten        | `eastasia`           |
| Asia          | Asien, Südosten   | `southeastasia`      |
| Indien         | Indien, Mitte    | `centralindia`       |

### <a name="speaker-recognition"></a>Sprechererkennung

Folgende Regionen sind für **Sprechererkennung** verfügbar:

| Gebiet | Region           | Regionsbezeichner |
| ------------- | ---------------- | -------------------- |
| Amerika     | USA, Mitte   | `centralus` |
| Amerika     | East US   | `eastus`  |
| Amerika     | USA (Ost) 2  | `eastus2`  |
| Amerika     | USA, Westen-Mitte  | `westcentralus`  |
| Amerika     | USA, Westen  | `westus`  |
| Amerika     | USA, Westen 2  | `westus2`  |
| Asien-Pazifik  | Asien, Osten   | `eastasia` |
| Asien-Pazifik  | Asien, Südosten   | `southeastasia` |
| Asien-Pazifik  | Indien, Mitte   | `centralindia` |
| Australien     | Australien (Osten)   | `australiaeast` |
| Europa     | Nordeuropa   | `northeurope` |
| Europa     | Europa, Westen   | `westeurope` |
| Europa     | UK, Süden   | `uksouth` |

## <a name="rest-apis"></a>REST-APIs

Der Dienst Speech stellt auch REST-Endpunkte für Anfragen zu Sprache-zu-Text, Text-zu-Sprache und Sprechererkennung bereit.

### <a name="speech-to-text"></a>Spracherkennung

Die Referenzdokumentation zur Spracherkennung finden Sie unter [Spracherkennungs-REST-API](rest-speech-to-text.md).

Der Endpunkt für die REST-API weist das folgende Format auf:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersetzen Sie `<REGION_IDENTIFIER>` durch den Bezeichner aus der folgenden Tabelle, der mit der Region Ihres Abonnements übereinstimmt:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Der Sprachparameter muss an die URL angefügt werden, um HTTP Fehler des Typs „4xx“ zu vermeiden. Das folgende Beispiel zeigt die Spracheinstellung „Englisch (USA)“ bei Verwendung des Endpunkt „USA, Westen“: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Text-zu-Sprache

Die Referenzdokumentation zur Sprachsynthese finden Sie unter [Text-to-Speech-REST-API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="speaker-recognition"></a>Sprechererkennung

Eine Referenzdokumentation zur Sprechererkennung finden Sie unter [Speaker Recognition REST API](/rest/api/speakerrecognition/). Die verfügbaren Regionen sind die gleichen wie im Sprechererkennungs-SDK.
