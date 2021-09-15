---
title: Clustering von Punktdaten im Android SDK | Microsoft Azure Maps
description: Hier wird erläutert, wie Sie Punktdaten auf Karten gruppieren. Hier erfahren Sie, wie Sie mit dem Azure Maps Android SDK Daten clustern, auf Mausereignisse für den Cluster reagieren und Clusteraggregate anzeigen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/23/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a6b9de675c32c893e872d23835c856a0fe6c49d3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429818"
---
# <a name="clustering-point-data-in-the-android-sdk"></a>Clustering von Punktdaten im Android SDK

Wenn auf der Karte viele Datenpunkte visualisiert werden, kann es bei diesen Datenpunkten zu gegenseitigen Überlappungen kommen. Die Überlappungen können dazu führen, dass die Karte unlesbar und schwer zu verwenden ist. Das Clustering von Punktdaten ist der Prozess, bei dem benachbarte Punktdaten kombiniert und auf der Karte als ein einzelner gruppierter Datenpunkt dargestellt werden. Wenn der Benutzer in die Karte zoomt, werden die Cluster in ihre einzelnen Datenpunkte unterteilt. Wenn Sie mit einer großen Anzahl von Datenpunkten arbeiten, verwenden Sie die Clusteringprozesse, um die Erfahrung Ihrer Benutzer zu verbessern.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen. Codeblöcke in diesem Artikel können in den `onReady`-Ereignishandler von Karten eingefügt werden.

## <a name="enabling-clustering-on-a-data-source"></a>Aktivieren des Clusterings für eine Datenquelle

