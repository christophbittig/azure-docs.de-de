---
title: Hinzufügen einer Kachelebene zu Android-Karten | Microsoft Azure Maps
description: Erfahren Sie, wie Sie einer Karte eine Kachelebene hinzufügen. Sehen Sie sich ein Beispiel an, in dem das Azure Maps Android SDK verwendet wird, um einer Karte eine Wetterradarüberlagerung hinzuzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: d35b1201d0f60c517f419186d01f493efa9fbeba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435238"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Hinzufügen einer Kachelebene zu einer Karte (Android SDK)

In diesem Artikel wird veranschaulicht, wie Sie mit dem Android SDK für Azure Maps eine Kachelebene auf einer Karte rendern können. Mithilfe von Kachelebenen lassen sich Bilder über die Azure Maps-Basiskartenkacheln legen. Weitere Informationen zum Azure Maps-Kachelsystem finden Sie in der Dokumentation [Zoomfaktoren und Linienraster](zoom-levels-and-tile-grid.md).

Eine Kachelebene wird in Kacheln von einem Server geladen. Diese Bilder können wie jedes andere Bild vorab gerendert und auf einem Server gespeichert werden, wobei eine Namenskonvention verwendet wird, die von der Kachelebene verstanden wird. Sie können diese Bilder aber auch mit einem dynamischen Dienst rendern, der die Bilder nahezu in Echtzeit generiert. Es gibt drei verschiedene Namenskonventionen für Kacheldienste, die von der Azure Maps-Klasse TileLayer unterstützt werden:

