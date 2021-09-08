---
title: Verringern der Wartezeit bei der Sprachsynthese mit dem Speech SDK
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die Wartezeit bei der Sprachsynthese mit dem Speech SDK verringern, einschließlich Streaming, Vorverbindung usw.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 38af8d1ffd422b151bebc2fea42c575f181cc72e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346807"
---
# <a name="lower-speech-synthesis-latency-using-speech-sdk"></a>Geringere Wartezeit bei der Sprachsynthese mit dem Speech SDK

> [!NOTE]
> Für diesen Artikel ist das Speech SDK 1.17.0 oder höher erforderlich.

Die Synthesewartezeit ist für Ihre Anwendungen von entscheidender Bedeutung.
In diesem Artikel werden die bewährten Methoden vorgestellt, um die Wartezeit zu verringern und Ihren Endbenutzern die beste Leistung zu bieten.

Normalerweise messen wir die Wartezeit wie folgt anhand von `first byte latency` und `finish latency`:

::: zone pivot="programming-language-csharp"

| Wartezeit | BESCHREIBUNG | [SpeechSynthesisResult](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult)-Eigenschaftsschlüssel |
|-----------|-------------|------------|
| first byte latency (Wartezeit für das erste Byte) | Gibt die Zeitverzögerung zwischen dem Start des Synthesetasks und dem Empfang des ersten Audiodatenblocks an. | SpeechServiceResponse_SynthesisFirstByteLatencyMs |
| finish latency (Abschlusswartezeit) | Gibt die Zeitverzögerung zwischen dem Start des Synthesetasks und dem Empfang der gesamten synthetisierten Audiodaten an. | SpeechServiceResponse_SynthesisFinishLatencyMs |

Das Speech SDK fügt die Wartezeitdauern in die Properties-Auflistung von [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) ein. Der folgende Beispielcode zeigt diese Werte.

```csharp
var result = await synthesizer.SpeakTextAsync(text);
Console.WriteLine($"first byte latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs)} ms");
Console.WriteLine($"finish latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs)} ms");
// you can also get the result id, and send to us when you need help for diagnosis
var resultId = result.ResultId;
```

::: zone-end

::: zone pivot="programming-language-cpp"

| Wartezeit | BESCHREIBUNG | [SpeechSynthesisResult](/cpp/cognitive-services/speech/speechsynthesisresult)-Eigenschaftsschlüssel |
|-----------|-------------|------------|
| `first byte latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang des ersten Audioblocks an. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang der gesamten synthetisierten Audiodaten an. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Das Speech SDK hat die Wartezeiten gemessen und sie in der Eigenschaftensammlung von [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) abgelegt. Sie erhalten sie über die folgenden Codes.

```cpp
auto result = synthesizer->SpeakTextAsync(text).get();
auto firstByteLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFirstByteLatencyMs));
auto finishedLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFinishLatencyMs));
// you can also get the result id, and send to us when you need help for diagnosis
auto resultId = result->ResultId;
```

::: zone-end

::: zone pivot="programming-language-java"

| Wartezeit | BESCHREIBUNG | [SpeechSynthesisResult](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult)-Eigenschaftsschlüssel |
|-----------|-------------|------------|
| `first byte latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang des ersten Audioblocks an. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang der gesamten synthetisierten Audiodaten an. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Das Speech SDK hat die Wartezeiten gemessen und sie in der Eigenschaftensammlung von [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) abgelegt. Sie erhalten sie über die folgenden Codes.

```java
SpeechSynthesisResult result = synthesizer.SpeakTextAsync(text).get();
System.out.println("first byte latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs) + " ms.");
System.out.println("finish latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs) + " ms.");
// you can also get the result id, and send to us when you need help for diagnosis
String resultId = result.getResultId();
```

::: zone-end


::: zone pivot="programming-language-python"

| Wartezeit | BESCHREIBUNG | [SpeechSynthesisResult](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult)-Eigenschaftsschlüssel |
|-----------|-------------|------------|
| `first byte latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang des ersten Audioblocks an. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang der gesamten synthetisierten Audiodaten an. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Das Speech SDK hat die Wartezeiten gemessen und sie in der Eigenschaftensammlung von [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) abgelegt. Sie erhalten sie über die folgenden Codes.

```python
result = synthesizer.speak_text_async(text).get()
first_byte_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs))
finished_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs))
# you can also get the result id, and send to us when you need help for diagnosis
result_id = result.result_id
```

::: zone-end

::: zone pivot="programming-language-objectivec"

| Wartezeit | BESCHREIBUNG | [SPXSpeechSynthesisResult](/objectivec/cognitive-services/speech/spxspeechsynthesisresult)-Eigenschaftsschlüssel |
|-----------|-------------|------------|
| `first byte latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang des ersten Audioblocks an. | `SPXSpeechServiceResponseSynthesisFirstByteLatencyMs` |
| `finish latency` | Gibt die Zeitverzögerung zwischen dem Start der Synthese und dem Empfang der gesamten synthetisierten Audiodaten an. | `SPXSpeechServiceResponseSynthesisFinishLatencyMs` |