Aktivieren des Clusterings in der `DataSource`-Klasse, indem Sie die Option `cluster` auf `true` festlegen. Durch Festlegen von `clusterRadius` werden nahe gelegene Punkte ausgewählt und zu einem Cluster kombiniert. Der Wert von `clusterRadius` wird in Pixel angegeben. Verwenden Sie `clusterMaxZoom`, um einen Zoomfaktor anzugeben, ab dem die Clusteringlogik deaktiviert werden soll. Hier folgt ein Beispiel, wie Sie das Clustering in einer Datenquelle aktivieren können.

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and enable clustering.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
 //Create a data source and enable clustering.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(45),  

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
)
```

::: zone-end

> [!CAUTION]
> Clustering funktioniert nur mit `Point`-Features. Wenn Ihre Datenquelle Features anderer Geometrietypen enthält (z. B. `LineString` oder `Polygon`), tritt ein Fehler auf.

> [!TIP]
> Wenn zwei Datenpunkte im Gelände dicht beieinander liegen, ist es möglich, dass der Cluster nie aufgelöst wird, unabhängig davon, wie stark der Benutzer vergrößert. Um dies zu umgehen, können Sie die Option `clusterMaxZoom` so festlegen, dass die Clusteringlogik deaktiviert und somit einfach alles angezeigt wird.

Die Klasse `DataSource` stellt im Hinblick auf das Clustering auch die folgenden Methoden bereit.

| Methode | Rückgabetyp | BESCHREIBUNG |
|--------|-------------|-------------|
| `getClusterChildren(Feature clusterFeature)` | `FeatureCollection` | Ruft die untergeordneten Elemente des angegebenen Clusters für den nächsten Zoomfaktor ab. Diese untergeordneten Elemente können eine Kombination aus Formen und untergeordneten Clustern sein. Die untergeordneten Cluster sind Features mit Eigenschaften, die ClusteredProperties entsprechen. |
| `getClusterExpansionZoom(Feature clusterFeature)` | `int` | Berechnet einen Zoomfaktor, bei dem der Cluster mit der Erweiterung oder Unterteilung beginnt. |
| `getClusterLeaves(Feature clusterFeature, long limit, long offset)` | `FeatureCollection` | Ruft alle Punkte in einem Cluster ab. Legen Sie `limit` fest, um eine Teilmenge der Punkte zurückzugeben, und verwenden Sie `offset`, um die Punkte zu durchlaufen. |

## <a name="display-clusters-using-a-bubble-layer"></a>Anzeigen von Clustern mithilfe einer Blasenebene

Eine Blasenebene ist eine hervorragende Möglichkeit, um geclusterte Datenpunkte zu rendern. Verwenden Sie Ausdrücke zum Skalieren des Radius, und ändern Sie die Farbe basierend auf der Anzahl der Punkte im Cluster. Bei der Darstellung von Clustern mit einer Blasenebene sollten Sie eine separate Ebene für das Rendering nicht geclusterter Datenpunkte verwenden.

Um die Größe des Clusters auf der Blase anzuzeigen, verwenden Sie eine Symbolebene mit Text und verwenden kein Symbol.

Der folgende Code zeigt geclusterte Punkte mithilfe einer Blasenebene und die Anzahl von Punkten in jedem Cluster mithilfe einer Symbolebene an. Eine zweite Symbolebene wird verwendet, um einzelne Punkte anzuzeigen, die sich nicht innerhalb eines Clusters befinden.

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
);

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a bubble layer for rendering clustered data points.
map.layers.add(new BubbleLayer(source,
    //Scale the size of the clustered bubble based on the number of points in the cluster.
    bubbleRadius(
        step(
            get("point_count"),
            20,             //Default of 20 pixel radius.
            stop(100, 30),  //If point_count >= 100, radius is 30 pixels.
            stop(750, 40)   //If point_count >= 750, radius is 40 pixels.
        )
    ),

    //Change the color of the cluster based on the value on the point_cluster property of the cluster.
    bubbleColor(
        step(
            toNumber(get("point_count")),
            color(Color.GREEN),              //Default to lime green.
            stop(100, color(Color.YELLOW)),  //If the point_count >= 100, color is yellow.
            stop(750, color(Color.RED))      //If the point_count >= 100, color is red.
        )
    ),

    bubbleStrokeWidth(0f),

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
));

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(new SymbolLayer(source,
    iconImage("none"),                //Hide the icon image.
    textField(get("point_count")),    //Display the point count as text.
    textOffset(new Float[]{ 0f, 0.4f }),

    //Allow clustered points in this layer.
    SymbolLayerOptions.filter(has("point_count"))
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count")))
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(45),  

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
)

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a bubble layer for rendering clustered data points.
map.layers.add(
    BubbleLayer(
        source,  

        //Scale the size of the clustered bubble based on the number of points in the cluster.
        bubbleRadius(
            step(
                get("point_count"),
                20,  //Default of 20 pixel radius.
                stop(100, 30),  //If point_count >= 100, radius is 30 pixels.
                stop(750, 40) //If point_count >= 750, radius is 40 pixels.
            )
        ),  

        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
        bubbleColor(
            step(
                toNumber(get("point_count")),
                color(Color.GREEN),  //Default to lime green.
                stop(100, color(Color.YELLOW)),  //If the point_count >= 100, color is yellow.
                stop(750, color(Color.RED)) //If the point_count >= 100, color is red.
            )
        ),
        bubbleStrokeWidth(0f),  

        //Only rendered data points which have a point_count property, which clusters do.
        BubbleLayerOptions.filter(has("point_count"))
    )
)

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(
    SymbolLayer(
        source,
        iconImage("none"),  //Hide the icon image.
        textField(get("point_count")),  //Display the point count as text.
        textOffset(arrayOf(0f, 0.4f)),  

        //Allow clustered points in this layer.
        SymbolLayerOptions.filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)
```

::: zone-end

Die folgende Abbildung zeigt, wie der obige Code geclusterte Punktfeatures skaliert und basierend auf der Anzahl von Punkten im Cluster farbig dargestellt in einer Blasenebene anzeigt. Nicht geclusterte Punkte werden mithilfe einer Symbolebene gerendert.

![Karte geclusterter Standorte, die beim Vergrößern der Karte aufgelöst bzw. unterteilt werden](media/clustering-point-data-android-sdk/android-cluster-bubble-layer.gif)

## <a name="display-clusters-using-a-symbol-layer"></a>Anzeigen von Clustern mithilfe einer Symbolebene

