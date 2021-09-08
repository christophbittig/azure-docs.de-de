---
title: Containeranforderungen und -empfehlungen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 81e86eda454bdbd262f6c38a4766ee2a899d225d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336034"
---
> [!NOTE]
> Die Anforderungen und Empfehlungen basieren auf Benchmarks mit einer einzelnen Anforderung pro Sekunde, wobei ein 523 KB großes Bild eines gescannten Geschäftsbriefs mit 29 Zeilen und insgesamt 803 Zeichen verwendet wird. Die empfohlene Konfiguration führte im Vergleich zur Mindestkonfiguration zu einer etwa doppelt so schnellen Reaktion.

In der folgenden Tabelle werden die minimale und empfohlene Zuordnung von Ressourcen für jeden OCR-Container für das Lesen beschrieben.

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
| Read 2.0-preview | 1 Kern, 8 GB Arbeitsspeicher |    8 Kerne, 16 GB Arbeitsspeicher |
| Read 3.2 | 4 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 24 GB Arbeitsspeicher |

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.
