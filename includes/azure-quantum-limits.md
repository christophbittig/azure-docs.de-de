---
title: Includedatei
description: Includedatei
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 10/27/2021
ms.author: dasto
ms.openlocfilehash: 1811faeeb5de3e300a35f7107c5cdf23bf20151c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520878"
---
### <a name="provider-limits--quota"></a>Grenzwerte und Kontingente für Anbieter

Der Azure Quantum-Dienst unterstützt sowohl Erst- als auch Drittanbieter von Diensten. Drittanbieter besitzen eigene Grenzwerte und Kontingente. Benutzer können Angebote und Grenzwerte im Azure-Portal anzeigen, wenn sie Drittanbieter konfigurieren. 

Nachfolgend finden Sie die veröffentlichten Kontingentgrenzwerte für den Erstanbieter von Optimierungslösungen von Microsoft. 

#### <a name="learn--develop-sku"></a>SKU für Lernen und Entwickeln (Learn & Develop)

| Resource | Begrenzung |
| --- | --- |
| CPU-basierte gleichzeitige Aufträge | bis zu 5<sup>1</sup> gleichzeitige Aufträge |
| FPGA-basierte gleichzeitige Aufträge | bis zu 2<sup>1</sup> gleichzeitige Aufträge |
| CPU-basierte Solver-Stunden | 20 Stunden pro Monat  |
| FPGA-basierte Solver-Stunden | 1 Stunde pro Monat  |

Wenn Sie die SKU „Learn & Develop“ nutzen, können Sie **keine** Erhöhung Ihrer Kontingentgrenzwerte anfordern. Stattdessen sollten Sie zur SKU „Leistung im großen Stil“ (Performance at Scale) wechseln.

#### <a name="performance-at-scale-sku"></a>SKU „Leistung im großen Stil“ (Performance at Scale)

| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| CPU-basierte gleichzeitige Aufträge | bis zu 100<sup>1</sup> gleichzeitige Aufträge | wie Standardgrenzwert |
| FPGA-basierte gleichzeitige Aufträge | bis zu 10<sup>1</sup> gleichzeitige Aufträge | wie Standardgrenzwert |
| Solver-Stunden | 1\.000 Stunden pro Monat  | bis zu 50.000 Stunden pro Monat |

Setzen Sie sich mit dem Azure-Support in Verbindung, um eine Erhöhung des Grenzwerts anzufordern.

Weitere Informationen finden Sie in der [Preisübersicht zu Azure Quantum](https://aka.ms/AQ/Pricing).
Einzelheiten zu den Angeboten von Drittanbietern finden Sie auf den entsprechenden Anbieterseiten mit Preisübersichten im Azure-Portal.

<sup>1</sup> Beschreibt die Anzahl von Aufträgen, die gleichzeitig in die Warteschlange eingereiht werden können.
