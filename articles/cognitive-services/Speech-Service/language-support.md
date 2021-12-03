---
title: Sprachunterstützung – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Der Speech-Dienst unterstützt neben der Sprachübersetzung zahlreiche Sprachen für die Konvertierung von Sprache in Text und Text in Sprache. Dieser Artikel enthält eine umfassende Liste zur Sprachunterstützung der einzelnen Dienstfunktionen.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: eur
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 1ae10bb589816ae40a033487fb5548e1bc1abeba
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549525"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Sprach- und Stimmunterstützung für den Speech-Dienst

Die Sprachunterstützung ist abhängig von der Funktion des Speech-Diensts. In den folgenden Tabellen ist die Sprachunterstützung für die Dienstangebote [Spracherkennung](#speech-to-text), [Sprachsynthese](#text-to-speech), [Sprachübersetzung](#speech-translation) und [Sprechererkennung](#speaker-recognition) zusammengefasst.

## <a name="speech-to-text"></a>Spracherkennung

Sowohl das Microsoft Speech SDK als auch die REST-API unterstützen die folgenden Sprachen (Gebietsschemas). 

Um die Genauigkeit zu erhöhen, wird die Anpassung für eine Teilmenge der Sprachen durch das Hochladen von **Audio und menschenmarkierten Transkripten** oder **zugehörigem Text (Sätze)** angeboten. Die Unterstützung für die Anpassung des Akustikmodells mit **Audio und menschenmarkierten Transkripten** ist auf die unten aufgeführten spezifischen Basismodelle beschränkt. Andere Basismodelle und Sprachen verwenden nur den Text der Transkripte, um benutzerdefinierte Modelle zu trainieren, wie mit **zugehörigem Text (Sätze)** angeboten. Weitere Informationen zur Anpassung finden Sie unter [Was ist Custom Speech?](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Sprache                 | Gebietsschema (BCP-47) | Anpassungen  | [Sprachenerkennung](how-to-automatic-language-detection.md) | [Aussprachebewertung](how-to-pronunciation-assessment.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|--------------------------|
| Arabisch (Algerien)                   | `ar-DZ` | Text                                   |                           |                          |
| Arabisch (Bahrain), modernes Hocharabisch  | `ar-BH` | Text                                   |                           |                          |
| Arabisch (Ägypten)                     | `ar-EG` | Text                                   | Ja                          |                          |
| Arabisch (Irak)                      | `ar-IQ` | Text                                   |                           |                          |
| Arabisch (Israel)                    | `ar-IL` | Text                                   |                           |                          |
| Arabisch (Jordanien)                    | `ar-JO` | Text                                   |                           |                          |
| Arabisch (Kuwait)                    | `ar-KW` | Text                                   |                           |                          |
| Arabisch (Libanon)                   | `ar-LB` | Text                                   |                           |                          |
| Arabisch (Libyen)                     | `ar-LY` | Text                                   |                           |                          |
| Arabisch (Marokko)                   | `ar-MA` | Text                                   |                           |                          |
| Arabisch (Oman)                      | `ar-OM` | Text                                   |                           |                          |
| Arabisch (Katar)                     | `ar-QA` | Text                                   |                           |                          |
| Arabisch (Saudi-Arabien)              | `ar-SA` | Text                                   |                           |                          |
| Arabisch (Palästinensische Autonomiebehörde)     | `ar-PS` | Text                                   |                           |                          |
| Arabisch (Syrien)                     | `ar-SY` | Text                                   |                           |                          |
| Arabisch (Tunesien)                   | `ar-TN` | Text                                   |                           |                          |
| Arabisch (Vereinigte Arabische Emirate)      | `ar-AE` | Text                                   |                           |                          |
| Arabisch (Jemen)                     | `ar-YE` | Text                                   |                           |                          |
| Bulgarisch (Bulgarien)               | `bg-BG` | Text                                   |                           |                          |
| Katalanisch (Spanien)                    | `ca-ES` | Text<br>Aussprache                  | Ja                          |                          |
| Chinesisch (Kantonesisch, traditionell)   | `zh-HK` | Audio (20201015)<br>Text                 |        Ja                   |                          |
| Chinesisch (Mandarin, vereinfacht)     | `zh-CN` | Audio (20200910)<br>Text                 |     Ja                      | Ja                         |
| Chinesisch (Taiwanesisch, Mandarin)       | `zh-TW` | Audio (20190701, 20201015)<br>Text                 |           Ja                |                          |
| Kroatisch (Kroatien)                 | `hr-HR` | Text<br>Aussprache                  |                           |                          |
| Tschechisch (Tschechische Republik)             | `cs-CZ` | Text<br>Aussprache                  |                           |                          |
| Dänisch (Dänemark)                   | `da-DK` | Text<br>Aussprache                  | Ja                          |                          |
| Niederländisch (Niederlande)                | `nl-NL` | Audio (20201015)<br>Text<br>Aussprache|    Ja                       |                          |
| Englisch (Australien)                | `en-AU` | Audio (20201019)<br>Text<br>Aussprache| Ja                          |                          |
| Englisch (Kanada)                   | `en-CA` | Audio (20201019)<br>Text<br>Aussprache| Ja                          |                          |
| Englisch (Ghana)                    | `en-GH` | Text<br>Aussprache                  |                           |                          |
| Englisch (Hongkong)                | `en-HK` | Text<br>Aussprache                  |                           |                          |
| Englisch (Indien)                    | `en-IN` | Audio (20200923)<br>Text<br>Aussprache |                          |                          |
| Englisch (Irland)                  | `en-IE` | Text<br>Aussprache                  |                           |                          |
| Englisch (Kenia)                    | `en-KE` | Text<br>Aussprache                  |                           |                          |
| Englisch (Neuseeland)              | `en-NZ` | Audio (20201019)<br>Text<br>Aussprache |                          |                          |
| Englisch (Nigeria)                  | `en-NG` | Text<br>Aussprache                  |                           |                          |
| Englisch (Philippinen)              | `en-PH` | Text<br>Aussprache                  |                           |                          |
| Englisch (Singapur)                | `en-SG` | Text<br>Aussprache                  |                           |                          |
| Englisch (Südafrika)             | `en-ZA` | Text<br>Aussprache                  |                           |                          |
| Englisch (Tansania)                 | `en-TZ` | Text<br>Aussprache                  |                           |                          |
| Walisisch (Großbritannien)           | `en-GB` | Audio (20201019)<br>Text<br>Aussprache| Ja                          | Ja                         |
| Englisch (USA)            | `en-US` | Audio (20201019, 20210223)<br>Text<br>Aussprache| Ja                          | Ja                         |
| Estnisch (Estland)                  | `et-EE` | Text<br>Aussprache                  |                           |                          |
| Philippinisch (Philippinen)             | `fil-PH`| Text<br>Aussprache                  |                           |                          |
| Finnisch (Finnland)                  | `fi-FI` | Text<br>Aussprache                  |     Ja                      |                          |
| Französisch (Kanada)                    | `fr-CA` | Audio (20201015)<br>Text<br>Aussprache|     Ja                      |                          |
| Französisch (Frankreich)                    | `fr-FR` | Audio (20201015)<br>Text<br>Aussprache|      Ja                     |                          |
| Französisch (Schweiz)               | `fr-CH` | Text<br>Aussprache                  |                           |                          |
| Deutsch (Österreich)                   | `de-AT` | Text<br>Aussprache                  |                           |                          |
| Deutsch (Schweiz)               | `de-CH` | Text<br>Aussprache                  |                           |                          |
| Deutsch (Deutschland)                   | `de-DE` | Audio (20190701, 20200619, 20201127)<br>Text<br>Aussprache|  Ja                         |                          |
| Griechisch (Griechenland)                     | `el-GR` | Text                                   |  Ja                         |                          |
| Gujarati (Indien)                  | `gu-IN` | Text                                   |                           |                          |
| Hebräisch (Israel)                    | `he-IL` | Text                                   |                           |                          |
| Hindi (Indien)                      | `hi-IN` | Audio (20200701)<br>Text                 |     Ja                      |                          |
| Ungarisch (Ungarn)                | `hu-HU` | Text<br>Aussprache                  |                           |                          |
| Indonesisch (Indonesien)             | `id-ID` | Text<br>Aussprache                  |                           |                          |
| Irisch (Irland)                    | `ga-IE` | Text<br>Aussprache                  |                           |                          |
| Italienisch (Italien)                    | `it-IT` | Audio (20201016)<br>Text<br>Aussprache|      Ja                     |                          |
| Japanisch (Japan)                   | `ja-JP` | Text                                   |      Ja                     |                          |
| Kannada (Indien)                   | `kn-IN` | Text                                   |                           |                          |
| Koreanisch (Korea)                     | `ko-KR` | Audio (20201015)<br>Text                 |      Ja                     |                          |
| Lettisch (Lettland)                   | `lv-LV` | Text<br>Aussprache                  |                           |                          |
| Litauisch (Litauen)             | `lt-LT` | Text<br>Aussprache                  |                           |                          |
| Malaiisch (Malaysia)                   | `ms-MY` | Text                                   |                           |                          |
| Maltesisch (Malta)                    | `mt-MT` | Text                                   |                           |                          |
| Marathi (Indien)                    | `mr-IN` | Text                                   |                           |                          |
| Norwegisch, Bokmål (Norwegen)         | `nb-NO` | Text                                   |     Ja                      |                          |
| Persisch (Iran)                     | `fa-IR` | Text                                   |                           |                          |
| Polnisch (Polen)                    | `pl-PL` | Text<br>Aussprache                  |       Ja                    |                          |
| Portugiesisch (Brasilien)                | `pt-BR` | Audio (20190620, 20201015)<br>Text<br>Aussprache|          Ja                 |                          |
| Portugiesisch (Portugal)              | `pt-PT` | Text<br>Aussprache                  |             Ja              |                          |
| Rumänisch (Rumänien)                 | `ro-RO` | Text<br>Aussprache                  |  Ja                         |                          |
| Russisch (Russische Föderation)                   | `ru-RU` | Audio (20200907)<br>Text                 |                Ja           |                          |
| Slowakisch (Slowakei)                  | `sk-SK` | Text<br>Aussprache                  |                           |                          |
| Slowenisch (Slowenien)               | `sl-SI` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Argentinien)                | `es-AR` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Bolivien)                  | `es-BO` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Chile)                    | `es-CL` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Kolumbien)                 | `es-CO` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Costa Rica)               | `es-CR` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Kuba)                     | `es-CU` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Dominikanische Republik)       | `es-DO` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Ecuador)                  | `es-EC` | Text<br>Aussprache                  |                           |                          |
| Spanisch (El Salvador)              | `es-SV` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Äquatorialguinea)        | `es-GQ` | Text                                   |                           |                          |
| Spanisch (Guatemala)                | `es-GT` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Honduras)                 | `es-HN` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Mexiko)                   | `es-MX` | Audio (20200907)<br>Text<br>Aussprache|    Ja                       |                          |
| Spanisch (Nicaragua)                | `es-NI` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Panama)                   | `es-PA` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Paraguay)                 | `es-PY` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Peru)                     | `es-PE` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Puerto Rico)              | `es-PR` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Spanien)                    | `es-ES` | Audio (20201015)<br>Text<br>Aussprache|  Ja                         |                          |
| Spanisch (Uruguay)                  | `es-UY` | Text<br>Aussprache                  |                           |                          |
| Spanisch (USA)                      | `es-US` | Text<br>Aussprache                  |                           |                          |
| Spanisch (Venezuela)                | `es-VE` | Text<br>Aussprache                  |                           |                          |
| Suaheli (Kenia)                    | `sw-KE` | Text                                   |                           |                          |
| Schwedisch (Schweden)                   | `sv-SE` | Text<br>Aussprache                  |   Ja                        |                          |
| Tamil (Indien)                      | `ta-IN` | Text                                   |                           |                          |
| Telugu (Indien)                     | `te-IN` | Text                                   |                           |                          |
| Thai (Thailand)                    | `th-TH` | Text                                   |      Ja                     |                          |
| Türkisch (Türkei)                   | `tr-TR` | Text                                   |                           |                          |
| Vietnamesisch (Vietnam)               | `vi-VN` | Text                                   |                           |                          |

