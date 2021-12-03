---
title: Sprachunterstützung – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: 'Dieser Artikel enthält eine Liste der natürlichen Sprachen, die von Features für maschinelles Sehen unterstützt werden: OCR, Bildanalyse.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: a79954ba0a96f6db6f12af13c6c74d7386e88231
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473412"
---
# <a name="language-support-for-computer-vision"></a>Sprachunterstützung für maschinelles Sehen

Einige Funktionen des maschinellen Sehens unterstützen mehrere Sprachen; alle hier nicht erwähnten Funktionen unterstützen nur Englisch.

## <a name="optical-character-recognition-ocr"></a>Optische Zeichenerkennung (OCR)

Die OCR-APIs für maschinelles Sehen unterstützen viele Sprachen. Read kann Text aus Bildern und Dokumenten mit gemischten Sprachen extrahieren, auch wenn sie in derselben Textzeile vorkommen, ohne dass ein Sprachparameter erforderlich ist. Weitere Informationen finden Sie in der [Übersicht über die optische Zeichenerkennung (OCR)](overview-ocr.md).


> [!NOTE]
> **Sprachcode (optional)**
>
> Die auf Deep Learning basierenden universellen Modelle der Read-OCR extrahieren den gesamten mehrsprachigen Text in Ihren Dokumenten (einschließlich Textzeilen mit gemischten Sprachen) und erfordern keine Angabe eines Sprachcodes. Geben Sie den Sprachcode nicht als Parameter an, es sei denn, Sie sind sich hinsichtlich der Sprache sicher und möchten erzwingen, dass der Dienst nur das relevante Modell anwendet. Andernfalls gibt der Dienst möglicherweise unvollständigen und fehlerhaften Text zurück.

Das Vorschaumodell enthält alle Verbesserungen an der derzeit allgemein verfügbaren Version der API. Informationen zu den Sprachen und Features während der Vorschau finden Sie unter [Angeben der Modellversion](./Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional). 

### <a name="handwritten-languages"></a>Handschriftliche Sprachen

In der folgenden Tabelle sind die Sprachen aufgeführt, die von Read für handschriftlichen Text unterstützt werden.

|Sprache| Sprachcode (optional) | Lesen |
|:-----|:----:|:-----|
|Englisch|`en`|✅ |
|Chinesisch (vereinfacht) |`zh-Hans`|✅ Vorschau |
|Französisch|`fr`|✅ Vorschau|
|Deutsch |`de`|✅ Vorschau |
|Italienisch|`it`|✅ Vorschau |
|Portugiesisch |`pt`|✅ Vorschau |
|Spanisch |`es`|✅ Vorschau |

### <a name="print-languages"></a>Drucksprachen

In der folgenden Tabelle sind die Sprachen aufgeführt, die von den OCR-APIs für gedruckten Text unterstützt werden.

