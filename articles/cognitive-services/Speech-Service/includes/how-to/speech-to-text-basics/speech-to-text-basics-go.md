---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: eur
ms.openlocfilehash: 015125d03d07c77f0d54d61ce3af0e52b1701036
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501962"
---
Die Funktion zum Erkennen und Transkribieren von menschlicher Sprache (Spracherkennung) ist eines der zentralen Features des Speech-Diensts. In diesem Schnellstart erfahren Sie, wie Sie das Speech SDK in Ihren Apps und Produkten verwenden, um hochwertige Spracherkennungen durchzuführen.

## <a name="skip-to-samples-on-github"></a>Mit den Beispielen auf GitHub fortfahren

Greifen Sie auf GitHub auf die [Go-Schnellstartbeispiele](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer) zu, falls Sie direkt zum Beispielcode springen möchten.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Zuallererst muss das [Speech SDK für Go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser) installiert werden.

## <a name="speech-to-text-from-microphone"></a>Spracherkennung über ein Mikrofon

Verwenden Sie das folgende Codebeispiel, um die Spracherkennung über Ihr standardmäßiges Gerätemikrofon durchzuführen. Ersetzen Sie die Variablen `subscription` und `region` durch Ihren Schlüssel für die Spracheingabe und durch Ihren Standort/Ihre Region. Ihr Schlüssel-Region/Standort-Paar finden Sie auf der Seite [Ermitteln von Schlüsseln und Region/Standort](../../../overview.md#find-keys-and-locationregion). Wenn Sie das Skript ausführen, wird eine Erkennungssitzung mit Ihrem Standardmikrofon gestartet und Text ausgegeben.

```go
package main

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
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
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Führen Sie die folgenden Befehle aus, um eine Datei namens „go.mod“ zu erstellen, die mit auf GitHub gehosteten Komponenten verknüpft ist.

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Erstellen Sie als Nächstes den Code, und führen Sie ihn aus.

```cmd
go build
go run quickstart
```

Ausführliche Informationen zu den Klassen [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechConfig) und [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechRecognizer) finden Sie in der Referenzdokumentation.

## <a name="speech-to-text-from-audio-file"></a>Spracherkennung über eine Audiodatei

Verwenden Sie das folgende Beispiel, um die Spracherkennung mit einer Audiodatei durchzuführen. Ersetzen Sie die Variablen `subscription` und `region` durch Ihren Schlüssel für die Spracheingabe und durch Ihren Standort/Ihre Region. Ihr Schlüssel-Region/Standort-Paar finden Sie auf der Seite [Ermitteln von Schlüsseln und Region/Standort](../../../overview.md#find-keys-and-locationregion). Ersetzen Sie außerdem die Variable `file` durch einen Pfad zu einer WAV-Datei. Wenn Sie das Skript ausführen, wird Sprache aus der Datei erkannt und das Textergebnis ausgegeben.

```go
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
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
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

Führen Sie die folgenden Befehle aus, um eine Datei namens „go.mod“ zu erstellen, die mit auf GitHub gehosteten Komponenten verknüpft ist.

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Erstellen Sie als Nächstes den Code, und führen Sie ihn aus.

```cmd
go build
go run quickstart
```

Ausführliche Informationen zu den Klassen [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechConfig) und [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechRecognizer) finden Sie in der Referenzdokumentation.
