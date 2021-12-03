---
title: Verwenden der Sprachenerkennung
titleSuffix: Azure Cognitive Services
description: Die Spracherkennung kann bei der Spracherkennung eingesetzt werden, um die Sprache zu bestimmen, die in den erkannten Sprachaufnahmen gesprochen wird.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/21/2021
ms.author: eur
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 94b3056f6766be37451a5d309243592eb31256dd
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488428"
---
# <a name="how-to-use-language-identification"></a>Verwenden der Sprachenerkennung

Die Sprachenerkennung wird verwendet, um aufgrund eines Vergleichs anhand einer Liste bereitgestellter Sprachen die gesprochene Sprache mit der an das Speech SDK übergebenen Audioeingabe zu ermitteln. Der von der Sprachenerkennung zurückgegebene Wert wird dann verwendet, um das Sprachmodell zur Umwandlung von Sprache in Text auszuwählen und Ihnen eine genauere Transkription zu bieten. 

Die Sprachenerkennung kann auch bei der [Sprachübersetzung](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall#multi-lingual-translation-with-language-identification) oder bei der [eigenständigen Erkennung](./language-identification.md) verwendet werden. 

Welche Sprachen verfügbar sind, erfahren Sie unter [Sprachunterstützung](language-support.md).

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie über ein Azure-Abonnement und eine Sprachressource verfügen, und es wird auch angenommen, dass Sie mit den Grundlagen der Spracherkennung vertraut sind. [Schließen Sie die Schnellstartanleitung ab](get-started-speech-to-text.md), sofern dies noch nicht erfolgt ist.

## <a name="language-identification-with-speech-to-text"></a>Sprachenerkennung mit Spracherkennung

Für die Sprachenerkennung gilt derzeit ein Grenzwert von **vier Sprachen** für die anfängliche Erkennung und von **zehn Sprachen** für die kontinuierliche Erkennung. Beachten Sie diese Einschränkung bei der Erstellung des `AutoDetectSourceLanguageConfig`-Objekts. In den folgenden Beispielen verwenden Sie `AutoDetectSourceLanguageConfig`, um eine Liste möglicher Sprachen zu definieren, die Sie erkennen möchten. Sie können dann beim Ausführen der Spracherkennung auf diese Sprachen verweisen.

> [!IMPORTANT]
> Die kontinuierliche Sprachenerkennung wird nur in C#, C++ und Python unterstützt.

::: zone pivot="programming-language-csharp"

Im folgenden Beispiel wird die anfängliche Erkennung ausgeführt und `Latency` priorisiert. Diese Eigenschaft kann je nach Priorität für Ihren Anwendungsfall auch auf `Accuracy` festgelegt werden. `Latency` ist die beste Option, die Sie verwenden können, wenn Sie ein latenzarmes Ergebnis benötigen (z. B. für Livestreamingszenarien), aber die Sprache im Audiobeispiel nicht kennen. 

`Accuracy` sollte in Szenarien verwendet werden, in denen die Audioqualität möglicherweise schlecht ist und eine höhere Wartezeit akzeptabel ist. Beispielsweise kann eine Voicemail Hintergrundgeräusche oder etwas Stille am Anfang aufweisen, und das Gewähren von mehr Zeit für die Engine verbessert die Erkennungsergebnisse.

In beiden Fällen sollte die anfängliche Erkennung, wie unten dargestellt, **nicht** für Szenarien verwendet werden, in denen sich die Sprache innerhalb desselben Audiobeispiels ändern kann. Informationen zur kontinuierlichen Erkennung für diese Arten von Szenarien finden Sie weiter unten.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE", "ja-JP", "de-DE" });

using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

Das folgende Beispiel zeigt die kontinuierliche Spracherkennung, die für ein mehrsprachiges Szenario eingerichtet wurde. In diesem Beispiel werden nur `en-US` und `ja-JP` in der Sprachkonfiguration verwendet, aber Sie können bis zu **zehn Sprachen** für dieses Entwurfsmuster verwenden. Jedes Mal, wenn Sprache erkannt wird, wird die Ausgangssprache identifiziert, und die Audiodaten werden zudem in die Textausgabe konvertiert. In diesem Beispiel wird der `Latency`-Modus verwendet, der die Antwortzeit priorisiert.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var region = "<paste-your-region>";
// currently the v2 endpoint is required for this design pattern
var endpointString = $"wss://{region}.stt.speech.microsoft.com/speech/universal/v2";
var endpointUrl = new Uri(endpointString);

var config = SpeechConfig.FromEndpoint(endpointUrl, "<paste-your-subscription-key>");
// can switch "Latency" to "Accuracy" depending on priority
config.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP" });

var stopRecognition = new TaskCompletionSource<int>();
using (var audioInput = AudioConfig.FromWavFileInput(@"path-to-your-audio-file.wav"))
{
    using (var recognizer = new SpeechRecognizer(config, autoDetectSourceLanguageConfig, audioInput))
    {
        // Subscribes to events.
        recognizer.Recognizing += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizingSpeech)
            {
                Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
        };

        recognizer.Recognized += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizedSpeech)
            {
                Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
            else if (e.Result.Reason == ResultReason.NoMatch)
            {
                Console.WriteLine($"NOMATCH: Speech could not be recognized.");
            }
        };

        recognizer.Canceled += (s, e) =>
        {
            Console.WriteLine($"CANCELED: Reason={e.Reason}");

            if (e.Reason == CancellationReason.Error)
            {
                Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                Console.WriteLine($"CANCELED: Did you update the subscription info?");
            }

            stopRecognition.TrySetResult(0);
        };

        recognizer.SessionStarted += (s, e) =>
        {
            Console.WriteLine("\n    Session started event.");
        };

        recognizer.SessionStopped += (s, e) =>
        {
            Console.WriteLine("\n    Session stopped event.");
            Console.WriteLine("\nStop recognition.");
            stopRecognition.TrySetResult(0);
        };

        // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
        await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

        // Waits for completion.
        // Use Task.WaitAny to keep the task rooted.
        Task.WaitAny(new[] { stopRecognition.Task });

        // Stops recognition.
        await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
    }
}
```

> [!NOTE]
> Die Modi `Latency` und `Accuracy` sowie die mehrsprachige kontinuierliche Erkennung werden derzeit nur in C#, C++ und Python unterstützt.
 
::: zone-end

::: zone pivot="programming-language-cpp"

Im folgenden Beispiel wird die anfängliche Erkennung ausgeführt und `Latency` priorisiert. Diese Eigenschaft kann je nach Priorität für Ihren Anwendungsfall auch auf `Accuracy` festgelegt werden. `Latency` ist die beste Option, die Sie verwenden können, wenn Sie ein latenzarmes Ergebnis benötigen (z. B. für einen Livestreamingfall), aber die Sprache im Audiobeispiel nicht kennen. 

`Accuracy` sollte in Szenarien verwendet werden, in denen die Audioqualität möglicherweise schlecht ist und eine höhere Wartezeit akzeptabel ist. Beispielsweise kann eine Voicemail Hintergrundgeräusche oder etwas Stille am Anfang aufweisen, und das Gewähren von mehr Zeit für die Engine verbessert die Erkennungsergebnisse.

In beiden Fällen sollte die anfängliche Erkennung, wie unten dargestellt, **nicht** für Szenarien verwendet werden, in denen sich die Sprache innerhalb desselben Audiobeispiels ändern kann. Informationen zur kontinuierlichen Erkennung für diese Arten von Szenarien finden Sie weiter unten.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto speechConfig = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE", "ja-JP", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

Das folgende Beispiel zeigt die kontinuierliche Spracherkennung, die für ein mehrsprachiges Szenario eingerichtet wurde. In diesem Beispiel werden nur `en-US` und `ja-JP` in der Sprachkonfiguration verwendet, aber Sie können bis zu **zehn Sprachen** für dieses Entwurfsmuster verwenden. Jedes Mal, wenn Sprache erkannt wird, wird die Ausgangssprache identifiziert, und die Audiodaten werden zudem in die Textausgabe konvertiert. In diesem Beispiel wird der `Latency`-Modus verwendet, der die Antwortzeit priorisiert.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto region = "<paste-your-region>";
// currently the v2 endpoint is required for this design pattern
auto endpointString = std::format("wss://{}.stt.speech.microsoft.com/speech/universal/v2", region);
auto config = SpeechConfig::FromEndpoint(endpointString, "<paste-your-subscription-key>");

config->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP" });

auto audioInput = AudioConfig::FromWavFileInput("path-to-your-audio-file.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, autoDetectSourceLanguageConfig, audioInput);

// promise for synchronization of recognition end.
promise<void> recognitionEnd;

// Subscribes to events.
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
        cout << "Recognizing in " << lidResult->Language << ": Text =" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
            cout << "RECOGNIZED in " << lidResult->Language << ": Text=" << e.Result->Text << "\n"
                << "  Offset=" << e.Result->Offset() << "\n"
                << "  Duration=" << e.Result->Duration() << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;

        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });

recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

> [!NOTE]
> Die Modi `Latency` und `Accuracy` sowie die mehrsprachige kontinuierliche Erkennung werden derzeit nur in C#, C++ und Python unterstützt.

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end


## <a name="use-a-custom-model-for-language-identification"></a>Verwenden eines benutzerdefinierten Modells für die Sprachenerkennung

Zur Ergänzung der Sprachenerkennung mit Basismodellen des Speech-Diensts können Sie auch ein benutzerdefiniertes Modell zur verbesserten Erkennung angeben. Wenn kein benutzerdefiniertes Modell bereitgestellt wird, verwendet der Dienst das Standardsprachmodell.

Die folgenden Codeausschnitte veranschaulichen, wie Sie beim Aufruf des Spracherkennungsdiensts ein benutzerdefiniertes Modell angeben. Wenn die erkannte Sprache `en-US` ist, wird das Standardmodell verwendet. Wenn die erkannte Sprache `fr-FR` ist, wird der Endpunkt für das benutzerdefinierte Modell verwendet:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end


## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="programming-language-csharp"
* Informationen zur Sprachenerkennung finden Sie im [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) auf GitHub.
::: zone-end

::: zone pivot="programming-language-cpp"
* Informationen zur Sprachenerkennung finden Sie im [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) auf GitHub.
::: zone-end

::: zone pivot="programming-language-java"
* Informationen zur Sprachenerkennung finden Sie im [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) auf GitHub.
::: zone-end

::: zone pivot="programming-language-python"
* Informationen zur Sprachenerkennung finden Sie im [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L458) auf GitHub.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Informationen zur Sprachenerkennung finden Sie im [Beispielcode](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L525) auf GitHub.
::: zone-end
