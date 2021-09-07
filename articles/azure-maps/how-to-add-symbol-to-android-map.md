---
title: Hinzufügen einer Symbolebene zu Android-Karten | Microsoft Azure Maps
description: Erfahren Sie, wie Sie einer Karte einen Marker hinzufügen. Sehen Sie sich ein Beispiel an, in dem das Azure Maps Android SDK verwendet wird, um eine Symbolebene hinzuzufügen, die punktbasierte Daten aus einer Datenquelle enthält.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: f2aaf03f4314386615fabfe65ca47132543577fa
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091697"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Hinzufügen einer Symbolebene (Android SDK)

Dieser Artikel zeigt Ihnen, wie Sie Punktdaten aus einer Datenquelle als Symbolebene auf einer Karte mit dem Android SDK für Azure Maps rendern können. Mit Symbolebenen werden Punkte als Bild und Text auf der Karte gerendert.

> [!TIP]
> Symbolebenen rendern in der Standardeinstellung die Koordinaten aller Geometrien in einer Datenquelle. Legen Sie die Option `filter` der Ebene auf `eq(geometryType(), "Point")` fest, um die Ebene so zu beschränken, dass nur Punktgeometriefeatures gerendert werden. Wenn Sie auch MultiPoint-Features einschließen möchten, legen Sie die Option `filter` der Ebene auf `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` fest.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen. Codeblöcke in diesem Artikel können in den `onReady`-Ereignishandler von Karten eingefügt werden.

## <a name="add-a-symbol-layer"></a>Hinzufügen einer Symbolebene

Bevor Sie der Karte eine Symbolebene hinzufügen können, müssen Sie einige Schritte ausführen. Erstellen Sie zunächst eine Datenquelle, und fügen Sie sie der Karte hinzu. Erstellen Sie eine Symbolebene. Übergeben Sie dann die Datenquelle an die Symbolebene, um die Daten aus der Datenquelle abzurufen. Abschließend fügen Sie Daten in die Datenquelle ein, damit diese gerendert werden können.

Der folgende Code zeigt, was der Karte hinzugefügt werden soll, nachdem sie geladen wurde. Dieses Beispiel rendert einen einzelnen Punkt auf der Karte mithilfe einer Symbolebene.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Es gibt drei verschiedene Arten von Punktdaten, die der Karte hinzugefügt werden können:

- GeoJSON-Point-Geometrie: Dieses Objekt enthält lediglich eine Koordinate eines Punkts. Mit der statischen `Point.fromLngLat`-Methode können diese Objekte ganz einfach erstellt werden.
- GeoJSON-MultiPoint-Geometrie: Dieses Objekt enthält lediglich die Koordinaten mehrerer Punkte. Übergeben Sie ein Array von Punkten an die `MultiPoint`-Klasse, um diese Objekte zu erstellen.
- GeoJSON-Feature: Dieses Objekt besteht aus einer beliebigen GeoJSON-Geometrie und einer Reihe von Eigenschaften, die Metadaten der Geometrie enthalten.

Weitere Informationen zum Erstellen und Hinzufügen von Daten zur Karte finden Sie im Dokument [Erstellen einer Datenquelle](create-data-source-android-sdk.md).

Mit dem folgenden Codebeispiel wird eine GeoJSON-Punktgeometrie erstellt und an das GeoJSON-Feature übergeben. Außerdem wird den Eigenschaften der `title`-Wert hinzugefügt. Die `title`-Eigenschaft wird über dem Symbol auf der Karte als Text angezeigt.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Der folgende Screenshot zeigt, wie durch den oben aufgeführten Code unter Verwendung eines Symbols und einer Beschriftung mit einer Symbolebene ein Punktfeature gerendert wird.