Das Speech SDK hat die Wartezeiten gemessen und sie in der Eigenschaftensammlung von [`SPXSpeechSynthesisResult`](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) abgelegt. Sie erhalten sie über die folgenden Codes.

```Objective-C
SPXSpeechSynthesisResult *speechResult = [speechSynthesizer speakText:text];
int firstByteLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFirstByteLatencyMs]];
int finishedLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFinishLatencyMs]];
// you can also get the result id, and send to us when you need help for diagnosis
NSString *resultId = result.resultId;
```

::: zone-end

Die Wartezeit für das erste Byte (first byte latency) ist in den meisten Fällen viel niedriger als die Abschlusswartezeit (finish latency).
Die Wartezeit für das erste Byte ist unabhängig von der Textlänge, während die Abschlusswartezeit mit der Textlänge zunimmt.

Im Idealfall möchten wir die vom Benutzer erlebte Wartezeit (die Wartezeit, bevor der Benutzer den Ton hört) auf eine Netzwerkstreckenzeit plus die Wartezeit für den ersten Audioblock des Sprachsynthesediensts minimieren.

## <a name="streaming"></a>Streaming

Streaming ist entscheidend für die Verringerung der Wartezeit.
Clientcode kann die Wiedergabe starten, wenn der erste Audioblock empfangen wird.
In einem Dienstszenario können Sie die Audioblöcke sofort an Ihre Clients weiterleiten, anstatt auf die gesamten Audiodaten zu warten.

::: zone pivot="programming-language-csharp"