Beim Visualisieren von Datenpunkten blendet die Symbolebene automatisch Symbole aus, die einander überlappen, um eine klarere Benutzeroberfläche sicherzustellen. Dieses Standardverhalten ist möglicherweise unerwünscht, wenn Sie die Datenpunktdichte auf der Karte anzeigen möchten. Diese Einstellungen können jedoch geändert werden. Um alle Symbole anzuzeigen, legen Sie die Option `iconAllowOverlap` der Symbolebenen auf `true` fest.

Verwenden Sie Clustering, um die Datenpunktdichte anzuzeigen und gleichzeitig eine aufgeräumte Benutzeroberfläche zu erhalten. Im Folgenden Beispiel wird gezeigt, wie Sie mithilfe der Symbolebene benutzerdefinierte Symbole hinzufügen sowie Cluster und einzelne Datenpunkte darstellen.

::: zone pivot="programming-language-java-android"

``` java
//Load all the custom image icons into the map resources.
map.images.add("earthquake_icon", R.drawable.earthquake_icon);
map.images.add("warning_triangle_icon", R.drawable.warning_triangle_icon);

//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true)
);

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a symbol layer to render the clusters.
map.layers.add(new SymbolLayer(source,
    iconImage("warning_triangle_icon"),
    textField(get("point_count")),
    textOffset(new Float[]{ 0f, -0.4f }),

    //Allow clustered points in this layer.
    filter(has("point_count"))
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    iconImage("earthquake_icon"),

    //Filter out clustered points from this layer.
    filter(not(has("point_count")))
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load all the custom image icons into the map resources.
map.images.add("earthquake_icon", R.drawable.earthquake_icon)
map.images.add("warning_triangle_icon", R.drawable.warning_triangle_icon)

//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true)
)

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a symbol layer to render the clusters.
map.layers.add(
    SymbolLayer(
        source,
        iconImage("warning_triangle_icon"),
        textField(get("point_count")),
        textOffset(arrayOf(0f, -0.4f)),  

        //Allow clustered points in this layer.
        filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,
        iconImage("earthquake_icon"),  

        //Filter out clustered points from this layer.
        filter(not(has("point_count")))
    )
)
```

::: zone-end

In diesem Beispiel wird die folgende Abbildung in den Ordner „drawable“ der App geladen.

| ![Abbildung des Erdbebensymbols](media/clustering-point-data-android-sdk/earthquake-icon.png) | ![Wettersymbolbild für Regenschauer](media/clustering-point-data-android-sdk/warning-triangle-icon.png) |
|:-----------------------:|:--------------------------:|
| earthquake_icon.png     | warning_triangle_icon.png  |

Die folgende Abbildung zeigt, wie der obige Code geclusterte und nicht geclusterte Punktfeatures mit benutzerdefinierten Symbolen rendert.

![Karte geclusterter Punkte, die mithilfe einer Symbolebene gerendert werden](media/clustering-point-data-android-sdk/android-cluster-symbol-layer.gif)

## <a name="clustering-and-the-heat-maps-layer&quot;></a>Clustering und Wärmebildebene

