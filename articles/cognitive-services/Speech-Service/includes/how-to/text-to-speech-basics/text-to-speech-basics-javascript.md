---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: eur
ms.custom: devx-track-js
ms.openlocfilehash: a01d68c36a2e3a7d9ad3a8755d92fbb5e403fb08
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510836"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt. Hierzu werden zunächst eine grundlegende Konfiguration und eine einfache Synthese durchgeführt, gefolgt von komplexeren Beispielen für die Entwicklung benutzerdefinierter Anwendungen:

* Erhalten von Antworten als In-Memory-Datenstrom
* Anpassen der Abtast- und Bitrate der Ausgabe
* Übermitteln von Syntheseanforderungen mithilfe von SSML (Speech Synthesis Markup Language, Markupsprache für Sprachsynthese)
* Verwenden neuronaler Stimmen

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [JavaScript-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über ein Azure-Konto und eine Speech-Dienstressource verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst müssen Sie das <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK für JavaScript </a> installieren. Verwenden Sie dazu die folgenden plattformspezifischen Anleitungen:
- [Node.js](../../../speech-sdk.md?tabs=nodejs#get-the-speech-sdk)
- [Webbrowser](../../../speech-sdk.md?tabs=browser#get-the-speech-sdk)

Verwenden Sie außerdem abhängig von der Zielumgebung eine der folgenden Funktionen:

# <a name="script"></a>[script](#tab/script)

Laden Sie die Datei *microsoft.cognitiveservices.speech.sdk.bundle.js* des <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK für JavaScript</a> herunter, und extrahieren Sie sie. Speichern Sie sie in einem Ordner, auf den von der HTML-Datei zugegriffen werden kann.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Verwenden Sie für einen Webbrowser das Tag `<script>`. Das Präfix `sdk` ist nicht erforderlich. Das Präfix `sdk` ist ein Alias, mit dem das Modul `require` benannt wird.

# <a name="import"></a>[import](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Weitere Informationen zu `import` finden Sie unter <a href="https://javascript.info/import-export" target="_blank">Exportieren und Importieren </a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Weitere Informationen zu `require` finden Sie unter <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Was ist „require“? </a>.

---


## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrer Ressource. Hierzu zählen etwa Ihr Schlüssel für die Spracheingabe und der zugeordnete Standort bzw. die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

Eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)) kann auf unterschiedliche Weise initialisiert werden:

* Mit einer Ressource: Übergeben Sie einen Schlüssel für die Spracheingabe und den zugeordneten Standort bzw. die zugeordnete Region.
* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel für die Spracheingabe und ein Autorisierungstoken sind optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel für die Spracheingabe und ein Autorisierungstoken sind optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und den zugeordneten Standort bzw. die zugeordnete Region.

In diesem Beispiel erstellen Sie ein [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)-Objekt mit einem Schlüssel für die Spracheingabe und einem Standort bzw. einer Region. Diese Anmeldeinformationen können Sie mithilfe der Schritte unter [Kostenloses Testen des Speech-Diensts](../../../overview.md#try-the-speech-service-for-free) abrufen. Sie können für den restlichen Teil dieses Artikels auch einfache Codebausteine erstellen, an denen Sie dann jeweils die entsprechenden Anpassungen vornehmen.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
}

synthesizeSpeech();
```

## <a name="select-synthesis-language-and-voice"></a>Auswählen von Synthesesprache und Stimme

Der Sprachsynthesedienst von Azure unterstützt mehr als 250 Stimmen und über 70 Sprachen und Varianten.
Sie können die [vollständige Liste](../../../language-support.md#neural-voices) abrufen oder die [Demo für die Sprachsynthese](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features) ausprobieren.
Geben Sie die Sprache oder Stimme von [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) entsprechend Ihres Eingabetexts an, und verwenden Sie die gewünschte Stimme.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    // Note: if only language is set, the default voice of that language is chosen.
    speechConfig.speechSynthesisLanguage = "<your-synthesis-language>"; // e.g. "de-DE"
    // The voice setting will overwrite language setting.
    // The voice setting will not overwrite the voice element in input SSML.
    speechConfig.speechSynthesisVoiceName = "<your-wanted-voice>";
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Als Nächstes erstellen Sie das Objekt [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer), mit dem Konvertierungen von Text in Sprache und Ausgaben an Lautsprecher, in Dateien oder andere Ausgabestreams erfolgen. Für [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) werden folgende Parameter akzeptiert: das Objekt [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) und das Objekt [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) aus dem vorherigen Schritt, mit dem angegeben wird, wie Ausgabeergebnisse verarbeitet werden sollten.

Erstellen Sie zum Beginnen ein `AudioConfig`-Objekt, um die Ausgabe mit der statischen Funktion `fromAudioFileOutput()` in eine `.wav`-Datei zu schreiben.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const audioConfig = sdk.AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Instanziieren Sie als Nächstes ein `SpeechSynthesizer`-Objekt, und übergeben Sie Ihr `speechConfig`-Objekt und das `audioConfig`-Objekt als Parameter. Das Ausführen der Sprachsynthese und Schreiben der Daten in eine Datei ist so einfach wie das Ausführen von `speakTextAsync()` mit einer Textzeichenfolge. Der Ergebnisrückruf eignet sich gut zum Aufrufen von `synthesizer.close()` und ist sogar erforderlich, damit die Synthese ordnungsgemäß funktioniert.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const audioConfig = sdk.AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Führen Sie das Programm aus. Eine synthetisierte Datei vom Typ `.wav` wird an den von Ihnen angegebenen Speicherort geschrieben. Dies ist ein gutes Beispiel für die einfachste Nutzung. Als Nächstes informieren Sie sich über die Anpassung der Ausgabe und die Verarbeitung der Ausgabeantwort als InMemory-Datenstrom für benutzerdefinierte Szenarien.

## <a name="synthesize-to-speaker-output-browser-only"></a>Synthetisieren der Lautsprecherausgabe (nur Browser)

In bestimmten Fällen kann es ratsam sein, synthetisierte Sprache direkt über einen Lautsprecher auszugeben. Instanziieren Sie zu diesem Zweck `AudioConfig`, indem Sie die statische Funktion `fromDefaultSpeakerOutput()` verwenden. Die Ausgabe erfolgt auf dem derzeit aktiven Ausgabegerät.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const audioConfig = sdk.AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Abrufen des Ergebnisses als InMemory-Datenstrom

Bei vielen Entwicklungsszenarien für Sprachanwendungen benötigen Sie die sich ergebenden Audiodaten als InMemory-Datenstrom, anstatt die Daten direkt in eine Datei zu schreiben. Auf diese Weise können Sie ein benutzerdefiniertes Verhalten erzielen, z. B.:

* Abstrahieren des sich ergebenden Bytearrays als durchsuchbaren Datenstrom für benutzerdefinierte nachgelagerte Dienste
* Integrieren des Ergebnisses in andere APIs oder Dienste
* Ändern der Audiodaten, Schreiben benutzerdefinierter `.wav`-Header usw.

Diese Änderung lässt sich einfach am vorherigen Beispiel vornehmen. Entfernen Sie zunächst den Block `AudioConfig`, weil Sie das Ausgabeverhalten ab jetzt manuell verwalten, um eine bessere Steuerung zu erzielen. Übergeben Sie anschließend `undefined` für das Objekt `AudioConfig` im Konstruktor `SpeechSynthesizer`.

> [!NOTE]
> Wenn `undefined` für `AudioConfig` übergeben wird, anstatt dieses Element wie im obigen Beispiel für die Lautsprecherausgabe wegzulassen, werden die Audiodaten auf dem derzeit aktiven Ausgabegerät nicht standardmäßig wiedergegeben.

Dieses Mal speichern Sie das Ergebnis in einer Variablen des Typs [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult). Die Eigenschaft `SpeechSynthesisResult.audioData` gibt ein `ArrayBuffer`-Objekt mit den Ausgabedaten zurück, den Standard-Browserstreamtyp. Konvertieren Sie das ArrayBuffer-Objekt für Servercode in einen Pufferstream.

Der folgende Code funktioniert für clientseitigen Code.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Ab hier können Sie mit dem sich ergebenden `ArrayBuffer`-Objekt ein beliebiges benutzerdefiniertes Verhalten implementieren. ArrayBuffer ist ein gängiger Typ, der in einem Browser empfangen und in diesem Format wiedergegeben wird.

Wenn Sie in serverbasiertem Code mit den Daten als Stream statt als ArrayBuffer-Objekt arbeiten müssen, müssen Sie das Objekt in einen Stream konvertieren.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>Anpassen des Audioformats

Im folgenden Abschnitt wird veranschaulicht, wie Sie die Attribute der Audioausgabe anpassen, z. B.:

* Audiodateityp
* Abtastrate
* Bittiefe

Zum Ändern des Audioformats wenden Sie die Eigenschaft `speechSynthesisOutputFormat` auf das Objekt `SpeechConfig` an. Diese Eigenschaft erwartet für `enum` den Typ [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat), den Sie zum Auswählen des Ausgabeformats verwenden. In den Referenzdokumenten finden Sie eine [Liste mit den verfügbaren Audioformaten](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat).

Sie enthält verschiedene Optionen für unterschiedliche Anforderungen in Bezug auf den Dateityp. Beachten Sie, dass Rohformate wie `Raw24Khz16BitMonoPcm` gemäß Definition keine Audioheader enthalten. Nutzen Sie Rohformate nur, wenn Sie wissen, dass Ihre nachgelagerte Implementierung einen unformatierten Bitstream decodieren kann, oder wenn Sie planen, Header basierend auf Bittiefe, Abtastrate, Kanalanzahl usw. manuell zu erstellen.

In diesem Beispiel geben Sie das High-Fidelity-RIFF-Format `Riff24Khz16BitMonoPcm` an, indem Sie `speechSynthesisOutputFormat` für das Objekt `SpeechConfig` festlegen. Rufen Sie die `ArrayBuffer`-Audiodaten ähnlich wie im Beispiel im vorherigen Abschnitt ab, und interagieren Sie damit.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird. Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `speakSsmlAsync()` anstelle von `speakTextAsync()`. Von dieser Funktion wird eine XML-Zeichenfolge erwartet. Sie erstellen also zuerst eine Funktion, um eine XML-Datei zu laden und als Zeichenfolge zurückzugeben.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Weitere Informationen zu `readFileSync` finden Sie unter <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js-Dateisystem</a>. Ab hier ist das Ergebnisobjekt genau identisch mit vorhergehenden Beispielen.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

> [!NOTE]
> Wenn Sie die Stimme ohne SSML ändern möchten, können Sie die Eigenschaft für `SpeechConfig` mithilfe von `SpeechConfig.speechSynthesisVoiceName = "en-US-ChristopherNeural";` festlegen.

## <a name="get-facial-pose-events"></a>Abrufen von Gesichtsausdrucksereignissen

Die Sprache kann eine gute Möglichkeit zum Steuern der Animation von Gesichtsausdrücken sein.
Häufig werden die Schlüssel in der beobachteten Sprache mithilfe von [visemes](../../../how-to-speech-synthesis-viseme.md) dargestellt, wie z. b. die Position der Lippen, der Kiefer und die Zunge, wenn ein bestimmtes Phoneme erzeugt wird.
Sie können das Ereignis „viseme“ in der Sprach-SDK abonnieren.
Anschließend können Sie das Gesicht eines Zeichens bei der Wiedergabe von sprach Audioereignissen auf das Zeichen eines Zeichens animieren.
Erfahren Sie [, wie Sie viseme-Ereignisse bekommen](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)
