---
title: Hinzufügen einer Kachelebene zu einer Karte | Microsoft Azure Maps
description: Erfahren Sie, wie Sie Karten mit Bildern überlagern. Sehen Sie sich ein Beispiel an, in dem das Azure Maps Web SDK verwendet wird, um einer Karte eine Kachelebene mit einer Wetterradarüberlagerung hinzuzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9b645ccc5347e55f46666563dc9bab565b8ba631
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439594"
---
# <a name="add-a-tile-layer-to-a-map"></a>Hinzufügen einer Kachelebene zu einer Karte

In diesem Artikel erfahren Sie, wie Sie eine Kachelebene auf der Karte überlagern. Mithilfe von Kachelebenen lassen sich Bilder über die Azure Maps-Basiskartenkacheln legen. Weitere Informationen zum Azure Maps-Kachelsystem finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

Eine Kachelebene wird in Kacheln von einem Server geladen. Diese Bilder können entweder vorab oder dynamisch gerendert werden. Vorab gerenderte Bilder werden wie jedes andere Bild auf einem Server gespeichert, wobei eine Namenskonvention verwendet wird, die von der Kachelebene verstanden wird. Dynamisch gerenderte Bilder verwenden einen Dienst, um die Bilder nahezu in Echtzeit zu laden. Es gibt drei verschiedene Namenskonventionen für Kacheldienste, die von der Azure Maps-Klasse [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) unterstützt werden:

