---
title: Sprachunterstützung für Sprachenerkennung
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird erläutert, welche natürlichen Sprachen von der Sprachenerkennungs-API unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 1c71e3d04ff770aefc50732c18fe49bd684e854a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095961"
---
# <a name="language-support-for-language-detection"></a>Sprachunterstützung für Sprachenerkennung

In diesem Artikel erfahren Sie, welche natürlichen Sprachen von der Sprachenerkennung unterstützt werden.


> [!NOTE]
> Sprachen werden hinzugefügt, wenn neue [Modellversionen](how-to/call-api.md#specify-the-language-detection-model) für bestimmte Features veröffentlicht werden. Die aktuelle Modellversion für die Sprachenerkennung ist `2021-01-05`.

Das Sprachenerkennungsfeature kann eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen erkennen und erkannte Sprachen mit Name und Code zurückgeben. Die zurückgegebenen Sprachcodeparameter entsprechen dem Standard [BCP-47](https://tools.ietf.org/html/bcp47), und der Großteil ist mit [ISO-639-1](https://www.iso.org/iso-639-language-codes.html)-Bezeichnern konform. 

Bei Inhalten in einer seltener verwendeten Sprache können Sie die Sprachenerkennung ausprobieren, um zu sehen, ob sie einen Code zurückgibt. Die Antwort bei Sprachen, die nicht erkannt werden können, lautet `unknown`.

## <a name="languages-supported-by-language-detection"></a>Von der Sprachenerkennung unterstützte Sprachen

| Sprache            | Sprachcode | Mit der Modellversion beginnen: |
|---------------------|---------------|------------------------------|
| Afrikaans           | `af`          |                              |
| Albanisch            | `sq`          |                              |
| Amharisch             | `am`          | 5\.1.2021                   |
| Arabisch              | `ar`          |                              |
| Armenisch            | `hy`          |                              |
| Assamesisch            | `as`          | 5\.1.2021                   |
| Aserbaidschanisch         | `az`          | 5\.1.2021                   |
| Baskisch              | `eu`          |                              |
| Belarussisch          | `be`          |                              |
| Bengali             | `bn`          |                              |
| Bosnisch             | `bs`          | 2020-09-01                   |
| Bulgarisch           | `bg`          |                              |
| Birmanisch             | `my`          |                              |
| Katalanisch             | `ca`          |                              |
| Khmer       | `km`          |                              |
| Chinesisch             | `zh`          |                              |
| Chinesisch (vereinfacht)  | `zh_chs`      |                              |
| Chinesisch (traditionell) | `zh_cht`      |                              |
| Korsisch            | `co`          | 5\.1.2021                   |
| Kroatisch            | `hr`          |                              |
| Tschechisch               | `cs`          |                              |
| Dänisch              | `da`          |                              |
| Dari                | `prs`         | 2020-09-01                   |
| Divehi              | `dv`          |                              |
| Niederländisch               | `nl`          |                              |
| Englisch             | `en`          |                              |
| Esperanto           | `eo`          |                              |
| Estnisch            | `et`          |                              |
| Fidschi              | `fj`          | 2020-09-01                   |
| Finnisch             | `fi`          |                              |
| Französisch              | `fr`          |                              |
| Galizisch            | `gl`          |                              |
| Georgisch            | `ka`          |                              |
| Deutsch              | `de`          |                              |
| Griechisch               | `el`          |                              |
| Gujarati            | `gu`          |                              |
| Haiti-Kreolisch             | `ht`          |                              |
| Haussa               | `ha`          | 5\.1.2021                   |
| Hebräisch              | `he`          |                              |
| Hindi               | `hi`          |                              |
| Hmong Daw           | `mww`         | 2020-09-01                   |
| Ungarisch           | `hu`          |                              |
| Isländisch           | `is`          |                              |
| Igbo                | `ig`          | 5\.1.2021                   |
| Indonesisch          | `id`          |                              |
| Inuktitut           | `iu`          |                              |
| Irisch               | `ga`          |                              |
| Italienisch             | `it`          |                              |
| Japanisch            | `ja`          |                              |
| Javanisch            | `jv`          | 5\.1.2021                   |
| Kannada             | `kn`          |                              |
| Kasachisch              | `kk`          | 2020-09-01                   |
| Kinyarwanda         | `rw`          | 5\.1.2021                   |
| Kirgisisch             | `ky`          | 5\.1.2021                   |
| Koreanisch              | `ko`          |                              |
| Kurdisch             | `ku`          |                              |
| Laotisch                 | `lo`          |                              |
| Lateinisch               | `la`          |                              |
| Lettisch             | `lv`          |                              |
| Litauisch          | `lt`          |                              |
| Luxemburgisch       | `lb`          | 5\.1.2021                   |
| Mazedonisch          | `mk`          |                              |
| Madagassisch            | `mg`          | 2020-09-01                   |
| Malaiisch               | `ms`          |                              |
| Malayalam           | `ml`          |                              |
| Maltesisch             | `mt`          |                              |
| Maori               | `mi`          | 2020-09-01                   |
| Marathi             | `mr`          | 2020-09-01                   |
| Mongolisch           | `mn`          | 5\.1.2021                   |
| Nepalesisch              | `ne`          | 5\.1.2021                   |
| Norwegisch           | `no`          |                              |
| Norwegisch Nynorsk   | `nn`          |                              |
| Oriya               | `or`          |                              |
| Paschto               | `ps`          |                              |
| Persisch             | `fa`          |                              |
| Polnisch              | `pl`          |                              |
| Portugiesisch          | `pt`          |                              |
| Pandschabi             | `pa`          |                              |
| Queretaro-Otomi     | `otq`         | 2020-09-01                   |
| Rumänisch            | `ro`          |                              |
| Russisch             | `ru`          |                              |
| Samoanisch              | `sm`          | 2020-09-01                   |
| Serbisch             | `sr`          |                              |
| Shona               | `sn`          | 5\.1.2021                   |
| Sindhi              | `sd`          | 5\.1.2021                   |
| Singhalesisch             | `si`          |                              |
| Slowakisch              | `sk`          |                              |
| Slowenisch           | `sl`          |                              |
| Somali              | `so`          |                              |
| Spanisch             | `es`          |                              |
| Sundanesisch           | `su`          | 5\.1.2021                   |
| Suaheli             | `sw`          |                              |
| Schwedisch             | `sv`          |                              |
| Tagalog             | `tl`          |                              |
| Tahitisch            | `ty`          | 2020-09-01                   |
| Tadschikisch               | `tg`          | 5\.1.2021                   |
| Tamilisch               | `ta`          |                              |
| Tatarisch               | `tt`          | 5\.1.2021                   |
| Telugu              | `te`          |                              |
| Thailändisch                | `th`          |                              |
| Tibetisch             | `bo`          | 5\.1.2021                   |
| Tigrinya            | `ti`          | 5\.1.2021                   |
| Tongaisch              | `to`          | 2020-09-01                   |
| Türkisch             | `tr`          | 5\.1.2021                   |
| Turkmenisch             | `tk`          | 5\.1.2021                   |
| Ukrainisch           | `uk`          |                              |
| Urdu                | `ur`          |                              |
| Usbekisch               | `uz`          |                              |
| Vietnamesisch          | `vi`          |                              |
| Walisisch               | `cy`          |                              |  
| Xhosa               | `xh`          | 5\.1.2021                   |
| Jiddisch             | `yi`          |                              |
| Yoruba              | `yo`          | 5\.1.2021                   |
| Yukatekisches Maya        | `yua`         |                              |  
| Zulu                | `zu`          | 5\.1.2021                   |

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Sprachenerkennung](overview.md)