|Sprache| Sprachcode (optional) | Lesen | OCR |
|:-----|:----:|:-----|:---:|
|Afrikaans|`af`|✅ | |
|Albanisch |`sq`|✅ | |
|Arabisch | `ar`|  | ✅ |
|Asturisch |`ast`|✅ | |
|Aserbaidschanisch (Lateinisch) | `az` | ✅ Vorschau | |
|Baskisch  |`eu`| ✅ | |
|Belarussisch (Kyrillisch) | `be` |✅ Vorschau | |
|Belarussisch (Lateinisch) | `be` |✅ Vorschau | |
|Bislama   |`bi`|✅ | |
|Bosnisch (Lateinisch)   |`bs`|✅ Vorschau | |
|Bretonisch    |`br`|✅ | |
|Bulgarisch |`bg`|✅ Vorschau | |
|Burjatisch (Kyrillisch)|`bua`|✅ Vorschau | |
|Katalanisch    |`ca`|✅ | |
|Cebuano    |`ceb`|✅ | |
|Chamorro  |`ch`|✅| |
|Chinesisch (vereinfacht) | `zh-Hans`|✅ |✅ |
|Chinesisch (traditionell) | `zh-Hant`|✅ |✅ |
|Kornisch     |`kw`|✅ | |
|Korsisch      |`co`|✅ | |
|Krimtatarisch (Lateinisch)|`crh`| ✅ | |
|Kroatisch |`hr`|✅ Vorschau | |
|Tschechisch | `cs` |✅ | ✅ |
|Dänisch | `da` |✅ | ✅ |
|Niederländisch | `nl` |✅ |✅ |
|Englisch | `en` |✅ |✅|
|Ersjanisch (Kyrillisch) |`myv`|✅ Vorschau | |
|Estnisch  |`et`|✅ | |
|Färöisch |`fo`|✅ Vorschau | |
|Fidschi |`fj`|✅ | |
|Filipino  |`fil`|✅ | |
|Finnisch | `fi` |✅ |✅ |
|Französisch | `fr` |✅ |✅ |
|Friaulisch  | `fur` |✅ | |
|Gagausisch (Lateinisch) |`gag`|✅ Vorschau | |
|Galizisch   | `gl` |✅ | |
|Deutsch | `de` |✅ |✅ |
|Gilbertese    | `gil` |✅ | |
|Griechisch | `el` | |✅ |
|Grönländisch   | `kl` |✅ | |
|Haitianisches Kreolisch  | `ht` |✅ | |
|Hani  | `hni` |✅ | |
|Hawaiianisch |`haw`|✅ Vorschau | |
|Hmong Daw (Lateinisch)| `mww` | ✅ | |
|Ungarisch | `hu` | ✅ |✅ |
|Isländisch |`is`|✅ Vorschau | |
|Inari-Samisch |`smn`|✅ Vorschau | |
|Indonesisch   | `id` |✅ | |
|Interlingual  | `ia` |✅ | |
|Inuktitut (Lateinisch) | `iu` | ✅ | |
|Irisch    | `ga` |✅ | |
|Italienisch | `it` |✅ |✅ |
|Japanisch | `ja` |✅ |✅ |
|Javanisch | `jv` |✅ | |
|K'iche'  | `quc` |✅ | |
|Kabuverdianu | `kea` |✅ | |
|Kachin (Lateinisch) | `kac` |✅ | |
|Karakalpakisch (Lateinisch) | `kaa` | ✅ | |
|Karakalpakisch (Kyrillisch) | `kaa-cyrl` | ✅ Vorschau | |
|Karatschai-Balkarisch |`krc`|✅ Vorschau | |
|Kaschubisch | `csb` |✅ | |
|Kasachisch (Kyrillisch) |`kk-cyrl`|✅ Vorschau | |
|Kasachisch (Lateinisch) |`kk-latn`|✅ Vorschau | |
|Khasi  | `kha` | ✅ | |
|Koreanisch | `ko` |✅ |✅ |
|Korjakisch |`kpy`|✅ Vorschau | |
|Kosraeanisch |`kos`|✅ Vorschau | |
|Kumykisch (Kyrillisch) |`kum`|✅ Vorschau | |
|Kurdisch (lateinisch)| `ku` |✅ | |
|Kirgisisch (Kyrillisch) |`ky`|✅ Vorschau | |
|Lakota |`lkt`|✅ Vorschau | |
|Lateinisch|`la`|✅ Vorschau | |
|Litauisch|`lt`|✅ Vorschau | |
|Niedersorbisch|`dsb`|✅ Vorschau | |
|Lule-Samisch|`smj`|✅ Vorschau | |
|Luxemburgisch  | `lb` | ✅ | |
|Malaiisch (Lateinisch) | `ms` | ✅ | |
|Maltesisch|`mt`|✅ Vorschau | |
|Manx  | `gv` | ✅ | |
|Maori|`mi`|✅ Vorschau | |
|Mongolisch (Kyrillisch)|`mn`|✅ Vorschau | |
|Montenegrinisch (Kyrillisch)|`cnr-cyrl`|✅ Vorschau | |
|Montenegrinisch (Lateinisch)|`cnr-latn`|✅ Vorschau | |
|Neapolitanisch   | `nap` | ✅ | |
|Niue|`niu`|✅ Vorschau | |
|Nogaisch|`nog`|✅ Vorschau | |
|Nordsamisch (Lateinisch)|`sme`|✅ Vorschau | |
|Norwegisch | `no` | ✅ | |
|Okzitanisch | `oc` | ✅ | |
|Ossetisch|`os`|✅ Vorschau | |
|Polnisch | `pl` | ✅ |✅ |
|Portugiesisch | `pt` |✅ |✅ |
|Ripuarisch|`ksh`|✅ Vorschau | |
|Rumänisch | `ro` | ✅ Vorschau | ✅|
|Rätoromanisch  | `rm` | ✅ | |
|Russisch | `ru` |✅ Vorschau |✅ |
|Samoanisch (Lateinisch)|`sm`|✅ Vorschau | |
|Schotten  | `sco` | ✅ | |
|Schottisch-Gälisch  | `gd` |✅ | |
|Serbisch (Kyrillisch) | `sr-cyrl` | |✅ |
|Serbisch (Lateinisch) | `sr-latn` | ✅ Vorschau |✅ |
|Skolt-Samisch|`sms`|✅ Vorschau | |
|Slowakisch | `sk` | ✅ Vorschau |✅ |
|Slowenisch  | `sl` | ✅ ||
|Südsamisch|`sma`|✅ Vorschau | |
|Spanisch | `es` |✅ |✅ |
|Suaheli (Lateinisch)  | `sw` |✅ | |
|Schwedisch | `sv` |✅ |✅ |
|Tadschikisch (Kyrillisch)|`tg`|✅ Vorschau | |
|Tatarisch (Lateinisch)  | `tt` | ✅ |
|Tetum    | `tet` |✅ |  |
|Tongaisch|`to`|✅ Vorschau | |
|Türkisch | `tr` |✅ | ✅ |
|Turkmenisch (Lateinisch)|`tk`|✅ Vorschau | |
|Tuwinisch|`tyv`|✅ Vorschau | |
|Obersorbisch  | `hsb` |✅ |  |
|Usbekisch (kyrillisch)  | `uz-cyrl` |✅ |  |
|Usbekisch (Lateinisch)     | `uz` |✅ |  |
|Volapük   | `vo` | ✅ | |
|Walser    | `wae` | ✅ | |
|Walisisch     | `cy` |✅ Vorschau |  |
|Westfriesisch | `fy` | ✅ | |
|Yukatekisches Maya | `yua` | ✅ | |
|Zhuang | `za` |✅ |  |
|Zulu  | `zu` | ✅ | |

