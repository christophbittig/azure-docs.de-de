---
title: Auswählen des richtigen Tarifs für Microsoft Azure Maps
description: Erfahren Sie mehr über Azure Maps-Tarife. Sehen Sie sich an, welche Tarife welche Funktionen anbieten, sowie wichtige Überlegungen zur Auswahl eines Tarifs.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef61b0c8a52e37b5eb73f38ab63cc97e66bf7e4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398913"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Auswählen des richtigen Tarifs in Azure Maps

Azure Maps bietet jetzt zwei Preisstufen an: Gen 1 und Gen 2. Die neue Preisstufe Gen 2 enthält alle Azure Maps-Funktionen mit erhöhten QPS-Grenzwerten (Queries per Second, Abfragen pro Sekunde) gegenüber Gen 1 und ermöglicht Ihnen Kosteneinsparungen bei steigenden Azure Maps-Transaktionen. Dieser Artikel soll Ihnen bei der Auswahl des richtigen Tarifs für Ihre Ansprüche helfen.

## <a name="pricing-tier-targeted-customers"></a>Tarife und Kundenzielgruppen

Zum besseren Verständnis der Preisstufen Gen 1 und Gen 2 beachten Sie bitte die untenstehende Tabelle mit den **Zielkunden für die Preisstufen**.  Weitere Informationen finden Sie unter [Azure Maps – Preise](https://aka.ms/CreatorPricing). Wenn Sie ein aktueller Azure Maps-Kunde sind, erfahren Sie im Artikel [Verwalten des Tarifs](how-to-manage-pricing-tier.md), wie Sie von der Preisstufe für Gen 1 zur Preisstufe für Gen 2 wechseln.

| Tarif  | SKU | Zielkunden|
|---------------|:---:| ------------------|
|**Gen1**|S0| Der Tarif S0 kann für Anwendungen in allen Phasen der Produktion verwendet werden: von der Entwicklung einer Machbarkeitsstudie und frühen Testphasen bis hin zur Produktion und Bereitstellung von Anwendungen. Dieser Tarif ist jedoch für die Entwicklung im geringeren Umfang oder für Kunden mit einer geringen Anzahl gleichzeitiger Benutzer oder beides konzipiert. S0 unterliegt einer Einschränkung von 50 QPS für alle Dienste zusammen.
|         |S1| Der Tarif S1 richtet sich an Kunden mit großen Unternehmensanwendungen, unternehmenskritischen Anwendungen oder einer großen Anzahl gleichzeitiger Benutzer. Er ist auch für Kunden geeignet, die komplexe Geodienste benötigen.
| **Gen2** | Karten/Standort-Erkenntnisse | Die Preisstufe Gen 2 ist für neue und aktuelle Azure Maps Kunden. Gen 2 wird mit einer kostenlosen monatlichen Transaktionsstufe geliefert, die zum Testen und Erstellen von Azure-Karten verwendet wird. Karten und SKU-Standort-Erkenntnisse enthalten alle Azure Maps Funktionen. Dadurch können Sie Kosteneinsparungen erzielen, wenn Azure Maps-Transaktionen zunehmen. Darüber hinaus weist es höhere QPS-Grenzwerte als Gen 1 auf. Der Tarif Gen 2 ist erforderlich, wenn [Creator für Gebäudepläne](creator-indoor-maps.md) verwendet wird.
|     |  |

Weitere Informationen zu QPS-Grenzwerten finden Sie unter [Grenzwerte für QPS-Raten in Azure Maps](azure-maps-qps-rate-limits.md).

Weitere Preisinformationen zu [Creator für Gebäudepläne](creator-indoor-maps.md)finden Sie im Abschnitt *Creator* in [Azure Maps (Karten) – Preise](https://aka.ms/CreatorPricing).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Anzeigen und Ändern von Tarifen:

> [!div class="nextstepaction"]
> [Verwalten eines Tarifs](how-to-manage-pricing-tier.md)
