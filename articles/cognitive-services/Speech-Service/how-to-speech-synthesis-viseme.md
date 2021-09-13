---
title: Abrufen von Gesichtsausdrucksereignissen für die Lippensynchronisierung
titleSuffix: Azure Cognitive Services
description: Das Speech SDK unterstützt Visemereignisse während der Sprachsynthese. Diese stellen wichtige Gesichtsausdrücke beim Sprechen dar, z. B. die Position der Lippen, des Kiefers und der Zunge beim Erzeugen eines bestimmten Phonems.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 3601cd6f7580a4d87dda7488826e25ca85b233c9
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654186"
---
# <a name="get-facial-pose-events"></a>Abrufen von Gesichtsausdrucksereignissen

> [!NOTE]
> Visemereignisse sind vorerst nur für [neuronale Stimmen](language-support.md#text-to-speech) in `en-US` Englisch (USA) verfügbar.

Ein _Visem_ ist die visuelle Beschreibung eines Phonems in der gesprochenen Sprache.
Es beschreibt die Position des Gesichts und Munds beim Sprechen eines Worts.
Jedes Visem stellt die wichtigsten Gesichtsausdrücke für einen bestimmten Satz von Phonemen dar.
Mundbilder können verwendet werden, um die Bewegung von 2D- und 3D-Avatarmodellen zu steuern, mit perfekter Anpassung von Mundbewegungen an synthetische Sprache.

Mit Mundbildern lassen sich Avatare einfacher nutzen und steuern. Mundbilder bieten folgende Möglichkeiten:

 * Erstellen Sie einen **animierten virtuellen Sprachassistenten** für intelligente Kioske, um integrierte Mehrfachmodusdienste für Ihre Kunden zu entwickeln.
 * Erstellen Sie **immersive Nachrichtensendungen**, und verbessern Sie das Zuschauererlebnis mit natürlichen Gesichts- und Mundbewegungen.
 * Erstellen Sie **interaktivere Gaming-Avatare und Cartoon-Figuren**, die mit dynamischen Inhalten sprechen können.
 * Erstellen Sie **effektivere Sprachlernvideos**, die Sprachlernenden helfen, das Mundverhalten der einzelnen Wörter und Phoneme zu verstehen.
 * Hörgeschädigte können Töne auch visuell wahrnehmen und Sprachinhalte von den **„Lippen“** ablesen, die auf einem animierten Gesicht zu sehen sind.

Sehen Sie sich das [Einführungsvideo](https://youtu.be/ui9XT47uwxs) des Mundbilds an.
> [!VIDEO https://www.youtube.com/embed/ui9XT47uwxs]

## <a name="azure-neural-tts-can-produce-visemes-with-speech"></a>Neuronales Text-to-Speech von Azure kann Mundbilder mit Sprache erzeugen.

Eine neuronale Stimme wandelt eingegebenen Text oder SSML (Speech Synthesis Markup Language) in synthetisierte Sprache um. Die Audiosprachausgabe kann durch Mundbild-IDs und deren Versatzzeitstempel ergänzt werden. Jede Mundbild-ID gibt eine bestimmte Haltung in der beobachteten Sprache an, z. B. die Position der Lippen, des Kiefers und der Zunge bei der Erzeugung eines bestimmten Phonems. Mit einem 2D- oder 3D-Renderingmodul können Sie diese Mundbildereignisse nutzen, um Ihren Avatar zu animieren.

Der gesamte Workflow eines Mundbilds ist im folgenden Flussdiagramm dargestellt.

![Der gesamte Workflow eines Mundbilds](media/text-to-speech/viseme-structure.png)

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| VisemeId | Eine ganze Zahl, die ein Visem angibt. In der Sprache „Englisch (USA)“ werden 22 verschiedene Viseme geboten, um die Mundformen für einen bestimmten Satz von Phonemen darzustellen. Es besteht keine 1:1-Entsprechung zwischen Visemen und Phonemen. Oftmals entsprechen mehrere Phoneme einem einzelnen Visem, da mehrere Phoneme, z. B. `s` und `z`, mit dem gleichen Mundbild einhergehen. Weitere Informationen finden Sie in der [Tabelle der Zuordnungen zwischen Visem-IDs und Phonemen](#map-phonemes-to-visemes).  |
| AudioOffset | Die Startzeit jedes Visems in Takten (100 Nanosekunden) |

## <a name="get-viseme-events-with-the-speech-sdk"></a>Abrufen von Visemereignissen mit dem Speech SDK

Abonnieren Sie das `VisemeReceived`-Ereignis im Speech SDK, um Mundbilder mit Ihrer synthetisierten Sprache zu erhalten.
Der folgende Codeausschnitt zeigt das Abonnieren des Mundbildereignisses.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

Hier ist ein Beispiel für die Mundbildausgabe.

```text
(Viseme), Viseme ID: 1, Audio offset: 200ms.

(Viseme), Viseme ID: 5, Audio offset: 850ms.

……

(Viseme), Viseme ID: 13, Audio offset: 2350ms.
```

Nachdem Sie die Mundbildausgabe erhalten haben, können Sie diese Ereignisse zur Steuerung der Figurenanimation verwenden. Sie können Ihre eigenen Figuren erstellen und diese automatisch animieren.

Für 2D-Figuren können Sie eine Figur entwerfen, die zu Ihrem Szenario passt, und Scalable Vector Graphics (SVG) für jede Mundbild-ID verwenden, um eine zeitbasierte Gesichtsposition zu erhalten. Mit temporalen Tags, die in einem Mundbildereignis bereitgestellt werden, werden diese gut gestalteten SVGs mit Glättungsmodifikationen verarbeitet und bieten den Benutzern stabile Animationen. Die folgende Abbildung zeigt zum Beispiel eine Figur mit roten Lippen, die zum Sprachenlernen konzipiert wurde.

![2D-Renderbeispiel](media/text-to-speech/viseme-demo-2D.png)

Bei 3D-Figuren können Sie sich die Figuren als Marionetten vorstellen. Der Puppenspieler zieht die Fäden von einem Zustand zum anderen, und die Gesetze der Physik erledigen den Rest und sorgen dafür, dass sich die Marionette fließend bewegt. Die Mundbildausgabe fungiert als Puppenspieler, der eine Aktionszeitleiste bereitstellt. Das Animationsmodul definiert die physischen Gesetze der Aktion. Durch die Interpolation von Frames mit Lockerungsalgorithmen kann das Modul außerdem hochwertige Animationen erzeugen.

## <a name="map-phonemes-to-visemes"></a>Zuordnen von Phonemen zu Visemen

Viseme variieren je nach Sprache. Jede Sprache verfügt über einen Satz von Visemen, die den jeweiligen Phonemen entsprechen. In der folgenden Tabelle wird die Entsprechung zwischen Visemen nach dem internationalen phonetischen Alphabet (IPA) und Visem-IDs für „Englisch (USA)“ gezeigt.

| IPA | Beispiel | Visem-ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard, f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|T  |   **d** ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|m  |   **m** at, s **m** ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|s  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id, g **l** ad     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>Nächste Schritte

* [Speech SDK-Referenzdokumentation](speech-sdk.md)
