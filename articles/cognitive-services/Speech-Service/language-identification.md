---
title: 'Sprachenerkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Die Sprachenerkennung wird verwendet, um aufgrund eines Vergleichs anhand einer Liste bereitgestellter Sprachen die gesprochene Sprache mit der an das Speech SDK übergebenen Audioeingabe zu ermitteln.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-cs-cpp-py
ms.openlocfilehash: d840ddce060e2ada6a0a237a896201dafdc90fac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511232"
---
# <a name="language-identification-preview"></a>Sprachenerkennung (Vorschauversion)

Die Sprachenerkennung wird verwendet, um aufgrund eines Vergleichs anhand einer Liste bereitgestellter Sprachen die gesprochene Sprache mit der an das Speech SDK übergebenen Audioeingabe zu ermitteln. 

Die Sprachenerkennung kann auch bei der [Sprachübersetzung](./get-started-speech-translation.md#multi-lingual-translation-with-language-identification) oder bei der [Sprachenerkennung im Rahmen der Spracherkennung](./how-to-automatic-language-detection.md) verwendet werden. 

Welche Sprachen verfügbar sind, erfahren Sie unter [Sprachunterstützung](language-support.md).

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie über ein Azure-Abonnement und eine Sprachressource verfügen, und es wird auch angenommen, dass Sie mit den Grundlagen der Spracherkennung vertraut sind.

## <a name="standalone-language-identification"></a>Eigenständige Sprachenerkennung

In Anwendungsfällen, in denen Sie nur die gesprochene Ausgangssprache erkennen möchten, können Sie die eigenständige Sprachenerkennung verwenden, wie im folgenden Codebeispiel gezeigt. `SourceLanguageRecognizer` kann auch in Szenarien mit kontinuierlicher Erkennung verwendet werden.

::: zone pivot="programming-language-csharp"

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SourceLanguageRecognizer(speechConfig, autoDetectSourceLanguageConfig))
{
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.RecognizedSpeech)
    {
        var lang = AutoDetectSourceLanguageResult.FromResult(result).Language;
        Console.WriteLine($"DETECTED: Language={lang}");
    }
}
```

Im [Beispiel auf GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/standalone_language_detection_samples.cs) finden Sie weitere Beispiele für die eigenständige Sprachenerkennung, einschließlich eines Beispiels für die kontinuierliche Erkennung.

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
config->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SourceLanguageRecognizer::FromConfig(config, autoDetectSourceLanguageConfig);
cout << "Say something...\n";

auto result = recognizer->RecognizeOnceAsync().get();
if (result->Reason == ResultReason::RecognizedSpeech)
{
    auto lidResult = AutoDetectSourceLanguageResult::FromResult(result);
    cout << "DETECTED: Language="<< lidResult->Language << std::endl;
}
```

Im [Beispiel auf GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/standalone_language_detection_samples.cpp) finden Sie weitere Beispiele für die eigenständige Sprachenerkennung, einschließlich eines Beispiels für die kontinuierliche Erkennung.

::: zone-end

::: zone pivot="programming-language-python"

```python
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    
speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, value='Accuracy')

speech_language_detection = speechsdk.SourceLanguageRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

result = speech_language_detection.recognize_once()

# Check the result
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("RECOGNIZED: {}".format(result))
    detectedSrcLang = result.properties[speechsdk.PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult]
    print("Detected Language: {}".format(detectedSrcLang))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized")
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

Im [Beispiel auf GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_language_detection_sample.py) finden Sie weitere Beispiele für die eigenständige Sprachenerkennung, einschließlich eines Beispiels für die kontinuierliche Erkennung.

::: zone-end
