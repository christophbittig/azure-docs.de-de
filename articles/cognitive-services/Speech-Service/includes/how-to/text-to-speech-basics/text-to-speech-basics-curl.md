---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 2272fbe18ac4d2241618f650fd3dab223ef242ee
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510878"
---
In dieser Schnellstartanleitung wird beschrieben, wie Sie Text in Sprache konvertieren, indem Sie den Speech-Dienst und cURL verwenden.

Allgemeine Informationen zu den Konzepten von Text-zu-Sprache finden Sie im [Übersichtsartikel](../../../text-to-speech.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Konvertieren von Text in Sprache

Führen Sie an der Eingabeaufforderung den folgenden Befehl aus. Sie müssen die folgenden Werte in den Befehl einfügen.
- Ihr Abonnementschlüssel des Spracherkennungsdiensts.
- Die Region Ihres Speech-Diensts.

Unter Umständen ist es auch erforderlich, die folgenden Werte zu ändern.
- Headerwert `X-Microsoft-OutputFormat`, mit dem das Format der Audioausgabe gesteuert wird. Eine Liste mit den unterstützten Formaten für die Audioausgabe finden Sie in der [Referenz zur Text-to-Speech-REST-API](../../../rest-text-to-speech.md#audio-outputs).
- Die Stimme für die Ausgabe. Eine Liste mit den Stimmen, die für Ihren Speech-Endpunkt verfügbar sind, finden Sie im nächsten Abschnitt.
- Die Ausgabedatei. In diesem Beispiel leiten wir die Antwort des Servers an eine Datei mit dem Namen `output.wav` weiter.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Auflisten von verfügbaren Stimmen für Ihren Speech-Endpunkt

Führen Sie den folgenden Befehl aus, um die verfügbaren Stimmen für Ihren Speech-Endpunkt aufzulisten.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Die Antwort sollte in etwa wie folgt aussehen:

```http
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "DisplayName": "Hoda",
        "LocalName": "هدى",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "DisplayName": "Naayf",
        "LocalName": "نايف",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "DisplayName": "Ivan",
        "LocalName": "Иван",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        // This response is truncated. The response will include 
        // a complete list of supported languages and specific 
        // details like short name, gender, etc. 
    }
]
```