## <a name="text-to-speech"></a>Text-zu-Sprache

Sowohl das Microsoft Speech SDK als auch die REST-API unterstützen diese Stimmen. Jede dieser Stimmen steht für eine bestimmte Sprache und einen bestimmten Dialekt und wird durch das Gebietsschema identifiziert. Eine vollständige Liste der Sprachen und Stimmen, die für jede bestimmte Region bzw. jeden Endpunkt unterstützt werden, finden Sie in der [API-Liste zu Stimmen](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Die Preise variieren für Standardstimmen, benutzerdefinierte und neuronale Stimmen. Weitere Informationen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Neuronale Stimmen

Die neuronale Sprachsynthese ist eine neue Art der Sprachsynthese mithilfe von Deep Neural Networks. Wenn Sie eine neuronale Stimme verwenden, kann die synthetisierte Sprache kaum von menschlichen Aufzeichnungen unterschieden werden.

Gestalten Sie mit neuronalen Stimmen Interaktionen mit Chatbots und Sprachassistenten noch natürlicher und einladender, wandeln Sie digitale Texte wie E-Books in Audiobooks um, und verpassen Sie Ihrem Navigationssystem im Auto ein Upgrade. Durch natürliche, menschenähnliche Intonation und klare Aussprache von Wörtern können neuronale Stimmen die Hörermüdung bei der Interaktion mit KI-Systemen erheblich verringern.

> [!NOTE]
> Neuronale Stimmen werden aus Stichproben erstellt, die eine 24-kHz-Abtastrate verwenden.
> Die Abtastrate aller Stimmen kann bei der Synchronisierung auf andere Abtastraten herauf- oder heruntergesetzt werden.

| Sprache | Gebietsschema | Geschlecht | Name der Stimme | Stilunterstützung |
|---|---|---|---|---|
| Afrikaans (Südafrika) | `af-ZA` | Female | `af-ZA-AdriNeural` <sup>Neu</sup>  | Allgemein |
| Afrikaans (Südafrika) | `af-ZA` | Male | `af-ZA-WillemNeural` <sup>Neu</sup>  | Allgemein |
| Amharisch (Äthiopien) | `am-ET` | Female | `am-ET-MekdesNeural` <sup>Neu</sup>  | Allgemein |
| Amharisch (Äthiopien) | `am-ET` | Male | `am-ET-AmehaNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Algerien) | `ar-DZ` | Female | `ar-DZ-AminaNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Algerien) | `ar-DZ` | Male | `ar-DZ-IsmaelNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Bahrain) | `ar-BH` | Female | `ar-BH-LailaNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Bahrain) | `ar-BH` | Male | `ar-BH-AliNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Ägypten) | `ar-EG` | Female | `ar-EG-SalmaNeural` | Allgemein |
| Arabisch (Ägypten) | `ar-EG` | Male | `ar-EG-ShakirNeural` | Allgemein |
| Arabisch (Irak) | `ar-IQ` | Female | `ar-IQ-RanaNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Irak) | `ar-IQ` | Male | `ar-IQ-BasselNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Jordanien) | `ar-JO` | Female | `ar-JO-Sana Neural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Jordanien) | `ar-JO` | Male | `ar-JO-Taim Neural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Kuwait) | `ar-KW` | Female | `ar-KW-NouraNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Kuwait) | `ar-KW` | Male | `ar-KW-FahedNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Libyen) | `ar-LY` | Female | `ar-LY-ImanNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Libyen) | `ar-LY` | Male | `ar-LY-OmarNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Marokko) | `ar-MA` | Female | `ar-MA-MounaNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Marokko) | `ar-MA` | Male | `ar-MA-JamalNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Katar) | `ar-QA` | Female | `ar-QA-AmalNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Katar) | `ar-QA` | Male | `ar-QA-MoazNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Saudi-Arabien) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | Allgemein |
| Arabisch (Saudi-Arabien) | `ar-SA` | Male | `ar-SA-HamedNeural` | Allgemein |
| Arabisch (Syrien) | `ar-SY` | Female | `ar-SY-AmanyNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Syrien) | `ar-SY` | Male | `ar-SY-LaithNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Tunesien) | `ar-TN` | Female | `ar-TN-ReemNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Tunesien) | `ar-TN` | Male | `ar-TN-HediNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Vereinigte Arabische Emirate) | `ar-AE` | Female | `ar-AE-FatimaNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Vereinigte Arabische Emirate) | `ar-AE` | Male | `ar-AE-HamdanNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Jemen) | `ar-YE` | Female | `ar-YE-MaryamNeural` <sup>Neu</sup>  | Allgemein |
| Arabisch (Jemen) | `ar-YE` | Male | `ar-YE-SalehNeural` <sup>Neu</sup>  | Allgemein |
| Bangla (Bangladesch) | `bn-BD` | Female | `bn-BD-NabanitaNeural` <sup>Neu</sup>  | Allgemein |
| Bangla (Bangladesch) | `bn-BD` | Male | `bn-BD-PradeepNeural` <sup>Neu</sup>  | Allgemein |
| Bulgarisch (Bulgarien) | `bg-BG` | Female | `bg-BG-KalinaNeural` | Allgemein |
| Bulgarisch (Bulgarien) | `bg-BG` | Male | `bg-BG-BorislavNeural` | Allgemein |
| Birmanisch (Myanmar) | `my-MM` | Female | `my-MM-NilarNeural` <sup>Neu</sup>  | Allgemein |
| Birmanisch (Myanmar) | `my-MM` | Male | `my-MM-ThihaNeural` <sup>Neu</sup>  | Allgemein |
| Katalanisch (Spanien) | `ca-ES` | Female | `ca-ES-AlbaNeural` | Allgemein |
| Katalanisch (Spanien) | `ca-ES` | Female | `ca-ES-JoanaNeural` | Allgemein |
| Katalanisch (Spanien) | `ca-ES` | Male | `ca-ES-EnricNeural` | Allgemein |
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | Allgemein |
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | Allgemein |
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Male | `zh-HK-WanLungNeural` | Allgemein |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | Allgemein, mehrere Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | Allgemein, mehrere Rollen und Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | Erwachsene Stimme, mehrere Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | Allgemein, mehrere Rollen und Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | Kinderstimme, optimiert für das Erzählen von Geschichten |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | Allgemein, mehrere Stile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male | `zh-CN-YunyangNeural` | Optimiert für die Ansage von Nachrichten,<br /> mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male | `zh-CN-YunyeNeural` | Optimiert für das Erzählen von Geschichten |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | Allgemein |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | Allgemein |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Male | `zh-TW-YunJheNeural` | Allgemein |
| Kroatisch (Kroatien) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | Allgemein |
| Kroatisch (Kroatien) | `hr-HR` | Male | `hr-HR-SreckoNeural` | Allgemein |
| Tschechisch (Tschechische Republik) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | Allgemein |
| Tschechisch (Tschechische Republik) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | Allgemein |
| Dänisch (Dänemark) | `da-DK` | Female | `da-DK-ChristelNeural` | Allgemein |
| Dänisch (Dänemark) | `da-DK` | Male | `da-DK-JeppeNeural` | Allgemein |
| Niederländisch (Belgien) | `nl-BE` | Female | `nl-BE-DenaNeural` | Allgemein | 
| Niederländisch (Belgien) | `nl-BE` | Male | `nl-BE-ArnaudNeural` | Allgemein | 
| Niederländisch (Niederlande) | `nl-NL` | Female | `nl-NL-ColetteNeural` | Allgemein |
| Niederländisch (Niederlande) | `nl-NL` | Female | `nl-NL-FennaNeural` | Allgemein |
| Niederländisch (Niederlande) | `nl-NL` | Male | `nl-NL-MaartenNeural` | Allgemein |
| Englisch (Australien) | `en-AU` | Female | `en-AU-NatashaNeural` | Allgemein |
| Englisch (Australien) | `en-AU` | Male | `en-AU-WilliamNeural` | Allgemein |
| Englisch (Kanada) | `en-CA` | Female | `en-CA-ClaraNeural` | Allgemein |
| Englisch (Kanada) | `en-CA` | Male | `en-CA-LiamNeural` | Allgemein |
| Englisch (Hongkong) | `en-HK` | Female | `en-HK-YanNeural` | Allgemein |
| Englisch (Hongkong) | `en-HK` | Male | `en-HK-SamNeural` | Allgemein |
| Englisch (Indien) | `en-IN` | Female | `en-IN-NeerjaNeural` | Allgemein |
| Englisch (Indien) | `en-IN` | Male | `en-IN-PrabhatNeural` | Allgemein |
| Englisch (Irland) | `en-IE` | Female | `en-IE-EmilyNeural` | Allgemein |
| Englisch (Irland) | `en-IE` | Male | `en-IE-ConnorNeural` | Allgemein |
| Englisch (Kenia) | `en-KE` | Female | `en-KE-AsiliaNeural` <sup>Neu</sup>  | Allgemein |
| Englisch (Kenia) | `en-KE` | Male | `en-KE-ChilembaNeural` <sup>Neu</sup>  | Allgemein |
| Englisch (Neuseeland) | `en-NZ` | Female | `en-NZ-MollyNeural` | Allgemein |
| Englisch (Neuseeland) | `en-NZ` | Male | `en-NZ-MitchellNeural` | Allgemein |
| Englisch (Nigeria) | `en-NG` | Female | `en-NG-EzinneNeural` <sup>Neu</sup>  | Allgemein |
| Englisch (Nigeria) | `en-NG` | Male | `en-NG-AbeoNeural` <sup>Neu</sup>  | Allgemein |
| Englisch (Philippinen) | `en-PH` | Female | `en-PH-RosaNeural` | Allgemein | 
| Englisch (Philippinen) | `en-PH` | Male | `en-PH-JamesNeural` | Allgemein | 
| Englisch (Singapur) | `en-SG` | Female | `en-SG-LunaNeural` | Allgemein |
| Englisch (Singapur) | `en-SG` | Male | `en-SG-WayneNeural` | Allgemein |
| Englisch (Südafrika) | `en-ZA` | Female | `en-ZA-LeahNeural` | Allgemein |
| Englisch (Südafrika) | `en-ZA` | Male | `en-ZA-LukeNeural` | Allgemein |
| Englisch (Tansania) | `en-TZ` | Female | `en-TZ-ImaniNeural` <sup>Neu</sup>  | Allgemein |
| Englisch (Tansania) | `en-TZ` | Male | `en-TZ-ElimuNeural` <sup>Neu</sup>  | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-LibbyNeural` | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-SoniaNeural` | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-MiaNeural` <sup>Am 30. Oktober 2021 eingestellt, siehe unten</sup> | Allgemein |
| Walisisch (Großbritannien) | `en-GB` | Male | `en-GB-RyanNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-AmberNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-AriaNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Englisch (USA) | `en-US` | Female | `en-US-AshleyNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-CoraNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-ElizabethNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-JennyNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Englisch (USA) | `en-US` | Female | `en-US-MichelleNeural`| Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-MonicaNeural` | Allgemein |
| Englisch (USA) | `en-US` | Female | `en-US-SaraNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Englisch (USA) | `en-US` | Kid | `en-US-AnaNeural`| Allgemein |
| Englisch (USA) | `en-US` | Male | `en-US-BrandonNeural` | Allgemein |
| Englisch (USA) | `en-US` | Male | `en-US-ChristopherNeural`  | Allgemein |
| Englisch (USA) | `en-US` | Male | `en-US-EricNeural` | Allgemein |
| Englisch (USA) | `en-US` | Male | `en-US-GuyNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Englisch (USA) | `en-US` | Male | `en-US-JacobNeural` | Allgemein |
| Estnisch (Estland) | `et-EE` | Female | `et-EE-AnuNeural` | Allgemein |
| Estnisch (Estland) | `et-EE` | Male | `et-EE-KertNeural` | Allgemein |
| Philippinisch (Philippinen) | `fil-PH` | Female | `fil-PH-BlessicaNeural` <sup>Neu</sup>  | Allgemein |
| Philippinisch (Philippinen) | `fil-PH` | Male | `fil-PH-AngeloNeural` <sup>Neu</sup>  | Allgemein |
| Finnisch (Finnland) | `fi-FI` | Female | `fi-FI-NooraNeural` | Allgemein |
| Finnisch (Finnland) | `fi-FI` | Female | `fi-FI-SelmaNeural` | Allgemein |
| Finnisch (Finnland) | `fi-FI` | Male | `fi-FI-HarriNeural` | Allgemein |
| Französisch (Belgien) | `fr-BE` | Female | `fr-BE-CharlineNeural` | Allgemein | 
| Französisch (Belgien) | `fr-BE` | Male | `fr-BE-GerardNeural` | Allgemein | 
| Französisch (Kanada) | `fr-CA` | Female | `fr-CA-SylvieNeural` | Allgemein |
| Französisch (Kanada) | `fr-CA` | Male | `fr-CA-AntoineNeural` | Allgemein |
| Französisch (Kanada) | `fr-CA` | Male | `fr-CA-JeanNeural` | Allgemein |
| Französisch (Frankreich) | `fr-FR` | Female | `fr-FR-DeniseNeural` | Allgemein |
| Französisch (Frankreich) | `fr-FR` | Male | `fr-FR-HenriNeural` | Allgemein |
| Französisch (Schweiz) | `fr-CH` | Female | `fr-CH-ArianeNeural` | Allgemein |
| Französisch (Schweiz) | `fr-CH` | Male | `fr-CH-FabriceNeural` | Allgemein |
| Galicisch (Spanien) | `gl-ES` | Female | `gl-ES-SabelaNeural` <sup>Neu</sup>  | Allgemein |
| Galicisch (Spanien) | `gl-ES` | Male | `gl-ES-RoiNeural` <sup>Neu</sup>  | Allgemein |
| Deutsch (Österreich) | `de-AT` | Female | `de-AT-IngridNeural` | Allgemein |
| Deutsch (Österreich) | `de-AT` | Male | `de-AT-JonasNeural` | Allgemein |
| Deutsch (Deutschland) | `de-DE` | Female | `de-DE-KatjaNeural` | Allgemein |
| Deutsch (Deutschland) | `de-DE` | Male | `de-DE-ConradNeural` | Allgemein |
| Deutsch (Schweiz) | `de-CH` | Female | `de-CH-LeniNeural` | Allgemein |
| Deutsch (Schweiz) | `de-CH` | Male | `de-CH-JanNeural` | Allgemein |
| Griechisch (Griechenland) | `el-GR` | Female | `el-GR-AthinaNeural` | Allgemein |
| Griechisch (Griechenland) | `el-GR` | Male | `el-GR-NestorasNeural` | Allgemein |
| Gujarati (Indien) | `gu-IN` | Female | `gu-IN-DhwaniNeural` | Allgemein |
| Gujarati (Indien) | `gu-IN` | Male | `gu-IN-NiranjanNeural` | Allgemein |
| Hebräisch (Israel) | `he-IL` | Female | `he-IL-HilaNeural` | Allgemein |
| Hebräisch (Israel) | `he-IL` | Male | `he-IL-AvriNeural` | Allgemein |
| Hindi (Indien) | `hi-IN` | Female | `hi-IN-SwaraNeural` | Allgemein |
| Hindi (Indien) | `hi-IN` | Male | `hi-IN-MadhurNeural` | Allgemein |
| Ungarisch (Ungarn) | `hu-HU` | Female | `hu-HU-NoemiNeural` | Allgemein |
| Ungarisch (Ungarn) | `hu-HU` | Male | `hu-HU-TamasNeural` | Allgemein |
| Indonesisch (Indonesien) | `id-ID` | Female | `id-ID-GadisNeural` | Allgemein |
| Indonesisch (Indonesien) | `id-ID` | Male | `id-ID-ArdiNeural` | Allgemein |
| Irisch (Irland) | `ga-IE` | Female | `ga-IE-OrlaNeural` | Allgemein |
| Irisch (Irland) | `ga-IE` | Male | `ga-IE-ColmNeural` | Allgemein |
| Italienisch (Italien) | `it-IT` | Female | `it-IT-ElsaNeural` | Allgemein |
| Italienisch (Italien) | `it-IT` | Female | `it-IT-IsabellaNeural` | Allgemein |
| Italienisch (Italien) | `it-IT` | Male | `it-IT-DiegoNeural` | Allgemein |
| Japanisch (Japan) | `ja-JP` | Female | `ja-JP-NanamiNeural` | Allgemein |
| Japanisch (Japan) | `ja-JP` | Male | `ja-JP-KeitaNeural` | Allgemein |
| Javanesisch (Indonesien) | `jv-ID` | Female | `jv-ID-SitiNeural` <sup>Neu</sup>  | Allgemein |
| Javanesisch (Indonesien) | `jv-ID` | Male | `jv-ID-DimasNeural` <sup>Neu</sup>  | Allgemein |
| Khmer (Kambodscha) | `km-KH` | Female | `km-KH-SreymomNeural` <sup>Neu</sup>  | Allgemein |
| Khmer (Kambodscha) | `km-KH` | Male | `km-KH-PisethNeural` <sup>Neu</sup>  | Allgemein |
| Koreanisch (Korea) | `ko-KR` | Female | `ko-KR-SunHiNeural` | Allgemein |
| Koreanisch (Korea) | `ko-KR` | Male | `ko-KR-InJoonNeural` | Allgemein |
| Lettisch (Lettland) | `lv-LV` | Female | `lv-LV-EveritaNeural` | Allgemein |
| Lettisch (Lettland) | `lv-LV` | Male | `lv-LV-NilsNeural` | Allgemein |
| Litauisch (Litauen) | `lt-LT` | Female | `lt-LT-OnaNeural` | Allgemein |
| Litauisch (Litauen) | `lt-LT` | Male | `lt-LT-LeonasNeural` | Allgemein |
| Malaiisch (Malaysia) | `ms-MY` | Female | `ms-MY-YasminNeural` | Allgemein |
| Malaiisch (Malaysia) | `ms-MY` | Male | `ms-MY-OsmanNeural` | Allgemein |
| Maltesisch (Malta) | `mt-MT` | Female | `mt-MT-GraceNeural` | Allgemein |
| Maltesisch (Malta) | `mt-MT` | Male | `mt-MT-JosephNeural` | Allgemein |
| Marathi (Indien) | `mr-IN` | Female | `mr-IN-AarohiNeural` | Allgemein |
| Marathi (Indien) | `mr-IN` | Male | `mr-IN-ManoharNeural` | Allgemein |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Female | `nb-NO-IselinNeural` | Allgemein |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Female | `nb-NO-PernilleNeural` | Allgemein |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Male | `nb-NO-FinnNeural` | Allgemein |
| Persisch (Iran) | `fa-IR` | Female | `fa-IR-DilaraNeural` <sup>Neu</sup>  | Allgemein |
| Persisch (Iran) | `fa-IR` | Male | `fa-IR-FaridNeural` <sup>Neu</sup>  | Allgemein |
| Polnisch (Polen) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | Allgemein |
| Polnisch (Polen) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | Allgemein |
| Polnisch (Polen) | `pl-PL` | Male | `pl-PL-MarekNeural` | Allgemein |
| Portugiesisch (Brasilien) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | Allgemein, mehrere Stimmstile verfügbar [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portugiesisch (Brasilien) | `pt-BR` | Male | `pt-BR-AntonioNeural` | Allgemein |
| Portugiesisch (Portugal) | `pt-PT` | Female | `pt-PT-FernandaNeural` | Allgemein |
| Portugiesisch (Portugal) | `pt-PT` | Female | `pt-PT-RaquelNeural` | Allgemein |
| Portugiesisch (Portugal) | `pt-PT` | Male | `pt-PT-DuarteNeural` | Allgemein |
| Rumänisch (Rumänien) | `ro-RO` | Female | `ro-RO-AlinaNeural` | Allgemein |
| Rumänisch (Rumänien) | `ro-RO` | Male | `ro-RO-EmilNeural` | Allgemein |
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-DariyaNeural` | Allgemein |
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | Allgemein |
| Russisch (Russische Föderation) | `ru-RU` | Male | `ru-RU-DmitryNeural` | Allgemein |
| Slowakisch (Slowakei) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | Allgemein |
| Slowakisch (Slowakei) | `sk-SK` | Male | `sk-SK-LukasNeural` | Allgemein |
| Slowenisch (Slowenien) | `sl-SI` | Female | `sl-SI-PetraNeural` | Allgemein |
| Slowenisch (Slowenien) | `sl-SI` | Male | `sl-SI-RokNeural` | Allgemein |
| Somali (Somalia) | `so-SO` | Female | `so-SO-UbaxNeural` <sup>Neu</sup>  | Allgemein |
| Somali (Somalia) | `so-SO`| Male | `so-SO-MuuseNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Argentinien) | `es-AR` | Female | `es-AR-ElenaNeural` | Allgemein |
| Spanisch (Argentinien) | `es-AR` | Male | `es-AR-TomasNeural` | Allgemein |
| Spanisch (Bolivien) | `es-BO` | Female | `es-BO-SofiaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Bolivien) | `es-BO` | Male | `es-BO-MarceloNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Chile) | `es-CL` | Female | `es-CL-CatalinaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Chile) | `es-CL` | Male | `es-CL-LorenzoNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Kolumbien) | `es-CO` | Female | `es-CO-SalomeNeural` | Allgemein |
| Spanisch (Kolumbien) | `es-CO` | Male | `es-CO-GonzaloNeural` | Allgemein |
| Spanisch (Costa Rica) | `es-CR` | Female | `es-CR-MariaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Costa Rica) | `es-CR` | Male | `es-CR-JuanNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Kuba) | `es-CU` | Female | `es-CU-BelkysNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Kuba) | `es-CU` | Male | `es-CU-ManuelNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Dominikanische Republik) | `es-DO` | Female | `es-DO-RamonaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Dominikanische Republik) | `es-DO` | Male | `es-DO-EmilioNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Ecuador) | `es-EC` | Female | `es-EC-AndreaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Ecuador) | `es-EC` | Male | `es-EC-LuisNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (El Salvador) | `es-SV` | Female | `es-SV-LorenaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (El Salvador) | `es-SV` | Male | `es-SV-RodrigoNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Äquatorialguinea) | `es-GQ` | Female | `es-GQ-TeresaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Äquatorialguinea) | `es-GQ` | Male | `es-GQ-JavierNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Guatemala) | `es-GT` | Female | `es-GT-MartaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Guatemala) | `es-GT` | Male | `es-GT-AndresNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Honduras) | `es-HN` | Female | `es-HN-KarlaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Honduras) | `es-HN` | Male | `es-HN-CarlosNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Mexiko) | `es-MX` | Female | `es-MX-DaliaNeural` | Allgemein |
| Spanisch (Mexiko) | `es-MX` | Male | `es-MX-JorgeNeural` | Allgemein |
| Spanisch (Nicaragua) | `es-NI` | Female | `es-NI-YolandaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Nicaragua) | `es-NI` | Male | `es-NI-FedericoNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Panama) | `es-PA` | Female | `es-PA-MargaritaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Panama) | `es-PA` | Male | `es-PA-RobertoNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Paraguay) | `es-PY` | Female | `es-PY-TaniaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Paraguay) | `es-PY` | Male | `es-PY-MarioNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Peru) | `es-PE` | Female | `es-PE-CamilaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Peru) | `es-PE` | Male | `es-PE-AlexNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Puerto Rico) | `es-PR` | Female | `es-PR-Karina Neural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Puerto Rico) | `es-PR` | Male | `es-PR-Victor Neural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Spanien) | `es-ES` | Female | `es-ES-ElviraNeural` | Allgemein |
| Spanisch (Spanien) | `es-ES` | Male | `es-ES-AlvaroNeural` | Allgemein |
| Spanisch (Uruguay) | `es-UY` | Female | `es-UY-ValentinaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Uruguay) | `es-UY` | Male | `es-UY-MateoNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (USA) | `es-US` | Female | `es-US-PalomaNeural` | Allgemein |
| Spanisch (USA) | `es-US` | Male | `es-US-AlonsoNeural` | Allgemein |
| Spanisch (Venezuela) | `es-VE` | Female | `es-VE-PaolaNeural` <sup>Neu</sup>  | Allgemein |
| Spanisch (Venezuela) | `es-VE` | Male | `es-VE-SebastianNeural` <sup>Neu</sup>  | Allgemein |
| Sundanesisch (Indonesien) | `su-ID` | Female | `su-ID-TutiNeural` <sup>Neu</sup>  | Allgemein |
| Sundanesisch (Indonesien) | `su-ID` | Male | `su-ID-JajangNeural` <sup>Neu</sup>  | Allgemein |
| Suaheli (Kenia) | `sw-KE` | Female | `sw-KE-ZuriNeural` | Allgemein |
| Suaheli (Kenia) | `sw-KE` | Male | `sw-KE-RafikiNeural` | Allgemein |
| Suaheli (Tansania) | `sw-TZ` | Female | `sw-TZ-RehemaNeural` <sup>Neu</sup>  | Allgemein |
| Suaheli (Tansania) | `sw-TZ` | Male | `sw-TZ-DaudiNeural` <sup>Neu</sup>  | Allgemein |
| Schwedisch (Schweden) | `sv-SE` | Female | `sv-SE-HilleviNeural` | Allgemein |
| Schwedisch (Schweden) | `sv-SE` | Female | `sv-SE-SofieNeural` | Allgemein |
| Schwedisch (Schweden) | `sv-SE` | Male | `sv-SE-MattiasNeural` | Allgemein |
| Tamil (Indien) | `ta-IN` | Female | `ta-IN-PallaviNeural` | Allgemein |
| Tamil (Indien) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | Allgemein |
| Tamilisch (Singapur) | `ta-SG` | Female | `ta-SG-VenbaNeural` <sup>Neu</sup>  | Allgemein |
| Tamilisch (Singapur) | `ta-SG` | Male | `ta-SG-AnbuNeural` <sup>Neu</sup>  | Allgemein |
| Tamilisch (Sri Lanka) | `ta-LK` | Female | `ta-LK-SaranyaNeural` <sup>Neu</sup>  | Allgemein |
| Tamilisch (Sri Lanka) | `ta-LK` | Male | `ta-LK-KumarNeural` <sup>Neu</sup>  | Allgemein |
| Telugu (Indien) | `te-IN` | Female | `te-IN-ShrutiNeural` | Allgemein |
| Telugu (Indien) | `te-IN` | Male | `te-IN-MohanNeural` | Allgemein |
| Thai (Thailand) | `th-TH` | Female | `th-TH-AcharaNeural` | Allgemein |
| Thai (Thailand) | `th-TH` | Female | `th-TH-PremwadeeNeural` | Allgemein |
| Thai (Thailand) | `th-TH` | Male | `th-TH-NiwatNeural` | Allgemein |
| Türkisch (Türkei) | `tr-TR` | Female | `tr-TR-EmelNeural` | Allgemein |
| Türkisch (Türkei) | `tr-TR` | Male | `tr-TR-AhmetNeural` | Allgemein |
| Ukrainisch (Ukraine) | `uk-UA` | Female | `uk-UA-PolinaNeural` | Allgemein | 
| Ukrainisch (Ukraine) | `uk-UA` | Male | `uk-UA-OstapNeural` | Allgemein | 
| Urdu (Indien) | `ur-IN` | Female | `ur-IN-GulNeural` <sup>Neu</sup>  | Allgemein |
| Urdu (Indien) | `ur-IN` | Male | `ur-IN-SalmanNeural` <sup>Neu</sup>  | Allgemein |
| Urdu (Pakistan) | `ur-PK` | Female | `ur-PK-UzmaNeural`  | Allgemein | 
| Urdu (Pakistan) | `ur-PK` | Male | `ur-PK-AsadNeural` | Allgemein | 
| Usbekisch (Usbekistan) | `uz-UZ` | Female | `uz-UZ-MadinaNeural` <sup>Neu</sup>  | Allgemein |
| Usbekisch (Usbekistan) | `uz-UZ` | Male | `uz-UZ-SardorNeural` <sup>Neu</sup>  | Allgemein |
| Vietnamesisch (Vietnam) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | Allgemein |
| Vietnamesisch (Vietnam) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | Allgemein |
| Walisisch (Großbritannien) | `cy-GB` | Female | `cy-GB-NiaNeural` | Allgemein | 
| Walisisch (Großbritannien) | `cy-GB` | Male | `cy-GB-AledNeural` | Allgemein | 
| Zulu (Südafrika) | `zu-ZA` | Female | `zu-ZA-ThandoNeural` <sup>Neu</sup>  | Allgemein |
| Zulu (Südafrika) | `zu-ZA` | Male | `zu-ZA-ThembaNeural` <sup>Neu</sup>  | Allgemein |

> [!IMPORTANT]
> Die Stimme „Englisch (Vereinigtes Königreich) `en-GB-MiaNeural`“ wurde am **30. Oktober 2021** außer Betrieb genommen. Alle Dienstanforderungen an `en-GB-MiaNeural` werden seit dem **30. Oktober 2021** automatisch an `en-GB-SoniaNeural` umgeleitet.
> Wenn Sie nach dem **30. Oktober 2021** den Container „Neural TTS“ verwenden möchten, laden Sie die [neueste Version](speech-container-howto.md#get-the-container-image-with-docker-pull) herunter und stellen sie bereit. Anforderungen an frühere Versionen werden abgelehnt.

#### <a name="neural-voices-in-preview"></a>Neuronale Stimmen in der Vorschau

Die folgenden neuronalen Stimmen sind in der öffentlichen Vorschau verfügbar. 

| Sprache                         | Gebietsschema  | Geschlecht | Name der Stimme                             | Stilunterstützung |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Englisch (USA) | `en-US` | Female | `en-US-JennyMultilingualNeural` <sup>Neu</sup> | Allgemeine, mehrsprachige Funktionen, die mit [SSML verfügbar sind](speech-synthesis-markup.md#create-an-ssml-document) |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaochenNeural` <sup>Neu</sup> | Optimiert für spontane Konversation |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoyanNeural` <sup>Neu</sup> | Optimiert für den Kundendienst |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoshuangNeural` <sup>Neu</sup> | Kinderstimme, optimiert für Kindergeschichten und -chats, mehrere Sprachstile [mittels SSML](speech-synthesis-markup.md#adjust-speaking-styles) verfügbar|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-XiaoqiuNeural` <sup>Neu</sup> | Optimiert für das Erzählen |

> [!IMPORTANT]
> Stimmen in der öffentlichen Vorschau sind nur in drei Dienstregionen verfügbar: „USA, Osten“, „Europa, Westen“ und „Asien, Südosten“.

> [!TIP]
> `en-US-JennyNeuralMultilingual` unterstützt mehrere Sprachen. In der [API-Liste zu Stimmen](rest-text-to-speech.md#get-a-list-of-voices) finden Sie die Liste der unterstützten Sprachen.

Weitere Informationen zur regionalen Verfügbarkeit finden Sie unter [Regionen](regions.md#neural-and-standard-voices).

Informationen dazu, wie Sie neuronale Stimmen konfigurieren und anpassen können (z. B. den Sprechstil), finden Sie unter [Anpassen von Sprechweisen](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> Die Stimme `en-US-JessaNeural` wurde in `en-US-AriaNeural` geändert. Wenn Sie zuvor „Jessa“ verwendet haben, wechseln Sie zu „Aria“.

> [!TIP]
> In Ihren Sprachsyntheseanforderungen kann weiterhin die vollständige Dienstnamenzuordnung wie etwa „Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)“ verwendet werden.

### <a name="standard-voices"></a>Standardstimmen

Es stehen mehr als 75 Standardstimmen in mehr als 45 Sprachen und Gebietsschemas zur Verfügung, mit denen Sie Text in Sprache umwandeln können. Weitere Informationen zur regionalen Verfügbarkeit finden Sie unter [Regionen](regions.md#neural-and-standard-voices).

> [!IMPORTANT]
> Wir stellen die Standardstimmen am **31. August 2024** ein und sie werden nach diesem Datum nicht mehr unterstützt.Dies wurde in E-Mails angekündigt, die an alle vorhandenen und vor dem **31. August 2021** abgeschlossenen Speech-Abonnements gesendet wurden. Während des Außerbetriebnahmezeitraums (**31. August 2021** - **31. August 2024**) können vorhandene Standardstimmenbenutzer weiterhin ihre Standardstimmen verwenden. Alle neuen Benutzer bzw. neuen Sprachressourcen sollten zu den neuronalen Stimmen wechseln.

> [!NOTE]
> Mit zwei Ausnahmen werden Standardstimmen aus Stichproben erstellt, die eine 16-kHz-Abtastrate verwenden.
> Die Stimmen **en-US-AriaRUS** und **en-US-GuyRUS** werden ebenfalls aus Stichproben erstellt, die eine Abtastrate von 24 khz verwenden.
> Die Abtastrate aller Stimmen kann bei der Synchronisierung auf andere Abtastraten herauf- oder heruntergesetzt werden.

| Sprache | Gebietsschema (BCP-47) | Geschlecht | Name der Stimme |
|--|--|--|--|
| Arabisch (Ägypten) | `ar-EG` | Female | `ar-EG-Hoda`|
| Arabisch (Saudi-Arabien) | `ar-SA` | Male | `ar-SA-Naayf`|
| Bulgarisch (Bulgarien) | `bg-BG` | Male | `bg-BG-Ivan`|
| Katalanisch (Spanien) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Male | `zh-HK-Danny`|
| Chinesisch (Kantonesisch, traditionell) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Male | `zh-CN-Kangkang`|
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| Chinesisch (Taiwanesisch, Mandarin) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| Chinesisch (Taiwanesisch, Mandarin) |  `zh-TW` | Female | `zh-TW-Yating`|
| Chinesisch (Taiwanesisch, Mandarin) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| Kroatisch (Kroatien) | `hr-HR` | Male | `hr-HR-Matej`|
| Tschechisch (Tschechische Republik) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| Dänisch (Dänemark) | `da-DK` | Female | `da-DK-HelleRUS`|
| Niederländisch (Niederlande) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| Englisch (Australien) | `en-AU` | Female | `en-AU-Catherine`|
| Englisch (Australien) | `en-AU` | Female | `en-AU-HayleyRUS`|
| Englisch (Kanada) | `en-CA` | Female | `en-CA-HeatherRUS`|
| Englisch (Kanada) | `en-CA` | Female | `en-CA-Linda`|
| Englisch (Indien) | `en-IN` | Female | `en-IN-Heera`|
| Englisch (Indien) | `en-IN` | Female | `en-IN-PriyaRUS`|
| Englisch (Indien) | `en-IN` | Male | `en-IN-Ravi`|
| Englisch (Irland) | `en-IE` | Male | `en-IE-Sean`|
| Walisisch (Großbritannien) | `en-GB` | Male | `en-GB-George`|
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-HazelRUS`|
| Walisisch (Großbritannien) | `en-GB` | Female | `en-GB-Susan`|
| Englisch (USA) | `en-US` | Male | `en-US-BenjaminRUS`|
| Englisch (USA) | `en-US` | Male | `en-US-GuyRUS`|
| Englisch (USA) | `en-US` | Female | `en-US-AriaRUS`|
| Englisch (USA) | `en-US` | Female | `en-US-ZiraRUS`|
| Finnisch (Finnland) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| Französisch (Kanada) | `fr-CA` | Female | `fr-CA-Caroline`|
| Französisch (Kanada) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| Französisch (Frankreich) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| Französisch (Frankreich) | `fr-FR` | Female | `fr-FR-Julie`|
| Französisch (Frankreich) | `fr-FR` | Male | `fr-FR-Paul`|
| Französisch (Schweiz) | `fr-CH` | Male | `fr-CH-Guillaume`|
| Deutsch (Österreich) | `de-AT` | Male | `de-AT-Michael`|
| Deutsch (Deutschland) | `de-DE` | Female | `de-DE-HeddaRUS`|
| Deutsch (Deutschland) | `de-DE` | Male | `de-DE-Stefan`|
| Deutsch (Schweiz) | `de-CH` | Male | `de-CH-Karsten`|
| Griechisch (Griechenland) | `el-GR` | Male | `el-GR-Stefanos`|
| Hebräisch (Israel) | `he-IL` | Male | `he-IL-Asaf`|
| Hindi (Indien) | `hi-IN` | Male | `hi-IN-Hemant`|
| Hindi (Indien) | `hi-IN` | Female | `hi-IN-Kalpana`|
| Ungarisch (Ungarn) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| Indonesisch (Indonesien) | `id-ID` | Male | `id-ID-Andika`|
| Italienisch (Italien) | `it-IT` | Male | `it-IT-Cosimo`|
| Italienisch (Italien) | `it-IT` | Female | `it-IT-LuciaRUS`|
| Japanisch (Japan) | `ja-JP` | Female | `ja-JP-Ayumi`|
| Japanisch (Japan) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| Japanisch (Japan) | `ja-JP` | Male | `ja-JP-Ichiro`|
| Koreanisch (Korea) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| Malaiisch (Malaysia) | `ms-MY` | Male | `ms-MY-Rizwan`|
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| Polnisch (Polen) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| Portugiesisch (Brasilien) | `pt-BR` | Male | `pt-BR-Daniel`|
| Portugiesisch (Brasilien) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| Portugiesisch (Portugal) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| Rumänisch (Rumänien) | `ro-RO` | Male | `ro-RO-Andrei`|
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| Russisch (Russische Föderation) | `ru-RU` | Female | `ru-RU-Irina`|
| Russisch (Russische Föderation) | `ru-RU` | Male | `ru-RU-Pavel`|
| Slowakisch (Slowakei) | `sk-SK` | Male | `sk-SK-Filip`|
| Slowenisch (Slowenien) | `sl-SI` | Male | `sl-SI-Lado`|
| Spanisch (Mexiko) | `es-MX` | Female | `es-MX-HildaRUS`|
| Spanisch (Mexiko) | `es-MX` | Male | `es-MX-Raul`|
| Spanisch (Spanien) | `es-ES` | Female | `es-ES-HelenaRUS`|
| Spanisch (Spanien) | `es-ES` | Female | `es-ES-Laura`|
| Spanisch (Spanien) | `es-ES` | Male | `es-ES-Pablo`|
| Schwedisch (Schweden) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| Tamil (Indien) | `ta-IN` | Male | `ta-IN-Valluvar`|
| Telugu (Indien) | `te-IN` | Female | `te-IN-Chitra`|
| Thai (Thailand) | `th-TH` | Male | `th-TH-Pattara`|
| Türkisch (Türkei) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| Vietnamesisch (Vietnam) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> Die Stimme `en-US-Jessa` wurde in `en-US-Aria` geändert. Wenn Sie zuvor „Jessa“ verwendet haben, wechseln Sie zu „Aria“.

> [!TIP]
> In Sprachsyntheseanforderungen kann weiterhin die vollständige Dienstnamenzuordnung wie etwa „Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)“ verwendet werden.

### <a name="customization"></a>Anpassung

Custom Voice ist auf der neuronalen Ebene (auch als „benutzerdefinierte neuronale Stimme“ bekannt) verfügbar. Basierend auf der neuronalen TTS-Technologie und dem mehrsprachigen universellen Modell mit mehreren Sprechern können Sie mit „Benutzerdefinierte neuronale Stimme“ synthetische Stimmen erstellen, die eine Vielzahl von Sprechstilen aufweisen oder sprachübergreifend angepasst werden können. Nachfolgend finden Sie die unterstützten Sprachen.  

> [!IMPORTANT]
> Die Standardebene, einschließlich die statistischen parametrischen und konkatenativen Trainingsmethoden von Custom Voice, sind veraltet und werden ab dem 29.2.2024 nicht mehr unterstützt. Wenn Sie nicht-neuronale bzw. nicht-standardmäßige Custom Voice-Features verwenden, führen Sie sofort eine Migration zur benutzerdefinierten neuronalen Stimme aus, um so von der besseren Qualität zu profitieren und um die Stimmen verantwortungsbewusst bereitstellen zu können. 

| Sprache | Gebietsschema | Neuronal | Sprachübergreifend |
|--|--|--|--|
| Arabisch (Ägypten) | `ar-EG` | Ja | Nein |
| Bulgarisch (Bulgarien) | `bg-BG` | Ja | Nein |
| Chinesisch (Mandarin, vereinfacht) | `zh-CN` | Ja | Ja |
| Chinesisch (Mandarin, vereinfacht), zweisprachig mit Englisch | `zh-CN` (zweisprachig) | Ja | Ja |
| Chinesisch (Taiwanesisch, Mandarin) | `zh-TW` | Ja | Nein |
| Tschechisch (Tschechische Republik) | `cs-CZ` | Ja | Nein |
| Niederländisch (Niederlande) | `nl-NL` | Ja | Nein |
| Englisch (Australien) | `en-AU` | Ja | Ja |
| Englisch (Kanada) | `en-CA` | Ja | Nein |
| Englisch (Indien) | `en-IN` | Ja | Nein |
| Englisch (Irland) | `en-IE` | Ja | Nein |
| Walisisch (Großbritannien) | `en-GB` | Ja | Ja |
| Englisch (USA) | `en-US` | Ja | Ja |
| Französisch (Kanada) | `fr-CA` | Ja | Ja |
| Französisch (Frankreich) | `fr-FR` | Ja | Ja |
| Deutsch (Österreich) | `de-AT` | Ja | Nein |
| Deutsch (Deutschland) | `de-DE` | Ja | Ja |
| Ungarisch (Ungarn) | `hu-HU` | Ja | Nein |
| Italienisch (Italien) | `it-IT` | Ja | Ja |
| Japanisch (Japan) | `ja-JP` | Ja | Ja |
| Koreanisch (Korea) | `ko-KR` | Ja | Ja |
| Norwegisch, Bokmål (Norwegen) | `nb-NO` | Ja | Nein |
| Portugiesisch (Brasilien) | `pt-BR` | Ja | Ja |
| Portugiesisch (Portugal) | `pt-PT` | Ja | Nein |
| Russisch (Russische Föderation) | `ru-RU` | Ja | Ja |
| Slowakisch (Slowakei) | `sk-SK` | Ja | Nein |
| Spanisch (Mexiko) | `es-MX` | Ja | Ja |
| Spanisch (Spanien) | `es-ES` | Ja | Ja |
| Türkisch (Türkei) | `tr-TR` | Ja | Nein |
| Vietnamesisch (Vietnam) | `vi-VN` | Ja | Nein |

Wählen Sie das richtige Gebietsschema, das den Trainingsdaten entspricht, die Sie für das Training eines benutzerdefinierten Sprachmodells benötigen. Wenn die von Ihnen aufgenommenen Daten beispielsweise auf Englisch mit britischem Akzent gesprochen werden, wählen Sie `en-GB`.

> [!NOTE]
> Wir unterstützen kein zweisprachiges Modelltraining in Custom Voice, mit Ausnahme des zweisprachigen Chinesisch-Englisch. Wählen Sie „Chinesisch-Englisch zweisprachig“, wenn Sie eine chinesische Stimme trainieren möchten, die auch Englisch sprechen kann. Zweisprachiges Modelltraining für Chinesisch-Englisch mithilfe der Standardmethode ist nur in den Regionen „Europa, Norden“ und „USA, Norden-Mitte“ verfügbar. Training mit dem Feature „Benutzerdefinierte neuronale Stimme“ ist in den Regionen „Vereinigtes Königreich, Süden“ und „USA, Osten“ verfügbar.

## <a name="speech-translation"></a>Sprachübersetzung

Die **Sprachübersetzungs**-API unterstützt verschiedene Sprachen für die Übersetzung von Sprache in Sprache und Sprache in Text. Die Ausgangssprache muss immer eine der Sprachen aus der Sprachentabelle für die Spracherkennung sein. Welche Zielsprachen verfügbar sind, richtet sich danach, ob es sich beim Übersetzungsziel um Sprache oder Text handelt. Sie können Spracheingaben in jede der [unterstützten Sprachen](https://www.microsoft.com/translator/business/languages/) übersetzen. Eine Teilmenge der Sprachen steht für die [Sprachsynthese](language-support.md#text-languages) zur Verfügung.

### <a name="text-languages"></a>Sprachen für Textausgabe

| Sprache für Textausgabe           | Sprachcode |
|:------------------------|:-------------:|
| Afrikaans | `af` |
| Albanisch | `sq` |
| Amharisch | `am` |
| Arabisch | `ar` |
| Armenisch | `hy` |
| Assamesisch | `as` |
| Aserbaidschanisch | `az` |
| Bengalisch | `bn` |
| Bosnisch (Lateinisch) | `bs` |
| Bulgarisch | `bg` |
| Chinesisch (traditionell) | `yue` |
| Katalanisch | `ca` |
| Chinesisch (literarisch) | `lzh` |
| Chinesisch (vereinfacht) | `zh-Hans` |
| Chinesisch (traditionell) | `zh-Hant` |
| Kroatisch | `hr` |
| Tschechisch | `cs` |
| Dänisch | `da` |
| Dari | `prs` |
| Niederländisch | `nl` |
| Englisch | `en` |
| Estnisch | `et` |
| Fidschi | `fj` |
| Filipino | `fil` |
| Finnisch | `fi` |
| Französisch | `fr` |
| Französisch (Kanada) | `fr-ca` |
| Deutsch | `de` |
| Griechisch | `el` |
| Gujarati | `gu` |
| Haitianisches Kreolisch | `ht` |
| Hebräisch | `he` |
| Hindi | `hi` |
| Hmong Daw | `mww` |
| Ungarisch | `hu` |
| Isländisch | `is` |
| Indonesisch | `id` |
| Inuktitut | `iu` |
| Irisch | `ga` |
| Italienisch | `it` |
| Japanisch | `ja` |
| Kannada | `kn` |
| Kasachisch | `kk` |
| Khmer | `km` |
| Klingonisch | `tlh-Latn` |
| Klingonisch (plqaD) | `tlh-Piqd` |
| Koreanisch | `ko` |
| Kurdisch (zentral) | `ku` |
| Kurdisch (nördlich) | `kmr` |
| Laotisch | `lo` |
| Lettisch | `lv` |
| Litauisch | `lt` |
| Madagassisch | `mg` |
| Malaiisch | `ms` |
| Malayalam | `ml` |
| Maltesisch | `mt` |
| Maori | `mi` |
| Marathi | `mr` |
| Myanmar | `my` |
| Nepalesisch | `ne` |
| Norwegisch | `nb` |
| Odia | `or` |
| Paschtu | `ps` |
| Persisch | `fa` |
| Polnisch | `pl` |
| Portugiesisch (Brasilien) | `pt` |
| Portugiesisch (Portugal) | `pt-pt` |
| Pandschabi | `pa` |
| Queretaro-Otomi | `otq` |
| Rumänisch | `ro` |
| Russisch | `ru` |
| Samoanisch | `sm` |
| Serbisch (Kyrillisch) | `sr-Cyrl` |
| Serbisch (Lateinisch) | `sr-Latn` |
| Slowakisch | `sk` |
| Slowenisch | `sl` |
| Spanisch | `es` |
| Suaheli | `sw` |
| Schwedisch | `sv` |
| Tahitisch | `ty` |
| Tamilisch | `ta` |
| Telugu | `te` |
| Thailändisch | `th` |
| Tigrinya | `ti` |
| Tongaisch | `to` |
| Türkisch | `tr` |
| Ukrainisch | `uk` |
| Urdu | `ur` |
| Vietnamesisch | `vi` |
| Walisisch | `cy` |
| Yukatekisches Maya | `yua` |

## <a name="speaker-recognition"></a>Sprechererkennung

Die Sprechererkennung ist größtenteils sprachunabhängig. Wir haben ein universelles Modell zur textunabhängigen Sprechererkennung entwickelt, indem wir verschiedene Datenquellen aus mehreren Sprachen kombiniert haben. Wir haben das Modell für die in der folgenden Tabelle aufgeführten Sprachen und Gebietsschemas angepasst und ausgewertet. Weitere Informationen zur Sprechererkennung finden Sie in der [Übersicht](speaker-recognition-overview.md).

| Sprache | Gebietsschema (BCP-47) | Textabhängige Überprüfung | Textunabhängige Überprüfung | Textunabhängige Identifikation |
|----|----|----|----|----|
|Englisch (USA)  |  `en-US`  |  ja  |  Ja  |  ja |
|Chinesisch (Mandarin, vereinfacht) | `zh-CN`     |     – |     Ja |     ja|
|Englisch (Australien)     | `en-AU`    | –     | Ja     | ja|
|Englisch (Kanada)     | `en-CA`     | – |     Ja |     Ja|
|Englisch (Indien)     | `en-IN`     | – |     Ja |     ja|
|English (UK)     | `en-GB`     | –     | Ja     | ja|
|Französisch (Kanada)     | `fr-CA`     | –     | Ja |     ja|
|Französisch (Frankreich)     | `fr-FR`     | –     | Ja     | ja|
|Deutsch (Deutschland)     | `de-DE`     | –     | Ja     | ja|
|Italienisch | `it-IT`     |     –     | Ja |     ja|
|Japanisch     | `ja-JP` | –     | Ja     | ja|
|Portugiesisch (Brasilien) | `pt-BR` |     – |     Ja |     ja|
|Spanisch (Mexiko)     | `es-MX`     | – |     Ja |     ja|
|Spanisch (Spanien)     | `es-ES` | –     | Ja |     Ja|

## <a name="custom-keyword-and-keyword-verification"></a>Benutzerdefiniertes Schlüsselwort und Schlüsselwortüberprüfung

In der folgenden Tabelle werden die unterstützten Sprachen für das benutzerdefinierte Schlüsselwort und die Schlüsselwortüberprüfung beschrieben.

| Sprache | Gebietsschema (BCP-47) | Benutzerdefiniertes Schlüsselwort | Schlüsselwortüberprüfung |
| -------- | --------------- | -------------- | -------------------- |
| Chinesisch (Mandarin, vereinfacht) | zh-CN | Ja | Ja |
| Englisch (USA) | de-DE | Ja | Ja |
| Japanisch (Japan) | ja-JP | Nein | Ja |
| Portugiesisch (Brasilien) | pt-BR | Nein | Ja |

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
* [Erkennen von Sprache in C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
