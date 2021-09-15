---
title: Grundlegendes zu Ebenen im Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie mehr über die verschiedenen Ebenen, die im Microsoft Azure Maps-Visual für Power BI verfügbar sind.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6014bbf5ed5f3600bc79d5083f5fcc85d323acd5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428900"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Grundlegendes zu Ebenen im Azure Maps-Visual in Power BI

Im Azure Maps-Visual sind zwei Arten von Ebenen verfügbar. Der erste Typ konzentriert sich auf das Rendern von Daten, die an den Bereich **Felder** des Visuals übergeben werden. Er besteht aus den folgenden Ebenen, die wir hier Datenrenderingebenen nennen.

:::row:::
    :::column span="":::
        **Blasenebene**

        Diese Ebene rendert Punkte in Form skalierter Kreise auf der Karte.

        ![Blasenebene auf der Karte](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Balkendiagrammebene**

        Diese Ebene rendert Punktdaten als 3D-Balken auf der Karte.
        
        ![Balkendiagrammebene auf Karte](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

Der zweite Ebenentyp verbindet zusätzliche externe Datenquellen für die Zuordnung, um mehr Kontext bereitzustellen, und besteht aus den folgenden Ebenen.

:::row:::
    :::column span="":::
        **Referenzebene**

        Diese Ebene überlagert eine hochgeladene GeoJSON-Datei auf der Karte.

        ![Referenzebene auf der Karte](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Kachelebene**

        Diese Ebene überlagert eine Kachelebene auf der Karte.
        
        ![Kachelebene auf der Karte](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Verkehrsinfoebene**

        Diese Ebene überlagert Verkehrsinfo in Echtzeit auf der Karte.
        
        ![Datenverkehrsebene auf der Karte](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Alle Datenrenderingebenen und die **Kachelebene** bieten Optionen für minimale und maximalen Zoomstufen, die zum Angeben eines Zoomstufenbereichs verwendet werden, in dem diese Ebenen dargestellt werden sollen. Dadurch kann ein Renderingebenentyp auf einer Zoomstufe verwendet werden und ein Übergang auf einer anderen Renderingebene auf einer anderen Zoomstufe.

Diese Ebenen verfügen auch über eine Option, die relativ zu anderen Ebenen auf der Karte positioniert werden kann. Wenn mehrere Datenrenderingebenen verwendet werden, bestimmt die Reihenfolge, in der sie der Karte hinzugefügt werden, die relative Ebenenreihenfolge, wenn sie dieselbe **Ebenenpositionswert** haben.

## <a name="general-layer-settings"></a>Allgemeine Ebeneneinstellungen

Der Abschnitt zur allgemeinen Ebene des Bereichs **Format** beinhaltet allgemeine Einstellungen, die für die Ebenen gelten, die mit dem Power BI-Dataset im Bereich **Felder** (Blasenebene, Balkendiagrammebene) verknüpft sind.

| Einstellung     | BESCHREIBUNG   |
|-------------|---------------|
| Transparenz für nicht ausgewählte Datenpunkte | Die Transparenz von Formen, die nicht ausgewählt sind, wenn eine oder mehrere Formen ausgewählt werden  |
| Nullwerte anzeigen              | Gibt an, ob Punkte mit einem Größenwert von 0 (null) mit dem Mindestradius auf der Karte angezeigt werden sollen |
| Negative anzeigen          | Gibt an, ob der absolute Wert negativer Größenwerte gezeichnet werden soll   |
| Minimaler Datenwert          | Der Mindestwert der Eingabedaten für die Skalierung. Gut für Clipping-Ausreißer.  |
| Maximaler Datenwert          | Der maximale Wert der Eingabedaten für die Skalierung. Gut für Clipping-Ausreißer.  |

## <a name="next-steps"></a>Nächste Schritte

Ändern Sie, wie Ihre Daten auf der Karte angezeigt werden:

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Balkendiagrammebene](power-bi-visual-add-bar-chart-layer.md)

Fügen Sie der Karte mehr Kontext hinzu:

> [!div class="nextstepaction"]
> [Hinzufügen einer Referenzebene](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Anzeigen von Verkehrsinfo in Echtzeit](power-bi-visual-show-real-time-traffic.md)
