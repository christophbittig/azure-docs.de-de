---
title: Rendern von benutzerdefinierten Daten auf einer Rasterkarte in Microsoft Azure Maps
description: Hier erfahren Sie, wie Sie Ortsmarken, Bezeichnungen und geometrische Formen zu einer Rasterkarte hinzufügen. Informieren Sie sich zu diesem Zweck über die Verwendung des statischen Bilddiensts in Azure Maps.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9197090d405210db485d1ac8d471d899f58b8e43
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443640"
---
# <a name="render-custom-data-on-a-raster-map"></a>Rendern von benutzerdefinierten Daten auf einer Rasterkarte

In diesem Artikel wird beschrieben, wie Sie den [statischen Bilddienst](/rest/api/maps/render/getmapimage) mit der Bildzusammensetzungsfunktionalität verwenden. Die Bildzusammensetzungsfunktion unterstützt das Abrufen einer statischen Rasterkachel, die benutzerdefinierte Daten enthält.

Nachfolgend finden Sie Beispiele für benutzerdefinierte Daten:

- Benutzerdefinierte Ortsmarken
- Bezeichnungen
- Geometrieüberlagerungen

> [!Tip]
> Häufig ist die Nutzung des Azure Maps Web SDK zum Anzeigen einer einfachen Karte auf einer Webseite kostengünstiger als die Verwendung des statischen Bilddiensts. Das Web SDK verwendet Kartenkacheln. Sofern der Benutzer in der Karte nicht schwenkt und zoomt, wird oftmals nur der Bruchteil einer Transaktion pro Kartenladevorgang generiert. Das Azure Maps Web SDK verfügt über Optionen zum Deaktivieren von Schwenken und Zoomen. Außerdem bietet das Azure Maps Web SDK umfassendere Optionen für die Datenvisualisierung als ein statischer Kartenwebdienst.  

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

In diesem Artikel wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

Wir verwenden die [Data Service-APIs](/rest/api/maps/data) von Azure Maps zum Speichern und Rendern von Überlagerungen.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Rendern von Ortsmarken mit Bezeichnungen und benutzerdefinierten Bildern

> [!Note]
> Das Verfahren in diesem Abschnitt erfordert ein Azure Maps-Konto im Tarif „Gen 1“ oder „Gen 2“.
Azure Maps-Konten im Tarif „Gen1 Standard S0“ unterstützen nur eine einzelne Instanz des Parameters `pins`. Sie können bis zu fünf Ortsmarken rendern, die in der URL-Anforderung mit einem benutzerdefinierten Bild festgelegt werden.

### <a name="get-static-image-with-custom-pins-and-labels"></a>Abrufen eines statischen Bilds mit benutzerdefinierten Ortsmarken und Bezeichnungen

So rufen Sie ein statisches Bild mit benutzerdefinierten Ortsmarken und Bezeichnungen ab

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein, beispielsweise *GET Static Image* (Statisches Bild ABRUFEN).


4. Wählen Sie als HTTP-Methode **GET** aus.


