---
title: Unterstützte integrierte Azure Maps-Kartenstile
description: Hier erfahren Sie mehr über die von Azure Maps unterstützten integrierten Kartenstile, etwa „road“, „blank_accessible“, „satellite“, „satellite_road_labels“, „road_shaded_relief“ und „night“.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 4032dec715fc12b4e8144e6d9eebd51faceaaaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355331"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>In Azure Maps unterstützte integrierte Kartenstile

Azure Maps unterstützt mehrere integrierte Kartenstile, die im Folgenden beschrieben werden.

>[!important]
>Der Vorgang in diesem Abschnitt erfordert ein Azure Maps-Konto der Preisstufe Gen 1 oder Gen 2. Weitere Informationen zu den Preisstufen finden Sie unter [Wählen der korrekten Preisstufe in Azure Maps](choose-pricing-tier.md).

## <a name="road"></a>Straße

Eine **Straßenkarte** ist eine Standardkarte, auf der Straßen angezeigt werden. Außerdem werden natürliche und künstliche Features sowie die Bezeichnungen für diese Features angezeigt.

![Kartenstil „Straße“](./media/supported-map-styles/road.png)

**Anwendbare APIs:**

* [Kartenbild](/rest/api/maps/render/getmapimage)
* [Kartenkachel](/rest/api/maps/render/getmaptile)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="blank-and-blank_accessible"></a>„blank“ und „blank_accessible“

Die Kartenstile **blank** und **blank_accessible** stellen einen leeren Zeichenbereich für die Visualisierung von Daten bereit. Der Stil **blank_accessible** wird weiterhin Updates für die Sprachausgabe mit Details zum Standort der Karte bereitstellen, obwohl die Basiskarte nicht angezeigt wird.

> [!Note]
> Im Web-SDK können Sie die Hintergrundfarbe der Karte ändern, indem Sie den CSS-Stil `background-color` des DIV-Kartenelements festlegen.

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement

## <a name="satellite"></a>Satellit

Der Kartenstil **Satellit** ist eine Kombination aus Satellitenbildern und Luftaufnahmen.

![Kartenstil „Satellitenkachel“](./media/supported-map-styles/satellite.png)

**Anwendbare APIs:**

* [Satellitenkachel](/rest/api/maps/render/getmapimagerytilepreview)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="satellite_road_labels"></a>Satellite_Road_Labels

Dieser Kartenstil ist eine Hybriddarstellung, bei der Straßen und Bezeichnungen Satellitenbilder und Luftaufnahmen überlagern.

![Kartenstil „satellite_road_labels“](./media/supported-map-styles/satellite-road-labels.png)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="grayscale_dark"></a>grayscale_dark

**grayscale_dark** ist eine dunkle Version des Kartenstils „Straße“.

![Kartenstil „gray_scale“](./media/supported-map-styles/grayscale-dark.png)

**Anwendbare APIs:**

* [Kartenbild](/rest/api/maps/render/getmapimage)
* [Kartenkachel](/rest/api/maps/render/getmaptile)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="grayscale_light"></a>grayscale_light

**grayscale_light** ist eine helle Version des Stils „Straßenkarte“.

![Kartenstil „grayscale_light“](./media/supported-map-styles/grayscale-light.jpg)

**Anwendbare APIs:**
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="night"></a>Nacht

**Nacht** ist eine dunkle Version des Kartenstils „Straße“ mit farbigen Straßen und Symbolen.

![Kartenstil „Nacht“](./media/supported-map-styles/night.png)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="road_shaded_relief"></a>road_shaded_relief

**road shaded relief** (Relief mit schattierten Straßen) ist eine Azure Maps-Hauptformatvorlage mit Konturen der Erde.

![Kartenstil „Schattiertes Relief“](./media/supported-map-styles/shaded-relief.png)

**Anwendbare APIs:**

* [Kartenkachel](/rest/api/maps/render/getmaptile)
* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** ist ein dunkler Kartenstil mit einem höheren Kontrast als bei anderen Stilen.

![Dunkler Kartenstil mit starkem Kontrast](./media/supported-map-styles/high-contrast-dark.png)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="high_contrast_light"></a>high_contrast_light

**high_contrast_light** ist ein heller Kartenstil mit einem höheren Kontrast als bei anderen Stilen.

![Heller Kartenstil mit starkem Kontrast](./media/supported-map-styles/high-contrast-light.jpg)

**Anwendbare APIs:**

* Web-SDK-Kartensteuerelement
* Android-Kartensteuerelement
* Power BI-Visualisierung

