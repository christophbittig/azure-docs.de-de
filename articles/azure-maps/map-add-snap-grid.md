---
title: Hinzufügen eines Ausrichtungsrasters zur Karte | Microsoft Azure Maps
description: Vorgehensweise zum Hinzufügen eines Ausrichtungsrasters zu einer Karte mithilfe des Web-SDK für Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 07/20/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 0236128925362bf2736489f2dbf97ab936183c93
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2021
ms.locfileid: "114670039"
---
# <a name="add-a-snap-grid-to-the-map"></a>Hinzufügen eines Ausrichtungsrasters zur Karte

Ein Ausrichtungsraster erleichtert das Zeichnen von Formen mit gemeinsam genutzten Kanten und Knoten sowie geraderen Linien. Das Ausrichten von Formen an einem Raster ist nützlich, wenn Sie Gebäudeumrisse oder Netzwerkpfade auf der Karte zeichnen.

Die Auflösung des Ausrichtungsrasters wird in Pixel angegeben. Das Raster ist quadratisch und relativ zum nächsten ganzzahligen Zoomfaktor. Das Raster wird um den Faktor 2 relativ zum physischen realen Bereich mit jedem Zoomfaktor skaliert.

## <a name="use-a-snap-grid"></a>Verwenden eines Ausrichtungsrasters

Erstellen Sie mithilfe der `atlas.drawing.SnapGridManager`-Klasse ein Ausrichtungsraster, und übergeben Sie einen Verweis auf die Karte, mit der Sie den Manager verbinden möchten. Legen Sie die Option `showGrid` auf `true` fest, wenn das Raster sichtbar sein soll. Um eine Form am Raster auszurichten, übergeben Sie sie an die `snapShape`-Funktion des Ausrichtungsraster-Managers. Wenn Sie ein Array von Positionen ausrichten möchten, übergeben Sie es an die `snapPositions`-Funktion.

Im folgenden Beispiel wird ein HTML-Marker beim Ziehen an einem Raster ausgerichtet. Zeichentools werden verwendet, um gezeichnete Formen am Raster auszurichten, wenn das `drawingcomplete`-Ereignis ausgelöst wird.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Verwenden eines Ausrichtungsrasters" src="https://codepen.io/azuremaps/embed/rNmzvXO?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href="https://codepen.io/azuremaps/pen/rNmzvXO">Use a snapping grid</a> (Verwenden eines Ausrichtungsrasters) von Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) auf <a href="https://codepen.io">CodePen</a>.
</iframe>


## <a name="snap-grid-options"></a>Ausrichtungsrasteroptionen

Das folgende Beispiel zeigt die verschiedenen Anpassungsoptionen, die für den Ausrichtungsraster-Manager verfügbar sind. Die Rasterlinienstile können angepasst werden, indem die zugrunde liegende Linienebene mithilfe der `getGridLayer`-Funktion des Ausrichtungsraster-Managers abgerufen wird.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Ausrichtungsrasteroptionen" src="https://codepen.io/azuremaps/embed/RwVZJry?default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href="https://codepen.io/azuremaps/pen/RwVZJry">Snap grid options</a> (Ausrichtungsrasteroptionen) von Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) auf <a href="https://codepen.io">CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie weitere Funktionen des Zeichentools-Moduls verwendet werden:

> [!div class="nextstepaction"]
> [Abrufen von Formdaten](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagieren auf Zeichnungsereignisse](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstypen und Tastenkombinationen](drawing-tools-interactions-keyboard-shortcuts.md)