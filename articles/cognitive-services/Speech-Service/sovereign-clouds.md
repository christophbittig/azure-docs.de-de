---
title: 'Sovereign Clouds: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Sovereign Clouds verwenden.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 11/09/2021
ms.author: alexeyo
ms.openlocfilehash: ae288de8ae05efc22534cfaf87c42261a5e6d59a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135799"
---
# <a name="speech-services-in-sovereign-clouds"></a>Speech-Dienste in Sovereign Clouds

## <a name="azure-government-united-states"></a>Azure Government (USA)

Azure Government ist nur für US-Behörden und ihre Partner verfügbar. Weitere Informationen zu Azure Government finden Sie [hier](../../azure-government/documentation-government-welcome.md) und [hier](../../azure-government/compare-azure-government-global-azure.md).

- **Azure-Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regionen:**
  - US Gov Arizona
  - US Government, Virginia
- **Verfügbare Tarife:**
  - Free (F0) und Standard (S0). Ausführlichere Informationen finden Sie [hier](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).
- **Unterstützte Features:**
  - Spracherkennung
    - Custom Speech (Anpassung von akustischem Modell (Acoustic Model, AM) und Sprachmodell (Language Model, LM))
      - [Speech Studio](https://speech.azure.us/)
  - Text-zu-Sprache
    - Standardstimmen
    - Neuronale Stimmen
  - Sprachübersetzung
- **Nicht unterstützte Funktionen:**
  - Custom Voice
- **Unterstützte Sprachen:**
  - Mehr dazu finden Sie in der Liste der [unterstützten Sprachen](language-support.md).

### <a name="endpoint-information"></a>Endpunktinformationen

In diesem Abschnitt erhalten Sie Informationen zum Endpunkt der Speech-Dienste zur Verwendung mit dem [Speech-SDK](speech-sdk.md), der [Sprache-in-Text-REST-API](rest-speech-to-text.md) und der [Text-zu-Sprache-REST-API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>REST-API der Speech-Dienste

REST-API-Endpunkte der Speech-Dienste in Azure Government weisen das folgende Format auf:

|  REST-API-Typ/-Vorgang | Endpunktformat |
|--|--|
| Zugriffstoken | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Spracherkennungs-REST-API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Text-to-Speech-REST-API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Ersetzen Sie `<REGION_IDENTIFIER>` durch den Bezeichner aus der folgenden Tabelle, der mit der Region Ihres Abonnements übereinstimmt:

|                     | Regionsbezeichner |
|--|--|
| **US Gov Arizona**  | `usgovarizona` |
| **US Government, Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Sprach-SDK

Für [Speech SDK](speech-sdk.md) in Sovereign Clouds müssen Sie die Instanziierung der Klasse `SpeechConfig` "from host / with host" oder die Option `--host` von [Speech CLI](spx-overview.md) verwenden. (Sie können auch die Instanziierung "von Endpunkt / mit Endpunkt" und die `--endpoint` Speech CLI-Option).

Die `SpeechConfig`-Klasse sollte folgendermaßen instanziiert werden:

# <a name="c"></a>[C#](#tab/c-sharp)
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
# <a name="c"></a>[C++](#tab/cpp)
```cpp
auto config = SpeechConfig::FromHost(usGovHost, subscriptionKey);
```
# <a name="java"></a>[Java](#tab/java)
```java
SpeechConfig config = SpeechConfig.fromHost(usGovHost, subscriptionKey);
```
# <a name="python"></a>[Python](#tab/python)
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(host=usGovHost, subscription=subscriptionKey)
```
# <a name="objective-c"></a>[Objective-C](#tab/objective-c)
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithHost:usGovHost subscription:subscriptionKey];
```
***

Die Speech-CLI sollte folgendermaßen verwendet werden (beachten Sie die `--host`-Option):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Ersetzen Sie `subscriptionKey` durch Ihren Speech-Ressourcenschlüssel. Ersetzen Sie `usGovHost` durch den Ausdruck, der mit dem erforderlichen Dienstangebot und der Region Ihres Abonnements in der folgenden Tabelle übereinstimmt:

|  Region/Dienstangebot | Hostausdruck |
|--|--|
| **US Gov Arizona** | |
| Spracherkennung | `wss://usgovarizona.stt.speech.azure.us` |
| Text-zu-Sprache | `https://usgovarizona.tts.speech.azure.us` |
| **US Government, Virginia** | |
| Spracherkennung | `wss://usgovvirginia.stt.speech.azure.us` |
| Text-zu-Sprache | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Azure China steht für Organisationen mit einer Geschäftspräsenz in China zur Verfügung. Weitere Informationen zu Azure China finden Sie [hier](/azure/china/overview-operations). 


- **Azure-Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regionen:**
  - China, Osten 2
  - China, Norden 2
- **Verfügbare Tarife:**
  - Free (F0) und Standard (S0). Ausführlichere Informationen finden Sie [hier](https://www.azure.cn/pricing/details/cognitive-services/index.html).
- **Unterstützte Features:**
  - Spracherkennung
    - Custom Speech (Anpassung von akustischem Modell (Acoustic Model, AM) und Sprachmodell (Language Model, LM))
      - [Speech Studio](https://speech.azure.cn/)
  - Text-zu-Sprache
    - Standardstimmen
    - Neuronale Stimmen
  - Sprachübersetzung
- **Nicht unterstützte Funktionen:**
  - Custom Voice
- **Unterstützte Sprachen:**
  - Mehr dazu finden Sie in der Liste der [unterstützten Sprachen](language-support.md).

### <a name="endpoint-information"></a>Endpunktinformationen

In diesem Abschnitt erhalten Sie Informationen zum Endpunkt der Speech-Dienste zur Verwendung mit dem [Speech-SDK](speech-sdk.md), der [Sprache-in-Text-REST-API](rest-speech-to-text.md) und der [Text-zu-Sprache-REST-API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>REST-API der Speech-Dienste

REST-API-Endpunkte der Speech-Dienste in Azure China weisen das folgende Format auf:

|  REST-API-Typ/-Vorgang | Endpunktformat |
|--|--|
| Zugriffstoken | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Spracherkennungs-REST-API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Spracherkennungs-REST-API für kurze Audiodaten](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Text-to-Speech-REST-API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Ersetzen Sie `<REGION_IDENTIFIER>` durch den Bezeichner aus der folgenden Tabelle, der mit der Region Ihres Abonnements übereinstimmt:

|                     | Regionsbezeichner |
|--|--|
| **China, Osten 2**  | `chinaeast2` |
| **China, Norden 2**  | `chinanorth2` |

#### <a name="speech-sdk"></a>Sprach-SDK

Für [Speech SDK](speech-sdk.md) in Sovereign Clouds müssen Sie die Instanziierung der Klasse `SpeechConfig` "from host / with host" oder die Option `--host` von [Speech CLI](spx-overview.md) verwenden. (Sie können auch die Instanziierung "von Endpunkt / mit Endpunkt" und die `--endpoint` Speech CLI-Option).

Die `SpeechConfig`-Klasse sollte folgendermaßen instanziiert werden:

# <a name="c"></a>[C#](#tab/c-sharp)
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
# <a name="c"></a>[C++](#tab/cpp)
```cpp
auto config = SpeechConfig::FromHost(azCnHost, subscriptionKey);
```
# <a name="java"></a>[Java](#tab/java)
```java
SpeechConfig config = SpeechConfig.fromHost(azCnHost, subscriptionKey);
```
# <a name="python"></a>[Python](#tab/python)
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(host=azCnHost, subscription=subscriptionKey)
```
# <a name="objective-c"></a>[Objective-C](#tab/objective-c)
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithHost:azCnHost subscription:subscriptionKey];
```
***

Die Speech-CLI sollte folgendermaßen verwendet werden (beachten Sie die `--host`-Option):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Ersetzen Sie `subscriptionKey` durch Ihren Speech-Ressourcenschlüssel. Ersetzen Sie `azCnHost` durch den Ausdruck, der mit dem erforderlichen Dienstangebot und der Region Ihres Abonnements in der folgenden Tabelle übereinstimmt:

|  Region/Dienstangebot | Hostausdruck |
|--|--|
| **China, Osten 2** | |
| Spracherkennung | `wss://chinaeast2.stt.speech.azure.cn` |
| Text-zu-Sprache | `https://chinaeast2.tts.speech.azure.cn` |
| **China, Norden 2** | |
| Spracherkennung | `wss://chinanorth2.stt.speech.azure.cn` |
| Text-zu-Sprache | `https://chinanorth2.tts.speech.azure.cn` |