Wärmebilder sind eine hervorragende Möglichkeit, die Dichte der Daten auf der Karte anzuzeigen. Diese Visualisierungsmethode kann eigenständig eine große Anzahl von Datenpunkten verarbeiten. Wenn die Datenpunkte geclustert werden und die Clustergröße als Gewichtung des Wärmebilds verwendet wird, kann das Wärmebild sogar noch mehr Daten handhaben. Um diese Option zu realisieren, legen Sie die `heatmapWeight`-Option der Wärmebildebene auf `get(&quot;point_count")` fest. Bei einem kleinen Clusterradius sieht das Wärmebild nahezu identisch zu einem Wärmebild aus, das die nicht gruppierten Datenpunkte verwendet. Es wird aber viel besser funktionieren. Je kleiner der Clusterradius, desto genauer ist das Wärmebild, jedoch mit geringerem Leistungsvorteil.

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(10)
);

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a heat map and add it to the map.
map.layers.add(new HeatMapLayer(source,
    //Set the weight to the point_count property of the data points.
    heatmapWeight(get("point_count")),

    //Optionally adjust the radius of each heat point.
    heatmapRadius(20f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(10)
)

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a heat map and add it to the map.
map.layers.add(
    HeatMapLayer(
        source,  
        
        //Set the weight to the point_count property of the data points.
        heatmapWeight(get("point_count")),  

        //Optionally adjust the radius of each heat point.
        heatmapRadius(20f)
    ), "labels"
)
```

::: zone-end

Die folgende Abbildung zeigt, wie der obige Code ein Wärmebild anzeigt, das mithilfe geclusterter Punktfeatures und der Clusteranzahl als Gewichtung im Wärmebild optimiert wurde.

![Karte eines Wärmebilds, das mit geclusterten Punkten als Gewichtung optimiert wurde](media/clustering-point-data-android-sdk/android-cluster-heat-map.gif)

## <a name="mouse-events-on-clustered-data-points"></a>Mausereignisse für gruppierte Datenpunkte

Wenn Mausereignisse auf einer Ebene auftreten, die gruppierte Datenpunkte enthält, wird der gruppierte Datenpunkt als GeoJSON-Punktfeatureobjekt an das Ereignis zurückgegeben. Dieses Punktfeature weist die folgenden Eigenschaften auf:

| Eigenschaftenname             | type    | BESCHREIBUNG   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Gibt an, ob das Feature einen Cluster darstellt. |
| `point_count`             | number  | Die Anzahl der Punkte, die der Cluster enthält.  |
| `point_count`             | number  | Die Anzahl der Punkte, die der Cluster enthält.  |
| `point_count_abbreviated` | Zeichenfolge  | Eine Zeichenfolge, die den `point_count`-Wert abkürzt, falls er zu lang ist. (Beispiel: 4.000 wird zu 4K)  |

Dieses Beispiel nutzt eine Blasenebene, die Clusterpunkte rendert und ein Klickereignis hinzufügt. Wenn das Klickereignis ausgelöst wird, berechnet der Code die Karte und zoomt Sie auf die nächste Zoomstufe, bei der der Cluster aufgelöst wird. Diese Funktion wird mit der `getClusterExpansionZoom`-Methode der `DataSource`-Klasse und der `cluster_id`-Eigenschaft des angeklickten geclusterten Datenpunkts implementiert.

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
);

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a bubble layer for rendering clustered data points.
BubbleLayer clusterBubbleLayer = new BubbleLayer(source,
    //Scale the size of the clustered bubble based on the number of points in the cluster.
    bubbleRadius(
        step(
            get("point_count"),
            20f,    //Default of 20 pixel radius.
            stop(100, 30),   //If point_count >= 100, radius is 30 pixels.
            stop(750, 40)    //If point_count >= 750, radius is 40 pixels.
        )
    ),

    //Change the color of the cluster based on the value on the point_cluster property of the cluster.
    bubbleColor(
        step(
            get("point_count"),
            color(Color.GREEN),  //Default to green.
            stop(100, color(Color.YELLOW)), //If the point_count >= 100, color is yellow.
            stop(750, color(Color.RED))     //If the point_count >= 100, color is red.
        )
    ),

    bubbleStrokeWidth(0f),

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))   
);

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer);

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),   

    //Display the 'point_count_abbreviated' property value.
    textField(get("point_count_abbreviated")), 
    
    //Offset the text position so that it's centered nicely.
    textOffset(new Float[] { 0f, 0.4f }),

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count")) 
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count"))) 
));

//Add a click event to the cluster layer so we can zoom in when a user clicks a cluster.
map.events.add((OnFeatureClick) (features) -> {
    if(features.size() > 0) {
        //Get the clustered point from the event.
        Feature cluster = features.get(0);

        //Get the cluster expansion zoom level. This is the zoom level at which the cluster starts to break apart.
        int expansionZoom = source.getClusterExpansionZoom(cluster);

        //Update the map camera to be centered over the cluster.
        map.setCamera(
            //Center the map over the cluster points location.
            center((Point)cluster.geometry()),

            //Zoom to the clusters expansion zoom level.
            zoom(expansionZoom),

            //Animate the movement of the camera to the new position.
            animationType(AnimationType.EASE),
            animationDuration(200)
        );
    }

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, clusterBubbleLayer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( //Tell the data source to cluster point data.
    //The radius in pixels to cluster points together.
    cluster(true),  

    //The maximum zoom level in which clustering occurs.
    clusterRadius(45),  

    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
)

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a bubble layer for rendering clustered data points.
val clusterBubbleLayer = BubbleLayer(
    source,  

    //Scale the size of the clustered bubble based on the number of points in the cluster.
    bubbleRadius(
        step(
            get("point_count"),
            20f,  //Default of 20 pixel radius.
            stop(100, 30),  //If point_count >= 100, radius is 30 pixels.
            stop(750, 40) //If point_count >= 750, radius is 40 pixels.
        )
    ),  
    
    //Change the color of the cluster based on the value on the point_cluster property of the cluster.
    bubbleColor(
        step(
            get("point_count"),
            color(Color.GREEN),  //Default to green.
            stop(
                100,
                color(Color.YELLOW)
            ),  //If the point_count >= 100, color is yellow.
            stop(750, color(Color.RED)) //If the point_count >= 100, color is red.
        )
    ),

    bubbleStrokeWidth(0f),  

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
)

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer)

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(
    SymbolLayer(
        source,  

        //Hide the icon image.
        iconImage("none"),  

        //Display the 'point_count_abbreviated' property value.
        textField(get("point_count_abbreviated")),  

        //Offset the text position so that it's centered nicely.
        textOffset(
            arrayOf(
                0f,
                0.4f
            )
        ),  

        //Only rendered data points which have a point_count property, which clusters do.
        SymbolLayerOptions.filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)

//Add a click event to the cluster layer so we can zoom in when a user clicks a cluster.
map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    if (features.size() > 0) {
        //Get the clustered point from the event.
        val cluster: Feature = features.get(0)

        //Get the cluster expansion zoom level. This is the zoom level at which the cluster starts to break apart.
        val expansionZoom: Int = source.getClusterExpansionZoom(cluster)

        //Update the map camera to be centered over the cluster.
        map.setCamera( 

            //Center the map over the cluster points location.
            center(cluster.geometry() as Point?),  

            //Zoom to the clusters expansion zoom level.
            zoom(expansionZoom),  

            //Animate the movement of the camera to the new position.
            animationType(AnimationType.EASE),
            animationDuration(200)
        )
    }
    true
}, clusterBubbleLayer)
```

::: zone-end

Die folgende Abbildung zeigt, wie der obige Code geclusterte Punkte auf einer Karte anzeigt, die beim Klicken auf den nächsten Zoomfaktor vergrößert werden, ab dem ein Cluster unterteilt und erweitert wird.

![Karte geclusterter Features, die beim Klicken vergrößert und unterteilt werden](media/clustering-point-data-android-sdk/android-cluster-expansion.gif)

## <a name="display-cluster-area"></a>Anzeigen des Clusterbereichs

Die Punktdaten, die ein Cluster darstellt, sind über einen Bereich verteilt. In diesem Beispiel treten zwei Hauptverhaltensweisen auf, wenn mit der Maus auf einen Cluster gezeigt wird. Zuerst werden die einzelnen Datenpunkte, die im Cluster enthalten sind, verwendet, um eine konvexe Hülle zu berechnen. Anschließend wird die konvexe Hülle auf der Karte angezeigt, um einen Bereich darzustellen.  Eine konvexe Hülle ist ein Polygon, das einen Satz von Punkten wie ein elastisches Band umhüllt und mit der `atlas.math.getConvexHull`-Methode berechnet werden kann. Alle in einem Cluster enthaltenen Punkte können mit der Methode `getClusterLeaves` aus der Datenquelle abgerufen werden.

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true)
);

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a data source for the convex hull polygon. Since this will be updated frequently it is more efficient to separate this into its own data source.
DataSource polygonDataSource = new DataSource();

