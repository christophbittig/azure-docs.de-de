---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.custom: devx-track-java
ms.author: eur
ms.openlocfilehash: 29518e68e3de51ae37812ba590b36ab37dc9b54c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510929"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Java-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/text-to-speech) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK installiert werden. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java Runtime </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Android </a>

## <a name="import-dependencies"></a>Importieren von Abhängigkeiten

Fügen Sie zum Ausführen der Beispiele in diesem Artikel oben in Ihrem Skript die folgenden Importanweisungen ein.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel für die Spracheingabe und der zugeordnete Standort bzw. die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einem Abonnement: Übergeben Sie einen Schlüssel für die Spracheingabe und den zugeordneten Standort bzw. die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel für die Spracheingabe und ein Autorisierungstoken sind optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel für die Spracheingabe und ein Autorisierungstoken sind optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und den zugeordneten Standort bzw. die zugeordnete Region.

In diesem Beispiel erstellen Sie ein [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig)-Objekt mit einem Schlüssel für die Spracheingabe und einem Standort bzw. einer Region. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen. Sie können für den restlichen Teil dieses Artikels auch einfache Codebausteine erstellen, an denen Sie dann jeweils die entsprechenden Anpassungen vornehmen.

```java
public class Program
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    }
}
```

## <a name="select-synthesis-language-and-voice"></a>Auswählen von Synthesesprache und Stimme

Der Sprachsynthesedienst von Azure unterstützt mehr als 250 Stimmen und über 70 Sprachen und Varianten.
Sie können die [vollständige Liste](../../../language-support.md#neural-voices) abrufen oder die [Demo für die Sprachsynthese](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features) ausprobieren.
Geben Sie die Sprache oder Stimme von [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) entsprechend Ihres Eingabetexts an, und verwenden Sie die gewünschte Stimme.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    // Note: if only language is set, the default voice of that language is chosen.
    config.setSpeechSynthesisLanguage("<your-synthesis-language>"); // e.g. "de-DE"
    // The voice setting will overwrite language setting.
    // The voice setting will not overwrite the voice element in input SSML.
    config.setSpeechSynthesisVoiceName("<your-wanted-voice>");
}
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Als Nächstes erstellen Sie das Objekt [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer), mit dem Konvertierungen von Text in Sprache und Ausgaben an Lautsprecher, in Dateien oder andere Ausgabestreams erfolgen. Für [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) werden folgende Parameter akzeptiert: das Objekt [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) und das Objekt [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) aus dem vorherigen Schritt, mit dem angegeben wird, wie Ausgabeergebnisse verarbeitet werden sollten.

Erstellen Sie zum Beginnen ein `AudioConfig`-Objekt, um die Ausgabe mit der statischen Funktion `fromWavFileOutput()` in eine `.wav`-Datei zu schreiben.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Instanziieren Sie als Nächstes ein `SpeechSynthesizer`-Objekt, und übergeben Sie Ihr `speechConfig`-Objekt und das `audioConfig`-Objekt als Parameter. Das Ausführen der Sprachsynthese und Schreiben der Daten in eine Datei ist so einfach wie das Ausführen von `SpeakText()` mit einer Textzeichenfolge.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Führen Sie das Programm aus. Eine synthetisierte Datei vom Typ `.wav` wird an den von Ihnen angegebenen Speicherort geschrieben. Dies ist ein gutes Beispiel für die einfachste Nutzung. Als Nächstes informieren Sie sich über die Anpassung der Ausgabe und die Verarbeitung der Ausgabeantwort als InMemory-Datenstrom für benutzerdefinierte Szenarien.

## <a name="synthesize-to-speaker-output"></a>Synthetisieren der Lautsprecherausgabe

In bestimmten Fällen kann es ratsam sein, synthetisierte Sprache direkt über einen Lautsprecher auszugeben. Instanziieren Sie zu diesem Zweck `AudioConfig`, indem Sie die statische Funktion `fromDefaultSpeakerOutput()` verwenden. Die Ausgabe erfolgt auf dem derzeit aktiven Ausgabegerät.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
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

