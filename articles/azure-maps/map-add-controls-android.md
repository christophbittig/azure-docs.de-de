---
title: Hinzufügen von Steuerelementen zu einer Android-Karte | Microsoft Azure Maps
description: Erfahren Sie, wie Sie einer Karte Steuerelemente für Zoom, Neigung und Drehen sowie eine Stilauswahl im Microsoft Azure Maps Android SDK hinzufügen.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b84150196467606800df737ba9c9d50c69346e30
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093587"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Hinzufügen von Steuerelementen zu einer Karte (Android SDK)

In diesem Artikel wird gezeigt, wie Sie der Karte Steuerelemente für die Benutzeroberfläche hinzufügen.

## <a name="add-zoom-control"></a>Hinzufügen eines Zoomsteuerelements

Ein Zoomsteuerelement fügt Schaltflächen zum Vergrößern und Verkleinern der Karte hinzu. Im folgenden Codebeispiel wird eine Instanz der `ZoomControl`-Klasse erstellt und einer Karte hinzugefügt.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

Der folgende Screenshot zeigt ein Zoomsteuerelement, das auf einer Karte geladen wurde.

![Einer Karte hinzugefügtes Zoomsteuerelement](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Hinzufügen eines Neigungssteuerelements

Ein Neigungssteuerelement fügt Schaltflächen zum Kippen der Neigung für eine Karte relativ zum Horizont hinzu. Im folgenden Codebeispiel wird eine Instanz der `PitchControl`-Klasse erstellt und einer Karte hinzugefügt.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

Der folgende Screenshot zeigt ein Neigungssteuerelement, das auf einer Karte geladen wurde.

![Einer Karte hinzugefügtes Neigungssteuerelement](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Hinzufügen eines Kompasssteuerelements

Ein Kompasssteuerelement fügt eine Schaltfläche zum Drehen der Karte hinzu. Im folgenden Codebeispiel wird eine Instanz der `CompassControl`-Klasse erstellt und einer Karte hinzugefügt.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

Der folgende Screenshot zeigt ein Kompasssteuerelement, das auf einer Karte geladen wurde.

![Einer Karte hinzugefügtes Kompasssteuerelement](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Hinzufügen eines Verkehrsinfo-Steuerelements

Ein Verkehrsinfo-Steuerelement fügt eine Schaltfläche zum Umschalten der Sichtbarkeit von Verkehrsdaten auf der Karte hinzu. Im folgenden Codebeispiel wird eine Instanz der `TrafficControl`-Klasse erstellt und einer Karte hinzugefügt.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

Der folgende Screenshot zeigt ein Verkehrsinfo-Steuerelement, das auf einer Karte geladen wurde.

![Einer Karte hinzugefügtes Verkehrsinfo-Steuerelement](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Eine Karte mit allen Steuerelementen

Mehrere Steuerelemente können in einem Array platziert, der Karte in einem Durchgang hinzugefügt und im gleichen Bereich der Karte positioniert werden, um die Entwicklung zu vereinfachen. Im folgenden Code werden der Karte bei diesem Ansatz die Standard-Navigationssteuerelemente hinzugefügt.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

Der folgende Screenshot zeigt alle auf einer Karte geladenen Steuerelemente. Die Reihenfolge, in der sie der Karte hinzugefügt werden, entspricht der Anzeigereihenfolge.

![Alle der Karte hinzugefügten Steuerelemente](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](how-to-add-shapes-to-android-map.md)
