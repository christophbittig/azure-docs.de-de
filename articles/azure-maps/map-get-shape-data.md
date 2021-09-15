---
title: Abrufen von Daten aus Formen auf einer Karte | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie mithilfe des Microsoft Azure Maps Web SDK Formendaten abrufen, die auf einer Karte gezeichnet wurden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: ae9683140eb1a3faf685bb3ffe2ee299313eabe3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435004"
---
# <a name="get-shape-data"></a>Abrufen von Formdaten

In diesem Artikel wird gezeigt, wie Sie Daten von Formen erhalten, die auf der Karte gezeichnet sind. Wir verwenden die Funktion **drawingManager.getSource()** innerhalb des [Zeichnungs-Managers](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--). Es gibt verschiedene Szenarien, in denen Sie GeoJSON-Daten einer gezeichneten Form extrahieren und an anderer Stelle verwenden möchten.  


## <a name="get-data-from-drawn-shape"></a>Abrufen von Daten aus gezeichneten Formen

Die folgende Funktion ruft die Quelldaten einer gezeichneten Form ab und gibt sie auf dem Bildschirm aus. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Nachstehend finden Sie das vollständige, ausführbare Codebeispiel, in dem Sie eine Form zeichnen können, um die Funktion zu testen:

<br/>

<iframe height="686" title="Abrufen von Formdaten" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get shape data</a> (Abrufen von Formdaten) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie die weiteren Funktionen des Zeichentools-Moduls verwendet werden:

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