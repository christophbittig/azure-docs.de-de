---
title: Docker-Pullvorgang für den Standpunktanalysecontainer
titleSuffix: Azure Cognitive Services
description: Befehl „docker pull“ für den Standpunktanalysecontainer
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/21/2021
ms.author: aahi
ms.openlocfilehash: f836a8313927dd773c432428cdf1b12c0cf00aef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677543"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-Pullvorgang für den Container der Standpunktanalyse v3

Der Container für die Standpunktanalyse v3 ist in mehreren Sprachen verfügbar. Verwenden Sie den folgenden Befehl, um den Container für Englisch herunterzuladen. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Wenn Sie den Container für eine andere Sprache herunterladen möchten, ersetzen Sie `en` durch einen der folgenden Sprachcodes: 

| Textanalysecontainer | Sprachcode |
|--|--|
| Chinesisch (vereinfacht)    |   `zh-hans`   |
| Chinesisch (traditionell)   |   `zh-hant`   |
| Niederländisch                 |     `nl`      |
| Englisch               |     `en`      |
| Französisch                |     `fr`      |
| Deutsch                |     `de`      |
| Hindi                 |    `hi`       |
| Italienisch               |     `it`      |
| Japanisch              |     `ja`      |
| Koreanisch                |     `ko`      |
| Norwegisch (Bokmål)   |     `no`      |
| Portugiesisch (Brasilien)   |    `pt-BR`    |
| Portugiesisch (Portugal) |    `pt-PT`    |
| Spanisch               |     `es`      |
| Türkisch               |     `tr`      |

Eine vollständige Beschreibung der verfügbaren Tags für die Textanalysecontainer finden Sie bei [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
