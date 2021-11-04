---
title: 'Tutorial: Erstellen eines Geofence und Nachverfolgen von Geräten auf einer Microsoft Azure-Karte'
description: Tutorial zum Einrichten eines Geofence. Es wird beschrieben, wie Sie mit dem räumlichen Dienst von Azure Maps Geräte relativ zum Geofence nachverfolgen.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: b3f98990a34ada3d832498a892d289323f485a28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455338"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Einrichten eines Geofence mit Azure Maps

In diesem Tutorial werden Schritt für Schritt die Grundlagen der Erstellung und Verwendung von Azure Maps-Geofencediensten beschrieben.

Nehmen Sie das folgende Szenario als Beispiel:

*Ein Baustellenleiter muss den Ein- und Ausgang von Geräten für eine Baustelle überwachen. Wenn ein Gerät den Baustellenbereich verlässt oder in den Baustellenbereich gebracht wird, erhält der Baustellenleiter eine E-Mail.*

Azure Maps verfügt über eine Reihe von Diensten, die die Nachverfolgung des Ein- und Ausgangs von Geräten unterstützen. In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Sie laden [GeoJSON-Geofencingdaten](geofence-geojson.md) hoch, mit denen die zu überwachenden Baustellenbereiche definiert werden. Sie verwenden die [Datenupload-API](/rest/api/maps/data-v2/upload-preview), um Geofences als Polygonkoordinaten in Ihr Azure Maps-Konto hochzuladen.
> * Sie richten zwei [Logik-Apps](../event-grid/handler-webhooks.md#logic-apps) ein, die ausgelöst werden können, um E-Mail-Benachrichtigungen an den Baustellenleiter zu senden, wenn Geräte in den Geofencebereich gebracht werden oder diesen verlassen.
> * Sie verwenden [Azure Event Grid](../event-grid/overview.md), um Eingangs- und Ausgangsereignisse für Ihren Azure Maps-Geofence zu abonnieren. Sie richten zwei Webhook-Ereignisabonnements ein, von denen die in Ihren beiden Logik-Apps definierten HTTP-Endpunkte aufgerufen werden. Von den Logik-Apps werden dann die entsprechenden E-Mail-Benachrichtigungen für Geräte gesendet, die in den Geofencebereich gebracht werden oder diesen verlassen.
> * Verwenden Sie die [GET-API für die Geofencesuche](/rest/api/maps/spatial/getgeofence), um Benachrichtigungen zu erhalten, wenn ein Gerät in die Geofencebereiche gebracht oder daraus entfernt wird.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen Sie ein Azure Maps-Konto](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können jedoch auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="upload-geofencing-geojson-data"></a>Hochladen von GeoJSON-Geofencingdaten

In diesem Tutorial werden GeoJSON-Geofencingdaten hochgeladen, die eine Merkmalsauswahl (`FeatureCollection`) enthalten. Die Merkmalsauswahl (`FeatureCollection`) enthält zwei Geofences, durch die polygonale Bereiche innerhalb der Baustelle definiert werden. Für den ersten Geofence sind keine Ablaufzeit und keine Einschränkungen festgelegt. Der zweite kann nur während der Geschäftszeiten (9:00 bis 17:00 Uhr Pacific Time) abgefragt werden und ist nach dem 1. Januar 2022 nicht mehr gültig. Weitere Informationen zum GeoJSON-Format finden Sie unter [Geofencing von GeoJSON-Daten](geofence-geojson.md).

>[!TIP]
>Sie können Ihre Geofencingdaten jederzeit aktualisieren. Weitere Informationen finden Sie unter [Datenupload-API](/rest/api/maps/data-v2/upload-preview).

So laden Sie die GeoJSON-Geofencingdaten hoch

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *POST GeoJSON-Datenupload*.

4. Wählen Sie als HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL ein. Die Anforderung sollte wie die folgende URL aussehen (ersetzen `{Azure-Maps-Primary-Subscription-key}` Sie durch Ihren primären Abonnementschlüssel):

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

    Der Parameter `geojson` im URL-Pfad steht für das Format der Daten, die hochgeladen werden.

6. Wählen Sie die Registerkarte **Body** (Text) aus.

7. Wählen Sie in den Dropdownlisten **raw** und **JSON** aus.

8. Kopieren Sie die folgenden GeoJSON-Daten, und fügen Sie sie in das Fenster **Body** (Text) ein:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

9. Wählen Sie **Send** (Senden) aus.

10. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

11. Kopieren Sie den Wert des Schlüssels **Operation-Location** (Vorgangsspeicherort). Hierbei handelt es sich um die Status-URL (`status URL`). Sie verwenden die `status URL`, um den Status des GeoJSON-Datenuploads zu überprüfen.

    ```http
    https://us.atlas.microsoft.com/mapData/operations/{operationId}?api-version=2.0
    ```

### <a name="check-the-geojson-data-upload-status"></a>Überprüfen des Uploadstatus der GeoJSON-Daten

So überprüfen Sie den Status der GeoJSON-Daten und rufen die eindeutige ID (`udid`) ab

1. Wählen Sie **Neu** aus.

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Datenuploadstatus*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die `status URL` ein, die Sie in [Hochladen der GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen (ersetzen `{Azure-Maps-Primary-Subscription-key}` Sie durch Ihren primären Abonnementschlüssel):

   ```HTTP
   https://us.atlas.microsoft.com/mapData/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
   ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

8. Kopieren Sie den Wert des Schlüssels **Resource Location** (Ressourcenspeicherort). Hierbei handelt es sich um die URL des Ressourcenspeicherorts (`resource location URL`). Die URL des Ressourcenspeicherorts (`resource location URL`) enthält den eindeutigen Bezeichner (`udid`) der hochgeladenen Daten. Speichern Sie die `udid`, die zum Abfragen der Get Geofence-API im letzten Abschnitt dieses Tutorials benötigt wird.

    :::image type="content" source="./media/tutorial-geofence/resource-location-url.png" alt-text="Kopieren Sie die URL des Ressourcenspeicherorts.":::

### <a name="optional-retrieve-geojson-data-metadata"></a>(Optional:) Abrufen der Metadaten von GeoJSON-Daten

Sie können Metadaten aus den hochgeladenen Daten abrufen. Die Metadaten enthalten Informationen wie Ressourcenstandort-URL, Erstellungsdatum, Aktualisierungsdatum, Größe und Uploadstatus.

So rufen Sie Inhaltsmetadaten ab

1. Wählen Sie **Neu** aus.

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Metadaten aus Datenupload*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die `resource Location URL` ein, die Sie in [Überprüfen des Uploadstatus der GeoJSON-Daten](#check-the-geojson-data-upload-status) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen (ersetzen `{Azure-Maps-Primary-Subscription-key}` Sie durch Ihren primären Abonnementschlüssel):

    ```http
    https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie im Antwortfenster die Registerkarte **Body** (Text) aus. Die Metadaten sollten wie das folgende JSON-Fragment aussehen:

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Erstellen von Workflows in Azure Logic Apps

Als Nächstes erstellen Sie zwei [Logik-App](../event-grid/handler-webhooks.md#logic-apps)-Endpunkte, die eine E-Mail-Benachrichtigung auslösen.

So erstellen Sie die Logik-Apps

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Azure-Portal oben links die Option **Ressource erstellen** aus.

3. Geben Sie im Feld **Marketplace durchsuchen** den Suchbegriff **Logik-App** ein.

4. Wählen Sie in den Ergebnissen **Logik-App** aus. Wählen Sie anschließend **Erstellen**.

5. Geben Sie auf der Seite **Logik-App** die folgenden Werte ein:
    * Das **Abonnement**, das Sie für diese Logik-App verwenden möchten.
    * Den Namen der **Ressourcengruppe** für diese Logik-App. Sie können für die Ressourcengruppe die Option **Neu erstellen** oder **Vorhandene verwenden** auswählen.
    * Den **Namen Ihrer Logik-App**. Verwenden Sie hier den Namen `Equipment-Enter`.

    Behalten Sie in diesem Tutorial für alle anderen Werte die Standardeinstellungen bei.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Screenshot: Erstellen einer Logik-App":::

6. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Einstellungen, und wählen Sie **Erstellen** aus.

7. Wählen Sie die Option **Zu Ressource wechseln** aus, nachdem die Bereitstellung erfolgreich abgeschlossen wurde.

8. Scrollen Sie im **Logik-App-Designer** nach unten zum Abschnitt **Starten Sie mit einem gängigen Trigger**. Wählen Sie **Beim Empfang einer HTTP-Anforderung** aus.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Screenshot: Erstellen eines HTTP-Triggers für eine Logik-App":::

9. Wählen Sie oben rechts im Logik-App-Designer die Option **Speichern** aus. Die **HTTP-POST-URL** wird automatisch generiert. Speichern Sie die URL. Sie wird im nächsten Abschnitt zum Erstellen eines Ereignisendpunkts benötigt.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Screenshot: Logik-App: HTTP-Anforderungs-URL und JSON-Code":::

10. Wählen Sie **+ Neuer Schritt** aus.

11. Geben Sie in das Suchfeld `outlook.com email` ein. Scrollen Sie in der Liste **Aktionen** nach unten, und wählen Sie **E-Mail senden (V2)** aus.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Screenshot: Erstellen eines Logik-App-Designers":::

12. Melden Sie sich bei Ihrem Outlook-Konto an. Wählen Sie **Ja** aus, damit die Logik-App auf das Konto zugreifen kann. Füllen Sie die Felder für das Senden einer E-Mail aus.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Screenshot: Erstellen einer Logik-App: Schritt zum Senden einer E-Mail":::

    >[!TIP]
    > Sie können GeoJSON-Antwortdaten, z. B. `geometryId` oder `deviceId`, in Ihren E-Mail-Benachrichtigungen abrufen. Sie können Logic Apps so konfigurieren, dass die von Event Grid gesendeten Daten gelesen werden. Weitere Informationen zum Konfigurieren von Logic Apps für die Nutzung und Übergabe von Ereignisdaten in E-Mail-Benachrichtigungen finden Sie unter [Tutorial: Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Event Grid und Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

13. Wählen Sie links oben im **Logik-App-Designer** die Option **Speichern** aus.

14. Wiederholen Sie den Vorgang, um eine zweite Logik-App zu erstellen, die den Baustellenleiter benachrichtigt, wenn Geräte den Baustellenbereich verlassen. Geben Sie der Logik-App den Namen `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Erstellen von Abonnements für Azure Maps-Ereignisse

Azure Maps unterstützt [drei Ereignistypen](../event-grid/event-schema-azure-maps.md). In diesem Tutorial erstellen Sie Abonnements für die folgenden beiden Ereignisse:

* Geofencingereignisse bei Eintritt
* Geofencingereignisse bei Austritt

So erstellen Sie einen Geofenceaustritt und geben das Ereignisabonnement ein

1. Wählen Sie in Ihrem Azure Maps-Konto **Abonnements** aus.

2. Wählen Sie Ihren Abonnementnamen aus.

3. Wählen Sie im Einstellungsmenü **Ereignisse** aus.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Screenshot: Navigieren zu Ereignissen im Azure Maps-Konto":::

4. Wählen Sie auf der Seite „Ereignisse“ die Option **+ Ereignisabonnement** aus.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Screenshot: Erstellen eines Abonnements für Azure Maps-Ereignisse":::

5. Geben Sie auf der Seite **Ereignisabonnement erstellen** die folgenden Werte ein:
    * Den **Namen** des Ereignisabonnements.
    * Das **Ereignisschema** muss *Event Grid-Schema* lauten.
    * Der **Name des Systemthemas** für dieses Ereignisabonnement, der in diesem Fall `Contoso-Construction` lautet.
    * Wählen Sie unter **Nach Ereignistypen filtern** die Option `Geofence Entered` (Eingang in Geofence) als Ereignistyp aus.
    * Wählen Sie unter **Endpunkttyp** die Option `Web Hook` (Webhook) aus.
    * Kopieren Sie unter **Endpunkt** die HTTP-POST-URL für den Logik-App-Eingangsendpunkt, den Sie im vorherigen Abschnitt erstellt haben. Sollten Sie die URL nicht gespeichert haben, können Sie zum Logik-App-Designer zurückkehren und sie im Schritt für den HTTP-Trigger kopieren.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Screenshot: Details zum Azure Maps-Ereignisabonnement":::

6. Klicken Sie auf **Erstellen**.

7. Wiederholen Sie denselben Prozess für das Geofencingereignis beim Austritt. Wählen Sie als Ereignistyp unbedingt `Geofence Exited` aus.

## <a name="use-spatial-geofence-get-api"></a>Verwenden der Spatial Geofence-GET-API

Als Nächstes verwenden Sie die [Spatial Geofence-GET-API](/rest/api/maps/spatial/getgeofence), um E-Mail-Benachrichtigungen an den Baustellenleiter zu senden, wenn ein Gerät in den Geofencebereich gebracht oder daraus entfernt wird.

Jedes Gerät verfügt über eine `deviceId`. In diesem Tutorial verfolgen Sie ein einzelnes Gerät mit der eindeutigen ID `device_1` nach.

Das folgende Diagramm zeigt die fünf Positionen des Geräts im Zeitverlauf – beginnend mit der *Startposition*, die sich außerhalb der Geofences befindet. Die *Startposition* ist in diesem Tutorial nicht definiert, da Sie das Gerät hierfür nicht abfragen.

Wenn Sie die [Spatial Geofence-GET-API](/rest/api/maps/spatial/getgeofence) mit einer Geräteposition abfragen, die angibt, dass das Gerät erstmals in den Geofencebereich gebracht wurde oder diesen verlassen hat, wird von Event Grid der entsprechende Logik-App-Endpunkt aufgerufen. Hierüber wird dann eine E-Mail-Benachrichtigung an den Baustellenleiter gesendet.

In den folgenden Abschnitten werden jeweils API-Anforderungen mit den fünf verschiedenen Positionskoordinaten des Geräts gesendet.

![Diagramm: Geofencekarte in Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Geräteposition 1 (47,638237,-122,132483)

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *Standort 1*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL ein. Die Anforderung sollte der folgenden URL ähneln. (Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.)

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

6. Wählen Sie **Send** (Senden) aus.

7. Die Antwort sollte in etwa wie das folgende GeoJSON-Fragment aussehen:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

In der obigen GeoJSON-Antwort bedeutet die negative Entfernung zum Geofence für den Hauptbereich, dass sich das Gerät innerhalb des Geofencebereichs befindet. Die positive Entfernung vom Geofence für den Unterbereich bedeutet, dass sich das Gerät außerhalb des Unterbereichs befindet. Da dieses Gerät zum ersten Mal innerhalb des Geofence für den Hauptbereich ermittelt wurde, wird der Parameter `isEventPublished` auf `true` festgelegt. Der Baustellenleiter erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass ein Gerät in den Geofencebereich gebracht wurde.

### <a name="location-2-4763800-122132531"></a>Position 2 (47.63800,-122.132531)

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *Standort 2*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL ein. Die Anforderung sollte der folgenden URL ähneln. (Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.)

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

6. Wählen Sie **Send** (Senden) aus.

7. Die Antwort sollte in etwa wie das folgende GeoJSON-Fragment aussehen:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

In der obigen GeoJSON-Antwort befindet sich das Gerät weiterhin innerhalb des Geofence für den Hauptbereich und wurde nicht in den Geofence für den Unterbereich gebracht. Der Parameter `isEventPublished` wird daher auf `false` festgelegt, und der Baustellenleiter erhält keine E-Mail-Benachrichtigungen.

### <a name="location-3-4763810783315048-12213336020708084"></a>Position 3 (47.63810783315048,-122.13336020708084)

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *Standort 3*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL ein. Die Anforderung sollte der folgenden URL ähneln. (Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.)

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

6. Wählen Sie **Send** (Senden) aus.

7. Die Antwort sollte in etwa wie das folgende GeoJSON-Fragment aussehen:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

In der obigen GeoJSON-Antwort befindet sich das Gerät weiterhin innerhalb des Geofence für den Hauptbereich, aber zusätzlich auch im Geofence für den Unterbereich. Der Parameter `isEventPublished` wird daher auf `true` festgelegt. Der Baustellenleiter erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass das Gerät in einen Geofencebereich gebracht wurde.

>[!NOTE]
>Falls das Gerät außerhalb der Arbeitszeit in den Unterbereich gebracht wurde, wird kein Ereignis veröffentlicht, und der Baustellenleiter erhält keine Benachrichtigungen.  

### <a name="location-4-47637988-1221338344"></a>Position 4 (47.637988,-122.1338344)

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *Standort 4*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL ein. Die Anforderung sollte der folgenden URL ähneln. (Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.)

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Die Antwort sollte in etwa wie das folgende GeoJSON-Fragment aussehen:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

In der obigen GeoJSON-Antwort befindet sich das Gerät weiterhin innerhalb des Geofence für den Hauptbereich, aber hat den Geofence für den Unterbereich verlassen. Beachten Sie hierbei aber, dass der Wert `userTime` nach dem in den Geofencedaten definierten Ablauftermin (`expiredTime`) liegt. Der Parameter `isEventPublished` wird deshalb auf `false` festgelegt, und der Baustellenleiter erhält keine E-Mail-Benachrichtigung.

### <a name="location-5-4763799--122134505"></a>Position 5 (47,63799, -122,134505)

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *Standort 5*.

4. Wählen Sie als HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL ein. Die Anforderung sollte der folgenden URL ähneln. (Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{udid}` durch die `udid`, die Sie im Abschnitt [Hochladen von GeoJSON-Geofencingdaten](#upload-geofencing-geojson-data) gespeichert haben.)

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Die Antwort sollte in etwa wie das folgende GeoJSON-Fragment aussehen:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

In der obigen GeoJSON-Antwort wurde das Gerät aus dem Geofence für den Hauptbereich entfernt. Der Parameter `isEventPublished` wird daher auf `true` festgelegt, und der Baustellenleiter erhält eine E-Mail-Benachrichtigung mit dem Hinweis, dass das Gerät aus einem Geofencebereich entfernt wurde.

Sie können mithilfe von Azure Maps außerdem [E-Mail-Benachrichtigungen mithilfe von Event Grid und Logic Apps senden](../event-grid/publish-iot-hub-events-to-logic-apps.md) und die [unterstützten Ereignishandler in Event Grid](../event-grid/event-handlers.md) überprüfen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es sind keine zu bereinigenden Ressourcen vorhanden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Behandeln von Inhaltstypen in Azure Logic Apps](../logic-apps/logic-apps-content-type.md)
