---
author: curib
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: cauribeg
ms.openlocfilehash: a04462ab50a5db71e9d019ab9d49fb137f569569
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585139"
---
| Resource | Begrenzung |
| --- | --- |
| Cachegröße |1,2 TB |
| Datenbanken |64 |
| Maximale Anzahl verbundener Clients |40.000 |
| Azure Cache for Redis-Replikate, für Hochverfügbarkeit |1 |
| Shards in einem Premium-Cache mit Clustering |10 |

Die erhältliche Größe und die Konfigurationsbeschränkungen von Azure Cache for Redis unterscheiden sich je nach Tarif. Die Tarife und die damit verbundenen Größen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/).

Weitere Informationen zu den Konfigurationsbeschränkungen von Azure Cache for Redis finden Sie unter [Standardmäßige Redis-Serverkonfiguration](../cache-configure.md#default-redis-server-configuration).

Da die Konfiguration und Verwaltung von Azure Cache for Redis-Instanzen von Microsoft übernommen wird, werden nicht alle Redis-Befehle in Azure Cache for Redis unterstützt. Weitere Informationen finden Sie unter [Redis-Befehle, die in Azure Cache for Redis nicht unterstützt werden](../cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).
