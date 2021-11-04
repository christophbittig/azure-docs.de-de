---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: eur
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ed099e73e142974eee87847a1318ee515687edb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510810"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [C#-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Um die Beispiele in diesem Artikel auszuführen, fügen Sie die folgenden `using`-Anweisungen oben in Ihr Skript ein.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel für die Spracheingabe und der zugeordnete Standort bzw. die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel und den zugeordneten Standort bzw. die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und den zugeordneten Standort bzw. die zugeordnete Region.

In diesem Beispiel erstellen Sie ein [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)-Objekt mit einem Schlüssel für die Spracheingabe und einem Standort bzw. einer Region. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen. Sie können für den restlichen Teil dieses Artikels auch einfache Codebausteine erstellen, an denen Sie dann jeweils die entsprechenden Anpassungen vornehmen.

```csharp
public class Program
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync()
    {
        var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    }
}
```

## <a name="select-synthesis-language-and-voice"></a>Auswählen von Synthesesprache und Stimme

Der Sprachsynthesedienst von Azure unterstützt mehr als 250 Stimmen und über 70 Sprachen und Varianten.
Sie können die [vollständige Liste](../../../language-support.md#neural-voices) abrufen oder die [Demo für die Sprachsynthese](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features) ausprobieren.
Geben Sie die Sprache oder Stimme von [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) entsprechend Ihres Eingabetexts an, und verwenden Sie die gewünschte Stimme.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    // Note: if only language is set, the default voice of that language is chosen.
    config.SpeechSynthesisLanguage = "<your-synthesis-language>"; // e.g. "de-DE"
    // The voice setting will overwrite language setting.
    // The voice setting will not overwrite the voice element in input SSML.
    config.SpeechSynthesisVoiceName = "<your-wanted-voice>";
}
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Als Nächstes erstellen Sie das Objekt [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer), mit dem Konvertierungen von Text in Sprache und Ausgaben an Lautsprecher, in Dateien oder andere Ausgabestreams erfolgen. Für [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer) werden folgende Parameter akzeptiert: das Objekt [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) aus dem vorherigen Schritt und das Objekt [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig), mit dem angegeben wird, wie Ausgabeergebnisse verarbeitet werden sollten.

Erstellen Sie zunächst ein `AudioConfig`-Element, um die Ausgabe mit der Funktion `FromWavFileOutput()` automatisch in eine `.wav`-Datei zu schreiben, und instanziieren Sie diese mit einer `using`-Anweisung. Eine `using`-Anweisung in diesem Kontext gibt automatisch nicht verwaltete Ressourcen frei und führt dazu, dass das Objekt nach der Freigabe nicht mehr zum Geltungsbereich gehört.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Instanziieren Sie als Nächstes mit einer weiteren `using`-Anweisung ein `SpeechSynthesizer`-Element. Übergeben Sie die Objekte `config` und `audioConfig`als Parameter. Das Ausführen der Sprachsynthese und Schreiben der Daten in eine Datei ist so einfach wie das Ausführen von `SpeakTextAsync()` mit einer Textzeichenfolge.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Führen Sie das Programm aus. Eine synthetisierte Datei vom Typ `.wav` wird an den von Ihnen angegebenen Speicherort geschrieben. Dies ist ein gutes Beispiel für die einfachste Nutzung. Als Nächstes informieren Sie sich über die Anpassung der Ausgabe und die Verarbeitung der Ausgabeantwort als InMemory-Datenstrom für benutzerdefinierte Szenarien.

## <a name="synthesize-to-speaker-output"></a>Synthetisieren der Lautsprecherausgabe

In bestimmten Fällen kann es ratsam sein, synthetisierte Sprache direkt an einen Lautsprecher auszugeben. Lassen Sie dazu einfach den Parameter `AudioConfig` bei der Erstellung des `SpeechSynthesizer`-Elements im obigen Beispiel weg. Die Ausgabe erfolgt auf dem derzeit aktiven Ausgabegerät.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Abrufen des Ergebnisses als InMemory-Datenstrom

Bei vielen Entwicklungsszenarien für Sprachanwendungen benötigen Sie die sich ergebenden Audiodaten als InMemory-Datenstrom, anstatt die Daten direkt in eine Datei zu schreiben. Auf diese Weise können Sie ein benutzerdefiniertes Verhalten erzielen, z. B.:

* Abstrahieren des sich ergebenden Bytearrays als durchsuchbaren Datenstrom für benutzerdefinierte nachgelagerte Dienste
* Integrieren des Ergebnisses in andere APIs oder Dienste
* Ändern der Audiodaten, Schreiben benutzerdefinierter `.wav`-Header usw.

Diese Änderung lässt sich einfach am vorherigen Beispiel vornehmen. Entfernen Sie zunächst den Block `AudioConfig`, weil Sie das Ausgabeverhalten ab jetzt manuell verwalten, um eine bessere Steuerung zu erzielen. Übergeben Sie anschließend `null` für das Objekt `AudioConfig` im Konstruktor `SpeechSynthesizer`.

> [!NOTE]
> Wenn `null` für `AudioConfig` übergeben wird, anstatt dieses Element wie im obigen Beispiel für die Lautsprecherausgabe wegzulassen, werden die Audiodaten auf dem derzeit aktiven Ausgabegerät nicht standardmäßig wiedergegeben.