* Notation von X, Y, Zoom – X ist die Spalten- und Y die Zeilenposition der Kachel im Kachelraster und die Zoomnotation ist ein Wert, der auf dem Zoomfaktor basiert.
* Quadkey-Notation – Kombiniert X-, Y- und Zoominformationen zu einem einzelnen Zeichenfolgenwert. Dieser Zeichenfolgenwert wird zu einem eindeutigen Bezeichner für eine einzelne Kachel.
* Begrenzungsrahmen – Geben Sie ein Bild im Koordinatenformat des Begrenzungsrahmens an: `{west},{south},{east},{north}`. Dieses Format wird häufig von [Webkartendiensten (Web Mapping Services, WMS)](https://www.opengeospatial.org/standards/wms) verwendet.

> [!TIP]
> Ein [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)-Element (Kachelebene) ist eine gute Möglichkeit, große Datensätze auf der Karte zu visualisieren. Eine Kachelebene lässt sich nicht nur aus einem Bild generieren, sondern auch Vektordaten können als Kachelebene gerendert werden. Durch das Rendern von Vektordaten als Kachelebene muss das Kartensteuerelement nur die Kacheln laden. Diese können eine kleinere Dateigröße aufweisen als die Vektordaten, die sie darstellen. Diese Technik wird häufig verwendet, um Millionen von Datenzeilen auf der Karte zu rendern.

Die in eine Kachelebene übergebene Kachel-URL muss eine HTTP- oder HTTPS-URL zu einer TileJSON-Ressource oder eine Kachel-URL-Vorlage sein, die die folgenden Parameter verwendet:

* `{x}`: X-Position der Kachel. Benötigt auch `{y}` und `{z}`.
* `{y}`: Y-Position der Kachel. Benötigt auch `{x}` und `{z}`.
* `{z}`: Zoomfaktor der Kachel. Benötigt auch `{x}` und `{y}`.
* `{quadkey}`: Kachel-Quadkey-Bezeichner basierend auf der Namenskonvention des Bing Maps-Kachelsystems.
* `{bbox-epsg-3857}`: Eine Begrenzungsrahmen-Zeichenfolge mit dem Format `{west},{south},{east},{north}` im Raumbezugssystem EPSG 3857.
* `{subdomain}`: Ein Platzhalter für die Unterdomänenwerte. Falls angegeben, wird die `subdomain` hinzugefügt.
* `{azMapsDomain}`: Ein Platzhalter zum Ausrichten der Domäne und Authentifizierung von Kachelanforderungen an dieselben Werte, die von der Karte verwendet werden.

## <a name="add-a-tile-layer"></a>Hinzufügen einer Kachelebene

 In diesem Beispiel wird das Erstellen einer Kachelebene veranschaulicht, die auf mehrere Kacheln verweisen. Dieses Beispiel verwendet das Kachelsystem „X, Y, Zoom“. Die Quelle dieser Kachelebene stellt das [OpenSeaMap-Projekt](https://openseamap.org/index.php) dar, das per Crowdsourcing erworbene Seekarten enthält. Beim Anzeigen von Radardaten würden die Benutzer im Idealfall die Bezeichnungen der Städte deutlich erkennen, wenn sie auf der Karte navigieren. Dieses Verhalten kann durch Einfügen der Kachelebene unter der Ebene `labels` implementiert werden.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Nachfolgend finden Sie das vollständige ausführbare Codebeispiel für die oben erläuterte Funktionalität.

<br/>

<iframe height='500' scrolling='no' title='Kachelebene mit X, Y und Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Sehen Sie sich den Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a> (Kachelebene mit X, Y und Z) in Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a> an.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Hinzufügen eines OGC-Webkartendiensts (WMS)

Ein Webkartendienst (Web Mapping Service, WMS) ist ein OGC-Standard (Open Geospatial Consortium) zum Bereitstellen von Bildern von Kartendaten. In diesem Format stehen viele offene Datasets zur Verfügung, die Sie mit Azure Maps verwenden können. Dieser Diensttyp kann mit einer Kachelebene verwendet werden, wenn der Dienst das Referenzkoordinatensystem `EPSG:3857` unterstützt. Bei Verwendung eines WMS-Diensts legen Sie die Parameter für Breite und Höhe auf den vom Dienst unterstützten Wert fest. Achten Sie darauf, dass Sie denselben Wert in der Option `tileSize` festlegen. In der formatierten URL legen Sie den Parameter `BBOX` des Diensts auf den Platzhalter `{bbox-epsg-3857}` fest.

Der folgende Screenshot zeigt den obigen Code zum Überlagern einer Karte mit einem Webkartendienst für geologische Daten von der [USGS (U.S. Geological Survey)](https://mrdata.usgs.gov/) unter den Bezeichnungen.

<br/>

<iframe height="500" scrolling="no" title="WMS-Kachelebene" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/BapjZqr'>WMS Tile Layer</a> (WMS-Kachelebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Hinzufügen eines OGC-Webkartenkacheldiensts (WMTS)

Ein Webkartenkacheldienst (Web Mapping Tile Service, WMTS) ist ein OGC-Standard (Open Geospatial Consortium) zum Bereitstellen von kachelbasierten Überlagerungen für Karten. In diesem Format stehen viele offene Datasets zur Verfügung, die Sie mit Azure Maps verwenden können. Dieser Diensttyp kann mit einer Kachelebene verwendet werden, wenn der Dienst das Referenzkoordinatensystem `EPSG:3857` oder `GoogleMapsCompatible` unterstützt. Bei Verwendung eines WMTS-Diensts legen Sie die Parameter für Breite und Höhe auf den vom Dienst unterstützten Wert fest. Achten Sie darauf, dass Sie denselben Wert in der Option `tileSize` festlegen. In der formatierten URL ersetzen Sie die folgenden Platzhalter entsprechend:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

Der folgende Screenshot zeigt den obigen Code zum Überlagern einer Karte mit einem Webkartenkacheldienst für Bilder des [USGS National Map Service (U.S. Geological Survey)](https://viewer.nationalmap.gov/services/) unter den Straßen und Bezeichnungen.

<br/>

<iframe height="500" scrolling="no" title="WMTS-Kachelebene" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/BapjZVY'>WMTS tile layer</a> (WMTS-Kachelebene) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Anpassen einer Kachelebene

Für die Kachelebenenklasse gibt es viele Formatierungsoptionen. Mit dem folgenden Tool können Sie diese ausprobieren.

<br/>

<iframe height='700' scrolling='no' title='Kachelebenenoptionen' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a> (Kachelebenenoptionen) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer Bildebene](./map-add-image-layer.md)
