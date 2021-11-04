---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: eur
ms.custom: devx-track-csharp
ms.openlocfilehash: bc554bf54f83d8721ef29b531feebd99757b2dce
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506921"
---
Eines der zentralen Features des Speech-Diensts ist die Fähigkeit, menschliche Sprache zu erkennen und in andere Sprachen zu übersetzen. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Sprachübersetzungen durchzuführen. In diesem Schnellstart werden folgende Themen behandelt:

* Übersetzen von Sprache in Text
* Übersetzen von Sprache in mehrere Zielsprachen
* Durchführen einer direkten Übersetzung von Sprache in Sprache

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [C#-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Folgen Sie je nach Plattform den Anweisungen im Abschnitt <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Abrufen des Speech SDK</a> des Artikels _Abrufen des Speech SDK_.

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie die folgenden `using`-Anweisungen oben in der *Program.cs*-Codedatei ein, um die Beispiele in diesem Artikel auszuführen.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Vertrauliche Daten und Umgebungsvariablen

Der Beispielquellcode in diesem Artikel benötigt Umgebungsvariablen zum Speichern vertraulicher Daten, wie z. B. den Schlüssel und die Region des Speech-Ressourcenabonnements. Die `Program`-Klasse enthält zwei `static readonly string`-Werte, die von den Umgebungsvariablen des Hostcomputers zugewiesen werden, nämlich `SPEECH__SUBSCRIPTION__KEY` und `SPEECH__SERVICE__REGION`. Beide Felder befinden sich im Klassenbereich, sodass sie innerhalb vom Methodentext der Klasse zugänglich sind. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Umgebungsvariablen und Anwendungskonfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Erstellen einer Sprachübersetzungskonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechTranslationConfig`][config]) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!TIP]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechTranslationConfig`][config]) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und die zugeordnete Region.