Dieses Mal speichern Sie das Ergebnis in einer Variablen des Typs [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult). Die `AudioData`-Eigenschaft gibt ein `byte []`-Element der Ausgabedaten zurück. Sie können dieses `byte []`-Element manuell nutzen oder die [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream)-Klasse verwenden, um den speicherinternen Stream zu verwalten. In diesem Beispiel verwenden Sie die statische Funktion `AudioDataStream.FromResult()`, um einen Datenstrom aus dem Ergebnis abzurufen.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    using var synthesizer = new SpeechSynthesizer(config, null);

    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Ab hier können Sie mit dem sich ergebenden `stream`-Objekt ein beliebiges benutzerdefiniertes Verhalten implementieren.

## <a name="customize-audio-format"></a>Anpassen des Audioformats

Im folgenden Abschnitt wird veranschaulicht, wie Sie die Attribute der Audioausgabe anpassen, z. B.:

* Audiodateityp
* Abtastrate
* Bittiefe

Zum Ändern des Audioformats wenden Sie die Funktion `SetSpeechSynthesisOutputFormat()` auf das Objekt `SpeechConfig` an. Diese Funktion erwartet für `enum` den Typ [`SpeechSynthesisOutputFormat`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat), den Sie zum Auswählen des Ausgabeformats verwenden. In den Referenzdokumenten finden Sie eine [Liste mit den verfügbaren Audioformaten](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat).

Sie enthält verschiedene Optionen für unterschiedliche Anforderungen in Bezug auf den Dateityp. Beachten Sie, dass Rohformate wie `Raw24Khz16BitMonoPcm` gemäß Definition keine Audioheader enthalten. Nutzen Sie Rohformate nur, wenn Sie wissen, dass Ihre nachgelagerte Implementierung einen unformatierten Bitstream decodieren kann, oder wenn Sie planen, Header basierend auf Bittiefe, Abtastrate, Kanalanzahl usw. manuell zu erstellen.

In diesem Beispiel geben Sie das High-Fidelity-RIFF-Format `Riff24Khz16BitMonoPcm` an, indem Sie `SpeechSynthesisOutputFormat` für das Objekt `SpeechConfig` festlegen. Ähnlich wie im Beispiel im vorherigen Abschnitt nutzen Sie [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream), um einen InMemory-Datenstrom des Ergebnisses zu erhalten, das Sie anschließend in eine Datei schreiben.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Beim erneuten Ausführen Ihres Programms wird eine Datei vom Typ `.wav` in den angegebenen Pfad geschrieben.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Verwenden von SSML zum Anpassen von Sprachmerkmalen

Mit der Speech Synthesis Markup Language (SSML, Markupsprache für Sprachsynthese) können Sie Tonhöhe, Aussprache, Sprechgeschwindigkeit, Lautstärke und weitere Aspekte der Ausgabe der Sprachsynthese optimieren, indem Sie Ihre Anforderungen per XML-Schema übermitteln. Dieser Abschnitt enthält ein Beispiel für das Ändern der Stimme. Eine ausführlichere Anleitung finden Sie jedoch im [Anleitungsartikel zu SSML](../../../speech-synthesis-markup.md).

Wenn Sie SSML für die Anpassung nutzen möchten, nehmen Sie eine einfache Änderung vor, um die Stimme zu wechseln.
Erstellen Sie zuerst in Ihrem Stammverzeichnis des Projekts eine neue XML-Datei für die SSML-Konfiguration. In diesem Beispiel ist dies `ssml.xml`. Das Stammelement ist immer `<speak>`. Indem Sie den Text mit einem `<voice>`-Element umschließen, können Sie die Stimme mit dem Parameter `name` ändern. Sehen Sie sich die [gesamte Liste](../../../language-support.md#neural-voices) mit den unterstützten **neuronalen Stimmen** an.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-ChristopherNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird. Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `SpeakSsmlAsync()` anstelle der `SpeakTextAsync()`-Funktion. Diese Funktion erwartet eine XML-Zeichenfolge, weshalb Sie Ihre SSML-Konfiguration zunächst mithilfe von `File.ReadAllText()` als Zeichenfolge laden. Ab hier ist das Ergebnisobjekt genau identisch mit vorhergehenden Beispielen.

> [!NOTE]
> Wenn Sie Visual Studio verwenden, findet Ihre Buildkonfiguration Ihre XML-Datei wahrscheinlich nicht standardmäßig. Um dies zu erreichen, klicken Sie mit der rechten Maustaste auf die XML-Datei und wählen **Eigenschaften** aus. Ändern Sie **Buildaktion** in *Inhalt* und **In Ausgabeverzeichnis kopieren** in *Immer kopieren*.

```csharp
public static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    using var synthesizer = new SpeechSynthesizer(config, null);

    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

> [!NOTE]
> Wenn Sie die Stimme ohne SSML ändern möchten, können Sie die Eigenschaft für `SpeechConfig` mithilfe von `SpeechConfig.SpeechSynthesisVoiceName = "en-US-ChristopherNeural";` festlegen.

## <a name="get-facial-pose-events"></a>Abrufen von Gesichtsausdrucksereignissen

Die Sprache kann eine gute Möglichkeit zum Steuern der Animation von Gesichtsausdrücken sein.
Häufig werden die Schlüssel in der beobachteten Sprache mithilfe von [visemes](../../../how-to-speech-synthesis-viseme.md) dargestellt, wie z. b. die Position der Lippen, der Kiefer und die Zunge, wenn ein bestimmtes Phoneme erzeugt wird.
Sie können das Ereignis „viseme“ in der Sprach-SDK abonnieren.
Anschließend können Sie das Gesicht eines Zeichens bei der Wiedergabe von sprach Audioereignissen auf das Zeichen eines Zeichens animieren.
Erfahren Sie [, wie Sie viseme-Ereignisse bekommen](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)