//Add data source to the map.
map.sources.add(polygonDataSource);

//Add a polygon layer and a line layer to display the convex hull.
map.layers.add(new PolygonLayer(polygonDataSource));
map.layers.add(new LineLayer(polygonDataSource));

//Create a symbol layer to render the clusters.
SymbolLayer clusterLayer = new SymbolLayer(source,
    iconImage("marker-red"),
    textField(get("point_count_abbreviated")),
    textOffset(new Float[] { 0f, -1.2f }),
    textColor(Color.WHITE),
    textSize(14f),

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count"))
);
map.layers.add(clusterLayer);

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count")))
));

//Add a click event to the layer so we can calculate the convex hull of all the points within a cluster.
map.events.add((OnFeatureClick) (features) -> {
    if(features.size() > 0) {
        //Get the clustered point from the event.
        Feature cluster = features.get(0);

        //Get all points in the cluster. Set the offset to 0 and the max long value to return all points.
        FeatureCollection leaves = source.getClusterLeaves(cluster, Long.MAX_VALUE, 0);

        //Get the point features from the feature collection.
        List<Feature> childFeatures = leaves.features();

        //When only two points in a cluster. Render a line.
        if(childFeatures.size() == 2){
            //Extract the geometry points from the child features.
            List<Point> points = new ArrayList();

            childFeatures.forEach(f -> {
                points.add((Point)f.geometry());
            });
            
            //Create a line from the points.
            polygonDataSource.setShapes(LineString.fromLngLats(points));
        } else {
            Polygon hullPolygon = MapMath.getConvexHull(leaves);

            //Overwrite all data in the polygon data source with the newly calculated convex hull polygon.
            polygonDataSource.setShapes(hullPolygon);
        }
    }

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, clusterLayer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true)
)

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a data source for the convex hull polygon. Since this will be updated frequently it is more efficient to separate this into its own data source.
val polygonDataSource = DataSource()

