---
title: Abfrage eines Containerendpunkts für die Sprachsynthese
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: eur
ms.openlocfilehash: e81b2bfb8781b3c8d14d182d8d6da14b48484953
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501070"
---
Der Container stellt [REST-basierte Endpunkt-APIs](../rest-text-to-speech.md) bereit. Es gibt viele [Beispielquellcodeprojekte](https://github.com/Azure-Samples/Cognitive-Speech-TTS) für Plattform-, Framework- und Sprachvarianten.

Mit den Containern für die Standard- oder Neuronalsprachsynthese sollten Sie sich auf das Gebietsschema und die Sprache des heruntergeladenen Imagetags verlassen. Wenn Sie z. B. das `latest`-Tag heruntergeladen haben, ist das Standardgebietsschema `en-US` und `AriaNeural` die Stimme. Das `{VOICE_NAME}`-Argument wäre dann [`en-US-AriaNeural`](../language-support.md#neural-voices). Betrachten Sie das folgende SSML-Beispiel:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```
