---
title: Audioverarbeitung mit dem Speech SDK – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Ein Überblick über die Funktionen, Eigenschaften und Einschränkungen für die Audioverarbeitung mithilfe des Speech Software Development Kits (SDK)
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: bbf774ac1f0b91848df48366436144d36b193033
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095421"
---
# <a name="audio-processing-with-speech-sdk"></a>Audioverarbeitung mit dem Speech SDK

Das Speech SDK integriert Microsoft Audio Stack (MAS), sodass jede Anwendung oder jedes Produkt seine Audioverarbeitungsfunktionen auf Eingabeaudiodaten anwenden kann. Es gelten die Mindestanforderungen aus dem Kapitel [Mindestanforderungen für die Verwendung von Microsoft Audio Stack](audio-processing-overview.md#minimum-requirements-to-use-microsoft-audio-stack).

Zu den wichtigsten Funktionen, die über Speech SDK-APIs zur Verfügung gestellt werden, gehören:
* **Echtzeit-Mikrofoneingabe & Dateieingabe:** Die Microsoft Audio Stack-Verarbeitung kann auf Echtzeit-Mikrofoneingaben, Streams und dateibasierte Eingaben angewendet werden. 
* **Auswahl von Erweiterungen:** Zur vollständigen Kontrolle über Ihr Szenario können Sie mit dem SDK einzelne Erweiterungen wie z. B. Hallunterdrückung, Rauschunterdrückung, automatische Verstärkungsregelung und akustische Echounterdrückung deaktivieren. Wenn Ihr Szenario beispielsweise kein Rendern von Ausgabeaudiodateien umfasst, die bspw. aus der Eingabeaudiodatei unterdrückt werden müssen, haben Sie die Möglichkeit, die akustische Echounterdrückung zu deaktivieren.
* **Benutzerdefinierte Mikrofongeometrien:** Mit dem SDK können Sie ihre eigenen benutzerdefinierten Geometrieinformationen für Mikrofone bereitstellen; und zwar zusätzlich zu voreingestellten Geometrien wie lineare 2-Mikrofon-, lineare 4-Mikrofon- und kreisförmige 7-Mikrofon-Arrays (weitere Informationen zu unterstützten voreingestellten Geometrien finden Sie unter [Empfehlungen für Mikrofonarrays](speech-sdk-microphone.md#microphone-geometry)).
* **Strahlformungswinkel:** Um Audioeingaben zu optimieren, die von einem vorab definierten Ort stammen, können bestimmte Strahlformungswinkel bereitgestellt werden (bezogen auf die Mikrofone).

Die Verarbeitung erfolgt komplett lokal an der Stelle, wo das Speech SDK verwendet wird. Für die Verarbeitung durch Microsoft Audio Stack werden keine Audiodaten an die Clouddienste von Microsoft gestreamt. Die einzige Ausnahme ist der Unterhaltungstranskriptionsdienst, bei dem Audiorohdaten zur Verarbeitung an die Clouddienste von Microsoft gesendet werden. 

> [!NOTE]
> Das Speech Devices SDK ist jetzt veraltet. Archivierte Versionen des Speech Devices SDK sind [hier](speech-devices-sdk.md) verfügbar; der entsprechende Beispielcode befindet sich auf [GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK). Allen Benutzern des Speech Devices SDK wird empfohlen, auf Speech SDK v1.19 oder eine neuere Version zu migrieren.

## <a name="reference-channel-for-echo-cancellation"></a>Referenzkanal für Echounterdrückung

Microsoft Audio Stack benötigt den Referenzkanal (auch als Loopbackkanal bezeichnet), um eine Echounterdrückung durchzuführen. Die Quelle des Referenzkanals variiert je nach Plattform:
* **Windows:** Der Referenzkanal wird automatisch vom Speech SDK erfasst, wenn die Option `SpeakerReferenceChannel::LastChannel` beim Erstellen von `AudioProcessingOptions` angegeben wird.
* **Linux:** ALSA (Advanced Linux Sound Architecture) muss so konfiguriert werden, dass der Referenzaudiodatenstrom als letzter Kanal für das zu verwendende Audioeingabegerät zur Verfügung stellt. Dies ist zusätzlich zur Bereitstellung der Option `SpeakerReferenceChannel::LastChannel` beim Erstellen von `AudioProcessingOptions` erforderlich.

## <a name="language-and-platform-support"></a>Sprach- und Plattformunterstützung

| Sprache   | Plattform(en)    | Referenz |
|------------|----------------|----------------|
| C++        | Windows, Linux | [C++ Dokumentation](/cpp/cognitive-services/speech/) |
| C#         | Windows, Linux | [C# Dokumentation](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java       | Windows, Linux | [Java Dokumentation](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Beispielcode

### <a name="using-microsoft-audio-stack-with-all-default-options"></a>Verwenden von Microsoft Audio Stack mit allen Standardoptionen

In diesem Beispiel wird gezeigt, wie Sie MAS mit allen Standarderweiterungsoptionen bei der Eingabe über das Standardmikrofon des Geräts verwenden.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-a-preset-microphone-geometry"></a>Verwenden von Microsoft Audio Stack mit einer voreingestellten Mikrofongeometrie

In diesem Beispiel wird gezeigt, wie Sie MAS mit einer voreingestellten Mikrofongeometrie auf einem angegebenen Audioeingabegerät verwenden. In diesem Beispiel:
* **Erweiterungsoptionen:** Die Standarderweiterungen werden auf den Eingabeaudiostream angewendet.
* **Voreingestellte Geometrie:** Die voreingestellte Geometrie stellt ein lineares 2-Mikrofon-Array dar.
* **Audioeingabegerät:** Die ID des Audioeingabegeräts ist `hw:0,1`. Weitere Informationen zum Auswählen eines Audioeingabegeräts finden Sie unter [Auswählen eines Audioeingabegeräts mit dem Speech SDK](how-to-select-audio-input-devices.md).

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
var audioInput = AudioConfig.FromMicrophoneInput("hw:0,1", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry::Linear2);
auto audioInput = AudioConfig::FromMicrophoneInput("hw:0,1", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
AudioConfig audioInput = AudioConfig.fromMicrophoneInput("hw:0,1", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-custom-microphone-geometry"></a>Verwenden von Microsoft Audio Stack mit einer benutzerdefinierten Mikrofongeometrie

In diesem Beispiel wird gezeigt, wie Sie MAS mit einer benutzerdefinierten Mikrofongeometrie auf einem angegebenen Audioeingabegerät verwenden. In diesem Beispiel:
* **Erweiterungsoptionen:** Die Standarderweiterungen werden auf den Eingabeaudiostream angewendet.
* **Benutzerdefinierte Geometrie:** Eine benutzerdefinierte Mikrofongeometrie für ein 7-Mikrofon-Array wird durch Angabe der Mikrofonkoordinaten bereitgestellt. Koordinaten werden in der Einheit Millimeter angegeben.
* **Audioeingabe:** Die Audioeingabe erfolgt aus einer Datei, deren Audiodaten von einem Audioeingabegerät erfasst wurden, das der angegebenen benutzerdefinierten Geometrie entspricht. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7]
{
    new MicrophoneCoordinates(0, 0, 0),
    new MicrophoneCoordinates(40, 0, 0),
    new MicrophoneCoordinates(20, -35, 0),
    new MicrophoneCoordinates(-20, -35, 0),
    new MicrophoneCoordinates(-40, 0, 0),
    new MicrophoneCoordinates(-20, 35, 0),
    new MicrophoneCoordinates(20, 35, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var audioInput = AudioConfig.FromWavFileInput("katiesteve.wav", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Planar,
    { { 0, 0, 0 }, { 40, 0, 0 }, { 20, -35, 0 }, { -20, -35, 0 }, { -40, 0, 0 }, { -20, 35, 0 }, { 20, 35, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto audioInput = AudioConfig::FromWavFileInput("katiesteve.wav", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7];
microphoneCoordinates[0] = new MicrophoneCoordinates(0, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(40, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, -35, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(-20, -35, 0);
microphoneCoordinates[4] = new MicrophoneCoordinates(-40, 0, 0);
microphoneCoordinates[5] = new MicrophoneCoordinates(-20, 35, 0);
microphoneCoordinates[6] = new MicrophoneCoordinates(20, 35, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
AudioConfig audioInput = AudioConfig.fromWavFileInput("katiesteve.wav", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-select-enhancements"></a>Verwenden von Microsoft Audio Stack mit ausgewählten Erweiterungen

In diesem Beispiel wird gezeigt, wie Sie MAS mit einem benutzerdefinierten Set an Erweiterungen auf die Eingabeaudiodaten anwenden. Standardmäßig sind alle Erweiterungen aktiviert; allerdings gibt es auch Optionen zum Deaktivieren der Funktionen Hallunterdrückung, Rauschunterdrückung, automatische Verstärkungsregelung und Echounterdrückung; die Deaktivierung erfolgt jeweils separat mit `AudioProcessingOptions`.

In diesem Beispiel:
* **Erweiterungsoptionen:** Echounterdrückung und Rauschunterdrückung werden deaktiviert, während alle anderen Erweiterungen aktiviert bleiben.
* **Audioeingabegerät:** Das Audioeingabegerät ist das Standardmikrofon des Geräts.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-to-specify-beamforming-angles"></a>Verwenden von Microsoft Audio Stack zum Angeben von Strahlformungswinkeln

In diesem Beispiel wird gezeigt, wie Sie MAS mit einer benutzerdefinierten Mikrofongeometrie und benutzerdefinierten Strahlformungswinkeln auf einem angegebenen Audioeingabegerät verwenden. In diesem Beispiel:
* **Erweiterungsoptionen:** Die Standarderweiterungen werden auf den Eingabeaudiostream angewendet.
* **Benutzerdefinierte Geometrie:** Eine benutzerdefinierte Mikrofongeometrie für ein 4-Mikrofon-Array wird durch Angabe der Mikrofonkoordinaten bereitgestellt. Die Koordinaten werden in der Einheit Millimeter angegeben.
* **Strahlformungswinkel:** Strahlformungswinkel werden angegeben, um die Audiodaten aus diesem Bereich zu optimieren. Die Winkel werden in der Einheit Grad angegeben. Im folgenden Beispielcode wird der Startwinkel auf 70 Grad und der Endwinkel auf 110 Grad festgelegt.
* **Audioeingabe:** Die Audioeingabe erfolgt aus einem Pushstream, wobei die Audiodaten innerhalb des Streams von einem Audioeingabegerät erfasst werden, das der angegebenen benutzerdefinierten Geometrie entspricht. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4]
{
    new MicrophoneCoordinates(-60, 0, 0),
    new MicrophoneCoordinates(-20, 0, 0),
    new MicrophoneCoordinates(20, 0, 0),
    new MicrophoneCoordinates(60, 0, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Linear, 70, 110, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var pushStream = AudioInputStream.CreatePushStream();
var audioInput = AudioConfig.FromStreamInput(pushStream, audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Linear,
    70,
    110,
    { { -60, 0, 0 }, { -20, 0, 0 }, { 20, 0, 0 }, { 60, 0, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto pushStream = AudioInputStream::CreatePushStream();
auto audioInput = AudioConfig::FromStreamInput(pushStream, audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4];
microphoneCoordinates[0] = new MicrophoneCoordinates(-60, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(-20, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, 0, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(60, 0, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, 70, 110, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
PushAudioInputStream pushStream = AudioInputStream.createPushStream();
AudioConfig audioInput = AudioConfig.fromStreamInput(pushStream, audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---