//Add data source to the map.
map.sources.add(polygonDataSource)

//Add a polygon layer and a line layer to display the convex hull.
map.layers.add(PolygonLayer(polygonDataSource))
map.layers.add(LineLayer(polygonDataSource))

//Create a symbol layer to render the clusters.
val clusterLayer = SymbolLayer(
    source,
    iconImage("marker-red"),
    textField(get("point_count_abbreviated")),
    textOffset(arrayOf(0f, -1.2f)),
    textColor(Color.WHITE),
    textSize(14f),  

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count"))
)
map.layers.add(clusterLayer)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)

//Add a click event to the layer so we can calculate the convex hull of all the points within a cluster.
map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    if (features.size() > 0) {
        //Get the clustered point from the event.
        val cluster: Feature = features.get(0)

        //Get all points in the cluster. Set the offset to 0 and the max long value to return all points.
        val leaves: FeatureCollection = source.getClusterLeaves(cluster, Long.MAX_VALUE, 0)

        //Get the point features from the feature collection.
        val childFeatures = leaves.features()

        //When only two points in a cluster. Render a line.
        if (childFeatures!!.size == 2) {
            //Extract the geometry points from the child features.
            val points: MutableList<Point?> = ArrayList()
            childFeatures!!.forEach(Consumer { f: Feature ->
                points.add(
                    f.geometry() as Point?
                )
            })

            //Create a line from the points.
            polygonDataSource.setShapes(LineString.fromLngLats(points))
        } else {
            val hullPolygon: Polygon = MapMath.getConvexHull(leaves)

            //Overwrite all data in the polygon data source with the newly calculated convex hull polygon.
            polygonDataSource.setShapes(hullPolygon)
        }
    }
    true
}, clusterLayer)
```

::: zone-end

Die folgende Abbildung zeigt, wie der obige Code den Bereich aller Punkte innerhalb eines Clusters anzeigt, auf den geklickt wird.

![Karte, die das Polygon der konvexen Hülle aller Punkte in einem Cluster zeigt, auf den geklickt wird](media/clustering-point-data-android-sdk/android-cluster-leaves-convex-hull.gif)

## <a name="aggregating-data-in-clusters"></a>Aggregieren von Daten in Clustern

Cluster werden oft mithilfe eines Symbols mit der Anzahl der im Cluster enthaltenen Punkte dargestellt. Manchmal ist es jedoch wünschenswert, den Clusterstil mit zusätzlichen Metriken anzupassen. Mit Clustereigenschaften können benutzerdefinierte Eigenschaften erstellt werden. Sie entsprechen einer Berechnung, die auf den Eigenschaften innerhalb jedes Punkts in einem Cluster basiert. Clustereigenschaften können in der Option `clusterProperties` der `DataSource` definiert werden.

Der folgende Code berechnet eine Anzahl basierend auf der Eigenschaft „Entitätstyp“ der einzelnen Datenpunkte in einem Cluster. Wenn ein Benutzer auf einen Cluster klickt, wird ein Popup mit zusätzlichen Informationen über den Cluster angezeigt.

::: zone pivot="programming-language-java-android"

``` java
//An array of all entity type property names in features of the data set.
String[] entityTypes = new String[] { "Gas Station", "Grocery Store", "Restaurant", "School" };

