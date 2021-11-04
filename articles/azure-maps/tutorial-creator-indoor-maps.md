---
title: 'Tutorial: Erstellen von Gebäudeplänen mithilfe von Microsoft Azure Maps Creator'
description: Tutorial zum Erstellen von Gebäudeplänen mithilfe von Microsoft Azure Maps Creator.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef1643f349fea853b15e0d8bcd2f07a0fd412188
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447149"
---
# <a name="tutorial-use-creator-to-create-indoor-maps"></a>Tutorial: Verwenden von Creator zum Erstellen von Gebäudeplänen

Dieses Tutorial enthält eine Anleitung zum Erstellen von Gebäudeplänen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hochladen des Zeichnungspakets für Ihren Gebäudeplan
> * Konvertieren Ihres Zeichnungspakets in Kartendaten
> * Erstellen eines Datasets auf der Grundlage Ihrer Kartendaten
> * Erstellen eines Kachelsets auf der Grundlage der Daten in Ihrem Dataset
> * Abfragen der Azure Maps-WFS-API (Web Feature Service), um Informationen zu Ihren Kartenfeatures zu erhalten
> * Erstellen eines Featurezustandssets unter Verwendung Ihrer Kartenfeatures und der Daten in Ihrem Dataset
> * Aktualisieren Ihres Featurezustandssets

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen Sie ein Azure Maps-Konto.](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)
3. [Erstellen Sie eine Creator-Ressource.](how-to-manage-creator.md)
4. Herunterladen des [Beispielzeichenpakets](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können jedoch auch eine andere API-Entwicklungsumgebung verwenden.

>[!IMPORTANT]
> In diesem Tutorial wird die geografische URL `us.atlas.microsoft.com` verwendet. Wenn Ihr Creator-Dienst nicht in den USA erstellt wurde, müssen Sie eine andere geografische URL verwenden.  Weitere Informationen finden Sie unter [Zugreifen auf Creator-Dienste](how-to-manage-creator.md#access-to-creator-services). Informationen zum Anzeigen von Zuordnungen der Region zum geografischen Standort finden Sie unter [Geografischer Bereich des Creator-Diensts](creator-geographic-scope.md).

## <a name="upload-a-drawing-package"></a>Hochladen eines Zeichnungspakets

Verwenden Sie die [Datenupload-API](/rest/api/maps/data-v2/upload-preview), um das Zeichnungspaket in Azure Maps-Ressourcen hochzuladen.

Bei der Datenupload-API handelt es sich um eine zeitintensive Transaktion, durch die das unter [Creator-API V2 für zeitintensive Vorgänge](creator-long-running-operation-v2.md) definierte Muster implementiert wird.

So laden Sie das Zeichnungspaket hoch:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsnamen) für die Anforderung ein, z. B. *POST Data Upload*.

4. Wählen Sie die HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL für die [Datenupload-API](/rest/api/maps/data-v2/upload-preview) ein. Die Anforderung sollte wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel:

    ```http
    https://us.atlas.microsoft.com/mapData?api-version=2.0&dataFormat=dwgzippackage&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie die Registerkarte **Headers** (Header) aus.

7. Wählen Sie im Feld **KEY** (SCHLÜSSEL) die Option `Content-Type` aus.

8. Wählen Sie im Feld **VALUE** (WERT) die Option `application/octet-stream` aus.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-header.png"alt-text="Informationen auf der Registerkarte „Headers“ für den Datenupload":::

9. Wählen Sie die Registerkarte **Body** (Text) aus.

10. Wählen Sie in der Dropdownliste die Option **binary** (Binär) aus.

11. Wählen Sie **Select File** (Datei auswählen) und anschließend ein Zeichnungspaket aus.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-body.png" alt-text="Auswählen eines Zeichnungspakets":::

12. Wählen Sie **Send** (Senden) aus.

13. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

14. Kopieren Sie den Wert des Schlüssels **Operation-Location** (Vorgangsspeicherort). Hierbei handelt es sich um die Status-URL (`status URL`). Die Status-URL (`status URL`) wird verwendet, um den Status des Zeichnungspaketuploads zu überprüfen.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-response-header.png" alt-text="Kopieren der Status-URL in den Speicherortschlüssel":::

### <a name="check-the-drawing-package-upload-status"></a>Überprüfen des Uploadstatus des Zeichnungspakets

So überprüfen Sie den Status des Zeichnungspakets und rufen die eindeutige ID (`udid`) ab:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen **Request name** (Anforderungsname) für die Anforderung ein, z. B. *GET Datenuploadstatus*.

4. Wählen Sie die HTTP-Methode **GET** aus.

5. Geben Sie die Status-URL (`status URL`) ein, die Sie im Schritt [Hochladen eines Zeichnungspakets](#upload-a-drawing-package) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel:

    ```http
    https://us.atlas.microsoft.com/mapData/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

8. Kopieren Sie den Wert des Schlüssels **Resource Location** (Ressourcenspeicherort). Hierbei handelt es sich um die URL des Ressourcenspeicherorts (`resource location URL`). Die URL des Ressourcenspeicherorts (`resource location URL`) enthält den eindeutigen Bezeichner (`udid`) der Zeichnungspaketressource.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/resource-location-url.png" alt-text="Kopieren Sie die URL des Ressourcenspeicherorts.":::

### <a name="optional-retrieve-drawing-package-metadata"></a>(Optional) Abrufen von Metadaten des Zeichnungspakets

Sie können Metadaten aus der Zeichnungspaketressource abrufen. Die Metadaten enthalten Informationen wie die URL des Ressourcenspeicherorts, das Erstellungsdatum, das Aktualisierungsdatum sowie die Größe und den Uploadstatus.

So rufen Sie Inhaltsmetadaten ab:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *GET Data Upload Metadata*.

4. . Wählen Sie die HTTP-Methode **GET** aus.

5. Geben Sie die URL des Ressourcenspeicherorts (`resource Location URL`) ein, die Sie im Schritt [Überprüfen des Uploadstatus des Zeichnungspakets](#check-the-drawing-package-upload-status) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel:

    ```http
    https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Body** (Text) aus. Die Metadaten sollten wie das folgende JSON-Fragment aussehen:

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

## <a name="convert-a-drawing-package"></a>Konvertieren eines Zeichnungspakets

Nach dem Hochladen des Zeichnungspakets wird die eindeutige Daten-ID (`udid`) für das hochgeladene Paket verwendet, um das Paket in Kartendaten zu konvertieren. Von der Konvertierungs-API wird eine zeitintensive Transaktion verwendet, durch die das [hier](creator-long-running-operation-v2.md) definierte Muster implementiert wird.

So konvertieren Sie ein Zeichnungspaket:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *POST Convert Drawing Package*.

4. Wählen Sie die HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL für den [Konvertierungsdienst](/rest/api/maps/v2/conversion/convert) ein. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `udid` durch den `udid`-Wert des hochgeladenen Pakets:

    ```http
    https://us.atlas.microsoft.com/conversions?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0&udid={udid}&inputType=DWG&outputOntology=facility-2.0
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

8. Kopieren Sie den Wert des Schlüssels **Operation-Location** (Vorgangsspeicherort). Hierbei handelt es sich um die Status-URL (`status URL`). Die Status-URL (`status URL`) wird verwendet, um den Status der Konvertierung zu überprüfen.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-convert-location-url.png" border="true" alt-text="Kopieren des Werts des Speicherortschlüssels für das Zeichnungspaket":::

### <a name="check-the-drawing-package-conversion-status"></a>Überprüfen des Konvertierungsstatus des Zeichnungspakets

Nach Abschluss des Konvertierungsvorgangs wird eine Konvertierungs-ID (`conversionId`) zurückgegeben. Sie können auf die Konvertierungs-ID (`conversionId`) zugreifen, indem Sie den Status des Konvertierungsprozesses für das Zeichnungspaket überprüfen. Die Konvertierungs-ID (`conversionId`) kann dann für den Zugriff auf die konvertierten Daten verwendet werden.

So können Sie den Status des Konvertierungsprozesses überprüfen und die Konvertierungs-ID (`conversionId`) abrufen:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *GET Conversion Status*.

4. Wählen Sie die HTTP-Methode **GET** aus:

5. Geben Sie die Status-URL (`status URL`) ein, die Sie im Schritt [Konvertieren eines Zeichnungspakets](#convert-a-drawing-package) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel:

    ```http
    https://us.atlas.microsoft.com/conversions/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

8. Kopieren Sie den Wert des Schlüssels **Resource Location** (Ressourcenspeicherort). Hierbei handelt es sich um die URL des Ressourcenspeicherorts (`resource location URL`). Die URL des Ressourcenspeicherorts (`resource location URL`) enthält den eindeutigen Bezeichner (`conversionId`), der von anderen APIs für den Zugriff auf die konvertierten Kartendaten verwendet werden kann.

      :::image type="content" source="./media/tutorial-creator-indoor-maps/data-conversion-id.png" alt-text="Kopieren der Konvertierungs-ID":::

Das Beispielzeichnungspaket sollte sich ohne Fehler oder Warnungen konvertieren lassen. Falls bei Ihrem eigenen Zeichnungspaket Fehler oder Warnungen zurückgegeben werden, enthält die JSON-Antwort einen Link zur [Schnellansicht für Zeichnungsfehler](drawing-error-visualizer.md). Mithilfe der Schnellansicht für Zeichnungsfehler können Sie Details von Fehlern und Warnungen untersuchen. Empfehlungen zur Behebung von Konvertierungsfehlern und -warnungen finden Sie unter [Fehler und Warnungen bei der Zeichnungskonvertierung](drawing-conversion-error-codes.md).

Das folgende JSON-Fragment zeigt ein Beispiel für eine Konvertierungswarnung:

```json
{
    "operationId": "{operationId}",
    "created": "2021-05-19T18:24:28.7922905+00:00",
    "status": "Succeeded",
     "warning": {
        "code": "dwgConversionProblem",
        "details": [
            {
                "code": "warning",
                "details": [
                    {
                        "code": "manifestWarning",
                        "message": "Ignoring unexpected JSON property: unitProperties[0].nonWheelchairAccessible with value False"
                    }
                ]
            }
        ]
    },
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Erstellen eines Datasets

Ein Dataset ist eine Sammlung von Kartenfeatures wie Gebäuden, Ebenen und Räumen. Verwenden Sie zum Erstellen eines Datasets die [Dataseterstellungs-API](/rest/api/maps/v2/dataset/create). Die Dataseterstellungs-API akzeptiert die Konvertierungs-ID (`conversionId`) für das konvertierte Zeichnungspaket und gibt eine Dataset-ID (`datasetId`) des erstellten Datasets zurück.

So erstellen Sie ein Dataset:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *POST Dataset Create*.

4. Wählen Sie die HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL für die [Dataset-API](/rest/api/maps/v2/dataset) ein. Die Anforderung sollte in etwa wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{conversionId` durch die Konvertierungs-ID (`conversionId`), die Sie im Schritt [Überprüfen des Konvertierungsstatus des Zeichnungspakets](#check-the-drawing-package-conversion-status) erhalten haben:

    ```http
    https://us.atlas.microsoft.com/datasets?api-version=2.0&conversionId={conversionId}&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus. 

8. Kopieren Sie den Wert des Schlüssels **Operation-Location** (Vorgangsspeicherort). Hierbei handelt es sich um die Status-URL (`status URL`). Die Status-URL (`status URL`) wird verwendet, um den Status des Datasets zu überprüfen.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-dataset-location-url.png" border="true" alt-text="Kopieren des Werts des Speicherortschlüssels für das Dataset":::

### <a name="check-the-dataset-creation-status"></a>Überprüfen des Status der Dataseterstellung

So können Sie den Status der Dataseterstellung überprüfen und die Dataset-ID (`datasetId`) abrufen:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *GET Dataset Status*.

4. Wählen Sie die HTTP-Methode **GET** aus.

5. Geben Sie die Status-URL (`status URL`) ein, die Sie im Schritt [Erstellen eines Datasets](#create-a-dataset) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel:

    ```http
    https://us.atlas.microsoft.com/datasets/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** (Header) aus. Der Wert des Schlüssels **Resource-Location** (Ressourcenspeicherort) ist die URL des Ressourcenspeicherorts (`resource location URL`). Die URL des Ressourcenspeicherorts (`resource location URL`) enthält den eindeutigen Bezeichner (`datasetId`) des Datasets.

8. Kopieren Sie die Dataset-ID (`datasetId`). Sie wird in den nächsten Abschnitten dieses Tutorials benötigt.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/dataset-id.png" alt-text="Kopieren der Dataset-ID":::

## <a name="create-a-tileset"></a>Erstellen eines Kachelsets

Bei einem Kachelset handelt es sich um eine Gruppe von Vektorkacheln, die auf der Karte gerendert werden. Kachelsets werden auf der Grundlage vorhandener Datasets erstellt. Ein Kachelset ist jedoch von dem zugrunde liegenden Dataset unabhängig. Wenn das Dataset gelöscht wird, ist das Kachelset weiterhin vorhanden.

So erstellen Sie ein Kachelset:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *POST Tileset Create*.

4. Wählen Sie die HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL für die [Kachelset-API](/rest/api/maps/v2/tileset) ein. Die Anforderung sollte in etwa wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{datasetId` durch die Dataset-ID (`datasetId`), die Sie im Schritt [Überprüfen des Status der Dataseterstellung](#check-the-dataset-creation-status) erhalten haben:

    ```http
    https://us.atlas.microsoft.com/tilesets?api-version=2.0&datasetID={datasetId}&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** aus.

8. Kopieren Sie den Wert des Schlüssels **Operation-Location** (Vorgangsspeicherort). Hierbei handelt es sich um die Status-URL (`status URL`). Die Status-URL (`status URL`) wird verwendet, um den Status des Kachelsets zu überprüfen.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-tileset-location-url.png" border="true" alt-text="Kopieren des Werts der Status-URL des Kachelsets":::

### <a name="check-the-tileset-creation-status"></a>Überprüfen des Status der Kachelseterstellung

So können Sie den Status der Dataseterstellung überprüfen und die Kachelset-ID (`tilesetId`) abrufen:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *GET Tileset Status*.

4. Wählen Sie die HTTP-Methode **GET** aus.

5. Geben Sie die Status-URL (`status URL`) ein, die Sie im Schritt [Erstellen eines Kachelsets](#create-a-tileset) kopiert haben. Die Anforderung sollte wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel:

    ```http
    https://us.atlas.microsoft.com/tilesets/operations/{operationId}?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Wählen Sie im Antwortfenster die Registerkarte **Headers** (Header) aus. Der Wert des Schlüssels **Resource-Location** (Ressourcenspeicherort) ist die URL des Ressourcenspeicherorts (`resource location URL`).  Die URL des Ressourcenspeicherorts (`resource location URL`) enthält den eindeutigen Bezeichner (`tilesetId`) des Datasets.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/tileset-id.png" alt-text="Kopieren der Kachelset-ID":::

## <a name="query-datasets-with-wfs-api"></a>Abfragen von Datasets mit der WFS-API

Datasets können mithilfe der [WFS-API](/rest/api/maps/v2/wfs) abgefragt werden. Mit der WFS-API können Sie alle Featuresammlungen oder eine bestimmte Sammlung abfragen. In diesem Abschnitt des Tutorials werden beide Möglichkeiten verwendet. Zuerst werden alle Sammlungen abgefragt. Danach wird eine Abfrage für die Sammlung `unit` durchgeführt.

### <a name="query-for-feature-collections"></a>Abfragen von Featuresammlungen

So fragen Sie alle Sammlungen in Ihrem Dataset ab:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *GET Dataset Collections*.

4. Wählen Sie die HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL für die [WFS-API](/rest/api/maps/v2/wfs) ein. Die Anforderung sollte in etwa wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{datasetId` durch die Dataset-ID (`datasetId`), die Sie im Schritt [Überprüfen des Status der Dataseterstellung](#check-the-dataset-creation-status) erhalten haben:

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Der Antworttext wird im GeoJSON-Format zurückgegeben und enthält alle Sammlungen im Dataset. Der Einfachheit halber enthält das hier gezeigte Beispiel nur die Sammlung `unit`. Ein Beispiel mit allen Sammlungen finden Sie unter [WFS-API für Sammlungsbeschreibungen](/rest/api/maps/v2/wfs/collection-description). Wenn Sie weitere Informationen zu einer Sammlung benötigen, können Sie eine der URLs im Element `link` auswählen.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

### <a name="query-for-unit-feature-collection"></a>Abfragen der Featuresammlung „unit“ (Einheit)

In diesem Abschnitt wird die [WFS-API](/rest/api/maps/v2/wfs) zum Abfragen der Featuresammlung `unit` verwendet.

So fragen Sie die Einheitensammlung in Ihrem Dataset ab:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *GET Unit Collection*.

4. Wählen Sie die HTTP-Methode **GET** aus.

5. Geben Sie die folgende URL ein. Ersetzen Sie dabei `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{datasetId` durch die Dataset-ID (`datasetId`), die Sie im Schritt [Überprüfen des Status der Dataseterstellung](#check-the-dataset-creation-status) erhalten haben:

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. Wählen Sie **Send** (Senden) aus.

7. Nachdem die Antwort zurückgegeben wurde, kopieren Sie das Feature `id` für eines der Features vom Typ `unit`. Im folgenden Beispiel lautet die ID (`id`) des Features „UNIT26“. Im nächsten Abschnitt dieses Tutorials wird „UNIT26“ als Feature `id` verwendet.

    ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Erstellen eines Featurezustandssets

Featurezustandssets dienen zum Definieren dynamischer Eigenschaften und Werte für bestimmte Features, die dies unterstützen. In diesem Abschnitt wird ein Zustandsset verwendet, das boolesche Werte und entsprechende Stile für die Eigenschaft **occupancy** (Auslastung) definiert.

So erstellen Sie ein Zustandsset:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *POST Create Stateset*.

4. Wählen Sie die HTTP-Methode **POST** aus.

5. Geben Sie die folgende URL für die [Zustandsset-API](/rest/api/maps/v2/feature-state/create-stateset) ein. Die Anforderung sollte in etwa wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{datasetId` durch die Dataset-ID (`datasetId`), die Sie im Schritt [Überprüfen des Status der Dataseterstellung](#check-the-dataset-creation-status) erhalten haben:

    ```http
    https://us.atlas.microsoft.com/featurestatesets?api-version=2.0&datasetId={datasetId}&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie die Registerkarte **Headers** (Header) aus.

7. Wählen Sie im Feld **KEY** (SCHLÜSSEL) die Option `Content-Type` aus. 

8. Wählen Sie im Feld **VALUE** (WERT) die Option `application/json` aus.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="Informationen zur Header-Registerkarte für die Erstellung von Zustandssets.":::

9. Wählen Sie die Registerkarte **Body** (Text) aus.

10. Wählen Sie in den Dropdownlisten **raw** und **JSON** aus.

11. Kopieren Sie die folgenden JSON-Stile, und fügen Sie sie in das Fenster **Body** (Text) ein:

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          }
       ]
    }
    ```

12. Wählen Sie **Send** (Senden) aus.

13. Kopieren Sie nach erfolgreicher Antwortrückgabe die Zustandsset-ID (`statesetId`) aus dem Antworttext. Die Zustandsset-ID (`statesetId`) wird im nächsten Abschnitt verwendet, um den Zustand der Eigenschaft `occupancy` (Auslastung) der Einheit mit der Feature-ID (`id`) „UNIT26“ zu ändern.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/response-stateset-id.png"alt-text="Antwort mit Zustandsset-ID":::

### <a name="update-a-feature-state"></a>Aktualisieren eines Featurezustands

So aktualisieren Sie den Zustand `occupied` (Belegt) der Einheit mit der Feature-ID (`id`) „UNIT26“:

1. Klicken Sie in der Postman-App auf **New** (Neu).

2. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus.

3. Geben Sie einen Anforderungsnamen (**Request name**) für die Anforderung ein – beispielsweise *PUT Set Stateset*.

4. Wählen Sie die HTTP-Methode **PUT** aus.

5. Geben Sie die folgende URL für die [API für Featurezustandssets](/rest/api/maps/v2/feature-state/create-stateset) ein. Die Anforderung sollte in etwa wie die folgende URL aussehen. Ersetzen Sie `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel und `{statesetId` durch die Zustandsset-ID (`statesetId`), die Sie im Schritt [Erstellen eines Featurezustandssets](#create-a-feature-stateset) erhalten haben:

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

6. Wählen Sie die Registerkarte **Headers** (Header) aus.

7. Wählen Sie im Feld **KEY** (SCHLÜSSEL) die Option `Content-Type` aus.

8. Wählen Sie im Feld **VALUE** (WERT) die Option `application/json` aus.

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="Informationen zur Header-Registerkarte für die Erstellung von Zustandssets.":::

9. Wählen Sie die Registerkarte **Body** (Text) aus.

10. Wählen Sie in den Dropdownlisten **raw** und **JSON** aus.

11. Kopieren Sie den folgenden JSON-Stil, und fügen Sie ihn in das **Body**-Fenster (Text) ein.

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

    >[!NOTE]
    > Die Aktualisierung wird nur gespeichert, wenn der Zeitstempel der Bereitstellung nach dem Zeitstempel der vorherigen Anforderung liegt.

12. Wählen Sie **Send** (Senden) aus.

13. Nach erfolgreicher Aktualisierung erhalten Sie den HTTP-Statuscode `200 OK`. Falls Sie [dynamische Stile](indoor-map-dynamic-styling.md) für einen Gebäudeplan implementiert haben, wird die Aktualisierung zum angegebenen Zeitstempel auf Ihrer Karte gerendert.

Mit der [API zum Abrufen von Featurezuständen](/rest/api/maps/v2/feature-state/get-states) können Sie den Zustand eines Features unter Verwendung der zugehörigen Feature-ID (`id`) abrufen. Sie können auch die [API zum Löschen von Featurezuständen](/rest/api/maps/v2/feature-state/delete-stateset) verwenden, um das Zustandsset und die zugehörigen Ressourcen zu löschen.

Weitere Informationen zu den verschiedenen Azure Maps Creator-Diensten aus diesem Artikel finden Sie unter [Creator für Gebäudepläne](creator-indoor-maps.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung des Moduls „Gebäudepläne“ finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwenden des Moduls „Gebäudepläne“ von Azure Maps](how-to-use-indoor-module.md)
