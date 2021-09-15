---
title: Hinzufügen einer Wärmebildebene zu Android-Karten | Microsoft Azure Maps
description: Informationen zum Erstellen eines Wärmebilds. Erfahren Sie, wie Sie einer Karte mit dem Azure Maps Android SDK eine Wärmebildebene hinzufügen. Lernen Sie, Wärmebildebenen anzupassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5e48be82a2486291d2b7fdecf42d759e2c13eb08
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425192"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Hinzufügen einer Wärmebildebene (Android SDK)

Bei Wärmebildern (auch Punktdichtekarten genannt) handelt es sich um einen Datenvisualisierungstyp. Sie dienen dazu, die Dichte von Daten mithilfe von Farbbereichen darzustellen und die „Hotspots“ der Daten auf einer Karte zu zeigen. Wärmebilder sind eine hervorragende Möglichkeit, Datensätze mit einer großen Anzahl von Punkten zu rendern.

Das Rendering von Zehntausenden von Punkten als Symbole kann den größten Teil des Kartenbereichs abdecken. Dieser Fall führt wahrscheinlich zu vielen überlappenden Symbolen. Das gestaltet es schwierig, ein besseres Verständnis der Daten zu erlangen. Wenn Sie dasselbe Dataset jedoch als Wärmebild visualisieren, können Sie einfach die Dichte und die relative Dichte der einzelnen Datenpunkte erkennen.

Sie können Wärmebilder in vielen verschiedenen Szenarien verwenden, z. B.:

- **Temperaturdaten:** Bietet Näherungen für die Temperatur zwischen zwei Datenpunkten.
- **Daten für Geräuschsensoren:** Zeigt nicht nur die Intensität der Geräusche am Standort des Sensors, sondern auch deren Dissipation über eine gewisse Entfernung. Der Geräuschpegel ist an einem bestimmten Standort möglicherweise nicht sehr hoch. Wenn sich aber die Geräuschebenen von mehreren Sensoren überlappen, können in diesem überlappenden Bereich höhere Geräuschpegel auftreten. Der überlappende Bereich wäre daher im Wärmebild sichtbar.
- **GPS-Überwachung:** Zeigt die Geschwindigkeit als gewichtete Höhenkarte, bei der die Intensität der einzelnen Datenpunkte auf der Geschwindigkeit basiert. Diese Funktionalität bietet zum Beispiel eine Möglichkeit zu erkennen, wo ein Fahrzeug beschleunigt wurde.

> [!TIP]
> Wärmebildebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Option `filter` der Ebene auf `eq(geometryType(), "Point")` fest, um die Ebene so zu beschränken, dass nur Punktgeometriefeatures gerendert werden. Wenn Sie auch MultiPoint-Features einschließen möchten, legen Sie die Option `filter` der Ebene auf `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` fest.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen. Codeblöcke in diesem Artikel können in den `onReady`-Ereignishandler von Karten eingefügt werden.

## <a name="add-a-heat-map-layer"></a>Hinzufügen einer Wärmebildebene

Wenn Sie eine Datenquelle, die aus Punkten besteht, als Wärmebild rendern möchten, übergeben Sie diese an eine Instanz der `HeatMapLayer`-Klasse, und fügen Sie sie der Karte hinzu.

Im folgenden Codebeispiel wird ein GeoJSON-Feed von Erdbeben aus der letzten Woche geladen und als Wärmebild gerendert. Jeder Datenpunkt wird bei allen Zoomfaktoren mit einem Radius von 10 Pixeln gerendert. Das Wärmebild befindet sich unter der Bezeichnungsebene, um eine bessere Benutzerfreundlichkeit und deutliche Sichtbarkeit der Bezeichnungen sicherzustellen. Die Daten in diesem Beispiel stammen aus dem [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). In diesem Beispiel werden GeoJSON-Daten mithilfe des im Dokument [Erstellen einer Datenquelle](create-data-source-android-sdk.md) bereitgestellten Codeblocks des Datenimport-Hilfsprogramms aus dem Web geladen.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")
```

::: zone-end

Der folgende Screenshot veranschaulicht, wie mithilfe des oben aufgeführten Codes ein Wärmebild in einer Karte geladen wird.

![Karte mit einer Wärmebildebene der letzten Erdbeben](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Anpassen der Wärmebildebene

Im vorherigen Beispiel wurde das Wärmebild angepasst, indem der Radius und die Deckkraft angepasst wurden. Auf der Wärmebildebene können verschiedene Optionen angepasst werden, z. B.:

- `heatmapRadius`: Definiert einen Pixelradius, in dem die einzelnen Datenpunkte gerendert werden sollen. Der Radius kann als feste Zahl oder als Ausdruck festgelegt werden. Mithilfe eines Ausdrucks können Sie den Radius auf der Grundlage des Zoomfaktors skalieren. Er stellt dann ein zusammenhängendes räumliches Gebiet auf der Karte dar (beispielsweise einen 5-Meilen-Radius).
- `heatmapColor`: Gibt an, welche Farben für das Wärmebild verwendet werden. Ein Farbverlauf ist ein häufiges Merkmal von Wärmebildern. Sie können den Effekt mit einem `interpolate`-Ausdruck erreichen. Sie können auch einen `step`-Ausdruck für die farbige Darstellung des Wärmebilds verwenden. In diesem Fall wird die Dichte visuell in Bereiche gegliedert, die einer Kontur- oder Radarkarte ähneln. Durch diese Farbpaletten werden die Farben kleinsten bis zum größten Dichtewert definiert.

  Sie geben die Farbwerte für Wärmebilder als Ausdruck im `heatmapDensity`-Wert an. Die Farbe des Bereichs, in dem keine Daten vorhanden sind, wird bei Index 0 des „Interpolation“-Ausdrucks oder der Standardfarbe eines „abgestuften“ Ausdrucks definiert. Sie können diesen Wert verwenden, um eine Hintergrundfarbe zu definieren. Häufig wird hierfür ein transparenter Wert oder ein halbtransparentes Schwarz festgelegt.

  Nachfolgend finden Sie Beispiele für Farbausdrücke:

  | Ausdruck für Farbinterpolation | Ausdruck für eine abgestufte Farbpalette |
  |--------------------------------|--------------------------|
  | interpolate(<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear(), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0, color(Color.TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(Color.MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(parseColor("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(parseColor("#00c3ff")))<br/>)` | step(<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;color(Color.TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.25, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(Color.RED))<br/>) |

