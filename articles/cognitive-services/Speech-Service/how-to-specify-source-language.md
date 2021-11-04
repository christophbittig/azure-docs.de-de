---
title: Angeben der Quellsprache für die Spracherkennung
titleSuffix: Azure Cognitive Services
description: Mit dem Sprach-SDK können Sie bei der Umwandlung von Sprache in Text die Quellsprache angeben. In diesem Artikel wird beschrieben, wie Sie die Methoden FromConfig und SourceLanguageConfig verwenden, damit der Spracherkennungsdienst die Quellsprache erkennen und ein benutzerdefiniertes Modellziel bereitstellen kann.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8ef79c841fcbaecb2b70ad0a95ce04c9628e4709
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508286"
---
# <a name="specify-source-language-for-speech-to-text"></a>Angeben der Quellsprache für die Spracherkennung

In diesem Artikel erfahren Sie, wie Sie die Quellsprache für eine Audioeingabe angeben, die zur Spracherkennung an das Sprach-SDK weitergeleitet wird. Außerdem wird ein Beispielcode bereitgestellt, um ein benutzerdefiniertes Sprachmodell zur verbesserten Erkennung anzugeben.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Vorgehensweise: Angeben der Quellsprache in C#

Im folgenden Beispiel wird die Ausgangssprache mithilfe eines `SpeechRecognizer`-Konstrukts explizit als Parameter bereitgestellt.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Im folgenden Beispiel wird die Quellsprache mithilfe von `SourceLanguageConfig` bereitgestellt. `sourceLanguageConfig` wird dann als Parameter an das `SpeechRecognizer`-Konstrukt übergeben.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Im folgenden Beispiel werden die Quellsprache und der benutzerdefinierte Endpunkt mithilfe von `SourceLanguageConfig` bereitgestellt. `sourceLanguageConfig` wird dann als Parameter an das `SpeechRecognizer`-Konstrukt übergeben.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> Die set-Methoden `SpeechRecognitionLanguage` und `EndpointId` sind veraltete Methoden aus der `SpeechConfig`-Klasse in C#. Die Verwendung dieser Methoden wird nicht empfohlen und sollte beim Erstellen eines `SpeechRecognizer` vermieden werden.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Vorgehensweise: Angeben der Quellsprache in C++

Im folgenden Beispiel wird die Quellsprache explizit mithilfe der `FromConfig`-Methode als Parameter bereitgestellt.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Im folgenden Beispiel wird die Quellsprache mithilfe von `SourceLanguageConfig` bereitgestellt. `sourceLanguageConfig` wird dann als Parameter an `FromConfig` übergeben, wenn der `recognizer` erstellt wird.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Im folgenden Beispiel werden die Quellsprache und der benutzerdefinierte Endpunkt mithilfe von `SourceLanguageConfig` bereitgestellt. `sourceLanguageConfig` wird als Parameter an `FromConfig` übergeben, wenn der `recognizer` erstellt wird.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` und `SetEndpointId` sind veraltete Methoden aus der `SpeechConfig`-Klasse in C++ und Java. Die Verwendung dieser Methoden wird nicht empfohlen und sollte beim Erstellen eines `SpeechRecognizer` vermieden werden.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Vorgehensweise: Angeben der Quellsprache in Java

Im folgenden Beispiel wird die Quellsprache beim Erstellen eines neuen `SpeechRecognizer`-Konstrukts explizit bereitgestellt.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Im folgenden Beispiel wird die Quellsprache mithilfe von `SourceLanguageConfig` bereitgestellt. `sourceLanguageConfig` wird dann als Parameterübergeben, wenn ein neuer `SpeechRecognizer` erstellt wird.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Im folgenden Beispiel werden die Quellsprache und der benutzerdefinierte Endpunkt mithilfe von `SourceLanguageConfig` bereitgestellt. `sourceLanguageConfig` wird dann als Parameterübergeben, wenn ein neuer `SpeechRecognizer` erstellt wird.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` und `setEndpointId` sind veraltete Methoden aus der `SpeechConfig`-Klasse in C++ und Java. Die Verwendung dieser Methoden wird nicht empfohlen und sollte beim Erstellen eines `SpeechRecognizer` vermieden werden.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Vorgehensweise: Angeben der Quellsprache in Python

Im folgenden Beispiel wird die Ausgangssprache mithilfe eines `SpeechRecognizer`-Konstrukts explizit als Parameter bereitgestellt.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Im folgenden Beispiel wird die Quellsprache mithilfe von `SourceLanguageConfig` bereitgestellt. `SourceLanguageConfig` wird dann als Parameter an das `SpeechRecognizer`-Konstrukt übergeben.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Im folgenden Beispiel werden die Quellsprache und der benutzerdefinierte Endpunkt mithilfe von `SourceLanguageConfig` bereitgestellt. `SourceLanguageConfig` wird dann als Parameter an das `SpeechRecognizer`-Konstrukt übergeben.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> Die Eigenschaften `speech_recognition_language` und `endpoint_id` sind veraltete Eigenschaften der Klasse `SpeechConfig` in Python. Die Verwendung dieser Eigenschaften wird nicht empfohlen und sollte bei der Erstellung eines `SpeechRecognizer`-Konstrukts vermieden werden.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Vorgehensweise: Angeben der Quellsprache in JavaScript

Im ersten Schritt wird ein `SpeechConfig` erstellt:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Geben Sie als nächstes die Quellsprache Ihrer Audiodatei mit `speechRecognitionLanguage` an:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Wenn Sie ein benutzerdefiniertes Modell zur Erkennung verwenden, können Sie den Endpunkt mit `endpointId` angeben:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Vorgehensweise: Angeben der Quellsprache in Objective-C

Im folgenden Beispiel wird die Ausgangssprache mithilfe eines `SPXSpeechRecognizer`-Konstrukts explizit als Parameter bereitgestellt.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

Im folgenden Beispiel wird die Quellsprache mithilfe von `SPXSourceLanguageConfiguration` bereitgestellt. `SPXSourceLanguageConfiguration` wird dann als Parameter an das `SPXSpeechRecognizer`-Konstrukt übergeben.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

Im folgenden Beispiel werden die Quellsprache und der benutzerdefinierte Endpunkt mithilfe von `SPXSourceLanguageConfiguration` bereitgestellt. `SPXSourceLanguageConfiguration` wird dann als Parameter an das `SPXSpeechRecognizer`-Konstrukt übergeben.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> Die Eigenschaften `speechRecognitionLanguage` und `endpointId` sind veraltete Eigenschaften der Klasse `SPXSpeechConfiguration` in Objective-C. Die Verwendung dieser Eigenschaften wird nicht empfohlen und sollte bei der Erstellung eines `SPXSpeechRecognizer`-Konstrukts vermieden werden.

::: zone-end

## <a name="see-also"></a>Weitere Informationen

* Eine Liste der unterstützten Sprachen und Gebietsschemas für die Spracherkennung finden Sie unter [Sprachunterstützung](language-support.md).

## <a name="next-steps"></a>Nächste Schritte

* [Speech SDK-Referenzdokumentation](speech-sdk.md)
