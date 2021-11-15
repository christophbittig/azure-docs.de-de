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
ms.openlocfilehash: 4900a05ead0f3abcc16177a2b7ef874f310c597f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455319"
---
# <a name="azure-maps-qps-rate-limits"></a>QPS-Ratenbegrenzung für Azure Maps

Bei Azure Maps gibt es keine maximalen Tageslimits für die Anzahl möglicher Anforderungen. Die maximale Anzahl von Abfragen pro Sekunde (Queries Per Second, QPS) ist jedoch begrenzt.

Im Anschluss finden Sie die QPS-Grenzwerte für die einzelnen Azure Maps-Dienste:

| Azure Maps-Dienst                                                                                         | QPS-Grenzwert   |
| ---------------------------------------------------------------------------------------------------------- | ----------- |
| Ersteller: Alias, GetTilesetDetails                                                                         | 10 |
| Ersteller: Konvertierung, Dataset, Featurezustand, WFS                                                          | 50 |
| Datendienst und Datendienst v2                                                                           | 50 |
| Höhenangabendienst                                                                                          | 50 |
| Geolocation-Dienst                                                                                        | 50 |
| Renderdienst: Copyright                                                                                 | 10 |
| Renderdienst: Konturenkacheln, DEM-Kacheln, Höhenkacheln, Kundenkacheln, Verkehrskacheln und statische Karten  | 50 |
| Renderdienst: Straßenkacheln                                                                                | 500 |
| Renderdienst: Bildkacheln                                                                             | 250 |
| Renderdienst: Wetterkacheln                                                                             | 100 |
| Routendienst:  Batch                                                                                      | 10 |
| Routendienst: Kein Batch                                                                                  | 50 |
| Suchdienst: Batch                                                                                     | 10 |
| Suchdienst: Kein Batch                                                                                 | 500 |
| Suchdienst: Rückwärts, kein Batch                                                                         | 250 |
| Räumlicher Dienst                                                                                            | 50 |
| Zeitzonendienst                                                                                          | 50 |
| Verkehrsinfodienst                                                                                            | 50 |
| Weather Service                                                                                            | 50 |

Wenn die QPS-Grenzwerte erreicht sind, wird ein HTTP 429-Fehler zurückgegeben. Erstellen Sie im [Azure-Portal](https://ms.portal.azure.com/) eine *technische* Supportanfrage für Azure Maps, wenn Sie einen bestimmten QPS-Grenzwert erhöhen möchten.
