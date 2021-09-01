---
author: yulin-li
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: yulili
ms.openlocfilehash: 7c64db9a503caff0bb2cda3e7dc576bb3e72884d
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423264"
---
In dieser Schnellstartanleitung werden gängige Entwurfsmuster für die Sprachsynthese per Speech SDK vermittelt.

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Go-Schnellstartbeispiele](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/synthesizer) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK


Zuallererst muss das [Speech SDK für Go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser) installiert werden.

## <a name="text-to-speech-to-speaker"></a>Text-zu-Sprache über Lautsprecher

Verwenden Sie das folgende Codebeispiel, um die Sprachsynthese über Ihr standardmäßiges Audioausgabegerät durchzuführen.
Ersetzen Sie die Variablen `subscription` und `region` durch Ihren Schlüssel für die Spracheingabe und durch Ihren Standort/Ihre Region.
Wenn Sie das Skript ausführen, wird Ihr Eingabetext über den Standardlautsprecher gesprochen.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/common"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func synthesizeStartedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Synthesis started.")
}

func synthesizingHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesizing, audio chunk size %d.\n", len(event.Result.AudioData))
}

func synthesizedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesized, audio length %d.\n", len(event.Result.AudioData))
}

func cancelledHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation.")
}

func main() {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    audioConfig, err := audio.NewAudioConfigFromDefaultSpeakerOutput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        task := speechSynthesizer.SpeakTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        if outcome.Result.Reason == common.SynthesizingAudioCompleted {
            fmt.Printf("Speech synthesized to speaker for text [%s].\n", text)
        } else {
            cancellation, _ := speech.NewCancellationDetailsFromSpeechSynthesisResult(outcome.Result)
            fmt.Printf("CANCELED: Reason=%d.\n", cancellation.Reason)

            if cancellation.Reason == common.Error {
                fmt.Printf("CANCELED: ErrorCode=%d\nCANCELED: ErrorDetails=[%s]\nCANCELED: Did you update the subscription info?\n",
                    cancellation.ErrorCode,
                    cancellation.ErrorDetails)
            }
        }
    }
}
```

Führen Sie die folgenden Befehle aus, um eine Datei namens `go.mod` zu erstellen, die mit auf GitHub gehosteten Komponenten verknüpft ist.

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Erstellen Sie als Nächstes den Code, und führen Sie ihn aus.

```shell
go build
go run quickstart
```

Ausführliche Informationen zu den Klassen [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) und [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer) finden Sie in der Referenzdokumentation.

## <a name="text-to-speech-to-in-memory-stream"></a>Text-zu-Sprache über In-Memory-Datenstrom

Bei vielen Entwicklungsszenarien für Sprachanwendungen benötigen Sie die sich ergebenden Audiodaten als InMemory-Datenstrom, anstatt die Daten direkt in eine Datei zu schreiben.
Auf diese Weise können Sie ein benutzerdefiniertes Verhalten erzielen, z. B.:

* Abstrahieren des sich ergebenden Bytearrays als durchsuchbaren Datenstrom für benutzerdefinierte nachgelagerte Dienste
* Integrieren des Ergebnisses in andere APIs oder Dienste
* Ändern der Audiodaten, Schreiben benutzerdefinierter `.wav`-Header usw.

Diese Änderung lässt sich einfach am vorherigen Beispiel vornehmen. Entfernen Sie zunächst das `AudioConfig`-Objekt, weil Sie das Ausgabeverhalten ab jetzt manuell verwalten, um eine bessere Steuerung zu erzielen. Übergeben Sie anschließend `nil` für das Objekt `AudioConfig` im Konstruktor `SpeechSynthesizer`.

> [!NOTE]
> Wenn `nil` für `AudioConfig` übergeben wird, anstatt dieses Element wie im obigen Beispiel für die Lautsprecherausgabe wegzulassen, werden die Audiodaten auf dem derzeit aktiven Ausgabegerät nicht standardmäßig wiedergegeben.

Hierbei speichern Sie das Ergebnis nun in einer Variablen vom Typ [`SpeechSynthesisResult`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesisResult).
Die Eigenschaft `AudioData` gibt ein `[]byte`-Element der Ausgabedaten zurück. Sie können dieses `[]byte`-Element manuell nutzen oder die [`AudioDataStream`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#AudioDataStream)-Klasse verwenden, um den speicherinternen Stream zu verwalten.
In diesem Beispiel verwenden Sie die statische Funktion `NewAudioDataStreamFromSpeechSynthesisResult()`, um einen Datenstrom aus dem Ergebnis abzurufen.

Ersetzen Sie die Variablen `subscription` und `region` durch Ihren Schlüssel für die Spracheingabe und durch Ihren Standort/Ihre Region.

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main(subscription string, region string) {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, nil)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        // StartSpeakingTextAsync sends the result to channel when the synthesis starts.
        task := speechSynthesizer.StartSpeakingTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        // in most case we want to streaming receive the audio to lower the latency,
        // we can use AudioDataStream to do so.
        stream, err := speech.NewAudioDataStreamFromSpeechSynthesisResult(outcome.Result)
        defer stream.Close()
        if err != nil {
            fmt.Println("Got an error: ", err)
            return
        }

        var all_audio []byte
        audio_chunk := make([]byte, 2048)
        for {
            n, err := stream.Read(audio_chunk)

            if err == io.EOF {
                break
            }

            all_audio = append(all_audio, audio_chunk[:n]...)
        }

        fmt.Printf("Read [%d] bytes from audio data stream.\n", len(all_audio))
    }
}
```

Führen Sie die folgenden Befehle aus, um eine Datei namens `go.mod` zu erstellen, die mit auf GitHub gehosteten Komponenten verknüpft ist.

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Erstellen Sie als Nächstes den Code, und führen Sie ihn aus.

```shell
go build
go run quickstart
```

Ausführliche Informationen zu den Klassen [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) und [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer) finden Sie in der Referenzdokumentation.

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

Als Nächstes müssen Sie die Anforderung der Sprachsynthese so ändern, dass darin auf Ihre XML-Datei verwiesen wird.
Die Anforderung bleibt größtenteils unverändert, aber Sie verwenden nun `SpeakSsmlAsync()` anstelle der `SpeakTextAsync()`-Funktion. Diese Funktion erwartet eine XML-Zeichenfolge, weshalb Sie Ihre SSML-Konfiguration zunächst als Zeichenfolge laden. Ab hier ist das Ergebnisobjekt genau identisch mit vorhergehenden Beispielen.

> [!NOTE]
> Wenn Sie die Stimme ohne SSML ändern möchten, können Sie die Eigenschaft für `SpeechConfig` mithilfe von `speechConfig.SetSpeechSynthesisVoiceName("en-US-ChristopherNeural")` festlegen.

## <a name="get-facial-pose-events"></a>Abrufen von Gesichtsausdrucksereignissen

Die Sprache kann eine gute Möglichkeit zum Steuern der Animation von Gesichtsausdrücken sein.
Häufig werden die Schlüssel in der beobachteten Sprache mithilfe von [visemes](../../../how-to-speech-synthesis-viseme.md) dargestellt, wie z. b. die Position der Lippen, der Kiefer und die Zunge, wenn ein bestimmtes Phoneme erzeugt wird.
Sie können das Ereignis „viseme“ in der Sprach-SDK abonnieren.
Anschließend können Sie das Gesicht eines Zeichens bei der Wiedergabe von sprach Audioereignissen auf das Zeichen eines Zeichens animieren.
Erfahren Sie [wie Sie viseme-Ereignisse bekommen](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)