//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

//Close the popup initially.
popup.close();

//Create a data source and add it to the map.
source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(50),

    //Calculate counts for each entity type in a cluster as custom aggregate properties.
    clusterProperties(new ClusterProperty[]{
        new ClusterProperty("Gas Station", sum(accumulated(), get("Gas Station")), switchCase(eq(get("EntityType"), literal("Gas Station")), literal(1), literal(0))),
        new ClusterProperty("Grocery Store", sum(accumulated(), get("Grocery Store")), switchCase(eq(get("EntityType"), literal("Grocery Store")), literal(1), literal(0))),
        new ClusterProperty("Restaurant", sum(accumulated(), get("Restaurant")), switchCase(eq(get("EntityType"), literal("Restaurant")), literal(1), literal(0))),
        new ClusterProperty("School", sum(accumulated(), get("School")), switchCase(eq(get("EntityType"), literal("School")), literal(1), literal(0)))
    })
);

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);

//Create a bubble layer for rendering clustered data points.
BubbleLayer clusterBubbleLayer = new BubbleLayer(source,
    bubbleRadius(20f),
    bubbleColor("purple"),
    bubbleStrokeWidth(0f),

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
);

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer);

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),

    //Display the 'point_count_abbreviated' property value.
    textField(get("point_count_abbreviated")),

    textColor(Color.WHITE),
    textOffset(new Float[] { 0f, 0.4f }),

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count"))
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count")))
));

//Add a click event to the cluster layer and display the aggregate details of the cluster.
map.events.add((OnFeatureClick) (features) -> {
    if(features.size() > 0) {
        //Get the clustered point from the event.
        Feature cluster = features.get(0);

        //Create a number formatter that removes decimal places.
        NumberFormat nf = DecimalFormat.getInstance();
        nf.setMaximumFractionDigits(0);

        //Create the popup's content.
        StringBuilder sb = new StringBuilder();

        sb.append("Cluster size: ");
        sb.append(nf.format(cluster.getNumberProperty("point_count")));
        sb.append(" entities\n");

        for(int i = 0; i < entityTypes.length; i++) {
            sb.append("\n");

            //Get the entity type name.
            sb.append(entityTypes[i]);
            sb.append(": ");

            //Get the aggregated entity type count from the properties of the cluster by name.
            sb.append(nf.format(cluster.getNumberProperty(entityTypes[i])));
        }

        //Retrieve the custom layout for the popup.
        View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

        //Access the text view within the custom view and set the text to the title property of the feature.
        TextView tv = customView.findViewById(R.id.message);
        tv.setText(sb.toString());

        //Get the position of the cluster.
        Position pos = MapMath.getPosition((Point)cluster.geometry());

        //Set the options on the popup.
        popup.setOptions(
            //Set the popups position.
            position(pos),

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),

            //Set the content of the popup.
            content(customView)
        );

        //Open the popup.
        popup.open();
    }

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return true;
}, clusterBubbleLayer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//An array of all entity type property names in features of the data set.
val entityTypes = arrayOf("Gas Station", "Grocery Store", "Restaurant", "School")