## <a name="image-analysis"></a>Bildanalyse

Einige Funktionen der [Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-API können Ergebnisse in anderen Sprachen zurückgeben, angegeben mit dem `language`-Abfrageparameter. Andere Aktionen geben unabhängig davon, welche Sprache angegeben ist, Ergebnisse in englischer Sprache zurück, und andere lösen bei nicht unterstützten Sprachen eine Ausnahme aus. Aktionen werden mit dem `visualFeatures`- und `details`-Abfrageparameter angegeben; in der [Übersicht](overview-image-analysis.md) finden Sie eine Liste mit allen Aktionen, die Sie mit der Bildanalyse durchführen können. Sprachen für das Tagging sind nur in der API-Version 3.2 oder höher verfügbar.

|Sprache | Sprachcode | Kategorien | `Tags` | BESCHREIBUNG | Erwachsene | Marken | Color | Gesichtserkennung | ImageType | Objekte | Prominente | Besondere Merkmale |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Arabisch |`ar`| | ✅| |||||| |||
|Azeri (Aserbaidschanisch) |`az`| | ✅| |||||| |||
|Bulgarisch |`bg`| | ✅| |||||| |||
|Bosnisch (Lateinisch) |`bs`| | ✅| |||||| |||
|Katalanisch |`ca`| | ✅| |||||| |||
|Tschechisch |`cs`| | ✅| |||||| |||
|Walisisch |`cy`| | ✅| |||||| |||
|Dänisch |`da`| | ✅| |||||| |||
|Deutsch |`de`| | ✅| |||||| |||
|Griechisch |`el`| | ✅| |||||| |||
|Englisch |`en`|✅ | ✅| ✅|✅|✅|✅|✅|✅|✅|✅|✅|
|Spanisch |`es`|✅ | ✅| ✅|||||| |✅|✅|
|Estnisch |`et`| | ✅| |||||| |||
|Baskisch |`eu`| | ✅| |||||| |||
|Finnisch |`fi`| | ✅| |||||| |||
|Französisch |`fr`| | ✅| |||||| |||
|Irisch |`ga`| | ✅| |||||| |||
|Galizisch |`gl`| | ✅| |||||| |||
|Hebräisch |`he`| | ✅| |||||| |||
|Hindi |`hi`| | ✅| |||||| |||
|Kroatisch |`hr`| | ✅| |||||| |||
|Ungarisch |`hu`| | ✅| |||||| |||
|Indonesisch |`id`| | ✅| |||||| |||
|Italienisch |`it`| | ✅| |||||| |||
|Japanisch  |`ja`|✅ | ✅| ✅|||||| |✅|✅|
|Kasachisch |`kk`| | ✅| |||||| |||
|Koreanisch |`ko`| | ✅| |||||| |||
|Litauisch |`It`| | ✅| |||||| |||
|Lettisch |`Iv`| | ✅| |||||| |||
|Mazedonisch |`mk`| | ✅| |||||| |||
|Malaiisch (Malaysia) |`ms`| | ✅| |||||| |||
|Norwegisch (Bokmål) |`nb`| | ✅| |||||| |||
|Niederländisch |`nl`| | ✅| |||||| |||
|Polnisch |`pl`| | ✅| |||||| |||
|Dari |`prs`| | ✅| |||||| |||
| Portugiesisch (Brasilien)|`pt-BR`| | ✅| |||||| |||
| Portugiesisch (Portugal) |`pt`/`pt-PT`|✅ | ✅| ✅|||||| |✅|✅|
|Rumänisch |`ro`| | ✅| |||||| |||
|Russisch |`ru`| | ✅| |||||| |||
|Slowakisch |`sk`| | ✅| |||||| |||
|Slowenisch |`sl`| | ✅| |||||| |||
|Serbisch (Kyrillisch RS) |`sr-Cryl`| | ✅| |||||| |||
|Serbisch (Lateinisch RS) |`sr-Latn`| | ✅| |||||| |||
|Schwedisch |`sv`| | ✅| |||||| |||
|Thailändisch |`th`| | ✅| |||||| |||
|Türkisch |`tr`| | ✅| |||||| |||
|Ukrainisch |`uk`| | ✅| |||||| |||
|Vietnamesisch |`vi`| | ✅| |||||| |||
|Chinesisch (vereinfacht) |`zh`/ `zh-Hans`|✅ | ✅| ✅|||||| |✅|✅|
|Chinesisch (traditionell) |`zh-Hant`| | ✅| |||||| |||
