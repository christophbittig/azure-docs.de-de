---
title: Sprachunterstützung für Stimmungsanalyse und Opinion Mining
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird erläutert, welche natürlichen Sprachen von Stimmungsanalyse- und Opinion Mining-Features von Azure Cognitive Service für Sprachen unterstützt werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: a0cbc5bab15e80bb18ca3d4a13efa2d05fe5aeff
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027145"
---
# <a name="sentiment-analysis-and-opinion-mining-language-support"></a>Sprachunterstützung für Stimmungsanalyse und Opinion Mining 

In diesem Artikel erfahren Sie, welche natürlichen Sprachen von Stimmungsanalyse und Opinion Mining unterstützt werden.

> [!NOTE]
> Sprachen werden hinzugefügt, wenn neue Modellversionen für bestimmte Features veröffentlicht werden. Die aktuelle allgemein verfügbare Modellversion für die Standpunktanalyse ist `2020-04-01`. Um die neueste Vorschaumodellversion (`2021-10-01-preview`) zu verwenden, geben Sie sie in Ihrem API-Aufruf an. Weitere Informationen finden Sie unter [Aufrufen der API](how-to/call-api.md#specify-the-sentiment-analysis-model).

## <a name="sentiment-analysis-language-support"></a>Sprachunterstützung für Stimmungsanalyse

| Sprache              | Sprachcode | Mit der Modellversion beginnen: |              Notizen |
|:----------------------|:-------------:|:--------------------------:|-------------------:|
| Chinesisch (vereinfacht)    |   `zh-hans`         |         2019-10-01         | `zh` wird ebenfalls akzeptiert. |
| Chinesisch (traditionell)   |   `zh-hant`         |         2019-10-01         |                    |
| Niederländisch                 |     `nl`            |         2019-10-01        |                    |
| Englisch               |     `en`            |         2019-10-01         |                    |
| Französisch                |     `fr`            |         2019-10-01         |                    |
| Deutsch                |     `de`            |         2019-10-01         |                    |
| Hindi                 |    `hi`             |         2020-04-01         |                    |
| Italienisch               |     `it`            |         2019-10-01         |                    |
| Japanisch              |     `ja`            |         2019-10-01         |                    |
| Koreanisch                |     `ko`            |         2019-10-01         |                    |
| Norwegisch (Bokmål)   |     `no`            |         2020-04-01         |                    |
| Portugiesisch (Brasilien)   |    `pt-BR`          |         2020-04-01         |                    |
| Portugiesisch (Portugal) |    `pt-PT`          |         2019-10-01         | `pt` wird ebenfalls akzeptiert. |
| Spanisch               |     `es`            |         2019-10-01         |                    |
| Türkisch               |     `tr`             |         2020-04-01        |                    |

### <a name="opinion-mining-language-support"></a>Sprachunterstützung für Opinion Mining

| Sprache              | Sprachcode | Mit der Modellversion beginnen: |              Notizen |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Englisch               |     `en`      |  2020-04-01              |                    |
| Französisch                |     `fr`      |         2021-10-01-preview         |                    |
| Deutsch                |     `de`      |         2021-10-01-preview         |                    |
| Italienisch               |     `it`      |         2021-10-01-preview         |                    |
| Portugiesisch (Brasilien)   |    `pt-BR`    |         2021-10-01-preview         |                    |
| Portugiesisch (Portugal) |    `pt-PT`    |         2021-10-01-preview         | `pt` wird ebenfalls akzeptiert. |
| Spanisch               |     `es`      |         2021-10-01-preview         |                    |

## <a name="next-steps"></a>Nächste Schritte

[Stimmungsanalyse und Opinion Mining-Übersicht](overview.md)