//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

//Close the popup initially.
popup.close()

//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(50),  

    //Calculate counts for each entity type in a cluster as custom aggregate properties.
    clusterProperties(
        arrayOf<ClusterProperty>(
            ClusterProperty("Gas Station", sum(accumulated(), get("Gas Station")), switchCase(eq(get("EntityType"), literal("Gas Station")), literal(1), literal(0))),
            ClusterProperty("Grocery Store", sum(accumulated(), get("Grocery Store")), switchCase(eq(get("EntityType"), literal("Grocery Store")), literal(1), literal(0))),
            ClusterProperty("Restaurant", sum(accumulated(), get("Restaurant")), switchCase(eq(get("EntityType"), literal("Restaurant")), literal(1), literal(0))),
            ClusterProperty("School", sum(accumulated(), get("School")), switchCase(eq(get("EntityType"), literal("School")), literal(1), literal(0)))
        )
    )
)

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/SamplePoiDataSet.json")

//Add data source to the map.
map.sources.add(source)

//Create a bubble layer for rendering clustered data points.
val clusterBubbleLayer = BubbleLayer(
    source,
    bubbleRadius(20f),
    bubbleColor("purple"),
    bubbleStrokeWidth(0f),  

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
)

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer)

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(
    SymbolLayer(
        source,  

        //Hide the icon image.
        iconImage("none"),  

        //Display the 'point_count_abbreviated' property value.
        textField(get("point_count_abbreviated")),

        textColor(Color.WHITE),
        textOffset(arrayOf(0f, 0.4f)),  

        //Only rendered data points which have a point_count property, which clusters do.
        SymbolLayerOptions.filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)

//Add a click event to the cluster layer and display the aggregate details of the cluster.
map.events.add(OnFeatureClick { features: List<Feature> ->
    if (features.size > 0) {
        //Get the clustered point from the event.
        val cluster = features[0]

        //Create a number formatter that removes decimal places.
        val nf: NumberFormat = DecimalFormat.getInstance()
        nf.setMaximumFractionDigits(0)

        //Create the popup's content.
        val sb = StringBuilder()

        sb.append("Cluster size: ")
        sb.append(nf.format(cluster.getNumberProperty("point_count")))
        sb.append(" entities\n")

        for (i in entityTypes.indices) {
            sb.append("\n")

            //Get the entity type name.
            sb.append(entityTypes[i])
            sb.append(": ")

            //Get the aggregated entity type count from the properties of the cluster by name.
            sb.append(nf.format(cluster.getNumberProperty(entityTypes[i])))
        }

        //Retrieve the custom layout for the popup.
        val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

        //Access the text view within the custom view and set the text to the title property of the feature.
        val tv: TextView = customView.findViewById(R.id.message)
        tv.text = sb.toString()

        //Get the position of the cluster.
        val pos: Position = MapMath.getPosition(cluster.geometry() as Point?)

        //Set the options on the popup.
        popup.setOptions( 
            //Set the popups position.
            position(pos),  

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),  

            //Set the content of the popup.
            content(customView)
        )

        //Open the popup.
        popup.open()
    }

    //Return a boolean indicating if event should be consumed or continue bubble up.
    true
} as OnFeatureClick, clusterBubbleLayer)
```

::: zone-end

Das Popup entspricht den Schritten, die im Dokument [Anzeigen eines Popups](display-feature-information-android.md?#display-a-popup) beschrieben werden.

Die folgende Abbildung zeigt, wie der obige Code ein Popup mit der aggregierten Anzahl jedes Entitätswerttyps für alle Punkte im geclusterten Punkt anzeigt, auf den geklickt wird.

![Karte mit einem Popup der aggregierten Anzahl von Entitätstypen aller Punkte in einem Cluster](media/clustering-point-data-android-sdk/android-cluster-aggregates.gif)

## <a name="next-steps"></a>Nächste Schritte

So fügen Sie Ihrer Karte weitere Daten hinzu:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer-android.md)