- `heatmapOpacity`: Gibt an, wie undurchsichtig oder transparent die Wärmebildebene ist.
- `heatmapIntensity`: Wendet einen Multiplikator auf die Gewichtung jedes Datenpunkts an, um die Gesamtintensität des Wärmebilds zu erhöhen. Dies verändert die Gewichtung der Datenpunkte, was die Visualisierung erleichtert.
- `heatmapWeight`: Standardmäßig weisen alle Datenpunkte eine Gewichtung von 1 auf, sodass alle Datenpunkte gleichmäßig gewichtet werden. Die Option „weight“ fungiert als Multiplikator und kann als Zahl oder als Ausdruck festgelegt werden. Wenn eine Zahl als Gewichtung festgelegt wird, entspricht das der zweifachen Platzierung jedes Datenpunkts auf der Karte. Wenn die Gewichtung z. B. `2` beträgt, dann verdoppelt sich die Dichte. Wenn Sie die Option „weight“ auf eine Zahl festlegen, wird das Wärmebild ähnlich wie mit der Option „intensity“ gerendert.

  Wenn Sie jedoch einen Ausdruck verwenden, kann die Gewichtung der einzelnen Datenpunkte auf den Eigenschaften der einzelnen Datenpunkte basieren. Gehen Sie beispielsweise davon aus, dass jeder Datenpunkt ein Erdbeben darstellt. Der Magnitudenwert ist für jeden Erdbeben-Datenpunkt eine wichtige Metrik gewesen. Es kommt ständig zu Erdbeben. Die meisten weisen jedoch eine so geringe Stärke auf, dass sie nicht bemerkt werden. Verwenden Sie den Magnitudenwert in einem Ausdruck, um jedem Datenpunkt die Gewichtung zuzuweisen. Das Verwenden des Magnitudenwerts, um die Gewichtung zuzuweisen, ermöglicht eine bessere Darstellung der Bedeutung von Erdbeben im Wärmebild.
- `minZoom` und `maxZoom`: Der Zoomfaktorbereich, in dem die Ebene angezeigt werden soll.
- `filter`: Ein Filterausdruck, der verwendet wird, um die aus der Quelle abgerufenen und in der Ebene gerenderten Daten zu beschränken.
- `sourceLayer`: Wenn die mit der Ebene verbundene Datenquelle eine Vektorkachelquelle darstellt, muss in den Vektorkacheln eine Quellebene angegeben werden.
- `visible`: Blendet die Ebene aus oder ein.

Im Folgenden ein Beispiel für ein Wärmebild, in dem ein Ausdruck für eine lineare Interpolation verwendet wird, um einen glatten Farbverlauf zu erzielen. Die in den Daten definierte `mag`-Eigenschaft wird mit einer exponentiellen Interpolation verwendet, um die Gewichtung oder Relevanz der einzelnen Datenpunkte festzulegen.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

Im folgenden Screenshot ist die oben aufgeführte benutzerdefinierte Wärmebildebene mit denselben Daten wie im vorherigen Wärmebildbeispiel zu sehen.

