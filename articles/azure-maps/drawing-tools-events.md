---
title: Ereignisse von Zeichentools | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie einer Karte mithilfe des Microsoft Azure Maps Web SDK eine Zeichnungssymbolleiste hinzufügen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ebafc4ac95afd4c9b6d414966d616ee7315414c1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437776"
---
# <a name="drawing-tool-events"></a>Ereignisse von Zeichentools

Wenn Sie auf einer Karte Zeichentools verwenden, ist es sinnvoll, auf bestimmte Ereignisse zu reagieren, während der Benutzer auf der Karte zeichnet. Die folgende Tabelle enthält alle Ereignisse, die von der Klasse `DrawingManager` unterstützt werden:

| Ereignis | BESCHREIBUNG |
|-------|-------------|
| `drawingchanged` | Wird ausgelöst, wenn in einer Form eine Koordinate hinzugefügt oder geändert wurde. | 
| `drawingchanging` | Wird ausgelöst, wenn eine Vorschaukoordinate für eine Form angezeigt wird. Dieses Ereignis wird beispielsweise mehrmals ausgelöst, wenn eine Koordinate gezogen wird. | 
| `drawingcomplete` | Wird ausgelöst, wenn eine Form fertig gezeichnet oder aus dem Bearbeitungsmodus entfernt wurde. |
| `drawingerased` | Wird ausgelöst, wenn eine Form im `erase-geometry`-Modus aus dem Zeichnungs-Manager gelöscht wird. |
| `drawingmodechanged` | Wird ausgelöst, wenn sich der Zeichnungsmodus ändert. Der neue Zeichnungsmodus wird an den Ereignishandler übergeben. |
| `drawingstarted` | Wird ausgelöst, wenn der Benutzer mit dem Zeichnen einer Form beginnt oder eine Form in den Bearbeitungsmodus versetzt.  |

Der folgende Code zeigt, wie die Ereignisse im Zeichentools-Modul funktionieren. Zeichnen Sie Formen auf der Karte, und beobachten Sie, wie die Ereignisse ausgelöst werden.

<br/>

<iframe height="500" scrolling="no" title="Ereignisse von Zeichentools" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>Drawing tools events</a> (Ereignisse von Zeichentools) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Beispiele

Hier finden Sie einige gängige Szenarien, in denen die Ereignisse von Zeichentools verwendet werden.

### <a name="select-points-in-polygon-area"></a>Auswählen von Punkten in Polygonbereichen

Dieser Code veranschaulicht die Überwachung eines Ereignisses, bei dem ein Benutzer Formen zeichnet. In diesem Beispiel werden durch den Code Formen von Polygonen, Rechtecken und Kreisen überwacht. Anschließend wird ermittelt, welche Datenpunkte auf der Karte innerhalb des gezeichneten Bereichs liegen. Mithilfe des `drawingcomplete`-Ereignisses wird die Auswahllogik ausgelöst. In der Auswahllogik durchläuft der Code alle Datenpunkte auf der Karte. Dabei wird überprüft, ob es eine Überschneidung zwischen dem Punkt und dem Bereich der gezeichneten Form gibt. In diesem Beispiel wird die Open-Source-Bibliothek [Turf.js](https://turfjs.org/) verwendet, um die Berechnung der räumlichen Überschneidung durchzuführen.

<br/>

<iframe height="500" scrolling="no" title="Auswählen von Daten in einem gezeichneten Polygonbereich" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select data in drawn polygon area</a> (Auswählen von Daten in einem gezeichneten Polygonbereich) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Zeichnen und Suchen in einem Polygonbereich

Dieser Code sucht innerhalb des Bereichs einer Form nach POIs, nachdem der Benutzer die Form fertig gezeichnet hat. Sie können den Code ändern und ausführen, indem Sie in der rechten oberen Ecke des Frames auf „Auf CodePen bearbeiten“ klicken. Mithilfe des `drawingcomplete`-Ereignisses wird die Suchlogik ausgelöst. Wenn der Benutzer ein Rechteck oder Polygon zeichnet, wird innerhalb der Geometrie eine Suche durchgeführt. Wenn ein Kreis gezeichnet wird, werden der Radius und der Mittelpunkt verwendet, um eine Point of Interest-Suche auszuführen. Mithilfe des `drawingmodechanged`-Ereignisses wird bestimmt, wann der Benutzer in den Zeichnungsmodus wechselt, und der Zeichenbereich wird geleert.

<br/>

<iframe height="500" scrolling="no" title="Zeichnen und Suchen in einem Polygonbereich" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Draw and search in polygon area</a> (Zeichnen und Suchen in einem Polygonbereich) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Erstellen eines Messtools

Der folgende Code veranschaulicht, wie mithilfe von Zeichenereignissen ein Messtool erstellt werden kann. Zum Überwachen der Form während des Zeichnens wird `drawingchanging` verwendet. Wenn der Benutzer den Mauszeiger bewegt, werden die Abmessungen der Form berechnet. Das `drawingcomplete`-Ereignis wird für die abschließende Berechnung der Form nach Fertigstellung der Zeichnung verwendet. Mithilfe des `drawingmodechanged`-Ereignisses wird bestimmt, wann der Benutzer in den Zeichnungsmodus wechselt. Außerdem wird durch das `drawingmodechanged`-Ereignis der Zeichenbereich geleert, und alte Messdaten werden gelöscht.

<br/>

<iframe height="500" scrolling="no" title="Messtool" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Measuring tool</a> (Messtool) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie die weiteren Funktionen des Zeichentools-Moduls verwendet werden:

> [!div class="nextstepaction"]
> [Abrufen von Formdaten](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interaktionstypen und Tastenkombinationen](drawing-tools-interactions-keyboard-shortcuts.md)

Erfahren Sie mehr über das Services-Modul:

> [!div class="nextstepaction"]
> [Services-Modul](how-to-use-services-module.md)

Schauen Sie sich weitere Codebeispiele an:

> [!div class="nextstepaction"]
> [Seite für Beispielcode](https://aka.ms/AzureMapsSamples)
