---
title: QPS-Ratenbegrenzung für Azure Maps
description: Azure Maps-spezifische Einschränkung der Anzahl von Abfragen pro Sekunde.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/15/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 8dd0c657ba645d1ed2bf0e8585149aec7b486536
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316012"
---
# <a name="azure-maps-qps-rate-limits"></a>QPS-Ratenbegrenzung für Azure Maps

Bei Azure Maps gibt es keine maximalen Tageslimits für die Anzahl möglicher Anforderungen. Die maximale Anzahl von Abfragen pro Sekunde (Queries Per Second, QPS) ist jedoch begrenzt.

Im Anschluss finden Sie die QPS-Grenzwerte für die einzelnen Azure Maps-Dienste nach Tarif:

| Azure Maps-Dienst | QPS-Grenzwert: Tarif „Gen 2“ | QPS-Grenzwert: Tarif „Gen 1 S1“ | QPS-Grenzwert: Tarif „Gen 1 S0“ |
|  ----------------- |  :--------------------------: | :------------------------------: | :------------------------: |
| Copyright-Dienst | 10 | 10 | 10 |
| Creator: Alias, TilesetDetails | 10 | Nicht verfügbar | Nicht verfügbar. |
| Creator: Konvertierung, Dataset, Featurezustand, WFS | 50 | Nicht verfügbar | Nicht verfügbar. |
| Datendienst | 50 | 50 | 50 |
| Höhenangabendienst | 50 | 50 | 50 |
| Geolocation-Dienst | 50 | 50 | 50 |
| Renderdienst: Konturenkacheln, DEM-Kacheln, Höhenkacheln, Kundenkacheln, Verkehrskacheln und statische Karten | 50 | 50 | 50 |
| Renderdienst: Straßenkacheln | 500 | 500 | 50 |
| Renderdienst: Satellitenkacheln | 250 | 250 | 50 |
| Renderdienst: Wetterkacheln | 100 | 100 | 50 |
| Routendienst: Batch | 10 | 10 | 10 |
| Routendienst: Kein Batch | 50 | 50 | 50 |
| Suchdienst: Batch | 10 | 10 | 10 |
| Suchdienst: Kein Batch | 500 | 500 | 50 |
| Suchdienst: Rückwärts, kein Batch | 250 | 250 | 50 |
| Räumlicher Dienst | 50 | 50 | 50 |
| Zeitzonendienst | 50 | 50 | 50 |
| Verkehrsinfodienst | 50 | 50 | 50 |
| Weather Service | 50 | 50 | 50 |

Wenn die QPS-Grenzwerte erreicht sind, wird ein HTTP 429-Fehler zurückgegeben. Wenn Sie den Tarif „Gen 2“ oder „Gen 1 S1“ verwenden, können Sie im [Azure-Portal](https://ms.portal.azure.com/) eine *technische* Supportanfrage für Azure Maps erstellen, um bei Bedarf einen bestimmten QPS-Grenzwert zu erhöhen. QPS-Grenzwerte für den Tarif „Gen 1 S0“ können nicht erhöht werden.