## <a name="map-style-accessibility"></a>Barrierefreiheit von Kartenstilen

Bei den interaktiven Kartensteuerelementen von Azure Maps kommen Vektorkacheln in den Kartenstilen zum Einsatz, damit die Sprachausgabe den angezeigten Kartenbereich beschreiben kann. Mehrere Kartenstile sind außerdem im Hinblick auf den Farbkontrast vollständig barrierefrei. Die folgende Tabelle enthält Details zu den Barrierefreiheitsfeatures, die von den einzelnen Kartenstilen unterstützt werden:

| Kartenstil  | Farbkontrast | Sprachausgabe | Notizen |
|------------|----------------|---------------|-------|
| `blank` | – | Nein | Eine leere Canvas, die für Entwickler nützlich ist, die ihre eigenen Kacheln als Basiskarte verwenden oder ihre Daten ganz ohne Hintergrund anzeigen möchten. Von der Sprachausgabe werden keine Vektorkacheln für Beschreibungen genutzt.  |
| `blank_accessible` | –  | Ja | Bei diesem Kartenstil werden im Hintergrund zwar weiterhin die zum Rendern der Karte verwendeten Vektorkacheln geladen, die Daten werden allerdings transparent gemacht. Dadurch werden die Daten weiterhin geladen und können von der Sprachausgabe verwendet werden. |
| `grayscale_dark` | Partial | Ja | Dieser Kartenstil ist in erster Linie für Business Intelligence-Szenarien konzipiert, eignet sich aber auch zum Anzeigen farbiger Überlagerungen (etwa Wetterradarbilder). |
| `grayscale_light` | Partial | Ja | Dieser Kartenstil ist in erster Linie für Business Intelligence-Szenarien konzipiert. |
| `high_contrast_dark` | Ja | Ja | Vollständig barrierefreier Kartenstil für Benutzer im Modus für hohen Kontrast mit einer dunklen Einstellung. Einstellungen für hohen Kontrast werden beim Laden der Karte automatisch erkannt. |
| `high_contrast_light` | Ja | Ja | Vollständig barrierefreier Kartenstil für Benutzer im Modus für hohen Kontrast mit einer hellen Einstellung. Einstellungen für hohen Kontrast werden beim Laden der Karte automatisch erkannt. |
| `night` | Partial | Ja | Dieser Stil wurde für den Fall konzipiert, dass sich der Benutzer in einer Umgebung mit schwachem Licht befindet und nicht durch eine helle Karte geblendet werden soll. |
| `road` | Partial | Ja | Dies ist das primäre farbige Straßenkartenformat in Azure Maps. Aufgrund der Anzahl verschiedener Farben und möglicher Überschneidungen bei Farbkombinationen ist es bei diesem Stil nahezu unmöglich, eine vollständige Barrierefreiheit zu erreichen. Die Barrierefreiheit dieses Kartenstils wird jedoch regelmäßig getestet, und der Kartenstil wird bei Bedarf verbessert, um die Lesbarkeit von Beschriftungen zu verbessern. |
| `road_shaded_relief` | Partial | Ja | Dieser Stil ist nahezu mit dem primären Straßenkartenformat identisch. Er verfügt allerdings über eine zusätzliche Kachelebene im Hintergrund, die ein schattiertes Relief für Gebirge sowie Farben für die Landnutzung hinzufügt, wenn die Ansicht auf höhere Ebenen verkleinert wird. |
| `satellite` | – | Ja | Reine Satellitenbilder und Luftaufnahmen; keine Beschriftungen oder Straßenlinien. Die Vektorkacheln werden im Hintergrund geladen, um die Sprachausgabe sowie einen reibungsloseren Übergang beim Wechsel zu `satellite_with_roads` zu ermöglichen. |
| `satellite_with_roads` | Nein | Ja | Satellitenbilder und Luftaufnahmen mit überlagerten Beschriftungen und Straßenlinien. Auf globaler Ebene gibt es eine unbegrenzte Anzahl von Farbkombinationen, die zwischen den überlagerten Daten und den Bildern möglich sind. Einer der Schwerpunkte besteht darin, Beschriftungen in den meisten gängigen Szenarien lesbar zu machen, aber an manchen Stellen kann der Farbkontrast mit den Hintergrundbildern das Lesen von Beschriftungen erschweren. |

## <a name="next-steps"></a>Nächste Schritte

Informationen, wie ein Kartenstil in Azure Maps festgelegt wird:

> [!div class="nextstepaction"]
> [Auswählen eines Kartenstils](./choose-map-style.md)
