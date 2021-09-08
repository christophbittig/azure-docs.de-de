---
title: Angeben der Modellversion in Textanalyse v3
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie das Textanalyse-API-Modell angeben, das für Ihre Daten verwendet wird.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/17/2021
ms.author: aahi
ms.openlocfilehash: d1804505a73be2db8d7088caf74063381fa0ba33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339492"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Versionsverwaltung der Modelle in der Textanalyse-API

Mit Version 3 der Textanalyse-API können Sie die für Ihre Daten verwendete Modellversion auswählen. Verwenden Sie den optionalen Parameter `model-version`, um die Version des Modells in Ihren API-Anforderungen auszuwählen. Beispiel: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Wenn dieser Parameter nicht angegeben wird, wird für die API standardmäßig die letzte stabile Version verwendet. 

## <a name="available-versions"></a>Verfügbare Versionen

Ermitteln Sie anhand der Tabelle unten, welche Modellversionen von den einzelnen gehosteten Endpunkten unterstützt werden.


| Endpunkt                        | Unterstützte Versionen                                     | Aktuelle Version |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-05-15`                           | `2021-05-15`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |


Sie finden Details zu den Updates für diese Modelle im Artikel zu den [Neuigkeiten ](../whats-new.md).

## <a name="extractive-summarization"></a>Extrahierende Zusammenfassung

Die extrahierende Zusammenfassung ist ab `version 3.1-preview.1` über den asynchronen `analyze`-Endpunkt verfügbar. 

Die aktuelle Modellversion ist `2021-08-01`.

## <a name="text-analytics-for-health"></a>Textanalyse für Gesundheit

Der Container [Text Analytics for Health](../how-tos/text-analytics-for-health.md) verwaltet gegenüber den oben aufgeführten API-Endpunkten die Modellversionen separat.  Bitte beachten Sie, dass pro Containerimage nur jeweils eine Modellversion verfügbar ist.

| Endpunkt                        | Tag für Containerimage                     | Modellversion |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` oder aktuellste            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Textanalyse](../overview.md)
* [Standpunktanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitätserkennung](../how-tos/text-analytics-how-to-entity-linking.md)
