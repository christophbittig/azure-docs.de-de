---
title: Sprachunterstützung für Erkennung benannter Entitäten (NER)
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird erläutert, welche natürlichen Sprachen vom NER-Erkennungsfeature von Azure Cognitive Service für Language unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 7f80599fe9b0a43b7b472bd6eff71ef11b62fe13
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095958"
---
# <a name="named-entity-recognition-ner-language-support"></a>Sprachunterstützung für Erkennung benannter Entitäten (NER) 

Verwenden Sie diesem Artikel, um zu erfahren, welche natürlichen Sprachen vom NER-Feature von Azure Cognitive Service für Language unterstützt werden.

> [!NOTE]
> * Sprachen werden hinzugefügt, wenn neue [Modellversionen](how-to-call.md#specify-the-ner-model) für bestimmte Features veröffentlicht werden. 
> * Nur die Entitäten „Person“, „Standort“ und „Organisation“ werden für mit einem * gekennzeichnete Sprachen zurückgegeben.
> * Die aktuelle Modellversion für NER ist `2021-06-01`.

## <a name="ner-language-support"></a>NER-Sprachunterstützung

| Sprache              | Sprachcode | Mit der Modellversion beginnen: | Notizen              |
|:----------------------|:-------------:|:----------------------------:|:------------------:|
| Arabisch*               | `ar`          | 2019-10-01                   |                    |
| Chinesisch (vereinfacht)    | `zh-hans`     | 15.1.2021                   | `zh` wird ebenfalls akzeptiert. |
| Chinesisch (traditionell)*  | `zh-hant`     | 2019-10-01                   |                    |
| Tschechisch*                | `cs`          | 2019-10-01                   |                    |
| Dänisch*               | `da`          | 2019-10-01                   |                    |
| Niederländisch*                | `nl`          | 2019-10-01                   |                    |
| Englisch               | `en`          | 2019-10-01                   |                    |
| Finnisch*              | `fi`          | 2019-10-01                   |                    |
| Französisch                | `fr`          | 15.1.2021                   |                    |
| Deutsch                | `de`          | 15.1.2021                   |                    |
| Hebräisch*               | `he`          | 2019-10-01                   |                    |
| Ungarisch*            | `hu`          | 2019-10-01                   |                    |
| Italienisch               | `it`          | 15.1.2021                   |                    |
| Japanisch              | `ja`          | 15.1.2021                   |                    |
| Koreanisch                | `ko`          | 15.1.2021                   |                    |
| Norwegisch  (Bokmål)*  | `no`          | 2019-10-01                   | `nb` wird ebenfalls akzeptiert. |
| Polnisch*               | `pl`          | 2019-10-01                   |                    |
| Portugiesisch (Brasilien)   | `pt-BR`       | 15.1.2021                   |                    |
| Portugiesisch (Portugal) | `pt-PT`       | 15.1.2021                   | `pt` wird ebenfalls akzeptiert. |
| Russisch*              | `ru`          | 2019-10-01                   |                    |
| Spanisch               | `es`          | 2020-04-01                   |                    |
| Schwedisch*              | `sv`          | 2019-10-01                   |                    |
| Türkisch*              | `tr`          | 2019-10-01                   |                    |

## <a name="next-steps"></a>Nächste Schritte

[PII-Featureübersicht](overview.md)
