---
title: Grenzwerte für den Dienst „Azure Chaos Studio“
description: Grundlegendes zu den Grenzwerten bei der Drosselung und Nutzung in Azure Chaos Studio
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.date: 11/01/2021
ms.topic: reference
ms.custom: ignite-fall-2021
ms.openlocfilehash: f9b5352597afb00b379363a2f151615a902dff8f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095279"
---
# <a name="azure-chaos-studio-service-limits"></a>Grenzwerte für den Dienst „Azure Chaos Studio“
Dieser Artikel enthält eine Beschreibung der Diensteinschränkungen für Azure Chaos Studio. 
## <a name="experiment-and-target-limits"></a>Grenzwerte für Experimente und Ziele

In Chaos Studio gelten für die Anzahl von Objekten, die Dauer von Aktivitäten und die Aufbewahrung von Daten bestimmte Grenzwerte.

| Begrenzung | Wert |
| -- | -- |
| Aktionen pro Experiment | 9 |
| Verzweigungen pro Experiment | 9 |
| Schritte pro Experiment | 4 |
| Aktionsdauer (Stunden) | 12 |
| Gleichzeitige Experimente, die pro Region und Abonnement ausgeführt werden | 5 |
| Gesamtdauer der Experimente (Stunden) | 12 |
| Anzahl von Experimenten pro Region und Abonnement | 500 |
| Anzahl von Zielen pro Aktion | 50 |
| Anzahl von aktiven Agents pro Ziel | 1\.000 |
| Anzahl von Zielen pro Region und Abonnement | 10.000 |

## <a name="api-throttling-limits"></a>Grenzwerte für API-Drosselung

In Chaos Studio gelten für alle ARM-Vorgänge bestimmte Grenzwerte. Anforderungen, für die der Grenzwert überschritten wird, werden gedrosselt. Alle Anforderungsgrenzwerte werden für ein Intervall mit einer Länge von fünf Minuten angewendet, sofern nichts anderes angegeben ist.

| Vorgang | Requests |
| -- | -- |
| Microsoft.Chaos/experiments/write | 100 |
| Microsoft.Chaos/experiments/read | 300 |
| Microsoft.Chaos/experiments/delete | 100 |
| Microsoft.Chaos/experiments/start/action | 20 |
| Microsoft.Chaos/experiments/cancel/action | 100 |
| Microsoft.Chaos/experiments/statuses/read | 100 |
| Microsoft.Chaos/experiments/executionDetails/read | 100 |
| Microsoft.Chaos/targets/write | 200 |
| Microsoft.Chaos/targets/read | 600 |
| Microsoft.Chaos/targets/delete | 200 |
| Microsoft.Chaos/targets/capabilities/write | 600 |
| Microsoft.Chaos/targets/capabilities/read | 1800 |
| Microsoft.Chaos/targets/capabilities/delete | 600 |
| Microsoft.Chaos/locations/targetTypes/read | 50 |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/read | 50 |
