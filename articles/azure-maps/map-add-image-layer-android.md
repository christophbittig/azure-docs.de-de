---
title: Hinzufügen einer Bildebene zu einer Android-Karte | Microsoft Azure Maps
description: Lernen Sie, einer Karte Bilder hinzuzufügen. Erfahren Sie, wie Sie das Azure Maps Android SDK zum Anpassen von Bildebenen und Überlagern mit Bildern auf festen Koordinatensätzen verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: d90771a639cbd904c3c61412b511d764d2d37ae8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432736"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Hinzufügen einer Bildebene zu einer Karte (Android SDK)

In diesem Artikel erfahren Sie, wie Sie einen festen Satz an Koordinaten mit einem Bild überlagern. Im Folgenden finden Sie einige Beispiele für verschiedene Bildtypen, die auf Karten überlagert werden können:

* Von Drohnen aufgenommene Bilder
* Gebäudegrundrisse
* Bilder von historischen oder anderen Spezialkarten
* Blaupausen von Auftragsstandorten

> [!TIP]
> Eine Bildebene ist eine einfache Möglichkeit, eine Karte mit einem Bild zu überlagern. Beachten Sie, dass große Bilder viel Arbeitsspeicher beanspruchen und zu Leistungsproblemen führen können. In diesem Fall ist das Aufteilen des Bilds in Kacheln sinnvoll, die Sie dann als Kachelebene in die Karte laden können.

## <a name="add-an-image-layer"></a>Hinzufügen einer Bildebene

Der folgende Code legt ein Bild einer Karte von Newark, New Jersey, von 1922 über die Karte. Dieses Bild wird dem Ordner `drawable` des Projekts hinzugefügt. Eine Bildebene wird erstellt, indem das Bild und die Koordinaten für die vier Ecken im Format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` festgelegt werden. Häufig ist das Hinzufügen von Bildebenen unterhalb der `label`-Ebene wünschenswert.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Alternativ kann eine URL zu einem Bild angegeben werden, das online gehostet wird. Sofern es in Ihrem Szenario möglich ist, fügen Sie das Bild jedoch dem Ordner `drawable` Ihres Projekts hinzu, der schneller geladen wird, da das Bild lokal verfügbar ist und nicht heruntergeladen werden muss.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Der folgende Screenshot zeigt eine Karte von Newark, New Jersey, von 1922, die mit einer Bildebene überlagert ist.

![Karte von Newark, New Jersey, von 1922, die mit einer Bildebene überlagert ist](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importieren einer KML-Datei als Bodenüberlagerung

In diesem Beispiel sehen Sie, wie KML-Bodenüberlagerungsdaten auf der Karte als Bildebene hinzugefügt werden. KML-Bodenüberlagerungen stellen die Nord-, Süd-, Ost- und Westkoordinaten und eine Drehung gegen den Uhrzeigersinn bereit. Die Bildebene erwartet jedoch für jede Ecke des Bilds Koordinaten. Bei der KML-Bodenüberlagerung in diesem Beispiel handelt es sich um die Kathedrale von Chartres (Quelle: [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

Der Code verwendet die statische `getCoordinatesFromEdges`-Methode der `ImageLayer`-Klasse. Diese Methode berechnet die vier Ecken des Bilds aus den Nord-, Süd-, Ost-, West- und Drehungsinformationen der KML-Bodenüberlagerung.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Der folgende Screenshot zeigt eine Karte mit einer KML-Bodenüberlagerung mithilfe einer Bildebene.

![Karte mit einer KML-Bodenüberlagerung mithilfe einer Bildebene](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Verwenden Sie die Methoden `getPixels` und `getPositions` der Bildebenenklasse, um zwischen geografischen Koordinaten der positionierten Bildebene und den lokalen Bildpixelkoordinaten zu konvertieren.

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie mehr über Möglichkeiten zum Überlagern mit Bildern auf einer Karte.

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](how-to-add-tile-layer-android-map.md)
