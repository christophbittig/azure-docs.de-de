---
title: 'Sprachunterstützung: Formularerkennung'
titleSuffix: Azure Applied AI Services
description: Hier erfahren Sie mehr über die menschlichen Sprachen, die in der Formularerkennung verfügbar sind.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: b9cd899516188dbac4671addd11d9c6cd05fc843
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216654"
---
# <a name="language-support-for-form-recognizer"></a>Sprachunterstützung für die Formularerkennung

 In der folgenden Tabelle sind die Schriftsprachen aufgeführt, die vom Formularerkennungsdienst unterstützt werden:

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

## <a name="layout-and-custom-model"></a>Layout und benutzerdefiniertes Modell

In den folgenden Listen werden die derzeit allgemein verfügbaren Sprachen in Version 2.1 und neue Vorschauversionen in der Vorschauversion 3.0 der Formularerkennung behandelt. Diese Sprachen werden von Layoutmodellen und benutzerdefinierten Modellen unterstützt. Das Vorschaurelease enthält unter Umständen Verbesserungen an den derzeit allgemein verfügbaren Sprachen.

> [!NOTE]
> **Sprachcode optional**
>
> Die auf Deep Learning basierenden universellen Modelle der Formularerkennung extrahieren den gesamten mehrsprachigen Text in Ihren Dokumenten (einschließlich Textzeilen mit gemischten Sprachen) und erfordern keine Angabe eines Sprachcodes. Geben Sie den Sprachcode nicht als Parameter an, es sei denn, Sie sind sich hinsichtlich der Sprache sicher und möchten erzwingen, dass der Dienst nur das relevante Modell anwendet. Andernfalls gibt der Dienst möglicherweise unvollständigen und fehlerhaften Text zurück.