5. Geben Sie die folgende URL ein (ersetzen Sie {`Your-Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel):

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Der Dienst gibt das folgende Bild zurück:

    :::image type="content" source="./media/how-to-render-custom-data/render-pins.png" alt-text="Eine benutzerdefinierte Ortsmarke mit einer Bezeichnung.":::

## <a name="upload-pins-and-path-data"></a>Hochladen von Ortsmarken- und Pfaddaten

> [!Note]
> Der Vorgang in diesem Abschnitt erfordert ein Azure Maps-Konto in der Preisstufe Gen 1 (S1) oder Gen 2.

In diesem Abschnitt laden wir Pfad- und Ortsmarkendaten in den Azure Map-Datenspeicher hoch.

So laden Sie Ortsmarken- und Pfaddaten hoch

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsnamen) für die Anforderung ein, z. B. *POST Path and Pin Data*.

4. Wählen Sie als HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL ein (ersetzen Sie {`Your-Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel):

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

6. Wählen Sie die Registerkarte **Body** (Text) aus.

7. Wählen Sie in den Dropdownlisten **raw** und **JSON** aus.

8. Kopieren Sie die folgenden JSON-Daten als hochzuladende Daten, und fügen Sie sie dann in das Fenster **Body** (Text) ein:
  
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

9. Wählen Sie **Send** (Senden) aus.

10. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

11. Kopieren Sie den Wert des Schlüssels **Operation-Location** (Vorgangsspeicherort). Hierbei handelt es sich um die Status-URL (`status URL`). Wir verwenden die `status URL`, um den Status der Uploadanforderung im nächsten Abschnitt zu überprüfen. `status URL` weist das folgende Format auf:

   ```HTTP
   https://us.atlas.microsoft.com/mapData/operations/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?api-version=2.0
   ```

>[!TIP]
>Verwenden Sie die [API zum Hochladen von Daten](/rest/api/maps/data-v2/upload-preview), um Ihre eigenen Pfad- und Ortsmarken-Positionsinformationen zu erhalten.

### <a name="check-pins-and-path-data-upload-status"></a>Überprüfen des Uploadstatus von Ortsmarken- und Pfaddaten

So überprüfen Sie den Status des Datenuploads und rufen seine eindeutige ID (`udid`) ab

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Datenuploadstatus*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die `status URL` ein, die Sie im Schritt [Hochladen von Ortsmarken- und Pfaddaten](#upload-pins-and-path-data) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen (ersetzen Sie {`Your-Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel):

   ```HTTP
     https://us.atlas.microsoft.com/mapData/operations/{statusUrl}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
   ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

8. Kopieren Sie den Wert des Schlüssels **Resource Location** (Ressourcenspeicherort). Hierbei handelt es sich um die URL des Ressourcenspeicherorts (`resource location URL`). Die URL des Ressourcenspeicherorts (`resource location URL`) enthält den eindeutigen Bezeichner (`udid`) der Zeichnungspaketressource.

    :::image type="content" source="./media/how-to-render-custom-data/resource-location-url.png" alt-text="Kopieren Sie die URL des Ressourcenspeicherorts.":::

### <a name="render-uploaded-features-on-the-map"></a>Rendern hochgeladener Features auf der Karte

So rendern Sie die hochgeladenen Ortsmarken- und Pfaddaten auf der Karte

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Datenuploadstatus*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL zum [Renderdienst](/rest/api/maps/render/get-map-image) ein (ersetzen Sie {`Your-Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `udid` durch den `udid`-Wert der hochgeladenen Daten):

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Der Dienst gibt das folgende Bild zurück:

    :::image type="content" source="./media/how-to-render-custom-data/uploaded-path.png" alt-text="Rendern von hochgeladenen Daten in einem statischen Kartenbild.":::

## <a name="render-a-polygon-with-color-and-opacity"></a>Rendern eines Polygons mit Farbe und Transparenz

> [!Note]
> Der Vorgang in diesem Abschnitt erfordert ein Azure Maps-Konto in der Preisstufe Gen 1 (S1) oder Gen 2.

Sie können das Aussehen eines Polygons ändern, indem Sie Stilmodifikatoren mit dem Parameter [path](/rest/api/maps/render/getmapimage#uri-parameters) verwenden.

So rendern Sie ein Polygon mit Farbe und Deckkraft

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Polygon*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL zum [Renderdienst](/rest/api/maps/render/get-map-image) ein (ersetzen Sie {`Your-Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel):
  
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Der Dienst gibt das folgende Bild zurück:

    :::image type="content" source="./media/how-to-render-custom-data/opaque-polygon.png" alt-text="Rendern eines undurchsichtigen (opaken) Polygons.":::

## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Rendern von Kreisen und Ortsmarken mit benutzerdefinierten Bezeichnungen

> [!Note]
> Der Vorgang in diesem Abschnitt erfordert ein Azure Maps-Konto in der Preisstufe Gen 1 (S1) oder Gen 2.

Sie können die Darstellung der Ortsmarken durch Hinzufügen von Stilmodifizierern ändern. Sie können Ortsmarken und ihre Bezeichnungen beispielsweise vergrößern oder verkleinern, indem Sie den „Skalierungsstil“-Modifizierer `sc` verwenden. Dieser Modifizierer akzeptiert Werte über 0 (null). Der Wert 1 ist die Standardskalierung. Werte größer als 1 machen die Ortsmarken größer, und Werte kleiner als 1 machen sie kleiner. Weitere Informationen zu den Modifizierern finden Sie unter [static image service path parameters (Parameter für statische Bilddienstpfade)](/rest/api/maps/render/getmapimage#uri-parameters).

So rendern Sie einen Kreis und Ortsmarken mit benutzerdefinierten Bezeichnungen

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Polygon*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL zum [Renderdienst](/rest/api/maps/render/get-map-image) ein (ersetzen Sie {`Your-Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel):

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Der Dienst gibt das folgende Bild zurück:

    :::image type="content" source="./media/how-to-render-custom-data/circle-custom-pins.png" alt-text="Rendern eines Kreises mit benutzerdefinierten Ortsmarken.":::

8. Nun ändern wir die Farbe der Ortsmarken, indem wir den Stilmodifizierer `co` ändern. Wenn Sie den Wert des `pins`-Parameters (`pins=default|la15+50|al0.66|lc003C62|co002D62|`) betrachten, sehen Sie, dass die aktuelle Farbe `#002D62` ist. Um die Farbe in `#41d42a` zu ändern, ersetzen wir `#002D62` durch `#41d42a`.  Der Parameter `pins` lautet nun `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Die Anforderung sieht wie die folgende URL aus:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

9. Wählen Sie **Send** (Senden) aus.

10. Der Dienst gibt das folgende Bild zurück:

    :::image type="content" source="./media/how-to-render-custom-data/circle-updated-pins.png" alt-text="Rendern eines Kreises mit aktualisierten Ortsmarken.":::

Auf ähnliche Weise können Sie andere Stilmodifiziererer ändern, hinzufügen und entfernen.

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit der API-Dokumentation zum [Get Map-Image-API von Azure Maps](/rest/api/maps/render/getmapimage) vertraut.
* Weitere Informationen zum Azure Maps-Datendienst finden Sie in der [Dienstdokumentation](/rest/api/maps/data).