Sie können das [`PullAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pullaudiooutputstream)-, [`PushAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudiooutputstream)-, [`Synthesizing`-Ereignis](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing) und [`AudioDateStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) des Speech SDK verwenden, um das Streaming zu aktivieren.

Nehmen wir `AudioDateStream` als Beispiel:

```csharp
using (var synthesizer = new SpeechSynthesizer(config, null as AudioConfig))
{
    using (var result = await synthesizer.StartSpeakingTextAsync(text))
    {
        using (var audioDataStream = AudioDataStream.FromResult(result))
        {
            byte[] buffer = new byte[16000];
            uint filledSize = 0;
            while ((filledSize = audioDataStream.ReadData(buffer)) > 0)
            {
                Console.WriteLine($"{filledSize} bytes received.");
            }
        }
    }
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

Sie können das [`PullAudioOutputStream`](/cpp/cognitive-services/speech/audio-pullaudiooutputstream)-, [`PushAudioOutputStream`](/cpp/cognitive-services/speech/audio-pushaudiooutputstream)-, [`Synthesizing`-Ereignis](/cpp/cognitive-services/speech/speechsynthesizer#synthesizing) und [`AudioDateStream`](/cpp/cognitive-services/speech/audiodatastream) des Speech SDK verwenden, um das Streaming zu aktivieren.

Nehmen wir `AudioDateStream` als Beispiel:

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto result = synthesizer->SpeakTextAsync(text).get();
auto audioDataStream = AudioDataStream::FromResult(result);
uint8_t buffer[16000];
uint32_t filledSize = 0;
while ((filledSize = audioDataStream->ReadData(buffer, sizeof(buffer))) > 0)
{
    cout << filledSize << " bytes received." << endl;
}
```

::: zone-end

::: zone pivot="programming-language-java"

Sie können das [`PullAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pullaudiooutputstream)-, [`PushAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pushaudiooutputstream)-, [`Synthesizing`-Ereignis](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_Synthesizing) und [`AudioDateStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) des Speech SDK verwenden, um das Streaming zu aktivieren.

Nehmen wir `AudioDateStream` als Beispiel:

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(config, null);
SpeechSynthesisResult result = synthesizer.StartSpeakingTextAsync(text).get();
AudioDataStream audioDataStream = AudioDataStream.fromResult(result);
byte[] buffer = new byte[16000];
long filledSize = audioDataStream.readData(buffer);
while (filledSize > 0) {
    System.out.println(filledSize + " bytes received.");
    filledSize = audioDataStream.readData(buffer);
}
```

::: zone-end

::: zone pivot="programming-language-python"

Sie können das [`PullAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pullaudiooutputstream)-, [`PushAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pushaudiooutputstream)-, [`Synthesizing`-Ereignis](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#synthesizing) und [`AudioDateStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) des Speech SDK verwenden, um das Streaming zu aktivieren.

Nehmen wir `AudioDateStream` als Beispiel:

```python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = speech_synthesizer.start_speaking_text_async(text).get()
audio_data_stream = speechsdk.AudioDataStream(result)
audio_buffer = bytes(16000)
filled_size = audio_data_stream.read_data(audio_buffer)
while filled_size > 0:
    print("{} bytes received.".format(filled_size))
    filled_size = audio_data_stream.read_data(audio_buffer)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

Sie können das [`SPXPullAudioOutputStream`](/objectivec/cognitive-services/speech/spxpullaudiooutputstream)-, [`SPXPushAudioOutputStream`](/objectivec/cognitive-services/speech/spxpushaudiooutputstream)-, [`Synthesizing`-Ereignis](/objectivec/cognitive-services/speech/spxspeechsynthesizer#addsynthesizingeventhandler) und [`SPXAudioDataStream`](/objectivec/cognitive-services/speech/spxaudiodatastream) des Speech SDK verwenden, um das Streaming zu aktivieren.

Nehmen wir `AudioDateStream` als Beispiel:

```Objective-C
SPXSpeechSynthesizer *synthesizer = [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig audioConfiguration:nil];
SPXSpeechSynthesisResult *speechResult = [synthesizer startSpeakingText:inputText];
SPXAudioDataStream *stream = [[SPXAudioDataStream alloc] initFromSynthesisResult:speechResult];
NSMutableData* data = [[NSMutableData alloc]initWithCapacity:16000];
while ([stream readData:data length:16000] > 0) {
    // Read data here
}
```

::: zone-end

## <a name="pre-connect-and-reuse-speechsynthesizer"></a>Vorabverbindung und Wiederverwendung von SpeechSynthesizer

Das Speech SDK verwendet ein Websocket für die Kommunikation mit dem Dienst.
Im Idealfall sollte die Netzwerkwartezeit einer Netzwerkstreckenzeit entsprechen.
Wenn die Verbindung neu hergestellt wird, erfordert die Netzwerkwartezeit zusätzliche Zeit zum Herstellen der Verbindung.
Für die Einrichtung einer Websocket-Verbindung sind TCP-Handshake, SSL-Handshake, HTTP-Verbindung und Protokollupgrade erforderlich, was zu einer Zeitverzögerung führt.
Um die Wartezeit für das Herstellen der Verbindung zu vermeiden, empfiehlt es sich, vorab eine Verbindung herzustellen und `SpeechSynthesizer` wiederzuverwenden.

### <a name="pre-connect"></a>Vor dem Herstellen einer Verbindung

Stellen Sie zum Herstellen einer Vorabverbindung eine Verbindung mit dem Speech-Dienst her, wenn Sie wissen, dass die Verbindung bald benötigt wird. Wenn Sie z. B. einen Sprachbot auf dem Client erstellen, können Sie vorab eine Verbindung mit dem Sprachsynthesedienst herstellen, wenn der Benutzer mit dem Sprechen beginnt, und `SpeakTextAsync` aufrufen, wenn der Antworttext des Bots bereit ist.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(uspConfig, null as AudioConfig))
{
    using (var connection = Connection.FromSpeechSynthesizer(synthesizer))
    {
        connection.Open(true);
    }
    await synthesizer.SpeakTextAsync(text);
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto connection = Connection::FromSpeechSynthesizer(synthesizer);
connection->Open(true);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, (AudioConfig) null);
Connection connection = Connection.fromSpeechSynthesizer(synthesizer);
connection.openConnection(true);
```

::: zone-end

::: zone pivot="programming-language-python"

```python
synthesizer = speechsdk.SpeechSynthesizer(config, None)
connection = speechsdk.Connection.from_speech_synthesizer(synthesizer)
connection.open(True)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer* synthesizer = [[SPXSpeechSynthesizer alloc]initWithSpeechConfiguration:self.speechConfig audioConfiguration:nil];
SPXConnection* connection = [[SPXConnection alloc]initFromSpeechSynthesizer:synthesizer];
[connection open:true];
```

::: zone-end

> [!NOTE]
> Wenn der synthetisierte Text verfügbar ist, rufen Sie einfach `SpeakTextAsync` auf, um die Audiodaten zu synthetisieren. Das SDK verarbeitet die Verbindung.

### <a name="reuse-speechsynthesizer"></a>Wiederverwenden von SpeechSynthesizer

Eine weitere Möglichkeit, die Verbindungswartezeit zu reduzieren, besteht darin, `SpeechSynthesizer` wiederzuverwenden, damit Sie für die einzelnen Synthesen keinen neuen `SpeechSynthesizer` erstellen müssen.
Es wird empfohlen, den Objektpool im Dienstszenario zu verwenden. Weitere Informationen finden Sie in unserem Beispielcode für [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) und [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java).


## <a name="transmit-compressed-audio-over-the-network"></a>Übertragen komprimierter Audiodaten über das Netzwerk

Wenn das Netzwerk instabil ist oder eine eingeschränkte Bandbreite aufweist, wirkt sich die Nutzdatengröße auch auf die Wartezeit aus.
In der Zwischenzeit hilft ein komprimiertes Audioformat dabei, die Netzwerkbandbreite der Benutzer zu schonen, was besonders für mobile Benutzer nützlich ist.

Wir unterstützen viele komprimierte Formate, z. B. `opus`, `webm`, `mp3`, `silk` usw. Eine vollständige Liste finden Sie unter [SpeechSynthesisOutputFormat](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat).
Die Bitrate des `Riff24Khz16BitMonoPcm`-Formats beträgt z. B. 384 KBit/s, während `Audio24Khz48KBitRateMonoMp3` nur 48 KBit/s erfordert.
Unser Speech SDK verwendet automatisch ein komprimiertes Format für die Übertragung, wenn ein `pcm`-Ausgabeformat festgelegt und `GStreamer` ordnungsgemäß installiert ist.
[Diese Anweisung](how-to-use-codec-compressed-audio-input-streams.md) enthält Informationen, um `GStreamer` zu installieren und für das Speech SDK zu konfigurieren.

## <a name="others-tips"></a>Weitere Tipps

### <a name="cache-crl-files"></a>Zwischenspeichern von Zertifikatsperrlistendateien

Das Speech SDK verwendet Zertifikatsperrlistendateien, um die Zertifizierung zu überprüfen.
Durch das Zwischenspeichern der Sperrlistendateien bis zu deren Ablauf können Sie vermeiden, jedes Mal die Sperrlistendateien herunterzuladen.
Weitere Informationen finden Sie unter [Konfigurieren von OpenSSL für Linux](how-to-configure-openssl-linux.md#certificate-revocation-checks).

### <a name="use-latest-speech-sdk"></a>Verwenden des neuesten Speech SDK

Wir verbessern weiterhin die Leistung des Speech SDK. Versuchen Sie daher, das neueste Speech SDK in Ihrer Anwendung zu verwenden.
Wir haben z. B. ein Problem mit der `TCP_NODELAY`-Einstellung in [1.16.0](releasenotes.md#speech-sdk-1160-2021-march-release) behoben, durch das die Netzwerkstreckenzeit verringert wurde.

## <a name="load-test-guideline"></a>Richtlinie für Auslastungstests

Sie können den Auslastungstest verwenden, um die Kapazität und Wartezeit des Sprachsynthesediensts zu testen.
Hier folgen einige Richtlinien.

 - Der Sprachsynthesedienst kann automatisch skaliert werden, benötigt jedoch Zeit für die Aufskalierung. Wenn die Parallelität in kurzer Zeit erhöht wird, erhält der Client möglicherweise eine lange Wartezeit oder einen `429`-Fehlercode (zu viele Anforderungen). Daher wird empfohlen, die Parallelität schrittweise im Auslastungstest zu erhöhen. [In diesem Artikel](speech-services-quotas-and-limits.md#general-best-practices-to-mitigate-throttling-during-autoscaling) finden Sie weitere Einzelheiten, insbesondere [dieses Beispiel für Workloadmuster](speech-services-quotas-and-limits.md#example-of-a-workload-pattern-best-practice).
 - Sie können unser Beispiel verwenden, indem Sie einen Objektpool ([C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) und [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java)) für Auslastungstests verwenden und die Latenzwerte erhalten. Sie können die Testdurchläufe und die Parallelität im Beispiel ändern, um Ihre Zielparallelität zu erreichen.
 - Der Dienst verfügt basierend auf dem tatsächlichen Datenverkehr über eine Kontingentgrenze. Wenn Sie also einen Auslastungstest mit einer deutlich höheren Parallelität als dem tatsächlichen Datenverkehr durchführen möchten, stellen Sie vor dem Test eine Verbindung her.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen finden Sie in den Beispielen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master) auf GitHub.
