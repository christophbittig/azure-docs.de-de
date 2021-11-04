---
title: Implementieren von dynamischen Stilen für Gebäudepläne von Azure Maps-Ersteller
description: Erfahren Sie, wie Sie dynamische Stile für Gebäudepläne von Ersteller implementieren.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 9089ff5adda1eedd441fc221ae1d5a0d3401ae12
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462062"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Implementieren dynamischer Stile für Gebäudepläne von Ersteller

Sie können den [Featurezustandsdienst](/rest/api/maps/v2/feature-state) von Azure Maps-Ersteller nutzen, um Stile basierend auf den dynamischen Eigenschaften der Features von Gebäudeplandaten anzuwenden.  Beispielsweise können Sie Besprechungsräume der Einrichtung mit einer bestimmten Farbe rendern, um den Belegungsstatus anzuzeigen. Dieser Artikel beschreibt, wie Sie Features von Gebäudeplänen mithilfe des [Featurezustandsdiensts](/rest/api/maps/v2/feature-state) und des [Indoor-Webmoduls](how-to-use-indoor-module.md) rendern.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. [Erstellen einer Erstellerressource](how-to-manage-creator.md)
4. Laden Sie das [Beispielzeichenpaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples) herunter.
5. [Erstellen Sie einen Gebäudeplan](tutorial-creator-indoor-maps.md), um eine `tilesetId` und `statesetId` zu erhalten.
6. Erstellen Sie eine Webanwendung, indem Sie die Schritte in [Verwenden des Moduls für Gebäudepläne](how-to-use-indoor-module.md) ausführen.

In diesem Tutorial wird die [Postman](https://www.postman.com/)-Anwendung verwendet, Sie können aber auch eine andere API-Entwicklungsumgebung auswählen.

## <a name="implement-dynamic-styling"></a>Implementieren dynamischer Stile

Nachdem Sie die Voraussetzungen erfüllt haben, sollten Sie über eine einfache Webanwendung verfügen, die mit Ihrem Abonnementschlüssel sowie der `tilesetId` und `statesetId` konfiguriert ist.

### <a name="select-features"></a>Auswählen von Features

Um dynamische Stile zu implementieren, muss ein Feature, wie z. B. ein Besprechungs- oder Konferenzraum, über die `id` seines Features referenziert werden. Sie verwenden die `id` des Features, um die dynamische Eigenschaft oder den *Zustand* dieses Features zu aktualisieren. Um die in einem Dataset definierten Features anzuzeigen, können Sie eine der folgenden Methoden verwenden:

* WFS-API (Web Feature Service). Sie können die [WFS-API](/rest/api/maps/v2/wfs) nutzen, um Datasets abzufragen. WFS hält die [Features der Open Geospatial Consortium-API](http://docs.opengeospatial.org/DRAFTS/17-069r1.html) ein. Die WFS-API ist hilfreich beim Abfragen von Features innerhalb eines Datasets. Beispielsweise können Sie WFS verwenden, um alle mittelgroßen Besprechungsräume einer bestimmten Einrichtung und in einem bestimmten Stockwerk zu finden.

* Implementieren Sie benutzerdefinierten Code, der es einem Benutzer ermöglicht, mithilfe Ihrer Webanwendung Features auf einer Karte auszuwählen. Wir nutzen die in diesem Artikel gezeigte Option.  

Das folgende Skript implementiert das Mausklick-Ereignis. Der Code ruft die `id` des Features basierend auf dem angeklickten Punkt ab. In Ihrer Anwendung können Sie den Code nach dem Block mit Ihrem Indoor-Manager-Code einfügen. Führen Sie Ihre Anwendung aus, und überprüfen Sie anschließend die Konsole, um die `id` des Features des angeklickten Punkts zu erhalten.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

Im Tutorial [Erstellen eines Gebäudeplans](tutorial-creator-indoor-maps.md) wurde das Featurezustandsset für die Annahme von Zustandsaktualisierungen für `occupancy` konfiguriert.

Im nächsten Abschnitt legen wir den *Zustand* von „occupancy“ (Belegung) von Büro `UNIT26` auf `true` und von Büro `UNIT27` auf `false` fest.

### <a name="set-occupancy-status"></a>Festlegen des Belegungsstatus

 Wir aktualisieren jetzt den Zustand der beiden Büros `UNIT26` und `UNIT27`:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsnamen) für die Anforderung ein, z. B. *POST Data Upload*.

4. Geben Sie die folgende URL in die [Featureupdatezustände-API](/rest/api/maps/v2/feature-state/update-states) ein (ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `statesetId` durch `statesetId`):

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

5. Wählen Sie die Registerkarte **Headers** (Header) aus.

6. Wählen Sie im Feld **KEY** (SCHLÜSSEL) die Option `Content-Type` aus. Wählen Sie im Feld **VALUE** (WERT) die Option `application/json` aus.

     :::image type="content" source="./media/indoor-map-dynamic-styling/stateset-header.png"alt-text="Informationen zur Header-Registerkarte für die Erstellung von Zustandssets.":::

7. Wählen Sie die Registerkarte **Body** (Text) aus.

8. Wählen Sie in den Dropdownlisten **raw** und **JSON** aus.

9. Kopieren Sie den folgenden JSON-Stil, und fügen Sie ihn in das **Body**-Fenster (Text) ein.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!IMPORTANT]
    >Das Update wird nur gespeichert, wenn der gepostete Zeitstempel nach dem Zeitstempel liegt, der in früheren Featurezustandsaktualisierungs-Anforderungen für dieselbe `ID` des Features verwendet wurde.

10. Ändern Sie die URL, die Sie in Schritt 7 verwendet haben, indem Sie `UNIT26` durch `UNIT27` ersetzen:

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT27?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

11. Kopieren Sie den folgenden JSON-Stil, und fügen Sie ihn in das **Body**-Fenster (Text) ein.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualisieren dynamischer Stile auf einer Karte

Die Webanwendung, die Sie zuvor in einem Browser geöffnet haben, sollte jetzt den aktualisierten Zustand der Kartenfeatures widerspiegeln:

* Office `UNIT27` (142) sollte grün angezeigt werden.
* Office `UNIT26` (143) sollte rot angezeigt werden.

![Freier Raum in Grün und belegter Raum in Rot](./media/indoor-map-dynamic-styling/room-state.png)

[Livedemo](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

> [!div class="nextstepaction"]
> [Ersteller für Gebäudepläne](creator-indoor-maps.md)

Informationen finden Sie in den API-Referenzen, die in diesem Artikel erwähnt werden:

> [!div class="nextstepaction"]
> [Datenupload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Datenkonvertierung](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Kachelset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Featurezustandsset](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS-Dienst](creator-indoor-maps.md#web-feature-service-api)