* Notation von X, Y, Zoomfaktor: Basierend auf dem Zoomfaktor ist X die Spalten- und Y die Zeilenposition der Kachel im Kachelraster.
* Quadkey-Notation: Kombination der Informationen X, Y und Zoomfaktor in einem einzelnen Zeichenfolgenwert, der ein eindeutiger Bezeichner für eine Kachel ist.
* Begrenzungsrahmen: Koordinaten des Begrenzungsrahmens können verwendet werden, um ein Bild im Format `{west},{south},{east},{north}` anzugeben, welches häufig von [Webkartendiensten (Web Mapping Services, WMS)](https://www.opengeospatial.org/standards/wms) verwendet wird.

> [!TIP]
> Ein TileLayer-Element (Kachelebene) ist eine gute Möglichkeit, große Datasets auf der Karte zu visualisieren. Eine Kachelebene lässt sich nicht nur aus einem Bild generieren, sondern auch Vektordaten können als Kachelebene gerendert werden. Durch das Rendern von Vektordaten als Kachelebene muss das Kartensteuerelement nur die Kacheln laden. Diese können eine viel kleinere Dateigröße aufweisen als die Vektordaten, die sie darstellen. Diese Technik wird von vielen verwendet, die Millionen von Datenzeilen auf der Karte rendern müssen.

Die in eine Kachelebene übergebene Kachel-URL muss eine HTTP/HTTPS-URL zu einer TileJSON-Ressource oder eine Kachel-URL-Vorlage sein, die die folgenden Parameter verwendet:

* `{x}`: X-Position der Kachel. Benötigt auch `{y}` und `{z}`.
* `{y}`: Y-Position der Kachel. Benötigt auch `{x}` und `{z}`.
* `{z}`: Zoomfaktor der Kachel. Benötigt auch `{x}` und `{y}`.
* `{quadkey}`: Kachel-Quadkey-Bezeichner basierend auf der Namenskonvention des Bing Maps-Kachelsystems.
* `{bbox-epsg-3857}`: Eine Begrenzungsrahmen-Zeichenfolge mit dem Format `{west},{south},{east},{north}` im Raumbezugssystem EPSG 3857.
* `{subdomain}`: Ein Platzhalter für die Unterdomänenwerte, sofern angegeben.
* `azmapsdomain.invalid`: Ein Platzhalter zum Ausrichten der Domäne und Authentifizierung von Kachelanforderungen an dieselben Werte, die von der Karte verwendet werden. Verwenden Sie diesen, wenn Sie einen von Azure Maps gehosteten Kacheldienst aufrufen.

## <a name="prerequisites"></a>Voraussetzungen

Um den Vorgang in diesem Artikel abzuschließen, müssen Sie das [Android SDK für Azure Maps](how-to-use-android-map-control-library.md) installieren, um eine Karte zu laden.

## <a name="add-a-tile-layer-to-the-map"></a>Hinzufügen einer Kachelebene zur Karte

In diesem Beispiel wird das Erstellen einer Kachelebene veranschaulicht, die auf mehrere Kacheln verweisen. In diesem Beispiel wird das Kachelsystem „X, Y, Zoom“ verwendet. Die Quelle dieser Kachelebene stellt das [OpenSeaMap-Projekt](https://openseamap.org/index.php) dar, das per Crowdsourcing erworbene Seekarten enthält. Bei der Anzeige von Kachelebenen ist es oft wünschenswert, dass die Bezeichnungen der Städte auf der Karte deutlich zu sehen sind. Dieses Verhalten wird erreicht, indem die Kachelebene unter den Kartenbezeichnungsebenen eingefügt wird.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Der folgende Screenshot zeigt, wie durch den oben aufgeführten Code eine Kachelebene mit nautischen Informationen auf einer Karte mit dem Stil „Graustufen dunkel“ angezeigt wird.

![Android-Karte mit einer Kachelebene](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Hinzufügen eines OGC-Webkartendiensts (WMS)

Ein Webkartendienst (Web Mapping Service, WMS) ist ein OGC-Standard (Open Geospatial Consortium) zum Bereitstellen von Bildern von Kartendaten. In diesem Format stehen viele offene Datasets zur Verfügung, die Sie mit Azure Maps verwenden können. Dieser Diensttyp kann mit einer Kachelebene verwendet werden, wenn der Dienst das Referenzkoordinatensystem `EPSG:3857` unterstützt. Bei Verwendung eines WMS-Diensts legen Sie die Parameter für Breite und Höhe auf den vom Dienst unterstützten Wert fest. Achten Sie darauf, dass Sie denselben Wert in der Option `tileSize` festlegen. In der formatierten URL legen Sie den Parameter `BBOX` des Diensts auf den Platzhalter `{bbox-epsg-3857}` fest.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Der folgende Screenshot zeigt den obigen Code zum Überlagern einer Karte mit einem Webkartendienst für geologische Daten von der [USGS (U.S. Geological Survey)](https://mrdata.usgs.gov/) unter den Bezeichnungen.

![Android-Karte mit einer WMS-Kachelebene](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Hinzufügen eines OGC-Webkartenkacheldiensts (WMTS)

Ein Webkartenkacheldienst (Web Mapping Tile Service, WMTS) ist ein OGC-Standard (Open Geospatial Consortium) zum Bereitstellen von kachelbasierten Überlagerungen für Karten. In diesem Format stehen viele offene Datasets zur Verfügung, die Sie mit Azure Maps verwenden können. Dieser Diensttyp kann mit einer Kachelebene verwendet werden, wenn der Dienst das Referenzkoordinatensystem `EPSG:3857` oder `GoogleMapsCompatible` unterstützt. Bei Verwendung eines WMTS-Diensts legen Sie die Parameter für Breite und Höhe auf den vom Dienst unterstützten Wert fest. Achten Sie darauf, dass Sie denselben Wert in der Option `tileSize` festlegen. In der formatierten URL ersetzen Sie die folgenden Platzhalter entsprechend:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

Der folgende Screenshot zeigt den obigen Code zum Überlagern einer Karte mit einem Webkartenkacheldienst für Bilder des [USGS National Map Service (U.S. Geological Survey)](https://viewer.nationalmap.gov/services/) unter den Straßen und Bezeichnungen.

![Android-Karte mit einer WMTS-Kachelebene](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie mehr über Möglichkeiten zum Überlagern mit Bildern auf einer Karte.

> [!div class="nextstepaction"]
> [Bildebene](map-add-image-layer-android.md)
