---
title: Zeichentools-Modul | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie mithilfe des Microsoft Azure Maps Web SDK Daten für Zeichenoptionen festlegen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: fd861e4ab92235ec4f2b3ec8051e854a08effd3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072368"
---
# <a name="use-the-drawing-tools-module"></a>Verwenden des Zeichentools-Moduls

Das Web SDK für Azure Maps stellt ein *Zeichentools-Modul* bereit. Dieses Modul vereinfacht das Zeichnen und Bearbeiten von Formen auf der Karte mit einem Eingabegerät wie einer Maus oder einem Touchscreen. Die Kernklasse dieses Moduls ist der [Zeichnungs-Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Der Zeichnungs-Manager stellt alle Funktionen zur Verfügung, die zum Zeichnen und Bearbeiten von Formen auf der Karte benötigt werden. Er kann direkt verwendet werden und ist in eine benutzerdefinierte Symbolleisten-Benutzeroberfläche integriert. Sie können auch die integrierte [Zeichnen-Symbolleisten](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)-Klasse verwenden.

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Laden des Zeichentools-Moduls auf eine Webseite

1. Erstellen Sie eine neue HTML-Datei, und [implementieren Sie die Karte wie üblich](./how-to-use-map-control.md).
2. Laden Sie das Azure Maps-Zeichentools-Modul. Hierzu stehen zwei Möglichkeiten zur Verfügung:
    - Verwenden Sie die global gehostete Azure Content Delivery Network-Version des Azure Maps-Moduls „Dienste“. Fügen Sie einen Verweis auf das JavaScript und das CSS-Stylesheet im `<head>`-Element der Datei hinzu:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/1/atlas-drawing.min.js"></script>
        ```

    - Alternativ können Sie das Zeichentools-Modul für den Quellcode des Azure Maps Web SDK lokal mithilfe des npm-Pakets [azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) laden und es anschließend zusammen mit Ihrer App hosten. Dieses Paket enthält außerdem TypeScript-Definitionen. Verwenden Sie diesen Befehl:

      `npm install azure-maps-drawing-tools`

      Fügen Sie anschließend einen Verweis auf das JavaScript und das CSS-Stylesheet im `<head>`-Element der Datei hinzu:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
      ```

## <a name="use-the-drawing-manager-directly"></a>Direktes Verwenden des Zeichnungs-Managers

Sobald das Zeichentools-Modul in Ihre Anwendung geladen ist, können Sie die Zeichen- und Bearbeitungsfunktionen mit dem [Zeichnungs-Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-) aktivieren. Sie können Optionen für den Zeichnungs-Manager festlegen, während Sie ihn instanziieren, oder alternativ die Funktion `drawingManager.setOptions()` verwenden.

### <a name="set-the-drawing-mode"></a>Festlegen des Zeichenmodus

Mit dem folgenden Code wird eine Instanz des Zeichnungs-Managers erstellt und die Option für den **Zeichnungsmodus** festgelegt. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Der nachstehende Code ist ein vollständiges, ausführbares Beispiel zum Festlegen eines Zeichnungsmodus für den Zeichnungs-Manager. Klicken Sie auf die Karte, um mit dem Zeichnen eines Polygons zu beginnen.

<br/>

<iframe height="500" scrolling="no" title="Zeichnen eines Polygons" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw a polygon</a> (Zeichnen eines Polygons) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Festlegen des Interaktionstyps

Der Zeichnungs-Manager unterstützt drei verschiedene Arten der Interaktion mit der Karte zum Zeichnen von Formen.

* `click`: Koordinaten werden hinzugefügt, wenn mit der Maus oder per Toucheingabe geklickt wird.
* `freehand `: Koordinaten werden hinzugefügt, wenn die Maus auf die Karte gezogen bzw. die gleiche Geste per Toucheingabe vollzogen wird. 
* `hybrid`: Koordinaten werden hinzugefügt, wenn mit der Maus oder per Toucheingabe geklickt oder gezogen wird.

Der folgende Code aktiviert den Polygon-Zeichenmodus und legt den Typ von Zeichnungsinteraktion, dem der Zeichnungs-Manager entsprechen soll, auf `freehand` fest. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 In diesem Codebeispiel wird die Funktionalität zum Zeichnen eines Polygons auf der Karte implementiert. Halten Sie einfach die linke Maustaste gedrückt und ziehen Sie sie frei umher.

<br/>

<iframe height="500" scrolling="no" title="Freihandzeichnung" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a> (Freihandzeichnung) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Anpassen von Zeichnungsoptionen

In den vorherigen Beispielen wurde gezeigt, wie Zeichnungsoptionen beim Instanziieren des Zeichnungs-Managers angepasst werden. Sie können die Optionen für den Zeichnungs-Manager auch mithilfe der `drawingManager.setOptions()`-Funktion festlegen. Nachstehend finden Sie ein Tool zum Testen der Anpassung aller Optionen für den Zeichnungs-Manager mit der setOptions-Funktion.

<br/>

<iframe height="685" title="Anpassen des Zeichnungs-Managers" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get shape data</a> (Abrufen von Formdaten) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="put-a-shape-into-edit-mode"></a>Eine Form in den Bearbeitungsmodus setzen

Sie können eine bestehende Form programmgesteuert in den Bearbeitungsmodus setzen, indem Sie diese an die `edit`-Funktion des Zeichnungs-Managers übergeben. Wenn die Form ein GeoJSON-Feature ist, sollten Sie vor der Übergabe die Klasse `atls.Shape` als Wrapper verwenden.

Setzen Sie den Modus des Zeichnungs-Managers auf `idle`, um eine Form programmgesteuert aus dem Bearbeitungsmodus zu nehmen.

```javascript
//If you are starting with a GeoJSON feature, wrap it with the atlas.Shape class.
var feature = { 
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0,0]
        },
    "properties":  {}
};

var shape = new atlas.Shape(feature);

//Pass the shape into the edit function of the drawing manager.
drawingManager.edit(shape);

//Later, to programmatically take shape out of edit mode, set mode to idle. 
drawingManager.setOptions({ mode: 'idle' });
```

> [!NOTE]
> Wenn eine Form an die `edit`-Funktion des Zeichnungs-Managers übergeben wird, wird sie zur Datenquelle hinzugefügt, die vom Zeichnungs-Manager gepflegt wird. Wenn die Form zuvor in einer anderen Datenquelle war, wird sie aus dieser Datenquelle entfernt.

Wenn Sie Formen zum Zeichnungs-Manager hinzufügen möchten, damit Endbenutzer diese aufrufen und bearbeiten können, die Formen aber nicht programmgesteuert in den Bearbeitungsmodus setzen möchten, müssen Sie die Datenquelle vom Zeichnungs-Manager abrufen und Ihre Formen der Quelle hinzufügen.

```javascript
//The shape(s) you want to add to the drawing manager so 
var shape = new atlas.Shape(feature);

//Retrieve the data source from the drawing manager.
var source = drawingManager.getSource();

//Add your shape.
source.add(shape);

//Alternatively, load in a GeoJSON feed using the sources importDataFromUrl function.
source.importDataFromUrl('yourFeatures.json');
```

In der folgenden Tabelle werden die jeweiligen verschiedenen Bearbeitungsarten für Formfeatures aufgelistet.

| Formfeature | Bearbeitungspunkte | Rotate | Form löschen |
|---------------|:-----------:|:------:|:------------:|
| Point         | ✓           |        | ✓           |
| LineString    | ✓           | ✓      | ✓           |
| Polygon       | ✓           | ✓      | ✓           |
| MultiPoint    |             | ✓      | ✓           |
| MultiLineString |           | ✓      | ✓           |
| MultiPolygon  |             | ✓      | ✓           |
| Circle        | ✓           |        | ✓           |
| Rechteck     | ✓           | ✓      | ✓           |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie die weiteren Funktionen des Zeichentools-Moduls verwendet werden:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolleiste zum Zeichnen](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Abrufen von Formdaten](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagieren auf Zeichnungsereignisse](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstypen und Tastenkombinationen](drawing-tools-interactions-keyboard-shortcuts.md)

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Zeichnungs-Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Zeichnungssymbolleiste](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