Wenn Sie die Vorschausprachen in Layoutmodellen und benutzerdefinierten Modellen verwenden möchten, finden Sie im [Migrationshandbuch für die v3.0-REST-API](/rest/api/media/#changes-to-the-rest-api-endpoints) Informationen zu den Unterschieden zur allgemein verfügbaren v2.1-API. Sehen Sie sich außerdem die Artikel [Schnellstart: Python-Clientbibliothek-SDKs für Formularerkennung v3.0 | Vorschau](quickstarts/try-v3-python-sdk.md) und [Erste Schritte: REST-API für Formularerkennung | Vorschau](quickstarts/try-v3-rest-api.md) an.

### <a name="handwritten-languages"></a>Handschriftliche Sprachen
In der folgenden Tabelle sind die handschriftlichen Sprachen aufgeführt, die von den Formularerkennungsfunktionen für Layoutmodelle und benutzerdefinierte Modelle unterstützt werden.

|Sprache| Sprachcode (optional) | Vorschau?  |
|:-----|:----:|:----:|
|Englisch|`en`||
|Chinesisch (vereinfacht) |`zh-Hans`| preview
|Französisch |`fr`| preview
|Deutsch |`de`| preview
|Italienisch|`it`| preview
|Portugiesisch |`pt`| preview
|Spanisch |`es`| preview

### <a name="print-languages"></a>Drucksprachen
In der folgenden Tabelle sind die Drucksprachen aufgeführt, die von den Formularerkennungsfunktionen für Layoutmodelle und benutzerdefinierte Modelle unterstützt werden.

|Sprache| Sprachcode (optional) | Vorschau? |
|:-----|:----:|:----:|
|Afrikaans|`af`||
|Albanisch |`sq`||
|Asturisch |`ast`| |
|Aserbaidschanisch (Lateinisch) | `az` | preview |
|Baskisch  |`eu`| |
|Belarussisch (Kyrillisch) | `be` | preview |
|Belarussisch (Lateinisch) | `be` | preview |
|Bislama   |`bi`| |
|Bosnisch (Lateinisch)   |`bs`| preview |
|Bretonisch    |`br`| |
|Bulgarisch |`bg`| preview |
|Burjatisch (Kyrillisch)|`bua`| preview |
|Katalanisch    |`ca`| |
|Cebuano    |`ceb`| |
|Chamorro  |`ch`| |
|Chinesisch (vereinfacht) | `zh-Hans`| |
|Chinesisch (traditionell) | `zh-Hant`| |
|Kornisch     |`kw`| |
|Korsisch      |`co`| |
|Krimtatarisch (Lateinisch)|`crh`| |
|Kroatisch |`hr`| preview |
|Tschechisch | `cs` | |
|Dänisch | `da` | |
|Niederländisch | `nl` | |
|Englisch | `en` | |
|Ersjanisch (Kyrillisch) |`myv`| preview |
|Estnisch  |`et`|  |
|Färöisch |`fo`| preview |
|Fidschi |`fj`| |
|Filipino  |`fil`| |
|Finnisch | `fi` | |
|Französisch | `fr` | |
|Friaulisch  | `fur` | |
|Gagausisch (Lateinisch) |`gag`| preview |
|Galizisch   | `gl` | |
|Deutsch | `de` | |
|Gilbertese    | `gil` | |
|Grönländisch   | `kl` | |
|Haitianisches Kreolisch  | `ht` | |
|Hani  | `hni` | |
|Hawaiianisch |`haw`| preview |
|Hmong Daw (Lateinisch)| `mww` | |
|Ungarisch | `hu` | |
|Isländisch |`is`| preview |
|Inari-Samisch |`smn`| preview |
|Indonesisch   | `id` | |
|Interlingual  | `ia` | |
|Inuktitut (Lateinisch) | `iu` | |
|Irisch    | `ga` | |
|Italienisch | `it` | |
|Japanisch | `ja` | |
|Javanisch | `jv` | |
|K'iche'  | `quc` | |
|Kabuverdianu | `kea` | |
|Kachin (Lateinisch) | `kac` | |
|Karatschai-Balkarisch |`krc`| preview |
|Karakalpakisch (Lateinisch) | `kaa` | |
|Karakalpakisch (Kyrillisch) | `kaa-cyrl` | preview |
|Kaschubisch | `csb` | |
|Kasachisch (Kyrillisch) |`kk-cyrl`| preview |
|Kasachisch (Lateinisch) |`kk-latn`| preview |
|Khasi  | `kha` |  |
|Koreanisch | `ko` | |
|Korjakisch |`kpy`| preview |
|Kosraeanisch |`kos`| preview |
|Kumykisch (Kyrillisch) |`kum`| preview |
|Kurdisch (lateinisch)| `ku` | |
|Kirgisisch (Kyrillisch) |`ky`| preview |
|Lakota |`lkt`| preview |
|Lateinisch|`la`| preview |
|Litauisch|`lt`| preview |
|Niedersorbisch|`dsb`| preview |
|Lule-Samisch|`smj`| preview |
|Luxemburgisch  | `lb` |  |
|Malaiisch (Lateinisch) | `ms` |  |
|Maltesisch|`mt`| preview |
|Manx  | `gv` |  |
|Maori|`mi`| preview |
|Mongolisch (Kyrillisch)|`mn`| preview |
|Montenegrinisch (Kyrillisch)|`cnr-cyrl`| preview |
|Montenegrinisch (Lateinisch)|`cnr-latn`| preview |
|Neapolitanisch   | `nap` | |
|Niue|`niu`| preview |
|Nogaisch|`nog`| preview |
|Nordsamisch (Lateinisch)|`sme`| preview |
|Norwegisch | `no` |  |
|Okzitanisch | `oc` | |
|Ossetisch|`os`| preview |
|Polnisch | `pl` | |
|Portugiesisch | `pt` | |
|Ripuarisch|`ksh`| preview |
|Rumänisch | `ro` | preview |
|Rätoromanisch  | `rm` | |
|Russisch | `ru` | preview |
|Samoanisch (Lateinisch)|`sm`| preview |
|Schotten  | `sco` | |
|Schottisch-Gälisch  | `gd` | |
|Serbisch (Lateinisch) | `sr-latn` | preview |
|Skolt-Samisch|`sms`| preview |
|Slowakisch | `sk` | preview |
|Slowenisch  | `sl` | |
|Südsamisch|`sma`| preview |
|Spanisch | `es` | |
|Suaheli (Lateinisch)  | `sw` | |
|Schwedisch | `sv` | |
|Tadschikisch (Kyrillisch)|`tg`| preview |
|Tatarisch (Lateinisch)  | `tt` | |
|Tetum    | `tet` |  |
|Tongaisch|`to`|(Vorschauversion) |
|Türkisch | `tr` | |
|Turkmenisch (Lateinisch)|`tk`| preview |
|Tuwinisch|`tyv`| preview |
|Obersorbisch  | `hsb` | |
|Usbekisch (kyrillisch)  | `uz-cyrl` |  |
|Usbekisch (Lateinisch)     | `uz` |  |
|Volapük   | `vo` | |
|Walser    | `wae` |  |
|Walisisch     | `cy` | preview |
|Westfriesisch | `fy` |  |
|Yukatekisches Maya | `yua` |  |
|Zhuang | `za` | |
|Zulu  | `zu` |  |

## <a name="receipt-and-business-card-models"></a>Beleg- und Visitenkartenmodelle

>[!NOTE]
 > Es ist nicht erforderlich, ein Gebietsschema anzugeben. Dies ist ein optionaler Parameter. Die Deep Learning-Technologie der Formularerkennung erkennt automatisch die Sprache des Texts in Ihrem Bild.

Vordefinierte Belege und Visitenkarten unterstützen alle englischen Belege und Visitenkarten mit den folgenden Gebietsschemas:

|Sprache| Code des Gebietsschemas |
|:-----|:----:|
|Englisch (Australien)|`en-au`|
|Englisch (Kanada)|`en-ca`|
|Walisisch (Großbritannien)|`en-gb`|
|Englisch (Indien)|`en-in`|
|Englisch (USA)| `en-us`|

## <a name="invoice-model"></a>Rechnungsmodell

Sprache| Code des Gebietsschemas |
|:-----|:----:|
|Englisch (USA)|de-de|

## <a name="id-documents"></a>Ausweisdokumente

Diese Technologie ist derzeit für US-Führerscheine und die biografische Seite von internationalen Reisepässen (ohne Visa und andere Reisedokumente) verfügbar.

> [!div class="nextstepaction"]
> [Formularerkennung ausprobieren](https://aka.ms/fott-2.1-ga)

## <a name="general-document"></a>Allgemeines Dokument

Sprache| Code des Gebietsschemas |
|:-----|:----:|
|Englisch (USA)|de-de|

