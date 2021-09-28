---
title: Tabelle der Azure SignalR-Dienstlimits
description: Beschreibt die Systemgrenzwerte für den Azure SignalR-Dienst.
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2020
ms.author: zhshang
ms.openlocfilehash: f5b197448529e953d5d7d188b00a88d60e87e900
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453910"
---
| Resource | Standardlimit | Maximales Limit | 
| --- | --- | --- |
| Azure SignalR-Diensteinheiten pro Instanz für Free-Tarif |1 |1 |
| Azure SignalR-Diensteinheiten pro Instanz für Standard-Tarif |100 |100 |
| Azure SignalR-Diensteinheiten pro Abonnement und Region für Free-Tarif|5 |5 |
| Gesamtzahl der Azure SignalR-Einheiten pro Abonnement und Region |150 |Unbegrenzt |
| Verbindungen pro Einheit und Tag für Free-Tarif |20 |20 |
| Verbindungen pro Einheit pro Tag für Standard-Tarif |1\.000 |1\.000|
| Enthaltene Nachrichten pro Einheit und Tag für Free-Tarif|20.000 |20.000 |
| Zusätzliche Nachrichten pro Einheit und Tag für Free-Tarif|0 |0 |
| Enthaltene Nachrichten pro Einheit und Tag für Standard-Tarif|1\.000.000 |1\.000.000 |
| Zusätzliche Nachrichten pro Einheit und Tag für Standard-Tarif|Unbegrenzt |Unbegrenzt |

Öffnen Sie ein Supportticket, um eine Aktualisierung der Standardgrenzwerte Ihres Abonnements anzufordern.

Weitere Informationen zum Zählen von Verbindungen und Nachrichten finden Sie unter [Nachrichten und Verbindungen in Azure SignalR Service](../articles/azure-signalr/signalr-concept-messages-and-connections.md).

Wenn Ihre Anforderungen die Grenzwerte überschreiten, wechseln Sie vom Free-Tarif zur Standardebene und fügen Sie Einheiten hinzu. Weitere Informationen finden Sie unter [Skalieren einer Azure SignalR Service-Instanz](../articles/azure-signalr/signalr-howto-scale-signalr.md). 

Wenn Ihre Anforderungen die Grenzwerte einer einzelnen Instanz überschreiten, fügen Sie Instanzen hinzu. Weitere Informationen finden Sie unter [Skalieren des SignalR Service mit mehreren Instanzen](../articles/azure-signalr/signalr-howto-scale-multi-instances.md).
