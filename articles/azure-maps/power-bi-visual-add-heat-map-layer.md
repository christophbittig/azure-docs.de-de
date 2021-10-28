---
title: Hinzufügen eines Wärmebilds zum Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie das Wärmebild im Microsoft Azure Maps-Visual in Power BI verwenden.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.custom: ''
ms.openlocfilehash: 37b03d96e67016bb9a250cd889da93fb30578d54
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240184"
---
# <a name="add-a-heat-map-layer-to-the-azure-maps-visual-for-power-bi"></a>Hinzufügen einer Wärmebildebene zum Azure Maps-Visual in Power BI

In diesem Artikel erfahren Sie, wie Sie einem Azure Maps-Visual in Power BI eine Wärmebildebene hinzufügen.

:::image type="content" source="media/power-bi-visual/heat-map.png" alt-text="Wärmebildebene im Azure Maps-Visual in Power BI.":::

Wärmebilder, die auch als Dichtekarten bezeichnet werden, sind eine Art Überlagerung auf einer Karte, die verwendet wird, um die Dichte von Daten mit verschiedenen Farben darzustellen. Wärmebilder werden häufig eingesetzt, um die „Daten-Hotspots“ auf einer Karte anzuzeigen. Wärmebilder sind eine hervorragende Möglichkeit, Datensätze mit einer großen Anzahl von Punkten zu rendern. Das Anzeigen einer großen Anzahl von Datenpunkten auf einer Karte führt zu einer Beeinträchtigung der Leistung und kann die Karte mit überlappenden Symbolen bedecken, wodurch sie unbrauchbar wird. Das Rendern der Daten als Wärmebild führt nicht nur zu einer besseren Leistung, sondern hilft Ihnen auch, die Daten besser zu verstehen, da die relative Dichte jedes Datenpunkts leicht ersichtlich ist.

Ein Wärmebild erweist sich als nützlich, wenn Benutzer umfangreiche Vergleichsdaten visualisieren möchten:

- Vergleichen von Kundenzufriedenheitsraten oder der Leistung von Geschäften in verschiedenen Regionen oder Ländern.
- Messen der Häufigkeit, mit der Kunden Einkaufspassagen an verschiedenen Orten besuchen.
- Visualisieren großer statistischer und geografischer Datensätze.

## <a name="prerequisites"></a>Voraussetzungen

- [Erste Schritte mit dem Azure Maps-Visual in Power BI](/azure/azure-maps/power-bi-visual-getting-started).
- [Grundlegendes zu Ebenen im Azure Maps-Visual in Power BI](/azure/azure-maps/power-bi-visual-understanding-layers).

## <a name="add-the-heat-map-layer"></a>Hinzufügen der Wärmebildebene

1. Wählen Sie in Power BI Desktop die von Ihnen erstellte Azure-Karte aus.
1. Schalten Sie im **Formatbereich**  die Umschaltfläche **Wärmebild** auf **Ein**.

Sie können jetzt alle Einstellungen der Wärmebildebene an Ihren Bericht anpassen.

## <a name="heat-map-layer-settings"></a>Einstellungen für Wärmebildebenen

Der Abschnitt **Wärmebild** im **Formatbereich** bietet Flexibilität beim Anpassen und Entwerfen der Wärmebildvisualisierungen an Ihre spezifischen Anforderungen. Im Abschnitt **Wärmebild** können Sie:

- Den Radius jedes Datenpunkts mithilfe von Pixeln oder Metern als Maßeinheit konfigurieren.
- Die Deckkraft und Intensität der Wärmebildebene anpassen.  
- Angeben, ob der Wert im Größenfeld als Gewichtung der einzelnen Datenpunkte verwendet werden soll.
- Verschiedene Farben aus dem Farb-Editor auswählen.
- Den minimalen und maximalen Zoomfaktor festlegen, bei dem die Wärmekartenebene sichtbar sein soll.
- Die Position der Wärmebildebene zwischen verschiedenen Ebenen festlegen, z. B. 3D-Balkendiagrammebene und Blasenebene.

In der nachstehenden Tabelle sind die Haupteinstellungen im Abschnitt **Wärmebild** aufgeführt, die im **Formatbereich** verfügbar sind:

| Einstellung              | BESCHREIBUNG      |
|----------------------|------------------|
| Radius | Der Radius für jeden Datenpunkt im Wärmebild.<br /><br />Gültige Werte, wenn Einheit = 'Pixel': 1–200. Standardwert: **20**<br />Gültige Werte, wenn Einheit = 'Meter': 1–4.000.000|
| Einheiten  | Die Entfernungseinheiten für den Radius. Mögliche Werte:<br /><br />**Pixel**. Wenn diese Einstellung auf „Pixel“ festgelegt ist, ist die Größe jedes Datenpunkts unabhängig vom Zoomfaktor immer gleich.<br />**Meter**. Wenn diese Einstellung auf „Meter“ festgelegt ist, wird die Größe der Datenpunkte basierend auf dem Zoomfaktor skaliert, um sicherzustellen, dass der Radius räumlich genau ist.<br /><br /> Standardeinstellung: **Pixel**  |
| Opacity | Legt die Deckkraft für die Wärmebildebene fest. Standardwert: **1**<br/>Der Wert muss ein Dezimalwert zwischen 0 und 1 sein. |
| Intensität | Die Intensität der einzelnen Wärmepunkte. Die Intensität ist ein Dezimalwert zwischen 0 und 1, der verwendet wird, um anzugeben, wie „warm“ ein einzelner Datenpunkt sein soll. Standardwert: **0,5** |
| Verwenden der Größe als Gewichtung | Ein boolescher Wert, der vorgibt, ob der Wert des Größenfelds als Gewichtung der einzelnen Datenpunkte verwendet werden soll. Bei Aktivierung wird die Ebene als gewichtetes Wärmebild gerendert. Standardeinstellung: **Aus** |
| Farbverlauf |Farbauswahl, die es dem Benutzer ermöglicht 3 Farben für niedrige (0 %), mittlere (50 %) und hohe (100 %) Farbverläufe zu wählen. |
| Mindestzoom |Minimaler Zoomfaktor, bei dem die Ebene sichtbar ist. Gültige Werte reichen von 1 bis 22. Standardwert: **0** |
|Maximaler Zoom |Maximaler Zoomfaktor, bei dem die Ebene sichtbar ist.  Gültige Werte reichen von 1 bis 22. Standardwert: **22**|
|Ebenenposition |Angabe der relativen Position der Ebene in Bezug auf andere Kartenebenen. Gültige Werte sind **„Über“-Bezeichnungen,** **„Unter“-Bezeichnungen** und **„Unter Straßen“** . |

## <a name="next-steps"></a>Nächste Schritte

Ändern Sie, wie Ihre Daten auf der Karte angezeigt werden:

> [!div class="nextstepaction"]
> [Hinzufügen einer Balkendiagrammebene](power-bi-visual-add-bar-chart-layer.md)

Fügen Sie der Karte mehr Kontext hinzu:

> [!div class="nextstepaction"]
> [Hinzufügen einer Referenzebene](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Anzeigen von Verkehrsinfo in Echtzeit](power-bi-visual-show-real-time-traffic.md)

Passen Sie das Visual an:

> [!div class="nextstepaction"]
> [Tipps und Tricks zur Farbformatierung in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Anpassen von Visualisierungstiteln, -hintergründen und -legenden](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
