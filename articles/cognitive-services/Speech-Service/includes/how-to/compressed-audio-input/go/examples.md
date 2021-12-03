---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 09/17/2021
ms.author: amishu
ms.openlocfilehash: 01b63f92ba7cf36802c0d91e4930280a3d802ab7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156701"
---
Um das Speech SDK so zu konfigurieren, dass es komprimierte Audioeingaben akzeptiert, erstellen Sie eine `PullAudioInputStream` oder `PushAudioInputStream`. Erstellen Sie dann eine `AudioConfig` aus einer Instanz Ihrer stream-Klasse, und geben Sie dabei das Komprimierungsformat des Streams an.

Im folgenden Beispiel gehen wir davon aus, dass Ihr Anwendungsfall darin besteht, `PushStream` für eine komprimierte Datei zu verwenden. 

```go

package recognizer

import (
  "fmt"
  "time"
    "strings"

  "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
  "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
  "github.com/Microsoft/cognitive-services-speech-sdk-go/samples/helpers"
)

func RecognizeOnceFromCompressedFile(subscription string, region string, file string) {
  var containerFormat audio.AudioStreamContainerFormat
  if strings.Contains(file, ".mulaw") {
    containerFormat = audio.MULAW
  } else if strings.Contains(file, ".alaw") {
    containerFormat = audio.ALAW
  } else if strings.Contains(file, ".mp3") {
    containerFormat = audio.MP3
  } else if strings.Contains(file, ".flac") {
    containerFormat = audio.FLAC
  } else if strings.Contains(file, ".opus") {
    containerFormat = audio.OGGOPUS
  } else {
    containerFormat = audio.ANY
  }
  format, err := audio.GetCompressedFormat(containerFormat)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer format.Close()
  stream, err := audio.CreatePushAudioInputStreamFromFormat(format)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer stream.Close()
  audioConfig, err := audio.NewAudioConfigFromStreamInput(stream)
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
  helpers.PumpFileIntoStream(file, stream)
  task := speechRecognizer.RecognizeOnceAsync()
  var outcome speech.SpeechRecognitionOutcome
  select {
  case outcome = <-task:
  case <-time.After(40 * time.Second):
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
