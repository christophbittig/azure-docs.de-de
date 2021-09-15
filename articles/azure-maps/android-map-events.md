---
title: Verarbeiten von Kartenereignissen in Android-Karten | Microsoft Azure Maps
description: Erfahren Sie, welche Ereignisse ausgelöst werden, wenn Benutzer mit Karten interagieren. Zeigen Sie eine Liste aller unterstützten Kartenereignisse an. Hier erfahren Sie, wie Sie mit dem Azure Maps Android SDK Ereignisse verarbeiten können.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 49a06fdd2c8025515fd42124aa7d00f27d8c5978
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425714"
---
# <a name="interact-with-the-map-android-sdk"></a>Interagieren mit der Karte (Android SDK)

In diesem Artikel erfahren Sie, wie Sie den Kartenereignis-Manager verwenden.

## <a name="interact-with-the-map"></a>Interaktion mit der Karte

Die Karte verwaltet alle Ereignisse über ihre `events`-Eigenschaft. In der folgenden Tabelle sind alle unterstützten Kartenereignisse aufgelistet.

| Ereignis                  | Ereignishandlerformat | BESCHREIBUNG |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Wird ausgelöst, nachdem der letzte Frame gerendert wurde, jedoch bevor die Karte in den Leerlauf wechselt:<ul><li>Es werden keine Kameraübergänge ausgeführt.</li><li>Alle zurzeit angeforderten Kacheln wurden geladen.</li><li>Alle Ausblend-/Übergangsanimationen wurden abgeschlossen.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Wird während eines animierten Übergangs von einer Ansicht zu einer anderen wiederholt ausgelöst, entweder infolge einer Benutzerinteraktion oder durch Methoden. |
| `OnCameraMoveCanceled` | `()`                 | Wird ausgelöst, wenn eine Bewegungsanforderung an die Kamera abgebrochen wurde. |
| `OnCameraMoveStarted`  | `(int reason)`       | Wird ausgelöst, kurz bevor die Karte den Übergang von einer Ansicht zu einer anderen beginnt, entweder infolge einer Benutzerinteraktion oder durch Methoden. Das Argument `reason` des Ereignislistener gibt einen ganzzahligen Wert zurück, der Aufschluss darüber gibt, wie die Kamerabewegung initiiert wurde. Im Folgenden finden Sie eine Liste möglicher Gründe:<ul><li>1: Geste</li><li>2: Entwickleranimation</li><li>3: API-Animation</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | Wird ausgelöst, wenn die Karte an derselben Stelle gedrückt und wieder losgelassen wird. Dieser Ereignishandler gibt einen booleschen Wert zurück, der angibt, ob das Ereignis verarbeitet oder an andere Ereignislistener weitergeleitet werden soll. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | Wird ausgelöst, wenn die Karte an derselben Stelle eines Features gedrückt und wieder losgelassen wird. Dieser Ereignishandler gibt einen booleschen Wert zurück, der angibt, ob das Ereignis verarbeitet oder an andere Ereignislistener weitergeleitet werden soll. |
| `OnLayerAdded` | `(Layer layer)` | Wird ausgelöst, wenn der Karte eine Ebene hinzugefügt wird. |
| `OnLayerRemoved` | `(Layer layer)` | Wird ausgelöst, wenn aus der Karte eine Ebene entfernt wird. |
| `OnLoaded` | `()` | Wird sofort ausgelöst, nachdem alle erforderlichen Ressourcen heruntergeladen wurden und das erste visuell vollständige Rendering der Karte stattgefunden hat. |
| `OnLongClick`          | `(double lat, double lon): boolean` | Wird ausgelöst, wenn die Karte gedrückt wird, für einen Moment gehalten und dann an derselben Stelle auf der Karte wieder losgelassen wird. Dieser Ereignishandler gibt einen booleschen Wert zurück, der angibt, ob das Ereignis verarbeitet oder an andere Ereignislistener weitergeleitet werden soll. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | Wird ausgelöst, wenn die Karte gedrückt wird, für einen Moment gehalten und dann an derselben Stelle eines Features losgelassen wird. Dieser Ereignishandler gibt einen booleschen Wert zurück, der angibt, ob das Ereignis verarbeitet oder an andere Ereignislistener weitergeleitet werden soll. |
| `OnReady`              | `(AzureMap map)`     | Wird ausgelöst, wenn die Karte anfänglich geladen wird oder wenn sich die App-Ausrichtung ändert und die erforderlichen Kartenressourcen geladen sind und die Karte bereit ist, programmgesteuert interaktiv genutzt zu werden. |
| `OnSourceAdded` | `(Source source)` | Wird ausgelöst, wenn der Karte `DataSource` oder `VectorTileSource` hinzugefügt wird. |
| `OnSourceRemoved` | `(Source source)` | Wird ausgelöst, wenn aus der Karte `DataSource` oder `VectorTileSource` entfernt wird. |
| `OnStyleChange` | `()` | Wird ausgelöst, wenn der Kartenstil geladen oder geändert wird. |

Der folgende Code zeigt, wie die `OnClick`-, `OnFeatureClick`- und `OnCameraMove`-Ereignisse der Karte hinzugefügt werden.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Weitere Informationen zur Interaktion mit den Karten- und Auslöserereignissen finden Sie in der Dokumentation [Navigieren auf einer Karte](how-to-use-android-map-control-library.md#navigating-the-map).

## <a name="scope-feature-events-to-layer"></a>Beschränken von Featureereignissen auf eine Ebene

Beim Hinzufügen der `OnFeatureClick`- oder `OnLongFeatureClick`-Ereignisse zur Karte kann eine Ebeneninstanz oder Ebenenkennung als zweiter Parameter übergeben werden. Beim Übergeben einer Ebene wird das Ereignis nur ausgelöst, wenn es auf dieser Ebene auftritt. Auf Ebenen bezogene Ereignisse werden von Symbol-, Blasen-, Linien- und Polygonebenen unterstützt.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten vollständige Codebeispiele:

> [!div class="nextstepaction"]
> [Navigieren auf einer Karte](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](how-to-add-shapes-to-android-map.md)
