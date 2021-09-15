---
title: Hinzufügen einer Polygonebene zu Android-Karten | Microsoft Azure Maps
description: Hier wird erläutert, wie Sie Karten Polygone oder Kreise hinzufügen. Hier erfahren Sie, wie Sie mit dem Azure Maps Android SDK geometrische Formen anpassen und wie sich diese einfach aktualisieren und verwalten lassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5659e366fd5c949ea374768bf848313d747896d2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425833"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Hinzufügen einer Polygonebene zur Karte (Android SDK)

In diesem Artikel erfahren Sie, wie Sie mithilfe einer Polygonebene die Bereiche der `Polygon`- und `MultiPolygon`-Featuregeometrien auf einer Karte rendern können.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen. Codeblöcke in diesem Artikel können in den `onReady`-Ereignishandler von Karten eingefügt werden.

## <a name="use-a-polygon-layer"></a>Verwenden einer Polygonebene

Wenn eine Polygonebene mit einer Datenquelle verbunden ist und in die Karte geladen wird, werden die Bereiche mit `Polygon`- und `MultiPolygon`-Features gerendert. Fügen Sie zum Erstellen eines Polygons dieses zu einer Datenquelle hinzu und rendern Sie es mit einer Polygonebene unter Verwendung der `PolygonLayer`-KIasse.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("red"),
        fillOpacity(0.7f)
    ), "labels"
)
```

::: zone-end

Der folgende Screenshot zeigt den obigen Code, der den Bereich eines Polygons mithilfe einer Polygonebene rendert.

![Polygon mit gerendertem Füllbereich](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Verwenden von Polygon- und Linienebene

Eine Linienebene wird verwendet, um die Kontur von Polygonen zu rendern. Das folgende Codebeispiel rendert ebenso wie das vorherige Beispiel ein Polygon, fügt aber eine Linienebene hinzu. Diese Linienebene ist eine zweite Ebene, die mit der Datenquelle verbunden ist.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("rgba(0, 200, 200, 0.5)")
    ), "labels"
)

//Create and add a line layer to render the outline of the polygon.
map.layers.add(
    LineLayer(
        source,
        strokeColor("red"),
        strokeWidth(2f)
    )
)
```

::: zone-end

Der folgende Screenshot zeigt den obigen Code, der ein Polygon mit gerenderter Kontur mithilfe einer Linienebene rendert.

![Polygon, dessen Füllfläche und Kontur gerendert wurden](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Wenn Sie ein Polygon mit einer Linienebene gliedern, achten Sie darauf, alle Ringe in Polygonen zu schließen, sodass jedes Array von Punkten denselben Start- und Endpunkt aufweist. Wenn dies nicht erfolgt, stellt die Linienebene möglicherweise keine Verbindung zwischen dem letzten Punkt des Polygons und dem ersten Punkt her.

## <a name="fill-a-polygon-with-a-pattern"></a>Füllen eines Polygons mit einem Muster

Zusätzlich zum Füllen eines Polygons mit einer Farbe können Sie auch ein Bildmuster verwenden, um das Polygon zu füllen. Laden Sie ein Bildmuster in die Sprite-Ressourcen für Kartenbilder, und verweisen Sie dann mit der `fillPattern`-Option der Polygonebene (PolygonLayer) auf dieses Bild.

::: zone pivot="programming-language-java-android"

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-50, -20),
                Point.fromLngLat(0, 40),
                Point.fromLngLat(50, -20),
                Point.fromLngLat(-50, -20)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
    ), "labels"
)
```

::: zone-end

In diesem Beispiel wurde die folgende Abbildung in den Ordner „drawable“ der App geladen.

| ![Lilafarbenes Pfeilsymbol](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

Der folgende Screenshot zeigt den obigen Code, der ein Polygon mit einem Füllmuster auf der Karte rendert.

![Auf der Karte gerendertes Polygon mit einem Füllmuster](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonextrusionsebene](map-extruded-polygon-android.md)
