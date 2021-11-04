---
title: Schnellstart für die Sprachsynthese – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie das Speech SDK verwenden, um Text in Sprache zu konvertieren. In dieser Schnellstartanleitung werden die Objektkonstruktion und Entwurfsmuster, die unterstützten Formate für die Audioausgabe, die Speech-CLI und die benutzerdefinierten Konfigurationsoptionen für die Sprachsynthese beschrieben.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/17/2021
ms.author: eur
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: Sprachsynthese
ms.openlocfilehash: 36fcad1845a654aff1e195e23bddb8090add6719
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510801"
---
# <a name="get-started-with-text-to-speech"></a>Erste Schritte bei der Sprachsynthese

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Abrufen von Positionsinformationen

Ihr Projekt benötigt möglicherweise die Information, wann ein Wort von der Sprachsynthese gesprochen wird, um auf der Grundlage dieses Timings eine bestimmte Aktion ausführen zu können.
Wenn Sie also beispielsweise Wörter hervorheben möchten, während sie gesprochen werden, müssen Sie wissen, welche Wörter wann und wie lange hervorgehoben werden sollen.

Hierzu können Sie das in `SpeechSynthesizer` verfügbare Ereignis `WordBoundary` verwenden.
Dieses Ereignis wird am Anfang jedes neuen gesprochenen Worts ausgelöst und stellt einen Zeitversatz innerhalb des gesprochenen Streams sowie einen Textversatz innerhalb der Eingabeaufforderung bereit.

* `AudioOffset` meldet die verstrichene Zeit der Audioausgabe zwischen dem Beginn der Synthese und dem Anfang des nächsten Worts. Dieser Wert wird in HNS-Einheiten (hundert Nanosekunden) gemessen. 10.000 HNS entsprechen einer Millisekunde.
* `WordOffset` meldet die Zeichenposition in der Eingabezeichenfolge (ursprünglicher Text oder [SSML](speech-synthesis-markup.md)) unmittelbar vor dem Wort, das als Nächstes gesprochen wird.

> [!NOTE]
> Ereignisse vom Typ `WordBoundary` werden ausgelöst, wenn die ausgegebenen Audiodaten verfügbar werden, was schneller passiert als die Wiedergabe über ein Ausgabegerät. Das Timing des Streams muss vom Aufrufer ordnungsgemäß mit der tatsächlichen Zeit synchronisiert werden.

Beispiele für die Verwendung von `WordBoundary` finden Sie auf GitHub in den [Beispielen für die Sprachsynthese](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Custom Voice](how-to-custom-voice.md)
* [Verbessern der Synthese mit Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML)](speech-synthesis-markup.md)
* Informieren Sie sich darüber, wie Sie die [API für lange Audioinhalte](long-audio-api.md) für längere Texte, z. B. Bücher und Zeitungsartikel, verwenden.
* Sehen Sie sich die [Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) auf GitHub an.
