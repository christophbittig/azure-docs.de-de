---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: eur
ms.custom: devx-track-js
ms.openlocfilehash: 6606ff523330b314e3a0593557b159c4915cd33c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529655"
---
Die Funktion zum Erkennen und Transkribieren von menschlicher Sprache (Spracherkennung) ist eines der zentralen Features des Speech-Diensts. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Spracherkennungen durchzuführen.

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [JavaScript-Schnellstartbeispiele](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) zu, falls Sie direkt zum Beispielcode springen möchten.

Sehen Sie sich alternativ das [React-Beispiel](https://github.com/Azure-Samples/AzureSpeechReactSample) an, um zu erfahren, wie Sie das Speech SDK in einer browserbasierten Umgebung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das Speech SDK für Node.js installiert werden. Falls Sie lediglich den Paketnamen für die Installation benötigen, können Sie den folgenden Befehl ausführen: `npm install microsoft-cognitiveservices-speech-sdk`. Anweisungen zu einer geführten Installation finden Sie im Artikel zu den [ersten Schritten](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs).

Verwenden Sie die folgende `require`-Anweisung, um das SDK zu importieren.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Weitere Informationen zu `require` finden Sie in der [Dokumentation zu „require“](https://nodejs.org/en/knowledge/getting-started/what-is-require/).

## <a name="create-a-speech-configuration"></a>Erstellen einer Sprachkonfiguration

Um den Speech-Dienst über das Speech SDK aufrufen zu können, muss eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)) erstellt werden. Diese Klasse enthält Informationen zu Ihrem Abonnement. Hierzu zählen etwa Ihr Schlüssel und der zugeordnete Standort bzw. die zugeordnete Region, der Endpunkt, der Host oder das Autorisierungstoken. Erstellen Sie mithilfe des Schlüssels und dem Standort/der Region eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)). Ihr Schlüssel-Region/Standort-Paar finden Sie auf der Seite [Ermitteln von Schlüsseln und Region/Standort](../../../overview.md#find-keys-and-locationregion).

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

Es gibt noch einige andere Möglichkeiten, eine Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)) zu initialisieren:

* Mit einem Endpunkt: Übergeben Sie einen Endpunkt für den Speech-Dienst. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Host: Übergeben Sie eine Hostadresse. Ein Schlüssel oder Autorisierungstoken ist optional.
* Mit einem Autorisierungstoken: Übergeben Sie ein Autorisierungstoken und den zugeordneten Standort bzw. die zugeordnete Region.

> [!NOTE]
> Eine Konfiguration ist immer erforderlich. Dabei spielt es keine Rolle, ob Sie eine Spracherkennung, eine Sprachsynthese, eine Übersetzung oder eine Absichtserkennung durchführen möchten.

## <a name="recognize-from-microphone-browser-only"></a>Erkennen über das Mikrofon (nur Browser)

