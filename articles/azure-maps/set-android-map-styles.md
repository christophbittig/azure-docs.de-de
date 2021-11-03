---
title: Festlegen eines Kartenstils in Android-Karten | Microsoft Azure Maps
description: Erlernen Sie zwei Möglichkeiten, den Stil einer Karte festzulegen. Erfahren Sie, wie Sie den Stil mit dem Azure Maps Android SDK in der Layoutdatei oder der „Activity“-Klasse anpassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1d16131c51527ead525bf17143892d15229658d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072425"
---
# <a name="set-map-style-android-sdk"></a>Festlegen eines Kartenstils (Android SDK)

In diesem Artikel werden Ihnen zwei Möglichkeiten gezeigt, Kartenstile mit dem Android SDK von Azure Maps festzulegen. In Azure Maps stehen sechs Kartenstile zur Auswahl. Weitere Informationen zu unterstützten Kartenstilen finden Sie unter [Unterstützte Kartenstile in Azure Maps](supported-map-styles.md).

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen.

>[!IMPORTANT]
>Das Verfahren in diesem Abschnitt erfordert ein Azure Maps-Konto im Tarif „Gen 1“ oder „Gen 2“. Weitere Informationen zu Tarifen finden Sie unter [Auswählen des richtigen Tarifs in Azure Maps](choose-pricing-tier.md).


## <a name="set-map-style-in-the-layout"></a>Festlegen des Kartenstils im Layout

Sie können einen Kartenstil In der Layoutdatei für Ihre Activity-Klasse festlegen, wenn Sie das Kartensteuerelement hinzufügen. Im folgenden Code werden der Mittelpunkt, der Zoomfaktor und der Kartenstil festgelegt.

```xml
<com.azure.android.maps.control.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_centerLat="47.602806"
    app:azure_maps_centerLng="-122.329330"
    app:azure_maps_zoom="12"
    app:azure_maps_style="grayscale_dark"
    />
```

Der folgende Screenshot veranschaulicht, wie durch den oben aufgeführten Code eine Straßenkarte mit dem Stil „Graustufen dunkel“ dargestellt wird.

![Straßenkarte mit dem Stil „Graustufen dunkel“](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Festlegen des Kartenstils im Code

Der Kartenstil kann mithilfe der `setStyle`-Methode der Karte programmgesteuert im Code festgelegt werden. Mit dem folgenden Code werden der Mittelpunkt und Zoomfaktor der Karte mithilfe der `setCamera`-Methode festgelegt. Außerdem wird der Kartenstil auf `SATELLITE_ROAD_LABELS` festgelegt.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

Der folgende Screenshot veranschaulicht, wie durch den oben aufgeführten Code eine Karte mit dem Stil „Straßenbezeichnungen in Satellitenansicht“ dargestellt wird.

![Karte mit dem Stil „Straßenbezeichnungen in Satellitenansicht“](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Festlegen der Kartenkamera

Mit der Kartenkamera wird gesteuert, welcher Teil der Welt im Kartenviewport angezeigt wird. Die Kamera kann im Layout oder programmgesteuert im Code festgelegt werden. Beim Festlegen im Code gibt es zwei Hauptmethoden zum Festlegen der Kartenposition: Sie verwenden „center“ und „zoom“, oder Sie übergeben einen Begrenzungsrahmen. Der folgende Code zeigt, wie Sie bei Verwendung von `center` und `zoom` alle optionalen Kameraoptionen festlegen.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Häufig ist es wünschenswert, den Fokus der Karte auf einen Satz von Daten zu richten. Ein Begrenzungsrahmen kann mithilfe der `MapMath.fromData`-Methode aus Features berechnet und an die Option `bounds` der Kartenkamera übergeben werden. Beim Festlegen einer Kartenansicht, die auf einem Begrenzungsrahmen basiert, ist es häufig sinnvoll, einen `padding`-Wert anzugeben, um die Pixelgröße der Punkte zu berücksichtigen, die als Blasen oder Symbole gerendert werden. Der folgende Code zeigt, wie Sie alle optionalen Kameraoptionen festlegen, wenn Sie einen Begrenzungsrahmen zum Festlegen der Kameraposition verwenden.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Das Seitenverhältnis eines Begrenzungsrahmens ist u. U. nicht mit dem Seitenverhältnis der Karte identisch, da auf der Karte häufig der vollständige Bereich des Begrenzungsrahmens zu sehen ist, während sie häufig auf einen kleineren vertikalen oder horizontalen Ausschnitt beschränkt sein kann.

### <a name="animate-map-view"></a>Animieren der Kartenansicht

Beim Festlegen der Kameraoptionen der Karte können auch Animationsoptionen verwendet werden, um einen Übergang zwischen der aktuellen Kartenansicht und der nächsten zu erstellen. Mit diesen Optionen werden der Animationstyp und die Dauer zum Verschieben der Kamera angegeben.

| Option | BESCHREIBUNG |
|--------|-------------|
| `animationDuration(Integer durationMs)` | Gibt in Millisekunden (ms) an, wie lange die Kamera zwischen den Ansichten animiert. |
| `animationType(AnimationType animationType)` | Gibt den Typ des auszuführenden Animationsübergangs an.<br/><br/> - `JUMP` – eine sofortige Änderung.<br/> - `EASE` – schrittweise Änderung der Einstellungen der Kamera.<br/> - `FLY` – schrittweise Änderung der Einstellungen der Kamera nach einem Bogen, der einem Flug ähnelt. |

Der folgende Code zeigt, wie die Kartenansicht mithilfe einer `FLY`-Animation über einen Zeitraum von drei Sekunden animiert wird.

::: zone pivot="programming-language-java-android"

``` java
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.6)),
    zoom(12),
    animationType(AnimationType.FLY), 
    animationDuration(3000)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.6)),
    zoom(12.0),
    AnimationOptions.animationType(AnimationType.FLY),
    AnimationOptions.animationDuration(3000)
)
```

::: zone-end

Im Folgenden wird der obige Code veranschaulicht, der die Kartenansicht von New York nach Seattle animiert.

![Karte, die die Kamera von New York nach Seattle animiert](media/set-android-map-styles/android-animate-camera.gif)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer-android.md)
