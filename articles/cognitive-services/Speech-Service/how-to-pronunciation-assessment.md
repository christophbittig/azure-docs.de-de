---
title: Verwenden des Speech SDK zur Bewertung der Aussprache
titleSuffix: Azure Cognitive Services
description: Das Speech SDK unterstützt die Aussprachebewertung, bei der die Qualität der Aussprache von Spracheingaben mit Indikatoren für Genauigkeit, Redefluss, Vollständigkeit usw. bewertet wird.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2335b1e85290cdea805c3a77754a5843da6cbbda
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672360"
---
# <a name="pronunciation-assessment"></a>Aussprachebewertung

Die Aussprachebewertung bewertet die Aussprache und gibt den Rednern Feedback zur Genauigkeit und zum Redefluss der gesprochenen Audioinformationen.
Mit dieser Aussprachebewertung erhalten Menschen, die Fremdsprachen lernen, direktes Feedback und können ihre Aussprache verbessern, sodass sie selbstbewusst sprechen und sich präsentieren können.
Lehrkräfte können diese Funktion verwenden, um die Aussprache mehrerer Redner in Echtzeit auszuwerten.

In diesem Artikel erfahren Sie, wie Sie `PronunciationAssessmentConfig` einrichten und `PronunciationAssessmentResult` mit dem Speech SDK abrufen.

> [!NOTE]
> Die Funktion zur Beurteilung der Aussprache unterstützt `en-US` derzeit Sprache, die in allen [Sprache-in-Text-Regionen verfügbar ist](regions.md#speech-to-text-text-to-speech-and-translation). Die Unterstützung für die Sprachen `en-GB` und `zh-CN` ist in der Vorschau.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Aussprachebewertung mit dem Speech SDK

In den folgenden Beispielen erstellen Sie eine `PronunciationAssessmentConfig`, und wenden sie dann auf einen `SpeechRecognizer` an.

Die folgenden Codeausschnitte veranschaulichen die Verwendung der Sprachenerkennung in Ihren Apps:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Konfigurationsparameter der Aussprachebewertung

In dieser Tabelle sind die Konfigurationsparameter für die Aussprachebewertung aufgeführt.

| Parameter | BESCHREIBUNG | Erforderlich? |
|-----------|-------------|---------------------|
| ReferenceText | Der Text, für den die Aussprache ausgewertet wird. | Erforderlich |
| GradingSystem | Das Punktesystem zur Kalibrierung der Bewertung. Das `FivePoint`-System gibt eine 0-5-Gleitkommabewertung und `HundredMark` eine 0-100-Gleitkommabewertung aus. Standardwert: `FivePoint`. | Optional |
| Granularität | Die Granularität der Auswertung. Akzeptierte Werte sind `Phoneme`, für das die Bewertung auf Volltext-, Wort- und Phonemebene, `Word`, für das die Bewertung auf Volltext und Wortebene und `FullText`, für das nur die Bewertung auf der Volltextebene angezeigt wird. Standardwert: `Phoneme`. | Optional |
| EnableMiscue | Aktiviert die Fehlschlagsberechnung. Wenn diese Option aktiviert ist, werden die ausgesprochenen Wörter mit dem Referenztext verglichen und auf der Grundlage des Vergleichs mit Auslassung/Einfügung gekennzeichnet. Zulässige Werte sind `False` und `True`. Standardwert: `False`. | Optional |
| ScenarioId | Eine GUID, die ein benutzerdefiniertes Punktesystem angibt. | Optional |

### <a name="pronunciation-assessment-result-parameters"></a>Ergebnisparameter für die Aussprachebewertung

Diese Tabelle listet die Ergebnisparameter der Aussprachebewertung auf.

| Parameter | Beschreibung |
|-----------|-------------|
| `AccuracyScore` | Genauigkeit der Aussprache des Texts. Genauigkeit heißt dabei, wie exakt Phoneme der Aussprache eines Muttersprachlers entsprechen. Der Score für Genauigkeit auf Wort- und Volltextebene wird aus dem Score der Genauigkeit auf Phonemebene aggregiert. |
| `FluencyScore` | Redefluss eines gegebenen Texts. Der Redefluss bedeutet, wie exakt der ausgegebene Text mit den Pausen zwischen Wörtern eines Muttersprachlers übereinstimmt. |
| `CompletenessScore` | Vollständigkeit des Texts. Dafür wird das Verhältnis ausgesprochener Wörter zur Texteingabe berechnet. |
| `PronunciationScore` | Der Gesamtscore, der die Aussprachequalität des übergebenen Sprachtexts angibt. Dieser wird aus `AccuracyScore`, `FluencyScore` und `CompletenessScore` mit Gewichtung aggregiert. |
| `ErrorType` | Dieser Wert gibt an, ob ein Wort im Vergleich zu `ReferenceText`ausgelassen, eingefügt oder schlecht ausgesprochen wird. Die möglichen Werte sind `None` (d. h. kein Fehler in diesem Wort), `Omission`, `Insertion` und `Mispronunciation`. |

### <a name="sample-responses"></a>Beispielantworten

Ein typisches Ergebnis einer Aussprachebeurteilung in JSON:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

<!-- TODO: update JavaScript sample links after release -->

* Sehen Sie sich die[Videoeinführung](https://www.youtube.com/watch?v=cBE8CUHOFHQ) -und [Videotutorial](https://www.youtube.com/watch?v=zFlwm7N4Awc) zur  Aussprachebeurteilung an

* Testen Sie die [Demo zur Aussprachebeurteilung](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)

::: zone pivot="programming-language-csharp"
* Sehen Sie sich den [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) auf GitHub für die Aussprachebewertung an.
::: zone-end

::: zone pivot="programming-language-cpp"
* Sehen Sie sich den [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) auf GitHub für die Aussprachebewertung an.
::: zone-end

::: zone pivot="programming-language-java"
* Sehen Sie sich den [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) auf GitHub für die Aussprachebewertung an.
::: zone-end

::: zone pivot="programming-language-python"
* Sehen Sie sich den [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) auf GitHub für die Aussprachebewertung an.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Sehen Sie sich den [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) auf GitHub für die Aussprachebewertung an.
::: zone-end

* [Speech SDK-Referenzdokumentation](speech-sdk.md)

* [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