![Karte mit einer benutzerdefinierten Wärmebildebene der letzten Erdbeben](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Konsistentes zoombares Wärmebild

Standardmäßig ist für die in der Wärmebildebene gerenderten Daten ein fester Pixelradius für alle Zoomfaktoren definiert. Wenn Sie die Karte zoomen, werden die Daten aggregiert und die Wärmebildebene ändert sich. Das folgende Video veranschaulicht das Standardverhalten des Wärmebilds. Dabei wird der Pixelradius beim Zoomen der Karte beibehalten.

![Animation einer gezoomten Karte mit einer Wärmebildebene, die eine konsistente Pixelgröße aufweist](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Zum Skalieren des Radius für jede Zoomebene können Sie einen `zoom`-Ausdruck verwenden, sodass jeder Datenpunkt den gleichen physischen Bereich der Karte abdeckt. Durch diesen Ausdruck sieht die Wärmebildebene statischer und konsistenter aus. Jede Zoomebene der Karte hat vertikal und horizontal doppelt so viele Pixel wie die vorherige Zoomebene.

Wenn der Radius so skaliert wird, dass er sich mit jeder Zoomebene verdoppelt, wird ein Wärmebild erstellt, das auf allen Zoomebenen einheitlich aussieht. Verwenden Sie zum Anwenden dieser Skalierung Folgendes (wie im folgenden Beispiel veranschaulicht): `zoom` mit dem Basis-2-Ausdruck `exponential interpolation`, einen festgelegten Pixelradius für den minimalen Zoomfaktor und einen skalierten Radius für den maximalen Zoomfaktor mit der Berechnung `2 * Math.pow(2, minZoom - maxZoom)`. Zoomen Sie die Karte, um zu sehen, wie das Wärmebild mit der Zoomebene skaliert wird.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

Das folgende Video zeigt eine Karte, die unter Verwendung des oben aufgeführten Codes ausgeführt wird. Beim Zoomen der Karte wird der Radius skaliert, damit das Wärmebild bei allen Zoomfaktoren auf konsistente Weise gerendert werden kann.

![Animation einer gezoomten Karte mit einer Wärmebildebene, die eine konsistente räumliche Größe aufweist](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

Der `zoom`-Ausdruck kann nur in `step`- und `interpolate`-Ausdrücken verwendet werden. Der folgende Ausdruck kann für eine ungefähre Berechnung eines Radius in Metern verwendet werden. Dieser Ausdruck verwendet den Platzhalter `radiusMeters`, den Sie durch den gewünschten Radius ersetzen. Dieser Ausdruck berechnet den ungefähren Pixelradius für einen Zoomfaktor am Äquator für die Zoomfaktoren 0 und 24 und verwendet einen `exponential interpolation`-Ausdruck, um auf dieselbe Weise zwischen diesen Werten zu skalieren, wie das Kachelsystem in der Karte funktioniert.

::: zone pivot="programming-language-java-android"

```java
interpolate(
    exponential(2),
    zoom(),
    stop(1, product(radiusMeters, 0.000012776039596366526)),
    stop(24, product(radiusMeters, 214.34637593279402))
)
```

> [!TIP]
> Wenn Sie das Clustering für die Datenquelle aktivieren, werden die Punkte, die nah beieinander liegen, zu einem Punkt gruppiert. Sie können die Punktzahl der einzelnen Cluster als Gewichtungsausdruck für das Wärmebild verwenden. Dadurch kann die Anzahl der zu rendernden Punkte erheblich reduziert werden. Die Anzahl der Punkte in einem Cluster wird wie im Folgenden veranschaulicht in einer `point_count`-Eigenschaft der point-Funktion gespeichert.
>
> ```java
> HeatMapLayer layer = new HeatMapLayer(dataSource,
>    heatmapWeight(get("point_count"))
> );
> ```
>
> Wenn der Radius für das Clustering nur wenige Pixel beträgt, gibt es beim Rendern nur geringe sichtbare Unterschiede. Bei einem größeren Radius werden mehr Punkte zusammengefasst und damit die Leistung des Wärmebilds verbessert.

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
interpolate(
    exponential(2),
    zoom(),
    stop(1, product(radiusMeters, 0.000012776039596366526)),
    stop(24, product(radiusMeters, 214.34637593279402))
)
```

> [!TIP]
> Wenn Sie das Clustering für die Datenquelle aktivieren, werden die Punkte, die nah beieinander liegen, zu einem Punkt gruppiert. Sie können die Punktzahl der einzelnen Cluster als Gewichtungsausdruck für das Wärmebild verwenden. Dadurch kann die Anzahl der zu rendernden Punkte erheblich reduziert werden. Die Anzahl der Punkte in einem Cluster wird wie im Folgenden veranschaulicht in einer `point_count`-Eigenschaft der point-Funktion gespeichert.
>
> ```kotlin
> var layer = new HeatMapLayer(dataSource,
>    heatmapWeight(get("point_count"))
> )
> ```
>
> Wenn der Radius für das Clustering nur wenige Pixel beträgt, gibt es beim Rendern nur geringe sichtbare Unterschiede. Bei einem größeren Radius werden mehr Punkte zusammengefasst und damit die Leistung des Wärmebilds verbessert.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Weitere Codebeispiele, die Sie zu Ihren Karten hinzufügen können, finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-android-sdk.md)