Das Erkennen von Spracheingaben per Mikrofon wird **in Node.js nicht unterstützt**. Es wird nur in einer browserbasierten JavaScript-Umgebung unterstützt. Das [React-Beispiel](https://github.com/Azure-Samples/AzureSpeechReactSample) auf GitHub enthält weitere Informationen zur [Implementierung der Spracherkennung über das Mikrofon](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29).

> [!NOTE]
> Wenn Sie ein *bestimmtes* Audioeingabegerät verwenden möchten, müssen Sie in `AudioConfig` die Geräte-ID angeben. Informationen zum Abrufen der Geräte-ID für Ihr Audioeingabegerät finden Sie [hier](../../../how-to-select-audio-input-devices.md).

## <a name="recognize-from-file"></a>Erkennen aus Datei 

Um die Sprache aus einer Audiodatei zu erkennen, erstellen Sie mithilfe von `fromWavFileInput()` ein `AudioConfig`-Objekt, das ein `Buffer`-Objekt akzeptiert. Initialisieren Sie dann [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer), und übergeben Sie `audioConfig` und `speechConfig`.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>Erkennen über InMemory-Datenstrom

Bei vielen Anwendungsfällen stammen Ihre Audiodaten wahrscheinlich aus Blobspeicher oder sind anderweitig bereits als [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) oder eine ähnliche Rohdatenstruktur im Arbeitsspeicher vorhanden. Der folgende Code

* Erstellt einen Pushstream mithilfe von `createPushStream()`.
* Liest eine Datei vom Typ `.wav` mithilfe von `fs.createReadStream` zu Demonstrationszwecken. Wenn `ArrayBuffer` jedoch bereits Audiodaten enthält, können Sie direkt mit dem Schreiben von Inhalten in den Eingabestream fortfahren.
* Erstellt eine Audiokonfiguration mithilfe des Pushstreams.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

Bei der Verwendung eines Pushstreams als Eingabe wird davon ausgegangen, dass es sich bei den Audiodaten um unformatiertes PCM handelt, d. h. Header werden übersprungen.
In bestimmten Fällen kann die API weiterhin verwendet werden, wenn der Header nicht übersprungen wurde. Um die besten Ergebnisse zu erzielen, empfiehlt es sich jedoch, eine Logik zum Lesen der Header zu implementieren, sodass `fs` am *Anfang der Audiodaten* beginnt.

## <a name="error-handling"></a>Fehlerbehandlung

In den vorherigen Beispielen wird lediglich der erkannte Text aus `result.text` abgerufen. Zur Behandlung von Fehlern und anderen Antworten müssen Sie jedoch Code schreiben, um das Ergebnis zu verarbeiten. Der folgende Code wertet die Eigenschaft [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) aus und geht wie folgt vor:

* Das Erkennungsergebnis wird ausgegeben: `ResultReason.RecognizedSpeech`
* Wurde kein Erkennungstreffer gefunden, wird der Benutzer davon in Kenntnis gesetzt: `ResultReason.NoMatch`
* Ist ein Fehler aufgetreten, wird die Fehlermeldung ausgegeben: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case sdk.ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case sdk.ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case sdk.ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == sdk.CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the key and location/region info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Kontinuierliche Erkennung

In den vorherigen Beispielen wird die anfängliche Erkennung verwendet, bei der eine einzelne Äußerung erkannt wird. Zur Erkennung des Endes einer einzelnen Äußerung wird auf Stille am Ende gelauscht oder gewartet, bis maximal 15 Sekunden an Audiodaten verarbeitet wurden.

Im Gegensatz dazu wird die kontinuierliche Erkennung verwendet, wenn Sie **steuern** möchten, wann die Erkennung beendet wird. Für die kontinuierliche Erkennung müssen die Ereignisse `Recognizing`, `Recognized` und `Canceled` abonniert werden, um die Erkennungsergebnisse zu erhalten. Zum Beenden der Erkennung muss [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync) aufgerufen werden. Im folgenden Beispiel wird eine kontinuierliche Erkennung für eine Audioeingabedatei durchgeführt.

Beginnen Sie mit dem Definieren der Eingabe und dem Initialisieren einer Spracherkennung ([`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer)):

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Abonnieren Sie anschließend die von [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) gesendeten Ereignisse:

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Signal für Ereignisse mit Zwischenergebnissen der Erkennung.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Signal für Ereignisse mit Endergebnissen der Erkennung. (Der Erkennungsversuch war also erfolgreich.)
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Signal für Ereignisse, die das Ende einer Erkennungssitzung (bzw. eines Erkennungsvorgangs) angeben.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Signal für Ereignisse mit Ergebnissen einer abgebrochenen Erkennung. (Der Erkennungsversuch wurde also aufgrund einer direkten Abbruchanforderungen oder infolge eines Transport- oder Protokollfehlers abgebrochen.)

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == sdk.ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == sdk.ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == sdk.CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the key and location/region info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Rufen Sie nach Abschluss der Einrichtung [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) auf, um die Erkennung zu starten.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.stopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktiermodus

Bei Verwendung der kontinuierlichen Erkennung können Sie die Diktatverarbeitung mithilfe der entsprechenden Funktion „Diktat aktivieren“ aktivieren. Dieser Modus bewirkt, dass die Sprachkonfigurationsinstanz Wortbeschreibungen von Satzstrukturen wie z. B. Interpunktion interpretiert. Beispielsweise würde die Äußerung „Leben Sie in der Stadt Fragezeichen“ als Text „Leben Sie in der Stadt?“ interpretiert.

Verwenden Sie zum Aktivieren des Diktiermodus die Methode [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) in Ihrer Sprachkonfiguration ([`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Ändern der Ausgangssprache

Eine gängige Aufgabe für die Spracherkennung ist die Angabe der Eingabesprache (Ausgangssprache). Im folgenden Beispiel wird gezeigt, wie Sie die Eingabesprache auf Italienisch festlegen. Navigieren Sie in Ihrem Code zu [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig), und fügen Sie direkt darunter die folgende Zeile hinzu:

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

Von der Eigenschaft [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) wird eine Zeichenfolge im Format „Sprache-Gebietsschema“ erwartet. Sie können einen beliebigen Wert aus der Spalte **Gebietsschema** der Liste mit den unterstützten [Gebietsschemas/Sprachen](../../../language-support.md) angeben.

## <a name="improve-recognition-accuracy"></a>Verbessern der Erkennungsgenauigkeit

Begriffslisten dienen zur Identifizierung bekannter Begriffe in Audiodaten, wie beispielsweise den Namen einer Person oder einen bestimmten Ort. Durch die Bereitstellung einer Begriffsliste können Sie die Genauigkeit der Spracherkennung verbessern.

Wenn Sie beispielsweise den Befehl „Move to“ und „Ward“ als mögliches Ziel haben, das gesprochen werden kann, können Sie den Eintrag „Move to Ward“ hinzufügen. Das Hinzufügen eines Begriffs erhöht die Wahrscheinlichkeit, dass bei der Audioerkennung „Move to Ward“ anstelle von „Move toward“ erkannt wird.

Einer Begriffsliste können einzelne Wörter oder ganze Phrasen hinzugefügt werden. Bei der Erkennung wird ein Eintrag in einer Begriffsliste verwendet, um die Erkennung der Wörter und Begriffe in der Liste zu optimieren, auch wenn sich die Einträge in der Mitte der Äußerung befinden. 

> [!IMPORTANT]
> Das Begriffslistenfeature steht für folgende Sprachen zur Verfügung: de-DE, en-US, en-AU, en-CA, en-GB, en-IN, es-ES, fr-FR, it-IT, ja-JP, pt-BR, zh-CN
>
> Das Begriffslistenfeature sollte maximal mit ein paar hundert Begriffen verwendet werden. Wenn Sie über eine größere Liste verfügen oder Sprachen benötigen, die derzeit nicht unterstützt werden, ist das [Trainieren eines benutzerdefinierten Modells](../../../custom-speech-overview.md) wahrscheinlich die bessere Wahl, um die Genauigkeit zu verbessern.
>
> Das Begriffslistenfeature wird bei benutzerdefinierten Endpunkten nicht unterstützt. Verwenden Sie es nicht mit benutzerdefinierten Endpunkten. Trainieren Sie stattdessen ein benutzerdefiniertes Modell, das die Begriffe enthält.

Wenn Sie eine Begriffsliste verwenden möchten, erstellen Sie zunächst ein Objekt vom Typ [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar), und fügen Sie anschließend mithilfe von [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) bestimmte Wörter und Begriffe hinzu.

Änderungen an [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) werden bei der nächsten Erkennung oder nach erneuter Verbindungsherstellung mit dem Speech-Dienst wirksam.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Wenn Sie Ihre Begriffsliste löschen möchten, verwenden Sie Folgendes:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Weitere Optionen zum Verbessern der Erkennungsgenauigkeit

Begriffslisten sind nur eine von mehreren Optionen zur Verbesserung der Erkennungsgenauigkeit. Sie können außerdem: 

* [Verbessern der Genauigkeit mithilfe von Custom Speech](../../../custom-speech-overview.md)
* [Verbessern der Genauigkeit mithilfe von Mandantenmodellen](../../../tutorial-tenant-model.md)
