---
title: 'Nachverfolgen der Speech SDK-Speichernutzung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Das Speech Service SDK unterstützt neben der Sprachübersetzung zahlreiche Programmiersprachen für die Konvertierung von Sprache in Text und Text in Sprache. In diesem Artikel werden die in das SDK integrierten Speicherverwaltungstools erläutert.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: programming-languages-set-two
ROBOTS: NOINDEX
ms.openlocfilehash: b1269860e0f79c207df2cdd1b0f54689dab08ea9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507073"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Nachverfolgen der Speech SDK-Speichernutzung

Das Speech SDK basiert auf nativem Code, der durch eine Reihe von Interoperabilitätsebenen in mehrere Programmiersprachen projiziert wird. Jede sprachspezifische Projektion verfügt über idiomatisch korrekte Features zur Verwaltung des Objektlebenszyklus. Darüber hinaus enthält das Speech SDK Tools zur Speicherverwaltung, mit denen die Ressourcennutzung mit Objektprotokollierung und Objektgrenzwerten nachverfolgt werden kann. 

## <a name="how-to-read-object-logs"></a>Lesen von Objektprotokollen

Ist die [Speech SDK-Protokollierung aktiviert](how-to-use-logging.md), werden Nachverfolgungstags ausgegeben, um die verlaufsbezogene Objektbeobachtung zu ermöglichen. Diese Tags beinhalten Folgendes: 

* `TrackHandle` oder `StopTracking` 
* den Objekttyp
* die aktuelle Anzahl der Objekte, die nachverfolgt werden, den Objekttyp und die aktuelle Zahl, die nachverfolgt wird

Hier sehen Sie ein Beispielprotokoll: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Festlegen eines Warnungsschwellenwerts

Sie können einen Warnungsschwellenwert erstellen. Wenn dieser Schwellenwert überschritten wird, wird eine Warnmeldung protokolliert (vorausgesetzt, die Protokollierung ist aktiviert). Die Warnmeldung enthält ein Speicherabbild aller vorhandenen Objekte sowie ihre Anzahl. Anhand dieser Informationen können Probleme besser nachvollzogen werden. 

Wenn Sie einen Warnungsschwellenwert aktivieren möchten, muss dieser für ein Objekt vom Typ `SpeechConfig` angegeben werden. Dieses Objekt wird bei der Erstellung einer neuen Erkennung überprüft. In den folgenden Beispielen wird davon ausgegangen, dass Sie eine `SpeechConfig`-Instanz namens `config` erstellt haben:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Der Standardwert dieser Eigenschaft ist 10.000.

## <a name="set-an-error-threshold"></a>Festlegen eines Fehlerschwellenwerts 

Mit dem Speech SDK können Sie die maximale Anzahl von Objekten festlegen, die zu einem bestimmten Zeitpunkt zulässig ist. Ist diese Einstellung aktiviert, tritt bei der Erstellung neuer Erkennungsobjekte ein Fehler auf, wenn die maximal zulässige Anzahl erreicht wird. Vorhandene Objekte sind weiterhin funktionsfähig.

Hier sehen Sie ein Beispiel für einen Fehler:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Wenn Sie einen Fehlerschwellenwert aktivieren möchten, muss dieser für ein Objekt vom Typ `SpeechConfig` angegeben werden. Dieses Objekt wird bei der Erstellung einer neuen Erkennung überprüft. In den folgenden Beispielen wird davon ausgegangen, dass Sie eine `SpeechConfig`-Instanz namens `config` erstellt haben:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Der Standardwert für diese Eigenschaft ist der plattformspezifische Höchstwert für den Datentyp `size_t`. Eine typische Erkennung nutzt zwischen sieben und zehn interne Objekte.

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zum Speech-SDK](speech-sdk.md)