Hierbei speichern Sie das Ergebnis nun in einer Variablen vom Typ [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult). Die Funktion `SpeechSynthesisResult.getAudioData()` gibt ein `byte []`-Element der Ausgabedaten zurück. Sie können dieses `byte []`-Element manuell nutzen oder die [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream)-Klasse verwenden, um den speicherinternen Stream zu verwalten. In diesem Beispiel verwenden Sie die statische Funktion `AudioDataStream.fromResult()`, um einen Datenstrom aus dem Ergebnis abzurufen.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Ab hier können Sie mit dem sich ergebenden `stream`-Objekt ein beliebiges benutzerdefiniertes Verhalten implementieren.

## <a name="customize-audio-format"></a>Anpassen des Audioformats

Im folgenden Abschnitt wird veranschaulicht, wie Sie die Attribute der Audioausgabe anpassen, z. B.:

* Audiodateityp
* Abtastrate
* Bittiefe

Zum Ändern des Audioformats wenden Sie die Funktion `setSpeechSynthesisOutputFormat()` auf das Objekt `SpeechConfig` an. Diese Funktion erwartet für `enum` den Typ [`SpeechSynthesisOutputFormat`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat), den Sie zum Auswählen des Ausgabeformats verwenden. In den Referenzdokumenten finden Sie eine [Liste mit den verfügbaren Audioformaten](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat).

Sie enthält verschiedene Optionen für unterschiedliche Anforderungen in Bezug auf den Dateityp. Beachten Sie, dass Rohformate wie `Raw24Khz16BitMonoPcm` gemäß Definition keine Audioheader enthalten. Nutzen Sie Rohformate nur, wenn Sie wissen, dass Ihre nachgelagerte Implementierung einen unformatierten Bitstream decodieren kann, oder wenn Sie planen, Header basierend auf Bittiefe, Abtastrate, Kanalanzahl usw. manuell zu erstellen.

In diesem Beispiel geben Sie das High-Fidelity-RIFF-Format `Riff24Khz16BitMonoPcm` an, indem Sie `SpeechSynthesisOutputFormat` für das Objekt `SpeechConfig` festlegen. Ähnlich wie im Beispiel im vorherigen Abschnitt nutzen Sie [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream), um einen InMemory-Datenstrom des Ergebnisses zu erhalten, das Sie anschließend in eine Datei schreiben.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird. Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `SpeakSsml()` anstelle von `SpeakText()`. Von dieser Funktion wird eine XML-Zeichenfolge erwartet. Sie erstellen also zuerst eine Funktion, um eine XML-Datei zu laden und als Zeichenfolge zurückzugeben.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Ab diesem Punkt stimmt das sich ergebende Objekt genau mit den Objekten in den vorherigen Beispielen überein.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

> [!NOTE]
> Wenn Sie die Stimme ohne SSML ändern möchten, können Sie die Eigenschaft für `SpeechConfig` mithilfe von `SpeechConfig.setSpeechSynthesisVoiceName("en-US-ChristopherNeural");` festlegen.

## <a name="get-facial-pose-events"></a>Abrufen von Gesichtsausdrucksereignissen

Die Sprache kann eine gute Möglichkeit zum Steuern der Animation von Gesichtsausdrücken sein.
Häufig werden die Schlüssel in der beobachteten Sprache mithilfe von [visemes](../../../how-to-speech-synthesis-viseme.md) dargestellt, wie z. b. die Position der Lippen, der Kiefer und die Zunge, wenn ein bestimmtes Phoneme erzeugt wird.
Sie können das Sprach-SDK für das "mundbilds"-Ereignis abonnieren, um Daten der Gesichtsanimation zu erhalten, und dann die Daten während der Gesichtsanimation auf ein Zeichen anwenden.
Erfahren Sie [, wie Sie viseme-Ereignisse bekommen](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)
