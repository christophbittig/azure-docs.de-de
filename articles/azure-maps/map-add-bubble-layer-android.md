---
title: Hinzufügen einer Blasenebene zu Android-Karten | Microsoft Azure Maps
description: Lernen Sie, Punkte auf Karten als Kreise mit fester Größe zu rendern. Erfahren Sie, wie Sie das Azure Maps Android SDK verwenden, um Blasenebenen zu diesem Zweck hinzuzufügen und anzupassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ef4d397520e375059cfcfaca0786bafbb41ccc1e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430628"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Hinzufügen einer Blasenebene zu einer Karte (Android SDK)

In diesem Artikel erfahren Sie, wie Sie Punktdaten aus einer Datenquelle als Blasenebene auf einer Karte rendern können. Blasenebenen rendern Punkte als Kreise mit einem festen Pixelradius auf der Karte.

> [!TIP]
> Blasenebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Option `filter` der Ebene auf `eq(geometryType(), "Point")` fest, um die Ebene so zu beschränken, dass nur Punktgeometriefeatures gerendert werden. Wenn Sie auch MultiPoint-Features einschließen möchten, legen Sie die Option `filter` der Ebene auf `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` fest.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen. Codeblöcke in diesem Artikel können in den `onReady`-Ereignishandler von Karten eingefügt werden.

## <a name="add-a-bubble-layer"></a>Hinzufügen einer Blasenebene

Mit dem folgenden Code wird ein Array von Punkten in eine Datenquelle geladen. Anschließend werden die Datenpunkte mit einer Blasenebene verbunden. Die Blasenebene rendert den Radius jeder Blase mit fünf Pixeln und einer weißen Füllfarbe sowie einer blauen Strichfarbe und einer Strichbreite von sechs Pixeln.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

Der folgende Screenshot veranschaulicht, wie durch den oben aufgeführten Code Punkte in einer Blasenebene gerendert werden.

![Karte mit Punkten, die unter Verwendung einer Blasenebene gerendert wurden](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Anzeigen von Bezeichnungen bei einer Blasenebene

Dieser Code zeigt, wie Sie eine Blasenebene zum Rendern eines Punkts auf der Karte verwenden. Außerdem wird gezeigt, wie Sie eine Symbolebene verwenden, um eine Bezeichnung zu rendern. Legen Sie die Option `iconImage` auf `"none"` fest, um das Symbol der Symbolebene auszublenden.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

Der folgende Screenshot veranschaulicht, wie durch den obigen Code ein Punkt in einer Blasenebene und eine Beschriftung für den Punkt in einer Symbolebene gerendert werden.

![Karte mit Punkt, der unter Verwendung einer Blasenebene gerendert wurde, und einer Beschriftung, die unter Verwendung einer Symbolebene gerendert wurde](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Clusterpunktdaten](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Anzeigen von Featureinformationen](display-feature-information-android.md)
