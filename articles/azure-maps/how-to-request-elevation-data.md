---
title: Anfordern von Höhendaten mithilfe des Azure Maps-Höhenangabendiensts
description: Hier erfahren Sie, wie Sie mit dem Azure Maps-Höhenangabendienst Höhendaten anfordern.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
ms.custom: mvc
ms.openlocfilehash: a9920d3b512bda0b2710ad9c0c6daf5c5801602a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432166"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>Anfordern von Höhendaten mithilfe des Azure Maps-Höhenangabendiensts

Der [Höhenangabendienst](/rest/api/maps/elevation) von Azure Maps umfasst APIs, über die Höhendaten an beliebigen Punkten auf der Erdoberfläche abgefragt werden können. Sie können Stichproben für Höhenangaben entlang von Routen, innerhalb eines definierten Begrenzungsrahmens oder an bestimmten Koordinaten anfordern. Außerdem können Sie die [Render V2-API zum Abrufen von Kartenkacheln](/rest/api/maps/renderv2) verwenden, um Höhendaten im Kachelformat abzurufen. Die Kacheln werden im GeoTIFF-Rasterformat bereitgestellt. In diesem Artikel wird erläutert, wie Sie mit dem Azure Maps-Höhenangabendienst und der API zum Abrufen von Kartenkacheln Höhendaten anfordern. Die Höhendaten können im GeoJSON- und im GeoTIFF-Format angefordert werden.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos im Tarif „Gen 1 (S1)“ oder „Gen 2“](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

In diesem Artikel wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="request-elevation-data-in-raster-tile-format"></a>Anfordern von Höhendaten im Rasterkachelformat

Verwenden Sie zum Abrufen von Höhendaten im Rasterkachelformat die [Render V2-Get Map Tile-API](/rest/api/maps/renderv2). Wenn die Kachel gefunden wird, wird sie von der API im GeoTIFF-Format zurückgegeben. Andernfalls wird „0“ (null) zurückgegeben. Alle DEM-Rasterkacheln verwenden den GeoID-Modus (Meeresspiegel). In diesem Beispiel werden Höhendaten für den Mount Everest angefordert.

>[!TIP]
>Suchen Sie zum Abrufen einer Kachel in einem bestimmten Bereich der Weltkarte die richtige Kachel in der entsprechenden Zoomstufe. Beachten Sie auch, dass WorldDEM die gesamte globale Landmasse, jedoch keine Ozeane abdeckt.  Weitere Informationen finden Sie unter [Zoomstufen und Grobraster](zoom-levels-and-tile-grid.md).

So fordern Sie mithilfe der Postman-App Höhendaten im Rasterkachelformat an:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Klicken Sie im Fenster **Create New** (Neu erstellen) auf **HTTP Request** (HTTP-Anforderung).

3. Geben Sie einen Anforderungsnamen (**Request name**) ein.

4. Wählen Sie auf der Registerkarte **Builder** (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein, um die Rasterkachel anzufordern.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

    >[!Important]
    >Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

5. Wählen Sie die Schaltfläche **Send (Senden)** aus.

    Es sollte die Rasterkachel mit den Höhendaten im GeoTIFF-Format ausgegeben werden. Jedes Pixel in den Rohdaten der Rasterkachel hat den Typ `float`. Der Wert jedes Pixels stellt die Höhe in Metern dar.

## <a name="request-elevation-data-in-geojson-format"></a>Anfordern von Höhendaten im GeoJSON-Format

Verwenden Sie zum Anfordern von Höhendaten im GeoJSON-Format die Höhenangabendienst-APIs. In diesem Abschnitt wird jede dieser APIs beschrieben:

* [Abrufen von Daten für Punkte](/rest/api/maps/elevation/getdataforpoints)
* [Veröffentlichen von Daten für Punkte](/rest/api/maps/elevation/postdataforpoints)
* [Abrufen von Daten für Polylinie](/rest/api/maps/elevation/getdataforpolyline)
* [Veröffentlichen von Daten für Polylinie](/rest/api/maps/elevation/postdataforpolyline)
* [Abrufen von Daten für Begrenzungsrahmen](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Wenn keine Daten zurückgegeben werden können, geben alle APIs **0** zurück.

### <a name="request-elevation-data-for-points"></a>Anfordern von Höhendaten für Punkte

In diesem Beispiel werden über die [API zum Abrufen von Daten für Punkte](/rest/api/maps/elevation/getdataforpoints) Höhendaten für den Mount Everest und den Chamlang angefordert. Anschließend werden über die [API zum Veröffentlichen von Daten für Punkte](/rest/api/maps/elevation/postdataforpoints) Höhendaten anhand der gleichen beiden Punkte angefordert. Es wird davon ausgegangen, dass die Längen- und Breitenangaben in der URL in WGS84-Dezimalgrad (World Geodetic System) angegeben werden.

 >[!IMPORTANT]
 >Die Länge von URLs ist auf 2048 Zeichen beschränkt, sodass nicht mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge in der GET-Anforderung einer URL übergeben werden können. Wenn Sie mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge übergeben möchten, verwenden Sie die API zum Veröffentlichen von Daten für Punkte.

So erstellen Sie die Anforderung:

1. Klicken Sie in der Postman-App noch mal auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) ein.

4. Wählen Sie auf der Registerkarte **Builder** (Generator) die HTTP-Methode **GET** aus, und geben Sie dann die folgende URL ein (dabei `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel ersetzen):

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

5. Wählen Sie die Schaltfläche **Send (Senden)** aus.  Die folgende JSON-Antwort wird zurückgegeben:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

6. Nun wird die [API zum Veröffentlichen von Daten für Punkte](/rest/api/maps/elevation/postdataforpoints) aufgerufen, um die Höhendaten für die beiden gleichen Punkte abzurufen. Wählen Sie auf der Registerkarte **Builder** (Generator) die HTTP-Methode **POST** aus, und geben Sie dann die folgende URL ein (dabei `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel ersetzen):

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

7. Legen Sie im Feld **Header** der **POST**-Anforderung `Content-Type` auf `application/json` fest. 

8. Geben Sie im Feld **Text** die folgenden Koordinatenpunktinformationen an:

    ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

9. Wählen Sie **Send** (Senden) aus.

### <a name="request-elevation-data-samples-along-a-polyline"></a>Anfordern von Stichproben für Höhenangaben entlang einer Polylinie

In diesem Beispiel werden über die [API zum Abrufen von Daten für die Polylinie](/rest/api/maps/elevation/getdataforpolyline) fünf gleichmäßig verteilte Stichproben für Höhenangaben entlang einer geraden Linie zwischen den Koordinaten für den Mount Everest und den Chamlang angefordert. Beide Koordinaten müssen im Längen- und Breitengradformat definiert werden. Wenn Sie keinen Wert für den `samples`-Parameter angeben, wird die Anzahl der Stichproben standardmäßig auf 10 festgelegt. Die maximale Anzahl der Stichproben liegt bei 2.000.

Anschließend werden über die API zum Abrufen von Daten für die Polylinie drei gleichmäßig verteilte Stichproben für Höhenangaben entlang einer Route angefordert. Sie definieren die genaue Position für die Stichproben durch Übergeben der drei Koordinatenpaare im Längen- und Breitengradformat.

Schließlich werden über die [API zum Veröffentlichen von Daten für Polylinie](/rest/api/maps/elevation/postdataforpolyline) Höhendaten für dieselben drei gleichmäßig verteilten Stichproben angefordert.

Es wird davon ausgegangen, dass die Längen- und Breitenangaben in der URL in WGS84-Dezimalgrad (World Geodetic System) angegeben werden.

 >[!IMPORTANT]
 >Die Länge von URLs ist auf 2048 Zeichen beschränkt, sodass nicht mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge in der GET-Anforderung einer URL übergeben werden können. Wenn Sie mehr als 100 Koordinaten als eine durch senkrechte Striche getrennte Zeichenfolge übergeben möchten, verwenden Sie die API zum Veröffentlichen von Daten für Punkte.

So erstellen Sie die Anforderung:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Anforderungsnamen** ein.

4. Wählen Sie auf der Registerkarte **Builder** (Generator) die HTTP-Methode **GET** aus, und geben Sie dann die folgende URL ein (dabei `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel ersetzen):

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

5. Wählen Sie die Schaltfläche **Send (Senden)** aus.  Die folgende JSON-Antwort wird zurückgegeben:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

6. Nun werden drei Stichproben für Höhenangaben entlang einer Route zwischen den Koordinaten des Mount Everest, des Chamlang und des Jannu angefordert. Geben Sie im Feld **Params** das folgende Koordinatenarray für den Wert des Abfrageschlüssels `lines` ein.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

7. Ändern Sie den Wert `samples` des Abfrageschlüssels in `3`.  In der folgenden Abbildung sind die neuen Werte dargestellt.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Abrufen von drei Stichproben für Höhenangaben":::

8. Wählen Sie **Send** (Senden) aus. Die folgende JSON-Antwort wird zurückgegeben:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

9. Nun wird die [API zum Veröffentlichen von Daten für Polylinie](/rest/api/maps/elevation/postdataforpolyline) aufgerufen, um die Höhendaten für die drei gleichen Punkte abzurufen.  Wählen Sie auf der Registerkarte **Builder** (Generator) die HTTP-Methode **POST** aus, und geben Sie dann die folgende URL ein (dabei `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel ersetzen):

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&samples=5
    ```

10. Legen Sie im Feld **Header** der **POST**-Anforderung `Content-Type` auf `application/json` fest. 

11. Geben Sie im Feld **Text** die folgenden Koordinatenpunktinformationen an.

    ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

12. Wählen Sie **Send** (Senden) aus.

### <a name="request-elevation-data-by-bounding-box"></a>Anfordern von Höhendaten nach Begrenzungsrahmen

Nun verwenden Sie die [API zum Abrufen von Daten für Begrenzungsrahmen](/rest/api/maps/elevation/getdataforboundingbox), um Höhendaten in der Nähe von Mount Rainier im US-Bundesstaat Washington anzufordern. Die Höhendaten werden an gleichmäßig verteilten Positionen innerhalb eines Begrenzungsrahmens zurückgegeben. Der Begrenzungsrahmen ist durch zwei Gruppen von Längen- und Breitengradkoordinaten (südliche Breite, westliche Länge | nördliche Breite, östliche Länge) definiert und in Zeilen und Spalten unterteilt. Die Ränder des Begrenzungsrahmens umfassen zwei der Zeilen und zwei der Spalten. Für die an den Zeilen- und Spaltenschnittpunkten generierten Rasterpunkte werden Höhenangaben zurückgegeben. In einer Anforderung können bis zu 2.000 Höhenangaben zurückgegeben werden.

In diesem Beispiel wird „Rows = 3“ und „Columns = 6“ angegeben. Die Antwort gibt 18 Höhenangaben zurück. Im folgenden Diagramm sind die Höhenwerte beginnend in der südwestlichen Ecke und dann von Westen nach Osten und von Süden nach Norden angeordnet.  Die Höhenpunkte sind in der Reihenfolge nummeriert, in der sie zurückgegeben werden.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Koordinaten des Begrenzungsrahmens in den NO- und SO-Ecken":::

So erstellen Sie die Anforderung:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Anforderungsnamen** ein.

4. Wählen Sie auf der Registerkarte **Builder** (Generator) die HTTP-Methode **GET** aus, und geben Sie dann die folgende URL ein (dabei `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel ersetzen):

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

5. Wählen Sie **Send** (Senden) aus.  Die Antwort gibt 18 Stichproben für Höhenangaben zurück, jeweils eine für jeden Scheitelpunkt des Rasters.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>Beispiele: Verwenden von APIs des Höhenangabendiensts im Azure Maps-Steuerelement

### <a name="get-elevation-data-by-coordinate-position"></a>Abrufen von Höhendaten nach Koordinatenposition

Auf der folgenden Beispielwebseite wird beschrieben, wie Sie mit dem Kartensteuerelement Höhendaten an einem Koordinatenpunkt anzeigen. Wenn Sie den Marker bewegen, zeigt die Karte Höhendaten in einem Popupfenster an.

<br/>

<iframe height="500" scrolling="no" title="Abrufen der Höhenangabe an einer Position" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Stift <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>Abrufen der Höhenangabe an einer Position</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Abrufen von Höhendaten nach Begrenzungsrahmen

Auf der folgenden Beispielwebseite wird beschrieben, wie Sie mit dem Kartensteuerelement Höhendaten in einem Begrenzungsrahmen anzeigen. Sie definieren den Begrenzungsrahmen, indem Sie auf das Symbol `square` links oben klicken und dann das Quadrat an die gewünschte Position auf der Karte ziehen. Mit dem Kartensteuerelement werden die Höhendaten dann in den Farben gerendert, die im Schlüssel rechts oben angegeben sind.

<br/>

<iframe height="500" scrolling="no" title="Höhenangaben nach Begrenzungsrahmen" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Stift <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>Höhenangaben nach Begrenzungsrahmen</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Abrufen von Höhendaten nach Polylinienroute

Auf der folgenden Beispielwebseite wird beschrieben, wie Sie mit dem Kartensteuerelement Höhendaten entlang einer Route anzeigen. Benutzer definieren die Route, indem sie in der oberen linken Ecke auf das Symbol `Polyline` klicken und dann die Polylinie auf der Karte zeichnen. Mit dem Kartensteuerelement werden die Höhendaten dann in den Farben gerendert, die im Schlüssel oben rechts angegeben sind.

<br/>

<iframe height="500" scrolling="no" title="Farbverlauf der Höhenroute" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter dem Stift <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>Farbverlauf der Höhenroute</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den ElevationAPIs von Azure Maps finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Höhenangabendienst: Abrufen von Daten für Längen- und Breitenkoordinaten](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Höhenangabendienst: Abrufen von Daten für Begrenzungsrahmen](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Höhenangabendienst: Abrufen von Daten für Polylinie](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2: Abrufen von Kartenkacheln](/rest/api/maps/renderv2)

Eine vollständige Liste mit Azure Maps-REST-APIs finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Maps-REST-APIs](/rest/api/maps/)