![Karte mit gerendertem Punkt. Dabei wird eine Symbolebene zur Darstellung eines Symbols und einer Beschriftung für ein Punktfeature verwendet.](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Standardmäßig optimieren Symbolebenen das Rendering von Symbolen, indem sie überlappende Symbole ausblenden. Beim Zoomen werden die ausgeblendeten Symbole angezeigt. Legen Sie die Optionen `iconAllowOverlap` und `textAllowOverlap` auf `true` fest, um dieses Feature zu deaktivieren und immer alle Symbole zu rendern.

## <a name="add-a-custom-icon-to-a-symbol-layer&quot;></a>Hinzufügen eines benutzerdefinierten Zeichens zu einer Symbolebene

Symbolebenen werden mit WebGL gerendert. Daher müssen alle Ressourcen wie etwa Zeichenbilder in den WebGL-Kontext geladen werden. Dieses Beispiel zeigt, wie Sie ein benutzerdefiniertes Symbol zu den Kartenressourcen hinzufügen können. Dieses Symbol wird dann zum Rendern von Punktdaten mit einem benutzerdefinierten Symbol auf der Karte verwendet. Die `textField`-Eigenschaft der Symbolebene erfordert, dass ein Ausdruck angegeben wird. In diesem Fall möchten wir die temperature-Eigenschaft rendern. Da die Temperatur eine Zahl ist, muss Sie in eine Zeichenfolge konvertiert werden. Außerdem soll „°F“ angefügt werden. Diese Verkettung ist mit folgendem Ausdruck möglich: `concat(Expression.toString(get(&quot;temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

In diesem Beispiel wird das folgende Bild in den Ordner „drawable“ der App geladen.

| ![Wettersymbolbild für Regenschauer](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

Der folgende Screenshot zeigt, wie durch den oben aufgeführten Code ein Punktfeature unter Verwendung eines benutzerdefinierten Symbols und einer formatierten Beschriftung mit einer Symbolebene gerendert wird.

![Karte mit gerendertem Punkt. Dabei wird eine Symbolebene zur Darstellung eines benutzerdefinierten Symbols und einer formatierten Beschriftung für ein Punktfeature verwendet.](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Wenn Sie nur Text mit einer Symbolebene rendern möchten, können Sie das Symbol ausblenden, indem Sie die `iconImage`-Eigenschaft der Symboloptionen auf `"none&quot;` setzen.

## <a name=&quot;modify-symbol-colors&quot;></a>Ändern von Symbolfarben

Das Azure Maps Android SDK enthält eine Reihe vordefinierter Farbvariationen des Standardmarkersymbols. So kann beispielsweise `marker-red` in der Option `iconImage` einer Symbolebene übergeben werden, um eine rote Version des Markersymbols auf dieser Ebene zu rendern.

::: zone pivot=&quot;programming-language-java-android&quot;

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage(&quot;marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

In der folgenden Tabelle sind alle verfügbaren integrierten Symbolbildnamen aufgelistet. Alle diese Marker beziehen ihre Farben von Farbressourcen, die Sie überschreiben können. Dies ist zusätzlich zum Überschreiben der Hauptfüllfarbe dieses Markers möglich. Das Überschreiben der Farbe eines dieser Marker wird allerdings auf allen Ebenen angewendet, die dieses Symbolbild verwenden.

| Symbolbildname | Farbressourcenname |
|-----------------|---------------------|
| `marker-default` | `azure_maps_marker_default` |
| `marker-black` | `azure_maps_marker_black` |
| `marker-blue` | `azure_maps_marker_blue` |
| `marker-darkblue` | `azure_maps_marker_darkblue` |
| `marker-red` | `azure_maps_marker_red` |
| `marker-yellow` | `azure_maps_marker_yellow` |

Mit dem Farbressourcennnamen `azure_maps_marker_border` können Sie zudem die Rahmenfarbe aller Marker überschreiben. Die Farben dieser Marker können überschrieben werden, indem eine Farbe mit demselben Namen in der Datei `colors.xml` Ihrer App hinzugefügt wird. Beispielsweise würde mit der folgenden Datei `colors.xml` die Standardmarkerfarbe auf Hellgrün festgelegt werden.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="azure_maps_marker_default">#00FF00</color>
</resources>
```

Bei dem folgenden Code handelt es sich um eine modifizierte Version des XML-Codes für den Standardmarkervektor, den Sie ändern können, um benutzerdefinierte Versionen des Standardmarkers zu erstellen. Die geänderte Version kann dem Ordner `drawable` Ihrer App hinzugefügt werden und dem Kartenbild-Sprite mithilfe von `map.images.add` hinzugefügt werden und dann mit einer Symbolebene verwendet werden.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/azure_maps_marker_default"
        android:strokeColor="@color/azure_maps_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Erstellen einer Datenquelle](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Clusterpunktdaten](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Anzeigen von Featureinformationen](display-feature-information-android.md)