Hier sehen Sie, wie eine Sprachkonfiguration ([`SpeechTranslationConfig`][config]) mit einem Schlüssel und einer Region erstellt wird. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen.

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine häufige Aufgabe bei der Sprachübersetzung ist die Angabe der Eingabesprache (Quellsprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Interagieren Sie in Ihrem Code mit der [`SpeechTranslationConfig`][config]-Instanz, und weisen Sie die `SpeechRecognitionLanguage`-Eigenschaft zu.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

Von der Eigenschaft [`SpeechRecognitionLanguage`][recognitionlang] wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="add-translation-language"></a>Hinzufügen der Übersetzungssprache

Eine weitere häufige Aufgabe bei der Sprachübersetzung ist die Angabe von Zielübersetzungssprachen, wobei mindestens eine erforderlich ist, aber die Verwendung mehrerer unterstützt wird. Im folgenden Codeausschnitt sind sowohl Französisch als auch Deutsch als Zielübersetzungssprachen angegeben.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Mit jedem Aufruf von [`AddTargetLanguage`][addlang] wird eine neue Zielübersetzungssprache angegeben. Anders ausgedrückt: Wenn für die Ausgangssprache eine Spracherkennung erfolgt, wird im Rahmen des daraus resultierenden Übersetzungsvorgangs eine Zielübersetzung bereitgestellt.

## <a name="initialize-a-translation-recognizer"></a>Initialisieren einer Übersetzungserkennung

Nach der Erstellung einer Sprachkonfiguration ([`SpeechTranslationConfig`][config]) muss eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisiert werden. Wenn Sie eine Spracherkennung ([`TranslationRecognizer`][recognizer]) initialisieren, müssen Sie Ihre Sprachkonfiguration (`translationConfig`) übergeben. Das Konfigurationsobjekt stellt Anmeldeinformationen bereit, die der Speech-Dienst zur Überprüfung Ihrer Anforderung benötigt.

Wenn Sie für die Spracherkennung das Standardmikrofon Ihres Geräts verwenden, sollte [`TranslationRecognizer`][recognizer] wie folgt aussehen:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Wenn Sie das Audioeingabegerät angeben möchten, müssen Sie eine Audiokonfiguration ([`AudioConfig`][audioconfig]) erstellen und beim Initialisieren Ihrer Spracherkennung ([`TranslationRecognizer`][recognizer]) den Parameter `audioConfig` angeben.

> [!TIP]
> Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

Verweisen Sie zunächst auf das `AudioConfig`-Objekt. Gehen Sie dabei wie folgt vor:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Eine Audiokonfiguration (`audioConfig`) ist auch erforderlich, wenn Sie anstelle eines Mikrofons eine Audiodatei verwenden möchten. In diesem Fall muss beim Erstellen einer Audiokonfiguration ([`AudioConfig`][audioconfig]) allerdings `FromWavFileInput` (anstelle von `FromDefaultMicrophoneInput`) aufgerufen und der Parameter `filename` übergeben werden.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Übersetzen von gesprochener Sprache

Zum Übersetzen von Sprache benötigt das Speech SDK eine Audioeingabe von einem Mikrofon oder einer Audiodatei. Die Spracherkennung erfolgt vor der Sprachübersetzung. Nachdem alle Objekte initialisiert wurden, verwenden Sie die Funktion „Einmal erkennen“, und rufen Sie das Ergebnis ab.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Weitere Informationen zur Spracherkennung finden Sie unter [Grundlegendes zur Spracherkennung](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Synthetisieren von Übersetzungen

Nach erfolgreicher Spracherkennung und -übersetzung werden alle Übersetzungen in einem Wörterbuch bereitgestellt. Der Wörterbuchschlüssel [`Translations`][translations] ist die Zielübersetzungssprache, und der Wert ist der übersetzte Text. Erkannte Sprache kann übersetzt und dann in einer anderen Sprache synthetisiert werden (Sprache-zu-Sprache).

### <a name="event-based-synthesis"></a>Ereignisbasierte Synthese

Das `TranslationRecognizer`-Objekt macht ein einzelnes `Synthesizing`-Ereignis verfügbar. Das Ereignis wird mehrmals ausgelöst und bietet einen Mechanismus, mit dem das synthetische Audioformat aus dem Übersetzungserkennungsergebnis abgerufen werden kann. Weitere Informationen zum Übersetzen in mehrere Sprachen finden Sie unter [Manuelle Synthese](#manual-synthesis). Geben Sie die Synthesestimme an, indem Sie einen [`VoiceName`][voicename] zuweisen und einen Ereignishandler für das `Synthesizing`-Ereignis zum Abrufen der Audioinhalte bereitstellen. Im folgenden Beispiel wird die übersetzte Audiodatei als *WAV*-Datei gespeichert.

> [!IMPORTANT]
> Die ereignisbasierte Synthese funktioniert nur mit einer einzigen Übersetzung. Fügen Sie deshalb **nur eine** Zielübersetzungssprache hinzu. Zudem muss die Sprache in [`VoiceName`][voicename] mit der Zielübersetzungssprache übereinstimmen. So wird `"de"` beispielsweise `"de-DE-Hedda"` zugeordnet.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Manuelle Synthese

Das [`Translations`][translations]-Wörterbuch kann verwendet werden, um Audiodaten aus dem Übersetzungstext zu synthetisieren. Durchlaufen Sie die Übersetzungen, und synthetisieren Sie die jeweiligen Übersetzungen. Beim Erstellen einer `SpeechSynthesizer`-Instanz muss die [`SpeechSynthesisVoiceName`][speechsynthesisvoicename]-Eigenschaft des `SpeechConfig`-Objekts auf die gewünschte Stimme festgelegt werden. Im folgenden Beispiel wird in fünf Sprachen übersetzt. Jede Übersetzung wird anschließend in eine Audiodatei in der entsprechenden neuronalen Sprache synthetisiert.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Weitere Informationen zur Sprachsynthese finden Sie unter [Grundlegendes zur Sprachsynthese](../../../get-started-text-to-speech.md).

## <a name="multi-lingual-translation-with-language-identification"></a>Mehrsprachige Übersetzung mit Sprachidentifikation

In vielen Szenarios wissen Sie möglicherweise nicht, welche Eingabesprachen angegeben werden sollen. Mithilfe der Sprachidentifikation können Sie bis zu zehn mögliche Eingabesprachen angeben und automatisch in Ihre Zielsprachen übersetzen. 

Im folgenden Beispiel wird die kontinuierliche Übersetzung aus einer Audiodatei verwendet, und die Eingabesprache wird automatisch erkannt, auch wenn sich die gesprochene Sprache ändert. Wenn Sie das Beispiel ausführen, werden `en-US` und `zh-CN` automatisch erkannt, da sie in der `AutoDetectSourceLanguageConfig` definiert sind. Anschließend wird die Sprache wie in den Aufrufen von `AddTargetLanguage()` angegeben in `de` und `fr` übersetzt.

> [!IMPORTANT]
> Diese Funktion steht derzeit als **Vorschau** zur Verfügung.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

public static async Task MultiLingualTranslation()
{
    var region = "<paste-your-region>";
    // currently the v2 endpoint is required for this design pattern
    var endpointString = $"wss://{region}.stt.speech.microsoft.com/speech/universal/v2";
    var endpointUrl = new Uri(endpointString);
    
    var config = SpeechConfig.FromEndpoint(endpointUrl, "<paste-your-subscription-key>");

    // Source lang is required, but is currently NoOp 
    string fromLanguage = "en-US";
    config.SpeechRecognitionLanguage = fromLanguage;

    config.AddTargetLanguage("de");
    config.AddTargetLanguage("fr");

    config.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
    var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "zh-CN" });

    var stopTranslation = new TaskCompletionSource<int>();
    using (var audioInput = AudioConfig.FromWavFileInput(@"path-to-your-audio-file.wav"))
    {
        using (var recognizer = new TranslationRecognizer(config, autoDetectSourceLanguageConfig, audioInput))
        {
            recognizer.Recognizing += (s, e) =>
            {
                var lidResult = e.Result.Properties.GetProperty(PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult);

                Console.WriteLine($"RECOGNIZING in '{lidResult}': Text={e.Result.Text}");
                foreach (var element in e.Result.Translations)
                {
                    Console.WriteLine($"    TRANSLATING into '{element.Key}': {element.Value}");
                }
            };

            recognizer.Recognized += (s, e) => {
                if (e.Result.Reason == ResultReason.TranslatedSpeech)
                {
                    var lidResult = e.Result.Properties.GetProperty(PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult);

                    Console.WriteLine($"RECOGNIZED in '{lidResult}': Text={e.Result.Text}");
                    foreach (var element in e.Result.Translations)
                    {
                        Console.WriteLine($"    TRANSLATED into '{element.Key}': {element.Value}");
                    }
                }
                else if (e.Result.Reason == ResultReason.RecognizedSpeech)
                {
                    Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                    Console.WriteLine($"    Speech not translated.");
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

                stopTranslation.TrySetResult(0);
            };

            recognizer.SpeechStartDetected += (s, e) => {
                Console.WriteLine("\nSpeech start detected event.");
            };

            recognizer.SpeechEndDetected += (s, e) => {
                Console.WriteLine("\nSpeech end detected event.");
            };

            recognizer.SessionStarted += (s, e) => {
                Console.WriteLine("\nSession started event.");
            };

            recognizer.SessionStopped += (s, e) => {
                Console.WriteLine("\nSession stopped event.");
                Console.WriteLine($"\nStop translation.");
                stopTranslation.TrySetResult(0);
            };

            // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
            Console.WriteLine("Start translation...");
            await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

            Task.WaitAny(new[] { stopTranslation.Task });
            await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
        }
    }
}
```

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename
